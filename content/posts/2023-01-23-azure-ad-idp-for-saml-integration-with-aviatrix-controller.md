---
title: Azure AD IdP for SAML Integration with Aviatrix Controller
author: admin
type: post
date: 2023-01-23T04:39:41+00:00
url: /2023/01/23/azure-ad-idp-for-saml-integration-with-aviatrix-controller/
wordads_ufa:
  - s:wpcom-ufa-v3-beta:1674468033
  - s:wpcom-ufa-v3-beta:1674468033
  - s:wpcom-ufa-v3-beta:1674468033
timeline_notification:
  - 1674448784
  - 1674448784
  - 1674448784
publicize_twitter_user:
  - thecontrolplane
  - thecontrolplane
  - thecontrolplane
categories:
  - Aviatrix
  - Azure
  - Azure AD
  - Cloud
  - SAML

---
Enabling SSO (Single Sign On) for your Aviatrix Controller is a great way to leverage your existing Identity Provider to allow users access to the Aviatrix Controller. This post will walk through the steps of enabling SAML integration with Azure AD. The steps at a high level are as follows:

  1. Create an Azure AD SAML Application
  2. Create an Aviatrix SAML endpoint
  3. Define Aviatrix permission groups
  4. Configure passing of permission group  
    

## 1. Create an Azure AD SAML Application {.wp-block-heading}

### Create a new Azure Enterprise App {.wp-block-heading}

First, we&#8217;ll need to navigate to the Azure portal and search for &#8220;Enterprise Applications.&#8221; Once there, you&#8217;ll select the option to create a new application.<figure class="wp-block-image size-large">

<img decoding="async" loading="lazy" width="1382" height="258" src="https://thecontrolplane.files.wordpress.com/2023/01/enterpriseapp-new.png?w=1024" alt="" class="wp-image-479" srcset="http://localhost/wp-content/uploads/2023/01/enterpriseapp-new.png 1382w, http://localhost/wp-content/uploads/2023/01/enterpriseapp-new-300x56.png 300w, http://localhost/wp-content/uploads/2023/01/enterpriseapp-new-1024x191.png 1024w, http://localhost/wp-content/uploads/2023/01/enterpriseapp-new-768x143.png 768w" sizes="(max-width: 1382px) 100vw, 1382px" /> </figure> 



Since this will be a custom endpoint, we&#8217;ll want to create our own application. We&#8217;re going to name this application &#8220;Aviatrix-SSO,&#8221; but you can input any name you prefer based on your requirements. Make sure you choose the option for the &#8220;non-gallery&#8221; application.<figure class="wp-block-gallery has-nested-images columns-default is-cropped wp-block-gallery-23 is-layout-flex"> <figure class="wp-block-image size-large">

<img decoding="async" loading="lazy" width="580" height="392" data-id="488"  src="https://thecontrolplane.files.wordpress.com/2023/01/appname-1.png?w=580" alt="" class="wp-image-488" srcset="http://localhost/wp-content/uploads/2023/01/appname-1.png 580w, http://localhost/wp-content/uploads/2023/01/appname-1-300x203.png 300w" sizes="(max-width: 580px) 100vw, 580px" /> </figure> <figure class="wp-block-image size-large"><img decoding="async" loading="lazy" width="1385" height="563" data-id="489"  src="https://thecontrolplane.files.wordpress.com/2023/01/createyourown-1.png?w=1024" alt="" class="wp-image-489" srcset="http://localhost/wp-content/uploads/2023/01/createyourown-1.png 1385w, http://localhost/wp-content/uploads/2023/01/createyourown-1-300x122.png 300w, http://localhost/wp-content/uploads/2023/01/createyourown-1-1024x416.png 1024w, http://localhost/wp-content/uploads/2023/01/createyourown-1-768x312.png 768w" sizes="(max-width: 1385px) 100vw, 1385px" /></figure> </figure> 



Next, from the overview page for the application, we will navigate to the &#8220;Users and Groups&#8221; section. This is where you can assign users/groups access to the specific application. The Users and Groups referenced here will be who we want to grant access to the Aviatrix Controller, so it is important to select the appropriate assignees. We know that all the users assigned to our &#8220;NetOps&#8221; group should be granted access to the Aviatrix Controller, so we&#8217;ll start by selecting the group and assigning them to the application. <figure class="wp-block-gallery has-nested-images columns-default is-cropped wp-block-gallery-25 is-layout-flex"> <figure class="wp-block-image size-large">

