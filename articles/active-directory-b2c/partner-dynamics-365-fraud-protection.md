---
title: Kurz konfigurace Azure Active Directory B2C s ochranou podvodů v Microsoft Dynamics 365
titleSuffix: Azure AD B2C
description: Kurz konfigurace Azure Active Directory B2C s ochranou podvodů v Microsoft Dynamics 365 pro identifikaci rizikového a podvodného účtu
services: active-directory-b2c
author: gargi-sinha
manager: martinco
ms.service: active-directory
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2021
ms.author: gasinh
ms.subservice: B2C
ms.openlocfilehash: cf441108c9fd0ae87f265604f6f0706d92516746
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101646550"
---
# <a name="tutorial-configure-microsoft-dynamics-365-fraud-protection-with-azure-active-directory-b2c"></a>Kurz: Konfigurace ochrany proti podvodům v Microsoft Dynamics 365 pomocí Azure Active Directory B2C

V tomto ukázkovém kurzu poskytujeme pokyny k integraci [ochrany před únikem informací v Microsoft Dynamics 365](/dynamics365/fraud-protection/overview) (DFP) pomocí služby Azure Active Directory (AD) B2C.

Microsoft DFP poskytuje klientům možnost posoudit, jestli riziko pokusů o vytvoření nových účtů a pokusů o přihlášení k ekosystému klienta je podvodné. Společnost Microsoft DFP Assessment může být používána zákazníkem k blokování nebo výzvě podezřelých pokusů o vytvoření nových falešných účtů nebo k ohrožení stávajících účtů. Ochrana účtů zahrnuje pomocí otisků prstů zařízení, rozhraní API pro posuzování rizik v reálném čase, pravidla a seznam, optimalizaci rizikových strategií podle obchodních potřeb klienta a scorecard pro monitorování efektivity ochrany před únikem informací a trendy v ekosystému klienta.

V této ukázce budeme integraci funkcí ochrany účtů Microsoft DFP s Azure AD B2Cým uživatelským tokem. Služba bude Přihlaste se k externímu otisku při každém přihlášení nebo při pokusu o přihlášení a sledujte jakékoli minulé nebo přítomné podezřelé chování. Azure AD B2C vyvolá rozhodovací koncový bod z Microsoft DFP, který vrátí výsledek založený na všech minulých a současných chování od identifikovaného uživatele a také vlastní pravidla zadaná v rámci služby Microsoft DFP. Azure AD B2C provede rozhodnutí o schválení na základě tohoto výsledku a předá ho zpět do Microsoft DFP.

## <a name="prerequisites"></a>Požadavky

Abyste mohli začít, budete potřebovat:

