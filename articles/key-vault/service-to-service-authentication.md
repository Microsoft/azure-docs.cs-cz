---
title: Ověřování služba-služba pro Azure Key Vault pomocí .NET
description: K ověření Azure Key Vault používání .NET použijte knihovnu Microsoft. Azure. Services. AppAuthentication.
keywords: místní přihlašovací údaje pro ověřování trezoru klíčů Azure Key
author: msmbaldwin
manager: barbkess
services: key-vault
ms.author: mbaldwin
ms.date: 07/06/2019
ms.topic: conceptual
ms.service: key-vault
ms.openlocfilehash: d34c94ccca47d29afc4f3d83bec58db737be270c
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/06/2019
ms.locfileid: "68840414"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Ověřování služba-služba pro Azure Key Vault pomocí .NET

K ověření, že Azure Key Vault potřebujete pověření Azure Active Directory (AD), buď sdílený tajný klíč, nebo certifikát. 

Správa takových přihlašovacích údajů může být obtížná a je potřeba, aby se do aplikace zařadí přihlašovací údaje, a to tak, že je zahrnete do zdrojového nebo konfiguračního souboru.  `Microsoft.Azure.Services.AppAuthentication` Tento problém usnadňuje knihovna rozhraní .NET. Používá pověření vývojáře k ověřování během místního vývoje. Když je řešení později nasazené do Azure, knihovna se automaticky přepne na přihlašovací údaje aplikace.    Použití přihlašovacích údajů pro vývojáře během místního vývoje je bezpečnější, protože nemusíte vytvářet přihlašovací údaje Azure AD nebo sdílet přihlašovací údaje mezi vývojáři.

`Microsoft.Azure.Services.AppAuthentication` Knihovna spravuje ověřování automaticky, což vám umožní soustředit se na vaše řešení, nikoli na vaše přihlašovací údaje.  Podporuje místní vývoj pomocí Microsoft Visual Studio, Azure CLI nebo integrovaného ověřování Azure AD. Při nasazení do prostředku Azure, který podporuje spravovanou identitu, knihovna automaticky používá [spravované identity pro prostředky Azure](../active-directory/msi-overview.md). Nejsou vyžadovány žádné změny kódu nebo konfigurace. Knihovna také podporuje přímé použití [přihlašovacích údajů klienta](../azure-resource-manager/resource-group-authenticate-service-principal.md) Azure AD, když spravovaná identita není k dispozici, nebo když není možné určit kontext zabezpečení vývojáře během místního vývoje.

## <a name="using-the-library"></a>Použití knihovny

V případě aplikací .NET je nejjednodušší způsob práce se spravovanou identitou prostřednictvím `Microsoft.Azure.Services.AppAuthentication` balíčku. Tady je postup, jak začít:

1. Do své aplikace přidejte odkazy na balíčky [Microsoft. Azure. Services. AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a [Microsoft. Azure. trezoru](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) . 

2. Přidejte následující kód:

    ``` csharp
    using Microsoft.Azure.Services.AppAuthentication;
    using Microsoft.Azure.KeyVault;

    // Instantiate a new KeyVaultClient object, with an access token to Key Vault
    var azureServiceTokenProvider1 = new AzureServiceTokenProvider();
    var kv = new KeyVaultClient(new KeyVaultClient.AuthenticationCallback(azureServiceTokenProvider1.KeyVaultTokenCallback));

    // Optional: Request an access token to other Azure services
    var azureServiceTokenProvider2 = new AzureServiceTokenProvider();
    string accessToken = await azureServiceTokenProvider2.GetAccessTokenAsync("https://management.azure.com/").ConfigureAwait(false);
    ```

`AzureServiceTokenProvider` Třída ukládá token do mezipaměti a načítá ho z Azure AD těsně před vypršením platnosti. V důsledku toho již nemusíte před voláním `GetAccessTokenAsync` metody kontrolovat vypršení platnosti. Stačí zavolat metodu, pokud chcete použít token. 

`GetAccessTokenAsync` Metoda vyžaduje identifikátor prostředku. Další informace najdete v tématu [které služby Azure podporují spravované identity pro prostředky Azure](../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Místní ověřování při vývoji

Pro místní vývoj existují dva scénáře primárního ověřování: [ověřování ve službách Azure](#authenticating-to-azure-services)a [ověřování vlastních služeb](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Ověřování ve službách Azure

Místní počítače nepodporují spravované identity pro prostředky Azure.  V důsledku toho `Microsoft.Azure.Services.AppAuthentication` knihovna používá vaše přihlašovací údaje pro vývojáře ke spuštění ve vašem místním vývojovém prostředí. Když se řešení nasadí do Azure, knihovna pomocí spravované identity přepne na tok udělení přihlašovacích údajů klienta OAuth 2,0.  To znamená, že můžete stejný kód otestovat místně a vzdáleně bez obav.

Pro místní `AzureServiceTokenProvider` vývoj načte tokeny pomocí sady **Visual Studio**, **rozhraní příkazového řádku Azure** (CLI) nebo **integrovaného ověřování Azure AD**. Každá možnost se zkouší postupně a knihovna používá první možnost, která je úspěšná. Pokud žádná možnost nefunguje, `AzureServiceTokenProviderException` je vyvolána výjimka s podrobnými informacemi.

### <a name="authenticating-with-visual-studio"></a>Ověřování pomocí sady Visual Studio

Ověřování v aplikaci Visual Studio má následující požadavky:

1. [Visual Studio 2017 v 15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/) nebo novějším.

2. [Rozšíření pro ověřování aplikací pro Visual Studio](https://go.microsoft.com/fwlink/?linkid=862354), které je dostupné jako samostatné rozšíření pro visual Studio 2017 Update 5 a je součástí produktu v Update 6 nebo novějším. S aktualizací Update 6 nebo novější můžete ověřit instalaci rozšíření ověřování aplikace výběrem nástrojů pro vývoj pro Azure v instalačním programu sady Visual Studio.
 
Přihlaste se k aplikaci Visual Studio a pomocí**možností**&nbsp; **nástroje**&nbsp;&nbsp;>možnosti>&nbsp;**ověřování služby Azure** vyberte účet pro místní vývoj. 

Pokud narazíte na problémy pomocí sady Visual Studio, jako jsou chyby týkající se souboru poskytovatele tokenů, pečlivě zkontrolujte tyto kroky. 

Může být také nutné znovu ověřit váš token pro vývojáře. Provedete to tak, že přejdete na **nástroje**&nbsp;>&nbsp;**Možnosti možnosti**>**ověřování služby&nbsp;&nbsp;Azure** a vyhledáte odkaz **znovu ověřit** pod vybraným zohledňují.  Vyberte ji pro ověření. 

### <a name="authenticating-with-azure-cli"></a>Ověřování pomocí Azure CLI

Použití rozhraní příkazového řádku Azure pro místní vývoj:

1. Nainstalujte rozhraní příkazového [řádku Azure CLI v 2.0.12](/cli/azure/install-azure-cli) nebo novějším. Upgradujte starší verze. 

2. Přihlaste se k Azure pomocí **AZ Login** .

Použijte `az account get-access-token` k ověření přístupu.  Pokud se zobrazí chyba, ověřte, že krok 1 byl úspěšně dokončen. 

Pokud rozhraní příkazového řádku Azure není nainstalované ve výchozím adresáři, může se zobrazit zpráva o chybách `AzureServiceTokenProvider` , která nemůže najít cestu k rozhraní příkazového řádku Azure CLI.  K definování instalační složky Azure CLI použijte proměnnou prostředí **AzureCLIPath** . `AzureServiceTokenProvider`v případě potřeby přidá adresář zadaný v proměnné prostředí **AzureCLIPath** do proměnné prostředí **path** .

Pokud jste přihlášeni do Azure CLI pomocí několika účtů nebo má váš účet přístup k několika předplatným, je nutné zadat konkrétní předplatné, které se má použít.  K tomu použijte:

```
az account set --subscription <subscription-id>
```

Tento příkaz generuje výstup pouze při selhání.  Pokud chcete ověřit aktuální nastavení účtu, použijte:

```
az account list
```

### <a name="authenticating-with-azure-ad-authentication"></a>Ověřování pomocí ověřování Azure AD

Pokud chcete používat ověřování Azure AD, ověřte, že:

- Vaše místní služba Active Directory [se synchronizuje do Azure AD](../active-directory/connect/active-directory-aadconnect.md).

- Váš kód je spuštěný na počítači připojeném k doméně.


### <a name="authenticating-to-custom-services"></a>Ověřování pro vlastní služby

Když služba volá služby Azure, předchozí postup funguje, protože služby Azure umožňují přístup k uživatelům i aplikacím.  

Při vytváření služby, která volá vlastní službu, použijte přihlašovací údaje klienta Azure AD pro místní ověřování při vývoji.  Existují dvě možnosti: 

1.  Přihlaste se k Azure pomocí instančního objektu:

    1.  [Vytvoření instančního objektu](/cli/azure/create-an-azure-service-principal-azure-cli).

    2.  Přihlaste se pomocí Azure CLI:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Vzhledem k tomu, že objekt služby nemusí mít přístup k předplatnému `--allow-no-subscriptions` , použijte argument.

2.  K určení podrobností instančního objektu použijte proměnné prostředí.  Podrobnosti najdete v tématu [spuštění aplikace pomocí instančního objektu](#running-the-application-using-a-service-principal).

Po přihlášení k Azure `AzureServiceTokenProvider` používá instanční objekt k načtení tokenu pro místní vývoj.

To platí jenom pro místní vývoj. Když se vaše řešení nasadí do Azure, knihovna se přepne na spravovanou identitu pro ověřování.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Spuštění aplikace pomocí spravované identity nebo identity přiřazené uživatelem 

Když spustíte kód na Azure App Service nebo VIRTUÁLNÍm počítači Azure se zapnutou spravovanou identitou, knihovna automaticky použije spravovanou identitu. 

Případně se můžete ověřit pomocí uživatelsky přiřazené identity. Další informace o identitách přiřazených uživateli najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Chcete-li provést ověření pomocí uživatelsky přiřazené identity, je třeba zadat ID klienta identity přiřazené uživatelem v připojovacím řetězci. Připojovací řetězec je uveden v části [Podpora připojovacího řetězce](#connection-string-support) níže.

## <a name="running-the-application-using-a-service-principal"></a>Spuštění aplikace pomocí instančního objektu 

Pro ověření může být nutné vytvořit přihlašovací údaje klienta Azure AD. Mezi běžné příklady patří:

- Váš kód běží na místním vývojovém prostředí, ale ne v rámci identity vývojáře.  Service Fabric například používá [účet NetworkService](../service-fabric/service-fabric-application-secret-management.md) pro místní vývoj.
 
- Váš kód běží na místním vývojovém prostředí a Vy se ověřujete ve vlastní službě, takže nemůžete použít vaši identitu vývojáře. 
 
- Váš kód běží na výpočetním prostředku Azure, který zatím nepodporuje spravované identity pro prostředky Azure, jako je Azure Batch.

Existují tři primární metody použití instančního objektu ke spuštění vaší aplikace. Chcete-li použít některý z nich, je nutné nejprve [vytvořit instanční objekt](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Přihlášení k Azure AD pomocí certifikátu v místním úložišti klíčů

1. Pomocí příkazu Azure CLI [AZ AD SP Create-for-RBAC](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) vytvořte certifikát instančního objektu. 

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Tím se vytvoří soubor. pem (privátní klíč), který bude uložený v domovském adresáři. Tento certifikát můžete nasadit do úložiště *LocalMachine* nebo *CurrentUser* . 

    > [!Important]
    > Příkaz CLI vygeneruje soubor. pem, ale systém Windows poskytuje pouze nativní podporu certifikátů PFX. K vygenerování certifikátu PFX použijte příkazy PowerShellu, které jsou tady uvedené: [Vytvoření instančního objektu s certifikátem podepsaným svým držitelem](../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate) Tyto příkazy také automaticky nasadí certifikát.

1. Nastavte proměnnou prostředí s názvem **AzureServicesAuthConnectionString** na:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```
 
    Nahraďte hodnoty *{AppID}* , *{TenantId}* a *{kryptografický otisk}* hodnotami generovanými v kroku 1. Nahraďte *{CertificateStore}* buď `LocalMachine` nebo `CurrentUser`, podle plánu nasazení.

1. Spusťte aplikaci. 

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Přihlášení ke službě Azure AD pomocí sdíleného tajného pověření

1. Pomocí hesla příkazem [AZ AD SP Create-for-RBAC--Password](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)vytvořte certifikát instančního objektu. 

2. Nastavte proměnnou prostředí s názvem **AzureServicesAuthConnectionString** na:

    ```
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret} 
    ```

    Nahraďte hodnoty _{AppID}_ , _{TenantId}_ a _{ClientSecret}_ hodnotami generovanými v kroku 1.

3. Spusťte aplikaci. 

Jakmile se všechno nastaví správně, nemusíte dělat žádné další změny kódu.  `AzureServiceTokenProvider`používá k ověření ve službě Azure AD proměnnou prostředí a certifikát. 

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Přihlášení k Azure AD pomocí certifikátu v Key Vault

Tato možnost umožňuje uložit klientský certifikát instančního objektu do služby Key Vault a použít ho pro ověřování instančního objektu. Tuto situaci můžete použít pro následující scénáře:

* Místní ověřování, kde se chcete ověřit pomocí explicitního instančního objektu a chcete bezpečně uchovávat přihlašovací údaje instančního objektu v trezoru klíčů. Vývojářský účet musí mít přístup k trezoru klíčů. 
* Ověřování z Azure, kde chcete používat explicitní přihlašovací údaje (třeba pro více tenantů), a chcete přihlašovací údaje instančního objektu udržovat v trezoru klíčů bezpečně. Spravovaná identita musí mít přístup k trezoru klíčů. 

Spravovaná identita nebo vaše identita vývojáře musí mít oprávnění k načtení certifikátu klienta z Key Vault. Knihovna AppAuthentication používá načtený certifikát jako přihlašovací údaje klienta instančního objektu.

Použití klientského certifikátu pro ověřování instančního objektu

1. Vytvořte certifikát instančního objektu a automaticky ho uložte do trezoru klíčů pomocí rozhraní příkazového řádku Azure CLI [AZ AD SP Create-for-RBAC- <keyvaultname> -webtrezor <certificatename> --CERT--Create-CERT--Skip-Assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```
    
    Identifikátor certifikátu bude adresa URL ve formátu.`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. V `{KeyVaultCertificateSecretIdentifier}` tomto připojovacím řetězci nahraďte identifikátorem certifikátu:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Pokud se například vašemu trezoru klíčů nazýval "myKeyVault" a vytvořili jste certifikát s názvem "myCert", identifikátor certifikátu by byl:

    ```
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```


## <a name="connection-string-support"></a>Podpora připojovacích řetězců

Ve výchozím nastavení `AzureServiceTokenProvider` používá k získání tokenu více metod. 

Chcete-li řídit proces, použijte připojovací řetězec předaný `AzureServiceTokenProvider` konstruktoru nebo zadaného v proměnné prostředí *AzureServicesAuthConnectionString* . 

Podporovány jsou následující možnosti:

| Možnost připojovacího řetězce | Scénář | Komentáře|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Místní vývoj | AzureServiceTokenProvider používá Azure CLI k získání tokenu. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Místní vývoj | AzureServiceTokenProvider používá Visual Studio k získání tokenu. |
| `RunAs=CurrentUser` | Místní vývoj | AzureServiceTokenProvider používá k získání tokenu integrované ověřování Azure AD. |
| `RunAs=App` | [Spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/index.yml) | AzureServiceTokenProvider používá ke získání tokenu spravovanou identitu. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Uživatelsky přiřazená identita pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | AzureServiceTokenProvider používá k získání tokenu uživatelem přiřazenou identitu. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Ověřování vlastních služeb | KeyVaultCertificateSecretIdentifier = tajný identifikátor certifikátu. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Instanční objekt | `AzureServiceTokenProvider`Nástroj používá k získání tokenu z Azure AD certifikát. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Instanční objekt | `AzureServiceTokenProvider`k získání tokenu z Azure AD používá certifikát.|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Instanční objekt |`AzureServiceTokenProvider`k získání tokenu z Azure AD používá tajný klíč. |

## <a name="samples"></a>Ukázky kódu

Chcete-li `Microsoft.Azure.Services.AppAuthentication` Zobrazit knihovnu v akci, přečtěte si následující ukázky kódu.

1. [Použití spravované identity k načtení tajného klíče z Azure Key Vault za běhu](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

2. [Programové nasazení Azure Resource Manager šablony z virtuálního počítače Azure se spravovanou identitou](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

3. [Použijte ukázku .NET Core a spravovanou identitu pro volání služeb Azure z virtuálního počítače Azure Linux](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>Řešení potíží s AppAuthentication

### <a name="common-issues-during-local-development"></a>Běžné problémy při místním vývoji

#### <a name="azure-cli-is-not-installed-you-are-not-logged-in-or-you-do-not-have-the-latest-version"></a>Rozhraní příkazového řádku Azure není nainstalované, nejste přihlášeni nebo nemáte nejnovější verzi.

Spuštěním **AZ Account Get-Access-token** zjistíte, jestli Azure CLI zobrazuje token pro vás. Pokud tento program nenalezne, nainstalujte prosím [nejnovější verzi rozhraní příkazového řádku Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Pokud jste ho nainstalovali, může se zobrazit výzva k přihlášení. 
 
#### <a name="azureservicetokenprovider-cannot-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider nemůže najít cestu pro rozhraní příkazového řádku Azure CLI.

AzureServiceTokenProvider vyhledá Azure CLI ve svých výchozích umístěních pro instalaci. Pokud nejde najít Azure CLI, nastavte prosím proměnnou prostředí **AzureCLIPath** na instalační SLOŽKU Azure CLI. AzureServiceTokenProvider přidá proměnnou prostředí do proměnné prostředí PATH.
 
#### <a name="you-are-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>K rozhraní příkazového řádku Azure jste přihlášeni pomocí několika účtů, stejný účet má přístup k předplatným ve více klientech, nebo při pokusu o volání během místního vývoje dojde k chybě odepření přístupu.

Pomocí Azure CLI nastavte výchozí předplatné na jeden, který má účet, který chcete použít, a je ve stejném tenantovi jako prostředek, ke kterému chcete získat přístup: **AZ Account set--Subscription [ID předplatného]** . Pokud se žádný výstup nezobrazuje, je úspěšný. Ověřte, že je teď správný účet ve výchozím nastavení pomocí **AZ Account list**.

### <a name="common-issues-across-environments"></a>Běžné problémy v různých prostředích

#### <a name="unauthorized-access-access-denied-forbidden-etc-error"></a>Neoprávněný přístup, přístup odepřen, zakázáno atd. Chyba
 
Použitý objekt zabezpečení nemá přístup k prostředku, ke kterému se pokouší získat přístup. Udělte vašemu uživatelskému účtu App Service nebo přispěvateli instalační služby MSI přístup k požadovanému prostředku, a to v závislosti na tom, jestli jste ukázku spustili na místním počítači pro vývoj nebo nasazení do Azure do vašeho App Service. Některé prostředky, jako jsou například trezory klíčů, mají také vlastní [zásady přístupu](https://docs.microsoft.com/en-us/azure/key-vault/key-vault-secure-your-key-vault#data-plane-and-access-policies) , které můžete použít k udělení přístupu k objektům zabezpečení (uživatelům, aplikacím, skupinám atd.).

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Běžné problémy při nasazení na Azure App Service

#### <a name="managed-identity-is-not-setup-on-the-app-service"></a>Spravovaná identita není nastavená na App Service
 
Ověřte, že proměnné prostředí MSI_ENDPOINT a MSI_SECRET existují pomocí [konzoly ladění Kudu](https://azure.microsoft.com/en-us/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Pokud tyto proměnné prostředí neexistují, spravovaná identita není na App Service povolena. 
 
### <a name="common-issues-when-deployed-locally-with-iis"></a>Běžné problémy při nasazení v místním prostředí se službou IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Nejde načíst tokeny při ladění aplikace ve službě IIS.

Ve výchozím nastavení AppAuth běží v jiném uživatelském kontextu ve službě IIS, takže nemá přístup k tomu, aby k získání přístupových tokenů použil vaši identitu vývojáře. Službu IIS můžete nakonfigurovat tak, aby běžela s vaším uživatelským kontextem, a to pomocí následujících dvou kroků:
- Nakonfigurujte fond aplikací webové aplikace tak, aby běžel jako aktuální uživatelský účet. Další informace najdete [tady](https://docs.microsoft.com/en-us/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities) .
- Nakonfigurujte "setProfileEnvironment" na "true". Další informace najdete [tady](https://docs.microsoft.com/en-us/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Přejít na%windir%\System32\inetsrv\config\applicationHost.config
    - Vyhledejte "setProfileEnvironment". Pokud je nastavená na false, změňte ho na true (pravda). Pokud není k dispozici, přidejte jej jako atribut do elementu processModel (/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironment) a nastavte jej na hodnotu "true".

- Přečtěte si další informace o [spravovaných identitách pro prostředky Azure](../active-directory/managed-identities-azure-resources/index.yml).
- Přečtěte si další informace o [scénářích ověřování Azure AD](../active-directory/develop/active-directory-authentication-scenarios.md).
