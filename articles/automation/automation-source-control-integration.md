---
title: Integrace správy zdrojového kódu ve službě Azure Automation
description: Tento článek popisuje integrace správy zdrojového kódu pomocí GitHub ve službě Azure Automation.
services: automation
ms.service: automation
ms.component: process-automation
author: georgewallace
ms.author: gwallace
ms.date: 03/16/2018
ms.topic: conceptual
manager: carmonm
ms.openlocfilehash: 0b8f97103a892b0c9a3cb6f88298295a20caf302
ms.sourcegitcommit: f3bd5c17a3a189f144008faf1acb9fabc5bc9ab7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/10/2018
ms.locfileid: "44296385"
---
# <a name="source-control-integration-in-azure-automation"></a>Integrace správy zdrojového kódu ve službě Azure Automation
Integrace správy zdrojového kódu můžete přidružit sady runbook ve vašem účtu Automation a úložištěm správy zdrojového kódu na Githubu. Správy zdrojového kódu můžete snadno spolupracovat se svým týmem, sledovat změny a vrátit zpět na starší verze vašich sadách runbook. Například správy zdrojového kódu umožňuje synchronizovat různými větvemi ve správě zdrojového kódu pro vývojové, testovací nebo produkční prostředí účty služby Automation, což usnadňuje podporu kód, který byl testován ve vašem vývojovém prostředí automatizace v produkčním prostředí účet.

Správy zdrojového kódu umožňuje vloží kód do správy zdrojového kódu ze služby Azure Automation nebo přijmout jejich změny vašich sadách runbook ze správy zdrojového kódu do Azure Automation. Tento článek popisuje, jak nastavit správu zdrojového kódu v prostředí Azure Automation. Začneme tím, že nakonfigurujete Azure Automation. pro přístup k úložišti GitHub a provede různé operace, které lze provést pomocí integrace správy zdrojového kódu. 

