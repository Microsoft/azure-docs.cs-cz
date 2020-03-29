---
title: Plánování jednotného přihlašovacího nasazení služby Azure Active Directory
description: Průvodce, který vám pomůže plánovat, nasazovat a spravovat přisychované přibližování a služeb ve vaší organizaci.
services: active-directory
author: jeevansd
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.date: 05/22/2019
ms.author: baselden
ms.reviewer: jeedes
ms.collection: M365-identity-device-management
ms.openlocfilehash: 92496fa572c5c1cae4588f82ac61c18de3024045
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76512823"
---
# <a name="plan-a-single-sign-on-deployment"></a>Plánování nasazení jednotného přihlašování

Jednotné přihlašování (SSO) znamená přístup ke všem aplikacím a prostředkům, které uživatel potřebuje, a to tak, že se přihlašuje pouze jednou pomocí jednoho uživatelského účtu. S s přihlašování uživatelů přístup ke všem potřebným aplikacím, aniž by bylo nutné ověřit podruhé.

## <a name="benefits-of-sso"></a>Výhody sso

Jednotné přihlašování (SSO) přidává zabezpečení a pohodlí, když se uživatelé přihlašují k aplikacím ve službě Azure Active Directory (Azure AD). 

Mnoho organizací spoléhá na software jako na aplikace služby (SaaS), jako je Office 365, Box a Salesforce, pro produktivitu koncových uživatelů. V minulosti museli pracovníci IT individuálně vytvářet a aktualizovat uživatelské účty v každé aplikaci SaaS a uživatelé si museli pamatovat heslo pro každou z nich.

Azure Marketplace má více než 3000 aplikací s předem integrovanými připojeními při hlášce, takže je snadno integrujete do vašeho tenanta.

## <a name="licensing"></a>Licencování

