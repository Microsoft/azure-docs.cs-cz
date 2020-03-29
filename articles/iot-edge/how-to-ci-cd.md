---
title: Průběžná integrace & průběžného nasazení – Azure IoT Edge
description: Nastavení průběžné integrace a průběžného nasazování – Azure IoT Edge s Azure DevOps, Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 4b99e83a8e71b13183c76321c7076b85a212f021
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76510970"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Průběžná integrace a průběžné nasazování do Azure IoT Edge

DevOps můžete snadno přijmout s vašimi aplikacemi Azure IoT Edge s integrovanými úlohami Azure IoT Edge v Azure Pipelines. Tento článek ukazuje, jak můžete používat funkce průběžné integrace a průběžného nasazování Azure Pipelines k rychlému a efektivnímu vytváření, testování a nasazování aplikací do azure IoT Edge.

![Diagram - VĚTVE CI a CD pro vývoj a výrobu](./media/how-to-ci-cd/cd.png)

V tomto článku se dozvíte, jak používat integrované úlohy Azure IoT Edge pro Azure Pipelines k vytvoření dvou kanálů pro vaše řešení IoT Edge. Existují čtyři akce, které se dá použít v úlohách Azure IoT Edge.

* **Azure IoT Edge – image modulu sestavení** převezme kód řešení IoT Edge a vytvoří image kontejneru.
* **Azure IoT Edge – iimage push modulu** odešle ibi modulu do registru kontejneru, který jste zadali.
* **Azure IoT Edge – generování manifestu nasazení** přebírá soubor deployment.template.json a proměnné a pak generuje konečný soubor manifestu nasazení IoT Edge.
* **Azure IoT Edge – nasazení na zařízení IoT Edge** pomáhá vytvářet nasazení IoT Edge pro jedno a více zařízení IoT Edge.

## <a name="prerequisites"></a>Požadavky

