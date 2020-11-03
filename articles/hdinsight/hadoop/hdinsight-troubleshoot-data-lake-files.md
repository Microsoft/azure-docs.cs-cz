---
title: Nepovedlo se získat přístup k souborům úložiště Data Lake ve službě Azure HDInsight.
description: Nepovedlo se získat přístup k souborům úložiště Data Lake ve službě Azure HDInsight.
ms.service: hdinsight
ms.topic: troubleshooting
author: hrasheed-msft
ms.author: hrasheed
ms.reviewer: jasonh
ms.date: 08/13/2019
ms.openlocfilehash: 8bac53cd08629e8b0a9cb91e596856c0ae6b5a2f
ms.sourcegitcommit: 7863fcea618b0342b7c91ae345aa099114205b03
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2020
ms.locfileid: "93289110"
---
# <a name="unable-to-access-data-lake-storage-files-in-azure-hdinsight"></a>Nepovedlo se získat přístup k souborům úložiště Data Lake ve službě Azure HDInsight.

Tento článek popisuje postup řešení potíží a možná řešení potíží při komunikaci s clustery Azure HDInsight.

## <a name="issue-acl-verification-failed"></a>Problém: ověření seznamu ACL selhalo.

Zobrazí se chybová zpráva podobná této:

```
LISTSTATUS failed with error 0x83090aa2 (Forbidden. ACL verification failed. Either the resource does not exist or the user is not authorized to perform the requested operation.).
```

### <a name="cause"></a>Příčina

Uživatel může mít odvolaná oprávnění k instančnímu objektu (SP) pro soubory nebo složky.

### <a name="resolution"></a>Řešení

