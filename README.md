from getValueFromLookupTable import getValueFromLookupTable

def nvl(data, newVal=None):
    if newVal is None:
        return
    
    if isinstance(data, str):
        data = [data]
    else:
        data = asCellStruct(data)
    
    for iData in range(len(data)):
        if np.isnan(data[iData]):
            data[iData] = newVal
    
    if all(isinstance(d, (int, float, complex)) for d in data):
        data = np.array(data)
    
    return data

def asCellStruct(data):
    return data.tolist()

def func_handle(x):
    return np.array([getValueFromLookupTable('BNK_OperEnvData', 'EconStrt', np.nan, 
                 {'minValField': ['ISO'], 'inputVal': [str(nvl([x], '__'))]}), []])
