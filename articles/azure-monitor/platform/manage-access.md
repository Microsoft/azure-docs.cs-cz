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
ms.date: 08/05/2019
ms.author: magoedte
ms.openlocfilehash: 59e5bbaf8deccdd8218e9c5590266070ed3b5ebb
ms.sourcegitcommit: 55e0c33b84f2579b7aad48a420a21141854bc9e3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2019
ms.locfileid: "69624343"
---
# <a name="manage-log-data-and-workspaces-in-azure-monitor"></a>Správa dat protokolu a pracovních prostorů v Azure Monitor

Azure Monitor ukládá data [protokolu](data-platform-logs.md) v pracovním prostoru Log Analytics, což je v podstatě kontejner, který obsahuje informace o datech a konfiguraci. Ke správě přístupu k datům protokolu provádíte různé úlohy správy související s vaším pracovním prostorem.

Tento článek vysvětluje, jak spravovat přístup k protokolům a jak spravovat pracovní prostory, které je obsahují, včetně těchto:

* Postup udělení přístupu uživatelům, kteří potřebují přístup k datům protokolu z konkrétních prostředků pomocí řízení přístupu na základě role (RBAC).

* Jak udělit přístup k pracovnímu prostoru pomocí oprávnění pracovního prostoru

* Postup udělení přístupu uživatelům, kteří potřebují přístup k datům protokolu v konkrétní tabulce v pracovním prostoru pomocí Azure RBAC.

## <a name="define-access-control-mode"></a>Definovat režim řízení přístupu

Režim řízení přístupu nakonfigurovaný v pracovním prostoru můžete zobrazit z Azure Portal nebo pomocí Azure PowerShell.  Toto nastavení můžete změnit pomocí jedné z následujících podporovaných metod:

* portál Azure

* Azure PowerShell

* Šablona Azure Resource Manageru

### <a name="configure-from-the-azure-portal"></a>Konfigurace z Azure Portal

Aktuální režim řízení přístupu k pracovnímu prostoru můžete zobrazit na stránce **Přehled** pracovního prostoru v nabídce **Log Analytics pracovní** prostor.

![Zobrazit režim řízení přístupu k pracovnímu prostoru](media/manage-access/view-access-control-mode.png)

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
1. V Azure Portal vyberte pracovní prostory Log Analytics > pracovní prostor.

Toto nastavení můžete změnit na stránce **vlastností** pracovního prostoru. Změna nastavení se zakáže, pokud nemáte oprávnění ke konfiguraci pracovního prostoru.

![Změnit režim přístupu k pracovnímu prostoru](media/manage-access/change-access-control-mode.png)

### <a name="configure-using-powershell"></a>Konfigurace pomocí PowerShellu

Pomocí následujícího příkazu prověřte režim řízení přístupu pro všechny pracovní prostory v rámci předplatného:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

Výstup by měl vypadat takto:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Hodnota `False` znamená, že pracovní prostor je nakonfigurovaný pomocí režimu přístupu kontextu pracovního prostoru.  Hodnota `True` znamená, že pracovní prostor je nakonfigurovaný pomocí režimu přístupu kontextu prostředků.

> [!NOTE]
> Pokud je pracovní prostor vrácen bez logické hodnoty a je prázdný, bude také odpovídat výsledkům `False` hodnoty.
>

Pomocí následujícího skriptu nastavte režim řízení přístupu pro konkrétní pracovní prostor na oprávnění kontextu prostředku:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Pomocí následujícího skriptu nastavte režim řízení přístupu pro všechny pracovní prostory v předplatném na oprávnění kontextu prostředků:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
```

### <a name="configure-using-a-resource-manager-template"></a>Konfigurace pomocí šablony Správce prostředků

Pro konfiguraci režimu přístupu v šabloně Azure Resource Manager nastavte příznak funkce **enableLogAccessUsingOnlyResourcePermissions** v pracovním prostoru na jednu z následujících hodnot.

* **NEPRAVDA**: Nastaví pracovní prostor na oprávnění kontextu. Toto je výchozí nastavení, pokud příznak není nastaven.
* **hodnota true**: Nastavte pracovní prostor na oprávnění kontextu prostředků.

## <a name="manage-accounts-and-users"></a>Správa účtů a uživatelů

Oprávnění, která se vztahují k pracovnímu prostoru pro konkrétního uživatele, jsou definovaná v [režimu přístupu](design-logs-deployment.md#access-mode) a v [režimu řízení přístupu](design-logs-deployment.md#access-control-mode) pracovního prostoru. Pomocí **kontextu pracovní prostor**můžete zobrazit všechny protokoly v pracovním prostoru, ke kterým máte oprávnění, protože dotazy v tomto režimu jsou vymezeny na všechna data ve všech tabulkách v pracovním prostoru. Pomocí **kontextu prostředků**můžete zobrazit data protokolů v pracovním prostoru pro konkrétní prostředek, skupinu prostředků nebo předplatné při hledání přímo z prostředku v Azure Portal, ke kterému máte přístup. Dotazy v tomto režimu jsou vymezeny jenom na data přidružená k tomuto prostředku.

### <a name="workspace-permissions"></a>Oprávnění k pracovnímu prostoru

Každý pracovní prostor může mít přiřazených více účtů a každý účet může mít přístup k několika pracovním prostorům. Přístup se spravuje pomocí [přístupu založeného na rolích Azure](../../role-based-access-control/role-assignments-portal.md).

Následující aktivity také vyžadují oprávnění Azure:

|Akce |Potřebná oprávnění Azure |Poznámky |
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

## <a name="manage-access-using-azure-permissions"></a>Správa přístupu pomocí oprávnění Azure

Pokud chcete udělit přístup k Log Analytics pomocí oprávnění Azure, postupujte podle kroků v tématu [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md).

Azure má dvě předdefinované role uživatelů pro Log Analytics pracovní prostory:

* Čtenář Log Analytics
* Přispěvatel Log Analytics

Členové role *Čtenář Log Analytics* můžou provádět:

* Zobrazení a prohledávání všech dat monitorování
* Zobrazení nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure pro všechny prostředky Azure

Role Čtenář Log Analytics zahrnuje Azure takto:

| Typ    | Oprávnění | Popis |
| ------- | ---------- | ----------- |
| Akce | `*/read`   | Možnost zobrazit všechny prostředky Azure a jejich konfiguraci. To zahrnuje zobrazení: <br> Stavu rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure pro prostředky <br> Všechny vlastnosti a nastavení všech prostředků. <br> U pracovních prostorů umožňuje plná neomezená oprávnění ke čtení nastavení pracovního prostoru a provádění dotazů na data. Podívejte se na podrobnější možnosti výše. |
| Action | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Zastaralé, není nutné je přiřazovat uživatelům. |
| Action | `Microsoft.OperationalInsights/workspaces/search/action` | Zastaralé, není nutné je přiřazovat uživatelům. |
| Action | `Microsoft.Support/*` | Možnost otevírat případy podpory |
|Jiný než akce | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Brání čtení pracovního prostoru klíče potřebné k používání rozhraní API pro shromažďování dat a nainstalovat agenty. To zabrání uživateli v přidávání nových prostředků do pracovního prostoru |

Členové role *Přispěvatel Log Analytics* můžou provádět:

* Jak Čtenář Log Analytics může číst všechna data monitorování
* Vytváření a konfigurace účtů služby Automation
* Přidání a odebrání řešení pro správu

    > [!NOTE]
    > Aby bylo možné úspěšně provést dvě poslední akce, musí být udělena na úrovni skupiny nebo předplatného resource toto oprávnění.

* Čtení klíčů účtu úložiště
* Konfigurace shromažďování protokolů ze služby Azure Storage
* Úprava nastavení monitorování pro prostředky Azure, včetně
  * Přidání rozšíření virtuálního počítače na virtuální počítače
  * Konfigurace diagnostiky Azure pro všechny prostředky Azure

> [!NOTE]
> Možnost přidat rozšíření virtuálního počítače na virtuální počítač můžete použít k získání úplné kontroly nad virtuálním počítačem.

Role Přispěvatel Log Analytics zahrnuje Azure takto:

| Oprávnění | Popis |
| ---------- | ----------- |
| `*/read`     | Možnost zobrazit všechny prostředky a jejich konfiguraci. To zahrnuje zobrazení: <br> Stavu rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure pro prostředky <br> Všechny vlastnosti a nastavení všech prostředků. <br> U pracovních prostorů umožňuje úplná neomezená oprávnění číst nastavení pracovního prostoru a provádět dotaz na data. Podívejte se na podrobnější možnosti výše. |
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

* Předplatné – Přístup ke všem pracovním prostorům v rámci předplatného
* Skupina prostředků – Přístup ke všem pracovním prostorům v rámci skupiny prostředků
* Prostředek – Přístup pouze k zadanému pracovnímu prostoru

Pro zajištění přesného řízení přístupu byste měli provést přiřazení na úrovni prostředků (pracovní prostor).  Pomocí [vlastních rolí](../../role-based-access-control/custom-roles.md) můžete vytvářet role s konkrétními požadovanými oprávněními.

### <a name="resource-permissions"></a>Oprávnění prostředků

Když se uživatelé dotazují v protokolech z pracovního prostoru pomocí přístupu kontextu prostředků, budou mít pro tento prostředek následující oprávnění:

| Oprávnění | Popis |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Příklady:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Možnost Zobrazit všechna data protokolu pro daný prostředek.  |
| `Microsoft.Insights/diagnosticSettings/write` | Možnost konfigurace nastavení diagnostiky tak, aby povolovala nastavování protokolů pro tento prostředek. |

`/read`oprávnění je obvykle uděleno z role, která zahrnuje  _\*/Read nebo_ _\*_ oprávnění, jako jsou například předdefinované role [Čtenář](../../role-based-access-control/built-in-roles.md#reader) a [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) . Všimněte si, že vlastní role, které zahrnují konkrétní akce nebo vyhrazené předdefinované role, nemusí zahrnovat toto oprávnění.

Pokud chcete vytvořit různé řízení přístupu pro různé tabulky, přečtěte si téma [Definování řízení přístupu pro jednotlivé tabulky](#table-level-rbac) .

## <a name="table-level-rbac"></a>RBAC na úrovni tabulky

**RBAC na úrovni tabulky** umožňuje definovat podrobnější řízení dat v log Analyticsovém pracovním prostoru kromě dalších oprávnění. Tento ovládací prvek umožňuje definovat konkrétní datové typy, které jsou přístupné pouze konkrétní sadě uživatelů.

Pomocí [vlastních rolí Azure](../../role-based-access-control/custom-roles.md) implementujete řízení přístupu k tabulkám a buď udělíte, nebo odepřete přístup ke konkrétním [tabulkám](../log-query/logs-structure.md) v pracovním prostoru. Tyto role se v pracovních prostorech aplikují pomocí [režimů řízení přístupu](design-logs-deployment.md#access-control-mode) kontextu pracovního prostoru nebo kontextu prostředků bez ohledu na [režim přístupu](design-logs-deployment.md#access-mode)uživatele.

Vytvořte [vlastní roli](../../role-based-access-control/custom-roles.md) s následujícími akcemi, které definují přístup k řízení přístupu k tabulce.

* Chcete-li udělit přístup k tabulce, zahrňte ji do oddílu **Akce** definice role.
* Pokud chcete odepřít přístup k tabulce, zahrňte ji do oddílu **NotActions** v definici role.
* Pomocí * můžete zadat všechny tabulky.

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

 Vlastní protokoly se vytvářejí ze zdrojů dat, jako jsou vlastní protokoly a rozhraní API kolekce dat HTTP. Nejjednodušší způsob, jak identifikovat typ protokolu, je kontrola tabulek uvedených v části [vlastní protokoly ve schématu protokolu](../log-query/get-started-portal.md#understand-the-schema).

 V současné době nemůžete udělit ani odepřít přístup jednotlivým vlastním protokolům, můžete ale udělit nebo odepřít přístup ke všem vlastním protokolům. Chcete-li vytvořit roli s přístupem ke všem vlastním protokolům, vytvořte vlastní roli pomocí následujících akcí:

```
    "Actions":  [
        "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
    ],
```

### <a name="considerations"></a>Požadavky

* Pokud má uživatel udělené globální oprávnění ke čtení s rolemi čtenář úrovně Standard a přispěvatel,  _\*_ které zahrnují akci/Read, potlačí řízení přístupu na jednotlivé tabulky a udělí jim přístup ke všem datům protokolu.
* Pokud je uživateli udělen přístup pro jednotlivé tabulky, ale žádná další oprávnění, by mohl získat přístup k datům protokolu z rozhraní API, ale nikoli z Azure Portal. K poskytnutí přístupu z Azure Portal jako základní roli použijte nástroj Log Analytics Reader.
* Správci předplatného budou mít přístup ke všem datovým typům bez ohledu na všechna ostatní nastavení oprávnění.
* Vlastníci pracovního prostoru se považují za každého jiného uživatele pro řízení přístupu k jednotlivým tabulkám.
* Chcete-li snížit počet přiřazení, je třeba přiřadit role ke skupinám zabezpečení místo jednotlivých uživatelů. Pomůže vám to také při konfiguraci a ověření přístupu pomocí existujících nástrojů pro správu skupin.

## <a name="next-steps"></a>Další kroky

* Zobrazit [přehled agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) ke shromažďování dat z počítačů ve vašem datovém centru nebo jiných cloudovém prostředí.

* Další informace najdete v tématu [shromáždění dat o virtuálních počítačích Azure](../../azure-monitor/learn/quick-collect-azurevm.md) ke konfiguraci shromažďování dat z virtuálních počítačů Azure.
