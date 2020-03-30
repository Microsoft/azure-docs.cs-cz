---
title: Řízení přístupu na základě role ve službě Azure Automation
description: Řízení přístupu na základě role (RBAC) umožňuje správu přístupu k prostředkům Azure. Tento článek popisuje způsob nastavení řízení přístupu na základě role ve službě Azure Automation.
keywords: rbac v automation, řízení přístupu na základě rolí, rbac v azure
services: automation
ms.subservice: shared-capabilities
ms.date: 05/17/2018
ms.topic: conceptual
ms.openlocfilehash: 8caf502db91ab09eea48fc8a902dacf6bf40f24c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79278633"
---
# <a name="role-based-access-control-in-azure-automation"></a>Řízení přístupu na základě role ve službě Azure Automation

Řízení přístupu na základě role (RBAC) umožňuje správu přístupu k prostředkům Azure. Pomocí [RBAC](../role-based-access-control/overview.md)můžete oddělit povinnosti v rámci týmu a udělit pouze množství přístupu uživatelům, skupinám a aplikacím, které potřebují k provádění svých úloh. Uživatelům můžete udělit přístup založený na rolích pomocí portálu Azure, nástrojů příkazového řádku Azure nebo api pro správu Azure.

## <a name="roles-in-automation-accounts"></a>Role v účtech automatizace

Ve službě Azure Automation se přístup uděluje přiřazením příslušné role RBAC uživatelům, skupinám a aplikacím v rozsahu účtu Automation. Níže jsou uvedené vestavěné role, které účet Automation podporuje:

| **Role** | **Popis** |
|:--- |:--- |
| Vlastník |Role Vlastník umožňuje přístup ke všem prostředkům a akcím v rámci účtu automatizace, včetně poskytování přístupu ostatním uživatelům, skupinám a aplikacím ke správě účtu automatizace. |
| Přispěvatel |Role přispěvatele umožňuje spravovat všechno kromě úpravy oprávnění jiných uživatelů k přístupu k účtu Automation. |
| Čtenář |Role čtenáře vám umožní zobrazit všechny prostředky na účtu Automation, ale neumožní vám provádět změny. |
| Operátor služby Automation |Role Operátor automatizace umožňuje zobrazit název a vlastnosti sady Runbook a vytvářet a spravovat úlohy pro všechny sady Runbook v účtu Automation. Tato role je užitečná, pokud chcete chránit prostředky účtu Automation, jako jsou prostředky pověření a sady Runbook, před zobrazením nebo úpravou, ale přesto umožňují členům vaší organizace spouštět tyto sady Runbook. |
|Automatizace Obsluha úloh|Role Automatizační operátor umožňuje vytvářet a spravovat úlohy pro všechny sady Runbook v účtu automatizace.|
|Automatizace Runbook Operátor|Role Operátor runbooku automatizace umožňuje zobrazit název a vlastnosti runbooku.|
| Přispěvatel Log Analytics | Role přispěvatele analýzy protokolů umožňuje číst všechna data monitorování a upravovat nastavení monitorování. Úpravy nastavení monitorování zahrnuje přidání rozšíření virtuálních počítačů do virtuálních počítačů, čtení klíčů účtu úložiště, aby bylo možné konfigurovat shromažďování protokolů z úložiště Azure, vytváření a konfiguraci účtů automatizace, přidávání řešení a konfiguraci diagnostiky Azure na všechny prostředky Azure.|
| Čtenář Log Analytics | Role Čtečka log Analytics umožňuje zobrazit a prohledat všechna data monitorování a také zobrazit nastavení monitorování. To zahrnuje zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure. |
| Přispěvatel monitorování | Role Přispěvatel monitorování umožňuje číst všechna data monitorování a aktualizovat nastavení monitorování.|
| Monitorovací čtečka | Role Čtečka monitorování umožňuje číst všechna data monitorování. |
| Správce přístupu uživatelů |Role správce přístupu uživatelů umožňuje spravovat přístup uživatelů k účtům Azure Automation. |

## <a name="role-permissions"></a>Oprávnění role

Následující tabulky popisují konkrétní oprávnění udělená každé roli. To může zahrnovat akce, které udělují oprávnění, a NotActions, které je omezují.

### <a name="owner"></a>Vlastník

Vlastník může spravovat vše, včetně přístupu. V následující tabulce jsou uvedena oprávnění udělená pro roli:

|Akce|Popis|
|---|---|
|Microsoft.Automation/automationAccounts/|Vytvářejte a spravujte prostředky všech typů.|

### <a name="contributor"></a>Přispěvatel

Přispěvatel může spravovat vše kromě přístupu. V následující tabulce jsou uvedena oprávnění udělená a odepřená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/|Vytváření a správa prostředků všech typů|
|**Ne akce**||
|Microsoft.Authorization/*/Delete| Odstraňte role a přiřazení rolí.       |
|Microsoft.Authorization/*/Write     |  Vytvořte role a přiřazení rolí.       |
|Microsoft.Authorization/elevateAccess/Action    | Odepře možnost vytvořit správce přístupu uživatele.       |

### <a name="reader"></a>Čtenář

Čtenář může zobrazit všechny prostředky v účtu automatizace, ale nemůže provádět žádné změny.

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/read|Zobrazení všech prostředků v účtu automatizace. |

### <a name="automation-operator"></a>Operátor služby Automation

Operátor automatizace je schopen vytvářet a spravovat úlohy a číst názvy a vlastnosti sady Runbook pro všechny sady Runbook v účtu Automation.  Poznámka: Pokud chcete řídit přístup operátora k jednotlivým runbookům, nenastavujte tuto roli a místo toho použijte role "Automatizační operátor" a "Automatizační operátor" v kombinaci. V následující tabulce jsou uvedena oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Přečtěte si autorizaci.|
|Microsoft.Automation/automationAccounts/hybridRunbookWorkerGroups/čt|Přečtěte si hybridní prostředky pracovníka runbooku.|
|Microsoft.Automation/automationAccounts/jobs/read|Seznam úloh runbooku.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Pokračovat v práci, která je pozastavena.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Zrušte probíhající úlohu.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Přečtěte si datové proudy úloh a výstup.|
|Microsoft.Automation/automationAccounts/jobs/output/read|Získejte výstup úlohy.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pozastavte probíhající úlohu.|
|Microsoft.Automation/automationAccounts/jobs/write|Vytvářejte pracovní místa.|
|Microsoft.Automation/automationAccounts/jobSchedules/read|Získejte plán úloh Azure Automation.|
|Microsoft.Automation/automationAccounts/jobSchedules/write|Vytvořte plán úloh Azure Automation.|
|Microsoft.Automation/automationAccounts/linkedWorkspace/read|Získejte pracovní prostor propojený s účtem Automation.|
|Microsoft.Automation/automationAccounts/read|Získejte účet Azure Automation.|
|Microsoft.Automation/automationAccounts/runbooks/read|Získejte runbook Azure Automation.|
|Microsoft.Automation/automationAccounts/plány/čtení|Získejte datový zdroj plánu Azure Automation.|
|Microsoft.Automation/automationAccounts/schedules/write|Vytvořte nebo aktualizujte datový zdroj plánu Azure Automation.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |Přečtěte si role a přiřazení rolí.         |
|Microsoft.Resources/deployments/*      |Vytvořte a spravujte nasazení skupin prostředků.         |
|Microsoft.Insights/alertRules/*      | Vytvořte a spravujte pravidla výstrah.        |
|Microsoft.Support/* |Vytvořte a spravujte lístky podpory.|

### <a name="automation-job-operator"></a>Automatizace Obsluha úloh

Role operátoru úlohy automatizace je udělena v oboru účtu automatizace.To umožňuje operátoroprávnění vytvářet a spravovat úlohy pro všechny sady Runbook v účtu. V následující tabulce jsou uvedena oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Authorization/*/read|Přečtěte si autorizaci.|
|Microsoft.Automation/automationAccounts/jobs/read|Seznam úloh runbooku.|
|Microsoft.Automation/automationAccounts/jobs/resume/action|Pokračovat v práci, která je pozastavena.|
|Microsoft.Automation/automationAccounts/jobs/stop/action|Zrušte probíhající úlohu.|
|Microsoft.Automation/automationAccounts/jobs/streams/read|Přečtěte si datové proudy úloh a výstup.|
|Microsoft.Automation/automationAccounts/jobs/suspend/action|Pozastavte probíhající úlohu.|
|Microsoft.Automation/automationAccounts/jobs/write|Vytvářejte pracovní místa.|
|Microsoft.Resources/subscriptions/resourceGroups/read      |  Přečtěte si role a přiřazení rolí.       |
|Microsoft.Resources/deployments/*      |Vytvořte a spravujte nasazení skupin prostředků.         |
|Microsoft.Insights/alertRules/*      | Vytvořte a spravujte pravidla výstrah.        |
|Microsoft.Support/* |Vytvořte a spravujte lístky podpory.|

### <a name="automation-runbook-operator"></a>Automatizace Runbook Operátor

Role operátoru runbooku automatizace je udělena v oboru runbooku. Operátor runbook automatizace můžete zobrazit název a vlastnosti runbooku.Tato role v kombinaci s rolí "Automatizace operátor úlohy" umožňuje operátorovi také vytvářet a spravovat úlohy pro runbook. V následující tabulce jsou uvedena oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|Microsoft.Automation/automationAccounts/runbooks/read     | Seznam runbooků.        |
|Microsoft.Authorization/*/read      | Přečtěte si autorizaci.        |
|Microsoft.Resources/subscriptions/resourceGroups/read      |Přečtěte si role a přiřazení rolí.         |
|Microsoft.Resources/deployments/*      | Vytvořte a spravujte nasazení skupin prostředků.         |
|Microsoft.Insights/alertRules/*      | Vytvořte a spravujte pravidla výstrah.        |
|Microsoft.Support/*      | Vytvořte a spravujte lístky podpory.        |

### <a name="log-analytics-contributor"></a>Přispěvatel Log Analytics

Přispěvatel analýzy protokolů může číst všechna data monitorování a upravovat nastavení monitorování. Úpravy nastavení monitorování zahrnuje přidání rozšíření virtuálních počítače do virtuálních počítače; čtení klíčů účtu úložiště, abyste mohli konfigurovat kolekci protokolů z Azure Storage; vytváření a konfigurace účtů automatizace; přidávání řešení; a konfigurace diagnostiky Azure na všech prostředcích Azure. V následující tabulce jsou uvedena oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|*/čtení|Přečtěte si prostředky všech typů, s výjimkou tajných kódů.|
|Microsoft.Automation/automationAccounts/*|Správa účtů automatizace.|
|Microsoft.ClassicCompute/virtualMachines/extensions/*|Vytvářejte a spravujte rozšíření virtuálních strojů.|
|Microsoft.ClassicStorage/storageAccounts/listKeys/action|Seznam klasických klíčů účtů úložiště.|
|Microsoft.Compute/virtualMachines/extensions/*|Vytvářejte a spravujte klasická rozšíření virtuálních strojů.|
|Microsoft.Insights/alertRules/*|Pravidla výstrah pro čtení/zápis/odstranění.|
|Microsoft.Insights/diagnosticSettings/*|Nastavení diagnostiky pro čtení/zápis/odstranění.|
|Microsoft.OperationalInsights/*|Správa protokolů Azure Monitor.|
|Microsoft.OperationsManagement/*|Spravujte řešení v pracovních prostorech.|
|Microsoft.Resources/deployments/*|Vytvořte a spravujte nasazení skupin prostředků.|
|Microsoft.Resources/subscriptions/resourcegroups/deployments/*|Vytvořte a spravujte nasazení skupin prostředků.|
|Microsoft.Storage/storageAccounts/listKeys/action|Seznam klíčů účtů úložiště.|
|Microsoft.Support/*|Vytvořte a spravujte lístky podpory.|

### <a name="log-analytics-reader"></a>Čtenář Log Analytics

Čtečka analýzy protokolů může zobrazit a prohledávat všechna data monitorování a také zobrazit nastavení monitorování, včetně zobrazení konfigurace diagnostiky Azure na všech prostředcích Azure. V následující tabulce jsou uvedena oprávnění udělená nebo odepřená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|*/čtení|Přečtěte si prostředky všech typů, s výjimkou tajných kódů.|
|Microsoft.OperationalInsights/pracovní prostory/analytika/dotaz/akce|Správa dotazů v protokolech Azure Monitor.|
|Microsoft.OperationalInsights/pracovní prostory/hledání/akce|Prohledejte data protokolu Azure Monitor.|
|Microsoft.Support/*|Vytvořte a spravujte lístky podpory.|
|**Ne akce**| |
|Microsoft.OperationalInsights/pracovní prostory/sdílenéKlíče/čtení|Sdílené přístupové klíče nelze přečíst.|

### <a name="monitoring-contributor"></a>Přispěvatel monitorování

Přispěvatel monitorování může číst všechna data monitorování a aktualizovat nastavení monitorování. V následující tabulce jsou uvedena oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|*/čtení|Přečtěte si prostředky všech typů, s výjimkou tajných kódů.|
|Microsoft.AlertsManagement/alerts/*|Správa výstrah.|
|Microsoft.AlertsManagement/alertsSummary/*|Spravujte řídicí panel Výstrahy.|
|Microsoft.Insights/AlertRules/*|Spravujte pravidla výstrah.|
|Microsoft.Insights/komponenty/*|Správa součástí Application Insights.|
|Microsoft.Insights/DiagnosticSettings/*|Spravujte nastavení diagnostiky.|
|Microsoft.Insights/eventtypes/*|Seznam událostí protokolu aktivit (události správy) v předplatném. Toto oprávnění se vztahuje na programový i portálový přístup k protokolu aktivit.|
|Microsoft.Insights/LogDefinitions/*|Toto oprávnění je nezbytné pro uživatele, kteří potřebují přístup k protokolům aktivit prostřednictvím portálu. Seznam kategorií protokolu v protokolu aktivit.|
|Microsoft.Insights/MetricDefinitions/*|Čtení definic metrik (seznam dostupných typů metrik pro prostředek).|
|Microsoft.Insights/Metriky/*|Přečtěte si metriky pro prostředek.|
|Microsoft.Insights/Register/Action|Zaregistrujte poskytovatele Microsoft.Insights.|
|Microsoft.Insights/webtests/*|Spravujte webové testy Application Insights.|
|Microsoft.OperationalInsights/pracovní prostory/zpravodajské balíčky/*|Správa balíčků řešení Azure Monitor protokoly.|
|Microsoft.OperationalInsights/pracovní prostory/uloženáVyhledávání/*|Správa protokolů Azure Monitor uložených vyhledávání.|
|Microsoft.OperationalInsights/pracovní prostory/hledání/akce|Hledat v pracovních prostorech Analýzy protokolů.|
|Microsoft.OperationalInsights/pracovní prostory/sdílenéKlíče/akce|Seznam klíčů pro pracovní prostor Analýzy protokolů.|
|Microsoft.OperationalInsights/pracovní prostory/storageinsightconfigs/*|Spravujte konfigurace přehledů úložiště Azure Monitor.|
|Microsoft.Support/*|Vytvořte a spravujte lístky podpory.|
|Microsoft.WorkloadMonitor/workloads/*|Správa úloh.|

### <a name="monitoring-reader"></a>Monitorovací čtečka

Monitorovací čtečka může číst všechna data monitorování. V následující tabulce jsou uvedena oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|*/čtení|Přečtěte si prostředky všech typů, s výjimkou tajných kódů.|
|Microsoft.OperationalInsights/pracovní prostory/hledání/akce|Hledat v pracovních prostorech Analýzy protokolů.|
|Microsoft.Support/*|Vytvoření a správa lístků podpory|

### <a name="user-access-administrator"></a>Správce přístupu uživatelů

Správce přístupu uživatelů může spravovat přístup uživatelů k prostředkům Azure. V následující tabulce jsou uvedena oprávnění udělená pro roli:

|**Akce**  |**Popis**  |
|---------|---------|
|*/čtení|Přečtěte si všechny zdroje|
|Microsoft.Authorization/*|Správa autorizace|
|Microsoft.Support/*|Vytvoření a správa lístků podpory|

## <a name="onboarding"></a>Onboarding

V následujících tabulkách jsou uvedena minimální požadovaná oprávnění potřebná pro připojení virtuálních počítačů pro řešení pro sledování změn nebo pro správu aktualizací.

### <a name="onboarding-from-a-virtual-machine"></a>Registrace z virtuálního počítače

|**Akce**  |**Oprávnění**  |**Minimální rozsah**  |
|---------|---------|---------|
|Napište nové nasazení      | Microsoft.Resources/deployments/*          |Předplatné          |
|Napsat novou skupinu prostředků      | Microsoft.Resources/subscriptions/resourceGroups/write        | Předplatné          |
|Vytvoření nového výchozího pracovního prostoru      | Microsoft.OperationalInsights/pracovní prostory/zápis         | Skupina prostředků         |
|Vytvořit nový účet      |  Microsoft.Automation/automationAccounts/write        |Skupina prostředků         |
|Propojit pracovní prostor a účet      |Microsoft.OperationalInsights/pracovní prostory/zápis</br>Microsoft.Automation/automationAccounts/read|Pracovní prostor</br>Účet Automation
|Vytvořit rozšíření MMA      | Microsoft.Compute/virtualMachines/write         | Virtuální počítač         |
|Vytvořit uložené hledání      | Microsoft.OperationalInsights/pracovní prostory/zápis          | Pracovní prostor         |
|Vytvořit konfiguraci oboru      | Microsoft.OperationalInsights/pracovní prostory/zápis          | Pracovní prostor         |
|Kontrola stavu registrace – čtení pracovního prostoru      | Microsoft.OperationalInsights/pracovní prostory/čtení         | Pracovní prostor         |
|Kontrola stavu registrace – čtení vlastnosti propojeného pracovního prostoru účtu     | Microsoft.Automation/automationAccounts/read      | Účet Automation        |
|Kontrola stavu registrace - Řešení pro čtení      | Microsoft.OperationalInsights/pracovní prostory/intelligencepacks/read          | Řešení         |
|Kontrola stavu registrace – čtení virtuálního počítače      | Microsoft.Compute/virtualMachines/read         | Virtuální počítač         |
|Kontrola stavu registrace - Číst účet      | Microsoft.Automation/automationAccounts/read  |  Účet Automation   |
| Kontrola pracovního prostoru při zařazování pro virtuální počítač<sup>1</sup>       | Microsoft.OperationalInsights/pracovní prostory/čtení         | Předplatné         |
| Registrace poskytovatele Log Analytics |Microsoft.Insights/registr/akce | Předplatné|

<sup>1</sup> Toto oprávnění je potřeba k napalubě prostřednictvím prostředí portálu virtuálních počítači.

### <a name="onboarding-from-automation-account"></a>Registrace z účtu Automation

|**Akce**  |**Oprávnění** |**Minimální rozsah**  |
|---------|---------|---------|
|Vytvořit nové nasazení     | Microsoft.Resources/deployments/*        | Předplatné         |
|Vytvořit novou skupinu prostředků     | Microsoft.Resources/subscriptions/resourceGroups/write         | Předplatné        |
|Automatonboarding blade – vytvoření nového pracovního prostoru     |Microsoft.OperationalInsights/pracovní prostory/zápis           | Skupina prostředků        |
|Automatboardingboarding blade – čtení propojeného pracovního prostoru     | Microsoft.Automation/automationAccounts/read        | Účet Automation       |
|Automatizační čepel - čtecí řešení     | Microsoft.OperationalInsights/pracovní prostory/intelligencepacks/read         | Řešení        |
|Automatizační čepel - čtecí pracovní prostor     | Microsoft.OperationalInsights/pracovní prostory/intelligencepacks/read        | Pracovní prostor        |
|Vytvořit odkaz pro pracovní prostor a účet     | Microsoft.OperationalInsights/pracovní prostory/zápis        | Pracovní prostor        |
|Napsat účet pro krabici od bot      | Microsoft.Automation/automationAccounts/write        | Účet        |
|Vytvořit nebo upravit uložené hledání     | Microsoft.OperationalInsights/pracovní prostory/zápis        | Pracovní prostor        |
|Konfigurace oboru Vytvořit nebo upravit     | Microsoft.OperationalInsights/pracovní prostory/zápis        | Pracovní prostor        |
| Registrace poskytovatele Log Analytics |Microsoft.Insights/registr/akce | Předplatné|
|**Krok 2 – na palubě více virtuálních počítačů**     |         |         |
|VMOnboarding blade – vytvoření rozšíření MMA     | Microsoft.Compute/virtualMachines/write           | Virtuální počítač        |
|Vytvořit / upravit uložené hledání     | Microsoft.OperationalInsights/pracovní prostory/zápis           | Pracovní prostor        |
|Konfigurace oboru Vytvořit nebo upravit  | Microsoft.OperationalInsights/pracovní prostory/zápis   | Pracovní prostor|

## <a name="update-management"></a>Správa aktualizací

Správa aktualizací dosahuje napříč více službami a poskytuje svou službu. V následující tabulce jsou uvedena oprávnění potřebná ke správě nasazení správy aktualizací:

|**Zdrojů**  |**Role**  |**Rozsah**  |
|---------|---------|---------|
|Účet Automation     | Přispěvatel Log Analytics       | Účet Automation        |
|Účet Automation    | Přispěvatel virtuálních počítačů        | Skupina prostředků pro účet        |
|Pracovní prostor služby Log Analytics     | Přispěvatel Log Analytics| Pracovní prostor služby Log Analytics        |
|Pracovní prostor služby Log Analytics |Čtenář Log Analytics| Předplatné|
|Řešení     |Přispěvatel Log Analytics         | Řešení|
|Virtuální počítač     | Přispěvatel virtuálních počítačů        | Virtuální počítač        |

## <a name="configure-rbac-for-your-automation-account"></a>Konfigurace rbac pro váš účet automatizace

Následující část ukazuje, jak nakonfigurovat RBAC na vašem účtu automatizace prostřednictvím [portálu](#configure-rbac-using-the-azure-portal) a [prostředí PowerShell](#configure-rbac-using-powershell).

### <a name="configure-rbac-using-the-azure-portal"></a>Konfigurace RBAC pomocí portálu Azure

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com/) a na stránce Účty Automation otevřete svůj účet Automation.
2. Kliknutím na **ovládací prvek Přístup (IAM)** v levém horním rohu otevřete stránku Řízení přístupu (IAM). Na této stránce můžete přidat nové uživatele, skupiny a aplikace ke správě účtu automatizace a zobrazit existující role, které lze konfigurovat pro účet automatizace.
3. Klikněte na kartu **Přiřazení rolí**.

   ![Tlačítko Přístup](media/automation-role-based-access-control/automation-01-access-button.png)

#### <a name="add-a-new-user-and-assign-a-role"></a>Přidání nového uživatele a přiřazení role

1. Na stránce Řízení přístupu (IAM) klikněte na **+ Přidat přiřazení role**. Tato akce otevře stránku Přidat přiřazení role, kde můžete přidat uživatele, skupinu nebo aplikaci a přiřadit odpovídající roli.

2. V seznamu dostupných rolí vyberte jednu roli. Můžete zvolit některou z dostupných předdefinovaných rolí, které podporuje účet automatizace, nebo libovolné vlastní role, kterou jste definovali.

3. Do pole **Vybrat** zadejte uživatelské jméno uživatele, kterému chcete udělit oprávnění. Vyberte uživatele ze seznamu a klepněte na tlačítko **Uložit**.

   ![Přidání uživatelů](media/automation-role-based-access-control/automation-04-add-users.png)

   Nyní byste měli vidět uživatele přidaného na stránku Uživatelé s přiřazenou vybranou rolí.

   ![Vypsání uživatelů](media/automation-role-based-access-control/automation-05-list-users.png)

   Roli můžete uživateli přiřadit také na stránce Role.
4. Kliknutím na **Role** na stránce Řízení přístupu (IAM) otevřete stránku Role. Můžete zobrazit název role a počet uživatelů a skupin přiřazených k této roli.

    ![Přiřazení role na stránce Uživatelé](media/automation-role-based-access-control/automation-06-assign-role-from-users-blade.png)

   > [!NOTE]
   > Řízení přístupu založené na rolích můžete nastavit pouze v oboru účtu automatizace a nikoli na žádném prostředku pod účtem Automatizace.

#### <a name="remove-a-user"></a>Odebrání uživatele

Přístupová oprávnění můžete odebrat uživateli, který nespravuje účet Automatizace nebo který již pro organizaci nepracuje. Uživatele můžete odebrat následujícím postupem:

1. Na stránce Řízení přístupu (IAM) vyberte uživatele, který chcete odebrat, a klepněte na tlačítko **Odebrat**.
2. V podokně podrobností přiřazení klikněte na tlačítko **Odebrat**.
3. Kliknutím na **Ano** odebrání potvrďte.

   ![Odebrání uživatelů](media/automation-role-based-access-control/automation-08-remove-users.png)

### <a name="configure-rbac-using-powershell"></a>Konfigurace rbac pomocí prostředí PowerShell

Můžete také nakonfigurovat přístup založený na rolích k účtu Automation pomocí [následujících rutin Prostředí Azure PowerShell](../role-based-access-control/role-assignments-powershell.md):

[Get-AzureRmRoleDefinition](/previous-versions/azure/mt603792(v=azure.100)) uvádí všechny role RBAC, které jsou dostupné ve službě Azure Active Directory. Pomocí této rutiny s parametrem *Name* můžete vypsat všechny akce, které může určitá role provádět.

```azurepowershell-interactive
Get-AzureRmRoleDefinition -Name 'Automation Operator'
```

Následuje ukázkový výstup:

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

[Get-AzureRmRoleAssignment](/previous-versions/azure/mt619413(v=azure.100)) uvádí přiřazení rolí Azure AD RBAC v zadaném oboru. Bez parametrů tato rutina vrátí všechna přiřazení rolí provedená v rámci předplatného. Pomocí parametru *ExpandPrincipalGroups* můžete vypsat přiřazení přístupu pro zadaného uživatele a také skupiny, do kterých uživatel patří.

**Příklad:** Pomocí následující rutiny můžete vypsat všechny uživatele a jejich role v rámci účtu automatizace.

```azurepowershell-interactive
Get-AzureRMRoleAssignment -scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Následuje ukázkový výstup:

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

Pomocí [new-AzureRmRoleAssignment](/previous-versions/azure/mt603580(v=azure.100)) přiřaďte přístup uživatelům, skupinám a aplikacím k určitému oboru.
    
**Příklad:** Pomocí následujícího příkazu přiřaďte roli "Automatizační operátor" uživateli v oboru účtu automatizace.

```azurepowershell-interactive
New-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to grant access> -RoleDefinitionName 'Automation operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

Následuje ukázkový výstup:

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

Pomocí [funkce Remove-AzureRmRoleAssignment](/previous-versions/azure/mt603781(v=azure.100)) odeberte přístup určeného uživatele, skupiny nebo aplikace z určitého oboru.

**Příklad:** Pomocí následujícího příkazu odeberte uživatele z role "Operátor automatizace" v oboru účtu automatizace.

```azurepowershell-interactive
Remove-AzureRmRoleAssignment -SignInName <sign-in Id of a user you wish to remove> -RoleDefinitionName 'Automation Operator' -Scope '/subscriptions/<SubscriptionID>/resourcegroups/<Resource Group Name>/Providers/Microsoft.Automation/automationAccounts/<Automation account name>'
```

V předchozích příkladech nahraďte "přihlašovací ID uživatele, který chcete odebrat", "SubscriptionID", "Název skupiny prostředků" a název účtu automatizace s podrobnostmi účtu. Zvolte **ano,** pokud budete vyzváni k potvrzení, než budete pokračovat v odstraňování přiřazení rolí uživatelů.

### <a name="user-experience-for-automation-operator-role---automation-account"></a>Uživatelské prostředí pro roli operátora automatizace – účet automatizace

Když uživatel přiřazený k roli Automatizační operátor v oboru účtu automatizace zobrazí účet Automatizace, ke kterému je přiřazen, může zobrazit pouze seznam sad Runbook, úloh sady Runbook a plánů vytvořených v účtu Automation. Tento uživatel nemůže zobrazit definice těchto položek. Uživatel může spustit, zastavit, pozastavit, obnovit nebo naplánovat úlohu runbooku. Uživatel však nemá přístup k jiným prostředkům automatizace, jako jsou konfigurace, hybridní pracovní skupiny nebo uzly DSC.

![Žádný přístup k prostředkům](media/automation-role-based-access-control/automation-10-no-access-to-resources.png)

## <a name="configure-rbac-for-runbooks"></a>Konfigurace RBAC pro sady Runbook

Azure Automation umožňuje přiřadit RBAC ke konkrétním runbookům. Chcete-li to provést, spusťte následující skript a přidejte uživatele do konkrétní ho spouště. Tento skript můžete spustit správce účtu automatizace nebo správce tenanta.

```azurepowershell-interactive
$rgName = "<Resource Group Name>" # Resource Group name for the Automation account
$automationAccountName ="<Automation account name>" # Name of the Automation account
$rbName = "<Name of Runbook>" # Name of the runbook
$userId = "<User ObjectId>" # Azure Active Directory (AAD) user's ObjectId from the directory

# Gets the Automation account resource
$aa = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts" -ResourceName $automationAccountName

# Get the Runbook resource
$rb = Get-AzureRmResource -ResourceGroupName $rgName -ResourceType "Microsoft.Automation/automationAccounts/runbooks" -ResourceName "$automationAccountName/$rbName"

# The Automation Job Operator role only needs to be run once per user.
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Job Operator" -Scope $aa.ResourceId

# Adds the user to the Automation Runbook Operator role to the Runbook scope
New-AzureRmRoleAssignment -ObjectId $userId -RoleDefinitionName "Automation Runbook Operator" -Scope $rb.ResourceId
```

Po spuštění skriptu se uživatel přihlásí na portál Azure a zobrazí **všechny prostředky**. V seznamu může uživatel zobrazit runbook, pro který byl přidán jako operátor runbook automatizace.

![Runbook RBAC na portálu](./media/automation-role-based-access-control/runbook-rbac.png)

### <a name="user-experience-for-automation-operator-role---runbook"></a>Uživatelské prostředí pro roli operátora automatizace – Runbook

Když uživatel přiřazený k roli Automatizační operátor v oboru runbooku zobrazí přiřazenou runbook, může spustit pouze runbook a zobrazit úlohy runbooku.

![Pouze má přístup ke spuštění](media/automation-role-based-access-control/automation-only-start.png)

## <a name="next-steps"></a>Další kroky

* Informace o způsobech konfigurace RBAC pro Azure Automation najdete v průvodu do [správy RBAC pomocí Azure PowerShellu](../role-based-access-control/role-assignments-powershell.md).
* Podrobnosti o způsobech spuštění runbooku najdete v [tématu Spuštění runbooku](automation-starting-a-runbook.md).
* Informace o typech runbooků najdete v tom, že se jedná [o typy runbooků Azure Automation](automation-runbook-types.md).