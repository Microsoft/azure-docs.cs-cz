---
title: Vykázat Automatické zřizování uživatelských účtů do aplikací SaaS
description: Zjistěte, jak kontrolovat stav automatických úloh zřizování uživatelských účtů a jak řešit problémy zřizování jednotlivých uživatelů.
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 09/09/2018
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 5d93cec201feeb43700fe849f2fddc8eaf22488a
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104579480"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Kurz: vytváření sestav o automatickém zřizování uživatelských účtů

Azure Active Directory (Azure AD) obsahuje [službu zřizování uživatelských účtů](user-provisioning.md) , která pomáhá automatizovat zřizování uživatelských účtů v aplikacích SaaS a dalších systémech za účelem komplexní správy životního cyklu identity. Azure AD podporuje předem integrované konektory pro zřizování uživatelů pro všechny aplikace a systémy [pomocí kurzů pro](../saas-apps/tutorial-list.md)zřizování uživatelů.

Tento článek popisuje, jak kontrolovat stav zřizovacích úloh po jejich nastavení a jak řešit problémy zřizování jednotlivých uživatelů a skupin.

## <a name="overview"></a>Přehled

Zřizovací konektory se nastavují a konfigurují pomocí [Azure Portal](https://portal.azure.com), a to podle [uvedené dokumentace](../saas-apps/tutorial-list.md) pro podporovanou aplikaci. Po nakonfigurování a spuštění lze úlohy zřizování ohlásit pomocí jedné ze dvou metod:

* **Azure Portal** – Tento článek popisuje, jak načíst informace o sestavě z [Azure Portal](https://portal.azure.com), která poskytuje souhrnnou sestavu zřizování i podrobné protokoly auditu pro danou aplikaci.
* **Audit API** – Azure Active Directory taky poskytuje rozhraní API pro audit, které umožňuje programové načtení podrobných protokolů auditu zřizování. Dokumentaci specifickou pro použití tohoto rozhraní API najdete v tématu [referenční informace k rozhraní api Azure Active Directory audit](/graph/api/resources/directoryaudit) . I když tento článek nezahrnuje konkrétně použití rozhraní API, podrobně popisuje typy událostí zřizování, které jsou zaznamenány v protokolu auditu.

### <a name="definitions"></a>Definice

Tento článek používá následující výrazy, které jsou definovány níže:

* **Zdrojový systém** – úložiště uživatelů, ze kterých se služba zřizování Azure AD synchronizuje. Azure Active Directory je zdrojovým systémem většiny předintegrovaných zřizovacích konektorů, ale vyskytly se nějaké výjimky (příklad: synchronizace příchozích dat Workday).
* **Cílový systém** – úložiště uživatelů, se kterými se služba zřizování Azure AD synchronizuje. Obvykle se jedná o aplikaci SaaS (příklady: Salesforce, ServiceNow, G Suite, Dropbox pro firmy), ale v některých případech může jít o místní systém, jako je například služba Active Directory (například: příchozí synchronizace Workday do služby Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Získávání sestav zřizování z Azure Portal

Pokud chcete získat informace o zřizovacích sestavách pro danou aplikaci, Začněte spuštěním [Azure Portal](https://portal.azure.com) a **Azure Active Directorych** &gt; protokolů zřizování **podnikových aplikací** &gt; **(Preview)** v části **aktivita** . Můžete také přejít na podnikovou aplikaci, pro kterou je zřizování nakonfigurováno. Pokud například zřizujete uživatele na zvýšení oprávnění LinkedInu, navigační cesta k aplikaci je následující:

**Azure Active Directory > podnikové aplikace > všech aplikacích > zvýšení úrovně LinkedInu**

Odtud můžete přístup k řádku průběh zřizování i k protokolům zřizování, které jsou popsané níže.

## <a name="provisioning-progress-bar"></a>Indikátor průběhu zřizování

[Indikátor průběhu zřizování](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) se zobrazuje na kartě **zřizování** pro danou aplikaci. Je umístěn v aktuálním oddílu **stav** a zobrazuje stav aktuálního počátečního nebo přírůstkového cyklu. Tato část také obsahuje:

* Celkový počet uživatelů a skupin, které byly synchronizovány a které jsou aktuálně v rozsahu pro zřizování mezi zdrojovým systémem a cílovým systémem.
* Čas posledního spuštění synchronizace. K synchronizaci obvykle dochází každých 20-40 minut po dokončení [počátečního cyklu](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) .
* Bez ohledu na to, zda byl dokončen [počáteční cyklus](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental) .
* Bez ohledu na to, jestli je proces zřizování umístěný do karantény, a jaký je důvod stavu karantény (například selhání komunikace s cílovým systémem z důvodu neplatných přihlašovacích údajů správce).

**Aktuální stav** by měl být prvním místem, kde správci hledají provozní stav úlohy zřizování.

 ![Souhrnná sestava](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Zřizování protokolů (Preview)

Všechny aktivity prováděné službou zřizování se zaznamenávají v [protokolech zřizování](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)Azure AD. K protokolům zřizování v Azure Portal můžete získat přístup tak, že  &gt; v části aktivita vyberete Azure Active Directory protokoly zřizování **podnikových aplikací** &gt; **(Preview)** .  Data zřizování můžete vyhledat na základě jména uživatele nebo identifikátoru v systému zdrojového systému nebo v cílovém systému. Podrobnosti najdete v tématu [zřizování protokolů (Preview)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Typy událostí protokolovaných aktivit zahrnují:

## <a name="troubleshooting"></a>Řešení potíží

Sestava souhrnu zřizování a zřizovacích protokolů hraje klíčovou roli, která pomáhá správcům řešit problémy s různými problémy zřizování uživatelských účtů.

Pokyny k řešení potíží s automatickým zřizováním uživatelů najdete v tématu problémy s [konfigurací a zřizováním uživatelů do aplikace](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Další materiály

* [Správa zřizování uživatelských účtů pro podnikové aplikace](configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)