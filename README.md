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
