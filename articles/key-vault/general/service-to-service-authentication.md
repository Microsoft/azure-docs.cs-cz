---
title: Ověřování služby azure key vault pomocí rozhraní .NET
description: Pomocí knihovny Microsoft.Azure.Services.AppAuthentication ověřujte v trezoru klíčů Azure pomocí rozhraní .NET.
keywords: azure key-vault ověřování místní pověření
author: msmbaldwin
manager: rkarlin
services: key-vault
ms.author: mbaldwin
ms.date: 08/28/2019
ms.topic: conceptual
ms.service: key-vault
ms.subservice: general
ms.openlocfilehash: cd630acfd65f0a79c186ba35bc15627bf7ccfdbe
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81686197"
---
# <a name="service-to-service-authentication-to-azure-key-vault-using-net"></a>Ověřování služby azure key vault pomocí rozhraní .NET

K ověření v trezoru klíčů Azure potřebujete přihlašovací údaje služby Azure Active Directory (Azure AD), buď sdílený tajný klíč, nebo certifikát.

Správa těchto pověření může být obtížná. Je lákavé sdružovat přihlašovací údaje do aplikace jejich zahrnutím do zdrojových nebo konfiguračních souborů. Knihovna `Microsoft.Azure.Services.AppAuthentication` for .NET tento problém zjednodušuje. Používá pověření vývojáře k ověření během místního vývoje. Když se řešení později nasadí do Azure, knihovna se automaticky přepne na přihlašovací údaje aplikace. Použití přihlašovacích údajů pro vývojáře během místního vývoje je bezpečnější, protože nemusíte vytvářet přihlašovací údaje Azure AD nebo sdílet přihlašovací údaje mezi vývojáři.

Knihovna `Microsoft.Azure.Services.AppAuthentication` spravuje ověřování automaticky, což vám umožní soustředit se na vaše řešení, nikoli na vaše přihlašovací údaje. Podporuje místní vývoj pomocí Microsoft Visual Studio, Azure CLI nebo Azure AD integrované ověřování. Při nasazení do prostředku Azure, který podporuje spravovanou identitu, knihovna automaticky používá [spravované identity pro prostředky Azure](../../active-directory/msi-overview.md). Nejsou vyžadovány žádné změny kódu nebo konfigurace. Knihovna také podporuje přímé použití [přihlašovacích údajů klienta](../../azure-resource-manager/resource-group-authenticate-service-principal.md) Azure AD, když není k dispozici spravovaná identita nebo když kontext zabezpečení vývojáře nelze určit během místního vývoje.

## <a name="prerequisites"></a>Požadavky

