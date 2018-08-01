---
title: Správa aplikací pomocí Azure Active Directory | Dokumentace Microsoftu
description: Tento článek výhody integrace Azure Active Directory s vaší místní a cloudové aplikace a aplikace SaaS.
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/15/2018
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: bf53829a2d2578132f9a3595c0bac5e8eb588916
ms.sourcegitcommit: f86e5d5b6cb5157f7bde6f4308a332bfff73ca0f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/31/2018
ms.locfileid: "39366767"
---
# <a name="managing-applications-with-azure-active-directory"></a>Správa aplikací pomocí Azure Active Directory
Nad rámec samotný pracovní postup nebo obsahu firmy mají dva základní požadavky pro všechny aplikace:

1. Zvýšení produktivity, aplikace by měly být snadno zjistit a přístup
2. Povolit zabezpečení a zásad správného řízení, tato organizace požaduje řízení a dohled nad kdo má přístup k a kdo je ve skutečnosti používá každá aplikace

V řadě cloudových aplikací, nejlépe jde tohoto dosáhnout pomocí identity na ovládací prvek "*KDO může co*."

Při výpočtu terminologie:

* *Kdo* se označuje jako *identity* -správu uživatelů a skupin
* *Co* se označuje jako *správy přístupu* – řízení přístupu k chráněným prostředkům

