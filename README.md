def rabl_getRatioMeanFin(mu_in, sigma_in, votes_in):
    if mu_in == 0:
        return 0

    if np.isnan(mu_in):
        return np.nan

    cep = np.array([0, 100, 200, 300, 400, 500, 600, 700]) / 7
    licdf = np.array([-11, -10, -3.5, -3, -2.5, -2, -1.5, -1.2, -1, -0.8, -0.5, 0, 0.5, 0.8, 1, 1.2, 1.5, 2, 2.5, 3, 3.5, 10])
    cumulative_integral = np.array([0, 0, 0.0002, 0.0013, 0.0062, 0.0228, 0.0668, 0.1151, 0.1587, 0.2119, 0.3085, 0.5, 0.6915, 0.7881, 0.8413, 0.8849, 0.9332, 0.9772, 0.9938, 0.9987, 0.9998, 1])
    m_function = np.diff(cumulative_integral) / np.diff(licdf)
    m_function = np.insert(m_function, 0, 0)

    z_score = (cep - mu_in) / sigma_in
    z_score[-1] = 0
    e_z_score = np.clip(z_score, licdf[0], licdf[-1])
    intvl_num = np.zeros(7, dtype=np.int)

    for ii in range(7):
        intvl_num[ii] = np.argmin(e_z_score[ii] <= licdf)

    op = m_function[intvl_num] * (e_z_score - licdf[intvl_num]) + cumulative_integral[intvl_num]
    op[-1] = 1

    if z_score[0] < licdf[1]:
        fcdf = 0
    elif z_score[0] > licdf[-1]:
        fcdf = 1
    else:
        fcdf = op

    cp = np.diff(np.insert(fcdf, 0, fcdf[0]))
    cp[-1] = max(0, cp[-1])
    return np.dot(cp, votes_in)
