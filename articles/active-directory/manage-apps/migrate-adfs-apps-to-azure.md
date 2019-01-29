---
title: Přesun aplikací ze služby AD FS do Azure AD. | Dokumenty Microsoft
description: Tento článek je určený pomoct organizacím porozumět postupu přesunutí aplikací do služby Azure AD se zaměřením na federované aplikace SaaS.
services: active-directory
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 03/02/2018
ms.author: barbkess
ms.openlocfilehash: e71cab407e88bcac1b1bb87e015a3ab5adabf734
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55152247"
---
# <a name="move-applications-from-ad-fs-to-azure-ad"></a>Přesunout aplikace ze služby AD FS do Azure AD 

Tento článek vám pomůže pochopit postup přesunutí aplikací ze služby AD FS do Azure Active Directory (Azure AD). Zaměřuje se na federované aplikace SaaS. 

Tento článek neobsahuje podrobné pokyny. Poskytuje koncepční pokyny, které vám pomůžou dosáhnout migrace díky tomu, že porozumíte způsobu převodu místních konfigurací do služby Azure AD. Věnuje se také běžným scénářům.

## <a name="introduction"></a>Úvod

Pokud máte místní adresář, který obsahuje uživatelské účty, pravděpodobně máte alespoň jednu nebo dvě aplikace. A tyto aplikace jsou nakonfigurované pro přístup uživatelů prostřednictvím přihlášení pomocí těchto identit.

Pokud jste jako většina organizací, pravděpodobně jste v nějaké fázi zavádění cloudových aplikací a identit. Možná jste zprovoznili Office 365 a službu Azure AD Connect. Možná jste nastavili cloudové aplikace SaaS pro některé klíčové úlohy, ale ne pro všechny.  

Řada organizací má kromě Office 365 a aplikací založených na službě Azure AD i aplikace SaaS nebo vlastní obchodní aplikace federované přímo do místní služby pro přihlašování, jako je Azure Active Directory Federation Services (AD FS). Tato příručka popisuje, proč a jak přesunout vaše aplikace do služby Azure AD.

>[!NOTE]
>Tento průvodce obsahuje podrobné informace o konfiguraci a migraci aplikací SaaS a základní informace o vlastních obchodních aplikacích. Do budoucna plánujeme přidat podrobnější pokyny pro obchodní aplikace.

![Přímo propojené aplikace v místním prostředí](media/migrate-adfs-apps-to-azure/migrate1.png)

![Aplikace federované přes službu Azure AD](media/migrate-adfs-apps-to-azure/migrate2.png)

## <a name="reasons-for-moving-apps-to-azure-ad"></a>Důvody pro přesun aplikací do služby Azure AD

Pro organizace, která již používá službu AD FS, Ping nebo jiného místního zprostředkovatele ověřování umožňuje přesouvají aplikace do Azure AD následující výhody:

**Lepší zabezpečení přístupu**
- [Podmíněný přístup služby Azure AD](../active-directory-conditional-access-azure-portal.md) umožňuje konfigurovat podrobné řízení přístupu k jednotlivým aplikacím, včetně vícefaktorového ověřování Azure. Zásady je možné použít pro aplikace SaaS i vlastní aplikace stejným způsobem, jako to pravděpodobně již děláte v Office 365.
- Pokud chcete detekovat hrozby a pomoct ochránit přihlašování na základě strojového učení a heuristik identifikujících rizikový provoz, můžete využít službu [Azure AD Identity Protection](../active-directory-identityprotection.md).

**Spolupráce Azure AD s B2B**
- Jakmile bude přihlašování k aplikacím SaaS založené na službě Azure AD, můžete partnerům poskytnout přístup ke cloudovým prostředkům prostřednictvím [Spolupráce Azure AD s B2B](../b2b/what-is-b2b.md).

**Jednodušší prostředí pro správu a další možnosti služby Azure AD**
- Azure AD jako zprostředkovatel identity pro aplikace SaaS podporuje další možnosti, jako například:
  - Podpisové certifikáty tokenů pro jednotlivé aplikace.
  - [Konfigurovatelná data vypršení platnosti certifikátů](manage-certificates-for-federated-single-sign-on.md).
  - [Automatizované zřizování](user-provisioning.md) uživatelských účtů (v klíčových aplikacích z Azure Marketplace) na základě identit Azure AD.

