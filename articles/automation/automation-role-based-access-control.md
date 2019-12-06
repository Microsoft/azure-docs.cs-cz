---
title: Řízení přístupu podle rolí ve službě Azure Automation
description: Řízení přístupu na základě role (RBAC) umožňuje správu přístupu k prostředkům Azure. Tento článek popisuje způsob nastavení řízení přístupu na základě role ve službě Azure Automation.
keywords: rbac v automation, řízení přístupu na základě rolí, rbac v azure
services: automation
ms.service: automation
ms.subservice: shared-capabilities
author: mgoedtel
ms.author: magoedte
ms.date: 05/17/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0ee524768f46de965b1755f2cfffdf9e2034bec8
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74850784"
---
# <a name="role-based-access-control-in-azure-automation"></a>Řízení přístupu podle rolí ve službě Azure Automation

Řízení přístupu na základě role (RBAC) umožňuje správu přístupu k prostředkům Azure. Pomocí [RBAC](../role-based-access-control/overview.md)můžete oddělit povinnosti v rámci svého týmu a udělit jenom přístup k uživatelům, skupinám a aplikacím, které potřebují k provádění svých úloh. Přístup na základě role můžete uživatelům udělit pomocí webu Azure Portal, nástrojů příkazového řádku Azure nebo rozhraní API pro správu Azure.

## <a name="roles-in-automation-accounts"></a>Role v účtech Automation

Ve službě Azure Automation se přístup uděluje přiřazením příslušné role RBAC uživatelům, skupinám a aplikacím v rozsahu účtu Automation. Níže jsou uvedené vestavěné role, které účet Automation podporuje:

| **Role** | **Popis** |
|:--- |:--- |
| Vlastník |Role vlastníka umožňuje přístup ke všem prostředkům a akcím v rámci účtu Automation, včetně poskytnutí přístupu dalším uživatelům, skupinám a aplikacím za účelem správy účtu Automation. |
| Přispěvatel |Role přispěvatele umožňuje spravovat všechno kromě úpravy oprávnění jiných uživatelů k přístupu k účtu Automation. |
| Čtenář |Role čtenáře vám umožní zobrazit všechny prostředky na účtu Automation, ale neumožní vám provádět změny. |
| Operátor služby Automation |Role operátora služby Automation umožňuje zobrazit název a vlastnosti Runbooku a vytvářet a spravovat úlohy pro všechny Runbooky v účtu Automation. Tato role je užitečná, pokud chcete chránit prostředky na účtu Automation, například assety přihlašovacích údajů a runbooky, aby je nikdo nemohl zobrazit nebo upravit, ale aby členové vaší organizace mohli tyto runbooky stále spouštět. |
|Operátor úloh automatizace|Role operátora úlohy Automation umožňuje vytvářet a spravovat úlohy pro všechny Runbooky v účtu Automation.|
|Operátor Runbooku služby Automation|Role operátora Runbooku Automation umožňuje zobrazit název a vlastnosti Runbooku.|
| Přispěvatel Log Analytics | Role Přispěvatel Log Analytics umožňuje číst všechna data monitorování a upravovat nastavení monitorování. Úprava nastavení monitorování zahrnuje přidání rozšíření virtuálního počítače do virtuálních počítačů, čtení klíčů účtu úložiště, aby bylo možné konfigurovat shromažďování protokolů z Azure Storage, vytváření a konfiguraci účtů služby Automation, přidávání řešení a konfigurace diagnostiky Azure na všechny prostředky Azure.|
| Čtenář Log Analytics | Role čtecího modulu Log Analytics umožňuje zobrazit a vyhledat všechna data monitorování a také zobrazit nastavení monitorování. To zahrnuje zobrazení konfigurace diagnostiky Azure pro všechny prostředky Azure. |
| Přispěvatel monitorování | Role Přispěvatel monitorování umožňuje číst všechna data monitorování a aktualizovat nastavení monitorování.|
| Čtečka monitorování | Role čtenář monitorování umožňuje číst všechna data monitorování. |
| Správce přístupu uživatelů |Role správce přístupu uživatelů umožňuje spravovat přístup uživatelů k účtům Azure Automation. |

## <a name="role-permissions"></a>Oprávnění role

V následujících tabulkách jsou popsána konkrétní oprávnění udělená jednotlivým rolím. To může zahrnovat akce, které poskytují oprávnění a NotActions, které je omezují.

### <a name="owner"></a>Vlastník

Vlastník může spravovat všechno, včetně přístupu. Následující tabulka uvádí oprávnění udělená pro roli:

|Akce|Popis|
|---|---|
|Microsoft. Automation/automationAccounts/|Vytváření a Správa prostředků všech typů.|

### <a name="contributor"></a>Přispěvatel

Přispěvatel může spravovat všechno kromě přístupu. Následující tabulka uvádí oprávnění udělená a Zamítnutá pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/|Vytváření a Správa prostředků všech typů|
|**Neakce**||
|Microsoft. Authorization/*/DELETE| Odstraňte role a přiřazení rolí.       |
|Microsoft.Authorization/*/Write     |  Vytvořte role a přiřazení rolí.       |
|Microsoft. Authorization/elevateAccess/Action    | Zakazuje možnost vytvořit správce přístupu uživatele.       |

### <a name="reader"></a>Čtenář

Čtenář může zobrazit všechny prostředky v účtu Automation, ale nemůže provádět žádné změny.

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/Read|Zobrazit všechny prostředky v účtu Automation. |

### <a name="automation-operator"></a>Operátor služby Automation

Operátor automatizace může vytvářet a spravovat úlohy a číst názvy a vlastnosti sad Runbook pro všechny sady Runbook v účtu Automation.  Poznámka: Pokud chcete řídit přístup operátora k jednotlivým sadám Runbook, pak tuto roli nenastavujte a místo toho použijte role operátora úlohy Automation a operátora Runbooku služby Automation v kombinaci. Následující tabulka uvádí oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft. Authorization/*/Read|Ověřování pro čtení.|
|Microsoft. Automation/automationAccounts/hybridRunbookWorkerGroups/Read|Číst Hybrid Runbook Worker prostředky.|
|Microsoft. Automation/automationAccounts/Jobs/Read|Vypíše úlohy Runbooku.|
|Microsoft. Automation/automationAccounts/Jobs/Resume/Action|Obnoví pozastavenou úlohu.|
|Microsoft. Automation/automationAccounts/Jobs/stop/Action|Zruší probíhající úlohu.|
|Microsoft. Automation/automationAccounts/Jobs/Streams/Read|Přečtěte si streamy a výstup úlohy.|
|Microsoft. Automation/automationAccounts/Jobs/Output/Read|Získat výstup úlohy.|
|Microsoft. Automation/automationAccounts/Jobs/Suspend/Action|Pozastaví probíhající úlohu.|
|Microsoft. Automation/automationAccounts/Jobs/Write|Vytváření úloh.|
|Microsoft. Automation/automationAccounts/jobSchedules/Read|Získat Azure Automation plán úlohy.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Vytvoří Azure Automation plán úlohy.|
|Microsoft. Automation/automationAccounts/linkedWorkspace/Read|Získejte pracovní prostor propojený s účtem Automation.|
|Microsoft. Automation/automationAccounts/Read|Získejte účet Azure Automation.|
|Microsoft. Automation/automationAccounts/Runbooky/číst|Získat Azure Automation sadu Runbook.|
|Microsoft. Automation/automationAccounts/Schedules/Read|Získat Azure Automation plánování prostředku.|
|Microsoft. Automation/automationAccounts/Schedules/Write|Vytvoří nebo aktualizuje prostředek plánování Azure Automation.|
|Microsoft. Resources/Subscriptions/resourceGroups/Read      |Načte role a přiřazení rolí.         |
|Microsoft. Resources/nasazení/*      |Vytváření a Správa nasazení skupin prostředků         |
|Microsoft.Insights/alertRules/*      | Vytváření a Správa pravidel výstrah        |
|Microsoft. support/* |Vytváření a Správa lístků podpory.|

### <a name="automation-job-operator"></a>Operátor úloh automatizace

Role operátora úlohy Automation se uděluje v oboru účtu Automation. To umožňuje operátorovi oprávnění vytvářet a spravovat úlohy pro všechny Runbooky v účtu. Následující tabulka uvádí oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft. Authorization/*/Read|Ověřování pro čtení.|
|Microsoft. Automation/automationAccounts/Jobs/Read|Vypíše úlohy Runbooku.|
|Microsoft. Automation/automationAccounts/Jobs/Resume/Action|Obnoví pozastavenou úlohu.|
|Microsoft. Automation/automationAccounts/Jobs/stop/Action|Zruší probíhající úlohu.|
|Microsoft. Automation/automationAccounts/Jobs/Streams/Read|Přečtěte si streamy a výstup úlohy.|
|Microsoft. Automation/automationAccounts/Jobs/Suspend/Action|Pozastaví probíhající úlohu.|
|Microsoft. Automation/automationAccounts/Jobs/Write|Vytváření úloh.|
|Microsoft. Resources/Subscriptions/resourceGroups/Read      |  Načte role a přiřazení rolí.       |
|Microsoft. Resources/nasazení/*      |Vytváření a Správa nasazení skupin prostředků         |
|Microsoft.Insights/alertRules/*      | Vytváření a Správa pravidel výstrah        |
|Microsoft. support/* |Vytváření a Správa lístků podpory.|

### <a name="automation-runbook-operator"></a>Operátor Runbooku služby Automation

Role operátora Runbooku Automation se uděluje v oboru Runbooku. Operátor Runbooku služby Automation může zobrazit název a vlastnosti Runbooku.  Tato role v kombinaci s rolí operátora úlohy Automation umožňuje operátorovi také vytvářet a spravovat úlohy pro sadu Runbook. Následující tabulka uvádí oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft. Automation/automationAccounts/Runbooky/číst     | Vypíše Runbooky.        |
|Microsoft. Authorization/*/Read      | Ověřování pro čtení.        |
|Microsoft. Resources/Subscriptions/resourceGroups/Read      |Načte role a přiřazení rolí.         |
|Microsoft. Resources/nasazení/*      | Vytváření a Správa nasazení skupin prostředků         |
|Microsoft.Insights/alertRules/*      | Vytváření a Správa pravidel výstrah        |
|Microsoft. support/*      | Vytváření a Správa lístků podpory.        |

### <a name="log-analytics-contributor"></a>Přispěvatel Log Analytics

Přispěvatel Log Analytics může číst všechna data monitorování a upravovat nastavení monitorování. Úprava nastavení monitorování zahrnuje přidání rozšíření virtuálního počítače do virtuálních počítačů. čtení klíčů účtu úložiště, aby bylo možné konfigurovat shromažďování protokolů z Azure Storage; vytváření a konfigurace účtů služby Automation; přidávání řešení; a konfigurují se diagnostiky Azure na všech prostředcích Azure. Následující tabulka uvádí oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|*/read|Přečte prostředky všech typů s výjimkou tajných klíčů.|
|Microsoft. Automation/automationAccounts/*|Spravujte účty služby Automation.|
|Microsoft. ClassicCompute/virtualMachines/Extensions/*|Vytváření a Správa rozšíření virtuálních počítačů.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Vypíše klíče klasického účtu úložiště.|
|Microsoft. COMPUTE/virtualMachines/Extensions/*|Vytvářejte a spravujte rozšíření klasických virtuálních počítačů.|
|Microsoft.Insights/alertRules/*|Pravidla upozornění pro čtení, zápis a odstranění.|
|Microsoft.Insights/diagnosticSettings/*|Nastavení diagnostiky pro čtení, zápis a odstranění|
|Microsoft.OperationalInsights/*|Správa protokolů Azure Monitor.|
|Microsoft.OperationsManagement/*|Spravujte řešení v pracovních prostorech.|
|Microsoft. Resources/nasazení/*|Vytváření a Správa nasazení skupin prostředků|
|Microsoft. Resources/Subscriptions/ResourceGroups/Deployments/*|Vytváření a Správa nasazení skupin prostředků|
|Microsoft. Storage/storageAccounts/klíče listkey/Action|Vypíše klíče účtu úložiště.|
|Microsoft. support/*|Vytváření a Správa lístků podpory.|

### <a name="log-analytics-reader"></a>Čtenář Log Analytics

Log Analytics čtenář může zobrazit a vyhledat všechna data monitorování a také zobrazit nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure. Následující tabulka uvádí oprávnění udělená nebo zakázaná pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|*/read|Přečte prostředky všech typů s výjimkou tajných klíčů.|
|Microsoft.OperationalInsights/workspaces/analytics/query/action|Spravujte dotazy v protokolech Azure Monitor.|
|Microsoft. OperationalInsights/pracovní prostory/hledání/akce|Vyhledávejte data protokolu Azure Monitor.|
|Microsoft. support/*|Vytváření a Správa lístků podpory.|
|**Neakce**| |
|Microsoft.OperationalInsights/workspaces/sharedKeys/read|Nebylo možné číst sdílené přístupové klíče.|

### <a name="monitoring-contributor"></a>Přispěvatel monitorování

Přispěvatel monitorování může číst všechna data monitorování a aktualizovat nastavení monitorování. Následující tabulka uvádí oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|*/read|Přečte prostředky všech typů s výjimkou tajných klíčů.|
|Microsoft.AlertsManagement/alerts/*|Spravujte výstrahy.|
|Microsoft. AlertsManagement/alertsSummary/*|Spravujte řídicí panel výstrah.|
|Microsoft.Insights/AlertRules/*|Spravujte pravidla výstrah.|
|Microsoft. Insights/Components/*|Spravovat součásti Application Insights.|
|Microsoft. Insights/DiagnosticSettings/*|Spravovat nastavení diagnostiky.|
|Microsoft.Insights/eventtypes/*|Vypíše události protokolu aktivit (události správy) v předplatném. Toto oprávnění platí pro programový i portálový přístup k protokolu aktivit.|
|Microsoft. Insights/LogDefinitions/*|Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit prostřednictvím portálu. Vypíše kategorie protokolů v protokolu aktivit.|
|Microsoft. Insights/MetricDefinitions/*|Čtení definic metriky (seznam dostupných typů metrik pro prostředek).|
|Microsoft.Insights/Metrics/*|Načte metriky pro prostředek.|
|Microsoft. Insights/registr/Action|Zaregistrujte poskytovatele Microsoft. Insights.|
|Microsoft.Insights/webtests/*|Spravujte Application Insights webové testy.|
|Microsoft.OperationalInsights/workspaces/intelligencepacks/*|Správa Azure Monitor protokoluje balíčky řešení.|
|Microsoft.OperationalInsights/workspaces/savedSearches/*|Správa protokolů Azure Monitor uložených hledání|
|Microsoft. OperationalInsights/pracovní prostory/hledání/akce|Hledání Log Analytics pracovních prostorů.|
|Microsoft. OperationalInsights/pracovní prostory/sharedKeys/Action|Zobrazí seznam klíčů pro Log Analytics pracovní prostor.|
|Microsoft.OperationalInsights/workspaces/storageinsightconfigs/*|Správa Azure Monitor protokolů konfigurace služby Storage Insight.|
|Microsoft. support/*|Vytváření a Správa lístků podpory.|
|Microsoft. monitor zátěže byl/úlohy/*|Spravujte úlohy.|

### <a name="monitoring-reader"></a>Čtečka monitorování

Čtečka monitorování může číst všechna data monitorování. Následující tabulka uvádí oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|*/read|Přečte prostředky všech typů s výjimkou tajných klíčů.|
|Microsoft. OperationalInsights/pracovní prostory/hledání/akce|Hledání Log Analytics pracovních prostorů.|
|Microsoft. support/*|Vytváření a Správa lístků podpory|

### <a name="user-access-administrator"></a>Správce přístupu uživatelů

Správce přístupu uživatelů může spravovat přístup uživatelů k prostředkům Azure. Následující tabulka uvádí oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|*/read|Čtení všech prostředků|
|Microsoft. Authorization/*|Spravovat autorizaci|
|Microsoft. support/*|Vytváření a Správa lístků podpory|

## <a name="onboarding"></a>Připojování

V následujících tabulkách jsou uvedeny minimální požadovaná oprávnění potřebná ke zprovoznění virtuálních počítačů pro řešení Change Tracking nebo Update Management.

### <a name="onboarding-from-a-virtual-machine"></a>Připojování z virtuálního počítače

|**Akce**  |**Udělen**  |**Minimální rozsah**  |
|---------|---------|---------|
|Zapsat nové nasazení      | Microsoft. Resources/nasazení/*          |Předplatné          |
|Zapsat novou skupinu prostředků      | Microsoft. Resources/Subscriptions/resourceGroups/Write        | Předplatné          |
|Vytvořit nový výchozí pracovní prostor      | Microsoft.OperationalInsights/workspaces/write         | Skupina prostředků         |
|Vytvořit nový účet      |  Microsoft. Automation/automationAccounts/Write        |Skupina prostředků         |
|Propojit pracovní prostor a účet      |Microsoft.OperationalInsights/workspaces/write</br>Microsoft. Automation/automationAccounts/Read|Pracovní prostor</br>Účet Automation
|Vytvořit řešení      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write |Skupina prostředků          |
|Vytvořit rozšíření MMA      | Microsoft. COMPUTE/virtualMachines/Write         | Virtual Machines         |
|Vytvořit uložené výsledky hledání      | Microsoft.OperationalInsights/workspaces/write          | Pracovní prostor         |
|Vytvořit konfiguraci oboru      | Microsoft.OperationalInsights/workspaces/write          | Pracovní prostor         |
|Propojit řešení s konfigurací oboru      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Řešení         |
|Kontrolu stavu připojování – pracovní prostor pro čtení      | Microsoft.OperationalInsights/workspaces/read         | Pracovní prostor         |
|Kontrolní stav zprovoznění – vlastnost propojeného pracovního prostoru účtu     | Microsoft. Automation/automationAccounts/Read      | Účet Automation        |
|Vracení se změnami stavu zprovoznění – řešení pro čtení      | Microsoft.OperationalInsights/workspaces/intelligencepacks/read          | Řešení         |
|Kontrolní stav připojování – virtuální počítač pro čtení      | Microsoft. COMPUTE/virtualMachines/Read         | Virtual Machines         |
|Kontrolní stav připojování – účet pro čtení      | Microsoft. Automation/automationAccounts/Read  |  Účet Automation   |
| Vyhledání pracovního prostoru připojování pro virtuální počítač<sup>1</sup>       | Microsoft.OperationalInsights/workspaces/read         | Předplatné         |
| Registrace poskytovatele Log Analytics |Microsoft. Insights/registr/Action | Předplatné|

<sup>1</sup> toto oprávnění je potřeba k připojování prostřednictvím prostředí portálu virtuálních počítačů.

### <a name="onboarding-from-automation-account"></a>Připojování z účtu Automation

|**Akce**  |**Udělen** |**Minimální rozsah**  |
|---------|---------|---------|
|Vytvořit nové nasazení     | Microsoft. Resources/nasazení/*        | Předplatné         |
|Vytvořit novou skupinu prostředků     | Microsoft. Resources/Subscriptions/resourceGroups/Write         | Předplatné        |
|Okno AutomationOnboarding – vytvořit nový pracovní prostor     |Microsoft.OperationalInsights/workspaces/write           | Skupina prostředků        |
|Okno AutomationOnboarding – číst propojený pracovní prostor     | Microsoft. Automation/automationAccounts/Read        | Účet Automation       |
|AutomationOnboarding – řešení pro čtení     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read         | Řešení        |
|Pracovní prostor pro čtení v okně AutomationOnboarding     | Microsoft.OperationalInsights/workspaces/intelligencepacks/read        | Pracovní prostor        |
|Vytvořit odkaz pro pracovní prostor a účet     | Microsoft.OperationalInsights/workspaces/write        | Pracovní prostor        |
|Zápis účtu pro Shoebox      | Microsoft. Automation/automationAccounts/Write        | Účet        |
|Vytvořit řešení      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write        | Skupina prostředků         |
|Vytvořit nebo upravit uložené výsledky hledání     | Microsoft.OperationalInsights/workspaces/write        | Pracovní prostor        |
|Vytvořit nebo upravit konfiguraci oboru     | Microsoft.OperationalInsights/workspaces/write        | Pracovní prostor        |
|Propojit řešení s konfigurací oboru      | Microsoft.OperationalInsights/workspaces/intelligencepacks/write         | Řešení         |
| Registrace poskytovatele Log Analytics |Microsoft. Insights/registr/Action | Předplatné|
|**Krok 2 – zprovoznění více virtuálních počítačů**     |         |         |
|VMOnboarding okno – vytvořit MMA rozšíření     | Microsoft. COMPUTE/virtualMachines/Write           | Virtual Machines        |
|Vytvořit nebo upravit uložené výsledky hledání     | Microsoft.OperationalInsights/workspaces/write           | Pracovní prostor        |
|Vytvořit nebo upravit konfiguraci oboru  | Microsoft.OperationalInsights/workspaces/write   | Pracovní prostor|

## <a name="update-management"></a>Správa aktualizací

Služba Update Management dosáhne v rámci více služeb, aby poskytovala službu. Následující tabulka uvádí oprávnění potřebná ke správě nasazení správy aktualizací:

|**Prostředek**  |**Role**  |**Rozsah**  |
|---------|---------|---------|
|Účet Automation     | Přispěvatel Log Analytics       | Účet Automation        |
|Účet Automation    | Přispěvatel virtuálních počítačů        | Skupina prostředků pro účet        |
|Pracovní prostor Log Analytics     | Přispěvatel Log Analytics| Pracovní prostor Log Analytics        |
|Pracovní prostor Log Analytics |Čtenář Log Analytics| Předplatné|
|Řešení     |Přispěvatel Log Analytics         | Řešení|
|Virtual Machines     | Přispěvatel virtuálních počítačů        | Virtual Machines        |

## <a name="configure-rbac-for-your-automation-account"></a>Konfigurace RBAC pro váš účet Automation

V následující části se dozvíte, jak nakonfigurovat RBAC na svém účtu Automation prostřednictvím [portálu](#configure-rbac-using-the-azure-portal) a [PowerShellu](#configure-rbac-using-powershell) .

### <a name="configure-rbac-using-the-azure-portal"></a>Konfigurace RBAC pomocí Azure Portal

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a na stránce Účty Automation otevřete svůj účet Automation.
2. V levém horním rohu klikněte na ovládací prvek **řízení přístupu (IAM)** . Tím se otevře stránka **řízení přístupu (IAM)** , kde můžete přidat nové uživatele, skupiny a aplikace pro správu účtu Automation a zobrazit existující role, které se dají pro účet Automation nakonfigurovat.
3. Klikněte na tlačítko **přiřazení rolí** kartu.

   ![Tlačítko Přístup](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Přidání nového uživatele a přiřazení role

1. Na stránce **řízení přístupu (IAM)** kliknutím na **+ Přidat přiřazení role** otevřete stránku **Přidat přiřazení role** , kde můžete přidat uživatele, skupiny nebo aplikace a přiřadit jim roli.

2. V seznamu dostupných rolí vyberte jednu roli. Můžete zvolit některou z dostupných předdefinovaných rolí, které účet Automation podporuje, nebo libovolnou vlastní roli, kterou jste mohli definovat.

3. Do pole **Vybrat** zadejte uživatelské jméno uživatele, kterému chcete udělit oprávnění. Vyberte uživatele ze seznamu a klikněte na **Uložit**.

   ![Přidání uživatelů](media/automation-role-based-access-control/automation-04-add-users.png)

   Nyní byste měli vidět, že uživatel byl přidán na stránku **Uživatelé** s přiřazenou vybranou rolí.

   ![Vypsání uživatelů](media/automation-role-based-access-control/automation-05-list-users.png)

   Roli můžete uživateli přiřadit také na stránce **Role**.
4. Kliknutím na **role** na stránce **řízení přístupu (IAM)** otevřete stránku **role** . Tady můžete zobrazit název role a počet uživatelů a skupin přiřazených k této roli.

    ![Přiřazení role na stránce Uživatelé](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Řízení přístupu na základě rolí se dá nastavit jenom v oboru účtu Automation, a ne v jakémkoli prostředku pod účtem Automation.

#### <a name="remove-a-user"></a>Odebrání uživatele

Můžete odebrat přístupová oprávnění pro uživatele, který účet Automation nespravuje, nebo už pro organizaci nefunguje. Uživatele můžete odebrat následujícím postupem:

1. Na stránce **řízení přístupu (IAM)** vyberte uživatele, kterého chcete odebrat, a klikněte na tlačítko **Odebrat**.
2. V podokně podrobností přiřazení klikněte na tlačítko **Odebrat**.
3. Kliknutím na **Ano** odebrání potvrďte.

   ![Odebrání uživatelů](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Konfigurace RBAC pomocí prostředí PowerShell

Přístup na základě rolí se dá nakonfigurovat taky na účet Automation pomocí následujících [rutin Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

[Get-AzureRmRoleDefinition](/previous-versions/azure/mt603792(v=azure.100)) vypíše všechny role RBAC, které jsou k dispozici v Azure Active Directory. Tento příkaz můžete použít spolu s vlastností **Název** k vypsání všech akcí, které může konkrétní role provádět.

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

[Get-AzureRmRoleAssignment](/previous-versions/azure/mt619413(v=azure.100)) vypíše přiřazení rolí RBAC Azure AD v zadaném oboru. Pokud nezadáte žádné parametry, tento příkaz vrátí všechna přiřazení rolí v rámci předplatného. K vypsání přiřazení přístupu konkrétního uživatele nebo skupin, kterých je uživatel členem, použijte parametr **ExpandPrincipalGroups**.
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

[New-AzureRmRoleAssignment](/previous-versions/azure/mt603580(v=azure.100)) k přiřazení přístupu uživatelům, skupinám a aplikacím do konkrétního rozsahu.
    **Příklad:** K přiřazení role "operátor služby Automation" pro uživatele v rozsahu účtu Automation použijte následující příkaz.

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

K odebrání přístupu zadaného uživatele, skupiny nebo aplikace z konkrétního oboru použijte [příkaz Remove-AzureRmRoleAssignment](/previous-versions/azure/mt603781(v=azure.100)) .
    **Příklad:** Pomocí následujícího příkazu Odeberte uživatele z role "operátor služby Automation" v oboru účtu služby Automation.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

V předchozích příkladech nahraďte **ID přihlášení**, **ID předplatného**, **název skupiny prostředků**a **název účtu Automation** údaji svého účtu. Po zobrazení výzvy, která požádá o potvrzení odebrání přiřazené role uživatele, zvolte **Ano**.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Činnost koncového uživatele pro roli operátora automatizace – účet Automation

Když uživatel, který je přiřazený k roli operátor Automation v oboru účtu Automation, zobrazuje účet Automation, ke kterému jsou přiřazené, může zobrazit jenom seznam runbooků, úlohy Runbooku a plány vytvořené v účtu Automation, ale nemůže zobrazit jejich definition. Může spustit, zastavit, pozastavit, obnovit nebo naplánovat úlohu runbooku. Uživatel nemá přístup k jiným prostředkům služby Automation, například ke konfiguracím, skupinám Hybrid Worker nebo uzlům DSC.

![Žádný přístup k prostředkům](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Konfigurace RBAC pro Runbooky

Azure Automation umožňuje přiřadit RBAC ke konkrétním sadám Runbook. Pokud chcete přidat uživatele do konkrétní sady Runbook, spusťte následující skript. Následující skript může být spuštěn správcem účtu Automation nebo správcem tenanta.

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

Po spuštění se uživatel přihlásí k Azure Portal a zobrazí **všechny prostředky**. V seznamu uvidí sadu Runbook, kterou přidal jako **operátor Runbooku Automation** pro.

![Sada Runbook RBAC na portálu](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Činnost koncového uživatele pro roli operátora automatizace – Runbook

Když uživatel, který je přiřazený k roli operátor Automation v oboru Runbooku, zobrazí sadu Runbook, ke které jsou přiřazené, může spustit jenom Runbook a zobrazit úlohy Runbooku.

![Má přístup jenom ke spuštění.](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Další kroky

* Další informace o různých způsobech konfigurace RBAC pro Azure Automation najdete v článku [Správa řízení přístupu na základě role pomocí Azure PowerShellu](../role-based-access-control/role-assignments-powershell.md).
* Podrobnosti o různých způsobech spouštění runbooků najdete v článku [Spuštění runbooku](automation-starting-a-runbook.md)
* Další informace o různých typech runbooků najdete v článku [Typy runbooků ve službě Azure Automation](automation-runbook-types.md).

