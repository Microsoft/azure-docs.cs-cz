---
title: Správa pracovních prostorů v Azure Log Analytics a na portálu OMS | Dokumentace Microsoftu
description: Pracovní prostory můžete spravovat v Log Analytics a na portálu OMS pomocí různých úloh správy prováděných s uživateli, účty, pracovními prostory a účty Azure.
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
ms.date: 11/13/2018
ms.author: magoedte
ms.openlocfilehash: 32a31a87bacbb13cd3b2cb4561ac04e54d51ba46
ms.sourcegitcommit: de32e8825542b91f02da9e5d899d29bcc2c37f28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2019
ms.locfileid: "55656749"
---
# <a name="manage-workspaces"></a>Správa pracovních prostorů

Pro správu přístupu k Log Analytics provádíte různé úlohy správy související s pracovními prostory. Tento článek obsahuje pokyny a postupy při správě pracovních prostorů. Pracovní prostor je v podstatě kontejner, který obsahuje informace o účtu a jednoduché konfigurační informace. Vy nebo další členové vaší organizace můžete používat víc pracovních prostorů ke správě různých sad dat, která se shromažďují ze všech částí vaší infrastruktury IT.

K vytvoření pracovního prostoru budete muset:

1. Mít předplatné Azure.
2. Zvolit název pracovního prostoru.
3. Přidružte pracovní prostor s jednou z vašich předplatných a skupin prostředků.
4. Vybrat zeměpisné umístění.

## <a name="determine-the-number-of-workspaces-you-need"></a>Určení potřebného počtu pracovních prostorů
Pracovní prostor je prostředek Azure v podobě kontejneru, ve kterém se data shromažďují, agregují, analyzují a zobrazují na webu Azure Portal.

V rámci jednoho předplatného Azure můžete mít více pracovních prostorů a přístup k více než jednomu pracovnímu prostoru s možností snadného dotazování napříč pracovními prostory. Tato část popisuje případy, kdy je užitečné mít víc než jeden pracovní prostor.

V současné době pracovní prostor nabízí:

* Zeměpisné umístění úložiště dat
* Izolace dat k definování různých uživatelská přístupová práva
* Obor pro konfiguraci nastavení, jako jsou [cenovou úroveň](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [uchování](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) a [malá a velká data](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap) 

Z využití pohledu doporučujeme, abyste že nejdříve vytvoříte jako několik pracovních prostorů. Správa a práce s dotazy díky jednodušší a rychlejší. Ale podle výše uvedených charakteristik, můžete chtít vytvořit víc pracovních prostorů, pokud:

* Jste globální společnost a potřebujete ukládat data v různých oblastech z důvodů suverenity dat nebo dodržování předpisů.
* Používáte Azure a chcete se vyhnout poplatkům za odchozí datové přenosy tím, že budete mít pracovní prostor ve stejné oblasti jako prostředky Azure, které spravuje.
* Chcete rozdělit náklady na různá oddělení nebo obchodní skupiny na základě jejich využití tak, že vytvoříte pracovní prostor pro každé oddělení nebo obchodní skupiny ve vlastním předplatném Azure.
* Jste poskytovatel spravované služby a potřebujete uchovávat data Log Analytics pro každého zákazníka odděleně od dat ostatních zákazníků.
* Spravujete víc zákazníků a chcete, aby každý zákazník / oddělení nebo obchodní skupina mohli podívat na svoje vlastní data, ale ne data od ostatních.

Při používání agentů Windows ke shromažďování dat můžete [každého agenta konfigurovat tak, aby ukládal data do jednoho nebo více pracovních prostorů](../../azure-monitor/platform/agent-windows.md).

Pokud používáte System Center Operations Manager, můžete připojit každou skupinu nástroje Operations Manager jen do jednoho pracovního prostoru. Můžete nainstalovat Microsoft Monitoring Agent do počítačů spravovaných nástrojem Operations Manager a nastavit agenta tak, aby odesílal data do nástroje Operations Manager i do jiného pracovního prostoru Log Analytics.

## <a name="workspace-information"></a>Informace o pracovním prostoru

Podrobnosti o pracovním prostoru můžete zobrazit na webu Azure Portal. 

1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com).

2. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.  

    ![portál Azure](media/manage-access/azure-portal-01.png)  

3. V podokně předplatná Log Analytics vyberte pracovní prostor.

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


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Správa přístupu k Log Analytics pomocí oprávnění Azure
Pokud chcete udělit přístup k Log Analytics pomocí oprávnění Azure, postupujte podle kroků v tématu [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md).

Azure má pro Log Analytics dvě předdefinované role uživatele:
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
* Článek [Přidání řešení Log Analytics z galerie řešení](../../azure-monitor/insights/solutions.md) popisuje přidání funkcí a shromažďování dat.

