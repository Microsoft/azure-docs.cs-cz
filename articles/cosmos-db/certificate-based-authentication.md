---
title: Ověřování založené na certifikátech pomocí Azure Cosmos DB a Active Directory
description: Zjistěte, jak nakonfigurovat identitu Azure AD pro ověřování na základě certifikátu pro přístup ke klíčům z Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: 085280a8064e4d12ac63939ada7cdb296d47dc70
ms.sourcegitcommit: 07d62796de0d1f9c0fa14bfcc425f852fdb08fb1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80365777"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Ověřování na základě certifikátu pro identitu Azure AD pro přístup ke klíčům z účtu Azure Cosmos DB

Ověřování pomocí certifikátů umožňuje ověřování klientské aplikace pomocí Azure Active Directory (Azure AD) a klientského certifikátu. Ověřování pomocí certifikátů můžete provádět na počítači, na kterém potřebujete identitu, jako je místní počítač nebo virtuální počítač v Azure. Vaše aplikace pak můžete číst klíče Azure Cosmos DB bez nutnosti klíče přímo v aplikaci. Tento článek popisuje, jak vytvořit ukázkovou aplikaci Azure AD, nakonfigurovat ji pro ověřování na základě certifikátů, přihlásit se do Azure pomocí nové identity aplikace a pak načte klíče z vašeho účtu Azure Cosmos. Tento článek používá Azure PowerShell k nastavení identit a poskytuje ukázkovou aplikaci C#, která ověřuje a přistupuje ke klíčům z vašeho účtu Azure Cosmos.  

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [nejnovější verzi](/powershell/azure/install-az-ps) Azure PowerShellu.

* Pokud ještě nemáte [předplatné Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) před tím, než začnete.

## <a name="register-an-app-in-azure-ad"></a>Registrace aplikace ve službě Azure AD

