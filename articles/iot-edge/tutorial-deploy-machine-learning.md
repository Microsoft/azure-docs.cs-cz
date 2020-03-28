---
title: Kurz – nasazení Azure Machine Learning u zařízení pomocí Azure IoT Edge
description: V tomto kurzu vytvoříte model Azure Machine Learning a pak ho nasadíte jako modul do hraničního zařízení.
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 11/11/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc
ms.openlocfilehash: 5bfbf4a432f720b683ded4c85530135d86b24eba
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "76773007"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Kurz: Nasazení Azure Machine Learning jako modulu IoT Edge (Preview)

Pomocí poznámkových bloků Azure můžete vyvinout modul strojového učení a nasadit ho na linuxové zařízení se systémem Azure IoT Edge.
Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás provede nasazením modulu Azure Machine Learning, který předpovídá, kdy zařízení selže, na základě simulovaných dat teploty počítače. Další informace o Azure Machine Learning na IoT Edge najdete v [tématu dokumentace k Azure Machine Learning](../machine-learning/how-to-deploy-and-where.md).

>[!NOTE]
>Moduly Azure Machine Learning na Azure IoT Edge jsou ve verzi Public Preview.

Modul Azure Machine Learning, který vytvoříte v tomto kurzu, přečte data o prostředí vygenerovaná zařízením a označí zprávy jako standardní nebo neobvyklé.

V tomto kurzu se naučíte:

> [!div class="checklist"]
>
> * Vytvořit modul Azure Machine Learning
> * Doručit kontejner modulu do služby Azure Container Registry
> * Nasadit modul Azure Machine Learning na zařízení IoT Edge
> * Zobrazení vygenerovaných dat

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Virtuální počítač Azure můžete použít jako zařízení IoT Edge podle kroků v rychlém startu pro [Linux](quickstart-linux.md).
* Modul Azure Machine Learning nepodporuje kontejnery Windows.
* Modul Azure Machine Learning nepodporuje procesory ARM.

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* Pracovní prostor služby Azure Machine Learning. Postupujte podle pokynů v [části Azure Portal, abyste mohli začít s Azure Machine Learning,](../machine-learning/tutorial-1st-experiment-sdk-setup.md) abyste ho vytvořili a zjistili, jak ho používat.
  * Poznamenejte si název pracovního prostoru, skupinu prostředků a ID předplatného. Všechny tyto hodnoty jsou dostupné v přehledu pracovního prostoru na webu Azure Portal. Tyto hodnoty použijete později v kurzu k připojení poznámkového bloku Azure k prostředkům pracovního prostoru.

## <a name="create-and-deploy-azure-machine-learning-module"></a>Vytvoření a nasazení modulu Azure Machine Learning

