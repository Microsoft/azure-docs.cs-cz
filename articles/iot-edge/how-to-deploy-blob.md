---
title: Nasaďte v modulu úložiště objektů blob do zařízení – Azure IoT Edge
description: Modul služby Azure Blob Storage nasadíte do zařízení IoT Edge k ukládání dat na hraničních zařízeních.
author: kgremban
ms.author: kgremban
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: fe09fb47a75ff9d412ffab2daafaf241a43443b4
ms.sourcegitcommit: c32050b936e0ac9db136b05d4d696e92fefdf068
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2020
ms.locfileid: "75729603"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Nasazení služby Azure Blob Storage v modulu IoT Edge do zařízení

Existuje několik způsobů, jak nasadit moduly do zařízení IoT Edge a všechny je fungují pro Azure Blob Storage v IoT Edgech modulech. Tyto dvě metody nejjednodušší jsou pomocí webu Azure portal nebo šablony Visual Studio Code.

## <a name="prerequisites"></a>Požadavky

- [Služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.
- [Zařízení IoT Edge](how-to-register-device.md) nainstalován modul runtime IoT Edge.
- [Visual Studio Code](https://code.visualstudio.com/) a [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , pokud nasazujete z Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Nasazení z Azure Portal

Azure Portal vás provede vytvořením manifestu nasazení a vložením nasazení do IoT Edgeho zařízení.

### <a name="select-your-device"></a>Vyberte zařízení

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT hub.
1. Vyberte **IoT Edge** z nabídky.
1. Kliknutím na ID cílové zařízení ze seznamu zařízení.
1. Vyberte **Nastavit moduly**.

### <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly chcete nasadit, tok dat mezi moduly a požadované vlastnosti dvojčat modulů. Azure Portal má průvodce, který vás provede vytvořením manifestu nasazení místo ručního vytváření dokumentu JSON. Obsahuje tři kroky uspořádané do karet: **moduly**, **trasy**a **Revize + vytvořit**.

#### <a name="add-modules"></a>Přidat moduly

1. V části **IoT Edge moduly** na stránce klikněte na rozevírací seznam **přidat** a vyberte **IoT Edge modul** . zobrazí se stránka **Přidat IoT Edge modul** .

2. Na kartě **nastavení modulu** zadejte název modulu a pak zadejte identifikátor URI image kontejneru:

   Příklady:
  
   - **Název modulu IoT Edge**: `azureblobstorageoniotedge`
   - **Identifikátor URI image**: `mcr.microsoft.com/azure-blob-storage:latest`

   ![Nastavení vlákna v modulu](./media/how-to-deploy-blob/addmodule-tab1.png)

   Nevybírejte možnost **Přidat** , dokud neurčíte hodnoty v **nastavení modulu**, **možnosti vytvoření kontejneru**a **Dvojitá nastavení modulu** , jak je popsáno v tomto postupu.

   > [!IMPORTANT]
   > Azure IoT Edge rozlišuje velká a malá písmena, když provedete volání modulů a sada SDK úložiště má výchozí hodnotu malá písmena. I když je název modulu v [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) **AzureBlobStorageonIoTEdge**, změna názvu na malá písmena vám pomůže zajistit, že se připojení k Azure Blob Storage v modulu IoT Edge nepřerušila.

3. Na kartě **možnosti vytvoření kontejneru** vám poskytnete kód JSON, který poskytne informace o účtu úložiště a připojení k úložišti na vašem zařízení.

   ![Nastavení vlákna v modulu](./media/how-to-deploy-blob/addmodule-tab3.png)

   Zkopírujte následující kód JSON a vložte ho do pole s odkazem na popisy zástupných symbolů v dalším kroku.
  
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

4. Aktualizujte kód JSON, který jste zkopírovali z části **vytvoření kontejneru** , s následujícími informacemi:

   - Nahraďte `<your storage account name>` názvem, který si můžete pamatovat. Názvy účtů by měly mít délku 3 až 24 znaků a malými písmeny a číslicemi. Žádné mezery.

   - Nahraďte `<your storage account key>` klíčem Base64 64-byte. Vygenerujete nějaký klíč pomocí nástrojů jako [GeneratePlus](https://generate.plus/en/base64). Použijete tyto přihlašovací údaje pro přístup k úložišti objektů blob z jiných modulů.

   - V závislosti na operačním systému vašeho kontejneru nahraďte `<storage mount>`. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cesta do adresáře na zařízení IoT Edge místo modulu objektů blob pro uložení data. Připojení úložiště mapuje umístění v zařízení, které zadáte do umístění sady v modulu.

     - U kontejnerů Linux je formát *\<cesta úložiště nebo svazek >:/blobroot*. Například
         - použít [připojení svazku](https://docs.docker.com/storage/volumes/): **My-Volume:/blobroot**
         - použijte [připojení BIND](https://docs.docker.com/storage/bind-mounts/): **/SRV/containerdata:/blobroot**. Nezapomeňte postupovat podle pokynů pro [udělení přístupu k adresáři uživateli kontejneru](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) .
     - V případě kontejnerů Windows je formát *\<cesta k úložišti nebo svazek >: C:/BlobRoot*. Například
         - použít [připojení svazku](https://docs.docker.com/storage/volumes/): **můj svazek: C:/blobroot**.
         - použijte [připojení BIND](https://docs.docker.com/storage/bind-mounts/): **c:/ContainerData: c:/BlobRoot**.
         - Místo používání místní jednotky můžete namapovat umístění sítě protokolu SMB, kde najdete další informace v tématu [použití sdílené složky SMB jako místního úložiště](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) .

     > [!IMPORTANT]
     > Neměňte druhou polovinu hodnoty připojení úložiště, která odkazuje na konkrétní umístění v modulu. Připojení úložiště by mělo vždy končit **:/blobroot** for Linux Containers a **: C:/blobroot** for Windows Containers.

5. Na kartě **nastavení s dvojitým modulem** ZKOPÍRUJTE následující JSON a vložte ho do pole.

   ![Nastavení vlákna v modulu](./media/how-to-deploy-blob/addmodule-tab4.png)

   Nakonfigurujte každou vlastnost s odpovídající hodnotou, která je označena zástupnými symboly. Pokud používáte simulátor IoT Edge, nastavte hodnoty na související proměnné prostředí pro tyto vlastnosti, jak jsou popsány v tématu [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) a [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

   ```json
   {
     "deviceAutoDeleteProperties": {
       "deleteOn": <true, false>,
       "deleteAfterMinutes": <timeToLiveInMinutes>,
       "retainWhileUploading": <true,false>
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
       "deleteAfterUpload": <true,false>
     }
   }
   ```

   Informace o konfiguraci deviceToCloudUploadProperties a deviceAutoDeleteProperties po nasazení modulu najdete v tématu [editace modulu s dvojitou](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin)platností. Další informace o požadovaných vlastnostech najdete v tématu [definice nebo aktualizace požadovaných vlastností](module-composition.md#define-or-update-desired-properties).

6. Vyberte **Přidat**.

7. Vyberte **Další: trasy** pro pokračování v části trasy.

#### <a name="specify-routes"></a>Zadejte trasy

Ponechte výchozí trasy a vyberte **Další: zkontrolovat + vytvořit** , abyste mohli pokračovat v části revize.

#### <a name="review-deployment"></a>Zkontrolujte nasazení

Zkontrolujte část pořady, kterou jste nasazení JSON manifestu byl vytvořen na základě vašeho výběru v předchozích dvou částech. K dispozici jsou také dva moduly, které jste nepřidali: **$edgeAgent** a **$edgeHub**. Tyto dva moduly tvoří [modul runtime IoT Edge](iot-edge-runtime.md) a jsou povinné výchozí hodnoty v každém nasazení.

Zkontrolujte informace o svém nasazení a pak vyberte **vytvořit**.

### <a name="verify-your-deployment"></a>Ověření nasazení

Po vytvoření nasazení se vrátíte na stránku **IoT Edge** služby IoT Hub.

1. Vyberte zařízení IoT Edge, na které cílíte nasazení, a otevřete jeho podrobnosti.
1. V podrobnostech o zařízení ověřte, že je modul BLOB Storage uveden v části **nasazení** a **nahlášený zařízením**.

Může chvíli trvat, než se modul na zařízení spustí a pak se znovu oznámí IoT Hub. Aktualizujte stránku, aby se zobrazil aktualizovaný stav.

## <a name="deploy-from-visual-studio-code"></a>Nasazení z Visual Studio Code

Azure IoT Edge poskytuje šablony ve Visual Studio Code a pomohou vám vytvořit řešení edge. Pomocí následujících kroků můžete vytvořit nové řešení IoT Edge s modulem BLOB Storage a nakonfigurovat manifest nasazení.

1. Vyberte **zobrazení** > **příkaz palety**.

1. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge).

   ![Spustit nové řešení IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Pro vytvoření souborů řešení vyberte umístění na vývojovém počítači Visual Studio Code. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Vyberte **existující modul (zadejte úplnou adresu URL obrázku)** . |
   | Zadejte název modulu | Zadejte název pro váš modul, například **azureblobstorageoniotedge**.<br/><br/>Je důležité používat pro Azure Blob Storage v modulu IoT Edge malý název. IoT Edge rozlišuje velká a malá písmena při odkazování na moduly a sada SDK pro úložiště je ve výchozím nastavení malá. |
   | Poskytněte image Docker pro modul. | Zadejte identifikátor URI Image: **MCR.Microsoft.com/Azure-Blob-Storage:Latest** |

   Visual Studio Code převezme poskytnuté informace, vytvoří řešení IoT Edge a pak ho načte do nového okna. Šablona řešení vytvoří šablonu manifestu nasazení, která obsahuje bitové kopie modulu úložiště objektů blob, ale je potřeba nakonfigurovat možnosti vytvoření modulu.

1. Otevřít *deployment.template.json* nový pracovní prostor řešení a najít **moduly** oddílu. Proveďte následující změny konfigurace:

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

1. Nahraďte `<your storage account key>` klíčem Base64 64-byte. Vygenerujete nějaký klíč pomocí nástrojů jako [GeneratePlus](https://generate.plus/en/base64). Použijete tyto přihlašovací údaje pro přístup k úložišti objektů blob z jiných modulů.

1. V závislosti na operačním systému vašeho kontejneru nahraďte `<storage mount>`. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cesta do adresáře na zařízení IoT Edge místo modulu objektů blob pro uložení data. Připojení úložiště mapuje umístění v zařízení, které zadáte do umístění sady v modulu.  

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

1. Klikněte pravým tlačítkem na **deployment.template.json** a vyberte **manifest nasazení IoT Edge generovat**.

1. Visual Studio Code přebírá informace, které jste zadali v souboru *Deployment. template. JSON* a používá ho k vytvoření nového souboru manifestu nasazení. Manifest nasazení se vytvoří v novém **config** složky v pracovním prostoru řešení. Až budete mít tento soubor, můžete postupovat podle kroků v [moduly nasazení Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md) nebo [moduly nasazení Azure IoT Edge pomocí Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Nasazení více instancí modulu

Pokud chcete nasadit více instancí služby Azure Blob Storage v modulu IoT Edge, je nutné zadat jinou cestu úložiště a změnit `HostPort` hodnotu, na kterou se modul váže. Moduly objektu blob úložiště vždy zpřístupňuje porty 11002 v kontejneru, ale je možné deklarovat port, který je vázán na hostiteli.

Upravit **možnosti vytvoření kontejneru** (v Azure Portal) nebo pole **createOptions** (v Visual Studio Code souboru *Deployment. template. JSON* ), chcete-li změnit hodnotu `HostPort`:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Když se připojíte k modulům další objekt blob úložiště, změňte koncový bod tak, aby odkazoval na aktualizovaném hostiteli port.

## <a name="next-steps"></a>Další kroky
Další informace o [Azure Blob Storage v IoT Edge](how-to-store-data-blob.md)

Další informace o způsobu práce manifesty nasazení a o tom, k jejich vytvoření najdete v tématu [pochopit, jak můžete použít moduly IoT Edge a způsob jejich konfiguraci a znovu použít](module-composition.md).
