---
title: Řízení přístupu na základě role ve službě Azure Automation
description: Řízení přístupu na základě role (RBAC) umožňuje správu přístupu k prostředkům Azure. Tento článek popisuje způsob nastavení řízení přístupu na základě role ve službě Azure Automation.
keywords: rbac v automation, řízení přístupu na základě rolí, rbac v azure
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: georgewallace
ms.author: gwallace
ms.date: 05/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 4fb90dbdb02fc0a0448b8cb6723c980c0fe41bd6
ms.sourcegitcommit: 9999fe6e2400cf734f79e2edd6f96a8adf118d92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/22/2019
ms.locfileid: "54424315"
---
# <a name="role-based-access-control-in-azure-automation"></a>Řízení přístupu na základě role ve službě Azure Automation

Řízení přístupu na základě role (RBAC) umožňuje správu přístupu k prostředkům Azure. Pomocí [RBAC](../role-based-access-control/overview.md), můžete v rámci týmu oddělit a udělit pouze takovou úroveň přístupu uživatele, skupiny a aplikace, které potřebují k provádění svých úloh. Přístup na základě role můžete uživatelům udělit pomocí webu Azure Portal, nástrojů příkazového řádku Azure nebo rozhraní API pro správu Azure.

## <a name="roles-in-automation-accounts"></a>Role v účtech Automation

Ve službě Azure Automation se přístup uděluje přiřazením příslušné role RBAC uživatelům, skupinám a aplikacím v rozsahu účtu Automation. Níže jsou uvedené vestavěné role, které účet Automation podporuje:

| **Role** | **Popis** |
|:--- |:--- |
| Vlastník |Role vlastníka umožňuje přístup ke všem prostředkům a akcím v rámci účtu Automation, včetně poskytnutí přístupu pro ostatní uživatele, skupiny a aplikacím za účelem správy účtu Automation. |
| Přispěvatel |Role přispěvatele umožňuje spravovat všechno kromě úpravy oprávnění jiných uživatelů k přístupu k účtu Automation. |
| Čtenář |Role čtenáře vám umožní zobrazit všechny prostředky na účtu Automation, ale neumožní vám provádět změny. |
| Operátor služby Automation |Role operátora služby Automation umožňuje zobrazit název sady runbook a vlastnosti a vytváření a Správa úloh pro všechny sady runbook v účtu Automation. Tato role je užitečná, pokud chcete chránit prostředky na účtu Automation, například assety přihlašovacích údajů a runbooky, aby je nikdo nemohl zobrazit nebo upravit, ale aby členové vaší organizace mohli tyto runbooky stále spouštět. |
|Operátor úloh Automation|Role operátor úloh Automation umožňuje vytváření a Správa úloh pro všechny sady runbook v účtu Automation.|
|Operátor runbooků Automation|Role operátora služby Automation Runbook umožňuje uživateli zobrazit název a vlastnosti sady runbook.|
| Přispěvatel Log Analytics | Role Přispěvatel Log Analytics umožňuje číst všechna data monitorování a upravit nastavení monitorování. Úprava nastavení monitorování patří přidávání rozšíření do virtuálních počítačů, čtení klíčů účtů úložiště, abyste mohli konfigurovat shromažďování protokolů ze služby Azure storage, vytváření a konfigurace účtů služby Automation, přidávání řešení a konfigurace diagnostik Azure na všechny prostředky Azure.|
| Čtenář Log Analytics | Role Čtenář Log Analytics umožňuje zobrazit a prohledat všechna sledování a také zobrazení nastavení monitorování. To zahrnuje zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure. |
| Přispěvatel monitorování | Role Přispěvatel monitorování umožňuje číst všechny monitorování dat a aktualizovat nastavení monitorování.|
| Čtenář monitorování | Role Čtenář monitorování umožňuje číst všechna data monitorování. |
| Správce přístupu uživatelů |Role správce přístupu uživatelů umožňuje spravovat přístup uživatelů k účtům Azure Automation. |

## <a name="role-permissions"></a>Oprávnění role

Následující tabulky popisují konkrétní oprávnění udělená pro jednotlivé role. To může zahrnovat akce, které poskytují oprávnění a NotActions, což je omezit.

### <a name="owner"></a>Vlastník

Vlastník můžou spravovat všechno včetně přístupu. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|Akce|Popis|
|---|---|
|Microsoft.Automation/automationAccounts/|Vytváření a správě prostředků všech typů.|

