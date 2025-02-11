# Revoke shared access signatures (SAS) for Azure Local VM images created via Azure storage accounts

This TSG covers how to address an issue Microsoft has identified where the SAS created from a storage account resource to create an Arc Virtual Machine running on Azure Local image appears in logs and/or error messages. 

## Symptom

The SAS used to create an Arc VM image from a storage account is logged and can be accessed for reuse. It can appear in an error message or system logs.

## Cause

The error message or logs did not redact the SAS and would leave this SAS vulnerable for reuse outside of the intended single-use creation of a VM image for Azure Local.

VM images created on releases prior to Azure Local 2411.2 release are vulnerable.

## Mitigation Details

Microsoft resolved this issue via Azure Local version 2411.2. 

All VM images created after this version are expected to redact any SAS used in logs and error messages.

To mitigate this risk on prior versions, we recommend you take the following actions depending on the method of deploying your VM image from your storage account. 

### For VM images created via Azure Portal

1.  If it has been more than 7 days since creating the image, no action needs to be taken. The SAS is no longer valid and has expired.

2.  If it has been less than 7 days, you will need to revoke user delegation key or change/remove role assignments. See [Revoke a user delegation SAS](https://learn.microsoft.com/en-us/rest/api/storageservices/create-user-delegation-sas#revoke-a-user-delegation-sas) for full details.

### For VM images created via Azure CLI

1.  You were required to input a SAS as for the "image path" input and
    revoking the SAS will depend on how you created your SAS. This SAS
    can either be an ad-hoc SAS or a SAS with an associated stored
    access policy.

    -   Azure Storage accounts "generate SAS" action
        -   default to eight (8) hours for SAS validity unless you edit the expiry time on the Azure Portal.

        -   requires an expiry time specified by the user on Azure CLI.

    -   Depending on the signing method:

        -   Account keys: you can delegate a long duration (\>7 days)

        -   User delegation keys: up to 7 days

2.  Follow the steps below depending on the SAS that was created. If you
    are not sure which SAS you used to create, please follow the steps
    for revoking ad hoc SAS or SAS with stored access policy:

  | **SAS Policy**   | **Expired?** |  **Steps to revoke** | 
  ----------------- | --------------| --------------------------
  | Any SAS          |  Yes         |    No action needs to be taken, SAS is no longer valid.
  | Ad hoc SAS signed account key | No          |    Rotate or regenerate the account key used to create SAS, see [Manually rotate access keys](https://learn.microsoft.com/en-us/azure/storage/common/storage-account-keys-manage?tabs=azure-portal#manually-rotate-access-keys).| 
  | Ad hoc SAS signed user delegation key | No     |        Revoke user delegation key or change/remove role assignments, see [Revoke a user delegation SAS](https://learn.microsoft.com/en-us/rest/api/storageservices/create-user-delegation-sas#revoke-a-user-delegation-sas).|
  | SAS with stored access policy | No          |   Update the expiration time to a past date/time OR delete the stored access policy, see [Modify or revoke a stored access policy](https://learn.microsoft.com/en-us/rest/api/storageservices/define-stored-access-policy#modify-or-revoke-a-stored-access-policy).|


***Full details on how to revoke a SAS can be found in [Revoke a SAS](https://learn.microsoft.com/en-us/rest/api/storageservices/create-service-sas#revoke-a-sas).***
    
If you require further assistance, contact Microsoft Support.
