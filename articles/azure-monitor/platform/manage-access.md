---
title: Správa pracovních prostorů Log Analytics v Azure Monitor | Microsoft Docs
description: Můžete spravovat přístup k datům uloženým v pracovním prostoru Log Analytics v Azure Monitor pomocí oprávnění k prostředkům, pracovnímu prostoru nebo úrovni tabulky. Tento článek podrobně popisuje, jak to provést.
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
ms.date: 09/30/2019
ms.author: magoedte
ms.openlocfilehash: 920e470a8bc06050219d0f603ab842cfc267e6ce
ms.sourcegitcommit: 8bae7afb0011a98e82cbd76c50bc9f08be9ebe06
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2019
ms.locfileid: "71695006"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Správa přístupu k datům a pracovním prostorům protokolu v Azure Monitor

Azure Monitor ukládá data [protokolu](data-platform-logs.md) v pracovním prostoru Log Analytics, což je v podstatě kontejner, který obsahuje informace o datech a konfiguraci. Ke správě přístupu k datům protokolu provádíte různé úlohy správy související s vaším pracovním prostorem.

Tento článek vysvětluje, jak spravovat přístup k protokolům a jak spravovat pracovní prostory, které je obsahují, včetně těchto:

* Postup udělení přístupu uživatelům, kteří potřebují přístup k datům protokolu z konkrétních prostředků pomocí řízení přístupu na základě role (RBAC).

* Jak udělit přístup k pracovnímu prostoru pomocí oprávnění pracovního prostoru

* Postup udělení přístupu uživatelům, kteří potřebují přístup k datům protokolu v konkrétní tabulce v pracovním prostoru pomocí Azure RBAC.

## <a name="configure-access-control-mode"></a>Konfigurovat režim řízení přístupu

Režim řízení přístupu nakonfigurovaný v pracovním prostoru můžete zobrazit z Azure Portal nebo pomocí Azure PowerShell.  Toto nastavení můžete změnit pomocí jedné z následujících podporovaných metod:

* Azure Portal

* Azure PowerShell

* Šablona Azure Resource Manager

### <a name="from-the-azure-portal"></a>Z Azure Portal

Aktuální režim řízení přístupu k pracovnímu prostoru můžete zobrazit na stránce **Přehled** pracovního prostoru v nabídce **Log Analytics pracovní** prostor.

![Zobrazit režim řízení přístupu k pracovnímu prostoru](media/manage-access/view-access-control-mode.png)

