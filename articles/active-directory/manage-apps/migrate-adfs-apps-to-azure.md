---
title: Přesunutí ověřování aplikace z AD FS na Azure Active Directory
description: Tento článek je určený k tomu, aby pomáhal organizacím pochopit, jak přesouvat aplikace do Azure Active Directory se zaměřením na federované aplikace SaaS.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: how-to
ms.workload: identity
ms.date: 02/10/2021
ms.author: kenwith
ms.reviewer: baselden
ms.openlocfilehash: b8ad137389ac11a41872190122eae6405a126b5b
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100381235"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Přesun ověřování aplikací z Active Directory Federation Services (AD FS) do Azure Active Directory

[Azure Active Directory (Azure AD)](../fundamentals/active-directory-whatis.md) nabízí univerzální platformu pro identitu, která poskytuje vašim lidem, partnerům a zákazníkům jedinou identitu pro přístup k aplikacím a spolupráci z libovolné platformy a zařízení. Azure AD má [plnou sadu funkcí pro správu identit](../fundamentals/active-directory-whatis.md). Standardizace ověřování aplikací (App) a autorizace do Azure AD umožní výhodám, které tyto funkce poskytují.

> [!TIP]
> Tento článek je napsán pro vývojáře vývojářů. Vedoucí projektu a správci, kteří plánují přesun aplikace do služby Azure AD, by měli zvážit přečtení našeho článku [migrace ověřování aplikace do služby Azure AD](migrate-application-authentication-to-azure-active-directory.md) .

## <a name="introduction"></a>Úvod

Pokud máte místní adresář, který obsahuje uživatelské účty, pravděpodobně máte mnoho aplikací, pro které se uživatelé ověřují. Každá z těchto aplikací je nakonfigurovaná pro uživatele, kteří budou mít přístup pomocí svých identit.


Uživatelé se také můžou ověřit přímo v místní službě Active Directory. Active Directory Federation Services (AD FS) (AD FS) jsou standardy založené na místních službách identity. AD FS rozšiřuje schopnost používat funkci jednotného přihlašování (SSO) mezi důvěryhodnými obchodními partnery, aniž by se uživatelé museli přihlašovat samostatně pro každou aplikaci. Tato skutečnost se označuje jako federace.

Mnoho organizací má SaaS (software jako služba) nebo vlastní obchodní aplikace (LOB), které jsou federované přímo AD FS, společně s Microsoft 365 a aplikacemi založenými na službě Azure AD.

