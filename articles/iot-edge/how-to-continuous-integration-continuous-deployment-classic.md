---
title: Průběžná integrace a průběžné nasazování na zařízení Azure IoT Edge (klasický Editor) – Azure IoT Edge
description: Nastavení průběžné integrace a průběžného nasazování pomocí klasického editoru Azure IoT Edge s využitím Azure DevOps Azure Pipelines
author: shizn
manager: philmea
ms.author: xshi
ms.date: 08/26/2020
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 9cb1a2074e7ec64ed16f1f7c9a1f70bf2307b5c3
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90033485"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices-classic-editor"></a>Průběžná integrace a průběžné nasazování na zařízení Azure IoT Edge (klasický Editor)

Pomocí integrovaných Azure IoT Edge úloh v Azure Pipelines můžete snadno přijmout DevOps s aplikacemi Azure IoT Edge. Tento článek ukazuje, jak můžete používat funkce průběžné integrace a průběžného nasazování Azure Pipelines k vytváření, testování a nasazování aplikací rychle a efektivně do Azure IoT Edge pomocí klasického editoru. Alternativně můžete [použít YAML](how-to-continuous-integration-continuous-deployment.md).

![Diagram – větve CI a CD pro vývoj a produkci](./media/how-to-continuous-integration-continuous-deployment-classic/model.png)

