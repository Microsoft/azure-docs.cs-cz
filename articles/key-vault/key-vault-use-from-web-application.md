---
title: Použití Azure Key Vault z webové aplikace | Microsoft Docs
description: Pomocí tohoto kurzu můžete Naučte se používat Azure Key Vault z webové aplikace.
services: key-vault
author: adhurwit
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 9b7d065e-1979-4397-8298-eeba3aec4792
ms.service: key-vault
ms.workload: identity
ms.topic: article
ms.date: 05/10/2018
ms.author: adhurwit
ms.openlocfilehash: de8276b22377db37075c6793d0710bb18625446c
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="use-azure-key-vault-from-a-web-application"></a>Použití Azure Key Vault z webové aplikace

## <a name="introduction"></a>Úvod

Pomocí tohoto kurzu můžete Naučte se používat Azure Key Vault z webové aplikace v Azure. Provede vás procesem přístup k tajného klíče z Azure Key Vault, takže je možné ve webové aplikaci.

**Odhadovaný čas dokončení:** 15 minut

Souhrnné informace o Azure Key Vault naleznete v tématu [Co je Azure Key Vault?](key-vault-whatis.md).

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete:

* Identifikátor URI pro tajný klíč v Azure Key Vault
* ID klienta a tajný klíč klienta pro webovou aplikaci, které jsou zaregistrované v Azure Active Directory, který má přístup k trezoru klíč
* Webová aplikace. Jsme se, že se zobrazuje kroky pro aplikaci ASP.NET MVC nasazené v Azure jako webovou aplikaci.

>[!IMPORTANT]
>* Tato ukázka závisí na starší způsob, jak ručně zřizování identit AAD. V současné době je nová funkce ve verzi preview názvem [identita spravované služby (MSI)](https://docs.microsoft.com/azure/active-directory/msi-overview), které mohou automaticky poskytovat identit AAD. Naleznete v následujícím příkladu v [Githubu](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) další podrobnosti.

> [!NOTE]
>* Je nezbytné, že jste dokončili kroky uvedené v [Začínáme s Azure Key Vault](key-vault-get-started.md) pro účely tohoto kurzu tak, aby měli identifikátor URI tajného klíče a ID klienta a tajný klíč klienta pro webovou aplikaci.


Webovou aplikaci, která bude mít přístup k Key Vault je ten, který je zaregistrován ve službě Azure Active Directory a byl poskytnut přístup k trezoru klíč. Pokud tomu tak není, přejděte zpět na zaregistrovat aplikaci v kurzu Začínáme a opakujte kroky uvedené.

Tento kurz je určen pro vývojářům webů, které pochopit základy toho vytváření webových aplikací v Azure. Další informace o službě Azure Web Apps, naleznete v části [přehled Web Apps](../app-service/app-service-web-overview.md).

## <a id="packages"></a>Přidání balíčků NuGet

Jsou dva balíčky, které webové aplikace musí mít nainstalovaný.

* Knihovna ověřování Active Directory - obsahuje metody pro interakci s Azure Active Directory a správa identity uživatele
* Azure Key Vault Library - obsahuje metody pro interakci s Azure Key Vault

Obě tyto balíčky můžete nainstalovat pomocí konzoly Správce balíčků pomocí příkazu Install-Package.

```
// this is currently the latest stable version of ADAL
Install-Package Microsoft.IdentityModel.Clients.ActiveDirectory -Version 2.16.204221202
Install-Package Microsoft.Azure.KeyVault
```

## <a id="webconfig"></a>Upravit soubor Web.Config

Existují tři nastavení aplikace, které je třeba přidat do souboru web.config následujícím způsobem.

```
    <!-- ClientId and ClientSecret refer to the web application registration with Azure Active Directory -->
    <add key="ClientId" value="clientid" />
    <add key="ClientSecret" value="clientsecret" />

    <!-- SecretUri is the URI for the secret in Azure Key Vault -->
    <add key="SecretUri" value="secreturi" />
```

Pokud nechcete kvůli hostování vaší aplikace jako webové aplikace Azure, měli byste k souboru web.config přidat skutečnými hodnotami ClientId, sdílený tajný klíč klienta a identifikátor URI tajného klíče. V opačném případě ponechte tyto fiktivní hodnoty, protože jsme přidáním skutečnými hodnotami na portálu Azure vytváří další úroveň zabezpečení.

## <a id="gettoken"></a>Přidejte metodu k získání tokenu přístupu

Chcete-li použít rozhraní API trezoru klíč musíte přístupový token. Klient klíče trezoru zpracovává volání do rozhraní API Key Vault, ale budete muset zadat pomocí funkce, který získá přístupový token.  

Následuje kód slouží k získání tokenu přístupu z Azure Active Directory. Tento kód můžete přejít kdekoli v aplikaci. Líbí se přidání Utils nebo EncryptionHelper třídy.  

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
```

> [!NOTE]
>* Nejjednodušší způsob ověřování v současnosti představuje nová funkce identity spravované služby (MSI). Další podrobnosti vám poskytne následující odkaz na ukázku s použití služby [Key Vault s využitím MSI v aplikaci v .NET](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet/) a související [kurz k MSI se službami App Service a Functions](https://docs.microsoft.com/azure/app-service/app-service-managed-service-identity). 
>* Pomocí ID klienta a tajný klíč klienta je další způsob ověření aplikaci Azure AD. A použití ve vaší webové aplikace je možné oddělení povinností a větší kontrolu nad vaší správy klíčů. Je však závislý na uvedení tajný klíč klienta v nastavení konfigurace, které pro některé můžou být jako rizikové jako uvedení tajný klíč, který chcete chránit v nastavení konfigurace. Najdete zde informace o tom, jak použít ID klienta a certifikát místo ID klienta a tajný klíč klienta k ověření aplikace Azure AD.

## <a id="appstart"></a>Načtení tajný klíč na spuštění aplikace

Nyní potřebujeme kódu pro volání rozhraní API Key Vault a načítání tajného klíče. Následující kód můžete umístit kdekoli, tak dlouho, dokud se označuje jako předtím, než je nutné ji použít. Tento kód v události spustit aplikace v soubor Global.asax mít umístíte tak, aby při spuštění se spustí jednou a zpřístupní tajný klíč pro aplikaci.

```cs
//add these using statements
using Microsoft.Azure.KeyVault;
using System.Web.Configuration;

// I put my GetToken method in a Utils class. Change for wherever you placed your method.
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetToken));
var sec = await kv.GetSecretAsync(WebConfigurationManager.AppSettings["SecretUri"]);