1. Přihlaste se k Azure Portal v [https://portal.azure.com](https://portal.azure.com).
1. V Azure Portal vyberte pracovní prostory Log Analytics > pracovní prostor.

Toto nastavení můžete změnit na stránce **vlastností** pracovního prostoru. Změna nastavení se zakáže, pokud nemáte oprávnění ke konfiguraci pracovního prostoru.

![Změnit režim přístupu k pracovnímu prostoru](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>Použití PowerShellu

Pomocí následujícího příkazu prověřte režim řízení přístupu pro všechny pracovní prostory v rámci předplatného:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

Výstup by měl vypadat takto:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Hodnota `False` znamená, že se pracovní prostor konfiguruje pomocí režimu přístupu kontextu pracovního prostoru.  Hodnota `True` znamená, že je pracovní prostor nakonfigurovaný pomocí režimu přístupu kontextu prostředků.

> [!NOTE]
> Pokud je pracovní prostor vrácen bez logické hodnoty a je prázdný, bude také odpovídat výsledkům hodnoty `False`.
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

### <a name="using-a-resource-manager-template"></a>Použití šablony Správce prostředků

Pro konfiguraci režimu přístupu v šabloně Azure Resource Manager nastavte příznak funkce **enableLogAccessUsingOnlyResourcePermissions** v pracovním prostoru na jednu z následujících hodnot.

* **false**: Nastavte pracovní prostor na oprávnění kontextu v pracovním prostoru. Toto je výchozí nastavení, pokud příznak není nastaven.
* **true**: Nastavte pracovní prostor na oprávnění kontextu prostředků.

## <a name="manage-access-using-workspace-permissions"></a>Správa přístupu pomocí oprávnění k pracovnímu prostoru

Ke každému pracovnímu prostoru může být přidruženo několik účtů a každý účet může mít přístup k několika pracovním prostorům. Přístup se spravuje pomocí [přístupu založeného na rolích Azure](../../role-based-access-control/role-assignments-portal.md).

Následující aktivity také vyžadují oprávnění Azure:

|Akce |Potřebná oprávnění Azure |Poznámky |
|-------|-------------------------|------|
| Přidávání a odebírání řešení monitorování | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Tato oprávnění je potřeba udělit na úrovni skupiny prostředků nebo předplatného. |
| Změna cenové úrovně | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Zobrazení dat na dlaždicích řešení *Backup* a *Site Recovery* | Správce nebo spolusprávce | Přistupuje k prostředkům nasazeným pomocí modelu nasazení Classic. |
| Vytvoření pracovního prostoru v Azure Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Zobrazit základní vlastnosti pracovního prostoru a zadat okno pracovního prostoru na portálu | `Microsoft.OperationalInsights/workspaces/read` ||
| Dotazování protokolů pomocí libovolného rozhraní | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Přístup ke všem typům protokolů pomocí dotazů | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Přístup ke konkrétní tabulce protokolu | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Přečtěte si klíče pracovního prostoru, abyste povolili odesílání protokolů do tohoto pracovního prostoru. | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Správa přístupu pomocí oprávnění Azure

Pokud chcete udělit přístup k pracovnímu prostoru Log Analytics pomocí oprávnění Azure, postupujte podle kroků v části [použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md). Například vlastní role najdete v tématu [Příklady vlastních rolí](#custom-role-examples) .

Azure má dvě předdefinované role uživatelů pro Log Analytics pracovní prostory:

* Čtecí modul Log Analytics
* Přispěvatel Log Analytics

Členové role *čtenář Log Analytics* můžou:

* Zobrazení a prohledávání všech dat monitorování
* Podívejte se na nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure.

Role čtecího modulu Log Analytics zahrnuje následující akce Azure:

| Typ    | Oprávnění | Popis |
| ------- | ---------- | ----------- |
| Akce | `*/read`   | Možnost Zobrazit všechny prostředky a konfiguraci prostředků Azure. Zahrnuje zobrazení: <br> Stav rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure na prostředcích <br> Všechny vlastnosti a nastavení všech prostředků. <br> U pracovních prostorů umožňuje plná neomezená oprávnění ke čtení nastavení pracovního prostoru a provádění dotazů na data. Podívejte se na podrobnější možnosti výše. |
| Akce | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Zastaralé, není nutné je přiřazovat uživatelům. |
| Akce | `Microsoft.OperationalInsights/workspaces/search/action` | Zastaralé, není nutné je přiřazovat uživatelům. |
| Akce | `Microsoft.Support/*` | Možnost otevřít případy podpory |
|Neakce | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Zabraňuje čtení klíče pracovního prostoru požadovaného k použití rozhraní API pro shromažďování dat a k instalaci agentů. Tím se zabrání uživateli v přidávání nových prostředků do pracovního prostoru. |

Členové role *přispěvatel Log Analytics* můžou:

* Číst všechna data monitorování jako Log Analytics čtenář může
* Vytváření a konfigurace účtů služby Automation
* Přidání a odebrání řešení pro správu

    > [!NOTE]
    > Aby bylo možné úspěšně provést poslední dvě akce, je nutné toto oprávnění udělit na úrovni skupiny prostředků nebo předplatného.

* Čtení klíčů účtu úložiště
* Konfigurovat shromažďování protokolů z Azure Storage
* Úprava nastavení monitorování pro prostředky Azure, včetně
  * Přidání rozšíření virtuálního počítače do virtuálních počítačů
  * Konfigurace diagnostiky Azure na všech prostředcích Azure

> [!NOTE]
> Můžete použít možnost přidat rozšíření virtuálního počítače k virtuálnímu počítači a získat tak úplnou kontrolu nad virtuálním počítačem.

Role Přispěvatel Log Analytics zahrnuje následující akce Azure:

| Oprávnění | Popis |
| ---------- | ----------- |
| `*/read`     | Možnost zobrazení všech prostředků a konfigurace prostředků. Zahrnuje zobrazení: <br> Stav rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure na prostředcích <br> Všechny vlastnosti a nastavení všech prostředků. <br> U pracovních prostorů umožňuje úplná neomezená oprávnění číst nastavení pracovního prostoru a provádět dotaz na data. Podívejte se na podrobnější možnosti výše. |
| `Microsoft.Automation/automationAccounts/*` | Možnost vytvářet a konfigurovat účty Azure Automation, včetně přidávání a úprav runbooků |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Přidání, aktualizace a odebrání rozšíření virtuálních počítačů, včetně rozšíření Microsoft Monitoring Agent a rozšíření Agent sady OMS pro Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Zobrazte klíč účtu úložiště. Vyžaduje se ke konfiguraci Log Analytics ke čtení protokolů z účtů Azure Storage. |
| `Microsoft.Insights/alertRules/*` | Přidání, aktualizace a odebrání pravidel výstrah |
| `Microsoft.Insights/diagnosticSettings/*` | Přidání, aktualizace a odebrání nastavení diagnostiky v prostředcích Azure |
| `Microsoft.OperationalInsights/*` | Přidání, aktualizace a odebrání konfigurace Log Analytics pracovních prostorů. Pro úpravu rozšířených nastavení pracovního prostoru potřebuje uživatel `Microsoft.OperationalInsights/workspaces/write`. |
| `Microsoft.OperationsManagement/*` | Přidání a odebrání řešení pro správu |
| `Microsoft.Resources/deployments/*` | Vytváření a odstraňování nasazení. Vyžaduje se pro přidání a odebrání řešení, pracovních prostorů a účtů Automation. |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Vytváření a odstraňování nasazení. Vyžaduje se pro přidání a odebrání řešení, pracovních prostorů a účtů Automation. |

Chcete-li přidat nebo odebrat uživatele k roli uživatele, je nutné mít oprávnění `Microsoft.Authorization/*/Delete` a `Microsoft.Authorization/*/Write`.

Tyto role použijte k poskytnutí přístupu uživatelům v různých oborech:

* Předplatné – přístup ke všem pracovním prostorům v předplatném
* Skupina prostředků – přístup ke všem pracovním prostorům ve skupině prostředků
* Prostředek – přístup jenom k zadanému pracovnímu prostoru

Pro zajištění přesného řízení přístupu byste měli provést přiřazení na úrovni prostředků (pracovní prostor).  Pomocí [vlastních rolí](../../role-based-access-control/custom-roles.md) můžete vytvářet role s konkrétními požadovanými oprávněními.

### <a name="resource-permissions"></a>Oprávnění prostředků

Když se uživatelé dotazují v protokolech z pracovního prostoru pomocí přístupu kontextu prostředků, budou mít pro tento prostředek následující oprávnění:

| Oprávnění | Popis |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Příklady:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Možnost Zobrazit všechna data protokolu pro daný prostředek.  |
| `Microsoft.Insights/diagnosticSettings/write` | Možnost konfigurace nastavení diagnostiky tak, aby povolovala nastavování protokolů pro tento prostředek. |

oprávnění `/read` obvykle uděluje role, která zahrnuje oprávnění _\*/číst nebo_ _\*_ , jako jsou například předdefinované role [Čtenář](../../role-based-access-control/built-in-roles.md#reader) a [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) . Všimněte si, že vlastní role, které zahrnují konkrétní akce nebo vyhrazené předdefinované role, nemusí zahrnovat toto oprávnění.

Pokud chcete vytvořit různé řízení přístupu pro různé tabulky, přečtěte si téma [Definování řízení přístupu pro jednotlivé tabulky](#table-level-rbac) .

## <a name="custom-role-examples"></a>Příklady vlastních rolí

1. Pokud chcete uživateli udělit přístup k datům protokolu z jejich prostředků, udělejte toto:

    * Konfigurace režimu řízení přístupu k pracovnímu prostoru pro **použití oprávnění k pracovnímu prostoru nebo prostředku**

    * Udělte uživatelům oprávnění `*/read` nebo `Microsoft.Insights/logs/*/read` k prostředkům. Pokud jsou v pracovním prostoru již přiřazeny role [čtecího modulu Log Analytics](../../role-based-access-control/built-in-roles.md#reader) , je dostatečná.

2. Pokud chcete uživateli udělit přístup k datům protokolu z jejich prostředků a nakonfigurovat jejich prostředky k odesílání protokolů do pracovního prostoru, udělejte toto:

    * Konfigurace režimu řízení přístupu k pracovnímu prostoru pro **použití oprávnění k pracovnímu prostoru nebo prostředku**

    * Udělte uživatelům v pracovním prostoru následující oprávnění: `Microsoft.OperationalInsights/workspaces/read` a `Microsoft.OperationalInsights/workspaces/sharedKeys/action`. Pomocí těchto oprávnění uživatelé nemůžou provádět žádné dotazy na úrovni pracovního prostoru. Mohou pouze vyčíslit pracovní prostor a použít ho jako cíl pro nastavení diagnostiky nebo konfiguraci agenta.

    * Udělte uživatelům následující oprávnění k prostředkům: `Microsoft.Insights/logs/*/read` a `Microsoft.Insights/diagnosticSettings/write`. Pokud jsou jim již přiřazena role [přispěvatel Log Analytics](../../role-based-access-control/built-in-roles.md#contributor) , přiřazená role čtenář nebo udělená oprávnění `*/read` u tohoto prostředku, je dostačující.

3. Chcete-li uživateli udělit přístup k datům protokolu ze svých prostředků, aniž by bylo možné číst události zabezpečení a odesílat data, postupujte následovně:

    * Konfigurace režimu řízení přístupu k pracovnímu prostoru pro **použití oprávnění k pracovnímu prostoru nebo prostředku**

    * Udělte uživatelům následující oprávnění k prostředkům: `Microsoft.Insights/logs/*/read`.

    * Přidejte následující neakci, abyste uživatelům zablokovali čtení SecurityEvent typu: `Microsoft.Insights/logs/SecurityEvent/read`. Neakce musí být ve stejné vlastní roli jako akce, která poskytuje oprávnění ke čtení (`Microsoft.Insights/logs/*/read`). Pokud uživatel vlastní akci čtení z jiné role, která je přiřazená tomuto prostředku nebo k předplatnému nebo skupině prostředků, mohl by číst všechny typy protokolů. To platí také v případě, že dědí `*/read`, které existují například s rolí čtenář nebo Přispěvatel.

4. Pokud chcete uživateli udělit přístup k datům protokolu z jejich prostředků a číst všechna přihlášení a data protokolu Azure AD, Update Management z pracovního prostoru, udělejte toto:

    * Konfigurace režimu řízení přístupu k pracovnímu prostoru pro **použití oprávnění k pracovnímu prostoru nebo prostředku**

    * Udělte uživatelům následující oprávnění k pracovnímu prostoru: 

        * `Microsoft.OperationalInsights/workspaces/read` – povinné, aby použití mohl vytvořit výčet pracovního prostoru a otevřít okno pracovního prostoru v Azure Portal
        * `Microsoft.OperationalInsights/workspaces/query/read` – vyžadováno pro každého uživatele, který může spouštět dotazy
        * @no__t – 0 – aby bylo možné číst protokoly přihlášení k Azure AD
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` – aby bylo možné číst Update Management protokoly řešení
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` – aby bylo možné číst Update Management protokoly řešení
        * @no__t – 0 – aby bylo možné číst protokoly správy aktualizací
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` – vyžaduje se, aby bylo možné používat Update Management řešení
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` – vyžaduje se, aby bylo možné používat Update Management řešení

    * Udělte uživatelům následující oprávnění ke svým prostředkům: `*/read`, přiřazený k roli Čtenář nebo `Microsoft.Insights/logs/*/read`. 

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

### <a name="considerations"></a>Odůvodněn

* Pokud má uživatel udělené globální oprávnění ke čtení s rolemi čtenář úrovně Standard a přispěvatel, které zahrnují akci _\*/čtení_ , potlačí řízení přístupu na základě tabulky a udělí jim přístup ke všem datům protokolu.
* Pokud je uživateli udělen přístup pro jednotlivé tabulky, ale žádná další oprávnění, by mohl získat přístup k datům protokolu z rozhraní API, ale nikoli z Azure Portal. K poskytnutí přístupu z Azure Portal jako základní roli použijte nástroj Log Analytics Reader.
* Správci předplatného budou mít přístup ke všem datovým typům bez ohledu na všechna ostatní nastavení oprávnění.
* Vlastníci pracovního prostoru se považují za každého jiného uživatele pro řízení přístupu k jednotlivým tabulkám.
* Chcete-li snížit počet přiřazení, je třeba přiřadit role ke skupinám zabezpečení místo jednotlivých uživatelů. Pomůže vám to také při konfiguraci a ověření přístupu pomocí existujících nástrojů pro správu skupin.

## <a name="next-steps"></a>Další kroky

* Informace o shromažďování dat z počítačů ve vašem datovém centru nebo v jiném cloudovém prostředí najdete v tématu [Přehled agenta Log Analytics](../../azure-monitor/platform/log-analytics-agent.md) .

* Další informace najdete v tématu [shromáždění dat o virtuálních počítačích Azure](../../azure-monitor/learn/quick-collect-azurevm.md) ke konfiguraci shromažďování dat z virtuálních počítačů Azure.
