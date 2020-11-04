---
title: Ověřování na základě certifikátů pomocí Azure Cosmos DB a služby Active Directory
description: Naučte se konfigurovat identitu Azure AD pro ověřování pomocí certifikátů pro přístup k klíčům z Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.subservice: cosmosdb-sql
ms.topic: how-to
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: e0913351d40cd75da17d16cca119b4ad5ce20de0
ms.sourcegitcommit: fa90cd55e341c8201e3789df4cd8bd6fe7c809a3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93334697"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Ověřování pomocí certifikátu pro identitu Azure AD pro přístup k klíčům z Azure Cosmos DB účtu
[!INCLUDE[appliesto-sql-api](includes/appliesto-sql-api.md)]

Ověřování pomocí certifikátů umožňuje ověřování klientské aplikace pomocí Azure Active Directory (Azure AD) a klientského certifikátu. Ověřování pomocí certifikátů můžete provádět na počítači, na kterém potřebujete identitu, jako je místní počítač nebo virtuální počítač v Azure. Vaše aplikace potom může číst Azure Cosmos DB klíčů bez použití klíčů přímo v aplikaci. Tento článek popisuje, jak vytvořit ukázkovou aplikaci Azure AD, nakonfigurovat ji pro ověřování na základě certifikátů, přihlaste se k Azure pomocí nové identity aplikace a pak načte klíče z vašeho účtu Azure Cosmos. Tento článek používá Azure PowerShell k nastavení identit a poskytuje ukázkovou aplikaci v jazyce C#, která ověřuje a přistupuje k klíčům z vašeho účtu Azure Cosmos.  

## <a name="prerequisites"></a>Předpoklady

* Nainstalujte [nejnovější verzi](/powershell/azure/install-az-ps) Azure PowerShell.

* Pokud ještě nemáte [předplatné Azure](../guides/developer/azure-developer-guide.md#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

## <a name="register-an-app-in-azure-ad"></a>Registrace aplikace ve službě Azure AD

V tomto kroku zaregistrujete ukázkovou webovou aplikaci do svého účtu služby Azure AD. Tato aplikace se později používá ke čtení klíčů z účtu Azure Cosmos DB. K registraci aplikace použijte následující postup: 

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. Otevřete podokno Azure **Active Directory** , přejdete na **Registrace aplikací** podokno a vyberte **Nová registrace**. 

   :::image type="content" source="./media/certificate-based-authentication/new-app-registration.png" alt-text="Nová registrace aplikace ve službě Active Directory":::

1. Vyplňte formulář **aplikace registrem** s následujícími podrobnostmi:  

   * **Název** – zadejte název vaší aplikace, může to být libovolný název, například "dotazů".
   * **Podporované typy účtů** – vyberte **účty v tomto adresáři organizace (výchozí adresář)** , aby se pro přístup k této aplikaci povolily prostředky v aktuálním adresáři. 
   * **Adresa URL pro přesměrování** – vyberte aplikaci typu **Web** a zadejte adresu URL, kde je vaše aplikace hostovaná, může to být libovolná adresa URL. V tomto příkladu můžete zadat testovací URL, jako je to v `https://sampleApp.com` pořádku i v případě, že aplikace neexistuje.

   :::image type="content" source="./media/certificate-based-authentication/register-sample-web-app.png" alt-text="Registrace ukázkové webové aplikace":::

1. Po vyplnění formuláře vyberte **Registrovat** .

1. Po zaregistrování aplikace si poznamenejte **ID aplikace (ID klienta)** a **ID objektu**. Tyto podrobnosti budete používat v dalších krocích. 

   :::image type="content" source="./media/certificate-based-authentication/get-app-object-ids.png" alt-text="Získat ID aplikace a objektu":::

## <a name="install-the-azuread-module"></a>Instalace modulu AzureAD

V tomto kroku nainstalujete modul Azure AD PowerShell. Tento modul je nutný k získání ID aplikace, kterou jste zaregistrovali v předchozím kroku, a k této aplikaci přidružit certifikát podepsaný svým držitelem. 

1. Otevřete Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell s právy správce. Pokud jste to ještě neudělali, nainstalujte modul AZ PowerShell a připojte se k vašemu předplatnému. Pokud máte více předplatných, můžete nastavit kontext aktuálního předplatného, jak je znázorněno v následujících příkazech:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Instalace a import modulu [AzureAD](/powershell/module/azuread/?view=azureadps-2.0&preserve-view=true)

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Přihlaste se k Azure AD

Přihlaste se k Azure AD, kde jste zaregistrovali aplikaci. Pomocí příkazu Connect-AzureAD se přihlaste k účtu, v místním okně zadejte svoje přihlašovací údaje účtu Azure. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Vytvoření certifikátu podepsaného svým držitelem (self-signed certificate)

Otevřete jinou instanci Integrované skriptovací prostředí (ISE) v prostředí Windows PowerShell a spusťte následující příkazy k vytvoření certifikátu podepsaného svým držitelem a přečtěte si klíč přidružený k certifikátu:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Vytvoření přihlašovacích údajů založených na certifikátech 

Dále spusťte následující příkazy, abyste získali ID objektu vaší aplikace a vytvořili přihlašovací údaje založené na certifikátech. V tomto příkladu nastavíme platnost certifikátu na vypršení platnosti po roce, můžete ho nastavit na požadované koncové datum.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Výše uvedený příkaz vede výstup podobný následujícímu snímku obrazovky:

:::image type="content" source="./media/certificate-based-authentication/certificate-based-credential-output.png" alt-text="Výstup vytváření přihlašovacích údajů založený na certifikátech":::

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Konfigurace účtu Azure Cosmos pro použití nové identity

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. Přejděte k účtu Azure Cosmos, otevřete okno **řízení přístupu (IAM)** .

1. Vyberte **Přidat** a **Přidat přiřazení role**. Přidejte dotazů, který jste vytvořili v předchozím kroku, s rolí **přispěvatele** , jak je znázorněno na následujícím snímku obrazovky:

   :::image type="content" source="./media/certificate-based-authentication/configure-cosmos-account-with-identify.png" alt-text="Konfigurace účtu Azure Cosmos pro použití nové identity":::

1. Po vyplnění formuláře vyberte **Save (Uložit** ).

## <a name="register-your-certificate-with-azure-ad"></a>Registrace certifikátu pomocí Azure AD

Přihlašovací údaje založené na certifikátech můžete přidružit k klientské aplikaci v Azure AD z Azure Portal. Chcete-li přidružit přihlašovací údaje, je třeba odeslat soubor certifikátu pomocí následujících kroků:

V registraci aplikace Azure pro klientskou aplikaci:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).

