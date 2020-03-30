---
title: Nahlásit automatické zřizování uživatelských účtů aplikacím SaaS
description: Zjistěte, jak zkontrolovat stav úloh automatického zřizování uživatelských účtů a jak řešit potíže s zřizováním jednotlivých uživatelů.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: app-provisioning
ms.devlang: na
ms.topic: conceptual
ms.date: 09/09/2018
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: 19d76f69669ffa13d1d55ffa807e6c4818b8840c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282184"
---
# <a name="tutorial-reporting-on-automatic-user-account-provisioning"></a>Kurz: Vytváření sestav automatického zřizování uživatelských účtů

Azure Active Directory (Azure AD) zahrnuje [službu zřizování uživatelských účtů,](user-provisioning.md) která pomáhá automatizovat zřizování zrušení zřizování uživatelských účtů v aplikacích SaaS a dalších systémech pro účely správy životního cyklu identity od konce. Azure AD podporuje předem integrované uživatelské zřizovací konektory pro všechny aplikace a systémy s uživatelskými kurzy pro zřizování [zde](https://docs.microsoft.com/azure/active-directory/saas-apps/tutorial-list).

Tento článek popisuje, jak zkontrolovat stav zřizovacích úloh po jejich nastavení a jak řešit řešení zřizování jednotlivých uživatelů a skupin.

## <a name="overview"></a>Přehled

Zřizovací konektory se nastavují a nakonfigurují pomocí [portálu Azure](https://portal.azure.com)podle [poskytnuté dokumentace](../saas-apps/tutorial-list.md) pro podporovanou aplikaci. Po nakonfigurování a spuštění zřizovací úlohy lze hlásit pomocí jedné ze dvou metod:

* **Portál Azure** – tento článek popisuje především načítání informací sestavy z [portálu Azure](https://portal.azure.com), který poskytuje souhrnnou sestavu zřizování i podrobné protokoly auditování zřizování pro danou aplikaci.
* **Rozhraní API auditu** – Azure Active Directory také poskytuje rozhraní API pro audit, které umožňuje programové načítání podrobných protokolů auditování zřizování. Dokumentace specifická pro použití tohoto rozhraní API najdete [v tématu Odkaz na rozhraní API pro audit služby Azure Active Directory.](https://developer.microsoft.com/graph/docs/api-reference/beta/resources/directoryaudit) Zatímco tento článek nezahrnuje konkrétně, jak používat rozhraní API, podrobně popisuje typy zřizování událostí, které jsou zaznamenány v protokolu auditu.

### <a name="definitions"></a>Definice

Tento článek používá následující termíny definované níže:

* **Zdrojový systém** – úložiště uživatelů, které služba zřizování Azure AD synchronizuje od. Azure Active Directory je zdrojový systém pro většinu předem integrovaných zřizovacích konektorů, ale existují některé výjimky (příklad: Synchronizace příchozích pracovních dnů).
* **Cílový systém** – úložiště uživatelů, se kterými se synchronizuje zřizovací služba Azure AD. Obvykle se jedná o aplikaci SaaS (příklady: Salesforce, ServiceNow, G Suite, Dropbox pro firmy), ale v některých případech může být místní systém, jako je služba Active Directory (příklad: Synchronizace příchozích pracovních dnů do služby Active Directory).

## <a name="getting-provisioning-reports-from-the-azure-portal"></a>Získání sestav zřizování z portálu Azure

Chcete-li získat informace sestavy zřizování pro danou aplikaci, začněte spuštěním [portálu Azure portal](https://portal.azure.com) a **protokolů zřizování** **podnikových aplikací** &gt; Azure **Active Directory** &gt; (preview) v části **Aktivita.** Můžete také přejít na podnikovou aplikaci, pro kterou je nakonfigurováno zřizování. Například pokud zřizujete uživatele na LinkedIn Elevate, navigační cesta k podrobnostem aplikace je:

**Azure Active Directory > Podnikové aplikace > všechny aplikace > LinkedIn Elevate**

Odtud můžete přistupovat k panelu průběhu zřizování a protokolům zřizování, které jsou popsány níže.

## <a name="provisioning-progress-bar"></a>Panel průběhu zřizování

[Indikátor průběhu zřizování](application-provisioning-when-will-provisioning-finish-specific-user.md#view-the-provisioning-progress-bar) je viditelný na kartě **Zřizování** pro danou aplikaci. Nachází se v části **Aktuální stav** pod **nastavením**a zobrazuje stav aktuálního počátečního nebo přírůstkového cyklu. Tato část také ukazuje:

* Celkový počet uživatelů a skupin, které byly synchronizovány a jsou aktuálně v oboru pro zřizování mezi zdrojovým systémem a cílovým systémem.
* Při posledním spuštění synchronizace. Synchronizace obvykle dochází každých 20-40 minut po dokončení [počátečního cyklu.](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)
* Zda byl dokončen [počáteční cyklus.](../app-provisioning/how-provisioning-works.md#provisioning-cycles-initial-and-incremental)
* Určuje, zda byl proces zřizování umístěn do karantény a jaký je důvod stavu karantény (například nekomunikace s cílovým systémem z důvodu neplatných pověření správce).

**Aktuální stav** by měl být první místo, které správci hledají pro kontrolu provozního stavu úlohy zřizování.

 ![Souhrnná sestava](./media/check-status-user-account-provisioning/provisioning-progress-bar-section.png)

## <a name="provisioning-logs-preview"></a>Zřizování protokolů (náhled)

Všechny aktivity prováděné službou zřizování se zaznamenávají v [protokolech zřizování](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context)Azure AD . K protokolům zřizování na webu Azure Portal můžete přistupovat tak, že v části **Aktivita** **vyberete protokoly zřizování** aplikací **Azure Active Directory** &gt; **Enterprise Provisioning** &gt; (preview). Zřizování dat můžete prohledávat na základě jména uživatele nebo identifikátoru ve zdrojovém nebo cílovém systému. Podrobnosti naleznete v [tématu Zřizování protokolů (náhled)](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context). Mezi typy událostí protokolované aktivity patří:

## <a name="troubleshooting"></a>Řešení potíží

Zřizování souhrnné sestavy a zřizování protokoly hrají klíčovou roli pomáhá správci řešení různých problémů s zřizováním uživatelských účtů.

Pokyny založené na scénářích, jak řešit potíže s automatickým zřizováním uživatelů, naleznete [v tématu Problémy s konfigurací a zřizováním uživatelů do aplikace](../app-provisioning/application-provisioning-config-problem.md).

## <a name="additional-resources"></a>Další zdroje

* [Správa zřizování uživatelských účtů pro podnikové aplikace](configure-automatic-user-provisioning-portal.md)
* [Jak ve službě Azure Active Directory probíhá přístup k aplikacím a jednotné přihlašování?](../manage-apps/what-is-single-sign-on.md)
