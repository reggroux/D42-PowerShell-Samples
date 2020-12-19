# D42-PowerShell-Samples Encrypted
Example script for running Windows PowerShell scripts against the Device42 API's. Forked from: https://github.com/device42/D42-PowerShell-Samples . I didn't want my Username and Password in Plaintext of the script so figure I would post this incase anyone is in the same boat as me.


## Sample GET call using PowerShell
```
$D42Credentials = Get-Credential -Credential $null
$D42User = $D42Credentials.UserName
$D42Credentials.Password | ConvertFrom-SecureString
$D42Password = $D42Credentials.GetNetworkCredential().password
$d42_headers = @{"Authorization" = "Basic "+[System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($D42User+":"+$D42Password ))}
$d42_url = "https://d42_url/api/1.0/devices/id/10482/"


try {
   $result = Invoke-WebRequest -Uri $d42_url -Headers $d42_headers -Method Get
   $json = $result | ConvertFrom-Json
   Write-Output "Device Id = $($json.id)"
   Write-Output "Device UUID = $($json.uuid)"
} catch {
   $result = $_.Exception.Response.GetResponseStream()
   $reader = New-Object System.IO.StreamReader($result)
   $responseBody = $reader.ReadToEnd();
}
```

## Sample POST call using PowerShell Encrypted

```
$D42Credentials = Get-Credential -Credential $null
$D42User = $D42Credentials.UserName
$D42Credentials.Password | ConvertFrom-SecureString
$D42Password = $D42Credentials.GetNetworkCredential().password
$d42_headers = @{"Authorization" = "Basic "+[System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($D42User+":"+$D42Password ))}
$d42_url = "https://d42_url/api/1.0/devices/"
try {
   $pos_data = "uuid=$($json.uuid)&notes=Updated from PowerShell"
   $result = Invoke-WebRequest -Uri $d42_url -Headers $d42_headers -Body $pos_data -Method post
   $json_post = $result | ConvertFrom-Json
   Write-Output "Result:"
   Write-Output $json_post.msg
}
catch {
   $result = $_.Exception.Response.GetResponseStream()
   $reader = New-Object System.IO.StreamReader($result)
   $responseBody = $reader.ReadToEnd();
}
```

## Sample PUT call using PowerShell

```
$D42Credentials = Get-Credential -Credential $null
$D42User = $D42Credentials.UserName
$D42Credentials.Password | ConvertFrom-SecureString
$D42Password = $D42Credentials.GetNetworkCredential().password
$d42_headers = @{"Authorization" = "Basic "+[System.Convert]::ToBase64String([System.Text.Encoding]::UTF8.GetBytes($D42User+":"+$D42Password ))}
$d42_url = "https://d42_url/api/1.0/device/custom_field/"
try {
   $pos_data = "name=device_name&key=key_test&value=value_test"
   $result = Invoke-WebRequest -Uri $d42_url -Headers $d42_headers -Body $pos_data -Method put -ContentType "application/x-www-form-urlencoded"
   $json_post = $result | ConvertFrom-Json
   Write-Output "Result:"
   Write-Output $json_post.msg
}
catch {
   $result = $_.Exception.Response.GetResponseStream()
   $reader = New-Object System.IO.StreamReader($result)
   $responseBody = $reader.ReadToEnd();
   Write-Output $responseBody
}
write-output "complete"
```

## Notes
Please substitute your Device42 credentials for *$d42_username* and *$d42_password* and your Device42 URL for *$d42_url*.

Default results are in JSON format so use *ConvertFrom-Json* to get JSON object for easier data manipulation. 

If you see the following error: `"Error during serialization or deserialization using the JSON JavaScriptSerializer. The length of the string exceeds the value set on the maxJsonLength property."` You should be able to fix it by using the `$jsonserial.MaxJsonLength` property to increase the max JSON length to something like 67108864. See this [Technet](https://social.technet.microsoft.com/Forums/windowsserver/en-US/833c99c1-d8eb-400d-bf58-38f7265b4b0e/error-when-converting-from-json?forum=winserverpowershell&prof=required) thread for details. 

The *catch* block is used to handle error results returned from Device42 and will be in JSON.
