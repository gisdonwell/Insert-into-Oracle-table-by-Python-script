# Insert-into-Oracle-table-by-Python-script
# How to use python script to insert values into table.
#-------------------------------------------------------------------------------
# Name:        module1
# Purpose:
#
# Author:      stexu1
#
# Created:     16/09/2019
# Copyright:   (c) stexu1 2019
# Licence:     <your licence>
#-------------------------------------------------------------------------------
#
# callproc(name, parameters)
# !!! change '!civicdba password!?' in the script below
#import arcpy
import cx_Oracle
import shutil,os,time
import traceback

import smtplib
from email.MIMEMultipart import MIMEMultipart
from email.MIMEBase import MIMEBase
from email.MIMEText import MIMEText
from email import Encoders

#test
varCivicdba='civicdba/civicdbatest@gisdba'

#corp in production
#varCivicdba='civicdba/xxxxxx@corp'


print " This app is used for inserting street name.                  \n"
print " STREETID, NAME, STTYPE, DIRECTION, CITYCODE, TS, TYPEID      \n"
print " Values for STREETID and TS will be automatically generated.  \n"
print " Values for TYPEID field must be entered.                     \n"
print "  STTYPE TYPEID, STTYPE TYPEID, STTYPE TYPEID"
print "  AVE        1    HWY      12    VIEW     25"
print "  BAY        2    LANE     13    RIDGE    26"
print "  BLVD       3    MEWS     14    POINTE   27"
print "  CIR        4    PL       15    ROW      29"
print "  CLOSE      5    RD       16    COVE     30"
print "  CRES       6    ST       17    BEND     31"
print "  CRT        7    BAY      17    DALE     32"
print "  DR         8    TRAIL    18"
print "  GATE       9    WAY      19"
print "  GREEN     10    LINK     21"
print "  HILL      11    POINT    22"
print "  HWY       12    GDNS     24"


outfile=open('C:\\temp\\insertStreetName.txt','w')
starttime=time.strftime("%H:%M:%S: %m-%d-%y")
outfile.writelines("Start at: " + starttime + " \n")

StreetName=raw_input('Please enter street name')
StreetName=StreetName.upper()
StreetType=raw_input('Please enter street type')
StreetType=StreetType.upper()
StreetDirection=raw_input('Please enter street direction')
StreetDirection=StreetDirection.upper()
CITYCODE=raw_input('Please enter city code')
CITYCODE=CITYCODE.upper()
TYPEID=raw_input('Please enter typeid')
TYPEID=TYPEID.upper()

## Grant role CIVICDBA_PURPOSE_VIEWER on CIVICDBA.purpose
try:
    connection = cx_Oracle.connect(varCivicdba)
    outfile.writelines( connection.version + "\n")
    cursor = connection.cursor()

##    sqlgrant='grant CIVICDBA_PURPOSE_VIEWER to {0}'.format(userName)
    sqlgrant="INSERT INTO STREETNAME (NAME, STTYPE, DIRECTION, CITYCODE, TYPEID) VALUES ('{0}', '{1}','{2}','{3}',{4})".format(StreetName,StreetType,StreetDirection,CITYCODE,TYPEID)
    #print sqlgrant
    cursor.execute(sqlgrant)
    connection.commit()
    outfile.writelines( " {0}, {1},{2},{3},{4} have been successfully updated in CIVICDBA.STREETNAME".format(StreetName,StreetType,StreetDirection,CITYCODE,TYPEID) + "\n")
    print " {0} {1} {2} {3} {4} have been successfully updated in CIVICDBA.STREETNAME".format(StreetName,StreetType,StreetDirection,CITYCODE,TYPEID) + "\n"
##    sqlAlter='ALTER USER {0}'.format(userName) + ' DEFAULT ROLE CIVICDBA_PURPOSE_VIEWER'
##    cursor.execute(sqlAlter)
##    outfile.writelines( "Set role CIVICDBA_PURPOSE_VIEWER as default for user {0}".format(userName) + "\n")

    cursor.close()
    connection.close()
except cx_Oracle.Error, ex:
    error, =ex.args
    outfile.writelines('Error.code =' + str(error) + '\n')
##    outfile.writelines('Error.message =' + error.message + '\n')
##    outfile.writelines('Error.offset =' + str(error.offset) + '\n')
    traceback.print_exc()
## Grant role SDE_GIS_ADDRESS_VIEWER on dataset PRODDBA.Civic to user

endtime=time.strftime("%H:%M:%S: %m-%d-%y")
outfile.writelines("End at: " + endtime + "\n")
outfile.close()
