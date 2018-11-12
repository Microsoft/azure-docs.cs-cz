---
title: Běžné otázky týkající se přechodu z portálu OMS na webu Azure portal pro uživatele Log Analytics | Dokumentace Microsoftu
description: Odpovědi na běžné otázky pro uživatele Log Analytics přechod z portálu OMS na webu Azure portal.
services: log-analytics
documentationcenter: ''
author: bwren
manager: carmonm
editor: ''
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 10/18/2018
ms.author: bwren
ms.component: ''
ms.openlocfilehash: 442371ffc63e32906d9faafc3b803bb5e6386905
ms.sourcegitcommit: ba4570d778187a975645a45920d1d631139ac36e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2018
ms.locfileid: "51283440"
---
# <a name="common-questions-for-transition-from-oms-portal-to-azure-portal-for-log-analytics-users"></a>Běžné otázky týkající se přechodu z portálu OMS na webu Azure portal pro uživatele Log Analytics
Vlastní portál, volá se na portálu OMS log Analytics původně použitý ke správě své konfiguraci a analýzy shromážděných dat.  Všechny funkce z tohoto portálu byl přesunut na webu Azure portal ve kterém bude pokračovat na vývoj.

Tento článek obsahuje odpovědi na běžné otázky pro uživatele, provedení tohoto přechodu.  Pokud jste na portálu OMS Log Analytics, můžete najít tady odpovědi pro stejné úlohy můžete provádět jak na webu Azure Portal.

## <a name="do-i-need-to-migrate-anything"></a>Je potřeba cokoli migrovat?
Ne. Neexistují žádné změny prováděné ke službě Log Analytics, takže není nic, kterou je potřeba migrovat. Jediné, co se mění je rozhraní, které používáte pro přístup k ní. Ve skutečnosti můžete pomocí portálu Azure pro přístup ke stejným pracovní prostory, řešení, zobrazení a hledání, které použijete přihlášení na portálu OMS ještě dnes.

## <a name="where-do-i-find-log-analytics-in-azure"></a>Kde najdu Log Analytics v Azure?
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).  Klikněte na tlačítko **všechny služby**a v seznamu prostředků zadejte **Log Analytics**. Vyberte **Log Analytics** a pak vyberte pracovní prostor. Zobrazí se souhrn stránka pro pracovní prostor.

![Pracovní prostor Log Analytics](media/log-analytics-oms-portal-faq/log-analytics.png)

