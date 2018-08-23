---
title: Import a export identit zařízení Azure IoT Hub | Dokumentace Microsoftu
description: Jak používat službu Azure IoT SDK k provedení operace hromadného proti registr identit pro import a export identit zařízení. Operace importu umožňují vytvářet, aktualizovat a odstraňovat identit zařízení hromadně.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: aedf2d0012f5af8ea2eb8e944f06b20c7f1a6bb8
ms.sourcegitcommit: a2ae233e20e670e2f9e6b75e83253bd301f5067c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/13/2018
ms.locfileid: "42054622"
---
# <a name="manage-your-iot-hub-device-identities-in-bulk"></a>Správa identit zařízení služby IoT Hub hromadné

Každý IoT hub obsahuje registr identit, které lze použít k vytváření prostředků na zařízení ve službě. Registr identit také umožňuje řídit přístup ke koncovým bodům přístupem k zařízení. Tento článek popisuje, jak importovat a exportovat identit zařízení hromadné do a z registr identit.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Import a export operace můžou probíhat v kontextu *úlohy* , díky kterým můžete provádět hromadné operace služby pro službu IoT hub.

**RegistryManager** třída zahrnuje **ExportDevicesAsync** a **ImportDevicesAsync** metody, které používají **úlohy** rozhraní framework. Tyto metody umožňují export, import a synchronizaci rozsahu registru identit IoT hub.

Toto téma popisuje použití **RegistryManager** třídy a **úlohy** systému a provést hromadné importy a exporty zařízení do a z registru identit služby IoT hub. Azure IoT Hub Device Provisioning Service můžete také zajistit plně automatizované, just-in-time zřizování pro jeden nebo více centra IoT bez zásahu člověka. Další informace najdete v tématu [dokumentace ke službě zřizování](/azure/iot-dps).


## <a name="what-are-jobs"></a>Jaké jsou úlohy?

Operace registru identit použijte **úlohy** systému při operaci:

* Má potenciálně dlouhou dobu spuštění ve srovnání s operace úrovně standard za běhu.

* Vrací velké množství dat pro uživatele.

Místo jediného volání rozhraní API čekání nebo blokování na výsledek operace, operace asynchronně vytvoří **úlohy** pro tuto službu IoT hub. Operace a okamžitě vrátí **JobProperties** objektu.

Následující fragment kódu jazyka C# ukazuje, jak vytvořit úlohu exportu:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Použít **RegistryManager** třídy v kódu jazyka C#, přidejte **Microsoft.Azure.Devices** do svého projektu balíček NuGet. **RegistryManager** hodina může začít **Microsoft.Azure.Devices** oboru názvů.

Můžete použít **RegistryManager** třídy k dotazování na stav **úlohy** pomocí vráceného **JobProperties** metadat. Chcete-li vytvořit instanci **RegistryManager** třídy, použijte **CreateFromConnectionString** metoda.

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Chcete-li nalezen připojovací řetězec služby IoT hub, na webu Azure Portal:

- Přejděte do svého centra IoT.

- Vyberte **zásady sdíleného přístupu**.

- Vyberte zásadu, zohledněním potřebných oprávnění.

- Vlastnost connectionstring zkopírujte z panelu na pravé straně obrazovky.

Následující fragment kódu jazyka C# ukazuje, jak dotazovat každých pět sekund. Chcete-li zobrazit, pokud úloha neskončí:

```csharp
// Wait until job is finished
while(true)
{
  exportJob = await registryManager.GetJobAsync(exportJob.JobId);
  if (exportJob.Status == JobStatus.Completed || 
      exportJob.Status == JobStatus.Failed ||
      exportJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="export-devices"></a>Export zařízení

Použití **ExportDevicesAsync** metodu exportu rozsahu registr identit IoT hub do [služby Azure Storage](../storage/index.yml) objektů blob v kontejneru pomocí [sdílený přístupový podpis](../storage/common/storage-security-guide.md#data-plane-security).

Tato metoda umožňuje vytvoření spolehlivé zálohování informací o vašich zařízeních v kontejneru objektů blob, který určujete vy.

**ExportDevicesAsync** metoda vyžaduje dva parametry:

* A *řetězec* , která obsahuje identifikátor URI kontejneru objektů blob. Tento identifikátor URI musí obsahovat token SAS, který uděluje oprávnění k zápisu do kontejneru. Tato úloha vytvoří objekt blob bloku v tomto kontejneru k ukládání dat serializovaná export zařízení. SAS token musí obsahovat tato oprávnění:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* A *logická* , která označuje, že pokud budete chtít vyloučit ověřovací klíče exportovat data. Pokud **false**, ověřovací klíče jsou součástí výstup exportu. V opačném případě jsou klíče exportovány jako **null**.

Následující fragment kódu jazyka C# ukazuje, jak spustit úlohy exportu, která obsahuje zařízení ověřovací klíče v exportu data a následně dotazovat na dokončení:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = 
  await registryManager.ExportDevicesAsync(containerSasUri, false);

// Wait until job is finished
while(true)
{
    exportJob = await registryManager.GetJobAsync(exportJob.JobId);
    if (exportJob.Status == JobStatus.Completed || 
        exportJob.Status == JobStatus.Failed ||
        exportJob.Status == JobStatus.Cancelled)
    {
    // Job has finished executing
    break;
    }

    await Task.Delay(TimeSpan.FromSeconds(5));
}
```

Úloha uloží výstup v kontejneru objektů blob v zadané jako objekt blob bloku s názvem **devices.txt**. Výstupní data se skládá z dat zařízení serializován do formátu JSON, s jedno zařízení na řádek.

Následující příklad ukazuje výstupní data:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Pokud má data dvojčete zařízení, dvojče data se exportují také ve spolu s daty zařízení. Následující příklad ukazuje tento formát. Všechna data z řádku "twinETag" až do konce se data dvojčete.

```json
{
   "id":"export-6d84f075-0",
   "eTag":"MQ==",
   "status":"enabled",
   "statusReason":"firstUpdate",
   "authentication":null,
   "twinETag":"AAAAAAAAAAI=",
   "tags":{
      "Location":"LivingRoom"
   },
   "properties":{
      "desired":{
         "Thermostat":{
            "Temperature":75.1,
            "Unit":"F"
         },
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
            "$lastUpdatedVersion":2,
            "Thermostat":{
               "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
               "$lastUpdatedVersion":2,
               "Temperature":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               },
               "Unit":{
                  "$lastUpdated":"2017-03-09T18:30:52.3167248Z",
                  "$lastUpdatedVersion":2
               }
            }
         },
         "$version":2
      },
      "reported":{
         "$metadata":{
            "$lastUpdated":"2017-03-09T18:30:51.1309437Z"
         },
         "$version":1
      }
   }
}
```

Pokud potřebujete přístup k těmto datům v kódu, můžete snadno rekonstruovat tato data s využitím **ExportImportDevice** třídy. Následující fragment kódu jazyka C# ukazuje, jak číst informace o zařízení, který jste předtím exportovali do objektu blob bloku:

```csharp
var exportedDevices = new List<ExportImportDevice>();

using (var streamReader = new StreamReader(await blob.OpenReadAsync(AccessCondition.GenerateIfExistsCondition(), null, null), Encoding.UTF8))
{
  while (streamReader.Peek() != -1)
  {
    string line = await streamReader.ReadLineAsync();
    var device = JsonConvert.DeserializeObject<ExportImportDevice>(line);
    exportedDevices.Add(device);
  }
}
```

## <a name="import-devices"></a>Importovat zařízení

