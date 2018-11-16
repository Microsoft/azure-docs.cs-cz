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
ms.devlang: na
ms.topic: conceptual
ms.date: 11/13/2018
ms.author: magoedte
ms.component: ''
ms.openlocfilehash: 14a33a6480cdb2dbc19b912d51eebda386c55d75
ms.sourcegitcommit: a4e4e0236197544569a0a7e34c1c20d071774dd6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/15/2018
ms.locfileid: "51713159"
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
* Obor pro konfiguraci nastavení, jako je uchovávání informací a dat malá a velká

Z využití pohledu doporučujeme, abyste že nejdříve vytvoříte jako několik pracovních prostorů. Správa a práce s dotazy díky jednodušší a rychlejší. Ale podle výše uvedených charakteristik, můžete chtít vytvořit víc pracovních prostorů, pokud:

* Jste globální společnost a potřebujete ukládat data v různých oblastech z důvodů suverenity dat nebo dodržování předpisů.
* Používáte Azure a chcete se vyhnout poplatkům za odchozí datové přenosy tím, že budete mít pracovní prostor ve stejné oblasti jako prostředky Azure, které spravuje.
* Chcete rozdělit náklady na různá oddělení nebo obchodní skupiny na základě jejich využití tak, že vytvoříte pracovní prostor pro každé oddělení nebo obchodní skupiny ve vlastním předplatném Azure.
* Jste poskytovatel spravované služby a potřebujete uchovávat data Log Analytics pro každého zákazníka odděleně od dat ostatních zákazníků.
* Spravujete víc zákazníků a chcete, aby každý zákazník / oddělení nebo obchodní skupina mohli podívat na svoje vlastní data, ale ne data od ostatních.

Při používání agentů Windows ke shromažďování dat můžete [každého agenta konfigurovat tak, aby ukládal data do jednoho nebo více pracovních prostorů](log-analytics-agent-windows.md).

Pokud používáte System Center Operations Manager, můžete připojit každou skupinu nástroje Operations Manager jen do jednoho pracovního prostoru. Můžete nainstalovat Microsoft Monitoring Agent do počítačů spravovaných nástrojem Operations Manager a nastavit agenta tak, aby odesílal data do nástroje Operations Manager i do jiného pracovního prostoru Log Analytics.

## <a name="workspace-information"></a>Informace o pracovním prostoru

Podrobnosti o pracovním prostoru můžete zobrazit na webu Azure Portal. 

