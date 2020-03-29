---
title: Blobů bloků úložiště na zařízeních – Azure IoT Edge | Dokumenty společnosti Microsoft
description: Pochopte funkce vrstvení a čas od času do provozu, podívejte se na podporované operace úložiště objektů blob a připojte se k účtu úložiště objektů blob.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: bea00f429f31f2be62ee6a9c00f88873c595d94c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76509814"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Ukládání dat na hraničních zařízeních s využitím služby Azure Blob Storage ve službě IoT Edge

Azure Blob Storage na IoT Edge poskytuje [objekt blob bloku](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) a připojit řešení úložiště [objektů blob](https://docs.microsoft.com/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) na hraničních zařízeních. Modul úložiště objektů blob na vašem zařízení IoT Edge se chová jako služba objektů blob Azure, s výjimkou objektů BLOB jsou uloženy místně na zařízení IoT Edge. K objektům BLOB můžete přistupovat pomocí stejných metod Azure storage SDK nebo volání rozhraní API objektů blob, na které jste už zvyklí. Tento článek vysvětluje koncepty související s azure blob storage na kontejneru IoT Edge, který spouští službu blob na vašem zařízení IoT Edge.

Tento modul je užitečný ve scénářích:

* kde musí být data uložena místně, dokud je nelze zpracovat nebo přenést do cloudu. Tato data mohou být videa, obrázky, finanční data, nemocniční data nebo jakákoli jiná nestrukturovaná data.
* pokud jsou zařízení umístěna na místě s omezenou konektivitou.
* Pokud chcete efektivně zpracovávat data místně získat přístup k datům s nízkou latencí, tak, že můžete reagovat na mimořádné události co nejrychleji.
* pokud chcete snížit náklady na šířku pásma a vyhnout se přenosu terabajtů dat do cloudu. Data můžete zpracovat místně a odesílat pouze zpracovaná data do cloudu.

Podívejte se na video pro rychlý úvod
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Tento modul je dodáván s **funkcemi deviceToCloudUpload** a **deviceAutoDelete.**

**deviceToCloudUpload** je konfigurovatelná funkce. Tato funkce automaticky nahraje data z místního úložiště objektů blob do Azure s přerušovanou podporou připojení k internetu. To vám umožní:

* Zapněte/vypněte funkci deviceToCloudUpload.
* Zvolte pořadí, ve kterém se data zkopírují do Azure jako NewestFirst nebo OldestFirst.
* Zadejte účet Azure Storage, do kterého chcete data nahrát.
* Zadejte kontejnery, které chcete nahrát do Azure. Tento modul umožňuje zadat názvy zdrojových i cílových kontejnerů.
* Zvolte možnost okamžitého odstranění objektů BLOB po dokončení nahrávání do cloudového úložiště.
* Proveďte úplné nahrávání `Put Blob` objektů blob (pomocí `Put Block`operace) `Append Block` a nahrávání na úrovni bloku (pomocí a `Put Block List` operací).

Tento modul používá nahrávání na úrovni bloku, když se objekt blob skládá z bloků. Zde jsou některé z běžných scénářů:

* Aplikace aktualizuje některé bloky dříve nahraného objektu blob bloku nebo připojí nové bloky k objektu blob připojení, tento modul nahraje pouze aktualizované bloky a ne celý objekt blob.
* Modul nahrává objekt blob a připojení k internetu zmizí, když je připojení opět zpět, nahraje pouze zbývající bloky a ne celý objekt blob.

Pokud dojde k neočekávanému ukončení procesu (jako je selhání napájení) během nahrávání objektů blob, všechny bloky, které byly splatné pro nahrávání, budou znovu nahrány, jakmile se modul vrátí do režimu online.

**deviceAutoDelete** je konfigurovatelná funkce. Tato funkce automaticky odstraní objekty BLOB z místního úložiště, když vyprší zadaná doba trvání (měřeno v minutách). To vám umožní:

* Zapněte/vypněte funkci automatického odstranění zařízení.
* Zadejte čas v minutách (deleteAfterMinutes), po kterém budou objekty BLOB automaticky odstraněny.
* Zvolte možnost zachovat objekt blob při nahrávání, pokud vyprší platnost hodnoty deleteAfterMinutes.

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Vývojový počítač nebo virtuální počítač můžete použít jako zařízení IoT Edge podle kroků v rychlém startu pro [Linux](quickstart-linux.md) nebo [zařízení s Windows](quickstart.md).

* Seznam podporovaných operačních systémů a architektur [azure IoT Edge](support.md#operating-systems) najdete v seznamu podporovaných operačních systémů a architektur. Azure Blob Storage na IoT Edge modul podporuje následující architektury:
  * Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (náhled)

Cloudové prostředky:

[IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Standard v Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>vlastnosti deviceToCloudUpload a deviceAutoDelete

Pomocí požadovaných vlastností modulu nastavte **deviceToCloudUploadProperties** a **deviceAutoDeleteProperties**. Požadované vlastnosti lze nastavit během nasazení nebo později změnit úpravou dvojčete modulu bez nutnosti opětovného nasazení. Doporučujeme zkontrolovat "Twin modulu" pro `reported configuration` a `configurationValidation` ujistěte se, že hodnoty jsou správně šířeny.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Název tohoto nastavení `deviceToCloudUploadProperties`je . Pokud používáte simulátor IoT Edge, nastavte hodnoty na související proměnné prostředí pro tyto vlastnosti, které najdete v části vysvětlení.

| Vlastnost | Možné hodnoty | Vysvětlení |
| ----- | ----- | ---- |
| uploadOn | pravda, nepravdivé | Ve `false` výchozím nastavení nastaveno na hodnotu. Chcete-li funkci zapnout, nastavte toto pole na . `true` <br><br> Proměnná prostředí:`deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NejnovějšíPrvní, NejstaršíPrvní | Umožňuje zvolit pořadí, ve kterém se data zkopírují do Azure. Ve `OldestFirst` výchozím nastavení nastaveno na hodnotu. Pořadí je určeno čas emb poslední změny. <br><br> Proměnná prostředí:`deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"`je připojovací řetězec, který umožňuje zadat účet úložiště, do kterého chcete data nahrát. `Azure Storage Account Name`Zadejte `Azure Storage Account Key` `End point suffix`, , . Přidejte příslušnou koncovku EndpointSuffix Azure, kde se budou nahrávat data, liší se u Globálního Azure, Government Azure a Microsoft Azure Stack. <br><br> Můžete zadat připojovací řetězec Azure Storage SAS zde. Ale budete muset aktualizovat tuto vlastnost, když vyprší její platnost. <br><br> Proměnná prostředí:`deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| skladováníContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Umožňuje zadat názvy kontejnerů, které chcete nahrát do Azure. Tento modul umožňuje zadat názvy zdrojových i cílových kontejnerů. Pokud nezadáte název cílového kontejneru, automaticky přiřadí `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>`název kontejneru jako . Můžete vytvořit řetězce šablon pro název cílového kontejneru, podívejte se na sloupec možných hodnot. <br>* %h -> Název centra IoT (3-50 znaků). <br>* %d -> ID zařízení IoT Edge (1 až 129 znaků). <br>* %m -> Název modulu (1 až 64 znaků). <br>* %c -> název zdrojového kontejneru (3 až 63 znaků). <br><br>Maximální velikost názvu kontejneru je 63 znaků, zatímco automatické přiřazení názvu cílového kontejneru, pokud velikost kontejneru překročí 63 znaků, ořízne každou sekci (IoTHubName, IotEdgeDeviceID, ModuleName, SourceContainerName) na 15 Znaky. <br><br> Proměnná prostředí:`deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | pravda, nepravdivé | Ve `false` výchozím nastavení nastaveno na hodnotu. Když je nastavena na `true`, automaticky odstraní data po dokončení nahrávání do cloudového úložiště. <br><br> **UPOZORNĚNÍ:** Pokud používáte připojit objekty BLOB, toto nastavení odstraní objekty BLOB z místního úložiště po úspěšném nahrání a všechny budoucí operace připojit blok na tyto objekty BLOB se nezdaří. Toto nastavení používejte opatrně, nepovolujte toto políčko, pokud aplikace provádí méně časté připojování operací nebo nepodporuje nepřetržité operace připojení<br><br> Proměnná `deviceToCloudUploadProperties__deleteAfterUpload={false,true}`prostředí: . |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Název tohoto nastavení `deviceAutoDeleteProperties`je . Pokud používáte simulátor IoT Edge, nastavte hodnoty na související proměnné prostředí pro tyto vlastnosti, které najdete v části vysvětlení.

| Vlastnost | Možné hodnoty | Vysvětlení |
| ----- | ----- | ---- |
| deleteOn | pravda, nepravdivé | Ve `false` výchozím nastavení nastaveno na hodnotu. Chcete-li funkci zapnout, nastavte toto pole na . `true` <br><br> Proměnná prostředí:`deviceAutoDeleteProperties__deleteOn={false,true}` |
| odstranitAfterMinutes | `<minutes>` | Zadejte čas v minutách. Modul automaticky odstraní objekty BLOB z místního úložiště po vypršení platnosti této hodnoty. <br><br> Proměnná prostředí:`deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | pravda, nepravdivé | Ve výchozím nastavení `true`je nastavena na , a zachová objekt blob při nahrávání do cloudového úložiště, pokud deleteAfterMinutes vyprší. Můžete jej nastavit `false` a odstraní data, jakmile vyprší platnost deleteAfterMinutes. Poznámka: Pro tuto vlastnost do práce uploadOn by měla být nastavena na hodnotu true.  <br><br> **UPOZORNĚNÍ:** Pokud používáte připojit objekty BLOB, toto nastavení odstraní objekty BLOB připojení z místního úložiště, když vyprší platnost hodnoty a všechny budoucí operace připojit blok na tyto objekty BLOB se nezdaří. Můžete chtít ujistěte se, že hodnota vypršení platnosti je dostatečně velký pro očekávané frekvence připojení operací prováděných vaší aplikací.<br><br> Proměnná prostředí:`deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Použití sdílené složky SMB jako místního úložiště

Můžete poskytnout sdílenou složku SMB jako cestu místního úložiště při nasazení kontejneru systému Windows tohoto modulu na hostiteli systému Windows.

Ujistěte se, že sdílená složky SMB a zařízení IoT jsou ve vzájemně důvěryhodných doménách.

Příkaz PowerShell můžete spustit `New-SmbGlobalMapping` k mapování sdílené položky SMB místně na zařízení IoT se systémem Windows.

Níže jsou uvedeny kroky konfigurace:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Například:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

Tento příkaz použije pověření k ověření pomocí vzdáleného serveru SMB. Potom namapujte vzdálenou cestu sdílení na písmeno jednotky G: (může být jakékoli jiné dostupné písmeno jednotky). Zařízení IoT má nyní namapovaný datový svazek na cestu na jednotce G:.

Ujistěte se, že uživatel v zařízení IoT může číst a zapisovat do vzdálené sdílené složky SMB.

Pro vaše nasazení `<storage mount>` hodnota může být **G:/ContainerData:C:/BlobRoot**.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Udělení přístupu k adresáři uživateli kontejneru na Linuxu

Pokud jste použili [připojení svazku](https://docs.docker.com/storage/volumes/) pro úložiště ve vašich možnostech vytváření pro kontejnery Linux, pak nemusíte dělat žádné další kroky, ale pokud jste použili [připojení vazby,](https://docs.docker.com/storage/bind-mounts/) pak jsou tyto kroky nutné ke správnému spuštění služby.

Podle zásady nejnižšího oprávnění omezit přístupová práva pro uživatele na minimální oprávnění, která potřebují k provedení své práce, tento modul obsahuje uživatele (jméno: absie, ID: 11000) a skupinu uživatelů (název: absie, ID: 11000). Pokud je kontejner spuštěn jako **root** (výchozí uživatel je **root**), naše služba bude spuštěna jako uživatel **absie s nízkými** oprávněními.

Toto chování umožňuje konfigurace oprávnění na hostitelské cestě váže zásadní pro službu pracovat správně, jinak služba bude selhání s přístup odepřenchyby. Cesta, která se používá ve vazbě adresáře musí být přístupné uživatele kontejneru (příklad: absie 11000). Uživateli kontejneru můžete udělit přístup k adresáři provedením níže uvedených příkazů na hostiteli:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Například:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Pokud potřebujete spustit službu jako uživatele jiné než **absie**, můžete zadat vlastní ID uživatele v createOptions pod "Uživatel" vlastnost v manifestu nasazení. V takovém případě je třeba použít výchozí `0`nebo kořenové id skupiny .

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

Nyní udělte uživateli kontejneru přístup k adresáři.

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Konfigurace souborů protokolu

Informace o konfiguraci souborů protokolu pro váš modul naleznete v těchto [doporučených postupech pro produkční prostředí](https://docs.microsoft.com/azure/iot-edge/production-checklist#set-up-logs-and-diagnostics).

## <a name="connect-to-your-blob-storage-module"></a>Připojení k modulu úložiště objektů blob

Můžete použít název účtu a klíč účtu, který jste nakonfigurovali pro váš modul pro přístup k úložišti objektů blob na zařízení IoT Edge.

Zadejte zařízení IoT Edge jako koncový bod objektu blob pro všechny požadavky na úložiště, které pro něj provedete. Můžete [vytvořit připojovací řetězec pro explicitní koncový bod úložiště](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) pomocí informací o zařízení IoT Edge a název účtu, který jste nakonfigurovali.

* Pro moduly, které jsou nasazené na stejném zařízení jako kde je spuštěna služba Azure Blob `http://<module name>:11002/<account name>`Storage na IoT Edge, koncový bod objektu blob je: .
* Pro moduly nebo aplikace spuštěné na jiném zařízení je třeba zvolit správný koncový bod pro vaši síť. V závislosti na nastavení sítě zvolte formát koncového bodu tak, aby datový provoz z vašeho externího modulu nebo aplikace mohl dosáhnout zařízení se systémem Azure Blob Storage na IoT Edge. Koncový bod objektu blob pro tento scénář je jedním z:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`

## <a name="azure-blob-storage-quickstart-samples"></a>Ukázky rychlého startu úložiště objektů blob Azure

Dokumentace k úložišti objektů blob Azure obsahuje ukázkový kód rychlého startu v několika jazycích. Tyto ukázky můžete spustit k testování azure blob storage na IoT Edge změnou koncového bodu objektu blob pro připojení k místnímu modulu úložiště objektů blob.

Následující ukázky rychlého startu používají jazyky, které podporuje i IoT Edge, takže je můžete nasadit jako moduly IoT Edge vedle modulu úložiště objektů blob:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Verze před V2.1 pythonu SDK mají známý problém, kdy modul nevrátí čas vytvoření objektu blob. Z důvodu tohoto problému některé metody, jako je seznam objektů BLOB nefunguje. Jako řešení explicitně nastavte verzi rozhraní API v klientovi objektů blob na "2017-04-17". Příklad:`block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Připojit ukázku objektu blob](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Přejít](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Připojení k místnímu úložišti pomocí Azure Storage Exploreru

[Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/) můžete použít k připojení k účtu místního úložiště.

1. Stažení a instalace Průzkumníka služby Azure Storage

1. Připojení k Úložišti Azure pomocí připojovacího řetězce

1. Zadejte připojovací řetězec:`DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Projděte si kroky pro připojení.

1. Vytvoření kontejneru uvnitř místního účtu úložiště

1. Začněte nahrávat soubory jako objekty BLOB bloku nebo Připojit objekty BLOB.
   > [!NOTE]
   > Tento modul nepodporuje objekty BLOB stránky.

1. Účty úložiště Azure můžete připojit taky v Průzkumníku úložiště. Tato konfigurace poskytuje jediné zobrazení pro váš účet místního úložiště i pro účet úložiště Azure.

## <a name="supported-storage-operations"></a>Podporované operace úložiště

Moduly úložiště objektů blob na IoT Edge používají sady Azure Storage SDK a jsou konzistentní s verzí rozhraní Azure Storage API pro koncové body bloku blob 2017-04-17.

Vzhledem k tomu, že ne všechny operace úložiště objektů blob Azure jsou podporované úložištěm objektů blob Azure na IoT Edge, tato část uvádí stav každého z nich.

### <a name="account"></a>Účet

Podporováno:

* Výpis kontejnerů

Nepodporovaný:

* Získání a nastavení vlastností služby objektu blob
* Požadavek objektu blob kontroly před výstupem
* Získání statistik služby blob service
* Získání informací o účtu

### <a name="containers"></a>Kontejnery

Podporováno:

* Vytvoření a odstranění kontejneru
* Získání vlastností a metadat kontejneru
* Výpis objektů blob
* Získat a nastavit kontejner ACL
* Nastavení metadat kontejneru

Nepodporovaný:

* Kontejner pro zapůjčení

### <a name="blobs"></a>Objekty blob

Podporováno:

* Umístit, získat a odstranit objekt blob
* Získání a nastavení vlastností objektu blob
* Získání a nastavení metadat objektu blob

Nepodporovaný:

* Objekt blob zapůjčení
* Objekt blob snímku
* Kopírování a přerušování objektu blob kopírování
* Zrušit odstraněný objekt blob
* Nastavení úrovně objektu blob

### <a name="block-blobs"></a>Objekty blob bloku

Podporováno:

* Umístit blok
* Umístit a získat seznam blokování

Nepodporovaný:

* Umístit blok z adresy URL

### <a name="append-blobs"></a>Doplňovací objekty blob

Podporováno:

* Připojit blok

Nepodporovaný:

* Připojit blok z adresy URL

## <a name="event-grid-on-iot-edge-integration"></a>Mřížka událostí na integraci ioT edge

> [!CAUTION]
> Integrace s event gridem na IoT Edge je ve verzi Preview

Tento azure blob storage na IoT Edge modul teď poskytuje integraci s Event Grid na IoT Edge. Podrobné informace o této integraci naleznete [v kurzu pro nasazení modulů, publikování událostí a ověření doručení událostí](../event-grid/edge/react-blob-storage-events-locally.md).

## <a name="release-notes"></a>Poznámky k verzi

Zde jsou [poznámky k verzi v docker hubu](https://hub.docker.com/_/microsoft-azure-blob-storage) pro tento modul

## <a name="feedback"></a>Váš názor

Vaše zpětná vazba je pro nás důležitá, aby byl tento modul a jeho funkce užitečné a snadno použitelné. Prosím, podělte se o své názory a dejte nám vědět, jak můžeme zlepšit.

Můžete nás kontaktovat na adreseabsiotfeedback@microsoft.com

## <a name="next-steps"></a>Další kroky

Zjistěte, jak [nasadit azure blob storage na IoT Edge](how-to-deploy-blob.md)

Mějte aktuální informace o nejnovějších aktualizacích a oznámeních v [blogu Azure Blob Storage on IoT Edge](https://aka.ms/abs-iot-blogpost)