**Zachování výhod místního zprostředkovatele identity**
- Přestože získáte výhody služby Azure AD, můžete k ověřování nadále používat své místní řešení. Díky tomu si zachováte výhody, jako jsou místní řešení vícefaktorového ověřování, protokolování a auditování. 

**Pomoc s vyřazením místního zprostředkovatele identity**
- Pro organizace, které chcete vyřadit z provozu místní ověřování produkt umožňuje přesouvají aplikace do Azure AD usnadňuje přechod díky tomu že určitou část práce eliminuje. 

## <a name="mapping-types-of-apps-on-premises-to-types-of-apps-in-azure-ad"></a>Mapování typů místních aplikací na typy aplikací ve službě Azure AD
Většina aplikací spadá do jedné z několika kategorií v závislosti na typu přihlašování, které používají. Tyto kategorie určují, jak je aplikace reprezentovaná ve službě Azure AD.

Stručně řečeno, aplikace SAML 2.0 je možné integrovat se službou Azure AD buď prostřednictvím galerie aplikací Azure AD na Marketplace, nebo jako aplikace mimo Marketplace. Podobným způsobem je možné integrovat se službou Azure AD aplikace, které používají OAuth 2.0 nebo OpenID Connect, jako *registrace aplikací*. Další podrobnosti najdete dále v tomto dokumentu.

### <a name="federated-saas-apps-vs-custom-lob-apps"></a>Federované aplikace SaaS vs. vlastní obchodní aplikace
Mezi federované aplikace patří aplikace, které spadají do těchto kategorií:

- Aplikace SaaS 
    - Pokud se vaši uživatelé přihlašují k aplikacím SaaS, jako jsou Salesforce, ServiceNow nebo Workday, a přihlašování integrujete do místního zprostředkovatele identity, jako je AD FS nebo Ping, používáte federované přihlašování pro aplikace SaaS.
    - Aplikace obvykle k federovanému přihlašování používají protokol SAML 2.0.
    - Aplikace spadající do této kategorie je možné integrovat se službou Azure AD jako podnikové aplikace, a to buď z Marketplace, nebo jako aplikace mimo Marketplace.
- Vlastní obchodní aplikace
    - Toto označení odkazuje na jiné aplikace než SaaS vyvinuté interně ve vaší organizaci nebo dostupné jako standardní zabalené produkty, které jsou nainstalované ve vašem datacentru. Patří mezi ně aplikace SharePointu a aplikace využívající architekturu Windows Identity Foundation.
    - Aplikace můžou k federovanému přihlašování používat protokol SAML 2.0, WS-Federation, OAuth nebo OpenID Connect.
    - Vlastní aplikace, které používají OAuth 2.0, OpenID Connect nebo WS-Federation, je možné integrovat se službou Azure AD jako registrace aplikací. Vlastní aplikace, které používají protokol SAML 2.0 nebo specifikaci WF-Federation, je možné integrovat v rámci podnikových aplikací jako aplikace mimo Marketplace.