1. Pokud jste to ještě neudělali, přihlaste se k [Portálu Azure](https://portal.azure.com).

2. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.  

    ![portál Azure](media/log-analytics-manage-access/azure-portal-01.png)  

3. V podokně předplatná Log Analytics vyberte pracovní prostor.

4. Na stránce pracovního prostoru se zobrazí podrobnosti o zahájení práce, konfiguraci a odkazy na další informace.  

    ![Podrobnosti o pracovním prostoru](./media/log-analytics-manage-access/workspace-overview-page.png)  

## <a name="manage-accounts-and-users"></a>Správa účtů a uživatelů
Každý pracovní prostor může mít přiřazených více účtů a každý účet může mít přístup k několika pracovním prostorům. Přístup ke správě [přístupu na základě rolí Azure](../role-based-access-control/role-assignments-portal.md). Tato přístupová práva se vztahuje na webu Azure portal i na přístup k rozhraní API.


Následující aktivity také vyžadují oprávnění Azure:

| Akce                                                          | Potřebná oprávnění Azure | Poznámky |
|-----------------------------------------------------------------|--------------------------|-------|
| Přidání a odebrání řešení pro správu                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Tato oprávnění je potřeba udělit na úrovni skupiny prostředků nebo předplatného. |
| Změna cenové úrovně                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Zobrazení dat na dlaždicích řešení *Backup* a *Site Recovery* | Správce nebo spolusprávce | Má přístup k prostředkům nasazeným pomocí modelu nasazení Azure Classic. |
| Vytvoření pracovního prostoru na webu Azure Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


### <a name="managing-access-to-log-analytics-using-azure-permissions"></a>Správa přístupu k Log Analytics pomocí oprávnění Azure
Pokud chcete udělit přístup k Log Analytics pomocí oprávnění Azure, postupujte podle kroků v tématu [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../role-based-access-control/role-assignments-portal.md).

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

Doporučujeme provést přiřazení na úrovni prostředků (pracovního prostoru), abyste zajistili přesné řízení přístupu.  Pomocí [vlastních rolí](../role-based-access-control/custom-roles.md) můžete vytvářet role s konkrétními požadovanými oprávněními.

## <a name="link-an-existing-workspace-to-an-azure-subscription"></a>Připojení existujícího pracovního prostoru k předplatnému Azure
Všechny pracovní prostory vytvořené po 26. září 2016 musí být propojené s předplatným Azure v okamžiku vytvoření. Pracovní prostory vytvořené před tímto datem je potřeba propojit s předplatným při přihlášení. Když vytváříte pracovní prostor z webu Azure Portal nebo propojujete pracovní prostor s předplatným Azure, služba Azure Active Directory se propojí jako účet vaší organizace.

### <a name="to-link-a-workspace-to-an-azure-subscription-in-the-azure-portal"></a>Propojení pracovního prostoru s předplatným Azure na webu Azure Portal
1. Na webu Azure Portal klikněte na **Všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics**.  

2. V podokně předplatná Log Analytics, klikněte na tlačítko **přidat**.  

    ![Seznam pracovních prostorů](./media/log-analytics-manage-access/workspace-link-existing-01.png)

3. Z **pracovní prostor Log Analytics** podokně klikněte na tlačítko **propojit stávající**.  

4. Klikněte na **Configure required settings** (Konfigurovat požadovaná nastavení).  

5. Uvidíte seznam pracovních prostorů, které ještě nejsou propojené s vaším účtem Azure. Vyberte pracovní prostor.  
   
6. Je-li to třeba, můžete změnit hodnoty následujících položek:
   * Předplatné
   * Skupina prostředků
   * Umístění
   * Cenová úroveň  

7. Klikněte na **OK**. Pracovní prostor je teď propojený s vaším účtem Azure.

> [!NOTE]
> Pokud nevidíte pracovní prostor, s kterým chcete účet propojit, znamená to, že vaše předplatné Azure nemá přístup k pracovnímu prostoru, který jste vytvořili na portálu OMS.  Informace o udělení přístupu k tomuto účtu z portálu OMS najdete v části [Přidání uživatele do existujícího pracovního prostoru](#add-a-user-to-an-existing-workspace).
>
>

## <a name="upgrade-a-workspace-to-a-paid-plan"></a>Upgrade pracovního prostoru na placený tarif
Ve službě OMS existují tři tarify pracovního prostoru: **Free**, **Standalone** a **Premium**.  Pokud používáte tarif *Free*, existuje limit 500 MB pro odesílání dat do služby Log Analytics.  Pokud toto množství překročíte, budete muset přejít na placený tarif, aby se vám neshromažďovala data nad tento limit. Svůj tarif můžete kdykoli změnit.  Informace o cenách OMS najdete na stránce [podrobností o cenách](https://www.microsoft.com/en-us/cloud-platform/operations-management-suite-pricing).

### <a name="using-entitlements-from-an-oms-subscription"></a>Používání nároků z předplatného OMS
Pokud chcete používat nároky z nákupu OMS E1, OMS E2 OMS nebo doplňku OMS pro System Center, zvolte tarif *OMS* služby OMS Log Analytics.

Když si koupíte předplatné OMS, nároky se přidají do vaší smlouvy Enterprise. Jakékoli předplatné vytvořené v rámci této smlouvy může tento nárok uplatnit. Všechny pracovní prostory v těchto předplatných uplatňují nárok na OMS.

Pokud se chcete ujistit, že váš pracovní prostor využívá nárok plynoucí z předplatného OMS, proveďte následující:

1. Vytvořte pracovní prostor v předplatném Azure, které je součástí smlouvy Enterprise obsahující předplatné OMS.

2. Vyberte pro pracovní prostor tarif *OMS*.

> [!NOTE]
> Pokud jste pracovní prostor vytvořili před 26. zářím 2016 a cenový tarif služby Log Analytics je *Premium*, bude tento pracovní prostor uplatňovat nárok z doplňku OMS pro System Center. Své nároky můžete využít také tak, že přejdete na cenovou úroveň *OMS*.
>
>

Nároky na předplatné OMS nejsou viditelné na webu Azure Portal. Tento nárok uvidíte jen na webu Enterprise Portal.  

Pokud potřebujete změnit předplatné Azure, se kterým je pracovní prostor propojený, můžete použít rutinu prostředí Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).

### <a name="using-azure-commitment-from-an-enterprise-agreement"></a>Využití závazků Azure ze smlouvy Enterprise
Pokud nemáte předplatné OMS, platíte za každou součást OMS zvlášť a využití se zobrazí na faktuře Azure.

Pokud jsou vaše předplatná Azure propojena se smlouvou Enterprise s finančním závazkem, využití služby Log Analytics se bude automaticky strhávat ze zbývajícího finančního závazku.

Pokud potřebujete změnit předplatné Azure, se kterým je pracovní prostor propojený, můžete použít rutinu prostředí Azure PowerShell [Move-AzureRmResource](https://msdn.microsoft.com/library/mt652516.aspx).  

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-azure-portal"></a>Změna pracovního prostoru na placenou cenovou úroveň na webu Azure Portal
1. Na webu Azure Portal v podokně předplatná Log Analytics vyberte pracovní prostor.

2. V podokně pracovní prostor v rámci **Obecné**vyberte **cenová úroveň**.  

3. V části **cenová úroveň**, vyberte cenovou úroveň a potom klikněte na tlačítko **vyberte**.  
    ![Vybraná cenový plán](./media/log-analytics-manage-access/workspace-pricing-tier-info.png)

> [!NOTE]
> Pokud je váš pracovní prostor propojený s účtem Automation, musíte před tím, než budete moci vybrat cenovou úroveň *Standalone (za GB)*, odstranit všechna řešení **Automation and Control** a zrušit propojení s účtem Automation. V okně pracovního prostoru v části **Obecné** klikněte na **Řešení**. Zobrazí se řešení a můžete je odstranit. Propojení s účtem Automation zrušíte kliknutím na název účtu Automation v okně **Cenová úroveň**.
>
>

### <a name="change-a-workspace-to-a-paid-pricing-tier-in-the-oms-portal"></a>Změna pracovního prostoru na placenou cenovou úroveň na portálu OMS

Pokud chcete změnit cenovou úroveň pomocí portálu OMS, musíte mít předplatné Azure.

1. Na portálu OMS klikněte na dlaždici **Nastavení**.

2. Klikněte na kartu **Accounts** (Účty) a potom na kartu **Azure Subscription & Data Plan** (Předplatné a datový tarif Azure).

3. Klikněte na cenovou úroveň, kterou chcete použít.

4. Klikněte na **Uložit**.  

    ![Předplatné a datové tarify](./media/log-analytics-manage-access/subscription-tab.png)

Váš nový datový tarif se zobrazí pásu karet portálu OMS v horní části webové stránky.

![Pás karet OMS](./media/log-analytics-manage-access/data-plan-changed.png)

## <a name="next-steps"></a>Další postup
* Zobrazit [přehled agenta Log Analytics](log-analytics-agent-overview.md) ke shromažďování dat z počítačů ve vašem datovém centru nebo jiných cloudovém prostředí.
* V článku [Shromažďování dat o virtuálních počítačích Azure](log-analytics-quick-collect-azurevm.md) najdete informace o konfiguraci shromažďování dat z virtuálních počítačů Azure.  
* Článek [Přidání řešení Log Analytics z galerie řešení](../azure-monitor/insights/solutions.md) popisuje přidání funkcí a shromažďování dat.

