# Overview

This document shows an example of setting up SSO for AWS console access with an admin 
role, using Stanford production IdP service as identify provider.  
You need to have a workgroup that contains users who will use the SSO to
access AWS console to manage resources.

# Identify provider and IAM role setup

## Identify provider setup

Login to your AWS console.

1. Select IAM service
1. Click 'Identity Providers' 
1. Click 'Create Provider'

    Choose a name that is easy to identify which provider provides SSO, e.g. stanford-idp. 
1. Upload idP-only SAML metadata document from:

       [idp-only metadata](https://idp.stanford.edu/metadata.xml)
   
    Click "Create" button to finish the provider setup.

## Create IAM role using this provider in the trust policy

   When you finish identy provider creation, there is a link to take you
   to create an IAM role, or you you can go back to IAM service, select 
   "Roles->Create New Role".

1. Create role name: e.g. admin-sso
1. Select __Role for Identity Provider Access__ in the role type selection screen near
   the bottom.
1. Select __Grant Web Single Sign-On (WebSSO) access to SAML providers__
1. Set permissions

   Give the role a permission, e.g. Administrator, Power admin user, etc. It depends on your use case.
1. Click "Create Role" to finish

#  Configure relying party trust between  IdP and AWS

Amazon currently only works with IdP-initiated SSO - our Unsolicited SSO Endpoints 
are not listed in our IdP metadata, but the handlers and decoders appear to be 
enabled.  AWS only uses the metadata for the certificate / entity ID.

You need to submit the request to shibboleth-team@lists.stanford.edu to complete the
setup with the following information.

* The [AWS metadata](https://signin.aws.amazon.com/static/saml-metadata.xml)

* Attribute Resolver Settings

  Replace values with values you created in the SSO setup steps. You can find the information
  from AWS console under IAM->Roles, IAM->Identify Provider sections. 
  
    Account number: 123456789012
    Provider name: arn:aws:iam::972569769453:saml-provider/stanford-idp
    Role-name: admin-sso
    Workgroup: itservices:idg-aws

#  Create a virtual host to access the AWS console

For example, create a virtual host name "my-account-aws-console.stanford.edu"
and redirect it to:

        https://idp.stanford.edu/idp/profile/SAML2/Unsolicited/SSO?providerId=urn:amazon:webservices
