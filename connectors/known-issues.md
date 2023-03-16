## AAD refresh token behavior
If you are using the connector with first-party AAD refresh/access tokens, note that password changes will not affect the existing connection.

## File Content parameter size calculation
Please note that file content being passed into action card parameter will be encoded as base64 string. Therefore new file content size will be larger than the original content up to 30-40 percent additionally. If any content size throttling rule is applicable - new file content size will be considered.

## InvokerConnectionOverrideFailed error
If you get an error similar to '{"error":{"code":"InvokerConnectionOverrideFailed","message":"Could not find any valid connection for connection reference name 'shared_office365' in APIM tokens header."}}'. Try these:
- Clear the browser's cache.
- Delete the connection and then re-add it.

## IP addresses for AAD-based connections
When creating a connection to the connectors which support the AAD-based authentication, the user authenticates against AAD and then we get the token with the device ID claim. The value of this claim is set with the IP address, from where the user is authenticating from. The device ID claim is set on the initial login (even if it's single sign on), and then stays in claims. Refreshing the token does not reset this claim. We only store it safely until we need to refresh it, when that happens we call AAD again to refresh it and that's what that IP is (in a nutshell, it doesn't come from the connector in this case, it comes from the user's public address). So, the IP address that is used by the user once they logon to the connection (either automatically or by hand), is their public facing IP address. Therefore if there is an IP addresses allow list configured in AAD, the user's IP address should also be allowed in addition to the other documented IP addresses.

## OAuth connections cannot be shared
OAuth connections cannot be shared with other users due to security reasons. For example, it is a potential security problem if user A is able to use a connection owned by user B to perform actions that would make it look like user B did the action.

## Pagination support
Pagination is implemented in many connectors action cards, which return a set of items - GetRows, GetItems, GetList, etc. You can utilize this feature by clicking "Settings" on card's menu item:

![Action Card Settings Menu](./assets/ActionCardSettings.png "Action Card Settings Menu")
![Action Card Settings Menu](./assets/ActionCardSettings1.png "Action Card Settings Menu")

By turning on pagination, the flow engine will continue to call the service until it has all of the items – or – hits the "Threshold" that you explicitly define in the settings:

![Action Card Settings](./assets/ActionCardSettings2.png "Action Card Settings")

## Polling trigger behavior
If you turn off the flow for some period of time and turn it back on, all items during that period will be triggered.
This happens because flow Turn off/Turn on does not reset the trigger state. As a workaround, provide 'Filter Query' trigger parameter to filter out unwanted items by conditions using various fields like entity id, date created, and date updated. If the 'Filter Query' parameter is not supported, you can use flow Condition action card after the trigger action card to filter out unwanted items.
