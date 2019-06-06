---
title: Plánování Azure Active Directory jednotné přihlašování – nasazení
description: Tento průvodce pomůže při plánování, nasazení a Správa jednotného přihlašování ve vaší organizaci.
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
ms.openlocfilehash: 29569302d20e23c95b6508a5b58c7ed96e005885
ms.sourcegitcommit: 600d5b140dae979f029c43c033757652cddc2029
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/04/2019
ms.locfileid: "66499246"
---
# <a name="plan-a-single-sign-on-deployment"></a>Plánování nasazení jednotné přihlašování

Jednotné přihlašování (SSO) znamená, že přístup ke všem aplikacím a prostředkům potřebuje uživatel podle stačí, když pomocí jediného uživatelského účtu. Pomocí jednotného přihlašování uživatelé mohou využívat všechny potřebné aplikace bez museli znovu ověřovat podruhé.

## <a name="benefits-of-sso"></a>Výhody jednotného přihlašování

Jednotné přihlašování (SSO), přidá zabezpečení a pohodlí, když uživatelé přihlásit k aplikacím v Azure Active Directory (Azure AD). 

Mnoho organizací využívají software jako služba (SaaS) aplikací, jako je Office 365, Box nebo Salesforce, a použít jej pro produktivita koncového uživatele. V minulosti pracovníky technické potřeby jednotlivě vytvářet a aktualizovat uživatelské účty v každé aplikaci SaaS a uživatelé museli pamatovat si hesla pro všechny.

Na webu Azure Marketplace má víc než 3000 aplikace s předem integrované jednotného přihlašování k připojení, což usnadňuje integraci ve vašem tenantovi.

## <a name="licensing"></a>Licencování

