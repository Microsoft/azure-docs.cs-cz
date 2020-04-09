---
title: Přesunutí ověřování aplikací ze služby AD FS do služby Azure Active Directory
description: Tento článek je určen k pomoci organizacím pochopit, jak přesunout aplikace do Azure AD, se zaměřením na federované aplikace SaaS.
services: active-directory
author: barbaraselden
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: conceptual
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.date: 04/01/2020
ms.author: baselden
ms.collection: M365-identity-device-management
ms.openlocfilehash: bcb39606cdbb6488ccdee2828029d3617d689508
ms.sourcegitcommit: df8b2c04ae4fc466b9875c7a2520da14beace222
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2020
ms.locfileid: "80891914"
---
# <a name="moving-application-authentication-from-active-directory-federation-services-to-azure-active-directory"></a>Přesunutí ověřování aplikací ze služby AD FS do služby Azure Active Directory

[Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis) nabízí univerzální platformu identit, která poskytuje vašim lidem, partnerům a zákazníkům jednu identitu pro přístup k aplikacím a spolupráci z libovolné platformy a zařízení. Azure AD má [úplnou sadu funkcí správy identit](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-whatis). Standardizace ověřování aplikace (aplikace) a autorizace na Azure AD umožňuje výhody, které tyto funkce poskytují. 