* Úložiště Azure Repos. Pokud ho nemáte, můžete [v projektu vytvořit nové úložiště Gitu](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Řešení IoT Edge potvrzené a zasunuté do vašeho úložiště. Pokud chcete vytvořit nové ukázkové řešení pro testování tohoto článku, postupujte podle kroků v [modulu Vývoj a ladění v kódu sady Visual Studio](how-to-vs-code-develop-module.md) nebo Vývoj a ladění [modulů Jazyka C# v sadě Visual Studio](how-to-visual-studio-develop-csharp-module.md).

   Pro tento článek vše, co potřebujete, je složka řešení vytvořená šablonami IoT Edge v kódu Visual Studio nebo Visual Studiu. Před pokračováním není nutné tento kód vytvářet, tlačit, nasazovat nebo ladit. Tyto procesy nastavíte v Azure Pipelines.

   Pokud vytváříte nové řešení, nejprve naklonujte úložiště místně. Potom při vytváření řešení můžete vytvořit přímo ve složce úložiště. Můžete snadno potvrdit a tlačit nové soubory odtud.

* Registr kontejnerů, kde můžete nabízeny image modulu. Můžete použít [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo registru jiného výrobce.
* Aktivní [centrum IoT](../iot-hub/iot-hub-create-through-portal.md) s alespoň zařízeními IoT Edge pro testování jednotlivých fází testování a nasazení v produkčním prostředí. Podle článků rychlého startu můžete vytvořit zařízení IoT Edge v [Linuxu](quickstart-linux.md) nebo [Windows.](quickstart.md)

Další informace o používání Azure Repos najdete v [tématu Sdílení kódu s Visual Studio a Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Konfigurace kontinuální integrace

V této části vytvoříte nový kanál sestavení. Nakonfigurujte kanál tak, aby se spouštěl automaticky, když se změnami v ukázkovém řešení IoT Edge a publikujete protokoly sestavení.

>[!NOTE]
>Tento článek používá vizuální návrhář Azure DevOps. Než budete postupovat podle kroků v této části, vypněte funkci náhledu pro nové prostředí pro vytváření kanálu YAML.
>
>1. V Azure DevOps vyberte ikonu profilu a pak vyberte **Náhled funkcí**.
>2. Vypněte **nové prostředí pro vytváření kanálu YAML.**
>
>Další informace naleznete [v tématu Vytvoření kanálu sestavení](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline).

1. Přihlaste se ke své organizaci Azure DevOps (**\/https: /dev.azure.com/{vaše organizace}/**) a otevřete projekt, který obsahuje úložiště řešení IoT Edge.

   Pro tento článek jsme vytvořili úložiště s názvem **IoTEdgeRepo**. Toto úložiště obsahuje **IoTEdgeSolution,** který má kód pro modul s názvem **filtermodule**.

   ![Otevření projektu DevOps](./media/how-to-ci-cd/init-project.png)

2. Přejděte na Azure Kanálu ve vašem projektu. Otevřete kartu **Sestavení** a vyberte **Nový kanál**. Nebo pokud už máte kanály sestavení, vyberte **tlačítko Nový.** Pak zvolte **Nový kanál sestavení**.

    ![Vytvoření nového kanálu sestavení](./media/how-to-ci-cd/add-new-build.png)

3. Podle pokynů vytvořte kanál.

   1. Zadejte zdrojové informace pro váš nový kanál sestavení. Vyberte **Azure Repos Git** jako zdroj a pak vyberte projekt, úložiště a pobočku, kde se nachází kód řešení IoT Edge. Potom vyberte **Pokračovat**.

      ![Výběr zdroje kanálu](./media/how-to-ci-cd/pipeline-source.png)

   2. Místo šablony vyberte **Prázdná úloha.**

      ![Začněte s prázdným procesem](./media/how-to-ci-cd/start-with-empty.png)

4. Po vytvoření kanálu se přenesete do editoru kanálu. V popisu kanálu zvolte správný fond agentů na základě cílové platformy:

   * Pokud byste chtěli vytvořit své moduly v platformě amd64 pro linuxové kontejnery, zvolte **Hosted Ubuntu 1604**

   * Pokud chcete vytvořit moduly v platformě amd64 pro Windows 1809 kontejnery, musíte [nastavit agenta hostovaného vlastním hostitelem v systému Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Pokud byste chtěli vytvořit své moduly v platformě arm32v7 nebo arm64 pro linuxové kontejnery, musíte [nastavit samoobslužného agenta na Linuxu](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).

     ![Konfigurace fondu agentů sestavení](./media/how-to-ci-cd/configure-env.png)

5. Váš kanál je dodáván s úlohou nazvanou **Úloha agenta 1**. Vyberte znaménko plus**+**( ) chcete-li k úloze přidat tři úkoly: Azure **IoT Edge** dvakrát, Kopírovat **soubory** jednou a publikovat **artefakty sestavení** jednou. (Najeďte nad názvem jednotlivých úkolů a zodvaňte tlačítko **Přidat.)**

   ![Přidání úlohy Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Po přidání všech čtyř úkolů bude úloha agenta vypadat jako v následujícím příkladu:

   ![Tři úkoly v kanálu sestavení](./media/how-to-ci-cd/add-tasks.png)

6. Vyberte první úlohu **Azure IoT Edge,** kterou chcete upravit. Tento úkol vytvoří všechny moduly v řešení s cílovou platformou, kterou zadáte.

   * **Zobrazovaný název**: Přijměte výchozí **image modulu Azure IoT Edge – build**.
   * **Akce**: Přijmout výchozí **image modulu sestavení**.
   * **.template.json soubor**: Vyberte tři tečky (**...**) a přejděte do souboru **deployment.template.json** v úložišti, které obsahuje řešení IoT Edge.
   * **Výchozí platforma**: Vyberte příslušnou platformu pro vaše moduly na základě cílového zařízení IoT Edge.
   * **Výstupní proměnné**: Výstupní proměnné zahrnují název odkazu, který můžete použít ke konfiguraci cesty k souboru, kde bude generován soubor deployment.json. Nastavte název odkazu na něco zapamatovatelného, jako **je okraj**.

7. Vyberte druhou úlohu **Azure IoT Edge** a upravte ji. Tato úloha odešle všechny image modulu do registru kontejneru, který vyberete.

   * **Zobrazovaný název**: Zobrazovaný název se automaticky aktualizuje při změně pole akce.
   * **Akce**: Pomocí rozevíracího seznamu vyberte **možnost Nabízeno obrázky modulů**.
   * **Typ registru kontejneru**: Vyberte typ registru kontejneru, který používáte k ukládání bitových kopií modulu. V závislosti na zvoleném typu registru se formulář změní. Pokud zvolíte **Azure Container Registry**, použijte rozevírací seznamy k výběru předplatného Azure a název registru kontejnerů. Pokud zvolíte **obecný registr kontejnerů**, vyberte **nový** a vytvořte připojení služby registru.
   * **.template.json soubor**: Vyberte tři tečky (**...**) a přejděte do souboru **deployment.template.json** v úložišti, které obsahuje řešení IoT Edge.
   * **Výchozí platforma**: Vyberte stejnou platformu jako vaše obrazy modulu.

   Pokud máte více registrů kontejnerů pro hostování bitových kopií modulu, je třeba duplikovat tento úkol, vybrat jiný registr kontejnerů a použít **moduly Bypass** v pokročilých nastaveních k obejití bitových kopií, které nejsou pro tento konkrétní registr.

8. Vyberte úlohu **Kopírovat soubory,** kterou chcete upravit. Pomocí této úlohy můžete kopírovat soubory do testovacího adresáře artefaktů.

   * **Zobrazovaný název**: Kopírovat soubory do: Přetáhnout složku.
   * **Obsah**: Do této části `deployment.template.json` `**/module.json`vložte dva řádky a . Tyto dva typy souborů jsou vstupy pro generování manifestu nasazení IoT Edge. Je třeba zkopírovat do pracovní složky artefaktů a publikovat pro kanál vydání.
   * **Cílová složka**: `$(Build.ArtifactStagingDirectory)`Vložte proměnnou . Informace o popisu najdete v tématu [Vytváření proměnných.](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables)

9. Vyberte úlohu **Publikovat artefakty sestavení,** kterou chcete upravit. Poskytněte cestu k úkolu pracovního adresáře artefaktů, aby bylo možné ji publikovat a uvolnit kanál.

   * **Zobrazovaný název**: Publikovat artefakt: drop.
   * **Cesta k publikování**: `$(Build.ArtifactStagingDirectory)`Vložte proměnnou . Informace o popisu najdete v tématu [Vytváření proměnných.](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables)
   * **Název artefaktu**: drop.
   * **Umístění publikování artefaktů**: Azure Pipelines.

10. Otevřete kartu **Aktivační události** a zaškrtněte políčko **Povolit průběžnou integraci**. Ujistěte se, že je zahrnuta větev obsahující váš kód.

    ![Zapnutí aktivační události průběžné integrace](./media/how-to-ci-cd/configure-trigger.png)

11. Uložte nový kanál sestavení pomocí tlačítka **Uložit.**

Tento kanál je nyní nakonfigurován tak, aby se automaticky spouštěl při nabízení nového kódu do vašeho opětovného poučení. Poslední úloha, publikování artefaktů kanálu, aktivuje kanál vydání. Pokračujte k další části a vytvořte kanál vydání.

## <a name="configure-continuous-deployment"></a>Konfigurace průběžného nasazování

V této části vytvoříte kanál vydání, který je nakonfigurován tak, aby se automaticky spouštěl, když váš kanál sestavení přeruší artefakty, a zobrazí protokoly nasazení v Azure Pipelines.

Vytvoření nového kanálu a přidání nové fáze

1. Na kartě **Zprávy** zvolte **+ Nový kanál**. Nebo pokud již máte kanály vydání, zvolte **tlačítko + Nové** a vyberte + Nový kanál **vydání**.  

    ![Přidat kanál vydání](./media/how-to-ci-cd/add-release-pipeline.png)

2. Po zobrazení výzvy k výběru šablony zvolte, zda chcete začít s **prázdnou úlohou**.

    ![Začít s prázdnou úlohou](./media/how-to-ci-cd/start-with-empty-job.png)

3. Váš kanál nové verze se inicializuje s jednou fází nazvanou **Fáze 1**. Přejmenujte fázi 1 na **dev** a zacházejte s ní jako s testovacím prostředím. Kanály průběžného nasazení mají obvykle více fází včetně **dev**, **staging** a **prod**. Můžete vytvořit další na základě vaší devOps praxi. Po přejmenování zavřete okno podrobností o vymezené ploše.

4. Propojte vydání s artefakty sestavení, které jsou publikovány kanálem sestavení. Klikněte na **Přidat** do oblasti artefaktů.

   ![Přidání artefaktů](./media/how-to-ci-cd/add-artifacts.png)  

5. V **stránce Přidat artefakt**vyberte typ zdroje **Build**. Potom vyberte projekt a kanál sestavení, který jste vytvořili. Pak vyberte **Přidat**.

   ![Přidání artefaktu buildu](./media/how-to-ci-cd/add-an-artifact.png)

6. Otevřete aktivační události artefaktu a vyberte přepínač pro povolení aktivační události průběžného nasazení. Nyní bude vytvořena nová verze pokaždé, když je k dispozici nové sestavení.

   ![Konfigurace aktivační události průběžného nasazení](./media/how-to-ci-cd/add-a-trigger.png)

7. Fáze **dev** je předkonfigurována s jednou úlohou a nulou úloh. V nabídce kanálu vyberte **Úkoly a** pak zvolte **fázi dev.**  Vyberte počet úloh a úloh a nakonfigurujte úkoly v této fázi.

    ![Konfigurace úloh pro dev](./media/how-to-ci-cd/view-stage-tasks.png)

8. Ve fázi **dev** byste měli vidět výchozí **úlohu agenta**. Můžete nakonfigurovat podrobnosti o úloze agenta, ale úloha nasazení je necitlivá na platformě, takže můžete použít **hostovaný VS2017** nebo **Hostovaný Ubuntu 1604** ve **fondu agentů** (nebo jakýkoli jiný agent spravovaný sami).

9. Vyberte znaménko plus (**+**) a přidejte dva úkoly. Vyhledejte a přidejte **Azure IoT Edge** dvakrát.

    ![Přidání úkolů pro dev](./media/how-to-ci-cd/add-task-qa.png)

10. Vyberte první úlohu **Azure IoT Edge** a nakonfigurujte ji s následujícími hodnotami:

    * **Zobrazovaný název**: Zobrazovaný název se automaticky aktualizuje při změně pole akce.
    * **Akce**: Pomocí rozevíracího seznamu vyberte **Generovat manifest nasazení**. Změna hodnoty akce také aktualizuje zobrazovaný název úkolu tak, aby odpovídal.
    * **Soubor .template.json**: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`Vložte cestu . Cesta je publikována z kanálu sestavení.
    * **Výchozí platforma**: Při vytváření obrazů modulu zvolte stejnou hodnotu.
    * **Výstupní cesta**: `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`Vložte ji . Tato cesta je konečný soubor manifestu nasazení IoT Edge.

    Tyto konfigurace pomáhají nahradit adresy URL bitových adres modulu v souboru. `deployment.template.json` **Manifest generovat nasazení** také pomáhá nahradit proměnné přesnou hodnotou, kterou jste definovali v souboru. `deployment.template.json` V kódu VS/VS zadáváte skutečnou `.env` hodnotu v souboru. V Azure Pipelines nastavíte hodnotu na kartě Proměnné kanálu vydání.

    * **ACR_ADDRESS**: Adresa registru kontejnerů Azure.
    * **ACR_PASSWORD:** Heslo registru kontejnerů Azure.
    * **ACR_USER**: Uživatelské jméno registru kontejnerů Azure.

    Pokud máte v projektu jiné proměnné, můžete na této kartě zadat název a hodnotu. Manifest **generovat nasazení** lze rozpoznat pouze `${VARIABLE}` proměnné jsou v chuť, ujistěte se, že používáte v souborech. `*.template.json`

    ![Konfigurace proměnných pro kanál vydání](./media/how-to-ci-cd/configure-variables.png)

11. Vyberte druhou úlohu **Azure IoT Edge** a nakonfigurujte ji s následujícími hodnotami:

    * **Zobrazovaný název**: Zobrazovaný název se automaticky aktualizuje při změně pole akce.
    * **Akce**: Pomocí rozevíracího seznamu vyberte **Nasadit zařízení IoT Edge**. Změna hodnoty akce také aktualizuje zobrazovaný název úkolu tak, aby odpovídal.
    * **Předplatné Azure**: Vyberte předplatné, které obsahuje vaše služby IoT Hub.
    * **Název ioT hubu**: Vyberte svůj iot hub.
    * **Zvolte jedno nebo více zařízení**: Zvolte, jestli chcete kanál vydání nasadit do jednoho zařízení nebo více zařízení.
      * Pokud nasadíte do jednoho zařízení, zadejte **ID zařízení IoT Edge**.
      * Pokud nasazujete do více zařízení, zadejte **cílovou podmínku**zařízení . Cílová podmínka je filtr, který odpovídá sadě zařízení IoT Edge v centru IoT Hub. Pokud chcete jako podmínku použít značky zařízení, musíte aktualizovat odpovídající značky zařízení pomocí dvojčete zařízení služby IoT Hub. Aktualizujte **ID nasazení IoT Edge** a **prioritu nasazení IoT Edge** v pokročilých nastaveních. Další informace o vytváření nasazení pro více zařízení naleznete v [tématu Principy automatických nasazení IoT Edge](module-deployment-monitoring.md).
    * Rozbalte upřesnit nastavení, vyberte **ID nasazení IoT Edge**, vložte proměnnou `$(System.TeamProject)-$(Release.EnvironmentName)`. To mapuje název projektu a vydání s ID nasazení IoT Edge.

12. Výběrem **možnosti Uložit** uložíte změny do kanálu nové verze. Vraťte se do zobrazení kanálu výběrem **možnosti Kanál** z nabídky.

## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Ověření ci/cd IoT Edge pomocí kanálů sestavení a vydání

Chcete-li spustit úlohu sestavení, můžete buď push potvrzení úložiště zdrojového kódu nebo ručně aktivovat. V této části ručně spustíte kanál CI/CD a otestujete, zda funguje. Potom ověřte, zda je nasazení úspěšné.

1. Přejděte na kanál sestavení, který jste vytvořili na začátku tohoto článku.

2. Úlohu sestavení v kanálu sestavení můžete spustit výběrem tlačítka **Fronta** jako na následujícím snímku obrazovky.

    ![Ruční aktivační událost](./media/how-to-ci-cd/manual-trigger.png)

3. Vyberte úlohu sestavení a sledujte její průběh. Pokud je kanál sestavení úspěšně dokončen, aktivuje fázi vydání do **fáze dev.**

    ![Vytváření protokolů](./media/how-to-ci-cd/build-logs.png)

4. Úspěšná verze **dev** vytvoří nasazení IoT Edge pro cílová zařízení IoT Edge.

    ![Uvolnění do společnosti Dev](./media/how-to-ci-cd/pending-approval.png)

5. Kliknutím na **fázi dev** zobrazíte protokoly verzí.

    ![Protokoly vydaných verzí](./media/how-to-ci-cd/release-logs.png)

## <a name="next-steps"></a>Další kroky

* Ukázka osvědčených postupů ioT Edge DevOps v [projektu Azure DevOps pro IoT Edge](how-to-devops-project.md)
* Seznamte se s nasazením IoT Edge v [nasazení chodicích IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md)
* Projděte si kroky k vytvoření, aktualizaci nebo odstranění nasazení v [modulech Deploy and Monitor IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).