V této části převedete soubory modelů trénovaného strojového učení do kontejneru Azure Machine Learning. Veškeré komponenty potřebné pro image Dockeru najdete v [úložišti Git AI Toolkit pro Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Podle těchto kroků nahrajte toto úložiště do poznámkových bloků Microsoft Azure a vytvořte kontejner a převezte ho do registru kontejnerů Azure.

1. Přejděte k projektům poznámkových bloků Azure. K dispozici můžete z pracovního prostoru Azure Machine Learning na [webu Azure Portal](https://portal.azure.com) nebo přihlášením k [poznámkovým blokům Microsoft Azure](https://notebooks.azure.com/home/projects) pomocí svého účtu Azure.

2. Vyberte **Nahrát úložiště GitHub**.

3. Zadejte následující název úložiště `Azure/ai-toolkit-iot-edge`GitHub: . Chcete-li zachovat soukromý projekt, zaškrtněte políčko **Veřejné.** Vyberte **Importovat**.

4. Po dokončení importu přejděte do nového projektu **ai-toolkit-iot-edge** a otevřete **složky výukového programu pro detekci anomálií IoT Edge.**

5. Ověřte, zda je projekt spuštěn. Pokud ne, vyberte **Spustit na volné výpočetní prostředky**.

   ![Spouštějte na bezplatných výpočetních výkonech](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Otevřete soubor **aml_config/config.json.**

7. Upravte konfigurační soubor tak, aby zahrnoval hodnoty pro Vaše ID předplatného Azure, skupinu prostředků ve vašem předplatném a název pracovního prostoru Azure Machine Learning. Všechny tyto hodnoty můžete získat z části **Přehled** vašeho pracovního prostoru v Azure.

8. Uložte konfigurační soubor.

9. Otevřete soubor **00-anomaly-detection-tutorial.ipynb.**

10. Po zobrazení výzvy vyberte jádro **Pythonu 3.6** a pak vyberte **Nastavit jádro**.

11. Upravte první buňku v poznámkovém bloku podle pokynů v komentářích. Použijte stejnou skupinu prostředků, ID předplatného a název pracovního prostoru, který jste přidali do konfiguračního souboru.

12. Spusťte buňky v poznámkovém bloku tak, že je vyberete a vyberete **Spustit** nebo stisknete `Shift + Enter`.

    >[!TIP]
    >Některé buňky v poznámkovém bloku kurz pro detekci anomálií jsou volitelné, protože vytvářejí prostředky, které někteří uživatelé mohou nebo ještě nemusí mít, jako je ioT hub. Pokud vložíte existující informace o prostředcích do první buňky, zobrazí se chyby, pokud spustíte buňky, které vytvářejí nové prostředky, protože Azure nevytvoří duplicitní prostředky. To je v pořádku a můžete ignorovat chyby nebo přeskočit tyto volitelné oddíly úplně.

Dokončením všech kroků v poznámkovém bloku jste vycvičili model detekce anomálií, vytvořili ho jako image kontejneru Dockeru a tuto bitovou kopii jste přeřadili do registru kontejnerů Azure. Potom jste model otestovali a nakonec ho nasadili do zařízení IoT Edge.

## <a name="view-container-repository"></a>Zobrazit úložiště kontejnerů

Zkontrolujte, zda byla image kontejneru úspěšně vytvořena a uložena v registru kontejnerů Azure přidruženém k vašemu prostředí strojového učení. Poznámkový blok, který jste použili v předchozí části, automaticky poskytl image kontejneru a přihlašovací údaje registru zařízení IoT Edge, ale měli byste vědět, kde jsou uloženy, abyste později mohli najít informace sami.

1. Na [webu Azure Portal](https://portal.azure.com)přejděte do pracovního prostoru služby Machine Learning.

2. V části **Přehled** jsou uvedeny podrobnosti pracovního prostoru a také jeho přidružené zdroje. Vyberte hodnotu **registru,** která by měla být názvem pracovního prostoru následovanou náhodnými čísly.

3. V registru kontejnerů vyberte **Úložiště**. Měli byste vidět úložiště s názvem **tempanomalydetection,** který byl vytvořen poznámkový blok, který jste spustili v předchozí části.

4. Vyberte **detekci tempanomaly**. Měli byste vidět, že úložiště má jednu značku: **1**.

   Nyní, když znáte název registru, název úložiště a značku, znáte úplnou cestu k bitové kopii kontejneru. Cesty k obrázkům vypadají jako ** \<\>registry_name .azurecr.io/tempanomalydetection:1**. Cestu k imagi můžete použít k nasazení tohoto kontejneru na zařízení IoT Edge.

5. V registru kontejnerů vyberte **přístupové klíče**. Měli byste vidět řadu přístupových pověření, včetně **přihlašovacího serveru** a **uživatelského jména**a **hesla** pro uživatele správce.

   Tyto přihlašovací údaje mohou být součástí manifestu nasazení a vaše zařízení IoT Edge by tak mohlo mít přístup k vyžádání imagí kontejneru z registru.

Nyní víte, kde je uložena image kontejneru Machine Learning. V další části se provede kroky k zobrazení kontejneru běží jako modul na zařízení IoT Edge.

## <a name="view-the-generated-data"></a>Zobrazení vygenerovaných dat

Můžete zobrazit zprávy generované každým modulem IoT Edge a také zprávy, které se doručují do vašeho IoT Hubu.

### <a name="view-data-on-your-iot-edge-device"></a>Zobrazení dat na zařízení IoT Edge

Na zařízení IoT Edge můžete zobrazit zprávy odesílané z každého jednotlivého modulu.

Ke spouštění `sudo` `iotedge` příkazů může být nutné použít oprávnění se zvýšenými oprávněními. Odhlášení a návrat k zařízení automaticky aktualizuje vaše oprávnění.

1. Takto zobrazíte všechny moduly na zařízení IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Takto zobrazíte zprávy odesílané z konkrétního zařízení. Použijte název modulu z výstupu předchozího příkazu.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Zobrazení dat odesílaných do IoT Hubu

Můžete zobrazit zprávy zařízení cloud, které vaše služba IoT hub přijímá pomocí [rozšíření Azure IoT Hub pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit).

Následující kroky ukazují, jak nastavit Visual Studio Code k monitorování zpráv zařízení-cloud, které přicházejí do vašeho IoT Hubu.

1. V editoru Visual Studio Code vyberte **IoT Hub Devices** (zařízení IoT Hubu).

2. Vyberte **...** a potom v nabídce vyberte **Set IoT Hub Connection String** (Nastavení připojovacího řetězce IoT Hubu).

   ![Nastavení připojovacího řetězce centra IoT](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Do textového pole, které se otevře nahoře na stránce, zadejte připojovací řetězec iothubowner svého IoT Hubu. Vaše zařízení IoT Edge by se mělo zobrazit v seznamu zařízení IoT Hubu.

4. Vyberte **...** znovu a pak vyberte **Spustit sledování vestavěného koncového bodu událostí**.

5. Sledujte zprávy, které přicházejí každých pět sekund ze senzoru tempSenzor. Text zprávy obsahuje vlastnost s názvem **anomálie**, které machinelearningmodul poskytuje true nebo false hodnotu. Pokud bylo spuštění modelu úspěšné, obsahuje vlastnost **AzureMLResponse** hodnotu „OK“.

   ![Odpověď Azure Machine Learning v textu zprávy](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste nasadili modul IoT Edge, který používá technologii Azure Machine Learning. Pokračujte některým z dalších kurzů, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoci přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Klasifikace obrázků s využitím služby Custom Vision](tutorial-deploy-custom-vision.md)
