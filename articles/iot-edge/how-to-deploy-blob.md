---
title: Nasazení modulu Azure Blob Storage do zařízení – Azure IoT Edge | Dokumentace Microsoftu
description: Modul služby Azure Blob Storage nasadíte do zařízení IoT Edge k ukládání dat na hraničních zařízeních.
author: kgremban
ms.author: kgremban
ms.date: 05/21/2019
ms.topic: article
ms.service: iot-edge
ms.custom: seodec18
ms.reviewer: arduppal
manager: philmea
ms.openlocfilehash: d844e81de9cfb556e91ab5c0d5a8074c822cce0a
ms.sourcegitcommit: cfbc8db6a3e3744062a533803e664ccee19f6d63
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/21/2019
ms.locfileid: "65990471"
---
# <a name="deploy-the-azure-blob-storage-on-iot-edge-module-to-your-device"></a>Nasazení Azure Blob Storage na modul IoT Edge do zařízení

Existuje několik způsobů, jak nasadit moduly IoT Edge zařízení a všechny z nich pracovat pro službu Azure Blob Storage na moduly IoT Edge. Tyto dvě metody nejjednodušší jsou pomocí webu Azure portal nebo šablony Visual Studio Code.

## <a name="prerequisites"></a>Požadavky

- [Služby IoT hub](../iot-hub/iot-hub-create-through-portal.md) ve vašem předplatném Azure.
- [Zařízení IoT Edge](how-to-register-device-portal.md) nainstalován modul runtime IoT Edge.
- [Visual Studio Code](https://code.visualstudio.com/) a [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools) Pokud nasazení z Visual Studio Code.

## <a name="deploy-from-the-azure-portal"></a>Nasazení na webu Azure Portal

Na webu Azure portal vás provede vytvořením manifest nasazení a doručením (push) nasazení do zařízení IoT Edge.

### <a name="select-your-device"></a>Vyberte zařízení

1. Přihlaste se k [webu Azure portal](https://portal.azure.com) a přejděte do služby IoT hub.
1. Vyberte **IoT Edge** z nabídky.
1. Kliknutím na ID cílové zařízení ze seznamu zařízení.
1. Vyberte **Nastavit moduly**.

### <a name="configure-a-deployment-manifest"></a>Konfigurace manifestu nasazení

Manifest nasazení je dokument JSON, který popisuje, které moduly chcete nasadit, tok dat mezi moduly a požadované vlastnosti dvojčat modulů. Na webu Azure portal obsahuje průvodce, který vás provede procesem vytvoření manifestu nasazení, místo ruční vytváření dokumentů JSON. Má tři kroky: **Přidat moduly**, **trasy zadejte**, a **zkontrolujte nasazení**.

#### <a name="add-modules"></a>Přidat moduly

1. V **moduly nasazení** části stránky vyberte **přidat**.

1. Typy modulů v rozevíracím seznamu vyberte **modul IoT Edge**.

1. Zadejte název pro modul a poté zadejte image kontejneru:

   - **Název** -azureblobstorageoniotedge
   - **Identifikátor URI image** -mcr.microsoft.com/azure-blob-storage:latest

   > [!IMPORTANT]
   > Azure IoT Edge je velká a malá písmena, při volání k modulům a sady SDK služby Storage také výchozí hodnoty na malá písmena. I když název modulu v [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) je **AzureBlobStorageonIoTEdge**, mění se název na malá písmena pomáhá zajistit, aby připojení k Azure Blob Storage na modul IoT Edge nepřerušily.

1. Výchozí hodnota **možnosti vytvoření kontejneru** definovat hodnoty vazby portu, které váš kontejner potřebuje, ale musíte také přidat informace o vašem účtu úložiště a vazby k adresáři úložiště ve vašem zařízení. Nahraďte výchozí JSON na portálu ve formátu JSON:

   ```json
   {
     "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
     ],
     "HostConfig":{
       "Binds":[
           "<storage directory bind>"
       ],
     "PortBindings":{
       "11002/tcp":[{"HostPort":"11002"}]
       }
     }
   }
   ```

1. Aktualizace ve formátu JSON, který jste zkopírovali s následujícími informacemi:

   - Nahraďte `<your storage account name>` s názvem, které si zapamatujete. Názvy účtů musí být 3 až 24 znaků dlouhá a malá písmena a číslice. Bez mezer.

   - Nahraďte `<your storage account key>` s klíčem ve formátu base64 64 bajtů. Vygenerujete nějaký klíč pomocí nástrojů jako [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Použijete tyto přihlašovací údaje pro přístup k úložišti objektů blob z jiných modulů.

   - Nahraďte `<storage directory bind>` podle operačního systému kontejneru. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cesta do adresáře na zařízení IoT Edge místo modulu objektů blob pro uložení data. Vazba adresář úložiště mapuje umístění na vašem zařízení, které poskytujete nastavení umístění v modulu.

     - Pro kontejnery Linuxu ve formátu je  *\<cestu úložiště >: / blobroot*. Například **/srv/containerdata: / blobroot** nebo **tento svazek: / blobroot**.
     - Pro kontejnery Windows ve formátu je  *\<cestu úložiště >: C: / BlobRoot*. Například **C: / ContainerData:C: / BlobRoot** nebo **my-svazku: C: / blobroot**.

     > [!IMPORTANT]
     > Neměňte druhou polovinu adresáře úložiště vytvořit vazbu hodnotu, která odkazuje na konkrétní umístění v modulu. Vazba adresář úložiště by měl končit vždy **: / blobroot** pro kontejnery Linuxu a **: C: / BlobRoot** pro kontejnery Windows.

    ![Aktualizace modulu kontejneru vytvořit možnosti – portál](./media/how-to-store-data-blob/edit-module.png)

1. Nastavte [ovládání datových vrstev na](how-to-store-data-blob.md#tiering-properties) a [time-to-live](how-to-store-data-blob.md#time-to-live-properties) vlastnosti pro modul následující JSON zkopírováním a vložením do **požadované vlastnosti dvojčete modulu sady** pole. Konfigurace jednotlivých vlastností s odpovídající hodnotou, uložit a pokračovat s nasazením.

   ```json
   {
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>,
         "timeToLiveInMinutes": <timeToLiveInMinutes>
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>; EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }

      ```

   ![nastavit vlastnosti vrstvení a time-to-live](./media/how-to-store-data-blob/iotedge_custom_module.png)

   Informace o konfiguraci ovládání datových vrstev a hodnota TTL po nasazení modulu najdete v tématu [upravit dvojče modulu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Další informace o požadovaných vlastností najdete v tématu [definovat nebo aktualizace požadované vlastnosti](module-composition.md#define-or-update-desired-properties).

1. Vyberte **Uložit**.

1. Vyberte **Další** pokračujte k části trasy.

#### <a name="specify-routes"></a>Zadejte trasy

Ponechte výchozí trasy a vyberte **Další** pokračujte k části revize.

#### <a name="review-deployment"></a>Zkontrolujte nasazení

Zkontrolujte část pořady, kterou jste nasazení JSON manifestu byl vytvořen na základě vašeho výběru v předchozích dvou částech. Existují také dva moduly deklarovat, že jste nepřidali: **$edgeAgent** a **$edgeHub**. Tyto dva moduly tvoří [modul runtime IoT Edge](iot-edge-runtime.md) a jsou povinné výchozí hodnoty v každém nasazení.

Zkontrolujte informace o nasazení a pak vyberte **odeslat**.

### <a name="verify-your-deployment"></a>Ověření nasazení

Po odeslání nasazení můžete vrátit **IoT Edge** stránka služby IoT hub.

1. Vyberte zařízení IoT Edge, že jste určili při nasazení a otevřete její podrobnosti.
1. V podrobnostech zařízení zkontrolujte, zda modulu služby storage blob jako **zadaný v nasazení** a **zařízení ohlásilo**.

Může trvat několik minut pro modul na zařízení a potom hlášeny zpět do služby IoT Hub. Aktualizujte stránku, pokud chcete zobrazit aktualizovaný stav.

## <a name="deploy-from-visual-studio-code"></a>Nasazení z Visual Studio Code

Azure IoT Edge poskytuje šablony ve Visual Studio Code a pomohou vám vytvořit řešení edge. Použijte následující postup k vytvoření nového řešení IoT Edge s modulem úložiště objektů blob a ke konfiguraci manifest nasazení.

1. Vyberte **zobrazení** > **příkaz palety**.

1. V paletu příkazů zadejte a spusťte příkaz **Azure IoT Edge: Nové řešení IoT Edge**.

   ![Spustit nové řešení IoT Edge](./media/how-to-develop-csharp-module/new-solution.png)

   Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Vyberte umístění na vývojovém počítači pro Visual Studio Code k vytvoření těchto souborů řešení. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Zvolte **existujícímu modulu (úplnou bitovou kopii zadejte adresu URL)**. |
   | Zadejte název modulu | Zadejte název všechna malá pro modul, jako je třeba **službě Azure BLOB Storage**.<br /><br />Je důležité používat malá písmena název pro službu Azure Blob Storage na modul IoT Edge. IoT Edge je velká a malá písmena, při odkazování na moduly a sady SDK služby Storage výchozí hodnoty na malá písmena. |
   | Použijte image Dockeru pro modul | Zadejte identifikátor URI image: **mcr.microsoft.com/azure-blob-storage:latest** |

   Visual Studio Code přebírá informace k dispozici, vytvoří řešení IoT Edge a nahraje je v novém okně. Šablona řešení vytvoří šablonu manifestu nasazení, která obsahuje bitové kopie modulu úložiště objektů blob, ale je potřeba nakonfigurovat možnosti vytvoření modulu.

1. Otevřít *deployment.template.json* nový pracovní prostor řešení a najít **moduly** oddílu. Proveďte následující změny konfigurace:

   1. Odstranit **tempSensor** je modul, protože není nezbytné pro toto nasazení.

   1. Zkopírujte a vložte následující kód do `createOptions` pole:

      ```json
      "Env":[
       "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
       "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Aktualizace modulu CreateOptions field - Visual Studio Code](./media/how-to-store-data-blob/create-options.png)

1. Nahraďte `<your storage account name>` s názvem, které si zapamatujete. Názvy účtů musí být 3 až 24 znaků dlouhá a malá písmena a číslice. Bez mezer.

1. Nahraďte `<your storage account key>` s klíčem ve formátu base64 64 bajtů. Vygenerujete nějaký klíč pomocí nástrojů jako [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Použijete tyto přihlašovací údaje pro přístup k úložišti objektů blob z jiných modulů.

1. Nahraďte `<storage directory bind>` podle operačního systému kontejneru. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cesta do adresáře na zařízení IoT Edge místo modulu objektů blob pro uložení data. Vazba adresář úložiště mapuje umístění na vašem zařízení, které poskytujete nastavení umístění v modulu.  

      - Pro kontejnery Linuxu ve formátu je  *\<cestu úložiště >: / blobroot*. Například **/srv/containerdata: / blobroot** nebo **tento svazek: / blobroot**.
      - Pro kontejnery Windows ve formátu je  *\<cestu úložiště >: C: / BlobRoot*. Například **C: / ContainerData:C: / BlobRoot** nebo **my-svazku: C: / blobroot**.

      > [!IMPORTANT]
      > Neměňte druhou polovinu adresáře úložiště vytvořit vazbu hodnotu, která odkazuje na konkrétní umístění v modulu. Vazba adresář úložiště by měl končit vždy **: / blobroot** pro kontejnery Linuxu a **: C: / BlobRoot** pro kontejnery Windows.

1. Konfigurace [ovládání datových vrstev na](how-to-store-data-blob.md#tiering-properties) a [time-to-live](how-to-store-data-blob.md#time-to-live-properties) vlastnosti pro modul přidáním následujícího formátu JSON *deployment.template.json* souboru. Nakonfigurujte každou vlastnost s odpovídající hodnotu a uložte soubor.

   ```json
   "<your azureblobstorageoniotedge module name>":{
     "properties.desired": {
       "ttlSettings": {
         "ttlOn": <true, false>,
         "timeToLiveInMinutes": <timeToLiveInMinutes>
       },
       "tieringSettings": {
         "tieringOn": <true, false>,
         "backlogPolicy": "<NewestFirst, OldestFirst>",
         "remoteStorageConnectionString": "DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>",
         "tieredContainers": {
           "<source container name1>": {
             "target": "<target container name1>"
           }
         }
       }
     }
   }
   ```

   ![Nastavte požadované vlastnosti pro azureblobstorageoniotedge – Visual Studio Code](./media/how-to-store-data-blob/tiering_ttl.png)

   Informace o konfiguraci ovládání datových vrstev a hodnota TTL po nasazení modulu najdete v tématu [upravit dvojče modulu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin). Další informace o kontejneru možnosti vytváření, restartování zásad a požadované stavu, najdete v tématu [EdgeAgent požadované vlastnosti](module-edgeagent-edgehub.md#edgeagent-desired-properties).

1. Uložte soubor *deployment.template.json*.

1. Klikněte pravým tlačítkem na **deployment.template.json** a vyberte **manifest nasazení IoT Edge generovat**.

1. Visual Studio Code přebírá informace, které jste zadali v *deployment.template.json* a použije ho k vytvoření nového souboru manifestu nasazení. Manifest nasazení se vytvoří v novém **config** složky v pracovním prostoru řešení. Až budete mít tento soubor, můžete postupovat podle kroků v [moduly nasazení Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md) nebo [moduly nasazení Azure IoT Edge pomocí Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="deploy-multiple-module-instances"></a>Nasazení více instancí modulu

Pokud budete chtít nasadit víc instancí služby Azure Blob Storage na modul IoT Edge, budete muset zadat cestu jiného úložiště a změnit `HostPort` hodnotu, která vytvoří vazbu modulu. Moduly objektu blob úložiště vždy zpřístupňuje porty 11002 v kontejneru, ale je možné deklarovat port, který je vázán na hostiteli.

Upravit **možnosti vytvoření kontejneru** (na portálu Azure) nebo **CreateOptions field** pole (v *deployment.template.json* soubor ve Visual Studio Code) Chcete-li změnit `HostPort` hodnotu:

```json
"PortBindings":{
  "11002/tcp": [{"HostPort":"<port number>"}]
}
```

Když se připojíte k modulům další objekt blob úložiště, změňte koncový bod tak, aby odkazoval na aktualizovaném hostiteli port.

## <a name="next-steps"></a>Další postup

Další informace o způsobu práce manifesty nasazení a o tom, k jejich vytvoření najdete v tématu [pochopit, jak můžete použít moduly IoT Edge a způsob jejich konfiguraci a znovu použít](module-composition.md).
