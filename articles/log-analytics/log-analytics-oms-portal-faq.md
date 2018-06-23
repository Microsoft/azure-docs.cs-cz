---
title: Běžné otázky pro přechod z portálu OMS na portálu Azure Log Analytics uživatelům | Microsoft Docs
description: Odpovědi na časté otázky pro uživatele analýzy protokolů přechod z portálu OMS na portál Azure.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/07/2018
ms.author: bwren
ms.openlocfilehash: 1e0fd56b6e420103b4f786985f71a84737db642d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333628"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>Běžné otázky pro přechod z portálu OMS na portál Azure pro uživatele, analýzy protokolů
Vlastní portál označuje portál OMS analýzy protokolů původně použitý ke správě konfigurace a analýza shromážděná data.  Všechny funkce z tohoto portálu byl přesunut do portálu Azure kde bude pokračovat na.

Tento článek obsahuje odpovědi na běžné otázky pro uživatele, což tento přechod.  Pokud jste použili Log Analytics na portálu OMS, můžete najít zde odpovědi pro jak můžete stejné úlohy provádět na portálu Azure.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Kde analýzy protokolů v Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).  Klikněte na tlačítko **všechny služby**a v seznamu prostředků zadejte **analýzy protokolů**. Vyberte **analýzy protokolů** a pak vyberte pracovní prostor. Zobrazí se stránka souhrnu pro pracovní prostor.