- [Visual Studio 2019](https://www.visualstudio.com/downloads/) nebo [Visual Studio 2017 v15.5](https://blogs.msdn.microsoft.com/visualstudio/2017/10/11/visual-studio-2017-version-15-5-preview/).

- Rozšíření Ověřování aplikací pro Visual Studio, které je k dispozici jako samostatné rozšíření pro Visual Studio 2017 Update 5 a je dodáváno s produktem v aktualizaci 6 a novějších. Pomocí aktualizace 6 nebo novější můžete ověřit instalaci rozšíření Ověřování aplikací výběrem nástrojů pro vývoj Azure z instalačního programu Visual Studia.

## <a name="using-the-library"></a>Použití knihovny

Pro aplikace .NET nejjednodušší způsob, jak pracovat se spravovanou identitou `Microsoft.Azure.Services.AppAuthentication` je prostřednictvím balíčku. Tady je postup, jak začít:

1. Vyberte **Nástroje** > **NuGet Správce** > balíčků**Správa NuGet balíčky pro řešení** přidat odkazy na [Microsoft.Azure.Services.AppAuthentication](https://www.nuget.org/packages/Microsoft.Azure.Services.AppAuthentication) a [Microsoft.Azure.KeyVault](https://www.nuget.org/packages/Microsoft.Azure.KeyVault) NuGet balíčky do vašeho projektu.

1. Přidejte následující kód:

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

Třída `AzureServiceTokenProvider` ukládá token do mezipaměti v paměti a načte ho z Azure AD těsně před vypršením platnosti. Takže již není třeba zkontrolovat vypršení platnosti před voláním `GetAccessTokenAsync` metody. Stačí zavolat metodu, když chcete použít token.

Metoda `GetAccessTokenAsync` vyžaduje identifikátor prostředku. Další informace o službách Microsoft Azure najdete v tématu [Co je spravované identity pro prostředky Azure](../../active-directory/msi-overview.md).

## <a name="local-development-authentication"></a>Ověřování místního vývoje

Pro místní vývoj existují dva primární scénáře ověřování: [ověřování služeb Azure](#authenticating-to-azure-services)a ověřování [vlastních služeb](#authenticating-to-custom-services).

### <a name="authenticating-to-azure-services"></a>Ověřování služeb Azure

Místní počítače nepodporují spravované identity pro prostředky Azure. V důsledku toho `Microsoft.Azure.Services.AppAuthentication` knihovna používá pověření vývojáře ke spuštění v místním vývojovém prostředí. Když se řešení nasadí do Azure, knihovna používá spravovanou identitu k přepnutí na tok udělení pověření klienta OAuth 2.0. Tento přístup znamená, že můžete testovat stejný kód místně a vzdáleně bez obav.

Pro místní `AzureServiceTokenProvider` vývoj načte tokeny pomocí **Visual Studio**, **Rozhraní příkazového řádku Azure** (CLI) nebo **integrovanéověřování Azure AD**. Každá možnost se snaží postupně a knihovna používá první možnost, která je úspěšná. Pokud žádná možnost `AzureServiceTokenProviderException` funguje, je vyvolána výjimka s podrobnými informacemi.

#### <a name="authenticating-with-visual-studio"></a>Ověřování pomocí sady Visual Studio

Ověření pomocí sady Visual Studio:

1. Přihlaste se k sadě Visual Studio a **pomocí**&nbsp;>&nbsp;**nástroje Možnosti** otevřete **možnosti**.

1. Vyberte **Azure Service Authentication**, zvolte účet pro místní vývoj a vyberte **OK**.

Pokud narazíte na problémy pomocí sady Visual Studio, jako jsou chyby, které se týkají souboru zprostředkovatele tokenu, pečlivě zkontrolujte předchozí kroky.

Možná budete muset znovu ověřit váš vývojářský token. Chcete-li tak učinit, vyberte**možnosti** **nástrojů**&nbsp;>&nbsp;a pak vyberte **Azure&nbsp;Service&nbsp;Authentication**. Vyhledejte odkaz **Znovu ověřit** pod vybraným účtem. Vyberte ji k ověření.

#### <a name="authenticating-with-azure-cli"></a>Ověřování pomocí azure cli

Chcete-li použít Azure CLI pro místní vývoj, ujistěte se, že máte verzi [Azure CLI v2.0.12](/cli/azure/install-azure-cli) nebo novější.

Použití azure cli:

1. Vyhledejte rozhraní příkazového řádku Azure na hlavním panelu Windows a otevřete **příkazový řádek Microsoft Azure**.

1. Přihlaste se na portál Azure: *az přihlášení* pro přihlášení do Azure.

1. Ověřte přístup zadáním *az účtu get-access-token --resource https:\//vault.azure.net*. Pokud se zobrazí chyba, zkontrolujte, zda je správně nainstalována správná verze rozhraní příkazového příkazu Azure.

   Pokud rozhraní příkazového od ponesení příkazového odpovky do výchozího adresáře není nainstalováno, může se zobrazit hlášení chyb, které `AzureServiceTokenProvider` nemůže najít cestu pro rozhraní příkazového příkazové k dispozici azure. Pomocí proměnné prostředí **AzureCLIPath** definujte instalační složku Azure CLI. `AzureServiceTokenProvider`v případě potřeby přidá adresář zadaný v proměnné prostředí **AzureCLIPath** do proměnné prostředí **Path.**

1. Pokud jste přihlášení k Azure CLI pomocí více účtů nebo váš účet má přístup k více předplatných, musíte zadat předplatné, které chcete použít. Zadejte příkaz *az účet nastavit --subscription <subscription-id>*.

Tento příkaz generuje výstup pouze při selhání. Chcete-li ověřit nastavení aktuálního `az account list`účtu, zadejte příkaz .

#### <a name="authenticating-with-azure-ad-authentication"></a>Ověřování pomocí ověřování Azure AD

Pokud chcete použít ověřování Azure AD, ověřte, že:

- Místní služba Active Directory se synchronizuje se službou Azure AD. Další informace najdete v tématu [Co je hybridní identita ve službě Azure Active Directory?](../../active-directory/connect/active-directory-aadconnect.md).

- Kód je spuštěn v počítači přilehlém k doméně.

### <a name="authenticating-to-custom-services"></a>Ověřování vlastních služeb

Když služba volá služby Azure, předchozí kroky fungují, protože služby Azure umožňují přístup uživatelům i aplikacím.

Při vytváření služby, která volá vlastní službu, použijte přihlašovací údaje klienta Azure AD pro ověřování místního vývoje. Existují dvě možnosti:

- K přihlášení do Azure použijte instanční objekt:

    1. Vytvořte instanční objekt. Další informace najdete [v tématu Vytvoření instančního objektu Azure pomocí azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

    1. Pomocí azure cli se přihlaste pomocí následujícího příkazu:

        ```azurecli
        az login --service-principal -u <principal-id> --password <password> --tenant <tenant-id> --allow-no-subscriptions
        ```

        Vzhledem k tomu, že instanční `--allow-no-subscriptions` objekt nemusí mít přístup k předplatnému, použijte argument.

- Proměnné prostředí slouží k určení podrobností o zaregistrovaný objekt servisu. Další informace naleznete [v tématu Spuštění aplikace pomocí instančního objektu](#running-the-application-using-a-service-principal).

Po přihlášení k Azure `AzureServiceTokenProvider` použije instanční objekt k načtení tokenu pro místní vývoj.

Tento přístup se vztahuje pouze na místní rozvoj. Když se vaše řešení nasadí do Azure, knihovna přepne na spravovanou identitu pro ověřování.

## <a name="running-the-application-using-managed-identity-or-user-assigned-identity"></a>Spuštění aplikace pomocí spravované identity nebo identity přiřazené uživateli

Když spustíte kód ve službě Azure App Service nebo virtuálním počítači Azure s povolenou spravovanou identitou, knihovna automaticky použije spravovanou identitu. Nejsou vyžadovány žádné změny kódu, ale spravovaná identita musí *mít* oprávnění pro trezor klíčů. Spravovanou identitu můžete *udělit* oprávnění prostřednictvím *přístupových zásad trezoru*klíčů .

Případně můžete ověřit pomocí identity přiřazené uživateli. Další informace o identitách přiřazených uživatelem najdete v tématu [O spravovaných identitách pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work). Chcete-li se ověřit pomocí identity přiřazené uživateli, je třeba zadat ID klienta identity přiřazené uživateli v připojovacím řetězci. Připojovací řetězec je určen v [poli Podpora připojovacího řetězce](#connection-string-support).

## <a name="running-the-application-using-a-service-principal"></a>Spuštění aplikace pomocí instančního objektu

Může být nutné vytvořit pověření klienta Azure AD k ověření. K této situaci může dojít v následujících příkladech:

- Váš kód běží v místním vývojovém prostředí, ale ne pod identitou vývojáře. Service Fabric například používá [účet NetworkService](../../service-fabric/service-fabric-application-secret-management.md) pro místní rozvoj.

- Váš kód běží v místním vývojovém prostředí a ověření na vlastní službu, takže nelze použít identitu vývojáře.

- Váš kód běží na výpočetní prostředek Azure, který ještě nepodporuje spravované identity pro prostředky Azure, jako je Azure Batch.

Existují tři primární metody použití instančního objektu ke spuštění aplikace. Chcete-li použít některý z nich, musíte nejprve vytvořit instanční objekt. Další informace najdete [v tématu Vytvoření instančního objektu Azure pomocí azure CLI](/cli/azure/create-an-azure-service-principal-azure-cli).

### <a name="use-a-certificate-in-local-keystore-to-sign-into-azure-ad"></a>Přihlášení do služby Azure AD pomocí certifikátu v místním úložišti klíčů

1. Vytvořte certifikát hlavního povinného poskytování služeb pomocí příkazu Azure CLI [az az ad sp create-for-rbac.](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac)

    ```azurecli
    az ad sp create-for-rbac --create-cert
    ```

    Tento příkaz vytvoří soubor PEM (soukromý klíč), který je uložen v domovském adresáři. Nasaďte tento certifikát do úložiště *LocalMachine* nebo *CurrentUser.*

    > [!Important]
    > Příkaz CLI generuje soubor .pem, ale systém Windows poskytuje pouze nativní podporu certifikátů PFX. Chcete-li místo toho vygenerovat certifikát PFX, použijte zde uvedené příkazy prostředí PowerShell: [Vytvoření instančního objektu s certifikátem podepsaným svým držitelem](../../active-directory/develop/howto-authenticate-service-principal-powershell.md#create-service-principal-with-self-signed-certificate). Tyto příkazy automaticky nasadit certifikát také.

1. Nastavte proměnnou prostředí s názvem **AzureServicesAuthConnectionString** na následující hodnotu:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};
          CertificateStoreLocation={CertificateStore}
    ```

    Nahraďte *{AppId}*, *{TenantId}* a *{Thumbprint}* hodnotami generovanými v kroku 1. Nahraďte *{CertificateStore}* buď *localmachine*' nebo *CurrentUser*, na základě plánu nasazení.

1. Spusťte aplikaci.

### <a name="use-a-shared-secret-credential-to-sign-into-azure-ad"></a>Přihlášení do Služby Azure AD pomocí sdílených tajných přihlašovacích údajů

1. Vytvořte certifikát hlavního povinného servisu s heslem pomocí [příkazu AZ az az sp create-for-rbac](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) azure s parametrem --sdk-auth.

    ```azurecli
    az ad sp create-for-rbac --sdk-auth
    ```

1. Nastavte proměnnou prostředí s názvem **AzureServicesAuthConnectionString** na následující hodnotu:

    ```azurecli
    RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}
    ```

    Nahraďte _{AppId}_, _{TenantId}_ a _{ClientSecret}_ hodnotami generovanými v kroku 1.

1. Spusťte aplikaci.

Jakmile je vše správně nastaveno, nejsou nutné žádné další změny kódu. `AzureServiceTokenProvider`používá proměnnou prostředí a certifikát k ověření ve službě Azure AD.

### <a name="use-a-certificate-in-key-vault-to-sign-into-azure-ad"></a>Přihlášení do služby Azure AD pomocí certifikátu v trezoru klíčů

Tato možnost umožňuje uložit klientský certifikát instančního objektu v trezoru klíčů a použít jej pro ověřování instančního objektu. Tuto možnost můžete použít v následujících scénářích:

- Místní ověřování, kde chcete ověřit pomocí explicitní holokaustu služby a chcete bezpečně uchovávat pověření instančního objektu v trezoru klíčů. Vývojářský účet musí mít přístup k trezoru klíčů.

- Ověřování z Azure, kde chcete použít explicitní pověření a chcete bezpečně uchovávat pověření hlavního povinného servisu v trezoru klíčů. Tuto možnost můžete použít pro scénář mezi tenanty. Spravovaná identita musí mít přístup k trezoru klíčů.

Spravovaná identita nebo identita vývojáře musí mít oprávnění k načtení klientského certifikátu z trezoru klíčů. Knihovna AppAuthentication používá načtený certifikát jako pověření klienta instančního objektu.

Použití klientského certifikátu pro ověřování instančního objektu:

1. Vytvořte certifikát hlavního povinného servisu a automaticky jej uložte do trezoru klíčů. Použijte příkaz [Az az az az az \<sp pro Azure pro \<rbac --keyvault keyvaultname> --cert certificatename> --create-cert --skip-assignment](/cli/azure/ad/sp?view=azure-cli-latest#az-ad-sp-create-for-rbac) příkaz:

    ```azurecli
    az ad sp create-for-rbac --keyvault <keyvaultname> --cert <certificatename> --create-cert --skip-assignment
    ```

    Identifikátor certifikátu bude adresa URL ve formátu`https://<keyvaultname>.vault.azure.net/secrets/<certificatename>`

1. Nahraďte `{KeyVaultCertificateSecretIdentifier}` v tomto připojovacím řetězci identifikátorem certifikátu:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}
    ```

    Pokud se například váš trezor klíčů nazýval *myKeyVault* a vytvořili jste certifikát s názvem *myCert*, identifikátor certifikátu by byl:

    ```azurecli
    RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier=https://myKeyVault.vault.azure.net/secrets/myCert
    ```

## <a name="connection-string-support"></a>Podpora připojovacího řetězce

Ve výchozím `AzureServiceTokenProvider` nastavení používá více metod k načtení tokenu.

Chcete-li řídit proces, použijte připojovací řetězec předaný konstruktoru `AzureServiceTokenProvider` nebo zadaný v proměnné prostředí *AzureServicesAuthConnectionString.*

Podporovány jsou následující možnosti:

| Možnost připojovacího řetězce | Scénář | Komentáře|
|:--------------------------------|:------------------------|:----------------------------|
| `RunAs=Developer; DeveloperTool=AzureCli` | Místní vývoj | `AzureServiceTokenProvider`používá AzureCli získat token. |
| `RunAs=Developer; DeveloperTool=VisualStudio` | Místní vývoj | `AzureServiceTokenProvider`používá Visual Studio získat token. |
| `RunAs=CurrentUser` | Místní vývoj | `AzureServiceTokenProvider`používá integrované ověřování Azure AD získat token. |
| `RunAs=App` | [Spravované identity pro prostředky Azure](../../active-directory/managed-identities-azure-resources/index.yml) | `AzureServiceTokenProvider`používá spravovanou identitu k získání tokenu. |
| `RunAs=App;AppId={ClientId of user-assigned identity}` | [Identita přiřazená uživatelem pro prostředky Azure](../../active-directory/managed-identities-azure-resources/overview.md#how-does-the-managed-identities-for-azure-resources-work) | `AzureServiceTokenProvider`používá uživatelem přiřazenou identitu k získání tokenu. |
| `RunAs=App;AppId={TestAppId};KeyVaultCertificateSecretIdentifier={KeyVaultCertificateSecretIdentifier}` | Ověřování vlastních služeb | `KeyVaultCertificateSecretIdentifier`je tajný identifikátor certifikátu. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateThumbprint={Thumbprint};CertificateStoreLocation={LocalMachine or CurrentUser}`| Instanční objekt | `AzureServiceTokenProvider`používá certifikát k získání tokenu z Azure AD. |
| `RunAs=App;AppId={AppId};TenantId={TenantId};CertificateSubjectName={Subject};CertificateStoreLocation={LocalMachine or CurrentUser}` | Instanční objekt | `AzureServiceTokenProvider`Používá certifikát k získání tokenu z Azure AD|
| `RunAs=App;AppId={AppId};TenantId={TenantId};AppKey={ClientSecret}` | Instanční objekt |`AzureServiceTokenProvider`používá tajný klíč k získání tokenu z Azure AD. |

## <a name="samples"></a>Ukázky

Chcete-li `Microsoft.Azure.Services.AppAuthentication` zobrazit knihovnu v akci, naleznete v následujících ukázkách kódu.

- [Použití spravované identity k načtení tajného klíče z úložiště klíčů Azure za běhu](https://github.com/Azure-Samples/app-service-msi-keyvault-dotnet)

- [Programově nasadit šablonu Azure Resource Manager z virtuálního počítače Azure se spravovanou identitou](https://github.com/Azure-Samples/windowsvm-msi-arm-dotnet).

- [Pomocí ukázky .NET Core a spravované identity můžete volat služby Azure z virtuálního počítače Azure Linux](https://github.com/Azure-Samples/linuxvm-msi-keyvault-arm-dotnet/).

## <a name="appauthentication-troubleshooting"></a>Řešení potíží s ověřováním appauthentication

### <a name="common-issues-during-local-development"></a>Běžné problémy během místního rozvoje

#### <a name="azure-cli-is-not-installed-youre-not-logged-in-or-you-dont-have-the-latest-version"></a>Azure CLI není nainstalovaný, nejste přihlášeni, nebo nemáte nejnovější verzi

Spusťte *az účet get-access-token* a zjistěte, jestli Azure CLI zobrazuje token pro vás. Pokud se **říká,** že žádný takový program nebyl nalezen , nainstalujte nejnovější verzi [příkazového příkazového příkazu Azure](/cli/azure/install-azure-cli?view=azure-cli-latest). Může se zobrazit výzva k přihlášení.

#### <a name="azureservicetokenprovider-cant-find-the-path-for-azure-cli"></a>AzureServiceTokenProvider nemůže najít cestu pro Azure CLI

AzureServiceTokenProvider hledá azure cli na jeho výchozí umístění instalace. Pokud nemůže najít Azure CLI, nastavte proměnnou prostředí **AzureCLIPath** do instalační složky Azure CLI. AzureServiceTokenProvider přidá proměnnou prostředí do proměnné prostředí Path.

#### <a name="youre-logged-into-azure-cli-using-multiple-accounts-the-same-account-has-access-to-subscriptions-in-multiple-tenants-or-you-get-an-access-denied-error-when-trying-to-make-calls-during-local-development"></a>Jste přihlášeni k Azure CLI pomocí více účtů, stejný účet má přístup k předplatným ve více tenantů, nebo se zobrazí chyba odepřen přístup při pokusu o volání během místního vývoje

Pomocí azure cli nastavte výchozí předplatné na ten, který má účet, který chcete použít. Předplatné musí být ve stejném tenantovi jako prostředek, ke kterém chcete získat přístup: **sada účtu az --subscription [subscription-id]**. Pokud není zobrazen žádný výstup, byl úspěšný. Ověřte, zda je správný účet nyní výchozí pomocí **seznamu účtů az**.

### <a name="common-issues-across-environments"></a>Běžné problémy napříč prostředími

#### <a name="unauthorized-access-access-denied-forbidden-or-similar-error"></a>Neoprávněný přístup, odepření, zakázání nebo podobná chyba

Použitý objekt zabezpečení nemá přístup k prostředku, ke kterému se pokouší získat přístup. Udělte buď svůj uživatelský účet, nebo přístup přispěvatele msi služby MSI k prostředku. Který z nich závisí na tom, jestli spouštějíte ukázku v místním počítači nebo nasadíte v Azure do služby App Service. Některé prostředky, jako jsou trezory klíčů, mají také vlastní [zásady přístupu,](https://docs.microsoft.com/azure/key-vault/secure-your-key-vault#data-plane-and-access-policies) které používáte udělit přístup k objektům zabezpečení, jako jsou uživatelé, aplikace a skupiny.

### <a name="common-issues-when-deployed-to-azure-app-service"></a>Běžné problémy při nasazení do služby Azure App Service

#### <a name="managed-identity-isnt-set-up-on-the-app-service"></a>Spravovaná identita není nastavená ve službě App Service

Zkontrolujte proměnné prostředí, MSI_ENDPOINT a MSI_SECRET existují pomocí [konzoly ladění Kudu](https://azure.microsoft.com/resources/videos/super-secret-kudu-debug-console-for-azure-web-sites/). Pokud tyto proměnné prostředí neexistují, spravovaná identita není povolena ve službě App Service.

### <a name="common-issues-when-deployed-locally-with-iis"></a>Běžné problémy při místním nasazení se službou IIS

#### <a name="cant-retrieve-tokens-when-debugging-app-in-iis"></a>Při ladění aplikace ve správě IIS nelze načíst tokeny.

Ve výchozím nastavení appauth běží v jiném uživatelském kontextu ve složce IIS. To je důvod, proč nemá přístup k použití vaší identity vývojáře k načtení přístupových tokenů. Službu IIS můžete nakonfigurovat tak, aby se spouštěla s uživatelským kontextem, a to pomocí následujících dvou kroků:
- Nakonfigurujte fond aplikací pro webovou aplikaci tak, aby běžela jako aktuální uživatelský účet. Více informací [zde](https://docs.microsoft.com/iis/manage/configuring-security/application-pool-identities#configuring-iis-application-pool-identities)
- Nakonfigurujte "setProfileEnvironment" na hodnotu True. Více informací [naleznete zde](https://docs.microsoft.com/iis/configuration/system.applicationhost/applicationpools/add/processmodel#configuration). 

    - Přejít na %windir%\System32\inetsrv\config\applicationHost.config
    - Vyhledejte výraz "setProfileEnvironment". Pokud je nastavena na "False", změňte ji na "True". Pokud není k dispozici, přidejte jej jako atribut/configuration/system.applicationHost/applicationPools/applicationPoolDefaults/processModel/@setProfileEnvironmentprocessModel element ( ) a nastavte jej na "True".

- Přečtěte si další informace o [spravovaných identitách pro prostředky Azure](../../active-directory/managed-identities-azure-resources/index.yml).
- Další informace o [scénářích ověřování Azure AD](../../active-directory/develop/active-directory-authentication-scenarios.md).
