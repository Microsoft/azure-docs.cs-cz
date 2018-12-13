---
title: Průběžná integrace a průběžné nasazování – Azure IoT Edge | Dokumentace Microsoftu
description: Nastavte průběžnou integraci a průběžné nasazování – Azure IoT Edge s Azure DevOps, Azure kanály
author: shizn
manager: philmea
ms.author: xshi
ms.date: 11/29/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 4db5fce89df0b5974261788608b785cf16917f1a
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/08/2018
ms.locfileid: "53074789"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Průběžná integrace a průběžné nasazování do Azure IoT Edge

Můžete snadno přijmout DevOps s vašimi aplikacemi Azure IoT Edge integrované úlohy Azure IoT Edge v kanálech Azure nebo [modul plug-in Azure IoT Edge pro Jenkinse](https://plugins.jenkins.io/azure-iot-edge) na serveru Jenkins. Tento článek ukazuje, jak můžete pomocí průběžné integrace a průběžného nasazování funkce kanály Azure a Azure DevOps Server sestavení, testování a rychle a efektivně nasadit aplikace pro vaše Azure IoT Edge. 

V tomto článku se dozvíte, jak:
* Vytvoření a vrátit se změnami ukázkové řešení IoT Edge.
* Nakonfiguruje kontinuální integraci (CI), abyste mohli sestavit řešení.
* Konfigurace průběžného nasazování (CD) k nasazení řešení a zobrazování odpovědí.

Bude trvat 20 minut, než k dokončení kroků v tomto článku.

![Diagram – CI a CD větve pro vývoj a provoz](./media/how-to-ci-cd/cd.png)


## <a name="create-a-sample-azure-iot-edge-solution-using-visual-studio-code"></a>Vytvoření ukázkové řešení Azure IoT Edge pomocí Visual Studio Code

V této části vytvoříte ukázkové hraničních zařízeních IoT řešení obsahující testy jednotek, které můžete spouštět jako součást procesu sestavení. Než budete postupovat pokyny v této části, proveďte kroky v [vývoj řešení IoT Edge s několika moduly v aplikaci Visual Studio Code](tutorial-multiple-modules-in-vscode.md).

1. Paleta příkazů VS Code, zadejte a spusťte příkaz **Azure IoT Edge: nový IoT Edge řešení**. Vyberte si složku pracovního prostoru, zadejte název řešení (výchozí název je **EdgeSolution**) a vytvořit modulu jazyka C# (**FilterModule**) jako první modul uživatele v tomto řešení. Bude také nutné zadat úložiště imagí Dockeru pro první modul. Výchozí úložiště imagí vychází z místního registru Dockeru (`localhost:5000/filtermodule`). Změňte ho na Azure Container Registry (`<your container registry address>/filtermodule`) nebo centra Dockeru pro další průběžnou integraci.

    ![Nastavení Azure Container Registry](./media/how-to-ci-cd/acr.png)

2. Okna nástroje VS Code se načte pracovní prostor řešení IoT Edge. Volitelně zadejte a spustit **Azure IoT Edge: modul IoT Edge přidat** přidáte další moduly. Je `modules` složku, `.vscode` složku a soubor manifestu šablony nasazení v kořenové složce. Všechny kódy modulu uživatele bude podsložky ve složce `modules`. `deployment.template.json` Je v šabloně manifestu nasazení. Některé parametry v tomto souboru se získá analýzou z `module.json`, která existuje v všechny složky, které modul.

3. Ukázku řešení IoT Edge je teď připravený. Výchozí C# modul funguje jako zprávy modulu kanálu. V `deployment.template.json`, uvidíte toto řešení obsahuje dva moduly. Zpráva se budou generovat z `tempSensor` modulu a budou směrované přímo prostřednictvím `FilterModule`, pak odešlou do služby IoT hub.

4. Uložte tyto projekty a potom vrátit se změnami do úložiště Azure nebo Azure DevOps serveru úložiště.
    
> [!NOTE]
> Další informace o použití úložiště Azure najdete v tématu [sdílení kódu pomocí sady Visual Studio a úložiště Azure](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts).


## <a name="configure-azure-pipelines-for-continuous-integration"></a>Konfigurovat kanály Azure pro průběžnou integraci
V této části vytvoříte kanál sestavení, která je nakonfigurována na automatické spuštění při vrácení se změnami změny ukázkové řešení IoT Edge a zobrazí sestavení zaznamená v kanálech Azure.

1. Přihlaste se k vaší organizaci Azure DevOps ( **https://dev.azure.com/{your organizace} /**) a otevřete projekt, ve kterém můžete zkontrolovat v ukázkové aplikaci.

    ![Vrácení se změnami kódu kanály Azure](./media/how-to-ci-cd/init-project.png)

1. V Azure kanálů, otevřete **sestavení** kartě **+ nový kanál**. Nebo, pokud již máte kanály pro sestavování, vyberte **+ nová** tlačítko. Potom vyberte **nové vytvoření kanálu**.

    ![Vytvoření nového kanálu sestavení](./media/how-to-ci-cd/add-new-build.png)

1. Pokud se zobrazí výzva, vyberte **Azure DevOps Git** typ zdroje. Vyberte projekt, úložiště a větev, ve kterém se nachází váš kód. Zvolte **pokračovat**.

    ![Vyberte Azure úložišť Git](./media/how-to-ci-cd/select-vsts-git.png)

    V **vyberte šablonu** okně zvolte **začněte s prázdným procesem**.

    ![Začněte s prázdným procesem.](./media/how-to-ci-cd/start-with-empty.png)

1. V editoru kanálu zvolte fond agentů. 
    
    * Pokud chcete vytvářet moduly v amd64 platformu pro kontejnery Linuxu, zvolte **hostované 1604 Ubuntu**
    * Pokud chcete vytvářet moduly v amd64 platformu pro kontejnery Windows, zvolte **hostované VS2017** 
    * Pokud chcete vytvářet moduly v arm32v7 platformu pro kontejnery Linuxu, budete muset sadu si vlastního agenta sestavení kliknutím **spravovat** tlačítko.
    
    ![Konfigurace fondu agentů sestavení](./media/how-to-ci-cd/configure-env.png)

1. Úlohy agenta, klikněte na tlačítko "+" a přidejte tři úkoly v kanálu sestavení. První dvě jsou z **Azure IoT Edge**. A třetí příkaz je z **publikujte artefakty sestavení**
    
    ![Přidání úkolů do kanálu sestavení](./media/how-to-ci-cd/add-tasks.png)

1. V prvním **Azure IoT Edge** úkolů, aktualizujte **zobrazovaný název** k **Azure IoT Edge – bitové kopie sestavení modulu**a v **akce** rozevíracího seznamu Vyberte možnost **vytváření bitové kopie modulu**. V **. soubor template.json** ovládací prvek, vyberte **deployment.template.json** soubor, který popisuje vaše řešení IoT Edge. Klikněte na tlačítko **výchozí platformu**, ujistěte se, že vyberete jako zařízení IoT Edge stejnou platformu. Tato úloha se sestaví všechny moduly v řešení s cílovou platformu, kterou jste zadali. A vygenerovat také **deployment.json** souboru, cesta k souboru můžete najít ve výstupní proměnné. Nastavte alias na `edge` pro tuto proměnnou.
    
    ![Konfigurace úlohy bitové kopie modulu sestavení](./media/how-to-ci-cd/build-and-push.png)

1. Ve druhém **Azure IoT Edge** úkolů, aktualizujte **zobrazovaný název** k **Azure IoT Edge – bitové kopie modulu nabízených**a v **akce** rozevíracího seznamu Vyberte možnost **Push bitové kopie modulu**. Zvolte typ registru kontejneru, ujistěte se, že nakonfigurujete a vyberte stejné registr ve vaší code(module.json). V **. soubor template.json** ovládací prvek, vyberte **deployment.template.json** soubor, který popisuje vaše řešení IoT Edge. Klikněte na tlačítko **výchozí platformu**, ujistěte se, že vyberete stejnou platformu pro vaše Image sestavené modulu. Tato úloha zařadí všechny bitové kopie modulu do registru kontejneru, který jste vybrali. A také přidat přihlašovací údaje registru kontejneru v **deployment.json** souboru, cesta k souboru můžete najít ve výstupní proměnné. Nastavte alias na `edge` pro tuto proměnnou. Pokud máte více registrů kontejnerů k hostování vaší bitové kopie modulu, budete muset Duplikovat tuto úlohu, vyberte jiný registr kontejneru a použijte **obejít modulu nebo modulech** v upřesňujících nastaveních obejít imagí, které nejsou pro tuto konkrétního registru.

    ![Nakonfigurovat úlohu imagí modul nabízených oznámení](./media/how-to-ci-cd/push.png)

1. V **publikujte artefakty sestavení** úkolu, zadali byste nasazení soubor generovaný nástrojem pro úkol sestavení. Nastavte **cesta k publikování** k `$(edge.DEPLOYMENT_FILE_PATH)`.

    ![Konfigurace publikování artefaktů úloh](./media/how-to-ci-cd/publish-build-artifacts.png)

1. Otevřít **aktivační události** kartu a zapnout **kontinuální integrace** aktivační události. Ujistěte se, že větev, která obsahuje kód je součástí.

    ![Zapnout trigger průběžné integrace](./media/how-to-ci-cd/configure-trigger.png)

    Uložte nový kanál sestavení. Klikněte na tlačítko **Uložit**.


## <a name="configure-azure-pipelines-for-continuous-deployment"></a>Konfigurovat kanály Azure pro průběžné nasazování
V této části vytvoříte kanál pro vydávání verzí, která je nakonfigurována na automatické spuštění při vašeho kanálu sestavení zahodí artefakty a zobrazí protokolech nasazení v kanálech Azure.

1. V **verze** kartě **+ nový kanál**. Nebo, pokud již máte kanály pro vydávání, vyberte **+ nová** tlačítko.  

    ![Přidejte kanál pro vydávání verzí](./media/how-to-ci-cd/add-release-pipeline.png)

    V **vyberte šablonu** okně zvolte **začít s prázdnou úlohu.**

    ![Začít s prázdnou úlohu](./media/how-to-ci-cd/start-with-empty-job.png)

2. Potom bude kanál pro vydávání verzí inicializovat pomocí jedné fáze: **fáze 1**. Přejmenovat **fáze 1** k **QA** a ji považovat za testovací prostředí. V kanálu typické průběžné nasazování obvykle existuje více fázích, můžete vytvořit více založené na vaše postupy DevOps.

    ![Vytvořit testovací prostředí fáze](./media/how-to-ci-cd/QA-env.png)

3. Odkaz na verzi na artefakty sestavení. Klikněte na tlačítko **přidat** v oblasti artefakty.

    ![Přidat artefakty](./media/how-to-ci-cd/add-artifacts.png)  
    
    V **přidání artefaktu stránky**, zvolte typ zdroje **sestavení**. Vyberte projekt a sestavení kanál, který jste vytvořili. Pak klikněte na tlačítko **přidat**.

    ![Přidejte artefakt sestavení](./media/how-to-ci-cd/add-an-artifact.png)

    Trigger průběžného nasazování otevřete tak, aby se nová verze vytvoří pokaždé, když je k dispozici nové sestavení.

    ![Konfigurace aktivační události pro průběžné nasazování](./media/how-to-ci-cd/add-a-trigger.png)

4. Přejděte do **QA fázi** a nakonfigurovat úlohy v této fázi.

    ![Konfigurace úloh dotazů a odpovědí](./media/how-to-ci-cd/view-stage-tasks.png)

   Úloha nasazení je platforma malá a velká písmena, což znamená, že můžete použít buď **hostované VS2017** nebo **hostované 1604 Ubuntu** v **fondu Agentských** (nebo libovolného agenta, které spravuje sami sebe). Klikněte na tlačítko "+" a přidejte jeden úkol.

    ![Přidání úkolů pro kontrolu kvality](./media/how-to-ci-cd/add-task-qa.png)

5. V úloze Azure IoT Edge, přejděte **akce** rozevíracího seznamu vyberte **nasadit do zařízení IoT Edge**. Vyberte vaše **předplatného Azure** a vstupní vaše **název centra IoT**. Můžete nasadit do jedné nebo více zařízení. Pokud provádíte nasazení do **více zařízení**, je třeba zadat zařízení **cílová podmínka**. Cílová podmínka se filtr tak, aby odpovídaly sadu hraničních zařízení ve službě IoT Hub. Pokud chcete použít značky zařízení jako podmínka, musíte aktualizovat dvojče zařízení služby IoT Hub zařízení odpovídající značky. Aktualizace **ID nasazení IoT Edge** "nasazení – qa" v upřesňujících nastavení. Předpokládejme, že máte několik IoT Edge zařízení byla označena jako "qa", pak by měl být konfigurace úlohy jako na následujícím snímku obrazovky. 

    ![Nasazení do dotazů a odpovědí](./media/how-to-ci-cd/deploy-to-qa.png)

    Uložte nový kanál pro vydávání verzí. Klikněte na tlačítko **Uložit**. A pak klikněte na tlačítko **kanálu** se vrátíte do kanálu.

6. Druhá fáze je pro vaše produkční prostředí. Chcete-li přidat nový fáze "Produkční", můžete klonovat fáze "QA" a přejmenovat naklonované fázi **produkční**,

    ![Klonovat fáze](./media/how-to-ci-cd/clone-stage.png)

7. Nakonfigurujte úlohy pro vaše produkční prostředí. Předpokládejme, že máte několik IoT Edge zařízení byla označena jako "produkční", v konfiguracích úlohy aktualizovat cílovou podmínku "produkční" a nastavit ID nasazení jako "nasadit prod" v části Upřesnit nastavení. Klikněte na tlačítko **Uložit**. A pak klikněte na tlačítko **kanálu** se vrátíte do kanálu.
    
    ![Nasazení do produkčního prostředí](./media/how-to-ci-cd/deploy-to-prod.png)

7. V současné době naše artefakt sestavení aktivuje se průběžné **QA** fáze a potom **produkční** fázi. Ale většině případů je potřeba integrovat některé testovací případy na zařízeních, dotazů a odpovědí a ručně schválit bity. Bity později se nasadí do PRODUKČNÍHO prostředí. Nastavení schválení v produkční fázi jako na následujícím snímku obrazovky.

    1. Otevřít **podmínky před nasazením** panel nastavení.

        ![Otevřít před nasazením podmínky](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Nastavte **povoleno** v **schválení před nasazením**. A vyplňte **schvalovatelů** vstupu. Potom klikněte na **Uložit**.
    
        ![Nastavení podmínek](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


8. Nyní kanálu pro vydávání verzí je nastavený jako následující snímek obrazovky.

    ![Kanál pro vydávání verzí](./media/how-to-ci-cd/release-pipeline.png)

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Ověřte IoT Edge CI/CD s sestavení a vydávání kanálů

V této části se aktivují sestavení, aby kanál CI/CD fungovat. Ověřte, že bude nasazení úspěšné.

1. K aktivaci úlohy sestavení, můžete potvrdíte a vložíte změny do úložiště zdrojového kódu nebo ruční spuštění. Úlohu sestavení a ve vašem kanálu sestavení můžete aktivovat kliknutím **fronty** tlačítko stejně jako v následujícím snímku obrazovky.

    ![Ruční aktivační události](./media/how-to-ci-cd/manual-trigger.png)

2. Pokud kanál sestavení bylo úspěšně dokončeno, bude vyvolal vydání do **QA** fázi. Přejděte do kanálu protokoly o sestavení a měli byste vidět na následujícím snímku obrazovky.

    ![Protokoly o sestavení](./media/how-to-ci-cd/build-logs.png)

3. Úspěšné nasazení **QA** fáze aktivuje oznámení schvalovateli. Přejděte na kanál verze, vidíte na následujícím snímku obrazovky. 

    ![Čeká na schválení](./media/how-to-ci-cd/pending-approval.png)


4. Jakmile schvalující schválit tuto změnu, je možné nasadit do **produkční**.

    ![Nasazení do produkčního prostředí](./media/how-to-ci-cd/approve-and-deploy-to-prod.png)


## <a name="next-steps"></a>Další postup

* Vysvětlení nasazení IoT Edge v [vysvětlení nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md)
* Provede kroky k vytvoření, aktualizace nebo odstranění nasazení v [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).
