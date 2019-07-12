---
title: Azure Active Directory na základě certifikátů ověřování pomocí služby Azure Cosmos DB
description: Zjistěte, jak nakonfigurovat identity Azure AD pro ověřování pomocí certifikátů pro přístup ke klíčům ze služby Azure Cosmos DB.
author: voellm
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 06/11/2019
ms.author: tvoellm
ms.reviewer: sngun
ms.openlocfilehash: cc39cc09259c1ae681e1fee070777575e2788323
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827849"
---
# <a name="certificate-based-authentication-for-an-azure-ad-identity-to-access-keys-from-an-azure-cosmos-db-account"></a>Ověřování pomocí certifikátů pro identitu služby Azure AD pro přístup ke klíčům z účtu služby Azure Cosmos DB

Ověřování pomocí certifikátů umožňuje klientská aplikace mohla ověřit pomocí služby Azure Active Directory (Azure AD) pomocí klientského certifikátu. Na počítači, kde je třeba identity, jako je místní počítač nebo virtuální počítač v Azure můžete provádět ověřování pomocí certifikátů. Aplikace pak můžete přečíst klíče služby Azure Cosmos DB bez nutnosti klíče přímo v aplikaci. Tento článek popisuje, jak vytvořit ukázková aplikace Azure AD, ho nakonfigurovat k ověřování pomocí certifikátu, přihlášení do Azure pomocí nové identity aplikace a pak načte klíče ze svého účtu Azure Cosmos. Tento článek používá prostředí Azure PowerShell k nastavení identity a poskytuje C# ukázkovou aplikaci, která ověřuje a má přístup k klíče z vašeho účtu Azure Cosmos.  

## <a name="prerequisites"></a>Požadavky

* Nainstalujte [nejnovější verzi](/powershell/azure/install-az-ps) prostředí Azure PowerShell.

