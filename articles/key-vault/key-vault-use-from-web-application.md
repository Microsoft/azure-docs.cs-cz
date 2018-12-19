---
title: Kurz použití služby Azure Key Vault z webové aplikace | Microsoft Docs
description: Tento kurz vám pomůže naučit se používat službu Azure Key Vault z webové aplikace.
services: key-vault
author: barclayn
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: tutorial
ms.date: 10/09/2018
ms.author: barclayn
ms.openlocfilehash: 272238e41327e09af8e4d3967868c21c37683236
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/19/2018
ms.locfileid: "53602046"
---
# <a name="tutorial-use-azure-key-vault-from-a-web-application"></a>Kurz: Použití Azure Key Vault z webové aplikace

Tento kurz vám pomůže naučit se používat službu Azure Key Vault z webové aplikace v Azure. Ukazuje postup při přístupu k tajnému klíči ze služby Azure Key Vault pro použití ve webové aplikaci. Kurz pak tento prostup rozšiřuje a místo tajného klíče klienta používá certifikát. Tento kurz je určený pro webové vývojáře se základními znalostmi vytváření webových aplikací v Azure.

V tomto kurzu se naučíte: 

> [!div class="checklist"]
> * Přidání nastavení aplikace do souboru web.config
> * Přidání metody pro získání přístupového tokenu
> * Načtení tokenu při spuštění aplikace
> * Ověřování pomocí certifikátu

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete následující položky:

* Identifikátor URI pro tajný klíč ve službě Azure Key Vault
* ID klienta a tajný klíč klienta pro webovou aplikaci zaregistrovanou v Azure Active Directory, která má přístup k vaší službě Key Vault
* Webová aplikace. Tento kurz obsahuje kroky pro aplikaci ASP.NET MVC nasazenou v Azure jako webová aplikace.

Proveďte kroky v tématu [Začínáme s Azure Key Vault](key-vault-get-started.md) a získejte identifikátor URI pro tajný klíč, ID klienta a tajný klíč klienta a zaregistrujte aplikaci. Webová aplikace bude mít přístup k trezoru a musí být zaregistrovaná v Azure Active Directory. Kromě toho musí mít přístupová práva ke službě Key Vault. Pokud tomu tak není, vraťte se k části Registrace aplikace v kurzu Začínáme a zopakujte uvedené kroky. Další informace o vytvoření služby Azure Web Apps najdete v [přehledu Web Apps](../app-service/overview.md).

Tato ukázka se spoléhá na ruční zřizování identit Azure Active Directory. Vy byste ale měli místo toho použít [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md), které umožňují automatické zřizování identit Azure AD. Další informace najdete v [ukázce na GitHubu](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) a v souvisejícím [kurzu o službách App Service a Functions](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). Můžete se taky podívat na kurz [Konfigurace webové aplikace Azure pro čtení tajného kódu ze služby Key Vault](tutorial-web-application-keyvault.md), který se týká konkrétně služby Key Vault.

## <a id="packages"></a>Přidání balíčků NuGet

Vaše webová aplikace musí mít nainstalované dva balíčky.

* Active Directory Authentication Library obsahuje metody pro práci s Azure Active Directory a správu identity uživatele.
* Knihovna služby Azure Key Vault obsahuje metody pro práci se službou Azure Key Vault.

Oba tyto balíčky můžete nainstalovat pomocí konzoly Správce balíčků a příkazu Install-Package.

```powershell
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory 
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Úprava souboru web.config

Do souboru web.config je potřeba následujícím způsobem přidat tři nastavení aplikace. Abychom zajistili další úroveň zabezpečení, přidáme skutečné hodnoty na webu Azure Portal.

```xml
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
    <!-- If you aren't hosting your app as an Azure Web App, then you should use the actual ClientId, Client Secret, and Secret URI values -->