V tomto článku se naučíte, jak používat integrované [úlohy Azure IoT Edge](https://docs.microsoft.com/azure/devops/pipelines/tasks/build/azure-iot-edge) pro Azure Pipelines k vytváření kanálů sestavení a vydání pro vaše řešení IoT Edge. Každý Azure IoT Edge úkol přidaný do vašeho kanálu implementuje jednu z následujících čtyř akcí:

 | Akce | Popis |
 | --- | --- |
 | Image modulu sestavení | Převezme IoT Edge kód řešení a sestaví image kontejneru.|
 | Image modulu push | Vloží image modulu do určeného registru kontejneru. |
 | Generovat manifest nasazení | Převezme deployment.template.jssouboru a proměnných a pak vygeneruje konečný soubor manifestu nasazení IoT Edge. |
 | Nasazení do zařízení IoT Edge | Vytvoří IoT Edge nasazení do jednoho nebo více IoT Edge zařízení. |

Pokud není uvedeno jinak, postupy v tomto článku nezkoumá všechny funkce dostupné prostřednictvím parametrů úlohy. Další informace najdete v následujících článcích:

* [Verze úlohy](https://docs.microsoft.com/azure/devops/pipelines/process/tasks?view=azure-devops&tabs=classic#task-versions)
* **Pokročilá** – Pokud je k dispozici, zadejte moduly, které nechcete sestavit.
* [Možnosti ovládacích prvků](https://docs.microsoft.com/azure/devops/pipelines/process/tasks?view=azure-devops&tabs=classic#task-control-options)
* [Proměnné prostředí](https://docs.microsoft.com/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch#environment-variables)
* [Výstupní proměnné](https://docs.microsoft.com/azure/devops/pipelines/process/variables?view=azure-devops&tabs=yaml%2Cbatch#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Požadavky

* Úložiště Azure Repos. Pokud ho ještě nemáte, můžete [v projektu vytvořit nové úložiště Git](https://docs.microsoft.com/azure/devops/repos/git/create-new-repo?view=vsts&tabs=new-nav). V tomto článku jsme vytvořili úložiště s názvem **IoTEdgeRepo**.
* Řešení IoT Edge potvrzené a vložené do úložiště. Chcete-li vytvořit nové ukázkové řešení pro testování tohoto článku, postupujte podle kroků v tématu [vývoj a ladění modulů v Visual Studio Code](how-to-vs-code-develop-module.md) nebo [vývoj a ladění modulů C# v aplikaci Visual Studio](how-to-visual-studio-develop-csharp-module.md). V tomto článku jsme vytvořili řešení v našem úložišti s názvem **IoTEdgeSolution**, které obsahuje kód pro modul s názvem **filtermodule**.

   Pro tento článek stačí, když je složka řešení vytvořená IoT Edge šablonami v Visual Studio Code nebo v aplikaci Visual Studio. Než budete pokračovat, nemusíte tento kód sestavovat, předávat, nasazovat ani ladit. Tyto procesy nastavíte v Azure Pipelines.

   Pokud vytváříte nové řešení, naklonujte své úložiště jako první. Pak při vytváření řešení si můžete zvolit, že se má vytvořit přímo ve složce úložiště. Z nich můžete snadno potvrzovat a nabízet nové soubory.

* Registr kontejnerů, kde můžete vkládat image modulu. Můžete použít [Azure Container Registry](https://docs.microsoft.com/azure/container-registry/) nebo v registru třetí strany.
* Aktivní Azure [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) s aspoň dvěma IoT Edgemi zařízeními pro testování samostatných fází testování a produkčního nasazení. Pomocí článků rychlý Start můžete vytvořit zařízení IoT Edge v systému [Linux](quickstart-linux.md) nebo [Windows](quickstart.md) .

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Vytvoření kanálu sestavení pro kontinuální integraci

V této části vytvoříte nový kanál sestavení. Nakonfigurujete, aby se kanál spouštěl automaticky při vrácení změn se změnami ukázkového IoT Edge řešení a publikování protokolů sestavení.

1. Přihlaste se ke svojí organizaci Azure DevOps ( `https://dev.azure.com/{your organization}` ) a otevřete projekt, který obsahuje úložiště řešení IoT Edge.

    ![Otevřete projekt DevOps](./media/how-to-continuous-integration-continuous-deployment-classic/initial-project.png)

2. V nabídce levého podokna v projektu vyberte **kanály**. V středu stránky vyberte **vytvořit kanál** . Nebo pokud již máte kanály sestavení, vyberte tlačítko **Nový kanál** v pravém horním rohu.

    ![Vytvoření nového kanálu sestavení](./media/how-to-continuous-integration-continuous-deployment-classic/add-new-pipeline.png)

3. V dolní části stránky **kde je váš kód?** vyberte **použít klasický Editor**. Pokud chcete vytvořit kanály sestavení projektu pomocí YAML, přečtěte si [příručku YAML](how-to-continuous-integration-continuous-deployment.md).

    ![Vyberte použít klasický Editor.](./media/how-to-continuous-integration-continuous-deployment-classic/create-without-yaml.png)

4. Podle pokynů vytvořte svůj kanál.

   1. Zadejte zdrojové informace pro nový kanál sestavení. Jako zdroj vyberte **Azure Repos Git** a pak vyberte projekt, úložiště a větev, kde se nachází váš kód řešení IoT Edge. Pak vyberte **pokračovat**.

      ![Vyberte zdroj kanálu.](./media/how-to-continuous-integration-continuous-deployment-classic/pipeline-source.png)

   2. Vyberte **prázdnou úlohu** místo šablony.

      ![Začínáme s prázdnou úlohou pro svůj kanál sestavení](./media/how-to-continuous-integration-continuous-deployment-classic/start-with-empty-build-job.png)

5. Po vytvoření kanálu přejdete do editoru kanálů. Tady můžete změnit název kanálu, fond agentů a specifikaci agenta.

   Můžete vybrat fond hostovaný Microsoftem nebo vámi spravovaný fond s místním hostitelem.

   V popisu kanálu vyberte správnou specifikaci agenta podle vaší cílové platformy:

   * Pokud chcete sestavit moduly v kontejnerech AMD64 pro Linux, vyberte **Ubuntu-16,04** .

   * Pokud chcete sestavit moduly v kontejnerech AMD64 pro Windows 1809, musíte [nastavit agenta v místním prostředí ve Windows](https://docs.microsoft.com/azure/devops/pipelines/agents/v2-windows?view=vsts).

   * Pokud chcete sestavit moduly v kontejnerech platforem arm32v7 nebo arm64 pro Linux, musíte [nastavit agenta v místním](https://devblogs.microsoft.com/iotdev/setup-azure-iot-edge-ci-cd-pipeline-with-arm-agent)prostředí pro Linux.

    ![Konfigurace specifikace agenta sestavení](./media/how-to-continuous-integration-continuous-deployment-classic/configure-env.png)

6. Váš kanál je předem nakonfigurovaný s úlohou s názvem **úloha agenta 1**. Vyberte znaménko plus ( **+** ) a přidejte čtyři úkoly do úlohy: **Azure IoT Edge** dvakrát, **Kopírovat soubory** jednou a **publikovat artefakty sestavení** jednou. Vyhledejte každý úkol a najeďte myší na jeho název, aby se zobrazilo tlačítko **Přidat** .

   ![Přidat úlohu Azure IoT Edge](./media/how-to-continuous-integration-continuous-deployment-classic/add-iot-edge-task.png)

   Po přidání všech čtyř úkolů bude úloha agenta vypadat jako v následujícím příkladu:

   ![Čtyři úlohy v kanálu sestavení](./media/how-to-continuous-integration-continuous-deployment-classic/add-tasks.png)

7. Vyberte první úlohu **Azure IoT Edge** , kterou chcete upravit. Tato úloha vytvoří všechny moduly v řešení s cílovou platformou, kterou určíte. Upravte úlohu s následujícími hodnotami:

    | Parametr | Popis |
    | --- | --- |
    | Zobrazované jméno | Zobrazovaný název se automaticky aktualizuje při změně pole akce. |
    | Akce | Vyberte **Image modulu sestavení**. |
    | .template.jsv souboru | Vyberte tři tečky (**...**) a přejděte do **deployment.template.js** v souboru v úložišti, které obsahuje vaše řešení IoT Edge. |
    | Výchozí platforma | Vyberte odpovídající operační systém pro vaše moduly na základě cíleného zařízení IoT Edge. |
    | Výstupní proměnné | Zadejte referenční název, který chcete přidružit k cestě k souboru, kde deployment.jsv souboru vygeneruje, jako je například **Edge**. |

   Tyto konfigurace používají úložiště a značku bitové kopie, které jsou definovány v `module.json` souboru pro pojmenování a označení image modulu. **Image modulu sestavení** také pomáhají nahradit proměnné stejnou hodnotou, kterou definujete v `module.json` souboru. V aplikaci Visual Studio nebo Visual Studio Code zadáváte skutečnou hodnotu v `.env` souboru. V Azure Pipelines nastavte hodnotu na kartě **proměnné kanálu** . V nabídce editor kanálů vyberte kartu **proměnné** a nakonfigurujte název a hodnotu následujícím způsobem:

    * **ACR_ADDRESS**: hodnota **přihlašovacího serveru** Azure Container Registry. Přihlašovací server můžete načíst ze stránky přehled v registru kontejneru v Azure Portal.

    Pokud máte v projektu jiné proměnné, můžete na této kartě zadat název a hodnotu. **Image modulu sestavení** rozpoznává pouze proměnné, které jsou ve `${VARIABLE}` formátu. Ujistěte se, že ve svých souborech používáte tento formát `**/module.json` .

8. Vyberte druhý úkol **Azure IoT Edge** , který chcete upravit. Tato úloha vloží všechny image modulu do registru kontejneru, který jste vybrali.

    | Parametr | Popis |
    | --- | --- |
    | Zobrazované jméno | Zobrazovaný název se automaticky aktualizuje při změně pole akce. |
    | Akce | Vyberte **Image modulu push**. |
    | Typ registru kontejneru | Použít výchozí typ: `Azure Container Registry` . |
    | Předplatné Azure | Zvolte vaše předplatné. |
    | Azure Container Registry | Vyberte typ registru kontejneru, který použijete k uložení imagí modulu. V závislosti na zvoleném typu registru se formulář změní. Pokud zvolíte **Azure Container Registry**, pomocí rozevíracích seznamů vyberte předplatné Azure a název vašeho registru kontejneru. Pokud zvolíte **obecné Container Registry**, vyberte **Nový** a vytvořte připojení služby registru. |
    | .template.jsv souboru | Vyberte tři tečky (**...**) a přejděte do **deployment.template.js** v souboru v úložišti, které obsahuje vaše řešení IoT Edge. |
    | Výchozí platforma | Vyberte odpovídající operační systém pro vaše moduly na základě cíleného zařízení IoT Edge. |
    | Přidání přihlašovacích údajů registru do manifestu nasazení | Chcete-li přidat přihlašovací údaje registru pro vložení imagí Docker do manifestu nasazení, zadejte hodnotu true. |

   Pokud máte více registrů kontejnerů pro hostování imagí modulu, musíte duplikovat tuto úlohu, vybrat jiný registr kontejnerů a v **rozšířených** nastaveních používat nepoužívané **moduly** pro obejít tak obrázky, které nejsou pro tento konkrétní registr.

9. Vyberte úlohu **kopírování souborů** a upravte ji. Pomocí této úlohy můžete kopírovat soubory do pracovního adresáře artefaktu.

    | Parametr | Popis |
    | --- | --- |
    | Zobrazované jméno | Použijte výchozí název nebo si ho přizpůsobte. |
    | Zdrojová složka | Složka se soubory, které mají být zkopírovány. |
    | Obsah | Přidejte dva řádky: `deployment.template.json` a `**/module.json` . Tyto dva soubory slouží jako vstupy pro generování manifestu nasazení IoT Edge. |
    | Cílová složka | Zadejte proměnnou `$(Build.ArtifactStagingDirectory)` . Informace o popisu najdete v tématu věnovaném [proměnným sestavení](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) . |

10. Vyberte úlohu **publikování artefaktů sestavení** a upravte ji. Zadejte cestu pracovního adresáře artefaktu k úloze, aby bylo možné cestu publikovat do kanálu uvolnění.

    | Parametr | Popis |
    | --- | --- |
    | Zobrazované jméno | Použijte výchozí název nebo přizpůsobení. |
    | Cesta pro publikování | Zadejte proměnnou `$(Build.ArtifactStagingDirectory)` . Další informace najdete v tématu věnovaném [proměnným sestavení](https://docs.microsoft.com/azure/devops/pipelines/build/variables?view=azure-devops&tabs=yaml#build-variables) . |
    | Název artefaktu | Použijte výchozí název: **drop** |
    | Umístění publikování artefaktu | Použijte výchozí umístění: **Azure Pipelines** |

11. Otevřete kartu **triggery** a zaškrtněte políčko pro **Povolení průběžné integrace**. Ujistěte se, že je součástí větve obsahující váš kód.

    ![Zapnout aktivační událost nepřetržitá integrace](./media/how-to-continuous-integration-continuous-deployment-classic/configure-trigger.png)

12. V rozevíracím seznamu **uložit & fronta** vyberte **Save (Uložit** ).

Tento kanál je teď nakonfigurovaný tak, aby se spouštěl automaticky při vložení nového kódu do úložiště. Poslední úkol, který publikuje artefakty kanálu, aktivuje kanál vydání. Pokračujte k další části a sestavte tak kanál pro vydávání verzí.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

>[!NOTE]
>Pokud chcete ve svém kanálu používat **vrstvená nasazení** , vrstvená nasazení ještě nejsou podporovaná v Azure IoT Edge úlohách v Azure DevOps.
>
>V Azure DevOps ale můžete použít [úlohu Azure CLI](https://docs.microsoft.com/azure/devops/pipelines/tasks/deploy/azure-cli) k vytvoření nasazení jako vrstveného nasazení. Pro hodnotu **vloženého skriptu** můžete použít [příkaz AZ IoT Edge Deployment Create](https://docs.microsoft.com/cli/azure/ext/azure-cli-iot-ext/iot/edge/deployment):
>
>   ```azurecli-interactive
>   az iot edge deployment create -d {deployment_name} -n {hub_name} --content modules_content.json --layered true
>   ```

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Další kroky

* Ukázka osvědčených postupů pro IoT Edge DevOps v [Azure DevOps Starter pro IoT Edge](how-to-devops-starter.md)
* Pochopení nasazení IoT Edge v části [Principy nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md)
* Projděte si postup vytvoření, aktualizace nebo odstranění nasazení v části [nasazení a monitorování IoT Edgech modulů ve velkém měřítku](how-to-deploy-at-scale.md).
