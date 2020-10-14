---
title: Průběžná integrace a průběžné nasazování pro Azure IoT Edge zařízení – Azure IoT Edge
description: Pomocí YAML-Azure IoT Edge s Azure DevOps nastavte průběžnou integraci a průběžné nasazování Azure Pipelines
author: shizn
manager: philmea
ms.author: kgremban
ms.date: 08/20/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: 57031d4ccdfdba73b8b36c8dc943280a8280ffcc
ms.sourcegitcommit: 2e72661f4853cd42bb4f0b2ded4271b22dc10a52
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92048521"
---
# <a name="continuous-integration-and-continuous-deployment-to-azure-iot-edge-devices"></a>Průběžná integrace a průběžné nasazování pro Azure IoT Edge zařízení

Pomocí integrovaných Azure IoT Edge úloh v Azure Pipelines můžete snadno přijmout DevOps s aplikacemi Azure IoT Edge. Tento článek ukazuje, jak můžete pomocí funkcí průběžné integrace a průběžného nasazování Azure Pipelines sestavovat, testovat a nasazovat aplikace rychle a efektivně do vašich Azure IoT Edge pomocí YAML. Alternativně můžete [použít klasický Editor](how-to-continuous-integration-continuous-deployment-classic.md).

![Diagram – větve CI a CD pro vývoj a produkci](./media/how-to-continuous-integration-continuous-deployment/model.png)

V tomto článku se naučíte, jak používat integrované [úlohy Azure IoT Edge](/azure/devops/pipelines/tasks/build/azure-iot-edge) pro Azure Pipelines k vytváření kanálů sestavení a vydání pro vaše řešení IoT Edge. Každý Azure IoT Edge úkol přidaný do vašeho kanálu implementuje jednu z následujících čtyř akcí:

 | Akce | Popis |
 | --- | --- |
 | Image modulu sestavení | Převezme IoT Edge kód řešení a sestaví image kontejneru.|
 | Image modulu push | Vloží image modulu do určeného registru kontejneru. |
 | Generovat manifest nasazení | Převezme deployment.template.jssouboru a proměnných a pak vygeneruje konečný soubor manifestu nasazení IoT Edge. |
 | Nasazení do zařízení IoT Edge | Vytvoří IoT Edge nasazení do jednoho nebo více IoT Edge zařízení. |

Pokud není uvedeno jinak, postupy v tomto článku nezkoumá všechny funkce dostupné prostřednictvím parametrů úlohy. Další informace najdete v následujících článcích:

* [Verze úlohy](/azure/devops/pipelines/process/tasks?tabs=classic&view=azure-devops#task-versions)
* **Pokročilá** – Pokud je k dispozici, zadejte moduly, které nechcete sestavit.
* [Možnosti ovládacích prvků](/azure/devops/pipelines/process/tasks?tabs=classic&view=azure-devops#task-control-options)
* [Proměnné prostředí](/azure/devops/pipelines/process/variables?tabs=yaml%252cbatch&view=azure-devops#environment-variables)
* [Výstupní proměnné](/azure/devops/pipelines/process/variables?tabs=yaml%252cbatch&view=azure-devops#use-output-variables-from-tasks)

## <a name="prerequisites"></a>Požadované součásti

* Úložiště Azure Repos. Pokud ho ještě nemáte, můžete [v projektu vytvořit nové úložiště Git](/azure/devops/repos/git/create-new-repo?tabs=new-nav&view=vsts). V tomto článku jsme vytvořili úložiště s názvem **IoTEdgeRepo**.
* Řešení IoT Edge potvrzené a vložené do úložiště. Chcete-li vytvořit nové ukázkové řešení pro testování tohoto článku, postupujte podle kroků v tématu [vývoj a ladění modulů v Visual Studio Code](how-to-vs-code-develop-module.md) nebo [vývoj a ladění modulů C# v aplikaci Visual Studio](./how-to-visual-studio-develop-module.md). V tomto článku jsme vytvořili řešení v našem úložišti s názvem **IoTEdgeSolution**, které obsahuje kód pro modul s názvem **filtermodule**.

   Pro tento článek stačí, když je složka řešení vytvořená IoT Edge šablonami v Visual Studio Code nebo v aplikaci Visual Studio. Než budete pokračovat, nemusíte tento kód sestavovat, předávat, nasazovat ani ladit. Tyto procesy nastavíte v Azure Pipelines.

   Pokud vytváříte nové řešení, naklonujte své úložiště jako první. Pak při vytváření řešení si můžete zvolit, že se má vytvořit přímo ve složce úložiště. Z nich můžete snadno potvrzovat a nabízet nové soubory.

* Registr kontejnerů, kde můžete vkládat image modulu. Můžete použít [Azure Container Registry](../container-registry/index.yml) nebo v registru třetí strany.
* Aktivní Azure [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) s aspoň dvěma IoT Edgemi zařízeními pro testování samostatných fází testování a produkčního nasazení. Pomocí článků rychlý Start můžete vytvořit zařízení IoT Edge v systému [Linux](quickstart-linux.md) nebo [Windows](quickstart.md) .

Další informace o použití Azure Repos naleznete v tématu [sdílení kódu v aplikaci Visual Studio a Azure Repos](/azure/devops/repos/git/share-your-code-in-git-vs?view=vsts)

## <a name="create-a-build-pipeline-for-continuous-integration"></a>Vytvoření kanálu sestavení pro kontinuální integraci

V této části vytvoříte nový kanál sestavení. Nakonfigurujete, aby se kanál spouštěl automaticky při vrácení změn se změnami ukázkového IoT Edge řešení a publikování protokolů sestavení.

1. Přihlaste se ke svojí organizaci Azure DevOps ( `https://dev.azure.com/{your organization}` ) a otevřete projekt, který obsahuje úložiště řešení IoT Edge.

   ![Otevřete projekt DevOps](./media/how-to-continuous-integration-continuous-deployment/initial-project.png)

2. V nabídce levého podokna v projektu vyberte **kanály**. V středu stránky vyberte **vytvořit kanál** . Nebo pokud již máte kanály sestavení, vyberte tlačítko **Nový kanál** v pravém horním rohu.

    ![Vytvoření nového kanálu sestavení pomocí tlačítka nový kanál](./media/how-to-continuous-integration-continuous-deployment/add-new-pipeline.png)

3. Na stránce **kde je váš kód?** vyberte **Azure Repos Git `YAML` **. Pokud chcete použít klasický editor k vytvoření kanálů sestavení projektu, přečtěte si článek [Průvodce klasickým editorem](how-to-continuous-integration-continuous-deployment-classic.md).

4. Vyberte úložiště, pro které vytváříte kanál.

    ![Výběr úložiště pro kanál sestavení](./media/how-to-continuous-integration-continuous-deployment/select-repository.png)

5. Na stránce **konfigurace vašeho kanálu** vyberte **Počáteční kanál**. Pokud máte existující soubor Azure Pipelines YAML, který chcete použít k vytvoření tohoto kanálu, můžete vybrat **existující Azure Pipelines soubor YAML** a zadat větev a cestu do úložiště do souboru.

    ![Vyberte počáteční kanál nebo existující soubor Azure Pipelines YAML a začněte svůj kanál sestavení.](./media/how-to-continuous-integration-continuous-deployment/configure-pipeline.png)

6. Na stránce **Zkontrolujte YAML kanálu** můžete kliknutím na výchozí název `azure-pipelines.yml` Přejmenovat konfigurační soubor kanálu.

   Výběrem **Zobrazit pomocníka** otevřete paletu **úkoly** .

    ![Výběrem Zobrazit pomocníka otevřete paletu úlohy.](./media/how-to-continuous-integration-continuous-deployment/show-assistant.png)

7. Pokud chcete přidat úlohu, umístěte kurzor na konec YAML nebo kdekoli chcete, aby se přidaly pokyny k vašemu úkolu. Vyhledejte a vyberte **Azure IoT Edge**. Vyplňte parametry úlohy následujícím způsobem. Pak vyberte **Přidat**.

   | Parametr | Popis |
   | --- | --- |
   | Akce | Vyberte **Image modulu sestavení**. |
   | .template.jsv souboru | Zadejte cestu k **deployment.template.js** v souboru v úložišti, které obsahuje vaše řešení IoT Edge. |
   | Výchozí platforma | Vyberte odpovídající operační systém pro vaše moduly na základě cíleného zařízení IoT Edge. |

    ![Použití palety úlohy k přidání úkolů do kanálu](./media/how-to-continuous-integration-continuous-deployment/add-build-task.png)

   >[!TIP]
   > Po přidání každého úkolu Editor automaticky zvýrazní přidané řádky. Chcete-li zabránit náhodnému přepsání, zrušte výběr řádků a před přidáním dalších úkolů zadejte nové místo pro svůj další úkol.

8. Tento postup opakujte, pokud chcete přidat tři další úkoly s následujícími parametry:

   * Úkol: **Azure IoT Edge**

       | Parametr | Popis |
       | --- | --- |
       | Akce | Vyberte **Image modulu push**. |
       | Typ registru kontejneru | Použijte výchozí typ: **Azure Container Registry**. |
       | Předplatné Azure | Vyberte své předplatné. |
       | Azure Container Registry | Vyberte registr, který chcete použít pro kanál. |
       | .template.jsv souboru | Zadejte cestu k **deployment.template.js** v souboru v úložišti, které obsahuje vaše řešení IoT Edge. |
       | Výchozí platforma | Vyberte odpovídající operační systém pro vaše moduly na základě cíleného zařízení IoT Edge. |

   * Úkol: **kopírování souborů**

       | Parametr | Popis |
       | --- | --- |
       | Zdrojová složka | Zdrojová složka, ze které se má kopírovat. Prázdné je kořen úložiště. Použijte proměnné, pokud soubory nejsou v úložišti. Příklad: `$(agent.builddirectory)`.
       | Obsah | Přidejte dva řádky: `deployment.template.json` a `**/module.json` . |
       | Cílová složka | Zadejte proměnnou `$(Build.ArtifactStagingDirectory)` . Informace o popisu najdete v tématu věnovaném [proměnným sestavení](/azure/devops/pipelines/build/variables?tabs=yaml&view=azure-devops#build-variables) . |

   * Úkol: **publikování artefaktů sestavení**

       | Parametr | Popis |
       | --- | --- |
       | Cesta pro publikování | Zadejte proměnnou `$(Build.ArtifactStagingDirectory)` . Informace o popisu najdete v tématu věnovaném [proměnným sestavení](/azure/devops/pipelines/build/variables?tabs=yaml&view=azure-devops#build-variables) . |
       | Název artefaktu | Zadejte výchozí název: `drop` |
       | Umístění publikování artefaktu | Použít výchozí umístění: `Azure Pipelines` |

9. V rozevíracím seznamu **Uložit a spustit** v pravém horním rohu vyberte **Save (Uložit** ).

10. Aktivační událost pro kontinuální integraci je ve výchozím nastavení povolená pro váš YAML kanál. Pokud chcete tato nastavení upravit, vyberte svůj kanál a v pravém horním rohu klikněte na **Upravit** . V pravém horním rohu vyberte **Další akce** vedle tlačítka **Spustit** a pak přejít na **triggery**. **Nepřetržitá integrace** zobrazuje název vašeho kanálu jako povolený. Pokud chcete zobrazit podrobnosti triggeru, zaškrtněte políčko **přepsat aktivační událost YAML Continuous Integration z tohoto místa** .

    ![Pokud chcete zkontrolovat nastavení triggeru vašeho kanálu, přečtěte si téma triggery v části Další akce.](./media/how-to-continuous-integration-continuous-deployment/check-trigger-settings.png)

Pokračujte k další části a sestavte tak kanál pro vydávání verzí.

[!INCLUDE [iot-edge-create-release-pipeline-for-continuous-deployment](../../includes/iot-edge-create-release-pipeline-for-continuous-deployment.md)]

[!INCLUDE [iot-edge-verify-iot-edge-continuous-integration-continuous-deployment](../../includes/iot-edge-verify-iot-edge-continuous-integration-continuous-deployment.md)]

## <a name="next-steps"></a>Další kroky

* Ukázka osvědčených postupů pro IoT Edge DevOps v [Azure DevOps Starter pro IoT Edge](how-to-devops-starter.md)
* Pochopení nasazení IoT Edge v části [Principy nasazení IoT Edge pro jednotlivá zařízení nebo ve velkém měřítku](module-deployment-monitoring.md)
* Projděte si postup vytvoření, aktualizace nebo odstranění nasazení v části [nasazení a monitorování IoT Edgech modulů ve velkém měřítku](how-to-deploy-at-scale.md).