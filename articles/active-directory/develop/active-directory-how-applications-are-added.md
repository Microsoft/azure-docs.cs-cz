---
title: Jak a proč se aplikace přidávají do služby Azure Active Directory
description: Co znamená pro aplikaci přidat do služby Azure AD a jak se toho, že?
services: active-directory
documentationcenter: ''
author: CelesteDG
manager: mtillman
editor: ''
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.subservice: develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/18/2018
ms.author: celested
ms.custom: aaddev
ms.reviewer: elisol, lenalepa
ms.collection: M365-identity-device-management
ms.openlocfilehash: 82ccfe6a8d77926d2b908f84db600b0665565d51
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56173887"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Jak a proč se aplikace přidávají do služby Azure AD

Existují dva reprezentace aplikace ve službě Azure AD: 
* [Objekty aplikací](app-objects-and-service-principals.md#application-object) – i když jsou [výjimky](#notes-and-exceptions), objekty aplikací lze považovat za definici aplikace.
* [Instanční](app-objects-and-service-principals.md#service-principal-object) -lze považovat za instanci aplikace. Instanční objekty obecně odkazovat na objekt aplikace a jeden objekt aplikace lze odkazovat pomocí více instanční objekty v adresáři.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Co jsou objekty aplikací a odkud jsou pocházejí z?
Můžete spravovat [objekty aplikací](app-objects-and-service-principals.md#application-object) na webu Azure Portal prostřednictvím [registrace aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ApplicationsListBlade) prostředí. Objekty aplikací popisují aplikace do služby Azure AD a lze považovat za definici aplikace, povolení služby vědět, jak pro vydávání tokenů na aplikaci na základě nastavení. Objekt aplikace bude existovat pouze v jeho domovského adresáře, i když je aplikace s více tenanty podpora instanční objekty v jiných adresářích. Objekt aplikace může zahrnovat následující (jako dalšími informace, které zde nejsou uvedeny):
* Název, loga a vydavatel
* Adresy URL odpovědí
* Tajné kódy (symetrický a/nebo asymetrické klíče používané k ověření aplikace)
* Rozhraní API závislosti (OAuth)
* Publikované rozhraní API/zdroje/obory (OAuth)
* Aplikace role (RBAC)
* Konfigurace a metadat jednotného přihlašování
* Metadata a konfigurace zřizování uživatelů
* Metadata serveru proxy a konfigurace

Objekty aplikací je možné vytvářet přes více cest, včetně:
* Registrace aplikací na webu Azure Portal
* Vytvoření nové aplikace pomocí sady Visual Studio a jeho konfigurace ověřování Azure AD
* Když správce přidá aplikaci z Galerie aplikací (které budou také vytvořit instanční objekt)
* Použití rozhraní Microsoft Graph API, Azure AD Graph API nebo Powershellu k vytvoření nové aplikace
* Řada dalších včetně různé prostředí pro vývojáře v Azure a v prostředí Průzkumníka rozhraní API v centrech pro vývojáře

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Co jsou instančních objektů a odkud jsou pocházejí z?
Můžete spravovat [instanční](app-objects-and-service-principals.md#service-principal-object) na webu Azure Portal prostřednictvím [podnikové aplikace](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) prostředí. Instanční objekty jsou, co řídí aplikaci připojení ke službě Azure AD a lze považovat za instanci aplikace ve vašem adresáři. Pro danou aplikaci může mít maximálně jeden objekt aplikace, (který je registrován v adresáři "domovskou") a nejmíň jeden instanční objekty představující instancí aplikace v každém adresáři, ve kterém funguje. 

Instanční objekt služby mohou zahrnovat:

* Odkaz na objekt aplikace prostřednictvím vlastnosti ID aplikace
* Zaznamenává místní uživatele a skupiny přiřazení role aplikace
* Zaznamenává místní uživatele a správce oprávnění udělené aplikaci
  * Příklad: oprávnění pro aplikaci pro přístup k e-mailu určitého uživatele
* Zaznamenává místní zásady, včetně zásad podmíněného přístupu
* Záznamy o alternativní místní nastavení pro aplikaci
  * Pravidla transformace deklarací identity
  * Mapování atributů (zřizování uživatelů)
  * Role adresáře konkrétní aplikace (Pokud aplikace podporuje vlastní role)
  * Název adresáře nebo loga

Jako objekty aplikace je možné také vytvářet instanční objekty přes více způsoby včetně:

* Když se uživatelé přihlásí do aplikace třetích stran integrovat se službou Azure AD
  * Při přihlašování uživatelé vyzváni k zadání oprávnění k aplikaci přístup k jejich profil a další oprávnění. První osoby, čímž udělíte souhlas způsobí, že hlavní název služby, který představuje aplikaci přidávané do adresáře.
* Při přihlášení uživatele k online službám Microsoftu, jako jsou [Office 365](https://products.office.com/)
  * Při přihlášení k odběru Office 365 nebo začít zkušební verzi, jeden nebo více objektů služby se vytvoří v adresáři reprezentující různé služby, které se používají k doručování všechny funkce související s Office 365.
  * Některé služby Office 365, jako je SharePoint vytvoření instančních objektů průběžně pro umožnění bezpečné komunikace mezi komponentami včetně pracovních postupů.
* Když správce přidá aplikaci z Galerie aplikací (tím se také vytvoří základní objekt aplikace)
* Přidat aplikace pro použití [Proxy aplikací Azure AD](https://msdn.microsoft.com/library/azure/dn768219.aspx)
* Připojení aplikace pro jednotné přihlašování pomocí SAML nebo heslem jednotného přihlašování (SSO)
* Prostřednictvím kódu programu přes Azure AD Graph API nebo Powershellu

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Jak jsou objekty aplikací a instanční objekty vzájemně souvisí?
Aplikace obsahuje jeden objekt aplikace v jeho domovského adresáře, který odkazuje jeden nebo více instanční objekty v každém z adresáře, kde pracuje (včetně domovský adresář aplikace).
![Diagram znázorňující, jak objekty aplikací a instanční objekty komunikovat s sebou a instancí služby Azure AD.][apps_service_principals_directory]

Na předchozím obrázku, společnost Microsoft spravuje dva adresáře interně (viz vlevo), používá k publikování aplikace:

* Jeden pro Microsoft Apps (adresáře služby Microsoft)
* Jeden pro předem integrovaných aplikací třetí strany (aplikaci Galerie adresář)

Aplikace vydavatel/dodavatelů, kteří integrace s Azure AD musí mít publikování adresáře (zobrazené na pravé straně jako "Některé SaaS adresář").

Aplikace, které přidáte sami sebe (reprezentovány jako **aplikace (vaší)** v diagramu) patří:

* Aplikace, které jste vytvořili (integrované s Azure AD)
* Aplikace pro připojené jednotného přihlašování
* Aplikace, které jste publikovali pomocí proxy aplikací služby Azure AD

### <a name="notes-and-exceptions"></a>Poznámky a výjimky
* Ne všechny instanční objekty odkazovat zpět do aplikační objekt. Když Azure AD byl původně sestaven poskytované služby k aplikace byly omezenější a instanční objekt služby byla dostatečná pro vytvoření identity aplikací. Původní instanční objekt se blíže v obrazci k účtu služby Windows Server Active Directory. Z tohoto důvodu je stále možné vytvořit instanční objekty prostřednictvím různé přístupy, například pomocí Azure AD PowerShell, dokud nevytvoříte objekt aplikace. Azure AD Graph API vyžaduje objekt aplikace před vytváření instančního objektu.
* Ne všechny informace popsané výše je aktuálně přístupný prostřednictvím kódu programu. Následující jsou k dispozici pouze v uživatelském rozhraní:
  * Pravidla transformace deklarací identity
  * Mapování atributů (zřizování uživatelů)
* Podrobnější informace o objektu služby a aplikace objektů naleznete v referenční dokumentaci rozhraní REST API služby Azure AD Graph:
  * [Aplikace](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#application-entity)
  * [Instanční objekt služby](https://msdn.microsoft.com/library/azure/ad/graph/api/entity-and-complex-type-reference#serviceprincipal-entity)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Proč aplikace integrují s Azure AD?
Aplikace se přidají do služby Azure AD využívat jeden nebo více služeb, které nabízí, včetně:

* Aplikace ověřování a autorizace
* Ověřování a autorizace uživatelů
* Jednotné přihlašování pomocí federace nebo hesla
* Zřizování uživatelů a synchronizace
* Řízení přístupu na základě rolí – kontroluje použijte adresář k definování rolí aplikace provádět ověřování na základě role v aplikaci
* Služby autorizace OAuth - používá Office 365 a jiné aplikace od Microsoftu k autorizaci přístupu k rozhraní API nebo prostředků
* Publikování aplikací a proxy - publikovat aplikaci v privátní síti k Internetu

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Kdo má oprávnění k přidávání aplikací na Moje instance služby Azure AD?
I když existují některé úkoly, jen globální správci můžou provádět (jako je přidání aplikací z Galerie aplikací a konfigurace aplikace pro použití Proxy aplikace) ve výchozím nastavení všichni uživatelé ve vašem adresáři mít práva pro registraci aplikace objekty, které jsou vývoj a podle vlastního uvážení prostřednictvím aplikace, které jsou sdílenou složku/poskytují přístup k datům jejich organizace prostřednictvím souhlasu. Pokud uživatel je první uživatel ve vašem adresáři se přihlásit k aplikaci a udělit souhlas, ve vašem tenantovi; který vytvoření instančního objektu jinak informace o udělení souhlasu se uloží na existující instanční objekt.

Umožňuje uživatelům zaregistrovat se a souhlas s aplikací může zpočátku zvukové týkající se ale mějte na paměti následující:


* Aplikace byly využívat bez nutnosti aplikace má být zaregistrován nebo zaznamenány v adresáři systému Windows Server Active Directory k ověřování uživatelů po mnoho let. Organizace teď bude lepší viditelnosti přesně kolik aplikace používají adresáře a pro jaké účely.
* Delegování tyto povinnosti uživatelům Neguje potřeba registraci správy řízené aplikace a proces publikování. S Active Directory Federation Services (ADFS) je pravděpodobné, že správce musel aplikaci přidat jako předávající strana jménem svého vývojáře. Vývojáři teď můžou samoobslužné funkce.
* Uživatelé s přihlašováním k aplikacím pomocí jejich účtů organizace pro obchodní účely je dobrá věc. Pokud následně opustí organizaci, které jsou automaticky byste ztratili přístup ke svému účtu v aplikaci, kterou používají.
* Záznam o jaká data se sdílí aplikace je dobrá věc, se kterou máte. Data jsou přenositelné více než kdy dřív a je užitečné mít zrušte záznam o který s aplikací, které sdílí data.
* Rozhraní API vlastníky, kteří používají Azure AD pro OAuth rozhodnout přesně se může udělovat aplikacím oprávnění uživatelé a jaká oprávnění vyžadují souhlas správce. Pouze správci můžou udělit souhlas s obory větší a větší oprávnění, zatímco souhlasu uživatele působí na vlastní data uživatelů a možnosti.
* Když uživatel přidá nebo umožňuje aplikaci přístup ke svým datům, můžete tak můžete zobrazit sestavy auditu na portálu Azure portal k určení, jak se do adresáře přidávat aplikace auditované události.

Pokud stále chcete zabránit uživatelům ve vašem adresáři v registraci aplikace a přihlašování k aplikacím bez schválení správcem, existují dvě nastavení, která můžou změnit, chcete-li vypnout tyto možnosti:

* Chcete uživatelům zabránit v vyjádření souhlasu s aplikací na jejich vlastní účet:
  1. Na webu Azure Portal, přejděte [uživatelská nastavení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) části podnikových aplikací.
  2. Změna **uživatelé můžou udělit souhlas s aplikací, které přistupují k firemním datům jejich jménem** k **ne**. 
      > [!NOTE]
      > Pokud se rozhodnete vypnout souhlas uživatele, bude správce muset vyjádřit souhlas s všechny nové aplikace, které uživatel potřebuje používat.    
* Chcete-li uživatelé nemohli registrovat vlastní aplikace:
  1. Na webu Azure Portal, přejděte [uživatelská nastavení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) části Azure Active Directory
  2. Změna **uživatelé můžou registrovat aplikace** k **ne**.

> [!NOTE]
> Jen samotná společnost Microsoft používá výchozí konfigurace se uživatelé moct registrovat aplikace a dávat souhlas aplikacím svým jménem.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg

