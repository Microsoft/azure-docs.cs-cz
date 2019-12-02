---
title: Průběžná integrace & průběžného nasazování – Azure IoT Edge
description: Nastavení průběžné integrace a průběžného nasazování – Azure IoT Edge s využitím Azure DevOps Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 463de1f49ad8fd21c355395bec3a55d9d40474e6
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666354"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Průběžná integrace a průběžné nasazování pro Azure IoT Edge

Pomocí integrovaných Azure IoT Edge úloh v Azure Pipelines můžete snadno přijmout DevOps s aplikacemi Azure IoT Edge. Tento článek ukazuje, jak můžete pomocí funkcí průběžné integrace a průběžného nasazování pro Azure Pipelines sestavovat, testovat a nasazovat aplikace rychle a efektivně do Azure IoT Edge. 

![Diagram – větve CI a CD pro vývoj a produkci](./media/how-to-ci-cd/cd.png)

V tomto článku se dozvíte, jak pomocí integrovaných úloh Azure IoT Edge pro Azure Pipelines vytvořit dva kanály pro řešení IoT Edge. V úlohách Azure IoT Edge lze použít čtyři akce.
   - **Azure IoT Edge – image modulu sestavení** přebírají kód řešení IoT Edge a sestavují image kontejneru.
   - **Image modulu nabízených oznámení Azure IoT Edge** vloží image modulů do registru kontejneru, který jste zadali.
   - **Azure IoT Edge – vygenerovat manifest nasazení** převezme soubor Deployment. template. JSON a proměnné a pak vygeneruje finální soubor manifestu nasazení IoT Edge.
   - **Azure IoT Edge – nasazení do IoT Edge zařízení** pomáhá vytvářet IoT Edge nasazení do jednoho nebo více IoT Edge zařízení.

## <a name="prerequisites"></a>Předpoklady

