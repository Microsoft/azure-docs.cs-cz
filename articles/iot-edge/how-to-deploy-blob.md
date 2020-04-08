---
title: Nasazení úložiště objektů blob na modulu do vašeho zařízení – Azure IoT Edge
description: Nasaďte modul úložiště objektů blob Azure do zařízení IoT Edge a uložte data na hraniční chod.
author: kgremban
ms.author: kgremban
ms.date: 3/10/2020
ms.topic: conceptual
ms.service: iot-edge
ms.reviewer: arduppal
ms.openlocfilehash: da163e902d06bd98ac47a24256cb809cb222173b
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804618"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Nasazení modulu IoT Edge pro službu Azure Blob Storage na zařízení

Existuje několik způsobů, jak nasadit moduly do zařízení IoT Edge a všechny z nich fungují pro azure blob storage na modulech IoT Edge. Dvě nejjednodušší metody jsou použití portálu Azure nebo šablony kódu Visual Studia.

## <a name="prerequisites"></a>Požadavky

- Centrum [IoT v](../iot-hub/iot-hub-create-through-portal.md) předplacenéazure.
- [Zařízení IoT Edge](how-to-register-device.md) s nainstalovaným runtimem IoT Edge.
- [Visual Studio Code](https://code.visualstudio.com/) a [nástroje Azure IoT,](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) pokud se nasazujete z kódu Visual Studia.

## <a name="deploy-from-the-azure-portal"></a>Nasazení z webu Azure Portal

Portál Azure vás provede vytvořením manifestu nasazení a odesláním nasazení na zařízení IoT Edge.

### <a name="select-your-device"></a>Výběr zařízení

1. Přihlaste se k [portálu Azure](https://portal.azure.com) a přejděte do svého centra IoT hub.
1. V nabídce vyberte **IoT Edge.**
1. Klikněte na ID cílového zařízení ze seznamu zařízení.
1. Vyberte **možnost Nastavit moduly**.

### <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly nasadit, jak toky dat mezi moduly a požadované vlastnosti dvojčat modulu. Portál Azure má průvodce, který vás provede vytvořením manifestu nasazení. Má tři kroky uspořádané do karet: **Moduly**, **Trasy**a **Revize + Vytvořit**.

#### <a name="add-modules"></a>Přidat moduly

1. V části **Moduly IoT Edge** na stránce klikněte na rozevírací seznam **Přidat** a vyberte **Modul IoT Edge,** abyste zobrazili stránku **Přidat modul IoT Edge.**

2. Na kartě **Nastavení modulu** zadejte název modulu a zadejte identifikátor URI bitové kopie kontejneru:

   Příklady:
  
   - **Název modulu IoT Edge**:`azureblobstorageoniotedge`
   - **Identifikátor URI obrázku**:`mcr.microsoft.com/azure-blob-storage:latest`

   ![Nastavení dvojčete modulu](./media/how-to-deploy-blob/addmodule-tab1.png)

   Nevybírejte **přidat,** dokud nezadáte hodnoty na kartách **Nastavení modulu**, **Možnosti vytvoření kontejneru**a **Nastavení dvojčete modulu,** jak je popsáno v tomto postupu.

   > [!IMPORTANT]
   > Azure IoT Edge se rozlišuje malá a velká písmena při volání modulů a sada Storage SDK také výchozí malé a malé písmena. I když název modulu na [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) je **AzureBlobStorageonIoTEdge**, změna názvu na malá písmena pomáhá zajistit, že vaše připojení k úložišti objektů blob Azure na IoT Edge modul nejsou přerušeny.

3. Otevřete kartu **Možnosti vytvoření kontejneru.**

   ![Nastavení dvojčete modulu](./media/how-to-deploy-blob/addmodule-tab3.png)

   Zkopírujte a vložte následující JSON do pole, abyste poskytli informace o účtu úložiště a připojení pro úložiště v zařízení.
  
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

4. Aktualizujte json, který jste zkopírovali do **možností vytvoření kontejneru,** pomocí následujících informací:

   - Nahraďte `<your storage account name>` název, který si pamatujete. Názvy účtů by měly mít dlouhý počet 3 až 24 znaků s velkými písmeny a čísly. Žádné mezery.

   - Nahraďte `<your storage account key>` 64bajtova základnou64. Můžete vygenerovat klíč s nástroji, jako [je GeneratePlus](https://generate.plus/en/base64). Tato pověření použijete k přístupu k úložišti objektů blob z jiných modulů.

   - Vyměňte `<storage mount>` podle operačního systému kontejneru. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cestu k existujícímu adresáři na zařízení IoT Edge, kde bude modul objektů blob ukládat svá data. Připojení úložiště mapuje umístění na vašem zařízení, které zadáte na nastavené umístění v modulu.

     - Pro linuxové kontejnery je ** \<formát vaší cestou úložiště nebo> svazku:/blobroot**. Příklad:
         - použijte [držák hlasitosti](https://docs.docker.com/storage/volumes/):`my-volume:/blobroot`
         - použijte [vazbu:](https://docs.docker.com/storage/bind-mounts/) `/srv/containerdata:/blobroot`. Postupujte podle pokynů k [udělení přístupu k adresáři uživateli kontejneru.](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Pro kontejnery Windows je ** \<formát cesta úložiště nebo svazek>:C:/BlobRoot**. Příklad:
         - použijte držák `my-volume:C:/BlobRoot` [hlasitosti](https://docs.docker.com/storage/volumes/): .
         - použijte [vazbu:](https://docs.docker.com/storage/bind-mounts/) `C:/ContainerData:C:/BlobRoot`.
         - Místo použití místního disku můžete mapovat umístění v síti SMB, kde najdete další informace, viz [použití sdílené složky SMB jako místního úložiště](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Neměňte druhou polovinu hodnoty připojení úložiště, která odkazuje na konkrétní umístění v modulu Úložiště objektů blob na ioT edge. Připojení úložiště musí vždy končit **:/blobroot** pro linuxové kontejnery a **:C:/BlobRoot** pro kontejnery Windows.

5. Na kartě **Nastavení dvojčete modulu** zkopírujte následující json a vložte jej do pole.

   ![Nastavení dvojčete modulu](./media/how-to-deploy-blob/addmodule-tab4.png)

   Nakonfigurujte každou vlastnost s příslušnou hodnotou, jak je uvedeno zástupnými symboly. Pokud používáte simulátor IoT Edge, nastavte hodnoty na související proměnné prostředí pro tyto vlastnosti, jak je popsáno [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) a [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties).

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

   Informace o konfiguraci zařízeníToCloudUploadProperties a deviceAutoDeleteProperties po nasazení modulu naleznete v [tématu Úprava dvojčete modulu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Další informace o požadovaných vlastnostech naleznete v [tématu Definování nebo aktualizace požadovaných vlastností](module-composition.md#define-or-update-desired-properties).

6. Vyberte **Přidat**.

7. Vyberte **Další: Trasy,** abyste pokračovali do úseku trasy.

#### <a name="specify-routes"></a>Určení tras

Zachovat výchozí trasy a vyberte **Další: Revize + vytvořit** pokračovat do sekce revize.

#### <a name="review-deployment"></a>Zkontrolovat nasazení

Část revize zobrazuje manifest nasazení JSON, který byl vytvořen na základě vašich výběrů v předchozích dvou částech. Existují také dva moduly, které jste nepřidali: **$edgeAgent** a **$edgeHub**. Tyto dva moduly tvoří [modul runtime IoT Edge](iot-edge-runtime.md) a jsou povinné výchozí hodnoty v každém nasazení.

Zkontrolujte informace o nasazení a vyberte **Vytvořit**.

### <a name="verify-your-deployment"></a>Ověření nasazení

Po vytvoření nasazení se vrátíte na stránku **IoT Edge** vašeho centra IoT hub.

1. Vyberte zařízení IoT Edge, na které jste cílí s nasazením otevřít jeho podrobnosti.
1. V podrobnostech o zařízení ověřte, že modul úložiště objektů blob je uveden jako **zadán v nasazení** a hlášeno **zařízením**.

Může trvat několik okamžiků, než se modul spustí na zařízení a pak se ohlásí zpět do ioT hubu. Aktualizujte stránku, abyste viděli aktualizovaný stav.

## <a name="deploy-from-visual-studio-code"></a>Nasazení z kódu sady Visual Studio

Azure IoT Edge poskytuje šablony v kódu Visual Studia, které vám pomůžou vyvíjet hraniční řešení. Pomocí následujících kroků vytvořte nové řešení IoT Edge s modulem úložiště objektů blob a nakonfigurujte manifest nasazení.

1. Vyberte **Zobrazit** > **paletu příkazů**.

1. Na paletě příkazů zadejte a spusťte příkaz **Azure IoT Edge: New IoT Edge solution** (Azure IoT Edge: Nové řešení IoT Edge).

   ![Spuštění nového řešení IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění ve vývojovém počítači pro kód sady Visual Studio a vytvořte soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název řešení nebo přijměte výchozí **edgesolution**. |
   | Vyberte šablonu modulu | Zvolte **Existující modul (Zadejte úplnou adresu URL obrázku).** |
   | Zadejte název modulu | Zadejte název s podiňovým písmenem pro váš modul, například **azureblobstorageoniotedge**.<br/><br/>Je důležité použít malý název pro azure blob storage na IoT Edge modulu. IoT Edge rozlišuje malá a velká písmena při odkazování na moduly a storage SDK výchozí malá písmena. |
   | Poskytnout image Dockeru pro modul | Zadejte identifikátor URI obrázku: **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code převezme informace, které jste zadali, vytvoří řešení IoT Edge a pak je načte do nového okna. Šablona řešení vytvoří šablonu manifestu nasazení, která obsahuje bitovou kopii modulu úložiště objektů blob, ale je třeba nakonfigurovat možnosti vytvoření modulu.

1. Otevřete *soubor deployment.template.json* v novém pracovním prostoru řešení a vyhledejte část **modulů.** Proveďte následující změny konfigurace:

   1. Odstraňte modul **SimulatedTemperatureSensor,** protože to není nutné pro toto nasazení.

   1. Zkopírujte a vložte `createOptions` do pole následující kód:

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

      ![Aktualizovat modul createOptions – kód sady Visual Studio](./media/how-to-deploy-blob/create-options.png)

1. Nahraďte `<your storage account name>` název, který si pamatujete. Názvy účtů by měly mít dlouhý počet 3 až 24 znaků s velkými písmeny a čísly. Žádné mezery.

1. Nahraďte `<your storage account key>` 64bajtova základnou64. Můžete vygenerovat klíč s nástroji, jako [je GeneratePlus](https://generate.plus/en/base64). Tato pověření použijete k přístupu k úložišti objektů blob z jiných modulů.

1. Vyměňte `<storage mount>` podle operačního systému kontejneru. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cestu k adresáři na zařízení IoT Edge, kde chcete, aby modul objektů blob ukládaný jeho data. Připojení úložiště mapuje umístění na vašem zařízení, které zadáte na nastavené umístění v modulu.  

     - Pro linuxové kontejnery je ** \<formát vaší cestou úložiště nebo> svazku:/blobroot**. Příklad:
         - použijte [držák hlasitosti](https://docs.docker.com/storage/volumes/):`my-volume:/blobroot`
         - použijte [vazbu:](https://docs.docker.com/storage/bind-mounts/) `/srv/containerdata:/blobroot`. Postupujte podle pokynů k [udělení přístupu k adresáři uživateli kontejneru.](how-to-store-data-blob.md#granting-directory-access-to-container-user-on-linux)
     - Pro kontejnery Windows je ** \<formát cesta úložiště nebo svazek>:C:/BlobRoot**. Například
         - použijte držák `my-volume:C:/BlobRoot` [hlasitosti](https://docs.docker.com/storage/volumes/): .
         - použijte [vazbu:](https://docs.docker.com/storage/bind-mounts/) `C:/ContainerData:C:/BlobRoot`.
         - Namísto použití místního disku můžete mapovat umístění v síti SMB, kde naleznete další informace, viz [použití sdílené složky SMB jako místního úložiště](how-to-store-data-blob.md#using-smb-share-as-your-local-storage)

     > [!IMPORTANT]
     > Neměňte druhou polovinu hodnoty připojení úložiště, která odkazuje na konkrétní umístění v modulu Úložiště objektů blob na ioT edge. Připojení úložiště musí vždy končit **:/blobroot** pro linuxové kontejnery a **:C:/BlobRoot** pro kontejnery Windows.

1. Nakonfigurujte [zařízeníToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) a [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties) pro váš modul přidáním následujícího json do souboru *deployment.template.json.* Nakonfigurujte každou vlastnost s příslušnou hodnotou a uložte soubor. Pokud používáte simulátor IoT Edge, nastavte hodnoty na související proměnné prostředí pro tyto vlastnosti, které najdete v části vysvětlení [deviceToCloudUploadProperties](how-to-store-data-blob.md#devicetoclouduploadproperties) a [deviceAutoDeleteProperties](how-to-store-data-blob.md#deviceautodeleteproperties)

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

   ![nastavit požadované vlastnosti pro azureblobstorageoniotedge - Visual Studio Code](./media/how-to-deploy-blob/devicetocloud-deviceautodelete.png)

   Informace o konfiguraci zařízeníToCloudUploadProperties a deviceAutoDeleteProperties po nasazení modulu naleznete v [tématu Úprava dvojčete modulu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Další informace o možnostech vytváření kontejnerů, restartování zásad a požadovaném stavu naleznete v [tématu EdgeAgent požadované vlastnosti](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Uložte soubor *deployment.template.json*.

1. Klikněte pravým tlačítkem myši na **soubor deployment.template.json** a vyberte **generovat manifest nasazení IoT Edge**.

1. Visual Studio Code přebírá informace, které jste zadali v *deployment.template.json* a používá k vytvoření nového souboru manifestu nasazení. Manifest nasazení je vytvořen v nové složce **konfigurace** v pracovním prostoru řešení. Jakmile budete mít tento soubor, můžete postupovat podle kroků v [nasazení modulů Azure IoT Edge z visual studio kódu](how-to-deploy-modules-vscode.md) nebo nasazení [modulů Azure IoT Edge s Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Nasazení více instancí modulů

Pokud chcete nasadit více instancí azure blob storage na IoT Edge modulu, `HostPort` musíte poskytnout jinou cestu úložiště a změnit hodnotu, která se váže na modul. Moduly úložiště objektů blob vždy vystavit port 11002 v kontejneru, ale můžete deklarovat, který port je vázán na hostitele.

Chcete-li změnit hodnotu, upravte **možnosti vytvoření kontejneru** `HostPort` (na webu Azure Portal) nebo pole **createOptions** (v souboru *deployment.template.json* v kódu sady Visual Studio):

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Když se připojíte k dalším modulům úložiště objektů blob, změňte koncový bod tak, aby ukazoval na aktualizovaný port hostitele.

## <a name="configure-proxy-support"></a>Konfigurace podpory proxy

Pokud vaše organizace používá proxy server, budete muset nakonfigurovat podporu proxy pro moduly edgeAgent a edgeHub runtime. Tento proces zahrnuje dva úkoly:

- Nakonfigurujte runtime daemons a agenta IoT Edge na zařízení.
- Nastavte proměnnou prostředí HTTPS_PROXY pro moduly v souboru Manifestu nasazení JSON.

Tento proces je popsán v [části Konfigurace zařízení IoT Edge pro komunikaci prostřednictvím proxy serveru](how-to-configure-proxy-support.md).

Kromě toho modul úložiště objektů blob také vyžaduje nastavení HTTPS_PROXY v souboru nasazení manifestu. Můžete přímo upravit soubor manifestu nasazení nebo použít portál Azure.

1. Přejděte do svého centra Iot hub na webu Azure Portal a v yberte **Iot Edge** z nabídky levého podokna.

1. Vyberte zařízení s modulem, který chcete konfigurovat.

1. Vyberte **možnost Nastavit moduly**.

1. V části **Moduly IoT Edge** na stránce vyberte modul úložiště objektů blob.

1. Na stránce **Aktualizovat modul IoT Edge** vyberte kartu Proměnné **prostředí.**

1. Přidejte `HTTPS_PROXY` **název** a adresu URL proxy **pro hodnotu**.

      ![Nastavit proměnnou prostředí HTTPS_PROXY](./media/how-to-deploy-blob/https-proxy-config.png)

1. Klepněte na tlačítko **Aktualizovat**, potom **na položku Revize + Vytvořit**.

1. Všimněte si, že proxy server je přidán do modulu v manifestu nasazení a vyberte **Vytvořit**.

1. Ověřte nastavení výběrem modulu na stránce podrobností o zařízení a ve spodní části stránky **Podrobnosti modulů IoT Edge** vyberte kartu **Proměnné prostředí.**

      ![Nastavit proměnnou prostředí HTTPS_PROXY](./media/how-to-deploy-blob/verify-proxy-config.png)

## <a name="next-steps"></a>Další kroky

Další informace o [Azure Blob Storage na IoT Edge](how-to-store-data-blob.md).

Další informace o tom, jak manifesty nasazení fungují a jak je vytvořit, najdete v [tématu Informace o tom, jak lze moduly IoT Edge používat, konfigurovat a znovu používat](module-composition.md).
