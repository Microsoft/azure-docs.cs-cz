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
ms.openlocfilehash: 6990bed4065183ecabb502ea90b5ddf26db563b4
ms.sourcegitcommit: f24fdd1ab23927c73595c960d8a26a74e1d12f5d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2019
ms.locfileid: "58500181"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Správa dat protokolů a pracovním prostorům ve službě Azure Monitor
Azure Monitor úložiště vytvářet protokoly dat v pracovním prostoru Log Analytics, která je v podstatě kontejner, který obsahuje data a informace o konfiguraci. Ke správě přístupu k protokolování dat, můžete provádět různé úlohy správy související s pracovními prostory. Vy nebo další členové vaší organizace můžete používat víc pracovních prostorů ke správě různých sad dat, která se shromažďují ze všech částí vaší infrastruktury IT.

Tento článek vysvětluje, jak spravovat přístup k protokolům a jak spravovat pracovní prostory, které je obsahují. 

## <a name="create-a-workspace"></a>Vytvoření pracovního prostoru
Pokud chcete vytvořit pracovní prostor Log Analytics, budete muset:

1. Mít předplatné Azure.
2. Zvolit název pracovního prostoru.
3. Přidružte pracovní prostor s jednou z vašich předplatných a skupin prostředků.
4. Vybrat zeměpisné umístění.

Přečtěte si podrobné informace o vytváření pracovního prostoru v následujících článcích:

- [Vytvoření pracovního prostoru Log Analytics na portálu Azure portal](../learn/quick-create-workspace.md)
- [Vytvoření pracovního prostoru Log Analytics pomocí Azure CLI 2.0](../learn/quick-create-workspace-cli.md)
- [Vytvořit pracovní prostor Log Analytics pomocí Azure Powershellu](../learn/quick-create-workspace-posh.md)

## <a name="determine-the-number-of-workspaces-you-need"></a>Určení potřebného počtu pracovních prostorů
Pracovní prostor Log Analytics je prostředek Azure a je kontejner se shromažďují data, agregují, analyzují a zobrazí ve službě Azure Monitor. Můžete mít několik pracovních prostorů na předplatné Azure a budete mít přístup k více než jednomu pracovnímu prostoru, umožňuje snadno vytvářet dotazy mezi nimi. Tato část popisuje případy, kdy je užitečné mít víc než jeden pracovní prostor.

Pracovní prostor Log Analytics nabízí:

* Zeměpisné umístění úložiště dat
* Izolace dat k definování různých uživatelská přístupová práva v režimu zaměřené na pracovní prostor. Nejsou důležité při práci v režimu zaměřené na prostředek.
* Obor pro konfiguraci nastavení, jako jsou [cenovou úroveň](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#changing-pricing-tier), [uchování](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#change-the-data-retention-period) a [malá a velká data](https://docs.microsoft.com/azure/azure-monitor/platform/manage-cost-storage#daily-cap).
* Poplatky za související s příjmem dat a jejich uchovávání probíhají v prostředku pracovního prostoru.

Z využití pohledu doporučujeme, abyste že nejdříve vytvoříte jako několik pracovních prostorů. Správa a práce s dotazy díky jednodušší a rychlejší. Ale podle výše uvedených charakteristik, můžete chtít vytvořit víc pracovních prostorů, pokud:

* Jste globální společnost a potřebujete protokolovat data uložená v různých oblastech z důvodů suverenity nebo dodržování předpisů data.
* Používáte Azure a chcete se vyhnout poplatkům za odchozí datové přenosy tím, že budete mít pracovní prostor ve stejné oblasti jako prostředky Azure, které spravuje.
* Jste poskytovatel spravované služby a potřebujete uchovávat data Log Analytics pro každého zákazníka odděleně od dat ostatních zákazníků.
* Spravujete víc zákazníků a chcete, aby každý zákazník / oddělení nebo obchodní skupina mohli podívat na svoje vlastní data, ale ne data od ostatních, a není nutné obchodní konsolidované křížové zákazníka nebo oddělení nebo pracovní skupina zobrazení. ".

Při používání agentů Windows ke shromažďování dat můžete [každého agenta konfigurovat tak, aby ukládal data do jednoho nebo více pracovních prostorů](../../azure-monitor/platform/agent-windows.md).

Pokud používáte System Center Operations Manager, můžete připojit každou skupinu nástroje Operations Manager jen do jednoho pracovního prostoru. Můžete nainstalovat Microsoft Monitoring Agent do počítačů spravovaných nástrojem Operations Manager a nastavit agenta tak, aby odesílal data do nástroje Operations Manager i do jiného pracovního prostoru Log Analytics.

Po definování architektury pracovního prostoru by měl vynutit tyto zásady pro prostředky Azure s [Azure Policy](../../governance/policy/overview.md). Získáte tak integrované definice, která automaticky platit pro všechny prostředky Azure. Můžete například nastavit zásadu, která Ujistěte se, že všechny prostředky Azure v konkrétní oblasti odesílat jejich diagnostické protokoly pro konkrétní pracovní prostor.

## <a name="view-workspace-details"></a>Zobrazit podrobnosti o pracovním prostoru
Při analýze dat v pracovním prostoru Log Analytics z **Azure Monitor** nabídky na webu Azure Portal, můžete vytvořit a spravovat pracovní prostory v **pracovních prostorů Log Analytics** nabídky.
 

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a klikněte na tlačítko **všechny služby**. V seznamu prostředků zadejte **Log Analytics**. Seznam se průběžně filtruje podle zadávaného textu. Vyberte **Log Analytics** pracovní prostory.  

    ![portál Azure](media/manage-access/azure-portal-01.png)  

3. Vyberte pracovní prostor ze seznamu.

4. Na stránce pracovní prostor se zobrazí podrobnosti o pracovním prostoru, jak začít, konfigurace a odkazy na další informace.  

    ![Podrobnosti o pracovním prostoru](./media/manage-access/workspace-overview-page.png)  


## <a name="workspace-permissions-and-scope"></a>Oprávnění k pracovním prostorům a oboru
Data, která má uživatel přístup k jsou určena několika faktory, které jsou uvedeny v následující tabulce. Každá je popsána v následujících částech.

| faktor | Popis |
|:---|:---|
| [Režim přístupu](#access-modes) | Metoda, která uživatel používá pro má přístup k pracovním prostoru.  Definuje rozsah dat, které jsou k dispozici a režim řízení přístupu, který se použije. |
| [Režim kontroly přístupu](#access-control-mode) | Nastavení v pracovním prostoru, který definuje, zda jsou oprávnění použít na úrovni pracovního prostoru nebo prostředků. |
| [Oprávnění](#manage-accounts-and-users) | Oprávnění pro jednotlivce nebo skupiny uživatelů pro pracovní prostor nebo prostředků. Definuje, jaká data uživatel bude mít přístup k. |
| [Na úrovni tabulky RBAC](#table-level-rbac) | Volitelné granulární oprávnění, která platí pro všechny uživatele bez ohledu na jejich přístupovém režimu nebo režimu řízení přístupu. Definuje typy dat, které má uživatel přístup. |



## <a name="access-modes"></a>Režimy přístupu
_Přístupovém režimu_ odkazuje na přístup uživatele k pracovnímu prostoru Log Analytics a definuje rozsah datům bude mít přístup. 

**Workspace-centric**: V tomto režimu může uživatel zobrazit všechny protokoly v pracovním prostoru, které mají oprávnění k. Dotazy v tomto režimu se rozsahu ke všem datům ve všech tabulkách v pracovním prostoru. Jedná se o režim přístupu používá protokoly jsou přístup k pracovnímu prostoru jako obor, například když vyberete **protokoly** z **Azure Monitor** nabídky na webu Azure Portal.

**Závislý na prostředku**: Když otevřete pracovní prostor pro určitý prostředek, třeba když vyberete **protokoly** v nabídce prostředků na webu Azure Portal můžete zobrazit protokoly pro pouze příslušný prostředek ve všech tabulkách, které mají přístup k. Dotazy v tomto režimu mají rozsah pouze data přidružená k tomuto prostředku. Tento režim také umožňuje řízení přístupu na detailní na základě rolí (RBAC). 

> [!NOTE]
> Protokoly jsou k dispozici pro dotazy na střed prostředků pouze v případě, že byly správně přidružené odpovídající zdroj. V současné době následující prostředky mají omezení: 
> - Počítače mimo Azure
> - Service Fabric
> - Application Insights
> - Containers
> - Vlastní protokoly vytvořené metodou rozhraní API kolekce dat HTTP
>
> Můžete otestovat, pokud protokoly jsou správně spojené s jejich prostředků spuštěním dotazu a kontrola záznamů vás zajímá. Pokud je ID správné prostředku v [_ResourceId](log-standard-properties.md#_resourceid) vlastnost a potom dat je k dispozici pro dotazy zaměřené na prostředek.

### <a name="comparing-access-modes"></a>Porovnává režimy přístupu

Následující tabulka shrnuje režimy přístupu:

| | Workspace-centric | Resource-centric |
|:---|:---|:---|
| Komu je každý model určená? | Centrální správy. Správci, kteří potřebují přístup k široké škály zdrojů, kteří potřebují ke konfiguraci shromažďování dat a uživatelů. Také se momentálně nutná pro uživatele, kteří mají na přístup k protokolům pro prostředky mimo Azure. | Aplikační týmy. Správce prostředků Azure, které jsou monitorovány. |
| Chcete-li zobrazit protokoly co vyžaduje uživatele? | Oprávnění k pracovnímu prostoru. Zobrazit **oprávnění k pracovním prostorům** v [Správa účtů a uživatelů](#manage-accounts-and-users). | Přístup pro čtení k prostředku. Zobrazit **oprávnění zdrojů** v [Správa účtů a uživatelů](#manage-accounts-and-users). Oprávnění může být zděděno (jako třeba příslušnou skupinu prostředků) nebo přímo přiřazenou k prostředku. Oprávnění k protokolům pro prostředek se automaticky přiřadí. |
| Jaký je rozsah oprávnění? | Pracovní prostor. Uživatelé s přístupem k pracovnímu prostoru se můžete dotazovat všechny protokoly v daném pracovním prostoru z tabulek, které mají oprávnění k. Zobrazit [řízení přístupu na tabulku](#table-level-rbac) | Prostředek Azure. Uživatele můžete dotazovat protokoly pro prostředky máte přístup z libovolného pracovního prostoru, ale nemohou spustit dotaz pro protokoly pro další prostředky. |
| Jak může uživatel přístup k protokolům? | Spustit **protokoly** z **Azure Monitor** nabídky nebo **pracovních prostorů Log Analytics**. | Spustit **protokoly** z nabídky pro prostředky Azure. |


## <a name="access-control-mode"></a>Režim kontroly přístupu
_Režim řízení přístupu_ je nastavení na jednotlivých pracovních prostorů, která definuje, jak se určují oprávnění pro tento pracovní prostor.

**Vyžadovat oprávnění k pracovním prostorům**:  Tento režim ovládacího prvku neumožňuje detailní RBAC. Pro uživatele pro přístup k pracovnímu prostoru musí jim být udělena oprávnění do pracovního prostoru nebo na konkrétní tabulky. 

Pokud uživatel zobrazuje pracovní prostor v režimu zaměřené na pracovní prostor, mají přístup ke všem datům žádné tabulky, které jste byl udělen přístup k. Pokud uživatel zobrazuje pracovní prostor v režimu zaměřené na prostředek, bude mít přístup uživatelé pouze data pro daný prostředek v žádné tabulky, které jste byl udělen přístup k.

Toto je výchozí nastavení pro všechny pracovní prostory vytvořené před březnem 2019.

**Použití prostředků nebo pracovní prostor oprávnění**: Tento ovládací prvek režim umožňuje granulární RBAC. Uživateli je udělen přístup k pouze data související s prostředky můžete zobrazit prostřednictvím oprávnění Azure, prostředky, ke kterým mají `read` oprávnění. 

Když uživatel zobrazuje pracovní prostor v režimu zaměřené na pracovní prostor, použije se oprávnění pracovního prostoru. Když uživatel zobrazuje pracovní prostor v režimu zaměřené na prostředek, se ověří pouze příslušná oprávnění a oprávnění pracovního prostoru se bude ignorovat. Povolte RBAC pro uživatele. to je odeberete z pracovního prostoru, oprávnění a jejich příslušná oprávnění, chcete-li rozpoznán.

Toto je výchozí nastavení pro všechny pracovní prostory vytvořené po března 2019.

> [!NOTE]
> Pokud má uživatel jenom oprávnění zdrojů do pracovního prostoru, pouze bude mít přístup k použití pracovního prostoru [zaměřené na prostředek režimu](#access-modes).


### <a name="define-access-control-mode-in-azure-portal"></a>Definujte režim řízení přístupu na webu Azure portal
Aktuální režim řízení přístupu pracovního prostoru můžete zobrazit na **přehled** stránky pro pracovní prostor v **pracovní prostor Log Analytics** nabídky.

![Režim zobrazení pracovního prostoru přístup ovládacího prvku](media/manage-access/view-access-control-mode.png)

Toto nastavení můžete změnit na **vlastnosti** stránky pro pracovní prostor. Změna nastavení bude zakázáno, pokud nemáte oprávnění ke konfiguraci pracovního prostoru.

![Režim přístupu změnit pracovní prostor](media/manage-access/change-access-control-mode.png)

### <a name="define-access-control-mode-in-powershell"></a>Definujte režim řízení přístupu v prostředí PowerShell

Použijte následující příkaz k prozkoumání režim řízení přístupu pro všechny pracovní prostory v předplatném:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions} 
```

Pomocí následujícího skriptu pro nastavení režimu řízení přístupu pro konkrétní pracovní prostor:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Pomocí následujícího skriptu pro nastavení režimu řízení přístupu pro všechny pracovní prostory v rámci předplatného

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null) 
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else 
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="define-access-mode-in-resource-manager-template"></a>Režim přístupu k definování v šabloně Resource Manageru
Chcete-li nakonfigurovat režim přístupu v šabloně Azure Resource Manageru, nastavte **enableLogAccessUsingOnlyResourcePermissions** funkce příznak v pracovním prostoru na jeden z následujících hodnot.

- **False**: Nastavte pracovní prostor zaměřené na pracovní prostor oprávnění. Toto je výchozí nastavení, pokud není nastaven příznak.
- **Hodnota TRUE**: Nastavte pracovní prostor zaměřené na prostředek oprávnění.


## <a name="manage-accounts-and-users"></a>Správa účtů a uživatelů
Oprávnění k pracovnímu prostoru, které se použijí pro určitého uživatele jsou určené jejich přístupovém režimu a [režim řízení přístupu](#access-control-mode) pracovního prostoru. **Oprávnění k pracovním prostorům** se použijí, když uživatel přistupuje k jakékoli pracovní prostor pomocí **zaměřené na pracovní prostor** v [zaměřené na pracovní prostor režimu](#access-modes). **Oprávnění k prostředkům** se použijí, když uživatel zobrazuje pracovní prostor se **použít oprávnění prostředků nebo pracovní prostor** [režim řízení přístupu](#access-control-mode) pomocí [zaměřené na prostředek režimu ](#access-modes).

### <a name="workspace-permissions"></a>Oprávnění pracovního prostoru.
Každý pracovní prostor může mít přiřazených více účtů a každý účet může mít přístup k několika pracovním prostorům. Přístup ke správě [přístupu na základě rolí Azure](../../role-based-access-control/role-assignments-portal.md). 


Následující aktivity také vyžadují oprávnění Azure:

| Akce                                                          | Potřebná oprávnění Azure | Poznámky |
|-----------------------------------------------------------------|--------------------------|-------|
| Přidávání a odebírání řešení monitorování                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Tato oprávnění je potřeba udělit na úrovni skupiny prostředků nebo předplatného. |
| Změna cenové úrovně                                       | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Zobrazení dat na dlaždicích řešení *Backup* a *Site Recovery* | Správce nebo spolusprávce | Má přístup k prostředkům nasazeným pomocí modelu nasazení Azure Classic. |
| Vytvoření pracovního prostoru na webu Azure Portal                        | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||


#### <a name="manage-access-to-log-analytics-workspace-using-azure-permissions"></a>Správa přístupu k pracovním prostoru Log Analytics pomocí oprávnění Azure 
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

Měli byste provést přiřazení na úrovni prostředků (pracovní prostor), aby zajistil řízení přístupu na přesné.  Pomocí [vlastních rolí](../../role-based-access-control/custom-roles.md) můžete vytvářet role s konkrétními požadovanými oprávněními.

### <a name="resource-permissions"></a>Oprávnění k prostředkům 
Když se přihlásí uživatelé dotazu z pracovního prostoru pomocí závislý na prostředku přístup, budou mít následující oprávnění pro prostředek:

| Oprávnění | Popis |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Příklady:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Možnost zobrazit všechny data protokolu pro prostředek.  |


Tato oprávnění jsou obvykle udělena z role, která zahrnuje  _\*/čtení nebo_ _\*_ oprávnění, jako je například předdefinované [čtečky](../../role-based-access-control/built-in-roles.md#reader) a [ Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) role. Mějte na paměti, že vlastní role, které zahrnují určité akce nebo vyhrazené předdefinovaných rolí nemusí zahrnovat toto oprávnění.

V tématu [definování řízení přístupu na tabulky](#table-level-rbac) níže Pokud chcete vytvořit různá řízení přístupu pro jiné tabulky.


## <a name="table-level-rbac"></a>Na úrovni tabulky RBAC
**Tabulka úroveň RBAC** umožňuje poskytovat podrobnější řízení dat v pracovním prostoru Log Analytics další oprávnění. Tento ovládací prvek můžete zadat konkrétní datové typy, které jsou dostupné jenom pro konkrétní skupinu uživatelů.

Implementujte řízení přístupu na tabulku pomocí [vlastní role Azure](../../role-based-access-control/custom-roles.md) udělit nebo odepřít přístup na konkrétní [tabulky](../log-query/log-query-overview.md#how-azure-monitor-log-data-is-organized) v pracovním prostoru. Tyto role se použijí u pracovních prostorů s zaměřené na pracovní prostor nebo zaměřené na prostředek [přístup k řízení režimy](#access-control-mode) bez ohledu na uživatele [režim přístupu](#access-modes).

Vytvoření [vlastní roli](../../role-based-access-control/custom-roles.md) s definují přístup k řízení přístupu k tabulce následujících akcí.

- Pokud chcete udělit přístup k tabulce, zahrnují ho **akce** část definice role.
- Pokud chcete odepřít přístup k tabulce, zahrnují ho **NotActions** část definice role.
- Použití * k určení všech tabulek.

Chcete-li například vytvořit roli s přístupem k _prezenčního signálu_ a _AzureActivity_ tabulky, vytvořte vlastní roli, pomocí následujících akcí:

```
"Actions":  [
              "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
              "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Postup vytvoření role s přístupem k pouze _SecurityBaseline_ a žádné další tabulky, vytvořte vlastní roli, pomocí následujících akcí:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/*/read"
    ],
    "NotActions":  [
        "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
    ],
```

### <a name="custom-logs"></a>Vlastní protokoly
 Vlastní protokoly jsou vytvořené zdroje dat, jako jsou vlastní protokoly a rozhraní API kolekce dat HTTP. Nejjednodušší způsob, jak identifikovat typ protokolu, který je tak, že zkontrolujete tabulky uvedené v části [vlastní protokoly ve schématu protokolu](../log-query/get-started-portal.md#understand-the-schema).

 Momentálně nelze udělit nebo odepřít přístup k jednotlivým vlastní protokoly, ale můžete udělit nebo odepřít přístup na všechny vlastní protokoly. Postup vytvoření role s přístupem pro všechny vlastní protokoly, vytvořte vlastní roli pomocí následujících akcí:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Požadavky

- Pokud je uživatel udělil globální oprávnění standardní role přispěvatele nebo čtenáře, které zahrnují čtení  _\*/čtení_ akce, přepíše řízení přístupu na tabulky a poskytněte jim přístup ke všem datům protokolu.
- Pokud uživateli je udělen přístup na tabulce, ale žádná další oprávnění, bude mít přístup k datům protokolů z rozhraní API, ale ne z portálu Azure portal. Pokud chcete poskytnout přístup k webu Azure portal, použijte jako jeho základní role Čtenář Log Analytics.
- Správci předplatného budou mít přístup ke všem typům dat bez ohledu na to jinými nastaveními oprávnění.
- Vlastníci pracovního prostoru jsou považovány jako každý jiný uživatel pro řízení přístupu podle tabulky.
- Skupiny zabezpečení místo jednotlivým uživatelům a snížit počet přiřazení by měla přiřadit role. To vám také pomůže použít stávající nástroje pro správu skupiny se konfigurace a ověření přístupu.




## <a name="next-steps"></a>Další postup
* Zobrazit [přehled agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) ke shromažďování dat z počítačů ve vašem datovém centru nebo jiných cloudovém prostředí.
* V článku [Shromažďování dat o virtuálních počítačích Azure](../../azure-monitor/learn/quick-collect-azurevm.md) najdete informace o konfiguraci shromažďování dat z virtuálních počítačů Azure.  