<img decoding="async" loading="lazy" width="1105" height="348" data-id="486"  src="https://thecontrolplane.files.wordpress.com/2023/01/assignusersgroups-1.png?w=1024" alt="" class="wp-image-486" srcset="http://localhost/wp-content/uploads/2023/01/assignusersgroups-1.png 1105w, http://localhost/wp-content/uploads/2023/01/assignusersgroups-1-300x94.png 300w, http://localhost/wp-content/uploads/2023/01/assignusersgroups-1-1024x322.png 1024w, http://localhost/wp-content/uploads/2023/01/assignusersgroups-1-768x242.png 768w" sizes="(max-width: 1105px) 100vw, 1105px" /> </figure> <figure class="wp-block-image size-large"><img decoding="async" data-id="502"  src="https://thecontrolplane.files.wordpress.com/2023/01/netopsgroup.png?w=1024" alt="" class="wp-image-502" /></figure> </figure> 

### SAML Configuration {.wp-block-heading}

Once we have our user assigned, we&#8217;ll need to configure the options for SSO (Single Sign-On). Before doing this, we&#8217;ll need to determine our Assumer Consumer Service (ACS) URL for the Aviatrix Controller. The ACS URL for the Aviatrix Controller will be in the following format:

_`https://[controller_IP or FQDN]/flask/saml/sso/[endpoint_name]`_

The name of the endpoint we will create in section 2 will be `avx-azuread`, but you can use whichever name you would like. You just need to make sure this is also the name of the endpoint you create on the Aviatrix Controller. For this example, our full ACS URL will be like so:

`https://[controller_IP]/flask/saml/sso/avx-azuread`

Now that we have the ACS URL, we can configure the SAML options under the Single Sign-On section of the Azure portal. Make sure to choose the option for SAML as the SSO method. Edit the basic SAML configuration and update the following fields:

  1. **Identifier (Entity ID)**: This can be set to `https://[controller_IP]` or the FQDN of the controller
  2. **Reply URL (Assertion Consumer Service URL)**: ACS URL listed above
  3. **Sign on URL**: ACS URL listed above
  4. **Relay State**: Leave blank
  5. **Logout URL**: Leave blank<figure class="wp-block-image size-large">

<img decoding="async" loading="lazy" width="1905" height="925" src="https://thecontrolplane.files.wordpress.com/2023/01/samlconfig.png?w=1024" alt="" class="wp-image-491" srcset="http://localhost/wp-content/uploads/2023/01/samlconfig.png 1905w, http://localhost/wp-content/uploads/2023/01/samlconfig-300x146.png 300w, http://localhost/wp-content/uploads/2023/01/samlconfig-1024x497.png 1024w" sizes="(max-width: 1905px) 100vw, 1905px" /> </figure> 

Lastly, we&#8217;ll move on to the Attributes & Claims section to configure our user claims. Initially, we&#8217;ll configure the following 3 SAML Token attributes. We&#8217;ll also set the Unique User Identifier to `user.mail`. This will complete the initial configuration for the Azure SAML application.<figure class="wp-block-table">

<table>
  <tr>
    <td>
      <strong>Name</strong>
    </td>
    
    <td>
      <strong>Value</strong>
    </td>
    
    <td>
      <strong>Namespace</strong>
    </td>
  </tr>
  
  <tr>
    <td>
      FirstName
    </td>
    
    <td>
      <code>user.givenname</code>
    </td>
    
    <td>
      (blank)
    </td>
  </tr>
  
  <tr>
    <td>
      LastName
    </td>
    
    <td>
      <code>user.surname</code>
    </td>
    
    <td>
      (blank)
    </td>
  </tr>
  
  <tr>
    <td>
      Email
    </td>
    
    <td>
      <code>user.mail</code>
    </td>
    
    <td>
      (blank)
    </td>
  </tr>
</table><figcaption class="wp-element-caption">User SAML Token Attributes</figcaption></figure> 

<figure class="wp-block-image size-large">

<img decoding="async" src="https://thecontrolplane.files.wordpress.com/2023/01/usersamltokens.png?w=838" alt="" class="wp-image-493" /> </figure> 



## 2. Create an Aviatrix SAML endpoint {.wp-block-heading}

Now that we have our Azure SAML App in place, we need to configure the SP endpoint within our Aviatrix Controller. We&#8217;ll start by navigating in the Controller UI to Setting => Controller => SAML Login and selecting &#8220;Add New&#8221; to create a new endpoint.<figure class="wp-block-image size-large">

<img decoding="async" src="https://thecontrolplane.files.wordpress.com/2023/01/avxsaml.png?w=1024" alt="" class="wp-image-494" /> </figure> 

### Fetch Metadata URL {.wp-block-heading}

