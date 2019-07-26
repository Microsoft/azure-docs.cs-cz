---
title: Průběžná integrace a průběžné nasazování – Azure IoT Edge | Dokumentace Microsoftu
description: Nastavte průběžnou integraci a průběžné nasazování – Azure IoT Edge s Azure DevOps, Azure kanály
author: shizn
manager: philmea
ms.author: xshi
ms.date: 01/22/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 659a6f5acaac848084ed1e9590a414191542b54a
ms.sourcegitcommit: c556477e031f8f82022a8638ca2aec32e79f6fd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/23/2019
ms.locfileid: "68414633"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Průběžná integrace a průběžné nasazování do Azure IoT Edge

Pomocí integrovaných Azure IoT Edge úloh v Azure Pipelines můžete snadno přijmout DevOps s aplikacemi Azure IoT Edge. Tento článek ukazuje, jak můžete pomocí funkcí průběžné integrace a průběžného nasazování pro Azure Pipelines sestavovat, testovat a nasazovat aplikace rychle a efektivně do Azure IoT Edge. 

V tomto článku se dozvíte, jak pomocí integrovaných úloh Azure IoT Edge pro Azure Pipelines vytvořit dva kanály pro řešení IoT Edge. První vezme váš kód a sestaví řešení, nasdílením imagí modulu do registru kontejneru a vytvořením manifestu nasazení. Druhý nasadí moduly do cílových IoT Edgech zařízení.  

![Diagram – CI a CD větve pro vývoj a provoz](./media/how-to-ci-cd/cd.png)


## <a name="prerequisites"></a>Požadavky

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
>Další informace najdete v tématu [vytvoření kanálu sestavení](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline).

