---
title: Integrace správy zdrojového kódu v Azure Automation – starší verze
description: Tento článek popisuje integraci správy zdrojového kódu s GitHubv Azure Automation.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: dcadfcb4c2f8e6bc371b0a70b917c8c1e218fba9
ms.sourcegitcommit: acb82fc770128234f2e9222939826e3ade3a2a28
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/21/2020
ms.locfileid: "81679514"
---
# <a name="source-control-integration-in-azure-automation---legacy"></a>Integrace správy zdrojového kódu v Azure Automation – starší verze

> [!NOTE]
> Je nové prostředí pro svoz zdrojového kódu. Další informace o novém prostředí najdete v [tématu Source Control (Preview).](source-control-integration.md)

Integrace správy zdrojového kódu umožňuje přidružit runbooky ve vašem účtu Automation do úložiště správy zdrojového kódu GitHub. Správa zdrojového kódu umožňuje snadno spolupracovat s týmem, sledovat změny a vrátit se k dřívějším verzím runbooků. Například správy zdrojového kódu umožňuje synchronizovat různé větve ve správě zdrojového kódu s účty automatizace vývoje, testování nebo výroby, což usnadňuje propagaci kódu, který byl testován ve vývojovém prostředí, na váš účet automatizace produkční ho diody.

Source Control umožňuje nabízení kódu z Azure Automation do správy zdrojového kódu nebo vyžádat runbooky ze správy zdrojového kódu do Azure Automation. Tento článek popisuje, jak nastavit správě zdrojového kódu v prostředí Azure Automation. Začneme konfigurací Azure Automation pro přístup k úložišti GitHub a procházíme různými operacemi, které lze provést pomocí integrace správy zdrojového kódu. 

