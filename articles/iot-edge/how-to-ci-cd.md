---
title: Azure IoT Edge průběžnou integraci a průběžné nasazování | Dokumentace Microsoftu
description: Přehled průběžné integrace a průběžného nasazování pro Azure IoT Edge
author: shizn
manager: ''
ms.author: xshi
ms.date: 11/12/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: a110c0a938e56c8ac276e0efed22ea3af23f111a
ms.sourcegitcommit: 0fc99ab4fbc6922064fc27d64161be6072896b21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51578531"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Průběžná integrace a průběžné nasazování do Azure IoT Edge

Tento článek popisuje, jak můžete použít tak průběžnou integraci a průběžné nasazování funkce služeb Azure DevOps a Microsoft Team Foundation Server (TFS) pro vytváření, testování a rychle a efektivně nasadit aplikace pro vaše Azure IoT Edge. 

V tomto článku se dozvíte, jak:
* Vytvoření a vrátit se změnami ukázkové řešení IoT Edge.
* Nainstalujte rozšíření Azure IoT Edge pro Azure DevOps.
* Nakonfiguruje kontinuální integraci (CI), abyste mohli sestavit řešení.
* Konfigurace průběžného nasazování (CD) k nasazení řešení a zobrazování odpovědí.

Bude trvat 30 minut na dokončení kroků v tomto článku.

