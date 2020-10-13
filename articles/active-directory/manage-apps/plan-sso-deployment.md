---
title: Plánování nasazení s jednotným přihlašováním Azure Active Directory
description: Průvodce vám pomůže s plánováním, nasazením a správou jednotného přihlašování ve vaší organizaci.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 06/10/2020
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.custom: has-adal-ref
ms.openlocfilehash: 87d455a77096a2ae9339c578f3405c629d79fa76
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90603335"
---
# <a name="plan-a-single-sign-on-deployment"></a>Plánování nasazení jednotného přihlašování

Jednotné přihlašování (SSO) znamená přístup k všem aplikacím a prostředkům, které uživatel potřebuje, pomocí jediného uživatelského účtu. Pomocí jednotného přihlašování uživatelé mají přístup ke všem potřebným aplikacím, aniž by se museli ověřit podruhé.

## <a name="benefits-of-sso"></a>Výhody jednotného přihlašování

Jednotné přihlašování (SSO) zvyšuje zabezpečení a pohodlí, když se uživatelé přihlásí k aplikacím v Azure Active Directory (Azure AD). 

Mnoho organizací spoléhá na aplikace typu software jako služba (SaaS), jako je například Microsoft 365, box nebo Salesforce, pro produktivitu koncových uživatelů. Historicky zaměstnanci IT potřebují individuálně vytvářet a aktualizovat uživatelské účty v jednotlivých aplikacích SaaS a uživatelé si pro ně museli pamatovat heslo.

Azure Marketplace má více než 3000 aplikací s předem integrovanými připojeními SSO, což usnadňuje jejich integraci ve vašem tenantovi.

## <a name="licensing"></a>Licensing

