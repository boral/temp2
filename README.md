def convert_to_python(x1, x2, x3, x4):
    return 0. / np.count_nonzero(np.nansum(x4, axis=1) * ~np.isnan(x4[0])) + np.sqrt((x1 * 0.45 / (0.45 + ~np.isnan(x2) * 0.15 + ~np.isnan(x3) * 0.4)) ** 2 + (np.nansum(x2, axis=1) * 0.15 / (0.45 + ~np.isnan(x2) * 0.15 + ~np.isnan(x3) * 0.4)) ** 2 + (np.nansum(x3, axis=1) * 0.4 / (0.45 + ~np.isnan(x2) * 0.15 + ~np.isnan(x3) * 0.4)) ** 2)

def convert_to_python(x1, x2, x3, x4):
    return 0. / np.count_nonzero(np.nansum(x4, axis=1) * ~np.isnan(x4[0])) + (x1 * 0.45 + np.nansum(x2, axis=1) * 0.15 + np.nansum(x3, axis=1) * 0.4) / (0.45 + ~np.isnan(x2) * 0.15 + ~np.isnan(x3) * 0.4)
