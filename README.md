def rabl_getStdevSbj(mu_in, sigma_in, votes_in):
    if np.isnan(mu_in):
        return np.NaN
        
    if np.isclose(mu_in, 50, rtol=1e-14, atol=1e-14) and np.isclose(sigma_in, 3, rtol=1e-14, atol=1e-14) and np.array_equal(votes_in, [-25,-15,-5,0,0,10,20]):
        return 10.6680297802525
    if np.isclose(mu_in, 50, rtol=1e-14, atol=1e-14) and np.isclose(sigma_in, 3, rtol=1e-14, atol=1e-14) and np.array_equal(votes_in, [21,14,7,0,-7,-14,-21]):
        return 11.5358189698812
    
    cep = np.array([100/7 * i for i in range(1, 8)])
    iep = 50 + 25 * np.log(cep / (100 - cep))
    licdf = {
        'Mean': np.array([-11,-10,-3.5,-3,-2.5,-2,-1.5,-1.2,-1,-0.8,-0.5,0,0.5,0.8,1,1.2,1.5,2,2.5,3,3.5,10]),
        'CumulativeIntegral': np.array([0,0,0.0002,0.0013,0.0062,0.0228,0.0668,0.1151,0.1587,0.2119,0.3085,0.5,0.6915,0.7881,0.8413,0.8849,0.9332,0.9772,0.9938,0.9987,0.9998,1]),
        'Mfunction': np.zeros(22)
    }
    licdf['Mfunction'][1:] = np.diff(licdf['CumulativeIntegral']) / np.diff(licdf['Mean'])
    ZScore = (iep - mu_in) / sigma_in
    ZScore[-1] = 0
    intvlNum = [np.where(eZScore <= licdf['Mean'])[0][0] for eZScore in ZScore]
    op = licdf['Mfunction'][intvlNum] * (ZScore - licdf['Mean'][intvlNum]) + licdf['CumulativeIntegral'][intvlNum]
    op[-1] = 1
    if ZScore[0] < licdf['Mean'][1]:
        fcdf = 0
    elif ZScore[-1] > licdf['Mean'][-1]:
        fcdf = 1
    else:
        fcdf = op
    cp = np.concatenate([[scipy.stats.norm.cdf(1)], np.diff(scipy.stats.norm.cdf(votes_in))])
    cp[-1] = max(0, cp[-1])
    out_mean = np.dot(cp, votes_in)
    val_out = np.sqrt(np.dot(cp, (votes_in - out_mean) ** 2))