### <a name="contributor"></a>Přispěvatel

Přispěvatelé můžou spravovat všechno kromě přístupu. V následující tabulce jsou uvedeny oprávnění udělena a odepřen pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Vytváření a správě prostředků všech typů.|
|**Ne akce**||
|Microsoft.Authorization/*/Delete| Odstraňte role a přiřazení rolí.       |
|Microsoft.Authorization/*/Write     |  Vytvoření role a přiřazení rolí.       |
|Microsoft.Authorization/elevateAccess/Action    | Odepřít možnost vytvořit správce uživatelských přístupů.       |

### <a name="reader"></a>Čtenář

Čtečku můžete zobrazit všechny prostředky v účtu Automation, ale nemůže provádět změny.

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Zobrazte všechny prostředky v účtu Automation. |

### <a name="automation-operator"></a>Operátor služby Automation

Operátor služby Automation je možné vytvářet a spravovat úlohy a číst runbook názvy a vlastnosti pro všechny sady runbook v účtu Automation.  Poznámka: Pokud chcete řídit přístup operátor pro jednotlivé sady runbook pak není nastavena tato role a role operátor úloh Automation a operátor Runbooků Automation místo toho použít v kombinaci. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Přečtěte si autorizace.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/read|Číst prostředky Hybrid Runbook Worker.|
|Microsoft.Automation/automationAccounts/jobs/read|Vypisovat úlohy runbooku.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Obnovit úlohu, která je pozastavená.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Zrušte probíhající úlohu.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Číst datové proudy úlohy a výstup.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Získáte výstup úlohy.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pozastavte úlohu probíhá.|
|Microsoft.Automation/automationAccounts/jobs/write|Vytváření úloh.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Získáte plán úloh Azure Automation.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Vytvořte plán úloh Azure Automation.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Získáte pracovní prostor propojený s účtem automation.|
|Microsoft.Automation/automationAccounts/read|Získáte účet služby Azure Automation.|
|Microsoft.Automation/automationAccounts/runbooks/read|Získání runbooku Azure Automation.|
|Microsoft.Automation/automationAccounts/schedules/read|Získáte prostředek plánování Azure Automation.|
|Microsoft.Automation/automationAccounts/schedules/write|Vytvořit nebo aktualizovat prostředek plánování Azure Automation.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Další role a přiřazení rolí.         |
|Microsoft.Resources/deployments/*      |Vytvářejte a spravujte nasazení skupiny prostředků.         |
|Microsoft.Insights/alertRules/*      | Vytvářet a spravovat pravidla výstrah.        |
|Microsoft.Support/* |Vytvořit a spravovat lístky podpory.|

### <a name="automation-job-operator"></a>Operátor úloh Automation

Role operátor úloh Automation je poskytnuta v rozsahu účtu Automation. Díky tomu oprávnění operátora pro vytváření a Správa úloh pro všechny sady runbook v rámci účtu. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Přečtěte si autorizace.|
|Microsoft.Automation/automationAccounts/jobs/read|Vypisovat úlohy runbooku.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Obnovit úlohu, která je pozastavená.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Zrušte probíhající úlohu.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Číst datové proudy úlohy a výstup.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pozastavte úlohu probíhá.|
|Microsoft.Automation/automationAccounts/jobs/write|Vytváření úloh.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Další role a přiřazení rolí.       |
|Microsoft.Resources/deployments/*      |Vytvářejte a spravujte nasazení skupiny prostředků.         |
|Microsoft.Insights/alertRules/*      | Vytvářet a spravovat pravidla výstrah.        |
|Microsoft.Support/* |Vytvořit a spravovat lístky podpory.|

### <a name="automation-runbook-operator"></a>Operátor runbooků Automation

Role operátora služby Automation Runbook jsou udělena v oboru sady Runbook. Operátor automatizace sady Runbook můžete zobrazit název a vlastnosti sady runbook.  Tato role v kombinaci s role operátor úloh Automation umožňuje operátor můžete také vytvářet a spravovat úlohy runbooku. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Seznam runbooků.        |
|Microsoft.Authorization/*/read      | Přečtěte si autorizace.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Další role a přiřazení rolí.         |
|Microsoft.Resources/deployments/*      | Vytvářejte a spravujte nasazení skupiny prostředků.         |
|Microsoft.Insights/alertRules/*      | Vytvářet a spravovat pravidla výstrah.        |
|Microsoft.Support/*      | Vytvořit a spravovat lístky podpory.        |

### <a name="log-analytics-contributor"></a>Přispěvatel Log Analytics

Přispěvatel Log Analytics může číst všechna data monitorování a upravit nastavení monitorování. Úprava nastavení monitorování, zahrnuje přidání rozšíření virtuálního počítače na virtuální počítače; čtení klíčů účtů úložiště, abyste mohli konfigurovat shromažďování protokolů ze služby Azure Storage; Vytvoření a konfigurace účtů služby Automation; přidávání řešení a konfigurace diagnostik Azure na všech prostředcích Azure. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|* / čtení|Číst prostředky všech typů, s výjimkou tajných kódů.|
|Microsoft.Automation/automationAccounts/*|Správa účtů služby automation.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Vytvoření a správa rozšíření virtuálních počítačů.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Vypsat klíče účtu klasického úložiště.|
|Microsoft.Compute/virtualMachines/extensions/*|Vytvářejte a spravujte rozšíření klasický virtuální počítač.|
|Microsoft.Insights/alertRules/*|Čtení, zápisu a odstranění pravidla upozornění.|
|Microsoft.Insights/diagnosticSettings/*|Nastavení diagnostiky pro čtení/zápis/delete.|
|Microsoft.OperationalInsights/*|Správa služby Log Analytics.|
|Microsoft.OperationsManagement/*|Spravujte řešení v pracovních prostorech.|
|Microsoft.Resources/deployments/*|Vytvářejte a spravujte nasazení skupiny prostředků.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Vytvářejte a spravujte nasazení skupiny prostředků.|
|Microsoft.Storage/storageAccounts/listKeys/action|Vypsat klíče účtu úložiště.|
|Microsoft.Support/*|Vytvořit a spravovat lístky podpory.|

### <a name="log-analytics-reader"></a>Čtenář Log Analytics

Čtenář Log Analytics můžete zobrazit a vyhledat všechna data monitorování a zobrazení nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure. V následující tabulce jsou uvedeny oprávnění povolen nebo odepřen pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|* / čtení|Číst prostředky všech typů, s výjimkou tajných kódů.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Spravujte dotazy v Log Analytics.|
|Microsoft.OperationalInsights/workspaces/search/action|Vyhledávání dat Log Analytics.|
|Microsoft.Support/*|Vytvořit a spravovat lístky podpory.|
|**Ne akce**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Nebylo možné získat klíče pro sdílený přístup.|

### <a name="monitoring-contributor"></a>Přispěvatel monitorování

Přispěvatel monitorování může číst všechna data monitorování a aktualizovat nastavení monitorování. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|* / čtení|Číst prostředky všech typů, s výjimkou tajných kódů.|
|Microsoft.AlertsManagement/alerts/*|Správa výstrah.|
|Microsoft.AlertsManagement/alertsSummary/*|Správa výstrah řídicí panel.|
|Microsoft.Insights/AlertRules/*|Spravujte pravidla výstrah.|
|Microsoft.Insights/components/*|Spravujte součásti Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Spravujte nastavení diagnostiky.|
|Microsoft.Insights/eventtypes/*|Vypsat události protokolu aktivit (události správy) v rámci předplatného. Toto oprávnění se vztahuje na programování a portálu přístup k protokolům aktivit.|
|Microsoft.Insights/LogDefinitions/*|Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit na portálu. Seznam kategorií protokolu v protokolu aktivit.|
|Microsoft.Insights/MetricDefinitions/*|Přečíst definice metrik (seznam dostupných typů metriky pro prostředek).|
|Microsoft.Insights/Metrics/*|Číst metriky pro prostředek.|
|Microsoft.Insights/Register/Action|Zaregistrujte zprostředkovatele Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Správa webových testů Application Insights.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Správa sad řešení Log Analytics.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Správa uložené výsledky hledání Log Analytics.|
|Microsoft.OperationalInsights/workspaces/search/action|Hledejte pracovní prostory Log Analytics.|
|Microsoft.OperationalInsights/workspaces/sharedKeys/action|Vypsat klíče pracovního prostoru Log Analytics.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Správa konfigurace přehledu úložiště Log Analytics.|
|Microsoft.Support/*|Vytvořit a spravovat lístky podpory.|
|Microsoft.WorkloadMonitor/workloads/*|Správa úloh.|

### <a name="monitoring-reader"></a>Čtenář monitorování

Čtenář monitorování může číst všechna data monitorování. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|* / čtení|Číst prostředky všech typů, s výjimkou tajných kódů.|
|Microsoft.OperationalInsights/workspaces/search/action|Hledejte pracovní prostory Log Analytics.|
|Microsoft.Support/*|Vytvořit a spravovat lístky podpory|

### <a name="user-access-administrator"></a>Správce přístupu uživatelů

Správce uživatelských přístupů můžete spravovat přístup uživatelů k prostředkům Azure. V následující tabulce jsou uvedeny oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|* / čtení|Číst všechny prostředky|
|Microsoft.Authorization/*|Správa autorizací|
|Microsoft.Support/*|Vytvořit a spravovat lístky podpory|

## <a name="onboarding"></a>Onboarding

V následujících tabulkách minimální požadované oprávnění potřebná pro připojení virtuálních počítačů pro sledování změn umožňuje zobrazit nebo aktualizovat řešení pro správu.

### <a name="onboarding-from-a-virtual-machine"></a>Připojení z virtuálního počítače

|**Akce**  |**Oprávnění**  |**Minimální rozsah**  |
|---------|---------|---------|
|Zápis nové nasazení      | Microsoft.Resources/deployments/*          |Předplatné          |
|Zapsat novou skupinu prostředků      | Microsoft.Resources/subscriptions/resourceGroups/write        | Předplatné          |
|Vytvořit nový výchozí pracovní prostor      | Microsoft.OperationalInsights/workspaces/write         | Skupina prostředků         |
|Vytvořit nový účet      |  Microsoft.Automation/automationAccounts/write        |Skupina prostředků         |
|Propojení pracovního prostoru a účtu      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft.Automation/automationAccounts/read|Pracovní prostor</br>Účet Automation
|Vytvořit řešení      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Skupina prostředků          |
|Jak vytvořit rozšíření agenta MMA      | Microsoft.Compute/virtualMachines/write         | Virtuální počítač         |
|Vytvořit uložené výsledky hledání      | Microsoft.OperationalInsights/workspaces/write          | Pracovní prostor         |
|Vytvořit konfiguraci oboru      | Microsoft.OperationalInsights/workspaces/write          | Pracovní prostor         |
|Řešení odkazu ke konfiguraci oboru      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Řešení         |
|Zkontrolovat stav registrace – pracovní prostor pro čtení      | Microsoft.OperationalInsights/workspaces/read         | Pracovní prostor         |
|Kontrola stavu registrace – pro čtení propojený pracovní prostor vlastnictví účtu     | Microsoft.Automation/automationAccounts/read      | Účet Automation        |
|Zkontrolovat stav registrace – řešení pro čtení      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Řešení         |
|Kontrola stavu registrace – virtuální počítač pro čtení      | Microsoft.Compute/virtualMachines/read         | Virtuální počítač         |
|Zkontrolovat stav registrace – účet pro čtení      | Microsoft.Automation/automationAccounts/read  |  Účet Automation   |

### <a name="onboarding-from-automation-account"></a>Připojení z účtu služby Automation

|**Akce**  |**Oprávnění** |**Minimální rozsah**  |
|---------|---------|---------|
|Vytvořte nové nasazení     | Microsoft.Resources/deployments/*        | Předplatné         |
|Vytvořte novou skupinu prostředků     | Microsoft.Resources/subscriptions/resourceGroups/write         | Předplatné        |
|Okno AutomationOnboarding – vytvořit nový pracovní prostor     |Microsoft.OperationalInsights/workspaces/write           | Skupina prostředků        |
|Okno AutomationOnboarding – přečtěte si propojení pracovního prostoru     | Microsoft.Automation/automationAccounts/read        | Účet Automation       |
|Okno AutomationOnboarding – přečtěte si řešení     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Řešení        |
|Okno AutomationOnboarding – čtení pracovního prostoru     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Pracovní prostor        |
|Vytvoření odkazu na pracovní prostor a účet     | Microsoft.OperationalInsights/workspaces/write        | Pracovní prostor        |
|Zapsat účet pro materiálů uložených      | Microsoft.Automation/automationAccounts/write        | Účet        |
|Vytvořit řešení      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Skupina prostředků         |
|Vytvořit/upravit uložené hledání     | Microsoft.OperationalInsights/workspaces/write        | Pracovní prostor        |
|Vytvořit nebo upravit konfiguraci oboru     | Microsoft.OperationalInsights/workspaces/write        | Pracovní prostor        |
|Řešení odkazu ke konfiguraci oboru      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Řešení         |
|**Krok 2 – připojit více virtuálních počítačů**     |         |         |
|Okno VMOnboarding – rozšíření vytvořit agenta MMA     | Microsoft.Compute/virtualMachines/write           | Virtuální počítač        |
|Vytvořit / upravit uložené hledání     | Microsoft.OperationalInsights/workspaces/write           | Pracovní prostor        |
|Vytvořit / upravit konfiguraci oboru  | Microsoft.OperationalInsights/workspaces/write   | Pracovní prostor|

## <a name="update-management"></a>Správa aktualizací

Správa aktualizací dosáhne napříč několika službami a zajistit tak jeho služby. V následující tabulce jsou uvedeny oprávnění potřebná ke správě nasazení správy aktualizací:

|**Prostředek**  |**Role**  |**Rozsah**  |
|---------|---------|---------|
|Účet Automation     | Přispěvatel Log Analytics       | Účet Automation        |
|Účet Automation    | Přispěvatel virtuálních počítačů        | Skupina prostředků pro účet        |
|Pracovní prostor Log Analytics     | Přispěvatel Log Analytics| Pracovní prostor Log Analytics        |
|Pracovní prostor Log Analytics |Čtenář Log Analytics| Předplatné|
|Řešení     |Přispěvatel Log Analytics         | Řešení|
|Virtuální počítač     | Přispěvatel virtuálních počítačů        | Virtuální počítač        |

## <a name="configure-rbac-for-your-automation-account"></a>Konfigurace RBAC pro svůj účet Automation

Následující části se dozvíte, jak nakonfigurovat RBAC na účtu Automation pomocí [portál](#configure-rbac-using-the-azure-portal) a [prostředí PowerShell](#configure-rbac-using-powershell)

### <a name="configure-rbac-using-the-azure-portal"></a>Nakonfigurujte RBAC pomocí webu Azure portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a na stránce Účty Automation otevřete svůj účet Automation.
2. Klikněte na **řízení přístupu (IAM)** ovládací prvek v levém horním rohu. Tím se otevře **řízení přístupu (IAM)** stránce, kde můžete přidat nové uživatele, skupiny, a účet aplikace pro správu automatizace a zobrazovat existující role, které je možné nakonfigurovat pro účet služby Automation.
3. Klikněte na tlačítko **přiřazení rolí** kartu.

   ![Tlačítko Přístup](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Přidání nového uživatele a přiřazení role

1. Z **řízení přístupu (IAM)** klikněte na **+ přidat přiřazení role** otevřít **přidat přiřazení role** stránku, kde můžete přidat uživatele, skupinu nebo aplikaci a přiřazení role k nim.

2. V seznamu dostupných rolí vyberte jednu roli. Můžete vybrat některý z dostupných předdefinované role, které účet Automation podporuje nebo jakoukoli vlastní roli, kterou jste definovali.

3. Zadejte uživatelské jméno uživatele, kterému chcete udělit oprávnění k v **vyberte** pole. Vyberte uživatele ze seznamu a klikněte na tlačítko **Uložit**.

   ![Přidání uživatelů](media/automation-role-based-access-control/automation-04-add-users.png)

   Teď byste měli vidět uživatel přidaný do **uživatelé** stránka s vybranou roli přiřadit

   ![Vypsání uživatelů](media/automation-role-based-access-control/automation-05-list-users.png)

   Roli můžete uživateli přiřadit také na stránce **Role**.
4. Klikněte na tlačítko **role** z **řízení přístupu (IAM)** otevřít stránku **role** stránky. Tady můžete zobrazit název role a počet uživatelů a skupin přiřazených k této roli.

    ![Přiřazení role na stránce Uživatelé](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Řízení přístupu na základě rolí lze nastavit pouze v rozsahu účtu Automation a ne u jakéhokoliv prostředku pod účtem Automation.

#### <a name="remove-a-user"></a>Odebrání uživatele

Můžete odebrat přístupová oprávnění pro uživatele nespravuje účet Automation, nebo který již v organizaci nepracuje. Uživatele můžete odebrat následujícím postupem:

1. Z **řízení přístupu (IAM)** vyberte uživatele chcete odebrat a klikněte na tlačítko **odebrat**.
2. V podokně podrobností přiřazení klikněte na tlačítko **Odebrat**.
3. Kliknutím na **Ano** odebrání potvrďte.

   ![Odebrání uživatelů](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Nakonfigurujte RBAC pomocí Powershellu

Přístup na základě role se dají konfigurovat i na účtu Automation pomocí následujících [rutin prostředí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

[Get-AzureRmRoleDefinition](https://msdn.microsoft.com/library/mt603792.aspx) vypíše všechny role RBAC, které jsou k dispozici v Azure Active Directory. Tento příkaz můžete použít spolu s vlastností **Název** k vypsání všech akcí, které může konkrétní role provádět.

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

[Get-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt619413.aspx) uvádí přiřazení role Azure AD RBAC v zadaném oboru. Pokud nezadáte žádné parametry, tento příkaz vrátí všechna přiřazení rolí v rámci předplatného. K vypsání přiřazení přístupu konkrétního uživatele nebo skupin, kterých je uživatel členem, použijte parametr **ExpandPrincipalGroups**.
    **Příklad:** Seznam všech uživatelů a jejich rolí v rámci účtu automation použijte následující příkaz.

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

[Nový-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603580.aspx) přiřazení přístupu k uživatelům, skupinám a aplikacím na konkrétní rozsah.
    **Příklad:** Přiřazení role "Operátor služby Automation" pro uživatele v rozsahu účtu Automation použijte následující příkaz.

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

Použití [Remove-AzureRmRoleAssignment](https://msdn.microsoft.com/library/mt603781.aspx) k odebrání přístupu konkrétního uživatele, skupinu nebo aplikaci v určitém rozsahu.
    **Příklad:** Použijte následující příkaz k odebrání uživatele z role "Operátor služby Automation" v rozsahu účtu Automation.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

V předchozích příkladech nahraďte **přihlašovací Id**, **Id předplatného**, **název skupiny prostředků**, a **název účtu služby Automation** s vaší Podrobnosti o účtu. Po zobrazení výzvy, která požádá o potvrzení odebrání přiřazené role uživatele, zvolte **Ano**.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Činnost koncového uživatele pro roli operátora služby Automation – účet služby Automation

Když se uživatel, kdo je přiřazen k roli operátor služby Automation v rozsahu účtu Automation účtu Automation jsou přiřazeny k zobrazení, jsou pouze zobrazení seznamu sad runbook, úlohy runbooku, a plány vytvořené v automatizaci účtu, ale nemůže zobrazit jejich definice. Může spustit, zastavit, pozastavit, obnovit nebo naplánovat úlohu runbooku. Uživatel nemá přístup k dalším prostředkům Automation, jako je například konfigurace, skupinám hybrid worker nebo uzlům DSC.

![Přístup k prostředkům](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Konfigurace RBAC pro sady Runbook

Azure Automation umožňuje přiřazení RBAC pro konkrétní sady runbook. Provedete to spuštěním následujícího skriptu k přidání uživatele do konkrétní sadu runbook. Následující skript můžete spustit správce účtu služby Automation nebo správce Tenanta.

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

Jednou spustili, požádejte uživatele, přihlaste se k webu Azure portal a zobrazení **všechny prostředky**. V seznamu zobrazí byly přidány jako sadu Runbook **operátor Runbooků Automation** pro.

![RBAC Runbooku na portálu](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Činnost koncového uživatele pro roli operátora služby Automation – sady Runbook

Když se uživatel, který je přiřazený k roli operátor služby Automation v Runbooku nastavit obor zobrazení sady Runbook jsou přiřazeny, mohou pouze spouštět sadu runbook a zobrazit úlohy runbooku.

![Má přístup pouze k spuštění](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Další postup

* Další informace o různých způsobech konfigurace RBAC pro Azure Automation najdete v článku [Správa řízení přístupu na základě role pomocí Azure PowerShellu](../role-based-access-control/role-assignments-powershell.md).
* Podrobnosti o různých způsobech spouštění runbooků najdete v článku [Spuštění runbooku](automation-starting-a-runbook.md)
* Další informace o různých typech runbooků najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).

