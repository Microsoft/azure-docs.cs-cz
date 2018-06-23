---
title: Jak a proč se aplikace přidávají do Azure Active Directory
description: Co znamená pro aplikace, který se má přidat do služby Azure AD a jak se získat existuje?
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.component: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol, lenalepa
ms.openlocfilehash: 5c8ae9534e79b8dc801262f85d8a007e050f4da7
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36316955"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Jak a proč se aplikace přidávají do služby Azure AD

Existují dvě reprezentace aplikací ve službě Azure AD: 
* [Objekty aplikací](active-directory-application-objects.md#application-object) – i když jsou [výjimky](#notes-and-exceptions), objekty aplikací lze považovat za definici aplikace.
* [Služba objektů](active-directory-application-objects.md#service-principal-object) -lze považovat za instance aplikace. Objekty služby obecně odkazují na objekt, aplikace a jeden objekt aplikace lze odkazovat pomocí více objekty služby ve adresářích.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Co jsou objekty aplikací a kde do které pocházejí z?
Můžete spravovat [objekty aplikací](active-directory-application-objects.md#application-object) na portálu Azure pomocí [registrace aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade) prostředí. Objekty aplikací popisují aplikace do služby Azure AD a lze považovat za definici aplikace a umožňuje službu, kterou chcete vědět, jak mají vystavovat tokeny pro aplikaci na základě nastavení. Objekt aplikace bude existovat pouze v jeho domovského adresáře, i když je víceklientské aplikaci, která podporuje objekty služby v jiných adresářích. Objekt aplikace může obsahovat žádné z těchto (jako zde nejsou uvedeny další informace o):
* Název, loga a vydavatel
* Adresy URL odpovědí
* Tajné klíče (symetrické nebo asymetrické klíče, které slouží k ověření aplikace)
* Rozhraní API závislosti (OAuth)
* Publikované rozhraní API nebo prostředky nebo obory (OAuth)
* Aplikace role (RBAC)
* Metadata jednotné přihlašování a konfigurace
* Zřizování uživatelů metadata a konfigurace
* Proxy server, metadat a konfigurace

Objekty aplikací lze vytvořit pomocí několika způsoby, včetně:
* Registrace aplikace na portálu Azure
* Vytvoření nové aplikace pomocí sady Visual Studio a nakonfigurovat jej pro používání ověřování Azure AD
* Když správce přidá aplikaci z Galerie aplikace (které také vytvoří službu objektu zabezpečení)
* Chcete-li vytvořit novou aplikaci pomocí Microsoft Graph API, Azure AD Graph API nebo prostředí PowerShell
* Mnoho dalších včetně různé možnosti pro vývojáře v Azure a rozhraní API Průzkumníka prostředí napříč centra pro vývojáře

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Co jsou objekty služby a kde do které pocházejí z?
Můžete spravovat [služby objekty](active-directory-application-objects.md#service-principal-object) na portálu Azure pomocí [podnikové aplikace, které](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) prostředí. Objekty služby jsou co řídit aplikaci připojení ke službě Azure AD a lze považovat za instanci aplikace ve vašem adresáři. Pro dané aplikaci může mít maximálně jednu aplikaci (která je registrována v adresáři, "home") a jeden nebo více služby hlavní objekty představující instancí aplikace v každém adresáři, ve kterém funguje. 

Objekt služby mohou zahrnovat:

* Odkaz na objekt aplikace prostřednictvím vlastnost ID aplikace zpět
* Záznamy místních uživatelů a přiřazení skupin pro aplikační role
* Zaznamenává místní uživatele a správce oprávnění udělená aplikaci
  * Příklad: oprávnění pro aplikaci pro přístup k e-mailu určitého uživatele
* Zaznamenává místní zásady, včetně zásad podmíněného přístupu
* Zaznamenává alternativní místní nastavení pro aplikace
  * Pravidla transformace deklarací identity
  * Mapování atributů (zřizování uživatelů)
  * Aplikace Directory specifické role (pokud to aplikace podporuje vlastní role)
  * Specifické pro adresář názvem a logem

Podobně jako objekty aplikací mohou být také vytvořeny objekty služby prostřednictvím několika způsoby, včetně:

* Když se uživatel přihlásí k aplikaci jiného výrobce integrované s Azure AD
  * Při přihlašování uživatelé vyzváni k zadání oprávnění k aplikaci pro přístup k jejich profil a další oprávnění. První osoba, která vyjádřit svůj souhlas způsobí, že objekt služby, který představuje aplikaci, která má být přidán do adresáře.
* Když se uživatelé přihlašují k online službám společnosti Microsoft, jako [Office 365](http://products.office.com/)
  * Při přihlášení k odběru Office 365 nebo začít se zkušební verzí, vytvoří se jeden nebo více služby objekty v adresáři představující různé služby, které se používají k zajišťování všechny funkce související s Office 365.
  * Některé služby Office 365, jako je SharePoint vytvořit objekty služby průběžně zabezpečené komunikaci mezi součástmi, včetně pracovních postupů.
* Když správce přidá aplikaci z Galerie aplikací (tím se také vytvoří základní objekt aplikace)
* Přidat aplikace pro použití [proxy aplikace služby Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Připojení aplikace pro jednotné přihlašování na použití SAML a hesla jednotné přihlašování (SSO)
* Programově pomocí rozhraní Azure AD Graph API nebo prostředí PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Jak jsou objekty aplikací a objekty služby související se vzájemně k sobě?
Aplikace má jeden objekt aplikace v jeho domovského adresáře, který odkazuje jeden nebo více objekty služby v každé z adresáře, kde funguje (včetně domovský adresář aplikace).
![Diagram znázorňující způsob, jakým se objekty aplikací a objekty služby komunikovat s sebe navzájem a instancí služby Azure AD.][apps_service_principals_directory]

Na předchozím obrázku, společnost Microsoft spravuje dva adresáře interně (zobrazené na levé straně), tak používá k publikování aplikací:

* Jeden pro Microsoft Apps (Microsoft services directory)
* Jeden pro předběžně integrované aplikace jiných výrobců (adresář Galerie aplikace)

Aplikace vydavatelů nebo dodavatelů, kteří integraci se službou Azure AD musí mít publikování adresář (zobrazené na pravé straně jako "Některá SaaS adresář").

Aplikace, které přidáte sami (vyjádřené **aplikace (váš)** v diagramu) patří:

* Aplikace, které jste vytvořili (integrované s Azure AD)
* Aplikace, které jste připojeni pro jednotného přihlašování
* Aplikace můžete publikovat pomocí proxy aplikace služby Azure AD

### <a name="notes-and-exceptions"></a>Poznámky a výjimek
* Ne všechny objekty služby, přejděte zpět do objektu application. Pokud Azure AD bylo původně vytvořeno služeb poskytovaných aplikacích byly omezenější a instanční objekt dostatečné pro vytvoření identity aplikací. Původní instanční objekt byl co nejblíže ve tvaru k účtu služby Windows Server Active Directory. Z tohoto důvodu je stále možné vytvořit objekty služby prostřednictvím různé způsoby, například pomocí Azure AD PowerShell bez vytvoření první objekt aplikace. Azure AD Graph API vyžaduje objekt aplikace před vytvořením objektu služby.
* Ne všechny informace popsané výše je aktuálně zveřejněný prostřednictvím kódu programu. Toto jsou k dispozici pouze v uživatelském rozhraní:
  * Pravidla transformace deklarací identity
  * Mapování atributů (zřizování uživatelů)
* Podrobnější informace o objektu služby a objekty aplikací naleznete v dokumentaci rozhraní API REST Azure AD Graph odkaz:
  * [Aplikace](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Instanční objekt](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Proč aplikace integraci s Azure AD?
Aplikace byla přidána do služby Azure AD můžete využít jeden nebo více služeb, které poskytuje, včetně:

* Aplikace ověřování a autorizace
* Uživatel ověřování a autorizace
* Jednotné přihlašování pomocí federace nebo hesla
* Zřizování uživatelů a synchronizace
* Řízení přístupu na základě role - kontroluje adresáři použijte k definování role aplikaci k provedení ověření na základě role v aplikaci
* Služby autorizace OAuth - používá Office 365 a jiných aplikací společnosti Microsoft k autorizaci přístupu k rozhraní API nebo prostředky
* Publikování aplikací a proxy - publikovat aplikaci z privátní sítě k Internetu

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Kdo má oprávnění k přidání aplikace do instance Moje Azure AD?
Přestože jsou některé úlohy, jenom globální správci může (například přidání aplikací z Galerie aplikací a konfigurace aplikace pro použití Proxy aplikace) ve výchozím nastavení provádět všechny uživatele ve vašem adresáři mít oprávnění k registraci aplikací objekty, které jde o vývoj a uvážení přes aplikace, které se sdílené složky nebo udělení přístupu k jejich organizačním datům prostřednictvím souhlasu. Pokud uživatel je první uživatel ve vašem adresáři pro přihlášení k aplikaci a udělení souhlasu, který vytvoří objekt služby ve vašem klientovi; informace o udělení souhlasu, jinak budou uložená v existující instanční objekt.

Umožníte uživatelům zaregistrovat a souhlas týkající se původně zvukové aplikace může, ale mějte na paměti následující:


* Aplikace se moct využívat Windows Server Active Directory pro ověřování uživatelů pro mnoho let bez nutnosti aplikace má být zaregistrován nebo zaznamenány v adresáři. Organizace teď bude lepší viditelnost přesně kolik aplikace používají k adresáři a pro jaké účely.
* Delegování těchto odpovědnosti uživatelům Neguje potřeba registraci aplikace řízené správce a proces publikování. S Active Directory Federation Services (ADFS) je pravděpodobné, že správce musel přidat aplikaci jako předávající strana jménem svého vývojáře. Nyní mohou vývojáři samoobslužné služby.
* Uživatelé přihlášení do aplikace pomocí účtu organizace pro obchodní účely je dobré. Pokud následně opustí organizaci, které budou automaticky ztratíte přístup k účtu v aplikaci, kterou používají.
* S záznam jaká data byla sdílené, pomocí kterého aplikace je dobré. Data jsou přenositelné více než kdy dřív a je užitečné používat zrušte záznam kdo sdílet s aplikací, které jaká data.
* Vlastníci rozhraní API, které používají Azure AD pro OAuth rozhodnout přesně jaké oprávnění uživatelé mohou udělit k aplikacím a oprávnění, která vyžadují, správce s. Pouze správci můžete svůj souhlas větší obory a větších oprávnění, zatímco souhlas uživatele je omezená na vlastní data uživatelů a možnosti.
* Pokud uživatel přidá nebo umožňuje aplikaci přístup k datům, dají auditovat události, aby se dala zobrazit zprávy o auditu v rámci portálu Azure k určení, jak aplikaci byl přidán do adresáře.

Pokud stále chcete zabránit uživatelům ve vašem adresáři z registrace aplikací a přihlášení do aplikace bez schválení správce, existují dvě nastavení, které je možné změnit vypnout tyto možnosti:

* Chcete-li zabránit uživatelům v souhlas pro aplikace na jejich vlastní účet:
  1. V portálu Azure přejděte do [uživatelská nastavení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) části v rámci podnikové aplikace.
  2. Změna **uživatelů můžete souhlas aplikace přístup k datům společnosti jejich jménem** k **ne**. 
      > [!NOTE]
      > Pokud se rozhodnete vypnout souhlas uživatele, budou muset souhlasit s žádné nové aplikace, které uživatel potřebuje používat správce.    
* Chcete-li zabránit uživatelům v registraci svých vlastních aplikacích:
  1. V portálu Azure přejděte do [uživatelská nastavení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) části v rámci Azure Active Directory
  2. Změna **uživatelé mohou registrovat aplikace** k **ne**.

> [!NOTE]
> Společnost Microsoft, samotné používá výchozí konfiguraci se uživatelé moct zaregistrovat aplikace a souhlas aplikace na jejich vlastní účet.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

