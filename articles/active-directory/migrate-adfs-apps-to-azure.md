---
title: Migrace místních aplikací AD FS do Azure | Dokumenty Microsoft
description: Účelem tohoto dokumentu je pomoct organizacím porozumět postupu při migraci místních aplikací do služby Azure AD se zaměřením na federované aplikace SaaS.
services: active-directory
author: billmath
manager: mtillman
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/02/2018
ms.author: billmath
ms.openlocfilehash: f1256e2fefe1ad3d37403f37804888ab9dcf6e99
ms.sourcegitcommit: 168426c3545eae6287febecc8804b1035171c048
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/08/2018
---
# <a name="migrate-ad-fs-on-premises-apps-to-azure"></a>Migrace místních aplikací AD FS do Azure 

Účelem tohoto dokumentu je pomoct organizacím porozumět postupu při migraci místních aplikací do služby Azure AD.  Zaměřuje se na federované aplikace SaaS.  Tento dokument neobsahuje podrobné pokyny.  Poskytuje koncepční pokyny, které vám pomůžou dosáhnout migrace díky tomu, že porozumíte způsobu převodu místních konfigurací do služby Azure AD. Popisuje také požadavky nejběžnějších scénářů.

## <a name="introduction"></a>Úvod

Pokud máte místní adresář, který obsahuje uživatelské účty, pravděpodobně máte alespoň jednu nebo dvě aplikace.  A tyto aplikace jsou nakonfigurované pro přístup uživatelů prostřednictvím přihlášení pomocí těchto identit.

Pokud jste jako většina organizací, pravděpodobně jste v nějaké fázi zavádění cloudových aplikací a identit.  Možná jste zprovoznili Office 365 a službu Azure AD Connect.  Možná jste nainstalovali cloudové aplikace SaaS pro některé klíčové úlohy, ale ne pro všechny.  

Řada organizací má kromě Office 365 a aplikací založených na službě Azure AD i aplikace SaaS nebo vlastní obchodní aplikace federované přímo do místní služby pro přihlašování, jako je Azure Active Directory Federation Services (AD FS).  Tento průvodce migrací popisuje, proč a jak migrovat místní aplikace do služby Azure AD.

>[!NOTE]
>Tento průvodce obsahuje podrobné informace o konfiguraci a migraci aplikací SaaS a základní informace o vlastních obchodních aplikacích.  Do budoucna plánujeme přidat podrobnější pokyny pro obchodní aplikace.

Obrázek 1: Přímo propojené aplikace v místním prostředí ![místní prostředí](media/migrate-adfs-apps-to-azure/migrate1.png)

