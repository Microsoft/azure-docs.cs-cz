---
author: v-tcassi
ms.service: iot-edge
ms.topic: include
ms.date: 08/26/2020
ms.author: v-tcassi
ms.openlocfilehash: 9572f4c663c820c76a57cdbdcecff082b150b577
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "104761118"
---
## <a name="create-a-release-pipeline-for-continuous-deployment"></a>Vytvoření kanálu vydání pro průběžné nasazování

V této části vytvoříte kanál pro vydávání verzí, který je nakonfigurován tak, aby běžel automaticky v případě, že kanál sestavení vyřazuje artefakty a zobrazí v Azure Pipelines protokoly nasazení.

Vytvořte nový kanál a přidejte novou fázi:

1. Na kartě **vydání** v části **kanály** vyberte **+ Nový kanál**. Nebo pokud již máte kanály verzí, klikněte na tlačítko **+ Nový** a vyberte **+ Nový kanál vydání**.  

    ![Přidání kanálu vydání pomocí tlačítka + nový kanál](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-release-pipeline.png)

2. Po zobrazení výzvy k výběru šablony zvolte, že chcete začít s **prázdnou úlohou**.

    ![Začínáme s prázdnou úlohou pro svůj kanál pro vydávání verzí](./media/iot-edge-create-release-pipeline-for-continuous-deployment/start-with-empty-release-job.png)

3. Nový kanál vydaných verzí se inicializuje s jednou fází nazvanou **fáze 1**. Přejmenujte fázi 1 na **vývoj** a považovat ji za kanál průběžného nasazování pro vaše vývojové prostředí. Kanály průběžného nasazování mají obvykle několik fází včetně **vývoje**, **přípravy** a **produkčního** prostředí. Můžete použít různé názvy a vytvořit další v závislosti na DevOps praxi. Po přejmenování zavřete okno Podrobnosti fáze.

   Kanál pro vydávání verzí můžete také přejmenovat tak, že v horní části vyberete text nový kanál verze.

4. Propojte tuto verzi s artefakty sestavení, které jsou publikovány kanálem sestavení. V oblasti artefakty klikněte na **Přidat** .

   ![Klikněte na Přidat v oblasti artefakty rozhraní.](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifacts.png)

5. Na **stránce Přidat artefakt** vyberte **sestavení** jako **typ zdroje**. Vyberte projekt a kanál sestavení, který jste vytvořili. Pokud chcete, můžete **zdrojový alias** změnit na výstižnější. Pak vyberte **Přidat**.

   ![Na stránce Přidat artefakt vyberte Přidat a vytvořte artefakt.](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-artifact.png)

6. Otevřete triggery artefaktů a výběrem přepínače povolte aktivační událost průběžného nasazování. Nyní se vytvoří nová vydaná verze pokaždé, když bude k dispozici nové sestavení.

   ![Otevřete triggery artefaktů a přepněte se na povolení triggeru průběžného nasazování.](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-trigger.png)

7. Fáze **vývoje** je předem nakonfigurovaná s jednou úlohou a nulovými úkoly. V nabídce kanálu vyberte **úlohy** a pak zvolte fázi **vývoje** . Vyberte **úlohu agenta** a změňte její **Zobrazovaný název** na **QA**. Můžete nakonfigurovat podrobnosti o úloze agenta, ale úloha nasazení je nezávislá na platformě, takže můžete použít jakoukoli **specifikaci agenta** ve zvoleném **fondu agentů**.

   ![Zobrazit úlohy pro vaši fázi vývoje na kartě úlohy](./media/iot-edge-create-release-pipeline-for-continuous-deployment/view-stage-tasks.png)

8. V úloze QA vyberte znaménko plus ( **+** ) a přidejte dvě úlohy. Vyhledejte a přidejte **Azure IoT Edge** dvakrát.