When configuring the endpoint, we can see that we need to provide an IDP Metadata URL. This can be obtained by referring back to the Azure SAML application we configured in the previous section. Under the app configuration, refer to the SSO section, and the App Federation Metadata URL can be found under SAML Certificates.<figure class="wp-block-image size-large">

<img decoding="async" src="https://thecontrolplane.files.wordpress.com/2023/01/appfedurl.png?w=1024" alt="" class="wp-image-496" /> </figure> 

### Azure AD Custom SAML Request Template {.wp-block-heading}

Another item we&#8217;ll need to configure our SAML endpoint is a custom SAML request template. To integrate Azure AD with the Aviatrix Controller, this is the SAML request template we&#8217;ll need to use:

<pre class="wp-block-code"><code>&lt;?xml version="1.0" encoding="UTF-8"?&gt;
&lt;samlp:AuthnRequest xmlns:samlp="urn:oasis:names:tc:SAML:2.0:protocol" ID="$ID" Version="2.0" IssueInstant="$Time" Destination="$Dest" ForceAuthn="false" IsPassive="false" ProtocolBinding="urn:oasis:names:tc:SAML:2.0:bindings:HTTP-POST" AssertionConsumerServiceURL="$ACS"&gt;
&lt;saml:Issuer xmlns:saml="urn:oasis:names:tc:SAML:2.0:assertion"&gt;$Issuer&lt;/saml:Issuer&gt;
&lt;/samlp:AuthnRequest&gt;</code></pre>

### SAML Endpoint Configuration {.wp-block-heading}

Now we have all the information we need to configure our SAML endpoint. Populate each of the fields as follows:<figure class="wp-block-table">

<table>
  <tr>
    <td>
      <strong>Field</strong>
    </td>
    
    <td>
      <strong>Value</strong>
    </td>
  </tr>
  
  <tr>
    <td>
      Endpoint Name
    </td>
    
    <td>
      Endpoint name<br /><br />*Note: This MUST match the name of the endpoint you chose in the configuration of the ACS URL for the Azure application
    </td>
  </tr>
  
  <tr>
    <td>
      IDP Metadata Type
    </td>
    
    <td>
      URL
    </td>
  </tr>
  
  <tr>
    <td>
      IDP Metadata URL
    </td>
    
    <td>
      App Federation Metadata URL (copied from Azure application)
    </td>
  </tr>
  
  <tr>
    <td>
      Entity ID
    </td>
    
    <td>
      Hostname
    </td>
  </tr>
  
  <tr>
    <td>
      Access Set By
    </td>
    
    <td>
      SAML IDP attribute (more on this in the next sections)
    </td>
  </tr>
  
  <tr>
    <td>
      Sign Authn Requests
    </td>
    
    <td>
      Unchecked
    </td>
  </tr>
  
  <tr>
    <td>
      Custom SAML Request Template
    </td>
    
    <td>
      Copy the above request template and replace all the text in this section
    </td>
  </tr>
</table><figcaption class="wp-element-caption">Aviatrix SAML Endpoint Configuration</figcaption></figure> 

<figure class="wp-block-image size-large">

<img decoding="async" src="https://thecontrolplane.files.wordpress.com/2023/01/avxendpoint.png?w=1024" alt="" class="wp-image-501" /> </figure> 

### Test SAML Login {.wp-block-heading}

Once we have the endpoint configured, we get to test it out! Start by signing out of the Controller UI (or navigating to the Controller in a private browser tab), and you&#8217;ll now see the option to &#8220;Sign in with SAML&#8221; towards the bottom. Select this option for signing in and authenticating with your Azure AD user account. 

Remember, this user account must either be assigned to the Azure app directly or a member of a group assigned to the application.<figure class="wp-block-gallery has-nested-images columns-default is-cropped wp-block-gallery-27 is-layout-flex"> <figure class="wp-block-image size-large">

<img decoding="async" data-id="505"  src="https://thecontrolplane.files.wordpress.com/2023/01/avxsignon-1.png?w=576" alt="" class="wp-image-505" /> </figure> <figure class="wp-block-image size-large"><img decoding="async" data-id="506"  src="https://thecontrolplane.files.wordpress.com/2023/01/azurelogin.png?w=1024" alt="" class="wp-image-506" /></figure> </figure> 



Success! And just like that&#8230; we&#8217;re logged in with our Azure AD user. <figure class="wp-block-image size-large">

<img decoding="async" src="https://thecontrolplane.files.wordpress.com/2023/01/rbacuser.png?w=1024" alt="" class="wp-image-508" /> </figure> 



