def rabl_getRatioStdevFin(mu_in, sigma_in, votes_in):
    if mu_in == 0:
        return 0
    elif np.isnan(mu_in):
        return np.nan
    
    cep = np.array([i * 100/7 for i in range(1,8)])
    licdf_mean = np.array([-11,-10,-3.5,-3,-2.5,-2,-1.5,-1.2,-1,-0.8,-0.5,0,0.5,0.8,1,1.2,1.5,2,2.5,3,3.5,10])
    licdf_cumulative_integral = np.array([0,0,0.0002,0.0013,0.0062,0.0228,0.0668,0.1151,0.1587,0.2119,0.3085,0.5,0.6915,0.7881,0.8413,0.8849,0.9332,0.9772,0.9938,0.9987,0.9998,1])
    licdf_mfunction = np.zeros(22)
    licdf_mfunction[1:] = np.diff(licdf_cumulative_integral) / np.diff(licdf_mean)
    
    ZScore = (cep - mu_in) / sigma_in
    ZScore[-1] = 0
    eZScore = np.minimum(np.maximum(ZScore, licdf_mean[0]), licdf_mean[-1])
    intvlNum = np.zeros(7, dtype=int)
    for ii in range(7):
        intvlNum[ii] = np.argmin(eZScore[ii] <= licdf_mean)
        
    op = licdf_mfunction[intvlNum] * (eZScore - licdf_mean[intvlNum]) + licdf_cumulative_integral[intvlNum]
    op[-1] = 1
    
    fcdf = op
    fcdf[ZScore < licdf_mean[1]] = 0
    fcdf[ZScore > licdf_mean[-2]] = 1
    
    cp = np.append(fcdf[0], np.diff(fcdf))
    cp[-1] = max(0, cp[-1])
    
    out_mean = np.dot(cp, votes_in)
    val_out = np.sqrt(np.dot(cp, (votes_in - out_mean) ** 2))
    
    return val_out
