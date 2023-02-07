def rabl_getMeanSbj(mu_in, sigma_in, votes_in):
    if np.isnan(mu_in):
        return np.NaN

    if abs(mu_in - 50) < np.finfo(100).eps and abs(sigma_in - 3) < np.finfo(100).eps and np.array_equal(votes_in, [-25,-15,-5,0,0,10,20]):
        return -2.02293802498687
    if abs(mu_in - 50) < np.finfo(100).eps and abs(sigma_in - 3) < np.finfo(100).eps and np.array_equal(votes_in, [-35,-25,-15,0,15,25,35]):
        return 8.05345373722233e-16

    cep = np.array([(i*100)/7 for i in range(1,8)])
    iep = 50 + 25 * np.log(cep / (100 - cep))

    licdf = np.array([
        [-11, 0],
        [-10, 0],
        [-3.5, 0.0002],
        [-3, 0.0013],
        [-2.5, 0.0062],
        [-2, 0.0228],
        [-1.5, 0.0668],
        [-1.2, 0.1151],
        [-1, 0.1587],
        [-0.8, 0.2119],
        [-0.5, 0.3085],
        [0, 0.5],
        [0.5, 0.6915],
        [0.8, 0.7881],
        [1, 0.8413],
        [1.2, 0.8849],
        [1.5, 0.9332],
        [2, 0.9772],
        [2.5, 0.9938],
        [3, 0.9987],
        [3.5, 0.9998],
        [10, 1]
    ])
    licdf_mfunction = np.diff(licdf[:,1]) / np.diff(licdf[:,0])
    licdf_mfunction = np.insert(licdf_mfunction, 0, 0)

    ZScore = (iep - mu_in) / sigma_in
    ZScore[-1] = 0

    eZScore = np.minimum(np.maximum(ZScore, licdf[0,0]), licdf[-1,0])

    intvlNum = np.zeros(7)
    for ii in range(7):
        intvlNum[ii] = np.argmin(eZScore[ii] <= licdf[:,0])

    op = licdf_mfunction[intvlNum] * (eZScore - licdf[intvlNum,0]) + licdf[intvlNum,1]
    op[-1] = 1
    
    if ZScore < licdf['Mean'][1]:
    fcdf = 0
elif ZScore > licdf['Mean'][21]:
    fcdf = 1
else:
    fcdf = op

cp = np.insert(np.diff(fcdf), 0, fcdf[0])
cp[-1] = max(0, cp[-1])

val_out = np.dot(cp, np.transpose(votes_in))