```



## <a id="gettoken"></a>Přidání metody pro získání přístupového tokenu

Pokud chcete používat rozhraní API služby Key Vault, potřebujete přístupový token. Volání do rozhraní API služby Key Vault zajišťuje klient služby Key Vault. Musíte mu však vytvořit funkci, která získá přístupový token. V následujícím příkladu je kód pro získání přístupového tokenu z Azure Active Directory. Tento kód můžete v rámci aplikace umístit kamkoli. Doporučujeme přidat třídu Utils nebo EncryptionHelper a kód vložit tam.  

```cs
//add these using statements
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Threading.Tasks;
using System.Web.Configuration;

//this is an optional property to hold the secret after it is retrieved
public static string EncryptSecret { get; set; }

//the method that will be provided to the KeyVaultClient
public static async Task<string> GetToken(string authority, string resource, string scope)
{
    var authContext = new AuthenticationContext(authority);
    ClientCredential clientCred = new ClientCredential(WebConfigurationManager.AppSettings["ClientId"],
                WebConfigurationManager.AppSettings["ClientSecret"]);
    AuthenticationResult result = await authContext.AcquireTokenAsync(resource, clientCred);

    if (result == null)
        throw new InvalidOperationException("Failed to obtain the JWT token");

    return result.AccessToken;
}
// Using Client ID and Client Secret is a way to authenticate an Azure AD application.
// Using it in your web application allows for a separation of duties and more control over your key management. 
// However, it does rely on putting the Client Secret in your configuration settings.
// For some people, this can be as risky as putting the secret in your configuration settings.
```

## <a id="appstart"></a>Načtení tajného klíče při spuštění aplikace

Teď potřebujeme kód, který zavolá rozhraní API služby Key Vault a načte tajný klíč. Následující kód můžete umístit kamkoli za předpokladu, že se zavolá před tím, než ho budete potřebovat použít. V tomto příkladu jsme tento kód umístili do události spuštění aplikace v souboru Global.asax, takže se spustí jednou při spuštění a zpřístupní tajný klíč aplikaci.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Přidání nastavení aplikace na webu Azure Portal

Do webové aplikace Azure teď můžete na webu Azure Portal přidat skutečné hodnoty pro nastavení aplikace. Provedením tohoto kroku zajistíte, že skutečné hodnoty nebudou v souboru web.config, ale chráněné přes portál, který nabízí možnosti odděleného řízení přístupu. Těmito hodnotami se nahradí hodnoty, které jste zadali do souboru web.config. Ujistěte se, že používáte stejné názvy.

![Zobrazená nastavení aplikace na webu Azure Portal][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Ověřování pomocí certifikátu místo tajného klíče klienta

Teď, když rozumíte ověřování aplikace Azure AD pomocí ID klienta a tajného klíče klienta, použijeme ID klienta a certifikát. Pokud chcete ve webové aplikaci Azure použít certifikát, proveďte následující kroky:

1. Získání nebo vytvoření certifikátu
2. Přidružení certifikátu k aplikaci Azure AD
3. Přidání kódu do webové aplikace, který umožní používání certifikátu
4. Přidání certifikátu do webové aplikace

### <a name="get-or-create-a-certificate"></a>Získání nebo vytvoření certifikátu

 Pro účely tohoto kurzu vytvoříme testovací certifikát. Tady je skript pro vytvoření certifikátu podepsaného svým držitelem. Přejděte do adresáře, ve kterém chcete soubory certifikátu vytvořit.  Jako počáteční a koncové datum platnosti certifikátu můžete použít aktuální datum plus jeden rok.

```powershell
#Create self-signed certificate and export pfx and cer files 
$PfxFilePath = 'KVWebApp.pfx'
$CerFilePath = 'KVWebApp.cer'
$DNSName = 'MyComputer.Contoso.com'
$Password = 'MyPassword"'

$StoreLocation = 'CurrentUser' #be aware that LocalMachine requires elevated privileges
$CertBeginDate = Get-Date
$CertExpiryDate = $CertBeginDate.AddYears(1)

