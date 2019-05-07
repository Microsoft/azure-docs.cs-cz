---
title: Azure Machine Learning nasadit do zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: V tomto kurzu vytvoříte model ve službě Azure Machine Learning a pak ho nasadíme jako modulu do hraničního zařízení
author: kgremban
manager: philmea
ms.author: kgremban
ms.date: 03/07/2019
ms.topic: tutorial
ms.service: iot-edge
services: iot-edge
ms.custom: mvc, seodec18
ms.openlocfilehash: 6f85b0088fac97f4b9f2dd2835e3052cb598a987
ms.sourcegitcommit: f6ba5c5a4b1ec4e35c41a4e799fb669ad5099522
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/06/2019
ms.locfileid: "65142761"
---
# <a name="tutorial-deploy-azure-machine-learning-as-an-iot-edge-module-preview"></a>Kurz: Nasazení služby Azure Machine Learning jako modulu IoT Edge (preview)

Použití poznámkových bloků Azure k vývoji modul machine learning a nasazování k Linuxovému zařízení s Azure IoT Edge. 

Moduly IoT Edge můžete použít k nasazení kódu, který implementuje obchodní logiku přímo do zařízení IoT Edge. Tento kurz vás provede nasazením modulu Azure Machine Learning, který předpovídá, kdy zařízení selže, na základě simulovaných dat teploty počítače. Další informace o službě Azure Machine Learning na hraničních zařízeních IoT najdete v tématu [dokumentace ke službě Azure Machine Learning](../machine-learning/service/how-to-deploy-to-iot.md).

Modul Azure Machine Learning, který vytvoříte v tomto kurzu, přečte data o prostředí vygenerovaná zařízením a označí zprávy jako standardní nebo neobvyklé.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit modul Azure Machine Learning
> * Doručit kontejner modulu do služby Azure Container Registry
> * Nasadit modul Azure Machine Learning na zařízení IoT Edge
> * Zobrazení vygenerovaných dat

>[!NOTE]
>Moduly Azure Machine Learning na Azure IoT Edge jsou ve verzi Public Preview.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]


## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Virtuální počítač Azure můžete použít jako zařízení IoT Edge podle pokynů v tomto rychlém startu pro [Linux](quickstart-linux.md).
* Modul Azure Machine Learning nepodporuje kontejnery Windows.
* Modul Azure Machine Learning nepodporuje procesory ARM.

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Free nebo Standard v Azure.
* Pracovní prostor služby Azure Machine Learning. Postupujte podle pokynů v [Začínáme s Azure Machine Learning pomocí webu Azure portal](../machine-learning/service/quickstart-get-started.md) vytvořte si ho a zjistěte, jak ho použít.
   * Poznamenejte si název pracovního prostoru, skupinu prostředků a ID předplatného. Tyto hodnoty jsou všechny dostupné na pracovní prostor Přehled na webu Azure Portal. Tyto hodnoty použijete v pozdější části kurzu pro připojení k vašim prostředkům pracovního prostoru Azure poznámkového bloku. 


## <a name="create-and-deploy-azure-machine-learning-module"></a>Vytvoření a nasazení modulu Azure Machine Learning

