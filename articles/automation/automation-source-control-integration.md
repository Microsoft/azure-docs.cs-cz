---
title: Zdroj integrace ovládacích prvků ve službě Azure Automation
description: Tento článek popisuje integrace ovládacích prvků zdrojového s Githubu ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: article
manager: carmonm
ms.openlocfilehash: 29ec32c933f3dbe07a844bd99a1f5aa3fa57d61b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="source-control-integration-in-azure-automation"></a>Integrace správy zdrojového kódu v Azure Automation
Integrace ovládacích prvků zdrojového umožňuje přidružit sady runbook ve vašem účtu Automation se úložištěm řízení zdrojů Githubu. Správa zdrojového kódu umožňuje snadno spolupracovat s týmem, sledovat změny a vrátit zpět na dřívější verze sadu runbook. Například Správa zdrojového kódu umožňuje synchronizovat různých větví ve správě zdrojového kódu do vaší vývoj, testovací nebo produkční účty Automation, což usnadňuje povýšit kód, který byl testován v vývojové prostředí pro vaše produkční automatizace účet.

Správa zdrojového kódu umožňuje nabízené kódu do správy zdrojového kódu ve službě Azure Automation nebo runbooků od správy zdrojového kódu pro Azure Automation pro vyžádání obsahu. Tento článek popisuje, jak nastavit zdrojového kódu ve vašem prostředí Azure Automation. Začneme konfigurací Azure Automation pro přístup k vaší úložiště GitHub a provede různé operace, které lze provést pomocí integrace ovládacích prvků zdrojového. 