//I put a variable in a Utils class to hold the secret for general  application use.
Utils.EncryptSecret = sec.Value;
```

## <a id="portalsettings"></a>Přidání nastavení aplikace v portálu Azure (volitelné)

Pokud máte webové aplikace Azure, můžete nyní přidat skutečnými hodnotami pro AppSettings na portálu Azure. Díky tomu skutečnými hodnotami nebude v souboru web.config, ale chráněná přes portál, kde můžete dělat samostatnou přístupovou ovládacího prvku. Tyto hodnoty se nahradí hodnoty, které jste zadali v souboru web.config. Ujistěte se, že názvy jsou stejné.

![Nastavení aplikace se zobrazí na portálu Azure][1]

## <a name="authenticate-with-a-certificate-instead-of-a-client-secret"></a>Ověřování pomocí certifikátu místo tajný klíč klienta

Jiný způsob, jak ověřit aplikaci Azure AD je pomocí ID klienta a certifikát místo ID klienta a tajný klíč klienta. Toto jsou kroky pro použití certifikátu ve webové aplikaci Azure:

1. Získat nebo vytvořit certifikát
2. Certifikát přidružit aplikaci Azure AD
3. Přidání kódu do vaší webové aplikace na použití certifikátu
4. Přidat certifikát do vaší webové aplikace

### <a name="get-or-create-a-certificate"></a>Získat nebo vytvořit certifikát

Pro naše účely budeme testovacího certifikátu. Tady je několik příkazů, které můžete použít v příkazovém řádku vývojáře vytvořit certifikát. Změňte adresář na místo, kam chcete vytvořené soubory certifikátu.  Navíc pro počáteční a koncové datum platnosti certifikátu použijte aktuální datum plus 1 rok.

```
makecert -sv mykey.pvk -n "cn=KVWebApp" KVWebApp.cer -b 07/31/2017 -e 07/31/2018 -r
pvk2pfx -pvk mykey.pvk -spc KVWebApp.cer -pfx KVWebApp.pfx -po test123
```

Poznamenejte si koncové datum a heslo .pfx (v tomto příkladu: 07/31/2018 a test123). Je nutné je níže.

Další informace o vytvoření testovacího certifikátu najdete v tématu [postup: vytvořit vaše vlastní testovací certifikát](https://msdn.microsoft.com/library/ff699202.aspx)

### <a name="associate-the-certificate-with-an-azure-ad-application"></a>Certifikát přidružit aplikaci Azure AD

Teď, když máte certifikát, musíte přidružit aplikaci Azure AD. Na portálu Azure v současné době nepodporuje tento pracovní postup; To lze provést pomocí prostředí PowerShell. Spusťte následující příkazy, které certifikát přidružit aplikaci Azure AD:

```ps
$x509 = New-Object System.Security.Cryptography.X509Certificates.X509Certificate2
$x509.Import("C:\data\KVWebApp.cer")
$credValue = [System.Convert]::ToBase64String($x509.GetRawCertData())


$adapp = New-AzureRmADApplication -DisplayName "KVWebApp" -HomePage "http://kvwebapp" -IdentifierUris "http://kvwebapp" -CertValue $credValue -StartDate $x509.NotBefore -EndDate $x509.NotAfter


$sp = New-AzureRmADServicePrincipal -ApplicationId $adapp.ApplicationId