- Předplatné Azure. Pokud předplatné nemáte, můžete získat [bezplatný účet](https://azure.microsoft.com/free/).

- [Tenant Azure AD B2C](./tutorial-create-tenant.md). Tenant je propojený s vaším předplatným Azure.

- Získejte [předplatné](https://dynamics.microsoft.com/pricing/#Sales)Microsoft DFPe. Můžete nastavit i [zkušební verzi klienta](https://dynamics.microsoft.com/ai/fraud-protection/signin/?RU=https%3A%2F%2Fdfp.microsoft.com%2Fsignin) .

## <a name="scenario-description"></a>Popis scénáře

Integrace Microsoft DFP zahrnuje tyto komponenty:

- **Azure AD B2C tenant**: ověřuje uživatele a funguje jako klient služby Microsoft DFP. Hostuje skript pro otisky prstů shromažďováním identifikačních a diagnostických dat každého uživatele, který spouští cílovou zásadu. Pozdější blokování nebo výzvy k přihlášení nebo pokusy o registraci, pokud je Microsoft DFP najde podezřelé.

- **Vlastní služba App Service**: webová aplikace, která slouží ke dvěma účelům.

  - Obsluhuje stránky HTML jako uživatelské rozhraní architektury rozhraní identity. Zodpovídá za vkládání skriptu otisku prstu Microsoft Dynamics 365.

  - Kontroler rozhraní API s koncovými body RESTful, které připojují Microsoft DFP k Azure AD B2C. Zpracování dat, struktury a dodržování požadavků na zabezpečení obou.

- **Služba Microsoft DFP otisků prstů**: dynamicky vložený skript, který zapisuje telemetrii zařízení a podrobnosti uživatele s vlastním uplatněním k vytvoření jednoznačně identifikovatelného otisku prstu pro uživatele, který se použije později v procesu rozhodování.

- **Koncové body rozhraní API pro Microsoft DFP**: poskytuje výsledek rozhodnutí a přijímá konečný stav odrážející operaci prováděnou klientskou aplikací. Azure AD B2C nekomunikuje s koncovými body přímo z důvodu různých požadavků na zabezpečení a datovou část rozhraní API, místo toho používá službu App Service jako zprostředkující.

V následujícím diagramu architektury se zobrazuje implementace.

![Obrázek znázorňuje diagram architektury ochrany před podvody Microsoft dynamics365](./media/partner-dynamics365-fraud-protection/microsoft-dynamics-365-fraud-protection-diagram.png)

|Krok | Popis |
|:-----| :-----------|
| 1. | Uživatel dostane přihlašovací stránku. Uživatelé si vyberou přihlášení a vytvoří nový účet a na stránce se zadají informace. Azure AD B2C shromažďuje atributy uživatele.
| 2. | Azure AD B2C volá rozhraní API střední vrstvy a předá ho atributům uživatele.
| 3. | Rozhraní API prostřední vrstvy shromažďuje atributy uživatelů a transformuje je do formátu, který může využívat rozhraní Microsoft DFP API. Poté po odeslání do rozhraní Microsoft DFP API.
| 4. | Když rozhraní Microsoft DFP API spotřebuje informace a zpracuje je, vrátí výsledek do prostřední vrstvy API.
| 5. | Rozhraní API střední vrstvy tyto informace zpracovává a odesílá zpátky relevantní informace pro Azure AD B2C.
| 6. | Azure AD B2C přijímá informace zpátky z rozhraní API střední vrstvy. Pokud se zobrazí reakce na selhání, zobrazí se uživateli chybová zpráva. Pokud se zobrazí odpověď úspěšnosti, je uživatel ověřen a zapsán do adresáře.

## <a name="set-up-the-solution"></a>Nastavení řešení

1. [Vytvořte aplikaci Facebooku](./identity-provider-facebook.md#create-a-facebook-application) nakonfigurovanou tak, aby povolovala Azure AD B2C federaci.
2. [Přidejte tajný klíč Facebooku](./custom-policy-get-started.md#create-the-facebook-key) , který jste vytvořili jako klíč zásad rozhraní identity Experience Framework.

## <a name="configure-your-application-under-microsoft-dfp"></a>Konfigurace aplikace v rámci Microsoft DFP

[Nastavte tenanta Azure AD](/dynamics365/fraud-protection/integrate-real-time-api) tak, aby používal Microsoft DFP.

## <a name="deploy-to-the-web-application"></a>Nasazení do webové aplikace

### <a name="implement-microsoft-dfp-service-fingerprinting"></a>Implementace otisku prstu služby Microsoft DFP

[Otisky zařízení Microsoft DFP](/dynamics365/fraud-protection/device-fingerprinting) je požadavek na ochranu účtu Microsoft DFP.

>[!NOTE]
>Kromě Azure AD B2C stránek uživatelského rozhraní může zákazník také implementovat službu otisků prstů uvnitř kódu aplikace a komplexnější profilování zařízení. V této ukázce není zahrnutá služba otisků prstů v kódu aplikace.

### <a name="deploy-the-azure-ad-b2c-api-code"></a>Nasazení kódu rozhraní Azure AD B2C API

Nasaďte [poskytnutý kód rozhraní API](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/API) do služby Azure. Kód lze [publikovat ze sady Visual Studio](/visualstudio/deployment/quickstart-deploy-to-azure?view=vs-2019).

Nastavení CORS, přidání **povoleného původu**`https://{your_tenant_name}.b2clogin.com`

>[!NOTE]
>Později budete potřebovat adresu URL nasazené služby, abyste mohli nakonfigurovat Azure AD s požadovaným nastavením.

Další informace najdete v [dokumentaci ke službě App Service](../app-service/app-service-web-tutorial-rest-api.md) .

### <a name="add-context-dependent-configuration-settings"></a>Přidat nastavení konfigurace závislé na kontextu

Nakonfigurujte nastavení aplikace ve [službě App Service v Azure](../app-service/configure-common.md#configure-app-settings). To umožňuje zabezpečenou konfiguraci nastavení bez jejich vrácení se změnami do úložiště. Rozhraní REST API vyžaduje následující nastavení:

| Nastavení aplikace | Zdroj | Poznámky |
| :-------- | :------------| :-----------|
|FraudProtectionSettings: InstanceId | Konfigurace Microsoft DFP |     |
|FraudProtectionSettings:DeviceFingerprintingCustomerId | ID zákazníka otisku prstu zařízení Microsoftu |     |
| FraudProtectionSettings:ApiBaseUrl |  Vaše základní adresa URL z portálu Microsoft DFP   | Pokud chcete místo toho volat rozhraní API pro produkční prostředí, odeberte klíčové slovo-int.
|  TokenProviderConfig: prostředek | https://api.dfp.dynamics-int.com |   Pokud chcete místo toho volat rozhraní API pro produkční prostředí, odeberte klíčové slovo-int.  |
|   TokenProviderConfig: ClientId       |ID klientské aplikace Azure AD ochrany před podvody      |       |
| TokenProviderConfig: autorita | https://login.microsoftonline.com/<directory_ID> | Autorita tenanta ochrany před podvody vaší obchodní autority Azure AD |
| TokenProviderConfig: CertificateThumbprint * | Kryptografický otisk certifikátu, který se má použít k ověření vůči klientské aplikaci Azure AD |
| TokenProviderConfig: ClientSecret * | Tajný kód pro klientskou aplikaci Azure AD | Doporučuje se použít Správce tajných klíčů. |

* V závislosti na tom, jestli se ověřujete pomocí certifikátu nebo tajného klíče, jako je třeba heslo, nastavte jenom 1 z 2 označených parametrů.

## <a name="azure-ad-b2c-configuration"></a>Konfigurace Azure AD B2C

### <a name="replace-the-configuration-values"></a>Nahraďte konfigurační hodnoty.

V části poskytnuté [vlastní zásady](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies)vyhledejte následující zástupné symboly a nahraďte je odpovídajícími hodnotami z vaší instance.

| Zástupný symbol | Nahradit hodnotou | Poznámky |
| :-------- | :------------| :-----------|
|{your_tenant_name} | Krátký název tenanta |  "yourtenant" z yourtenant.onmicrosoft.com   |
|{your_tenantId} | ID tenanta vašeho tenanta Azure AD B2C |  01234567-89AB-CDEF-0123-456789ABCDEF   |
|  {your_tenant_IdentityExperienceFramework_appid}    |   ID aplikace IdentityExperienceFramework nakonfigurované ve vašem tenantovi Azure AD B2C    |  01234567-89AB-CDEF-0123-456789ABCDEF   |
|  {your_tenant_ ProxyIdentityExperienceFramework _appid}     |  ID aplikace ProxyIdentityExperienceFramework nakonfigurované ve vašem tenantovi Azure AD B2C      |   01234567-89AB-CDEF-0123-456789ABCDEF     |
|  {your_tenant_extensions_appid}   |  ID aplikace pro aplikaci úložiště vašeho tenanta   |  01234567-89AB-CDEF-0123-456789ABCDEF  |
|   {your_tenant_extensions_app_objectid}  | ID objektu aplikace úložiště vašeho tenanta    | 01234567-89AB-CDEF-0123-456789ABCDEF   |
|   {your_app_insights_instrumentation_key}  |   Klíč instrumentace instance App Insights *  |   01234567-89AB-CDEF-0123-456789ABCDEF |
|  {your_ui_base_url}   | Koncový bod ve službě App Service, ze které jsou obsluhovány soubory uživatelského rozhraní    | https://yourapp.azurewebsites.net/B2CUI/GetUIPage   |
|   {your_app_service_url}  | Adresa URL služby App Service    |  https://yourapp.azurewebsites.net  |
|   {vaše-Facebook-App-ID}  |  ID aplikace pro Facebook, kterou jste nakonfigurovali pro federaci s Azure AD B2C   | 000000000000000   |
|  {vaše aplikace Facebook-App-Secret}   |  Název klíče zásad, na který jste uložili tajný klíč aplikace Facebook   | B2C_1A_FacebookAppSecret   |

* App Insights může být v jiném tenantovi. Tento krok je volitelný. Odeberte odpovídající TechnicalProfiles a OrechestrationSteps, pokud není potřeba.

### <a name="call-microsoft-dfp-label-api"></a>Volání rozhraní Microsoft DFP Label API

Zákazníci musí [implementovat rozhraní API pro označování](/dynamics365/fraud-protection/integrate-ap-api). Další informace najdete v tématu [rozhraní API pro Microsoft DFP](https://apidocs.microsoft.com/services/dynamics365fraudprotection#/AccountProtection/v1.0) .

`URI: < API Endpoint >/v1.0/label/account/create/<userId>`

Hodnota ID uživatele musí být stejná jako ta v odpovídající hodnotě konfigurace Azure AD B2C (ObjectID).

>[!NOTE]
>Přidejte oznámení o souhlasu na stránku kolekce atributů. Informujte, že se budou zaznamenávat informace o telemetrie uživatelů a identitě uživatelů pro účely ochrany účtů.

## <a name="configure-the-azure-ad-b2c-policy"></a>Konfigurace zásad Azure AD B2C

1. Ve složce policies (zásady) vyberte [zásady Azure AD B2C](https://github.com/azure-ad-b2c/partner-integrations/tree/master/samples/Dynamics-Fraud-Protection/Policies) .

2. Pomocí tohoto [dokumentu](./custom-policy-get-started.md?tabs=applications#custom-policy-starter-pack) stáhněte [LocalAccounts Starter Pack](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/LocalAccounts) .

3. Nakonfigurujte zásady pro klienta Azure AD B2C.

>[!NOTE]
>Aktualizujte zásady, které se mají vztahovat k vašemu konkrétnímu tenantovi.

## <a name="test-the-user-flow"></a>Testování toku uživatele

1. Otevřete Azure AD B2C tenant a v části zásady vyberte **Architektura prostředí identity**.

2. Vyberte dříve vytvořenou **SignUpSignIn**.

3. Vyberte **Spustit tok uživatele** a vyberte nastavení:

   a. **Aplikace**: vyberte registrovanou aplikaci (ukázka je JWT).

   b. **Adresa URL odpovědi**: vyberte **adresu URL pro přesměrování** .

   c. Vyberte **Spustit tok uživatele**.

4. Projděte si registrační tok a vytvořte účet.

5. Po vytvoření atributu uživatele bude v průběhu toku volána služba Microsoft DFP. Pokud tok není úplný, ověřte, že uživatel není uložen v adresáři.

>[!NOTE]
>Aktualizujte pravidla přímo na portálu Microsoft DFP, pokud používáte [modul pravidel Microsoft DFP](/dynamics365/fraud-protection/rules).

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících článcích:

- [Ukázky Microsoft DFP](https://github.com/Microsoft/Dynamics-365-Fraud-Protection-Samples)

- [Vlastní zásady v Azure AD B2C](./custom-policy-overview.md)

- [Začínáme s vlastními zásadami v Azure AD B2C](./custom-policy-get-started.md?tabs=applications)