## <a name="how-do-i-manage-permissions"></a>Jak změním oprávnění?
Pokud nemáte přístup k pracovnímu prostoru Log Analytics na portálu Azure portal, budete muset nakonfigurovat oprávnění pomocí [přístupu na základě rolí Azure](../role-based-access-control/role-assignments-portal.md). Podrobnosti o správě oprávnění pracovního prostoru najdete v tématu [Správa pracovních prostorů](../log-analytics/log-analytics-manage-access.md#manage-accounts-and-users). Informace o správě oprávnění pro výstrahy, najdete v tématu [role, oprávnění a zabezpečení prostřednictvím služby Azure Monitor vám začít](../monitoring-and-diagnostics/monitoring-roles-permissions-security.md).

## <a name="how-do-i-create-a-new-workspace"></a>Jak vytvořit nový pracovní prostor? 
Ze seznamu pracovních prostorů na webu Azure Portal, klikněte na tlačítko **přidat** v seznamu pracovních prostorů.  Úplné podrobnosti najdete v tématu [vytvořit pracovní prostor Log Analytics na portálu Azure portal](../log-analytics/log-analytics-quick-create-workspace.md).

![Stránka s přehledem](media/log-analytics-oms-portal-faq/new-workspace.png)

## <a name="where-is-my-overview-page"></a>Kde je Moje stránka s přehledem?
Na hlavní obrazovce portálu OMS se zobrazí dlaždice všech řešení pro správu nainstalovat v jakékoli vlastní zobrazení, které jste vytvořili a pracovního prostoru. Toto zobrazení stejné je k dispozici na webu Azure Portal. V pracovním prostoru vyberte **shrnutí pracovního prostoru**.

![Stránka s přehledem](media/log-analytics-oms-portal-faq/overview.png)

## <a name="how-do-i-open-log-search-and-view-designer"></a>Jak otevřít prohledávání protokolů a zobrazení návrháře?
Obě **prohledávání protokolů** a **Návrhář zobrazení** mají k dispozici na hlavní stránce a v levé nabídce pracovního prostoru na webu Azure Portal, vpravo vedle **přehled**.

## <a name="where-do-i-find-settings"></a>Kde najdu nastavení?
Mnoho nastavení v **nastavení** jsou k dispozici v části portálu OMS **upřesňující nastavení** nabídky na webu Azure Portal pro pracovní prostor.

![Upřesnit nastavení](media/log-analytics-oms-portal-faq/advanced-settings.png)

Následující oddíly poskytují úplný seznam přístupu k nastavení, které byly dříve k dispozici v **nastavení** části portálu OMS.

### <a name="accounts"></a>Účty 
Nastavení účtů se spravují na různých místech na webu Azure Portal, jak je popsáno v následující tabulce.

| Nastavení na portálu OMS | Ekvivalent na webu Azure Portal |
|:---|:---|
| Účet Automation | **Účet Automation** nabídce pracovního prostoru. |
| Předplatné Azure a datový tarif | **Cenová úroveň** nabídce pracovního prostoru. |
| Správa uživatelů | Použití Azure přístup na základě rolí k [spravovat oprávnění pro váš pracovní prostor](#how-do-i-manage-permissions). |
| Informace o pracovním prostoru | Informace, které jsou k dispozici na **pracovní prostor OMS** nabídce pracovního prostoru. |

### <a name="alerts"></a>Výstrahy
Pravidla upozornění na základě dotazů Log Analytics se teď spravují v [sjednocené prostředí pro upozorňování](#how-do-i-create-and-manage-alerts). 

### <a name="computer-groups"></a>Skupiny počítačů
Spravovat skupiny počítačů v **upřesňující nastavení** nabídce pracovního prostoru. 

### <a name="connected-sources"></a>Připojené zdroje
Spravovat většinu nastavení připojené zdroje v **upřesňující nastavení** nabídce pracovního prostoru. Následující tabulka obsahuje podrobnosti pro jednotlivé oddíly této nabídky.

| Nastavení na portálu OMS | Ekvivalent na webu Azure Portal |
|:---|:---|
| Servery Windows   | **Upřesňující nastavení** nabídce pracovního prostoru. |
| Servery s Linuxem   | **Upřesňující nastavení** nabídce pracovního prostoru. |
| Azure Storage     | **Upřesňující nastavení** nabídce pracovního prostoru. |
| System Center     | **Upřesňující nastavení** nabídce pracovního prostoru. |
| Office 365        | Zobrazit [dokumentaci k řešení pro správu Office 365](../monitoring/monitoring-solution-office-365.md) podrobnosti o konfiguraci. |
| Telemetrie Windows | Nabídka nastavení řešení. Zobrazit [Windows Analytics na portálu Azure portal](/windows/deployment/update/windows-analytics-azure-portal) podrobnosti o konfiguraci. |
| Konektor ITSM    | Zobrazit [připojení ITSM produktů a služeb. s IT Service Management Connector](../log-analytics/log-analytics-itsmc-connections.md) pokyny týkající se připojení ITSM služby s využitím Log Analytics. |

### <a name="data"></a>Data
Spravovat většinu dat nastavení v **upřesňující nastavení** nabídce pracovního prostoru. Následující tabulka obsahuje podrobnosti pro jednotlivé oddíly této nabídky.

| Nastavení na portálu OMS | Ekvivalent na webu Azure Portal |
|:---|:---|
| Protokoly událostí Windows           | **Upřesňující nastavení** nabídce pracovního prostoru. |
| Čítače výkonu Windows | **Upřesňující nastavení** nabídce pracovního prostoru. |
| Čítače výkonu Linuxu   | **Upřesňující nastavení** nabídce pracovního prostoru. |
| Protokoly IIS                     | **Upřesňující nastavení** nabídce pracovního prostoru. |
| Vlastní pole                | **Upřesňující nastavení** nabídce pracovního prostoru. |
| Vlastní protokoly                  | **Upřesňující nastavení** nabídce pracovního prostoru. |
| Syslog                       | **Upřesňující nastavení** nabídce pracovního prostoru. |
| Application Insights         | Toto řešení je zastaralá teď Log Analytics a Application Insights sdílejí stejný stroj data.  |
| Sledování souborů Windows        | **Sledování změn** nabídky ve službě Azure Automation. Zobrazit [sledování změn ve vašem prostředí pomocí řešení Change Tracking](../automation/automation-change-tracking.md) podrobnosti. |
| Sledování registru Windows        | **Sledování změn** nabídky ve službě Azure Automation. Zobrazit [sledování změn ve vašem prostředí pomocí řešení Change Tracking](../automation/automation-change-tracking.md) podrobnosti. |
| Sledování souborů v Linuxu          | **Sledování změn** nabídky ve službě Azure Automation. Zobrazit [sledování změn ve vašem prostředí pomocí řešení Change Tracking](../automation/automation-change-tracking.md) podrobnosti. |

### <a name="solutions"></a>Řešení
Spravovat řešení v **řešení** nabídce pracovního prostoru. 

## <a name="how-do-i-install-and-remove-management-solutions"></a>Jak nainstalovat a odebrání řešení pro správu?
Na portálu OMS z Galerie řešení nainstalovat řešení pro správu a odebrána z **nastavení**. Na webu Azure Portal [nainstalovat řešení pro správu](../monitoring/monitoring-solutions.md#install-a-management-solution) z Azure Marketplace. [Odebrat řešení](../monitoring/monitoring-solutions.md#remove-a-management-solution) ze seznamu nainstalovaná řešení.

## <a name="how-do-i-create-and-manage-alerts"></a>Jak vytvořit a spravovat upozornění?
Pravidla upozornění na základě dotazů Log Analytics se teď spravují v [sjednocené prostředí pro upozorňování](../monitoring-and-diagnostics/alert-metric.md). Zobrazit [jak rozšíření upozornění z Log Analytics do upozornění Azure](../monitoring-and-diagnostics/monitoring-alerts-extend-tool.md) podrobné informace o konfiguraci a použití upozornění na webu Azure Portal.

## <a name="how-do-i-access-my-dashboards"></a>Jak získám přístup do své řídicí panely?
[Můj řídicí panel](../log-analytics/log-analytics-dashboards.md) funkce v Log Analytics je zastaralá. Tato funkce umožňují mít privátní kolekci částí Návrhář zobrazení a nahrazována integrované funkce řídicího panelu systému Azure. Můžete pokračovat k vizualizaci dat v Log Analytics pomocí [Návrhář zobrazení](../log-analytics/log-analytics-view-designer.md) pro sdílená zobrazení. Můžete také připnout vizualizace z těchto zobrazení nebo [jednotlivé dotazy](../log-analytics/log-analytics-tutorial-dashboards.md) do řídicích panelů Azure.

## <a name="how-do-i-check-my-usage"></a>Jak můžu zkontrolovat využití?
Teď můžete snadno zobrazit a spravovat vaše využití a náklady na služby Log Analytics tak, že vyberete **využití a odhadované náklady** ve vašem pracovním prostoru.

![Využití a odhadované náklady](media/log-analytics-oms-portal-faq/usage.png)


## <a name="can-i-still-use-the-classic-portal"></a>Použití portálu classic
Po omezenou dobu, budete k němu přístup portálu přes tuto adresu URL s vlastní název pracovního prostoru: https://\<názvu pracovního prostoru\>. portal.mms.microsoft.com. Doporučujeme, ale pomocí webu Azure portal a poskytnutí zpětné vazby na LAUpgradeFeedback@microsoft.com na libovolným omezujícím problémům.

## <a name="next-steps"></a>Další postup

- [Najít a nainstalovat řešení pro správu](../monitoring/monitoring-solutions.md) pomocí webu Azure portal.
- Další informace o [prohledávání protokolů na webu Azure Portal](log-analytics-log-search-portals.md).
