---
title: Kurz aktualizace zařízení pro Azure IoT Hub pomocí Image Yocto malin 3 B + Referenční dokumentace k imagi | Microsoft Docs
description: Začínáme s aktualizací zařízení pro Azure IoT Hub pomocí referenční image Yocto Malina 3 B +.
author: valls
ms.author: valls
ms.date: 2/11/2021
ms.topic: tutorial
ms.service: iot-hub-device-update
ms.openlocfilehash: ca689df97e7268a5c0f7c0479e6514b98ffda9f2
ms.sourcegitcommit: 5bbc00673bd5b86b1ab2b7a31a4b4b066087e8ed
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/07/2021
ms.locfileid: "102443450"
---
# <a name="device-update-for-azure-iot-hub-tutorial-using-the-raspberry-pi-3-b-reference-image"></a>Kurz aktualizace zařízení pro Azure IoT Hub pomocí referenční image Malina 3 B +

Aktualizace zařízení pro IoT Hub podporuje dvě formy aktualizací – založené na bitových kopiích a na balíčcích.

Aktualizace obrázků poskytují v konečném stavu zařízení vyšší úroveň spolehlivosti. Obvykle je snazší replikovat výsledky aktualizace image mezi předprodukčním prostředím a produkčním prostředím, protože nepředstavuje stejné výzvy jako balíčky a jejich závislosti. Z důvodu atomické povahy může jeden model převzetí služeb při selhání A/B také snadno přijmout.

Tento kurz vás provede kroky k dokončení kompletní aktualizace na základě bitové kopie pomocí aktualizace zařízení IoT Hub. 

V tomto kurzu se naučíte, jak:
> [!div class="checklist"]
> * Stáhnout obrázek
> * Přidání značky do zařízení IoT
> * Importovat aktualizaci
> * Vytvoření skupiny zařízení
> * Nasazení aktualizace image
> * Monitorování nasazení aktualizace

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky
* Přístup k IoT Hub. Doporučuje se použít úroveň S1 (Standard) nebo vyšší.

## <a name="download-image"></a>Stáhnout obrázek