However&#8230; we now notice that although we&#8217;ve logged in, we can see that no RBAC Groups have been assigned. As of Aviatrix release version 6.9, the default behaviour is that assigned users are granted _read-only_ access in the event a user is authenticated, but no RBAC Group profile is assigned. We can see this behaviour by running a simple account audit against one of CSP accounts we have onboarded to our Aviatrix Controller:<figure class="wp-block-image size-large">

<img decoding="async" src="https://thecontrolplane.files.wordpress.com/2023/01/rbacerr-1.png?w=578" alt="" class="wp-image-511" /> </figure> 



While read-only access may be enough for some users, we&#8217;ll want to properly integrate the SAML endpoint to provide the appropriate level of access as users log into the Controller. Let&#8217;s take a look at that next&#8230;



## 3. Define Aviatrix permission groups {.wp-block-heading}

Before updating the Azure application to pass the proper information to assign RBAC Groups to each user, we must first configure the appropriate groups for users to land in. For this particular example, let&#8217;s say we have two major groups that will manage our Aviatrix deployment: NetOps and SecOps. The NetOps team should be granted &#8220;admin&#8221; level access to the controller as they are the primary owners of the platform. The SecOps team needs to be able to log into the controller but only needs access to a few particular items to manage the Aviatrix Firenet deployment. 

Lucky for us, the admin group already exists on the Aviatrix Controller. However, we still need to configure the appropriate group for our SecOps team. This group can be created by navigating to Accounts => Permission Groups in the Controller UI. We&#8217;ll configure this group with the name &#8220;secops.&#8221;<figure class="wp-block-image size-large">

<img decoding="async" src="https://thecontrolplane.files.wordpress.com/2023/01/secopsgroup.png?w=1024" alt="" class="wp-image-514" /> </figure> 



Next, we&#8217;ll configure the appropriate permissions for this group by highlighting the group and then selecting &#8220;Manage Permission.&#8221; For this example, we&#8217;ll be granting this group the following permissions:<figure class="wp-block-image size-large">

<img decoding="async" src="https://thecontrolplane.files.wordpress.com/2023/01/secopspermission.png?w=691" alt="" class="wp-image-517" /> </figure> 



At this point, we have all the appropriate permission groups we need to configure the Azure application to correctly pass the value of the RBAC group to be assigned at the time of authentication. Let&#8217;s now move back to Azure to finish this up.



## 4. Configure passing of permission group {.wp-block-heading}

### Create App Roles {.wp-block-heading}

First, we&#8217;ll need to configure the appropriate app roles for the application that can be assigned to each of the user/groups who are associated with the application. From the Azure portal, navigate to the App Registrations page and select &#8220;All Applications.&#8221; You should see the SAML application we configured earlier listed here. Select this application navigate to the &#8220;App roles&#8221; section to create a new app role.<figure class="wp-block-gallery has-nested-images columns-default is-cropped wp-block-gallery-29 is-layout-flex"> <figure class="wp-block-image size-large">

<img decoding="async" data-id="521"  src="https://thecontrolplane.files.wordpress.com/2023/01/appreg-1.png?w=898" alt="" class="wp-image-521" /> </figure> <figure class="wp-block-image size-large"><img decoding="async" data-id="522"  src="https://thecontrolplane.files.wordpress.com/2023/01/createapprole.png?w=1024" alt="" class="wp-image-522" /></figure> </figure> 



Here we need to create two roles that will align with our designated permission groups don&#8217;t the Aviatrix Controller. Remember, our groups were defined as &#8220;admin&#8221; and &#8220;secops,&#8221; therefore these will need to be set as the Value in each of the roles. Create each of the roles with their corresponding value:<figure class="wp-block-table">

<table>
  <tr>
    <td>
      <strong>Field</strong>
    </td>
    
    <td>
      <strong>Value</strong>
    </td>
  </tr>
  
  <tr>
    <td>
      Display Name
    </td>
    
    <td>
      Display name for the role
    </td>
  </tr>
  
  <tr>
    <td>
      Allowed member types
    </td>
    
    <td>
      Users/Groups
    </td>
  </tr>
  
  <tr>
    <td>
      Value
    </td>
    
    <td>
      [Permission Group name]
    </td>
  </tr>
  
  <tr>
    <td>
      Description
    </td>
    
    <td>
      Description for the role
    </td>
  </tr>
  
  <tr>
    <td>
      Enable app role
    </td>
    
    <td>
      True
    </td>
  </tr>
</table><figcaption class="wp-element-caption">App Role Values</figcaption></figure> 