> [!NOTE]
> Správa zdrojového kódu podporuje vytahování a tlačení [runbooků Pracovního postupu prostředí PowerShell](automation-runbook-types.md#powershell-workflow-runbooks) a [runbooků prostředí PowerShell](automation-runbook-types.md#powershell-runbooks). [Grafické sady Runbook](automation-runbook-types.md#graphical-runbooks) ještě nejsou podporovány.

## <a name="configuring-source-control"></a>Konfigurace správy zdrojového kódu

Existují dva jednoduché kroky potřebné ke konfiguraci správy zdrojového kódu pro váš účet Automation a jenom jeden, pokud už máte účet GitHub. 

### <a name="create-a-github-repository"></a>Vytvoření úložiště GitHubu

Pokud už máte účet GitHub a úložiště, které chcete propojit s Azure Automation, přihlaste se ke svému stávajícímu účtu a začněte od kroku 2 níže. V opačném případě přejděte na [GitHub](https://github.com/), zaregistrujte si nový účet a [vytvořte nové úložiště](https://help.github.com/articles/create-a-repo/).

### <a name="set-up-source-control"></a>Nastavení správy zdrojového kódu

1. Na stránce Účet automatizace na portálu Azure klikněte v části **Nastavení účtu**na **Položku Řízení zdrojového kódu.**

2. Otevře se stránka Správy zdrojového kódu, kde můžete nakonfigurovat podrobnosti o účtu GitHub. Níže je uvedený seznam parametrů ke konfiguraci:  

   | **Parametr** | **Popis** |
   |:--- |:--- |
   | Zvolte zdroj |Vyberte zdroj. V současné době je podporován pouze **GitHub.** |
   | Autorizace |Kliknutím na tlačítko **Autorizovat** udělíte Azure Automation přístup k úložišti GitHub. Pokud jste již přihlášeni ke svému účtu GitHub v jiném okně, pak se používají přihlašovací údaje tohoto účtu. Jakmile je autorizace úspěšná, stránka zobrazí vaše uživatelské jméno GitHub u **autorizační vlastnosti**. |
   | Zvolte úložiště |Ze seznamu dostupných úložišť vyberte úložiště GitHub. |
   | Zvolte větev |Vyberte větev ze seznamu dostupných větví. Pokud jste nevytvořili žádné větve, zobrazí se pouze **hlavní** větev. |
   | Cesta ke složce runbooku |Cesta ke složce Runbook určuje cestu v úložišti GitHub, ze kterého chcete vysunout nebo vytáhnout váš kód. Je nutné jej zadat ve formátu **/foldername/subfoldername**. Do vašeho účtu Automation se synchronizují jenom runbooky v cestě ke složce sady Runbook. Sady Runbook v podsložkách cesty ke složce sady Runbook **nebudou** synchronizovány. Slouží **/** k synchronizaci všech sad Runbook pod úložištěm. |
3. Máte-li například úložiště s názvem **PowerShellScripts,** které obsahuje složku s názvem **RootFolder**, která obsahuje složku s názvem **Podsložka**. K synchronizaci jednotlivých úrovní složek můžete použít následující řetězce:

   1. Chcete-li synchronizovat runbooky z **/** **úložiště**, je cesta ke složce sady Runbook .
   2. Chcete-li synchronizovat runbooky ze **služby RootFolder**, je cesta ke složce sady Runbook **/RootFolder**.
   3. Chcete-li synchronizovat sady Runbook z **podsložky**, je cesta ke složce sady Runbook **/RootFolder/SubFolder**.
4. Po konfiguraci parametrů jsou zobrazeny na stránce Nastavit směřování zdrojového kódu.  

    ![Stránka Ovládací prvek zdroj zobrazující nastavení](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Po klepnutí na tlačítko **OK**, integrace správy zdrojového kódu je nyní nakonfigurovánpro váš účet automatizace a měla by být aktualizována informacemi GitHub. Nyní můžete kliknout na tuto část a zobrazit všechny historie úloh synchronizace správy zdrojového kódu.  

    ![Hodnoty pro aktuální konfiguraci nakonfigurovaného řízení zdrojového kódu](media/source-control-integration-legacy/automation-RepoValues.png)
6. Po nastavení správy zdrojového kódu jsou ve vašem účtu automation vytvořeny dva [proměnné datové](automation-variables.md) zdroje. Kromě toho je autorizovaná aplikace přidána do vašeho účtu GitHub.

   * Proměnná **Microsoft.Azure.Automation.SourceControl.Connection** obsahuje hodnoty připojovacího řetězce, jak je znázorněno níže.  

     | **Parametr** | **Hodnota** |
     |:--- |:--- |
     | `Name`  |Microsoft.Azure.Automation.SourceControl.Connection |
     | `Type`  |Řetězec |
     | `Value` |{"Branch":\<*Název pobočky*>"RunbookFolderPath":\<Cesta ke*složce Runbook*>"ProviderType":\<má\<*hodnotu 1 pro GitHub*>,"Úložiště": Název vašeho*úložiště*>"Uživatelské jméno":\<Vaše uživatelské jméno*GitHub*>} |

   * Proměnná **Microsoft.Azure.Automation.SourceControl.OAuthToken**obsahuje zabezpečenou šifrovanou hodnotu vašeho Tokenu OAuth.  

     |**Parametr**            |**Hodnota** |
     |:---|:---|
     | `Name`  | `Microsoft.Azure.Automation.SourceControl.OAuthToken` |
     | `Type`  | `Unknown(Encrypted)` |
     | `Value` | <`Encrypted OAuthToken`> |  

     ![Okno zobrazující proměnné správy zdrojového kódu](media/source-control-integration-legacy/automation-Variables.png)  

   * **Automatizace Source Control** se přidává jako autorizovaná aplikace k vašemu účtu GitHub. Chcete-li aplikaci zobrazit, přejděte na domovské stránce GitHubu do**aplikace****nastavení** >  **profilu** > . Tato aplikace umožňuje Azure Automation synchronizovat úložiště GitHub s účtem Automation.  

     ![Nastavení aplikace v GitHubu](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="using-source-control-in-automation"></a>Použití správy zdrojového kódu v automatizaci

Vrácení runbooku se změnami umožňuje posunout změny provedené v runbooku v Azure Automation do úložiště správy zdrojového kódu. Níže jsou uvedeny kroky pro vrácení runbooku se změnami:

1. Z účtu Automation [vytvořte novou textovou runbook](automation-first-runbook-textual.md)nebo [upravte existující textovou knihu runbook](automation-edit-textual-runbook.md). Tato kniha runbook může být buď pracovní postup prostředí PowerShell nebo spuštění skriptu PowerShell.  
2. Po úpravě runbooku ji uložte a na stránce úprav klikněte na **Vrácení se změnami.**  

    ![Okno zobrazující vrácení se změnami na tlačítko GitHub](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > Vrácení se změnami z Azure Automation přepíše kód, který aktuálně existuje ve vaší správě zdrojového kódu. Instrukce příkazového řádku ekvivalentní Git u vrácení se změnami je **git add + git commit + git push**  

3. Po klepnutí na **tlačítko Vrácení se změnami**se zobrazí výzva s potvrzovací zprávou, klepnutím na tlačítko **Ano** pokračujte.  

    ![Dialogové okno potvrzující vrácení se změnami do správy zdrojového kódu](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. Vrácení se změnami spustí sadu runbook správy zdrojového kódu: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Tento runbook se připojí k GitHubu a odešle změny z Azure Automation do vašeho úložiště. Chcete-li zobrazit vrácení se změnami v historii úloh, přejděte zpět na kartu **Integrace správy zdrojového kódu** a klepnutím otevřete stránku Synchronizace úložiště. Na této stránce jsou zobrazeny všechny úlohy správy zdrojového kódu. Vyberte úlohu, kterou chcete zobrazit, a kliknutím zobrazte podrobnosti.  

    ![Okno zobrazující výsledky úlohy synchronizace](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Sady runbooky správy zdrojového kódu jsou speciální runbooky automatizace, které nelze zobrazit nebo upravit. I když se v seznamu runbooků nezobrazují, zobrazí se v seznamu úloh úloh úlohy úlohy.

5. Název upraveného runbooku je odeslán jako vstupní parametr pro rezervovaný runbook. [Podrobnosti o úloze](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) můžete zobrazit rozbalením runbooku na stránce Synchronizace úložiště.  

    ![Okno zobrazující vstup pro úlohu synchronizace](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Aktualizujte úložiště GitHub po dokončení úlohy a zobrazte změny.  Ve vašem úložišti by měla být revize se zprávou potvrzení: **Aktualizovaný *název runbooku* v Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Synchronizace runbooků ze správy zdrojového kódu do Azure Automation

Tlačítko synchronizace na stránce Synchronizace úložiště umožňuje vytáhnout všechny sady Runbook v cestě ke složce sady Runbook vašeho úložiště do vašeho účtu Automation. Stejné úložiště lze synchronizovat s více než jedním účtem automatizace. Níže jsou uvedeny kroky pro synchronizaci runbooku:

1. Z účtu Automatizace, do kterého nastavujete snímání zdrojového kódu, otevřete stránku Synchronizace integrace/úložiště správy zdrojového kódu a klepněte na tlačítko **Synchronizovat**.  Zobrazí se výzva s potvrzovací zprávou, pokračujte klepnutím na tlačítko **Ano.**  

    ![Tlačítko Synchronizovat se zprávou potvrzující synchronizaci všech sad Runbook](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. Synchronizace spustí **runbook Sync-MicrosoftAzureAutomationAccountFromGitHubV1,** který se připojí k GitHubu a stáhne změny z vašeho úložiště do Azure Automation. Pro tuto akci byste měli vidět novou úlohu na stránce Synchronizace úložiště. Chcete-li zobrazit podrobnosti o úloze synchronizace, kliknutím otevřete stránku podrobností o úloze.  

    ![Okno zobrazující výsledky synchronizace úlohy synchronizace v úložišti GitHub](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Synchronizace ze správy zdrojového kódu přepíše koncept verze runbooků, které aktuálně existují ve vašem účtu Automation pro **všechny** sady Runbook, které jsou aktuálně ve správě zdrojového kódu. Instrukce pro synchronizaci ekvivalentnímu příkazu Git je **git pull**

![Okno zobrazující všechny protokoly z úlohy synchronizace pozastaveného řízení zdrojového kódu](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnecting-source-control"></a>Odpojení správy zdrojového kódu

Pokud se chcete odpojit od svého účtu GitHub, otevřete stránku Synchronizace úložiště a klikněte na **Odpojit**. Jakmile odpojíte správě zdrojového kódu, sady Runbook, které byly synchronizovány dříve, zůstanou ve vašem účtu Automation, ale stránka Synchronizace úložiště nebude povolena.  

  ![Okno zobrazující tlačítko Odpojit pro odpojení správy zdrojového kódu](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Další kroky

Další informace o integraci správy zdrojového kódu naleznete v následujících zdrojích:  

* [Azure Automation: Integrace správy zdrojového kódu v azure automatizaci](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Automatizace Azure: Integrace správy zdrojového kódu runbooku pomocí Azure DevOps](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  
