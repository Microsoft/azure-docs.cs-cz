---
title: Store objekty BLOB bloku v zařízeních – Azure IoT Edge | Dokumentace Microsoftu
description: Modul služby Azure Blob Storage nasadíte do zařízení IoT Edge k ukládání dat na hraničních zařízeních.
author: kgremban
manager: philmea
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 03/07/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.custom: seodec18
ms.openlocfilehash: 0fc34c913453abd174009213233a54e30b9346d3
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/18/2019
ms.locfileid: "57881380"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store dat na hraničních zařízeních s Azure Blob Storage na hraničních zařízeních IoT (preview)

Poskytuje úložiště objektů Blob v Azure na hraničních zařízeních IoT [objektů blob bloku](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) řešení úložiště na hraničních zařízeních. Modul úložiště objektů blob na vašem zařízení IoT Edge se chová jako služby objektů blob bloku Azure, ale objekty BLOB bloku se ukládají místně na vašem zařízení IoT Edge. Získat přístup pomocí stejných metod, sady SDK služby Azure storage BLOB nebo blokovat volání rozhraní API objektů blob, které jste už zvyklí. 

Tento modul se dodává s **automatické vrstvení** a **automatické vypršení platnosti** funkce.

> [!NOTE]
> Aktuálně automatické vrstvení automaticky – funkce a vypršení platnosti jsou k dispozici pouze v systému Linux AMD64 a Linux ARM32.

**Automatické vrstvení** se dají konfigurovat funkce, které vám umožní automaticky nahrát data z vaší místní blob storage do Azure podporující přerušované připojení k Internetu. To vám umožní:
- Zapnout nebo vypnout funkce výběru vrstvy
- Zvolte, že pořadí, ve kterém se data zkopírují do Azure, jako je NewestFirst nebo OldestFirst
- Zadejte účet úložiště Azure, na který chcete nahrát data.
- Zadejte kontejnery, které chcete nahrát do Azure. Tento modul umožňuje zadat zdroj a cíl názvy kontejnerů.
- Úplná ovládání datových vrstev na objekt blob (pomocí `Put Blob` operace) a výběr vrstvy na úrovni blokování (pomocí `Put Block` a `Put Block List` operace).

Tento modul používá blok úrovně ovládání datových vrstev, pokud se objekt blob skládá z bloků. Tady jsou některé běžné scénáře:
- Aplikace aktualizuje některé bloky dříve nahraném objektu blob, bude tento modul nahrát pouze aktualizované bloky a ne celý objekt blob.
- Modul je ukládání objektů blob a připojení k Internetu zmizet, je-li připojení zpět znovu že odešle jenom zbývající bloky a ne celý objekt blob.

Pokud dojde procesu pro neočekávané ukončení (např. výpadku) při odeslání objektu blob, všechny bloky, které byly termín pro nahrávání se nahraje znovu, když modul přejde do režimu online.

**Automatické vypršení platnosti** je konfigurovat funkce, kde tento modul automaticky odstraní objektů blob z místního úložiště po uplynutí této doby to Live (TTL). Se měří v minutách. To vám umožní:
- Zapnout nebo vypnout funkci automatického vypršení platnosti
- Zadejte interval TTL, ZÍSKÁ v minutách

Scénáře, kde data, jako jsou videa, obrázky, daty o financování, data nemocnici nebo všechna data, která musí být uložená místně, později, která by mohla zpracovat místně nebo do cloudu přenáší jsou dobrými příklady použití tohoto modulu.

Tento článek obsahuje pokyny pro nasazení služby Azure Blob Storage na hraničních zařízeních IoT kontejneru, na kterém běží služba objektů blob na vašem zařízení IoT Edge. 