* Pokud nemáte [předplatného Azure](https://docs.microsoft.com/azure/guides/developer/azure-developer-guide#understanding-accounts-subscriptions-and-billing), vytvořit [bezplatný účet](https://azure.microsoft.com/free/?ref=microsoft.com&utm_source=microsoft.com&utm_medium=docs&utm_campaign=visualstudio) předtím, než začnete.

## <a name="register-an-app-in-azure-ad"></a>Registrace aplikace ve službě Azure AD

V tomto kroku se zaregistrujte ukázkovou webovou aplikaci ve vašem účtu Azure AD. Tuto aplikaci se později používá ke čtení klíče z vašeho účtu služby Azure Cosmos DB. Použijte následující postup pro registraci aplikace: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Otevřete Azure **služby Active Directory** podokně přejděte na podokno registrace aplikace a vyberte **registrace nové**. 

   ![Registrace nové aplikace ve službě Active Directory](./media/certificate-based-authentication/new-app-registration.png)

1. Zadejte **zaregistrovat aplikaci** formuláře s následujícími podrobnostmi:  

   * **Název** – zadejte název pro vaši aplikaci, může být jakýkoli název, jako je například "Ukázková aplikace".
   * **Podporované typy účtů** – zvolte **účty v této organizační adresář pouze (výchozí adresář)** umožňující prostředkům v aktuálním adresáři přistupovat k této aplikaci. 
   * **Adresa URL přesměrování** – vyberte aplikaci typu **webové** a zadejte adresu URL, která je hostitelem vaší aplikace, může být libovolnou adresu URL. V tomto příkladu můžete zadat adresu URL testu jako `https://sampleApp.com` nevadí, i v případě, že aplikace neexistuje.

   ![Zaregistrovat ukázkovou webovou aplikaci](./media/certificate-based-authentication/register-sample-web-app.png)

1. Vyberte **zaregistrovat** po vyplnění formuláře.

1. Po registraci aplikace, poznamenejte si, **Application(client) ID** a **ID objektu**, tyto údaje použijete v dalších krocích. 

   ![Získání ID aplikace a objektu](./media/certificate-based-authentication/get-app-object-ids.png)

## <a name="install-the-azuread-module"></a>Instalace modulu Azure AD

V tomto kroku nainstalujete modul Azure AD PowerShell. Tento modul je potřeba získat ID aplikace, které jste zaregistrovali v předchozím kroku a přidružit certifikát podepsaný svým držitelem k dané aplikaci. 

1. Otevřete Windows PowerShell ISE s oprávněním správce. Pokud jste již takto neučinili, nainstalujte modul prostředí PowerShell AZ a připojení k vašemu předplatnému. Pokud máte více předplatných, můžete nastavit kontext aktuálního předplatného, jak je znázorněno v následující příkazy:

   ```powershell

   Install-Module -Name Az -AllowClobber
   Connect-AzAccount

   Get-AzSubscription
   $context = Get-AzSubscription -SubscriptionId <Your_Subscription_ID>
   Set-AzContext $context 
   ```

1. Nainstalujte a importujte [AzureAD](/powershell/module/azuread/?view=azureadps-2.0) modulu

   ```powershell
   Install-Module AzureAD
   Import-Module AzureAD 
   ```

## <a name="sign-into-your-azure-ad"></a>Přihlaste se k Azure AD.

Přihlášení do služby Azure AD, kde jste zaregistrovali aplikaci. Pomocí příkazu Connect-AzureAD přihlásit ke svému účtu, zadejte svoje přihlašovací údaje účtu Azure v místním okně. 

```powershell
Connect-AzureAD 
```

## <a name="create-a-self-signed-certificate"></a>Vytvořit certifikát podepsaný svým držitelem (self-signed certificate)

Otevřete Windows PowerShell ISE jiná instance a spusťte následující příkazy k vytvoření certifikátu podepsaného svým držitelem a čtení klíče přidruženého k certifikátu:

```powershell
$cert = New-SelfSignedCertificate -CertStoreLocation "Cert:\CurrentUser\My" -Subject "CN=sampleAppCert" -KeySpec KeyExchange
$keyValue = [System.Convert]::ToBase64String($cert.GetRawCertData()) 
```

## <a name="create-the-certificate-based-credential"></a>Vytvoření přihlašovacích údajů na základě certifikátů 

Potom spusťte následující příkazy k získání ID objektu vaší aplikace a vytvoření přihlašovacích údajů na základě certifikátů. V tomto příkladu jsme nastavili certifikát, který vyprší po roce, můžete ho nastavit na všechny požadované koncové datum.

```powershell
$application = Get-AzureADApplication -ObjectId <Object_ID_of_Your_Application>

New-AzureADApplicationKeyCredential -ObjectId $application.ObjectId -CustomKeyIdentifier "Key1" -Type AsymmetricX509Cert -Usage Verify -Value $keyValue -EndDate "2020-01-01"
```

Výše uvedený příkaz vrátí výstup jako na následujícím snímku obrazovky:

![Výstup vytváření přihlašovacích údajů na základě certifikátů](./media/certificate-based-authentication/certificate-based-credential-output.png)

## <a name="configure-your-azure-cosmos-account-to-use-the-new-identity"></a>Nastavte v účtu Azure Cosmos používat nové identity

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/).

1. Přejděte do svého účtu Azure Cosmos, otevřete **řízení přístupu (IAM)** okno.

1. Vyberte **přidat** a **přidat přiřazení role**. Přidat ukázková aplikace, kterou jste vytvořili v předchozím kroku s **Přispěvatel** role, jak je znázorněno na následujícím snímku obrazovky:

   ![Konfigurovat účet služby Azure Cosmos používat novou identitu](./media/certificate-based-authentication/configure-cosmos-account-with-identify.png)

1. Vyberte **Uložit** po vyplnění formuláře


## <a name="access-the-keys-from-powershell"></a>Pro přístup z prostředí PowerShell

V tomto kroku se přihlaste do Azure s použitím aplikace a certifikát, který jste vytvořili a přístupové klíče účtu Azure Cosmos. 