![CI a CD](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Vytvoření ukázkové řešení Azure IoT Edge pomocí Visual Studio Code

V této části vytvoříte ukázkové hraničních zařízeních IoT řešení obsahující testy jednotek, které můžete spouštět jako součást procesu sestavení. Než budete postupovat pokyny v této části, proveďte kroky v [vývoj řešení IoT Edge s několika moduly v aplikaci Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. Paleta příkazů VS Code, zadejte a spusťte příkaz **Azure IoT Edge: nový IoT Edge řešení**. Vyberte si složku pracovního prostoru, zadejte název řešení (výchozí název je **EdgeSolution**) a vytvořit modulu jazyka C# (**FilterModule**) jako první modul uživatele v tomto řešení. Bude také nutné zadat úložiště imagí Dockeru pro první modul. Výchozí úložiště imagí vychází z místního registru Dockeru (`localhost:5000/filtermodule`). Je třeba změnit ho do služby Azure Container Registry (`<your container registry address>/filtermodule`) nebo centra Dockeru pro další průběžnou integraci.

    ![Nastavení služby ACR](./media/how-to-ci-cd/acr.png)

2. Okna nástroje VS Code se načte pracovní prostor řešení IoT Edge. Volitelně zadejte a spustit **Azure IoT Edge: modul IoT Edge přidat** přidáte další moduly. Je `modules` složku, `.vscode` složku a soubor manifestu šablony nasazení v kořenové složce. Všechny kódy modulu uživatele bude podsložky ve složce `modules`. `deployment.template.json` Je v šabloně manifestu nasazení. Některé parametry v tomto souboru se získá analýzou z `module.json`, která existuje v všechny složky, které modul.

3. Ukázku řešení IoT Edge je teď připravený. Výchozí C# modul funguje jako zprávy modulu kanálu. V `deployment.template.json`, uvidíte toto řešení obsahuje dva moduly. Zpráva se budou generovat z `tempSensor` modulu a budou směrované přímo prostřednictvím `FilterModule`, pak odešlou do služby IoT hub.

4. Uložte tyto projekty a potom vrátit se změnami do úložiště Azure DevOps nebo TFS.
    
> [!NOTE]
> Další informace o použití úložiště Azure najdete v tématu [sdílení kódu pomocí sady Visual Studio a úložiště Azure](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-azure-pipeline-for-continuous-integration"></a>Nakonfigurujte Azure kanál pro průběžnou integraci
V této části vytvoříte kanál sestavení, která je nakonfigurována na automatické spuštění při vrácení se změnami změny ukázkové řešení IoT Edge a zobrazí sestavení zaznamená v kanálu Azure.

1. Přihlaste se k vaší organizaci Azure DevOps (**https://**_svůj účet_**. visualstudio.com**) a otevřete projekt, ve kterém můžete zkontrolovat v ukázkové aplikaci.

    ![Vrácení se změnami kódu](./media/how-to-ci-cd/init-project.png)

1. Navštivte [Azure IoT Edge pro Azure kanál](https://marketplace.visualstudio.com/items?itemName=vsc-iot.iot-edge-build-deploy) na Azure DevOps Marketplace. Klikněte na tlačítko **získání bezplatného** a postupujte podle pokynů průvodce a nainstalujte toto rozšíření pro vaši organizaci Azure DevOps nebo ke stažení pro TFS.

    ![Instalace rozšíření](./media/how-to-ci-cd/install-extension.png)

1. V Azure DevOps, otevřete **sestavení a vydání** hub a v **sestavení** kartě **+ nový kanál**. Nebo, pokud již máte kanály pro sestavování, vyberte **+ nová** tlačítko.

    ![Nový kanál](./media/how-to-ci-cd/add-new-build.png)

1. Pokud se zobrazí výzva, vyberte **Azure DevOps Git** typ zdroje. Vyberte projekt, úložiště a větev, ve kterém se nachází váš kód. Zvolte **pokračovat**.

    ![Vyberte git](./media/how-to-ci-cd/select-vsts-git.png)

    V **vyberte šablonu** okně zvolte **začněte s prázdným procesem**.

    ![Vybrat šablonu](./media/how-to-ci-cd/start-with-empty.png)

1. V editoru kanálu zvolte fond agentů. 
    
    * Pokud chcete vytvářet moduly v amd64 platformu pro kontejnery Linuxu, zvolte **hostované 1604 Ubuntu**
    * Pokud chcete vytvářet moduly v amd64 platformu pro kontejnery Windows, zvolte **hostované VS2017** 
    * Pokud chcete vytvářet moduly v arm32v7 platformu pro kontejnery Linuxu, budete muset sadu si vlastního agenta sestavení kliknutím **spravovat** tlačítko.
    
    ![Konfigurace agenta sestavení](./media/how-to-ci-cd/configure-env.png)

1. Úlohy agenta, klikněte na tlačítko "+" a přidejte dvě úlohy v kanálu sestavení. První z nich je z **Azure IoT Edge**. A druhý je z **publikujte artefakty sestavení**
    
    ![Přidat úlohy](./media/how-to-ci-cd/add-tasks.png)

1. V prvním **Azure IoT Edge** úkolů, aktualizujte **zobrazovaný název** k **modul sestavení a nabízených oznámení**a v **akce** rozevíracího seznamu vyberte možnost **Vytváření a nasdílení změn**. V **Module.json souboru** textové pole, přidat následující cestu k němu. Klikněte na tlačítko **typ registru kontejneru**, ujistěte se, že nakonfigurujete a vyberte stejné registr ve vaší code(module.json). Tato úloha bude sestavovat a push všechny moduly v řešení a publikování do registru kontejneru, který jste zadali. Pokud moduly se vloží do různých registrů, můžete mít více **modul sestavení a Push** úlohy. V případě, že řešení IoT Edge není v kořenovém adresáři vašeho úložiště kódu, můžete zadat **kořen řešení cestě z okraje** v definici sestavení.
    
    ![Vytváření a nasdílení změn](./media/how-to-ci-cd/build-and-push.png)

1. V **publikujte artefakty sestavení** úkolu, zadali byste nasazení soubor generovaný nástrojem pro úkol sestavení. Nastavte **cesta k publikování** na "config/deployment.json". Pokud nastavíte **kořen řešení cestě z okraje** v posledním úkolem, budete muset připojit v kořenové cestě. Například, pokud cesta Edge kořen řešení je ". / edgesolution", pak bude **cesta k publikování** by měl být ". / edgesolution/config/deployment.json". `deployment.json` Soubor se generuje během doby sestavení, takže je bezpečné ignorovat červenou chybovou řádků do textového pole. 

    ![Publikování artefaktů](./media/how-to-ci-cd/publish-build-artifacts.png)

1. Otevřít **aktivační události** kartu a zapnout **kontinuální integrace** aktivační události. Ujistěte se, že větev, která obsahuje kód je součástí.

    ![Konfigurace aktivační události](./media/how-to-ci-cd/configure-trigger.png)

    Uložte nový kanál sestavení. Klikněte na tlačítko **Uložit**.


## <a name="configure-azure-pipeline-for-continuous-deployment"></a>Nakonfigurujte Azure kanál pro průběžné nasazování
V této části vytvoříte kanál pro vydávání verzí, která je nakonfigurována na automatické spuštění při vašeho kanálu sestavení zahodí artefakty a zobrazí protokolech nasazení v kanálu Azure.

1. V **verze** kartě **+ nový kanál**. Nebo, pokud již máte kanály pro vydávání, vyberte **+ nová** tlačítko.  

    ![Přidejte kanál pro vydávání verzí](./media/how-to-ci-cd/add-release-pipeline.png)

    V **vyberte šablonu** okně zvolte **začít s prázdnou úlohu.**

    ![Začít s prázdnou úlohu](./media/how-to-ci-cd/start-with-empty-job.png)

2. Potom bude kanál pro vydávání verzí inicializovat pomocí jedné fáze: **fáze 1**. Přejmenovat **fáze 1** k **QA** a ji považovat za testovací prostředí. V kanálu typické průběžné nasazování obvykle existuje více fázích, můžete vytvořit více založené na vaše postupy DevOps.

    ![Vytvořit fázi](./media/how-to-ci-cd/QA-env.png)

3. Odkaz na verzi na artefakty sestavení. Klikněte na tlačítko **přidat** v oblasti artefakty.

    ![Přidat artefakty](./media/how-to-ci-cd/add-artifacts.png)  
    
    V **přidání artefaktu stránky**, zvolte typ zdroje **sestavení**. Vyberte projekt a sestavení kanál, který jste vytvořili. Pak klikněte na tlačítko **přidat**.

    ![Přidání artefaktu](./media/how-to-ci-cd/add-an-artifact.png)

    Trigger průběžného nasazování otevřete tak, aby se nová verze vytvoří pokaždé, když je k dispozici nové sestavení.

    ![Konfigurace aktivační události](./media/how-to-ci-cd/add-a-trigger.png)

4. Přejděte do **QA fázi** a nakonfigurovat úlohy v této fázi.

    ![Konfigurace úloh dotazů a odpovědí](./media/how-to-ci-cd/view-stage-tasks.png)

   Úloha nasazení je platforma malá a velká písmena, což znamená, že můžete použít buď **hostované VS2017** nebo **hostované 1604 Ubuntu** v **fondu Agentských** (nebo libovolného agenta, které spravuje sami sebe). Klikněte na tlačítko "+" a přidejte jeden úkol.

    ![Přidání úkolů pro kontrolu kvality](./media/how-to-ci-cd/add-task-qa.png)

5. V úloze Azure IoT Edge, přejděte **akce** rozevíracího seznamu vyberte **nasadit do zařízení IoT Edge**. Vyberte vaše **předplatného Azure** a vstupní vaše **název centra IoT**. Můžete zadat IoT Edge **ID nasazení** a nasazení **priority**. Můžete také nasadit do jedné nebo více zařízení. Pokud provádíte nasazení do **více zařízení**, je třeba zadat zařízení **cílová podmínka**. Cílová podmínka se filtr tak, aby odpovídaly sadu hraničních zařízení ve službě IoT Hub. Pokud chcete použít značky zařízení jako podmínka, musíte aktualizovat dvojče zařízení služby IoT Hub zařízení odpovídající značky. Předpokládejme, že máte několik IoT Edge zařízení byla označena jako "qa", pak by měl být konfigurace úlohy jako na následujícím snímku obrazovky. 

    ![Nasazení do dotazů a odpovědí](./media/how-to-ci-cd/deploy-to-qa.png)

    Uložte nový kanál pro vydávání verzí. Klikněte na tlačítko **Uložit**. A pak klikněte na tlačítko **kanálu** se vrátíte do kanálu.

6. Druhá fáze je pro vaše produkční prostředí. Chcete-li přidat nový fáze "Produkční", můžete jednoduše klonovat fáze "QA" a přejmenovat naklonované fázi **produkční**,

    ![Klonovat fáze](./media/how-to-ci-cd/clone-stage.png)

7. Nakonfigurujte úlohy pro vaše produkční prostředí. Předpokládejme, že máte několik IoT Edge zařízení byla označena jako "produkční", v konfiguracích úlohy aktualizovat cílovou podmínku "produkční" a nastavit id nasazení jako "nasazení prod". Klikněte na tlačítko **Uložit**. A pak klikněte na tlačítko **kanálu** se vrátíte do kanálu.
    
    ![Nasazení do produkčního prostředí](./media/how-to-ci-cd/deploy-to-prod.png)

7. V současné době naše artefakt sestavení aktivuje se průběžné **QA** fáze a potom **produkční** fázi. Ale většině případů je potřeba integrovat některé testovací případy na zařízeních, dotazů a odpovědí a ručně schválit bity. Bity později se nasadí do PRODUKČNÍHO prostředí. Žádost o schválení v produkční fázi následující nastavení.

    1. Otevřít **podmínky před nasazením** panel nastavení.

        ![Otevřít před nasazením podmínky](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Nastavte **povoleno** v **schválení před nasazením**. A vyplňte **schvalovatelů** vstupu. Potom klikněte na **Uložit**.
    
        ![Nastavení podmínek](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. Nyní kanálu pro vydávání verzí je nastavený jako následující.

    ![Kanál pro vydávání verzí](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Ověřte IoT Edge CI/CD s sestavení a vydávání kanálů

V této části se aktivují sestavení, aby kanál CI/CD fungovat. Ověřte výsledek pomocí portálu Azure DevOps. 

1. K aktivaci úlohy sestavení, můžete potvrdíte a vložíte změny do úložiště zdrojového kódu nebo ruční spuštění. Úlohu sestavení a ve vašem kanálu sestavení můžete aktivovat kliknutím **fronty** tlačítko stejně jako v následujícím snímku obrazovky.

    ![Ruční aktivační události](./media/how-to-ci-cd/manual-trigger.png)

2. Pokud kanál sestavení bylo úspěšně dokončeno, bude vyvolal vydání do **QA** fázi. Přejděte do kanálu protokoly o sestavení a měli byste vidět následující.

    ![Protokoly o sestavení](./media/how-to-ci-cd/build-logs.png)

3. Úspěšné nasazení **QA** fáze aktivuje oznámení schvalovateli. Přejděte na kanál verze, zobrazí se následující. 

    ![Čeká na schválení](./media/how-to-ci-cd/pending-approval.png)


4. Jakmile schvalující schválit tuto změnu, je možné nasadit do **produkční**.

    ![Nasazení do produkčního prostředí](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>Další postup

* Vysvětlení nasazení IoT Edge v [vysvětlení nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md)
* Provede kroky k vytvoření, aktualizace nebo odstranění nasazení v [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).