- **Licencování Azure AD** – semaforpro předem integrované aplikace SaaS je zdarma. Počet objektů v adresáři a funkce, které chcete nasadit, však může vyžadovat další licence. Úplný seznam licenčních požadavků najdete v [tématu Azure Active Directory Pricing](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licencování aplikací** – Budete potřebovat příslušné licence pro vaše aplikace SaaS, abyste vyhověli vašim obchodním potřebám. Spolupracujte s vlastníkem aplikace a zjistěte, zda uživatelé přiřazení k aplikaci mají příslušné licence pro své role v rámci aplikace. Pokud Azure AD spravuje automatické zřizování na základě rolí, role přiřazené ve službě Azure AD musí zarovnat s počtem licencí vlastněných v rámci aplikace. Nesprávný počet licencí vlastněných v aplikaci může vést k chybám během zřizování/aktualizace uživatele.

## <a name="plan-your-sso-team"></a>Plánování týmu s připojoukem a služeb

- **Zapojte správné zúčastněné strany** – když technologické projekty selžou, je to obvykle kvůli neodpovídajícím očekáváním ohledně dopadu, výsledků a odpovědností. Chcete-li se těmto nástrahám vyhnout, [ujistěte se, že zapojujete správné zúčastněné strany](https://aka.ms/deploymentplans) a že zúčastněné strany chápou jejich role.
- **Plánování komunikace** - Komunikace je rozhodující pro úspěch každé nové služby. Proaktivně sdělujte uživatelům informace o tom, jak se jejich prostředí změní, kdy se změní a jak získat podporu, pokud se u nich vyskytnou problémy. Zkontrolujte možnosti, [jak koncoví uživatelé budou přistupovat k aplikacím s podporou automatického přihlašování](end-user-experiences.md)a vytvářet komunikaci tak, aby odpovídala vašemu výběru. 

## <a name="plan-your-sso-protocol"></a>Plánování protokolu s připojouho služeb pro propojení

Implementace jednotného přihrádky založené na federačních protokolech zlepšuje zabezpečení, spolehlivost a prostředí koncových uživatelů a snadněji se implementuje. Mnoho aplikací je předem integrované ve službě Azure AD s [podrobnými průvodci, které jsou k dispozici](../saas-apps/tutorial-list.md). Najdete je na našem [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Podrobné informace o jednotlivých metodách jednotného přihlašování najdete v článku [Jednotné přihlašování k aplikacím ve službě Azure Active Directory](what-is-single-sign-on.md).

Existují dva primární způsoby, jak uživatelům povolit jednotné přihlašování k aplikacím:

- **S federovaným jedním přihlášením** Azure AD ověřuje uživatele k aplikaci pomocí svého účtu Azure AD. Tato metoda je podporována pro aplikace, které podporují protokoly, jako je SAML 2.0, WS-Federation nebo OpenID Connect, a je nejbohatším režimem jednotného přihlašování. Doporučujeme používat federované přihlašované služby při řazení s azure ad, když to aplikace podporuje, namísto zabezpečení založeného na heslech a služby ADFS.

- **S heslem založené jednotné přihlášení** uživatelé přihlásit do aplikace s uživatelským jménem a heslem poprvé, mají přístup k němu. Po prvním přihlášení Azure AD poskytuje uživatelské jméno a heslo do aplikace. Jednotné přihlašování založené na heslech umožňuje bezpečné ukládání hesel aplikací a přehrávání pomocí rozšíření webového prohlížeče nebo mobilní aplikace. Tato možnost využívá existující proces přihlášení poskytované aplikací, umožňuje správci spravovat hesla a nevyžaduje, aby uživatel znát heslo.

### <a name="considerations-for-federation-based-sso"></a>Důležité informace pro služby přisuzování služby založené na federaci

- **Použití OpenID Connect a OAuth** - Pokud aplikace, ke které se připojujete, podporuje, použijte metodu OIDC/OAuth 2.0 k povolení vašeho sazí k této aplikaci. Tato metoda vyžaduje méně konfigurace a umožňuje bohatší uživatelské prostředí. Další informace naleznete [v tématech OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md)a [Azure Active Directory developer's Guide](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Konfigurace koncového bodu pro přihlašování založené na SAML** – pokud používáte SAML, vývojáři budou před konfigurací aplikace potřebovat konkrétní informace. Další informace naleznete [v tématu Úprava základní konfigurace SAML](configure-single-sign-on-non-gallery-applications.md).
- **Správa certifikátů pro samolo založené přihlašování** – když povolíte federované přihlašování k samoobsluze pro vaši aplikaci, Azure AD vytvoří certifikát, který je ve výchozím nastavení platný po dobu tří let. V případě potřeby můžete upravit datum vypršení platnosti tohoto certifikátu. Ujistěte se, že máte procesy na místě obnovit certifikáty před jejich vypršením platnosti. Další informace najdete v [tématu Správa certifikátů azure ad](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Důležité informace o zabezpečení založené na heslech

Použití služby Azure AD pro zabezpečení založené na heslech vyžaduje nasazení rozšíření prohlížeče, které bezpečně načte přihlašovací údaje a vyplní přihlašovací formuláře. Definujte mechanismus pro nasazení rozšíření ve velkém měřítku s [podporovanými prohlížeči](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Mezi možnosti patří:

- [Zásady skupiny pro aplikaci Internet Explorer](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [Správce konfigurace pro aplikaci Internet Explorer](https://docs.microsoft.com/configmgr/core/clients/deploy/deploy-clients-to-windows-computers)
- [Stahování a konfigurace řízené uživatelem pro Chrome, Firefox, Microsoft Edge nebo IE](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Další informace naleznete v [tématu Konfigurace jednotného přihlášení hesla](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Zachytávání metadat přihlašovacích formulářů pro aplikace, které nejsou v galerii

Společnost Microsoft podporuje zachytávání metadat ve webové aplikaci pro úschovu hesel (zachycení polí uživatelského jména a hesla). Během procesu konfigurace aplikace pro zachycení metadat formulářů přejděte na přihlašovací adresu URL. Požádejte vlastníka aplikace o adresu URL přesného přihlášení. Tyto informace se používají během procesu přihlašování, mapování přihlašovacích údajů Azure AD do aplikace během přihlášení.

Další informace najdete v [tématu Co je přístup k aplikacím a přihlašující pomocí azure ad? – heslo založené na přihlašující .](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/)

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Indikace, že metadata ve formulářích musí být znovu zachycena

Když aplikace změní rozložení HTML, možná budete muset znovu zachytit metadata, abyste je upravili. Mezi běžné příznaky, které označují změnu rozložení HTML, patří:

- Uživatelé hlásí, že kliknutím na aplikaci dostane "přilepená" na přihlašovací stránce
- Uživatelé hlásí, že uživatelské jméno nebo heslo není vyplněno

#### <a name="shared-accounts"></a>Sdílené účty

Z hlediska přihlášení se aplikace se sdílenými účty neliší od aplikace galerie, která používá semanové zabezpečení hesla pro jednotlivé uživatele. Při plánování a konfiguraci aplikace určené pro použití sdílených účtů je však vyžadováno několik dalších kroků:

1. Spolupracujte s firemními uživateli aplikací a zdokumentujte následující:
   1. Sada uživatelů v organizaci, kteří budou aplikaci používat
   1. Existující sada pověření v aplikaci přidružené k sadě uživatelů 
1. Pro každou kombinaci sady uživatelů a přihlašovacích údajů vytvořte skupinu zabezpečení v cloudu nebo v místním prostředí na základě vašich požadavků.
1. Obnovte sdílená pověření. Jakmile se aplikace nasadí ve službě Azure AD, jednotlivci nepotřebují heslo sdíleného účtu. Vzhledem k tomu, že Azure AD bude ukládat heslo, zvažte nastavení na velmi dlouhé a složité. 
1. Nakonfigurujte automatické převrácení hesla, pokud ho aplikace podporuje. Tímto způsobem ani správce, který provedl počáteční nastavení, nebude znát heslo sdíleného účtu. 

## <a name="plan-your-authentication-method"></a>Plánování metody ověřování

Výběr správné metody ověřování je zásadní první rozhodnutí při nastavování řešení hybridní identity Azure AD. Implementujte metodu ověřování, která je nakonfigurovaná pomocí Služby Azure AD Connect, která také zřizuje uživatele v cloudu.

Chcete-li zvolit metodu ověřování, je třeba zvážit čas, existující infrastrukturu, složitost a náklady na implementaci vaší volby. Tyto faktory se liší pro každou organizaci a mohou se v průběhu času měnit. Měli byste zvolit ten, který nejvíce odpovídá vašemu konkrétnímu scénáři. Další informace najdete [v tématu Výběr správné metody ověřování pro řešení hybridní identity služby Azure Active Directory](https://docs.microsoft.com/azure/security/fundamentals/choose-ad-authn).

## <a name="plan-your-security-and-governance"></a>Plánování zabezpečení a zásad správného řízení 

Identita je novým primárním pivotem pro bezpečnostní pozornost a investice, protože obvody sítě jsou stále poréznější a méně efektivní s explozí zařízení BYOD a cloudových aplikací. 

### <a name="plan-access-reviews"></a>Plánování kontrol přístupu

[Funkce Access Reviews](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) umožňuje organizacím efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Měli byste plánovat kontrolu přístupu uživatelů v pravidelných intervalech, aby se ujistil, pouze ti praví lidé mají trvalý přístup.

Mezi klíčová témata, která je třeba plánovat při nastavování kontrol přístupu, patří:

1. Identifikace kadence pro kontroly přístupu, které odpovídají potřebám vaší firmy. To může být stejně časté jako jednou týdně, měsíčně, ročně, nebo jako on-demand cvičení.

1. Vytvořte skupiny, které představují recenzenty sestav přístupu k aplikaci. Budete muset zajistit, aby se účastníci, kteří jsou s aplikací a jejími cílovými uživateli a případy použití nejvíce obeznámeni, podíleli na vašich kontrolách přístupu.

1. Dokončení kontroly přístupu zahrnuje odebrání oprávnění k přístupu k aplikacím uživatelům, kteří již přístup nepotřebují. Plán pro zpracování potenciálních žádostí o podporu od odepřených uživatelů. Odstraněný uživatel zůstane odstraněn ve službě Azure AD po dobu 30 dnů, během které může být v případě potřeby obnoven správcem. Po 30 dnech je tento uživatel odstraněn trvale. Pomocí portálu Azure Active Directory může globální správce explicitně trvale odstranit nedávno odstraněného uživatele před dosažením tohoto časového období.

### <a name="plan-auditing"></a>Plánování auditování

Azure AD poskytuje [sestavy obsahující technické a obchodní přehledy](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). 

K dispozici jsou zprávy o zabezpečení i aktivitě. Sestavy zabezpečení zobrazují uživatele označené jako rizikové a riskantní přihlášení. Sestavy můžete použít ke správě rizik, zvýšení produktivity a sledování dodržování předpisů.

| Typ sestavy | Kontrola přístupu | Sestavy zabezpečení | Sestava přihlášení |
|-------------|---------------|------------------|----------------|
| Slouží ke kontrole | Oprávnění a využití aplikací. | Potenciálně ohrožené účty | Kdo přistupuje k aplikacím |
| Potenciální akce | Přístup k auditu; odvolání oprávnění | Odvolat přístup; vynutit reset zabezpečení | Odvolat přístup |

Azure AD uchovávětšinu většinu dat auditování po dobu 30 dnů a zpřístupní data prostřednictvím portálu pro správu Azure nebo prostřednictvím rozhraní API, které si můžete stáhnout do svých analytických systémů.

### <a name="consider-using-microsoft-cloud-application-security"></a>Zvažte použití zabezpečení cloudových aplikací společnosti Microsoft

Microsoft Cloud App Security (MCAS) je řešení casb (Cloud Access Security Broker). Poskytuje přehled o vašich cloudových aplikacích a službách, poskytuje sofistikované analýzy pro identifikaci a boj proti kybernetickým hrozbám a umožňuje vám kontrolovat, jak vaše data cestují.

Nasazení mcas umožňuje:

- Pomocí cloudového zjišťování můžete mapovat a identifikovat cloudové prostředí a cloudové aplikace, které vaše organizace používá.
- Sankce a zrušení sankcí aplikací ve vašem cloudu
- Používejte snadno nasadité konektory aplikací, které využívají rozhraní API poskytovatele, pro viditelnost a zásady správného řízení aplikací, ke kterým se připojujete
- Pomocí ochrany podmíněného řízení přístupu v reálném čase získáte přehled a kontrolu nad přístupem a aktivitami v rámci cloudových aplikací.
- Pomáhá mít nepřetržitou kontrolu nastavením a následným neustálým dolaďováním zásad.

Řízení relací Microsoft Cloud Application Security (MCAS) je k dispozici pro všechny prohlížeče na libovolné hlavní platformě v libovolném operačním systému. Mobilní aplikace a aplikace klasické pracovní plochy mohou být také blokovány nebo povoleny. Nativně integrací s Azure AD, všechny aplikace, které jsou nakonfigurované s SAML nebo Open ID Connect aplikace s jedním přihlášením ve službě Azure AD může být podporována, včetně [několika doporučených aplikací](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad).

Informace o mcas, najdete v [tématu Microsoft Cloud App Security přehled](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). MCAS je uživatelská předplacená služba. Podrobnosti o licencích můžete zkontrolovat v [datovém listu licencí MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Používání podmíněného přístupu

S podmíněným přístupem můžete automatizovat rozhodnutí o řízení přístupu pro vaše cloudové aplikace na základě kritérií.

Zásady podmíněného přístupu jsou vynuceny po dokončení ověřování prvního faktoru. Podmíněný přístup proto není určen jako obrana první linie pro scénáře, jako je odmítnutí služby (DoS), ale můžete použít signály z těchto událostí k určení přístupu. Můžete například použít úroveň rizika přihlášení, umístění požadavku a tak dále. Další informace o podmíněném přístupu naleznete [v přehledu](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) a [plánu nasazení](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Technické požadavky služby Azure SSO

V následující části jsou uvedeny požadavky na konfiguraci konkrétní aplikace včetně potřebného prostředí, koncových bodů, mapování deklarací, požadovaných atributů, certifikátů a použitých protokolů. Tyto informace budete potřebovat ke konfiguraci přisuzovaného zabezpečení na [portálu Azure AD](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Podrobnosti mechanismu ověřování

Pro všechny předem integrované aplikace SaaS poskytuje Společnost Microsoft kurz a tyto informace nebudete potřebovat. Pokud aplikace není v našem trhu aplikací / galerie, možná budete muset shromáždit následující kousky dat:

- **Aktuální zprostředkovatel identity, který aplikace používá pro sso, pokud je to možné** - například: AD FS, PingFederate, Okta
- **Protokoly podporované cílovou aplikací** - například SAML 2.0, OpenID Connect, OAuth, Auth založené na formulářích, WS-Fed, WS-Trust
- **Protokol je konfigurován s Azure AD** – například SAML 2.0 nebo 1.1, OpenID Connect, OAuth, Forms-Based, WS-Fed

### <a name="attribute-requirements"></a>Požadavky na atributy

Existuje předem nakonfigurovaná sada atributů a mapování atributů mezi uživatelskými objekty Azure AD a uživatelskými objekty každé aplikace SaaS. Některé aplikace spravují jiné typy objektů, například skupiny. Naplánujte mapování uživatelských atributů z Azure AD do vaší aplikace a [přizpůsobte výchozí mapování atributů podle vašich obchodních](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) potřeb.

### <a name="certificate-requirements"></a>Požadavky na certifikáty

Certifikát pro aplikaci musí být aktuální nebo existuje riziko, že uživatelé nebudou mít k aplikaci přístup. Většina certifikátů žádosti SaaS je dobrá po dobu 36 měsíců. Můžete změnit dobu trvání certifikátu v okně aplikace. Ujistěte se, že dokument vypršení platnosti a vědět, jak budete spravovat obnovení certifikátu. 

Certifikáty můžete spravovat dvěma způsoby. 

- **Automatický přechod na certifikáty** – Microsoft podporuje [přechod na podepisovací klíč ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). I když se jedná o naši upřednostňovanou metodu pro správu certifikátů, ne všechny nepoužívejte tento scénář.

- **Ruční aktualizace** – každá aplikace má svůj vlastní certifikát, který vyprší na základě způsobu, jakým je definován. Před vypršením platnosti certifikátu aplikace vytvořte nový certifikát a odešlete jej výrobci certifikátu. Tyto informace lze získat z metadat federace. [Přečtěte si více o federačních metadatech zde.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementovat sso

Pomocí následujících fází můžete naplánovat a nasadit řešení ve vaší organizaci:

### <a name="user-configuration-for-sso"></a>Konfigurace uživatele pro službu SSO

- **Identifikace testovacích uživatelů**

   Obraťte se na vlastníka aplikace a požádejte ho, aby v rámci aplikace vytvořil minimálně tři testovací uživatele. Ujistěte se, že informace, které budete používat jako primární identifikátor je vyplněna správně a odpovídá atribut, který je k dispozici ve službě Azure AD. Ve většině případů to bude mapovat na "NameID" pro aplikace založené na SAML. Pro JWT tokeny, je to "preferred_username."
   
   Vytvořte uživatele ve službě Azure AD buď ručně jako cloudový uživatel, nebo synchronizujte uživatele z místního prostředí pomocí synchronizačního modulu Azure AD Connect. Ujistěte se, že informace odpovídají deklaracím odesílaných do aplikace.

- **Konfigurace služby SSO**

   Ze [seznamu aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list)vyhledejte a otevřete kurz služby SSO pro vaši aplikaci a postupujte podle pokynů kurzu a úspěšně nakonfigurujte aplikaci SaaS.

   Pokud aplikaci nemůžete najít, přečtěte [si dokumentaci k aplikaci .](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery) To vás provede o tom, jak přidat aplikaci, která není umístěna v galerii Azure AD.

   Volitelně můžete použít deklarace vydané v tokenu SAML pro podnikovou aplikaci pomocí [pokynů společnosti Microsoft](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Ujistěte se, že to mapuje na to, co očekáváte, že obdržíte v odpovědi SAML pro vaši aplikaci. Pokud narazíte na problémy během konfigurace, použijte naše [pokyny, jak ladit integrace přihlašovat k sociálnímu zabezpečení](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

Vlastní registrace aplikací je funkce licencí Azure AD Premium P1 nebo P2.

### <a name="provide-sso-change-communications-to-end-users"></a>Poskytování komunikace změny při změně uživatelského přiznamuje koncovým uživatelům

Implementujte svůj komunikační plán. Ujistěte se, že dáváte koncovým uživatelům vědět, že se blíží změna, kdy přišla, co dělat a jak vyhledat pomoc.

### <a name="verify-end-user-scenarios-for-sso"></a>Ověření scénářů koncových uživatelů pro přistoužení zabezpečení

Následující testovací případy můžete použít k provádění testů na podnikových a osobních zařízeních, abyste zajistili, že konfigurace jednotného připomenení fungují podle očekávání. Níže uvedené scénáře předpokládají, že uživatel přechází na adresu URL aplikace a prochází tokověřování iniciovaný poskytovatelem služeb (tok ověření iniciované sp).

| Scénář | Očekávaný výsledek toku ověření iniciovaného sp uživatelem |
|----------|---------------------------------------------------|
| Přihlásit se do aplikace s IE, zatímco na corpnet. | K integrovanému ověřování systému Windows (IWA) dochází bez dalších výzev. |
| Přihlaste se do aplikace s IE, zatímco off corpnet s novým pokusem o přihlášení. | Výzva založená na formulářích na serveru Služby AD FS. Uživatel úspěšně přihlásí a prohlížeč vyzve k vícefaktorové ověřování. |
| Přihlaste se k aplikaci s IE, zatímco off corpnet s aktuální relace a nikdy provádí Vícefaktorové řízení. | Uživatel neobdrží výzvu k zadání prvního faktoru. Uživatel obdrží výzvu pro vícefaktorové ověřování. |
| Přihlaste se k aplikaci s IE, zatímco off corpnet s aktuální relace a již provedl vícefaktorové v této relaci. | Uživatel neobdrží výzvu k zadání prvního faktoru. Uživatel neobdrží vícefaktorové ověřování. Uživatelské objekty SCO do aplikace. |
| Přihlaste se do aplikace s Chrome / Firefox / Safari, zatímco off corpnet s aktuální relace a již provádí Vícefaktorové v této relaci. | Uživatel neobdrží výzvu k zadání prvního faktoru. Uživatel neobdrží vícefaktorové ověřování. Uživatelské informace o uživatelském rozhraní se naněj nahlašují. |
| Přihlaste se do aplikace s Chrome / Firefox / Safari, zatímco off corpnet s novým pokusem o přihlášení. | Výzva založená na formulářích na serveru Služby AD FS. Uživatel úspěšně přihlásí a prohlížeč vyzve k vícefaktorové ověřování. |
| Přihlaste se do aplikace s Chrome / Firefox, zatímco na podnikové síti s aktuální relace. | Uživatel neobdrží výzvu k zadání prvního faktoru. Uživatel neobdrží vícefaktorové ověřování. Uživatelské informace o uživatelském rozhraní se naněj nahlašují. |
| Přihlaste se do aplikace pomocí mobilní aplikace s novým pokusem o přihlášení. | Výzva založená na formulářích na serveru Služby AD FS. Uživatel se úspěšně přihlásí a klient ADAL vyzve k příkazu MFA. |
| Neoprávněný uživatel se pokusí přihlásit do aplikace s přihlašovací adresou URL. | Výzva založená na formulářích na serveru Služby AD FS. Uživatel i ono nese přihlásit s první faktor. |
| Oprávněný uživatel se pokusí přihlásit, ale zadá nesprávné heslo. | Uživatel přejde na adresu URL aplikace a zobrazí se chybná chyba uživatelského jména a hesla. |
| Oprávněný uživatel klikne na odkaz v e-mailu a je již ověřen. | Uživatel klikne na adresu URL a je přihlášen do aplikace bez dalších výzev. |
| Oprávněný uživatel klikne na odkaz v e-mailu a ještě není ověřen. | Uživatel klikne na adresu URL a zobrazí výzvu k ověření s prvním faktorem. |
| Autorizovaný uživatel se přihlásí do aplikace pomocí mobilní aplikace aplikace (SPUŠTĚNÁ SP) s novým pokusem o přihlášení. | Výzva založená na formulářích na serveru Služby AD FS. Uživatel se úspěšně přihlásí a klient ADAL vyzve k příkazu MFA. |
| Neoprávněný uživatel se pokusí přihlásit do aplikace s přihlašovací adresou URL (spuštěna sp). | Výzva založená na formulářích na serveru Služby AD FS. Uživatel i ono nese přihlásit s první faktor. |
| Oprávněný uživatel se pokusí přihlásit, ale zadá nesprávné heslo.| Uživatel přejde na adresu URL aplikace a zobrazí se chybná chyba uživatelského jména a hesla. |
| Oprávněný uživatel se odhlásí a znovu přihlásí. | Pokud je nakonfigurována adresa URL pro odhlášení, uživatel je odhlášen ze všech služeb a vyzve k ověření. |
| Oprávněný uživatel se odhlásí a znovu přihlásí. | Pokud není nakonfigurovaná adresa URL pro odhlášení, uživatel se automaticky přihlásí zpět pomocí existujícího tokenu z existující relace prohlížeče Azure AD. |
| Oprávněný uživatel klikne na odkaz v e-mailu a je již ověřen. | Uživatel klikne na adresu URL a je přihlášen do aplikace bez dalších výzev. |
| Oprávněný uživatel klikne na odkaz v e-mailu a ještě není ověřen. | Uživatel klikne na adresu URL a zobrazí výzvu k ověření s prvním faktorem. |

## <a name="manage-sso"></a>Správa přisávacího řízení podle

Tato část popisuje požadavky a doporučení pro úspěšnou správu přispojené k zážehové mu.

### <a name="required-administrative-roles"></a>Požadované role pro správu

Vždy používejte roli s co nejmenším počtem oprávnění, která jsou k dispozici k provedení požadované úlohy v rámci služby Azure Active Directory. Společnost Microsoft doporučuje [zkontrolovat různé role, které jsou k dispozici,](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) a zvolit ten správný, který vyřeší vaše potřeby pro každou osobu pro tuto aplikaci. Některé role může být nutné dočasně použít a odebrat po dokončení nasazení.

| Persona| Role | Role Azure AD (v případě potřeby) |
|--------|-------|-----------------------------|
| Správce technické podpory | Podpora pro Tier 1 | Žádný |
| Správce identity | Konfigurace a ladění, když problémy ovlivňují Azure AD | Globální správce |
| Správce aplikace | Ověření uživatele v aplikaci, konfigurace uživatelů s oprávněními | Žádný |
| Správci infrastruktury | Vlastník převrácení certifikátu | Globální správce |
| Vlastník/zúčastněná strana podniku | Ověření uživatele v aplikaci, konfigurace uživatelů s oprávněními | Žádný |

Doporučujeme používat [privilegovanou správu identit](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) ke správě rolí a poskytovat uživatelům další auditování, řízení a kontrolu přístupu s oprávněními adresáře.

### <a name="sso-certificate-lifecycle-management"></a>Správa životního cyklu certifikátu SSO

Je důležité určit správné role a seznamy distribuce e-mailů, které mají za úkol spravovat životní cyklus podpisového certifikátu mezi Službou Azure AD a aplikací, která se nakonfiguruje s jediným přihlášením. Zde jsou některé z klíčových rolí, které doporučujeme mít na místě:

- Vlastník pro aktualizaci vlastností uživatele v aplikaci
- Podpora přerušení/opravy aplikace na volání vlastníka
- Pečlivě sledovaný seznam e-mailových distribucí pro oznámení o změnách souvisejících s certifikátem

Maximální životnost certifikátu je tři roky. Doporučujeme vytvořit proces, jak budete zpracovávat změnu certifikátu mezi Azure AD a vaší aplikací. To může pomoci zabránit nebo minimalizovat výpadek z důvodu vypršení platnosti certifikátu nebo převrácení certifikátu platnosti.

### <a name="rollback-process"></a>Proces vrácení zpět

Po dokončení testování na základě testovacích případů je čas přejít do produkčního prostředí s vaší aplikací. Přechod do produkčního prostředí znamená, že budete implementovat plánované a testované konfigurace ve vašem produkčním tenantovi a rozvádíte je uživatelům. Je však důležité naplánovat, co dělat v případě, že vaše nasazení nepůjde podle plánu. Pokud konfigurace s přihlašování nezdaří během nasazení, musíte pochopit, jak zmírnit výpadky a snížit dopad na uživatele.

Dostupnost metod ověřování v rámci aplikace určí nejlepší strategii. Vždy se ujistěte, že máte podrobnou dokumentaci pro vlastníky aplikací o tom, jak se přesně dostat zpět do původního stavu konfigurace přihlášení v případě, že vaše nasazení narazí na problémy.

- **Pokud vaše aplikace podporuje více poskytovatelů identit**, například LDAP a AD FS a Ping, neodstraňujte existující konfiguraci přiřazování při přihlašování během zavádění. Místo toho jej během migrace zakažte v případě, že ji budete muset později přepnout zpět. 

- **Pokud vaše aplikace nepodporuje více idpů,** ale umožňuje uživatelům přihlásit pomocí ověřování na základě formulářů (uživatelské jméno/heslo), ujistěte se, že uživatelé mohou přejít k tomuto přístupu v případě, že nové zavedení konfigurace jednotného přihlašování nezdaří.

### <a name="access-management"></a>Správa přístupu

Při správě přístupu k prostředkům doporučujeme zvolit přístup s škálami. Mezi běžné přístupy patří využití místních skupin synchronizací prostřednictvím služby Azure AD Connect, [vytváření dynamických skupin ve službě Azure AD na základě atributů uživatelů](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal)nebo vytváření [samoobslužných skupin](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) ve službě Azure AD spravovaných vlastníkem prostředků.

### <a name="monitor-security"></a>Sledování zabezpečení

Doporučujeme nastavit pravidelnou kadenci, ve které zkontrolujete různé aspekty zabezpečení aplikací SaaS a provedete všechny potřebné nápravné akce.

### <a name="troubleshooting"></a>Řešení potíží

Následující odkazy představují scénáře řešení potíží. Můžete chtít vytvořit konkrétní průvodce pro pracovníky podpory, který zahrnuje tyto scénáře a kroky k jejich opravě.

#### <a name="consent-issues"></a>Problémy se souhlasem

- [Neočekávaná chyba souhlasu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Chyba souhlasu uživatele](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problémy s přihlašováním

- [Problémy s přihlášením z vlastního portálu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Potíže s přihlašováním z přístupového panelu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Chyba na přihlašovací stránce aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problém s přihlášením k aplikaci společnosti Microsoft](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Problémy s přihlašování pro aplikace uvedené v Galerii aplikací Azure

- [Problém s heslem přihlašování pro aplikace uvedené v Galerii aplikací Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problém s federovaným přihlašování pro aplikace uvedené v Galerii aplikací Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Problémy s přihlašování pro aplikace, které nejsou uvedeny v Galerii aplikací Azure

- [Problém s heslem SSO pro aplikace, které nejsou uvedeny v Galerii aplikací Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problém s federovaným přihlašování pro aplikace, které nejsou uvedeny v Galerii aplikací Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Další kroky

[Ladění SSO založeného na SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Mapování deklarací pro aplikace přes PowerShell](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Přizpůsobení deklarací vydaných v tokenu SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protokol SAML jednotného přihlášení](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Protokol SAML jednotného odhlášení](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (pro externí uživatele, jako jsou partneři a dodavatelé)

[Podmíněný přístup Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Přístup s jednotným přihlašováním](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Kurz přihlašuje přihlašované k aplikací](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
