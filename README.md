def rabl_getPeerRank(ratio_in, peerID_in, ratioName_in, peerDataTable):
    ratio_in = ratio_in[0]
    if np.isnan(ratio_in):
        return np.nan

    valQ1 = 0.01 * getValueFromLookupTable(peerDataTable, ratioName_in + 'Q1', 0, {'minValField': ['PeerID'], 'inputVal': [peerID_in]})
    valQ2 = 0.01 * getValueFromLookupTable(peerDataTable, ratioName_in + 'Q2', 0, {'minValField': ['PeerID'], 'inputVal': [peerID_in]})
    valQ3 = 0.01 * getValueFromLookupTable(peerDataTable, ratioName_in + 'Q3', 0, {'minValField': ['PeerID'], 'inputVal': [peerID_in]})

    if valQ1 == valQ2:
        if valQ1 < valQ3:
            valQ1 = valQ1 * 0.99
        else:
            valQ1 = valQ1 * 1.01
    if valQ2 == valQ3:
        if valQ1 < valQ3:
            valQ3 = valQ3 * 1.01
        else:
            valQ3 = valQ3 * 0.99
    
    if ratio_in > valQ2:
        val_r = (valQ2 - ratio_in) / (max(valQ1, valQ3) - valQ2)
    else:
        val_r = (valQ2 - ratio_in) / (min(valQ1, valQ3) - valQ2)
    
    if np.isnan(val_r) or np.isinf(val_r):
        return np.nan

    if (valQ3 > valQ1 and ratio_in > valQ2) or (valQ3 < valQ1 and ratio_in < valQ2):
        return 2 ** (val_r - 1)
    else:
        return 1 - 2 ** (val_r - 1)
