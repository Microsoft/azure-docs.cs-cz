---
title: Správa pracovních prostorů Log Analytics v Azure Monitoru | Dokumenty společnosti Microsoft
description: Přístup k datům uloženým v pracovním prostoru Log Analytics v Azure Monitoru můžete spravovat pomocí prostředků, pracovního prostoru nebo oprávnění na úrovni tabulky. Tento článek podrobně popisuje, jak dokončit.
ms.subservice: logs
ms.topic: conceptual
author: bwren
ms.author: bwren
ms.date: 10/22/2019
ms.openlocfilehash: 1e559309b8e8d9768ca2f79dabfb01ec6086a961
ms.sourcegitcommit: 8a9c54c82ab8f922be54fb2fcfd880815f25de77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80348723"
---
# <a name="manage-access-to-log-data-and-workspaces-in-azure-monitor"></a>Správa přístupu k datům protokolu a pracovním prostorům v Azure Monitoru

Azure Monitor ukládá data [protokolu](data-platform-logs.md) v pracovním prostoru Analýzy protokolů. Pracovní prostor je kontejner, který obsahuje data a informace o konfiguraci. Chcete-li spravovat přístup k datům protokolu, provádějte různé úlohy správy související s pracovním prostorem.

Tento článek vysvětluje, jak spravovat přístup k protokolům a spravovat pracovní prostory, které je obsahují, včetně toho, jak udělit přístup k: 

* Pracovní prostor pomocí oprávnění pracovního prostoru.
* Uživatelé, kteří potřebují přístup k datům protokolu z konkrétních prostředků pomocí řízení přístupu na základě rolí Azure (RBAC).
* Uživatelé, kteří potřebují přístup k protokolu dat v konkrétní tabulce v pracovním prostoru pomocí Azure RBAC.

## <a name="configure-access-control-mode"></a>Konfigurace režimu řízení přístupu

Režim řízení [přístupu](design-logs-deployment.md) nakonfigurovaný v pracovním prostoru můžete zobrazit z webu Azure Portal nebo pomocí Azure PowerShellu.  Toto nastavení můžete změnit pomocí jedné z následujících podporovaných metod:

* portál Azure

* Azure PowerShell

* Šablona Azure Resource Manageru

### <a name="from-the-azure-portal"></a>Pomocí webu Azure Portal

Aktuální režim řízení přístupu k pracovnímu prostoru můžete zobrazit na stránce **Přehled** pracovního prostoru v nabídce **pracovního prostoru Analýzy protokolů.**

![Zobrazení režimu řízení přístupu k pracovnímu prostoru](media/manage-access/view-access-control-mode.png)