> [!NOTE]
> Tento článek se zaměřuje na přesunutí ověřování aplikací z místní služby Active Directory a služby AD Federita do služby Azure AD. Přehled plánování tohoto tahu najdete v dokumentu white paper [Migrace ověřování aplikací do služby Azure AD.](https://aka.ms/migrateapps/whitepaper) Bílá kniha popisuje, jak naplánovat migraci, testování a přehledy.

## <a name="introduction"></a>Úvod

Pokud máte místní adresář, který obsahuje uživatelské účty, pravděpodobně máte mnoho aplikací, na které se uživatelé ověřují. Každá z těchto aplikací je nakonfigurovánpro uživatele přístup pomocí jejich identity. 


Uživatelé se také mohou ověřovat přímo v místním adresáři Active Directory. Služba AD FS (Active Directory Federation Services) je místní služba identit založená na standardech. AD FS rozšiřuje možnost používat funkce jednotného přihlašování (SSO) mezi důvěryhodnými obchodními partnery, aniž by uživatelé museli přihlašovat samostatně ke každé aplikaci. Tomu se říká Federace.

Mnoho organizací má software jako službu (SaaS) nebo vlastní line-of-business (LOB) aplikace federované přímo do AD FS, vedle Office 365 a Azure AD-založené aplikace. 

![Aplikace připojené přímo v místním prostředí](media/migrate-adfs-apps-to-azure/app-integration-before-migration1.png)

**Chcete-li zvýšit zabezpečení aplikací, vaším cílem je mít jednu sadu řízení přístupu a zásad v celém místním a cloudovém prostředí**. 

![Aplikace připojené prostřednictvím Azure AD](media/migrate-adfs-apps-to-azure/app-integration-after-migration1.png)



## <a name="types-of-apps-to-migrate"></a>Typy aplikací k migraci

Migrace všech vašich aplikací ověřování do Azure AD je optimální, protože poskytuje jednu rovinu ovládacího prvku pro správu identit a přístupu.

Vaše aplikace mohou pro ověřování používat moderní nebo starší protokoly. Zvažte první migraci aplikací, které používají moderní ověřovací protokoly (například SAML a Open ID Connect). Tyto aplikace lze překonfigurovat tak, aby se ověřovaly pomocí Služby Azure AD prostřednictvím integrovaného konektoru v naší Galerii aplikací, nebo registrací aplikace ve službě Azure AD. Aplikace používající starší protokoly lze integrovat pomocí [proxy aplikace](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-proxy). 

Další informace najdete [v tématu Jaké typy aplikací lze integrovat s Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-application-management)?

[Sestavu aktivity aplikace služby AD FS](https://docs.microsoft.com/azure/active-directory/manage-apps/migrate-adfs-application-activity) můžete použít k migraci aplikací do Azure AD, pokud máte [povoleno u Azure AD Connect Health](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-health-adfs). 

### <a name="the-migration-process"></a>Proces migrace

Během procesu přesouvání ověřování aplikací do Azure AD adekvátně otestujte aplikace a konfiguraci. Doporučujeme, abyste nadále používali existující testovací prostředí pro testování migrace, které se přesouvá do produkčního prostředí. Pokud testovací prostředí není momentálně k dispozici, můžete ho nastavit pomocí [služby Azure App Service](https://azure.microsoft.com/services/app-service/) nebo [Virtuální počítače Azure](https://azure.microsoft.com/free/virtual-machines/search/?OCID=AID2000128_SEM_lHAVAxZC&MarinID=lHAVAxZC_79233574796345_azure%20virtual%20machines_be_c__1267736956991399_kwd-79233582895903%3Aloc-190&lnkd=Bing_Azure_Brand&msclkid=df6ac75ba7b612854c4299397f6ab5b0&ef_id=XmAptQAAAJXRb3S4%3A20200306231230%3As&dclid=CjkKEQiAhojzBRDg5ZfomsvdiaABEiQABCU7XjfdCUtsl-Abe1RAtAT35kOyI5YKzpxRD6eJS2NM97zw_wcB)v závislosti na architektuře aplikace.

Můžete nastavit samostatný test klienta Azure AD pro použití při vývoji konfigurace aplikací. 

Proces migrace může vypadat takto:

**Fáze 1 – Aktuální stav: Ověřování produkční aplikace pomocí ad FS**

![Fáze migrace 1 ](media/migrate-adfs-apps-to-azure/stage1.jpg)

 
**Fáze 2 – VOLITELNÉ: Testovací instance aplikace ukazující na test klienta Azure**

Aktualizujte konfiguraci tak, aby vaše testovací instance aplikace byla převedena na testovacího klienta Azure AD, a proveďte požadované změny. Aplikaci můžete testovat s uživateli v testovacím tenantovi Azure AD. Během procesu vývoje můžete pomocí nástrojů, jako je [Fiddler](https://www.telerik.com/fiddler) porovnat a ověřit požadavky a odpovědi.

Pokud nastavení samostatného testovacího klienta není možné, přeskočte tuto fázi a postavte se testovací instanci aplikace a najeďte ji na produkčního klienta Azure AD, jak je popsáno ve fázi 3 níže.

![Fáze migrace 2 ](media/migrate-adfs-apps-to-azure/stage2.jpg)

**Fáze 3 – Testovací aplikace ukazující na produkčního tenanta Azure**

Aktualizujte konfiguraci tak, aby vaše testovací instance aplikace byla na produkční instanci Azure. Nyní můžete testovat s uživateli v produkční instanci. V případě potřeby zkontrolujte část tohoto článku o přechodu uživatelů.

![Fáze migrace 3 ](media/migrate-adfs-apps-to-azure/stage3.jpg)

**Fáze 4 – Produkční aplikace ukazující na produkčního klienta AD**

Aktualizujte konfiguraci produkční aplikace tak, aby ukazovala na produkčního tenanta Azure.

![Fáze migrace 1 ](media/migrate-adfs-apps-to-azure/stage4.jpg)

 Aplikace, které se ověřují pomocí služby AD FS, mohou pro oprávnění používat skupiny služby Active Directory. Synchronizace [Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) slouží k synchronizaci dat identit mezi místním prostředím a službou Azure AD před zahájením migrace. Před migrací ověřte tyto skupiny a členství, abyste mohli udělit přístup stejným uživatelům při migraci aplikace.

### <a name="line-of-business-lob-apps"></a>Obchodní aplikace (LOB)

Obchodní aplikace jsou vyvíjeny interně vaší organizací nebo jsou k dispozici jako standardní balený produkt, který je nainstalován ve vašem datovém centru. Mezi příklady patří aplikace postavené na Platformě Windows Identity Foundation a sharepointových aplikacích (ne sharepointové online). 

Obchodní aplikace, které používají OAuth 2.0, OpenID Connect nebo WS-Federation, lze integrovat s Azure AD jako [registrace aplikací](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users). Integrujte vlastní aplikace, které používají SAML 2.0 nebo WS-Federation jako [aplikace bez galerie,](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app) na stránce podnikových aplikací na [webu Azure Portal](https://portal.azure.com/).

## <a name="saml-based-single-sign-on"></a>Jednotné přihlašování založené na SAML

Aplikace, které používají SAML 2.0 pro ověřování lze nakonfigurovat pro [jednotné přihlašování založené na SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on) (jednotné přihlašování založené na SAML). Pomocí [samolo založené hospodařící sazbuje](https://docs.microsoft.com/azure/active-directory/manage-apps/what-is-single-sign-on), můžete mapovat uživatele na konkrétní role aplikace na základě pravidel, která definujete v deklaracích SAML. 

Pokud chcete nakonfigurovat aplikaci SaaS pro jednotné přihlašování založené na saml, přečtěte si informace [o konfiguraci jednotného přihlašování založeného na SAML](https://docs.microsoft.com/azure/active-directory/manage-apps/configure-single-sign-on-non-gallery-applications). 

![Snímky obrazovky uživatele SSO SAML ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)

 
Mnoho aplikací SaaS mají [kurz specifický pro aplikaci,](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list) který vás provede konfigurací jednotného přihlašování založenéna SAML.

![kurz aplikace](media/migrate-adfs-apps-to-azure/app-tutorial.png)

Migrace některých aplikací je snadná. Aplikace se složitějšími požadavky, jako jsou vlastní deklarace identity, můžou vyžadovat další konfiguraci ve službě Azure AD nebo Azure AD Connect. Informace o podporovaných mapováních deklarací naleznete [v tématu Mapování deklarací ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping).

Při mapování atributů mějte na paměti následující omezení:

* Ne všechny atributy, které mohou být vydány ve službě AD FS se zobrazí ve službě Azure AD jako atributy pro vyzařují tokeny SAML, i v případě, že tyto atributy jsou synchronizovány. Při úpravě atributu, rozbalovací seznam hodnota zobrazí různé atributy, které jsou k dispozici ve službě Azure AD. Zkontrolujte konfiguraci [synchronizace Azure AD Connect](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-sync-whatis) a ujistěte se, že se do Azure AD synchronizuje požadovaný atribut – například samAccountName--. Atributy rozšíření můžete použít k vyzařování jakékoli deklarace, která není součástí standardního uživatelského schématu ve službě Azure AD.

* V nejběžnějších scénářích aplikace vyžaduje pouze deklaraci identity NameID a další běžné deklarace identity s identifikátorem uživatele. Chcete-li zjistit, zda jsou požadovány další nároky, zkontrolujte, jaké nároky vydáváte ze společnosti AD FS.

* Ne všechny deklarace identity mohou být problémy, protože některé deklarace jsou chráněné ve službě Azure AD. 

* Možnost používat šifrované tokeny SAML je nyní ve verzi preview. Viz [Postup: přizpůsobení deklarací vydaných v tokenu SAML pro podnikové aplikace](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

 

### <a name="software-as-a-service-saas-apps"></a>Aplikace software jako služby (SaaS)

Pokud se uživatel přihlašuje k aplikacím SaaS, jako jsou Salesforce, ServiceNow nebo Workday, a je integrovaný se službou AD FS, používáte federované přihlašování pro aplikace SaaS. 

Většinu aplikací SaaS už dá nakonfigurovat ve službě Azure AD. Microsoft má mnoho předkonfigurovaných připojení k aplikacím SaaS v [galerii aplikací Azure AD](https://azuremarketplace.microsoft.com/marketplace/apps/category/azure-active-directory-apps), což vám usnadní přechod. Saml 2.0 aplikace lze integrovat s Azure AD prostřednictvím galerie aplikací Azure AD nebo jako [aplikace bez galerie](https://docs.microsoft.com/azure/active-directory/manage-apps/add-non-gallery-app). 

Podobným způsobem je možné integrovat se službou Azure AD aplikace, které používají OAuth 2.0 nebo OpenID Connect, jako [registrace aplikací](https://docs.microsoft.com/azure/active-directory/develop/app-registrations-training-guide-for-app-registrations-legacy-users). Aplikace, které používají starší protokoly, můžou k ověření pomocí Azure AD používat [proxy aplikací Azure AD.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy)

V případě jakýchkoli problémů s připojením aplikací SaaS můžete kontaktovat [alias podpory integrace aplikací SaaS](mailto:SaaSApplicationIntegrations@service.microsoft.com).

**Podpisové certifikáty SAML pro automatické přihlašování**: Podpisové certifikáty jsou důležitou součástí každého nasazení automatického přihlašování. Azure AD vytvoří podpisové certifikáty k vytvoření federovaného přihlašování založeného na SAML pro vaše aplikace SaaS. Jakmile přidáte aplikace galerie nebo jiné galerie, nakonfigurujete přidanou aplikaci pomocí možnosti federovaného přihlašování. Viz [Správa certifikátů pro federované jednotné přihlašování ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/manage-certificates-for-federated-single-sign-on).

### <a name="apps-and-configurations-that-can-be-moved-today"></a>Aplikace a konfigurace, které lze přesunout ještě dnes

Aplikace, které můžete přesunout snadno dnes patří SAML 2.0 aplikace, které používají standardní sadu konfiguračních prvků a deklarací:

* Hlavní název uživatele

* E-mailová adresa

* Křestní jméno

* příjmení

* Alternativní atribut jako SAML **NameID**, včetně atributu Azure AD mail, předpony atributu mail, ID zaměstnance, atributů rozšíření 1–15 nebo místního atributu **SamAccountName**. Další informace najdete v tématu [Úprava deklarace identity NameIdentifier](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization).

* Vlastní deklarace identity.

Následující vyžadují další kroky konfigurace pro migraci do Služby Azure AD:

* Vlastní autorizace nebo vícefaktorové ověřování (MFA) pravidla ve službě AD FS. Nakonfigurujete je pomocí funkce [podmíněného přístupu Azure AD.](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal)

* Aplikace s více koncovými body adresy URL pro odpověď. Nakonfigurujete je ve službě Azure AD pomocí PowerShellu nebo v rozhraní portálu Azure.

* Aplikace WS-Federation, jako jsou aplikace SharePointu, které vyžadují tokeny SAML verze 1.1. Můžete je nakonfigurovat ručně pomocí prostředí PowerShell. Z galerie můžete také přidat předem integrovanou obecnou šablonu pro aplikace SharePoint a SAML 1.1. Podporujeme protokol SAML 2.0.

* Komplexní vystavování deklarací transformuje pravidla. Informace o podporovaných mapováních deklarací naleznete v tématu:
   *  [Mapování deklarací v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-claims-mapping) 
   * [Přizpůsobení deklarací vydaných v tokenu SAML pro podnikové aplikace ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization)

 

### <a name="apps-and-configurations-not-supported-in-azure-ad-today"></a>Aplikace a konfigurace, které služba Azure AD v současné době nepodporuje

Aplikace, které vyžadují následující funkce, nelze dnes migrovat.

**Možnosti protokolu**

* Podpora vzoru WS-Trust ActA

* Překlad artefaktů SAML

* Ověření podpisu podepsaných požadavků SAML  
Všimněte si, že podepsané požadavky jsou přijímány, ale podpis není ověřen.  
Vzhledem k tomu, že Azure AD vrátí token pouze koncové body předkonfigurované v aplikaci, ověření podpisu pravděpodobně není vyžadováno ve většině případů.

**Deklarace identity ve schopnostech tokenu**

* Deklarace z atributu ukládá jiné než adresář Azure AD, pokud tato data se synchronizuje do Azure AD. Další informace naleznete v [přehledu rozhraní API synchronizace Azure AD](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta).

* Vystavování atributů s více hodnotami adresáře. V tuto chvíli například nemůžeme vydat deklaraci s více hodnotami pro proxy adresy.

## <a name="map-app-settings-from-ad-fs-to-azure-ad"></a>Mapování nastavení aplikace ze služby AD FS do Služby Azure AD

Migrace začíná vyhodnocením konfigurace aplikace v místním prostředí a namapováním této konfigurace na službu Azure AD. Služby AD FS a Azure AD fungují podobně, takže v obou případech platí koncepty konfigurace důvěryhodných adres, přihlašovacích a odhlašovacích adres URL a identifikátorů. Dokumentujte nastavení konfigurace služby AD FS vašich aplikací, abyste je mohli snadno konfigurovat ve službě Azure AD.

### <a name="map-app-configuration-settings"></a>Nastavení konfigurace mapové aplikace

Následující tabulka popisuje některé z nejběžnějších mapování nastavení mezi vztahem důvěryhodnosti předávající strany služby AD FS na aplikaci Azure AD Enterprise:

* AD FS – Najděte nastavení ve vztahu důvěryhodnosti předávající strany ad FS pro aplikaci. Klikněte pravým tlačítkem myši na předávající stranu a vyberte Vlastnosti. 

* Azure AD – nastavení se nakonfiguruje na [webu Azure Portal](https://portal.azure.com/) ve vlastnostech jednotného přihlašování každé aplikace.

| Nastavení konfigurace| AD FS| Jak nakonfigurovat ve službě Azure AD| SAML Token |
| - | - | - | - |
| **Přihlašovací adresa URL aplikace** <p>Adresa URL pro uživatele pro přihlášení k aplikaci v toku SAML iniciovaného poskytovatelem služeb (SP).| –| Otevřít základní konfiguraci SAML z přihlašování založeného na saml| – |
| **Adresa URL odpovědi aplikace** <p>Adresa URL aplikace z pohledu poskytovatele identity (IdP). IdP odešle uživatele a token zde poté, co uživatel se přihlásil k IdP.  To se také označuje jako **koncový bod příjemce výrazu SAML**.| Výběr karty **Koncové body**| Otevřít základní konfiguraci SAML z přihlašování založeného na saml| Cílový prvek v tokenu SAML. Příklad hodnoty:[https://contoso.my.salesforce.com](https://contoso.my.salesforce.com/) |
| **Adresa URL pro odhlášení z aplikace** <p>Toto je adresa URL, na kterou jsou odesílány požadavky na vyčištění odhlášení, když se uživatel odhlásí z aplikace. IdP odešle požadavek na odhlášení uživatele ze všech ostatních aplikací.| Výběr karty **Koncové body**| Otevřít základní konfiguraci SAML z přihlašování založeného na saml| – |
| **Identifikátor aplikace** <p>Toto je identifikátor aplikace z pohledu idp. Hodnota přihlašovací adresy URL se často používá pro identifikátor (ale ne vždy).  Někdy aplikace nazývá toto "ID entity."| Výběr karty **Identifikátory**|Otevřít základní konfiguraci SAML z přihlašování založeného na saml| Mapuje na cíl **Cílová skupina** v tokenu SAML. |
| **Federační metadata aplikace** <p>Toto je umístění metadat federace aplikace. Zprostředkovatel identity je používá k automatické aktualizaci konkrétních nastavení konfigurace, jako jsou koncové body nebo šifrovací certifikáty.| Výběr karty **Sledování**| Není k dispozici. Azure AD nepodporuje spotřebovávající metadata federace aplikací přímo. Metadata federace můžete importovat ručně.| – |
| **Identifikátor uživatele/ ID jména** <p>Atribut sloužící k jedinečné identifikaci identity uživatele ze služby Azure AD nebo AD FS ve vaší aplikaci.  Tento atribut je obvykle hlavní název uživatele nebo e-mailová adresa uživatele.| Pravidla reklamace. Ve většině případů pravidlo deklarace pohledávky vydá deklaraci s typem, který končí NameIdentifier.| Identifikátor naleznete v záhlaví **Uživatelské atributy a deklarace identity**. Ve výchozím nastavení se hlavní upn používá| Mapuje na **NameID** prvek v tokenu SAML. |
| **Ostatní pohledávky** <p>Příklady dalších informací o deklaracích, které se běžně odesílají z idp do aplikace, zahrnují jméno, příjmení, e-mailovou adresu a členství ve skupině.| Ve službě AD FS se nachází v podobě dalších pravidel deklarace identity na přijímající straně.| Identifikátor naleznete v záhlaví **Atributy uživatele & deklarace identity**. Vyberte **Zobrazit** a upravte všechny ostatní atributy uživatele.| – |


### <a name="map-identity-provider-idp-settings"></a>Nastavení zprostředkovatele identity (IdP) mapy

Nakonfigurujte své aplikace tak, aby ukazovaly na Azure AD versus AD FS pro službu SSO. Zde se zaměřujeme na aplikace SaaS, které používají protokol SAML. Tento koncept se však vztahuje i na vlastní obchodní aplikace.

> [!NOTE]
> Hodnoty konfigurace pro Azure AD se řídí vzorem, kde vaše ID klienta Azure nahradí {tenant-id} a ID aplikace nahradí {application-id}. Tyto informace najdete na [webu Azure Portal](https://portal.azure.com/) v části Azure Active Directory > vlastnosti: 

* Vyberte ID adresáře, chcete-li zobrazit id klienta. 

* Vyberte ID aplikace, chcete-li zobrazit ID aplikace.

 Na vysoké úrovni namapujte následující klíčové prvky konfigurace aplikací SaaS do Azure AD. 

 

| Element| Konfigurační hodnota |
| - | - |
| Vydavatel zprostředkovatele identity| [https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| Adresa URL přihlášení zprostředkovatele identity| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Adresa URL odhlášení poskytovatele identity| [https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) |
| Umístění metadat federace| [https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}](https://login.windows.net/{tenant-id}/federationmetadata/2007-06/federationmetadata.xml?appid={application-id}) |


### <a name="map-sso-settings-for-saas-apps"></a>Mapování nastavení přisuzování služeb Při používání služeb SaaS

Aplikace SaaS potřebují vědět, kam mají odesílat žádosti o ověření a jak ověřit přijaté tokeny. Následující tabulka popisuje prvky konfigurace nastavení přicertifikačníka v aplikaci a jejich hodnoty nebo umístění ve službě AD FS a Azure AD

| Nastavení konfigurace| AD FS| Jak nakonfigurovat ve službě Azure AD |
| - | - | - |
| **Adresa URL pro přihlášení k idp** <p>Přihlašovací adresa URL idp z pohledu aplikace (kde je uživatel přesměrován pro přihlášení).| Přihlašující adresa URL služby AD FS je název federační služby služby služby AD FS následovaný názvem /adfs/ls/." <p>Například:[https://fs.contoso.com/adfs/ls/](https://fs.contoso.com/adfs/ls/)| Nahraďte {tenant-id} id klienta. <p> Pro aplikace, které používají protokol SAML-P:[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p>Pro aplikace, které používají protokol WS-Federation:[https://login.microsoftonline.com/{tenant-id}/wsfed](https://login.microsoftonline.com/{tenant-id}/wsfed) |
| **Adresa URL pro odhlášení idp**<p>Adresa URL pro odhlášení z pohledu aplikace (kde je uživatel přesměrován, když se rozhodne odhlásit se z aplikace).| Adresa URL pro odhlášení je buď stejná jako adresa URL pro přihlášení, nebo stejná adresa URL s připojenou adresou "wa=wsignout1.0". Například:[https://fs.contoso.com/adfs/ls/?wa=wsignout1.0](https://fs.contoso.com/adfs/ls/?wa=wsignout1.0)| Nahraďte {tenant-id} id klienta.<p>Pro aplikace, které používají protokol SAML-P:<p>[https://login.microsoftonline.com/{tenant-id}/saml2](https://login.microsoftonline.com/{tenant-id}/saml2) <p> Pro aplikace, které používají protokol WS-Federation:[https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0](https://login.microsoftonline.com/common/wsfederation?wa=wsignout1.0) |
| **Podpisový certifikát tokenu**<p>IdP používá soukromý klíč certifikátu k podepsání vydaných tokenů. Ověřuje, že token pochází ze stejného zprostředkovatele identity, kterému aplikace podle konfigurace důvěřuje.| Podpisový certifikát tokenu AD FS se nachází ve správě služby AD FS v části **Certifikáty**.| Najdete ji na webu Azure Portal ve **vlastnostech jednotného přihlašování** aplikace pod hlavičkou **podpisového certifikátu SAML**. Tam můžete certifikát stáhnout, abyste ho mohli nahrát do aplikace.  <p>Pokud má aplikace více než jeden certifikát, můžete najít všechny certifikáty v souboru XML metadat federace. |
| **Identifikátor/ "emitent"**<p>Identifikátor idp z pohledu aplikace (někdy volal "ID vystavittele").<p>V tokenu SAML se hodnota zobrazí jako element Vystavitr.| Identifikátor služby AD FS je obvykle identifikátor federační služby ve správě služby AD FS v části **Správa > úpravy vlastností služby Federation Service**. Například:[http://fs.contoso.com/adfs/services/trust](http://fs.contoso.com/adfs/services/trust)| Nahraďte {tenant-id} id klienta.<p>[https://sts.windows.net/{tenant-id}/](https://sts.windows.net/{tenant-id}/) |
| **Metadata federace IdP**<p>Umístění veřejně dostupných federačních metadat idp. (Některé aplikace používají federační metadata jako alternativu ke konfiguraci adres URL, identifikátoru a podpisového certifikátu tokenu správcem.)| Vyhledejte adresu URL metadat federace služby AD FS ve správě služby AD FS v části **Servisní > koncové body > metadata > Typ: Metadata federace**. Například:[https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml](https://fs.contoso.com/FederationMetadata/2007-06/FederationMetadata.xml)| Odpovídající hodnota pro Azure AD [https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml](https://login.microsoftonline.com/{TenantDomainName}/FederationMetadata/2007-06/FederationMetadata.xml)následuje vzor . Nahraďte {TenantDomainName} názvem svého klienta ve formátu "contoso.onmicrosoft.com".   <p>Další informace najdete v tématu [Federační metadata](https://docs.microsoft.com/azure/active-directory/azuread-dev/azure-ad-federation-metadata). |


## <a name="represent-ad-fs-security-policies-in-azure-ad"></a>Představují zásady zabezpečení služby AD FS ve službě Azure AD

Při přesouvání ověřování aplikací do Azure AD vytvořte mapování od stávajících zásad zabezpečení až po jejich ekvivalentní nebo alternativní varianty dostupné ve službě Azure AD. Zajištění, že tato mapování lze provést při plnění bezpečnostních standardů požadovaných vlastníky aplikací výrazně usnadní zbytek migrace aplikace.

Pro každý typ pravidla a jeho příklady zde doporučujeme, jak pravidlo vypadá ve službě AD FS, kód ekvivalentní jazyk pravidla služby AD FS a jak toto mapování ve službě Azure AD.

### <a name="map-authorization-rules"></a>Pravidla autorizace mapy

Následují příklady typů autorizačních pravidel ve službě AD FS a jak je můžete namapovat na Azure AD:

#### <a name="example-1-permit-access-to-all-users"></a>Příklad 1: Povolit přístup všem uživatelům

Povolit přístup všem uživatelům vypadá ve ad FS: 

![Fáze migrace 1 ](media/migrate-adfs-apps-to-azure/sso-saml-user-attributes-claims.png)


To se mapuje na Azure AD jedním z následujících způsobů:

Na [webu Azure Portal](https://portal.azure.com/):
* Možnost 1: Nastavení přiřazení uživatele požadované na ne ![úprava zásad řízení přístupu pro aplikace SaaS ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-2.png)

    Všimněte si, že nastavení přiřazení uživatele požadované přepnout na Ano vyžaduje, aby uživatelé byli přiřazeni k aplikaci získat přístup. Pokud je nastavena možnost Ne, mají přístup všichni uživatelé. Tento přepínač neřídí, co se uživatelům zobrazuje v prostředí Moje aplikace. 

 
* Možnost 2: Na kartě Uživatelé a skupiny přiřaďte aplikaci automatické skupině Všichni uživatelé. <p>
Je nutné [povolit dynamické skupiny](https://docs.microsoft.com/azure/active-directory/users-groups-roles/groups-create-rule) ve vašem tenantovi Azure AD pro výchozí skupiny "Všichni uživatelé", které mají být k dispozici.

   ![Moje aplikace SaaS ve službě Azure AD ](media/migrate-adfs-apps-to-azure/permit-access-to-all-users-3.png)


#### <a name="example-2-allow-a-group-explicitly"></a>Příklad 2: Povolit skupinu explicitně

Explicitní oprávnění skupiny ve s.r.o.FS:


![vystavování autorizačních pravidel ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-1.png)


Toje, jak pravidlo mapuje na Azure AD:

Na [webu Azure Portal](https://portal.azure.com/)nejprve [vytvoříte skupinu uživatelů,](https://docs.microsoft.com/azure/active-directory/fundamentals/active-directory-groups-create-azure-portal) která odpovídá skupině uživatelů ze služby AD FS, a pak této skupině přiřadíte oprávnění k aplikaci:

![Přidat přiřazení ](media/migrate-adfs-apps-to-azure/allow-a-group-explicitly-2.png)


#### <a name="example-3-authorize-a-specific-user"></a>Příklad 3: Autorizace konkrétního uživatele

Explicitní autorizace uživatele ve s.r.o.FS:

![vystavování autorizačních pravidel ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-1.png)

Toje, jak pravidlo mapuje na Azure AD:

Na [webu Azure Portal](https://portal.azure.com/)přidejte uživatele do aplikace prostřednictvím karty Přidat přiřazení aplikace, jak je znázorněno níže:

![Moje aplikace SaaS v Azure ](media/migrate-adfs-apps-to-azure/authorize-a-specific-user-2.png)

 
### <a name="map-multi-factor-authentication-rules"></a>Mapovat pravidla vícefaktorového ověřování 

Místní nasazení [vícefaktorového ověřování (MFA)](https://docs.microsoft.com/azure/active-directory/authentication/multi-factor-authentication) a služby AD FS bude i po migraci fungovat, protože jste federováni se službou AD FS. Zvažte však migraci na integrované funkce MFA azure, které jsou vázané na pracovní postupy podmíněného přístupu Azure AD. 

Následují příklady typů pravidel vícefaktorového přístupu ve službě AD FS a jak je můžete namapovat na Azure AD na základě různých podmínek:

Nastavení pravidel vícefaktorové mezifaktorí ve službě AD FS:

![Nastavení Azure AD MFA](media/migrate-adfs-apps-to-azure/mfa-location-1.png)


#### <a name="example-1-enforce-mfa-based-on-usersgroups"></a>Příklad 1: Vynucení vícefaktorové na základě uživatelů nebo skupin

Volič uživatele nebo skupin je pravidlo, které umožňuje vynutit vícefaktorové ověřování na základě jednotlivé skupiny (SID) skupiny nebo pro jednotlivé uživatele (primární SID). Kromě přiřazení uživatele/skupin všechna další zaškrtávací políčka v uživatelském rozhraní konfigurace služby AD FS MFA jako další pravidla, která jsou vyhodnocována po vynucení pravidla Uživatele/skupin. 


Zadejte pravidla mfa pro uživatele nebo skupinu ve službě Azure AD:

1. Vytvořte [novou zásadu podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Vyberte **přiřazení**. Přidejte uživatele nebo skupiny, na které chcete vynutit vícefaktorové ověřování.

3. Nakonfigurujte možnosti **ovládacích prvků aplikace Access,** jak je znázorněno níže:  
‎

![Nastavení vícefaktorové houfové služby AAD](media/migrate-adfs-apps-to-azure/mfa-usersorgroups.png)

 
 #### <a name="example-2-enforce-mfa-for-unregistered-devices"></a>Příklad 2: Vynucení vícefaktorové registrace pro neregistrovaná zařízení

Zadejte pravidla mfa pro neregistrovaná zařízení ve službě Azure AD:

1. Vytvořte [novou zásadu podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

2. Nastavte **přiřazení** pro **všechny uživatele**.

3. Nakonfigurujte možnosti **ovládacích prvků aplikace Access,** jak je znázorněno níže:  
‎

![Nastavení vícefaktorové houfové služby AAD](media/migrate-adfs-apps-to-azure/mfa-unregistered-devices.png)

 
Pokud nastavíte možnost Pro více ovládacích prvků vyžadovat jeden z vybraných ovládacích prvků, znamená to, že pokud uživatel splní některou z podmínek určených tímto zaškrtávacím políčkem, bude jim udělen přístup k vaší aplikaci.

#### <a name="example-3-enforce-mfa-based-on-location"></a>Příklad 3: Vynucení vícefaktorového přístupu na základě umístění

Zadejte pravidla mfa na základě umístění uživatele ve službě Azure AD:

1. Vytvořte [novou zásadu podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json).

1. Nastavte **přiřazení** pro **všechny uživatele**.

1. [Konfigurace pojmenovaných umístění ve službě Azure AD,](https://docs.microsoft.com/azure/active-directory/active-directory-named-locations) jinak federace z evnitř podnikové sítě je důvěryhodná. 

1. Nakonfigurujte **pravidla podmínky** a určete umístění, pro která chcete vynutit vícefaktorové řízení.

![Nastavení Azure AD MFA](media/migrate-adfs-apps-to-azure/mfa-location-1.png)

5. Nakonfigurujte možnosti **ovládacích prvků aplikace Access,** jak je znázorněno níže:


![Mapovat zásady řízení přístupu](media/migrate-adfs-apps-to-azure/mfa-location-2.png)

 
### <a name="map-emit-attributes-as-claims-rule"></a>Mapovat atributy emitu jako pravidlo deklarací

Zde je příklad mapování atributů ve snaze OV FS:

![Nastavení Azure AD MFA](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-1.png)


Toje, jak pravidlo mapuje na Azure AD:

Na [webu Azure Portal](https://portal.azure.com/)vyberte **Podnikové aplikace**, **Jednotné přihlašování**a přidejte **atributy tokenu SAML,** jak je znázorněno níže:

![Nastavení Azure AD MFA](media/migrate-adfs-apps-to-azure/map-emit-attributes-as-claimsrule-2.png)



### <a name="map-built-in-access-control-policies"></a>Mapování integrovaných zásad řízení přístupu

AD FS 2016 má několik integrovaných zásad řízení přístupu, ze kterých si můžete vybrat:

![Azure AD integrované řízení přístupu](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-1.png)

 
Chcete-li implementovat integrované zásady ve službě Azure AD, můžete použít [novou zásadu podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/authentication/tutorial-enable-azure-mfa?toc=/azure/active-directory/conditional-access/toc.json&bc=/azure/active-directory/conditional-access/breadcrumb/toc.json) a nakonfigurovat ovládací prvky přístupu, nebo můžete použít vlastní návrhář zásad ve službě AD FS 2016 ke konfiguraci zásad řízení přístupu. Editor pravidel má vyčerpávající seznam povolení a kromě možností, které vám mohou pomoci provádět všechny druhy permutací.

![Zásady řízení přístupu azure ad](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-2.png)



V této tabulce jsme uvedeny některé užitečné povolení a kromě možnosti a jak se mapovat na Azure AD. 


| | Jak nakonfigurovat možnost povolení ve službě Azure AD?| Jak nakonfigurovat možnost S výjimkou ve službě Azure AD? |
| - | - | - |
| Ze specifické sítě| Mapy na [pojmenované umístění](https://docs.microsoft.com/azure/active-directory/reports-monitoring/quickstart-configure-named-locations) ve službě Azure AD| Použití možnosti **Vyloučit** pro [důvěryhodná umístění](https://docs.microsoft.com/azure/active-directory/conditional-access/location-condition) |
| Z konkrétních skupin| [Nastavení přiřazení uživatele/skupin](https://docs.microsoft.com/azure/active-directory/manage-apps/assign-user-or-group-access-portal)| Použití možnosti **Vyloučit** v uživatelích a skupinách |
| Ze zařízení s konkrétní úrovní důvěryhodnosti| Nastavte tuto možnost z ovládacího prvku Stav zařízení v části Podmínky přiřazení ->| Použití možnosti **Vyloučit** v části Stav stavu zařízení a Zahrnout **všechna zařízení** |
| Se specifickými nároky v žádosti| Toto nastavení nelze migrovat.| Toto nastavení nelze migrovat. |


Příklad konfigurace možnosti Vyloučit pro důvěryhodná umístění na webu Azure Portal:

![Snímek obrazovky s mapováním zásad řízení přístupu](media/migrate-adfs-apps-to-azure/map-builtin-access-control-policies-3.png)



## <a name="transition-users-from-ad-fs-to-azure-ad"></a>Přechod uživatelů ze služby AD FS do služby Azure AD

### <a name="sync-ad-fs-groups-in-azure-ad"></a>Synchronizace skupin služby AD FS ve službě Azure AD

Při mapování autorizačních pravidel mohou aplikace, které se ověřují pomocí služby AD FS, používat skupiny služby Active Directory pro oprávnění. V takovém případě použijte [Azure AD Connect](https://go.microsoft.com/fwlink/?LinkId=615771) k synchronizaci těchto skupin s Azure AD před migrací aplikací. Ujistěte se, že jste tyto skupiny a členství před migrací ověřili, abyste mohli udělit přístup stejným uživatelům při migraci aplikace.

Další informace naleznete [v tématu Požadavky pro použití atributů skupiny synchronizovaných ze služby Active Directory](https://docs.microsoft.com/azure/active-directory/hybrid/how-to-connect-fed-group-claims).

### <a name="setup-user-self-provisioning"></a>Nastavení samozbytného zřizování uživatelů 

Některé aplikace SaaS podporují možnost vlastního zřízení uživatelů při prvním přihlášení k aplikaci. Ve službě Azure Active Directory (Azure AD) termín zřizování aplikací odkazuje na automatické vytváření identit uživatelů a rolí v cloudu[(SaaS)](https://azure.microsoft.com/overview/what-is-saas/)aplikace, které uživatelé potřebují přístup k. Migrovaní uživatelé již budou mít účet v aplikaci SaaS. Všichni noví uživatelé přidaný po migraci bude nutné zřídit. Otestujte [zřizování aplikací SaaS](https://docs.microsoft.com/azure/active-directory/app-provisioning/user-provisioning) po migraci aplikace.

### <a name="sync-external-users-in-azure-ad"></a>Synchronizace externích uživatelů ve službě Azure AD

Stávající externí uživatelé mohou být v rámci ad FS nastaveni dvěma hlavními způsoby:

#### <a name="external-users-with-a-local-account-within-your-organization"></a>Externí uživatelé s místním účtem ve vaší organizaci

Budete i nadále moci používat tyto účty stejným způsobem, že vaše interní uživatelské účty fungují. Tyto externí uživatelské účty mají v rámci organizace principový název, i když e-mail účtu může ukazovat externě. Jak budete postupovat s migrací, můžete využít výhod, které [Azure AD B2B](https://docs.microsoft.com/azure/active-directory/b2b/what-is-b2b) nabízí migrací těchto uživatelů používat vlastní firemní identitu, když taková identita je k dispozici. Tím se zjednoduší proces přihlášení pro tyto uživatele, protože jsou často přihlášeni pomocí vlastního firemního přihlášení. Správa vaší organizace bude také usnadněna tím, že již nebude muset spravovat účty pro externí uživatele.

#### <a name="federated-external-identities"></a>Federované externí identity

Pokud jste v současné době federující s externí organizací, máte několik přístupů, aby se:

* [Přidejte uživatele spolupráce Azure Active Directory B2B na webu Azure Portal](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator). Pozvánky pro spolupráci B2B můžete proaktivně odesílat z portálu pro správu Azure AD do partnerské organizace, aby jednotliví členové mohli nadále používat aplikace a prostředky, na které jsou zvyklí. 

* [Vytvořte samoobslužný pracovní postup registrace B2B,](https://docs.microsoft.com/azure/active-directory/b2b/self-service-portal) který generuje požadavek pro jednotlivé uživatele ve vaší partnerské organizaci pomocí rozhraní API pro pozvání B2B.

Bez ohledu na to, jak jsou nakonfigurováni stávající externí uživatelé, mají pravděpodobně oprávnění, která jsou přidružena k jejich účtu, a to buď v členství ve skupině, nebo v konkrétních oprávněních. Vyhodnoťte, zda je třeba tato oprávnění migrovat nebo vyčistit. Účty ve vaší organizaci, které představují externího uživatele, musí být zakázány, jakmile je uživatel migrován na externí identitu. Proces migrace by měl být projednán s vašimi obchodními partnery, protože může dojít k přerušení jejich schopnosti připojit se k vašim prostředkům.

## <a name="migrate-and-test-your-apps"></a>Migrace a testování aplikací

Postupujte podle procesu migrace podrobně popsaného v tomto článku.

Pak přejděte na [portál Azure](https://aad.portal.azure.com/) a otestujte, jestli byla migrace úspěšná. Postupujte podle následujících pokynů:
1. Vyberte **Podnikové aplikace** > **Všechny aplikace** a najděte aplikaci ze seznamu.

1. Vyberte **Spravovat** > **uživatele a skupiny,** chcete-li aplikaci přiřadit alespoň jednoho uživatele nebo skupinu.

1. Vyberte **Spravovat** > **podmíněný přístup**. Zkontrolujte seznam zásad a ujistěte se, že neblokujete přístup k aplikaci pomocí [zásad podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/active-directory-conditional-access-azure-portal).

V závislosti na konfiguraci aplikace ověřte, zda přizpůsobovat služby ASO funguje správně. 

| Typ ověřování| Testování |
| - | - |
| OAuth / OpenID Připojení| Vyberte **podnikové aplikace > oprávnění** a ujistěte se, že jste v uživatelském nastavení aplikace souhlasili s použitím aplikace ve vaší organizaci.  
‎ |
| Jednotné přihlašování založené na SAML| Použijte tlačítko [Testovat nastavení SAML](https://docs.microsoft.com/azure/active-directory/develop/howto-v1-debug-saml-sso-issues) v části **Jednotné přihlašování**.  
‎ |
| Připomene se pomocí hesla| Stáhněte a nainstalujte rozšíření [MyApps Secure Sign](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[-](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)[in](https://docs.microsoft.com/azure/active-directory/user-help/active-directory-saas-access-panel-introduction)Extension . Toto rozšíření vám pomůže spustit všechny cloudové aplikace vaší organizace, které vyžadují použití procesu přistaň.  
‎ |
| Proxy aplikací| Ujistěte se, že je konektor spuštěný a přiřazený k vaší aplikaci. Další pomoc naleznete v příručce[ ](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)pro řešení potíží s [aplikačním serverem Proxy.](https://docs.microsoft.com/azure/active-directory/manage-apps/application-proxy-troubleshoot)  
‎ |

> [!NOTE]
> Soubory cookie ze starého prostředí ad fs budou na počítačích uživatele stále trvalé. Tyto soubory cookie může způsobit problémy s migrací jako uživatelé mohou být přesměrováni na staré přihlašovací prostředí služby AD FS versus nové přihlášení Azure AD. Možná budete muset vymazat soubory cookie uživatelského prohlížeče ručně nebo pomocí skriptu. Můžete také použít Správce konfigurace system center nebo podobnou platformu.

### <a name="troubleshoot"></a>Řešení potíží

Pokud existují chyby z testu migrovaných aplikací, řešení potíží může být prvním krokem před návratem do stávajících předávajících stran služby AD FS. Přečtěte [si, jak ladit jednotné přihlašování na saml k aplikacím ve službě Azure Active Directory](https://docs.microsoft.com/azure/active-directory/azuread-dev/howto-v1-debug-saml-sso-issues).

### <a name="rollback-migration"></a>Migrace vrácení zpět

Pokud se migrace nezdaří, doporučujeme ponechat stávající předávající strany na serverech služby AD FS a odebrat přístup předávajícím stranám. To umožní rychlé záložní v případě potřeby během nasazení.

### <a name="end-user-communication"></a>Komunikace s koncovým uživatelem

Zatímco plánované okno výpadku sám může být minimální, měli byste stále plánovat na komunikaci těchto časových rámců proaktivně zaměstnancům při provádění přeškrtnutí ze služby AD FS na Azure AD. Ujistěte se, že vaše aplikace má tlačítko Zpětná vazba nebo odkazy na helpdesk pro problémy.

Po dokončení nasazení můžete odeslat komunikaci informující uživatele o úspěšném nasazení a připomenout jim všechny nové kroky, které musí provést.

* Dejte uživatelům pokyn, aby pomocí [přístupového panelu](https://myapps.microsoft.com.com/) přistupovali ke všem migrovaných aplikacím. 

* Připomeňte uživatelům, že možná budou muset aktualizovat nastavení vícefaktorové hod. 

* Pokud je nasazeno samoobslužné resetování hesla, uživatelé možná budou muset aktualizovat nebo ověřit své metody ověřování. Viz Šablony komunikace [koncových](https://aka.ms/mfatemplates) uživatelů vícefaktorové ověřování a [samoobslužné](https://aka.ms/ssprtemplates) ověřování dat.

Komunikace s externími uživateli: Tato skupina uživatelů je obvykle nejvíce kriticky ovlivněna v případě problémů. To platí zejména v případě, že stav zabezpečení určuje jinou sadu pravidel podmíněného přístupu nebo rizikových profilů pro externí partnery. Ujistěte se, že externí partneři jsou si vědomi plánu migrace do cloudu a mají časový rámec, během kterého se doporučuje k účasti na pilotní nasazení, které testuje všechny toky jedinečné pro externí spolupráci. Nakonec se ujistěte, že mají způsob, jak získat přístup k helpdesku v případě problémů s porušením.

## <a name="next-steps"></a>Další kroky
Čtení [migrace ověřování aplikací do Azure AD](https://aka.ms/migrateapps/whitepaper)<p>
Nastavení [podmíněného přístupu](https://docs.microsoft.com/azure/active-directory/conditional-access/overview) a [vícefaktorové ho řízení přístupu](https://docs.microsoft.com/azure/active-directory/authentication/concept-mfa-howitworks)
