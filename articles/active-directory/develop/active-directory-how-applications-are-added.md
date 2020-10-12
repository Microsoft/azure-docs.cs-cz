---
title: Jak a proč se aplikace přidávají do Azure AD
titleSuffix: Microsoft identity platform
description: Co to znamená, když se aplikace přidá do Azure AD a jak se tam dostanou?
services: active-directory
author: rwike77
manager: CelesteDG
ms.service: active-directory
ms.subservice: develop
ms.topic: conceptual
ms.workload: identity
ms.date: 11/26/2019
ms.author: ryanwi
ms.custom: aaddev
ms.reviewer: lenalepa, sureshja
ms.openlocfilehash: bccdb830fba4d55266dc9eff8f06c5ac4e3f2a34
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90706179"
---
# <a name="how-and-why-applications-are-added-to-azure-ad"></a>Jak a proč se aplikace přidávají do Azure AD

Ve službě Azure AD existují dvě reprezentace aplikací:

* [Aplikační objekty](app-objects-and-service-principals.md#application-object) – i když existují [výjimky](#notes-and-exceptions), objekty aplikace lze považovat za definici aplikace.
* [Instanční objekty](app-objects-and-service-principals.md#service-principal-object) – lze považovat za instanci aplikace. Instanční objekty obecně odkazují na objekt aplikace a jeden objekt aplikace může být odkazován více instančními objekty v adresáři.

## <a name="what-are-application-objects-and-where-do-they-come-from"></a>Co jsou objekty aplikace a odkud pocházejí?

Můžete spravovat [objekty aplikace](app-objects-and-service-principals.md#application-object) v Azure Portal prostřednictvím možností [Registrace aplikací](https://aka.ms/appregistrations) . Aplikační objekty popisují aplikaci do služby Azure AD a je možné ji považovat za definici aplikace, což službě umožňuje zjistit, jak vydávat tokeny aplikaci na základě jejího nastavení. Objekt aplikace bude existovat pouze v domovském adresáři, i v případě, že se jedná o víceklientské aplikace podporující instanční objekty v jiných adresářích. Objekt aplikace může obsahovat některé z následujících (a také další informace, které zde nejsou zmíněny):

* Název, logo a Vydavatel
* Identifikátory URI pro přesměrování
* Tajné kódy (symetrické a/nebo asymetrické klíče používané k ověření aplikace)
* Závislosti rozhraní API (OAuth)
* Publikovaná rozhraní API/prostředky/obory (OAuth)
* Aplikační role (RBAC)
* Konfigurace a metadata jednotného přihlašování
* Metadata a konfigurace zřizování uživatelů
* Metadata a konfigurace proxy serveru

Aplikační objekty lze vytvořit pomocí několika cest, včetně:

* Registrace aplikací v Azure Portal
* Vytvoření nové aplikace pomocí sady Visual Studio a její konfigurace pro použití ověřování Azure AD
* Když správce přidá aplikaci z Galerie aplikací (tím se vytvoří také instanční objekt)
* Vytvoření nové aplikace pomocí rozhraní Microsoft Graph API nebo PowerShellu
* Mnoho dalších, včetně různých vývojářských prostředí v Azure a v Průzkumníkovi rozhraní API napříč centry pro vývojáře

## <a name="what-are-service-principals-and-where-do-they-come-from"></a>Co jsou instanční objekty a odkud přicházejí z provozu?

[Objekty služby](app-objects-and-service-principals.md#service-principal-object) v Azure Portal můžete spravovat prostřednictvím prostředí [podnikových aplikací](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/AllApps/menuId/) . Instanční objekty se řídí aplikací připojujícími se k Azure AD a je možné ji považovat za instanci aplikace ve vašem adresáři. V případě jakékoli aplikace může mít maximálně jeden aplikační objekt (který je zaregistrován v "domovském" adresáři) a jeden nebo více instančních objektů, které představují instance aplikace v každém adresáři, v němž funguje. 

Instanční objekt může zahrnovat:

* Odkaz zpátky na objekt aplikace pomocí vlastnosti ID aplikace
* Záznamy aplikace místních uživatelů a skupin – přiřazení rolí
* Záznamy oprávnění místního uživatele a správce udělená aplikaci
  * Příklad: oprávnění pro aplikaci pro přístup k e-mailu konkrétního uživatele
* Záznamy místních zásad včetně zásad podmíněného přístupu
* Záznamy alternativních místních nastavení pro aplikaci
  * Pravidla transformace deklarací identity
  * Mapování atributů (zřizování uživatelů)
  * Role aplikací specifické pro adresář (Pokud aplikace podporuje vlastní role)
  * Název nebo logo specifické pro adresář

Podobně jako aplikační objekty lze instanční objekty vytvořit také prostřednictvím několika cest, včetně:

* Když se uživatelé přihlásí k aplikaci třetí strany integrované s Azure AD
  * Během přihlašování se uživatelům zobrazí výzva, aby aplikaci udělili oprávnění k přístupu k jejich profilu a dalším oprávněním. První osoba, která by dala udělit souhlas, způsobí instanční objekt, který představuje aplikaci, která se má přidat do adresáře.
* Když se uživatelé přihlásí k Microsoft online služby jako [Microsoft 365](https://products.office.com/)
  * Když se přihlásíte k odběru Microsoft 365 nebo zahájíte zkušební období, v adresáři představujících různé služby, které se používají k doručování všech funkcí přidružených k Microsoft 365, se vytvoří jeden nebo více instančních objektů.
  * Některé Microsoft 365 služby, jako je SharePoint, vytvářejí instanční objekty průběžně a umožňují zabezpečenou komunikaci mezi součástmi včetně pracovních postupů.
* Když správce přidá aplikaci z Galerie aplikací (tím se vytvoří také základní objekt aplikace)
* Přidání aplikace pro použití [proxy aplikací služby AD Azure](../manage-apps/application-proxy.md)
* Připojení aplikace pro jednotné přihlašování pomocí protokolu SAML nebo jednotného přihlašování k heslu (SSO)
* Prostřednictvím kódu programu Microsoft Graph rozhraní API nebo PowerShell

## <a name="how-are-application-objects-and-service-principals-related-to-each-other"></a>Jak jsou objekty aplikace a instanční objekty vzájemně propojené?

Aplikace obsahuje jeden objekt aplikace v jeho domovském adresáři, na který odkazuje jeden nebo více instančních objektů v každém adresáři, kde je provozován (včetně domovského adresáře aplikace).

![Zobrazuje vztah mezi objekty aplikace a instančními objekty.][apps_service_principals_directory]

V předchozím diagramu společnost Microsoft udržuje dva adresáře interně (zobrazené vlevo), které používá k publikování aplikací:

* Jedna pro aplikace Microsoftu (adresář služeb Microsoftu)
* Jedna pro předem integrované aplikace třetích stran (adresář Galerie aplikací)

Vydavatelé aplikací/dodavatelé, kteří se integrují se službou Azure AD, musí mít adresář pro publikování (zobrazuje se napravo jako "část adresáře SaaS").

Aplikace, které přidáte sami (v diagramu jsou reprezentovány jako **aplikace)** , zahrnují:

* Aplikace, které jste vyvinuli (integrovány se službou Azure AD)
* Aplikace, které jste připojili pro jednotné přihlašování
* Aplikace, které jste publikovali pomocí proxy aplikace služby Azure AD

### <a name="notes-and-exceptions"></a>Poznámky a výjimky

* Ne všechny instanční objekty odkazují zpátky na objekt aplikace. Když je služba Azure AD původně sestavená o služby poskytované aplikacím, byly omezenější a objekt služby byl pro vytvoření identity aplikace dostačující. Původní instanční objekt byl blíž v obrazci k účtu služby Windows Server Active Directory. Z tohoto důvodu je stále možné vytvářet instanční objekty prostřednictvím různých cest, jako je například použití Azure AD PowerShellu, aniž byste nejdřív vytvořili objekt aplikace. Rozhraní Microsoft Graph API vyžaduje aplikační objekt před vytvořením instančního objektu.
* Ne všechny výše popsané informace jsou aktuálně vystaveny prostřednictvím kódu programu. Následující jsou k dispozici pouze v uživatelském rozhraní:
  * Pravidla transformace deklarací identity
  * Mapování atributů (zřizování uživatelů)
* Podrobnější informace o instančním objektu a objektech aplikace najdete v referenční dokumentaci k rozhraní Microsoft Graph API:
  * [Aplikace](/graph/api/resources/application?view=graph-rest-1.0)
  * [Instanční objekt](/graph/api/resources/serviceprincipal?view=graph-rest-beta)

## <a name="why-do-applications-integrate-with-azure-ad"></a>Proč se aplikace integrují s Azure AD?

Do Azure AD se přidají aplikace, které budou využívat jednu nebo víc služeb, které poskytuje, včetně:

* Ověřování a autorizace aplikací
* Ověřování a autorizace uživatelů
* Jednotné přihlašování pomocí federace nebo hesla
* Zřizování a synchronizace uživatelů
* Řízení přístupu na základě rolí – pomocí adresáře definujte aplikační role k provádění kontrol autorizace na základě rolí v aplikaci.
* Autorizační služba OAuth – používá Microsoft 365 a další aplikace Microsoftu k autorizaci přístupu k rozhraním API nebo prostředkům.
* Publikování a proxy aplikací – publikování aplikace z privátní sítě na Internet

## <a name="who-has-permission-to-add-applications-to-my-azure-ad-instance"></a>Kdo má oprávnění přidávat aplikace do své instance Azure AD?

I když existují některé úlohy, které můžou provádět jenom globální správci (například přidávají aplikace z Galerie aplikací a konfigurovat aplikaci pro používání proxy aplikací), ve výchozím nastavení mají všichni uživatelé ve vašem adresáři práva k registraci aplikačních objektů, které vyvíjí a připravují aplikace, které sdílí nebo poskytují přístup k datům organizace prostřednictvím souhlasu. Pokud je osoba prvním uživatelem v adresáři pro přihlášení k aplikaci a udělení souhlasu, která vytvoří instanční objekt ve vašem tenantovi; v opačném případě se informace o udělení souhlasu uloží na existující instanční objekt.

Umožnění, aby se uživatelé mohli registrovat a odsouhlasit s aplikacemi, se můžou na začátku zvuk, ale pamatujte na toto:


* Aplikace byly schopné využívat Windows Server Active Directory pro ověřování uživatelů po celou dobu spousty let, aniž by bylo nutné aplikaci registrovat nebo zaznamenat v adresáři. Organizace teď bude mít lepší přehled o tom, kolik aplikací používá adresář a za jaký účel.
* Delegování těchto odpovědností uživatelům se bude vyžadovat proces registrace a publikování aplikace řízené správcem. U Active Directory Federation Services (AD FS) (ADFS) bylo nejspíš, že správce musel přidat aplikaci jako předávající stranu jménem svých vývojářů. Vývojáři teď můžou samoobslužné služby.
* Dobrým účelem jsou uživatelé, kteří se přihlašují k aplikacím pomocí jejich účtů organizace pro obchodní účely. Pokud následně opustí organizaci, budou automaticky ztratit přístup k jejich účtu v aplikaci, kterou používali.
* Záznam o tom, která data byla sdílena s aplikací, která je dobrá. Data jsou lépe přenosná než kdy dřív a je užitečné mít jasný záznam, který sdílí data, která aplikace používají.
* Vlastníci rozhraní API, kteří používají Azure AD pro OAuth, rozhodují přesně o tom, jaká oprávnění uživatelé můžou udělit aplikacím a jaká oprávnění vyžaduje, aby správce souhlasil. Pouze správci mohou vyjádřit souhlas s většími obory a důležitějšími oprávněními, zatímco souhlas uživatele je vymezen na vlastní data a možnosti uživatelů.
* Když uživatel přidá nebo povolí aplikaci přístup ke svým datům, může být událost auditována, takže můžete zobrazit sestavy auditu v rámci Azure Portal, abyste zjistili, jak byla aplikace přidána do adresáře.

Pokud stále chcete uživatelům ve vašem adresáři zabránit v registraci aplikací a přihlášení k aplikacím bez schválení správcem, můžete pro tyto funkce vypnout dvě nastavení:

* Chcete-li zabránit uživatelům v posílání aplikací vlastním jménem:
  1. V Azure Portal v části podnikové aplikace otevřete oddíl [uživatelská nastavení](https://portal.azure.com/#blade/Microsoft_AAD_IAM/StartboardApplicationsMenuBlade/UserSettings/menuId/) .
  2. Změna **uživatelů může vyjádřit souhlas s aplikacemi, které přistupují k firemním datům za jejich jménem** **.**
     
     > [!NOTE]
     > Pokud se rozhodnete vypnout souhlas s uživatelem, bude správce vyžadovat souhlas s každou novou aplikací, kterou uživatel potřebuje použít.

* Zabránění uživatelům v registraci vlastních aplikací:
  1. V Azure Portal v části Azure Active Directory klikněte na část [nastavení uživatele](https://portal.azure.com/#blade/Microsoft_AAD_IAM/ActiveDirectoryMenuBlade/UserSettings) .
  2. Změna **uživatelů může registrovat aplikace** na **ne**.

> [!NOTE]
> Microsoft sám používá výchozí konfiguraci s uživateli, kteří můžou registrovat aplikace a souhlasit s aplikacemi vlastním jménem.

<!--Image references-->
[apps_service_principals_directory]:../media/active-directory-how-applications-are-added/HowAppsAreAddedToAAD.jpg