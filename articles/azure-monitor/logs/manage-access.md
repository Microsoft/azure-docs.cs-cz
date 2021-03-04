---
title: Správa pracovních prostorů Log Analytics v Azure Monitor | Microsoft Docs
description: Můžete spravovat přístup k datům uloženým v pracovním prostoru Log Analytics v Azure Monitor pomocí oprávnění k prostředkům, pracovnímu prostoru nebo úrovni tabulky. Tento článek podrobně popisuje, jak dokončit.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 04/10/2019
ms.openlocfilehash: bc03d8cdcaf0079ef15e648d16fd5656d9da368c
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101725675"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Správa přístupu k datům protokolů a pracovním prostorům ve službě Azure Monitor

Azure Monitor ukládá data [protokolu](../logs/data-platform-logs.md) v pracovním prostoru Log Analytics. Pracovní prostor je kontejner, který obsahuje data a informace o konfiguraci. Ke správě přístupu k datům protokolu provádíte různé úlohy správy související s vaším pracovním prostorem.

Tento článek vysvětluje, jak spravovat přístup k protokolům a spravovat pracovní prostory, které je obsahují, včetně toho, jak udělit přístup k: 

* Pracovní prostor s oprávněními v pracovním prostoru.
* Uživatelé, kteří potřebují přístup k datům protokolu z konkrétních prostředků pomocí řízení přístupu na základě role Azure (Azure RBAC), označovaného také jako [kontext prostředku](../logs/design-logs-deployment.md#access-mode)
* Uživatelé, kteří potřebují přístup k datům protokolů v konkrétní tabulce v pracovním prostoru pomocí Azure RBAC.

Pokud chcete porozumět koncepcím, které se týkají služby Azure RBAC a strategií přístupu, přečtěte si téma [návrh nasazení Azure Monitorch protokolů](../logs/design-logs-deployment.md)

## <a name="configure-access-control-mode"></a>Konfigurovat režim řízení přístupu

[Režim řízení přístupu](../logs/design-logs-deployment.md) nakonfigurovaný v pracovním prostoru můžete zobrazit z Azure Portal nebo pomocí Azure PowerShell.  Toto nastavení můžete změnit pomocí jedné z následujících podporovaných metod:

* portál Azure

* Azure PowerShell

* Šablona Azure Resource Manageru

### <a name="from-the-azure-portal"></a>Pomocí webu Azure Portal

Aktuální režim řízení přístupu k pracovnímu prostoru můžete zobrazit na stránce **Přehled** pracovního prostoru v nabídce **Log Analytics pracovní** prostor.

![Zobrazit režim řízení přístupu k pracovnímu prostoru](media/manage-access/view-access-control-mode.png)

1. Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).
1. V Azure Portal vyberte pracovní prostory Log Analytics > pracovní prostor.

Toto nastavení můžete změnit na stránce **vlastností** pracovního prostoru. Změna nastavení se zakáže, pokud nemáte oprávnění ke konfiguraci pracovního prostoru.

