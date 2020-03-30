---
title: Jak a proč se aplikace přidávají do Azure AD
titleSuffix: Microsoft identity platform
description: Co to znamená pro aplikaci, která má být přidána do Azure AD a jak se tam dostat?
services: active-directory
author: rwike77
manager: CelesteDG
ms.assetid: 3321d130-f2a8-4e38-b35e-0959693f3576
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: d47ed3a4cd4fbdcb69b956d3c8418f70a71cf44f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79263137"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Jak a proč se aplikace přidávají do Azure AD

Existují dvě reprezentace aplikací ve službě Azure AD:

* [Aplikační objekty](app-objects-and-service-principals.md#application-object) - Přestože existují [výjimky](#notes-and-exceptions), aplikační objekty lze považovat za definici aplikace.
* [Instanční objekty](app-objects-and-service-principals.md#service-principal-object) - lze považovat za instanci aplikace. Instanční objekty obecně odkazují na objekt aplikace a jeden objekt aplikace může odkazovat více instančních objektů napříč adresáři.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Co jsou aplikační objekty a odkud pocházejí?

[Objekty aplikací](app-objects-and-service-principals.md#application-object) můžete spravovat na webu Azure Portal prostřednictvím prostředí [Registrace aplikací.](https://aka.ms/appregistrations) Aplikační objekty popisují aplikaci do Služby Azure AD a lze je považovat za definici aplikace, což službě umožňuje vědět, jak vydávat tokeny do aplikace na základě jejího nastavení. Aplikační objekt bude existovat pouze v jeho domovském adresáři, i když je víceklientské aplikace podporující instanční objekty v jiných adresářích. Aplikační objekt může obsahovat některou z následujících informací (stejně jako další informace zde uvedené):

* Název, logo a vydavatel
* Přesměrovat identifikátory URI
* Tajné klíče (symetrické a/nebo asymetrické klíče používané k ověření aplikace)
* Závislosti rozhraní API (OAuth)
* Publikovaná api/zdroje/obory (OAuth)
* Role aplikací (RBAC)
* Metadata a konfigurace přiřazování a konfiguraci
* Zřizování metadat a konfigurace uživatele
* Metadata a konfigurace serveru proxy

Aplikační objekty lze vytvářet prostřednictvím více cest, včetně:

* Registrace aplikací na webu Azure Portal
* Vytvoření nové aplikace pomocí Sady Visual Studio a její konfigurace pro použití ověřování Azure AD
* Když správce přidá aplikaci z galerie aplikací (která také vytvoří instanční objekt)
* Vytvoření nové aplikace pomocí rozhraní Microsoft Graph API nebo Prostředí PowerShell
* Mnoho dalších, včetně různých vývojářských prostředí v Azure a v prostředí průzkumníka rozhraní API napříč vývojářskými centry

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Co jsou instanční objekty a odkud pocházejí?

[Objekty zabezpečení služeb](app-objects-and-service-principals.md#service-principal-object) můžete spravovat na portálu Azure prostřednictvím prostředí enterprise [aplikací.](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) Instanční objekty jsou to, co řídí aplikace připojení k Azure AD a lze považovat za instanci aplikace ve vašem adresáři. Pro danou aplikaci může mít maximálně jeden aplikační objekt (který je registrován v domovském adresáři) a jeden nebo více objektů instančního objektu představujících instance aplikace v každém adresáři, ve kterém pracuje. 

Instanční objekt může zahrnovat:

* Odkaz zpět na objekt aplikace prostřednictvím vlastnosti ID aplikace
* Záznamy o přiřazení rolí aplikace místních uživatelů a skupin
* Záznamy o oprávněních místních uživatelů a správců udělených aplikaci
  * Například: oprávnění pro aplikaci pro přístup k e-mailu konkrétního uživatele
* Záznamy místních zásad včetně zásad podmíněného přístupu
* Záznamy alternativních místních nastavení aplikace
  * Pravidla transformace deklarací
  * Mapování atributů (zřizování uživatelů)
  * Role aplikací specifických pro adresář (pokud aplikace podporuje vlastní role)
  * Název nebo logo specifické pro adresář

Stejně jako objekty aplikace, instanční objekty lze také vytvořit prostřednictvím více cest, včetně:

* Když se uživatelé přihlásí k aplikaci jiného výrobce integrované s Azure AD
  * Během přihlášení jsou uživatelé požádáni o udělení oprávnění k přístupu k jejich profilu a dalším oprávněním. První osoba, která udělit souhlas způsobí, že instanční objekt, který představuje aplikaci, které mají být přidány do adresáře.
* Když se uživatelé přihlásí k online službám Microsoftu, jako je [Office 365](https://products.office.com/)
  * Když se přihlásíte k odběru Office 365 nebo začnete zkušební verzi, vytvoří se v adresáři představující různé služby, které se používají k poskytování všech funkcí přidružených k Office 365, jeden nebo více instančních objektů.
  * Některé služby Office 365, jako je SharePoint, průběžně vytvářejí instanční objekty, aby umožňovaly zabezpečenou komunikaci mezi součástmi včetně pracovních postupů.
* Když správce přidá aplikaci z galerie aplikací (to také vytvoří základní objekt aplikace)
* Přidání aplikace pro použití [proxy aplikace Azure AD](/azure/active-directory/manage-apps/application-proxy)
* Připojení aplikace pro jednotné přihlašování pomocí saml nebo hesla jednotného přihlašování (SSO)
* Programově prostřednictvím rozhraní Microsoft Graph API nebo PowerShellu

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Jak se vzájemně vztahují aplikační objekty a instanční objekty?

Aplikace má jeden objekt aplikace ve svém domovském adresáři, na který odkazuje jeden nebo více instančních objektů v každém adresáři, kde pracuje (včetně domovského adresáře aplikace).

![Zobrazuje vztah mezi objekty aplikace a objekty zabezpečení služeb.][apps_service_principals_directory]

V předchozím diagramu společnost Microsoft udržuje dva adresáře interně (zobrazené vlevo), které používá k publikování aplikací:

* Jeden pro Aplikace microsoftu (adresář služeb Microsoft)
* Jeden pro předem integrované aplikace třetích stran (adresář galerie aplikací)

Vydavatelé/dodavatelé aplikací, kteří se integrují s Azure AD, musí mít adresář publikování (zobrazený vpravo jako "Nějaký adresář SaaS").

Aplikace, které přidáte sami (reprezentované jako **Aplikace (vaše)** v diagramu) zahrnují:

* Aplikace, které jste vyvinuli (integrované s Azure AD)
* Aplikace, které jste připojili pro jednotné přihlašování
* Aplikace, které jste publikovali pomocí proxy aplikace Azure AD

### <a name="notes-and-exceptions"></a>Poznámky a výjimky

* Ne všechny objekty zabezpečení služeb odkazují zpět na objekt aplikace. Když azure ad byl původně sestaven služby poskytované aplikacím byly omezenější a instanční objekt byl dostatečný pro vytvoření identity aplikace. Původní instanční objekt služby byl blíže k účtu služby Windows Server Active Directory. Z tohoto důvodu je stále možné vytvořit instanční objekty prostřednictvím různých cest, jako je například pomocí prostředí Azure AD PowerShell, bez předchozího vytvoření objektu aplikace. Rozhraní Microsoft Graph API vyžaduje objekt aplikace před vytvořením instančního objektu.
* Ne všechny výše popsané informace jsou aktuálně vystaveny programově. V ui jsou k dispozici pouze následující:
  * Pravidla transformace deklarací
  * Mapování atributů (zřizování uživatelů)
* Podrobnější informace o objektu instančního objektu a objektech aplikace naleznete v referenční dokumentaci rozhraní MICROSOFT Graph API:
  * [Aplikace](https://docs.microsoft.com/graph/api/resources/application?view=graph-rest-1.0)
  * [Instanční objekt](https://docs.microsoft.com/graph/api/resources/serviceprincipal?view=graph-rest-beta)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Proč se aplikace integrují s Azure AD?

Aplikace se přidávají do Azure AD, aby využily jednu nebo více služeb, které poskytuje, včetně:

* Ověřování a autorizace aplikace
* Ověřování a autorizace uživatele
* Připřipomene pomocí federace nebo hesla
* Zřizování a synchronizace uživatelů
* Řízení přístupu založené na rolích – použití adresáře k definování rolí aplikace k provádění kontrol autorizací na základě rolí v aplikaci
* Autorizační služby OAuth – používá office 365 a další aplikace Microsoftu k autorizaci přístupu k rozhraním API/prostředkům
* Publikování a proxy aplikace - Publikování aplikace z privátní sítě na internet

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Kdo má oprávnění k přidávání aplikací do instance Azure AD?

I když existují některé úkoly, které mohou dělat pouze globální správci (například přidávání aplikací z galerie aplikací a konfigurace aplikace pro použití proxy aplikace) ve výchozím nastavení mají všichni uživatelé ve vašem adresáři práva k registraci objektů aplikace, které vyvíjejí, a uvážení, které aplikace sdílejí nebo udělují přístup k jejich organizačním datům prostřednictvím souhlasu. Pokud je osoba prvním uživatelem ve vašem adresáři, který se přihlásí k aplikaci a udělí souhlas, vytvoří ve vašem tenantovi instanční objekt. v opačném případě budou informace o udělení souhlasu uloženy na existujícím instančním objektu.

Povolení uživatelům zaregistrovat se a souhlas s aplikacemi může zpočátku znít, ale mějte na paměti následující:


* Aplikace byly schopny využít Windows Server Active Directory pro ověřování uživatelů po mnoho let, aniž by bylo nutné, aby aplikace byla registrována nebo zaznamenána v adresáři. Nyní organizace bude mít lepší viditelnost přesně, kolik aplikací používají adresář a za jakým účelem.
* Delegování těchto odpovědností na uživatele neguje potřebu procesu registrace a publikování aplikací řízených správcem. Se službou ADFS (Active Directory Federation Services) bylo pravděpodobné, že správce musel přidat aplikaci jako předávající stranu jménem svých vývojářů. Nyní mohou vývojáři samoobsluhu.
* Uživatelé, kteří se přihlašují k aplikacím pomocí svých organizačních účtů pro obchodní účely, je dobrá věc. Pokud následně opustí organizaci, automaticky ztratí přístup ke svému účtu v aplikaci, kterou používali.
* Mít záznam o tom, jaká data byla sdílena s jakou aplikací je dobrá věc. Data jsou přenosnější než kdy dříve a je užitečné mít jasný záznam o tom, kdo sdílel jaká data s jakými aplikacemi.
* Vlastníci rozhraní API, kteří používají Azure AD pro OAuth, přesně rozhodnou, jaká oprávnění mohou uživatelé aplikacím udělit a která oprávnění vyžadují, aby správce souhlasil. Pouze správci mohou souhlasit s většími obory a významnějšími oprávněními, zatímco souhlas uživatele je vymezen na vlastní data a možnosti uživatelů.
* Když uživatel přidá nebo povolí aplikaci přístup ke svým datům, událost může být auditována, takže můžete zobrazit sestavy auditu na webu Azure Portal a určit, jak byla aplikace přidána do adresáře.

Pokud chcete uživatelům ve vašem adresáři přesto zabránit v registraci aplikací a v přihlášení k aplikacím bez souhlasu správce, můžete tyto možnosti vypnout dvěma nastaveními:

* Chcete-li uživatelům zabránit v tom, aby s aplikacemi souhlasili svým vlastním jménem:
  1. Na webu Azure Portal přejděte do části [Uživatelská nastavení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) v části Podnikové aplikace.
  2. **Změnit Uživatelé mohou souhlasit s tím, aby aplikace přistupující k firemním datům jejich jménem** **ne**.
     
     > [!NOTE]
     > Pokud se rozhodnete vypnout souhlas uživatele, bude správce muset souhlasit s jakoukoli novou aplikací, kterou uživatel potřebuje použít.

* Chcete-li uživatelům zabránit v registraci vlastních aplikací:
  1. Na webu Azure Portal přejděte do části [Nastavení uživatelů](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) v části Azure Active Directory.
  2. **Změnit Uživatelé mohou zaregistrovat aplikace** na **ne**.

> [!NOTE]
> Společnost Microsoft sama používá výchozí konfiguraci s uživateli, kteří mohou zaregistrovat aplikace a souhlas s aplikacemi vlastním jménem.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg
