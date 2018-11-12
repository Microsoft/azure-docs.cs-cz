---
title: Správa aplikací pomocí služby Azure Active Directory | Microsoft Docs
description: Tento článek popisuje výhody integrace služby Azure Active Directory s místními a cloudovými aplikacemi a aplikacemi SaaS.
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: overview
ms.workload: identity
ms.date: 10/30/2018
ms.author: barbkess
ms.reviewer: arvinh
ms.openlocfilehash: 577be6669442befec0c14a275505e2eebed72611
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50418088"
---
# <a name="application-management-with-azure-active-directory"></a>Správa aplikací pomocí služby Azure Active Directory

Azure Active Directory (Azure AD) poskytuje zabezpečený a hladký přístup ke cloudovým a místním aplikacím. Uživatelům se stačí přihlásit jednou a získají přístup k Office 365 a dalším obchodním aplikacím od Microsoftu, aplikacím typu software jako služba (SaaS), místním aplikacím a obchodním aplikacím. Automatizací zřizování uživatelů snížíte náklady na správu. K poskytování zabezpečeného přístupu k aplikacím využijte vícefaktorové ověřování a zásady podmíněného přístupu.

![Aplikace federované přes službu Azure AD](media/what-is-application-management/app-management-overview.png)

## <a name="why-manage-applications-with-a-cloud-solution"></a>Proč spravovat aplikace pomocí cloudového řešení?

Organizace mají často stovky aplikací, které uživatelé potřebují k výkonu své práce. Uživatelé tyto aplikace používají na různých zařízeních a v různých umístěních. Každý den se přidávají, vyvíjejí a vyřazují nové aplikace. S takovým počtem aplikací a přístupových bodů je důležitější než kdy dříve používat cloudové řešení ke správě uživatelského přístupu ke všem aplikacím.

## <a name="manage-risk-with-conditional-access-policies"></a>Správa rizik pomocí zásad podmíněného přístupu
Spojení jednotného přihlašování Azure AD se zásadami podmíněného přístupu poskytuje vysoké úrovně zabezpečení při přístupu k aplikacím. Funkce zabezpečení zahrnují ochranu identit v cloudovém měřítku, řízení přístupu na základě rizik, nativní vícefaktorové ověřování a zásady podmíněného přístupu. Tyto funkce umožňují podrobné zásady řízení přístupu na základě aplikací nebo skupin, které potřebují vyšší úroveň zabezpečení.

## <a name="improve-productivity-with-single-sign-on"></a>Vyšší produktivita pomocí jednotného přihlašování
Povolení jednotného přihlašování mezi aplikacemi a Office 365 poskytuje stávajícím uživatelům nadstandardní prostředí pro přihlašování, protože snižuje počet výzev k přihlášení nebo je úplně eliminuje. Prostředí je tak pro uživatele ucelenější, neruší ho zobrazováním mnoha výzev k přihlášení a uživatel nemusí spravovat mnoho hesel. Obchodní skupiny mohou spravovat a schvalovat přístup prostřednictvím samoobslužného a dynamického členství. Když správu přístupu k aplikacím povolíte správným lidem, zvýšíte zabezpečení systému identit.

Jednotné přihlašování zvyšuje zabezpečení. *Bez jednotného přihlašování* musí správci vytvářet a aktualizovat uživatelské účty pro každou jednotlivou aplikaci, což zabere nějaký čas. Uživatelé navíc musí používat pro přístup ke svým aplikacím různé přihlašovací údaje. V důsledku toho uživatelé tíhnou k tomu, aby si zapisovali hesla nebo používali jiná řešení pro správu hesel, což s sebou nese riziko ohrožení zabezpečení dat. 

## <a name="address-governance-and-compliance"></a>Zajištění správného řízení a dodržování předpisů
Pomocí Azure AD můžete monitorovat přihlašování k aplikacím prostřednictvím sestav, které využívají nástroje SIEM (Security Incident and Event Monitoring). K sestavám se dostanete z portálu nebo z rozhraní API. Prostřednictvím kódu programu můžete kontrolovat, kdo má přístup k aplikacím, a přes kontrolu přístupu můžete neaktivním uživatelům přístup odebrat.

## <a name="manage-costs"></a>Správa nákladů
Migrací do Azure AD můžete snížit náklady a zbavit se nepříjemností spojených se správou místní infrastruktury. Azure AD navíc poskytuje samoobslužný přístup k aplikacím, což ušetří čas jak správcům, tak i uživatelům. Jednotné přihlašování eliminuje hesla pro konkrétní aplikace. Tato možnost přihlásit se pouze jednou šetří náklady spojené s resetováním hesel pro aplikace a pomáhá předejít ztrátě produktivity při načítání hesel.

