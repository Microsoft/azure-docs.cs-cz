---
title: Přehled Azure AD B2C vlastní zásady | Microsoft Docs
description: Téma o Azure Active Directory B2C vlastních zásadách a prostředí identit.
services: active-directory-b2c
author: msmimart
manager: celestedg
ms.service: active-directory
ms.workload: identity
ms.topic: reference
ms.date: 12/14/2020
ms.author: mimart
ms.subservice: B2C
ms.openlocfilehash: ed477a931ed63c0db378ff84f85544072492ef96
ms.sourcegitcommit: ea17e3a6219f0f01330cf7610e54f033a394b459
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/14/2020
ms.locfileid: "97387033"
---
# <a name="azure-ad-b2c-custom-policy-overview"></a>Přehled Azure AD B2C vlastní zásady

Vlastní zásady jsou konfigurační soubory, které definují chování klienta Azure Active Directory B2C (Azure AD B2C). I když jsou [toky uživatelů](user-flow-overview.md) předdefinované na portálu Azure AD B2C pro nejběžnější úlohy identity. Vlastní zásady mohou být plně upravovány vývojářem identity, aby bylo možné dokončit mnoho různých úloh.

Vlastní zásady jsou plně konfigurovatelné, řízené pomocí zásad, které orchestrují vztahy důvěryhodnosti mezi entitami ve standardních formátech protokolů, jako jsou OpenID Connect, OAuth, SAML a několik nestandardních, například REST APIch výměn deklarací identity z systému na systém. Rozhraní vytváří uživatelsky přívětivé prostředí s bílým označením.

Vlastní zásady jsou reprezentovány jako jeden nebo více souborů ve formátu XML, které na sebe navzájem odkazují v hierarchickém řetězu. Prvky XML definují stavební blokes, interakci s uživatelem a další strany a obchodní logiku. 

## <a name="custom-policy-starter-pack"></a>Startovní sada vlastních zásad

