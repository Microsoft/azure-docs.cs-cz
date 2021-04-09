---
title: Použití integrace správy zdrojového kódu v Azure Automation – starší verze
description: V tomto článku se dozvíte, jak používat integraci správy zdrojového kódu.
services: automation
ms.subservice: process-automation
ms.date: 12/04/2019
ms.topic: conceptual
ms.openlocfilehash: 4dedbcf58e76b8c969f8607db6922e87a85f08e5
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "97591869"
---
# <a name="use-source-control-integration-in-azure-automation---legacy"></a>Použití integrace správy zdrojového kódu v Azure Automation – starší verze

> [!NOTE]
> Existuje nové prostředí pro správu zdrojového kódu. Další informace o novém prostředí najdete v tématu [Správa zdrojového kódu (Preview)](source-control-integration.md).

Integrace správy zdrojového kódu umožňuje přidružit Runbooky do svého účtu Automation k úložišti správy zdrojových kódů GitHub. Správa zdrojového kódu umožňuje snadnou spolupráci se svým týmem, sledovat změny a vracet se zpět k předchozím verzím runbooků. Například Správa zdrojového kódu umožňuje synchronizovat různé větve ve správě zdrojového kódu do účtů pro vývoj, testování nebo produkční automatizaci, což usnadňuje povýšení kódu, který byl testován ve vašem vývojovém prostředí, na váš účet služby Automation.

Správa zdrojového kódu umožňuje odeslat kód z Azure Automation do správy zdrojových kódů nebo načíst Runbooky ze správy zdrojového kódu do Azure Automation. Tento článek popisuje, jak nastavit správu zdrojového kódu v prostředí Azure Automation. Začneme konfigurací Azure Automation pro přístup k úložišti GitHub a projdete různými operacemi, které je možné provádět pomocí integrace správy zdrojového kódu. 

