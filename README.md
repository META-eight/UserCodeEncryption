# UserCodeEncryption  
Enables selected Epicor Kinetic User Codes to be encrypted in the database  

## Overview  

This solution adds a simple encryption to the 'Long Description' field of the User Codes within Epicor Kinetic. It relies on Epicor's own symmetrical encryption, as used within the system generally, so should remain robust, but is not intended to provide serious security. The purpose is to provide a convenient way of storing values that may be regularly needed (such as details for integrations etc) without making them trivially easy to read and access, or harvest.  

It works by catching the data saved to the database, and encrypting the LongDesc field value before it reaches the save, and by reversing the process when the data is fetched. So any access to the data directly will only see the encrypted version.  

The data can be read and edited as normal by users as long as they belong to a Security Group named exactly as the Code Type of the User Code. Anyone else will see the encrypted version, or a mask if it's saved as masked. Users not in the Security Group cannot save new or changed encrypted values.  

## To Set Up  

1. Download the components: UDCodesEncrypt.bpm, Encryption.efxb, App.UserCodesEntry.UserCodesForm_Customization_UDCodes_v01_CustomExport.xml and the Layers folder.
2. Add two custom (UD) fields to the Ice.UDCodes tables: Encrypt_c (boolean) and Mask_c (boolean).
3. Regenerate the database before installing anything, and confirm the custom fields are OK.
4. Open Epicor Functions and import Encryption.efxb.
5. Open Directives Import and import UDCodesEncrypt.bpm.
6. For Kinetic clients, open Application Studio and import the Layers. You may need to zip it in a containing folder first.
7. For classic client, open the User Codes screen in developer mode, go to the Customization menu, and from there import App.UserCodesEntry.UserCodesForm_Customization_UDCodes_v01_CustomExport.xml. If you aren't using the classic client then skip this step.
8. Check the following method directives are present and enabled:
- UserCodes.GetByID.Post.Decrypt
- UserCodes.GetBySysRowID.Post.Decrypt
- UserCodes.GetBySysRowIDs.Post.Decrypt
- UserCodes.GetRows.Post.Decrypt
- UserCodes.Update.Pre.Encrypt
- UserCodes.Update.Post.Decrypt
- UserCodes.UpdateExt.Pre.Encrypt
- UserCodes.UpdateExt.Post.Decrypt
9. Apply the custom Kinetic layer and/or the classic customization to the required menu items.

## To Use  

For any Code Type you want to use for encrypted items, create a Security Group with the same name as the Code Type. Add users who will need to have access (at least one user) to the group.  

Create or load the Code Type in User Codes.  

Add a code, set the ID and description, and save. Then open it in detail view, where the checkboxes for "Encrypt" and "Mask" will appear.  

Once the "Encrypt" option is selected, any value saved in the Long Description, will be saved in encrypted form. This will not be visible to a user with access, so to confirm, view the same code as a user without access, or temporarily remove your user from the Security Group and reload the User Codes page.  

If you need to use the actual unencrypted value in other customizations in the system, for example in Functions or BPMs, it's most predictable to fetch directly from the database and then use the Encrypt function to decrypt it. If using GetByID or similar, you will need to check the user's permission (UserSecurityOK is for this purpose) to deduce whether the value has been returned in encrypted or clear form and act accordingly.  

To use the "Encrypt" function, pass the clear value into the "input" parameter with the "encrypt" parameter true, to return the encrypted value, or pass the encrypted value into the "input" with "encrypt" false to return the clear value.  
