def getValueFromLookupTable(lookupTableName, outColName, valIfNan, lkpFilter, upperBoundIncl=False):
    # Get lookup table data
    lookupTable = StaticData.getLookupTable(lookupTableName)
    
    # Add double quotes for eval
    for i in range(len(lkpFilter)):
        lkpFilter[i]["inputVal"] = [str(x).replace("'", "''") for x in lkpFilter[i]["inputVal"]]
    
    # Get output column data
    outColumn = lookupTable[outColName]
    outVal = [None] * len(lkpFilter[0]["inputVal"])
    
    # Loop through each filter and build up the logical index
    logicalIdx = np.ones((len(lookupTable), len(lkpFilter[0]["inputVal"])), dtype=bool)
    for lkp in lkpFilter:
        if "maxValField" in lkp and isinstance(lkp["maxValField"], list):
            # Range filter
            inputMinVal = lookupTable[lkp["minValField"][0]]
            inputMaxVal = lookupTable[lkp["maxValField"][0]]
            inputVal = lkp["inputVal"]
            for iInput in range(len(inputVal)):
                if upperBoundIncl:
                    logicalIdx[:, iInput] &= inputMinVal < inputVal[iInput] <= inputMaxVal
                else:
                    logicalIdx[:, iInput] &= inputMinVal <= inputVal[iInput] < inputMaxVal
        else:
            # Equality filter
            inputField = lookupTable[lkp["minValField"][0]]
            inputVal = lkp["inputVal"]
            for iInput in range(len(inputVal)):
                logicalIdx[:, iInput] &= np.char.equal(inputField, inputVal[iInput])
    
    # Apply logical index to get lookup values
    for iInput in range(len(lkpFilter[0]["inputVal"])):
        idx = np.argmax(logicalIdx[:, iInput])
        lkpVal = outColumn[idx] if logicalIdx[idx, iInput] else None

        if lkpVal is None:
            lkpVal = np.nan
        if not isinstance(lkpVal, list):
            lkpVal = [lkpVal]

        outVal[iInput] = lkpVal[0]
    
    # Apply nvl function
    outVal = nvl(outVal, valIfNan)
    
    # Convert to numpy array if all elements are numeric
    if all(isinstance(v, (int, float)) for v in outVal):
        outVal = np.array(outVal)
    
    return outVal