1. Ověřte, zda má aktualizace SP oprávnění "x", aby bylo možné procházet podél cesty. Další informace najdete v tématu [oprávnění](https://hdinsight.github.io/ClusterCRUD/ADLS/adls-create-permission-setup.html). Ukázkový `dfs` příkaz pro kontrolu přístupu k souborům nebo složkám v data lakem účtu úložiště:

    ```
    hdfs dfs -ls /<path to check access>
    ```

1. Nastavte požadovaná oprávnění pro přístup k cestě na základě prováděné operace čtení/zápisu. Oprávnění požadovaná pro různé operace se systémem souborů systému souborů najdete tady.

---

## <a name="issue-service-principal-certificate-expiry"></a>Problém: vypršení platnosti certifikátu instančního objektu

Zobrazí se chybová zpráva podobná této:

```
Token Refresh failed - Received invalid http response: 500
```

### <a name="cause"></a>Příčina

Platnost certifikátu zadaného pro přístup k instančnímu objektu možná vypršela.

1. SSH do hlavnímu uzlu. Pomocí následujícího příkazu ověřte přístup k účtu úložiště `dfs` :

    ```
    hdfs dfs -ls /
    ```

1. Potvrďte, že je chybová zpráva podobná následujícímu výstupu:

    ```
    {"stderr": "-ls: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken}}...
    ```

1. Získejte jednu z adres URL z `core-site.xml property`  -  `fs.azure.datalake.token.provider.service.urls` .

1. Spuštěním následujícího příkazu složeného načtěte token OAuth.

    ```
    curl gw0-abccluster.24ajrd4341lebfgq5unsrzq0ue.fx.internal.cloudapp.net:909/api/oauthtoken
    ```

1. Výstup platného objektu služby by měl být podobný tomuto:

    ```
    {"AccessToken":"MIIGHQYJKoZIhvcNAQcDoIIGDjCCBgoCAQA…….","ExpiresOn":1500447750098}
    ```

1. Pokud vypršela platnost certifikátu instančního objektu, bude výstup vypadat přibližně takto:

    ```
    Exception in OAuthTokenController.GetOAuthToken: 'System.InvalidOperationException: Error while getting the OAuth token from AAD for AppPrincipalId 23abe517-2ffd-4124-aa2d-7c224672cae2, ResourceUri https://management.core.windows.net/, AADTenantId https://login.windows.net/80abc8bf-86f1-41af-91ab-2d7cd011db47, ClientCertificateThumbprint C49C25705D60569884EDC91986CEF8A01A495783 ---> Microsoft.IdentityModel.Clients.ActiveDirectory.AdalServiceException: AADSTS70002: Error validating credentials. AADSTS50012: Client assertion contains an invalid signature. **[Reason - The key used is expired.**, Thumbprint of key used by client: 'C49C25705D60569884EDC91986CEF8A01A495783', Found key 'Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1', Configured keys: [Key0:Start=08/03/2016, End=08/03/2017, Thumbprint=C39C25705D60569884EDC91986CEF8A01A4956D1;]]
    Trace ID: e4d34f1c-a584-47f5-884e-1235026d5000
    Correlation ID: a44d870e-6f23-405a-8b23-9b44aebfa4bb
    Timestamp: 2017-10-06 20:44:56Z ---> System.Net.WebException: The remote server returned an error: (401) Unauthorized.
    at System.Net.HttpWebRequest.GetResponse()
    at Microsoft.IdentityModel.Clients.ActiveDirectory.HttpWebRequestWrapper.<GetResponseSyncOrAsync>d__2.MoveNext()
    ```

1. Všechny ostatní chyby související s Azure Active Directory a chyby související s certifikátem lze rozpoznat pomocí testu adresy URL brány pomocí příkazů k získání tokenu OAuth.

1. Pokud se vám při pokusu o přístup k ADLS z clusteru HDI zobrazuje následující chyba. Podle výše uvedeného postupu ověřte, jestli vypršela platnost certifikátu.

    ```
    Error: java.lang.IllegalArgumentException: Token Refresh failed - Received invalid http response: 500, text = Response{protocol=http/1.1, code=500, message=Internal Server Error, url=http://clustername.hmssomerandomstringc.cx.internal.cloudapp.net:909/api/oauthtoken}
    ```

### <a name="resolution"></a>Řešení

Vytvořte nový certifikát nebo přiřaďte existující certifikát pomocí následujícího skriptu prostředí PowerShell:

```powershell
$clusterName = 'CLUSTERNAME'
$resourceGroupName = 'RGNAME'
$subscriptionId = 'SUBSCRIPTIONID'
$appId = 'APPLICATIONID'
$generateSelfSignedCert = $false
$addNewCertKeyCredential = $true
$certFilePath = 'NEW_CERT_PFX_LOCAL_PATH'
$certPassword = Read-Host "Enter Certificate Password"

if($generateSelfSignedCert)
{
    Write-Host "Generating new SelfSigned certificate"
    
    $cert = New-SelfSignedCertificate -CertStoreLocation "cert:\CurrentUser\My" -Subject "CN=hdinsightAdlsCert" -KeySpec KeyExchange
    $certBytes = $cert.Export([System.Security.Cryptography.X509Certificates.X509ContentType]::Pkcs12, $certPassword);
    $certString = [System.Convert]::ToBase64String($certBytes)
}
else
{

    Write-Host "Reading the cert file from path $certFilePath"

    $cert = new-object System.Security.Cryptography.X509Certificates.X509Certificate2($certFilePath, $certPassword)
    $certString = [System.Convert]::ToBase64String([System.IO.File]::ReadAllBytes($certFilePath))
}


Login-AzureRmAccount

if($addNewCertKeyCredential)
{
    Write-Host "Creating new KeyCredential for the app"

    $keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData())
    
    New-AzureRmADAppCredential -ApplicationId $appId -CertValue $keyValue -EndDate $cert.NotAfter -StartDate $cert.NotBefore

    Write-Host "Waiting for 30 seconds for the permissions to get propagated"

    Start-Sleep -s 30
}

Select-AzureRmSubscription -SubscriptionId $subscriptionId

Write-Host "Updating the certificate on HDInsight cluster."

Invoke-AzureRmResourceAction `
    -ResourceGroupName $resourceGroupName `
    -ResourceType 'Microsoft.HDInsight/clusters' `
    -ResourceName $clusterName `
    -ApiVersion '2015-03-01-preview' `
    -Action 'updateclusteridentitycertificate' `
    -Parameters @{ ApplicationId = $appId.ToString(); Certificate = $certString; CertificatePassword = $certPassword.ToString() } `
    -Force

```

Pokud chcete přiřadit existující certifikát, vytvořte certifikát a připravte si ho na soubor. pfx a heslo. Přidružte certifikát k instančnímu objektu, pomocí kterého byl cluster vytvořen, pomocí AppId připravený.

Po nahrazení parametrů skutečnými hodnotami spusťte příkaz prostředí PowerShell.

## <a name="next-steps"></a>Další kroky

[!INCLUDE [troubleshooting next steps](../../../includes/hdinsight-troubleshooting-next-steps.md)]