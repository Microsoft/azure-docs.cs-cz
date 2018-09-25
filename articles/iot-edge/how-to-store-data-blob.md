---
title: Azure Blob Storage na zařízení Azure IoT Edge | Dokumentace Microsoftu
description: Modul služby Azure Blob Storage nasadíte do zařízení IoT Edge k ukládání dat na hraničních zařízeních.
author: kgremban
manager: timlt
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 09/20/2018
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: b9e48eba4b46f024b056fe53b3b3df24feec802e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46995665"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge-preview"></a>Store dat na hraničních zařízeních s Azure Blob Storage na hraničních zařízeních IoT (preview)

Azure Blog Storage na hraničních zařízeních IoT poskytuje řešení úložiště objektů blob bloku na hraničních zařízeních. Modul úložiště objektů blob na vašem zařízení IoT Edge se chová jako služby objektů blob v Azure, ale objekty BLOB bloku se ukládají místně na vašem zařízení IoT Edge. Získat přístup pomocí stejných metod, sady SDK služby Azure storage BLOB nebo blokovat volání rozhraní API objektů blob, které jste už zvyklí. 

Tento článek obsahuje pokyny pro nasazení služby Azure Blob Storage na hraničních zařízeních IoT kontejneru, na kterém běží služba objektů blob na vašem zařízení IoT Edge. 

>[!NOTE]
>Azure Blob Storage na hraničních zařízeních IoT je v [ve verzi public preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/). 

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

Azure Blob Storage na hraničních zařízeních IoT poskytuje tři Image standardní kontejner, dvě pro kontejnery Linuxu (architektury AMD64 a ARM32) a jeden pro kontejnery Windows (AMD64). Použijete-li jednu z těchto imagí modulu nasazení úložiště objektů blob do zařízení IoT Edge, poskytují tři údaje nakonfigurovat instanci modulu pro vaše zařízení:

