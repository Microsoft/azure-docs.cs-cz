---
title: Správa aplikací pomocí služby Azure Active Directory | Microsoft Docs
description: Tento článek výhod integrace s místními, cloudu a aplikace SaaS Azure Active Directory.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: 1a8db218309eb48650443f1c9d57fe1ecdf04a6a
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="managing-applications-with-azure-active-directory"></a>Správa aplikací pomocí služby Azure Active Directory
Mimo pracovní postup nebo obsahu firmy mají dva základní požadavky pro všechny aplikace:

1. Zvýšení produktivity, aplikace by měla být snadno vyhledatelné a přístup
2. Zabezpečení a zásad správného řízení povolit, organizace musí řízení a dohled nad přístup a kdo přistupuje ve skutečnosti každá aplikace

V celém světě cloudových aplikací, nejlépe jde tohoto dosáhnout pomocí identity k řízení "*KDO může provádět co*."

V oblasti výpočetních terminologie:

* *Kdo* se označuje jako *identity* -správu uživatelů a skupin
* *Co* se označuje jako *správy přístupu* – správu přístup k chráněným prostředkům

I komponent společně se označují jako *Identity a řízení přístupu (IAM)*, která je definována [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) skupiny jako "*oborů zabezpečení, umožňující správné jednotlivce pro přístup k prostředkům správné vpravo časy správné z důvodů*".

V pořádku, a co je problém? Pokud je IAM *nespravovaná* na jednom místě s integrované řešení:

* Správci identity nemusí jednotlivě vytvářet a aktualizovat uživatelské účty ve všech aplikacích samostatně, aktivitu redundantní a časově náročné.
* Uživatelé mají k pamatovat více přihlašovací údaje pro přístup k aplikacím, které potřebují k práci s. Výsledkem je uživatelé zpravidla zapište své heslo nebo použít dalších řešení pro správu hesel. Tyto možnosti představují další data bezpečnostní rizika.
* Redundantní, časově náročné aktivity snížit čas uživatelé a správci potřebují k práci na obchodní aktivity, které zvýšit řádku dolní vaší firmy.

Ano co obecně brání organizace z přijetí integrované řešení IAM?

* Většinu technických řešení jsou založené na platformách softwaru, které je potřeba nasadit a přizpůsobit každé organizace pro své vlastní aplikace.
* Cloudové aplikace jsou často přijata s vyšší rychlostí než IT organizace může integrovat existující IAM řešení.
* Zabezpečení a monitorování nástrojů vyžadují další přizpůsobení a integrace zajistit komplexní E2E scénáře.

## <a name="azure-active-directory-integrated-with-applications"></a>Azure Active Directory integrovat s aplikacemi
Azure Active Directory je komplexní Identity společnosti Microsoft jako řešení služby (IDaaS) který:

* Jako cloudová služba umožňuje IAM 
* Poskytuje správu centrální přístupu, jednotné přihlašování (SSO) a vytváření sestav 
* Podporuje správu integrovaného přístupu k [tisíce aplikace](https://azure.microsoft.com/marketplace/active-directory/) v galerii aplikací, včetně služby Salesforce, Google Apps, pole, Concur a další. 

S Azure Active Directory všechny aplikace publikujete pro partnery a zákazníky (obchodní nebo příjemce) mají stejnou identitu a přístup k možnosti správy.<br> To vám umožňuje výrazně snížit provozní náklady.

Co když potřebujete implementovat aplikaci, která ještě není uveden v galerii aplikací? Přestože se trochu náročnější než nakonfigurovat jednotné přihlašování pro aplikace v galerii aplikací, Azure AD poskytuje průvodce, který vám pomůže s konfigurací.

Hodnota Azure AD překročí "právě" cloudové aplikace. Můžete ji použít i s místním aplikacím tím, že poskytuje zabezpečený vzdálený přístup. Zabezpečený vzdálený přístup můžete eliminovat potřebu sítě VPN nebo jiných implementacích správy tradiční vzdáleného přístupu.

Zadáním správy centrálního přístupu a jednotné přihlašování (SSO) pro všechny aplikace Azure AD poskytuje řešení pro zabezpečení hlavní data a produktivitu problémy.

* Uživatelé můžou používat více aplikací s jeden přihlašování udělíte více času k příjmu generování nebo obchodní aktivity operace provádí.
* Správci identity můžete spravovat přístup k aplikacím na jednom místě.

Výhodou pro uživatele a pro vaši společnost je zřejmé. Podívejme bližší pohled na výhody pro správce identit a v organizaci.

## <a name="integrated-application-benefits"></a>Výhody integrované aplikace
Proces přihlášení SSO má dva kroky:

* Ověřování, proces ověření identity uživatele.
* Autorizace, rozhodnutí povolit nebo blokovat přístup k prostředku s zásady přístupu.

Při správě aplikací a povolení jednotného přihlašování pomocí služby Azure AD:

* Ověřování se provádí na uživatele v místě (například AD) nebo účet Azure AD.
* Autorizace se spouští na Azure AD přiřazení a ochrany zásady zajistíte činnost koncového uživatele konzistentní a umožňuje přidat přiřazení, umístění a podmínky MFA na všechny aplikace, bez ohledu na jeho vnitřní funkce.

Je důležité si uvědomit, který způsob, jakým budou autorizaci přijaty na cílové aplikace, se liší v závislosti na tom, jak se aplikace integrované s Azure AD.

* **Aplikace je předem integrovaných poskytovatelem služby** jako je Office 365 a Azure, jedná se o aplikace založena přímo na Azure AD a spoléhat na něm pro jejich komplexní funkce pro správu identit a přístupu. Pomocí informací v adresáři a vystavování tokenů je povolen přístup k těmto aplikacím.
* **Aplikace je předem integrovaných společností Microsoft a vlastních aplikací** Toto jsou nezávislé cloudové aplikace, které spoléhají na adresář interní aplikace a může pracovat nezávisle na Azure AD. Po vydání specifické pro aplikaci pověření namapován na účet aplikace je povolen přístup k těmto aplikacím. V závislosti na možnosti aplikací může být pověření federační token nebo uživatelské jméno a heslo pro účet, který byl dříve zajištěny v aplikaci.
* **Místní aplikace** aplikacích publikovaných prostřednictvím proxy aplikace Azure AD, především povolení přístupu k místním aplikacím. Tyto aplikace se spoléhají na centrální místní adresář, jako je Windows Server Active Directory. Ve spouštěcí proxy server při doručování obsahu aplikace do koncového uživatele respektováním místní přihlášení požadavek je povolený přístup k těmto aplikacím.

Například pokud uživatel připojí vaší organizace, budete muset vytvořit účet pro uživatele ve službě Azure AD pro primární operace přihlášení. Pokud tento uživatel vyžaduje přístup ke spravované aplikaci, například služby Salesforce, budete muset taky vytvořit účet pro tohoto uživatele v Salesforce a tu propojit na účet Azure, aby jednotného přihlašování k práci. Když uživatel opustí vaši organizaci, se doporučuje odstranit účet Azure AD a všechny účty protějškem v IAM ukládá aplikací, které uživatel měl přístup k.

## <a name="access-detection"></a>Detekce přístup
V rámci moderní firmy IT oddělení nemají často informace všech cloudových aplikací, které jsou používány. Ve spojení s Cloud App Discovery Azure AD poskytuje řešení ke zjištění těchto aplikací.

## <a name="account-management"></a>Správa účtů
Správa účtů v různých aplikacích obvyklým je ruční proces provádí IT nebo pracovníci podpory v organizaci. Správa účtů Azure AD plně automatizuje napříč aplikace integrované poskytovateli služeb a aplikací předem integrovaných Microsoft podpora zřizování automatizované uživatelů nebo SAML JIT zřizování.

## <a name="automated-user-provisioning"></a>Zřizování automatizované uživatelů
Některé aplikace poskytují rozhraní automatizace pro vytváření a odebrání (nebo deaktivace) účtů. Pokud zprostředkovatele nabízí takového rozhraní, je využít Azure AD. To snižuje provozní náklady, protože úlohy správy dojít automaticky a zvyšuje zabezpečení vašeho prostředí, protože se sníží riziko neoprávněného přístupu.

## <a name="access-management"></a>Správa přístupu
S Azure AD můžete spravovat přístup k aplikacím pomocí jednotlivých nebo pravidlo řízené přiřazení. Můžete taky delegovat přístup správu odpovídajícím lidem v organizaci zajistit nejlepší dohledu a snížení zatížení na technickou podporu.

## <a name="on-premises-applications"></a>Místní aplikace
Proxy integrované aplikace umožňuje publikování aplikací pro vaše uživatele, což je místní i konzistentní přístup k prostředí s moderní cloudové aplikace a výhody z možnosti monitorování, vytváření sestav a zabezpečení služby Azure AD.

## <a name="reporting-and-monitoring"></a>Monitorování a vytváření sestav
Azure AD poskytuje předem integrovaných sestav a monitorování funkce, které vám umožní vědět, kdo má přístup k aplikacím a pokud je ve skutečnosti používány.

## <a name="related-capabilities"></a>Související možnosti
S Azure AD můžete zabezpečit vaše aplikace s zásady granulární přístupu a předem integrované MFA. Další informace o Azure MFA najdete [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Začínáme
Abyste mohli začít, integrace aplikací s Azure AD, prohlédněte si [Příručka Začínáme integrace Azure Active Directory s aplikacemi, získávání](plan-an-application-integration.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](../active-directory-apps-index.md)
* [Podrobný postup nasazení plán pro jednotné přihlašování do aplikace SaaS](http://aka.ms/ssodeploymentplan)

