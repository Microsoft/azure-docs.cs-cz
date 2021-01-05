---
title: Ukládat objekty blob bloku na zařízení – Azure IoT Edge | Microsoft Docs
description: Seznamte se s funkcemi pro zpracování vrstev a času na živé prostředí, najdete v tématu podporované operace služby Blob Storage a připojte se k účtu úložiště objektů BLOB.
author: kgremban
ms.author: kgremban
ms.reviewer: arduppal
ms.date: 12/13/2019
ms.topic: conceptual
ms.service: iot-edge
services: iot-edge
ms.openlocfilehash: e1031df9f305015048de7f708123a51875776e1b
ms.sourcegitcommit: 6cca6698e98e61c1eea2afea681442bd306487a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/24/2020
ms.locfileid: "97760584"
---
# <a name="store-data-at-the-edge-with-azure-blob-storage-on-iot-edge"></a>Ukládání dat na hraničních zařízeních s využitím služby Azure Blob Storage ve službě IoT Edge

Azure Blob Storage v IoT Edge poskytuje [objekt blob bloku](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-block-blobs) a [připojovat řešení BLOB](/rest/api/storageservices/understanding-block-blobs--append-blobs--and-page-blobs#about-append-blobs) Storage na hranici. Modul BLOB Storage v zařízení IoT Edge se chová jako služba Azure Blob Service, s výjimkou toho, že se objekty blob ukládají místně na vaše IoT Edge zařízení. K objektům blob můžete přistupovat pomocí stejných metod sady SDK služby Azure Storage nebo volání rozhraní API objektů blob, které jste už použili pro. V tomto článku se dozvíte o konceptech souvisejících s Azure Blob Storage v kontejneru IoT Edge, na kterém běží služba blob na zařízení IoT Edge.

Tento modul je užitečný ve scénářích:

* kde je potřeba ukládat data místně, dokud je nebudete moct zpracovat nebo přenést do cloudu. Tato data můžou být videa, obrázky, finanční údaje, ústavní data nebo jiná nestrukturovaná data.
* zařízení se nachází na místě s omezeným připojením.
* Pokud chcete efektivně zpracovávat data místně, abyste získali přístup k datům s nízkou latencí, můžete tak rychle reagovat na naléhavé situace.
* Když chcete snížit náklady na šířku pásma a vyhnout se přenosu terabajtů dat do cloudu. Data můžete zpracovávat místně a odesílat do cloudu jenom zpracovaná data.

Podívejte se na video s rychlým Úvodem
> [!VIDEO https://www.youtube.com/embed/xbwgMNGB_3Y]

Tento modul se dodává s funkcemi **deviceToCloudUpload** a **deviceAutoDelete** .

**deviceToCloudUpload** je konfigurovatelná funkce. Tato funkce automaticky nahraje data z místního úložiště objektů blob do Azure s možností občasné podpory připojení k Internetu. Umožňuje:

* Zapněte nebo vypněte funkci deviceToCloudUpload.
* Vyberte pořadí, ve kterém se data zkopírují do Azure, jako je NewestFirst nebo OldestFirst.
* Zadejte účet Azure Storage, na který chcete data nahrála.
* Zadejte kontejnery, které chcete nahrát do Azure. Tento modul umožňuje zadat název zdrojového i cílového kontejneru.
* Volba možnosti Odstranit objekty blob hned po dokončení nahrávání do cloudového úložiště
* Proveďte úplné nahrání objektu BLOB (pomocí `Put Blob` operace) a nahrávání na úrovni bloku (pomocí `Put Block` `Put Block List` `Append Block` operací a).

Tento modul používá nahrávání na úrovni bloku, když se objekt BLOB skládá z bloků. Tady jsou některé z běžných scénářů:

* Vaše aplikace aktualizuje některé bloky dříve nahraného objektu blob bloku nebo připojí nové bloky k doplňovacímu objektu blob, tento modul nahraje pouze aktualizované bloky a ne celý objekt BLOB.
* Modul odesílá objekt BLOB a připojení k Internetu se ukončí, když se znovu nahrává, nahraje jenom zbývající bloky a ne celý objekt BLOB.

Pokud během nahrávání objektu BLOB dojde k neočekávanému ukončení procesu (například výpadek napájení), všechny bloky, které byly v důsledku nahrávání, se nahrají znovu, až se modul vrátí do režimu online.

**deviceAutoDelete** je konfigurovatelná funkce. Tato funkce automaticky odstraní objekty BLOB z místního úložiště po uplynutí zadané doby trvání (měřeno v minutách). Umožňuje:

* Zapněte nebo vypněte funkci deviceAutoDelete.
* Zadejte dobu v minutách (deleteAfterMinutes), po jejímž uplynutí budou objekty blob automaticky odstraněny.
* Vyberte možnost zachovat objekt BLOB při jeho nahrávání, pokud hodnota deleteAfterMinutes vyprší.

## <a name="prerequisites"></a>Požadavky

Zařízení Azure IoT Edge:

* Pomocí postupu v rychlém startu pro zařízení se systémem [Linux](quickstart-linux.md) nebo [Windows](quickstart.md)můžete použít vývojový počítač nebo virtuální počítač jako zařízení IoT Edge.

* Seznam podporovaných operačních systémů a architektur najdete v tématu [Azure IoT Edge podporované systémy](support.md#operating-systems) . Modul Azure Blob Storage on IoT Edge podporuje následující architektury:
  * Systém Windows AMD64
  * Linux AMD64
  * Linux ARM32
  * Linux ARM64 (Preview)

Cloudové prostředky:

[IoT Hub](../iot-hub/iot-hub-create-through-portal.md) úrovně Standard v Azure.

## <a name="devicetocloudupload-and-deviceautodelete-properties"></a>vlastnosti deviceToCloudUpload a deviceAutoDelete

Pomocí požadovaných vlastností modulu nastavte **deviceToCloudUploadProperties** a **deviceAutoDeleteProperties**. Požadované vlastnosti lze nastavit nebo později změnit úpravou modulu bez nutnosti opětovného nasazení. Doporučujeme, abyste zkontrolovali, jestli je modul "nevlákenný" `reported configuration` , a `configurationValidation` aby se správně rozšířily hodnoty.

### <a name="devicetoclouduploadproperties"></a>deviceToCloudUploadProperties

Název tohoto nastavení je `deviceToCloudUploadProperties` . Pokud používáte simulátor IoT Edge, nastavte hodnoty na související proměnné prostředí pro tyto vlastnosti, které najdete v části vysvětlení.

| Vlastnost | Možné hodnoty | Vysvětlení |
| ----- | ----- | ---- |
| uploadOn | true, false | Nastaveno na `false` výchozí hodnotu. Pokud chcete funkci zapnout, nastavte toto pole na `true` . <br><br> Proměnná prostředí: `deviceToCloudUploadProperties__uploadOn={false,true}` |
| uploadOrder | NewestFirst, OldestFirst | Umožňuje zvolit pořadí, ve kterém se data zkopírují do Azure. Nastaveno na `OldestFirst` výchozí hodnotu. Pořadí je určeno časem poslední změny v objektu BLOB. <br><br> Proměnná prostředí: `deviceToCloudUploadProperties__uploadOrder={NewestFirst,OldestFirst}` |
| cloudStorageConnectionString |  | `"DefaultEndpointsProtocol=https;AccountName=<your Azure Storage Account Name>;AccountKey=<your Azure Storage Account Key>;EndpointSuffix=<your end point suffix>"` je připojovací řetězec, který umožňuje určit účet úložiště, na který chcete data nahráli. Zadejte `Azure Storage Account Name` , `Azure Storage Account Key` , `End point suffix` . Přidejte odpovídající EndpointSuffix z Azure, kam se budou data nahrávat, se liší pro globální Azure, státní správu Azure a Microsoft Azure Stack. <br><br> Sem můžete zadat připojovací řetězec SAS Azure Storage. Ale po vypršení platnosti musíte tuto vlastnost aktualizovat. Oprávnění SAS můžou zahrnovat přístup pro vytváření kontejnerů a vytváření, zápis a přidávání přístupu pro objekty blob.  <br><br> Proměnná prostředí: `deviceToCloudUploadProperties__cloudStorageConnectionString=<connection string>` |
| storageContainersForUpload | `"<source container name1>": {"target": "<target container name>"}`,<br><br> `"<source container name1>": {"target": "%h-%d-%m-%c"}`, <br><br> `"<source container name1>": {"target": "%d-%c"}` | Umožňuje zadat názvy kontejnerů, které chcete nahrát do Azure. Tento modul umožňuje zadat název zdrojového i cílového kontejneru. Pokud nezadáte název cílového kontejneru, automaticky se přiřadí název kontejneru jako `<IoTHubName>-<IotEdgeDeviceID>-<ModuleName>-<SourceContainerName>` . Můžete vytvořit řetězce šablon pro název cílového kontejneru, podívejte se do sloupce možné hodnoty. <br>*% h-> IoT Hub název (3-50 znaků). <br>*% d – > IoT Edge ID zařízení (1 až 129 znaků). <br>*% m-> název modulu (1 až 64 znaků). <br>*% c-> název zdrojového kontejneru (3 až 63 znaků). <br><br>Maximální velikost názvu kontejneru je 63 znaků a při automatickém přiřazování názvu cílového kontejneru, pokud velikost kontejneru překračuje 63 znaků, se všechny oddíly (IoTHubName, IotEdgeDeviceID, Module, SourceContainerName) oříznou na 15 znaků. <br><br> Proměnná prostředí: `deviceToCloudUploadProperties__storageContainersForUpload__<sourceName>__target=<targetName>` |
| deleteAfterUpload | true, false | Nastaveno na `false` výchozí hodnotu. Pokud je nastavená na `true` , budou data po dokončení nahrávání do cloudového úložiště automaticky odstraňovat. <br><br> **Upozornění**: Pokud používáte doplňovací objekty blob, bude toto nastavení po úspěšném nahrání odstranit doplňovací objekty BLOB z místního úložiště a jakékoli budoucí operace připojení bloku do těchto objektů BLOB selžou. Toto nastavení používejte opatrně, nepovolujte tuto možnost, pokud vaše aplikace provádí zřídka připojené operace nebo nepodporuje průběžné operace připojení.<br><br> Proměnná prostředí: `deviceToCloudUploadProperties__deleteAfterUpload={false,true}` . |

### <a name="deviceautodeleteproperties"></a>deviceAutoDeleteProperties

Název tohoto nastavení je `deviceAutoDeleteProperties` . Pokud používáte simulátor IoT Edge, nastavte hodnoty na související proměnné prostředí pro tyto vlastnosti, které najdete v části vysvětlení.

| Vlastnost | Možné hodnoty | Vysvětlení |
| ----- | ----- | ---- |
| deleteOn | true, false | Nastaveno na `false` výchozí hodnotu. Pokud chcete funkci zapnout, nastavte toto pole na `true` . <br><br> Proměnná prostředí: `deviceAutoDeleteProperties__deleteOn={false,true}` |
| deleteAfterMinutes | `<minutes>` | Zadejte čas v minutách. Modul automaticky odstraní objekty BLOB z místního úložiště, jakmile vyprší platnost této hodnoty. Aktuální maximální povolený počet minut je 35791. <br><br> Proměnná prostředí: `deviceAutoDeleteProperties__ deleteAfterMinutes=<minutes>` |
| retainWhileUploading | true, false | Ve výchozím nastavení se nastaví na `true` a při vypršení platnosti deleteAfterMinutes zůstane objekt BLOB při nahrávání do cloudového úložiště. Můžete ho nastavit na `false` a tato data budou odstraněna, jakmile deleteAfterMinutes vyprší platnost. Poznámka: aby tato vlastnost fungovala uploadOn, měla by být nastavená na true.  <br><br> **Upozornění**: Pokud používáte doplňovací objekty blob, toto nastavení odstraní doplňovací objekty BLOB z místního úložiště, jakmile vyprší platnost hodnoty, a jakékoli budoucí operace připojení bloku do těchto objektů BLOB se nezdaří. Možná budete chtít zajistit, aby byla hodnota vypršení platnosti dostatečně velká pro očekávanou frekvenci operací připojení provedených vaší aplikací.<br><br> Proměnná prostředí: `deviceAutoDeleteProperties__retainWhileUploading={false,true}`|

## <a name="using-smb-share-as-your-local-storage"></a>Použití sdílené složky SMB jako místního úložiště

Pokud nasadíte kontejner Windows tohoto modulu na hostitele Windows, můžete jako cestu k místnímu úložišti zadat sdílenou složku SMB.

Ujistěte se, že sdílená složka SMB a zařízení IoT jsou ve vzájemně důvěryhodných doménách.

Můžete spustit `New-SmbGlobalMapping` příkaz prostředí PowerShell pro místní mapování sdílené složky SMB na zařízení IoT s Windows.

Níže jsou uvedené kroky konfigurace:

```PowerShell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath <remote SMB path> -Credential $creds -LocalPath <Any available drive letter>
```

Například:

```powershell
$creds = Get-Credential
New-SmbGlobalMapping -RemotePath \\contosofileserver\share1 -Credential $creds -LocalPath G:
```

Tento příkaz použije přihlašovací údaje k ověření u vzdáleného serveru SMB. Pak namapujte cestu vzdálené sdílené složky na G: písmeno jednotky (může to být jakékoli jiné dostupné písmeno jednotky). Zařízení IoT teď má datový svazek namapovaný na cestu na jednotce G:.

Zajistěte, aby uživatel v zařízení IoT mohl číst a zapisovat do vzdálené sdílené složky protokolu SMB.

Pro nasazení `<storage mount>` může být hodnota **G:/ContainerData: C:/BlobRoot**.

## <a name="granting-directory-access-to-container-user-on-linux"></a>Udělení přístupu k adresáři uživateli kontejneru v systému Linux

Pokud jste v možnostech vytváření pro kontejnery pro Linux používali [připojení svazku](https://docs.docker.com/storage/volumes/) pro úložiště, nemusíte provádět žádné další kroky, ale pokud jste použili [připojení k vazbě](https://docs.docker.com/storage/bind-mounts/) , jsou tyto kroky nezbytné ke správnému spuštění služby.

V rámci principu minimálního oprávnění pro omezení oprávnění k přístupu pro uživatele, aby nedošlo k minimálnímu množství oprávnění, které potřebují k provedení své práce, obsahuje tento modul uživatele (název: absie, ID: 11000) a skupinu uživatelů (název: absie, ID: 11000). Pokud je kontejner spuštěný jako **kořen** (výchozí uživatel je **kořenový**), bude naše služba spuštěná jako uživatel s nízkými oprávněními **absie** .

Díky tomuto chování se konfigurace oprávnění v cestě hostitele váže pro správné fungování služby, jinak služba nebude fungovat s chybami odepření přístupu. Cesta, která se používá ve vazbě adresáře, musí být přístupná uživatelem kontejneru (například: absie 11000). Uživateli kontejneru můžete udělit přístup k adresáři spuštěním příkazů níže na hostiteli:

```terminal
sudo chown -R 11000:11000 <blob-dir>
sudo chmod -R 700 <blob-dir>
```

Například:

```terminal
sudo chown -R 11000:11000 /srv/containerdata
sudo chmod -R 700 /srv/containerdata
```

Pokud potřebujete službu spustit jako jiný uživatel než **absie**, můžete v manifestu nasazení zadat vlastní ID uživatele v createOptions pod vlastností "User". V takovém případě musíte použít výchozí ID nebo ID kořenové skupiny `0` .

```json
"createOptions": {
  "User": "<custom user ID>:0"
}
```

Nyní Udělte uživateli kontejneru přístup k adresáři.

```terminal
sudo chown -R <user ID>:<group ID> <blob-dir>
sudo chmod -R 700 <blob-dir>
```

## <a name="configure-log-files"></a>Konfigurace souborů protokolu

Informace o konfiguraci souborů protokolu pro váš modul najdete v tématu věnovaném [osvědčeným postupům pro produkční](./production-checklist.md#set-up-logs-and-diagnostics)prostředí.

## <a name="connect-to-your-blob-storage-module"></a>Připojení k modulu BLOB Storage

Můžete použít název účtu a klíč účtu, který jste nakonfigurovali pro váš modul, abyste měli přístup k úložišti objektů blob na vašem zařízení IoT Edge.

Zadejte zařízení IoT Edge jako koncový bod objektu BLOB pro všechny požadavky na úložiště, které v něm provedete. [Připojovací řetězec pro explicitní koncový bod úložiště můžete vytvořit](../storage/common/storage-configure-connection-string.md#create-a-connection-string-for-an-explicit-storage-endpoint) pomocí IoT Edge informací o zařízení a názvu účtu, který jste nakonfigurovali.

* Pro moduly, které jsou nasazené na stejném zařízení, ve kterém je spuštěná služba Azure Blob Storage v IoT Edge modulu, je koncový bod objektu BLOB: `http://<module name>:11002/<account name>` .
* Pro moduly nebo aplikace běžící na jiném zařízení musíte zvolit správný koncový bod pro vaši síť. V závislosti na nastavení sítě vyberte formát koncového bodu tak, aby přenos dat z externího modulu nebo aplikace mohl kontaktovat zařízení, na kterém běží Azure Blob Storage v modulu IoT Edge. Koncový bod objektu BLOB pro tento scénář je jedním z těchto:
  * `http://<device IP >:11002/<account name>`
  * `http://<IoT Edge device hostname>:11002/<account name>`
  * `http://<fully qualified domain name>:11002/<account name>`
 
 > [!IMPORTANT]
 > Azure IoT Edge rozlišuje velká a malá písmena, když provedete volání modulů a sada SDK úložiště má výchozí hodnotu malá písmena. I když je název modulu v [Azure Marketplace](how-to-deploy-modules-portal.md#deploy-modules-from-azure-marketplace) **AzureBlobStorageonIoTEdge**, změna názvu na malá písmena vám pomůže zajistit, že se připojení k Azure Blob Storage v modulu IoT Edge nepřerušila.
 
## <a name="azure-blob-storage-quickstart-samples"></a>Ukázky pro rychlý Start pro Azure Blob Storage

Dokumentace k Azure Blob Storage obsahuje ukázkový kód pro rychlý Start v několika jazycích. Tyto ukázky můžete použít k otestování Azure Blob Storage na IoT Edge změnou koncového bodu objektu BLOB tak, aby se připojil k vašemu místnímu modulu úložiště objektů BLOB.

Následující ukázky pro rychlý Start používají jazyky, které jsou podporované také nástrojem IoT Edge, takže je můžete nasadit jako IoT Edge moduly společně s modulem úložiště objektů BLOB:

* [.NET](../storage/blobs/storage-quickstart-blobs-dotnet.md)
* [Python](../storage/blobs/storage-quickstart-blobs-python.md)
  * Verze před V 2.1 sady Python SDK mají známý problém, kdy modul nevrací čas vytvoření objektu BLOB. Kvůli tomuto problému některé metody, jako jsou objekty blob seznamu, nefungují. Jako alternativní řešení explicitně nastavte verzi rozhraní API u klienta objektů blob na hodnotu 2017-04-17. Případě  `block_blob_service._X_MS_VERSION = '2017-04-17'`
  * [Příklad připojení objektu BLOB](https://github.com/Azure/azure-storage-python/blob/master/samples/blob/append_blob_usage.py)
* [Node.js](../storage/blobs/storage-quickstart-blobs-nodejs-legacy.md)
* [JS/HTML](../storage/blobs/storage-quickstart-blobs-javascript-client-libraries-legacy.md)
* [Ruby](../storage/blobs/storage-quickstart-blobs-ruby.md)
* [Přejít](../storage/blobs/storage-quickstart-blobs-go.md)
* [PHP](../storage/blobs/storage-quickstart-blobs-php.md)

## <a name="connect-to-your-local-storage-with-azure-storage-explorer"></a>Připojte se k místnímu úložišti pomocí Průzkumník služby Azure Storage

Pomocí [Průzkumník služby Azure Storage](https://github.com/microsoft/AzureStorageExplorer/releases/tag/v1.14.2) se můžete připojit k místnímu účtu úložiště.

1. Stažení a instalace Průzkumníka služby Azure Storage

1. Připojení k Azure Storage pomocí připojovacího řetězce

1. Zadejte připojovací řetězec: `DefaultEndpointsProtocol=http;BlobEndpoint=http://<host device name>:11002/<your local account name>;AccountName=<your local account name>;AccountKey=<your local account key>;`

1. Projděte si postup, kterým se připojíte.

1. Vytvoření kontejneru v účtu místního úložiště

1. Začněte nahrávat soubory jako objekty blob bloku nebo doplňovací objekty blob.
   > [!NOTE]
   > Tento modul nepodporuje objekty blob stránky.

1. Můžete také připojit účty úložiště Azure v Průzkumník služby Storage. Tato konfigurace poskytuje jedno zobrazení pro účet místního úložiště i pro účet Azure Storage.

## <a name="supported-storage-operations"></a>Podporované operace úložiště

Moduly BLOB Storage v IoT Edge používají sady Azure Storage SDK a jsou konzistentní s verzí 2017-04-17 rozhraní API Azure Storage pro koncové body objektů blob bloku.

Vzhledem k tomu, že služba Azure Blob Storage v IoT Edge nepodporuje všechny operace Azure Blob Storage, v této části je uveden seznam jejich stavů.

### <a name="account"></a>Účet

Podporováno:

* Výpis kontejnerů

Neplatné

* Získání a nastavení vlastností služby BLOB Service
* Požadavek na předběžné v objektu BLOB
* Získat statistiky služby BLOB Service
* Získat informace o účtu

### <a name="containers"></a>Kontejnery

Podporováno:

* Vytvořit a odstranit kontejner
* Získání vlastností kontejneru a metadat
* Výpis objektů blob
* Získání a nastavení seznamu ACL kontejneru
* Nastavení metadat kontejneru

Neplatné

* Kontejner zapůjčení

### <a name="blobs"></a>Objekty blob

Podporováno:

* Vložení, získání a odstranění objektu BLOB
* Získání a nastavení vlastností objektu BLOB
* Získání a nastavení metadat objektů BLOB

Neplatné

* Objekt BLOB zapůjčení
* Objekt BLOB snímku
* Kopírování a přerušování objektu BLOB kopírování
* Obnovit objekt BLOB
* Nastavit úroveň objektu BLOB

### <a name="block-blobs"></a>Objekty blob bloku

Podporováno:

* Blok vložení
* Vložit a získat seznam blokovaných

Neplatné

* Vložit blok z adresy URL

### <a name="append-blobs"></a>Doplňovací objekty blob

Podporováno:

* Připojit blok

Neplatné

* Připojit blok z adresy URL

## <a name="event-grid-on-iot-edge-integration"></a>Event Grid při integraci IoT Edge

> [!CAUTION]
> Integrace s Event Grid v IoT Edge je ve verzi Preview.

Tento Blob Storage Azure v modulu IoT Edge nyní poskytuje integraci s Event Grid IoT Edge. Podrobné informace o této integraci najdete v [kurzu nasazení modulů, publikování událostí a ověření doručení událostí](../event-grid/edge/react-blob-storage-events-locally.md).

## <a name="release-notes"></a>Zpráva k vydání verze

Tady jsou [poznámky k verzi v Docker Hub](https://hub.docker.com/_/microsoft-azure-blob-storage) pro tento modul.

## <a name="suggestions"></a>Návrhy

Váš názor je důležitý pro to, abychom tento modul a jeho funkce byly užitečné a snadno použitelné. Sdílejte prosím svůj názor a dejte nám vědět, jak můžeme vylepšit.

Můžete nás kontaktovat na absiotfeedback@microsoft.com

## <a name="next-steps"></a>Další kroky

Přečtěte si, jak [nasadit Azure Blob Storage v IoT Edge](how-to-deploy-blob.md)

Udržujte si přehled o nejnovějších aktualizacích a oznámeních ve [službě Azure Blob Storage na blogu IoT Edge](https://aka.ms/abs-iot-blogpost)