1. Zpočátku vymažte přihlašovací údaje účtu Azure, které jste použili pro přihlášení ke svému účtu. Přihlašovací údaje můžete vymazat tím, že pomocí následujícího příkazu:

   ```powershell
   Disconnect-AzAccount -Username <Your_Azure_account_email_id> 
   ```

1. Dále ověřte, že můžete přihlásit do portálu Azure portal s použitím přihlašovacích údajů vaší aplikace a přístupové klíče služby Azure Cosmos DB:

   ```powershell
   Login-AzAccount -ApplicationId <Your_Application_ID> -CertificateThumbprint $cert.Thumbprint -ServicePrincipal -Tenant <Tenant_ID_of_your_application>

   Invoke-AzResourceAction -Action listKeys -ResourceType "Microsoft.DocumentDB/databaseAccounts" -ApiVersion "2015-04-08" -ResourceGroupName <Resource_Group_Name_of_your_Azure_Cosmos_account> -ResourceName <Your_Azure_Cosmos_Account_Name> 
   ```

Předchozí příkaz zobrazí primární a sekundární hlavní klíče vašeho účtu Azure Cosmos. Můžete zobrazit protokol aktivit vašeho účtu Azure Cosmos k ověření, že požadavek na získání klíče proběhlo úspěšně a inicializuje události v aplikaci "Ukázková aplikace". 
 
![Ověření volání get klíče ve službě Azure AD](./media/certificate-based-authentication/activity-log-validate-results.png)


## <a name="access-the-keys-from-a-c-application"></a>Přístupové klíče z C# aplikace 

Můžete také ověřit tento scénář přístup ke klíčům z C# aplikace. Následující C# konzolovou aplikaci, která může přístupové klíče služby Azure Cosmos DB s použitím aplikace zaregistrované ve službě Active Directory. Nezapomeňte aktualizovat hodnotu tenantId, clientID, certName, název skupiny prostředků, ID předplatného Azure Cosmos název Podrobnosti účtu před spuštěním kódu. 

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
 
 
        /// <summary>
        /// Get an access token from Azure AD using client credentials.
        /// If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each
        /// </summary>
        private static async Task<AuthenticationResult> GetAccessToken(AuthenticationContext authContext, string resourceUri, ClientAssertionCertificate cert)
        {
            //
            // Get an access token from Azure AD using client credentials.
            // If the attempt to get a token fails because the server is unavailable, retry twice after 3 seconds each.
            //
            AuthenticationResult result = null;
            int retryCount = 0;
            bool retry = false;
 
            do
            {
                retry = false;
                errorCode = 0;
 
                try
                {
                    result = await authContext.AcquireTokenAsync(resourceUri, cert);
                }
                catch (AdalException ex)
                {
                    if (ex.ErrorCode == "temporarily_unavailable")
                    {
                        retry = true;
                        retryCount++;
                        Thread.Sleep(3000);
                    }
 
                    Console.WriteLine(
                        String.Format("An error occurred while acquiring a token\nTime: {0}\nError: {1}\nRetry: {2}\n",
                        DateTime.Now.ToString(),
                        ex.ToString(),
                        retry.ToString()));
 
                    errorCode = -1;
                }
 
            } while ((retry == true) && (retryCount < 3));
            return result;
        }
    }
}
```

Skript vypíše primární a sekundární klíče předlohy, jak je znázorněno na následujícím snímku obrazovky:

![výstup aplikace CSharp](./media/certificate-based-authentication/csharp-application-output.png)

Podobně jako v předchozí části, můžete zobrazit protokol aktivit vašeho účtu Azure Cosmos k ověření, že událost požadavku get klíče spuštěné aplikací "Ukázková aplikace". 


## <a name="next-steps"></a>Další postup

* [Zabezpečovací klíče Azure Cosmos pomocí služby Azure Key Vault](access-secrets-from-keyvault.md)

* [Atributy zabezpečení pro službu Azure Cosmos DB](cosmos-db-security-attributes.md)