>[!NOTE]
>Azure Blob Storage na hraničních zařízeních IoT je v [ve verzi public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

Sledovat video o stručný úvod
> [!VIDEO https://www.youtube.com/embed/wkprcfVidyM]

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Jako hraniční zařízení můžete použít svůj vývojový počítač nebo virtuální počítač podle postupu v rychlém startu pro zařízení s [Linuxem](quickstart-linux.md) nebo [Windows](quickstart.md).
* Azure Blob Storage na modul IoT Edge podporuje následující konfigurace zařízení:

   | Operační systém | Architektura |
   | ---------------- | ------------ |
   | Ubuntu Server 16.04 | AMD64 |
   | Ubuntu Server 18.04 | AMD64 |
   | Windows 10 IoT Core (říjen aktualizace) | AMD64 |
   | Windows 10 IoT Enterprise (říjen aktualizace) | AMD64 |
   | Windows Server. 2019 | AMD64 |
   | Raspbian Stretch | ARM32 |

Cloudové prostředky:

* [IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Standard v Azure. 


## <a name="deploy-blob-storage-to-your-device"></a>Nasazení do zařízení úložiště objektů blob

Existuje několik způsobů, jak nasadit moduly IoT Edge zařízení a všechny z nich pracovat pro službu Azure Blob Storage na moduly IoT Edge. Tyto dvě metody nejjednodušší jsou pomocí webu Azure portal nebo šablony Visual Studio Code. 

### <a name="azure-portal"></a>portál Azure

Na webu Azure Marketplace poskytuje IoT Edge moduly, které je možné nasadit přímo do zařízení IoT Edge, včetně služby Azure Blob Storage na hraničních zařízeních IoT. Postupujte podle těchto kroků nasadíte modulu na webu Azure Portal.

1. V [webu Azure portal](https://portal.azure.com), vyhledejte "Azure Blob Storage na hraničních zařízeních IoT". A **vyberte** výsledek hledání z webu Marketplace.

   ![Vytvoření modulu z Marketplace hledání](./media/how-to-store-data-blob/marketplace-module.png)

2. Vyberte zařízení IoT Edge pro příjem tento modul. Na **cílová zařízení pro modul IoT Edge** stránky, zadejte následující informace:

   1. Vyberte **předplatné** , který obsahuje IoT hub, kterou používáte.

   2. Vyberte vaše **služby IoT Hub**.

   3. Pokud znáte vaše **název zařízení IoT Edge**, zadejte, který v textovém poli. Nebo vyberte **najít zařízení** vybírat ze seznamu zařízení IoT Edge ve službě IoT hub. 
   
   4. Vyberte **Vytvořit**.

   Teď, když jste se rozhodli modul IoT Edge z Azure Marketplace a vybrali zařízení IoT Edge pro příjem modulu, budete přesměrováni na třech krocích průvodce, který vám umožní definovat přesně jak modulu se nasadí.

3. V **přidat moduly** kroku průvodce sada modulů, Všimněte si, že **AzureBlobStorageonIoTEdge** modul již uveden v části **moduly nasazení**. 

2. Vyberte ze seznamu modulů nasazení otevřete podrobnosti o modulu modulu služby storage blob. 

   ![Vyberte název modulu a otevřete podrobnosti o modulu](./media/how-to-store-data-blob/open-module-details.png)

3. Na **vlastní moduly IoT Edge** stránce, aktualizace Azure Blob Storage na modul IoT Edge pomocí následujících kroků:

   1. Změnit modul **název** na malá písmena. Pokud chcete, můžete přejmenovat modul nebo použít `azureblobstorageoniotedge`. 

      >[!IMPORTANT]
      >Azure IoT Edge je velká a malá písmena, při volání k modulům a sady SDK služby Storage výchozí hodnoty na malá písmena. Aby bylo zajištěno, že nedošlo k přerušení připojení k Azure Blob Storage na modul IoT Edge, pojmenujte ho malá písmena. 

   2. Výchozí hodnota **možnosti vytvoření kontejneru** vazby portu, které váš kontejner potřebuje, ale také muset přidat informace o vašem účtu úložiště a vazby k adresáři úložiště ve vašem zařízení. Přepište ve formátu JSON na portálu ve formátu JSON:
    
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
   3. Aktualizace ve formátu JSON, který jste zkopírovali s následujícími informacemi: 

      * Nahraďte `<your storage account name>` s názvem, které si zapamatujete. Názvy účtů musí být 3 do 24 znaků dlouhá a malá písmena a číslice.
      * Nahraďte `<your storage account key>` s klíčem ve formátu base64 64 bajtů. Vygenerujete nějaký klíč pomocí nástrojů jako [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Použijete tyto přihlašovací údaje pro přístup k úložišti objektů blob z jiných modulů.
      * Nahraďte `<storage directory bind>` v závislosti na váš kontejner operační systém. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cesta do adresáře na zařízení IoT Edge místo modulu objektů blob pro uložení data. Vazba adresář úložiště mapuje umístění na vašem zařízení, které poskytujete nastavení umístění v modulu. 

         * Kontejnery Linuxu:  **\<cestu úložiště >: / blobroot**. Například/srv/containerdata: / blobroot. Nebo tento svazek: / blobroot. 
         * Kontejnery Windows:  **\<cestu úložiště >: C: / BlobRoot**. Například C: / ContainerData:C: / BlobRoot. Nebo, my-svazku: C: / blobroot.
   
      > [!IMPORTANT]
      > Neměňte druhou polovinu adresáře úložiště vytvořit vazbu hodnotu, která odkazuje na konkrétní umístění v modulu. Vazba adresář úložiště by měl končit vždy **: / blobroot** pro kontejnery Linuxu a **: C: / BlobRoot** pro kontejnery Windows.

      ![Aktualizace modulu kontejneru vytvořit možnosti – portál](./media/how-to-store-data-blob/edit-module.png)

   4. Nastavte [automatické vrstvení a automatické vypršení platnosti](#configure-auto-tiering-and-auto-expiration-via-azure-portal) v požadované vlastnosti. Seznam [automatické vrstvení](#auto-tiering-properties) a [automatické vypršení platnosti](#auto-expiration-properties) vlastností a jejich možných hodnot. 

   5. Vyberte **Uložit**. 

4. Vyberte **Další** pokračujte k dalšímu kroku v průvodci.
5. V **určení tras** kroku v průvodci vyberte **Další**.
6. V **nasazení zkontrolujte** kroku v průvodci vyberte **odeslat**.
7. Po odeslání nasazení můžete vrátit **IoT Edge** stránka služby IoT hub. Vyberte zařízení IoT Edge, že jste určili při nasazení a otevřete její podrobnosti. 
8. V podrobnostech zařízení zkontrolujte, zda modulu služby storage blob jako **zadaný v nasazení** a **zařízení ohlásilo**. Může trvat několik minut pro modul na zařízení a potom hlášeny zpět do služby IoT Hub. Aktualizujte stránku, pokud chcete zobrazit aktualizovaný stav. 

### <a name="visual-studio-code-templates"></a>Šablony sady Visual Studio Code

Azure IoT Edge poskytuje šablony ve Visual Studio Code a pomohou vám vytvořit řešení edge. Tyto kroky vyžadují, abyste měli [Visual Studio Code](https://code.visualstudio.com/) na vývojovém počítači nainstalovaný a nakonfigurovaný s [nástroje Azure IoT](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-tools).

Pomocí následujícího postupu vytvořte nové řešení IoT Edge s modulem úložiště objektů blob a nakonfigurujte manifest nasazení. 

1. Vyberte **zobrazení** > **příkaz palety**. 

2. V paletu příkazů zadejte a spusťte příkaz **Azure IoT Edge: Nové řešení IoT Edge**. Vytvořte řešení podle výzev, které se zobrazí na paletě příkazů.

   | Pole | Hodnota |
   | ----- | ----- |
   | Vyberte složku | Zvolte umístění na vývojovém počítači, ve kterém VS Code vytvoří soubory řešení. |
   | Zadejte název řešení | Zadejte popisný název pro vaše řešení nebo přijměte výchozí **EdgeSolution**. |
   | Vyberte šablonu modulu | Zvolte **existujícímu modulu (úplnou bitovou kopii zadejte adresu URL)**. |
   | Zadejte název modulu | Zadejte název všechna malá pro modul, jako je třeba **službě Azure BLOB Storage**.<br><br>Je důležité používat malá písmena název pro službu Azure Blob Storage na modul IoT Edge. IoT Edge je velká a malá písmena, při odkazování na moduly a sady SDK služby Storage výchozí hodnoty na malá písmena. |
   | Použijte image Dockeru pro modul | Zadejte identifikátor URI image: **mcr.microsoft.com/azure-blob-storage:latest** |

   VS Code přebírá informace k dispozici, vytvoří řešení IoT Edge a nahraje je v novém okně. Šablona řešení vytvoří šablonu manifestu nasazení, která obsahuje bitové kopie modulu úložiště objektů blob, ale je potřeba nakonfigurovat možnosti vytvoření modulu. 

3. Otevřít **deployment.template.json** nový pracovní prostor řešení a najít **moduly** oddílu. Proveďte následující změny konfigurace:

   1. Odstranit **tempSensor** je modul, protože není nezbytné pro toto nasazení. 

   2. Zkopírujte a vložte následující kód do **CreateOptions field** pole modulu úložiště objektů blob: 

      ```json
      "Env": [
        "LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME","LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY"
      ],
      "HostConfig":{
        "Binds": ["<storage directory bind>"],
        "PortBindings":{
          "11002/tcp": [{"HostPort":"11002"}]
        }
      }
      ```

      ![Aktualizace modulu CreateOptions field - VS Code](./media/how-to-store-data-blob/create-options.png)

4. V možnosti vytvořit JSON, aktualizujte `<storage directory bind>` v závislosti na váš kontejner operační systém. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cesta do adresáře na zařízení IoT Edge místo modulu objektů blob pro uložení data. Vazba adresář úložiště mapuje umístění na vašem zařízení, které poskytujete nastavení umístění v modulu.  

   * Kontejnery Linuxu:  **\<cestu úložiště >: / blobroot**. Například/srv/containerdata: / blobroot. Nebo tento svazek: / blobroot.
   * Kontejnery Windows:  **\<cestu úložiště >: C: / BlobRoot**. Například C: / ContainerData:C: / BlobRoot. Nebo, my-svazku: C: / blobroot.
   
   > [!IMPORTANT]
   > Neměňte druhou polovinu adresáře úložiště vytvořit vazbu hodnotu, která odkazuje na konkrétní umístění v modulu. Vazba adresář úložiště by měl končit vždy **: / blobroot** pro kontejnery Linuxu a **: C: / BlobRoot** pro kontejnery Windows.

5. Konfigurace [automatické vrstvení a automatické vypršení platnosti](#configure-auto-tiering-and-auto-expiration-via-vscode). Seznam [automatické vrstvení](#auto-tiering-properties) a [automatické vypršení platnosti](#auto-expiration-properties) vlastnosti

6. Uložte soubor **deployment.template.json**.

7. Otevřít **.env** souboru ve vašem pracovním prostoru řešení. 

8. Souboru .env je nastavený na přihlašovací údaje registru kontejneru, ale není nutné, které pro bitovou kopii úložiště objektů blob od je veřejně dostupná. Místo toho nahradíte soubor dvě nové proměnné prostředí: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

9. Zadejte hodnotu pro `STORAGE_ACCOUNT_NAME`, názvy účtů by měl být 3 do 24 znaků dlouhá a malá písmena a číslice. Zadejte klíč ve formátu base64 64 bajtů pro `STORAGE_ACCOUNT_KEY`. Vygenerujete nějaký klíč pomocí nástrojů jako [GeneratePlus](https://generate.plus/en/base64?gp_base64_base[length]=64). Použijete tyto přihlašovací údaje pro přístup k úložišti objektů blob z jiných modulů. 

   Nemusíte být mezery ani uvozovky kolem hodnot, které zadáte. 

10. Uložte soubor **.env**. 

11. Klikněte pravým tlačítkem na **deployment.template.json** a vyberte **manifest nasazení IoT Edge generovat**. 

12. Visual Studio Code přebírá informace uvedené v deployment.template.json a .env a použije ho k vytvoření nového souboru manifestu nasazení. Manifest nasazení se vytvoří v novém **config** složky v pracovním prostoru řešení. Až budete mít tento soubor, můžete postupovat podle kroků v [moduly nasazení Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md) nebo [moduly nasazení Azure IoT Edge pomocí Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="auto-tiering-and-auto-expiration-properties-and-configuration"></a>Ovládání datových vrstev automaticky a vlastnosti automatické vypršení platnosti a konfigurace

Pomocí požadovaných vlastností můžete nastavit automatické vrstvení a vlastnosti automatické vypršení platnosti. Může být sadě během nasazování nebo později změnit úpravou dvojčete modulu, aniž by bylo nutné znovu nasadit. Doporučujeme zkontrolovat "Dvojčete modulu" pro `reported configuration` a `configurationValidation` k Ujistěte se, že se správně rozšíří hodnoty.

### <a name="auto-tiering-properties"></a>Ovládání datových vrstev automatické vlastnosti 
Název tohoto nastavení je `tieringSettings`

| Pole | Možné hodnoty | Vysvětlení |
| ----- | ----- | ---- |
| tieringOn | Hodnota TRUE, false | Ve výchozím nastavení nastavena na `false`, pokud chcete, chcete-li ho na hodnotu `true`|
| backlogPolicy | NewestFirst OldestFirst | Umožňuje zvolit, že pořadí, ve kterém se data zkopírují do Azure. Ve výchozím nastavení nastavena na `OldestFirst`. Pořadí je určeno čas poslední změny objektu Blob |
| remoteStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` připojovací řetězec, který vám umožní určit účet Azure Storage, do kterého chcete data uložit. Zadejte `Azure Storage Account Name`, `Azure Storage Account Key`, `End point suffix`. Přidejte příslušné EndpointSuffix Azure kde bude nahrávaných dat, se mění globální Azure, Government Azure a Microsoft Azure Stack. |
| tieredContainers | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Umožňuje zadat názvy kontejnerů, které chcete nahrát do Azure. Tento modul umožňuje zadat zdroj a cíl názvy kontejnerů. Pokud nezadáte název cílového kontejneru, se automaticky přiřadí název kontejneru jako `<IoTHubName>-<IotEdgeDeviceName>-<ModuleName>-<ContainerName>`. Můžete vytvořit šablony řetězce pro název kontejneru cílové checkout možných hodnot sloupce. <br>* %h -> název centra IoT (3 až 50 znaků). <br>* %d -> zařízení IoT Id (1 až 129 znaků). <br>* %m -> název modulu (1 až 64 znaků). <br>* %c -> velikost zdrojového kontejneru (3 až 63 znaků). <br><br>Maximální velikost název kontejneru je 63 znaků při automaticky přiřadí název cílového kontejneru Pokud velikost kontejneru je větší než 63 znaků, které budou trim každý oddíl (IoTHubName IotEdgeDeviceName, název modulu, ContainerName) maximálně 15 znaků. |

### <a name="auto-expiration-properties"></a>Automatické vypršení platnosti vlastnosti
Název tohoto nastavení je `ttlSettings`

| Pole | Možné hodnoty | Vysvětlení |
| ----- | ----- | ---- |
| ttlOn | Hodnota TRUE, false | Ve výchozím nastavení nastavena na `false`, pokud chcete, chcete-li ho na hodnotu `true`|
| timeToLiveInMinutes | `<minutes>` | Zadejte interval TTL, ZÍSKÁ během několika minut. Modul se automaticky odstraní objektů blob z místního úložiště při nevyprší platnost hodnoty TTL |

### <a name="configure-auto-tiering-and-auto-expiration-via-azure-portal"></a>Konfigurovat automatické vrstvení a automatické vypršení platnosti prostřednictvím webu Azure portal

Nastavte požadované vlastnosti, které chcete povolit automatické vrstvení a automatické vypršení platnosti, můžete nastavit tyto hodnoty:

- **Během počátečního nasazení**: Zkopírujte kód JSON ve **požadované vlastnosti dvojčete modulu sady** pole. Nakonfigurujte každou vlastnost s odpovídající hodnotou, uložit a pokračovat s nasazením.

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

  ![nastavit vlastnosti automatické vrstvení a automatické vypršení platnosti](./media/how-to-store-data-blob/iotedge_custom_module.png)

- **Po nasazení modulu prostřednictvím funkce "Dvojčete modulu Identity"**: Přejít na "Identity dvojče zařízení" tohoto modulu, zkopírujte JSON ve vlastnostech požadovaného, konfigurace jednotlivých vlastností s příslušnou hodnotu a uložte. Ve formátu Json "Dvojčete modulu Identity" Ujistěte se, že pokaždé, když přidáváte nebo aktualizujete všechny požadované vlastnosti, `reported configuration` části odráží změny a `configurationValidation` části oznámí úspěšné dokončení pro každou vlastnost.

   ```json 
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

   ```

![tiering+ttl module_identity_twin](./media/how-to-store-data-blob/module_identity_twin.png) 

### <a name="configure-auto-tiering-and-auto-expiration-via-vscode"></a>Konfigurovat automatické vrstvení a automatické vypršení prostřednictvím VSCode

- **Během počátečního nasazení**: Přidat následující JSON ve vaší deployment.template.json k definování požadované vlastnosti pro tento modul. Nakonfigurujte každou vlastnost příslušnou hodnotu a uložte ho.

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

Tady je příklad požadované vlastnosti pro tento modul: ![nastavte požadované vlastnosti pro azureblobstorageoniotedge – VS Code](./media/how-to-store-data-blob/tiering_ttl.png)

- **Po nasazení modulu prostřednictvím "Dvojče zařízení"**: [Upravit dvojče modulu](https://github.com/Microsoft/vscode-azure-iot-toolkit/wiki/Edit-Module-Twin) tohoto modulu zkopírujte JSON ve vlastnostech požadované konfigurace jednotlivých vlastností s odpovídající hodnotou a uložit. Ve formátu Json "Dvojče zařízení" Ujistěte se, že pokaždé, když přidáváte nebo aktualizujete všechny požadované vlastnosti, `reported configuration` části odráží změny a `configurationValidation` části oznámí úspěšné dokončení pro každou vlastnost.

   ```json 
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

   ```
  ## <a name="logs"></a>Logs

Postupujte podle pokynů [konfigurovat protokoly dockeru pro moduly IoT Edge](production-checklist.md#set-up-logs-and-diagnostics)

## <a name="connect-to-your-blob-storage-module"></a>Připojení k modulu úložiště objektů blob

Můžete použít název účtu a klíč účtu, že jste nakonfigurovali pro modul pro přístup k úložišti objektů blob na vašem zařízení IoT Edge. 

Zadejte zařízení IoT Edge jako koncový bod objektu blob pro jakékoli úložiště požadavků, které můžete provádět. Je možné [vytvoření připojovacího řetězce pro koncový bod explicitního úložiště](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) pomocí informací o zařízení IoT Edge a název účtu, který jste nakonfigurovali. 

1. Pro moduly, které jsou nasazené do stejné hraniční zařízení, ve kterém běží "Azure Blob Storage na hraničních zařízeních IoT", je koncový bod služby blob: `http://<module name>:11002/<account name>`. 
2. Pro moduly, které se nasazují na jiné hraniční zařízení, než hraničním zařízením, ve kterém běží "Azure Blob Storage na hraničních zařízeních IoT" a potom v závislosti na vašem nastavení koncový bod objektu blob je: `http://<device IP >:11002/<account name>` nebo `http://<IoT Edge device hostname>:11002/<account name>` nebo `http://<FQDN>:11002/<account name>`

## <a name="deploy-multiple-instances"></a>Nasazení více instancí

Pokud chcete nasadit několik instancí služby Azure Blob Storage na hraničních zařízeních IoT, musíte zadat cestu jiného úložiště a změňte HostPort spojující modulu. Moduly objektu blob úložiště vždy zpřístupňuje porty 11002 v kontejneru, ale je možné deklarovat port, který je vázán na hostiteli. 

Úpravy modulu vytváření možnost a změňte hodnotu HostPort:

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

Když se připojíte k modulům další objekt blob úložiště, změňte koncový bod tak, aby odkazoval na aktualizovaném hostiteli port. 

## <a name="try-it-out"></a>Vyzkoušet

### <a name="azure-blob-storage-quickstart-samples"></a>Ukázky rychlému startu pro úložiště objektů Blob v Azure
Dokumentace ke službě Azure Blob Storage zahrnuje šablony rychlý start, která poskytuje ukázkový kód v několika jazycích. Můžete spustit tyto ukázky otestovat tak, že změníte koncový bod služby blob tak, aby odkazovala na váš modul úložiště objektů blob Azure Blob Storage na hraničních zařízeních IoT. Uvedený postup [připojení k modulu úložiště objektů blob](#connect-to-your-blob-storage-module)

Následující šablony rychlý start použít jazyky, které jsou také podporovány pomocí IoT Edge, můžete je nasadit jako moduly IoT Edge spolu s modulu služby blob storage:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md) 

### <a name="azure-storage-explorer"></a>Azure Storage Explorer
Můžete také zkusit [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/) pro připojení k účtu místního úložiště. Jste se pokusili jsme [předchozí verze 1.5.0](https://github.com/Microsoft/AzureStorageExplorer/releases/tag/v1.5.0) Průzkumníka služby Azure.
> [!NOTE]
> Pravděpodobně dojde k chybám při provádění následujících kroků, ignorovat a aktualizovat. 

1. Stažení a instalace Průzkumníka služby Azure Storage
2. Připojení ke službě Azure Storage pomocí připojovacího řetězce
3. Zadejte připojovací řetězec: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`
4. Projděte si kroky pro připojení.
5. Vytvoření kontejneru v účtu místní úložiště
6. Spusťte nahrávání souborů jako objekty BLOB bloku.
   > [!NOTE]
   > Zrušte zaškrtnutí políčka a pak ji nahrajte jako objekty BLOB stránky. Tento modul nepodporuje objekty BLOB stránky. Zobrazí se to výzvy při nahrávání souborů, jako je ISO, VHD, vhdx nebo velké soubory.

7. Můžete připojit své účty úložiště Azure, pokud nahráváte data. Poskytuje jedno zobrazení pro účet místního úložiště a účet úložiště Azure

## <a name="supported-storage-operations"></a>Operace úložiště podporuje

Moduly úložiště objektů BLOB ve službě IoT Edge používat stejné sady SDK Azure Storage a jsou konzistentní s 2017-04-17 verzi rozhraní API služby Azure Storage pro koncové body objektu blob bloku. Pozdějších verzích jsou závislé na požadavky zákazníků.

Ne všechny operace úložiště objektů Blob v Azure jsou podporovány službou Azure Blob Storage na hraničních zařízeních IoT. V následující části jsou uvedeny podporované a nepodporované operace.

### <a name="account"></a>Účet

Podporovány: 
* Výpis kontejnerů

Nepodporovaný: 
* Získání a nastavení vlastností služby blob
* Žádost o předběžné objektů blob
* Získání statistiky služby blob
* Získejte informace o účtu

### <a name="containers"></a>Containers

Podporovány: 
* Vytvoření a odstranění kontejneru
* Získání kontejneru vlastností a metadat
* Výpis objektů blob
* Získání a nastavení ACL kontejneru
* Metadata kontejneru sady

Nepodporovaný:
* Zapůjčení kontejneru

### <a name="blobs"></a>Objekty blob

Podporovány: 
* Vložit, získání a odstranění objektu blob
* GET a set vlastnosti objektu blob
* Získání a nastavení metadata objektu blob

Nepodporovaný: 
* Zapůjčení objektu blob
* Vytvoření snímku objektu blob
* Zkopírujte a přerušení objekt blob kopírování
* Obnovit objekt blob
* Nastavení úrovně objektu blob

### <a name="block-blobs"></a>Objekty blob bloku

Podporovány: 
* Vložit blok
* Uvést a získat seznam blokovaných položek

Nepodporovaný:
* Vložit blok z adresy URL

## <a name="feedback"></a>Zpětná vazba:
Váš názor je velmi důležité, aby tento modul a jeho funkcí užitečné a snadno se používá. Sdělte nám prosím svůj názor a dejte nám vědět, jak můžeme vylepšit.

Abyste se obrátili na nás na adrese absiotfeedback@microsoft.com 

## <a name="next-steps"></a>Další postup

Další informace o [úložiště objektů Blob v Azure](../storage/blobs/storage-blobs-introduction.md)

