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
ms.openlocfilehash: 196d08f47ddfdbb86b8e96ae0e5ca3d3e3e5917e
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54886760"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge"></a>Průběžná integrace a průběžné nasazování do Azure IoT Edge

Pomocí aplikace Azure IoT Edge s využitím připravených úloh Azure IoT Edge v kanálech Azure můžete snadno přijmout DevOps. Tento článek popisuje, jak vám pomůže průběžné integrace a průběžného nasazování funkce Azure kanálů sestavovat, testovat a nasazovat aplikace rychle a efektivně do vašeho Azure IoT Edge. 

V tomto článku se dozvíte, jak použít integrované úlohy Azure IoT Edge pro kanály Azure k vytvoření dva kanály pro vaše řešení IoT Edge. První vezme váš kód a sestaví řešení a nahráním Image modulu do vašeho registru kontejneru a vytváření manifestu nasazení. Druhá nasadí modulů do cílových zařízení IoT Edge.  

![Diagram – CI a CD větve pro vývoj a provoz](./media/how-to-ci-cd/cd.png)


## <a name="prerequisites"></a>Požadavky

* Je úložiště na úložiště Azure. Pokud ho nemáte, můžete si [vytvoření nového úložiště Git ve vašem projektu](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav).
* Řešení IoT Edge, potvrdíte a nabídnete do úložiště. Pokud chcete vytvořit nové ukázkové řešení pro testování tohoto článku, postupujte podle kroků v [vývoj a ladění modulů ve Visual Studio Code](how-to-vs-code-develop-module.md) nebo [vývoj a ladění C# moduly v sadě Visual Studio](how-to-visual-studio-develop-csharp-module.md).
   * Pro účely tohoto článku je vše, co potřebujete složku řešení, které jsou vytvořeny pomocí šablon IoT Edge v aplikaci Visual Studio Code nebo Visual Studio. Není nutné vytvářet, vkládání, nasazení nebo ladit tento kód před pokračováním. Tyto procesy vám nastavit v kanálech Azure. 
   * Pokud vytváříte nové řešení, nejprve naklonujte úložiště místně. Potom když vytvoříte řešení můžete vytvořit přímo ve složce úložiště. Můžete snadno potvrzení a nasdílení změn nových souborů z něj. 
* Registr kontejnerů, kde můžete nabízet bitové kopie modulu. Můžete použít [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo registru třetích stran. 
* Aktivní [služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) s alespoň zařízení IoT Edge pro testování samostatný testovací a produkční fáze nasazení. Můžete postupovat podle článků rychlý start k vytvoření zařízení IoT Edge na [Linux](quickstart-linux.md) nebo [Windows](quickstart.md)


Další informace o použití úložiště Azure najdete v tématu [sdílení kódu pomocí sady Visual Studio a úložiště Azure](https://docs.microsoft.com/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="configure-continuous-integration"></a>Nakonfiguruje kontinuální integraci
V této části vytvoříte nový kanál sestavení. Nakonfigurujte kanál na automatické spuštění při vrácení se změnami změny ukázkové řešení IoT Edge a publikovat protokolů o sestavení.

>[!NOTE]
>Tento článek používá Azure DevOps vizuálního návrháře. Před provedením kroků v této části, vypněte funkci ve verzi preview nového prostředí vytváření kanálu YAML. 
>1. V Azure DevOps, vyberte ikonu vašeho profilu a pak vyberte **funkce ve verzi Preview**.
>2. Zapnout **uživatelského prostředí při vytváření nového YAML kanálu** vypnout. 
>
>Další informace najdete v tématu [vytvoření kanálu sestavení](https://docs.microsoft.com/azure/devops/pipelines/get-started-designer?view=vsts&tabs=new-nav#create-a-build-pipeline).

1. Přihlaste se k vaší organizaci Azure DevOps ( **https://dev.azure.com/{your organizace} /**) a otevřete projekt, který obsahuje vaše úložiště řešení IoT Edge.

   Pro účely tohoto článku jsme vytvořili úložiště volána **IoTEdgeRepo**. Daného úložiště obsahuje **IoTEdgeSolution** který má kód pro modul s názvem **filtermodule**. 

   ![Otevřete projekt DevOps](./media/how-to-ci-cd/init-project.png)

2. Přejděte na kanály Azure ve vašem projektu. Otevřít **sestavení** kartě a vyberte **nový kanál**. Nebo, pokud již máte kanály pro sestavování, vyberte **nový** tlačítko. Klikněte na tlačítko **nové vytvoření kanálu**.

    ![Vytvoření nového kanálu sestavení](./media/how-to-ci-cd/add-new-build.png)

3. Postupujte podle výzev a vytvořte svůj kanál. 

   1. Zadejte informace o zdroji pro svůj nový kanál sestavení. Vyberte **úložiště Git v Azure** jako zdroje a pak vyberte projekt, úložiště a větev, ve kterém se nachází váš kód řešení IoT Edge. Vyberte **pokračovat**. 

      ![Vyberte zdroj kanálu](./media/how-to-ci-cd/pipeline-source.png)

   2. Vyberte **prázdný úlohy** místo šablony. 

      ![Začněte s prázdným procesem.](./media/how-to-ci-cd/start-with-empty.png)

4. Po vytvoření kanálu, budete přesměrováni na editor kanálu. V popisu kanálu zvolte fond správnými založené na cílové platformě: 
    
    * Pokud chcete vytvářet moduly v amd64 platformu pro kontejnery Linuxu, zvolte **hostované 1604 Ubuntu**

    * Pokud chcete vytvářet moduly v amd64 platformu pro kontejnery Windows 1809, budete muset [nastavte v místním prostředí agenta na Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

    * Pokud chcete vytvářet moduly v arm32v7 platformu pro kontejnery Linuxu, budete muset [nastavení agenta v místním prostředí v Linuxu](https://blogs.msdn.microsoft.com/iotdev/2018/11/13/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent/).
    
    ![Konfigurace fondu agentů sestavení](./media/how-to-ci-cd/configure-env.png)

5. Kanál je součástí předkonfigurovaného úlohu s názvem **úlohu agenta 1**. Vyberte znaménko plus (**+**) přidáte tři úkoly do úlohy: **Azure IoT Edge** dvakrát a **publikujte artefakty sestavení** po. (Při najetí myší na název každé úlohy zobrazíte **přidat** tlačítko.)

   ![Přidat úlohu Azure IoT Edge](./media/how-to-ci-cd/add-iot-edge-task.png)

   Když se přidají všechny tři úkoly, vaše úloha agenta bude vypadat jako v následujícím příkladu:
    
   ![Tři úkoly v kanálu sestavení](./media/how-to-ci-cd/add-tasks.png)

6. Vyberte první **Azure IoT Edge** úkol a upravte ho. Tato úloha sestavení všechny moduly v řešení s cílovou platformu, že zadáte, zároveň se vygeneruje **deployment.json** souboru, který popisuje způsob konfigurace nasazení zařízení IoT Edge.

   * **Zobrazovaný název**: Přijměte výchozí nastavení **Azure IoT Edge – bitové kopie sestavení modulu**.
   * **Akce**: Přijměte výchozí nastavení **vytváření bitové kopie modulu**. 
   * **. soubor template.json**: Vyberte tři tečky (**...** ) a přejděte **deployment.template.json** souboru v úložišti, který obsahuje vaše řešení IoT Edge. 
   * **Výchozí platformu**: Vyberte příslušné platformy pro vaše moduly založené na cílové zařízení IoT Edge. 
   * **Výstupní proměnné**: Výstupní proměnné zahrnují název odkazu, který vám pomůže nakonfigurovat cestu k souboru odkud se bude generovat soubor deployment.json. Nastavte název odkazu na něco snadno zapamatovatelné jako **hrany**. 

7. Vyberte druhou **Azure IoT Edge** úkol a upravte ho. Tato úloha odesílá všechny bitové kopie modulu do registru kontejneru, kterou jste vybrali. Také přidá přihlašovací údaje registru kontejneru pro **deployment.json** souboru tak, aby vaše zařízení IoT Edge můžete přístup k bitové kopie modulu. 

   * **Zobrazovaný název**: Zobrazovaný název se aktualizuje automaticky při změně pole akce. 
   * **Akce**: Pomocí rozevíracího seznamu vyberte **Push bitové kopie modulu**. 
   * **Typ registru kontejneru**: Vyberte typ registru kontejneru, který použijete k uložení vaší bitové kopie modulu. V závislosti na tom, jaký typ registru zvolíte, změny formuláře. Pokud se rozhodnete **Azure Container Registry**, pomocí rozevíracích seznamů vyberte předplatné Azure a název vašeho registru kontejneru. Pokud se rozhodnete **obecný Container Registry**vyberte **nový** spojení služba registru. 
   * **. soubor template.json**: Vyberte tři tečky (**...** ) a přejděte **deployment.template.json** souboru v úložišti, který obsahuje vaše řešení IoT Edge. 
   * **Výchozí platformu**: Vyberte stejnou platformu jako váš obrázky vytvořené modulu.

   Pokud máte více registrů kontejnerů k hostování vaší bitové kopie modulu, budete muset Duplikovat tuto úlohu, vyberte jiný registr kontejneru a použijte **obejít modulu nebo modulech** v upřesňujících nastaveních obejít imagí, které nejsou pro tuto konkrétního registru.

8. Vyberte **publikujte artefakty sestavení** úkol a upravte ho. Zadejte cestu k souboru k nasazení souboru generovaných úloh sestavení. Nastavte **cesta k publikování** hodnotu tak, aby odpovídaly výstupní proměnné, které jste nastavili v úkolu sestavení modulu. Například, `$(edge.DEPLOYMENT_FILE_PATH)`. Ostatní hodnoty ponechte jejich výchozí hodnoty. 

9. Otevřít **triggery** kartě a zaškrtněte políčko **aktivovat nepřetržitou integraci**. Ujistěte se, že větev, která obsahuje kód je součástí.

    ![Zapnout trigger průběžné integrace](./media/how-to-ci-cd/configure-trigger.png)

10. Uložit nový kanál sestavení s **Uložit** tlačítko.

Tento kanál je nyní nakonfigurována na automatické spuštění při nasdílení změn nového kódu do úložiště. Posledním úkolem, publikováním artefaktů kanálu aktivuje kanál pro vydávání verzí. Pokračujte k další části, chcete-li vytvořit kanál pro vydávání verzí. 

## <a name="configure-continuous-deployment"></a>Konfigurace průběžného nasazování
V této části vytvoříte kanál pro vydávání verzí, která je nakonfigurována na automatické spuštění při vašeho kanálu sestavení zahodí artefakty a protokoly nasazení se zobrazí v kanálech Azure.

V této části vytvoříte dvě různé fáze, jeden pro testovací nasazení a jeden pro nasazení v produkčním prostředí. 

### <a name="create-test-stage"></a>Vytvořit testovací fáze

Vytvoření nového kanálu a konfigurace první fáze pro zajištění kvality nasazení kvality. 

1. V **verze** kartě **+ nový kanál**. Nebo, pokud již máte kanály pro vydávání, vyberte **+ nová** tlačítko a vyberte **+ nový kanál verze**.  

    ![Přidejte kanál pro vydávání verzí](./media/how-to-ci-cd/add-release-pipeline.png)

2. Po zobrazení výzvy vyberte šablonu, zvolte možnost začít s **prázdný úlohy**.

    ![Začít s prázdnou úlohu](./media/how-to-ci-cd/start-with-empty-job.png)

3. Inicializuje nový kanál pro vydávání verzí pomocí jedné fáze, volá **fáze 1**. Přejmenovat fáze 1 na **QA** a ji považovat za testovací prostředí. Postupů plynulého nasazování obvykle mít více fázích. Můžete vytvořit více založené na vaše postupy DevOps. Jakmile bude přejmenován, zavřete okno Podrobnosti fázi. 

    ![Vytvořit testovací prostředí fáze](./media/how-to-ci-cd/QA-env.png)

4. Propojte vydání artefaktů sestavení, které jsou publikované pomocí kanálu sestavení. Klikněte na tlačítko **přidat** v oblasti artefakty.

   ![Přidat artefakty](./media/how-to-ci-cd/add-artifacts.png)  
    
5. V **přidání artefaktu stránky**, vyberte typ zdroje **sestavení**. Vyberte projekt a sestavení kanál, který jste vytvořili. Pak vyberte **Přidat**.

   ![Přidejte artefakt sestavení](./media/how-to-ci-cd/add-an-artifact.png)

6. Otevřete artefaktů aktivační události a vyberte přepínač, který povolí trigger průběžného nasazování. Nyní nová verze vytvoří pokaždé, když je k dispozici nové sestavení.

   ![Konfigurace aktivační události pro průběžné nasazování](./media/how-to-ci-cd/add-a-trigger.png)

7. **QA** fáze je předem nakonfigurován se jednu úlohu a úkoly, nula. V nabídce kanálu vyberte **úlohy** klikněte na tlačítko **QA** fázi.  Vyberte počet úloh a ke konfiguraci úlohy v této fázi.

    ![Konfigurace úloh dotazů a odpovědí](./media/how-to-ci-cd/view-stage-tasks.png)

8. Ve fázi dotazů a odpovědí, byste měli vidět výchozí **úlohu agenta**. Můžete nakonfigurovat podrobnosti o úlohu agenta, ale úloha nasazení je nezávislé na platformě, takže můžete použít buď **hostované VS2017** nebo **hostované 1604 Ubuntu** v **fondu Agentských**(nebo jiné spravované sami agenty). 

9. Vyberte znaménko plus (**+**) Chcete-li přidat jeden úkol. Vyhledat a přidat **Azure IoT Edge**. 

    ![Přidání úkolů pro kontrolu kvality](./media/how-to-ci-cd/add-task-qa.png)

10. Vyberte novou úlohu Azure IoT Edge a nakonfigurujte následující hodnoty:

   * **Zobrazovaný název**: Zobrazovaný název se aktualizuje automaticky při změně pole akce. 
   * **Akce**: Pomocí rozevíracího seznamu vyberte **nasadit do zařízení IoT Edge**. Změna hodnoty akce rovněž aktualizuje tak, aby odpovídaly zobrazovaný název úlohy.
   * **Předplatné Azure**: Vyberte předplatné, které obsahuje váš IoT Hub.
   * **Název centra IoT**: Vyberte své Centrum IoT. 
   * **Vyberte jeden nebo několik zařízení**: Vyberte, zda má kanál pro vydávání verzí pro nasazení na jeden nebo více zařízení. 
      * Pokud provádíte nasazení na jedno zařízení, zadejte **ID zařízení IoT Edge**. 
      * Pokud provádíte nasazení na více zařízeních, zadejte zařízení **cílová podmínka**. Cílová podmínka se filtr tak, aby odpovídaly sadu hraničních zařízení ve službě IoT Hub. Pokud chcete použít značky zařízení jako podmínka, musíte aktualizovat dvojče zařízení služby IoT Hub zařízení odpovídající značky. Aktualizace **ID nasazení IoT Edge** a **prioritu nasazení IoT Edge** v upřesňujících nastaveních. Další informace o vytváření nasazení pro různá zařízení, najdete v části [vysvětlení nasazení IoT Edge automatické](module-deployment-monitoring.md).

11. Vyberte **Uložit** uložte provedené změny do nové kanál pro vydávání verzí. Vrátit do zobrazení kanálu tak, že vyberete **kanálu** z nabídky. 

### <a name="create-production-stage"></a>Vytvoření produkční fáze

Vytvořte druhé fáze v kanálu pro vydávání verzí pro produkční nasazení. 

1. Díky klonování fázi QA druhé fáze pro produkční prostředí. Přejeďte kurzorem přes fázi dotazů a odpovědí a potom vyberte tlačítko clone. 

    ![Klonovat fáze](./media/how-to-ci-cd/clone-stage.png)

2. Vyberte nový fázi, volá **kopírování QA**, otevřete jeho vlastnosti. Změna názvu fázi **produkční**, pro produkční prostředí. Zavřete okno Vlastnosti fázi. 

3. Otevřít úlohy produkční fázi, vyberte **úlohy** kanálu nabídce klikněte na tlačítko **produkční** fázi. 

4. Vyberte úlohu Azure IoT Edge ke konfiguraci Pokud pro vaše produkční prostředí. Nastavení nasazení jsou pravděpodobně stejné pro kontrolu kvality a výroba, s tím rozdílem, že chcete cílí na skupiny zařízení v produkčním prostředí nebo jiné zařízení. Aktualizujte pole ID zařízení nebo pole cílové podmínku a nasazení ID pro produkční zařízení. 

5. Uložte ji **Uložit** tlačítko. A pak vyberte **kanálu** chcete přejít zpátky k zobrazení kanálu.
    
6. Tento kanál pro vydávání verzí je aktuálně nakonfigurován případech artefakt sestavení spustí **dotazů a odpovědí** fáze a potom **produkční** fáze pokaždé, když se dokončí sestavování nové. Ale obvykle má být integrován některé testovací případy na zařízeních, dotazů a odpovědí a ručně schvalovat nasazení pro produkční prostředí. Chcete-li vytvořit podmínku schválení pro produkční fázi, postupujte následovně:

    1. Otevřít **podmínky před nasazením** panel nastavení.

        ![Otevřít před nasazením podmínky](./media/how-to-ci-cd/pre-deploy-conditions.png)    

    2. Přepnout **schválení před nasazením** podmínky k **povoleno**. Přidejte jeden nebo více uživatelů nebo skupin v **schvalovatelů** pole a upravit všechny ostatní, které mají zásady schválení. Pokud chcete změny uložit, zavřete panel podmínky před nasazením.
    
       ![Nastavení podmínek](./media/how-to-ci-cd/set-pre-deployment-conditions.png)


7. Uložení kanálu pro vydávání verzí s **Uložit** tlačítko. 

    
## <a name="verify-iot-edge-cicd-with-the-build-and-release-pipelines"></a>Ověřte IoT Edge CI/CD s sestavení a vydávání kanálů

K aktivaci úlohy sestavení, můžete potvrdíte a vložíte změny do úložiště zdrojového kódu nebo ruční spuštění. V této části ručně aktivujete kanál CI/CD pro testování, zda funguje. Ověřte, jestli nasazení proběhne úspěšně.

1. Přejděte do kanálu sestavení, který jste vytvořili na začátku tohoto článku. 

2. Můžete aktivovat úlohu sestavení a ve vašem kanálu sestavení tak, že vyberete **fronty** tlačítko stejně jako v následujícím snímku obrazovky.

    ![Ruční aktivační události](./media/how-to-ci-cd/manual-trigger.png)

3. Vyberte úlohu sestavení a sledujte průběh. Pokud kanál sestavení bylo úspěšně dokončeno, aktivaci vydání do **QA** fázi. 

    ![Protokoly o sestavení](./media/how-to-ci-cd/build-logs.png)

4. Úspěšné nasazení **QA** fáze spustí oznámení schvalovateli. Ověřte, že moduly úspěšně nasazena na zařízení nebo zařízení, která jste určili pomocí fázi dotazů a odpovědí. Pak přejděte do kanál verze a poskytněte schválení pro uvolnění pro přechod na produkční fázi tak, že vyberete **produkční** tlačítko a pak vyberete **schválit**. 

    ![Čeká na schválení](./media/how-to-ci-cd/pending-approval.png)

5. Jakmile schvalující schválit tuto změnu, je možné nasadit do **produkční**.

## <a name="next-steps"></a>Další postup

* Vysvětlení nasazení IoT Edge v [vysvětlení nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md)
* Provede kroky k vytvoření, aktualizace nebo odstranění nasazení v [nasadit a monitorovat moduly IoT Edge ve velkém měřítku](how-to-deploy-monitor.md).