1. Přihlaste se k [https://portal.azure.com](https://portal.azure.com)portálu Azure na adrese .
1. Na portálu Azure vyberte pracovní prostory Analýzy protokolů > pracovní prostor.

Toto nastavení můžete změnit na stránce **Vlastnosti** pracovního prostoru. Změna nastavení bude zakázána, pokud nemáte oprávnění ke konfiguraci pracovního prostoru.

![Změna režimu přístupu k pracovnímu prostoru](media/manage-access/change-access-control-mode.png)

### <a name="using-powershell"></a>Pomocí prostředí PowerShell

Pomocí následujícího příkazu zkontrolujte režim řízení přístupu pro všechny pracovní prostory v předplatném:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {$_.Name + ": " + $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions}
```

Výstup by se měl podobat následujícímu:

```
DefaultWorkspace38917: True
DefaultWorkspace21532: False
```

Hodnota `False` znamená, že pracovní prostor je nakonfigurován s režimem přístupu kontextu pracovního prostoru.  Hodnota `True` znamená, že pracovní prostor je nakonfigurován s režimem přístupu kontextu prostředků.

> [!NOTE]
> Pokud je pracovní prostor vrácen bez logické hodnoty a je prázdný, `False` odpovídá to také výsledkům hodnoty.
>

Pomocí následujícího skriptu nastavte režim řízení přístupu pro určitý pracovní prostor na oprávnění kontextu prostředků:

```powershell
$WSName = "my-workspace"
$Workspace = Get-AzResource -Name $WSName -ExpandProperties
if ($Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $Workspace.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $Workspace.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $Workspace.ResourceId -Properties $Workspace.Properties -Force
```

Pomocí následujícího skriptu nastavte režim řízení přístupu pro všechny pracovní prostory v předplatným na oprávnění kontextu prostředků:

```powershell
Get-AzResource -ResourceType Microsoft.OperationalInsights/workspaces -ExpandProperties | foreach {
if ($_.Properties.features.enableLogAccessUsingOnlyResourcePermissions -eq $null)
    { $_.Properties.features | Add-Member enableLogAccessUsingOnlyResourcePermissions $true -Force }
else
    { $_.Properties.features.enableLogAccessUsingOnlyResourcePermissions = $true }
Set-AzResource -ResourceId $_.ResourceId -Properties $_.Properties -Force
}
```

### <a name="using-a-resource-manager-template"></a>Použití šablony Správce prostředků

Chcete-li nakonfigurovat režim přístupu v šabloně Správce prostředků Azure, nastavte příznak funkce **enableLogAccessUsingOnlyResourcePermissions** v pracovním prostoru na jednu z následujících hodnot.

* **false**: Nastavte pracovní prostor na oprávnění kontextu pracovního prostoru. Toto je výchozí nastavení, pokud příznak není nastaven.
* **true**: Nastavte pracovní prostor na oprávnění kontextu prostředků.

## <a name="manage-access-using-workspace-permissions"></a>Správa přístupu pomocí oprávnění pracovního prostoru

Ke každému pracovnímu prostoru může být přidruženo více účtů a každý účet může mít přístup k více pracovním prostorům. Přístup se spravuje pomocí [přístupu založeného na rolích Azure](../../role-based-access-control/role-assignments-portal.md).

Následující aktivity také vyžadují oprávnění Azure:

|Akce |Potřebná oprávnění Azure |Poznámky |
|-------|-------------------------|------|
| Přidání a odebrání monitorovacích řešení | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/*` <br> `Microsoft.OperationsManagement/*` <br> `Microsoft.Automation/*` <br> `Microsoft.Resources/deployments/*/write` | Tato oprávnění je potřeba udělit na úrovni skupiny prostředků nebo předplatného. |
| Změna cenové úrovně | `Microsoft.OperationalInsights/workspaces/*/write` | |
| Zobrazení dat na dlaždicích řešení *Backup* a *Site Recovery* | Správce nebo spolusprávce | Má přístup k prostředkům nasazeným pomocí modelu nasazení Azure Classic. |
| Vytvoření pracovního prostoru na webu Azure Portal | `Microsoft.Resources/deployments/*` <br> `Microsoft.OperationalInsights/workspaces/*` ||
| Zobrazení základních vlastností pracovního prostoru a zadání okna pracovního prostoru na portálu | `Microsoft.OperationalInsights/workspaces/read` ||
| Protokoly dotazů pomocí libovolného rozhraní | `Microsoft.OperationalInsights/workspaces/query/read` ||
| Přístup ke všem typům protokolů pomocí dotazů | `Microsoft.OperationalInsights/workspaces/query/*/read` ||
| Přístup k určité tabulce protokolů | `Microsoft.OperationalInsights/workspaces/query/<table_name>/read` ||
| Čtení kláves pracovního prostoru umožňujícíodesílání protokolů do tohoto pracovního prostoru | `Microsoft.OperationalInsights/workspaces/sharedKeys/action` ||

## <a name="manage-access-using-azure-permissions"></a>Správa přístupu pomocí oprávnění Azure

Pokud chcete udělit přístup k pracovnímu prostoru služby Log Analytics pomocí oprávnění Azure, postupujte podle kroků v tématu [Použití přiřazení rolí ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md). Například vlastní role, viz [Příklad vlastní role](#custom-role-examples)

Azure má dvě předdefinované uživatelské role pro pracovní prostory Log Analytics:

* Čtenář Log Analytics
* Přispěvatel Log Analytics

Členové role *Čtenář Log Analytics* můžou provádět:

* Zobrazení a prohledávání všech dat monitorování
* Zobrazení nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure pro všechny prostředky Azure

Role Čtečka analýzy protokolů zahrnuje následující akce Azure:

| Typ    | Oprávnění | Popis |
| ------- | ---------- | ----------- |
| Akce | `*/read`   | Možnost zobrazit všechny prostředky Azure a konfigurace prostředků. To zahrnuje zobrazení: <br> Stavu rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure pro prostředky <br> Všechny vlastnosti a nastavení všech prostředků. <br> V pracovních prostorech umožňuje úplná neomezená oprávnění číst nastavení pracovního prostoru a provádět dotaz na data. Podívejte se na podrobnější možnosti výše. |
| Akce | `Microsoft.OperationalInsights/workspaces/analytics/query/action` | Zastaralé, není třeba je přiřazovat uživatelům. |
| Akce | `Microsoft.OperationalInsights/workspaces/search/action` | Zastaralé, není třeba je přiřazovat uživatelům. |
| Akce | `Microsoft.Support/*` | Možnost otevírat případy podpory |
|Jiný než akce | `Microsoft.OperationalInsights/workspaces/sharedKeys/read` | Zabraňuje čtení klíče pracovního prostoru potřebného k použití rozhraní API pro shromažďování dat a k instalaci agentů. To zabrání uživateli v přidávání nových zdrojů do pracovního prostoru. |

Členové role *Přispěvatel Log Analytics* můžou provádět:

* Zahrnuje všechna oprávnění *role Čtečka log Analytics*, která uživateli umožňují číst všechna data monitorování.
* Vytvoření a konfigurace účtů automatizace
* Přidání a odebrání řešení pro správu

    > [!NOTE]
    > Chcete-li úspěšně provést poslední dvě akce, musí být toto oprávnění uděleno na úrovni skupiny prostředků nebo předplatného.

* Čtení klíčů účtu úložiště
* Konfigurace kolekce protokolů z Azure Storage
* Úprava nastavení monitorování pro prostředky Azure, včetně
  * Přidání rozšíření virtuálního počítače na virtuální počítače
  * Konfigurace diagnostiky Azure pro všechny prostředky Azure

> [!NOTE]
> Možnost přidat rozšíření virtuálního počítače na virtuální počítač můžete použít k získání úplné kontroly nad virtuálním počítačem.

Role přispěvatele analýzy protokolů zahrnuje následující akce Azure:

| Oprávnění | Popis |
| ---------- | ----------- |
| `*/read`     | Možnost zobrazit všechny prostředky a jejich konfiguraci. To zahrnuje zobrazení: <br> Stavu rozšíření virtuálního počítače <br> Konfigurace diagnostiky Azure pro prostředky <br> Všechny vlastnosti a nastavení všech prostředků. <br> V pracovních prostorech umožňuje úplná neomezená oprávnění číst nastavení pracovního prostoru a provádět dotaz na data. Podívejte se na podrobnější možnosti výše. |
| `Microsoft.Automation/automationAccounts/*` | Možnost vytvořit a konfigurovat účty služby Azure Automation, včetně přidávání a úprav runbooků |
| `Microsoft.ClassicCompute/virtualMachines/extensions/*` <br> `Microsoft.Compute/virtualMachines/extensions/*` | Přidání, aktualizace a odebrání rozšíření virtuálního počítače, včetně rozšíření Microsoft Monitoring Agent a rozšíření Agent OMS pro Linux |
| `Microsoft.ClassicStorage/storageAccounts/listKeys/action` <br> `Microsoft.Storage/storageAccounts/listKeys/action` | Zobrazení klíče účtu úložiště. Požadovaný ke konfiguraci Log Analytics pro čtení protokolů z účtů služby Azure Storage |
| `Microsoft.Insights/alertRules/*` | Přidání, aktualizace a odebrání pravidel upozornění |
| `Microsoft.Insights/diagnosticSettings/*` | Přidání, aktualizace a odebrání nastavení diagnostiky pro prostředky Azure |
| `Microsoft.OperationalInsights/*` | Přidejte, aktualizujte a odeberte konfiguraci pro pracovní prostory Analýzy protokolů. Chcete-li upravit upřesňující `Microsoft.OperationalInsights/workspaces/write`nastavení pracovního prostoru, potřeby uživatelů . |
| `Microsoft.OperationsManagement/*` | Přidání a odebrání řešení pro správu |
| `Microsoft.Resources/deployments/*` | Vytvoření a odstranění nasazení. Požadováno pro přidávání a odebírání řešení, pracovních prostorů a účtů služby Automation |
| `Microsoft.Resources/subscriptions/resourcegroups/deployments/*` | Vytvoření a odstranění nasazení. Požadováno pro přidávání a odebírání řešení, pracovních prostorů a účtů služby Automation |

Pokud chcete přidat uživatele do role uživatele nebo je z ní odebrat, je potřeba mít oprávnění `Microsoft.Authorization/*/Delete` a `Microsoft.Authorization/*/Write`.

Pomocí těchto rolí můžete uživatelům udělit přístup v různých oborech:

* Předplatné – Přístup ke všem pracovním prostorům v rámci předplatného
* Skupina prostředků – Přístup ke všem pracovním prostorům v rámci skupiny prostředků
* Prostředek – Přístup pouze k zadanému pracovnímu prostoru

Doporučujeme provádět přiřazení na úrovni zdrojů (pracovní prostor) pro zajištění přesného řízení přístupu. Pomocí [vlastních rolí](../../role-based-access-control/custom-roles.md) můžete vytvářet role s konkrétními požadovanými oprávněními.

### <a name="resource-permissions"></a>Oprávnění k prostředkům

Když se uživatelé dotazují na protokoly z pracovního prostoru pomocí přístupu k kontextu prostředků, budou mít k prostředku následující oprávnění:

| Oprávnění | Popis |
| ---------- | ----------- |
| `Microsoft.Insights/logs/<tableName>/read`<br><br>Příklady:<br>`Microsoft.Insights/logs/*/read`<br>`Microsoft.Insights/logs/Heartbeat/read` | Možnost zobrazit všechna data protokolu pro prostředek.  |
| `Microsoft.Insights/diagnosticSettings/write` | Možnost konfigurovat nastavení diagnostiky, aby bylo možné nastavit protokoly pro tento prostředek. |

`/read`oprávnění je obvykle uděleno z role, která _\*_ zahrnuje _ \*/read nebo_ oprávnění, jako jsou předdefinované role [čtečky](../../role-based-access-control/built-in-roles.md#reader) a [přispěvatele.](../../role-based-access-control/built-in-roles.md#contributor) Vlastní role, které obsahují určité akce nebo vyhrazené předdefinované role, nemusí toto oprávnění zahrnovat.

Pokud chcete vytvořit jiný ovládací prvek přístupu pro různé tabulky, přečtěte si níže odkaz [Definování řízení přístupu podle tabulky.](#table-level-rbac)

## <a name="custom-role-examples"></a>Příklady vlastních rolí

1. Chcete-li uživateli udělit přístup k datům protokolu z jejich prostředků, proveďte následující kroky:

    * Konfigurace režimu řízení přístupu k pracovnímu prostoru pro **použití oprávnění pracovního prostoru nebo prostředků**

    * Udělte uživatelům `*/read` nebo `Microsoft.Insights/logs/*/read` oprávnění k jejich prostředkům. Pokud jsou již [přiřazeny role Čtečka analýzy protokolů](../../role-based-access-control/built-in-roles.md#reader) v pracovním prostoru, je dostačující.

2. Chcete-li uživateli udělit přístup k datům protokolu z jejich prostředků a nakonfigurovat jejich prostředky tak, aby odesílalo protokoly do pracovního prostoru, proveďte následující kroky:

    * Konfigurace režimu řízení přístupu k pracovnímu prostoru pro **použití oprávnění pracovního prostoru nebo prostředků**

    * Udělte uživatelům následující oprávnění `Microsoft.OperationalInsights/workspaces/read` k `Microsoft.OperationalInsights/workspaces/sharedKeys/action`pracovnímu prostoru: a . S těmito oprávněními uživatelé nemohou provádět žádné dotazy na úrovni pracovního prostoru. Mohou pouze vytvořit výčet pracovního prostoru a použít jej jako cíl pro nastavení diagnostiky nebo konfiguraci agenta.

    * Udělte uživatelům následující oprávnění `Microsoft.Insights/logs/*/read` `Microsoft.Insights/diagnosticSettings/write`k jejich prostředkům: a . Pokud jsou již [přiřazeny role přispěvatele analýzy protokolu,](../../role-based-access-control/built-in-roles.md#contributor) přiřazena role Čtenář nebo udělena `*/read` oprávnění pro tento prostředek, je dostačující.

3. Chcete-li uživateli udělit přístup k datům protokolu z jejich prostředků, aniž by mohli číst události zabezpečení a odesílat data, proveďte následující kroky:

    * Konfigurace režimu řízení přístupu k pracovnímu prostoru pro **použití oprávnění pracovního prostoru nebo prostředků**

    * Udělte uživatelům jejich prostředky `Microsoft.Insights/logs/*/read`následující oprávnění: .

    * Přidejte následující nonaction, abyste uživatelům zabránili `Microsoft.Insights/logs/SecurityEvent/read`ve čtení typu SecurityEvent: . NonAction musí být ve stejné vlastní roli jako akce,`Microsoft.Insights/logs/*/read`která poskytuje oprávnění ke čtení ( ). Pokud uživatel vlastní akce čtení z jiné role, která je přiřazena k tomuto prostředku nebo k odběru nebo skupiny prostředků, budou moci číst všechny typy protokolů. To platí také v `*/read`případě, že dědí , které existují například s rolí Čtenář nebo Přispěvatel.

4. Chcete-li uživateli udělit přístup k datům protokolu z jejich prostředků a číst všechna přihlášení k azure ad a číst data protokolu řešení správy aktualizací z pracovního prostoru, proveďte následující kroky:

    * Konfigurace režimu řízení přístupu k pracovnímu prostoru pro **použití oprávnění pracovního prostoru nebo prostředků**

    * Udělte uživatelům následující oprávnění k pracovnímu prostoru: 

        * `Microsoft.OperationalInsights/workspaces/read`– vyžaduje, aby použití můžete výčet pracovního prostoru a otevřít okno pracovního prostoru na webu Azure Portal
        * `Microsoft.OperationalInsights/workspaces/query/read`– vyžadováno pro každého uživatele, který může provádět dotazy
        * `Microsoft.OperationalInsights/workspaces/query/SigninLogs/read`– možnost číst protokoly přihlášení Azure AD
        * `Microsoft.OperationalInsights/workspaces/query/Update/read`– aby bylo možné číst protokoly řešení správy aktualizací
        * `Microsoft.OperationalInsights/workspaces/query/UpdateRunProgress/read`– aby bylo možné číst protokoly řešení správy aktualizací
        * `Microsoft.OperationalInsights/workspaces/query/UpdateSummary/read`– aby bylo možné číst protokoly správy aktualizací
        * `Microsoft.OperationalInsights/workspaces/query/Heartbeat/read`– musí být schopen používat řešení pro správu aktualizací
        * `Microsoft.OperationalInsights/workspaces/query/ComputerGroup/read`– musí být schopen používat řešení pro správu aktualizací

    * Udělte uživatelům jejich prostředky `*/read`následující oprávnění: , `Microsoft.Insights/logs/*/read`přiřazená roli Čtečka nebo . 

## <a name="table-level-rbac"></a>Úroveň tabulky RBAC

**Úroveň tabulky RBAC** umožňuje definovat podrobnější ovládací prvek pro data v pracovním prostoru Log Analytics kromě dalších oprávnění. Tento ovládací prvek umožňuje definovat konkrétní datové typy, které jsou přístupné pouze pro určitou sadu uživatelů.

Implementovat řízení přístupu k tabulce s [vlastní role Azure](../../role-based-access-control/custom-roles.md) buď udělit přístup k určité [tabulky](../log-query/logs-structure.md) v pracovním prostoru. Tyto role se používají pro pracovní prostory s [režimy řízení přístupu](design-logs-deployment.md#access-control-mode) kontextu pracovního prostoru nebo kontextu prostředků bez ohledu na [režim přístupu](design-logs-deployment.md#access-mode)uživatele .

Vytvořte [vlastní roli](../../role-based-access-control/custom-roles.md) s následujícími akcemi pro definování přístupu k ovládacímu prvku přístupu k tabulce.

* Chcete-li udělit přístup k tabulce, zahrňte ji do části **Akce** definice role. Chcete-li odečíst přístup od **povolených akcí**, zahrňte jej do části **NotActions.**
* K určení všech tabulek použijte Microsoft.OperationalInsights/workspaces/query/*.

Chcete-li například vytvořit roli s přístupem k _tabulkám Prezenční signál_ a _AzureActivity,_ vytvořte vlastní roli pomocí následujících akcí:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Heartbeat/read",
    "Microsoft.OperationalInsights/workspaces/query/AzureActivity/read"
  ],
```

Chcete-li vytvořit roli s přístupem pouze k tabulce _SecurityBaseline,_ vytvořte vlastní roli pomocí následujících akcí:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/SecurityBaseline/read"
],
```

### <a name="custom-logs"></a>Vlastní protokoly

 Vlastní protokoly jsou vytvářeny ze zdrojů dat, jako jsou vlastní protokoly a rozhraní API shromažďování dat HTTP. Nejjednodušší způsob, jak identifikovat typ protokolu, je kontrolou tabulek uvedených v části [Vlastní protokoly ve schématu protokolu](../log-query/get-started-portal.md#understand-the-schema).

 V současné době nelze udělit přístup k jednotlivým vlastním protokolům, ale můžete udělit přístup ke všem vlastním protokolům. Chcete-li vytvořit roli s přístupem ke všem vlastním protokolům, vytvořte vlastní roli pomocí následujících akcí:

```
"Actions":  [
    "Microsoft.OperationalInsights/workspaces/read",
    "Microsoft.OperationalInsights/workspaces/query/read",
    "Microsoft.OperationalInsights/workspaces/query/Tables.Custom/read"
],
```

### <a name="considerations"></a>Požadavky

* Pokud je uživateli uděleno globální oprávnění ke čtení se standardními rolemi čtečky nebo přispěvatele, které obsahují akci _ \*/read,_ přepíše řízení přístupu podle tabulky a poskytne mu přístup ke všem datům protokolu.
* Pokud je uživateli udělen přístup podle tabulky, ale žádná další oprávnění, bude mít přístup k datům protokolu z rozhraní API, ale ne z portálu Azure. Chcete-li poskytnout přístup z portálu Azure, použijte Log Analytics Reader jako svou základní roli.
* Správci předplatného budou mít přístup ke všem datovým typům bez ohledu na další nastavení oprávnění.
* S vlastníky pracovního prostoru se pro řízení přístupu podle tabulky zachází jako s ostatními uživateli.
* Doporučujeme přiřadit role skupinám zabezpečení namísto jednotlivým uživatelům, aby se snížil počet přiřazení. To vám také pomůže použít existující nástroje pro správu skupin ke konfiguraci a ověření přístupu.

## <a name="next-steps"></a>Další kroky

* V [tématu Přehled agenta Analýzy protokolů,](../../azure-monitor/platform/log-analytics-agent.md) kde najdete data z počítačů ve vašem datovém centru nebo jiném cloudovém prostředí.

* Najdete [v tématu Shromažďování dat o virtuálních počítačích Azure](../../azure-monitor/learn/quick-collect-azurevm.md) ke konfiguraci shromažďování dat z virtuálních počítačů Azure.