9. Vyberte první úlohu **Azure IoT Edge** a nakonfigurujte ji s následujícími hodnotami:

    | Parametr | Popis |
    | --- | --- |
    | Zobrazované jméno | Zobrazovaný název se automaticky aktualizuje při změně pole akce. |
    | Akce | Vyberte `Generate deployment manifest`. |
    | .template.jsv souboru | Zadejte cestu: `$(System.DefaultWorkingDirectory)/Drop/drop/deployment.template.json` . Cesta je publikovaná z kanálu sestavení. |
    | Výchozí platforma | Vyberte odpovídající operační systém pro vaše moduly na základě cíleného zařízení IoT Edge. |
    | Výstupní cesta| Vložte cestu `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Tato cesta je konečným souborem manifestu nasazení IoT Edge. |

    Tyto konfigurace pomáhají nahradit adresy URL imagí modulu v `deployment.template.json` souboru. **Manifest pro generování nasazení** také pomáhá nahradit proměnné s přesnou hodnotu, kterou jste definovali v `deployment.template.json` souboru. V VS/VS Code zadáváte skutečnou hodnotu v `.env` souboru. V Azure Pipelines nastavíte hodnotu na kartě **proměnné kanálu uvolnění** . Přejděte na kartu **proměnné** a nakonfigurujte název a hodnotu následujícím způsobem:

    * **ACR_ADDRESS**: hodnota **přihlašovacího serveru** Azure Container Registry. Přihlašovací server můžete načíst ze stránky přehled v registru kontejneru v Azure Portal.
    * **ACR_PASSWORD**: vaše heslo Azure Container Registry.
    * **ACR_USER**: vaše Azure Container Registry uživatelské jméno.

    Pokud máte v projektu další proměnné, můžete na této kartě zadat název a hodnotu. **Manifest pro generování nasazení** může rozpoznat pouze proměnné, které jsou v `${VARIABLE}` charakteru. Ujistěte se, že tento charakter používáte ve svých `*.template.json` souborech.
    
    ```json-interactive
    "registryCredentials": {
      "<ACR name>": { // Your Azure Container Registry **Registry name** value
        "username": "${ACR_USER}",
        "password": "${ACR_PASSWORD}",
        "address": "${ACR_ADDRESS}"
      }
    }
    ```
    
    ![Konfigurace proměnných pro kanál verze na kartě proměnné](./media/iot-edge-create-release-pipeline-for-continuous-deployment/configure-variables.png)

10. Vyberte druhou úlohu **Azure IoT Edge** a nakonfigurujte ji s následujícími hodnotami:

    | Parametr | Popis |
    | --- | --- |
    | Zobrazované jméno | Zobrazovaný název se automaticky aktualizuje při změně pole akce. |
    | Akce | Vyberte `Deploy to IoT Edge devices`. |
    | Soubor nasazení | Vložte cestu `$(System.DefaultWorkingDirectory)/Drop/drop/configs/deployment.json` . Tato cesta je soubor manifestu nasazení IoT Edge souboru. |
    | Předplatné Azure | Vyberte předplatné, které obsahuje vaše IoT Hub.|
    | Název IoT Hub | Vyberte centrum IoT.|
    | Vybrat jedno nebo více zařízení | Vyberte, zda chcete, aby byl kanál verze nasazen do jednoho nebo více zařízení. Pokud nasadíte do jednoho zařízení, zadejte **ID zařízení IoT Edge**. Pokud nasazujete na více zařízení, zadejte **podmínku cíle** zařízení. Podmínka cíle je filtr, který odpovídá sadě IoT Edge zařízení v IoT Hub. Pokud chcete jako podmínku použít značky zařízení, je potřeba aktualizovat značky odpovídajících zařízení pomocí IoT Hub s dvojitou podmínkou zařízení. Aktualizujte **ID nasazení IoT Edge** a **prioritu nasazení IoT Edge** v rozšířených nastaveních. Další informace o vytváření nasazení pro více zařízení najdete v tématu [principy IoT Edgeho automatického nasazení](../articles/iot-edge/module-deployment-monitoring.md). |
    | ID zařízení nebo cílová podmínka | V závislosti na předchozím výběru zadejte ID zařízení nebo [cílovou podmínku](../articles/iot-edge/module-deployment-monitoring.md#target-condition) pro nasazení do více zařízení. |
    | Pokročilý | Pro ID nasazení IoT Edge zadejte `$(System.TeamProject)-$(Release.EnvironmentName)` . Tato proměnná mapuje název projektu a verze s vaším ID nasazení IoT Edge. |

    ![Přidání úloh Azure IoT Edge pro vaši fázi vývoje](./media/iot-edge-create-release-pipeline-for-continuous-deployment/add-quality-assurance-task.png)

11. Vyberte **Uložit** a uložte změny do nového kanálu vydání. Vraťte se do Zobrazení kanálu výběrem karty **kanálu** z nabídky.
