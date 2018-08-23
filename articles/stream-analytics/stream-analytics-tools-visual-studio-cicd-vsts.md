---
title: Kurz nasazení úlohy Azure Stream Analytics s CI/CD pomocí VSTS
description: Tento článek popisuje, jak nasadit úlohu Stream Analytics s CI/CD pomocí VSTS.
services: stream-analytics
author: su-jie
ms.author: sujie
manager: kfile
ms.reviewer: mamccrea
ms.service: stream-analytics
ms.topic: tutorial
ms.date: 7/10/2018
ms.openlocfilehash: d4f1e188a1a145ba3be5fb45d2b0ea4d0bfd57a7
ms.sourcegitcommit: 4ea0cea46d8b607acd7d128e1fd4a23454aa43ee
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/15/2018
ms.locfileid: "41918185"
---
# <a name="tutorial-deploy-an-azure-stream-analytics-job-with-cicd-using-vsts"></a>Kurz: Nasazení úlohy Azure Stream Analytics s CI/CD pomocí VSTS
Tento kurz popisuje, jak nastavit kontinuální integraci a nasazení pro úlohu Azure Stream Analytics s použitím Visual Studio Team Services. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání správy zdrojového kódu do projektu
> * Vytvoření definice sestavení v Team Services
> * Vytvoření definice verze v Team Services
> * Automatické nasazení a upgrade aplikace

## <a name="prerequisites"></a>Požadavky
Než začnete, ujistěte se, že jste provedli následující akce:

* Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* Nainstalujte [Visual Studio](stream-analytics-tools-for-visual-studio-install.md) a úlohy **Vývoj pro Azure** nebo **Ukládání a zpracování dat**.
* Vytvořte [projekt Stream Analytics v sadě Visual Studio](https://docs.microsoft.com/azure/stream-analytics/stream-analytics-quick-create-vs).
* Vytvořte [účet služby Visual Studio Team Services](https://visualstudio.microsoft.com/team-services/).

## <a name="configure-nuget-package-dependency"></a>Nakonfigurujte závislost balíčku NuGet
Pokud chcete provádět automatické sestavení a automatické nasazení na libovolný počítač, je potřeba použít balíček NuGet `Microsoft.Azure.StreamAnalytics.CICD`. Poskytuje nástroj MSBuild, místní spuštění a nástroje pro nasazení, které podporují kontinuální integraci a proces nasazení projektů Stream Analytics v sadě Visual Studio. Další informace obsahuje článek o [nástrojích CI/CD pro Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md).

Přidejte **packages.config** do adresáře projektu.

```xml
<?xml version="1.0" encoding="utf-8"?>
<packages>
<package id="Microsoft.Azure.StreamAnalytics.CICD" version="1.0.0" targetFramework="net452" />
</packages>
```

## <a name="share-your-visual-studio-solution-to-a-new-team-services-git-repo"></a>Sdílení řešení sady Visual Studio do nového úložiště Team Services Git
Zdrojové soubory své aplikace můžete sdílet do týmového projektu v Team Services, abyste mohli generovat sestavení.  

1. Vytvořte pro svůj projekt nové místní úložiště Git tím, že na stavovém řádku v pravém dolním rohu sady Visual Studio vyberete **Přidat do správy zdrojového kódu** a pak **Git**. 

2. V zobrazení **Synchronizace** v **Team Exploreru** vyberte v části **Nasdílet do Visual Studio Team Services** tlačítko **Publikovat úložiště Git**.

   ![Nasdílení úložiště Git](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishgitrepo.png)

3. Ověřte svůj e-mail a v rozevíracím seznamu **Doména Team Services** vyberte svůj účet. Zadejte název svého úložiště a vyberte **Publikovat úložiště**.

   ![Nasdílení úložiště Git](./media/stream-analytics-tools-visual-studio-cicd-vsts/publishcode.png)

    Publikováním úložiště se ve vašem účtu vytvoří nový týmový projekt se stejným názvem jako místní úložiště. Pokud chcete úložiště vytvořit v existujícím týmovém projektu, klikněte na **Upřesnit** vedle **názvu úložiště** a vyberte týmový projekt. Svůj kód můžete zobrazit v prohlížeči výběrem možnosti **Podívejte se na webu**.
 
## <a name="configure-continuous-delivery-with-vsts"></a>Konfigurace průběžného doručování s VSTS
Definice sestavení Team Services popisuje pracovní postup, který se skládá z řady postupně prováděných kroků sestavení. Další informace o [definicích sestavení Team Services](https://www.visualstudio.com/docs/build/define/create). 

Definice verze Team Services popisuje pracovní postup, který nasadí balíček aplikace do clusteru. Při společném použití definice sestavení a definice verze provedou celý pracovní postup od zdrojových souborů až po spuštění aplikace v clusteru. Další informace o [definicích verzí](https://www.visualstudio.com/docs/release/author-release-definition/more-release-definition) Team Services.

### <a name="create-a-build-definition"></a>Vytvoření definice sestavení
Otevřete webový prohlížeč a přejděte do týmového projektu, který jste právě vytvořili ve službě [Visual Studio Team Services](https://app.vsaex.visualstudio.com/). 

1. Na kartě **Sestavení a vydání** vyberte **Sestavení** a nakonec **+Nové**.  Vyberte **VSTS Git** a **Pokračovat**.
    
    ![Výběr zdroje](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-source.png)

2. V části **Vyberte šablonu** kliknutím na **Prázdný proces** začněte od prázdné definice.
    
    ![Výběr šablony sestavení](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-select-template.png)

3. V části **Triggery** zaškrtněte stav **Povolit průběžnou integraci** a povolte tak průběžnou integraci.  Pokud chcete ručně spustit sestavení, vyberte **Uložit a zařadit do fronty**. 
    
    ![Stav triggeru](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-trigger.png)

4. Sestavení se aktivují také pro nasdílení změn nebo vrácení se změnami. Pokud chcete zkontrolovat průběh sestavení, přepněte na kartu **Sestavení**.  Jakmile ověříte, že se sestavení úspěšně provádí, je třeba určit definici verze, která nasadí vaši aplikaci do clusteru. Klikněte pravým tlačítkem na tři tečky vedle definice sestavení a vyberte **Upravit**.

5.  V části **Úlohy** zadejte do pole **Fronta agenta** „Hosted“.
    
    ![Výběr fronty agenta](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-agent-queue.png) 

6. Ve **Fázi 1** klikněte na tlačítko **+** a přidejte úlohu **NuGet**.
    
    ![Přidání úlohy NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget.png)

7. Rozbalte **Upřesnit** a přidejte `$(Build.SourcesDirectory)\packages` do **cílového adresáře**. Ponechejte zbývající výchozí hodnoty konfigurace NuGet.

   ![Konfigurace úlohy NuGet](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-nuget-config.png)

8. Ve **Fázi 1** klikněte na tlačítko **+** a přidejte úlohu **MSBuild**.

   ![Přidání úlohy MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild-task.png)

9. Změňte **Argumenty MSBuild** takto:

   ```
   /p:CompilerTaskAssemblyFile="Microsoft.WindowsAzure.StreamAnalytics.Common.CompileService.dll"  /p:ASATargetsFilePath="$(Build.SourcesDirectory)\packages\Microsoft.Azure.StreamAnalytics.CICD.1.0.0\build\StreamAnalytics.targets"
   ```

   ![Konfigurace úlohy MSBuild](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-msbuild.png)

10. Ve **Fázi 1** klikněte na tlačítko **+** a přidejte úlohu **nasazení skupiny prostředků Azure**. 
    
    ![Přidání úlohy Nasazení skupiny prostředků Azure](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy.png)

11. Rozbalte **Podrobnosti Azure** a zadejte následující hodnoty:
    
    |**Nastavení**  |**Navrhovaná hodnota**  |
    |---------|---------|
    |Předplatné  |  Zvolte vaše předplatné.   |
    |Akce  |  Vytvoření nebo aktualizace skupiny prostředků   |
    |Skupina prostředků  |  Zadejte název skupiny prostředků.   |
    |Šablona  | [Cesta k vašemu řešení] \bin\Debug\Deploy\\[Název projektu]. JobTemplate.json   |
    |Parametry šablony  | [Cesta k vašemu řešení] \bin\Debug\Deploy\\[Název projektu]. JobTemplate.parameters.json   |
    |Přepsání parametrů šablony  | Do textového pole zadejte parametry šablony, které chcete přepsat. Například –storageName fabrikam –adminUsername $(vmusername) –adminPassword $(password) –azureKeyVaultName $(fabrikamFibre). Tato vlastnost je volitelná, ale pokud klíčové parametry nepřepíšete, sestavení skončí s chybou.    |
    
    ![Nastavení vlastností](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-deploy-2.png)

12. Kliknutím na **Uložit a zařadit do fronty** otestujte definici sestavení.
    
    ![Nastavení přepsání parametrů](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-save-queue.png)

### <a name="failed-build-process"></a>Selhání procesu sestavení
Pokud nepřepíšete parametry šablony v úloze **nasazení skupiny prostředků Azure** vaší definice sestavení, může dojít k chybám parametrů nasazení s hodnotou null. Vraťte se k definici sestavení a přepište parametry null, abyste chybu vyřešili.

   ![Selhání procesu sestavení](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-process-failed.png)

### <a name="commit-and-push-changes-to-trigger-a-release"></a>Potvrzení a nasdílení změn s cílem aktivace vydání
Ověřte fungování kanálu průběžné integrace tím, že do Team Services vrátíte nějaké změny kódu.    

Při psaní kódu sada Visual Studio automaticky sleduje provedené změny. Potvrďte změny do svého místního úložiště Git výběrem ikony probíhajících změn na stavovém řádku vpravo dole.

1. V zobrazení **Změny** v Team Exploreru přidejte zprávu s popisem vaší aktualizace a potvrďte provedené změny.

    ![Potvrzení a nasdílení změn](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes.png)

2. Vyberte na stavovém řádku nebo v zobrazení Synchronizace v Team Exploreru ikonu nepublikovaných změn. Vyberte **Nasdílet změny** a aktualizujte svůj kód v Team Services nebo v sadě TFS.

    ![Potvrzení a nasdílení změn](./media/stream-analytics-tools-visual-studio-cicd-vsts/build-push-changes-2.png)

Nasdílením změn do Team Services se automaticky aktivuje sestavení.  Po úspěšném dokončení definice sestavení se automaticky vytvoří vydaná verze a začne se aktualizovat aplikace v clusteru.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Odstraňte skupinu prostředků, úlohu streamování a všechny související prostředky, pokud je už nepotřebujete. Odstraněním úlohy se zabrání zaúčtování jednotek streamování, které daná úloha spotřebovává. Pokud plánujete používat tuto úlohu v budoucnu, můžete ji zastavit a znovu ji spustit později, až ji budete potřebovat. Pokud nebudete tuto úlohu nadále používat, odstraňte všechny prostředky vytvořené podle tohoto kurzu pomocí následujícího postupu:

1. V nabídce vlevo na portálu Azure Portal klikněte na **Skupiny prostředků** a pak klikněte na název vytvořeného prostředku.  
2. Na stránce skupiny prostředků klikněte na **Odstranit**, do textového pole zadejte prostředek, který chcete odstranit, a pak klikněte na **Odstranit**.

## <a name="next-steps"></a>Další kroky

Další informace o použití nástrojů Azure Stream Analytics pro Visual Studio k nastavení průběžné integrace a procesu nasazení najdete v pokračování popisujícím nastavení kanálu CI/CD:

> [!div class="nextstepaction"]
> [Průběžná integrace a vývoj s použitím nástrojů Stream Analytics](stream-analytics-tools-for-visual-studio-cicd.md)