K dispozici jsou tři bitové kopie, které jsou součástí prostředků "assets" v dané [verzi GitHubu aktualizace zařízení](https://github.com/Azure/iot-hub-device-update/releases). K dispozici je základní Image (ADU-Base-image) a jeden aktualizační obrázek (ADU-Update-image), abyste mohli zkusit uvádění na jiné verze, aniž byste museli na zařízení flash kartu SD. Pokud to chcete udělat, budete muset nahrát image aktualizací do aktualizace zařízení služby IoT Hub jako součást importu.

## <a name="flash-sd-card-with-image"></a>Karta SD flash s obrázkem

Pomocí vašeho oblíbeného nástroje pro blikání OS nainstalujte na SD kartu základní Image (ADU-Base-image) zařízení, která se použije v zařízení malin 3 B + +.

### <a name="using-bmaptool-to-flash-sd-card"></a>Použití bmaptool na kartu SD flash

1. Pokud jste to ještě neučinili, nainstalujte `bmaptool` nástroj.

   ```shell
   sudo apt-get install bmap-tools
   ```

2. V části Najděte cestu pro SD kartu `/dev` . Cesta by měla vypadat nějak takto `/dev/sd*` `/dev/mmcblk*` : nebo. Pomocí tohoto nástroje můžete `dmesg` najít správnou cestu.

3. Před započetím blikání budete muset všechny připojené oddíly odpojit.

   ```shell
   sudo umount /dev/<device>
   ```

4. Ujistěte se, že máte oprávnění k zápisu do zařízení.

   ```shell
   sudo chmod a+rw /dev/<device>
   ```

5. Nepovinný parametr. Pro rychlejší blikání si stáhněte soubor bimap spolu se souborem obrázku a umístěte ho do stejného adresáře.

6. Zabliká kartu SD.

   ```shell
   sudo bmaptool copy <path to image> /dev/<device>
   ```
   
Aktualizace zařízení pro software Azure IoT Hub podléhá následujícím licenčním podmínkám:
   * [Aktualizace zařízení pro IoT Hub licenci](https://github.com/Azure/iot-hub-device-update/blob/main/LICENSE.md)
   * [Licence klienta Optimalizace doručení](https://github.com/microsoft/do-client/blob/main/LICENSE.md)
   
Přečtěte si licenčních podmínek před použitím agenta. Vaše instalace a použití představují Vaše přijetí těchto podmínek. Pokud s licenčními podmínkami nesouhlasíte, nepoužívejte aktualizaci zařízení pro agenta IoT Hub.

## <a name="create-device-in-iot-hub-and-get-connection-string"></a>Vytvoření zařízení v IoT Hub a získání připojovacího řetězce

Teď je potřeba přidat zařízení do Azure IoT Hub.  V rámci služby Azure IoT Hub se pro zařízení vygeneruje připojovací řetězec.

1. Z Azure Portal spusťte IoT Hub aktualizace zařízení.
2. Vytvoří nové zařízení.
3. Na levé straně stránky přejděte do ' Explorers ' > ' zařízení IoT ' > vyberte možnost "nové".
4. Zadejte název zařízení v části ID zařízení – Ujistěte se, že je zaškrtnuté políčko vygenerovat klíče.
5. Vyberte Save (Uložit).
6. Teď se vrátíte na stránku zařízení a zařízení, které jste vytvořili, by mělo být v seznamu. Vyberte toto zařízení.
7. V zobrazení zařízení vyberte ikonu Kopírovat vedle řetězce primární připojovací řetězec.
8. Zkopírované znaky vložte někam pro pozdější použití v následujících krocích.
   **Tento zkopírovaný řetězec je připojovací řetězec zařízení**.

## <a name="provision-connection-string-on-sd-card"></a>Zřídit připojovací řetězec na kartě SD

1. Ujistěte se, že je PI3 ke službě malin připojený k síti.
2. V PowerShellu použijte k zařízení SSH pomocí následujícího příkazu.
   ```markdown
   ssh raspberrypi3 -l root
      ```
4. Zadejte přihlašovací údaje jako "root" a heslo by mělo zůstat prázdné.
5. Po úspěšném SSH na zařízení spusťte níže uvedené příkazy.
 
Nahraďte `<device connection string>` připojovacím řetězcem.
 ```markdown
    echo "connection_string=<device connection string>" > adu-conf.txt  
    echo "aduc_manufacturer=ADUTeam" >> adu-conf.txt
    echo "aduc_model=RefDevice" >> adu-conf.txt
   ```

## <a name="connect-the-device-in-device-update-iot-hub"></a>Připojit zařízení v IoT Hub aktualizace zařízení

1. Na levé straně stránky vyberte v části průzkumníky možnost zařízení IoT.
2. Vyberte odkaz s názvem zařízení.
3. V horní části stránky vyberte možnost "zařízení je neaktivní".
4. V části hlášeno ve vlastnostech dodaného zařízení vyhledejte verzi jádra systému Linux.
Pro nové zařízení, které neobdrželo aktualizaci z aktualizace zařízení, hodnota [DeviceManagement: DeviceInformation: 1. swVersion](device-update-plug-and-play.md) bude představovat verzi firmwaru spuštěnou v zařízení.  Po použití aktualizace na zařízení bude aktualizace zařízení používat [AzureDeviceUpdateCore: ClientMetadata: 4. installedUpdateId](device-update-plug-and-play.md) hodnotu vlastnosti, která bude reprezentovat verzi firmwaru běžící na zařízení.
5. Základní a aktualizované soubory obrázků mají v názvu souboru číslo verze.

   ```markdown
   adu-<image type>-image-<machine>-<version number>.<extension>
   ```

V níže uvedeném kroku import aktualizace použijte číslo verze.

## <a name="add-a-tag-to-your-device"></a>Přidání značky do zařízení

1. Přihlaste se [Azure Portal](https://portal.azure.com) a přejděte do IoT Hub.

2. V levém navigačním podokně v části zařízení IoT nebo IoT Edge Najděte své zařízení IoT a přejděte k zařízení s dvojitou platností.

3. V případě, že je zařízení s dvojitou hodnotou, odstraňte všechny existující hodnoty značky aktualizace zařízení nastavením na hodnotu null.

4. Přidejte novou hodnotu značky aktualizace zařízení, jak je uvedeno níže.

```JSON
    "tags": {
            "ADUGroup": "<CustomTagValue>"
            }
```

## <a name="import-update"></a>Importovat aktualizaci

1. Podle těchto [pokynů](import-update.md)vytvořte manifest importu.
2. V levém navigačním panelu vyberte možnost aktualizace zařízení v části Automatická správa zařízení.
3. Vyberte kartu aktualizace.
4. Vyberte + Importovat novou aktualizaci.
5. V části vyberte soubor manifestu pro import vyberte ikonu složky nebo textové pole. Zobrazí se dialogové okno pro výběr souboru. Vyberte manifest importu, který jste vytvořili výše.  Potom v části vyberte jeden nebo více souborů aktualizace vyberte ikonu složky nebo textové pole. Zobrazí se dialogové okno pro výběr souboru. Vyberte soubor aktualizace, který chcete nasadit do zařízení IoT.
   
   :::image type="content" source="media/import-update/select-update-files.png" alt-text="Snímek obrazovky znázorňující výběr aktualizačního souboru" lightbox="media/import-update/select-update-files.png":::

5. V části vyberte kontejner úložiště vyberte ikonu složky nebo textové pole. Pak vyberte příslušný účet úložiště.

6. Pokud jste už kontejner vytvořili, můžete ho znovu použít. (Jinak vyberte + kontejner a vytvořte nový kontejner úložiště pro aktualizace.).  Vyberte kontejner, který chcete použít, a klikněte na vybrat.
  
  :::image type="content" source="media/import-update/container.png" alt-text="Snímek obrazovky znázorňující výběr kontejneru" lightbox="media/import-update/container.png":::

7. Kliknutím na Odeslat spusťte proces importu.

8. Spustí se proces importu a obrazovka se změní v části Historie importu. Pokud chcete zobrazit průběh až do dokončení procesu importu, vyberte aktualizovat. V závislosti na velikosti aktualizace to může trvat několik minut, ale může trvat delší dobu.
   
   :::image type="content" source="media/import-update/update-publishing-sequence-2.png" alt-text="Snímek obrazovky znázorňující sekvenci importu aktualizace" lightbox="media/import-update/update-publishing-sequence-2.png":::

9. Když sloupec status (stav) indikuje, že import proběhl úspěšně, vyberte hlavičku připraveno k nasazení. V seznamu by se teď měla zobrazit vaše importovaná aktualizace.

[Přečtěte si další informace](import-update.md) o importu aktualizací.

## <a name="create-update-group"></a>Vytvořit skupinu aktualizací

1. Přejít na IoT Hub, kterou jste dříve připojili k instanci aktualizace zařízení.

2. V levém navigačním panelu vyberte možnost aktualizace zařízení v části Automatická správa zařízení.

3. V horní části stránky vyberte kartu skupiny. 

4. Kliknutím na tlačítko Přidat vytvořte novou skupinu.

5. V seznamu vyberte značku IoT Hub, kterou jste vytvořili v předchozím kroku. Vyberte vytvořit skupinu aktualizací.

   :::image type="content" source="media/create-update-group/select-tag.PNG" alt-text="Snímek obrazovky znázorňující výběr značky" lightbox="media/create-update-group/select-tag.PNG":::

[Další informace](create-update-group.md) o přidávání značek a vytváření skupin aktualizací


## <a name="deploy-update"></a>Nasazení aktualizace

1. Po vytvoření skupiny byste měli vidět novou aktualizaci dostupnou pro skupinu zařízení s odkazem na aktualizaci v části čekají na aktualizace. Možná budete muset aktualizaci jednou aktualizovat. 

2. Klikněte na dostupnou aktualizaci.

3. Potvrďte, že je jako cílová skupina vybraná správná skupina. Naplánujte nasazení a pak vyberte nasadit aktualizaci.

   :::image type="content" source="media/deploy-update/select-update.png" alt-text="Vybrat aktualizaci" lightbox="media/deploy-update/select-update.png":::

4. Zobrazení grafu dodržování předpisů. Měla by se zobrazit, že aktualizace probíhá. 

   :::image type="content" source="media/deploy-update/update-in-progress.png" alt-text="Probíhá aktualizace." lightbox="media/deploy-update/update-in-progress.png":::

5. Po úspěšné aktualizaci zařízení by se měl zobrazit graf dodržování předpisů a aktualizace podrobností nasazení, aby odrážely stejnou hodnotu. 

   :::image type="content" source="media/deploy-update/update-succeeded.png" alt-text="Úspěšná aktualizace" lightbox="media/deploy-update/update-succeeded.png":::

## <a name="monitor-an-update-deployment"></a>Monitorování nasazení aktualizací

1. V horní části stránky vyberte kartu nasazení.

   :::image type="content" source="media/deploy-update/deployments-tab.png" alt-text="Karta nasazení" lightbox="media/deploy-update/deployments-tab.png":::

2. Vyberte nasazení, které jste vytvořili pro zobrazení podrobností o nasazení.

   :::image type="content" source="media/deploy-update/deployment-details.png" alt-text="Podrobnosti o nasazení" lightbox="media/deploy-update/deployment-details.png":::

3. Pokud chcete zobrazit nejnovější podrobnosti o stavu, vyberte aktualizovat. Pokračujte v tomto procesu, dokud se stav nezmění na úspěšné.

Nyní jste dokončili úspěšnou aktualizaci kompletních imagí na konci pomocí aktualizace zařízení pro IoT Hub na zařízení malin 3 B + +. 

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud už je nepotřebujete, vyčistěte účet aktualizace zařízení, instanci IoT Hub a zařízení IoT. 

## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Agent reference simulátoru](device-update-simulator.md)