1. Otevřete podokno Azure **Active Directory** , v podokně **Registrace aplikací** a otevřete ukázkovou aplikaci, kterou jste vytvořili v předchozím kroku. 

1. Vyberte **certifikáty & tajných** kódů a pak **Nahrajte certifikát**. Vyhledejte soubor certifikátu, který jste vytvořili v předchozím kroku, a nahrajte ho.

1. Vyberte **Add** (Přidat). Po nahrání certifikátu se zobrazí miniatura, datum zahájení a hodnoty vypršení platnosti.

## <a name="access-the-keys-from-powershell"></a>Přístup k klíčům z PowerShellu

V tomto kroku se přihlásíte k Azure pomocí aplikace a vytvořeného certifikátu a získáte přístup k klíčům účtu Azure Cosmos. 

1. Zpočátku se vymaže přihlašovací údaje účtu Azure, které jste použili k přihlášení k účtu. Přihlašovací údaje můžete vymazat pomocí následujícího příkazu:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Dále ověřte, že se můžete přihlásit k Azure Portal pomocí přihlašovacích údajů aplikace a přistupovat ke klíčům Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

V předchozím příkazu se zobrazí primární a sekundární primární klíč vašeho účtu Azure Cosmos. Můžete zobrazit protokol aktivit účtu Azure Cosmos a ověřit, zda požadavek GET Keys byl úspěšný a událost je iniciována aplikací "dotazů".

:::image type="content" source="./media/certificate-based-authentication/activity-log-validate-results.png" alt-text="Ověření volání Get Keys ve službě Azure AD":::

## <a name="access-the-keys-from-a-c-application"></a>Přístup k klíčům z aplikace v C# 

