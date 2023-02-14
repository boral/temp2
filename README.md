import pandas as pd
import numpy as np

def getValueFromLookupTable(lookupTableName, outColName, valIfNan, lkpFilter, upperBoundIncl=0):
    # load the lookup table as a pandas dataframe
    lookupTable = pd.read_excel(lookupTableName, engine='openpyxl')

    # add double quotes for eval
    lkpFilter[0]['inputVal'] = [str(x).replace("'", "''") for x in lkpFilter[0]['inputVal']]

    outVal = [None] * len(lkpFilter[0]['inputVal'])

    # get the output column
    outColumn = lookupTable[outColName]

    logicalIdx = np.full((lookupTable.shape[0], len(lkpFilter[0]['inputVal'])), True)

    # loop across the filters and build up the index vector idx by "and" 
    for iField in range(len(lkpFilter)):
        if 'maxValField' in lkpFilter[iField] and isinstance(lkpFilter[iField]['maxValField'], str):
            inputMinVal = lookupTable[lkpFilter[iField]['minValField']]
            inputMaxVal = lookupTable[lkpFilter[iField]['maxValField']]
            inputVal = lkpFilter[iField]['inputVal']
            for iInput in range(len(lkpFilter[0]['inputVal'])):
                if np.nansum(upperBoundIncl):
                    logicalIdx[:, iInput] = logicalIdx[:, iInput] & (inputMinVal < inputVal[iInput]) & (inputVal[iInput] <= inputMaxVal)
                else:
                    logicalIdx[:, iInput] = logicalIdx[:, iInput] & (inputMinVal <= inputVal[iInput]) & (inputVal[iInput] < inputMaxVal)
        else:
            inputField = lookupTable[lkpFilter[iField]['minValField']]
            inputVal = lkpFilter[iField]['inputVal']
            for iInput in range(len(lkpFilter[0]['inputVal'])):
                logicalIdx[:, iInput] = logicalIdx[:, iInput] & (inputField.str.lower() == inputVal[iInput].lower())

    # apply filter
    for iInput in range(len(lkpFilter[0]['inputVal'])):
        idx = np.where(logicalIdx[:, iInput])[0]
        if len(idx) > 0:
            lkpVal = outColumn[idx[0]]
            if pd.isna(lkpVal):
                lkpVal = np.nan
            outVal[iInput] = lkpVal

    outVal = nvl(outVal, valIfNan)
    if all(isinstance(v, (int, float)) for v in outVal):
        outVal = np.array(outVal)

    return outVal

def nvl(data, newVal):
    if newVal is None:
        return data
    if data is None:
        return newVal
    if isinstance(data, str):
        data = [data]
    data = np.array(data)
    data[np.isnan(data)] = newVal
    if all(isinstance(v, (int, float)) for v in data):
        data = data.astype(float)
    return data
