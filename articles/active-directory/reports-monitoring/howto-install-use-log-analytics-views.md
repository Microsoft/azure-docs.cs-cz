---
title: Jak nainstalovat a používat zobrazení analýzy protokolu | Dokumenty společnosti Microsoft
description: Přečtěte si, jak nainstalovat a používat zobrazení analýzy protokolů pro Službu Azure Active Directory
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 2290de3c-2858-4da0-b4ca-a00107702e26
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.subservice: report-monitor
ms.date: 04/18/2019
ms.author: markvi
ms.reviewer: dhanyahk
ms.collection: M365-identity-device-management
ms.openlocfilehash: b17026e4cfbe69e36c8e459aa259fe16b1c9d80d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74014425"
---
# <a name="install-and-use-the-log-analytics-views-for-azure-active-directory"></a>Instalace a použití zobrazení analýzy protokolů pro Službu Azure Active Directory

Zobrazení analýzy protokolu služby Azure Active Directory vám pomůže analyzovat a prohledávat protokoly aktivit Azure AD ve vašem tenantovi Azure AD. Protokoly aktivit Azure AD zahrnují:

* Protokoly auditu: [Sestava aktivit protokolů auditu](concept-audit-logs.md) umožňuje přístup k historii všech úloh, které se provádějí ve vašem tenantovi.
* Protokoly přihlášení: Pomocí [sestavy aktivit přihlášení](concept-sign-ins.md)můžete určit, kdo provedl úkoly, které jsou uvedeny v protokolech auditu.

## <a name="prerequisites"></a>Požadavky

Chcete-li použít zobrazení analýzy protokolů, potřebujete:

* Pracovní prostor Analýzy protokolů ve vašem předplatném Azure. Přečtěte si, jak [vytvořit pracovní prostor Analýzy protokolů](https://docs.microsoft.com/azure/log-analytics/log-analytics-quick-create-workspace).
* Nejprve proveďte kroky ke [směrování protokolů aktivit Azure AD do pracovního prostoru Analýzy protokolů](howto-integrate-activity-logs-with-log-analytics.md).
* Stáhněte si zobrazení z [úložiště GitHub](https://aka.ms/AADLogAnalyticsviews) do místního počítače.

## <a name="install-the-log-analytics-views"></a>Instalace zobrazení analýzy protokolů

1. Přejděte do pracovního prostoru Analýzy protokolů. Chcete-li to provést, nejprve přejděte na [portál Azure](https://portal.azure.com) a vyberte **všechny služby**. Do textového pole zadejte **Analýzu protokolů** a vyberte **pracovní prostory Log Analytics**. Jako součást předpokladů vyberte pracovní prostor, do který jste směrovali protokoly aktivit.
2. Vyberte **Návrhář zobrazení**, vyberte **Importovat** a pak vyberte **Zvolit soubor,** chcete-li importovat zobrazení z místního počítače.
3. Vyberte zobrazení stažená z požadavků a výběrem **možnosti Uložit** import uložte. Udělejte to pro zobrazení **události zřizování účtu Azure AD** a zobrazení **Události přihlášení.**

## <a name="use-the-views"></a>Použití zobrazení

1. Přejděte do pracovního prostoru Analýzy protokolů. Chcete-li to provést, nejprve přejděte na [portál Azure](https://portal.azure.com) a vyberte **všechny služby**. Do textového pole zadejte **Analýzu protokolů** a vyberte **pracovní prostory Log Analytics**. Jako součást předpokladů vyberte pracovní prostor, do který jste směrovali protokoly aktivit.

2. Až budete v pracovním prostoru, vyberte **Souhrn pracovního prostoru**. Měli byste vidět následující tři pohledy:

    * **Události zřizování účtu Azure AD**: Toto zobrazení zobrazuje sestavy související s aktivitou zřizování auditování, jako je počet nových uživatelů zřízených a zřizování selhání, počet uživatelů aktualizované a aktualizace selhání a počet uživatelů deprovisioned a odpovídající selhání.    
    * **Události přihlášení**: Toto zobrazení zobrazuje nejdůležitější sestavy související s aktivitou přihlašování monitorováním, jako je přihlášení podle aplikace, uživatele, zařízení a také souhrnné zobrazení sledující počet přihlášení v průběhu času.

3. Vyberte některý z těchto zobrazení, chcete-li přejít na jednotlivé sestavy. Můžete také nastavit výstrahy na některý z parametrů sestavy. Například nastavte výstrahu pro pokaždé, když dojde k chybě přihlášení. Chcete-li to provést, nejprve vyberte zobrazení **Události přihlášení,** vyberte sestavy **Chyby přihlášení v průběhu času** a pak vyberte **Analytics,** chcete-li otevřít stránku podrobností se skutečným dotazem za sestavou. 

    ![Podrobnosti](./media/howto-install-use-log-analytics-views/details.png)


4. Vyberte **Nastavit výstrahu**a pak v části Kritéria **výstrahy** vyberte **Vždy, když je &lt;hledání vlastního protokolu logice nedefinované.&gt; ** Vzhledem k tomu, že chceme upozornit vždy, když dojde k chybě přihlášení, nastavte **prahovou hodnotu** výchozí logiky výstrahy na **hodnotu 1** a vyberte **hotovo**. 

    ![Konfigurace logiky signálů](./media/howto-install-use-log-analytics-views/configure-signal-logic.png)

5. Zadejte název a popis výstrahy a nastavte závažnost na **Upozornění**.

    ![Vytvořit pravidlo](./media/howto-install-use-log-analytics-views/create-rule.png)

6. Vyberte skupinu akcí, kterou chcete upozornit. Obecně platí, že to může být buď tým, který chcete upozornit prostřednictvím e-mailu nebo textové zprávy, nebo to může být automatizovaný úkol pomocí webhooků, runbooků, funkcí, logických aplikací nebo externích řešení ITSM. Přečtěte si, jak [vytvářet a spravovat skupiny akcí na webu Azure Portal](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-action-groups).

7. Chcete-li **výstrahu** vytvořit, vyberte vytvořit pravidlo výstrahy. Nyní budete upozorněni pokaždé, když dojde k chybě přihlášení.

## <a name="next-steps"></a>Další kroky

* [Jak analyzovat protokoly aktivit pomocí protokolů Azure Monitor](howto-analyze-activity-logs-log-analytics.md)
* [Začínáme s protokoly Azure Monitoru na webu Azure Portal](https://docs.microsoft.com/azure/log-analytics/query-language/get-started-analytics-portal)