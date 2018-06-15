---
title: Řízení přístupu na základě role ve službě Azure Automation
description: Řízení přístupu na základě role (RBAC) umožňuje správu přístupu k prostředkům Azure. Tento článek popisuje způsob nastavení řízení přístupu na základě role ve službě Azure Automation.
keywords: rbac v automation, řízení přístupu na základě rolí, rbac v azure
services: automation
ms.service: automation
ms.component: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: fd96a6cfebe44bd02e3f44a44d91119ad1c2c5a9
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34598748"
---
# <a name="role-based-access-control-in-azure-automation"></a>Řízení přístupu na základě role ve službě Azure Automation

Řízení přístupu na základě role (RBAC) umožňuje správu přístupu k prostředkům Azure. Pomocí [RBAC](../role-based-access-control/overview.md), můžete v rámci týmu oddělit povinností a poskytnout pouze takovou úroveň přístupu pro uživatele, skupiny, a aplikace, které potřebují k plnění jejich pracovních úkolů. Přístup na základě role můžete uživatelům udělit pomocí webu Azure Portal, nástrojů příkazového řádku Azure nebo rozhraní API pro správu Azure.

## <a name="roles-in-automation-accounts"></a>Role v účtech Automation

Ve službě Azure Automation se přístup uděluje přiřazením příslušné role RBAC uživatelům, skupinám a aplikacím v rozsahu účtu Automation. Níže jsou uvedené vestavěné role, které účet Automation podporuje:

| **Role** | **Popis** |
|:--- |:--- |
| Vlastník |Role vlastníka umožňuje přístup ke všem prostředkům a akcím v rámci účtu Automation, včetně poskytnutí přístupu k další uživatelé, skupiny a aplikace pro správu účtu služby Automation. |
| Přispěvatel |Role přispěvatele umožňuje spravovat všechno kromě úpravy oprávnění jiných uživatelů k přístupu k účtu Automation. |
| Čtenář |Role čtenáře vám umožní zobrazit všechny prostředky na účtu Automation, ale neumožní vám provádět změny. |
| Operátor služby Automation |Roli operátor automatizace můžete zobrazit název sady runbook a vlastnosti a vytvářet a spravovat úlohy pro všechny sady runbook na účtu Automation. Tato role je užitečná, pokud chcete chránit prostředky na účtu Automation, například assety přihlašovacích údajů a runbooky, aby je nikdo nemohl zobrazit nebo upravit, ale aby členové vaší organizace mohli tyto runbooky stále spouštět. |
|Operátor úloh Automation|Roli operátor automatizace úloh umožňuje vytvářet a spravovat úlohy pro všechny sady runbook na účtu Automation.|
|Operátor runbooků Automation|Roli operátor automatizace sady Runbook vám umožní zobrazit název a vlastnosti sady runbook.|
| Přispěvatel Log Analytics | Role Přispěvatel Log Analytics umožňuje načíst všechna data monitorování a upravit nastavení monitorování. Úprava nastavení monitorování, zahrnuje přidání rozšíření virtuálního počítače pro virtuální počítače, čtení klíče účtu úložiště, abyste mohli nakonfigurovat shromažďování protokolů ze služby Azure storage, vytváření a konfiguraci účtů Automation, přidání řešení a konfigurace Azure diagnostics pro všechny prostředky Azure.|
| Čtenář Log Analytics | Role čtenáře analýzy protokolů umožňuje zobrazení a hledání všech monitorování dat a také zobrazení nastavení monitorování. To zahrnuje zobrazení konfigurace Azure diagnostics na všechny prostředky Azure. |
| Přispěvatel monitorování | Role Přispěvatel monitorování umožňuje načíst všechna data monitorování a aktualizovat nastavení monitorování.|
| Čtenář monitorování | Role čtenáře monitorování umožňuje načíst všechna data monitorování. |
| Správce přístupu uživatelů |Role správce přístupu uživatelů umožňuje spravovat přístup uživatelů k účtům Azure Automation. |

## <a name="role-permissions"></a>Oprávnění role

Následující tabulky popisují konkrétní oprávnění na každou roli. To může zahrnovat akce, které chcete udělit oprávnění, a NotActions, což je omezit.

### <a name="owner"></a>Vlastník

Vlastníka můžete spravovat všechno včetně přístupu. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|Akce|Popis|
|---|---|
|Microsoft.Automation/automationAccounts/|Vytvořit a spravovat prostředky všechny typy.|

### <a name="contributor"></a>Přispěvatel

Přispěvatel můžou spravovat všechno kromě přístupu. V následující tabulce jsou uvedena oprávnění udělená a odepřen pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Vytvořit a spravovat prostředky všech typů|
|**Není akce.**||
|Microsoft.Authorization/*/Delete| Odstraňte role a přiřazení rolí.       |
|Microsoft.Authorization/*/Write     |  Vytvoření role a přiřazení rolí.       |
|Microsoft.Authorization/elevateAccess/Action    | Odmítne schopnost vytvářet správce uživatelského přístupu.       |

### <a name="reader"></a>Čtenář

Čtečka čipových karet můžete zobrazit všechny prostředky na účtu Automation, ale nelze provést žádné změny.

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Zobrazte všechny prostředky na účtu Automation. |

### <a name="automation-operator"></a>Operátor služby Automation

Operátor automatizace je schopná vytvořit a spravovat úlohy a číst runbook názvy a vlastnosti pro všechny sady runbook na účtu Automation.  Poznámka: Pokud chcete řídit přístup operátor pro jednotlivé sady runbook pak není nastavena tato role a role "Operátor automatizace úlohy" a "operátor automatizace sady Runbook, místo toho použít v kombinaci. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Přečtěte si autorizace.|
|Microsoft.Automation/automationAccounts/jobs/read|Seznam úlohy sady runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Obnovte úlohu, která je pozastavena.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Zrušte probíhající úlohu.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Číst datové proudy úlohy a výstup.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pozastavení úlohy v průběhu.|
|Microsoft.Automation/automationAccounts/jobs/write|Vytvořte úlohy.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Přečtěte si role a přiřazení rolí.         |
|Microsoft.Resources/deployments/*      |Vytvořit a spravovat nasazení skupiny prostředků.         |
|Microsoft.Insights/alertRules/*      | Vytvořit a spravovat pravidla výstrah.        |
|Microsoft.Support/* |Vytvořit a spravovat lístky žádostí o podporu.|

### <a name="automation-job-operator"></a>Operátor úloh Automation

Roli operátor automatizace úloh je poskytnuta v rozsahu účtu Automation. To umožňuje operátor oprávnění k vytváření a správě úloh pro všechny runbooky v účtu. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Přečtěte si autorizace.|
|Microsoft.Automation/automationAccounts/jobs/read|Seznam úlohy sady runbook.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Obnovte úlohu, která je pozastavena.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Zrušte probíhající úlohu.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Číst datové proudy úlohy a výstup.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pozastavení úlohy v průběhu.|
|Microsoft.Automation/automationAccounts/jobs/write|Vytvořte úlohy.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Přečtěte si role a přiřazení rolí.       |
|Microsoft.Resources/deployments/*      |Vytvořit a spravovat nasazení skupiny prostředků.         |
|Microsoft.Insights/alertRules/*      | Vytvořit a spravovat pravidla výstrah.        |
|Microsoft.Support/* |Vytvořit a spravovat lístky žádostí o podporu.|

### <a name="automation-runbook-operator"></a>Operátor runbooků Automation

Roli operátor automatizace sady Runbook je poskytnuta v oboru sady Runbook. Název sady runbook a vlastnosti, můžete zobrazit operátor automatizace sady Runbook.  Tato role v kombinaci s role "operátor automatizace úloh, umožňuje operátorovi také vytvářet a spravovat úlohy pro runbook. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Zobrazí seznam sady runbook.        |
|Microsoft.Authorization/*/read      | Přečtěte si autorizace.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Přečtěte si role a přiřazení rolí.         |
|Microsoft.Resources/deployments/*      | Vytvořit a spravovat nasazení skupiny prostředků.         |
|Microsoft.Insights/alertRules/*      | Vytvořit a spravovat pravidla výstrah.        |
|Microsoft.Support/*      | Vytvořit a spravovat lístky žádostí o podporu.        |

### <a name="log-analytics-contributor"></a>Přispěvatel Log Analytics

Přispěvatel Log Analytics můžete načíst všechna data monitorování a upravit nastavení monitorování. Úprava nastavení monitorování, zahrnuje přidání rozšíření virtuálního počítače pro virtuální počítače; čtení klíče účtu úložiště, abyste mohli nakonfigurovat shromažďování protokolů ze služby Azure Storage; vytváření a konfiguraci účtů Automation; Přidání řešení; a konfigurace Azure diagnostics pro všechny prostředky Azure. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|* / Číst|Přečtěte si prostředky všech typů, s výjimkou tajných klíčů.|
|Microsoft.Automation/automationAccounts/*|Spravovat účty automation.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Vytvořit a spravovat rozšíření virtuálního počítače.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Vypsat klíče účtu úložiště classic.|
|Microsoft.Compute/virtualMachines/extensions/*|Vytvořit a spravovat rozšíření classic virtuálního počítače.|
|Microsoft.Insights/alertRules/*|Pravidla výstrah pro čtení, zápisu a odstranění.|
|Microsoft.Insights/diagnosticSettings/*|Nastavení diagnostiky pro čtení, zápisu a odstranění.|
|Microsoft.OperationalInsights/*|Spravujte analýzy protokolů.|
|Microsoft.OperationsManagement/*|Spravujte řešení v pracovních prostorů.|
|Microsoft.Resources/deployments/*|Vytvořit a spravovat nasazení skupiny prostředků.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Vytvořit a spravovat nasazení skupiny prostředků.|
|Microsoft.Storage/storageAccounts/listKeys/action|Vypsat klíče účtu úložiště.|
|Microsoft.Support/*|Vytvořit a spravovat lístky žádostí o podporu.|

### <a name="log-analytics-reader"></a>Čtenář Log Analytics

Log Analytics Reader můžete zobrazit a vyhledávat všechna data monitorování a také a zobrazení monitorování nastavení, včetně zobrazení konfigurace Azure diagnostics na všechny prostředky Azure. V následující tabulce jsou uvedena oprávnění přidělil nebo odepřel pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|* / Číst|Přečtěte si prostředky všech typů, s výjimkou tajných klíčů.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Správa dotazů v analýzy protokolů.|
|Microsoft.OperationalInsights/workspaces/search/action|Vyhledávání dat analýzy protokolů.|
|Microsoft.Support/*|Vytvořit a spravovat lístky žádostí o podporu.|
|**Není akce.**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Nepodařilo se přečíst sdílené přístupové klíče.|

### <a name="monitoring-contributor"></a>Přispěvatel monitorování

Přispěvatel monitorování můžete načíst všechna data monitorování a aktualizovat nastavení monitorování. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|* / Číst|Přečtěte si prostředky všech typů, s výjimkou tajných klíčů.|
|Microsoft.AlertsManagement/alerts/*|Správa výstrah.|
|Microsoft.AlertsManagement/alertsSummary/*|Správa výstrah řídicího panelu.|
|Microsoft.Insights/AlertRules/*|Spravovat pravidla výstrah.|
|Microsoft.Insights/components/*|Spravujte součásti Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Spravovat nastavení pro diagnostiku.|
|Microsoft.Insights/eventtypes/*|Zobrazí seznam aktivity protokolu události (události management) v předplatném. Toto oprávnění se vztahuje na portálu i programový přístup k protokolu aktivit.|
|Microsoft.Insights/LogDefinitions/*|Tato oprávnění jsou nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivity prostřednictvím portálu. Seznam kategorií protokolu v protokolu aktivit.|
|Microsoft.Insights/MetricDefinitions/*|Číst definice metrik (seznamu dostupných typů metriky pro prostředek).|
|Microsoft.Insights/Metrics/*|Číst metriky pro prostředek.|
|Microsoft.Insights/Register/Action|Zaregistrujte zprostředkovatele Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Spravujte testy webu Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Správa sad řešení analýzy protokolů.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Spravujte analýzy protokolů uložená hledání.|
|Microsoft.OperationalInsights/workspaces/search/action|Hledání pracovních prostorů analýzy protokolů.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Seznam klíčů pro pracovní prostor analýzy protokolů.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Správa konfigurací přehledy úložiště analýzy protokolů.|
|Microsoft.Support/*|Vytvořit a spravovat lístky žádostí o podporu.|
|Microsoft.WorkloadMonitor/workloads/*|Spravujte úlohy.|

### <a name="monitoring-reader"></a>Čtenář monitorování

Čtečka monitorování může číst veškerá data monitorování. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|* / Číst|Přečtěte si prostředky všech typů, s výjimkou tajných klíčů.|
|Microsoft.OperationalInsights/workspaces/search/action|Hledání pracovních prostorů analýzy protokolů.|
|Microsoft.Support/*|Vytvářet a spravovat lístky žádostí o podporu|

### <a name="user-access-administrator"></a>Správce přístupu uživatelů

Správce uživatelského přístupu můžete spravovat přístup uživatelů k prostředkům Azure. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|* / Číst|Číst všechny prostředky|
|Microsoft.Authorization/*|Spravovat autorizace|
|Microsoft.Support/*|Vytvářet a spravovat lístky žádostí o podporu|

## <a name="onboarding"></a>Onboarding

V následujících tabulkách minimální požadovaná oprávnění potřebná pro registrace virtuálních počítačů pro sledování změn umožňuje zobrazit nebo aktualizovat řešení pro správu.

### <a name="onboarding-from-a-virtual-machine"></a>Registrace z virtuálního počítače

|**Akce**  |**Oprávnění**  |**Minimální rozsah**  |
|---------|---------|---------|
|Zápis nové nasazení      | Microsoft.Resources/deployments/*          |Předplatné          |
|Zápis novou skupinu prostředků      | Microsoft.Resources/subscriptions/resourceGroups/write        | Předplatné          |
|Vytvořit nový výchozí pracovní prostor      | Microsoft.OperationalInsights/workspaces/write         | Skupina prostředků         |
|Vytvořit nový účet      |  Microsoft.Automation/automationAccounts/write        |Skupina prostředků         |
|Pracovní prostor odkaz a účet      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Pracovní prostor</br>Účet Automation
|Vytvořit řešení      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Skupina prostředků          |
|Vytváření rozšíření MMA      | Microsoft.Compute/virtualMachines/write         | Virtuální počítač         |
|Vytvoření uloženého hledání      | Microsoft.OperationalInsights/workspaces/write          | Pracovní prostor         |
|Vytvoření konfigurace oboru      | Microsoft.OperationalInsights/workspaces/write          | Pracovní prostor         |
|Řešení odkaz na obor konfigurace      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Řešení         |
|Zkontrolujte stav registrace - prostoru pro čtení      | Microsoft.OperationalInsights/workspaces/read         | Pracovní prostor         |
|Kontrola stavu registrace - čtení propojená vlastnost prostoru účtu     | Microsoft.Automation/automationAccounts/read      | Účet Automation        |
|Zkontrolujte stav registrace - řešení pro čtení      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Řešení         |
|Kontrola stavu registrace - virtuálních počítačů pro čtení      | Microsoft.Compute/virtualMachines/read         | Virtuální počítač         |
|Zkontrolujte stav registrace – účet pro čtení      | Microsoft.Automation/automationAccounts/read  |  Účet Automation   |

### <a name="onboarding-from-automation-account"></a>Registrace z účtu Automation

|**Akce**  |**Oprávnění** |**Minimální rozsah**  |
|---------|---------|---------|
|Vytvořit nové nasazení     | Microsoft.Resources/deployments/*        | Předplatné         |
|Vytvořte novou skupinu prostředků     | Microsoft.Resources/subscriptions/resourceGroups/write         | Předplatné        |
|Okno AutomationOnboarding - vytvořit nový pracovní prostor     |Microsoft.OperationalInsights/workspaces/write           | Skupina prostředků        |
|Okno AutomationOnboarding - čtení propojené prostoru     | Microsoft.Automation/automationAccounts/read        | Účet Automation       |
|Okno AutomationOnboarding – přečtěte si řešení     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Řešení        |
|Okno AutomationOnboarding - čtení pracovního prostoru     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Pracovní prostor        |
|Vytvoření odkazu na pracovní prostor a účet     | Microsoft.OperationalInsights/workspaces/write        | Pracovní prostor        |
|Účet pro materiálů uložených zápisu      | Microsoft.Automation/automationAccounts/write        | Účet        |
|Vytvořit řešení      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Skupina prostředků         |
|Vytvoření nebo úpravě uložené hledání     | Microsoft.OperationalInsights/workspaces/write        | Pracovní prostor        |
|Vytvoření nebo úpravě konfigurace oboru     | Microsoft.OperationalInsights/workspaces/write        | Pracovní prostor        |
|Řešení odkaz na obor konfigurace      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Řešení         |
|**Krok 2 – zaváděním víc virtuálních počítačů**     |         |         |
|Okno VMOnboarding - vytvořit MMA rozšíření     | Microsoft.Compute/virtualMachines/write           | Virtuální počítač        |
|Vytvoření a úpravy uloženého hledání     | Microsoft.OperationalInsights/workspaces/write           | Pracovní prostor        |
|Vytvoření a úpravy konfigurace oboru  | Microsoft.OperationalInsights/workspaces/write   | Pracovní prostor|

## <a name="update-management"></a>Správa aktualizací

Správa aktualizací dosáhne napříč více službami a zajistit tak jeho službě. V následující tabulce jsou uvedeny oprávnění potřebná ke správě nasazení správy aktualizací:

|**Prostředek**  |**Role**  |**Rozsah**  |
|---------|---------|---------|
|Účet Automation     | Přispěvatel Log Analytics       | Účet Automation        |
|Účet Automation    | Přispěvatel virtuálních počítačů        | Skupinu prostředků pro účet        |
|Pracovní prostor Log Analytics     | Přispěvatel Log Analytics| Pracovní prostor Log Analytics        |
|Pracovní prostor Log Analytics |Čtenář Log Analytics| Předplatné|
|Řešení     |Přispěvatel Log Analytics         | Řešení|
|Virtuální počítač     | Přispěvatel virtuálních počítačů        | Virtuální počítač        |

## <a name="configure-rbac-for-your-automation-account"></a>Konfigurace RBAC pro váš účet Automation.

Následující části se dozvíte, jak nakonfigurovat RBAC na účtu Automation pomocí [portál](#configure-rbac-using-the-azure-portal) a [prostředí PowerShell](#configure-rbac-using-powershell)

### <a name="configure-rbac-using-the-azure-portal"></a>Konfigurace RBAC pomocí portálu Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a na stránce Účty Automation otevřete svůj účet Automation.
2. Klikněte na **přístup k ovládacímu prvku (IAM)** ovládacího prvku v levém horním rohu. Tím se otevře **přístup k ovládacímu prvku (IAM)** stránky, kde můžete přidat nové uživatele, skupiny, a aplikace pro správu vaší automatizace účtu a zobrazovat existující role, které mohou být konfigurovány pro účet Automation.

   ![Tlačítko Přístup](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Přidání nového uživatele a přiřazení role

1. Z **přístup k ovládacímu prvku (IAM)** klikněte na tlačítko **+ přidat** otevřete **přidat oprávnění** stránky, kde můžete přidat uživatele, skupiny nebo aplikace a k nim přiřadíte roli.

2. V seznamu dostupných rolí vyberte jednu roli. Můžete vybrat všechny dostupné předdefinované role, které účet Automation podporuje, nebo jakoukoli vlastní roli, kterou jste definovali.

3. Zadejte uživatelské jméno uživatele, které chcete udělit oprávnění v **vyberte** pole. Vyberte uživatele ze seznamu a klikněte na tlačítko **Uložit**.

   ![Přidání uživatelů](media/automation-role-based-access-control/automation-04-add-users.png)

   Teď byste měli vidět přidaný uživatel **uživatelé** stránka s vybrané role přiřazené

   ![Vypsání uživatelů](media/automation-role-based-access-control/automation-05-list-users.png)

   Roli můžete uživateli přiřadit také na stránce **Role**.
4. Klikněte na tlačítko **role** z **přístup k ovládacímu prvku (IAM)** otevřít stránku **role** stránky. Tady můžete zobrazit název role a počet uživatelů a skupin přiřazených k této roli.

    ![Přiřazení role na stránce Uživatelé](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Řízení přístupu na základě rolí lze nastavit pouze v rozsahu účtu Automation a ne u jakéhokoliv prostředku pod účtem Automation.

#### <a name="remove-a-user"></a>Odebrání uživatele

Je možné odebrat oprávnění přístupu pro uživatele nespravuje účet Automation nebo už kdo pro organizaci. Uživatele můžete odebrat následujícím postupem:

1. Z **přístup k ovládacímu prvku (IAM)** vyberte uživatele chcete odebrat a klikněte na tlačítko **odebrat**.
2. V podokně podrobností přiřazení klikněte na tlačítko **Odebrat**.
3. Kliknutím na **Ano** odebrání potvrďte.

   ![Odebrání uživatelů](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Konfigurace RBAC pomocí prostředí PowerShell

Je také možné nakonfigurovat na základě rolí přístup k účtu Automation pomocí následujících [rutin prostředí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

[Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) vypíše všechny role RBAC, které jsou k dispozici ve službě Azure Active Directory. Tento příkaz můžete použít spolu s vlastností **Název** k vypsání všech akcí, které může konkrétní role provádět.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Následuje příklad výstupu:

```azurepowershell
Name             : Automation Operator
Id               : d3881f73-407a-4167-8283-e981cbba0404
IsCustom         : False
Description      : Automation Operators are able to start, stop, suspend, and resume jobs
Actions          : {Microsoft.Authorization/*/read, Microsoft.Automation/automationAccounts/jobs/read, Microsoft.Automation/automationAccounts/jobs/resume/action,
                   Microsoft.Automation/automationAccounts/jobs/stop/action...}
NotActions       : {}
AssignableScopes : {/}
```

[Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) uvádí přiřazení rolí pro Azure AD RBAC v zadaném oboru. Pokud nezadáte žádné parametry, tento příkaz vrátí všechna přiřazení rolí v rámci předplatného. K vypsání přiřazení přístupu konkrétního uživatele nebo skupin, kterých je uživatel členem, použijte parametr **ExpandPrincipalGroups**.
    **Příklad:** K vypsání všech uživatelů a jejich rolí v rámci účtu Automation použijte následující příkaz.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Následuje příklad výstupu:

```powershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/cc594d39-ac10-46c4-9505-f182a355c41f
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourceGroups/myResourceGroup/providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : 15f26a47-812d-489a-8197-3d4853558347
ObjectType         : User
```

[Nový-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) přiřazení přístupu pro uživatele, skupiny a aplikace na konkrétní rozsah.
    **Příklad:** přiřadit role "Operátor automatizace" pro uživatele v rozsahu účtu Automation použijte následující příkaz.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Následuje příklad výstupu:

```azurepowershell
RoleAssignmentId   : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount/provid
                     ers/Microsoft.Authorization/roleAssignments/25377770-561e-4496-8b4f-7cba1d6fa346
Scope              : /subscriptions/00000000-0000-0000-0000-000000000000/resourcegroups/myResourceGroup/Providers/Microsoft.Automation/automationAccounts/myAutomationAccount
DisplayName        : admin@contoso.com
SignInName         : admin@contoso.com
RoleDefinitionName : Automation Operator
RoleDefinitionId   : d3881f73-407a-4167-8283-e981cbba0404
ObjectId           : f5ecbe87-1181-43d2-88d5-a8f5e9d8014e
ObjectType         : User
```

Použití [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) k odebrání přístupu zadaného uživatele, skupiny nebo aplikace z konkrétní oboru.
    **Příklad:** odebrat uživatele z role "Operátor automatizace" v rozsahu účtu Automation použijte následující příkaz.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

V předchozích příkladech nahraďte **přihlašovací Id**, **Id předplatného**, **název skupiny prostředků**, a **název účtu Automation** s vaší Podrobnosti o účtu. Po zobrazení výzvy, která požádá o potvrzení odebrání přiřazené role uživatele, zvolte **Ano**.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Uživatelské prostředí pro role operátora služby Automation - účet služby Automation

Když se uživatel, kdo je přiřazen k roli operátor automatizace v rozsahu účtu Automation zobrazení účtu služby Automation jsou přiřazeny, mohou pouze prohlížet seznam runbooků, úlohy sady runbook a plány vytvořené v automatizace účet, ale nemůžou si prohlížet jejich definice. Může spustit, zastavit, pozastavit, obnovit nebo naplánovat úlohu runbooku. Uživatel nemá přístup k dalším prostředkům Automation, jako je například konfigurace, skupinám hybrid worker nebo uzlům DSC.

![Žádný přístup k prostředkům](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Konfigurace RBAC pro sady Runbook

Automatizace Azure umožňuje přiřadit RBAC pro konkrétní sady runbook. Provedete to spuštěním následujícího skriptu k přidání uživatele do konkrétní sady runbook. Následující skript můžete spustili automatizace účtu správce nebo správce klienta.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation Account
$automationAccountName ="<Automation Account Name>" # Name of the Automation Account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation Account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be ran once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Jednou spustili, mít uživatele, přihlaste se k portálu Azure a zobrazení **všechny prostředky**. V seznamu uvidí sady Runbook, které byly přidány jako **operátor automatizace sady Runbook** pro.

![RBAC Runbook na portálu](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Uživatelské prostředí pro role operátora služby Automation - Runbook

Když se uživatel, který je přiřazený k roli operátor automatizace v oboru zobrazení sady Runbook sady Runbook jsou přiřazeny, mohou pouze spouštět sadu runbook a zobrazit úlohy sady runbook.

![Má přístup pouze k spuštění](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Další postup

* Další informace o různých způsobech konfigurace RBAC pro Azure Automation najdete v článku [Správa řízení přístupu na základě role pomocí Azure PowerShellu](../role-based-access-control/role-assignments-powershell.md).
* Podrobnosti o různých způsobech spouštění runbooků najdete v článku [Spuštění runbooku](automation-starting-a-runbook.md)
* Další informace o různých typech runbooků najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).
