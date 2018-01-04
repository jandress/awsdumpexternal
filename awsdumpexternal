#search through AWS accounts for EC2s with public IP addresses and list them, where found
#1/2018
#jandress
import boto3
import ConfigParser
import os
 
#set an initial region so we can connect up and enumerate things
staticregion = 'us-east-1'

#we're specifically using EC2 here, but this could be retooled for other services easily
services = ['ec2']

#this is windows specific and is where the AWS creds live -- nothing will work without this file being present
#the file should be formatted as specified here http://docs.aws.amazon.com/cli/latest/userguide/cli-config-files.html
#updating the file path *should* make everything else work for other OSs
configdir = os.path.expandvars('%userprofile%\.aws\credentials')

#read in the sections from the credentials file
config = ConfigParser.ConfigParser()
config.read(configdir)

#loop through each section in the credentials file - this allows us to search through multiple AWS accounts
for section in config.sections():

	#setup a session for the account we read from the credentials file
	session = boto3.Session(profile_name = section)

	for service in services:

		#setup a client connection so we can ask for the regions -- we can use a static region for the connection, as we'll change it later
		client = session.client('ec2', region_name = staticregion)

		#get the list of regions relative to the service we're using
		regions = [region['RegionName'] for region in client.describe_regions()['Regions']]

		#loop through all of the regions
		for region in regions:
			print("Checking "+section+" "+region+" "+service)
			
			#connect up to the region
			foo = session.resource(service, region_name = region)
			
			#list out the instances
			instances = foo.instances.filter(
				Filters=[{'Name': 'instance-state-name', 'Values': ['running']}])
			
			#evaluate each instance and print the public IP, if we find it
			for instance in instances:
				if (instance.public_ip_address is not None):
					print(instance.public_ip_address)
