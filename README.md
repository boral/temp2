
import numpy as np

def my_function(x1, x2, x3, x4):
    a = 0./np.isnan(x4[0])
    b = 0./(np.count_nonzero(np.nansum(x4, axis=0) | (~np.count_nonzero(np.nansum(x4, axis=0)) & ~np.isin(np.sum((x4==0)*np.array([1,2,4,8]), axis=0), [1,3,7,15]))) + 1e-12)
    c = np.sqrt((x1*0.45/(0.45+np.isnan(x2)*0.25+np.isnan(x3)*0.3))**2 + (np.nansum(x2, axis=0)*0.25/(0.45+np.isnan(x2)*0.25+np.isnan(x3)*0.3))**2 + (np.nansum(x3, axis=0)*0.3/(0.45+np.isnan(x2)*0.25+np.isnan(x3)*0.3))**2)
    return a + b + c