![Změnit režim přístupu k pracovnímu prostoru](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>Pomocí prostředí PowerShell

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
}
```

### <a name="using-a-resource-manager-template"></a>Použití šablony Resource Manageru

Pro konfiguraci režimu přístupu v šabloně Azure Resource Manager nastavte příznak funkce **enableLogAccessUsingOnlyResourcePermissions** v pracovním prostoru na jednu z následujících hodnot.

* **false**: Nastavte pracovní prostor na oprávnění kontextu v pracovním prostoru. Toto je výchozí nastavení, pokud příznak není nastaven.
* **true**: Nastavte pracovní prostor na oprávnění kontextu prostředků.

## <a name="manage-access-using-workspace-permissions"></a>Správa přístupu pomocí oprávnění k pracovnímu prostoru

Ke každému pracovnímu prostoru může být přidruženo několik účtů a každý účet může mít přístup k několika pracovním prostorům. Přístup se spravuje pomocí [řízení přístupu na základě role Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md).

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

Pokud chcete udělit přístup k pracovnímu prostoru Log Analytics pomocí oprávnění Azure, postupujte podle kroků v části [přiřazení rolí Azure ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md). Například vlastní role najdete v tématu [Příklady vlastních rolí](#custom-role-examples) .

Azure má dvě předdefinované role uživatelů pro Log Analytics pracovní prostory:

* Čtenář Log Analytics
* Přispěvatel Log Analytics

Členové role *Čtenář Log Analytics* můžou provádět:

* Zobrazení a prohledávání všech dat monitorování
* Zobrazení nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure pro všechny prostředky Azure

Role čtecího modulu Log Analytics zahrnuje následující akce Azure:

| Typ    | Oprávnění | Popis |
| ------- | ---------- | ----------- |
| Akce | `*/read`   | Možnost Zobrazit všechny prostředky a konfiguraci prostředků Azure. To zahrnuje zobrazení: <br> Stavu rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure pro prostředky <br> Všechny vlastnosti a nastavení všech prostředků. <br> U pracovních prostorů umožňuje plná neomezená oprávnění ke čtení nastavení pracovního prostoru a provádění dotazů na data. Podívejte se na podrobnější možnosti výše. |
| Akce | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Zastaralé, není nutné je přiřazovat uživatelům. |
| Akce | `Microsoft.OperationalInsights/workspaces/search/action` | Zastaralé, není nutné je přiřazovat uživatelům. |
| Akce | `Microsoft.Support/*` | Možnost otevírat případy podpory |
|Jiný než akce | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Zabraňuje čtení klíče pracovního prostoru požadovaného k použití rozhraní API pro shromažďování dat a k instalaci agentů. Tím se zabrání uživateli v přidávání nových prostředků do pracovního prostoru. |

Členové role *Přispěvatel Log Analytics* můžou provádět:

* Zahrnuje všechna oprávnění *role čtecího modulu Log Analytics*, takže uživatel bude moct číst všechna data monitorování.
* Vytvoření a konfigurace účtů služby Automation
* Přidání a odebrání řešení pro správu

    > [!NOTE]
    > Aby bylo možné úspěšně provést poslední dvě akce, je nutné toto oprávnění udělit na úrovni skupiny prostředků nebo předplatného.

* Čtení klíčů účtu úložiště
* Konfigurace kolekce protokolů z Azure Storage
* Úprava nastavení monitorování pro prostředky Azure, včetně
  * Přidání rozšíření virtuálního počítače na virtuální počítače
  * Konfigurace diagnostiky Azure pro všechny prostředky Azure

> [!NOTE]
> Možnost přidat rozšíření virtuálního počítače na virtuální počítač můžete použít k získání úplné kontroly nad virtuálním počítačem.

Role Přispěvatel Log Analytics zahrnuje následující akce Azure:

| Oprávnění | Popis |
| ---------- | ----------- |
| `*/read`     | Možnost zobrazit všechny prostředky a jejich konfiguraci. To zahrnuje zobrazení: <br> Stavu rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure pro prostředky <br> Všechny vlastnosti a nastavení všech prostředků. <br> U pracovních prostorů umožňuje úplná neomezená oprávnění číst nastavení pracovního prostoru a provádět dotaz na data. Podívejte se na podrobnější možnosti výše. |
| `Microsoft.Automation/automationAccounts/*` | Možnost vytvořit a konfigurovat účty služby Azure Automation, včetně přidávání a úprav runbooků |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Přidání, aktualizace a odebrání rozšíření virtuálního počítače, včetně rozšíření Microsoft Monitoring Agent a rozšíření Agent OMS pro Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Zobrazení klíče účtu úložiště. Požadovaný ke konfiguraci Log Analytics pro čtení protokolů z účtů služby Azure Storage |
| `Microsoft.Insights/alertRules/*` | Přidání, aktualizace a odebrání pravidel upozornění |
| `Microsoft.Insights/diagnosticSettings/*` | Přidání, aktualizace a odebrání nastavení diagnostiky pro prostředky Azure |
| `Microsoft.OperationalInsights/*` | Přidání, aktualizace a odebrání konfigurace Log Analytics pracovních prostorů. Pro úpravu rozšířených nastavení pracovního prostoru, uživatelských potřeb `Microsoft.OperationalInsights/workspaces/write` . |
| `Microsoft.OperationsManagement/*` | Přidání a odebrání řešení pro správu |
| `Microsoft.Resources/deployments/*` | Vytvoření a odstranění nasazení. Požadováno pro přidávání a odebírání řešení, pracovních prostorů a účtů služby Automation |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Vytvoření a odstranění nasazení. Požadováno pro přidávání a odebírání řešení, pracovních prostorů a účtů služby Automation |

Pokud chcete přidat uživatele do role uživatele nebo je z ní odebrat, je potřeba mít oprávnění `Microsoft.Authorization/*/Delete` a `Microsoft.Authorization/*/Write`.

Pomocí těchto rolí můžete uživatelům udělit přístup v různých oborech:

* Předplatné – Přístup ke všem pracovním prostorům v rámci předplatného
* Skupina prostředků – Přístup ke všem pracovním prostorům v rámci skupiny prostředků
* Prostředek – Přístup pouze k zadanému pracovnímu prostoru

Pro zajištění přesného řízení přístupu doporučujeme provést přiřazení na úrovni prostředků (pracovní prostor). Pomocí [vlastních rolí](../../role-based-access-control/custom-roles.md) můžete vytvářet role s konkrétními požadovanými oprávněními.

### <a name="resource-permissions"></a>Oprávnění prostředků

Když se uživatelé dotazují v protokolech z pracovního prostoru pomocí přístupu kontextu prostředků, budou mít pro tento prostředek následující oprávnění:

| Oprávnění | Popis |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Příklady:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Možnost Zobrazit všechna data protokolu pro daný prostředek.  |
| `Microsoft.Insights/diagnosticSettings/write` | Možnost konfigurace nastavení diagnostiky tak, aby povolovala nastavování protokolů pro tento prostředek. |

`/read`oprávnění je obvykle uděleno z role, která zahrnuje _\* /Read nebo_ oprávnění, jako jsou například _\*_ předdefinované role [Čtenář](../../role-based-access-control/built-in-roles.md#reader) a [Přispěvatel](../../role-based-access-control/built-in-roles.md#contributor) . Toto oprávnění nemusí zahrnovat vlastní role, které obsahují konkrétní akce nebo vyhrazené předdefinované role.

Pokud chcete vytvořit různé řízení přístupu pro různé tabulky, přečtěte si téma [Definování řízení přístupu pro jednotlivé tabulky](#table-level-azure-rbac) .

## <a name="custom-role-examples"></a>Příklady vlastních rolí

1. Pokud chcete uživateli udělit přístup k datům protokolu z jejich prostředků, udělejte toto:

    * Konfigurace režimu řízení přístupu k pracovnímu prostoru pro **použití oprávnění k pracovnímu prostoru nebo prostředku**

    * Udělte uživatelům `*/read` nebo `Microsoft.Insights/logs/*/read` oprávněním ke svým prostředkům. Pokud jsou v pracovním prostoru již přiřazeny role [čtecího modulu Log Analytics](../../role-based-access-control/built-in-roles.md#reader) , je dostatečná.

2. Pokud chcete uživateli udělit přístup k datům protokolu z jejich prostředků a nakonfigurovat jejich prostředky k odesílání protokolů do pracovního prostoru, udělejte toto:

    * Konfigurace režimu řízení přístupu k pracovnímu prostoru pro **použití oprávnění k pracovnímu prostoru nebo prostředku**

    * Udělte uživatelům následující oprávnění v pracovním prostoru: `Microsoft.OperationalInsights/workspaces/read` a `Microsoft.OperationalInsights/workspaces/sharedKeys/action` . Pomocí těchto oprávnění uživatelé nemůžou provádět žádné dotazy na úrovni pracovního prostoru. Mohou pouze vyčíslit pracovní prostor a použít ho jako cíl pro nastavení diagnostiky nebo konfiguraci agenta.

    * Udělte uživatelům následující oprávnění k prostředkům: `Microsoft.Insights/logs/*/read` a `Microsoft.Insights/diagnosticSettings/write` . Pokud jsou jim již přiřazena role [přispěvatel Log Analytics](../../role-based-access-control/built-in-roles.md#contributor) , přiřazená role čtenáře nebo udělená `*/read` oprávnění k tomuto prostředku, je dostatečná.

3. Chcete-li uživateli udělit přístup k datům protokolu ze svých prostředků, aniž by bylo možné číst události zabezpečení a odesílat data, postupujte následovně:

    * Konfigurace režimu řízení přístupu k pracovnímu prostoru pro **použití oprávnění k pracovnímu prostoru nebo prostředku**

    * Udělte uživatelům následující oprávnění ke svým prostředkům: `Microsoft.Insights/logs/*/read` .

    * Přidejte následující neakci, pokud chcete uživatelům zablokovat čtení typu SecurityEvent: `Microsoft.Insights/logs/SecurityEvent/read` . Neakce musí být ve stejné vlastní roli jako akce, která poskytuje oprávnění ke čtení ( `Microsoft.Insights/logs/*/read` ). Pokud uživatel vlastní akci čtení z jiné role, která je přiřazená tomuto prostředku nebo k předplatnému nebo skupině prostředků, mohl by číst všechny typy protokolů. To platí také v případě, `*/read` že dědí, které existují například s rolí čtenář nebo Přispěvatel.

4. Pokud chcete uživateli udělit přístup k datům protokolu z jejich prostředků a číst všechna přihlášení a data protokolu Azure AD, Update Management z pracovního prostoru, udělejte toto:

    * Konfigurace režimu řízení přístupu k pracovnímu prostoru pro **použití oprávnění k pracovnímu prostoru nebo prostředku**

    * Udělte uživatelům následující oprávnění k pracovnímu prostoru: 

        * `Microsoft.OperationalInsights/workspaces/read` – požadováno, aby uživatel mohl vytvořit výčet pracovního prostoru a otevřít okno pracovního prostoru v Azure Portal
        * `Microsoft.OperationalInsights/workspaces/query/read` – vyžaduje se pro každého uživatele, který může spouštět dotazy.
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read` – aby bylo možné číst protokoly přihlášení k Azure AD
        * `Microsoft.OperationalInsights/workspaces/query/Update/read` – aby bylo možné číst Update Management protokoly řešení
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read` – aby bylo možné číst Update Management protokoly řešení
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read` – aby bylo možné číst protokoly správy aktualizací
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read` – vyžaduje se, aby bylo možné používat Update Management řešení
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read` – vyžaduje se, aby bylo možné používat Update Management řešení

    * Udělte uživatelům následující oprávnění k prostředkům, která jsou `*/read` přiřazená k roli Čtenář, nebo `Microsoft.Insights/logs/*/read` . 