Tento scénář můžete také ověřit přístupem k klíčům z aplikace v jazyce C#. Následující Konzolová aplikace v jazyce C#, která má přístup k Azure Cosmos DB klíčům, pomocí aplikace zaregistrované ve službě Active Directory. Před spuštěním kódu nezapomeňte aktualizovat tenantId, clientID, CERT, název skupiny prostředků, ID předplatného, informace o názvu účtu Azure Cosmos. 

```csharp
using System;
using Microsoft.IdentityModel.Clients.ActiveDirectory;
using System.Linq;
using System.Net.Http;
using System.Security.Cryptography.X509Certificates;
using System.Threading;
using System.Threading.Tasks;
 
namespace TodoListDaemonWithCert
{
    class Program
    {
        private static string aadInstance = "https://login.windows.net/";
        private static string tenantId = "<Your_Tenant_ID>";
        private static string clientId = "<Your_Client_ID>";
        private static string certName = "<Your_Certificate_Name>";
 
        private static int errorCode = 0;
        static int Main(string[] args)
        {
            MainAync().Wait();
            Console.ReadKey();
 
            return 0;
        } 
 
        static async Task MainAync()
        {
            string authContextURL = aadInstance + tenantId;
            AuthenticationContext authContext = new AuthenticationContext(authContextURL);
            X509Certificate2 cert = ReadCertificateFromStore(certName);
 
            ClientAssertionCertificate credential = new ClientAssertionCertificate(clientId, cert);
            AuthenticationResult result = await authContext.AcquireTokenAsync("https://management.azure.com/", credential);
            if (result == null)
            {
                throw new InvalidOperationException("Failed to obtain the JWT token");
            }
 
            string token = result.AccessToken;
            string subscriptionId = "<Your_Subscription_ID>";
            string rgName = "<ResourceGroup_of_your_Cosmos_account>";
            string accountName = "<Your_Cosmos_account_name>";
            string cosmosDBRestCall = $"https://management.azure.com/subscriptions/{subscriptionId}/resourceGroups/{rgName}/providers/Microsoft.DocumentDB/databaseAccounts/{accountName}/listKeys?api-version=2015-04-08";
 
            Uri restCall = new Uri(cosmosDBRestCall);
            HttpClient httpClient = new HttpClient();
            httpClient.DefaultRequestHeaders.Remove("Authorization");
            httpClient.DefaultRequestHeaders.Add("Authorization", "Bearer " + token);
            HttpResponseMessage response = await httpClient.PostAsync(restCall, null);
 
            Console.WriteLine("Got result {0} and keys {1}", response.StatusCode.ToString(), response.Content.ReadAsStringAsync().Result);
        }
 
        /// <summary>
        /// Reads the certificate
        /// </summary>
        private static X509Certificate2 ReadCertificateFromStore(string certName)
        {
            X509Certificate2 cert = null;
            X509Store store = new X509Store(StoreName.My, StoreLocation.CurrentUser);
            store.Open(OpenFlags.ReadOnly);
            X509Certificate2Collection certCollection = store.Certificates;
 
            // Find unexpired certificates.
            X509Certificate2Collection currentCerts = certCollection.Find(X509FindType.FindByTimeValid, DateTime.Now, false);
 
            // From the collection of unexpired certificates, find the ones with the correct name.
            X509Certificate2Collection signingCert = currentCerts.Find(X509FindType.FindBySubjectName, certName, false);
 
            // Return the first certificate in the collection, has the right name and is current.
            cert = signingCert.OfType<X509Certificate2>().OrderByDescending(c => c.NotBefore).FirstOrDefault();
            store.Close();
            return cert;
        }
    }
}
```

Tento skript vytvoří výstup primárních a sekundárních primárních klíčů, jak je znázorněno na následujícím snímku obrazovky:

:::image type="content" source="./media/certificate-based-authentication/csharp-application-output.png" alt-text="výstup aplikace CSharp":::

Podobně jako v předchozí části si můžete zobrazit protokol aktivit účtu Azure Cosmos a ověřit, že aplikace "dotazů" iniciuje událost žádosti o získání klíčů. 


## <a name="next-steps"></a>Další kroky

* [Zabezpečení klíčů Azure Cosmos s využitím služby Azure Key Vault](access-secrets-from-keyvault.md)

* [Základní hodnoty zabezpečení pro Azure Cosmos DB](security-baseline.md)