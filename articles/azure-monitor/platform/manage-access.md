---
title: Správa pracovních prostorů Log Analytics ve službě Azure Monitor | Dokumentace Microsoftu
description: Můžete spravovat pracovní prostory Log Analytics ve službě Azure Monitor pomocí různých úloh správy uživatelů, účty, pracovními prostory a účty Azure.
services: log-analytics
documentationcenter: ''
author: mgoedtel
manager: carmonm
editor: ''
ms.assetid: d0e5162d-584b-428c-8e8b-4dcaa746e783
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 02/07/2019
ms.author: magoedte
ms.openlocfilehash: 4a777c2bd57d40b4bb6c8d36c996b655cb019e5f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/11/2019
ms.locfileid: "56005365"
---
# <a name="manage-log-analytics-workspaces-in-azure-monitor"></a>Správa pracovních prostorů Log Analytics ve službě Azure Monitor
Azure Monitor úložiště vytvářet protokoly dat v pracovním prostoru Log Analytics, která je v podstatě kontejner, který obsahuje data a informace o konfiguraci. Ke správě přístupu k protokolování dat, můžete provádět různé úlohy správy související s pracovními prostory. Vy nebo další členové vaší organizace můžete používat víc pracovních prostorů ke správě různých sad dat, která se shromažďují ze všech částí vaší infrastruktury IT.

K vytvoření pracovního prostoru budete muset:

1. Mít předplatné Azure.
2. Zvolit název pracovního prostoru.
3. Přidružte pracovní prostor s jednou z vašich předplatných a skupin prostředků.
4. Vybrat zeměpisné umístění.

## <a name="determine-the-number-of-workspaces-you-need"></a>Určení potřebného počtu pracovních prostorů
Pracovní prostor Log Analytics je prostředek Azure a je kontejner se shromažďují data, agregují, analyzují a zobrazí ve službě Azure Monitor.

Můžete mít několik pracovních prostorů na předplatné Azure a budete mít přístup k více než jednomu pracovnímu prostoru, umožňuje snadno vytvářet dotazy mezi nimi. Tato část popisuje případy, kdy je užitečné mít víc než jeden pracovní prostor.

Pracovní prostor Log Analytics nabízí:

