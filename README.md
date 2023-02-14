def nvl(data, newVal=None):
    if newVal is None:
        return
    
    if isinstance(data, str):
        data = [data]
    elif isinstance(data, (list, tuple, np.ndarray)):
        data = asCellStruct(data)
    else:
        raise ValueError("Input data must be a string, list, tuple, or numpy array")
    
    for iData in range(len(data)):
        if isinstance(data[iData], (int, float, complex)) and np.isnan(data[iData]):
            data[iData] = newVal
        elif data[iData] is None:
            data[iData] = newVal
    
    if all(isinstance(d, (int, float, complex)) for d in data):
        data = np.array(data)
    
    return data