For this example, we will configure each the roles as _avx-admin_ and _avx-secops_. <figure class="wp-block-gallery has-nested-images columns-default is-cropped wp-block-gallery-31 is-layout-flex"> <figure class="wp-block-image size-large">

<img decoding="async" data-id="525"  src="https://thecontrolplane.files.wordpress.com/2023/01/adminrole.png?w=576" alt="" class="wp-image-525" /> </figure> <figure class="wp-block-image size-large"><img decoding="async" data-id="526"  src="https://thecontrolplane.files.wordpress.com/2023/01/secopsrole.png?w=574" alt="" class="wp-image-526" /></figure> </figure> 



### Assign App Roles {.wp-block-heading}

Now we&#8217;ll navigate back to the Enterprise Application section and select our SSO app. Under the &#8220;Users and Groups&#8221; section we already have the NetOps group associated and need to assign the app role to this group. We can select the group and choose &#8220;Edit assignment&#8221; from the top menu. <figure class="wp-block-image size-large">

<img decoding="async" src="https://thecontrolplane.files.wordpress.com/2023/01/editassignment.png?w=1024" alt="" class="wp-image-528" /> </figure> 



You&#8217;ll notice that the &#8220;Select a Role&#8221; option is now available. Edit this field, and you&#8217;ll see the option for the two roles we created in the previous step. For this group, we&#8217;ll assign the _avx-admin_ role.<figure class="wp-block-image size-large">

<img decoding="async" src="https://thecontrolplane.files.wordpress.com/2023/01/selectrole.png?w=1024" alt="" class="wp-image-530" /> </figure> 



Since we didn&#8217;t initially assign the SecOps Group to the application, we&#8217;ll need to select &#8220;Add user/group&#8221; to add the group and assign the _avx-secops_ role to this group.<figure class="wp-block-gallery has-nested-images columns-default is-cropped wp-block-gallery-33 is-layout-flex"> <figure class="wp-block-image size-large">

<img decoding="async" data-id="534"  src="https://thecontrolplane.files.wordpress.com/2023/01/secopsselect-1.png?w=1015" alt="" class="wp-image-534" /> </figure> <figure class="wp-block-image size-large"><img decoding="async" data-id="536"  src="https://thecontrolplane.files.wordpress.com/2023/01/secopsassign-1.png?w=1011" alt="" class="wp-image-536" /></figure> </figure> 

### Configure Profile SAML User Token {.wp-block-heading}

Once the roles are assigned to the AD groups, we&#8217;ll need to configure the Azure app to pass this value to the Aviatrix Controller so the appropriate Permission Group can be assigned. We can do this by configuring a new user claim under the SSO configuration for the Azure application. Navigate to the &#8220;Single sign-on&#8221; section of the app and edit the &#8220;Attributes & Claims.&#8221; You&#8217;ll need to add a new claim named Profile and use the attribute `user.assignedroles` to inherit the value from the app role.<figure class="wp-block-image size-large">

<img decoding="async" src="https://thecontrolplane.files.wordpress.com/2023/01/profileclaim.png?w=884" alt="" class="wp-image-543" /> </figure> 

### Test RBAC Assignment {.wp-block-heading}

Now that we have the app roles assigned to the appropriate groups, we can re-test and confirm that the appropriate RBAC groups are assigned within Aviatrix on a per-user basis. For this test, we&#8217;ll use two user accounts, Chris Miles and Test Admin.  


Chris Miles has been assigned to the NetOps AD group

Test Admin has been assigned to the SecOps AD group<figure class="wp-block-image size-large">

<img decoding="async" src="https://thecontrolplane.files.wordpress.com/2023/01/azureusers.png?w=986" alt="" class="wp-image-540" /> </figure> 



Now when we log into the controller with each user account, we can see that a different RBAC group is assigned to each user just as intended. Success!!<figure class="wp-block-gallery has-nested-images columns-default is-cropped wp-block-gallery-35 is-layout-flex"> <figure class="wp-block-image size-large">

<img decoding="async" data-id="544"  src="https://thecontrolplane.files.wordpress.com/2023/01/assignedrolenetops.png?w=295" alt="" class="wp-image-544" /> </figure> <figure class="wp-block-image size-large"><img decoding="async" data-id="545"  src="https://thecontrolplane.files.wordpress.com/2023/01/assignedrolesecops.png?w=307" alt="" class="wp-image-545" /></figure> </figure> 



I hope this post has been helpful in some way for those looking to enable SAML authentication via Azure AD within Aviatrix. Stay tuned for the next post. Thanks!



## Notes {.wp-block-heading}

  * This post was created using Aviatrix Controller version 6.9. 
  * This integration requires either an Azure AD Premium P1 or P2 subscription