> [!NOTE]
> Ovládací prvek zdroje podporuje aktivní a pasivní [runbooky pracovních postupů Powershellu](automation-runbook-types.md#powershell-workflow-runbooks) stejně jako [Powershellové runbooky](automation-runbook-types.md#powershell-runbooks). [Grafické runbooky](automation-runbook-types.md#graphical-runbooks) se zatím nepodporují.<br><br>
> 
> 

Existují dva jednoduché kroky potřebnými ke konfiguraci správy zdrojového kódu pro svůj účet Automation a pouze jeden, pokud už máte účet GitHub. Jsou to tyto:

## <a name="step-1--create-a-github-repository"></a>Krok 1 – Vytvoření úložiště GitHub
Pokud už máte účet GitHub a úložiště, který chcete propojit s Azure Automation, pak se přihlaste do vašeho stávajícího účtu a začít od kroku 2 níže. V opačném případě přejděte na [Githubu](https://github.com/), zaregistrujte si nový účet a [vytvořit nové úložiště](https://help.github.com/articles/create-a-repo/).

## <a name="step-2--set-up-source-control-in-azure-automation"></a>Krok 2 – nastavení správy zdrojového kódu ve službě Azure Automation
1. Z účtu služby Automation stránky na webu Azure Portal, v části **nastavení účtu**, klikněte na tlačítko **správy zdrojového kódu.** 
   
1. **Správy zdrojových kódů** stránka se otevře, kde můžete nakonfigurovat podrobnosti o vašem účtu GitHub. Níže je uvedený seznam parametrů ke konfiguraci:  
   
   | **Parametr** | **Popis** |
   |:--- |:--- |
   | Vyberte zdroj |Vyberte zdroj. V současné době pouze **Githubu** je podporována. |
   | Autorizace |Klikněte na tlačítko **Authorize** tlačítko pro udělení přístupu službě Azure Automation do vašeho úložiště GitHub. Pokud již jste přihlášeni ke svému účtu GitHub v jiném okně, se používají pověření tohoto účtu. Po úspěšném ověření na stránce se zobrazí vaše uživatelské jméno v Githubu pod **autorizace vlastnost**. |
   | Zvolit úložiště |Vyberte ze seznamu dostupných úložišť úložiště GitHub. |
   | Zvolte větev |Vyberte větev ze seznamu dostupných větví. Pouze **hlavní** větve se zobrazí, pokud jste ještě nevytvořili žádné větve. |
   | Cesta ke složce Runbooků |Cesta ke složce runbooků Určuje cestu v úložišti GitHub, ze kterého chcete vložit ani přijmout jejich změny kódu. Musí být zadána ve formátu **/název_složky/subfoldername**. Jenom runbooky v cesta ke složce runbooků se synchronizují do vašeho účtu Automation. Sady Runbook v podsložkách cesta ke složce runbooků se **není** synchronizovat. Použití **/** synchronizovat všechny sady runbook v úložišti. |
3. Například, pokud máte úložiště s názvem **PowerShellScripts** , která obsahuje složku s názvem **RootFolder**, která obsahuje složku s názvem **podsložky**. Na jednotlivých úrovních složka synchronizace můžete použít následující řetězce:
   
   1. Synchronizace sad runbook z **úložiště**, je cesta ke složce runbooků */*
   2. Synchronizace sad runbook z **RootFolder**, je cesta ke složce runbooků */RootFolder*
   3. Synchronizace sad runbook z **podsložky**, je cesta ke složce runbooků */RootFolder/podsložky*.
4. Po dokončení konfigurace parametrů se zobrazí na **nastavit zdrojového** stránky.  
   
    ![Konfigurace stránky zdrojového ovládacího prvku](media/automation-source-control-integration/automation_02_SourceControlConfigure.png)
5. Po kliknutí na **OK**, integrace správy zdrojového kódu je nyní nakonfigurováno pro svůj účet Automation a je třeba aktualizovat údaje Githubu. Můžete kliknout na tuto část a zobrazí se všechny vaše historie úlohy synchronizace zdrojového ovládacího prvku.  
   
    ![Úložiště hodnot](media/automation-source-control-integration/automation_03_RepoValues.png)
6. Po nastavení správy zdrojového kódu ve vašem účtu Automation vytvoří následující prostředky služby Automation:  
   Dvě [proměnných assetů](automation-variables.md) jsou vytvořeny.  
   
   * Proměnná **Microsoft.Azure.Automation.SourceControl.Connection** obsahuje hodnoty připojovacího řetězce, jak je znázorněno níže.  
     
     | **Parametr** | **Hodnota** |
     |:--- |:--- |
     | Název |Microsoft.Azure.Automation.SourceControl.Connection |
     | Typ |Řetězec |
     | Hodnota |{"Větve":\<*název vaší větve*>, "RunbookFolderPath":\<*cesta ke složce Runbooků*>, "typ zprostředkovatele":\<*má hodnotu 1 pro GitHub*>, "Úložiště":\<*název vašeho úložiště*>, "Username":\<*GitHub si uživatelské jméno*>} |

    * Proměnná **Microsoft.Azure.Automation.SourceControl.OAuthToken**, obsahuje zabezpečené zašifrovanou hodnotu vaše OAuthToken.  

    |**Parametr**            |**Hodnota** |
    |:---|:---|
    | Název  | Microsoft.Azure.Automation.SourceControl.OAuthToken |
    | Typ | Unknown(Encrypted) |
    | Hodnota | <*Šifrované OAuthToken*> |  

    ![Proměnné](media/automation-source-control-integration/automation_04_Variables.png)  

    * **Automatizace správy zdrojového kódu** se přidá jako autorizovanou aplikaci ke svému účtu GitHub. Chcete-li zobrazit aplikaci: na domovské stránce Githubu, přejděte do vaší **profilu** > **nastavení** > **aplikací**. Tato aplikace umožňuje službě Azure Automation k synchronizaci vašeho úložiště GitHub s účtem Automation.  

    ![Libovolná aplikace](media/automation-source-control-integration/automation_05_GitApplication.png)


## <a name="using-source-control-in-automation"></a>Pomocí správy zdrojového kódu ve službě Automation
### <a name="check-in-a-runbook-from-azure-automation-to-source-control"></a>Vrátit se změnami sadu runbook ve službě Azure Automation do správy zdrojového kódu
Runbook se změnami umožňuje nasdílení změn, které jste provedli v sadě runbook ve službě Azure Automation do svým úložištěm řízení zdrojů. Dále jsou uvedené kroky k vrácení se změnami sadu runbook:

1. Ve svém účtu Automation [vytvořit nový runbook textové](automation-first-runbook-textual.md), nebo [upravit existující, textovou runbook](automation-edit-textual-runbook.md). Tato sada runbook může být v pracovním postupu Powershellu nebo sady runbook skript prostředí PowerShell.  
2. Po úpravě runbooku, uložte ho a klikněte na tlačítko **vrácení se změnami** z **upravit** stránky.  
   
    ![Tlačítko pro vrácení se změnami](media/automation-source-control-integration/automation_06_CheckinButton.png)

     > [!NOTE] 
     > Vrácení se změnami ve službě Azure Automation přepíše kód, který aktuálně existuje ve vaší správě zdrojového kódu. Je Git ekvivalentní instrukce příkazového řádku k vrácení se změnami **git přidat + potvrzení změn git a git push**  

1. Po kliknutí na **vrácení se změnami**, zobrazí se výzva k potvrzení, klikněte na tlačítko **Ano** pokračujte.  
   
    ![Zpráva pro vrácení se změnami](media/automation-source-control-integration/automation_07_CheckinMessage.png)
2. Vrácení se změnami spustí runbook zdrojového ovládacího prvku: **synchronizace MicrosoftAzureAutomationAccountToGitHubV1**. Tato sada runbook umožňuje připojení ke Githubu a nasdílí změny ve službě Azure Automation do vašeho úložiště. Chcete-li zobrazit kontrolovaný v historii úlohy, přejděte zpět na **integrace správy zdrojového kódu** kartu a kliknutím otevřete stránku synchronizace úložiště. Tato stránka zobrazuje všechny vaše úlohy řízení zdrojů.  Vyberte úlohu chcete zobrazit a kliknutím zobrazíte podrobnosti.  
   
    ![Vrácení se změnami sadu Runbook](media/automation-source-control-integration/automation_08_CheckinRunbook.png)
   
   > [!NOTE]
   > Ovládací prvek zdroje sady runbook jsou speciální runbooků Automation, který nelze zobrazit nebo upravit. Zatímco se nezobrazí v seznamu sad runbook, můžete vidět úloh synchronizace zobrazující v seznamu úloh.
   > 
   > 
3. Název změněné runbooku se odešle jako vstupní parametr pro vrácena se změnami sadu runbook. Je možné [zobrazte podrobnosti o úloze](automation-runbook-execution.md#viewing-job-status-from-the-azure-portal) tak, že rozbalíte sadu runbook v **synchronizace úložišť** stránky.  
   
    ![Vstup vrácení se změnami](media/automation-source-control-integration/automation_09_CheckinInput.png)
4. Po dokončení úlohy a prohlédněte si změny, aktualizujte stránku vašeho úložiště GitHub.  Ve vašem úložišti a zobrazí se potvrzovací zpráva by měla být potvrzení: **aktualizované *název sady Runbook* ve službě Azure Automation.**  

### <a name="sync-runbooks-from-source-control-to-azure-automation"></a>Synchronizace sad runbook ze správy zdrojového kódu do Azure Automation
Tlačítko Synchronizovat na stránce synchronizace úložiště umožňuje stáhnout všechny sady runbook cesta ke složce runbooků úložiště do vašeho účtu Automation. Stejné úložiště můžete synchronizované do více než jeden účet služby Automation. Dále jsou uvedené kroky k synchronizaci sady runbook:

1. Z účtu Automation, kde nastavit správu zdrojového kódu, otevřete **synchronizace integrace nebo úložiště správy zdrojového** stránky a klikněte na tlačítko **synchronizace**.  Zobrazí se výzva k potvrzení klikněte na tlačítko **Ano** pokračujte.  
   
    ![Tlačítko Synchronizovat](media/automation-source-control-integration/automation_10_SyncButtonwithMessage.png)
2. Sada runbook spustí synchronizaci: **synchronizace MicrosoftAzureAutomationAccountFromGitHubV1**. Tato sada runbook umožňuje připojení ke Githubu a načte změny z úložiště do Azure Automation. Měli byste vidět nové úlohy na **synchronizace úložišť** stránky pro tuto akci. Chcete-li zobrazit podrobnosti o úloze synchronizace, klikněte na tlačítko otevřete stránku Podrobnosti úlohy.  
   
    ![Synchronizace sady Runbook](media/automation-source-control-integration/automation_11_SyncRunbook.png)

    > [!NOTE] 
    > Přepíše verzi konceptu sady runbook, které momentálně existují ve vašem účtu Automation pro synchronizaci ze správy zdrojových kódů **všechny** sady runbook, které jsou aktuálně ve správy zdrojového kódu. Je Git ekvivalentní instrukce příkazového řádku pro synchronizaci **o přijetí změn git**


## <a name="troubleshooting-source-control-problems"></a>Řešení potíží se ovládací prvek zdroje
Pokud jsou nějaké chyby pomocí vrácení se změnami nebo úloha synchronizace, měl by být pozastaven stav úlohy a další podrobnosti o chybě můžete zobrazit na stránce úlohy.  **Všechny protokoly** části se dozvíte, všechny Powershellu datové proudy přidružené k této úlohy. To vám poskytne informace potřebné k vám pomůžou s opravit všechny problémy s vrácení se změnami nebo synchronizace. Taky se naučíte posloupnost akcí, ke kterým došlo při synchronizaci nebo vrácení se změnami sadu runbook.  

![AllLogs image](media/automation-source-control-integration/automation_13_AllLogs.png)

## <a name="disconnecting-source-control"></a>Odpojení správy zdrojového kódu
Pokud chcete odpojit od účtu GitHub, otevřete stránku synchronizace úložiště a klikněte na tlačítko **odpojit**. Jakmile odpojíte správy zdrojového kódu, sady runbook, které byly dříve synchronizovaná zůstávají ve vašem účtu Automation, ale nepovolí stránce synchronizace úložiště.  

  ![Odpojit tlačítko](media/automation-source-control-integration/automation_12_Disconnect.png)

## <a name="next-steps"></a>Další postup
Další informace o integraci správy zdrojových kódů najdete v článku na následujících odkazech:  

* [Služby Azure Automation: Integrace správy zdrojového kódu ve službě Azure Automation](https://azure.microsoft.com/blog/azure-automation-source-control-13/)  
* [Hlasujte pro Oblíbené ve zdrojovém systému ovládacího prvku](https://www.surveymonkey.com/r/?sm=2dVjdcrCPFdT0dFFI8nUdQ%3d%3d)  
* [Služby Azure Automation: Integrace sada Runbook zdrojového kódu s Azure DevOps](https://azure.microsoft.com/blog/azure-automation-integrating-runbook-source-control-using-visual-studio-online/)  