**ImportDevicesAsync** metodu **RegistryManager** třída umožňuje provádět operace hromadného importu a synchronizace v registru identit IoT hub. Stejně jako **ExportDevicesAsync** metody **ImportDevicesAsync** metoda používá **úlohy** framework.

Je třeba dbát pomocí **ImportDevicesAsync** metoda protože kromě zřízení nová zařízení v registru identit, můžete taky aktualizovat a odstranit stávající zařízení.

> [!WARNING]
> Operace importu se nedá vrátit zpět. Vždy proveďte zálohu existující data pomocí **ExportDevicesAsync** metoda do jiného kontejneru objektů blob, před provedením hromadné změny do registru identity.

**ImportDevicesAsync** metoda přebírá dva parametry:

* A *řetězec* , která obsahuje identifikátor URI sady [služby Azure Storage](../storage/index.yml) kontejner objektů blob, který se použije jako *vstupní* do úlohy. Tento identifikátor URI musí obsahovat token SAS, který uděluje přístup pro čtení ke kontejneru. Tento kontejner může obsahovat objekt blob s názvem **devices.txt** , který obsahuje data serializovaná zařízení určená k importu do registru identity. Import dat musí obsahovat informace o zařízení ve stejném formátu JSON, který **ExportImportDevice** úloha používá při vytváření **devices.txt** objektů blob. SAS token musí obsahovat tato oprávnění:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* A *řetězec* , která obsahuje identifikátor URI sady [služby Azure Storage](https://azure.microsoft.com/documentation/services/storage/) kontejner objektů blob, který se použije jako *výstup* z úlohy. Tato úloha vytvoří objekt blob bloku v tomto kontejneru k ukládání jakékoli informace o chybách z dokončené import **úlohy**. SAS token musí obsahovat tato oprávnění:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Tyto dva parametry může odkazovat na stejný kontejner objektů blob. Oddělené parametry jednoduše povolit větší kontrolu nad vašimi daty jako výstupní kontejner vyžaduje další oprávnění.

Následující fragment kódu jazyka C# ukazuje, jak spustit úlohu importu:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Tato metoda slouží také k importu dat pro dvojče zařízení. Formát pro vstupní data je stejný jako formát je znázorněno **ExportDevicesAsync** oddílu. Tímto způsobem můžete znovu importovat exportovaná data. **$Metadata** je volitelný.

## <a name="import-behavior"></a>Nastavení importu

Můžete použít **ImportDevicesAsync** možností, jak provést následující operace hromadného v registru identit:

* Hromadná registrace nových zařízení
* Hromadné odstranění stávajících zařízení
* Hromadné změny stavu (povolit nebo zakázat zařízení)
* Hromadné přiřazení nové klíče pro ověřování zařízení
* Hromadné automaticky – opětovné vygenerování klíče pro ověřování zařízení
* Hromadná aktualizace dvojčete dat.

Může vykonávat jakoukoli kombinaci předchozí operace v rámci jediného **ImportDevicesAsync** volání. Můžete například zaregistrovat nová zařízení a odstranit nebo aktualizovat stávající zařízení ve stejnou dobu. Při použití společně s **ExportDevicesAsync** metodu, můžete zcela migrovat všechna vaše zařízení z jedné služby IoT hub do jiného.

Pokud soubor importu obsahuje metadata dvojčete, tato metadata přepíše existující dvojčete metadata. Pokud soubor importu neobsahuje metadata dvojčete, pak pouze `lastUpdateTime` jeho metadata aktualizují pomocí aktuálního času.

Použít nepovinný **režimem importu** vlastnost v datech serializace import pro každé zařízení k řízení import procesu zařízení. **Režimem importu** vlastnost má následující možnosti:

| režimem importu | Popis |
| --- | --- |
| **createOrUpdate** |Pokud zařízení se zadanou neexistuje **id**, je nově zaregistrovaný. <br/>Pokud už zařízení existuje, dojde k přepsání zadaná vstupní data bez ohledem na stávající informace o **ETag** hodnotu. <br> Uživatel můžete volitelně zadat dvojčete dat spolu s daty o zařízení. Etag dvojčete-li zadán, jsou zpracovávána nezávisle na sobě ze značky etag zařízení. Pokud došlo k neshodě s existující dvojčete etag, chyba zapsána do souboru protokolu. |
| **vytvoření** |Pokud zařízení se zadanou neexistuje **id**, je nově zaregistrovaný. <br/>Pokud zařízení už existuje, chyba zapsána do souboru protokolu. <br> Uživatel můžete volitelně zadat dvojčete dat spolu s daty o zařízení. Etag dvojčete-li zadán, jsou zpracovávána nezávisle na sobě ze značky etag zařízení. Pokud došlo k neshodě s existující dvojčete etag, chyba zapsána do souboru protokolu. |
| **update** |Pokud zařízení už se zadaným **id**, přepíše stávající informace o zadané vstupní data bez ohledem na **ETag** hodnotu. <br/>Pokud zařízení neexistuje, je do souboru protokolu zapíše chybu. |
| **updateIfMatchETag** |Pokud zařízení už se zadaným **id**, přepíše stávající informace o zadaný vstupní data pouze v případě, že dojde **ETag** odpovídat. <br/>Pokud zařízení neexistuje, je do souboru protokolu zapíše chybu. <br/>Pokud dojde **ETag** neshoda, zápisu chyby do souboru protokolu. |
| **createOrUpdateIfMatchETag** |Pokud zařízení se zadanou neexistuje **id**, je nově zaregistrovaný. <br/>Pokud už zařízení existuje, stávající informace o je přepsána zadaný vstupní data pouze v případě, že dojde **ETag** odpovídat. <br/>Pokud dojde **ETag** neshoda, zápisu chyby do souboru protokolu. <br> Uživatel můžete volitelně zadat dvojčete dat spolu s daty o zařízení. Etag dvojčete-li zadán, jsou zpracovávána nezávisle na sobě ze značky etag zařízení. Pokud došlo k neshodě s existující dvojčete etag, chyba zapsána do souboru protokolu. |
| **odstranění** |Pokud zařízení už se zadaným **id**, odstraní se bez ohledem na **ETag** hodnotu. <br/>Pokud zařízení neexistuje, je do souboru protokolu zapíše chybu. |
| **deleteIfMatchETag** |Pokud zařízení už se zadaným **id**, odstraní se pouze v případě, že je **ETag** shodovat. Pokud zařízení neexistuje, je do souboru protokolu zapíše chybu. <br/>Pokud dojde neshodě ETag, zápisu chyby do souboru protokolu. |

> [!NOTE]
> Pokud data serializace explicitně nedefinuje **režimem importu** příznak pro zařízení, použije se výchozí **createOrUpdate** během operace importu.

## <a name="import-devices-example--bulk-device-provisioning"></a>Importovat zařízení příklad – hromadně zřizování zařízení

Následující vzorový kód jazyka C# ukazuje, jak generovat více identit zařízení, které:

* Zahrnout ověřovací klíče.
* Informace o tomto zařízení zapisovat do objektů blob bloku.
* Importujte zařízení do registru identit.

```csharp
// Provision 1,000 more devices
var serializedDevices = new List<string>();

for (var i = 0; i < 1000; i++)
{
  // Create a new ExportImportDevice
  // CryptoKeyGenerator is in the Microsoft.Azure.Devices.Common namespace
  var deviceToAdd = new ExportImportDevice()
  {
    Id = Guid.NewGuid().ToString(),
    Status = DeviceStatus.Enabled,
    Authentication = new AuthenticationMechanism()
    {
      SymmetricKey = new SymmetricKey()
      {
        PrimaryKey = CryptoKeyGenerator.GenerateKey(32),
        SecondaryKey = CryptoKeyGenerator.GenerateKey(32)
      }
    },
    ImportMode = ImportMode.Create
  };

  // Add device to the list
  serializedDevices.Add(JsonConvert.SerializeObject(deviceToAdd));
}

// Write the list to the blob
var sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice => sb.AppendLine(serializedDevice));
await blob.DeleteIfExistsAsync();

using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Call import using the blob to add new devices
// Log information related to the job is written to the same container
// This normally takes 1 minute per 100 devices
JobProperties importJob =
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="import-devices-example--bulk-deletion"></a>Importovat zařízení příklad – hromadné odstranění

Následující příklad kódu ukazuje, jak odstranit zařízení, které jste přidali pomocí předchozí ukázce kódu:

```csharp
// Step 1: Update each device's ImportMode to be Delete
sb = new StringBuilder();
serializedDevices.ForEach(serializedDevice =>
{
  // Deserialize back to an ExportImportDevice
  var device = JsonConvert.DeserializeObject<ExportImportDevice>(serializedDevice);

  // Update property
  device.ImportMode = ImportMode.Delete;

  // Re-serialize
  sb.AppendLine(JsonConvert.SerializeObject(device));
});

// Step 2: Write the new import data back to the block blob
await blob.DeleteIfExistsAsync();
using (CloudBlobStream stream = await blob.OpenWriteAsync())
{
  byte[] bytes = Encoding.UTF8.GetBytes(sb.ToString());
  for (var i = 0; i < bytes.Length; i += 500)
  {
    int length = Math.Min(bytes.Length - i, 500);
    await stream.WriteAsync(bytes, i, length);
  }
}

// Step 3: Call import using the same blob to delete all devices
importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

// Wait until job is finished
while(true)
{
  importJob = await registryManager.GetJobAsync(importJob.JobId);
  if (importJob.Status == JobStatus.Completed || 
      importJob.Status == JobStatus.Failed ||
      importJob.Status == JobStatus.Cancelled)
  {
    // Job has finished executing
    break;
  }

  await Task.Delay(TimeSpan.FromSeconds(5));
}
```

## <a name="get-the-container-sas-uri"></a>Získat identifikátor URI SAS kontejneru

Následující příklad kódu ukazuje, jak vygenerovat [identifikátor URI SAS](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) čtení, zápisu a odstranění oprávnění pro kontejner objektů blob:

```csharp
static string GetContainerSasUri(CloudBlobContainer container)
{
  // Set the expiry time and permissions for the container.
  // In this case no start time is specified, so the
  // shared access signature becomes valid immediately.
  var sasConstraints = new SharedAccessBlobPolicy();
  sasConstraints.SharedAccessExpiryTime = DateTime.UtcNow.AddHours(24);
  sasConstraints.Permissions = 
    SharedAccessBlobPermissions.Write | 
    SharedAccessBlobPermissions.Read | 
    SharedAccessBlobPermissions.Delete;

  // Generate the shared access signature on the container,
  // setting the constraints directly on the signature.
  string sasContainerToken = container.GetSharedAccessSignature(sasConstraints);

  // Return the URI string for the container,
  // including the SAS token.
  return container.Uri + sasContainerToken;
}
```

## <a name="next-steps"></a>Další postup

V tomto článku jste zjistili, jak provádět hromadné operace registru identit ve službě IoT hub. Další informace o správě služby Azure IoT Hub na následujících odkazech:

* [Metriky služby IoT Hub](iot-hub-metrics.md)
* [Monitorování operací](iot-hub-operations-monitoring.md)

Podrobněji prozkoumat možnosti služby IoT Hub, najdete v tématech:

* [Příručka vývojáře pro IoT Hub](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení pomocí služby Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Prozkoumat pomocí IoT Hub Device Provisioning Service umožňuje plně automatizované, just-in-time zřizování, najdete v tématech: 

* [Služba Azure IoT Hub Device Provisioning](/azure/iot-dps)
