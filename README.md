srsd = (1 / 12) * np.sqrt((np.var(ratio_in, ddof=1) - (sr ** 2) * np.var(pits_in, ddof=1)) / ((n - 2) * np.var(pits_in, ddof=1)))

    if np.isnan(srsd) or np.isinf(srsd):
        return 3

    mond_srp = rabl_getMeanFin(sr + srsd, fp_in, votes_in)
    mond_srm = rabl_getMeanFin(sr - srsd, fp_in, votes_in)

    srsd_avg = np.abs((mond_srp - mond_sr) + np.abs(mond_srm - mond_sr)) / 2
    val_out = np.sqrt(9 + srsd_avg ** 2)
    return val_out