## <a name="table-level-azure-rbac"></a>Úroveň tabulky – Azure RBAC

**Úroveň tabulky Azure RBAC** umožňuje definovat podrobnější řízení dat v log Analyticsovém pracovním prostoru kromě dalších oprávnění. Tento ovládací prvek umožňuje definovat konkrétní datové typy, které jsou přístupné pouze konkrétní sadě uživatelů.

Pomocí [vlastních rolí Azure](../../role-based-access-control/custom-roles.md) implementujete řízení přístupu k tabulce pro udělení přístupu ke konkrétním [tabulkám](../logs/data-platform-logs.md) v pracovním prostoru. Tyto role se v pracovních prostorech aplikují pomocí [režimů řízení přístupu](../logs/design-logs-deployment.md#access-control-mode) kontextu pracovního prostoru nebo kontextu prostředků bez ohledu na [režim přístupu](../logs/design-logs-deployment.md#access-mode)uživatele.

Vytvořte [vlastní roli](../../role-based-access-control/custom-roles.md) s následujícími akcemi, které definují přístup k řízení přístupu k tabulce.

* Chcete-li udělit přístup k tabulce, zahrňte ji do oddílu **Akce** definice role. Pokud chcete odečíst přístup z povolených **akcí**, zahrňte ho do oddílu **NotActions** .
* Pro zadání všech tabulek použijte Microsoft. OperationalInsights/Workspaces/Query/*.

Chcete-li například vytvořit roli s přístupem k tabulkám _prezenčního signálu_ a _AzureActivity_ , vytvořte vlastní roli pomocí následujících akcí:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Chcete-li vytvořit roli s přístupem pouze k tabulce _SecurityBaseline_ , vytvořte vlastní roli pomocí následujících akcí:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```
Výše uvedené příklady definují seznam povolených tabulek. Tento příklad ukazuje definici blokovaného seznamu, když má uživatel přístup ke všem tabulkám, ale k tabulce _SecurityAlert_ :

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/*/read"
],
"notActions":  [
    "Microsoft.OperationalInsights/workspaces/query/SecurityAlert/read"
],
```

### <a name="custom-logs"></a>Vlastní protokoly

 Vlastní protokoly se vytvářejí ze zdrojů dat, jako jsou vlastní protokoly a rozhraní API kolekce dat HTTP. Nejjednodušší způsob, jak identifikovat typ protokolu, je kontrola tabulek uvedených v části [vlastní protokoly ve schématu protokolu](./log-analytics-tutorial.md#table-schema).

 Nemůžete udělit přístup k jednotlivým vlastním protokolům, ale můžete udělit přístup ke všem vlastním protokolům. Chcete-li vytvořit roli s přístupem ke všem vlastním protokolům, vytvořte vlastní roli pomocí následujících akcí:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```