> [!NOTE]
> Správa zdrojového kódu podporuje stahování a předání [runbooky pracovních postupů Powershellu](automation-runbook-types.md#powershell-workflow-runbooks) a také [Powershellové runbooky](automation-runbook-types.md#powershell-runbooks). [Grafické runbooky](automation-runbook-types.md#graphical-runbooks) ještě nejsou podporovány.<br><br>
> 
> 

Existují dvě jednoduchých kroků, které jsou potřeba ke konfiguraci zdrojového kódu pro svůj účet Automation a jenom jedna, pokud již máte účet GitHub. Jsou to tyto:

## <a name="step-1--create-a-github-repository"></a>Krok 1 – Vytvoření úložiště GitHub
Pokud již máte účet GitHub a úložiště, který chcete propojit s Azure Automation, pak se přihlaste do existujícího účtu a začít od kroku 2 níže. Jinak přejděte na [Githubu](https://github.com/), zaregistrujte si nový účet a [vytvořit nové úložiště](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Krok 2 – nastavení zdrojového kódu ve službě Azure Automation
1. Z účtu služby Automation stránky na portálu Azure v části **nastavení účtu**, klikněte na tlačítko **zdrojového kódu.** 
   
1. **Správy zdrojového kódu** otevře, kde můžete nakonfigurovat údaje o vašem účtu GitHub se stránka. Níže je uvedený seznam parametrů ke konfiguraci:  
   
   | **Parametr** | **Popis** |
   |:--- |:--- |
   | Vyberte zdroj |Vyberte zdroj. V současné době pouze **Githubu** je podporována. |
   | Autorizace |Klikněte **Autorizovat** tlačítko k udělení přístupu službě Azure Automation se svým úložištěm GitHub. Pokud jste již přihlášení k účtu GitHub v jiném okně, jsou použita pověření tohoto účtu. Po ověření je úspěšné, stránky se zobrazí vaše uživatelské jméno Githubu pod **autorizace vlastnost**. |
   | Vyberte úložiště |Vyberte ze seznamu dostupných úložišť úložiště GitHub. |
   | Zvolte větev |Vyberte ze seznamu dostupných větví větev. Pouze **hlavní** větev se zobrazí, pokud jste nevytvořili žádné větve. |
   | Cesta ke složce sady Runbook |Cesta ke složce runbook Určuje cestu v úložišti GitHub, ze kterého chcete push nebo pull kódu. Musí být zadána ve formátu **/název_složky/subfoldername**. Jenom runbooky ve složce cesty sady runbook se budou synchronizovat s účtu Automation. Sady Runbook v podsložkách cesty ke složce runbook bude **není** synchronizovat. Použití **/** k synchronizaci všech sad runbook v rámci úložiště. |
3. Například, pokud máte úložiště s názvem **PowerShellScripts** složku s názvem, který obsahuje **RootFolder**, která obsahuje složku s názvem **podsložky**. K synchronizaci každé úrovni složky, můžete použít následující řetězce:
   
   1. Pro synchronizaci runbooků **úložiště**, je cesta ke složce sady runbook */*
   2. Pro synchronizaci runbooků **RootFolder**, je cesta ke složce runbook */RootFolder*
   3. Pro synchronizaci runbooků **podsložky**, je cesta ke složce runbook */RootFolder/podsložky*.
4. Po dokončení konfigurace parametry, jsou zobrazeny na **nastavit řízení zdrojů** stránky.  
   
    ![Konfigurace stránky zdroj ovládacího prvku](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)
5. Po kliknutí na tlačítko **OK**, integrace ovládacích prvků zdrojového je teď nakonfigurovaný na účtu Automation a je třeba aktualizovat vaše informace Githubu. Nyní můžete kliknutím na tuto část zobrazíte všechna vaše historie úlohy synchronizace zdroj ovládacího prvku.  
   
    ![Hodnoty úložiště](media/automation-source-control-integration/automation_03_RepoValues.png)
6. Po nastavení správy zdrojového kódu, budou vytvořeny následující prostředky služby Automation v účtu Automation:  
   Dva [proměnných assetů](automation-variables.md) se vytvářejí.  
   
   * Proměnná **Microsoft.Azure.Automation.SourceControl.Connection** obsahuje hodnoty připojovacího řetězce, jak je uvedeno níže.  
     
     | **Parametr** | **Hodnota** |
     |:--- |:--- |
     | Název |Microsoft.Azure.Automation.SourceControl.Connection |
     | Typ |Řetězec |
     | Hodnota |{"Větve":\<*název vaší firemní pobočky*>, "RunbookFolderPath":\<*cesta ke složce Runbook*>, "typ zprostředkovatele":\<*má hodnotu 1 pro GitHub*>, "Úložiště":\<*název ve vašem úložišti*>, "Username":\<*Githubu vaše uživatelské jméno*>} |

    * Proměnná **Microsoft.Azure.Automation.SourceControl.OAuthToken**, obsahuje zabezpečené zašifrovanou hodnotu vaší OAuthToken.  

    |**Parametr**            |**Hodnota** |
    |:---|:---|
    | Název  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
    | Typ | Unknown(Encrypted) |
    | Hodnota | <*Šifrované OAuthToken*> |  

    ![Proměnné](media/automation-source-control-integration/automation_04_Variables.png)  

    * **Automatizace správy zdrojového kódu** se přidá jako autorizovaný aplikace ke svému účtu GitHub. Chcete-li zobrazit aplikaci: domovskou stránku Githubu, přejděte k vaší **profil** > **nastavení** > **aplikace**. Tato aplikace umožňuje Azure Automation k synchronizaci úložiště GitHub pro účet Automation.  

    ![Git aplikace](media/automation-source-control-integration/automation_05_GitApplication.png)


## <a name="using-source-control-in-automation"></a>Použití správy zdrojového kódu v automatizace
### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Sady runbook z Azure Automation do správy zdrojového kódu se změnami
Runbook se změnami umožňuje odešlete změny, které jste provedli v sadě runbook ve službě Azure Automation do úložiště řízení zdrojů. Tady jsou kroky pro sady runbook se změnami:

1. Z vašeho účtu Automation [vytvořte nový textový runbook](automation-first-runbook-textual.md), nebo [upravit runbook služby existující, textovou](automation-edit-textual-runbook.md). Tato sada runbook může být v pracovním postupu Powershellu nebo sadu runbook skript prostředí PowerShell.  
2. Po úpravě runbooku, uložte ho a klikněte na tlačítko **vrácení se změnami** z **upravit** stránky.  
   
    ![Tlačítko vrácení se změnami](media/automation-source-control-integration/automation_06_CheckinButton.png)

     > [!NOTE] 
     > Vrácení se změnami ve službě Azure Automation přepíše kód, který aktuálně existuje v zdrojového kódu. Ekvivalent příkazového řádku instrukce Git k vrácení se změnami je **přidat git + git potvrzení + git push**  

1. Když kliknete na tlačítko **vrácení se změnami**, zobrazí se výzva k potvrzení, klikněte na **Ano** pokračujte.  
   
    ![Zpráva pro vrácení se změnami](media/automation-source-control-integration/automation_07_CheckinMessage.png)
2. Vrácení se změnami spouští runbook řízení zdroj: **synchronizace MicrosoftAzureAutomationAccountToGitHubV1**. Tato sada runbook se připojuje k webu GitHub a nabízených oznámení změny ve službě Azure Automation se svým úložištěm. Chcete-li zobrazit rezervovanou v historii úlohy, přejděte zpět na **integrace ovládacích prvků zdrojového** a kliknutím otevřete stránku úložiště synchronizace. Tato stránka zobrazuje všechny vaše úlohy řízení zdrojů.  Vyberte úlohu chcete zobrazit a kliknutím zobrazíte podrobnosti.  
   
    ![Vrácení se změnami sadu Runbook](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
   
   > [!NOTE]
   > Zdroj řízení sady runbook jsou speciální automatizace sady runbook, které nemůžete zobrazit nebo upravit. Zatímco se není zobrazena v seznamu sad runbook, můžete vidět úloh synchronizace, zobrazuje v seznamu úloh.
   > 
   > 
3. Název změněné runbooku je odeslán jako vstupní parametr pro zaškrtnuté v runbooku. Můžete [zobrazení podrobností o úloze](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) rozšířením sady runbook v **úložiště synchronizace** stránky.  
   
    ![Vstup vrácení se změnami](media/automation-source-control-integration/automation_09_CheckinInput.png)
4. Po dokončení úlohy a prohlédněte si změny, aktualizujte úložiště GitHub.  Měla by existovat potvrzení změn v úložišti zprávou potvrzení: **aktualizované *název sady Runbook* ve službě Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Synchronizace sady runbook od správy zdrojového kódu pro Azure Automation.
Tlačítko Synchronizovat na stránce úložiště synchronizace umožňuje načítat všechny sady runbook v cestě ke složce runbook ve vašem úložišti ke svému účtu Automation. Stejného úložiště můžete synchronizovat s více než jeden účet Automation. Tady jsou kroky pro synchronizaci sady runbook:

1. Účet Automation, kde můžete nastavit řízení zdrojů, otevřete **zdroj ovládacího prvku integrace/úložiště synchronizace** a klikněte na tlačítko **synchronizace**.  Zobrazí se výzva k potvrzení, klikněte na tlačítko **Ano** pokračujte.  
   
    ![Tlačítko Synchronizovat](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
2. Sada runbook spustí synchronizace: **synchronizace MicrosoftAzureAutomationAccountFromGitHubV1**. Tato sada runbook se připojuje k webu GitHub a vrátí změny z úložiště pro Azure Automation. Měli byste vidět nové úlohy na **úložiště synchronizace** stránky pro tuto akci. Chcete-li zobrazit podrobnosti o úloze synchronizace, klikněte na Otevřít stránku Podrobnosti úlohy.  
   
    ![Synchronizace sady Runbook](media/automation-source-control-integration/automation_11_SyncRunbook.png)

    > [!NOTE] 
    > K synchronizaci od správy zdrojového kódu přepíše verzi konceptu sady runbook, které aktuálně neexistuje v účtu Automation pro **všechny** sady runbook, které jsou právě ve zdrojové ovládacího prvku. Ekvivalent příkazového řádku instrukce Git pro synchronizaci je **vyžádání git**


## <a name="troubleshooting-source-control-problems"></a>Řešení potíží zdroj ovládacího prvku
Pokud existují nějaké chyby s kontrolou v nebo úloha synchronizace, stav úlohy by měla být pozastavena, a na stránce úlohy můžete zobrazit další podrobnosti o této chybě.  **Všechny protokoly** část ukazuje všechny prostředí PowerShell datové proudy přidružené k této úlohy. To poskytuje informace potřebné k vám pomůžou s opravit všechny problémy s vaší vrácení se změnami nebo synchronizace. Také vám ukazuje posloupnost akcí, které došlo k chybě při synchronizaci nebo kontrola v sadě runbook.  

![Obrázek AllLogs](media/automation-source-control-integration/automation_13_AllLogs.png)

## <a name="disconnecting-source-control"></a>Odpojuje se správa zdrojového kódu
Pokud chcete odpojit od účtu Githubu, otevřete stránku úložiště synchronizace a klikněte na tlačítko **odpojení**. Po odpojení zdrojového kódu sady runbook, které byly synchronizovány dříve stále na účtu Automation, ale nebude povolen stránce úložiště synchronizace.  

  ![Tlačítko Odpojit](media/automation-source-control-integration/automation_12_Disconnect.png)

## <a name="next-steps"></a>Další postup
Další informace o integrace ovládacích prvků zdrojového najdete v následujících zdrojích informací:  

* [Služby Azure Automation: Integrace ovládacích prvků zdrojového ve službě Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Hlas pro Oblíbené ve zdrojovém systému ovládací prvek](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Služby Azure Automation: Runbook zdroj ovládacího prvku pomocí Visual Studio Team Services integraci](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  