Set-AzureRmKeyVaultAccessPolicy -VaultName 'contosokv' -ServicePrincipalName "http://kvwebapp" -PermissionsToSecrets all -ResourceGroupName 'contosorg'

# get the thumbprint to use in your app settings
$x509.Thumbprint
```

Po spuštění těchto příkazů se zobrazí aplikace ve službě Azure AD. Při hledání, zkontrolujte, že jste vybrali "Moje společnost vlastní aplikace" místo "Aplikace společnost používá" v dialogovém okně hledání.

Další informace o objektech aplikace Azure AD a ServicePrincipal objektů najdete v tématu [objekty aplikací a hlavní objekty služeb](../active-directory/active-directory-application-objects.md).

### <a name="add-code-to-your-web-app-to-use-the-certificate"></a>Přidání kódu do vaší webové aplikace na použití certifikátu

Teď přidáme kódu vaší webové aplikace na přístup certifikát a použít jej pro ověřování.

Nejprve je kód pro přístup k certifikát.

```cs
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

Všimněte si, že je StoreLocation CurrentUser místo LocalMachine. A že jsme se dodává false metodu najít vzhledem k tomu, že používáme testovací certifikát.

Dále je kód, který používá CertificateHelper a vytvoří ClientAssertionCertificate, která je potřebná pro ověřování.

```cs
public static ClientAssertionCertificate AssertionCert { get; set; }

public static void GetCert()
{
    var clientAssertionCertPfx = CertificateHelper.FindCertificateByThumbprint(WebConfigurationManager.AppSettings["thumbprint"]);
    AssertionCert = new ClientAssertionCertificate(WebConfigurationManager.AppSettings["clientid"], clientAssertionCertPfx);
}
```

Tady je nový kód slouží k získání tokenu přístupu. Tím se nahradí gettoken – metoda v předchozím příkladu. Uvedené ho jiný název pro usnadnění práce.

```cs
public static async Task<string> GetAccessToken(string authority, string resource, string scope)
{
    var context = new AuthenticationContext(authority, TokenCache.DefaultShared);
    var result = await context.AcquireTokenAsync(resource, AssertionCert);
    return result.AccessToken;
}
```

I všechny tohoto kódu umístili do projektu webové aplikace Utils třídy pro snadné použití.

Do metody Application_Start je poslední změny kódu. Je potřeba nejdřív voláním metody GetCert() zatížení ClientAssertionCertificate. A pak se nám změnit metoda zpětného volání, které jsme zadat při vytváření nové KeyVaultClient. Všimněte si, že tím se nahradí kód, který jsme měli v předchozím příkladu.

```cs
Utils.GetCert();
var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(Utils.GetAccessToken));
```

### <a name="add-a-certificate-to-your-web-app-through-the-azure-portal"></a>Přidat certifikát do vaší webové aplikace prostřednictvím portálu Azure

Přidání certifikátu do vaší webové aplikace je jednoduchý dvoustupňový proces. První přejděte na portál Azure a přejděte do vaší webové aplikace. V okně nastavení pro webové aplikace klikněte na položku "vlastní domény a SSL". V okně, otevře se okno, které bude možné nahrát certifikát, který jste vytvořili v předchozím příkladu KVWebApp.pfx, ujistěte se, že si pamatujete heslo pro soubor pfx.

![Přidání certifikátu do webové aplikace na portálu Azure][2]

Poslední věcí, kterou je potřeba udělat je přidat nastavení aplikace do webové aplikace, který má název webu\_zatížení\_certifikáty a hodnotu *. Tím bude zajištěno, že se načtou všechny certifikáty. Pokud chcete načíst jenom certifikáty, které jste odeslali, můžete zadat seznam jejich kryptografické otisky oddělených čárkami.

Další informace o přidání certifikátu do webové aplikace najdete v tématu [pomocí certifikátů v aplikacích weby Azure](https://azure.microsoft.com/blog/2014/10/27/using-certificates-in-azure-websites-applications/)

### <a name="add-a-certificate-to-key-vault-as-a-secret"></a>Přidat certifikát do Key Vault jako tajný klíč

Místo přímo nahrát certifikát služby webové aplikace, můžete ukládat v Key Vault jako tajný klíč a nasadit ho z ní. Toto je popsané v tomto příspěvku blogu ve dvou krocích [nasazení Azure certifikát webové aplikace prostřednictvím Key Vault](https://blogs.msdn.microsoft.com/appserviceteam/2016/05/24/deploying-azure-web-app-certificate-through-key-vault/)

## <a id="next"></a>Další kroky

Programátorské reference najdete v části [Azure Key Vault C# klienta referenční dokumentace rozhraní API](https://msdn.microsoft.com/library/azure/dn903628.aspx).

<!--Image references-->
[1]: ./media/key-vault-use-from-web-application/PortalAppSettings.png
[2]: ./media/key-vault-use-from-web-application/PortalAddCertificate.png