Obě komponenty společně se označují jako *identit a řízení přístupu (IAM)*, který je definován [Gartner](http://www.gartner.com/it-glossary/identity-and-access-management-iam) jako "*obor zabezpečení, která umožňuje jednotlivcům vpravo přístup k prostředkům vpravo na pravé straně časy doprava z důvodu*".

Dobře, takže v čem je problém? Pokud je IAM *nespravovaných* na jednom místě pomocí integrované řešení:

* Správci identity mají jednotlivě vytvářet a aktualizovat uživatelské účty ve všech aplikacích samostatně, aktivity, jako je redundantní a časově náročné.
* Uživatelé se muset učit nazpaměť různých přihlašovacích údajů pro přístup k aplikacím, které potřebují k práci s. Díky tomu uživatelé mají tendenci zapište svá hesla nebo používání jiných řešení pro správu hesel. Tyto alternativy zavést další data bezpečnostní rizika.
* Redundantní, časově náročné aktivity snížit dobu uživatelé a správci potřebují k práci na obchodní činnosti, které zvyšují zhoršit vaši firmu.

Ano co obecně organizacím brání v zavedení integrovaná řešení IAM?

* Většina technická řešení jsou založeny na softwarové platformy, které je potřeba nasadit a upravit každé organizace pro jejich vlastních aplikací.
* Cloudové aplikace jsou často přijata s vyšší rychlostí než IT v organizaci můžete integrovat se stávajícími řešeními IAM.
* Zabezpečení a monitorování nástroje vyžadují další přizpůsobení a integraci s komplexní scénáře E2E dosáhnout.

## <a name="azure-active-directory-integrated-with-applications"></a>Azure Active Directory integrován v aplikacích
Azure Active Directory je komplexní Identity od Microsoftu jako služba (IDaaS) řešení, které:

* Jako cloudová služba umožňuje IAM 
* Poskytuje centrální řízení přístupu, jednotné přihlašování (SSO) a vytváření sestav 
* Podporuje integrované access management pro službu [nepřeberným množstvím aplikací](https://azure.microsoft.com/marketplace/active-directory/) v galerii aplikací, včetně Salesforce, Google Apps, Box, Concur a další. 

S Azure Active Directory všechny aplikace, můžete publikovat pro partnery a zákazníky (firmy nebo spotřebitele) mají stejnou identitu a přístup k funkcím správy.<br> To umožňuje výrazně snížit provozní náklady.

Co když budete muset implementovat aplikaci, která ještě není uvedená v galerii aplikací? Přestože se jedná o něco náročnější než nakonfigurovat jednotné přihlašování pro aplikace z Galerie aplikací, Azure AD vám poskytne průvodce, který vám pomůže s konfigurací.

Hodnota Azure AD jde nad rámec "pouze" cloudových aplikací. Můžete také ji použijete s místním aplikacím tím, že poskytuje zabezpečený vzdálený přístup. Zabezpečený vzdálený přístup může eliminovat potřebu pro sítě VPN nebo v jiných implementacích správy tradiční vzdáleného přístupu.

Azure AD poskytuje správu centrálního přístupu a jednotné přihlašování (SSO) pro všechny aplikace a nabízí řešení pro hlavní data zabezpečení a produktivitu problémy.

* Uživatelé můžou používat více aplikací jeden přihlašování poskytuje tak více času na příjem při generování nebo obchodní aktivity operace provést.
* Identita správci můžou spravovat přístup k aplikacím na jednom místě.

Výhoda pro uživatele a pro vaši společnost je zřejmý. Pojďme se na to bližší pohled na výhody pro správce identit a organizaci.

## <a name="integrated-application-benefits"></a>Výhody integrované aplikace
Jednotné přihlašování proces má dva kroky:

* Ověřování, proces ověření identity uživatele.
* Autorizace, rozhodnutí o povolení nebo blokování přístupu k prostředku se zásady přístupu.

Při použití služby Azure AD ke správě aplikací a povolení jednotného přihlašování:

* Ověřování se provádí na on-premises (například AD) nebo účet Azure AD daného uživatele.
* Autorizační spouští na Azure AD přiřazení a ochranu zásadu, kterou zajišťuje konzistentní koncovým a umožňuje přidat přiřazení, umístění a podmínky MFA na všechny aplikace, bez ohledu na jeho vnitřní funkce.

Je důležité pochopit, který způsob, jakým je povolení plnění na cílové aplikace, se liší v závislosti na tom, jak se aplikace integrované s Azure AD.

* **Předem integrovaných aplikací od poskytovatele služeb** jako je Office 365 a Azure, jedná se o aplikace využívající Azure AD a spoléhání se na něm jednat jejich rozsáhlé možnosti správy identit a přístupu. Přístup k těmto aplikacím zajišťuje informace o adresáře a vydávání tokenů.
* **Předem integrovaných aplikací od Microsoftu a vlastních aplikací** Toto jsou nezávislé cloudových aplikací, které využívají adresář interní aplikace a může pracovat nezávisle na službě Azure AD. Povolení přístupu k těmto aplikacím pomocí pověření specifické pro aplikaci namapován na účet aplikace. V závislosti na možnosti aplikací může být přihlašovací údaje, které federační token nebo uživatelské jméno a heslo pro účet, který byl dříve zřízené v aplikaci.
* **Místní aplikace** aplikace publikované prostřednictvím proxy aplikací Azure AD, především povolení přístupu k místním aplikacím. Tyto aplikace využívají centrální místní adresář jako Windows Server Active Directory. K doručování obsahu aplikace koncovým uživatelům respektováním požadavek na místní přihlášení je povolen přístup k těmto aplikacím aktivací proxy serveru.

Například pokud se uživatel připojí vaše organizace, musíte vytvořit účet pro uživatele ve službě Azure AD pro operace primární přihlašování. Pokud tento uživatel vyžaduje přístup ke spravované aplikaci, jako je například Salesforce, musíte také vytvořit účet pro tohoto uživatele v Salesforce a připojit ho k účtu Azure, aby fungovat jednotné přihlašování. Pokud uživatel organizaci opustí, se doporučuje odstranit účet služby Azure AD a všechny účty protějšky v IAM ukládá aplikací, které uživatel měl přístup.

## <a name="access-detection"></a>Přístup k detekci
V moderních podnicích IT oddělení nemají často informace o všech cloudových aplikací, které jsou používány. Ve spojení s Cloud App Discovery Azure AD poskytuje řešení pro detekci těchto aplikací.

## <a name="account-management"></a>Správa účtů
Tradičně, správu účtů v různých aplikacích provádí ručně prováděné IT nebo pracovníci podpory v organizaci. Správa účtů služby Azure AD plně automatizuje aplikací integrovaných poskytovateli služeb a aplikací integruje společnost Microsoft podporuje zřizování automatizované uživatelů nebo zřizování za běhu SAML.

## <a name="automated-user-provisioning"></a>Zřizování automatizované uživatelů
Některé aplikace poskytují rozhraní automatizace pro vytváření a odebrání (nebo deaktivace) účtů. Pokud aliance takového rozhraní se využívá Azure AD. To snižuje provozní náklady, protože úlohy správy dojít automaticky a zvyšuje zabezpečení vašeho prostředí, protože snižuje pravděpodobnost neautorizovaného přístupu.

## <a name="access-management"></a>Správa přístupu
S Azure AD můžete spravovat přístup k aplikacím pomocí jednotlivých nebo pravidlo, které jsou řízené přiřazení. Můžete taky delegovat získat přístup k těm správným lidem v organizaci zajištění nejlepší dohledu a snížit zátěž na helpdesk.

## <a name="on-premises-applications"></a>Místní aplikace
Proxy integrované aplikace umožňuje publikování aplikací v místním uživatelům výsledkem je konzistentní vzhledem k aplikacím i přístup k prostředí s moderní cloudové aplikace a výhody z možnosti monitorování, vytváření sestav a zabezpečení Azure AD.

## <a name="reporting-and-monitoring"></a>Monitorování a vytváření sestav
Azure AD poskytuje s předem integrované generování sestav a monitorování, které vám umožní zjistit, kdo má přístup k aplikacím a jejich skutečně spotřebujete.

## <a name="related-capabilities"></a>Související funkce
S Azure AD můžete zabezpečit vaše aplikace zásady granulární přístup a předem integrované MFA. Další informace o Azure MFA viz [Azure MFA](https://azure.microsoft.com/services/multi-factor-authentication/).

## <a name="getting-started"></a>Začínáme
Abyste mohli začít, integrace aplikací s Azure AD, podívejte se na [příručku Začínáme s integrací Azure Active Directory s aplikacemi získávání](plan-an-application-integration.md).

## <a name="see-also"></a>Další informace najdete v tématech
* [Rejstřík článků o správě aplikací ve službě Azure Active Directory](../active-directory-apps-index.md)
* [Podrobný postup nasazení plánu pro jednotné přihlašování do aplikace SaaS](http://aka.ms/ssodeploymentplan)