> [!NOTE]
> Správa zdrojového kódu podporuje přijímání a vkládání [runbooků pracovního postupu PowerShellu](automation-runbook-types.md#powershell-workflow-runbooks) a [powershellových runbooků](automation-runbook-types.md#powershell-runbooks). [Grafické Runbooky](automation-runbook-types.md#graphical-runbooks) ještě nejsou podporované.

## <a name="configure-source-control"></a>Konfigurovat správu zdrojového kódu

Ke konfiguraci správy zdrojového kódu pro váš účet Automation se vyžadují dva jednoduché kroky, a to jenom v případě, že už máte účet GitHubu. 

### <a name="create-a-github-repository"></a>Vytvoření úložiště GitHub

Pokud již máte účet GitHub a úložiště, které chcete propojit s Azure Automation, přihlaste se k existujícímu účtu a začněte od kroku 2 níže. V opačném případě přejděte do [GitHubu](https://github.com/), zaregistrujte si nový účet a [vytvořte nové úložiště](https://help.github.com/articles/create-a-repo/).

### <a name="set-up-source-control"></a>Nastavení správy zdrojového kódu

1. Na stránce účet Automation v Azure Portal v části **Nastavení účtu** klikněte na **Správa zdrojového kódu.**

2. Otevře se stránka Správa zdrojového kódu, kde můžete nakonfigurovat podrobnosti o svém účtu GitHubu. Níže je uvedený seznam parametrů ke konfiguraci:  

   | **Parametr** | **Popis** |
   |:--- |:--- |
   | Zvolit zdroj |Vyberte zdroj. V současné době se podporuje jenom **GitHub** . |
   | Autorizace |Kliknutím na tlačítko **autorizovat** udělíte Azure Automation přístup k úložišti GitHub. Pokud jste už přihlášení k účtu GitHub v jiném okně, použijí se přihlašovací údaje tohoto účtu. Po úspěšném ověření se na stránce v části **vlastnost autorizace** zobrazí uživatelské jméno GitHubu. |
   | Zvolit úložiště |Vyberte úložiště GitHub ze seznamu dostupných úložišť. |
   | Zvolit větev |Vyberte větev ze seznamu dostupných větví. Pokud jste nevytvořili žádné větve, zobrazí se jenom **Hlavní** větev. |
   | Cesta ke složce sady Runbook |Cesta ke složce sady Runbook Určuje cestu v úložišti GitHub, ze které chcete vložit nebo načíst svůj kód. Musí být zadán ve formátu **/FolderName/subfoldername**. Pouze Runbooky v cestě ke složce sady Runbook budou synchronizovány do vašeho účtu Automation. Runbooky v podsložkách cesty ke složce sady **Runbook nebudou** synchronizovány. Použijte **/** k synchronizaci všech runbooků v úložišti. |
3. Například pokud máte úložiště s názvem **PowerShellScripts** , které obsahuje složku s názvem **RootFolder**, která obsahuje složku s názvem **podsložce**. K synchronizaci každé úrovně složky můžete použít následující řetězce:

   1. Chcete-li synchronizovat Runbooky z **úložiště**, cesta ke složce sady Runbook je **/** .
   2. Aby bylo možné synchronizovat Runbooky z **RootFolder**, cesta ke složce sady Runbook je **/RootFolder**.
   3. Chcete-li synchronizovat Runbooky z **podsložky**, cesta ke složce sady Runbook je **/RootFolder/SubFolder**.
4. Po nakonfigurování parametrů se zobrazí na stránce nastavit správu zdrojového kódu.  

    ![Stránka Správa zdrojového kódu zobrazující nastavení](media/source-control-integration-legacy/automation-SourceControlConfigure.png)
5. Po kliknutí na **OK** se integrace správy zdrojového kódu teď nakonfigurovala pro váš účet Automation a měla by se aktualizovat s informacemi z GitHubu. Nyní můžete kliknutím na tuto část zobrazit veškerou historii úloh synchronizace správy zdrojového kódu.  

    ![Hodnoty pro aktuálně konfigurovanou konfiguraci správy zdrojového kódu](media/source-control-integration-legacy/automation-RepoValues.png)
6. Po nastavení správy zdrojového kódu se ve vašem účtu Automation vytvoří dva [prostředky s proměnnými](./shared-resources/variables.md) . Do vašeho účtu GitHubu se navíc přidá autorizovaná aplikace.

   * Proměnná **Microsoft. Azure. Automation. SourceControl. Connection** obsahuje hodnoty připojovacího řetězce, jak je znázorněno níže.  

     | **Parametr** | **Hodnota** |
     |:--- |:--- |
     | `Name`  |Microsoft. Azure. Automation. SourceControl. Connection |
     | `Type`  |Řetězec |
     | `Value` |{"Větev": \<*Your branch name*> , "RunbookFolderPath": \<*Runbook folder path*> , "ProviderType": \<*has a value 1 for GitHub*> , "úložiště": \<*Name of your repository*> , "username": \<*Your GitHub user name*> } |

   * Proměnná **Microsoft. Azure. Automation. SourceControl. OAuthToken** obsahuje zabezpečenou šifrovanou hodnotu vašeho OAuthToken.  

     |**Parametr**            |**Hodnota** |
     |:---|:---|
     | `Name`  | `Microsoft.Azure.Automation.SourceControl.OAuthToken` |
     | `Type`  | `Unknown(Encrypted)` |
     | `Value` | <`Encrypted OAuthToken`> |  

     ![Okno zobrazující proměnné správy zdrojového kódu](media/source-control-integration-legacy/automation-Variables.png)  

   * **Správa zdrojového kódu** se přidá jako autorizovaná aplikace do vašeho účtu GitHubu. Pokud chcete zobrazit aplikaci, na domovské stránce GitHubu přejděte na nastavení **profilu**  >    >  **aplikace**. Tato aplikace umožňuje Azure Automation synchronizovat úložiště GitHub s účtem Automation.  

     ![Nastavení aplikace na GitHubu](media/source-control-integration-legacy/automation-GitApplication.png)

## <a name="use-source-control-in-automation"></a>Použití správy zdrojového kódu ve službě Automation

Vrácení se změnami Runbooku vám umožní nasdílet změny, které jste provedli v sadě Runbook, v Azure Automation do úložiště správy zdrojového kódu. Níže jsou uvedené kroky pro vrácení sady Runbook se změnami:

1. Z účtu Automation [vytvořte novou textovou sadu](./learn/automation-tutorial-runbook-textual.md)Runbook nebo [upravte existující textový Runbook](automation-edit-textual-runbook.md). Tato sada Runbook může být buď pracovní postup PowerShellu, nebo PowerShellový Runbook skriptu.  
2. Po úpravě Runbooku ji uložte a klikněte na tlačítko **vrátit se změnami** na stránce Upravit.  

    ![Okno zobrazující vrácení se změnami na tlačítko GitHub](media/source-control-integration-legacy/automation-CheckinButton.png)

     > [!NOTE] 
     > Vrácení se změnami z Azure Automation přepíše kód, který aktuálně existuje ve vašem správě zdrojového kódu. K vrácení se změnami do příkazového řádku pro Git se můžete vrátit v **Gitu přidání + Git potvrzení + Git push**  

3. Když kliknete na **vrácení se změnami**, zobrazí se výzva s potvrzením kliknutím na **Ano** budete pokračovat.  

    ![Dialogové okno potvrzující vrácení se změnami do správy zdrojového kódu](media/source-control-integration-legacy/automation-CheckinMessage.png)
4. Vrácení se změnami spustí Runbook správy zdrojového kódu: **Sync-MicrosoftAzureAutomationAccountToGitHubV1**. Tato sada Runbook se připojí k GitHubu a pošle změny z Azure Automation do vašeho úložiště. Chcete-li zobrazit historii vrácených úloh, přejděte zpět na kartu **integrace správy zdrojového kódu** a kliknutím otevřete stránku synchronizace úložiště. Tato stránka obsahuje všechny úlohy správy zdrojového kódu. Vyberte úlohu, kterou chcete zobrazit, a kliknutím zobrazíte podrobnosti.  

    ![Okno zobrazující výsledky úlohy synchronizace](media/source-control-integration-legacy/automation-CheckinRunbook.png)

   > [!NOTE]
   > Runbooky správy zdrojového kódu jsou speciální Runbooky pro automatizaci, které nemůžete zobrazit ani upravovat. I když se nezobrazují v seznamu sad Runbook, zobrazují se úlohy synchronizace v seznamu úloh.

5. Název upravené sady Runbook se odešle jako vstupní parametr pro vrácenou sadu Runbook. [Podrobnosti o úloze můžete zobrazit](automation-runbook-execution.md#job-statuses) rozbalením sady Runbook na stránce synchronizace úložiště.  

    ![Okno zobrazující vstup pro úlohu synchronizace](media/source-control-integration-legacy/automation-CheckinInput.png)
6. Až se úloha dokončí, aktualizujte úložiště GitHub.  V úložišti by mělo být potvrzení se zprávou potvrzení: **aktualizovaný *název runbooku* v Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Synchronizovat Runbooky ze správy zdrojového kódu až po Azure Automation

Tlačítko synchronizovat na stránce synchronizace úložiště vám umožní načíst všechny Runbooky v cestě ke složce sady Runbook vašeho úložiště do vašeho účtu Automation. Stejné úložiště lze synchronizovat s více než jedním účtem služby Automation. Níže jsou uvedené kroky pro synchronizaci sady Runbook:

1. Z účtu Automation, ve kterém jste nastavili správu zdrojového kódu, otevřete stránku Správa zdrojového kódu/synchronizace úložiště a klikněte na **synchronizovat**.  Zobrazí se zpráva s potvrzením, kliknutím na **Ano** pokračujte.  

    ![Tlačítko synchronizovat se zprávou potvrzující, že se všechny Runbooky budou synchronizovat](media/source-control-integration-legacy/automation-SyncButtonwithMessage.png)

2. Synchronizace spustí Runbook  **Sync-MicrosoftAzureAutomationAccountFromGitHubV1** , který se připojí k GitHubu a vyžádá změny z vašeho úložiště do Azure Automation. Na stránce synchronizace úložiště pro tuto akci by se měla zobrazit nová úloha. Chcete-li zobrazit podrobnosti o úloze synchronizace, kliknutím otevřete stránku s podrobnostmi o úloze.  

    ![Okno zobrazující výsledky synchronizace úlohy synchronizace v úložišti GitHub](media/source-control-integration-legacy/automation-SyncRunbook.png)

    > [!NOTE]
    > Synchronizace ze správy zdrojového kódu přepíše koncept verze runbooků, které aktuálně existují ve vašem účtu Automation, pro **všechny** Runbooky, které jsou aktuálně ve správě zdrojového kódu. Dopředné pokyny příkazového řádku pro Git, které se mají synchronizovat, je **Git Pull** .

![Okno zobrazující všechny protokoly ze pozastavené úlohy synchronizace správy zdrojového kódu](media/source-control-integration-legacy/automation-AllLogs.png)

## <a name="disconnect-source-control"></a>Odpojit správu zdrojového kódu

Pokud se chcete odpojit od svého účtu GitHubu, otevřete stránku synchronizace úložiště a klikněte na **Odpojit**. Jakmile odpojíte správu zdrojového kódu, sady Runbook, které byly synchronizovány dříve, zůstanou ve vašem účtu Automation, ale stránka synchronizace úložiště nebude povolena.  

  ![Okno zobrazující tlačítko Odpojit pro odpojení od správy zdrojového kódu](media/source-control-integration-legacy/automation-Disconnect.png)

## <a name="next-steps"></a>Další kroky

* Pro integraci správy zdrojového kódu v Azure Automation naleznete v tématu [Azure Automation: integrace správy zdrojového kódu v Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/).  
* Informace o integraci správy zdrojového kódu sady Runbook ve službě Visual Studio Online naleznete v tématu [Azure Automation: integrování správy zdrojového kódu sady Runbook pomocí služby Visual Studio Online](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/).  