[Počáteční sada](custom-policy-get-started.md#get-the-starter-pack) vlastních zásad Azure AD B2C obsahuje několik předem připravených zásad, které vám umožní rychle se vrátit. Každá z těchto startovních sad obsahuje nejmenší počet technických profilů a cest uživatelů potřebných k dosažení popsaných scénářů:

- **LocalAccounts** – povoluje pouze použití místních účtů.
- **SocialAccounts** – povoluje pouze použití sociálních (nebo federovaných) účtů.
- **SocialAndLocalAccounts** – povolí použití místních i sociálních účtů. Většina našich ukázek odkazuje na tuto zásadu.
- **SocialAndLocalAccountsWithMFA** – povolí možnosti pro sociální, místní a Multi-Factor Authentication.

## <a name="understanding-the-basics"></a>Seznámení se základy 

### <a name="claims"></a>Deklarace identit

Deklarace identity poskytuje dočasné úložiště dat během provádění zásad Azure AD B2C. Může ukládat informace o uživateli, jako je například křestní jméno, příjmení nebo jakákoli jiná deklarace identity získaná od uživatele nebo jiných systémů (výměny deklarací identity). [Schéma deklarací identity](claimsschema.md) je místo, kde deklarujete deklarace identity. 

Když se zásada spustí, Azure AD B2C odesílá a přijímá deklarace identity z interních a externích stran a pak odešle podmnožinu těchto deklarací do vaší aplikace předávající strany jako součást tokenu. Deklarace identity se používají v těchto ohledech: 

- Deklarace identity se uloží, přečte nebo aktualizuje s objektem uživatele adresáře.
- Od externího zprostředkovatele identity je obdržená deklarace identity.
- Deklarace identity se odesílají nebo přijímají pomocí vlastní REST API služby.
- Data se shromažďují jako deklarace identity od uživatele během toků profilů registrace nebo úprav.

### <a name="manipulating-your-claims"></a>Manipulace s deklaracemi

[Transformace deklarací identity](claimstransformations.md) jsou předdefinované funkce, které je možné použít k převedení dané deklarace na jinou, vyhodnocení deklarace identity nebo nastavení hodnoty deklarace identity. Například přidání položky do kolekce řetězců, změna velikosti písmen řetězce nebo vyhodnocení deklarace data a času. Transformace deklarací určuje metodu transformace. 

### <a name="customize-and-localize-your-ui"></a>Přizpůsobení a lokalizace uživatelského rozhraní

Pokud chcete shromažďovat informace od uživatelů tak, že ve svém webovém prohlížeči prezentujete stránku, použijte [technický profil s vlastním uplatněním](self-asserted-technical-profile.md). Technický profil s vlastním uplatněním můžete upravit [a přidat deklarace identity a přizpůsobit uživatelský vstup](custom-policy-configure-user-input.md).

Chcete-li [přizpůsobit uživatelské rozhraní](customize-ui-with-html.md) pro technický profil s vlastním uplatněním, zadejte adresu URL v prvku [definice obsahu](contentdefinitions.md) s přizpůsobeným obsahem HTML. V technickém profilu s vlastním uplatněním odkazujete na toto ID definice obsahu.

Chcete-li přizpůsobit řetězce pro konkrétní jazyk, použijte element [Localization](localization.md) . Definice obsahu může obsahovat odkaz na [lokalizaci](localization.md) , který určuje seznam lokalizovaných prostředků, které se mají načíst. Azure AD B2C sloučí prvky uživatelského rozhraní s obsahem HTML načteným z vaší adresy URL a pak zobrazí stránku uživateli. 

## <a name="relying-party-policy-overview"></a>Přehled zásad předávající strany

Aplikace předávající strany, nebo v protokolu SAML označovaném jako poskytovatel služeb, volá [zásady předávající strany](relyingparty.md) , aby spustily konkrétní cestu uživatele. Zásady předávající strany určují cestu uživatele, která se má spustit, a seznam deklarací identity, které token zahrnuje. 

![Diagram znázorňující tok spuštění zásad](./media/custom-policy-trust-frameworks/custom-policy-execution.png)

Všechny aplikace předávající strany, které používají stejné zásady, obdrží stejné deklarace identity a uživatel prochází stejnou cestou uživatele.

### <a name="user-journeys"></a>Cesty uživatelů

Pomocí cest [uživatelů](userjourneys.md) můžete definovat obchodní logiku s cestou, přes kterou bude uživatel postupovat, aby získal přístup k vaší aplikaci. Uživatel se převezme cestou uživatele, aby načetl deklarace identity, které mají být prezentovány vaší aplikaci. Cesta uživatele je sestavena z posloupnosti [kroků orchestrace](userjourneys.md#orchestrationsteps). Uživatel se musí dostat k poslednímu kroku, aby získal token. 

Následující článek popisuje, jak můžete přidat kroky orchestrace do zásady pro [Starter pro sociální a místní účet](https://github.com/Azure-Samples/active-directory-b2c-custom-policy-starterpack/tree/master/SocialAndLocalAccounts) . Zde je příklad volání REST API, které bylo přidáno.

![přizpůsobená cesta uživatele](media/custom-policy-trust-frameworks/user-journey-flow.png)


### <a name="orchestration-steps"></a>Kroky orchestrace

Krok Orchestration odkazuje na metodu, která implementuje zamýšlený účel nebo funkčnost. Tato metoda se nazývá [technický profil](technicalprofiles.md). Když vaše cesta uživatele potřebuje větvení, aby lépe představovala obchodní logiku, krok orchestrace odkazuje na [dílčí cestu](subjourneys.md). Dílčí cesta obsahuje svou vlastní sadu kroků orchestrace.

Uživatel se musí dostat k poslednímu kroku orchestrace v cestě uživatele a získat tak token. Ale uživatelé nemusí projít všemi kroky orchestrace. Kroky orchestrace můžou být podmíněně spouštěny na základě [předběžných podmínek](userjourneys.md#preconditions) definovaných v kroku orchestrace. 

Po dokončení kroku orchestrace Azure AD B2C uloží vložené deklarace do **kontejneru deklarací identity**. Deklarace identity v kontejneru deklarací identity můžou být využívány dalšími kroky orchestrace v cestě uživatele.

Následující diagram znázorňuje, jak můžou kroky orchestrace cest uživatelů získat přístup k kontejneru deklarací identity.

![Azure AD B2C cestu uživatele](media/custom-policy-trust-frameworks/user-journey-diagram.png)

### <a name="technical-profile"></a>Technický profil

Technický profil poskytuje rozhraní pro komunikaci s různými typy stran. Cesta uživatele kombinuje volání technických profilů prostřednictvím kroků orchestrace k definování obchodní logiky.

Všechny typy technických profilů sdílejí stejný koncept. Můžete odesílat vstupní deklarace identity, spouštět transformaci deklarací identity a komunikovat s nakonfigurovanou stranou. Až se proces dokončí, technický profil vrátí do kontejneru deklarací identity výstupní deklarace identity. Další informace najdete v tématu [Přehled technických profilů](technicalprofiles.md) .

### <a name="validation-technical-profile"></a>Technický profil ověřování

Když uživatel komunikuje s uživatelským rozhraním, může být vhodné ověřit shromážděná data. Pro interakci s uživatelem se musí použít [technický profil s vlastním uplatněním](self-asserted-technical-profile.md) .

K ověření vstupu uživatele se z technického profilu s vlastním uplatněním zavolá [ověřovací technický](validation-technical-profile.md) profil. 

Technický profil ověření je metoda volání libovolného neinteraktivního technického profilu. V takovém případě může technický profil vracet deklarace výstupů nebo chybovou zprávu. Chybová zpráva se vykreslí uživateli na obrazovce, takže se uživatel bude moct pokusit opakovat.

Následující diagram znázorňuje, jak Azure AD B2C používá k ověření přihlašovacích údajů uživatele technický profil ověřování.

![Diagram technického profilu ověření](media/custom-policy-trust-frameworks/validation-technical-profile.png)

## <a name="inheritance-model"></a>Model dědičnosti

Každá Startovní sada obsahuje následující soubory:

- **Základní** soubor, který obsahuje většinu definic. Pro pomoc s řešením potíží a dlouhodobou údržbou zásad doporučujeme provést v tomto souboru minimální počet změn.
- **Příponový** soubor, který obsahuje jedinečné změny konfigurace vašeho tenanta. Tento soubor zásad je odvozen ze základního souboru. Pomocí tohoto souboru můžete přidat nové funkce nebo přepsat existující funkce. Pomocí tohoto souboru můžete například federovat s novými zprostředkovateli identity.
- Soubor **předávající strany (RP)** , který je jedním souborem zaměřeným na úlohy, který je vyvolán přímo pomocí aplikace předávající strany, jako jsou webové, mobilní nebo desktopové aplikace. Každý jedinečný úkol, jako je registrace, přihlášení, resetování hesla nebo úprava profilu, vyžaduje vlastní soubor zásad předávající strany. Tento soubor zásad je odvozený od souboru rozšíření.

Model dědičnosti je následující:

- Podřízená zásada na libovolné úrovni může dědit z nadřazené zásady a rozšiřuje ji přidáním nových elementů.
- U složitějších scénářů můžete přidat další úroveň přírůstku (celkem až 5).
- Můžete přidat další zásady předávající strany. Například Odstraňte svůj účet, změňte telefonní číslo, zásadu předávající strany SAML a další.

Následující diagram znázorňuje vztah mezi soubory zásad a aplikacemi předávající strany.

![Diagram znázorňující model dědičnosti zásad pro pravidlo důvěryhodnosti](media/custom-policy-trust-frameworks/policies.png)


## <a name="guidance-and-best-practices"></a>Doprovodné materiály a osvědčené postupy

### <a name="best-practices"></a>Osvědčené postupy

V rámci Azure AD B2C vlastní zásady můžete integrovat vlastní obchodní logiku, která bude sestavovat uživatelské prostředí, které vyžaduje a rozšiřuje funkce služby. Máme sadu osvědčených postupů a doporučení, abyste mohli začít.

- Vytvořte logiku v rámci **zásad rozšíření** nebo **zásad předávající strany**. Můžete přidat nové prvky, které nahradí základní zásady odkazem na stejné ID. To vám umožní horizontální navýšení kapacity projektu a pozdější upgrade základních zásad, pokud společnost Microsoft vydává nové úvodní balíčky.
- V rámci **základních zásad** důrazně doporučujeme vyhnout se jakýmkoli změnám.  V případě potřeby udělejte komentáře, kde byly provedeny změny.
- Při přepisování elementu, jako jsou metadata technického profilu, se nekopíruje celý technický profil ze základních zásad. Místo toho zkopírujte pouze požadovanou část elementu. Příklad provedení změny najdete v tématu [Zakázání ověřování e-mailem](custom-policy-disable-email-verification.md) .
- Pokud chcete omezit duplicity technických profilů, ve kterých se sdílí základní funkce, použijte [zahrnutí technického profilu](technicalprofiles.md#include-technical-profile).
- Vyhněte se zápisu do adresáře služby Azure AD během přihlašování, což může vést k omezení problémů.
- Pokud vaše zásada obsahuje externí závislosti, například REST API, zajistíte vysokou dostupnost.
- Pro lepší uživatelské prostředí se ujistěte, že vlastní šablony HTML jsou globálně nasazené pomocí [online doručování obsahu](https://docs.microsoft.com/azure/cdn/). Azure Content Delivery Network (CDN) umožňuje zkrátit dobu načítání, ušetřit šířku pásma a zrychlit rychlost odezvy.
- Pokud chcete provést změnu na cestu uživatele. Zkopírujte veškerou cestu uživatele ze základní zásady na zásadu rozšíření. Zadejte jedinečné ID cesty uživatele pro cestu k uživateli, kterou jste zkopírovali. Pak v [zásadách předávající strany](relyingparty.md)změňte [výchozí prvek cesty uživatele](relyingparty.md#defaultuserjourney) tak, aby odkazoval na novou cestu uživatele.

## <a name="troubleshooting"></a>Řešení potíží

Při vývoji pomocí zásad Azure AD B2C můžete při provádění cesty uživatele spustit chyby nebo výjimky. Lze prozkoumat pomocí Application Insights.

- Integruje Application Insights s Azure AD B2C a [diagnostikuje výjimky](troubleshoot-with-application-insights.md).
- [Rozšíření Azure AD B2C pro vs Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c) vám může pomáhat při přístupu k [protokolům a jejich vizualizaci](https://github.com/azure-ad-b2c/vscode-extension/blob/master/src/help/app-insights.md) na základě názvu a času zásad.
- Nejběžnější Chyba při nastavování vlastních zásad je nesprávně naformátované XML. Použijte [ověřování schématu XML](troubleshoot-custom-policies.md) k identifikaci chyb před nahráním souboru XML.

## <a name="continuous-integration"></a>Kontinuální integrace

Pomocí kanálu průběžné integrace a doručování (CI/CD), který jste nastavili v Azure Pipelines, můžete [zahrnout vlastní zásady Azure AD B2C v rámci poskytování softwaru](deploy-custom-policies-devops.md) a automatizace řízení kódu. Při nasazení do různých Azure AD B2C prostředí, například pro vývoj, testování a produkci, doporučujeme odebrat ruční procesy a provádět automatizované testování pomocí Azure Pipelines.

## <a name="prepare-your-environment"></a>Příprava prostředí

Začnete s vlastními zásadami Azure AD B2C:

1. [Vytvoření tenanta Azure AD B2C](tutorial-create-tenant.md)
1. [Zaregistrujte webovou aplikaci](tutorial-register-applications.md) pomocí Azure Portal. Vaše zásady tak budete moct testovat.
1. Přidejte potřebné [klíče zásad](custom-policy-get-started.md#add-signing-and-encryption-keys) a [Zaregistrujte aplikace architektury identit prostředí identity](custom-policy-get-started.md#register-identity-experience-framework-applications) .
1. [Získejte úvodní sadu zásad Azure AD B2C](custom-policy-get-started.md#get-the-starter-pack) a nahrajte ji do svého tenanta. 
1. Po nahrání počátečního balíčku [otestujte zásady registrace nebo přihlašování](custom-policy-get-started.md#test-the-custom-policy) .
1. Doporučujeme, abyste si stáhli a nainstalovali [Visual Studio Code](https://code.visualstudio.com/) (vs Code). Visual Studio Code je jednoduchý, ale výkonný editor zdrojového kódu, který běží na vaší ploše a je dostupný pro Windows, macOS a Linux. Pomocí VS Code můžete upravovat soubory XML vlastních zásad Azure AD B2C.
1. Pokud chcete rychle procházet Azure AD B2C vlastní zásady, doporučujeme nainstalovat [Azure AD B2C rozšíření pro vs Code](https://marketplace.visualstudio.com/items?itemName=AzureADB2CTools.aadb2c)
 
## <a name="next-steps"></a>Další kroky

Po nastavení a otestování zásad Azure AD B2C můžete začít přizpůsobovat zásady. V následujících článcích se dozvíte, jak:

- [Přidání deklarací identity a přizpůsobení uživatelského vstupu](custom-policy-configure-user-input.md) pomocí vlastních zásad. Přečtěte si, jak definovat deklaraci identity, přidání deklarace identity do uživatelského rozhraní úpravou některých technických profilů pro startovní Pack.
- [Přizpůsobte uživatelské rozhraní](customize-ui-with-html.md) aplikace pomocí vlastní zásady. Naučte se vytvářet vlastní obsah HTML a přizpůsobovat definici obsahu.
- [Lokalizovat uživatelské rozhraní](custom-policy-localization.md) aplikace pomocí vlastních zásad. Přečtěte si, jak nastavit seznam podporovaných jazyků a zadat popisky specifické pro jazyk přidáním prvku lokalizovaných prostředků.
- Během vývoje a testování zásad můžete [zakázat ověřování e-mailů](custom-policy-disable-email-verification.md). Přečtěte si, jak přepsat metadata technického profilu.
- [Nastavte přihlášení pomocí účtu Google](identity-provider-google-custom.md) pomocí vlastních zásad. Naučte se, jak vytvořit nového zprostředkovatele deklarací identity pomocí OAuth2 Technical Profile. Pak upravte cestu uživatele tak, aby zahrnovala možnost přihlášení Google.
- K diagnostice problémů s vlastními zásadami můžete [shromažďovat protokoly Azure Active Directory B2C pomocí Application Insights](troubleshoot-with-application-insights.md). Naučte se přidávat nové technické profily a konfigurovat zásady předávací strany.