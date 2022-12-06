import argparse
import json
import requests
import sys
import warnings

def parse_args():
    parser = argparse.ArgumentParser()
    parser.add_argument('--domain', '-d', help='The domain to recon')
    parser.add_argument('--username', '-u', help='The username (email address) to enumerate')
    parser.add_argument('--password', '-p', help='The password for the username')
    parser.add_arugment('--credential', '-c', help='Credential in the form of username:password')
    parser.add_argument('--getcredtype', action='store_true', help='For querying the GetCredentialType endpoint')
    parser.add_argument('--oauthtoken', action='store_true', help='For querying the OAuth Token endpoint')
    parser.add_argument('--all', '-a', action='store_true', help='For querying both the GetCredentialType and OAuth Token endpoints')
    args = parser.parse_args()
    return args

def check_domain_managed(domain):
    url = f'https://login.microsoftonline.com/getuserrealm.srf?login={domain}&json=1'
    response = requests.get(url)
    response_json = response.json()
    if response_json['NameSpaceType'] != 'Managed':
        warnings.warn('Warning: the domain is NOT managed by Azure as the Identity Provider. Therefore, user enumeration may return false positives.')
        choice = input('Do you want to continue enumerating? (y/n)')
        if choice == 'n':
            exit()