* Zeměpisné umístění úložiště dat
* Izolace dat k definování různých uživatelská přístupová práva
* Obor pro konfiguraci nastavení, jako jsou [cenovou úroveň](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [uchování](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) a [malá a velká data](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap) 

Z využití pohledu doporučujeme, abyste že nejdříve vytvoříte jako několik pracovních prostorů. Správa a práce s dotazy díky jednodušší a rychlejší. Ale podle výše uvedených charakteristik, můžete chtít vytvořit víc pracovních prostorů, pokud:

* Jste globální společnost a potřebujete protokolovat data uložená v různých oblastech z důvodů suverenity nebo dodržování předpisů data.
* Používáte Azure a chcete se vyhnout poplatkům za odchozí datové přenosy tím, že budete mít pracovní prostor ve stejné oblasti jako prostředky Azure, které spravuje.
* Chcete rozdělit náklady na různá oddělení nebo obchodní skupiny na základě jejich využití tak, že vytvoříte pracovní prostor pro každé oddělení nebo obchodní skupiny ve vlastním předplatném Azure.
* Jste poskytovatel spravované služby a potřebujete uchovávat data Log Analytics pro každého zákazníka odděleně od dat ostatních zákazníků.
* Spravujete víc zákazníků a chcete, aby každý zákazník / oddělení nebo obchodní skupina mohli podívat na svoje vlastní data, ale ne data od ostatních.

Při používání agentů Windows ke shromažďování dat můžete [každého agenta konfigurovat tak, aby ukládal data do jednoho nebo více pracovních prostorů](../../azure-monitor/platform/agent-windows.md).

Pokud používáte System Center Operations Manager, můžete připojit každou skupinu nástroje Operations Manager jen do jednoho pracovního prostoru. Můžete nainstalovat Microsoft Monitoring Agent do počítačů spravovaných nástrojem Operations Manager a nastavit agenta tak, aby odesílal data do nástroje Operations Manager i do jiného pracovního prostoru Log Analytics.

## <a name="workspace-information"></a>Informace o pracovním prostoru
Při analýze dat v pracovním prostoru Log Analytics v **Azure Monitor** nabídky na webu Azure Portal, můžete vytvořit a spravovat pracovní prostory v **pracovních prostorů Log Analytics** nabídky.
 

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a klikněte na tlačítko **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics** pracovní prostory.  

    ![portál Azure](media/manage-access/azure-portal-01.png)  

3. Vyberte pracovní prostor ze seznamu.

4. Na stránce pracovního prostoru se zobrazí podrobnosti o zahájení práce, konfiguraci a odkazy na další informace.  

    ![Podrobnosti o pracovním prostoru](./media/manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>Správa účtů a uživatelů
Každý pracovní prostor může mít přiřazených více účtů a každý účet může mít přístup k několika pracovním prostorům. Přístup ke správě [přístupu na základě rolí Azure](../../role-based-access-control/role-assignments-portal.md). Tato přístupová práva se vztahuje na webu Azure portal i na přístup k rozhraní API.


Následující aktivity také vyžadují oprávnění Azure:

| Akce                                                          | Potřebná oprávnění Azure | Poznámky |
|-----------------------------------------------------------------|--------------------------|-------|
| Přidání a odebrání řešení pro správu                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Tato oprávnění je potřeba udělit na úrovni skupiny prostředků nebo předplatného. |
| Změna cenové úrovně                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Zobrazení dat na dlaždicích řešení *Backup* a *Site Recovery* | Správce nebo spolusprávce | Má přístup k prostředkům nasazeným pomocí modelu nasazení Azure Classic. |
| Vytvoření pracovního prostoru na webu Azure Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-workspace-using-azure-permissions"></a>Správa přístupu k pracovnímu prostoru Log Analytics pomocí oprávnění Azure
Pokud chcete udělit přístup k Log Analytics pomocí oprávnění Azure, postupujte podle kroků v tématu [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md).

Azure má dvě předdefinované role uživatele pro pracovní prostory Log Analytics:
- Čtenář Log Analytics
- Přispěvatel Log Analytics

Členové role *Čtenář Log Analytics* můžou provádět:
- Zobrazení a prohledávání všech dat monitorování 
- Zobrazení nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure pro všechny prostředky Azure

Role Čtenář Log Analytics zahrnuje Azure takto:

| Typ    | Oprávnění | Popis |
| ------- | ---------- | ----------- |
| Akce | `*/read`   | Možnost zobrazit všechny prostředky Azure a jejich konfiguraci. To zahrnuje zobrazení: <br> Stavu rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure pro prostředky <br> Všech vlastnosti a nastavení všech prostředků |
| Akce | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Možnost provádět dotazy prohledávání protokolů v2 |
| Akce | `Microsoft.OperationalInsights/workspaces/search/action` | Možnost provádět dotazy prohledávání protokolů v1 |
| Akce | `Microsoft.Support/*` | Možnost otevírat případy podpory |
|Jiný než akce | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Brání čtení pracovního prostoru klíče potřebné k používání rozhraní API pro shromažďování dat a nainstalovat agenty. To zabrání uživateli v přidávání nových prostředků do pracovního prostoru |


Členové role *Přispěvatel Log Analytics* můžou provádět:
- Jak Čtenář Log Analytics může číst všechna data monitorování  
- Vytváření a konfigurace účtů služby Automation  
- Přidání a odebrání řešení pro správu    
    > [!NOTE] 
    > Aby bylo možné úspěšně provést dvě poslední akce, musí být udělena na úrovni skupiny nebo předplatného resource toto oprávnění.  

- Čtení klíčů účtu úložiště   
- Konfigurace shromažďování protokolů ze služby Azure Storage  
- Úprava nastavení monitorování pro prostředky Azure, včetně
  - Přidání rozšíření virtuálního počítače na virtuální počítače
  - Konfigurace diagnostiky Azure pro všechny prostředky Azure

> [!NOTE] 
> Možnost přidat rozšíření virtuálního počítače na virtuální počítač můžete použít k získání úplné kontroly nad virtuálním počítačem.

Role Přispěvatel Log Analytics zahrnuje Azure takto:

| Oprávnění | Popis |
| ---------- | ----------- |
| `*/read`     | Možnost zobrazit všechny prostředky a jejich konfiguraci. To zahrnuje zobrazení: <br> Stavu rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure pro prostředky <br> Všech vlastnosti a nastavení všech prostředků |
| `Microsoft.Automation/automationAccounts/*` | Možnost vytvořit a konfigurovat účty služby Azure Automation, včetně přidávání a úprav runbooků |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Přidání, aktualizace a odebrání rozšíření virtuálního počítače, včetně rozšíření Microsoft Monitoring Agent a rozšíření Agent OMS pro Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Zobrazení klíče účtu úložiště. Požadovaný ke konfiguraci Log Analytics pro čtení protokolů z účtů služby Azure Storage |
| `Microsoft.Insights/alertRules/*` | Přidání, aktualizace a odebrání pravidel upozornění |
| `Microsoft.Insights/diagnosticSettings/*` | Přidání, aktualizace a odebrání nastavení diagnostiky pro prostředky Azure |
| `Microsoft.OperationalInsights/*` | Přidání, aktualizace a odebrání konfigurace pro pracovní prostory Log Analytics |
| `Microsoft.OperationsManagement/*` | Přidání a odebrání řešení pro správu |
| `Microsoft.Resources/deployments/*` | Vytvoření a odstranění nasazení. Požadováno pro přidávání a odebírání řešení, pracovních prostorů a účtů služby Automation |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Vytvoření a odstranění nasazení. Požadováno pro přidávání a odebírání řešení, pracovních prostorů a účtů služby Automation |

Pokud chcete přidat uživatele do role uživatele nebo je z ní odebrat, je potřeba mít oprávnění `Microsoft.Authorization/*/Delete` a `Microsoft.Authorization/*/Write`.

Pomocí těchto rolí můžete uživatelům udělit přístup v různých oborech:
- Předplatné – Přístup ke všem pracovním prostorům v rámci předplatného
- Skupina prostředků – Přístup ke všem pracovním prostorům v rámci skupiny prostředků
- Prostředek – Přístup pouze k zadanému pracovnímu prostoru

Doporučujeme provést přiřazení na úrovni prostředků (pracovního prostoru), abyste zajistili přesné řízení přístupu.  Pomocí [vlastních rolí](../../role-based-access-control/custom-roles.md) můžete vytvářet role s konkrétními požadovanými oprávněními.

## <a name="next-steps"></a>Další postup
* Zobrazit [přehled agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) ke shromažďování dat z počítačů ve vašem datovém centru nebo jiných cloudovém prostředí.
* V článku [Shromažďování dat o virtuálních počítačích Azure](../../azure-monitor/learn/quick-collect-azurevm.md) najdete informace o konfiguraci shromažďování dat z virtuálních počítačů Azure.  

