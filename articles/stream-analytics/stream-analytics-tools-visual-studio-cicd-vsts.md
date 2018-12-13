---
title: Nasazení úlohy Azure Stream Analytics s CI/CD s využitím Azure DevOps
description: Tento článek popisuje, jak nasadit úlohu Stream Analytics s CI/CD pomocí služby Azure DevOps Services.
services: stream-analytics
author: su-jie
ms.author: sujie
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 12/07/2018
ms.custom: seodec18
ms.openlocfilehash: 7e9ce598dbd8987ab32747f5fa9d14646ed4ee71
ms.sourcegitcommit: efcd039e5e3de3149c9de7296c57566e0f88b106
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2018
ms.locfileid: "53164071"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-azure-pipelines"></a>Kurz: Nasazení úlohy Azure Stream Analytics pomocí Azure kanálů CI/CD
Tento kurz popisuje, jak nastavit kontinuální integraci a nasazení pro úlohu Azure Stream Analytics pomocí služby Azure Pipelines. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání správy zdrojového kódu do projektu
> * Vytvoření kanálu buildu v Azure Pipelines
> * Vytvoření kanálu verze v Azure Pipelines
> * Automatické nasazení a upgrade aplikace

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že jste provedli následující akce:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) a úlohy **Vývoj pro Azure** nebo **Ukládání a zpracování dat**.
* Vytvořte [projekt Stream Analytics v sadě Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs).
* Vytvořte organizaci [Azure DevOps](https://visualstudio.microsoft.com/team-services/).

## <a name="configure-nuget-package-dependency"></a>Nakonfigurujte závislost balíčku NuGet
Pokud chcete provádět automatické sestavení a automatické nasazení na libovolný počítač, je potřeba použít balíček NuGet `Microsoft.Azure.StreamAnalytics.CICD`. Poskytuje nástroj MSBuild, místní spuštění a nástroje pro nasazení, které podporují kontinuální integraci a proces nasazení projektů Stream Analytics v sadě Visual Studio. Další informace obsahuje článek o [nástrojích CI/CD pro Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md).

Přidejte **packages.config** do adresáře projektu.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-azure-repos-git-repo"></a>Sdílení řešení sady Visual Studio do nového úložiště Gitu Azure Repos

Nasdílejte zdrojové soubory své aplikace do projektu v Azure DevOps, abyste mohli generovat buildy.  

1. Vytvořte pro svůj projekt nové místní úložiště Git tím, že na stavovém řádku v pravém dolním rohu sady Visual Studio vyberete **Přidat do správy zdrojového kódu** a pak **Git**. 

2. V zobrazení **Synchronizace** v **Team Exploreru** vyberte v části **Doručovat do Azure DevOps Services** tlačítko **Publikovat úložiště Git**.

   ![Vložit tlačítko Publikovat úložiště Git Azure DevOps služby](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-git-repo-devops.png)

3. Ověřte svůj e-mail a vyberte svoji organizaci v rozevíracího seznamu **Doména Azure DevOps Services**. Zadejte název svého úložiště a vyberte **Publikovat úložiště**.

   ![Stisknutím tlačítka úložišti publikovat úložiště Git](./media/stream-analytics-tools-visual-studio-cicd-vsts/publish-repository-devops.png)

    Publikováním úložiště se ve vaší organizaci vytvoří nový projekt se stejným názvem jako místní úložiště. Pokud chcete úložiště vytvořit v existujícím projektu, klikněte na **Upřesnit** vedle **názvu úložiště** a vyberte projekt. Svůj kód můžete zobrazit v prohlížeči výběrem možnosti **Podívejte se na webu**.
 
## <a name="configure-continuous-delivery-with-azure-devops"></a>Konfigurace průběžného doručování pomocí Azure DevOps
Kanál buildu Azure Pipelines popisuje pracovní postup složený z postupně prováděných kroků buildu. Přečtěte si další informace o [kanálech buildu Azure Pipelines](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav). 

Kanál verze Azure Pipelines popisuje pracovní postup, který nasadí balíček aplikace do clusteru. Při společném použití provedou kanál buildu a kanál verze celý pracovní postup od zdrojových souborů až po spuštění aplikace v clusteru. Přečtěte si další informace o [kanálech verze](https://docs.microsoft.com/azure/devops/pipelines/release/define-multistage-release-process?view=vsts) Azure Pipelines.

### <a name="create-a-build-pipeline"></a>Vytvoření kanálu buildu
Otevřete webový prohlížeč a přejděte na projekt, který jste právě vytvořili v [Azure DevOps](https://app.vsaex.visualstudio.com/). 

1. Na kartě **Sestavení a vydání** vyberte **Sestavení** a nakonec **+Nové**.  Vyberte **Git Azure DevOps Services** a **Pokračovat**.
    
    ![Vyberte zdroj DevOps Git v Azure DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source-devops.png)

2. V oblasti **Vybrat šablonu** klikněte na **Prázdný proces**. Začnete tak s prázdným kanálem.
    
    ![Prázdným procesem. Vyberte z možností šablony v DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template-empty-process.png)

3. V části **Triggery** zaškrtněte stav **Povolit průběžnou integraci** a povolte tak průběžnou integraci.  Pokud chcete ručně spustit sestavení, vyberte **Uložit a zařadit do fronty**. 
    
    ![Povolit stav trigger průběžné integrace](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger-status-ci.png)

4. Sestavení se aktivují také pro nasdílení změn nebo vrácení se změnami. Pokud chcete zkontrolovat průběh sestavení, přepněte na kartu **Sestavení**.  Jakmile ověříte, že se build úspěšně spouští, musíte definovat kanál verze, který nasadí vaši aplikaci do clusteru. Klikněte pravým tlačítkem na tři tečky vedle kanálu buildu a vyberte **Upravit**.

5.  V části **Úlohy** zadejte do pole **Fronta agenta** „Hosted“.
    
    ![V nabídce úlohy vyberte frontu agenta](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue-task.png) 

6. Ve **Fázi 1** klikněte na tlačítko **+** a přidejte úlohu **NuGet**.
    
    ![Přidat úkol NuGet fronta agenta](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-nuget-task.png)

7. Rozbalte **Upřesnit** a přidejte `$(Build.SourcesDirectory)\packages` do **cílového adresáře**. Ponechejte zbývající výchozí hodnoty konfigurace NuGet.

   ![Konfigurace úloh obnovení NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-restore-config.png)

8. Ve **Fázi 1** klikněte na tlačítko **+** a přidejte úlohu **MSBuild**.

   ![Přidat úkol MSBuild fronta agenta](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-msbuild-task.png)

9. Změňte **Argumenty MSBuild** takto:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![Konfigurace úlohy nástroje MSBuild v DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-config-msbuild-task.png)

10. Ve **Fázi 1** klikněte na tlačítko **+** a přidejte úlohu **nasazení skupiny prostředků Azure**. 
    
    ![Přidání úlohy Nasazení skupiny prostředků Azure](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-add-resource-group-deployment.png)

11. Rozbalte **Podrobnosti Azure** a zadejte následující hodnoty:
    
    |**Nastavení**  |**Navrhovaná hodnota**  |
    |---------|---------|
    |Předplatné  |  Zvolte vaše předplatné.   |
    |Akce  |  Vytvoření nebo aktualizace skupiny prostředků   |
    |Skupina prostředků  |  Zadejte název skupiny prostředků.   |
    |Šablona  | [Cesta k vašemu řešení] \bin\Debug\Deploy\\[Název projektu]. JobTemplate.json   |
    |Parametry šablony  | [Cesta k vašemu řešení] \bin\Debug\Deploy\\[Název projektu]. JobTemplate.parameters.json   |
    |Přepsání parametrů šablony  | Do textového pole zadejte parametry šablony, které chcete přepsat. Například –storageName fabrikam –adminUsername $(vmusername) –adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Tato vlastnost je volitelná, ale pokud klíčové parametry nepřepíšete, sestavení skončí s chybou.    |
    
    ![Nastavení vlastností pro nasazení skupiny prostředků Azure](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deployment-properties.png)

12. Kliknutím na **Uložit a zařadit do fronty** kanál buildu otestujte.
    
    ![Uložit a fronty sestavení v DevOps](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-and-queue-build.png)

### <a name="failed-build-process"></a>Selhání procesu sestavení
Pokud nepřepíšete parametry šablony v úloze **nasazení skupiny prostředků Azure** kanálu buildu, může dojít k chybám kvůli parametrům nasazení s hodnotou null. Tuto chybu opravíte tak, že se vrátíte ke kanálu buildu a přepíšete parametry s hodnotou null.

   ![Proces se nepovedlo sestavení DevOps Stream Analytics](./media/stream-analytics-tools-visual-studio-cicd-vsts/devops-build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Potvrzení a nasdílení změn s cílem aktivace vydání
Ověřte fungování kanálu kontinuální integrace tím, že do Azure DevOps vrátíte se změnami nějaký kód.    

Při psaní kódu sada Visual Studio automaticky sleduje provedené změny. Potvrďte změny do svého místního úložiště Git výběrem ikony probíhajících změn na stavovém řádku vpravo dole.

1. V zobrazení **Změny** v Team Exploreru přidejte zprávu s popisem vaší aktualizace a potvrďte provedené změny.

    ![Potvrzení změn úložiště ze sady Visual Studio](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-commit-changes-visual-studio.png)

2. Vyberte na stavovém řádku nebo v zobrazení Synchronizace v Team Exploreru ikonu nepublikovaných změn. Výběrem možnosti **Nasdílet změny** aktualizujte kód v Azure DevOps.

    ![Odešlete změny ze sady Visual Studio](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-visual-studio.png)

Nasdílením změn do Azure DevOps Services se automaticky aktivuje build.  Po úspěšném dokončení kanálu buildu se automaticky vytvoří verze a začne se aktualizovat úloha v clusteru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto kurzu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na název vytvořeného prostředku.  
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další postup

Další informace o použití nástrojů Azure Stream Analytics pro Visual Studio k nastavení průběžné integrace a procesu nasazení najdete v pokračování popisujícím nastavení kanálu CI/CD:

> [!div class="nextstepaction"]
> [Průběžná integrace a vývoj s použitím nástrojů Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)