![Pracovní prostor Log Analytics](media/log-analytics-new-portal/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>Jak lze spravovat oprávnění?
Pokud nemáte přístup do pracovního prostoru analýzy protokolů na portálu Azure, budete muset nakonfigurovat oprávnění pomocí [přístupu na základě role Azure](../active-directory/role-based-access-control-configure.md). Podrobnosti o správě prostoru oprávnění najdete v tématu [spravovat pracovní prostory](../log-analytics/log-analytics-manage-access.md#manage-accounts-and-users). Informace o správě oprávnění pro výstrahy, najdete v části [začít pracovat s rolí, oprávnění a zabezpečení pomocí Azure monitorování](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md).

## <a name="how-do-i-create-a-new-workspace"></a>Jak vytvořit nový pracovní prostor? 
Ze seznamu pracovní prostory v portálu Azure, klikněte na tlačítko **přidat** v seznamu pracovních prostorů.  Úplné podrobnosti najdete v tématu [na portálu Azure vytvořit pracovní prostor analýzy protokolů](../log-analytics/log-analytics-quick-create-workspace.md).

![Stránka s přehledem](media/log-analytics-new-portal/new-workspace.png)

## <a name="where-is-my-overview-page"></a>Kde je stránka Moje přehled?
Hlavní obrazovky na portálu OMS zobrazí dlaždice pro řešení správy nainstalovat v žádné vlastní zobrazení, které jste vytvořili a pracovního prostoru. Toto stejné zobrazení je k dispozici na portálu Azure. Z pracovního prostoru vyberte **prostoru Souhrn**.

![Stránka s přehledem](media/log-analytics-new-portal/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>Jak otevřít hledání protokolů a Návrhář zobrazení?
Obě **hledání protokolů** a **Návrhář zobrazení** jsou dostupné na hlavní stránce a v levé nabídce pracovního prostoru na portálu Azure další práva k **přehled**.

## <a name="where-do-i-find-settings"></a>Kde lze najít nastavení?
Mnoho nastavení v **nastavení** jsou k dispozici v sekci portálu OMS **upřesňující nastavení** nabídky na portálu Azure pro pracovní prostor.

![Upřesnit nastavení](media/log-analytics-new-portal/advanced-settings.png)

V následujících částech najdete úplný seznam přístupu k nastavení, které byly dřív dostupné v **nastavení** sekci portálu OMS.

### <a name="accounts"></a>Účty 
Nastavení účty se spravují v různých místech na portálu Azure, jak je popsáno v následující tabulce.

| Nastavení na portálu OMS | Ekvivalent v portálu Azure |
|:---|:---|
| Účet Automation | **Účet Automation** nabídky pro pracovní prostor. |
| Předplatné Azure & datový tarif | **Cenová úroveň** nabídky pro pracovní prostor. |
| Správa uživatelů | Použití Azure přístupu na základě rolí k [spravovat oprávnění pro pracovní prostor](#how-do-i-manage-permissions). |
| Informace o pracovním prostoru | Informace, které jsou k dispozici na **pracovním prostorem OMS** nabídky pro pracovní prostor. |

### <a name="alerts"></a>Výstrahy
Pravidla výstrah, které jsou založené na dotazech analýzy protokolů se teď spravují na [unified výstrahy prostředí](#how-do-i-create-and-manage-alerts). 

### <a name="computer-groups"></a>Skupiny počítačů
Správa skupin počítačů v **upřesňující nastavení** nabídky pro pracovní prostor. 

### <a name="connected-sources"></a>Připojené zdroje
Spravovat většinu nastavení připojené zdroje v **upřesňující nastavení** nabídky pro pracovní prostor. Následující tabulka obsahuje podrobnosti pro každou část této nabídky.

| Nastavení na portálu OMS | Ekvivalent v portálu Azure |
|:---|:---|
| Servery Windows   | **Upřesňující nastavení** nabídky pro pracovní prostor. |
| Servery se systémem Linux   | **Upřesňující nastavení** nabídky pro pracovní prostor. |
| Azure Storage     | **Upřesňující nastavení** nabídky pro pracovní prostor. |
| System Center     | **Upřesňující nastavení** nabídky pro pracovní prostor. |
| Office 365        | Najdete v článku [dokumentace pro řešení pro správu Office 365](../operations-management-suite/oms-solution-office-365.md) podrobnosti o konfiguraci. |
| Telemetrie Windows | Ještě není k dispozici na portálu Azure. |
| ITSM konektoru    | V tématu [připojit ITSM produkty nebo služby s konektoru služby správy IT](../log-analytics/log-analytics-itsmc-connections.md) pokyny pro připojení služby ITSM s analýzy protokolů. |

### <a name="data"></a>Data
Spravovat většinu nastavení dat v **upřesňující nastavení** nabídky pro pracovní prostor. Následující tabulka obsahuje podrobnosti pro každou část této nabídky.

| Nastavení na portálu OMS | Ekvivalent v portálu Azure |
|:---|:---|
| Protokoly událostí Windows           | **Upřesňující nastavení** nabídky pro pracovní prostor. |
| Čítače výkonu systému Windows | **Upřesňující nastavení** nabídky pro pracovní prostor. |
| Čítače výkonu systému Linux   | **Upřesňující nastavení** nabídky pro pracovní prostor. |
| Protokoly IIS                     | **Upřesňující nastavení** nabídky pro pracovní prostor. |
| Vlastní pole                | **Upřesňující nastavení** nabídky pro pracovní prostor. |
| Vlastní protokoly                  | **Upřesňující nastavení** nabídky pro pracovní prostor. |
| Syslog                       | **Upřesňující nastavení** nabídky pro pracovní prostor. |
| Application Insights         | Toto řešení je zastaralá nyní, analýzy protokolů a Application Insights sdílet stejný modul data.  |
| Sledování souborů Windows        | **Sledování změn** nabídky v Azure Automation. V tématu [sledovat změny v prostředí s řešením sledování změn](../automation/automation-change-tracking.md) podrobnosti. |
| Sledování registru Windows        | **Sledování změn** nabídky v Azure Automation. V tématu [sledovat změny v prostředí s řešením sledování změn](../automation/automation-change-tracking.md) podrobnosti. |
| Sledování souborů v Linuxu          | **Sledování změn** nabídky v Azure Automation. V tématu [sledovat změny v prostředí s řešením sledování změn](../automation/automation-change-tracking.md) podrobnosti. |

### <a name="solutions"></a>Řešení
Spravovat řešení **řešení** nabídky pro pracovní prostor. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>Jak nainstalovat a odebrat řešení pro správu?
Na portálu OMS nainstalujete řešení pro správu z Galerie řešení a odebere je ze **nastavení**. Na portálu Azure [nainstalovat řešení pro správu](../monitoring/monitoring-solutions.md#install-a-management-solution) z Azure Marketplace. [Odebrat řešení](../monitoring/monitoring-solutions.md#remove-a-management-solution) ze seznamu nainstalovaných řešení.

## <a name="how-do-i-create-and-manage-alerts"></a>Jak vytvořit a spravovat výstrahy?
Pravidla výstrah, které jsou založené na dotazech analýzy protokolů se teď spravují na [unified výstrahy prostředí](../monitoring-and-diagnostics/monitor-alerts-unified-usage.md). V tématu [postup rozšíření výstrahy z analýzy protokolů do Azure výstrahy](../monitoring-and-diagnostics/monitoring-alerts-extend-tool.md) podrobnosti o konfiguraci a použití výstrahy na portálu Azure.

## <a name="how-do-i-access-my-dashboards"></a>Přístupu Můj řídicí panely
[Řídicí panely](../log-analytics/log-analytics-dashboards.md) v analýzy protokolů jsou zastaralé.  Můžete vizualizovat data pomocí analýzy protokolů [Návrhář zobrazení](../log-analytics/log-analytics-view-designer.md) jehož další funkce a kód pin dotazů a zobrazení na Azure řídicí panely.

## <a name="how-do-i-check-my-usage"></a>Jak zkontrolovat mého používání?
Teď můžete snadno zobrazit a spravovat využití a náklady na analýzy protokolů výběrem **využití a odhadované náklady** v pracovním prostoru.

![Využití a odhadované náklady](media/log-analytics-new-portal/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>Používání portálu classic
Po omezenou dobu, můžete nadále přistupovat na portál prostřednictvím tuto adresu URL, s vlastní název pracovního prostoru: https://\<název pracovního prostoru\>. portal.mms.microsoft.com. Doporučujeme, ale pomocí portálu Azure a nám poskytnout zpětnou vazbu na LAUpgradeFeedback@microsoft.com na libovolným omezujícím problémům.

## <a name="next-steps"></a>Další postup

- [Najít a nainstalovat řešení pro správu](../monitoring/monitoring-solutions.md) pomocí portálu Azure.
- Další informace o [protokolu vyhledávání na webu Azure portal](log-analytics-log-search-portals.md).