---
title: Nasaďte v modulu úložiště objektů blob do zařízení – Azure IoT Edge
description: Nasaďte modul Azure Blob Storage do zařízení IoT Edge, abyste mohli ukládat data na hraničních zařízeních.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: 2b5b7b45cc52d900e5ecde59e6a5ae203533286b
ms.sourcegitcommit: d103a93e7ef2dde1298f04e307920378a87e982a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/13/2020
ms.locfileid: "91978862"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Nasazení modulu IoT Edge pro službu Azure Blob Storage na zařízení

Existuje několik způsobů, jak nasadit moduly do zařízení IoT Edge a všechny je fungují pro Azure Blob Storage v IoT Edgech modulech. Tyto dvě nejjednodušší metody slouží k použití šablon Azure Portal nebo Visual Studio Code.

## <a name="prerequisites"></a>Předpoklady

- [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.
- Zařízení IoT Edge.

  Pokud nemáte nastavené zařízení IoT Edge, můžete ho vytvořit na virtuálním počítači Azure. Použijte postup v jednom z článků rychlý Start k [Vytvoření virtuálního zařízení](quickstart-linux.md) se systémem Linux nebo [Vytvoření virtuálního zařízení s Windows](quickstart.md).

- [Visual Studio Code](https://code.visualstudio.com/) a [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) , pokud nasazujete z Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Nasazení z Azure Portal

Azure Portal vás provede vytvořením manifestu nasazení a vložením nasazení do IoT Edgeho zařízení.

### <a name="select-your-device"></a>Vyberte své zařízení.

1. Přihlaste se k [Azure Portal](https://portal.azure.com) a přejděte do služby IoT Hub.
1. V nabídce vyberte **IoT Edge** .
1. V seznamu zařízení klikněte na ID cílového zařízení.
1. Vyberte možnost **nastavit moduly**.

### <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly se mají nasadit, způsob, jakým jsou toky dat mezi moduly a požadované vlastnosti v modulu vlákna. Azure Portal má průvodce, který vás provede vytvořením manifestu nasazení. Obsahuje tři kroky uspořádané do karet: **moduly**, **trasy**a **Revize + vytvořit**.

#### <a name="add-modules"></a>Přidat moduly

1. V části **IoT Edge moduly** na stránce klikněte na rozevírací seznam **přidat** a vyberte **IoT Edge modul** . zobrazí se stránka **Přidat IoT Edge modul** .

2. Na kartě **nastavení modulu** zadejte název modulu a pak zadejte identifikátor URI image kontejneru:

   Příklady:
  
   - **Název modulu IoT Edge**: `azureblobstorageoniotedge`
   - **Identifikátor URI image**: `mcr.microsoft.com/azure-blob-storage:latest`

   ![Nastavení vlákna v modulu](./media/how-to-deploy-blob/addmodule-tab1.png)

   Nevybírejte možnost **Přidat** , dokud neurčíte hodnoty v **nastavení modulu**, **možnosti vytvoření kontejneru**a  **Dvojitá nastavení modulu** , jak je popsáno v tomto postupu.

   > [!IMPORTANT]
   > Azure IoT Edge rozlišuje velká a malá písmena, když provedete volání modulů a sada SDK úložiště má výchozí hodnotu malá písmena. I když je název modulu v [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) **AzureBlobStorageonIoTEdge**, změna názvu na malá písmena vám pomůže zajistit, že se připojení k Azure Blob Storage v modulu IoT Edge nepřerušila.

3. Otevřete kartu **možnosti vytvoření kontejneru** .

   ![Nastavení vlákna v modulu](./media/how-to-deploy-blob/addmodule-tab3.png)

   Zkopírováním a vložením následujícího formátu JSON do pole zadejte informace o účtu úložiště a připojení k úložišti na svém zařízení.
  
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

4. Aktualizujte kód JSON, který jste zkopírovali do **kontejneru možnosti vytvoření** , s následujícími informacemi:

   - Nahraďte `<your storage account name>` názvem, který si můžete pamatovat. Názvy účtů by měly mít délku 3 až 24 znaků a malými písmeny a číslicemi. Žádné mezery.

   - Nahraďte `<your storage account key>` klíčem base64 64-byte. Klíč můžete vygenerovat pomocí nástrojů, jako je [GeneratePlus](https://generate.plus/en/base64). Tyto přihlašovací údaje použijete pro přístup k úložišti objektů BLOB z jiných modulů.

   - Nahraďte `<storage mount>` v závislosti na vašem operačním systému vašeho kontejneru. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cestu k existujícímu adresáři na IoT Edge zařízení, ve kterém bude modul BLOB ukládat data. Připojení úložiště mapuje umístění v zařízení, které zadáte do umístění sady v modulu.

     - U kontejnerů pro Linux je formát ** \<your storage path or volume> :/blobroot**. Například:
         - použít [připojení svazku](https://docs.docker.com/storage/volumes/): `my-volume:/blobroot`
         - použít [připojení BIND](https://docs.docker.com/storage/bind-mounts/): `/srv/containerdata:/blobroot` . Nezapomeňte postupovat podle pokynů pro [udělení přístupu k adresáři uživateli kontejneru](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) .
     - V případě kontejnerů Windows je ve formátu ** \<your storage path or volume> : C:/BlobRoot**. Například:
         - použít [připojení svazku](https://docs.docker.com/storage/volumes/): `my-volume:C:/BlobRoot` .
         - použít [připojení BIND](https://docs.docker.com/storage/bind-mounts/): `C:/ContainerData:C:/BlobRoot` .
         - Místo používání místní jednotky můžete namapovat síťové umístění protokolu SMB. Další informace najdete v tématu [použití sdílené složky SMB jako místního úložiště](how-to-store-data-blob.md#using-smb-share-as-your-local-storage) .

     > [!IMPORTANT]
     > Neměňte druhou polovinu hodnoty připojení úložiště, která odkazuje na konkrétní umístění v Blob Storage v modulu IoT Edge. Připojení úložiště musí vždy končit **:/blobroot** for Linux Containers a **: C:/blobroot** for Windows Containers.

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

#### <a name="specify-routes"></a>Zadat trasy

Ponechte výchozí trasy a vyberte **Další: zkontrolovat + vytvořit** , abyste mohli pokračovat v části revize.

#### <a name="review-deployment"></a>Zkontrolovat nasazení

V části Kontrola se dozvíte, který manifest nasazení JSON byl vytvořen na základě vašich výběrů v předchozích dvou částech. K dispozici jsou také dva moduly, které jste nepřidali: **$edgeAgent** a **$edgeHub**. Tyto dva moduly tvoří [modul runtime IoT Edge](iot-edge-runtime.md) a jsou požadované výchozí hodnoty v každém nasazení.

Zkontrolujte informace o svém nasazení a pak vyberte **vytvořit**.

### <a name="verify-your-deployment"></a>Ověření nasazení

Po vytvoření nasazení se vrátíte na stránku **IoT Edge** služby IoT Hub.

1. Vyberte zařízení IoT Edge, na které cílíte nasazení, a otevřete jeho podrobnosti.
1. V podrobnostech o zařízení ověřte, že je modul BLOB Storage uveden v části **nasazení** a **nahlášený zařízením**.

Může chvíli trvat, než se modul na zařízení spustí a pak se znovu oznámí IoT Hub. Aktualizujte stránku, aby se zobrazil aktualizovaný stav.

## <a name="deploy-from-visual-studio-code"></a>Nasazení z Visual Studio Code

Azure IoT Edge poskytuje šablony v Visual Studio Code, které vám pomůžou vyvíjet řešení Edge. Pomocí následujících kroků můžete vytvořit nové řešení IoT Edge s modulem BLOB Storage a nakonfigurovat manifest nasazení.

1. Vyberte **zobrazení**  >  **paleta příkazů**.

1. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge).

   ![Spustit nové řešení IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vybrat složku | Pro vytvoření souborů řešení vyberte umístění na vývojovém počítači Visual Studio Code. |
   | Zadejte název řešení | Zadejte popisný název vašeho řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Vyberte **existující modul (zadejte úplnou adresu URL obrázku)**. |
   | Zadejte název modulu | Zadejte název pro váš modul, například **azureblobstorageoniotedge**.<br/><br/>Je důležité používat pro Azure Blob Storage v modulu IoT Edge malý název. IoT Edge rozlišuje velká a malá písmena při odkazování na moduly a sada SDK pro úložiště je ve výchozím nastavení malá. |
   | Poskytněte image Docker pro modul. | Zadejte identifikátor URI Image: **MCR.Microsoft.com/Azure-Blob-Storage:Latest** |

   Visual Studio Code převezme poskytnuté informace, vytvoří řešení IoT Edge a pak ho načte do nového okna. Šablona řešení vytvoří šablonu manifestu nasazení, která zahrnuje image modulu BLOB Storage, ale je potřeba nakonfigurovat možnosti vytváření modulu.

1. V novém pracovním prostoru řešení otevřete *deployment.template.js* a vyhledejte část **moduly** . Proveďte následující změny konfigurace:

   1. Odstraňte modul **SimulatedTemperatureSensor** , protože není pro toto nasazení nutný.

   1. Zkopírujte a vložte následující kód do `createOptions` pole:

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

1. Nahraďte `<your storage account key>` klíčem base64 64-byte. Klíč můžete vygenerovat pomocí nástrojů, jako je [GeneratePlus](https://generate.plus/en/base64). Tyto přihlašovací údaje použijete pro přístup k úložišti objektů BLOB z jiných modulů.

1. Nahraďte `<storage mount>` v závislosti na vašem operačním systému vašeho kontejneru. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cestu k adresáři na zařízení IoT Edge, kde chcete, aby modul BLOB ukládal svá data. Připojení úložiště mapuje umístění v zařízení, které zadáte do umístění sady v modulu.  

     - U kontejnerů pro Linux je formát ** \<your storage path or volume> :/blobroot**. Například:
         - použít [připojení svazku](https://docs.docker.com/storage/volumes/): `my-volume:/blobroot`
         - použít [připojení BIND](https://docs.docker.com/storage/bind-mounts/): `/srv/containerdata:/blobroot` . Nezapomeňte postupovat podle pokynů pro [udělení přístupu k adresáři uživateli kontejneru](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux) .
     - V případě kontejnerů Windows je ve formátu ** \<your storage path or volume> : C:/BlobRoot**. Například:
         - Použít [připojení svazku](https://docs.docker.com/storage/volumes/): `my-volume:C:/BlobRoot` .
         - Použít [připojení BIND](https://docs.docker.com/storage/bind-mounts/): `C:/ContainerData:C:/BlobRoot` .
         - Místo použití místního disku můžete mapovat síťové umístění protokolu SMB. Další informace najdete v tématu [použití sdílené složky SMB jako místního úložiště](how-to-store-data-blob.md#using-smb-share-as-your-local-storage).

     > [!IMPORTANT]
     > Neměňte druhou polovinu hodnoty připojení úložiště, která odkazuje na konkrétní umístění v Blob Storage v modulu IoT Edge. Připojení úložiště musí vždy končit **:/blobroot** for Linux Containers a **: C:/blobroot** for Windows Containers.

1. Nakonfigurujte [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) a [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) pro svůj modul přidáním následujícího JSON do *deployment.template.js* souboru. Nakonfigurujte každou vlastnost s odpovídající hodnotou a uložte soubor. Pokud používáte simulátor IoT Edge, nastavte hodnoty na související proměnné prostředí pro tyto vlastnosti, které najdete v části vysvětlení v tématu [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) a [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) .

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

1. Klikněte pravým tlačítkem na **deployment.template.jsna** a vyberte **generovat manifest nasazení IoT Edge**.

1. Visual Studio Code převezme informace, které jste zadali v *deployment.template.jsna* , a použije je k vytvoření nového souboru manifestu nasazení. Manifest nasazení se vytvoří v nové **konfigurační** složce v pracovním prostoru řešení. Jakmile budete mít tento soubor, můžete postupovat podle kroků v části [nasazení Azure IoT Edge moduly z Visual Studio Code](how-to-deploy-modules-vscode.md) nebo [nasazení Azure IoT Edge modulů pomocí Azure CLI 2,0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Nasazení více instancí modulu

Pokud chcete nasadit více instancí služby Azure Blob Storage v modulu IoT Edge, je nutné zadat jinou cestu úložiště a změnit `HostPort` hodnotu, na kterou se modul váže. Moduly BLOB Storage vždy zveřejňují port 11002 v kontejneru, ale můžete deklarovat, na který port je vázán na hostiteli.

Úpravou **možností vytvoření kontejneru** (v Azure Portal) nebo pole **createOptions** (v souboru *deployment.template.json* Visual Studio Code) změňte `HostPort` hodnotu:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Když se připojíte k dalším modulům úložiště objektů blob, změňte koncový bod tak, aby odkazoval na aktualizovaný port hostitele.

## <a name="configure-proxy-support"></a>Konfigurace podpory proxy

Pokud vaše organizace používá proxy server, budete muset nakonfigurovat podporu proxy pro moduly runtime edgeAgent a edgeHub. Tento proces zahrnuje dvě úlohy:

- Nakonfigurujte procesy démona modulu runtime a agenta IoT Edge v zařízení.
- Nastavte proměnnou prostředí HTTPS_PROXY pro moduly v souboru JSON manifestu nasazení.

Tento postup je popsaný v tématu [Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy server](how-to-configure-proxy-support.md).

Kromě toho modul BLOB Storage vyžaduje také nastavení HTTPS_PROXY v souboru nasazení manifestu. Můžete přímo upravit soubor manifestu nasazení nebo použít Azure Portal.

1. Přejděte do služby IoT Hub v Azure Portal a v nabídce levého podokna vyberte **IoT Edge** .

1. Vyberte zařízení s modulem, který chcete nakonfigurovat.

1. Vyberte možnost **nastavit moduly**.

1. V části **IoT Edge moduly** na stránce vyberte modul BLOB Storage.

1. Na stránce **aktualizovat IoT Edge modul** vyberte kartu **proměnné prostředí** .

1. Přidejte `HTTPS_PROXY` pro **název** a adresu URL proxy serveru pro danou **hodnotu**.

      ![Nastavit HTTPS_PROXY proměnnou prostředí](./media/how-to-deploy-blob/https-proxy-config.png)

1. Klikněte na **aktualizovat**a pak na **zkontrolovat + vytvořit**.

1. Všimněte si, že se proxy server přidal do modulu v manifestu nasazení a vyberte **vytvořit**.

1. Ověřte nastavení tak, že vyberete modul na stránce Podrobnosti o zařízení a v dolní části stránky s **podrobnostmi o IoT Edge modulech** vyberte kartu **proměnné prostředí** .

      ![Nastavit HTTPS_PROXY proměnnou prostředí](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [Azure Blob Storage v IoT Edge](how-to-store-data-blob.md).

Další informace o tom, jak manifesty nasazení fungují a jak je vytvořit, najdete v tématu [Vysvětlení způsobu použití, konfigurace a](module-composition.md)opětovného použití modulů IoT Edge.
