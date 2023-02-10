
import numpy as np

def getValueFromLookupTable(lookupTableName, outColName, valIfNan, lkpFilter, upperBoundIncl=0):
    lookupTable = StaticData.getLookupTable(lookupTableName)
    
    outVal = [None] * len(lkpFilter[0]['inputVal'])
    
    outColumn = lookupTable[outColName]
    
    logicalIdx = np.full((lookupTable.shape[0], len(lkpFilter[0]['inputVal'])), True)
    
    for iField in range(len(lkpFilter)):
        if 'maxValField' in lkpFilter[iField] and isinstance(lkpFilter[iField]['maxValField'][0], str):
            inputMinVal = lookupTable[lkpFilter[iField]['minValField'][0]]
            inputMaxVal = lookupTable[lkpFilter[iField]['maxValField'][0]]
            inputVal = lkpFilter[iField]['inputVal']
            for iInput in range(len(lkpFilter[0]['inputVal'])):
                if np.nansum(upperBoundIncl) > 0:
                    logicalIdx[:, iInput] = logicalIdx[:, iInput] & (inputMinVal < inputVal[iInput]) & (inputVal[iInput] <= inputMaxVal)
                else:
                    logicalIdx[:, iInput] = logicalIdx[:, iInput] & (inputMinVal <= inputVal[iInput]) & (inputVal[iInput] < inputMaxVal)
        else:
            inputField = lookupTable[lkpFilter[iField]['minValField'][0]]
            inputVal = lkpFilter[iField]['inputVal']
            for iInput in range(len(lkpFilter[0]['inputVal'])):
                logicalIdx[:, iInput] = logicalIdx[:, iInput] & np.char.str_equiv(inputField, inputVal[iInput])
    
    for iInput in range(len(lkpFilter[0]['inputVal'])):
        idx = np.where(logicalIdx[:, iInput])[0]
        if len(idx) > 0:
            lkpVal = outColumn[idx[0]]
            outVal[iInput] = lkpVal
        else:
            outVal[iInput] = np.NaN
    
    outVal = np.nansum(outVal) if np.nansum(outVal) else valIfNan
    if all(map(lambda x: isinstance(x, (int, float)), outVal)):
        outVal = np.array(outVal)
    return outVal
