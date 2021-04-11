---
title: Kurz – nasazení Azure Machine Learning do zařízení pomocí Azure IoT Edge
description: V tomto kurzu vytvoříte model Azure Machine Learning a pak ho nasadíte jako modul do hraničního zařízení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 07/29/2020
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: b3192c3ee8458623f3724da5f875cc09032a42bf
ms.sourcegitcommit: 3f684a803cd0ccd6f0fb1b87744644a45ace750d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2021
ms.locfileid: "106219412"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Kurz: Nasazení Azure Machine Learning jako modulu IoT Edge (Preview)

[!INCLUDE [iot-edge-version-all-supported](../../includes/iot-edge-version-all-supported.md)]

Pomocí Azure Notebooks můžete vyvinout modul Machine Learning a nasadit ho do zařízení s Azure IoT Edge s kontejnery Linux.
Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás provede nasazením modulu Azure Machine Learning, který předpovídá, kdy zařízení selže, na základě simulovaných dat teploty počítače. Další informace o Azure Machine Learning v IoT Edge najdete v [dokumentaci Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

>[!NOTE]
>Moduly Azure Machine Learning na Azure IoT Edge jsou ve verzi Public Preview.

Modul Azure Machine Learning, který vytvoříte v tomto kurzu, přečte data o prostředí vygenerovaná zařízením a označí zprávy jako standardní nebo neobvyklé.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvořte modul Azure Machine Learning.
> * Nahrajte kontejner modulu do služby Azure Container Registry.
> * Nasaďte modul Azure Machine Learning do IoT Edge zařízení.
> * Zobrazit vygenerovaná data.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Pomocí rychlých startů můžete nastavit zařízení se systémem [Linux](quickstart-linux.md) nebo [zařízení s Windows](quickstart.md).
* Modul Azure Machine Learning nepodporuje kontejnery Windows.
* Modul Azure Machine Learning nepodporuje procesory ARM.

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* Pracovní prostor služby Azure Machine Learning. Postupujte podle pokynů v [části použití Azure Portal k tomu, abyste mohli začít pracovat s Azure Machine Learning](../machine-learning/tutorial-1st-experiment-sdk-setup.md) a Naučte se, jak ho používat.
  * Poznamenejte si název pracovního prostoru, skupinu prostředků a ID předplatného. Tyto hodnoty jsou všechny dostupné v přehledu pracovního prostoru v Azure Portal. Tyto hodnoty použijete později v tomto kurzu, abyste připojili soubor Azure Notebooks k prostředkům pracovního prostoru.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Vytvořit a nasadit modul Azure Machine Learning

V této části převedete školicí soubory modelu Machine Learning a do kontejneru Azure Machine Learning. Veškeré komponenty potřebné pro image Dockeru najdete v [úložišti Git AI Toolkit pro Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Pomocí těchto kroků nahrajte toto úložiště do Microsoft Azure Notebooks a vytvořte kontejner a vložte ho do Azure Container Registry.

1. Přejděte do projektu Azure Notebooks. Z pracovního prostoru Azure Machine Learning můžete získat přístup v [Azure Portal](https://portal.azure.com) nebo přihlášením k [Microsoft Azure Notebooks](https://notebooks.azure.com/home/projects) s účtem Azure.

2. Vyberte **nahrát úložiště GitHub**.

3. Zadejte následující název úložiště GitHub: `Azure/ai-toolkit-iot-edge` . Zrušte políčko **veřejné** pole, pokud chcete zachovat projekt soukromý. Vyberte **Importovat**.

4. Po dokončení importu přejděte do nového projektu **AI-Toolkit-IoT-Edge** a otevřete složku **kurz detekce anomálií IoT Edge** .

5. Ověřte, že je váš projekt spuštěný. Pokud ne, vyberte **Spustit při bezplatném COMPUTE**.

   ![Spustit na bezplatné výpočetní prostředky](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Otevřete **aml_config/config.jsv** souboru.

7. Upravte konfigurační soubor tak, aby zahrnoval hodnoty pro ID předplatného Azure, skupinu prostředků ve vašem předplatném a název pracovního prostoru Azure Machine Learning. Všechny tyto hodnoty můžete získat z části **Přehled** pracovního prostoru v Azure.

8. Uložte konfigurační soubor.

9. Otevřete soubor. **ipynb 00-anomálie-Detection-tutorial** .

10. Po zobrazení výzvy vyberte jádro **Python 3,6** a potom vyberte **nastavit jádro**.

11. Upravte první buňku v poznámkovém bloku podle pokynů v komentářích. Použijte stejnou skupinu prostředků, ID předplatného a název pracovního prostoru, který jste přidali do konfiguračního souboru.

12. Buňky v poznámkovém bloku spustíte tak, že je vyberete a vyberete **Spustit** nebo stisknout `Shift + Enter` .

    >[!TIP]
    >Některé buňky v poznámkovém bloku s kurzem detekce anomálií jsou volitelné, protože vytvářejí prostředky, které někteří uživatelé mohou nebo nemusí zatím mít, například IoT Hub. Pokud do první buňky vložíte své informace o zdroji, zobrazí se chyby, pokud spustíte buňky, které vytvoří nové prostředky, protože Azure nevytváří duplicitní prostředky. To je přesné a můžete ignorovat chyby nebo tyto volitelné oddíly přeskočit zcela.

Po dokončení všech kroků v poznámkovém bloku jste si prosadili model detekce anomálií, vytvořili ho jako image kontejneru Docker a tuto image jste Azure Container Registry. Potom model otestujete a nakonec ho nasadíte do zařízení IoT Edge.

## <a name="view-container-repository"></a>Zobrazit úložiště kontejneru

Ověřte, že se image kontejneru úspěšně vytvořila a uložila do služby Azure Container Registry přidružené k vašemu prostředí Machine Learning. Poznámkový blok, který jste použili v předchozí části, automaticky poskytl image kontejneru a přihlašovací údaje registru k vašemu IoT Edge zařízení, ale měli byste znát, kde jsou uložené, abyste je mohli později najít.

1. V [Azure Portal](https://portal.azure.com)přejděte do pracovního prostoru služby Machine Learning.

2. V části **Přehled** jsou uvedeny podrobnosti o pracovním prostoru spolu s přidruženými prostředky. Vyberte hodnotu **registru** , kterou by měl být název vašeho pracovního prostoru následovaný náhodnými čísly.

3. V registru kontejnerů v části **služby** vyberte **úložiště**. Mělo by se zobrazit úložiště s názvem **tempanomalydetection** , které bylo vytvořeno pomocí poznámkového bloku, který jste spustili v předchozí části.

4. Vyberte **tempanomalydetection**. Měli byste vidět, že úložiště má jednu značku: **1**.

   Teď, když znáte název registru, název úložiště a značku, znáte úplnou cestu k bitové kopii kontejneru. Cesty k obrázkům vypadají jako **\<registry_name\> . azurecr.IO/tempanomalydetection:1**. Cestu k imagi můžete použít k nasazení tohoto kontejneru na zařízení IoT Edge.

5. V registru kontejnerů v části **Nastavení** vyberte **přístupové klíče**. Měl by se zobrazit počet přihlašovacích údajů, včetně **přihlašovacího serveru** a **uživatelského jména** a **hesla** pro uživatele s oprávněním správce.

   Tyto přihlašovací údaje mohou být součástí manifestu nasazení a vaše zařízení IoT Edge by tak mohlo mít přístup k vyžádání imagí kontejneru z registru.

Nyní víte, kde je uložená bitová kopie kontejneru Machine Learning. V další části se provedou kroky pro zobrazení kontejneru, který je spuštěný jako modul na zařízení IoT Edge.

## <a name="view-the-generated-data"></a>Zobrazení vygenerovaných dat

Můžete zobrazit zprávy generované každým modulem IoT Edge a také zprávy, které se doručují do vašeho IoT Hubu.

### <a name="view-data-on-your-iot-edge-device"></a>Zobrazení dat na zařízení IoT Edge

Na zařízení IoT Edge můžete zobrazit zprávy odesílané z každého jednotlivého modulu.

Možná budete muset použít `sudo` pro zvýšené oprávnění ke spouštění `iotedge` příkazů. Odhlášení a opětovné přihlášení do zařízení automaticky aktualizuje vaše oprávnění.

1. Takto zobrazíte všechny moduly na zařízení IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Takto zobrazíte zprávy odesílané z konkrétního zařízení. Použijte název modulu z výstupu předchozího příkazu.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Zobrazení dat odesílaných do IoT Hubu

Můžete zobrazit zprávy ze zařízení do cloudu, které služba IoT Hub přijme, pomocí [rozšíření Azure IoT Hub pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Následující kroky ukazují, jak nastavit Visual Studio Code k monitorování zpráv zařízení-cloud, které přicházejí do vašeho IoT Hubu.

1. V Průzkumníkovi Visual Studio Code v části **Azure IoT Hub** rozbalte **zařízení** , abyste viděli seznam zařízení IoT.

2. Klikněte pravým tlačítkem myši na název zařízení IoT Edge a vyberte možnost **Spustit sledování integrovaného koncového bodu události**.

3. Sledujte zprávy, které přicházejí každých pět sekund ze senzoru tempSensor. Text zprávy obsahuje vlastnost **anomaly**, která v modulu machinelearningmodule nabývá hodnoty true nebo false. Pokud bylo spuštění modelu úspěšné, obsahuje vlastnost **AzureMLResponse** hodnotu „OK“.

   ![Odpověď Azure Machine Learning v těle zprávy](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili modul IoT Edge, který používá technologii Azure Machine Learning. Pokračujte některým z dalších kurzů, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoci přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Klasifikace obrázků s využitím služby Custom Vision](tutorial-deploy-custom-vision.md)