Alternativním přístupem ke správě přístupu k vlastním protokolům je přiřazení prostředku Azure a Správa přístupu pomocí paradigma kontextu prostředků. Pokud chcete použít tuto metodu, musíte do hlavičky [x-MS-AzureResourceId](../logs/data-collector-api.md#request-headers) zadat ID prostředku, když se data ingestují Log Analytics prostřednictvím [rozhraní API pro shromažďování dat http](../logs/data-collector-api.md). ID prostředku musí být platné a na něj se vztahují pravidla přístupu. Po ingestování protokolů jsou přístupné uživatelům s přístupem pro čtení k prostředku, jak je popsáno zde.

Někdy vlastní protokoly pocházejí ze zdrojů, které nejsou přímo přidružené ke konkrétnímu prostředku. V takovém případě vytvořte skupinu prostředků jenom pro správu přístupu k těmto protokolům. V této skupině prostředků se neúčtují žádné náklady, ale poskytuje platné ID prostředku pro řízení přístupu k vlastním protokolům. Pokud třeba konkrétní brána firewall odesílá vlastní protokoly, vytvořte skupinu prostředků s názvem "MyFireWallLogs" a ujistěte se, že požadavky rozhraní API obsahují ID prostředku "MyFireWallLogs". Záznamy protokolu brány firewall jsou pak přístupné jenom uživatelům, kterým byl udělen přístup k MyFireWallLogs nebo s úplným přístupem k pracovnímu prostoru.          

### <a name="considerations"></a>Požadavky

* Pokud má uživatel udělené globální oprávnění ke čtení s rolemi čtenář úrovně Standard a přispěvatel, které zahrnují akci _\* /Read_ , potlačí řízení přístupu na jednotlivé tabulky a udělí jim přístup ke všem datům protokolu.
* Pokud je uživateli udělen přístup pro jednotlivé tabulky, ale žádná další oprávnění, by mohl získat přístup k datům protokolu z rozhraní API, ale nikoli z Azure Portal. K poskytnutí přístupu z Azure Portal jako základní roli použijte nástroj Log Analytics Reader.
* Správci a vlastníci předplatného budou mít přístup ke všem datovým typům bez ohledu na všechna ostatní nastavení oprávnění.
* Vlastníci pracovního prostoru se považují za každého jiného uživatele pro řízení přístupu k jednotlivým tabulkám.
* Pro snížení počtu přiřazení doporučujeme přiřadit role ke skupinám zabezpečení místo jednotlivých uživatelů. Pomůže vám to také při konfiguraci a ověření přístupu pomocí existujících nástrojů pro správu skupin.

## <a name="next-steps"></a>Další kroky

* Informace o shromažďování dat z počítačů ve vašem datovém centru nebo v jiném cloudovém prostředí najdete v tématu [Přehled agenta Log Analytics](../agents/log-analytics-agent.md) .

* Další informace najdete v tématu [shromáždění dat o virtuálních počítačích Azure](../vm/quick-collect-azurevm.md) ke konfiguraci shromažďování dat z virtuálních počítačů Azure.