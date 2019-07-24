---
title: Správa pracovních prostorů Log Analytics v Azure Monitor | Microsoft Docs
description: Pracovní prostory Log Analytics můžete spravovat v Azure Monitor pomocí různých úloh správy pro uživatele, účty, pracovní prostory a účty Azure.
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
ms.date: 07/16/2019
ms.author: magoedte
ms.openlocfilehash: fbfbd8e26ab3e92f06194322be7ec2be2fb180fd
ms.sourcegitcommit: a6873b710ca07eb956d45596d4ec2c1d5dc57353
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/16/2019
ms.locfileid: "68254463"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Správa dat protokolu a pracovních prostorů v Azure Monitor
Azure Monitor ukládá data protokolu v pracovním prostoru Log Analytics, což je v podstatě kontejner, který obsahuje informace o datech a konfiguraci. Ke správě přístupu k datům protokolu provádíte různé úlohy správy související s pracovními prostory. Vy nebo další členové vaší organizace můžete používat víc pracovních prostorů ke správě různých sad dat, která se shromažďují ze všech částí vaší infrastruktury IT.

Tento článek vysvětluje, jak spravovat přístup k protokolům a spravovat pracovní prostory, které je obsahují. 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
K vytvoření pracovního prostoru Log Analytics musíte:

1. Mít předplatné Azure.
2. Zvolit název pracovního prostoru.
3. Přidružte pracovní prostor s jednou z vašich předplatných a skupin prostředků.
4. Vybrat zeměpisné umístění.

Podrobné informace o vytváření pracovního prostoru najdete v následujících článcích:

- [Vytvoření pracovního prostoru Log Analytics v Azure Portal](../learn/quick-create-workspace.md)
- [Vytvoření pracovního prostoru Log Analytics pomocí Azure CLI 2,0](../learn/quick-create-workspace-cli.md)
- [Vytvoření pracovního prostoru Log Analytics pomocí Azure PowerShell](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>Určení potřebného počtu pracovních prostorů
Log Analytics pracovní prostor je prostředek Azure a je kontejner, ve kterém se data shromažďují, agregují, analyzují a prezentují v Azure Monitor. V každém předplatném Azure můžete mít několik pracovních prostorů a můžete mít přístup k více než jednomu pracovnímu prostoru, abyste se mohli snadno dotazovat napříč nimi. Tato část popisuje případy, kdy je užitečné mít víc než jeden pracovní prostor.

Pracovní prostor Log Analytics poskytuje:

* Geografické umístění pro ukládání dat.
* Izolace dat pro definování různých přístupových práv uživatele v režimu orientovaném na pracovní prostor. Není relevantní při práci v režimu orientovaném na prostředky.
* Obor pro konfiguraci nastavení, jako je [cenová úroveň](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [uchování](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) a [capping dat](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap)
* V prostředku pracovního prostoru se účtují poplatky související s přijímáním a uchováváním dat.

Z využití pohledu doporučujeme, abyste že nejdříve vytvoříte jako několik pracovních prostorů. Správa a práce s dotazy díky jednodušší a rychlejší. Ale podle výše uvedených charakteristik, můžete chtít vytvořit víc pracovních prostorů, pokud:

* Jste globální společnost a potřebujete protokolovat data uložená v určitých oblastech pro účely svrchovanosti nebo dodržování předpisů.
* Používáte Azure a chcete se vyhnout poplatkům za odchozí datové přenosy tím, že budete mít pracovní prostor ve stejné oblasti jako prostředky Azure, které spravuje.
* Jste poskytovatel spravované služby a potřebujete uchovávat data Log Analytics pro každého zákazníka odděleně od dat ostatních zákazníků.
* Můžete spravovat víc zákazníků a chcete, aby každý zákazník, oddělení nebo obchodní skupina viděli svoje vlastní data, ale ne data od ostatních a nemuseli si konsolidovat konsolidovanou oblast mezi zákazníky, oddělením nebo obchodní skupinou.

Při používání agentů Windows ke shromažďování dat můžete [každého agenta konfigurovat tak, aby ukládal data do jednoho nebo více pracovních prostorů](../../azure-monitor/platform/agent-windows.md).

Pokud používáte System Center Operations Manager, můžete připojit každou skupinu nástroje Operations Manager jen do jednoho pracovního prostoru. Můžete nainstalovat Microsoft Monitoring Agent do počítačů spravovaných nástrojem Operations Manager a nastavit agenta tak, aby odesílal data do nástroje Operations Manager i do jiného pracovního prostoru Log Analytics.

Po definování architektury pracovního prostoru byste měli vymáhat tuto zásadu pro prostředky Azure pomocí [Azure Policy](../../governance/policy/overview.md). To může poskytovat vestavěnou definici, která se bude automaticky vztahovat na všechny prostředky Azure. Můžete například nastavit zásadu, která zajistí, že všechny prostředky Azure v konkrétní oblasti odesílají všechny své diagnostické protokoly do konkrétního pracovního prostoru.

## <a name="view-workspace-details"></a>Zobrazit podrobnosti pracovního prostoru
Při analýze dat v pracovním prostoru Log Analytics v nabídce **Azure monitor** v Azure Portal můžete vytvářet a spravovat pracovní prostory v nabídce **Log Analytics pracovní prostory** .
 

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a klikněte na **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics** pracovní prostory.  

    ![portál Azure](media/manage-access/azure-portal-01.png)  

3. V seznamu vyberte svůj pracovní prostor.

4. Na stránce pracovní prostor se zobrazí podrobnosti o pracovním prostoru, začínáme, konfiguraci a odkazy, kde najdete další informace.  

    ![Podrobnosti o pracovním prostoru](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Oprávnění a obor v pracovním prostoru
Data, ke kterým má uživatel přístup, jsou určena více faktory, které jsou uvedeny v následující tabulce. Jednotlivé jsou popsány v následujících částech.

| faktor | Popis |
|:---|:---|
| [Režim přístupu](#access-modes) | Metoda, kterou uživatel používá pro přístup k pracovnímu prostoru.  Definuje rozsah dostupných dat a režim řízení přístupu, který se použije. |
| [Režim řízení přístupu](#access-control-mode) | Nastavení v pracovním prostoru definující, zda jsou oprávnění použita na úrovni pracovního prostoru nebo prostředku. |
| [Oprávnění](#manage-accounts-and-users) | Oprávnění použitá pro jednotlivé nebo skupiny uživatelů pro pracovní prostor nebo prostředek. Definuje data, ke kterým má uživatel přístup. |
| [RBAC na úrovni tabulky](#table-level-rbac) | Volitelná podrobné oprávnění, která platí pro všechny uživatele bez ohledu na jejich režim přístupu nebo režim řízení přístupu. Definuje typy dat, ke kterým má uživatel přístup. |



## <a name="access-modes"></a>Režimy přístupu
_Režim přístupu_ odkazuje na to, jak uživatel přistupuje k pracovnímu prostoru Log Analytics a definuje rozsah dat, ke kterým mají přístup. 

**Workspace-centric**: V tomto režimu může uživatel zobrazit všechny protokoly v pracovním prostoru, ke kterým mají oprávnění. Dotazy v tomto režimu jsou vymezeny na všechna data ve všech tabulkách v pracovním prostoru. Jedná se o režim přístupu, který se používá v případě, že se k protokolům přistupuje v pracovním prostoru jako s oborem, například při výběru **protokolů** z nabídky **Azure monitor** v Azure Portal.

**Orientované na prostředky**: Když přistupujete k pracovnímu prostoru pro konkrétní prostředek, například když v Azure Portal vyberete **protokoly** z nabídky prostředku, můžete zobrazit protokoly jenom pro tento prostředek ve všech tabulkách, ke kterým máte přístup. Dotazy v tomto režimu jsou vymezeny jenom na data přidružená k tomuto prostředku. Tento režim také umožňuje podrobné řízení přístupu na základě role (RBAC). 

> [!NOTE]
> Protokoly jsou k dispozici pro dotazy zaměřené na prostředky pouze v případě, že byly správně přidruženy k příslušnému prostředku. V současné době mají tato omezení následující zdroje: 
> - Počítače mimo Azure
> - Service Fabric
> - Application Insights
> - Containers
>
> Spuštěním dotazu a kontrolou záznamů, které vás zajímají, můžete otestovat, jestli jsou protokoly správně přidružené ke svým prostředkům. Pokud je ve vlastnosti [_ResourceId](log-standard-properties.md#_resourceid) správné ID prostředku, data jsou k dispozici pro dotazy orientované na prostředky.

### <a name="comparing-access-modes"></a>Porovnání režimů přístupu

Režimy přístupu jsou shrnuté v následující tabulce:

| | Orientované na pracovní prostor | Orientované na prostředky |
|:---|:---|:---|
| Pro koho je každý model určen? | Centrální správa. Správci, kteří potřebují nakonfigurovat shromažďování dat a uživatele, kteří potřebují přístup k nejrůznějším prostředkům. V současnosti se vyžaduje pro uživatele, kteří mají přístup k protokolům pro prostředky mimo Azure. | Týmy aplikace Správci prostředků Azure, které jsou monitorovány. |
| Co uživatel potřebuje k zobrazení protokolů? | Oprávnění k pracovnímu prostoru. V tématu **oprávnění k pracovnímu prostoru** v části [Správa účtů a uživatelů](#manage-accounts-and-users). | Přístup pro čtení k prostředku. Podívejte se na téma **oprávnění prostředků** v tématu [Správa účtů a uživatelů](#manage-accounts-and-users). Oprávnění lze zdědit (například z obsahující skupiny prostředků) nebo přímo přiřadit prostředku. Automaticky se přiřadí oprávnění k protokolům pro daný prostředek. |
| Jaký je rozsah oprávnění? | Stejných. Uživatelé s přístupem k pracovnímu prostoru mohou dotazovat všechny protokoly v tomto pracovním prostoru z tabulek, ke kterým mají oprávnění. Viz [řízení přístupu k tabulce](#table-level-rbac) | Prostředek Azure. Uživatel může v protokolech zadávat dotazy na prostředky, ke kterým mají přístup z libovolného pracovního prostoru, ale nemůžou zadávat dotazy na jiné prostředky. |
| Jak můžou protokoly přístupu uživatele? | Spusťte **protokoly** z nabídky **Azure monitor** nebo z **pracovních prostorů Log Analytics**. | Spusťte **protokoly** z nabídky pro prostředek Azure. |


## <a name="access-control-mode"></a>Režim řízení přístupu
_Režim řízení přístupu_ je nastavení v jednotlivých pracovních prostorech, která definují, jak jsou pro tento pracovní prostor určena oprávnění.

**Vyžadovat oprávnění k pracovnímu prostoru**:  Tento režim řízení neumožňuje podrobnou RBAC. Pro uživatele, kteří mají přístup k pracovnímu prostoru, musí mít udělená oprávnění k pracovnímu prostoru nebo určitým tabulkám. 

Pokud uživatel přistupuje k pracovnímu prostoru v režimu orientovaném na pracovní ploše, bude mít přístup ke všem datům, ke kterým mají přístup všechny tabulky, ke kterým byl udělen přístup. Pokud uživatel přistupuje k pracovnímu prostoru v režimu orientovaném na prostředky, bude mít přístup pouze k datům pro daný prostředek v jakýchkoli tabulkách, ke kterým byl udělen přístup.

Toto je výchozí nastavení pro všechny pracovní prostory vytvořené před březen 2019.

**Použít oprávnění prostředku nebo pracovního prostoru**: Tento režim řízení umožňuje členitou RBAC. Uživatelům se udělí přístup jenom k datům přidruženým k prostředkům, které můžou zobrazit prostřednictvím oprávnění Azure, prostředků, ke kterým `read` mají oprávnění. 

Když uživatel přistupuje k pracovnímu prostoru v režimu orientovaném na pracovní ploše, budou platit oprávnění pracovního prostoru. Když uživatel přistupuje k pracovnímu prostoru v režimu orientovaném na prostředky, ověří se jenom oprávnění prostředků a oprávnění pracovního prostoru se budou ignorovat. Povolte RBAC pro uživatele odebráním z oprávnění pracovního prostoru a povolením rozpoznání jejich oprávnění k prostředkům.

Toto je výchozí nastavení pro všechny pracovní prostory vytvořené po březnu 2019.

> [!NOTE]
> Pokud má uživatel k pracovnímu prostoru oprávnění pouze prostředky, bude mít přístup k pracovnímu prostoru pouze pomocí [režimu orientovaného na prostředky](#access-modes).


### <a name="define-access-control-mode-in-azure-portal"></a>Definování režimu řízení přístupu v Azure Portal
Aktuální režim řízení přístupu k pracovnímu prostoru můžete zobrazit na stránce **Přehled** pracovního prostoru v nabídce **Log Analytics pracovní** prostor.

![Zobrazit režim řízení přístupu k pracovnímu prostoru](media/manage-access/view-access-control-mode.png)

Toto nastavení můžete změnit na stránce **vlastností** pracovního prostoru. Změna nastavení se zakáže, pokud nemáte oprávnění ke konfiguraci pracovního prostoru.

![Změnit režim přístupu k pracovnímu prostoru](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Definování režimu řízení přístupu v PowerShellu

Pomocí následujícího příkazu prověřte režim řízení přístupu pro všechny pracovní prostory v rámci předplatného:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

Pomocí následujícího skriptu nastavte režim řízení přístupu pro konkrétní pracovní prostor:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Pomocí následujícího skriptu nastavte režim řízení přístupu pro všechny pracovní prostory v předplatném.

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Definování režimu přístupu v šabloně Správce prostředků
Pro konfiguraci režimu přístupu v šabloně Azure Resource Manager nastavte příznak funkce **enableLogAccessUsingOnlyResourcePermissions** v pracovním prostoru na jednu z následujících hodnot.

- **NEPRAVDA**: Nastavte pracovní prostor na oprávnění orientované na pracovní prostor. Toto je výchozí nastavení, pokud příznak není nastaven.
- **hodnota true**: Nastavte pracovní prostor na oprávnění orientované na prostředky.


## <a name="manage-accounts-and-users"></a>Správa účtů a uživatelů
Oprávnění k pracovnímu prostoru, který se použije pro konkrétního uživatele, jsou definovaná v režimu přístupu a v [režimu řízení přístupu](#access-control-mode) pracovního prostoru. **Oprávnění k pracovnímu prostoru** se používají, když uživatel přistupuje k  libovolnému pracovnímu prostoru v [režimu orientovaném](#access-modes)na pracovní prostor. **Oprávnění prostředků** se použijí, když uživatel přistupuje k pracovnímu prostoru s oprávněním k použití [režimu řízení přístupu](#access-control-mode) **prostředku nebo pracovního prostoru** pomocí [režimu orientovaného na prostředky](#access-modes).

### <a name="workspace-permissions"></a>Oprávnění k pracovnímu prostoru
Každý pracovní prostor může mít přiřazených více účtů a každý účet může mít přístup k několika pracovním prostorům. Přístup ke správě [přístupu na základě rolí Azure](../../role-based-access-control/role-assignments-portal.md). 


Následující aktivity také vyžadují oprávnění Azure:

||Akce |Potřebná oprávnění Azure |Poznámky |
|-------|-------------------------|------|
| Přidávání a odebírání řešení monitorování | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Tato oprávnění je potřeba udělit na úrovni skupiny prostředků nebo předplatného. |
| Změna cenové úrovně | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Zobrazení dat na dlaždicích řešení *Backup* a *Site Recovery* | Správce nebo spolusprávce | Má přístup k prostředkům nasazeným pomocí modelu nasazení Azure Classic. |
| Vytvoření pracovního prostoru na webu Azure Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Zobrazit základní vlastnosti pracovního prostoru a zadat okno pracovního prostoru na portálu | `Microsoft.OperationalInsights/workspaces/read` ||
| Dotazování protokolů pomocí libovolného rozhraní | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Přístup ke všem typům protokolů pomocí dotazů | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Přístup ke konkrétní tabulce protokolu | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Přečtěte si klíče pracovního prostoru, abyste povolili odesílání protokolů do tohoto pracovního prostoru. | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||



#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Správa přístupu k pracovnímu prostoru Log Analytics pomocí oprávnění Azure 
Pokud chcete udělit přístup k Log Analytics pomocí oprávnění Azure, postupujte podle kroků v tématu [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md).

Azure má dvě předdefinované role uživatelů pro Log Analytics pracovní prostory:
- Čtenář Log Analytics
- Přispěvatel Log Analytics

Členové role *Čtenář Log Analytics* můžou provádět:
- Zobrazení a prohledávání všech dat monitorování 
- Zobrazení nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure pro všechny prostředky Azure

Role Čtenář Log Analytics zahrnuje Azure takto:

| Typ    | Oprávnění | Popis |
| ------- | ---------- | ----------- |
| Akce | `*/read`   | Možnost zobrazit všechny prostředky Azure a jejich konfiguraci. To zahrnuje zobrazení: <br> Stavu rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure pro prostředky <br> Všechny vlastnosti a nastavení všech prostředků. <br> U pracovních prostorů umožňuje plná neomezená oprávnění ke čtení nastavení pracovního prostoru a provádění dotazů na data. Podívejte se na podrobnější možnosti výše. |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Zastaralé, není nutné je přiřazovat uživatelům. s |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | Zastaralé, není nutné je přiřazovat uživatelům. |
| Action | `Microsoft.Support/*` | Možnost otevírat případy podpory |
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
| `*/read`     | Možnost zobrazit všechny prostředky Azure a jejich konfiguraci. To zahrnuje zobrazení: <br> Stavu rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure pro prostředky <br> Všechny vlastnosti a nastavení všech prostředků. <br> U pracovních prostorů umožňuje plná neomezená oprávnění ke čtení nastavení pracovního prostoru a provádění dotazů na data. Podívejte se na podrobnější možnosti výše. |
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

Pro zajištění přesného řízení přístupu byste měli provést přiřazení na úrovni prostředků (pracovní prostor).  Pomocí [vlastních rolí](../../role-based-access-control/custom-roles.md) můžete vytvářet role s konkrétními požadovanými oprávněními.

### <a name="resource-permissions"></a>Oprávnění prostředků 
Když se uživatelé dotazují v protokolech z pracovního prostoru pomocí přístupu zaměřeného na prostředky, budou mít pro tento prostředek tato oprávnění:

| Oprávnění | Popis |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Příklady:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Možnost Zobrazit všechna data protokolu pro daný prostředek.  |
| `Microsoft.Insights/diagnosticSettings/write ` | Možnost konfigurace nastavení diagnostiky tak, aby povolovala nastavování protokolů pro tento prostředek. |

Toto oprávnění je obvykle uděleno z role, která zahrnuje  _\*/Read nebo_ _\*_ oprávnění, jako jsou například předdefinované role [Čtenář](../../role-based-access-control/built-in-roles.md#reader) a [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) . Všimněte si, že vlastní role, které zahrnují konkrétní akce nebo vyhrazené předdefinované role, nemusí zahrnovat toto oprávnění.

Pokud chcete vytvořit různé řízení přístupu pro různé tabulky, přečtěte si téma [Definování řízení přístupu pro jednotlivé tabulky](#table-level-rbac) .


## <a name="table-level-rbac"></a>RBAC na úrovni tabulky
**RBAC na úrovni tabulky** vám umožňuje poskytovat podrobnější kontrolu nad daty v log Analyticsovém pracovním prostoru kromě dalších oprávnění. Tento ovládací prvek umožňuje definovat konkrétní datové typy, které jsou přístupné pouze konkrétní sadě uživatelů.

Pomocí [vlastních rolí Azure](../../role-based-access-control/custom-roles.md) implementujete řízení přístupu k tabulkám a buď udělíte, nebo odepřete přístup ke konkrétním [tabulkám](../log-query/logs-structure.md) v pracovním prostoru. Tyto role se aplikují na pracovní prostory s [režimy řízení přístupu](#access-control-mode) orientovanými na pracovní prostor nebo prostředky, bez ohledu na [režim přístupu](#access-modes)uživatele.

Vytvořte [vlastní roli](../../role-based-access-control/custom-roles.md) s následujícími akcemi, které definují přístup k řízení přístupu k tabulce.

- Chcete-li udělit přístup k tabulce, zahrňte ji do oddílu **Akce** definice role.
- Pokud chcete odepřít přístup k tabulce, zahrňte ji do oddílu **NotActions** v definici role.
- Pomocí * můžete zadat všechny tabulky.

Chcete-li například vytvořit roli s přístupem k tabulkám _prezenčního signálu_ a _AzureActivity_ , vytvořte vlastní roli pomocí následujících akcí:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Chcete-li vytvořit roli s přístupem pouze k _SecurityBaseline_ a žádným jiným tabulkám, vytvořte vlastní roli pomocí následujících akcí:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
```

### <a name="custom-logs"></a>Vlastní protokoly
 Vlastní protokoly jsou vytvářeny zdroji dat, jako jsou vlastní protokoly a rozhraní API kolekce dat HTTP. Nejjednodušší způsob, jak identifikovat typ protokolu, je kontrola tabulek uvedených v části [vlastní protokoly ve schématu protokolu](../log-query/get-started-portal.md#understand-the-schema).

 V současné době nemůžete udělit ani odepřít přístup jednotlivým vlastním protokolům, můžete ale udělit nebo odepřít přístup ke všem vlastním protokolům. Chcete-li vytvořit roli s přístupem ke všem vlastním protokolům, vytvořte vlastní roli pomocí následujících akcí:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Požadavky

- Pokud má uživatel udělené globální oprávnění ke čtení s rolemi čtenář úrovně Standard a přispěvatel,  _\*_ které zahrnují akci/Read, potlačí řízení přístupu na jednotlivé tabulky a udělí jim přístup ke všem datům protokolu.
- Pokud je uživateli udělen přístup pro jednotlivé tabulky, ale žádná další oprávnění, by mohl získat přístup k datům protokolu z rozhraní API, ale nikoli z Azure Portal. Chcete-li poskytnout přístup k Azure Portal, jako základní roli použijte nástroj Log Analytics Reader.
- Správci předplatného budou mít přístup ke všem datovým typům bez ohledu na všechna ostatní nastavení oprávnění.
- Vlastníci pracovního prostoru se považují za každého jiného uživatele pro řízení přístupu k jednotlivým tabulkám.
- Chcete-li snížit počet přiřazení, je třeba přiřadit role ke skupinám zabezpečení místo jednotlivých uživatelů. Pomůže vám to také při konfiguraci a ověření přístupu pomocí existujících nástrojů pro správu skupin.




## <a name="next-steps"></a>Další postup
* Zobrazit [přehled agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) ke shromažďování dat z počítačů ve vašem datovém centru nebo jiných cloudovém prostředí.
* V článku [Shromažďování dat o virtuálních počítačích Azure](../../azure-monitor/learn/quick-collect-azurevm.md) najdete informace o konfiguraci shromažďování dat z virtuálních počítačů Azure.  