- **Licencování Azure AD** – jednotné přihlašování pro předem integrovaných aplikací SaaS je zdarma. Počet objektů v adresáři a funkce, které chcete nasadit však může vyžadovat další licence. Úplný seznam licenční požadavky najdete v tématu [cenami Azure Active Directory](https://azure.microsoft.com/pricing/details/active-directory/).
- **Licencování aplikací** – budete potřebovat příslušné licence pro aplikace SaaS pro splnění vašich obchodních potřeb. Práce s vlastníkem aplikace k určení, zda uživatelé přiřazení k aplikaci máte příslušné licence pro jejich role v rámci aplikace. Pokud Azure AD spravuje automatického zřizování na základě rolí, musí být s počtem licencí, které vlastní v rámci aplikace v souladu rolí ve službě Azure AD. Nesprávný počet licencí, které jsou ve vlastnictví aplikace může vést k chybám při zřízení nebo aktualizaci uživatele.

## <a name="plan-your-sso-team"></a>Plánování vašeho týmu jednotného přihlašování

- **Zapojení zúčastněných správné** – Pokud technologie projekty selže, je obvykle kvůli neodpovídající požadavkům na dopad, výsledky a odpovědnosti. Aby se zabránilo tyto nástrahy [Ujistěte se, že máte zajímavé správné zúčastněné strany](https://aka.ms/deploymentplans) a aby zúčastněné strany pochopili jejich role.
- **Plánování komunikace** – komunikace je zásadní pro úspěch jakékoli nové služby. Proaktivně komunikovat se o uživatele, jak se změní jejich prostředí, když se změní a jak získat podporu, pokud zaznamenají problémy. Projděte si možnosti pro [jak budou koncoví uživatelé přistupovat k jejich jednotného přihlašování aplikace s povolenou](end-user-experiences.md)a vytvořit komunikace podle vašeho výběru. 

## <a name="plan-your-sso-protocol"></a>Plánování váš protokol jednotného přihlašování

Implementace jednotné přihlašování založené na protokolech federace zvyšuje zabezpečení, spolehlivost, a koncový uživatel prostředí a usnadňují implementaci. Mnoho aplikací jsou předem integrované ve službě Azure AD s [provede krok za krokem k dispozici](../saas-apps/tutorial-list.md). Můžete najít na naší [Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/). Podrobné informace o jednotlivých metod jednotného přihlašování najdete v článku [jednotné přihlašování k aplikacím v Azure Active Directory](what-is-single-sign-on.md).

Existují dva základní způsoby, ve kterých můžete povolit uživatelům jednotné přihlašování pro vaše aplikace:

- **Pomocí federovaného jednotného přihlašování** Azure AD ověřuje uživatele k aplikaci pomocí svého účtu Azure AD. Tato metoda je podporována pro aplikace, které protokoly, například SAML 2.0, WS-Federation nebo OpenID Connect, podpory a bohaté možnosti režim jednotného přihlašování. Doporučujeme používat federované jednotné přihlašování s Azure AD, pokud aplikace podporuje, namísto jednotné přihlašování založené na heslech a AD FS.

- **S heslem jednotného přihlašování** uživatelé přihlásit k aplikaci pomocí uživatelského jména a hesla poprvé, když přístup. Po první přihlašování Azure AD poskytuje uživatelské jméno a heslo k aplikaci. Založené na heslech jednotné přihlašování umožňuje zabezpečené uložení hesel aplikace a jejich přehrání pomocí rozšíření webového prohlížeče nebo mobilní aplikace. Tato možnost využívá existující přihlašovací proces poskytovaný aplikací, umožňuje správcům spravovat hesla a nevyžaduje, aby uživatel heslo znal.

### <a name="considerations-for-federation-based-sso"></a>Důležité informace týkající se jednotné přihlašování založené na federace

- **Pomocí OpenID Connect a OAuth** – Pokud je aplikace, které se připojujete k podporuje, použít metodu OIDC/OAuth 2.0 pro váš jednotné přihlašování k dané aplikaci. Tato metoda vyžaduje méně konfiguraci a umožňuje pohodlnější a pestřejší prostředí uživatele. Další informace najdete v tématu [OAuth 2.0](../develop/v2-oauth2-auth-code-flow.md), [OpenID Connect 1.0](../develop/v2-protocols-oidc.md), a [Příručka pro vývojáře Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-developers-guide).
- **Konfigurace koncových bodů pro jednotné přihlašování založené na SAML** – Pokud používáte SAML, vývojáři potřebovat konkrétní informace před konfigurací aplikace. Další informace najdete v tématu [nakonfigurujte základní možnosti SAML](configure-single-sign-on-portal.md).
- **Certifikát správy založené na SAML SSO** – po povolení federovaného jednotného přihlašování pro vaše aplikace Azure AD vytvoří certifikát, který je ve výchozím nastavení platný po dobu tří let. Datum vypršení platnosti certifikátu v případě potřeby můžete přizpůsobit. Ujistěte se, že máte procesy na místě prodloužit platnost certifikátů před vypršením jejich platnosti. Další informace najdete v tématu [Azure AD, Správa certifikátů](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs).

### <a name="considerations-for-password-based-sso"></a>Důležité informace týkající se jednotné přihlašování pomocí hesla

Používání služby Azure AD pro jednotné přihlašování pomocí hesla vyžaduje nasazení rozšíření prohlížeče, které se bezpečně načíst přihlašovací údaje a vyplňovat přihlášení. Definování mechanismus pro nasazení rozšíření ve velkém měřítku [podporovaných prohlížečích](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction). Vaše možnosti jsou:

- [Zásady skupiny pro aplikaci Internet Explorer ](https://azure.microsoft.com/documentation/articles/active-directory-saas-ie-group-policy/)
- [System Center Configuration Manager (SCCM) pro aplikaci Internet Explorer ](https://docs.microsoft.com/sccm/core/clients/deploy/deploy-clients-to-windows-computers)
- [Uživatelské základě stažení a konfiguraci pro Chrome, Firefox, Microsoft Edge nebo Internet Exploreru ](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction)

Další informace najdete v tématu [jak ke konfiguraci hesla jedním přihlášení](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-password-sso-non-gallery).

#### <a name="capturing-login-forms-metadata-for-applications-that-arent-in-the-gallery"></a>Zachycení metadat formuláře přihlášení pro aplikace, které nejsou v galerii

Společnost Microsoft podporuje zachytávající metadat na webovou aplikaci pro heslo vaulting (zachycením pole uživatelské jméno a heslo). Přejděte na adresu URL pro přihlášení během procesu konfigurace aplikace pro zachycení metadat formuláře. Požádejte vlastníka, aplikací pro přesné přihlašovací adresu URL. Tyto informace se používají během procesu přihlašování, mapování přihlašovacích údajů Azure AD. aplikace během přihlašování.

Další informace najdete v tématu [co je přístup k aplikaci a jednotné přihlašování s Azure AD? – jednotné přihlašování založené na heslech](https://azure.microsoft.com/documentation/articles/active-directory-appssoaccess-whatis/).

#### <a name="indications-that-metadata-in-forms-needs-to-be-recaptured"></a>Označení těchto metadat ve formulářích je potřeba znovu

Když aplikace změní jejich rozložení HTML, potřebujete získat metadata pro změny upravte. Běžné příznaky, které označují, že obsahuje rozložení HTML změnu patří:

- Uživatelé, vytváření sestav, že kliknete na aplikace "zasekne" na přihlašovací stránce
- Uživatelé, vytváření sestav, že uživatelské jméno nebo heslo není naplněná

#### <a name="shared-accounts"></a>Sdílené účty

Z hlediska přihlášení nejsou aplikací pomocí sdílené účty liší od aplikace Galerie, která používá heslem jednotného přihlašování pro jednotlivé uživatele. Existují však některé další kroky potřebné při plánování a konfigurace aplikace určena pro použití sdílené účty:

1. Práce s aplikaci podnikovým uživatelům umožňuje dokumentovat následující věci:
   1. Skupinu uživatelů v organizaci, kteří budou používat aplikaci
   1. Existující sadu přihlašovacích údajů v aplikaci přidruženou k sadu uživatelů 
1. Pro každou kombinaci sadu uživatelů a přihlašovací údaje vytvořte skupinu zabezpečení v cloudu nebo místně na základě vašich požadavků.
1. Obnovení sdílené přihlašovací údaje. Po nasazení aplikace ve službě Azure AD není třeba jednotlivce heslo sdíleného účtu. Protože Azure AD uloží heslo, zvažte možnost nastavit ho jako velmi dlouhá a složitá. 
1. Automatická změna hesel nakonfiguruje, jestli aplikace podporuje. Tímto způsobem, dokonce ani správce, který nebyl počáteční nastavení bude znát heslo sdíleného účtu. 

## <a name="plan-your-authentication-method"></a>Plánování metodu ověřování

Volba správné metody ověřování je velmi důležitý první rozhodnutí při nastavování hybridní řešení identit Azure AD. Implementujte metodu ověřování, který je nakonfigurovaný pomocí Azure AD Connect, která také zřizuje uživatele v cloudu.

Chcete-li zvolit metodu ověřování, je potřeba zvážit čas, stávající infrastruktury, složitost a náklady na implementaci podle vašeho výběru. Tyto faktory se liší pro každý organizace a může v průběhu času měnit. Měli byste zvolit ten, který nejlépe odpovídá váš konkrétní scénář. Další informace najdete v tématu [zvolte správné ověřování pro vaše řešení hybridní identity Azure Active Directory](https://docs.microsoft.com/azure/security/azure-ad-choose-authn).

## <a name="plan-your-security-and-governance"></a>Plánování zabezpečení a zásad správného řízení 

Identita je nový primární pivot pro zabezpečení pozornost a investic, protože perimetry sítě se stává stále více porézní a méně efektivní s obrovské množství nových zařízení BYOD a cloudovým aplikacím. 

### <a name="plan-access-reviews"></a>Plán kontrol přístupu

[Kontroly přístupu](https://docs.microsoft.com/azure/active-directory/governance/create-access-review) umožňují organizacím efektivně spravovat členství ve skupinách, přístup k podnikovým aplikacím a přiřazení rolí. Měli byste naplánovat zkontrolujte přístup uživatelů v pravidelných intervalech, abyste měli jistotu, že pouze lidé mít nadále přístup.

K hlavním tématům plánovat při nastavování kontroly přístupu patří:

1. Identifikace tempo pro kontroly přístupu, která odpovídá obchodní potřebě. Může jít tak často jako jednou týdně, měsíčně, ročně nebo jako cvičení na vyžádání.

1. Vytvoření skupiny, které reprezentují revidující sestavy přístup k aplikaci. Je potřeba zajistit, aby zúčastněné strany nejvíce vědět, jak aplikace a její cílových uživatelů a případy použití účastníky vaší kontroly přístupu

1. Dokončení kontroly přístupu zahrnuje pořizování aplikace přístupová oprávnění, uživatelům, kteří už nepotřebují přístup. Plánování zpracování odepřených uživatelů potenciální žádosti o podporu. Odstraněný uživatel zůstane odstraněné ve službě Azure AD po dobu 30 dnů, během kterých lze je obnovit microsoftem nebo správcem v případě potřeby. Po 30 dnech je tento uživatel odstraněn trvale. Na portálu Azure Active Directory jako globální správce můžete explicitně trvale odstranit nedávno odstraněného uživatele předtím, než je dosaženo tohoto časového období.

### <a name="plan-auditing"></a>Plán auditu

Azure AD poskytuje [sestavy obsahující technické a obchodní přehledy](https://azure.microsoft.com/documentation/articles/active-directory-view-access-usage-reports/). 

Zabezpečení a sestavy aktivit jsou k dispozici. Zobrazit sestavy zabezpečení uživatelů označených příznakem rizika a rizikových přihlášení. Sestavy aktivit vám pomůže porozumět chování uživatelů ve vaší organizaci s podrobnostmi o přihlašovací aktivitu a poskytnutím záznamy pro audit všech přihlášení. Sestavy můžete použít k řízení rizik, zvyšování produktivity a monitorovat dodržování předpisů.

| Typ sestavy | Kontroly přístupu | Sestavy zabezpečení | Sestava přihlašování |
|-------------|---------------|------------------|----------------|
| Použít ke kontrole | Oprávnění aplikace a využití. | Potenciálně ohrožených účtů | Kdo přistupuje k aplikacím |
| Potenciální akce | Auditovat přístup k; Odvolat oprávnění | Odvolat přístup; Vynutit obnovení nastavení zabezpečení | Odvolat přístup |

Azure AD uchovává většinu auditování dat po dobu 30 dnů a zpřístupňuje je prostřednictvím portálu pro správu Azure nebo prostřednictvím rozhraní API pro stažení do analytické systémy.

### <a name="consider-using-microsoft-cloud-application-security"></a>Zvažte použití Microsoft Cloud Application Security

Microsoft Cloud App Security (MCAS) je řešení cloudové přístup zabezpečení zprostředkovatel (CASB). Budete mít přehled o vašich cloudových aplikacích a službách, poskytuje sofistikované analýzy k identifikaci a boji s kybernetickými hrozbami a umožňuje řídit, jak se přenáší vaše data.

Nasazení MCAS vám umožní:

- Pomocí Cloud Discovery k namapování a identifikování vašeho cloudového prostředí a cloudových aplikací, které vaše organizace používá.
- Schválení nebo zrušení schválit aplikace v cloudu
- Pomocí snadno použitelných aplikačních konektorů, které využít rozhraní API poskytovatelů, pro viditelnost a zásady správného řízení aplikací, které se připojují k
- Získat přehled v reálném čase a řízení přístupu a aktivit v rámci vašich cloudových aplikací pomocí ochrany Conditional Access App Control
- Umožňuje mít nepřetržitou kontrolu díky nastavení a následnému průběžnému dolaďování, zásady.

Řízení relace Microsoft cloudové aplikace zabezpečení (MCAS) je k dispozici pro všechny prohlížeče na všechny hlavní platformy pro všechny operační systémy. Mobilní aplikace a aplikace klasické pracovní plochy lze také blokované nebo povolené. Díky nativní integraci s Azure AD, všechny aplikace, které jsou konfigurovány pomocí SAML nebo Open ID Connect aplikací s jednotným přihlašováním ve službě Azure AD může být podporovaný, včetně [několik vybraných aplikací](https://docs.microsoft.com/cloud-app-security/proxy-intro-aad).

Informace o MCAS, najdete v článku [Microsoft Cloud App Security přehled](https://docs.microsoft.com/cloud-app-security/what-is-cloud-app-security). MCAS je služba založená na uživateli předplatného. Můžete zkontrolovat podrobnosti licenční [datový list pro licencování MCAS](https://query.prod.cms.rt.microsoft.com/cms/api/am/binary/RE2NXYO).

### <a name="use-conditional-access"></a>Použití podmíněného přístupu

S podmíněným přístupem můžete automatizovat při rozhodování o řízení přístupu na základě kritérií pro cloudové aplikace.

Po dokončení první dvojúrovňové ověřování se vynucují zásady podmíněného přístupu. Proto se podmíněný přístup není určen jako první linií obrany řádku pro scénáře, jako jsou útoky s cílem odepření služeb (DoS), ale signály z těchto událostí můžete použít k určení přístupu. Například úroveň rizika přihlášení je možné umístění žádosti a tak dále. Další informace o podmíněném přístupu najdete v tématu [přehledu](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access) a [plánu nasazení](https://docs.microsoft.com/azure/active-directory/conditional-access/plan-conditional-access).

## <a name="azure-sso-technical-requirements"></a>Technické požadavky pro Azure jednotného přihlašování

Následující část podrobně popisuje požadavky pro konfiguraci vaší konkrétní aplikaci, včetně nezbytné prostředí, koncové body, mapování deklarací, povinné atributy, certifikáty a protokoly používané. Budete potřebovat tato informace, jak nakonfigurovat jednotné přihlašování v [portálu Azure AD](https://portal.azure.com/).

### <a name="authentication-mechanism-details"></a>Podrobnosti o mechanismu ověřování

Pro všechny předem integrované aplikace SaaS společnost Microsoft poskytuje kurz a nebude nutné tyto informace. Pokud aplikace není na našem tržišti aplikací / galerie, možná budete muset shromažďovat následující časti dat:

- **Aktuální zprostředkovatel identity aplikace používá pro jednotné přihlašování, pokud je k dispozici** – například: AD FS, PingFederate, Okta
- **Protokoly podporované cílové aplikace** – například SAML 2.0, OpenID Connect, OAuth, ověřování pomocí formulářů, WS-Fed, WS-Trust
- **Protokol se službou Azure AD nakonfigurované** – například SAML 2.0 nebo 1.1, OpenID Connect, OAuth založené na formulářích, WS-Fed

### <a name="attribute-requirements"></a>Požadavky atributů

Je předem nakonfigurované sady atributů a atributů mapování mezi objekty uživatele služby Azure AD a příslušné aplikace SaaS uživatelské objekty. Některé aplikace spravovat další typy objektů, jako jsou skupiny. Plánování mapování atributů uživatele z Azure AD pro vaši aplikaci a [přizpůsobení mapování atributů výchozí](https://docs.microsoft.com/azure/active-directory/manage-apps/customize-application-attributes) podle vaše podnikání potřebuje.

### <a name="certificate-requirements"></a>Požadavky na certifikát

Certifikát pro aplikaci musí být aktuální nebo představuje riziko uživatel nebude mít přístup k aplikaci. Většina certifikátů aplikace SaaS jsou vhodné pro 36 měsíců. Změníte dobu trvání tohoto certifikátu v okně aplikace. Ujistěte se, že do vypršení platnosti dokumentu a vědět, jak budete spravovat vaše obnovení certifikátu. 

Existují dva způsoby, jak spravovat své certifikáty. 

- **Automatické certifikáty vyměnit** – společnost Microsoft podporuje [výměna podpisových klíčů ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-signing-key-rollover). Když je naše upřednostňovanou metodou pro správu certifikátů, ne všechny ISV tento scénář podporuje.

- **Ruční aktualizace** – každá aplikace má vlastní, jejíž platnost vyprší na základě certifikátů o tom, jak je definované. Před vypršením platnosti certifikátu aplikaci, vytvořte nový certifikát a jeho odeslání na výrobci. Tyto informace můžete načíst z federačních metadat. [Další informace o federačních metadatech tady.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata)

## <a name="implement-sso"></a>Implementace jednotné přihlašování

Pomocí následujících fází plánování a nasazení vašeho řešení ve vaší organizaci:

### <a name="user-configuration-for-sso"></a>Konfigurace uživatele pro jednotné přihlašování

- **Identifikace testovacích uživatelů**

   Obraťte se na vlastníka aplikace a požádejte ho o vytvoření minimálně tři testovací uživatele v rámci aplikace. Zkontrolujte informace, které budete používat jako primární identifikátor je vyplněný správně a odpovídá atribut, který je k dispozici ve službě Azure AD. Ve většině případů to bude mapovat na "NameID" pro aplikace založené na SAML. Pro tokeny JWT je "preferred_username."
   
   Vytvoření uživatele ve službě Azure AD buď ručně jako uživatel založené na cloudu nebo synchronizovat uživatele z místní pomocí modulu Azure AD Connect sync. Zajistěte, aby že informace odpovídající deklarace identity odesílají do aplikace.

- **Konfigurace jednotného přihlašování**

   Z [seznam aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list), vyhledání a spuštění kurzu jednotného přihlašování pro vaši aplikaci a potom postupujte podle kroků v kurzu úspěšně konfigurace aplikace SaaS.

   Pokud nemůžete najít aplikace, najdete v článku [dokumentace ke službě Application vlastní](https://docs.microsoft.com/azure/active-directory/application-config-sso-how-to-configure-federated-sso-non-gallery). To vás provede o tom, jak přidat aplikaci, která se nenachází v galerii Azure AD.

   Pokud chcete, můžete použít deklarací identity vystavených v tokenu SAML pro podnikové aplikace pomocí [doprovodných materiálech společnosti Microsoft](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping). Ujistěte se, že to se mapuje na čekáte pošle SAML pro vaši aplikaci. Pokud narazíte na problémy při konfiguraci, použijte naše doprovodné materiály na [jak ladit jednotné přihlašování integrace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging).

Registrace vlastní aplikace je funkce licence Azure AD Premium P1 nebo P2.

### <a name="provide-sso-change-communications-to-end-users"></a>Koncovým uživatelům poskytovat jednotné přihlašování změnu komunikace

Implementace plánu komunikace. Ujistěte se, že jste umožňuje koncovým uživatelům vědět, že změnu přichází, když dorazila, co dělat, nyní a tom, jak pomoci.

### <a name="verify-end-user-scenarios-for-sso"></a>Ověřte scénářů koncového uživatele pro jednotné přihlašování

Můžete použít následující testovací případy provádět testy na firemních a osobních zařízení tak, aby vaše konfigurace jednotného přihlašování fungují podle očekávání. Následující scénáře předpokládají, že je uživatel přejdete na adresu URL aplikace a probíhá ověřování tok, který iniciované poskytovatelem služeb (tok ověřování iniciovaného Zprostředkovatelem přihlašování).

| Scénář | Očekávaný výsledek na tok ověřování iniciovaného Zprostředkovatelem přihlašování uživatelem |
|----------|---------------------------------------------------|
| Přihlaste se k aplikaci pomocí aplikace Internet Explorer při připojení k síti corpnet. | Nastane, integrované ověřování Windows (IWA) bez dalších výzev. |
| Přihlaste se k aplikaci pomocí aplikace Internet Explorer při vypnutí corpnet se nový pokus o přihlášení. | Založené na formulářích řádku na serveru služby AD FS. Uživatel úspěšně přihlásí a prohlížeč zobrazí výzvu pro vícefaktorové ověřování. |
| Přihlášení k aplikaci pomocí aplikace Internet Explorer při vypnutí corpnet s aktuální relací a nikdy provedlo vícefaktorové ověřování. | Uživatel nezíská řádku pro první faktor. Uživateli se zobrazí výzva pro vícefaktorové ověřování. |
| Přihlášení k aplikaci pomocí aplikace Internet Explorer při vypnutí corpnet s aktuální relací a už provedl vícefaktorové ověřování v této relaci. | Uživatel nezíská řádku pro první faktor. Uživatel nezíská vícefaktorové ověřování. Uživatel SSOs do aplikace. |
| Přihlaste se k aplikaci Chrome nebo Firefox a Safari při vypnutí corpnet s aktuální relací a už provedl vícefaktorové ověřování v této relaci. | Uživatel nezíská řádku pro první faktor. Uživatel nezíská vícefaktorové ověřování. Uživatel pro jednotné přihlašování do aplikace. |
| Přihlaste se k do aplikaci Chrome nebo Firefox a Safari při vypnutí corpnet se nový pokus o přihlášení. | Založené na formulářích řádku na serveru služby AD FS. Uživatel úspěšně přihlásí a prohlížeč zobrazí výzvu pro vícefaktorové ověřování. |
| Přihlaste se k aplikaci Chrome nebo Firefox, zatímco v podnikové síti s aktuální relací. | Uživatel nezíská řádku pro první faktor. Uživatel nezíská vícefaktorové ověřování. Uživatel pro jednotné přihlašování do aplikace. |
| Přihlaste se k aplikaci s aplikací pro mobilní aplikace se nový pokus o přihlášení. | Založené na formulářích řádku na serveru služby AD FS. Uživatel úspěšně přihlásí a ADAL klienta zobrazí výzvu pro vícefaktorové ověřování. |
| Neoprávněný uživatel se pokusí přihlásit do aplikace pomocí adresy URL pro přihlášení. | Založené na formulářích řádku na serveru služby AD FS. Uživateli nepodaří přihlásit s první faktor. |
| Ověřený uživatel se pokusí přihlásit ale zadá nesprávné heslo. | Uživatel přejde na adresu URL aplikace a přijímá chybných zadání uživatelského jména/hesla. |
| Ověřený uživatel klikne na odkaz v e-mailu a je už ověřený. | Uživatel klikne na adresu URL a je podepsán do aplikace bez dalších výzev. |
| Ověřený uživatel klikne na odkaz v e-mailu a ještě není ověřen. | Uživatel klikne na adresu URL a je výzva k ověření na první faktor. |
| Autorizovaný uživatel přihlásí do aplikace s aplikací pro mobilní aplikace (iniciovaného Zprostředkovatelem přihlašování) se nový pokus o přihlášení. | Založené na formulářích řádku na serveru služby AD FS. Uživatel úspěšně přihlásí a ADAL klienta zobrazí výzvu pro vícefaktorové ověřování. |
| Neoprávněný uživatel se pokusí přihlásit do aplikace pomocí adresy URL pro přihlášení (iniciovaného Zprostředkovatelem přihlašování). | Založené na formulářích řádku na serveru služby AD FS. Uživateli nepodaří přihlásit s první faktor. |
| Ověřený uživatel se pokusí přihlásit ale zadá nesprávné heslo.| Uživatel přejde na adresu URL aplikace a přijímá chybných zadání uživatelského jména/hesla. |
| Ověřený uživatel neodhlásí a znovu přihlásí. | Pokud je nakonfigurovaná adresa URL odhlašování, jsou přihlášeni uživatelé mimo všechny služby a výzva k ověření. |
| Ověřený uživatel neodhlásí a znovu přihlásí. | Pokud není konfigurována adresa URL odhlašování, uživatel bude automaticky přihlásí zpět se změnami pomocí existující token z existující relaci prohlížeče Azure AD. |
| Ověřený uživatel klikne na odkaz v e-mailu a je už ověřený. | Uživatel klikne na adresu URL a je podepsán do aplikace bez dalších výzev. |
| Ověřený uživatel klikne na odkaz v e-mailu a ještě není ověřen. | Uživatel klikne na adresu URL a je výzva k ověření na první faktor. |

## <a name="manage-sso"></a>Správa jednotného přihlašování

Tato část popisuje požadavky a doporučení pro správu úspěšně jednotného přihlašování.

### <a name="required-administrative-roles"></a>Požadované role pro správu

Vždy pomocí role s co nejmenším oprávnění k dispozici k provedení požadované úlohy v Azure Active Directory. Společnost Microsoft doporučuje [zkontrolujte různé role, které jsou k dispozici](https://docs.microsoft.com/azure/active-directory/active-directory-assign-admin-roles-azure-portal) a zvolit ten správný vyřešit vašim potřebám každé osoby pro tuto aplikaci. Některé role možná muset použít dočasně a odebrat po dokončení nasazení.

| Modelové osoby| Role | Role Azure AD (v případě potřeby) |
|--------|-------|-----------------------------|
| Pomoc správci helpdesku | Podpora vrstvy 1 | Žádný |
| Správce identit | Konfigurace a ladění při problémy mít vliv na služby Azure AD | Globální správce |
| Správce aplikace | Ověření identity uživatele v aplikaci, konfigurace pro uživatele s oprávněními | Žádný |
| Správci infrastruktury | Certifikát výměny vlastníka | Globální správce |
| Firmy vlastníka/účastníka | Ověření identity uživatele v aplikaci, konfigurace pro uživatele s oprávněními | Žádný |

Doporučujeme používat [Privileged Identity Management](https://docs.microsoft.com/azure/active-directory/active-directory-privileged-identity-management-configure) (PIM) pro správu role poskytnout další auditování, řízení a přístupu zkontrolujte pro uživatele s oprávněními adresáře.

### <a name="sso-certificate-lifecycle-management"></a>Správa životního cyklu certifikátu jednotného přihlašování

Je důležité identifikovat správné role a e-mailové distribuční seznamy úkol se správou životního cyklu podpisový certifikát mezi službami Azure AD a aplikace, který je konfigurován s jednotným přihlašováním. Tady jsou některé klíčové úlohy, doporučujeme v místě:

- Vlastníka pro aktualizaci vlastnosti uživatele v aplikaci
- Vlastník na volání pro podporu pro případ přerušení nebo opravy aplikace
- Úzce monitorovaných e-mailu distribučního seznamu pro oznámení o změnách související s certifikátem

Maximální doba platnosti certifikátu je tři roky. Doporučujeme vytvoření procesu na tom, jak postaráme změnit certifikát mezi službami Azure AD a aplikace. To může pomoci zabránit nebo minimalizovat výpadku kvůli vypršení platnosti certifikátu nebo platnost certifikátu výměny.

### <a name="rollback-process"></a>Proces vrácení zpět

Po dokončení testování založené na testovací případy, je čas na přechod do produkčního prostředí s vaší aplikací. Přesun do produkčního prostředí znamená, že budete implementovat plánované a otestované konfigurace vašeho produkčního tenanta a zavádět vašim uživatelům. Je důležité mít plán, co dělat v případě, že vaše nasazení není přejít podle plánu. Pokud v průběhu nasazení nezdaří, konfigurace jednotného přihlašování, musíte pochopit, jak zmírnit jakémkoli výpadku a dopad na vaše uživatele.

Dostupnost metody ověřování v rámci aplikace určí nejlepší volbou. Vždy ujistěte se, že nebudete mít podrobné dokumentaci pro vlastníky aplikace přesně jak vrátit zpět do původního stavu konfigurace přihlášení v případě, že vaše nasazení spouští problémy.

- **Pokud vaše aplikace podporuje více zprostředkovatelů identity**pro příklad LDAP a služby AD FS a Ping, neodstraňujte existující konfiguraci jednotného přihlašování během uvedení. Místo toho zakažte během migrace v případě, že je potřeba přepnout zpět později. 

- **Pokud aplikace nepodporuje více zprostředkovatelů identity** , ale umožňuje uživatelům přihlášení pomocí ověřování pomocí formulářů (uživatelského jména a hesla), ujistěte se, že uživatelé mohou přejít k tento přístup v případě selhání uvedení nové konfigurace jednotného přihlašování.

### <a name="access-management"></a>Správa přístupu

Doporučujeme, abyste výběr škálován přístupu při správě přístupu k prostředkům. Běžné přístupy zahrnují využívání místních skupin synchronizovat prostřednictvím služby Azure AD Connect, [vytvoření dynamické skupiny ve službě Azure AD podle uživatelských atributů](https://docs.microsoft.com/azure/active-directory/active-directory-groups-dynamic-membership-azure-portal), nebo vytvořením [samoobslužných skupin](https://docs.microsoft.com/azure/active-directory/active-directory-accessmanagement-self-service-group-management) ve službě Azure AD spravuje vlastníka prostředku.

### <a name="monitor-security"></a>Monitorování zabezpečení

Doporučujeme nastavení regulární tempo, ve kterém můžete prohlédnout různé aspekty zabezpečení aplikací SaaS a provádět všechny nápravné akce, které jsou požadovány.

### <a name="troubleshooting"></a>Řešení potíží

Následující odkazy obsahují scénáře řešení potíží. Můžete vytvořit konkrétní Průvodce pracovníkům podpory, který zahrnuje tyto scénáře a kroky a opravte je.

#### <a name="consent-issues"></a>Souhlas s problémy

- [Souhlas Neočekávaná chyba](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-prompt)

- [Chyba souhlasu uživatele](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-unexpected-user-consent-error)

#### <a name="sign-in-issues"></a>Problémy s přihlašováním

- [Potíže při přihlašování z vlastním portálu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-deeplink)

- [Potíže s přihlašováním z přístupového panelu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-other-problem-access-panel)

- [Chyba na přihlašovací stránce aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-application-error)

- [Problém přihlášení k aplikaci Microsoftu](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-first-party-microsoft)

#### <a name="sso-issues-for-applications-listed-in-the-azure-application-gallery"></a>Problémy s jednotného přihlašování pro aplikace, které jsou uvedeny v galerii aplikací Azure

- [Potíže s heslem jednotného přihlašování pro aplikace uvedená v galerii aplikací Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-gallery) 

- [Problém s federovaného jednotného přihlašování pro aplikace, které jsou uvedeny v galerii aplikací Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-gallery)   

#### <a name="sso-issues-for-applications-not-listed-in-the-azure-application-gallery"></a>Problémy s jednotného přihlašování pro aplikace, které nejsou uvedené v galerii aplikací Azure

- [Potíže s heslem jednotného přihlašování pro aplikace, které nejsou uvedené v galerii aplikací Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-password-sso-non-gallery) 

- [Problém s federovaného jednotného přihlašování pro aplikace, které nejsou uvedené v galerii aplikací Azure](https://docs.microsoft.com/azure/active-directory/manage-apps/application-sign-in-problem-federated-sso-non-gallery)

## <a name="next-steps"></a>Další postup

[Ladění SSO založeného na SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)

[Mapování deklarací identity pro aplikace pomocí Powershellu](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping)

[Přizpůsobení deklarací identity vystavených v tokenu SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

[Protokol pro jednotné přihlašování – SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

[Protokol SAML pro jednotné odhlašování](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-out-protocol-reference)

[Azure AD s B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b) (pro externí uživatele, jako je například partnery a dodavateli)

[Podmíněný přístup Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

[Azure Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection)

[Přístup s jednotným přihlašováním](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)

[Kurz vývoje aplikace jednotného přihlašování](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list)