* Úložiště Azure Repos. Pokud ho ještě nemáte, můžete [v projektu vytvořit nové úložiště Git](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Řešení IoT Edge potvrzené a vložené do úložiště. Chcete-li vytvořit nové ukázkové řešení pro testování tohoto článku, postupujte podle kroků v tématu [vývoj a ladění modulů v tématu Visual Studio Code](how-to-vs-code-develop-module.md) nebo [vývoj a ladění C# modulů v aplikaci Visual Studio](how-to-visual-studio-develop-csharp-module.md).
   * Pro tento článek stačí, když je složka řešení vytvořená IoT Edge šablonami v Visual Studio Code nebo v aplikaci Visual Studio. Než budete pokračovat, nemusíte tento kód sestavovat, předávat, nasazovat ani ladit. Tyto procesy nastavíte v Azure Pipelines. 
   * Pokud vytváříte nové řešení, naklonujte své úložiště jako první. Pak při vytváření řešení si můžete zvolit, že se má vytvořit přímo ve složce úložiště. Z nich můžete snadno potvrzovat a nabízet nové soubory. 
* Registr kontejnerů, kde můžete vkládat image modulu. Můžete použít [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo v registru třetí strany. 
* Aktivní [Centrum IoT](../iot-hub/iot-hub-create-through-portal.md) s aspoň IoT Edgem zařízením pro testování samostatných fází testování a produkčního nasazení. Pomocí článků rychlý Start můžete vytvořit zařízení IoT Edge v systému [Linux](quickstart-linux.md) nebo [Windows](quickstart.md) .


Další informace o použití Azure Repos naleznete v tématu [sdílení kódu v aplikaci Visual Studio a Azure Repos](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Konfigurace kontinuální integrace
V této části vytvoříte nový kanál sestavení. Nakonfigurujte, aby se kanál spouštěl automaticky při vrácení změn se změnami ukázkového IoT Edge řešení a publikování protokolů sestavení.

>[!NOTE]
>Tento článek používá vizuálního návrháře Azure DevOps. Před provedením kroků v této části vypněte funkci Preview pro nové prostředí vytváření kanálu YAML. 
>1. V Azure DevOps vyberte ikonu vašeho profilu a pak vyberte **funkce ve verzi Preview**.
>2. Zapněte **nové prostředí pro vytváření kanálů YAML** . 
>
>Další informace najdete v tématu [vytvoření kanálu sestavení](https://docs.microsoft.com/azure/devops/pipelines/create-first-pipeline).

1. Přihlaste se ke svojí organizaci Azure DevOps (**https:\//dev.Azure.com/{Your Organization}/** ) a otevřete projekt, který obsahuje vaše úložiště IoT Edge řešení.

   V tomto článku jsme vytvořili úložiště s názvem **IoTEdgeRepo**. Toto úložiště obsahuje **IoTEdgeSolution** s kódem pro modul s názvem **filtermodule**. 

   ![Otevřete projekt DevOps](./media/how-to-ci-cd/init-project.png)

2. V projektu přejděte na Azure Pipelines. Otevřete kartu **sestavení** a vyberte **Nový kanál**. Nebo pokud již máte kanály sestavení, vyberte tlačítko **Nový** . Pak zvolte **Nový kanál sestavení**.

    ![Vytvoření nového kanálu sestavení](./media/how-to-ci-cd/add-new-build.png)

3. Podle pokynů vytvořte svůj kanál. 

   1. Zadejte zdrojové informace pro nový kanál sestavení. Jako zdroj vyberte **Azure Repos Git** a pak vyberte projekt, úložiště a větev, kde se nachází váš kód řešení IoT Edge. Pak vyberte **pokračovat**. 

      ![Vyberte zdroj kanálu.](./media/how-to-ci-cd/pipeline-source.png)

   2. Vyberte **prázdnou úlohu** místo šablony. 

      ![Začít s prázdným procesem](./media/how-to-ci-cd/start-with-empty.png)

4. Po vytvoření kanálu přejdete do editoru kanálů. V popisu kanálu vyberte na základě cílové platformy správný fond agentů: 
    
   * Pokud chcete sestavit moduly v kontejnerech AMD64 pro Linux, vyberte možnost **hostované Ubuntu 1604**

   * Pokud chcete sestavit moduly v kontejnerech AMD64 pro Windows 1809, musíte [nastavit agenta v místním prostředí ve Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Pokud chcete sestavit moduly v kontejnerech platforem arm32v7 nebo arm64 pro Linux, musíte [nastavit agenta v místním](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/)prostředí pro Linux.
    
     ![Konfigurovat fond agentů sestavení](./media/how-to-ci-cd/configure-env.png)

5. Váš kanál je předem nakonfigurovaný s úlohou s názvem **úloha agenta 1**. Vyberte znaménko plus ( **+** ), chcete-li přidat tři úkoly do úlohy: **Azure IoT Edge** dvakrát, **Kopírovat soubory** jednou a **publikovat artefakty sestavení** jednou. (Tlačítko **Přidat** můžete zobrazit tak, že najedete myší na název jednotlivých úkolů.)

   ![Přidat úlohu Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Po přidání všech čtyř úkolů bude úloha agenta vypadat jako v následujícím příkladu:
    
   ![Tři úkoly v kanálu sestavení](./media/how-to-ci-cd/add-tasks.png)

6. Vyberte první úlohu **Azure IoT Edge** , kterou chcete upravit. Tato úloha vytvoří všechny moduly v řešení s cílovou platformou, kterou určíte.

   * **Zobrazovaný název**: přijměte výchozí **Image modulu Azure IoT Edge-Build**.
   * **Akce**: přijměte výchozí **Image modulu buildu**. 
   * **soubor. template. JSON**: vyberte tři tečky ( **...** ) a přejděte do souboru **Deployment. template. JSON** v úložišti, které obsahuje vaše řešení IoT Edge. 
   * **Výchozí platforma**: vyberte příslušnou platformu pro vaše moduly na základě vašeho cílového zařízení IoT Edge. 
   * **Výstupní proměnné**: výstupní proměnné obsahují referenční název, který můžete použít ke konfiguraci cesty k souboru, ve kterém se soubor Deployment. JSON vygeneruje. Nastavte název odkazu na něco, co se zapamatovat jako **Edge**. 

7. Vyberte druhý úkol **Azure IoT Edge** , který chcete upravit. Tato úloha vloží všechny image modulu do registru kontejneru, který jste vybrali.

   * **Zobrazovaný název**: zobrazovaný název se automaticky aktualizuje při změně pole akce. 
   * **Akce**: k výběru **imagí modulu push**použijte rozevírací seznam. 
   * **Typ registru kontejneru**: Vyberte typ registru kontejneru, který použijete k uložení imagí modulu. V závislosti na zvoleném typu registru se formulář změní. Pokud zvolíte **Azure Container Registry**, pomocí rozevíracích seznamů vyberte předplatné Azure a název vašeho registru kontejneru. Pokud zvolíte **obecné Container Registry**, vyberte **Nový** a vytvořte připojení služby registru. 
   * **soubor. template. JSON**: vyberte tři tečky ( **...** ) a přejděte do souboru **Deployment. template. JSON** v úložišti, které obsahuje vaše řešení IoT Edge. 
   * **Výchozí platforma**: Vyberte stejnou platformu jako vaše sestavené image modulu.

   Pokud máte více registrů kontejnerů pro hostování imagí modulu, musíte duplikovat tuto úlohu, vybrat jiný registr kontejnerů a v rozšířených nastaveních používat nepoužívané **moduly** pro obejít tak obrázky, které nejsou pro tento konkrétní registr.

8. Vyberte úlohu **kopírování souborů** a upravte ji. Pomocí této úlohy můžete kopírovat soubory do pracovního adresáře artefaktu.

   * **Zobrazovaný název**: kopírovat soubory do: ukládací složka
   * **Contents**: vložte dva řádky do této části, `deployment.template.json` a `**/module.json`. Tyto dva typy souborů jsou vstupy pro generování manifestu nasazení IoT Edge. Je nutné zkopírovat do pracovní složky artefaktu a publikovat pro kanál vydaných verzí.
   * **Cílová složka**: vložte proměnnou `$(Build.ArtifactStagingDirectory)`. Informace o popisu najdete v tématu věnovaném [proměnným sestavení](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) .

9. Vyberte úlohu **publikování artefaktů sestavení** a upravte ji. Zadejte cestu pracovního adresáře artefaktu k úloze, aby bylo možné cestu publikovat do kanálu uvolnění.
   
   * **Zobrazovaný název**: Publish artefakt: drop.
   * **Cesta k publikování**: zadejte proměnnou `$(Build.ArtifactStagingDirectory)`. Informace o popisu najdete v tématu věnovaném [proměnným sestavení](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) .
   * **Název artefaktu**: drop.
   * **Umístění pro publikování artefaktu**: Azure Pipelines.


10. Otevřete kartu **triggery** a zaškrtněte políčko pro **Povolení průběžné integrace**. Ujistěte se, že je součástí větve obsahující váš kód.

    ![Zapnout aktivační událost nepřetržitá integrace](./media/how-to-ci-cd/configure-trigger.png)

11. Uložte nový kanál sestavení pomocí tlačítka **Save (Uložit** ).

Tento kanál je teď nakonfigurovaný tak, aby se spouštěl automaticky při vložení nového kódu do úložiště. Poslední úkol, který publikuje artefakty kanálu, aktivuje kanál vydání. Pokračujte k další části a sestavte tak kanál pro vydávání verzí. 

## <a name="configure-continuous-deployment"></a>Konfigurace průběžného nasazování
V této části vytvoříte kanál pro vydávání verzí, který je nakonfigurován tak, aby běžel automaticky v případě, že kanál sestavení vyřazuje artefakty a zobrazí v Azure Pipelines protokoly nasazení.

Vytvořit nový kanál a přidat novou fázi 

1. Na kartě **vydané verze** vyberte **+ Nový kanál**. Nebo pokud již máte kanály verzí, klikněte na tlačítko **+ Nový** a vyberte **+ Nový kanál vydání**.  

    ![Přidat kanál verze](./media/how-to-ci-cd/add-release-pipeline.png)

2. Po zobrazení výzvy k výběru šablony zvolte, že chcete začít s **prázdnou úlohou**.

    ![Začněte s prázdnou úlohou.](./media/how-to-ci-cd/start-with-empty-job.png)

3. Nový kanál vydaných verzí se inicializuje s jednou fází nazvanou **fáze 1**. Přejmenujte fázi 1 na **vývoj** a považovat ji za testovací prostředí. Kanály průběžného nasazování mají obvykle několik fází včetně **vývoje**, **přípravy** a **produkčního**prostředí. Můžete vytvořit další informace na základě DevOps praxe. Po přejmenování zavřete okno Podrobnosti fáze. 

4. Propojte tuto verzi s artefakty sestavení, které jsou publikovány kanálem sestavení. V oblasti artefakty klikněte na **Přidat** .

   ![Přidat artefakty](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Na **stránce Přidat artefakt**vyberte možnost **sestavení**typu zdroje. Pak vyberte projekt a kanál sestavení, který jste vytvořili. Pak vyberte **Přidat**.

   ![Přidat artefakt sestavení](./media/how-to-ci-cd/add-an-artifact.png)

6. Otevřete triggery artefaktů a výběrem přepínače povolte aktivační událost průběžného nasazování. Nyní se vytvoří nová vydaná verze pokaždé, když bude k dispozici nové sestavení.

   ![Konfigurovat Trigger průběžného nasazování](./media/how-to-ci-cd/add-a-trigger.png)

7. Fáze **vývoje** je předem nakonfigurovaná s jednou úlohou a nulovými úkoly. V nabídce kanálu vyberte **úlohy** a pak zvolte fázi **vývoje** .  Vyberte úlohu a počet úloh pro konfiguraci úkolů v této fázi.

    ![Konfigurace úloh vývoje](./media/how-to-ci-cd/view-stage-tasks.png)

8. Ve fázi **vývoje** byste měli vidět výchozí **úlohu agenta**. Můžete nakonfigurovat podrobnosti o úloze agenta, ale úloha nasazení je nezávislá na platformě, takže můžete použít buď **hostované VS2017** nebo **hostované Ubuntu 1604** ve **fondu agentů** (nebo jakýkoli jiný agent, který spravujete sami). 

9. Vyberte znaménko plus ( **+** ), chcete-li přidat dvě úlohy. Vyhledejte a přidejte **Azure IoT Edge** dvakrát.

    ![Přidat úkoly pro vývoj](./media/how-to-ci-cd/add-task-qa.png)

10. Vyberte první úlohu **Azure IoT Edge** a nakonfigurujte ji s následujícími hodnotami:

    * **Zobrazovaný název**: zobrazovaný název se automaticky aktualizuje při změně pole akce. 
    * **Akce**: v rozevíracím seznamu vyberte možnost **Generovat Manifest nasazení**. Změna hodnoty akce také aktualizuje zobrazovaný název úlohy tak, aby odpovídal.
    * **soubor. template. JSON**: vložte cestu `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json`. Cesta je publikovaná z kanálu sestavení.
    * **Výchozí platforma**: při sestavování imagí modulu vyberte stejnou hodnotu.
    * **Výstupní cesta**: zadejte cestu `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json`. Tato cesta je konečným souborem manifestu nasazení IoT Edge.

    Tyto konfigurace pomáhají nahradit adresy URL imagí modulu v souboru `deployment.template.json`. **Manifest pro generování nasazení** také pomáhá nahradit proměnné s přesnou hodnotu, kterou jste definovali v souboru `deployment.template.json`. V VS/VS Code určujete skutečnou hodnotu v souboru `.env`. V Azure Pipelines nastavte hodnotu na kartě proměnné kanálu uvolnění. přesunout na kartu proměnné a nakonfigurovat název a hodnotu následujícím způsobem.

    * **ACR_ADDRESS**: vaše Azure Container Registry adresa. 
    * **ACR_PASSWORD**: vaše heslo Azure Container Registry.
    * **ACR_USER**: vaše Azure Container Registry uživatelské jméno.

    Pokud máte v projektu další proměnné, můžete na této kartě zadat název a hodnotu. **Manifest pro generování nasazení** může rozpoznat pouze proměnné v `${VARIABLE}` charakteru, ujistěte se, že tuto funkci používáte ve vašich `*.template.json`ch souborech.

    ![Konfigurace proměnných pro kanál verze](./media/how-to-ci-cd/configure-variables.png)

10. Vyberte druhou úlohu **Azure IoT Edge** a nakonfigurujte ji s následujícími hodnotami:

    * **Zobrazovaný název**: zobrazovaný název se automaticky aktualizuje při změně pole akce. 
    * **Akce**: pomocí rozevíracího seznamu vyberte **nasadit pro IoT Edge zařízení**. Změna hodnoty akce také aktualizuje zobrazovaný název úlohy tak, aby odpovídal.
    * **Předplatné Azure**: vyberte předplatné, které obsahuje vaše IoT Hub.
    * **IoT Hub název**: vyberte Centrum IoT. 
    * **Vybrat jedno nebo více zařízení**: vyberte, jestli chcete, aby se kanál verze nasadil do jednoho nebo víc zařízení. 
      * Pokud nasadíte do jednoho zařízení, zadejte **ID zařízení IoT Edge**. 
      * Pokud nasazujete na více zařízení, zadejte **podmínku cíle**zařízení. Podmínka cíle je filtr, který odpovídá sadě IoT Edge zařízení v IoT Hub. Pokud chcete jako podmínku použít značky zařízení, je potřeba aktualizovat značky odpovídajících zařízení pomocí IoT Hub s dvojitou podmínkou zařízení. Aktualizujte **ID nasazení IoT Edge** a **prioritu nasazení IoT Edge** v rozšířených nastaveních. Další informace o vytváření nasazení pro více zařízení najdete v tématu [principy IoT Edgeho automatického nasazení](module-deployment-monitoring.md).
    * Rozbalte položku upřesňující nastavení, vyberte **IoT Edge ID nasazení**, vložte `$(System.TeamProject)-$(Release.EnvironmentName)`proměnné. Tím se namapuje název projektu a verze s vaším ID nasazení IoT Edge.

11. Vyberte **Uložit** a uložte změny do nového kanálu vydání. Vraťte se do Zobrazení kanálu výběrem **kanálu** z nabídky. 
    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Ověření IoT Edge CI/CD pomocí kanálů sestavení a vydání

Chcete-li spustit úlohu sestavení, můžete buď Odeslat potvrzení do úložiště zdrojového kódu, nebo jej ručně aktivovat. V této části ručně aktivujete kanál CI/CD, abyste otestovali, že funguje. Pak ověřte, že nasazení proběhlo úspěšně.

1. Přejděte na kanál sestavení, který jste vytvořili na začátku tohoto článku. 

2. Můžete aktivovat úlohu sestavení v kanálu sestavení tak, že vyberete tlačítko **fronta** jako na následujícím snímku obrazovky.

    ![Ruční aktivační událost](./media/how-to-ci-cd/manual-trigger.png)

3. Vyberte úlohu sestavení a sledujte její průběh. Pokud je kanál sestavení úspěšně dokončen, aktivuje vydání **vývojové** fáze. 

    ![Protokoly sestavení](./media/how-to-ci-cd/build-logs.png)

4. Úspěšná verze **dev** vytvoří nasazení IoT Edge pro cílení IoT Edge zařízení.

    ![Vydání pro vývoj](./media/how-to-ci-cd/pending-approval.png)

5. Kliknutím na **vývojové** fáze zobrazíte protokoly verzí.

    ![Protokoly verzí](./media/how-to-ci-cd/release-logs.png)



## <a name="next-steps"></a>Další kroky
* Ukázka osvědčených postupů IoT Edge DevOps v [projektu Azure DevOps pro IoT Edge](how-to-devops-project.md)
* Pochopení nasazení IoT Edge v části [Principy nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md)
* Projděte si postup vytvoření, aktualizace nebo odstranění nasazení v části [nasazení a monitorování IoT Edgech modulů ve velkém měřítku](how-to-deploy-monitor.md).