$SecStringPw = ConvertTo-SecureString -String $Password -Force -AsPlainText 
$Cert = New-SelfSignedCertificate -DnsName $DNSName -CertStoreLocation "cert:\$StoreLocation\My" -NotBefore $CertBeginDate -NotAfter $CertExpiryDate -KeySpec Signature
Export-PfxCertificate -cert $Cert -FilePath $PFXFilePath -Password $SecStringPw 
Export-Certificate -cert $Cert -FilePath $CerFilePath 
```

Poznamenejte si datum ukončení a heslo pro soubor .pfx (v tomto příkladu: 15. května 2019 a heslo). Budete je potřebovat pro následující skript. 
### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Přidružení certifikátu k aplikaci Azure AD

Teď, když máte certifikát, ho musíte přidružit k aplikaci Azure AD. Přidružení můžete provést prostřednictvím PowerShellu. Spuštěním následujících příkazů přidružte certifikát k aplikaci Azure AD:

```powershell
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

Po spuštění těchto příkazů se aplikace zobrazí v Azure AD. Při vyhledávání registrací aplikací nezapomeňte v dialogovém okně hledání vybrat **Moje aplikace**, a ne Všechny aplikace. 

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Přidání kódu do webové aplikace, který umožní používání certifikátu

Teď do webové aplikace přidáme kód pro přístup k certifikátu a jeho použití k ověřování. 

Nejprve přidáme kód pro přístup k certifikátu. Všimněte si, že StoreLocation má hodnotu CurrentUser, a ne LocalMachine. Také si všimněte, že pro metodu Find zadáváme hodnotu false, protože používáme testovací certifikát.

```cs
//Add this using statement
using System.Security.Cryptography.X509Certificates;  

public static class CertificateHelper
{
    public static X509Certificate2 FindCertificateByThumbprint(string findValue)
    {
        X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
        try
        {
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection col = store.Certificates.Find(X509FindType.FindByThumbprint,
                findValue, false); // Don't validate certs, since the test root isn't installed.
            if (col == null || col.Count == 0)
                return null;
            return col[0];
        }
        finally
        {
            store.Close();
        }
    }
}
```



Následuje kód, který využívá třídu CertificateHelper a vytváří certifikát ClientAssertionCertificate potřebný k ověřování.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Tady je nový kód pro získání přístupového tokenu. Tento kód nahradí metodu GetToken v předchozím příkladu. Pro usnadnění práce jsme použili jiný název. Z důvodu snadnějšího použití jsme veškerý tento kód umístili do třídy Utils v projektu webové aplikace.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```



Poslední změna kódu se týká metody Application_Start. Nejprve musíme zavolat metodu GetCert(), která načte certifikát ClientAssertionCertificate. Pak změníme metodu zpětného volání, kterou zadáme při vytváření nového klienta KeyVaultClient. Tento kód nahradí kód, který jsme měli v předchozím příkladu.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Přidání certifikátu do webové aplikace prostřednictvím webu Azure Portal

Přidání certifikátu do webové aplikace je jednoduchý dvoustupňový proces. Nejprve přejděte na web Azure Portal a přejděte do vaší webové aplikace. V nastavení vaší webové aplikace klikněte na položku **Nastavení SSL**. Až se Nastavení SSL otevře, nahrajte certifikát KVWebApp.pfx, který jste vytvořili v předchozím příkladu. Ujistěte se, že si pamatujete heslo pro daný soubor .pfx.

![Přidání certifikátu do webové aplikace na webu Azure Portal][2]

Poslední věcí, kterou je potřeba udělat, je přidat do webové aplikace nastavení aplikace s názvem WEBSITE\_LOAD\_CERTIFICATES a hodnotou *. Tímto krokem zajistíte načtení všech certifikátů. Pokud chcete načíst pouze certifikáty, které jste nahráli, zadejte čárkami oddělený seznam jejich kryptografických otisků.


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud už službu App Service, trezor klíčů a aplikaci Azure AD použité v tomto kurzu nepotřebujete, odstraňte je.  


## <a id="next"></a>Další kroky
> [!div class="nextstepaction"]
>[Referenční informace k rozhraní API pro správu služby Azure Key Vault](/dotnet/api/overview/azure/keyvault/management)


<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
 