Obrázek 2: Aplikace federované přes službu Azure AD ![Azure](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="why-migrate-apps-to-azure-ad"></a>Proč migrovat aplikace do služby Azure AD?

Organizaci, která již používá službu AD FS, Ping nebo jiného místního zprostředkovatele ověřování, přinese migrace aplikací do služby Azure AD následující výhody:

**Lepší zabezpečení přístupu**
- [Podmíněný přístup služby Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal) umožňuje konfigurovat podrobné řízení přístupu k jednotlivým aplikacím, včetně vícefaktorového ověřování (MFA).  Zásady je možné použít pro aplikace SaaS i vlastní aplikace stejným způsobem, jako to pravděpodobně již děláte v Office 365.
- Pokud chcete detekovat hrozby a chránit přihlašování na základě strojového učení a heuristik identifikujících rizikový provoz, můžete využít integrované a neustále se vyvíjející možnosti služby Azure AD ve službě [Azure AD Identity Protection](https://docs.microsoft.com/azure/active-directory/active-directory-identityprotection).

**Spolupráce Azure AD s B2B**
- Jakmile bude přihlašování k aplikacím SaaS založené na službě Azure AD, můžete partnerům poskytnout přístup ke cloudovým prostředkům prostřednictvím [Spolupráce Azure AD s B2B](https://docs.microsoft.com/azure/active-directory/active-directory-b2b-what-is-azure-ad-b2b).

**Jednodušší prostředí pro správu a další možnosti služby Azure AD**
- Azure AD jako zprostředkovatel identity pro aplikace SaaS podporuje další možnosti, jako jsou podpisové certifikáty tokenů pro jednotlivé aplikace, [konfigurovatelná data vypršení platnosti certifikátů](https://docs.microsoft.com/azure/active-directory/active-directory-sso-certs) a [automatické zřizování](https://docs.microsoft.com/azure/active-directory/active-directory-saas-app-provisioning) uživatelských účtů (v klíčových aplikacích z galerie) na základě identit Azure AD.

**Zachování výhod místního zprostředkovatele identity**
- Přestože získáte výhody služby Azure AD, můžete k ověřování nadále používat své místní řešení, abyste si zachovali výhody, jako jsou místní řešení vícefaktorového ověřování (MFA), protokolování a auditování. 

**Získání náskoku s ukončením provozu místního zprostředkovatele identity**
- Pro organizace, které chtějí ukončit provoz produktu místního ověřování, migrace aplikací do služby Azure AD usnadňuje přechod díky tomu, že určitou část práce eliminuje. 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Mapování typů místních aplikací na typy aplikací ve službě Azure AD
Většina aplikací spadá do jedné z několika kategorií v závislosti na typu přihlašování, které používají.  Tyto kategorie určují, jak je aplikace reprezentovaná ve službě Azure AD.

Stručně řečeno, aplikace SAML 2.0 je možné integrovat se službou Azure AD buď prostřednictvím galerie aplikací Azure AD, nebo jako aplikace mimo galerii.  Podobným způsobem je možné integrovat se službou Azure AD aplikace, které používají OAuth 2.0 nebo OpenID Connect, jako registrace aplikací.  Další podrobnosti najdete dále v tomto dokumentu.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Federované aplikace SaaS vs. vlastní obchodní aplikace
Mezi federované aplikace patří aplikace, které spadají do uvedených kategorií.

- Aplikace SaaS 
    - Pokud se vaši uživatelé přihlašují k aplikacím SaaS, jako jsou Salesforce, ServiceNow nebo Workday, a přihlašování integrujete do místního zprostředkovatele identity, jako je služba AD FS nebo Ping, používáte federované přihlašování pro aplikace SaaS.
    - Aplikace obvykle k federovanému přihlašování používají protokol SAML 2.0.
    - Aplikace spadající do této kategorie je možné integrovat se službou Azure AD jako podnikové aplikace, a to buď z galerie, nebo jako aplikace mimo galerii.
- Vlastní obchodní aplikace
    - Toto označení odkazuje na jiné aplikace než SaaS vyvinuté interně ve vaší organizaci nebo dostupné jako standardní zabalené produkty, které jsou nainstalované ve vašem datacentru.  Patří mezi ně aplikace SharePointu a aplikace využívající architekturu Windows Identity Foundation (WIF).
    - Aplikace můžou k federovanému přihlašování používat protokol SAML 2.0, WS-Federation, OAuth nebo OpenID Connect.
    - Vlastní aplikace, které používají protokol OAuth 2.0, OpenID Connect nebo WS-Federation, je možné integrovat se službou Azure AD jako registrace aplikací a vlastní aplikace, které používají protokol SAML 2.0 nebo WS-Federation, je možné integrovat jako aplikace mimo galerii v rámci podnikových aplikací.

### <a name="non-federated-apps"></a>Jiné než federované aplikace
Jiné než federované aplikace je navíc možné integrovat se službou Azure AD s využitím Proxy aplikací Azure AD a souvisejících možností.  Další informace o těchto možnostech najdete na následujících odkazech:
- Aplikace používající integrované ověřování Windows (WIA) přímo ke službě Active Directory
    - Tyto aplikace je možné integrovat do služby Azure AD prostřednictvím [Proxy aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/application-proxy-publish-azure-portal).
- Aplikace, které se prostřednictvím agenta integrují s vaším zprostředkovatelem jednotného přihlašování a k ověřování používají hlavičky
    - Místní aplikace, které k přihlašování používají nainstalovaného agenta a ověřování založené na hlavičkách, je možné nakonfigurovat pro přihlašování založené na službě Azure AD s použitím Proxy aplikací Azure AD pomocí [PingAccessu pro Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/).

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Převod místních federovaných aplikací do služby Azure AD 
Služby AD FS a Azure AD naštěstí fungují podobně, takže pro obě platí stejné koncepty konfigurace vztahu důvěryhodnosti, adres pro přihlášení a odhlášení a identifikátorů.  Existuje však několik malých rozdílů, které je při přechodu potřeba znát.

Následující tabulka obsahuje mapování několika klíčových pojmů sdílených službami AD FS, Azure AD a aplikacemi SaaS, které vám s převodem pomůžou. 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Reprezentace aplikace ve službě Azure AD nebo AD FS
Migrace začíná vyhodnocením konfigurace aplikace v místním prostředí a namapováním této konfigurace na službu Azure AD.  Následuje mapování elementů konfigurace přijímající strany AD FS na odpovídající elementy ve službě Azure AD.  
- Termín v AD FS: Přijímající strana nebo vztah důvěryhodnosti přijímající strany
- Termín v Azure AD: Podniková aplikace nebo registrace aplikace (v závislosti na typu aplikace)

|Element konfigurace aplikace|Popis|V rámci konfigurace AD FS|Odpovídající umístění v konfiguraci Azure AD|Element tokenu SAML|
|-----|-----|-----|-----|-----|
|Přihlašovací adresa URL aplikace|Adresa URL přihlašovací stránky této aplikace. Sem by uživatel přešel za účelem zahájení přihlašování k aplikaci v rámci toku SAML iniciovaného SP.|neuvedeno|Ve službě Azure AD se přihlašovací adresa URL konfiguruje na webu Azure Portal ve vlastnostech jednotného přihlašování aplikace jako Přihlašovací adresa URL.</br></br>(Pro zobrazení přihlašovací adresy URL možná budete muset kliknout na Zobrazit pokročilé nastavení URL)||
|Adresa URL odpovědi aplikace|Adresa URL aplikace z pohledu zprostředkovatele identity.  Sem se odešle uživatel a token po přihlášení uživatele ke zprostředkovateli identity.</br></br>  Tato adresa URL se někdy označuje jako koncový bod příjemce kontrolního výrazu SAML.|Nachází se ve vztahu důvěryhodnosti přijímající strany AD FS aplikace.  Klikněte pravým tlačítkem na přijímající stranu a zvolte Vlastnosti a kartu Koncové body.|Ve službě Azure AD se adresa URL odpovědi konfiguruje na webu Azure Portal ve vlastnostech jednotného přihlašování aplikace jako Adresa URL odpovědi.</br></br>(Pro zobrazení adresy URL odpovědi možná budete muset kliknout na Zobrazit pokročilé nastavení URL)|Mapuje se na cílový element v tokenu SAML.</br></br>  Příklad hodnoty: https://contoso.my.salesforce.com|
|Adresa URL pro odhlášení z aplikace|Adresa URL, na kterou se po odhlášení uživatele z aplikace odešlou požadavky na odhlášení a vyčištění, které zajistí odhlášení ze všech dalších aplikací, ke kterým zprostředkovatel identity uživatele přihlásil.|Nachází se ve správě služby AD FS v části Vztahy důvěryhodnosti přijímající strany.  Klikněte pravým tlačítkem na přijímající stranu, zvolte Vlastnosti a klikněte na kartu Koncové body.|Není k dispozici – Azure AD nepodporuje jednotné odhlašování, tedy odhlášení ze všech aplikací.  Jednoduše odhlásí uživatele ze samotné služby Azure AD.|Není k dispozici|
|Identifikátor aplikace|Identifikátor aplikace z pohledu zprostředkovatele identity. Jako identifikátor se často používá přihlašovací adresa URL (ale ne vždy).</br></br>  V některých případech se v aplikaci označuje jako ID entity.|Ve službě AD FS je to ID přijímající strany: Klikněte pravým tlačítkem na vztah důvěryhodnosti přijímající strany, zvolte Vlastnosti a klikněte na kartu Identifikátory.|Ve službě Azure AD se identifikátor konfiguruje na webu Azure Portal ve vlastnostech jednotného přihlašování aplikace jako Identifikátor v části Domény a adresy URL (možná budete muset kliknout na zaškrtávací políčko Zobrazit pokročilé nastavení URL).|Odpovídá elementu Audience v tokenu SAML.|
|Federační metadata aplikace|Umístění federačních metadat aplikace.  Používají se ve zprostředkovateli identity k automatické aktualizaci konkrétních nastavení konfigurace, jako jsou koncové body nebo šifrovací certifikáty.|Adresa URL federačních metadat aplikace se nachází ve vztahu důvěryhodnosti přijímající strany AD FS aplikace.  Klikněte na vztah důvěryhodnosti pravým tlačítkem, zvolte Vlastnosti a pak klikněte na kartu Monitorování.|Není k dispozici – Azure AD nepodporuje přímé používání federačních metadat aplikace.|Není k dispozici|
|Identifikátor uživatele / NameID|Atribut sloužící k jedinečné identifikaci identity uživatele ze služby Azure AD nebo AD FS ve vaší aplikaci.</br></br>  Obvykle se jedná o hlavní název uživatele (UPN) nebo e-mailovou adresu uživatele.|Ve službě AD FS se nachází v podobě pravidla deklarace identity na přijímající straně.  Ve většině případů se jedná o pravidlo deklarace identity, které vystavuje deklaraci identity s typem končícím na „nameidentifier“.|Ve službě Azure AD se identifikátor uživatele nachází na webu Azure Portal ve vlastnostech jednotného přihlašování aplikace pod nadpisem Atributy uživatele.</br></br>Ve výchozím nastavení se použije hlavní název uživatele (UPN).|Předává se do aplikace ze zprostředkovatele identity jako element NameID v tokenu SAML.|
|Další deklarace identity, které se mají do aplikace odeslat|Kromě elementu Identifikátor uživatele / NameID se do aplikace obvykle ze zprostředkovatele identity odesílají i další informace o deklaraci identity, například jméno, příjmení, e-mailová adresa a skupiny, kterých je uživatel členem.|Ve službě AD FS se nachází v podobě dalších pravidel deklarace identity na přijímající straně.|Ve službě Azure AD se nachází na webu Azure Portal ve vlastnostech jednotného přihlašování aplikace pod nadpisem Atributy uživatele. Klikněte na Zobrazit a upravit všechny ostatní atributy uživatele.|| 

### <a name="representing-azure-ad-as-an-identity-provider-idp-in-a-saas-app"></a>Reprezentace služby Azure AD jako zprostředkovatele identity (IdP) v aplikaci SaaS
V rámci migrace je nutné nakonfigurovat aplikaci tak, aby odkazovala na službu Azure AD (místo místního zprostředkovatele identity).  Tato část se zaměřuje především na aplikace SaaS používající protokol SAML, a ne na vlastní nebo obchodní aplikace. Popsané koncepty se však vztahují i na vlastní a obchodní aplikace. 

Směrování aplikace SaaS do služby Azure AD zajišťuje několik základních klíčových věcí:
- Vystavitel zprostředkovatele identity: https&#58;//sts.windows.net/{ID_tenanta}/
- Přihlašovací adresa URL zprostředkovatele identity: https&#58;//login.microsoftonline.com/{ID_tenanta}/saml2
- Odhlašovací adresa URL zprostředkovatele identity: https&#58;//login.microsoftonline.com/{ID_tenanta}/saml2 
- Umístění federačních metadat: https&#58;//login.windows.net/{ID_tenanta} <ID_tenanta>/federationmetadata/2007-06/federationmetadata.xml?appid={ID_aplikace} 

kde se {ID_tenanta} nahradí za ID vašeho tenanta, které se nachází na webu Azure Portal v části Azure Active Directory -> Vlastnosti jako ID adresáře, a {ID_aplikace} se nahradí za ID vaší aplikace, které se nachází ve vlastnostech aplikace jako ID aplikace.

Následující tabulka obsahuje podrobnější popis klíčových elementů konfigurace zprostředkovatele identity pro konfiguraci nastavení jednotného přihlašování v aplikaci a jejich hodnoty nebo umístění v rámci služeb AD FS a Azure AD.  Referenčním rámcem tabulky je aplikace SaaS, která potřebuje vědět, kam má odesílat požadavky na ověřování a jak ověřovat přijaté tokeny.

|Element konfigurace|Popis|AD FS|Azure AD|
|---|---|---|---|
|Federační </br>adresa URL </br>zprostředkovatele identity|Přihlašovací adresa URL zprostředkovatele identity z pohledu aplikace (kam se uživatel přesměruje kvůli přihlášení).|Přihlašovací adresa URL služby AD FS je název federační služby AD FS, za kterým následuje /adfs/ls/, například: https&#58;//fs.contoso.com/adfs/ls/|Odpovídající hodnota pro službu Azure AD má následující formát, kde se {ID_tenanta} nahradí za ID vašeho tenanta, které se nachází na webu Azure Portal v části Azure Active Directory -> Vlastnosti jako ID adresáře.</br></br>Aplikace používající protokol SAML-P: https&#58;//login.microsoftonline.com</br>/{ID_tenanta}/saml2 </br></br>Aplikace používající protokol WS-Federation: https&#58;//login.microsoftonline.com</br>/{ID_tenanta}/wsfed|
|Odhlašovací </br>adresa URL </br>zprostředkovatele identity|Odhlašovací adresa URL zprostředkovatele identity z pohledu aplikace (kam se uživatel přesměruje, když se rozhodne odhlásit z aplikace).|Pro službu AD FS je odhlašovací adresa URL buď stejná jako přihlašovací adresa URL, nebo stejná adresa URL, ke které je připojeno wa=wsignout1.0, například https&#58;//fs.contoso.com/adfs/ls?wa=wsignout1.0.|Odpovídající hodnota pro službu Azure AD závisí na tom, jestli je aplikace schopná podporovat odhlašování přes protokol SAML 2.0.</br></br>Pokud aplikace podporuje odhlašování přes protokol SAML, hodnota má následující formát, kde se hodnota {ID_tenanta} nahradí za ID tenanta, které se nachází na webu Azure Portal v části Azure Active Directory -> Vlastnosti jako ID adresáře. https&#58;//login.microsoftonline.com</br>/{ID_tenanta}/saml2</br></br>Pokud aplikace nepodporuje odhlašování přes protokol SAML: https&#58;//login.microsoftonline.com</br>/common/wsfederation?wa=wsignout1.0|
|Podpisový </br>certifikát </br>Certifikát|Certifikát, jehož privátní klíč používá zprostředkovatel identity k podepisování vydaných tokenů.  Ověřuje, že token pochází ze stejného zprostředkovatele identity, kterému aplikace podle konfigurace důvěřuje.|Podpisový certifikát tokenu AD FS se nachází ve správě služby AD FS v části Certifikáty.|Ve službě Azure AD se podpisový certifikát tokenu nachází na webu Azure Portal ve vlastnostech jednotného přihlašování aplikace pod nadpisem Podpisový certifikát SAML, kde můžete certifikát stáhnout, abyste ho mohli nahrát do aplikace.</br></br>  Pokud má aplikace více než jeden certifikát, pak se všechny certifikáty nacházejí v souboru XML s federačními metadaty.|
|Identifikátor / </br>Vystavitel|Identifikátor zprostředkovatele identity z pohledu aplikace (někdy se označuje jako Vystavitel nebo ID vystavitele).</br></br>V tokenu SAML se hodnota zobrazí jako element Issuer.|Identifikátor pro službu AD FS je obvykle Identifikátor federační služby ve správě služby AD FS v části Služba -> Upravit vlastnosti služby FS (Federation Service).  Příklad: http&#58;//fs.contoso.com/adfs/services/trust|Odpovídající hodnota pro službu Azure AD má následující formát, kde se hodnota {ID_tenanta} nahradí za ID tenanta, které se nachází na webu Azure Portal v části Azure Active Directory -> Vlastnosti jako ID adresáře.  https&#58;//sts.windows.net/{ID_tenanta}/|
|Federační </br>metadata </br>Metadata|Umístění veřejně dostupných federačních metadat zprostředkovatele identity.  (Federační metadata se v některých aplikacích používají jako alternativa ke konfiguraci adres URL, identifikátoru a podpisového certifikátu tokenu správcem)|Adresu URL federačních metadat služby AD FS najdete ve správě služby AD FS v části Služba -> Koncové body -> Metadata ->Typ: Federační metadata, například: https&#58;//fs.contoso.com/ FederationMetadata/2007-06/</br>FederationMetadata.xml|Odpovídající hodnota pro službu Azure AD má formát https&#58;//login.microsoftonline.com</br>/{název_domény_tenanta}/FederationMetadata/2007-06/</br>FederationMetadata.xml, kde se hodnota {název_domény_tenanta} nahradí názvem vašeho tenanta ve formátu contoso.onmicrosoft.com. </br></br>[Další informace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-federation-metadata) o federačních metadatech ve službě Azure AD.

## <a name="migrating-saas-apps"></a>Migrace aplikací SaaS
V současné době se migrace aplikací SaaS ze služby AD FS nebo jiného zprostředkovatele identity do služby Azure AD provádí ručně. Pokyny pro konkrétní aplikaci [najdete v seznamu kurzů integrace aplikací SaaS v galerii](https://docs.microsoft.com/azure/active-directory/active-directory-saas-tutorial-list).

Kurz integrace předpokládá, že provádíte integraci tzv. na zelené louce.  Během plánování, vyhodnocování, konfigurace a přímé migrace aplikací byste měli znát několik klíčových konceptů specifických pro migraci.  
- I když migrace některých aplikací může být snadná, aplikace se složitějšími požadavky, jako jsou vlastní deklarace identity, můžou vyžadovat další konfiguraci ve službě Azure AD nebo Azure AD Connect.
- V nejběžnějších scénářích aplikace vyžaduje pouze deklaraci identity NameId a další běžné deklarace identity s identifikátorem uživatele. Pokud chcete zjistit, jestli se nevyžadují nějaké další deklarace identity, zkontrolujte, jaké deklarace identity vydáváte ze služby AD FS nebo zprostředkovatele identity třetí strany.
- Jakmile zjistíte, že se vyžadují další deklarace identity, je potřeba zajistit jejich dostupnost ve službě Azure AD.  Je potřeba zkontrolovat konfiguraci synchronizace Azure AD Connect a ujistit se, že se požadovaný atribut, například samAccountName, synchronizuje do služby Azure AD.
- Po zpřístupnění atributů ve službě Azure AD přidejte do služby Azure AD pravidla vystavování deklarací identity, aby se tyto atributy zahrnuly jako deklarace identity do vydávaných tokenů.  To se provádí v rámci vlastnosti jednotného přihlašování aplikace ve službě Azure AD.

### <a name="assessing-what-can-be-migrated"></a>Vyhodnocení položek, které je možné migrovat
Aplikace SAML 2.0 je možné integrovat se službou Azure AD buď prostřednictvím galerie aplikací Azure AD, nebo jako aplikace mimo galerii.  

Existují některé konfigurace, jejichž konfigurace ve službě Azure AD vyžaduje další kroky, a některé, které v současné době nejsou podporované.  Pokud chcete zjistit, co je možné přesunout, prohlédněte si aktuální konfiguraci jednotlivých aplikací, především následující položky:
- Nakonfigurovaná pravidla deklarací identity (pravidla transformace vystavení)
- Formát a atribut elementu SAML NameID
- Vydané verze tokenu SAML
- Další konfigurace, jako jsou pravidla autorizace vystavení nebo zásady řízení přístupu a pravidla vícefaktorového ověřování (další ověřování)

#### <a name="what-can-be-migrated-today"></a>Co je aktuálně možné migrovat
Mezi aplikace, které je v aktuálně možné snadno migrovat, patří aplikace SAML 2.0 používající standardní sadu elementů konfigurace a deklarací identity.  Tyto aplikace můžou zahrnovat:
- hlavní název uživatele (UPN)
- e-mailovou adresu
- jméno
- příjmení
- Alternativní atribut jako SAML NameID, včetně atributu Azure AD mail, předpony atributu mail, atributu employeeid, atributů rozšíření 1–15 nebo místního atributu SamAccountName (viz [Úprava deklarace identity NameIdentifier](./develop/active-directory-saml-claims-customization.md))
- Vlastní deklarace identity (informace o podporovaných mapováních deklarací identity najdete v [tomto](active-directory-claims-mapping.md) a [tomto](./develop/active-directory-saml-claims-customization.md) dokumentu)

Kromě vlastních deklarací identity a elementů NameID jsou konfigurace, které v rámci migrace vyžadují další kroky konfigurace ve službě Azure AD, následující:
- Pravidla vlastního nebo vícefaktorového ověřování ve službě AD FS (nakonfigurovaná pomocí funkce [Podmíněný přístup služby Azure AD](active-directory-conditional-access-azure-portal.md))
- Aplikace s několika koncovými body SAML je možné ve službě Azure AD nakonfigurovat pomocí PowerShellu (tato možnost není dostupná na portálu)
- Aplikace WS-Federation, jako jsou aplikace SharePointu, které vyžadují tokeny SAML verze 1.1, je nutné nakonfigurovat ručně pomocí PowerShellu

#### <a name="appsconfigurations-not-supported-in-azure-ad-today"></a>Aplikace a konfigurace, které služba Azure AD v současné době nepodporuje
V současné době není možné migrovat aplikace, které vyžadují následující funkce.  Pokud máte aplikace, které tyto funkce vyžadují, poskytněte nám zpětnou vazbu v části Komentáře.
- Možnosti protokolu
    - Podpora jednotného odhlašování (SLO) SAML ze všech přihlášených aplikací
    - Podpora vzoru WS-Trust ActAs
    - Překlad artefaktů SAML 
    - Ověřování podpisů u podepsaných požadavků SAML (všimněte si, že se přijímají podepsané požadavky, ale podpis se neověřuje)
 - Možnosti tokenu 
     - Šifrování tokenu SAML 
     - Tokeny SAML verze 1.1 v rámci odpovědí protokolu SAML 
- Možnosti deklarací identity v rámci tokenů
    - Vystavování názvů místních skupin jako deklarací identity
    - Deklarace identity z jiných úložišť než služba Azure AD
    - Komplexní pravidla transformace vystavování deklarací identity (informace o podporovaných mapováních deklarací identity najdete v [tomto](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) a [tomto](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization) dokumentu)
    - Vystavování rozšíření adresáře jako deklarací identity
    - Vlastní specifikace formátu NameID
    - Vystavování atributů s více hodnotami

>[!NOTE]
>Azure AD se neustále vyvíjí a v této oblasti přidává další možnosti. Tento dokument pravidelně aktualizujeme. 

### <a name="configuring-azure-ad"></a>Konfigurace služby Azure AD    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Konfigurace nastavení jednotného přihlašování pro aplikaci SaaS

Ve službě Azure AD se konfigurace přihlašování SAML podle potřeb vaší aplikace provádí v rámci vlastností jednotného přihlašování aplikace v části Atributy uživatele, jak je znázorněno níže:

![](media/migrate-adfs-apps-to-azure/migrate3.png)
- Kliknutím na Zobrazit a upravit všechny ostatní atributy uživatele zobrazte atributy, které se mají odeslat jako deklarace identity v tokenu zabezpečení.

![](media/migrate-adfs-apps-to-azure/migrate4.png)
- Kliknutím na řádek konkrétního atributu můžete atribut upravit nebo můžete kliknout na Přidat atribut a přidat nový atribut. 
![](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Přiřazení uživatelů k aplikaci
Aby se vaši uživatelé v rámci Azure AD mohli přihlásit k dané aplikaci SaaS, musíte jim ze služby Azure AD udělit přístup.

Pokud chcete přiřadit uživatele na portálu Azure AD, přejděte na portálu na obrazovku příslušné aplikace SaaS a na bočním panelu klikněte na Uživatelé a skupiny. Uživatele nebo skupinu přidáte tak, že v horní části obrazovky kliknete na Přidat uživatele. 

![](media/migrate-adfs-apps-to-azure/migrate6.png) 

![](media/migrate-adfs-apps-to-azure/migrate7.png)Přístup ověříte tak, že by se uživatelům měla daná aplikace SaaS zobrazit po přihlášení na jejich [přístupovém panelu](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) na adrese http://myapps.microsoft.com. V následujícím příkladu byl uživateli úspěšně přidělený přístup k Salesforce i ServiceNow.

![](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configuring-the-saas-app"></a>Konfigurace aplikace SaaS
Proces přechodu od místní federace na službu Azure AD závisí na tom, jestli aplikace SaaS, se kterou pracujete, podporuje více zprostředkovatelů identity (IdP).  Následuje několik běžných otázek souvisejících s podporou více zprostředkovatelů identity:

   **Otázka: Co znamená, že aplikace podporuje více zprostředkovatelů identity?**
    
   Odpověď: Aplikace SaaS, které podporují více zprostředkovatelů identity, umožňují zadat veškeré informace o novém zprostředkovateli identity (v našem případě je to služba Azure AD) před změnou prostředí pro přihlašování.  Po dokončení konfigurace můžete přepnout konfiguraci ověřování aplikace, aby odkazovala na službu Azure AD.

   Otázka: Proč je důležité, jestli aplikace SaaS podporuje více zprostředkovatelů identity?

   Odpověď: Pokud se nepodporuje více zprostředkovatelů identity, musí si správce vyhradit krátké časové okno na řešení případných výpadků služby a údržbu během konfigurace služby Azure AD jako nového zprostředkovatele identity aplikace. Během takového výpadku by uživatelé měli obdržet upozornění na nemožnost přihlášení ke svým účtům.

   Pokud aplikace podporuje více zprostředkovatelů identity, je možné provést konfiguraci dalšího zprostředkovatele identity předem, aby správce mohl zprostředkovatele identity jednoduše přepnout v Azure.

   Navíc, pokud aplikace podporuje více zprostředkovatelů identity a zvolíte více zprostředkovatelů identity, kteří budou současně zpracovávat ověřování přihlašování, uživateli se na přihlašovací stránce zobrazí možnost výběru zprostředkovatele identity, který provede ověření.

#### <a name="example-multiple-idp-support"></a>Příklad: Podpora více zprostředkovatelů identity
Například v Salesforce se konfigurace zprostředkovatele identity nachází v části Nastavení -> Nastavení společnosti -> Moje doména -> Konfigurace ověřování.

![](media/migrate-adfs-apps-to-azure/migrate9.png)

Vzhledem k tomu, že jste dříve vytvořili konfiguraci v části Identita -> Nastavení jednotného přihlašování, měli byste být schopni změnit svého zprostředkovatele identity z řekněme AD FS na Azure AD. 

![](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Volitelné: Konfigurace zřizování uživatelů ve službě Azure AD
Pokud chcete, aby zřizování uživatelů pro danou aplikaci SaaS zpracovávala přímo služba Azure AD, můžete si volitelně prostudovat tento dokument o správě zřizování uživatelských účtů pro podnikové aplikace ve službě Azure AD.

## <a name="next-steps"></a>Další kroky

- [Správa aplikací pomocí Azure Active Directory](active-directory-enable-sso-scenario.md)
- [Správa přístupu k aplikacím](active-directory-managing-access-to-apps.md)
- [Federace služby Azure AD Connect](active-directory-aadconnectfed-whatis.md)