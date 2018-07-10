# python
# Python code learning and training
# coding: utf-8
import csv

class sampleMul:
    def __init__(self, RSlistFile, resFile):
        self.RSlistFile = RSlistFile
        self.resFile = resFile

    def stats(self):
        rs2pos = {}
        sample = {}
        mulinf = {}
        pos2genotype = {}
        allsample=[]
        Lfile = open(self.RSlistFile, 'r', encoding='utf-8')  # 一定要注意文件的编码方式，可通过editplus修改
        # open(self.RSlistFile, 'r', encoding='gb18030')
        Rfile = open(self.resFile, 'r', encoding='utf-8')

        for line1 in Lfile:
            cell = line1.strip().split()
            value = "{}_{}:{}/{}".format(cell[0], cell[1], cell[3], cell[4])
            rs2pos[cell[6]] = value  # 将rsid与每个位点的变异信息相对应

        Lfile.close()
        # print(rs2pos)
        for line2 in Rfile:
            cell = line2.strip().split('\t')
            if cell[0].startswith('XX'):
                if cell[8] == 'U':
                    continue
                if cell[4] in rs2pos.keys():  # 找到两个文件中相同的rs id
                    pos = rs2pos[cell[4]].split(':')[0]
                    mul = rs2pos[cell[4]].split(':')[1].split('/')
                    allel = cell[8]
                    if allel[0] != allel[1]:  # 判断位点的突变类型，0：野生型；1：杂合性；2：纯合型
                        multype = '1'
                    elif allel[0] == mul[0]:
                        multype = '0'
                    else:
                        multype = '2'

                    snp = [pos.split('_')[0], pos.split('_')[1], multype]
                    mulinf.setdefault(cell[0], []).append(snp)  #设置字典值为列表，并得到样本对应的位点信息
                    genotype = '{' + pos + '}:' + cell[8]
                    pos2genotype.setdefault(cell[0], []).append(genotype)  #设置字典值为列表，并得到样本对应的基因型信息
                else:
                    # print(cell[4],'\t')
                    continue
                sample[cell[0]] = [mulinf[cell[0]], pos2genotype[cell[0]]]
        # print(sample, '\n')
        # print('\n')
        for value in sample.values():
            allsample.append(value)
        print(allsample)
        f = open('C:\\Users\\wumengsi\\Desktop\\Learning_materials_and_training\\Task\\Task_delivery\\res_file', 'w')

        result = csv.writer(f, delimiter='\t')            #结果输出
        for key in sample.keys():
            result.writerow([key, ':', sample[key]])
        result.writerow([])
        result.writerow([allsample])


if __name__ == '__main__':
    print("OK")
    obj = sampleMul(
        RSlistFile="C:\\Users\\wumengsi\\Desktop\\Learning_materials_and_training\\Task\\Task_delivery\\cancerRSlist.txt",
        resFile="C:\\Users\\wumengsi\\Desktop\\Learning_materials_and_training\\Task\\Task_delivery\\result.xls")
    obj.stats()