1. Přihlaste se ke svojí organizaci Azure DevOps (**https:\//dev.Azure.com/{Your Organization}/** ) a otevřete projekt, který obsahuje úložiště řešení IoT Edge.

   V tomto článku jsme vytvořili úložiště s názvem **IoTEdgeRepo**. Toto úložiště obsahuje **IoTEdgeSolution** s kódem pro modul s názvem **filtermodule**. 

   ![Otevřete projekt DevOps](./media/how-to-ci-cd/init-project.png)

2. V projektu přejděte na Azure Pipelines. Otevřete kartu **sestavení** a vyberte **Nový kanál**. Nebo pokud již máte kanály sestavení, vyberte tlačítko **Nový** . Pak zvolte **Nový kanál sestavení**.

    ![Vytvoření nového kanálu sestavení](./media/how-to-ci-cd/add-new-build.png)

3. Podle pokynů vytvořte svůj kanál. 

   1. Zadejte zdrojové informace pro nový kanál sestavení. Jako zdroj vyberte **Azure Repos Git** a pak vyberte projekt, úložiště a větev, kde se nachází váš kód řešení IoT Edge. Pak vyberte **pokračovat**. 

      ![Vyberte zdroj kanálu.](./media/how-to-ci-cd/pipeline-source.png)

   2. Vyberte **prázdnou úlohu** místo šablony. 

      ![Začněte s prázdným procesem.](./media/how-to-ci-cd/start-with-empty.png)

4. Po vytvoření kanálu přejdete do editoru kanálů. V popisu kanálu vyberte na základě cílové platformy správný fond agentů: 
    
   * Pokud chcete vytvářet moduly v amd64 platformu pro kontejnery Linuxu, zvolte **hostované 1604 Ubuntu**

   * Pokud chcete sestavit moduly v kontejnerech AMD64 pro Windows 1809, musíte [nastavit agenta v místním prostředí ve Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Pokud chcete sestavit moduly v kontejnerech platforem arm32v7 nebo arm64 pro Linux, musíte [nastavit agenta v místním](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/)prostředí pro Linux.
    
     ![Konfigurace fondu agentů sestavení](./media/how-to-ci-cd/configure-env.png)

5. Váš kanál je předem nakonfigurovaný s úlohou s názvem **úloha agenta 1**. Vyberte znaménko plus ( **+** ) a přidejte do úlohy tři úkoly: **Azure IoT Edge** dvakrát a **publikujte artefakty sestavení** . (Tlačítko **Přidat** můžete zobrazit tak, že najedete myší na název jednotlivých úkolů.)

   ![Přidat úlohu Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Po přidání všech tří úkolů bude úloha agenta vypadat jako v následujícím příkladu:
    
   ![Tři úkoly v kanálu sestavení](./media/how-to-ci-cd/add-tasks.png)

6. Vyberte první úlohu **Azure IoT Edge** , kterou chcete upravit. Tato úloha sestaví všechny moduly v řešení s cílovou platformou, kterou zadáte, vygeneruje také soubor **Deployment. JSON** , který oznamuje IoT Edge zařízením, jak nakonfigurovat nasazení.

   * **Zobrazovaný název**: Přijměte výchozí **Image modulu Azure IoT Edge-Build**.
   * **Akce**: Přijměte výchozí **Image modulu buildu**. 
   * **soubor. template. JSON**: Vyberte tři tečky ( **...** ) a přejděte do souboru **Deployment. template. JSON** v úložišti, které obsahuje vaše řešení IoT Edge. 
   * **Výchozí platforma**: Vyberte odpovídající platformu pro vaše moduly na základě cílového IoT Edge zařízení. 
   * **Výstupní proměnné**: Výstupní proměnné obsahují referenční název, který lze použít ke konfiguraci cesty k souboru, ve kterém bude vytvořen soubor Deployment. JSON. Nastavte název odkazu na něco, co se zapamatovat jako **Edge**. 

7. Vyberte druhý úkol **Azure IoT Edge** , který chcete upravit. Tato úloha vloží všechny image modulu do registru kontejneru, který jste vybrali. Přidá taky přihlašovací údaje registru kontejneru do souboru **Deployment. JSON** , aby vaše zařízení IoT Edge mělo přístup k obrázkům modulu. 

   * **Zobrazovaný název**: Zobrazovaný název se automaticky aktualizuje při změně pole akce. 
   * **Akce**: Pomocí rozevíracího seznamu vyberte **Image nabízených modulů**. 
   * **Typ registru kontejneru**: Vyberte typ registru kontejneru, který použijete k uložení imagí modulu. V závislosti na zvoleném typu registru se formulář změní. Pokud zvolíte **Azure Container Registry**, pomocí rozevíracích seznamů vyberte předplatné Azure a název vašeho registru kontejneru. Pokud zvolíte **obecné Container Registry**, vyberte **Nový** a vytvořte připojení služby registru. 
   * **soubor. template. JSON**: Vyberte tři tečky ( **...** ) a přejděte do souboru **Deployment. template. JSON** v úložišti, které obsahuje vaše řešení IoT Edge. 
   * **Výchozí platforma**: Vyberte stejnou platformu jako vaše sestavené image modulu.

   Pokud máte více registrů kontejnerů k hostování vaší bitové kopie modulu, budete muset Duplikovat tuto úlohu, vyberte jiný registr kontejneru a použijte **obejít modulu nebo modulech** v upřesňujících nastaveních obejít imagí, které nejsou pro tuto konkrétního registru.

8. Vyberte úlohu **publikování artefaktů sestavení** a upravte ji. Zadejte cestu k souboru nasazení generovanému úlohou sestavení. Nastavte **cestu k publikování** hodnoty tak, aby odpovídala výstupní proměnné, kterou jste nastavili v úloze modulu sestavení. Například, `$(edge.DEPLOYMENT_FILE_PATH)`. Ostatní hodnoty ponechte jako výchozí. 

9. Otevřete kartu **triggery** a zaškrtněte políčko pro **Povolení průběžné integrace**. Ujistěte se, že větev, která obsahuje kód je součástí.

    ![Zapnout trigger průběžné integrace](./media/how-to-ci-cd/configure-trigger.png)

10. Uložte nový kanál sestavení pomocí tlačítka **Save (Uložit** ).

Tento kanál je teď nakonfigurovaný tak, aby se spouštěl automaticky při vložení nového kódu do úložiště. Poslední úkol, který publikuje artefakty kanálu, aktivuje kanál vydání. Pokračujte k další části a sestavte tak kanál pro vydávání verzí. 

## <a name="configure-continuous-deployment"></a>Konfigurace průběžného nasazování
V této části vytvoříte kanál pro vydávání verzí, který je nakonfigurován tak, aby běžel automaticky v případě, že kanál sestavení vyřazuje artefakty a zobrazí v Azure Pipelines protokoly nasazení.

V této části vytvoříte dvě různé fáze, jednu pro testovací nasazení a jednu pro produkční nasazení. 

### <a name="create-test-stage"></a>Vytvořit testovací fázi

Vytvořte nový kanál a nakonfigurujte jeho první fázi pro nasazení služby zabezpečování kvality (QA). 

1. V **verze** kartě **+ nový kanál**. Nebo pokud již máte kanály verzí, klikněte na tlačítko **+ Nový** a vyberte **+ Nový kanál vydání**.  

    ![Přidejte kanál pro vydávání verzí](./media/how-to-ci-cd/add-release-pipeline.png)

2. Po zobrazení výzvy k výběru šablony zvolte, že chcete začít s **prázdnou úlohou**.

    ![Začít s prázdnou úlohu](./media/how-to-ci-cd/start-with-empty-job.png)

3. Nový kanál vydaných verzí se inicializuje s jednou fází nazvanou **fáze 1**. Přejmenujte fázi 1 na **QA** a považovat ji za testovací prostředí. Kanály průběžného nasazování mají obvykle několik fází. Můžete vytvořit další informace na základě DevOps praxe. Po přejmenování zavřete okno Podrobnosti fáze. 

    ![Vytvořit testovací prostředí fáze](./media/how-to-ci-cd/QA-env.png)

4. Propojte tuto verzi s artefakty sestavení, které jsou publikovány kanálem sestavení. Klikněte na tlačítko **přidat** v oblasti artefakty.

   ![Přidat artefakty](./media/how-to-ci-cd/add-artifacts.png)  
    
5. Na **stránce Přidat artefakt**vyberte možnost **sestavení**typu zdroje. Pak vyberte projekt a kanál sestavení, který jste vytvořili. Pak vyberte **Přidat**.

   ![Přidejte artefakt sestavení](./media/how-to-ci-cd/add-an-artifact.png)

6. Otevřete triggery artefaktů a výběrem přepínače povolte aktivační událost průběžného nasazování. Nyní se vytvoří nová vydaná verze pokaždé, když bude k dispozici nové sestavení.

   ![Konfigurace aktivační události pro průběžné nasazování](./media/how-to-ci-cd/add-a-trigger.png)

7. Fáze kontroly **kvality** je předem nakonfigurovaná s jednou úlohou a nulovými úkoly. V nabídce kanálu vyberte **úlohy** a pak zvolte fázi kontroly **kvality** .  Vyberte úlohu a počet úloh pro konfiguraci úkolů v této fázi.

    ![Konfigurace úloh dotazů a odpovědí](./media/how-to-ci-cd/view-stage-tasks.png)

8. Ve fázi kontroly kvality by se měla zobrazit úloha výchozího **agenta**. Můžete nakonfigurovat podrobnosti o úloze agenta, ale úloha nasazení je nezávislá na platformě, takže můžete použít buď **hostované VS2017** nebo **hostované Ubuntu 1604** ve **fondu agentů** (nebo jakýkoli jiný agent, který spravujete sami). 

9. Vyberte znaménko plus ( **+** ) a přidejte jeden úkol. Vyhledejte a přidejte **Azure IoT Edge**. 

    ![Přidání úkolů pro kontrolu kvality](./media/how-to-ci-cd/add-task-qa.png)

10. Vyberte úlohu nový Azure IoT Edge a nakonfigurujte ji s následujícími hodnotami:

    * **Zobrazovaný název**: Zobrazovaný název se automaticky aktualizuje při změně pole akce. 
    * **Akce**: Pomocí rozevíracího seznamu vyberte **nasadit a IoT Edge zařízení**. Změna hodnoty akce také aktualizuje zobrazovaný název úlohy tak, aby odpovídal.
    * **Předplatné Azure**: Vyberte předplatné, které obsahuje vaše IoT Hub.
    * **Název IoT Hub**: Vyberte své Centrum IoT. 
    * **Vyberte jedno nebo více zařízení**: Vyberte, jestli chcete, aby se kanál verze nasadil do jednoho nebo víc zařízení. 
      * Pokud nasadíte do jednoho zařízení, zadejte **ID zařízení IoT Edge**. 
      * Pokud nasazujete na více zařízení, zadejte **podmínku cíle**zařízení. Cílová podmínka se filtr tak, aby odpovídaly sadu hraničních zařízení ve službě IoT Hub. Pokud chcete použít značky zařízení jako podmínka, musíte aktualizovat dvojče zařízení služby IoT Hub zařízení odpovídající značky. Aktualizujte **ID nasazení IoT Edge** a **prioritu nasazení IoT Edge** v rozšířených nastaveních. Další informace o vytváření nasazení pro více zařízení najdete v tématu [principy IoT Edgeho automatického nasazení](module-deployment-monitoring.md).

11. Vyberte **Uložit** a uložte změny do nového kanálu vydání. Vraťte se do Zobrazení kanálu výběrem **kanálu** z nabídky. 

### <a name="create-production-stage"></a>Vytvořit fázi výroby

Vytvořte ve svém kanálu pro vydání druhou fázi pro produkční nasazení. 

1. Naklonujte fázi kontroly kvality a vytvořte druhou fázi pro produkci. Najeďte ukazatelem myši na fázi kontroly kvality a pak vyberte tlačítko klonování. 

    ![Klonovat fáze](./media/how-to-ci-cd/clone-stage.png)

2. Výběrem nové fáze s názvem **kopie QA**otevřete její vlastnosti. Změňte název **fáze na produkční**prostředí. Zavřete okno Vlastnosti fáze. 

3. Chcete-li otevřít úkoly fáze výroby, vyberte možnost **úkoly** v nabídce kanál a pak zvolte fázi **výroby** . 

4. Vyberte úlohu Azure IoT Edge, kterou chcete nakonfigurovat, pokud má být v produkčním prostředí. Nastavení nasazení jsou pravděpodobně stejná pro QA a for PROD, s výjimkou toho, že chcete cílit na jiné zařízení nebo sadu zařízení v produkčním prostředí. Aktualizujte pole ID zařízení nebo cílovou podmínku a ID nasazení pro vaše produkční zařízení. 

5. Uložte ho pomocí tlačítka **Uložit** . Pak vyberte **kanál** a vraťte se do Zobrazení kanálu.
    
6. Způsob, jakým je tento kanál verze aktuálně nakonfigurovaný, artefakt sestavení aktivuje fázi **QA** a **potom zpracuje** fázi pokaždé, když se nové sestavení nedokončí. Obvykle ale budete chtít integrovat některé testovací případy na zařízeních s QA a ručně schválit nasazení v produkčním prostředí. Pro vytvoření podmínky schválení pro fázi výroby použijte následující postup:

    1. Otevřete panel nastavení **podmínek před nasazením** .

        ![Otevřít před nasazením podmínky](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Přepněte podmínku **schválení před nasazením** na **povoleno**. Do pole schvalovatelé přidejte jednoho nebo více uživatelů  nebo skupin a upravte všechny další požadované zásady schvalování. Pokud chcete změny uložit, zavřete panel podmínky před nasazením.
    
       ![Nastavení podmínek](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. Uložte svůj kanál pro vydávání verzí pomocí tlačítka **Uložit** . 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Ověřte IoT Edge CI/CD s sestavení a vydávání kanálů

K aktivaci úlohy sestavení, můžete potvrdíte a vložíte změny do úložiště zdrojového kódu nebo ruční spuštění. V této části ručně aktivujete kanál CI/CD, abyste otestovali, že funguje. Pak ověřte, že nasazení proběhlo úspěšně.

1. Přejděte na kanál sestavení, který jste vytvořili na začátku tohoto článku. 

2. Můžete aktivovat úlohu sestavení v kanálu sestavení tak, že vyberete tlačítko **fronta** jako na následujícím snímku obrazovky.

    ![Ruční aktivační události](./media/how-to-ci-cd/manual-trigger.png)

3. Vyberte úlohu sestavení a sledujte její průběh. Pokud je kanál sestavení úspěšně dokončen, aktivuje vydání verze do fáze pro kontrolu **kvality** . 

    ![Protokoly o sestavení](./media/how-to-ci-cd/build-logs.png)

4. Úspěšné nasazení do fáze kontroly **kvality** spustí oznámení schvalovateli. Ověřte, že se moduly nasazené úspěšně nasadily na zařízení nebo zařízení, na která cílíte ve fázi kontroly kvality. Potom přejděte na kanál pro vydání a sdělte schválení vydané verze, abyste přešli do fáze výroby, a to tak, že vyberete tlačítko **prod** a pak vyberete **schválit**. 

    ![Čeká na schválení](./media/how-to-ci-cd/pending-approval.png)

5. Jakmile schvalující schválit tuto změnu, je možné nasadit do **produkční**.

## <a name="next-steps"></a>Další postup

* Vysvětlení nasazení IoT Edge v [vysvětlení nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md)
* Provede kroky k vytvoření, aktualizace nebo odstranění nasazení v [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).