V této části převeďte trénovaného strojového učení soubory modelu a do Azure Machine Learning služby kontejneru. Veškeré komponenty potřebné pro image Dockeru najdete v [úložišti Git AI Toolkit pro Azure IoT Edge](https://github.com/Azure/ai-toolkit-iot-edge/tree/master/IoT%20Edge%20anomaly%20detection%20tutorial). Následujícím postupem nahrání daného úložiště do Microsoft poznámkových bloků Azure k vytvoření kontejneru a nasdílejte ji do Azure Container Registry.


1. Přejděte do vašich projektů poznámkových bloků Azure. Můžete se tam dostat z pracovního prostoru služby Azure Machine Learning v [webu Azure portal](https://portal.azure.com) nebo přihlášením k [poznámkových bloků Azure Microsoft](https://notebooks.azure.com/home/projects) pomocí svého účtu Azure.

2. Vyberte **nahrát úložiště GitHub se vzorovými**.

3. Zadejte následující název úložiště GitHub: `Azure/ai-toolkit-iot-edge`. Zrušte zaškrtnutí políčka **veřejné** pole, pokud chcete zachovat privátní váš projekt. Vyberte **Import**. 

4. Po dokončení importu, přejděte do nové **ai toolkit iot edge** projektu a otevřete **kurzu detekce anomálií IoT Edge** složky. 

5. Ověřte, zda je spuštěna váš projekt. Pokud ne, vyberte **spustili bezplatnou Compute**.

   ![Spustit na bezplatnými výpočetními funkcemi](./media/tutorial-deploy-machine-learning/run-on-free-compute.png)

6. Otevřít **aml_config/config.json** souboru.

7. Upravte konfigurační soubor, který zahrnuty hodnoty pro ID vašeho předplatného Azure, skupinu prostředků vašeho předplatného a název pracovního prostoru služby Azure Machine Learning. Získáte všechny tyto hodnoty z **přehled** části pracovního prostoru v Azure. 

8. Uložte konfigurační soubor.

9. Otevřít **00 anomálií detekce tutorial.ipynb** souboru.

10. Po zobrazení výzvy vyberte **Python 3.6** jádra vyberte **nastavit jádra**.

11. Upravte první buňky v poznámkovém bloku podle pokynů v komentářích. Použijte stejnou skupinu prostředků, ID předplatného a název pracovního prostoru, který jste přidali do konfiguračního souboru.

12. Spustit všechny buňky v poznámkovém bloku tak, že je vyberete a vyberete **spustit** nebo stiskněte `Shift + Enter`.

    >[!TIP]
    >Některé z buněk, kurz notebook detekce anomálií jsou volitelné, protože vytvářejí prostředky, které někteří uživatelé mohou nebo nemusí mít ještě, jako jsou služby IoT Hub. Když vložíte stávající informace o prostředku do první buňky, obdržíte chyb při spuštění buňky, které vytvářet nové prostředky, protože Azure nebude vytvářet duplicitní prostředky. To je v pořádku, a můžete ignorovat chyby nebo zcela Přeskočit tyto volitelné části. 

Po dokončení všech kroků v poznámkovém bloku, natrénovali model detekce anomálií, který je sestaven jako image kontejneru Dockeru a jste image Nasdíleli do služby Azure Container Registry. Potom Testovat model a nakonec ji nasadit do zařízení IoT Edge. 

## <a name="view-container-repository"></a>Zobrazit kontejner úložiště

Zkontrolujte, že svou image kontejneru byla úspěšně vytvořeny a uloženy v registru kontejnerů Azure spojené s vaším prostředím machine learning. Poznámkový blok, který jste použili v předchozí části automaticky k dispozici image kontejneru a přihlašovacích údajů registru do zařízení IoT Edge, ale měli byste vědět, kde jsou uloženy, abyste našli informace sami později. 

1. V [webu Azure portal](https://portal.azure.com), přejděte do pracovního prostoru služby Machine Learning. 

2. **Přehled** části jsou uvedené podrobnosti o pracovním prostoru jako a její přidružené prostředky. Vyberte **registru** hodnotu, která by měl být název vašeho pracovního prostoru a náhodných čísel. 

3. V registru kontejneru, vyberte **úložišť**. Byste měli vidět úložiště volána **tempanomalydetection** , který vytvořil Poznámkový blok spustíte v předchozí části. 

4. Vyberte **tempanomalydetection**. Měli byste vidět, že úložiště obsahuje jednu značku: **1**. 

   Teď, když znáte název registru, úložiště název a značku, můžete znát cestu úplnou bitovou kopii kontejneru. Cesty obrázků vypadat  **\<registry_name\>.azurecr.io/tempanomalydetection:1**. Cesta k obrázku můžete použít k nasazení do zařízení IoT Edge tohoto kontejneru. 

5. V registru kontejneru, vyberte **přístupové klíče**. Měli byste vidět počet přihlašovacích údajů pro přístup, včetně **přihlašovací server** a **uživatelské jméno**, a **heslo** pro správce.

   Tyto přihlašovací údaje mohou být součástí manifestu nasazení umožňují IoT Edge zařízení přístup k přetáhnout Image kontejneru z registru. 

Teď víte, kde je uložen image kontejneru Machine Learning. Následující části vás provede kroky k zobrazení jako na modul kontejneru na vašem zařízení IoT Edge. 

## <a name="view-generated-data"></a>Zobrazení vygenerovaných dat

Můžete zobrazit zprávy generované každým modulem IoT Edge a také zprávy, které se doručují do vašeho IoT Hubu.

### <a name="view-data-on-your-iot-edge-device"></a>Zobrazení dat na zařízení IoT Edge

Na zařízení IoT Edge můžete zobrazit zprávy odesílané z každého jednotlivého modulu.

Budete možná muset použít `sudo` pro zvýšenou úroveň oprávnění ke spuštění `iotedge` příkazy. Odhlaste se a přihlásit zpátky do vašeho zařízení automaticky aktualizuje příslušná oprávnění.

1. Takto zobrazíte všechny moduly na zařízení IoT Edge.

   ```cmd/sh
   iotedge list
   ```

2. Takto zobrazíte zprávy odesílané z konkrétního zařízení. Použijte název modulu z výstupu předchozího příkazu.

   ```cmd/sh
   iotedge logs <module_name> -f
   ```

### <a name="view-data-arriving-at-your-iot-hub"></a>Zobrazení dat odesílaných do IoT Hubu

Můžete zobrazit zprávy typu zařízení cloud, které služba IoT hub přijímá pomocí [rozšíření Azure IoT Hub Toolkit pro Visual Studio Code](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-toolkit) (dříve rozšíření Azure IoT Toolkit).

Následující kroky ukazují, jak nastavit Visual Studio Code k monitorování zpráv zařízení-cloud, které přicházejí do vašeho IoT Hubu.

1. V editoru Visual Studio Code vyberte **IoT Hub Devices** (zařízení IoT Hubu).

2. Vyberte **...** a potom v nabídce vyberte **Set IoT Hub Connection String** (Nastavení připojovacího řetězce IoT Hubu).

   ![Nastavit připojovací řetězec služby IoT Hub](./media/tutorial-deploy-machine-learning/set-connection.png)

3. Do textového pole, které se otevře nahoře na stránce, zadejte připojovací řetězec iothubowner svého IoT Hubu. Vaše zařízení IoT Edge by se mělo zobrazit v seznamu zařízení IoT Hubu.

4. Znovu vyberte **...** a pak vyberte **Start monitoring D2C message** (Začít monitorovat zprávy D2C).

5. Sledujte zprávy, které přicházejí každých pět sekund ze senzoru tempSenzor. Tělo zprávy obsahuje vlastnost s názvem **anomálií**, který machinelearningmodule poskytuje hodnotu true nebo false. Pokud bylo spuštění modelu úspěšné, obsahuje vlastnost **AzureMLResponse** hodnotu „OK“.

   ![Odpověď služby Azure Machine Learning v textu zprávy](./media/tutorial-deploy-machine-learning/ml-output.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud máte v plánu pokračovat k dalšímu doporučenému článku, můžete si vytvořené prostředky a konfigurace uschovat a znovu je použít. Také můžete dál používat stejné zařízení IoT Edge jako testovací zařízení.

Jinak můžete místní konfigurace a prostředky Azure vytvořené v tomto článku odstranit, abyste se vyhnuli poplatkům.

[!INCLUDE [iot-edge-clean-up-cloud-resources](../../includes/iot-edge-clean-up-cloud-resources.md)]

## <a name="next-steps"></a>Další postup

V tomto kurzu jste nasadili modul IoT Edge, který používá technologii Azure Machine Learning. Pokračujte některým z dalších kurzů, ve kterých poznáte další způsoby, jak vám může Azure IoT Edge pomoci přeměnit data na obchodní informace o hraničním zařízení.

> [!div class="nextstepaction"]
> [Klasifikace obrázků s využitím služby Custom Vision](tutorial-deploy-custom-vision.md)