V tomto kroku zaregistrujete ukázkovou webovou aplikaci ve vašem účtu Azure AD. Tato aplikace se později používá ke čtení klíčů z vašeho účtu Azure Cosmos DB. K registraci aplikace použijte následující kroky: 

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Otevřete podokno **Služby Azure Active Directory,** přejděte do podokna **Registrace aplikací** a vyberte **Nová registrace**. 

   ![Registrace nové aplikace ve službě Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Vyplňte **formulář Registrace žádosti** s následujícími údaji:  

   * **Název** - Zadejte název pro vaši aplikaci, může to být libovolný název, jako je "sampleApp".
   * **Podporované typy účtů** – Zvolte **účty v tomto organizačním adresáři pouze (výchozí adresář),** chcete-li povolit prostředkům v aktuálním adresáři přístup k této aplikaci. 
   * **Přesměrování URL** – Zvolte aplikaci typu **Web** a zadejte adresu URL, kde je aplikace hostována, může to být libovolná adresa URL. V tomto příkladu můžete zadat testovací `https://sampleApp.com` adresu URL, například je to v pořádku, i když aplikace neexistuje.

   ![Registrace ukázkové webové aplikace](./media/certificate-based-authentication/register-sample-web-app.png)

1. Po vyplnění formuláře vyberte **Registrovat.**

1. Po registraci aplikace si poznamenejte **ID aplikace(klienta)** a **ID objektu**, tyto podrobnosti použijete v dalších krocích. 

   ![Získání ID aplikace a objektu](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Instalace modulu AzureAD

V tomto kroku nainstalujete modul Azure AD PowerShell. Tento modul je nutný k získání ID aplikace, kterou jste zaregistrovali v předchozím kroku, a přidružení certifikátu podepsaného svým držitelem k této aplikaci. 

1. Sem otevřete prostředí Windows PowerShell ISE s právy správce. Pokud jste to ještě neudělali, nainstalujte modul AZ PowerShell a připojte se k předplatnému. Pokud máte více předplatných, můžete nastavit kontext aktuálního předplatného, jak je znázorněno v následujících příkazech:

   ```powershell
   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Instalace a import modulu [AzureAD](/powershell/module/azuread/?view=azureadps-2.0)

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Přihlášení do azure ad

Přihlaste se do služby Azure AD, kde jste zaregistrovali aplikaci. Pomocí příkazu Connect-AzureAD se přihlaste ke svému účtu a zadejte přihlašovací údaje účtu Azure do vyskakovacího okna. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)

Otevřete jinou instanci prostředí Windows PowerShell ISE a spusťte následující příkazy k vytvoření certifikátu podepsaného svým držitelem a přečtení klíče přidruženého k certifikátu:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Vytvoření pověření založeného na certifikátu 

Dále spusťte následující příkazy, abyste získali ID objektu aplikace a vytvořili pověření založená na certifikátu. V tomto příkladu jsme nastavili platnost certifikátu po roce, můžete jej nastavit na libovolné požadované koncové datum.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Výše uvedený příkaz má za následek výstup podobný následujícímu snímku obrazovky:

![Výstup vytvoření pověření založené na certifikátu](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Konfigurace účtu Azure Cosmos tak, aby používal novou identitu

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Přejděte na svůj účet Azure Cosmos a otevřete okno **řízení přístupu (IAM).**

1. Vyberte **Přidat** a **přidat přiřazení role**. Přidejte ukázkovou aplikaci, kterou jste vytvořili v předchozím kroku, s rolí **přispěvatele,** jak je znázorněno na následujícím snímku obrazovky:

   ![Konfigurace účtu Azure Cosmos pro použití nové identity](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Po vyplnění formuláře vyberte **Uložit.**

## <a name="register-your-certificate-with-azure-ad"></a>Registrace certifikátu ve službě Azure AD

Přihlašovací údaje založené na certifikátu můžete přidružit ke klientské aplikaci ve službě Azure AD z webu Azure Portal. Chcete-li pověření přidružit, je nutné nahrát soubor certifikátu pomocí následujících kroků:

V registraci aplikace Azure pro klientskou aplikaci:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).

1. Otevřete podokno **Služby Azure Active Directory,** přejděte do podokna **Registrace aplikací** a otevřete ukázkovou aplikaci, kterou jste vytvořili v předchozím kroku. 

1. Vyberte **certifikáty & tajných kódů** a potom **nahrát certifikát**. Projděte soubor certifikátu, který jste vytvořili v předchozím kroku, který chcete nahrát.

1. Vyberte **Přidat**. Po nahrání certifikátu se zobrazí kryptografický otisk, počáteční datum a hodnoty vypršení platnosti.

## <a name="access-the-keys-from-powershell"></a>Přístup ke klíčům z PowerShellu

V tomto kroku se přihlásíte do Azure pomocí aplikace a certifikátu, který jste vytvořili, a získáte přístup ke klíčům účtu Azure Cosmos. 

1. Zpočátku zrušte přihlašovací údaje účtu Azure, které jste použili k přihlášení k účtu. Pověření můžete vymazat pomocí následujícího příkazu:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Další ověření, že se můžete přihlásit k portálu Azure pomocí přihlašovacích údajů aplikace a přístup ke klíčům Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Get-AzCosmosDBAccountKey `
      -ResourceGroupName "<Resource_Group_Name_of_your_Azure_Cosmos_account>" `
      -Name "<Your_Azure_Cosmos_Account_Name>" `
      -Type "Keys"
   ```

Předchozí příkaz zobrazí primární a sekundární hlavní klíče vašeho účtu Azure Cosmos. Můžete zobrazit protokol aktivit vašeho účtu Azure Cosmos k ověření, že požadavek získat klíče proběhl úspěšně a událost je iniciována aplikací "sampleApp".

![Ověření volání get klíčů ve službě Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)

## <a name="access-the-keys-from-a-c-application"></a>Přístup ke klíčům z aplikace C# 

Tento scénář můžete také ověřit přístupem ke klíčům z aplikace jazyka C#. Následující konzolová aplikace C#, která má přístup ke klíčům Azure Cosmos DB pomocí aplikace registrované ve službě Active Directory. Ujistěte se, že aktualizovat tenantId, clientID, certName, název skupiny prostředků, ID předplatného, Podrobnosti o názvu účtu Azure Cosmos před spuštěním kódu. 

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

Tento skript vyvádí primární a sekundární hlavní klávesy, jak je znázorněno na následujícím snímku obrazovky:

![csharp výstup aplikace](./media/certificate-based-authentication/csharp-application-output.png)

Podobně jako v předchozí části můžete zobrazit protokol aktivit vašeho účtu Azure Cosmos k ověření, že událost požadavku získat klíče je iniciována aplikací "sampleApp". 


## <a name="next-steps"></a>Další kroky

* [Zabezpečení klíčů Azure Cosmos s využitím služby Azure Key Vault](access-secrets-from-keyvault.md)

* [Základní plán zabezpečení pro Azure Cosmos DB](security-baseline.md)