![Aplikace připojené přímo v místním prostředí](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**Aby bylo možné zvýšit zabezpečení aplikací, vaším cílem je mít v místních i cloudových prostředích jednu sadu řízení přístupu a zásad**.

![Aplikace připojené přes Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Typy aplikací, které se mají migrovat

Migrace všech ověřování aplikací do služby Azure AD je optimální, protože poskytuje jedinou řídicí plochu pro správu identit a přístupu.

Vaše aplikace můžou k ověřování používat moderní nebo starší protokoly. Zvažte nejprve migraci aplikací, které používají moderní ověřovací protokoly (například SAML a Open ID Connect). Tyto aplikace je možné překonfigurovat pro ověřování pomocí Azure AD prostřednictvím integrovaného konektoru v naší galerii aplikací nebo registrací aplikace v Azure AD. Aplikace používající starší protokoly lze integrovat pomocí [aplikačního proxy serveru](./what-is-application-proxy.md).

Další informace najdete v tématu [typy aplikací, které je možné integrovat se službou Azure AD](./what-is-application-management.md)?

Můžete použít [sestavu aktivita aplikace AD FS k migraci aplikací do služby Azure AD](./migrate-adfs-application-activity.md) , pokud máte [povolený Azure AD Connect Health](../hybrid/how-to-connect-health-adfs.md).

### <a name="the-migration-process"></a>Proces migrace

Během procesu přesunutí ověřování aplikace do služby Azure AD můžete odpovídajícím způsobem testovat aplikace a konfiguraci. Doporučujeme, abyste nadále používali existující testovací prostředí pro testování migrace, které se přesouvá do provozního prostředí. Pokud testovací prostředí není aktuálně k dispozici, můžete ho nastavit pomocí [Azure App Service](https://azure.microsoft.com/services/app-service/) nebo [Azure Virtual Machines](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)v závislosti na architektuře aplikace.

Můžete se rozhodnout nastavit samostatného testovacího tenanta Azure AD, který se použije při vývoji konfigurací aplikace.

Proces migrace může vypadat takto:

**Fáze 1 – aktuální stav: ověřování produkční aplikace pomocí AD FS**

![Fáze 1 migrace ](media/migrate-adfs-apps-to-azure/stage1.jpg)


**Fáze 2 – volitelné: testovací instance aplikace ukazující na testování tenanta Azure**

Aktualizujte konfiguraci tak, aby odkazovala na testovací instanci aplikace na testovacího tenanta Azure AD, a proveďte potřebné změny. Aplikaci lze testovat pomocí uživatelů v testovacím tenantovi služby Azure AD. Během procesu vývoje můžete pomocí nástrojů, jako je [Fiddler](https://www.telerik.com/fiddler) , porovnat a ověřit požadavky a odpovědi.

Pokud není možné nastavit samostatného testovacího tenanta, přeskočte tuto fázi a vytvořte testovací instanci aplikace a najeďte ji na klienta služby Azure AD, jak je popsáno v části fáze 3.

![Fáze 2 migrace ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Fáze 3 – testovací aplikace odkazující na produkčního tenanta Azure**

Aktualizujte konfiguraci tak, aby ukazovala vaši testovací instanci aplikace na provozní instanci Azure. Nyní můžete testovat s uživateli ve vaší produkční instanci. V případě potřeby si přečtěte část tohoto článku o přechodu uživatelů.

![Fáze 3 migrace ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Fáze 4 – produkční aplikace odkazující na produkčního tenanta AD**

Aktualizujte konfiguraci své produkční aplikace tak, aby odkazovala na produkčního tenanta Azure.

![Fáze 4 migrace ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Aplikace, které se ověřují pomocí AD FS, můžou pro oprávnění použít skupiny služby Active Directory. Než začnete s migrací, synchronizujte data identity mezi místním prostředím a službou Azure AD pomocí [Azure AD Connect synchronizace](../hybrid/how-to-connect-sync-whatis.md) . Před migrací ověřte tyto skupiny a členství, abyste mohli při migraci aplikace udělit přístup stejným uživatelům.

### <a name="line-of-business-lob-apps"></a>Obchodní aplikace (LOB)

Obchodní aplikace jsou vyvíjené interně ve vaší organizaci nebo dostupné jako standardní zabalený produkt, který je nainstalovaný v datovém centru. Mezi příklady patří aplikace založené na Windows Identity Foundation a aplikacích SharePoint (ne SharePoint Online).

Obchodní aplikace, které používají OAuth 2,0, OpenID Connect nebo WS-Federation, se dají integrovat s Azure AD jako [Registrace aplikací](../develop/quickstart-register-app.md). Integrujte vlastní aplikace, které používají SAML 2,0 nebo WS-Federation jako [aplikace mimo galerii](./add-application-portal.md) na stránce podnikové aplikace v [Azure Portal](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>Jednotné přihlašování založené na SAML

Aplikace, které používají SAML 2,0 pro ověřování, lze nakonfigurovat pro [jednotné přihlašování založené na SAML](./what-is-single-sign-on.md) (SSO-based SSO). Pomocí [jednotného přihlašování založeného na SAML](./what-is-single-sign-on.md)můžete mapovat uživatele na konkrétní aplikační role na základě pravidel definovaných v deklaracích SAML.

Konfigurace aplikace SaaS pro jednotné přihlašování založené na SAML najdete v tématu [Konfigurace jednotného přihlašování založené na SAML](./view-applications-portal.md).

![Snímky obrazovky uživatele SAML jednotného přihlašování ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


Mnoho aplikací SaaS má [kurz specifický pro aplikaci](../saas-apps/tutorial-list.md) , který vás provede konfigurací jednotného přihlašování založeného na SAML.

![kurz aplikace](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Migrace některých aplikací je snadná. Aplikace se složitějšími požadavky, jako jsou vlastní deklarace identity, můžou vyžadovat další konfiguraci ve službě Azure AD nebo Azure AD Connect. Informace o podporovaných mapováních deklarací identity najdete [v tématu Mapování deklarací identity v Azure Active Directory](../develop/active-directory-claims-mapping.md).

Při mapování atributů Pamatujte na následující omezení:

* Ne všechny atributy, které se dají vystavovat v AD FS, se ve službě Azure AD zobrazí jako atributy pro generování tokenů SAML, a to i v případě, že se tyto atributy synchronizují. Při úpravách atributu se v rozevíracím seznamu hodnota zobrazí různé atributy, které jsou k dispozici ve službě Azure AD. Zkontrolujte konfiguraci [Azure AD Connect synchronizace](../hybrid/how-to-connect-sync-whatis.md) , aby se zajistilo, že se požadovaný atribut, například samAccountName--, synchronizuje do služby Azure AD. Atributy rozšíření můžete použít k vygenerování jakékoli deklarace identity, která není součástí standardního schématu uživatele ve službě Azure AD.

* V nejběžnějších scénářích aplikace vyžaduje pouze deklaraci identity NameID a další běžné deklarace identity s identifikátorem uživatele. Pokud chcete zjistit, jestli se vyžadují nějaké další deklarace identity, Projděte si informace o deklaracích, které vystavujete z AD FS.

* Ne všechny deklarace identity můžou být problémy, protože některé deklarace identity jsou chráněné v Azure AD.

* Možnost používat šifrované tokeny SAML je teď ve verzi Preview. Viz [Postupy: přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](../develop/active-directory-saml-claims-customization.md).



### <a name="software-as-a-service-saas-apps"></a>Aplikace SaaS (software jako služba)

Pokud se uživatel přihlásí k SaaS aplikacím, jako je Salesforce, ServiceNow nebo Workday, a integruje se s AD FS, používáte federované přihlašování pro aplikace SaaS.

Ve službě Azure AD už je možné nakonfigurovat většinu aplikací SaaS. Microsoft má mnoho předkonfigurovaných připojení k aplikacím SaaS v  [galerii aplikací Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), což usnadňuje váš přechod. Aplikace SAML 2,0 je možné integrovat se službou Azure AD prostřednictvím Galerie aplikací Azure AD nebo jako [aplikace mimo galerii](./add-application-portal.md).

Podobným způsobem je možné integrovat se službou Azure AD aplikace, které používají OAuth 2.0 nebo OpenID Connect, jako [registrace aplikací](../develop/quickstart-register-app.md). Aplikace, které používají starší protokoly, můžou použít [azure proxy aplikací služby AD](./application-proxy.md) k ověřování pomocí Azure AD.

Pokud máte problémy s připojováním aplikací SaaS, můžete se obrátit na [alias podpory aplikace SaaS Integration support](mailto:SaaSApplicationIntegrations@service.microsoft.com).

**Podpisové certifikáty SAML pro jednotné přihlašování**: podpisové certifikáty jsou důležitou součástí jakéhokoli nasazení jednotného přihlašování. Azure AD vytvoří podpisové certifikáty pro vytvoření federovaného jednotného přihlašování založeného na SAML pro aplikace SaaS. Jakmile přidáte buď galerii nebo jiné aplikace, nakonfigurujete přidanou aplikaci pomocí možnosti federované jednotné přihlašování. Viz [Správa certifikátů pro federované jednotné přihlašování v Azure Active Directory](./manage-certificates-for-federated-single-sign-on.md).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Aplikace a konfigurace, které se dají přesunout dnes

Mezi aplikace, které se dají snadno přesunout, patří aplikace SAML 2,0, které používají standardní sadu elementů konfigurace a deklarace identity:

* Hlavní název uživatele

* E-mailová adresa

* Křestní jméno

* příjmení

* Alternativní atribut jako SAML **NameID**, včetně atributu Azure AD mail, předpony atributu mail, ID zaměstnance, atributů rozšíření 1–15 nebo místního atributu **SamAccountName**. Další informace najdete v tématu [Úprava deklarace identity NameIdentifier](../develop/active-directory-saml-claims-customization.md).

* Vlastní deklarace identity.

Následující postup vyžaduje další kroky konfigurace pro migraci do služby Azure AD:

* Pravidla pro vlastní autorizaci nebo Multi-Factor Authentication (MFA) v AD FS. Nakonfigurujete je pomocí funkce [podmíněného přístupu Azure AD](../conditional-access/overview.md) .

* Aplikace s více koncovými body adresy URL odpovědi Můžete je nakonfigurovat ve službě Azure AD pomocí prostředí PowerShell nebo rozhraní Azure Portal.

* Aplikace WS-Federation, jako jsou aplikace SharePointu, které vyžadují tokeny SAML verze 1.1. Můžete je ručně nakonfigurovat pomocí PowerShellu. Z galerie můžete také přidat předem integrovanou obecnou šablonu pro aplikace SharePoint a SAML 1,1. Podporujeme protokol SAML 2,0.

* Složitá vystavování deklarací identity transformací. Informace o podporovaných mapováních deklarací identity najdete v tématech:
   *  [Mapování deklarací v Azure Active Directory](../develop/active-directory-claims-mapping.md)
   * [Přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace v Azure Active Directory](../develop/active-directory-saml-claims-customization.md)



### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplikace a konfigurace, které služba Azure AD v současné době nepodporuje

Aplikace, které vyžadují následující možnosti, se nedají migrovat ještě dnes.

**Možnosti protokolu**

* Podpora pro model WS-Trust ActAs

* Překlad artefaktů SAML

* Ověřování podpisů u podepsaných požadavků SAML Všimněte si, že jsou přijímány podepsané požadavky, ale podpis není ověřen.
Vzhledem k tom, že služba Azure AD vrátí token do koncových bodů, které jsou v aplikaci předem nakonfigurované, ověřování podpisů ve většině případů pravděpodobně není vyžadováno.

**Deklarace identity ve schopnostech tokenů**

* Deklarace identity z jiných úložišť atributů než do adresáře služby Azure AD, pokud tato data nejsou synchronizovaná v Azure AD. Další informace najdete v tématu [Přehled rozhraní API pro synchronizaci Azure AD](/graph/api/resources/synchronization-overview?view=graph-rest-beta).

* Vystavování atributů s více hodnotami adresáře. V tuto chvíli nemůžete například vystavovat deklaraci deklarace identity pro proxy adresy.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Mapování nastavení aplikace z AD FS do služby Azure AD

Migrace začíná vyhodnocením konfigurace aplikace v místním prostředí a namapováním této konfigurace na službu Azure AD. AD FS a Azure AD fungují podobně, takže se v obou případech použijí i koncepty konfigurace vztahu důvěryhodnosti, přihlašování a adres URL a identifikátorů. Zdokumentujte AD FS nastavení konfigurace vašich aplikací, abyste je mohli snadno nakonfigurovat ve službě Azure AD.

### <a name="map-app-configuration-settings"></a>Mapování nastavení konfigurace aplikace

Následující tabulka popisuje některé nejběžnější mapování nastavení mezi AD FS vztahu důvěryhodnosti předávající strany na podnikovou aplikaci Azure AD:

* AD FS – vyhledejte nastavení v AD FS vztah důvěryhodnosti předávající strany pro aplikaci. Klikněte pravým tlačítkem na předávající stranu a vyberte vlastnosti.

* Azure AD – nastavení se konfiguruje v rámci [Azure Portal](https://portal.azure.com/) ve vlastnostech jednotného přihlašování každé aplikace.

| Nastavení konfigurace| AD FS| Jak konfigurovat v Azure AD| Token SAML |
| - | - | - | - |
| **Přihlašovací adresa URL aplikace** <p>Adresa URL pro uživatele, který se má přihlašovat k aplikaci v toku SAML iniciované poskytovatelem služeb (SP).| –| Otevře základní konfiguraci SAML z přihlašování založené na SAML.| – |
| **Adresa URL odpovědi aplikace** <p>Adresa URL aplikace z perspektivy poskytovatele identity (IdP). IdP odesílá uživatele a token, když se uživatel přihlásí k IdP.  Toto je také známé jako **koncový bod příjemce kontrolního výrazu SAML**.| Vyberte kartu **koncové body** .| Otevře základní konfiguraci SAML z přihlašování založené na SAML.| Cílový element v tokenu SAML. Příklad hodnoty: `https://contoso.my.salesforce.com` |
| **Adresa URL pro odhlášení z aplikace** <p>Toto je adresa URL, na kterou se odesílají žádosti o vyčištění odhlášení, když se uživatel z aplikace odhlásí. IdP odešle požadavek na odhlášení uživatele ze všech ostatních aplikací.| Vyberte kartu **koncové body** .| Otevře základní konfiguraci SAML z přihlašování založené na SAML.| – |
| **Identifikátor aplikace** <p>Toto je identifikátor aplikace z perspektivy IdP. Hodnota adresy URL pro přihlášení se často používá pro identifikátor (ale ne vždycky).  Někdy aplikace volá toto ID entity.| Vyberte kartu **identifikátory** .|Otevře základní konfiguraci SAML z přihlašování založené na SAML.| Provede mapování na element **cílové skupiny** v tokenu SAML. |
| **Federační metadata aplikace** <p>Toto je umístění federačních metadat aplikace. Zprostředkovatel identity je používá k automatické aktualizaci konkrétních nastavení konfigurace, jako jsou koncové body nebo šifrovací certifikáty.| Vyberte kartu **monitorování** .| Není k dispozici. Azure AD nepodporuje přímé využívání federačních metadat aplikace. Federační metadata můžete ručně importovat.| – |
| **Identifikátor uživatele/ID názvu** <p>Atribut sloužící k jedinečné identifikaci identity uživatele ze služby Azure AD nebo AD FS ve vaší aplikaci.  Tento atribut je obvykle buď hlavní název uživatele, nebo e-mailová adresa uživatele.| Pravidla deklarací identity. Ve většině případů pravidlo deklarace identity vydá deklaraci identity s typem, který končí na NameIdentifier.| Identifikátor najdete pod nadpisem **atributy uživatele a deklarace identity**. Ve výchozím nastavení se používá hlavní název uživatele (UPN).| Provede mapování na element **NameId** v tokenu SAML. |
| **Další deklarace identity** <p>Příklady dalších informací o deklaraci identity, které se obvykle odesílají z IdP do aplikace, zahrnují křestní jméno, příjmení, e-mailovou adresu a členství ve skupinách.| Ve službě AD FS se nachází v podobě dalších pravidel deklarace identity na přijímající straně.| Identifikátor můžete najít pod nadpisy **atributy uživatele & deklarace identity**. Vyberte **Zobrazit** a upravte všechny ostatní atributy uživatele.| – |


### <a name="map-identity-provider-idp-settings"></a>Nastavení IdP (map identity Provider)

Nakonfigurujte své aplikace tak, aby odkazovaly na Azure AD oproti AD FS pro jednotné přihlašování. Tady se zaměřujeme na aplikace SaaS, které používají protokol SAML. Tento koncept ale rozšiřuje i na vlastní obchodní aplikace.

> [!NOTE]
> Hodnoty konfigurace pro Azure AD se řídí vzorem, ve kterém vaše ID tenanta Azure nahrazuje {tenant-ID} a ID aplikace nahrazuje {ID aplikace}. Tyto informace najdete v [Azure Portal](https://portal.azure.com/) v části Azure Active Directory > vlastnosti:

* ID vašeho tenanta zobrazíte tak, že vyberete ID adresáře.

* ID aplikace zobrazíte tak, že vyberete ID aplikace.

 Na vysoké úrovni namapujte následující konfigurační prvky aplikace Key SaaS do služby Azure AD.



| Prvek| Konfigurační hodnota |
| - | - |
| Vystavitel zprostředkovatele identity| https: \/ /STS.Windows.NET/{tenant-ID}/ |
| Přihlašovací adresa URL zprostředkovatele identity| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Adresa URL pro odhlášení zprostředkovatele identity| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Umístění federačních metadat| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>Mapování nastavení jednotného přihlašování pro aplikace SaaS

Aplikace SaaS musí znát, kam se mají odesílat žádosti o ověření a jak ověřit přijaté tokeny. Následující tabulka popisuje prvky pro konfiguraci nastavení jednotného přihlašování v aplikaci a jejich hodnoty nebo umístění v rámci AD FS a Azure AD.

| Nastavení konfigurace| AD FS| Jak konfigurovat v Azure AD |
| - | - | - |
| **Přihlašovací adresa URL pro IdP** <p>Přihlašovací adresa URL IdP z pohledu aplikace (kam se uživatel přesměruje pro přihlášení).| Přihlašovací adresa URL AD FS je název služby AD FS Federation Service následovaný "/adfs/ls/.". <p>Příklad: `https://fs.contoso.com/adfs/ls/`| Nahraďte {tenant-ID} vaším ID tenanta. <p> Pro aplikace, které používají protokol SAML-P: [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Pro aplikace, které používají protokol WS-Federation: [https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **Adresa URL pro odhlášení IdP**<p>Odhlašovací adresa URL IdP z pohledu aplikace (kde se uživatel přesměruje, když se rozhodne odhlásit z aplikace).| Adresa URL pro odhlášení je buď shodná s přihlašovací adresou URL, nebo stejnou adresou URL s připojenou "WA = wsignout 1.0". Příklad: `https://fs.contoso.com/adfs/ls/?wa=wsignout1.0`| Nahraďte {tenant-ID} vaším ID tenanta.<p>Pro aplikace, které používají protokol SAML-P:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Pro aplikace, které používají protokol WS-Federation: [https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Podpisový certifikát tokenu**<p>IdP používá k podepsání vydaných tokenů privátní klíč certifikátu. Ověřuje, že token pochází ze stejného zprostředkovatele identity, kterému aplikace podle konfigurace důvěřuje.| Podpisový certifikát tokenu AD FS se nachází ve správě služby AD FS v části **Certifikáty**.| Najdete ho v Azure Portal ve **vlastnostech jednotného přihlašování** aplikace pod hlavičkou **podpisového certifikátu SAML**. Tam můžete certifikát stáhnout, abyste ho mohli nahrát do aplikace.  <p>Pokud má aplikace více než jeden certifikát, můžete najít všechny certifikáty v souboru XML s federačními metadaty. |
| **Identifikátor/Vystavitel**<p>Identifikátor IdP z pohledu aplikace (někdy se označuje jako "ID vystavitele").<p>V tokenu SAML se hodnota zobrazí jako element Issuer.| Identifikátor pro AD FS je obvykle identifikátor federační služby ve správě AD FS v části **služba > upravit služba FS (Federation Service) vlastnosti**. Příklad: `http://fs.contoso.com/adfs/services/trust`| Nahraďte {tenant-ID} vaším ID tenanta.<p>https: \/ /STS.Windows.NET/{tenant-ID}/ |
| **IdP federačních metadat**<p>Umístění veřejně dostupných federačních metadat IdP. (Některé aplikace používají federační metadata jako alternativu ke konfiguraci adres URL, identifikátoru a podpisového certifikátu tokenu správcem.)| Ve správě AD FS v části **> koncových bodů služby AD FS metadata federačních metadat > metadata > typ: federační metadata**. Příklad: `https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml`| Odpovídající hodnota pro Azure AD se řídí vzorem [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml) . Nahraďte {TenantDomainName} názvem vašeho tenanta ve formátu "contoso.onmicrosoft.com".   <p>Další informace najdete v tématu [Federační metadata](../azuread-dev/azure-ad-federation-metadata.md). |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Představuje AD FS zásady zabezpečení ve službě Azure AD.

Při přesunu ověřování aplikace do služby Azure AD vytvořte mapování z existujících zásad zabezpečení na jejich ekvivalentní nebo alternativní varianty dostupné v Azure AD. Zajistěte, aby se tato mapování mohla provádět, zatímco bezpečnostní standardy, které vyžaduje vaši vlastníci vaší aplikace, budou významně jednodušší migrace.

V případě každého typu pravidla a jeho příkladů doporučujeme, aby toto pravidlo vypadalo v AD FS, AD FS ekvivalent kódu jazyka a způsobu, jakým je tato mapa ve službě Azure AD.

### <a name="map-authorization-rules"></a>Mapování autorizačních pravidel

Následují příklady typů autorizačních pravidel v AD FS a způsob jejich namapování na Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>Příklad 1: povolení přístupu všem uživatelům

Povolit přístup všem uživatelům vypadá jako v AD FS:

![Snímek obrazovky s dialogovým oknem nastavit jeden Sign-On se zobrazí v dialogovém okně SAML.](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


To se mapuje na Azure AD jedním z následujících způsobů:

V [Azure Portal](https://portal.azure.com/):
* Možnost 1: nastavte přiřazení uživatele nutné na ne. ![úprava zásad řízení přístupu pro aplikace SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Všimněte si, že nastavení přepnutí uživatele na hodnotu Ano vyžaduje, aby se uživatelé k aplikaci přiřadili za účelem získání přístupu. Pokud je nastavena na ne, všichni uživatelé mají přístup. Tento přepínač neurčuje, co se zobrazuje pro uživatele v prostředí moje aplikace.


* Možnost 2: na kartě Uživatelé a skupiny přiřaďte aplikaci k automatické skupině Všichni uživatelé. <p>
V tenantovi Azure AD musíte [Povolit dynamické skupiny](../enterprise-users/groups-create-rule.md) , aby byla dostupná výchozí skupina všichni uživatelé.

   ![Moje aplikace SaaS ve službě Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Příklad 2: povolení skupiny explicitně

Explicitní autorizace skupiny v AD FS:


![Snímek obrazovky se zobrazí dialogové okno Upravit pravidlo pro pravidlo deklarace identity Domain Admins.](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


Toto pravidlo se mapuje na Azure AD:

V [Azure Portal](https://portal.azure.com/)nejprve [vytvoříte skupinu uživatelů](../fundamentals/active-directory-groups-create-azure-portal.md) , která odpovídá skupině uživatelů ze AD FS, a potom k této skupině přiřadíte oprávnění aplikace:

![Přidat přiřazení ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Příklad 3: autorizace konkrétního uživatele

Explicitní autorizace uživatele v AD FS:

![Snímek obrazovky se zobrazí dialogové okno Upravit pravidlo pro pravidlo deklarace identity konkrétního uživatele s typem příchozí deklarace identity Primary S I D.](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Toto pravidlo se mapuje na Azure AD:

V [Azure Portal](https://portal.azure.com/)přidejte uživatele do aplikace pomocí karty přidat přiřazení v aplikaci, jak je znázorněno níže:

![Moje aplikace SaaS v Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)


### <a name="map-multi-factor-authentication-rules"></a>Mapování Multi-Factor Authentication pravidel

Místní nasazení [Multi-Factor Authentication (MFA)](../authentication/concept-mfa-howitworks.md) a AD FS bude po migraci stále fungovat, protože jste federované AD FS. Zvažte však migraci na integrované možnosti vícefaktorového ověřování Azure, které jsou svázané s pracovními postupy podmíněného přístupu služby Azure AD.

Následují příklady typů pravidel MFA v AD FS a způsob jejich mapování na Azure AD na základě různých podmínek:

Nastavení pravidla vícefaktorového ověřování v AD FS:

![Snímek obrazovky ukazuje podmínky pro Azure A D v Azure Portal.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Příklad 1: vymáhání MFA na základě uživatelů nebo skupin

Selektor uživatel/skupiny je pravidlo, které umožňuje vymáhat MFA pro jednotlivé skupiny (SID skupin) nebo pro jednotlivé uživatele (primární SID). Kromě přiřazení uživatelů nebo skupin jsou všechna další zaškrtávací políčka v AD FS funkci uživatelského rozhraní konfigurace MFA jako další pravidla, která se vyhodnocují po vyhodnocování pravidla uživatele/skupiny.


Zadejte pravidla MFA pro uživatele nebo skupinu ve službě Azure AD:

1. Vytvořte [nové zásady podmíněného přístupu](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

2. Vyberte **přiřazení**. Přidejte uživatele nebo skupiny, pro které chcete vymáhat MFA.

3. Nakonfigurujte možnosti **řízení přístupu** , jak je znázorněno níže:

![Snímek obrazovky se zobrazí v podokně udělení, kde můžete udělit přístup.](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)


 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Příklad 2: vysazení MFA pro neregistrovaná zařízení

Zadejte pravidla MFA pro neregistrovaná zařízení ve službě Azure AD:

1. Vytvořte [nové zásady podmíněného přístupu](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

2. Nastavte **přiřazení** pro **všechny uživatele**.

3. Nakonfigurujte možnosti **řízení přístupu** , jak je znázorněno níže:

![Snímek obrazovky se zobrazí v podokně udělení, kde můžete udělit přístup a zadat další omezení.](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)


Když nastavíte možnost pro více ovládacích prvků tak, aby vyžadovala jeden z vybraných ovládacích prvků, znamená to, že pokud uživatel splní některou z podmínek zadaných v zaškrtávacím políčku, bude jim udělen přístup k vaší aplikaci.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Příklad 3: vysazení MFA na základě umístění

Zadejte pravidla MFA na základě umístění uživatele v Azure AD:

1. Vytvořte [nové zásady podmíněného přístupu](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json).

1. Nastavte **přiřazení** pro **všechny uživatele**.

1. [Nakonfigurujte pojmenovaná umístění ve službě Azure AD](../reports-monitoring/quickstart-configure-named-locations.md) . v opačném případě je důvěryhodná z vaší podnikové sítě.

1. Nakonfigurujte **pravidla podmínek** tak, aby určovala umístění, pro která byste chtěli vymáhat MFA.

![Snímek obrazovky zobrazuje podokno umístění pro pravidla podmínek.](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Nakonfigurujte možnosti **řízení přístupu** , jak je znázorněno níže:


![Mapování zásad řízení přístupu](media/migrate-adfs-apps-to-azure/mfa-location-2.png)


### <a name="map-emit-attributes-as-claims-rule"></a>Mapovat atributy generování jako deklarace

Tady je příklad, jak jsou atributy mapovány v AD FS:

![Snímek obrazovky se zobrazí dialogové okno Upravit pravidlo pro generování atributů jako deklarací identity.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


Toto pravidlo se mapuje na Azure AD:

V [Azure Portal](https://portal.azure.com/)vyberte **podnikové aplikace**, **jednotné přihlašování** a přidejte **atributy tokenu SAML** , jak je znázorněno níže:

![Snímek obrazovky zobrazuje stránku jednotného přihlašování pro vaši podnikovou aplikaci.](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Mapování vestavěných zásad řízení přístupu

AD FS 2016 obsahuje několik předdefinovaných zásad řízení přístupu, ze kterých si můžete vybrat:

![Integrované řízení přístupu k Azure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)


K implementaci integrovaných zásad v Azure AD můžete použít [nové zásady podmíněného přístupu](../authentication/tutorial-enable-azure-mfa.md?bc=%2fazure%2factive-directory%2fconditional-access%2fbreadcrumb%2ftoc.json&toc=%2fazure%2factive-directory%2fconditional-access%2ftoc.json) a nakonfigurovat řízení přístupu, nebo můžete použít vlastní návrháře zásad v AD FS 2016 ke konfiguraci zásad řízení přístupu. Editor pravidel má vyčerpávající seznam povolených a s výjimkou možností, které vám můžou usnadnit vytváření všech druhů permutací.

![Zásady řízení přístupu Azure AD](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



V této tabulce uvádíme několik užitečných povolení a s výjimkou možností a způsobu jejich mapování na službu Azure AD.


| Možnost | Jak nakonfigurovat možnost povolit ve službě Azure AD?| Jak nakonfigurovat s výjimkou možnosti ve službě Azure AD? |
| - | - | - |
| Z určité sítě| Mapuje se k [pojmenovanému umístění](../reports-monitoring/quickstart-configure-named-locations.md) ve službě Azure AD.| Použití možnosti **vyloučit** pro [důvěryhodná umístění](../conditional-access/location-condition.md) |
| Z konkrétních skupin| [Nastavení přiřazení uživatelů nebo skupin](./assign-user-or-group-access-portal.md)| Použití možnosti **vyloučit** v uživatelích a skupinách |
| Ze zařízení s určitou úrovní důvěryhodnosti| Tuto možnost nastavte v ovládacím prvku "stav zařízení" v části přiřazení – > podmínky.| Použijte možnost **vyloučit** v části stav zařízení podmínka a zahrnout **všechna zařízení** . |
| Se specifickými deklaracemi v žádosti| Toto nastavení nejde migrovat.| Toto nastavení nejde migrovat. |


Příklad konfigurace možnosti vyloučit pro důvěryhodná umístění v Azure Portal:

![Snímek obrazovky s mapováním zásad řízení přístupu](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Přechod uživatelů z AD FS do služby Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Synchronizace skupin AD FS ve službě Azure AD

Když namapujete autorizační pravidla, aplikace, které ověřují pomocí AD FS, můžou pro oprávnění použít skupiny služby Active Directory. V takovém případě použijte [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) k synchronizaci těchto skupin s Azure AD před migrací aplikací. Ujistěte se, že jste před migrací ověřili tyto skupiny a členství, abyste mohli při migraci aplikace udělit přístup stejným uživatelům.

Další informace najdete v tématu [předpoklady pro používání atributů skupin synchronizovaných ze služby Active Directory](../hybrid/how-to-connect-fed-group-claims.md).

### <a name="setup-user-self-provisioning"></a>Nastavení samoobslužného zřizování uživatele

Některé aplikace SaaS podporují možnost samoobslužně zřizovat uživatele při prvním přihlášení k aplikaci. V Azure Active Directory (Azure AD) pojem zřizování aplikací označuje automatické vytváření identit uživatelů a rolí v cloudových aplikacích ([SaaS](https://azure.microsoft.com/overview/what-is-saas/)), ke kterým uživatelé potřebují přístup. Přenesené uživatele již budou mít v aplikaci SaaS účet. Všichni noví uživatelé přidaní po migraci budou muset zřídit. Otestujte [zřizování aplikace SaaS](../app-provisioning/user-provisioning.md) , jakmile se aplikace migruje.

### <a name="sync-external-users-in-azure-ad"></a>Synchronizace externích uživatelů ve službě Azure AD

Stávající externí uživatelé se můžou v AD FS nastavit dvěma hlavními způsoby:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Externí uživatelé s místním účtem v rámci vaší organizace

Tyto účty budete moct dál používat stejným způsobem, jakým vaše interní uživatelské účty fungují. Tyto externí uživatelské účty mají ve vaší organizaci hlavní název, i když e-mailová adresa účtu může ukazovat externě. Při práci s migrací můžete využít výhody, které [Azure AD B2B](../external-identities/what-is-b2b.md) nabízí, a to migrací těchto uživatelů na používání vlastní podnikové identity, pokud je taková identita dostupná. Tím se zjednodušuje proces přihlášení pro tyto uživatele, protože jsou často přihlášeni pomocí vlastního podnikového přihlášení. Správa vaší organizace bude nesníží také, protože už nebudete muset spravovat účty externích uživatelů.

#### <a name="federated-external-identities"></a>Federované externí identity

Pokud aktuálně federování s externí organizací, máte k dispozici několik přístupů:

* [Přidat uživatele Azure Active Directory spolupráce B2B do Azure Portal](../external-identities/add-users-administrator.md) Můžete aktivně odesílat pozvánky B2B pro spolupráci z portálu pro správu Azure AD do partnerské organizace pro jednotlivé členy, abyste mohli dál používat aplikace a prostředky, ke kterým se používají.

* [Vytvořte pracovní postup pro samoobslužné registraci B2B](../external-identities/self-service-portal.md) , který vygeneruje žádost pro jednotlivé uživatele v partnerské organizaci pomocí rozhraní API pozvánky B2B.

Bez ohledu na to, jak se nakonfigurují stávající externí uživatelé, můžou mít pravděpodobně oprávnění, která jsou přidružená ke svému účtu, a to buď v členství ve skupině, nebo v konkrétních oprávněních. Vyhodnoťte, jestli se tato oprávnění musí migrovat nebo vyčistit. Účty v organizaci, které reprezentují externího uživatele, musí být po migraci uživatele na externí identitu zakázané. Proces migrace by se měl projednávat s vašimi obchodními partnery, protože může dojít k přerušení připojení k vašim prostředkům.

## <a name="migrate-and-test-your-apps"></a>Migrace a testování aplikací

Postup migrace proveďte podrobně v tomto článku.

Pak přejdete na [Azure Portal](https://aad.portal.azure.com/) a otestujete, jestli byla migrace úspěšná. Postupujte podle následujících pokynů:
1. Vyberte **podnikové aplikace**  >  **všechny aplikace** a v seznamu najděte svoji aplikaci.

1. Vyberte **Spravovat**  >  **uživatele a skupiny** a přiřaďte aplikaci aspoň jednoho uživatele nebo skupinu.

1. Vyberte **Spravovat**  >  **podmíněný přístup**. Zkontrolujte seznam zásad a ujistěte se, že neblokujete přístup k aplikaci pomocí [zásad podmíněného přístupu](../conditional-access/overview.md).

V závislosti na konfiguraci aplikace ověřte, že jednotné přihlašování funguje správně.

| Typ ověřování| Testování |
| - | - |
| OAuth/OpenID Connect| Vyberte **podnikové aplikace > oprávnění** a ujistěte se, že jste souhlasili s tím, že jste aplikaci ve vaší organizaci použili v nastavení uživatele pro vaši aplikaci.
‎ |
| Jednotné přihlašování založené na SAML| Použijte tlačítko [Test nastavení SAML](./debug-saml-sso-issues.md) , které se nachází v části **jednotné přihlašování**.
‎ |
| Password-Based JEDNOTNÉHO PŘIHLAŠOVÁNÍ| Stáhněte a nainstalujte si rozšíření pro [zabezpečené přihlašování](../user-help/my-apps-portal-end-user-access.md) [-](../user-help/my-apps-portal-end-user-access.md) [aplikace](../user-help/my-apps-portal-end-user-access.md)MyApp. Toto rozšíření vám pomůže začít používat cloudové aplikace vaší organizace, které vyžadují použití procesu jednotného přihlašování.
‎ |
| Proxy aplikací| Ujistěte se, že váš konektor je spuštěný a přiřazený k vaší aplikaci. Další pomoc najdete v [Průvodci odstraňováním potíží s proxy aplikací](./application-proxy-troubleshoot.md) .
‎ |

> [!NOTE]
> Soubory cookie ze starého prostředí AD FS budou v počítačích uživatele pořád trvalé. Tyto soubory cookie můžou způsobit problémy s migrací, protože uživatelé můžou směrovat na staré AD FS přihlašovací prostředí a nové přihlášení Azure AD. Možná bude nutné vymazat soubory cookie prohlížeče uživatelů ručně nebo pomocí skriptu. Můžete také použít System Center Configuration Manager nebo podobnou platformu.

### <a name="troubleshoot"></a>Řešení potíží

Pokud dojde k chybám z testu migrovaných aplikací, může být prvním krokem odstraňování potíží, než se vrátí stávajícím předávajícím stranám AD FS. Přečtěte si téma [Postup ladění jednotného přihlašování založeného na SAML pro aplikace v Azure Active Directory](./debug-saml-sso-issues.md).

### <a name="rollback-migration"></a>Vrácení migrace zpět

Pokud se migrace nezdařila, doporučujeme ponechat stávající předávající strany na serverech AD FS a odebrat přístup k předávající straně. V případě potřeby v průběhu nasazení to umožní rychlou zálohu.

### <a name="end-user-communication"></a>Komunikace koncového uživatele

I když může být v samotném časovém intervalu pro plánované výpadky minimální, měli byste i nadále plánovat komunikaci těchto časových období s zaměstnanci a zároveň přitom vyjímat z AD FS do Azure AD. Ujistěte se, že máte v prostředí aplikace tlačítko pro odeslání názoru, nebo ukazatele na helpdesk, kde jsou problémy.

Po dokončení nasazení můžete odeslat komunikaci s uživateli úspěšného nasazení a připomenout jim všechny nové kroky, které musí provést.

* Dejte uživatelům pokyn, aby k přístupu ke všem migrovaných aplikacím používali [Moje aplikace](https://myapps.microsoft.com) .

* Připomenout uživatelům, kteří můžou potřebovat aktualizovat nastavení MFA.

* Pokud se nasadí Self-Service resetování hesla, uživatelé budou muset aktualizovat nebo ověřit své metody ověřování. [Seznamte se s](https://aka.ms/mfatemplates) [SSPRmi](https://aka.ms/ssprtemplates) komunikačními šablonami pro koncové uživatele.

Komunikace s externími uživateli: Tato skupina uživatelů je většinou v případě problémů postižena. To platí hlavně v případě, že váš stav zabezpečení nadiktují pro externí partnery jinou sadu pravidel podmíněného přístupu nebo profilů rizik. Zajistěte, aby si externí partneři dozvěděli o plánu migrace do cloudu a měli časový rámec, během kterého se jim doporučuje účastnit pilotního nasazení, které testuje všechny toky jedinečné pro externí spolupráci. Nakonec se ujistěte, že mají způsob, jak získat přístup k helpdesku v případě narušení problému.

## <a name="next-steps"></a>Další kroky

Přečtěte si téma [migrace ověřování aplikace do služby Azure AD](https://aka.ms/migrateapps/whitepaper) .<p>
Nastavení [podmíněného přístupu](../conditional-access/overview.md) a [vícefaktorového ověřování](../authentication/concept-mfa-howitworks.md)

Vyzkoušejte si vzorový kód pro krok:[AD FS k Azure AD Application Migration PlayBook pro vývojáře](https://aka.ms/adfsplaybook)