### <a name="non-federated-apps"></a>Jiné než federované aplikace
Jiné než federované aplikace můžete integrovat se službou Azure AD s využitím Proxy aplikací služby Azure AD a souvisejících možností. Mezi jiné než federované aplikace patří:
- Aplikace používající integrované ověřování Windows přímo se službou Active Directory. Tyto aplikace můžete integrovat se službou Azure AD prostřednictvím [Proxy aplikací služby Azure AD](application-proxy-add-on-premises-application.md).
- Aplikace, které se prostřednictvím agenta integrují s vaším zprostředkovatelem jednotného přihlašování a k ověřování používají hlavičky. Místní aplikace, které k přihlašování používají nainstalovaného agenta a ověřování založené na hlavičkách, je možné nakonfigurovat pro přihlašování založené na službě Azure AD prostřednictvím Proxy aplikací služby Azure AD pomocí [PingAccessu pro Azure AD](https://blogs.technet.microsoft.com/enterprisemobility/2017/06/15/ping-access-for-azure-ad-is-now-generally-available-ga/).

## <a name="translating-on-premises-federated-apps-to-azure-ad"></a>Převod místních federovaných aplikací do služby Azure AD 
Služby AD FS a Azure AD fungují podobně, takže pro obě platí stejné koncepty konfigurace vztahu důvěryhodnosti, adres URL pro přihlášení a odhlášení a identifikátorů. Při přechodu však musíte znát několik malých rozdílů.

Následující tabulky obsahuje mapování několika klíčových pojmů sdílených službami AD FS, Azure AD a aplikacemi SaaS, které vám s převodem pomůžou. 

### <a name="representing-the-app-in-azure-ad-or-ad-fs"></a>Reprezentace aplikace ve službě Azure AD nebo AD FS
Migrace začíná vyhodnocením konfigurace aplikace v místním prostředí a namapováním této konfigurace na službu Azure AD. Následující tabulka obsahuje mapování elementů konfigurace přijímající strany AD FS na odpovídající elementy ve službě Azure AD.  
- Termín v AD FS: Přijímající strana nebo vztah důvěryhodnosti předávající strany.
- Azure AD termín: Podnikové aplikace nebo registrace aplikace (v závislosti na typu aplikace).

|Element konfigurace aplikace|Popis|Umístění v konfiguraci AD FS|Odpovídající umístění v konfiguraci Azure AD|Element tokenu SAML|
|-----|-----|-----|-----|-----|
|Přihlašovací adresa URL aplikace|Adresa URL přihlašovací stránky této aplikace. Sem uživatel přejde za účelem přihlášení k aplikaci v rámci toku SAML iniciovaného zprostředkovatelem přihlašování.|neuvedeno|Ve službě Azure AD se přihlašovací adresa URL konfiguruje na webu Azure Portal ve vlastnostech **jednotného přihlašování** aplikace jako Přihlašovací adresa URL.</br></br>(Pro zobrazení přihlašovací adresy URL možná budete muset vybrat možnost **Zobrazit pokročilé nastavení URL**.)|neuvedeno|
|Adresa URL odpovědi aplikace|Adresa URL aplikace z pohledu zprostředkovatele identity (IdP). Sem se odešle uživatel a token po přihlášení uživatele ke zprostředkovateli identity.</br></br> Tato adresa URL se někdy označuje jako Koncový bod příjemce kontrolního výrazu SAML.|Nachází se ve vztahu důvěryhodnosti přijímající strany AD FS aplikace. Klikněte pravým tlačítkem na přijímající stranu, vyberte **Vlastnosti** a pak vyberte kartu **Koncové body**.|Ve službě Azure AD se adresa URL odpovědi konfiguruje na webu Azure Portal ve vlastnostech **jednotného přihlašování** aplikace jako Adresa URL odpovědi.</br></br>(Pro zobrazení adresy URL odpovědi možná budete muset vybrat možnost **Zobrazit pokročilé nastavení URL**.)|Mapuje se na element **Destination** v tokenu SAML.</br></br> Příklad hodnoty: https://contoso.my.salesforce.com|
|Adresa URL pro odhlášení z aplikace|Adresa URL, na kterou se po odhlášení uživatele z aplikace odešlou požadavky na odhlášení a vyčištění, které zajistí odhlášení ze všech dalších aplikací, ke kterým zprostředkovatel identity uživatele přihlásil.|Nachází se ve správě služby AD FS v části **Vztahy důvěryhodnosti přijímající strany**. Klikněte pravým tlačítkem na přijímající stranu, vyberte **Vlastnosti** a pak vyberte kartu **Koncové body**.|Není k dispozici. Azure AD nepodporuje jednotné odhlašování, tedy odhlášení ze všech aplikací. Jednoduše odhlásí uživatele ze samotné služby Azure AD.|neuvedeno|
|Identifikátor aplikace|Identifikátor aplikace z pohledu zprostředkovatele identity. Jako identifikátor se často používá přihlašovací adresa URL (ale ne vždy).</br></br> V některých případech se v aplikaci označuje jako ID entity.|Ve službě AD FS je to ID přijímající strany. Klikněte pravým tlačítkem na vztah důvěryhodnosti přijímající strany, vyberte **Vlastnosti** a pak vyberte kartu **Identifikátory**.|Ve službě Azure AD se identifikátor konfiguruje na webu Azure Portal ve vlastnostech **jednotného přihlašování** aplikace jako identifikátor v části **Domény a adresy URL**. (Možná budete muset zaškrtnout políčko **Zobrazit pokročilé nastavení URL**.)|Odpovídá elementu **Audience** v tokenu SAML.|
|Federační metadata aplikace|Umístění federačních metadat aplikace. Zprostředkovatel identity je používá k automatické aktualizaci konkrétních nastavení konfigurace, jako jsou koncové body nebo šifrovací certifikáty.|Adresa URL federačních metadat aplikace se nachází ve vztahu důvěryhodnosti přijímající strany AD FS aplikace. Klikněte pravým tlačítkem na vztah důvěryhodnosti, vyberte **Vlastnosti** a pak vyberte kartu **Monitorování**.|Není k dispozici. Azure AD nepodporuje přímé používání federačních metadat aplikace.|neuvedeno|
|Identifikátor uživatele / **NameID**|Atribut sloužící k jedinečné identifikaci identity uživatele ze služby Azure AD nebo AD FS ve vaší aplikaci.</br></br> Tento atribut je obvykle hlavní název uživatele (UPN) nebo e-mailová adresa uživatele.|Ve službě AD FS se nachází v podobě pravidla deklarace identity na přijímající straně. Ve většině případů toto pravidlo deklarace identity vystavuje deklaraci identity s typem končícím na „nameidentifier“.|Ve službě Azure AD se identifikátor uživatele nachází na webu Azure Portal ve vlastnostech **jednotného přihlašování** aplikace pod nadpisem **Atributy uživatele**.</br></br>Ve výchozím nastavení se použije hlavní název uživatele (UPN).|Předává se do aplikace ze zprostředkovatele identity jako element **NameID** v tokenu SAML.|
|Další deklarace identity, které se mají do aplikace odeslat|Kromě identifikátoru uživatele nebo **NameID** se do aplikace obvykle ze zprostředkovatele identity odesílají i další informace o deklaraci identity. Mezi příklady patří jméno, příjmení, e-mailová adresa a skupiny, kterých je uživatel členem.|Ve službě AD FS se nachází v podobě dalších pravidel deklarace identity na přijímající straně.|Ve službě Azure AD se nachází na webu Azure Portal ve vlastnostech **jednotného přihlašování** aplikace pod nadpisem **Atributy uživatele**. Vyberte **Zobrazit** a upravte všechny ostatní atributy uživatele.|neuvedeno|  

### <a name="representing-azure-ad-as-an-identity-provider-in-an-saas-app"></a>Reprezentace služby Azure AD jako zprostředkovatele identity v aplikaci SaaS
V rámci migrace je nutné nakonfigurovat aplikaci tak, aby odkazovala na službu Azure AD (místo místního zprostředkovatele identity). Tato část se zaměřuje na aplikace SaaS používající protokol SAML, a ne na vlastní obchodní aplikace. Tyto koncepty se však vztahují i na vlastní obchodní aplikace. 

Směrování aplikace SaaS do služby Azure AD zajišťuje několik základních klíčových věcí:
- Vystavitel zprostředkovatele identity: https&#58;//sts.windows.net/{ID_tenanta}/
- Přihlašovací adresa URL zprostředkovatele identity: https&#58;//login.microsoftonline.com/{ID_tenanta}/saml2
- Odhlašovací adresa URL zprostředkovatele identity: https&#58;//login.microsoftonline.com/{ID_tenanta}/saml2 
- Umístění federačních metadat: https&#58;//login.windows.net/{ID_tenanta} <ID_tenanta>/federationmetadata/2007-06/federationmetadata.xml?appid={ID_aplikace} 

Nahraďte {ID_tenanta} za ID vašeho tenanta, které najdete na webu Azure Portal v části **Azure Active Directory** > **Vlastnosti** jako **ID adresáře**. Nahraďte {ID_aplikace} za ID vaší aplikace, které najdete ve vlastnostech aplikace jako **ID aplikace**.

Následující tabulka obsahuje popis klíčových elementů konfigurace zprostředkovatele identity pro konfiguraci nastavení jednotného přihlašování v aplikaci a jejich hodnoty nebo umístění v rámci služeb AD FS a Azure AD. Referenčním rámcem tabulky je aplikace SaaS, která potřebuje vědět, kam má odesílat požadavky na ověřování a jak ověřovat přijaté tokeny.

|Element konfigurace|Popis|AD FS|Azure AD|
|---|---|---|---|
|Federační </br>adresa URL </br>zprostředkovatele identity|Přihlašovací adresa URL zprostředkovatele identity z pohledu aplikace (kam se uživatel přesměruje kvůli přihlášení).|Přihlašovací adresa URL služby AD FS je název federační služby AD FS, za kterým následuje /adfs/ls/. Například: https&#58;//fs.contoso.com/adfs/ls/|Odpovídající hodnota pro službu Azure AD má následující formát, kde se {ID_tenanta} nahradí za ID vašeho tenanta. Nachází se na webu Azure Portal v části **Azure Active Directory** > **Vlastnosti** jako **ID adresáře**.</br></br>Aplikace používající protokol SAML-P: https&#58;//login.microsoftonline.com/{ID_tenanta}/saml2 </br></br>Aplikace používající protokol WS-Federation: https&#58;//login.microsoftonline.com/{ID_tenanta}/wsfed|
|Federační </br>adresa URL </br>zprostředkovatele identity|Odhlašovací adresa URL zprostředkovatele identity z pohledu aplikace (kam se uživatel přesměruje, když se rozhodne odhlásit z aplikace).|Pro službu AD FS je odhlašovací adresa URL buď stejná jako přihlašovací adresa URL, nebo stejná adresa URL, ke které je připojeno wa=wsignout1.0. Například: https&#58;//fs.contoso.com/adfs/ls/?wa=wsignout1.0|Odpovídající hodnota pro službu Azure AD závisí na tom, jestli aplikace podporuje odhlašování přes protokol SAML 2.0.</br></br>Pokud aplikace podporuje odhlašování přes protokol SAML, hodnota má následující formát, kde se hodnota {ID_tenanta} nahradí za ID tenanta. Nachází se na webu Azure Portal v části **Azure Active Directory** > **Vlastnosti** jako **ID adresáře**: https&#58;//login.microsoftonline.com/{ID_tenanta}/saml2</br></br>Pokud aplikace nepodporuje odhlašování přes protokol SAML: https&#58;//login.microsoftonline.com/common/wsfederation?wa=wsignout1.0|
|Podpisový </br>certifikát </br>certifikát|Certifikát, jehož privátní klíč používá zprostředkovatel identity k podepisování vydaných tokenů. Ověřuje, že token pochází ze stejného zprostředkovatele identity, kterému aplikace podle konfigurace důvěřuje.|Podpisový certifikát tokenu AD FS se nachází ve správě služby AD FS v části **Certifikáty**.|Ve službě Azure AD se podpisový certifikát tokenu nachází na webu Azure Portal ve vlastnostech **jednotného přihlašování** aplikace pod nadpisem **Podpisový certifikát SAML**. Tam můžete certifikát stáhnout, abyste ho mohli nahrát do aplikace.</br></br> Pokud má aplikace více než jeden certifikát, nacházejí se všechny certifikáty v souboru XML s federačními metadaty.|
|Identifikátor /</br>vystavitel|Identifikátor zprostředkovatele identity z pohledu aplikace (někdy se označuje jako ID vystavitele).</br></br>V tokenu SAML se hodnota zobrazí jako element **Issuer**.|Identifikátor pro službu AD FS je obvykle Identifikátor federační služby ve správě služby AD FS v části **Služba** > **Upravit vlastnosti služby FS (Federation Service)**. Příklad: http&#58;//fs.contoso.com/adfs/services/trust|Odpovídající hodnota pro službu Azure AD má následující formát, kde se hodnota {ID_tenanta} nahradí za ID tenanta. Nachází se na webu Azure Portal v části **Azure Active Directory** > **Vlastnosti** jako **ID adresáře**: https&#58;//sts.windows.net/{ID_tenanta}/|
|Federační </br>metadata </br>zprostředkovatele identity|Umístění veřejně dostupných federačních metadat zprostředkovatele identity. (Některé aplikace používají federační metadata jako alternativu ke konfiguraci adres URL, identifikátoru a podpisového certifikátu tokenu správcem.)|Adresa URL federačních metadat AD FS najdete ve správě služby AD FS v části **služby** > **koncové body** > **metadat**  >   **Typ: Federační Metadata**. Například: https&#58;//fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml|Odpovídající hodnota pro službu Azure AD má následující formát: https&#58;//login.microsoftonline.com/{název_domény_tenanta}/FederationMetadata/2007-06/FederationMetadata.xml. Hodnota {název_domény_tenanta} se nahradí názvem vašeho tenanta ve formátu contoso.onmicrosoft.com. </br></br>Další informace najdete v tématu [Federační metadata](../develop/azure-ad-federation-metadata.md).

## <a name="moving-saas-apps"></a>Přesunutí aplikace SaaS
Přesunutí aplikací SaaS ze služby AD FS nebo jiného zprostředkovatele identity do služby Azure AD provádí ručně ještě dnes. Pokyny pro konkrétní aplikaci najdete v [seznamu kurzů integrace aplikací SaaS z Marketplace](../saas-apps/tutorial-list.md).

Kurz integrace předpokládá, že provádíte integraci tzv. na zelené louce. Během plánování, vyhodnocování, konfigurace a přímé migrace aplikací byste měli znát několik klíčových konceptů specifických pro migraci:  
- Migrace některých aplikací je snadná. Aplikace se složitějšími požadavky, jako jsou vlastní deklarace identity, můžou vyžadovat další konfiguraci ve službě Azure AD nebo Azure AD Connect.
- V nejběžnějších scénářích aplikace vyžaduje pouze deklaraci identity **NameID** a další běžné deklarace identity s identifikátorem uživatele. Pokud chcete zjistit, jestli se nevyžadují nějaké další deklarace identity, zkontrolujte, jaké deklarace identity vydáváte ze služby AD FS nebo zprostředkovatele identity třetí strany.
- Pokud zjistíte, že se vyžadují další deklarace identity, ujistěte se, že jsou k dispozici ve službě Azure AD. Zkontrolujte konfiguraci synchronizace Azure AD Connect a ujistěte se, že se požadovaný atribut, například **samAccountName**, synchronizuje do služby Azure AD.
- Po zpřístupnění atributů ve službě Azure AD můžete do služby Azure AD přidat pravidla vystavování deklarací identity, aby se tyto atributy zahrnuly jako deklarace identity do vydávaných tokenů. Tato pravidla přidáte ve vlastnostech **jednotného přihlašování** aplikace ve službě Azure AD.

### <a name="assess-what-can-be-moved"></a>Vyhodnotit, co je možné přesunout
Aplikace SAML 2.0 je možné integrovat se službou Azure AD buď prostřednictvím galerie aplikací Azure AD na Marketplace, nebo jako aplikace mimo Marketplace.  

Některé konfigurace ve službě Azure AD vyžadují další kroky a některé konfigurace se v současné době nepodporují. Pokud chcete zjistit, co je možné přesunout, prohlédněte si aktuální konfiguraci jednotlivých aplikací. Hledejte především následující položky:
- Nakonfigurovaná pravidla deklarací identity (pravidla transformace vystavení).
- Formát a atribut elementu SAML **NameID**.
- Vydané verze tokenu SAML.
- Další konfigurace, jako jsou pravidla autorizace vystavení nebo zásady řízení přístupu a pravidla vícefaktorového ověřování (další ověřování).

#### <a name="what-can-be-moved-today"></a>Co je možné přesunout ještě dnes
Aplikace, které můžete snadno dnes přesunout patří aplikace SAML 2.0 používající standardní sadu elementů konfigurace a deklarací identity. Tyto aplikace můžou zahrnovat:
- Hlavní název uživatele (UPN)
- E-mailovou adresu
- Jméno
- Příjmení
- Alternativní atribut jako SAML **NameID**, včetně atributu Azure AD mail, předpony atributu mail, ID zaměstnance, atributů rozšíření 1–15 nebo místního atributu **SamAccountName**. Další informace najdete v tématu [Úprava deklarace identity NameIdentifier](../develop/active-directory-saml-claims-customization.md).
- Vlastní deklarace identity. Informace o podporovaných mapováních deklarací najdete v tématech [Mapování deklarací v Azure Active Directory](../develop/active-directory-claims-mapping.md) a [Přizpůsobování deklarací identity vystavených v tokenu SAML pro podnikové aplikace v Azure Active Directory](../develop/active-directory-saml-claims-customization.md).

Kromě vlastních deklarací identity a elementů **NameID** jsou konfigurace, které v rámci migrace vyžadují další kroky konfigurace ve službě Azure AD, následující:
- Pravidla vlastní autorizace nebo vícefaktorového ověřování ve službě AD FS. Můžete je nakonfigurovat pomocí funkce [Podmíněný přístup služby Azure AD](../active-directory-conditional-access-azure-portal.md).
- Aplikace s několika koncovými body SAML. Ve službě Azure AD je můžete nakonfigurovat pomocí PowerShellu. (Tato možnost není dostupná na portálu.)
- Aplikace WS-Federation, jako jsou aplikace SharePointu, které vyžadují tokeny SAML verze 1.1. Ty je nutné nakonfigurovat ručně pomocí PowerShellu.

#### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplikace a konfigurace, které služba Azure AD v současné době nepodporuje
V současné době není možné migrovat aplikace, které vyžadují následující funkce. Pokud máte aplikace, které tyto funkce vyžadují, poskytněte nám zpětnou vazbu v části Komentáře.
- Možnosti protokolu:
    - Podpora jednotného odhlašování (SLO) SAML ze všech přihlášených aplikací.
    - Podpora vzoru WS-Trust ActAs.
    - Překlad artefaktů SAML.
    - Ověřování podpisů u podepsaných požadavků SAML. Všimněte si, že se přijímají podepsané požadavky, ale podpis se neověřuje.
- Možnosti tokenu: 
    - Šifrování tokenu SAML. 
    - Tokeny SAML verze 1.1 v rámci odpovědí protokolu SAML. 
- Možnosti deklarací identity v rámci tokenů:
    - Vystavování názvů místních skupin jako deklarací identity.
    - Deklarace identity z jiných úložišť než služba Azure AD.
    - Komplexní pravidla transformace vystavování deklarací identity. Informace o podporovaných mapováních deklarací najdete v tématech [Mapování deklarací v Azure Active Directory](../develop/active-directory-claims-mapping.md) a [Přizpůsobování deklarací identity vystavených v tokenu SAML pro podnikové aplikace v Azure Active Directory](../develop/active-directory-saml-claims-customization.md).
    - Vystavování rozšíření adresáře jako deklarací identity.
    - Vlastní specifikace formátu **NameID**.
    - Vystavování atributů s více hodnotami.

>[!NOTE]
>Azure AD se neustále vyvíjí a v této oblasti přidává další možnosti. Tento článek pravidelně aktualizujeme. 

### <a name="configure-azure-ad"></a>Konfigurace služby Azure AD    
#### <a name="configure-single-sign-on-sso-settings-for-the-saas-app"></a>Konfigurace nastavení jednotného přihlašování pro aplikaci SaaS

Ve službě Azure AD můžete přihlašování SAML (podle potřeb vaší aplikace) nakonfigurovat ve vlastnostech **jednotného přihlašování** aplikace v části **Atributy uživatele**.

![Vlastnosti jednotného přihlašování s částí Atributy uživatele](media/migrate-adfs-apps-to-azure/migrate3.png)

Vyberte **Zobrazit a upravit všechny ostatní atributy uživatele** a zobrazte atributy, které se mají odeslat jako deklarace identity v tokenu zabezpečení.

![Seznam atributů](media/migrate-adfs-apps-to-azure/migrate4.png)

Výběrem řádku konkrétního atributu můžete atribut upravit nebo můžete vybrat **Přidat atribut** a přidat nový atribut.

![Podokno Upravit atribut](media/migrate-adfs-apps-to-azure/migrate5.png)

#### <a name="assign-users-to-the-app"></a>Přiřazení uživatelů k aplikaci
Aby se vaši uživatelé ve službě Azure AD mohli přihlašovat k aplikaci SaaS, musíte jim poskytnout přístup.

Pokud chcete přiřadit uživatele na portálu Azure AD, přejděte na stránku příslušné aplikace SaaS a na bočním panelu vyberte **Uživatelé a skupiny**. Uživatele nebo skupinu přidáte tak, že v horní části podokna vyberete **Přidat uživatele**. 

![Tlačítko Přidat uživatele v části Uživatelé a skupiny](media/migrate-adfs-apps-to-azure/migrate6.png) 

![Podokno Přidat přiřazení](media/migrate-adfs-apps-to-azure/migrate7.png)

Přístup ověříte tak, že by se uživatelům měla daná aplikace SaaS zobrazit po přihlášení na jejich [přístupovém panelu](../user-help/active-directory-saas-access-panel-introduction.md). Přístupový portál se nachází na adrese https://myapps.microsoft.com. V tomto příkladu byl uživateli úspěšně přidělený přístup k Salesforce i ServiceNow.

![Příklad přístupového panelu s aplikacemi Salesforce a ServiceNow](media/migrate-adfs-apps-to-azure/migrate8.png)

### <a name="configure-the-saas-app"></a>Konfigurace aplikace SaaS
Proces přechodu od místní federace na službu Azure AD závisí na tom, jestli aplikace SaaS, se kterou pracujete, podporuje více zprostředkovatelů identity. Tady je několik běžných dotazů týkajících se podpory více zprostředkovatelů identity:

   **Otázka: Co znamená pro aplikace podporuje více zprostředkovatelů identity?**
    
   Odpověď: Aplikace SaaS, které podporují více zprostředkovatelů identity, umožňují zadat veškeré informace o novém zprostředkovateli identity (v našem případě Azure AD) před změnou prostředí pro přihlašování. Po dokončení konfigurace můžete přepnout konfiguraci ověřování aplikace, aby odkazovala na službu Azure AD.

   **Otázka: Proč je důležité, jestli aplikace SaaS podporuje více zprostředkovatelů identity?**

   Odpověď: Pokud se nepodporuje více zprostředkovatelů identity, musí správce vyhradit krátké časové okno výpadků služby a údržbu během konfigurace služby Azure AD jako nového zprostředkovatele identity aplikace. Během takového výpadku by uživatelé měli obdržet upozornění na nemožnost přihlášení ke svým účtům.

   Pokud aplikace podporuje více zprostředkovatelů identity, je možné další zprostředkovatele identity nakonfigurovat předem. Správce pak může zprostředkovatele identity přepnout v Azure.

   Pokud aplikace podporuje více zprostředkovatelů identity a zvolíte více zprostředkovatelů identity, kteří budou současně zpracovávat ověřování přihlašování, uživateli se na přihlašovací stránce zobrazí možnost výběru zprostředkovatele identity, který provede ověření.

#### <a name="example-support-for-multiple-idps"></a>Příklad: Podpora více zprostředkovatelů identity
Například v Salesforce se konfigurace zprostředkovatele identity nachází v části **Nastavení** > **Nastavení společnosti** > **Moje doména** > **Konfigurace ověřování**.

![Část Konfigurace ověřování v aplikaci Salesforce](media/migrate-adfs-apps-to-azure/migrate9.png)

Vzhledem k tomu, že jste dříve vytvořili konfiguraci v části **Identita** > **Nastavení jednotného přihlašování**, měli byste být schopni pro konfiguraci ověřování změnit svého zprostředkovatele identity. Můžete ho například změnit z AD FS na Azure AD. 

![Výběr služby Azure AD jako ověřovací služby](media/migrate-adfs-apps-to-azure/migrate10.png)

### <a name="optional-configure-user-provisioning-in-azure-ad"></a>Volitelné: Konfigurace zřizování uživatelů ve službě Azure AD
Pokud chcete, aby zřizování uživatelů pro danou aplikaci SaaS zpracovávala přímo služba Azure AD, přečtěte si téma [Automatizace zřizování a rušení zřízení uživatelů pro aplikace SaaS pomocí Azure Active Directory](user-provisioning.md).

## <a name="next-steps"></a>Další postup

- [Správa aplikací pomocí Azure Active Directory](what-is-application-management.md)
- [Správa přístupu k aplikacím](what-is-access-management.md)
- [Federace služby Azure AD Connect](../hybrid/how-to-connect-fed-whatis.md)