- **Licencování Azure AD** – jednotné přihlašování pro předem integrované aplikace SaaS je zdarma. Nicméně počet objektů ve vašem adresáři a funkce, které chcete nasadit, můžou vyžadovat další licence. Úplný seznam licenčních požadavků najdete v tématu [Azure Active Directory ceny](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licencování aplikací** – k uspokojení vašich obchodních potřeb budete potřebovat příslušné licence pro aplikace SaaS. Spolupracujte s vlastníkem aplikace a zjistěte, jestli uživatelé přiřazení k aplikaci mají příslušné licence pro své role v rámci aplikace. Pokud Azure AD spravuje Automatické zřizování na základě rolí, role přiřazené v Azure AD musí odpovídat počtu licencí vlastněných v rámci aplikace. Nesprávný počet licencí vlastněných v aplikaci může vést k chybám během zřizování nebo aktualizace uživatele.

## <a name="plan-your-sso-team"></a>Plánování vašeho týmu jednotného přihlašování

- **Zapojení správných zúčastněných stran** – když se technologické projekty nezdaří, je obvykle způsobeno neodpovídajícími očekáváními v souvislosti s dopadem, výsledky a odpovědnostmi. Abyste se vyhnuli těmto nástrah, [Ujistěte se, že jste přiuměli správným zúčastněným stranám](https://aka.ms/deploymentplans) a že zúčastněné strany porozuměl jejich rolí
- **Plánování komunikace** – komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně komunikujte uživatelům o tom, jak se budou měnit, když se změní, a jak získat podporu, pokud dojde k problémům. Projděte si možnosti, [jak budou koncoví uživatelé přistupovat ke svým aplikacím s povoleným SSO](end-user-experiences.md)a že vaše komunikace bude odpovídat vašemu výběru. 

## <a name="plan-your-sso-protocol"></a>Plánování protokolu SSO

Implementace jednotného přihlašování na základě federačních protokolů vylepšuje možnosti zabezpečení, spolehlivosti a koncových uživatelů a je snazší ji implementovat. Mnoho aplikací je předem integrovaných ve službě Azure AD s [dostupnými podrobnými návody](../saas-apps/tutorial-list.md). Můžete je najít na našem [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Podrobné informace o jednotlivých metodách jednotného přihlašování najdete v článku [jednotné přihlašování k aplikacím v Azure Active Directory](what-is-single-sign-on.md).

Existují dva základní způsoby, kterými můžete uživatelům umožnit jednotné přihlašování k vašim aplikacím:

- **Se federovaném jednotným přihlašováním** Azure AD ověřuje uživatele v aplikaci pomocí svého účtu Azure AD. Tato metoda je podporovaná pro aplikace, které podporují protokoly jako SAML 2,0, WS-Federation nebo OpenID Connect, a je nejrozsáhlejším režimem jednotného přihlašování. Nedoporučujeme používat federované jednotné přihlašování se službou Azure AD, když ji aplikace podporuje místo jednotného přihlašování založeného na heslech a ADFS.

- Když se uživatelé s **jednotným přihlašováním založené na heslech** přihlásí k aplikaci pomocí uživatelského jména a hesla poprvé, budou k nim mít přístup. Po prvním přihlášení Azure AD zadá uživatelské jméno a heslo k aplikaci. Jednotné přihlašování založené na heslech umožňuje zabezpečené ukládání hesel aplikací a přehrávání pomocí rozšíření webového prohlížeče nebo mobilní aplikace. Tato možnost využívá stávající proces přihlášení, který je součástí aplikace, umožňuje správcům spravovat hesla a nevyžaduje, aby uživatel znal heslo.

### <a name="considerations-for-federation-based-sso"></a>Pokyny pro jednotné přihlašování na základě federace

- **Pomocí OpenID Connect a OAuth** – Pokud aplikace, ke které se připojujete, používá metodu OIDC/OAuth 2,0 k povolení jednotného přihlašování k této aplikaci. Tato metoda vyžaduje méně konfigurace a umožňuje bohatší uživatelské prostředí. Další informace najdete v tématu [OAuth 2,0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1,0](../develop/v2-protocols-oidc.md)a [Azure Active Directory příručka pro vývojáře](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Konfigurace koncových bodů pro jednotné přihlašování založené na SAML** – Pokud použijete SAML, vývojáři budou před konfigurací aplikace potřebovat konkrétní informace. Další informace najdete v tématu [Konfigurace jednotného přihlašování založeného na SAML](configure-saml-single-sign-on.md).
- **Správa certifikátů pro jednotné přihlašování založené na SAML** – Pokud pro svou aplikaci povolíte federované jednotné přihlašování (SSO), Azure AD vytvoří certifikát, který je ve výchozím nastavení platný po dobu tří let. V případě potřeby můžete upravit datum vypršení platnosti tohoto certifikátu. Ujistěte se, že máte zavedené procesy pro obnovení certifikátů před jejich vypršením platnosti. Další informace najdete v tématu [Správa certifikátů v Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Pokyny pro jednotné přihlašování založené na heslech

Použití Azure AD pro jednotné přihlašování pomocí hesla vyžaduje nasazení rozšíření prohlížeče, které bude bezpečně získávat přihlašovací údaje a vyplní přihlašovací formuláře. Definujte mechanismus nasazení rozšíření ve velkém měřítku s využitím [podporovaných prohlížečů](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Mezi možnosti patří:

- [Zásady skupiny pro Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Configuration Manager pro Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Stažení a konfigurace řízená uživatelem pro Chrome, Firefox, Microsoft Edge nebo IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Další informace najdete v tématu [Postup konfigurace jednotného přihlašování k heslu](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Zachytávání metadat přihlašovacích formulářů pro aplikace, které nejsou v galerii

Microsoft podporuje zachytávání metadat pro webovou aplikaci pro trezor hesel (zaznamenání polí uživatelské jméno a heslo). Při konfiguraci aplikace pro zachycení metadat formulářů přejděte na přihlašovací adresu URL. Zeptejte se vlastníka aplikace na přesnou adresu URL pro přihlášení. Tyto informace se používají během procesu přihlašování a mapují přihlašovací údaje Azure AD na aplikaci během přihlašování.

Další informace najdete v tématu [co je přístup k aplikacím a jednotné přihlašování pomocí služby Azure AD? – jednotné přihlašování založené na heslech](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Označení, že metadata ve formulářích je potřeba znovu zachytit

Když aplikace změní rozložení HTML, může být nutné znovu zachytit metadata, aby bylo možné změny upravit. Mezi běžné příznaky indikující změnu rozložení HTML patří:

- Uživatelům, kteří vytvářejí sestavy kliknutím na aplikaci, se na přihlašovací stránce dostane zablokovat.
- Uživatelé, kteří hlásí, že uživatelské jméno nebo heslo nejsou naplněny

#### <a name="shared-accounts"></a>Sdílené účty

V perspektivě přihlašování se aplikace se sdílenými účty neliší od aplikace Galerie, která pro jednotlivé uživatele používá jednotné přihlašování pomocí hesla. Při plánování a konfiguraci aplikace, která je určena k používání sdílených účtů, je však potřeba provést několik dalších kroků:

1. Pracujte s podnikovými uživateli aplikace a zdokumentujte následující:
   1. Sada uživatelů v organizaci, kteří budou aplikaci používat
   1. Existující sada přihlašovacích údajů v aplikaci přidružené k sadě uživatelů 
1. Pro každou kombinaci uživatelských sad a přihlašovacích údajů vytvořte skupinu zabezpečení v cloudu nebo v místním prostředí podle svých požadavků.
1. Obnovte sdílené přihlašovací údaje. Jakmile je aplikace nasazená ve službě Azure AD, uživatelé nepotřebují heslo sdíleného účtu. Vzhledem k tomu, že Azure AD uloží heslo, zvažte nastavení, aby bylo velmi dlouhé a složité. 
1. Nastavte automatickou výměnu hesla, pokud ji aplikace podporuje. Tímto způsobem není ani správce, který měl počáteční nastavení, znát heslo ke sdílenému účtu. 

## <a name="plan-your-authentication-method"></a>Naplánování metody ověřování

Volba správné metody ověřování je zásadním prvním rozhodnutím při nastavování řešení hybridní identity Azure AD. Implementujte metodu ověřování, která je nakonfigurovaná pomocí Azure AD Connect, která taky zřídí uživatele v cloudu.

Pokud chcete zvolit metodu ověřování, musíte vzít v úvahu čas, stávající infrastrukturu, složitost a náklady na implementaci podle vašeho výběru. Tyto faktory jsou pro každou organizaci odlišné a můžou se v průběhu času měnit. Měli byste zvolit ten, který nejlépe odpovídá vašemu konkrétnímu scénáři. Další informace najdete v tématu [Volba správné metody ověřování pro Azure Active Directory řešení hybridní identity](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn).

## <a name="plan-your-security-and-governance"></a>Plánování zabezpečení a zásad správného řízení 

Identita je novým primárním Pivotem z hlediska zabezpečení a cenných papírů, protože hraniční sítě jsou stále stále Porous a méně efektivní s rozbalením zařízení BYOD a cloudových aplikací. 

### <a name="plan-access-reviews"></a>Plánování kontrol přístupu

Kontroly [přístupu](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) umožňují organizacím efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazování rolí. Měli byste naplánovat pravidelné kontroly přístupu uživatelů, abyste měli jistotu, že budou mít přístup jenom přípravní uživatelé.

Mezi klíčová témata, která je potřeba naplánovat při nastavování kontrol přístupu, patří:

1. Identifikujte tempo pro kontroly přístupu, které vyhovují vašemu podniku. To může být tak časté, jako jednou týdně, měsíčně, ročně nebo jako cvičení na vyžádání.

1. Vytvořte skupiny, které reprezentují kontrolory sestav aplikace Access. Musíte zajistit, aby účastníci s aplikací a jejími cílovými uživateli a případy použití měli jistotu, že jsou vašimi recenzemi v Accessu.

1. Dokončení kontroly přístupu zahrnuje přístup k aplikacím bez oprávnění k přístupu pro uživatele, kteří už nepotřebují přístup. Plán pro zpracování potenciálních žádostí o podporu od zamítnutých uživatelů Odstraněný uživatel zůstane ve službě Azure AD po dobu 30 dnů odstraněný a v případě potřeby ho může obnovit správce. Po 30 dnech je tento uživatel odstraněn trvale. Pomocí Azure Active Directoryového portálu může globální správce explicitně trvale odstranit nedávno odstraněného uživatele před uplynutím tohoto časového období.

### <a name="plan-auditing"></a>Plánování auditování

Azure AD poskytuje [sestavy obsahující technické a obchodní přehledy](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). 

K dispozici jsou jak sestavy zabezpečení, tak aktivity. Sestavy zabezpečení zobrazují uživatele označené příznakem rizika a rizikovým přihlášením. Sestavy aktivit vám pomohou pochopit chování uživatelů ve vaší organizaci tím, že se podrobí přihlašovací aktivity a poskytnou revizní záznamy o všech přihlášeních. Pomocí sestav můžete spravovat rizika, zvýšit produktivitu a monitorovat dodržování předpisů.

| Typ sestavy | Kontrola přístupu | Sestavy zabezpečení | Sestava přihlášení |
|-------------|---------------|------------------|----------------|
| Použít ke kontrole | Oprávnění a používání aplikace. | Potenciálně ohrožené účty | Kdo přistupuje k aplikacím |
| Potenciální akce | Přístup pro audit; odvolat oprávnění | Odvolat přístup; vynutit resetování zabezpečení | Odvolat přístup |

Azure AD uchovává většinu dat auditování po dobu 30 dnů a zpřístupňuje data prostřednictvím portálu pro správu Azure nebo prostřednictvím rozhraní API, které můžete stáhnout do svých analytických systémů.

### <a name="consider-using-microsoft-cloud-application-security"></a>Zvažte použití Microsoft Cloud zabezpečení aplikací.

Microsoft Cloud App Security (MCAS) je řešení Cloud Access Security broker (CASB). Poskytuje přehled o vašich cloudových aplikacích a službách, zajišťuje propracované analýzy pro identifikaci a boj proti týká kybernetických hrozeb a umožňuje řídit, jak se data cestují.

Nasazení MCAS vám umožní:

- Použijte Cloud Discovery k mapování a identifikaci cloudového prostředí a cloudových aplikací, které vaše organizace používá.
- Schválení a zrušení schválení aplikací v cloudu
- Používejte snadno nasazovatelné konektory aplikací, které využívají rozhraní API pro poskytovatele, pro viditelnost a zásady správného řízení aplikací, ke kterým se připojujete.
- Pomocí Řízení podmíněného přístupu k aplikacím Protection získáte přehled a kontrolu nad přístupem a aktivitami v rámci cloudových aplikací v reálném čase.
- Vám pomůže průběžně ovládat nastavení a pak průběžně vyladit zásady.

Řízení relace MCAS (Microsoft Cloud Application Security) je k dispozici pro libovolný prohlížeč na libovolné hlavní platformě libovolného operačního systému. Mobilní aplikace a aplikace klasické pracovní plochy můžou být taky blokované nebo povolené. Díky nativně integraci s Azure AD můžou být podporované všechny aplikace nakonfigurované pomocí SAML nebo otevřené ID připojit aplikace s jednotným přihlašováním ve službě Azure AD, včetně [několika doporučených aplikací](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad).

Informace o MCAS najdete v tématu [přehled Microsoft Cloud App Security](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). MCAS je služba pro předplatné založená na uživatelích. Podrobnosti o licencování najdete v [datovém listu licencování MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Používání podmíněného přístupu

Pomocí podmíněného přístupu můžete automatizovat rozhodování o řízení přístupu na základě kritérií pro vaše cloudové aplikace.

Zásady podmíněného přístupu se vynutily po dokončení prvního faktoru ověřování. Proto podmíněný přístup není určený jako ochrana první linky pro scénáře, jako jsou útoky DoS (Denial-of-Service), ale může k určení přístupu použít signály z těchto událostí. Můžete například použít úroveň rizika přihlašování, umístění žádosti a tak dále. Další informace o podmíněném přístupu najdete [v tématu Přehled](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) a [plán nasazení](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Technické požadavky pro jednotné přihlašování Azure

V následující části najdete podrobnosti o požadavcích na konfiguraci konkrétní aplikace, včetně nezbytných prostředí, koncových bodů, mapování deklarací, požadovaných atributů, certifikátů a používaných protokolů. Tyto informace budete potřebovat ke konfiguraci jednotného přihlašování na [portálu Azure AD](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Podrobnosti ověřovacího mechanismu

Pro všechny předem integrované aplikace SaaS poskytuje Microsoft kurz a nebudete tyto informace potřebovat. Pokud aplikace není v tržišti nebo galerii aplikací, možná budete potřebovat shromáždit následující datové části:

- **Aktuální zprostředkovatel identity, který aplikace v případě potřeby používá pro jednotné přihlašování** – například: AD FS, PingFederate, okta
- **Protokoly podporované cílovou aplikací** – například SAML 2,0, OpenID Connect, OAuth, Forms-Based auth, WS-dokrmen WS-Trust
- **Protokol konfigurovaný pomocí Azure AD** – například SAML 2,0 nebo 1,1, OpenID Connect, OAuth, založené na formulářích, WS-Fed

### <a name="attribute-requirements"></a>Požadavky na atributy

K dispozici je předem nakonfigurovaná sada atributů a mapování atributů mezi uživatelskými objekty Azure AD a všemi uživatelskými objekty aplikace v SaaS. Některé aplikace spravují jiné typy objektů, jako jsou skupiny. Naplánujte mapování uživatelských atributů z Azure AD do vaší aplikace a [Přizpůsobte výchozí mapování atributů](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) podle vašich obchodních potřeb.

### <a name="certificate-requirements"></a>Požadavky na certifikáty

Certifikát pro aplikaci musí být aktuální nebo hrozí riziko, že uživatelé nebudou mít přístup k aplikaci. Většina SaaSch certifikátů aplikace je platná po dobu 36 měsíců. Dobu trvání certifikátu změníte v okně aplikace. Nezapomeňte si vyzdokumentovat vypršení platnosti a zjistit, jak budete obnovování certifikátu spravovat. 

Existují dva způsoby, jak spravovat certifikáty. 

- **Automatická změna certifikátu** – Microsoft podporuje [podpisové klíče ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). I když se jedná o upřednostňovanou metodu pro správu certifikátů, ne všechny tyto scénáře tento scénář podporují všichni výrobci ISV.

- **Ruční aktualizace** – každá aplikace má svůj vlastní certifikát, jehož platnost vyprší na základě toho, jak se definuje. Než vyprší platnost certifikátu aplikace, vytvořte nový certifikát a odešlete ho k nezávislému výrobci softwaru. Tyto informace lze získat z federačních metadat. [Tady si můžete přečíst další informace o federačních metadatech.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementace jednotného přihlašování

K naplánování a nasazení vašeho řešení ve vaší organizaci použijte následující fáze:

### <a name="user-configuration-for-sso"></a>Konfigurace uživatele pro jednotné přihlašování

- **Identifikujte své testovací uživatele**

   Kontaktujte vlastníka aplikace a požádejte ho, aby v rámci aplikace vytvořil minimálně tři testovací uživatele. Ujistěte se, že informace, které použijete jako primární identifikátor, jsou správně vyplněné a odpovídají atributu, který je k dispozici ve službě Azure AD. Ve většině případů se tato možnost mapuje na "NameID" pro aplikace založené na SAML. U tokenů JWT je to "preferred_username."
   
   Vytvořte uživatele ve službě Azure AD buď ručně jako cloudový uživatel, nebo synchronizujte uživatele z místního prostředí pomocí modulu Azure AD Connect Sync. Zajistěte, aby informace odpovídaly deklaracím odesílaným do aplikace.

- **Konfigurace jednotného přihlašování**

   V [seznamu aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)vyhledejte a otevřete kurz jednotného přihlašování pro vaši aplikaci a pak postupujte podle kroků kurzu v části pro úspěšnou konfiguraci aplikace SaaS.

   Pokud nemůžete najít svou aplikaci, přečtěte si část [dokumentace k vlastní aplikaci](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). To vás provede postupem, jak přidat aplikaci, která není umístěná v galerii Azure AD.

   Volitelně můžete použít deklarace identity vydané v tokenu SAML pro podnikovou aplikaci pomocí [doprovodné dokumentace Microsoftu](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Zajistěte, aby se tato mapa namapovala na to, co očekáváte v odpovědi SAML pro vaši aplikaci. Pokud narazíte na problémy při konfiguraci, využijte naše pokyny, [jak ladit integraci jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

### <a name="provide-sso-change-communications-to-end-users"></a>Poskytněte koncovým uživatelům komunikaci se změnou jednotného přihlašování.

Implementujte svůj komunikační plán. Ujistěte se, že zadáváte koncovým uživatelům informace o tom, že se po doručení dokončí změna, co dělat teď a jak se bude hledat pomoc.

### <a name="verify-end-user-scenarios-for-sso"></a>Ověřit scénáře koncových uživatelů pro jednotné přihlašování

Následující testovací případy můžete použít k provedení testů na zařízeních vlastněných společností a k zajištění toho, aby konfigurace jednotného přihlašování fungovaly podle očekávání. Níže uvedené scénáře předpokládají, že uživatel přejde k adrese URL aplikace a projde tokem ověřování iniciované poskytovatelem služeb (tok ověřování iniciované pomocí SP).

| Scénář | Očekávaný výsledek při ověřování iniciované nástrojem SP uživatelem |
|----------|---------------------------------------------------|
| Přihlaste se k aplikaci pomocí IE při Corpnet. | Integrované ověřování systému Windows (IWA) probíhá bez dalších výzev. |
| Přihlaste se k aplikaci pomocí IE, pokud je vypnutá Corpnet s novým pokusy o přihlášení. | Na základě formulářů se zobrazí výzva na AD FSovém serveru. Uživatel se úspěšně přihlásí a zobrazí výzvy pro MFA. |
| Přihlaste se k aplikaci pomocí aplikace IE, pokud je vypnutá Corpnet s aktuální relací a nikdy neprováděla MFA. | Uživatel neobdrží výzvu k zadání prvního faktoru. Uživatel obdrží výzvu pro MFA. |
| Přihlaste se k aplikaci pomocí aplikace IE, pokud je vypnuta Corpnet s aktuální relací a v této relaci již byla provedena MFA. | Uživatel neobdrží výzvu k zadání prvního faktoru. Uživatel neobdrží MFA. Uživatel SSOs do aplikace. |
| Přihlaste se k aplikaci s použitím Chrome/Firefox/Safari a Corpnet s aktuální relací a v této relaci už jste provedli MFA. | Uživatel neobdrží výzvu k zadání prvního faktoru. Uživatel neobdrží MFA. Jednotné přihlašování uživatele do aplikace. |
| Přihlaste se k aplikaci pomocí Chrome/Firefox/Safari a Corpnet se s novým pokusem o přihlášení. | Na základě formulářů se zobrazí výzva na AD FSovém serveru. Uživatel se úspěšně přihlásí a zobrazí výzvy pro MFA. |
| Přihlaste se k aplikaci pomocí Chrome/Firefox a v podnikové síti s aktuální relací. | Uživatel neobdrží výzvu k zadání prvního faktoru. Uživatel neobdrží MFA. Jednotné přihlašování uživatele do aplikace. |
| Přihlaste se k aplikaci pomocí mobilní aplikace aplikace s novým pokusy o přihlášení. | Na základě formulářů se zobrazí výzva na AD FSovém serveru. Uživatel se úspěšně přihlásí a zobrazí výzvy klienta ADAL pro MFA. |
| Neautorizovaný uživatel se pokusí přihlásit k aplikaci s přihlašovací adresou URL. | Na základě formulářů se zobrazí výzva na AD FSovém serveru. Uživatel se nemůže přihlásit s prvním faktorem. |
| Ověřený uživatel se pokusí přihlásit, ale zadá nesprávné heslo. | Uživatel přejde na adresu URL aplikace a přijme chybná chyba uživatelského jména a hesla. |
| Autorizovaný uživatel klikne na odkaz v e-mailu a je už ověřený. | Uživatel klikne na adresu URL a do aplikace se přihlásí bez dalších výzev. |
| Autorizovaný uživatel klikne na odkaz v e-mailu a ještě není ověřený. | Uživatel klikne na adresu URL a zobrazí výzvu k ověření pomocí prvního faktoru. |
| Autorizovaný uživatel se přihlásí do aplikace pomocí mobilní aplikace aplikace (iniciovaná v SP) s novým pokusem o přihlášení. | Na základě formulářů se zobrazí výzva na AD FSovém serveru. Uživatel se úspěšně přihlásí a zobrazí výzvy klienta ADAL pro MFA. |
| Neautorizovaný uživatel se pokusí přihlásit k aplikaci s adresou URL pro přihlášení (iniciovaná v SP). | Na základě formulářů se zobrazí výzva na AD FSovém serveru. Uživatel se nemůže přihlásit s prvním faktorem. |
| Ověřený uživatel se pokusí přihlásit, ale zadá nesprávné heslo.| Uživatel přejde na adresu URL aplikace a přijme chybná chyba uživatelského jména a hesla. |
| Ověřený uživatel se odhlásí a pak znovu přihlásí. | Pokud je nakonfigurovaná adresa URL pro odhlášení, uživatel se odhlásí ze všech služeb a zobrazí výzvu k ověření. |
| Ověřený uživatel se odhlásí a pak znovu přihlásí. | Pokud není adresa URL pro odhlášení nakonfigurovaná, uživatel se automaticky odhlásí pomocí existujícího tokenu z existující relace prohlížeče Azure AD. |
| Autorizovaný uživatel klikne na odkaz v e-mailu a je už ověřený. | Uživatel klikne na adresu URL a do aplikace se přihlásí bez dalších výzev. |
| Autorizovaný uživatel klikne na odkaz v e-mailu a ještě není ověřený. | Uživatel klikne na adresu URL a zobrazí výzvu k ověření pomocí prvního faktoru. |

## <a name="manage-sso"></a>Správa jednotného přihlašování

V této části najdete popis požadavků a doporučení pro úspěšnou správu jednotného přihlašování.

### <a name="required-administrative-roles"></a>Požadované role pro správu

Vždy používejte roli s nejmenším oprávněním, která jsou k dispozici k provedení požadované úlohy v rámci Azure Active Directory. Společnost Microsoft doporučuje [zkontrolovat různé role, které jsou k dispozici,](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) a vybrat tu správnou roli, abyste mohli vaše potřeby pro tuto aplikaci vyřešit podle svých potřeb. Některé role může být potřeba použít dočasně a odebrat po dokončení nasazení.

| Nežádoucí| Role | Role Azure AD (v případě potřeby) |
|--------|-------|-----------------------------|
| Správce helpdesku | Podpora vrstvy 1 | Žádné |
| Správce identit | Konfigurace a ladění v případě problémů ovlivňujících službu Azure AD | Globální správce |
| Správce aplikace | Ověření identity uživatele v aplikaci, konfigurace u uživatelů s oprávněním | Žádné |
| Správci infrastruktury | Vlastník změna certifikátu | Globální správce |
| Vlastník/účastník společnosti | Ověření identity uživatele v aplikaci, konfigurace u uživatelů s oprávněním | Žádné |

Pro správu rolí doporučujeme použít [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) a zajistit tak další auditování, řízení a kontrolu přístupu pro uživatele s oprávněními k adresáři.

### <a name="sso-certificate-lifecycle-management"></a>Správa životního cyklu certifikátů jednotného přihlašování

Je důležité, abyste identifikovali správné role a e-mailové e-mailové seznamy, které se týkají správy životního cyklu podpisového certifikátu mezi Azure AD a aplikace, která je nakonfigurovaná s jednotným přihlašováním. Tady jsou některé z klíčových rolí, které doporučujeme použít:

- Vlastník aktualizace vlastností uživatele v aplikaci
- Vlastník – volání podpory pro přerušení nebo opravu aplikace
- Podrobný seznam distribuce e-mailů s podrobným sledováním pro oznámení o změně certifikátu

Maximální doba života certifikátu je tři roky. Doporučujeme vytvořit proces, jak se bude zpracovávat změna certifikátu mezi Azure AD a vaší aplikací. To může přispět k prevenci nebo minimalizování výpadku z důvodu vypršení platnosti certifikátu nebo vynucení změny certifikátu.

### <a name="rollback-process"></a>Odvolat proces

Po dokončení testování na základě testovacích případů je čas přejít do produkčního prostředí s vaší aplikací. Přechodem do produkčního prostředí budete implementovat plánované a testované konfigurace v provozním tenantovi a nasadíte je uživatelům. Je ale důležité naplánovat, co dělat v případě, že vaše nasazení nepůjde naplánovat. Pokud v průběhu nasazení dojde k chybě konfigurace jednotného přihlašování, musíte pochopit, jak zmírnit případné výpadky a snížit dopad na uživatele.

Dostupnost metod ověřování v rámci aplikace určí vaši nejlepší strategii. Vždy se ujistěte, že máte k dispozici detailní dokumentaci pro vlastníky aplikací, abyste se mohli vrátit k původnímu stavu konfigurace přihlášení v případě, že dojde k potížím s nasazením.

- **Pokud vaše aplikace podporuje více zprostředkovatelů identity**, například LDAP a AD FS a příkaz if, neodstraňujte při zavedení existující konfiguraci jednotného přihlašování. Místo toho ho zakažte během migrace pro případ, že ho později budete potřebovat znovu zapnout. 

- **Pokud vaše aplikace nepodporuje více zprostředkovatelů identity** , ale umožňuje uživatelům přihlásit se pomocí ověřování založeného na formulářích (uživatelské jméno/heslo), zajistěte, aby se uživatelé mohli vrátit k tomuto přístupu v případě, že nedojde k chybě nové implementace konfigurace jednotného přihlašování.

### <a name="access-management"></a>Správa přístupu

Při správě přístupu k prostředkům doporučujeme zvolit přístup s větší škálou. Mezi běžné přístupy patří použití místních skupin synchronizací prostřednictvím Azure AD Connect, [vytváření dynamických skupin ve službě Azure AD na základě atributů uživatele](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)nebo vytváření [skupin samoobslužných služeb](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) ve službě Azure AD spravované vlastníkem prostředku.

### <a name="monitor-security"></a>Sledování zabezpečení

Doporučujeme nastavit regulární tempo, ve kterém si prohlédnete různé aspekty zabezpečení aplikací SaaS a provádíte jakékoli případné nápravné akce.

### <a name="troubleshooting"></a>Řešení potíží

Následující odkazy představují scénáře řešení potíží. Možná budete chtít vytvořit konkrétní vodítko pro pracovníky podpory, kteří budou tyto scénáře v podniku, a postup, jak je opravit.

#### <a name="consent-issues"></a>Problémy s vyjádřením souhlasu

- [Neočekávaná chyba souhlasu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Chyba souhlasu uživatele](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problémy s přihlašováním

- [Problémy s přihlašováním z vlastního portálu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Potíže s přihlašováním ze sekce Moje aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Chyba na přihlašovací stránce aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problém s přihlášením do aplikace Microsoftu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Problémy jednotného přihlašování pro aplikace uvedené v galerii aplikací Azure

- [Problém s heslem jednotného přihlašování pro aplikace uvedené v galerii aplikací Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problém se federovaném přihlašováním k aplikacím, které jsou uvedené v galerii aplikací Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Problémy jednotného přihlašování pro aplikace, které nejsou uvedené v galerii aplikací Azure

- [Problém s heslem jednotného přihlašování pro aplikace, které nejsou uvedené v galerii aplikací Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problém se federovaném přihlašováním k aplikacím, které nejsou uvedené v galerii aplikací Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Další kroky

[Ladění jednotného přihlašování založeného na SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Mapování deklarací pro aplikace přes PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Přizpůsobení deklarací identity vystavených v tokenu SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protokol SAML jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Jeden Sign-Out protokol SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (pro externí uživatele, jako jsou partneři a dodavatelé)

[Podmíněný přístup Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Přístup s jednotným přihlašováním](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Kurz jednotného přihlašování k aplikacím](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)

[Stažení dokumentu White Paper – plán nasazení s jednotným přihlašováním](https://aka.ms/SSODeploymentPlan)