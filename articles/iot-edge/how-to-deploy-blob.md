---
title: Nasazení modulu Azure Blob Storage do zařízení – Azure IoT Edge | Microsoft Docs
description: Nasaďte modul Azure Blob Storage do zařízení IoT Edge, abyste mohli ukládat data na hraničních zařízeních.
author: arduppal
ms.author: arduppal
ms.date: 08/07/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: mchad
ms.openlocfilehash: f1c5bb6f8a4c7705940f8659575690939c3e5433
ms.sourcegitcommit: c4700ac4ddbb0ecc2f10a6119a4631b13c6f946a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/27/2019
ms.locfileid: "72964987"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Nasazení služby Azure Blob Storage v modulu IoT Edge do zařízení

Existuje několik způsobů, jak nasadit moduly do zařízení IoT Edge a všechny je fungují pro Azure Blob Storage v IoT Edgech modulech. Tyto dvě nejjednodušší metody slouží k použití šablon Azure Portal nebo Visual Studio Code.

## <a name="prerequisites"></a>Předpoklady

- [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.
- [IoT Edge zařízení](how-to-register-device.md) s nainstalovaným modulem runtime IoT Edge.
- [Visual Studio Code](https://code.visualstudio.com/) a [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , pokud nasazujete z Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Nasazení z Azure Portal

Azure Portal vás provede vytvořením manifestu nasazení a vložením nasazení do IoT Edgeho zařízení.

### <a name="select-your-device"></a>Vyberte své zařízení.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.
1. V nabídce vyberte **IoT Edge** .
1. V seznamu zařízení klikněte na ID cílového zařízení.
1. Vyberte **Nastavit moduly**.

### <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly se mají nasadit, způsob, jakým jsou toky dat mezi moduly a požadované vlastnosti v modulu vlákna. Azure Portal má průvodce, který vás provede vytvořením manifestu nasazení místo ručního vytváření dokumentu JSON. Má tři kroky: **přidat moduly**, **zadat trasy**a **zkontrolovat nasazení**.

#### <a name="add-modules"></a>Přidat moduly

1. V části **moduly nasazení** na stránce vyberte **Přidat**.

1. Z typů modulů v rozevíracím seznamu vyberte možnost **IoT Edge modul**.

1. Zadejte název modulu a pak zadejte image kontejneru:

   - **Název** – azureblobstorageoniotedge
   - **Identifikátor URI image** – MCR.Microsoft.com/Azure-Blob-Storage:Latest

   > [!IMPORTANT]
   > Azure IoT Edge rozlišuje velká a malá písmena, když provedete volání modulů a sada SDK úložiště má výchozí hodnotu malá písmena. I když je název modulu v [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) **AzureBlobStorageonIoTEdge**, změna názvu na malá písmena vám pomůže zajistit, že se připojení k Azure Blob Storage v modulu IoT Edge nepřerušila.

1. Výchozí nastavení **kontejnerů vytvořit** hodnoty definují vazby portů, které váš kontejner potřebuje, ale musíte také přidat informace o svém účtu úložiště a připojení k úložišti na svém zařízení. Výchozí JSON na portálu nahraďte následujícím kódem JSON:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage mount>"
       ],
       "PortBindings":{
         "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Aktualizujte kód JSON, který jste zkopírovali, pomocí následujících informací:

   - Nahraďte `<your storage account name>` názvem, který si můžete pamatovat. Názvy účtů by měly mít délku 3 až 24 znaků a malými písmeny a číslicemi. Žádné mezery.

   - Nahraďte `<your storage account key>` klíčem Base64 64-byte. Klíč můžete vygenerovat pomocí nástrojů, jako je [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Tyto přihlašovací údaje použijete pro přístup k úložišti objektů BLOB z jiných modulů.

   - V závislosti na operačním systému vašeho kontejneru nahraďte `<storage mount>`. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cestu k adresáři na zařízení IoT Edge, kde chcete, aby modul BLOB ukládal svá data. Připojení úložiště mapuje umístění v zařízení, které zadáte do umístění sady v modulu.

     - U kontejnerů Linux je formát *\<cesta úložiště nebo svazek >:/blobroot*. Například
         - použít [připojení svazku](https://docs.docker.com/storage/volumes/): **My-Volume:/blobroot** 
         - použijte [připojení BIND](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**. Nezapomeňte postupovat podle pokynů pro [udělení přístupu k adresáři uživateli kontejneru](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) .
     - V případě kontejnerů Windows je formát *\<cesta k úložišti nebo svazek >: C:/BlobRoot*. Například
         - použít [připojení svazku](https://docs.docker.com/storage/volumes/): **můj svazek: C:/blobroot**. 
         - použijte [připojení BIND](https://docs.docker.com/storage/bind-mounts/): **c:/ContainerData: c:/BlobRoot**.
         - Místo používání místní jednotky můžete namapovat umístění sítě protokolu SMB, kde najdete další informace v tématu [použití sdílené složky SMB jako místního úložiště](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) .

     > [!IMPORTANT]
     > Neměňte druhou polovinu hodnoty připojení úložiště, která odkazuje na konkrétní umístění v modulu. Připojení úložiště by mělo vždy končit **:/blobroot** for Linux Containers a **: C:/blobroot** for Windows Containers.

1. Nastavte [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) a vlastnosti [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) pro modul tak, že zkopírujete následující kód JSON a vložíte ho do pole **požadované vlastnosti vlákna v modulu set** . Nakonfigurujte každou vlastnost s odpovídající hodnotou, uložte ji a pokračujte v nasazení. Pokud používáte simulátor IoT Edge, nastavte hodnoty na související proměnné prostředí pro tyto vlastnosti, které najdete v části vysvětlení v tématu [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) a [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading":<true,false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload":<true,false>
       }
     }
   }

      ```

   ![Nastavení možností vytvoření kontejneru, vlastností deviceAutoDeleteProperties a deviceToCloudUploadProperties](./media/how-to-deploy-blob/iotedge-custom-module.png)

   Informace o konfiguraci deviceToCloudUploadProperties a deviceAutoDeleteProperties po nasazení modulu najdete v tématu [editace modulu s dvojitou](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)platností. Další informace o požadovaných vlastnostech najdete v tématu [definice nebo aktualizace požadovaných vlastností](module-composition.md#define-or-update-desired-properties).

1. Vyberte **Save** (Uložit).

1. Kliknutím na tlačítko **Další** pokračujte v části trasy.

#### <a name="specify-routes"></a>Zadat trasy

Ponechte výchozí trasy a vyberte **Další** a pokračujte do části Kontrola.

#### <a name="review-deployment"></a>Zkontrolovat nasazení

V části Kontrola se dozvíte, který manifest nasazení JSON byl vytvořen na základě vašich výběrů v předchozích dvou částech. K dispozici jsou také dva moduly, které jste nepřidali: **$edgeAgent** a **$edgeHub**. Tyto dva moduly tvoří [modul runtime IoT Edge](iot-edge-runtime.md) a jsou požadované výchozí hodnoty v každém nasazení.

Zkontrolujte informace o svém nasazení a pak vyberte **Odeslat**.

### <a name="verify-your-deployment"></a>Ověření nasazení

Po odeslání nasazení se vrátíte na stránku **IoT Edge** služby IoT Hub.

1. Vyberte zařízení IoT Edge, na které cílíte nasazení, a otevřete jeho podrobnosti.
1. V podrobnostech o zařízení ověřte, že je modul BLOB Storage uveden v části **nasazení** a **nahlášený zařízením**.

Může chvíli trvat, než se modul na zařízení spustí a pak se znovu oznámí IoT Hub. Aktualizujte stránku, aby se zobrazil aktualizovaný stav.

## <a name="deploy-from-visual-studio-code"></a>Nasazení z Visual Studio Code

Azure IoT Edge poskytuje šablony v Visual Studio Code, které vám pomůžou vyvíjet řešení Edge. Pomocí následujících kroků můžete vytvořit nové řešení IoT Edge s modulem BLOB Storage a nakonfigurovat manifest nasazení.

1. Vyberte **zobrazit** > **paleta příkazů**.

1. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge).

   ![Spustit nové řešení IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Pro vytvoření souborů řešení vyberte umístění na vývojovém počítači Visual Studio Code. |
   | Zadejte název řešení | Zadejte popisný název vašeho řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Vyberte **existující modul (zadejte úplnou adresu URL obrázku)** . |
   | Zadejte název modulu | Zadejte název pro váš modul, například **azureblobstorageoniotedge**.<br /><br />Je důležité používat pro Azure Blob Storage v modulu IoT Edge malý název. IoT Edge rozlišuje velká a malá písmena při odkazování na moduly a sada SDK pro úložiště je ve výchozím nastavení malá. |
   | Poskytněte image Docker pro modul. | Zadejte identifikátor URI Image: **MCR.Microsoft.com/Azure-Blob-Storage:Latest** |

   Visual Studio Code převezme poskytnuté informace, vytvoří řešení IoT Edge a pak ho načte do nového okna. Šablona řešení vytvoří šablonu manifestu nasazení, která zahrnuje image modulu BLOB Storage, ale je potřeba nakonfigurovat možnosti vytváření modulu.

1. Otevřete *Deployment. template. JSON* v novém pracovním prostoru řešení a vyhledejte část **moduly** . Proveďte následující změny konfigurace:

   1. Odstraňte modul **SimulatedTemperatureSensor** , protože není pro toto nasazení nutný.

   1. Zkopírujte následující kód a vložte ho do pole `createOptions`:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage mount>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![CreateOptions modulu Update-Visual Studio Code](./media/how-to-deploy-blob/create-options.png)

1. Nahraďte `<your storage account name>` názvem, který si můžete pamatovat. Názvy účtů by měly mít délku 3 až 24 znaků a malými písmeny a číslicemi. Žádné mezery.

1. Nahraďte `<your storage account key>` klíčem Base64 64-byte. Klíč můžete vygenerovat pomocí nástrojů, jako je [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Tyto přihlašovací údaje použijete pro přístup k úložišti objektů BLOB z jiných modulů.

1. V závislosti na operačním systému vašeho kontejneru nahraďte `<storage mount>`. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cestu k adresáři na zařízení IoT Edge, kde chcete, aby modul BLOB ukládal svá data. Připojení úložiště mapuje umístění v zařízení, které zadáte do umístění sady v modulu.  

      
     - U kontejnerů Linux je formát *\<cesta úložiště nebo svazek >:/blobroot*. Například
         - použít [připojení svazku](https://docs.docker.com/storage/volumes/): **My-Volume:/blobroot** 
         - použijte [připojení BIND](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**. Nezapomeňte postupovat podle pokynů pro [udělení přístupu k adresáři uživateli kontejneru](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) .
     - V případě kontejnerů Windows je formát *\<cesta k úložišti nebo svazek >: C:/BlobRoot*. Například
         - použít [připojení svazku](https://docs.docker.com/storage/volumes/): **můj svazek: C:/blobroot**. 
         - použijte [připojení BIND](https://docs.docker.com/storage/bind-mounts/): **c:/ContainerData: c:/BlobRoot**.
         - Místo používání místní jednotky můžete namapovat umístění sítě protokolu SMB, kde najdete další informace v tématu [použití sdílené složky SMB jako místního úložiště](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) .

     > [!IMPORTANT]
     > Neměňte druhou polovinu hodnoty připojení úložiště, která odkazuje na konkrétní umístění v modulu. Připojení úložiště by mělo vždy končit **:/blobroot** for Linux Containers a **: C:/blobroot** for Windows Containers.

1. Nakonfigurujte [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) a [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) pro svůj modul přidáním následujícího kódu JSON do souboru *Deployment. template. JSON* . Nakonfigurujte každou vlastnost s odpovídající hodnotou a uložte soubor. Pokud používáte simulátor IoT Edge, nastavte hodnoty na související proměnné prostředí pro tyto vlastnosti, které najdete v části vysvětlení v tématu [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) a [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) .

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "deviceAutoDeleteProperties": {
         "deleteOn": <true, false>,
         "deleteAfterMinutes": <timeToLiveInMinutes>,
         "retainWhileUploading": <true, false>
       },
       "deviceToCloudUploadProperties": {
         "uploadOn": <true, false>,
         "uploadOrder": "<NewestFirst, OldestFirst>",
         "cloudStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "storageContainersForUpload": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         },
         "deleteAfterUpload": <true, false>
       }
     }
   }
   ```

   ![nastavit požadované vlastnosti pro azureblobstorageoniotedge-Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Informace o konfiguraci deviceToCloudUploadProperties a deviceAutoDeleteProperties po nasazení modulu najdete v tématu [editace modulu s dvojitou](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)platností. Další informace o možnostech vytvoření kontejneru, zásadách restartování a požadovaném stavu najdete v tématu [EdgeAgent požadované vlastnosti](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Uložte soubor *deployment.template.json*.

1. Klikněte pravým tlačítkem na **Deployment. template. JSON** a vyberte **Generovat IoT Edge manifest nasazení**.

1. Visual Studio Code přebírá informace, které jste zadali v souboru *Deployment. template. JSON* a používá ho k vytvoření nového souboru manifestu nasazení. Manifest nasazení se vytvoří v nové **konfigurační** složce v pracovním prostoru řešení. Jakmile budete mít tento soubor, můžete postupovat podle kroků v části [nasazení Azure IoT Edge moduly z Visual Studio Code](how-to-deploy-modules-vscode.md) nebo [nasazení Azure IoT Edge modulů pomocí Azure CLI 2,0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Nasazení více instancí modulu

Pokud chcete nasadit více instancí služby Azure Blob Storage v modulu IoT Edge, je nutné zadat jinou cestu úložiště a změnit `HostPort` hodnotu, na kterou se modul váže. Moduly BLOB Storage vždy zveřejňují port 11002 v kontejneru, ale můžete deklarovat, na který port je vázán na hostiteli.

Upravit **možnosti vytvoření kontejneru** (v Azure Portal) nebo pole **createOptions** (v Visual Studio Code souboru *Deployment. template. JSON* ), chcete-li změnit hodnotu `HostPort`:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Když se připojíte k dalším modulům úložiště objektů blob, změňte koncový bod tak, aby odkazoval na aktualizovaný port hostitele.

## <a name="next-steps"></a>Další kroky
Další informace o [Azure Blob Storage v IoT Edge](how-to-store-data-blob.md)

Další informace o tom, jak manifesty nasazení fungují a jak je vytvořit, najdete v tématu [Vysvětlení způsobu použití, konfigurace a](module-composition.md)opětovného použití modulů IoT Edge.
