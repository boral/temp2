def init_crr(self):
    if self.Modelscore <= 23:
        CRR = 8.3
    elif self.Modelscore <= 29:
        CRR = 8.2
    elif self.Modelscore <= 34:
        CRR = 8.1
    elif self.Modelscore <= 39:
        CRR = 7.2
    elif self.Modelscore <= 43:
        CRR = 7.1
    elif self.Modelscore <= 47:
        CRR = 6.2
    elif self.Modelscore <= 50:
        CRR = 6.1
    elif self.Modelscore <= 53:
        CRR = 5.3
    elif self.Modelscore <= 55:
        CRR = 5.2
    elif self.Modelscore <= 57:
        CRR = 5.1
    elif self.Modelscore <= 61:
        CRR = 4.3
    elif self.Modelscore <= 64:
        CRR = 4.2
    elif self.Modelscore <= 69:
        CRR = 4.1
    elif self.Modelscore <= 72:
        CRR = 3.3
    elif self.Modelscore <= 77:
        CRR = 3.2
    elif self.Modelscore <= 80:
        CRR = 3.1
    elif self.Modelscore <= 83:
        CRR = 2.2
    elif self.Modelscore <= 86:
        CRR = 2.1
    elif self.Modelscore <= 90:
        CRR = 1.2
    elif self.Modelscore <= 95:
        CRR = 1.1
    elif self.Modelscore <= 100:
        CRR = 0.1
    else:
        CRR = 0
    return CRR
