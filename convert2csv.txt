# Instructions
# To run the code use the below command
# python3 convert2csv.py --filename physsim-network.xml


# Libraries
from xml.etree import ElementTree  # for xml data file reading
import argparse  # for parsing command line arguments
import pandas as pd  # pandas dataframe

args = None  # arguments are appended in the global variable after parsing


# parsing arguments passed from command line
def args_parse():
    parser = argparse.ArgumentParser(description='python3 Convert2pdf.py --filename physsim-network.xml')
    parser.add_argument('--filename', required=False,
                        metavar="enter file path for physsim-network.xml file",
                        default=False,
                        help='path to .xml file')
    global args
    args = parser.parse_args()


# function accepts tree root and parses the tree to extract the data for nodes and links
def xml_parse(network):
    # loop for each item i.e link and nodes
    for item in range(len(network.getchildren())): 
        flag = 0
        # get elements data for each item
        for element in network.getchildren()[item]:

            if flag == 0:
                flag = 1
                # create a data frame to save the data
                data = pd.DataFrame(columns=element.attrib.keys())

            # get data from sub attrubutes if available 
            for att in element:  
                for sub_att in att:
                    # append the sub attribute data in the element data
                    element.attrib[sub_att.attrib['name']] = sub_att.text
            #  append data as new row in the dataframe
            data = data.append(element.attrib, ignore_index=True)  
        # finaly save each element data into respective csv
        data.to_csv(element.tag+'_Data.csv', index=False)  
        print(element.tag, ' data saved in:', element.tag+'_Data.csv')


# main function
if __name__ == '__main__':
    # parse the arguments and get the filename
    args_parse()
    # create xml tree from the .xml file
    tree = ElementTree.parse('physsim-network.xml')  
    # get the root of the tree
    network = tree.getroot()  
    # parse the tree and export the data in csv files
    xml_parse(network)  