* **Název účtu** a **klíč účtu**. Zůstat konzistentní s Azure Storage, blob storage moduly použití názvy účtů a klíčů účtu ke správě přístupu. Názvy účtů musí být 3 do 24 znaků dlouhá a malá písmena a číslice. Klíče účtu musí být v kódování base64 a délku 64 bajtů. Vygenerujete nějaký klíč pomocí nástrojů jako [GeneratePlus](https://generate.plus/en/base64).
* A **možnost místní úložiště**. Objekty BLOB modulu služby blob storage ukládá místně na zařízení IoT Edge, tak, aby objekty BLOB zachovat, pokud modul zastavení nebo restartování. Deklarovat existující [svazku](https://docs.docker.com/storage/volumes
) nebo cestu k místní složce kde by měly být uložené objekty BLOB ve vašem zařízení. 

Existuje několik způsobů, jak nasadit moduly IoT Edge zařízení a všechny z nich pracovat pro službu Azure Blob Storage na moduly IoT Edge. Tyto dvě metody nejjednodušší jsou pomocí webu Azure portal nebo šablony Visual Studio Code. 

### <a name="azure-portal"></a>portál Azure

Pokud chcete nasadit úložiště objektů blob pomocí webu Azure portal, postupujte podle kroků v [moduly nasazení Azure IoT Edge z portálu Azure portal](how-to-deploy-modules-portal.md). Před vytvoření přejít na nasazení modulu, zkopírujte identifikátor URI image a příprava kontejneru možnosti podle operačního systému vašeho kontejneru. Použijte tyto hodnoty **konfigurace manifestu nasazení** článku nasazení. 

Zadejte identifikátor URI image pro modul úložiště objektů blob: **mcr.microsoft.com/azure-blob-storage**. 
   
Pomocí následující šablony JSON pro **možnosti vytvoření kontejneru** pole. Konfigurace ve formátu JSON pomocí názvu účtu úložiště, klíč účtu úložiště a úložiště directory vazby.  
   
   ```json
   {
       "Env":[
           "LOCAL_STORAGE_ACCOUNT_NAME=<your storage account name>",
           "LOCAL_STORAGE_ACCOUNT_KEY=<your storage account key>"
       ],
       "HostConfig":[
           "Binds":[
               "<storage directory bind>"
           ],
           "PortBindings":{
               "11002/tcp":[{"HostPort":"11002"}]
           }
       ]
   }
   ```   
   
V možnosti vytvořit JSON, aktualizujte `\<your storage account name\>` s názvem. Aktualizace `\<your storage account key\>` s klíčem ve formátu base64 64 bajtů. Vygenerujete nějaký klíč pomocí nástrojů jako [GeneratePlus](https://generate.plus/en/base64) který vám umožní vybrat délka v bajtech. Použijete tyto přihlašovací údaje pro přístup k úložišti objektů blob z jiných modulů.

V možnosti vytvořit JSON, aktualizujte `<storage directory bind>` v závislosti na váš kontejner operační systém. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cesta do adresáře na zařízení IoT Edge místo modulu objektů blob pro uložení data.  

   * Kontejnery Linuxu:  **\<cestu úložiště >: / blobroot**. Například/srv/containerdata: / blobroot. Nebo tento svazek: / blobroot. 
   * Kontejnery Windows:  **\<cestu úložiště >: C: / BlobRoot**. Například C: / ContainerData:C: / BlobRoot. Nebo, my-svazku: C: / blobroot. 


Není potřeba zadávat přihlašovací údaje registru pro přístup k úložišti objektů Blob v Azure na hraničních zařízeních IoT a není nutné deklarovat všechny trasy pro vaše nasazení. 

### <a name="visual-studio-code-templates"></a>Šablony sady Visual Studio Code

Azure IoT Edge poskytuje šablony ve Visual Studio Code a pomohou vám vytvořit řešení edge. Tyto kroky vyžadují, abyste měli [Visual Studio Code](https://code.visualstudio.com/) na vývojovém počítači nainstalovaný a nakonfigurovaný s [rozšíření Azure IoT Edge](https://marketplace.visualstudio.com/items?itemName=vsciot-vscode.azure-iot-edge).

Pomocí následujícího postupu vytvořte nové řešení IoT Edge s modulem úložiště objektů blob a nakonfigurujte manifest nasazení. 

1. Vyberte **zobrazení** > **příkaz palety**. 

2. V paletu příkazů zadejte a spusťte příkaz **Azure IoT Edge: nové řešení IoT Edge**. 

3. Postupujte podle výzev a vytvořte nové řešení: 

   1. **Vyberte složku** – přejděte do složky, ve kterém chcete vytvořit nové řešení.  
   
   2. **Zadejte název řešení** – zadejte název pro vaše řešení, nebo přijměte výchozí nastavení.
   
   3. **Vyberte modul šablony** – zvolte **existujícímu modulu (úplnou bitovou kopii zadejte adresu URL)**.
   
   4. **Zadejte název modulu** -zapamatovatelný název pro modul, jako je třeba zadat **službě Azure BLOB Storage**.
   
   5. **Použijte image Dockeru pro modul** – zadejte identifikátor URI image: **mcr.microsoft.com/azure-blob-storage**

VS Code přebírá informace k dispozici, vytvoří řešení IoT Edge a nahraje je v novém okně. 

Šablona řešení vytvoří šablonu manifestu nasazení, která obsahuje bitové kopie modulu úložiště objektů blob, ale je potřeba nakonfigurovat možnosti vytvoření modulu. 

1. Otevřít **deployment.template.json** nový pracovní prostor řešení a najít **moduly** oddílu. 

2. Odstranit **tempSensor** je modul, protože není nezbytné pro toto nasazení. 

3. Zkopírujte a vložte následující kód do **CreateOptions field** pole modulu úložiště objektů blob: 

   ```json
   {\"Env\": [\"LOCAL_STORAGE_ACCOUNT_NAME=$STORAGE_ACCOUNT_NAME\",\" LOCAL_STORAGE_ACCOUNT_KEY=$STORAGE_ACCOUNT_KEY\"],\"HostConfig\": {\"Binds\": [\"<storage directory bind>\"],\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"11002\"}]}}}
   ```

   ![Možnosti vytvoření aktualizace modulu](./media/how-to-store-data-blob/create-options.png)

4. V možnosti vytvořit JSON, aktualizujte `<storage directory bind>` v závislosti na váš kontejner operační systém. Zadejte název [svazku](https://docs.docker.com/storage/volumes/) nebo absolutní cesta do adresáře na zařízení IoT Edge místo modulu objektů blob pro uložení data.  

   * Kontejnery Linuxu:  **\<cestu úložiště >: / blobroot**. Například/srv/containerdata: / blobroot. Nebo tento svazek: / blobroot.
   * Kontejnery Windows:  **\<cestu úložiště >: C: / BlobRoot**. Například C: / ContainerData:C: / BlobRoot. Nebo, my-svazku: C: / blobroot.

5. Uložit **deployment.template.json**.

6. Otevřít **.env** ve vašem pracovním prostoru řešení. 

7. Není nutné zadávat žádné hodnoty registru kontejneru pro bitovou kopii úložiště objektů blob vzhledem k tomu, že je veřejně dostupná. Místo toho přidejte dva nové proměnné prostředí: 

   ```env
   STORAGE_ACCOUNT_NAME=
   STORAGE_ACCOUNT_KEY=
   ```

8. Zadejte libovolný název pro název účtu úložiště a zadat klíč ve formátu base64 64 bajtů pro klíč účtu úložiště. Vygenerujete nějaký klíč pomocí nástrojů jako [GeneratePlus](https://generate.plus/en/base64). Použijete tyto přihlašovací údaje pro přístup k úložišti objektů blob z jiných modulů. 

9. Uložit **.env**. 

10. Klikněte pravým tlačítkem na **deployment.template.json** a vyberte **manifest nasazení IoT Edge generovat**. 

Visual Studio Code přebírá informace uvedené v deployment.template.json a .env a použije ho k vytvoření nového souboru manifestu nasazení. Manifest nasazení se vytvoří v novém **config** složky v pracovním prostoru řešení. Až budete mít tento soubor, můžete postupovat podle kroků v [moduly nasazení Azure IoT Edge z Visual Studio Code](how-to-deploy-modules-vscode.md) nebo [moduly nasazení Azure IoT Edge pomocí Azure CLI 2.0](how-to-deploy-modules-cli.md).

## <a name="connect-to-your-blob-storage-module"></a>Připojení k modulu úložiště objektů blob

Můžete použít název účtu a klíč účtu, že jste nakonfigurovali pro modul pro přístup k úložišti objektů blob na vašem zařízení IoT Edge. 

Zadejte zařízení IoT Edge jako koncový bod objektu blob pro jakékoli úložiště požadavků, které můžete provádět. Je možné [vytvoření připojovacího řetězce pro koncový bod explicitního úložiště](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) pomocí informací o zařízení IoT Edge a název účtu, který jste nakonfigurovali. 

Koncový bod objektu blob Azure Blob Storage na hraničních zařízeních IoT je `http://<IoT Edge device hostname>:11002/<account name>`. 

## <a name="deploy-multiple-instances"></a>Nasazení více instancí

Pokud budete chtít nasadit víc instancí služby Azure Blob Storage na hraničních zařízeních IoT, stačí změnit HostPort spojující modulu. Moduly objektu blob úložiště vždy zpřístupňuje porty 11002 v kontejneru, ale je možné deklarovat port, který je vázán na hostiteli. 

Úpravy modulu vytváření možnost a změňte hodnotu HostPort:

```json
\"PortBindings\": {\"11002/tcp\": [{\"HostPort\":\"<port number>\"}]}
```

Když se připojíte k modulům další objekt blob úložiště, změňte koncový bod tak, aby odkazoval na aktualizovaném hostiteli port. 

### <a name="try-it-out"></a>Vyzkoušet

Dokumentace ke službě Azure Blob Storage zahrnuje šablony rychlý start, která poskytuje ukázkový kód v několika jazycích. Můžete spustit tyto ukázky otestovat tak, že změníte koncový bod služby blob tak, aby odkazovala na váš modul úložiště objektů blob Azure Blob Storage na hraničních zařízeních IoT.

Následující šablony rychlý start použít jazyky, které jsou také podporovány pomocí IoT Edge, můžete je nasadit jako moduly IoT Edge spolu s modulu služby blob storage:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Java](../storage/blobs/storage-quickstart-blobs-java.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs.md)

## <a name="supported-storage-operations"></a>Operace úložiště podporuje

Moduly úložiště objektů BLOB ve službě IoT Edge používat stejné sady SDK Azure Storage a jsou konzistentní s 2018-03-28 verzi rozhraní API služby Azure Storage pro koncové body objektu blob bloku. Pozdějších verzích jsou závislé na požadavky zákazníků. 

Ne všechny operace úložiště objektů Blob v Azure jsou podporovány službou Azure Blob Storage na hraničních zařízeních IoT. Následující části podrobně popisují operace, které nejsou jsou podporovány. 

### <a name="account"></a>Účet

Podporovány: 
* Seznam kontejnerů

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

Nepodporovaný: 
* Získání a nastavení ACL kontejneru
* Zapůjčení kontejneru
* Metadata kontejneru sady

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

## <a name="next-steps"></a>Další postup

Další informace o [úložiště objektů Blob v Azure](../storage/blobs/storage-blobs-introduction.md)

