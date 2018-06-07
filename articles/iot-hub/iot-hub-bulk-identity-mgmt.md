---
title: Import a export identit zařízení Azure IoT Hub | Microsoft Docs
description: Jak používat sady SDK služby Azure IoT provádět hromadné operace proti registru identit pro import a export identit zařízení. Operace importu umožňují vytvářet, aktualizovat a odstraňovat identit zařízení hromadně.
author: dominicbetts
manager: timlt
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 07/03/2017
ms.author: dobett
ms.openlocfilehash: 63e7fd5807f0cf6d05d81af138d649b75024d9bb
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34634018"
---
# <a name="manage-your-iot-hub-device-identities-in-bulk"></a>Spravovat vaše identit zařízení IoT Hub hromadně

Každé centrum IoT má registru identit, které můžete použít k vytvoření prostředků na zařízení ve službě. Registr identit umožňuje řídit přístup k zařízení přístupem koncových bodů. Tento článek popisuje, jak importovat a exportovat identit zařízení hromadné do a z registru identit.

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Import a export operace proběhla v kontextu *úlohy* které umožňují provádět hromadné operace služby proti služby IoT hub.

**RegistryManager** třída zahrnuje **ExportDevicesAsync** a **ImportDevicesAsync** metody, které používají **úlohy** framework. Tyto metody umožňují exportovat, import a synchronizaci celého registru identit IoT hub.

Toto téma popisuje použití **RegistryManager** třídy a **úlohy** systému a provést hromadný import a export zařízení do a z registru identit služby IoT hub. Azure IoT Hub zařízení zřizování Service můžete také povolit nula-dotykového ovládání, v běhu zřizování na jeden nebo více centra IoT bez nutnosti lidského zásahu. Další informace najdete v tématu [zřizování dokumentace ke službě][lnk-dps].


## <a name="what-are-jobs"></a>Jaké jsou úlohy?

Operace s registrem identit pomocí **úlohy** systému při operaci:

* Má potenciálně dlouhou dobu spuštění ve srovnání s standardní spuštění operace.
* Vrátí velký objem dat pro uživatele.

Místo jednoho volání rozhraní API čekání nebo blokování na výsledek operace, operace asynchronně vytvoří **úlohy** tohoto centra IoT. Operace pak okamžitě vrátí **JobProperties** objektu.

Následující fragment kódu jazyka C# ukazuje postup vytvoření úlohy exportu:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Použít **RegistryManager** třídy v kódu jazyka C#, přidejte **Microsoft.Azure.Devices** balíček NuGet do projektu. **RegistryManager** třída je v **Microsoft.Azure.Devices** oboru názvů.

Můžete použít **RegistryManager** třída k dotazování na stav systému **úlohy** pomocí vráceného **JobProperties** metadat. Chcete-li vytvořit instanci **RegistryManager** třídy, použijte **CreateFromConnectionString** metoda:

```csharp
RegistryManager registryManager = RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Najít připojovací řetězec pro službu IoT hub v portálu Azure:

- Přejděte do svého centra IoT.
- Vyberte **zásady sdíleného přístupu**.
- Vyberte zásadu, vezme v úvahu oprávnění, která potřebujete.
- Zkopírujte connectionstring z panelu na pravé straně obrazovky.

Následující fragment kódu jazyka C# ukazuje, jak pokud chcete zobrazit, pokud úloha dokončí provádění dotazování každých pět sekund:

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

Použití **ExportDevicesAsync** metodu exportu celého IoT hub identity registru [Azure Storage](../storage/index.yml) pomocí kontejneru objektů blob [sdíleného přístupového podpisu](../storage/common/storage-security-guide.md#data-plane-security).

Tato metoda umožňuje vytvářet spolehlivá zálohy zařízení informací v kontejneru objektů blob, který ovládáte.

**ExportDevicesAsync** metoda vyžaduje dva parametry:

* A *řetězec* obsahující identifikátor URI kontejner objektů blob. Tento identifikátor URI musí obsahovat token SAS, která uděluje oprávnění k zápisu do kontejneru. Tato úloha vytvoří objekt blob bloku v tomto kontejneru pro ukládání dat zařízení serializovaných export. SAS token musí zahrnovat tato oprávnění:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

* A *boolean* určující, zda chcete vyloučit ověřovací klíče exportovat data. Pokud **false**, ověřovací klíče jsou zahrnuty ve výstupu export. Jinak, jsou klíče exportovány jako **null**.

Následující fragment kódu jazyka C# ukazuje, jak spustit úlohy exportu obsahující klíče ověřování zařízení v exportu dat a pak dotazování na dokončení:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await registryManager.ExportDevicesAsync(containerSasUri, false);

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

Úloha ukládá její výstup v kontejneru objektů blob zadané jako objekt blob bloku s názvem **devices.txt**. Výstupní data se skládá z data zařízení serializován do formátu JSON, s jedno zařízení na každý řádek.

Následující příklad ukazuje výstupní data:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Pokud zařízení obsahuje twin dat, twin data také exportovat společně s dat zařízení. Následující příklad ukazuje tento formát. Všechna data z řádku "twinETag" až do konce jsou twin data.

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

Pokud budete potřebovat přístup k těmto datům v kódu, můžete snadno rekonstruovat tato data pomocí **ExportImportDevice** třídy. Následující fragment kódu jazyka C# ukazuje, jak číst informace o zařízení, který jste předtím exportovali do objektu blob bloku:

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

## <a name="import-devices"></a>Import zařízení

**ImportDevicesAsync** metoda v **RegistryManager** vám umožňuje provádět operace hromadného importu a synchronizaci v registru identit IoT hub. Jako **ExportDevicesAsync** metody **ImportDevicesAsync** metoda používá **úlohy** framework.

Vezměte v potaz, pomocí **ImportDevicesAsync** metoda protože kromě zřizování nových zařízení v registru identit, můžete taky aktualizovat a odstranit existující zařízení.

> [!WARNING]
> Operace importu nelze vrátit zpět. Vždy zálohovat stávající data pomocí **ExportDevicesAsync** metodu pro jiný kontejner objektů blob, před provedením hromadné změny registru vaší identity.

**ImportDevicesAsync** metoda přebírá dva parametry:

* A *řetězec* identifikátor URI, který obsahuje [Azure Storage](../storage/index.yml) kontejner objektů blob, které chcete použít jako *vstupní* do úlohy. Tento identifikátor URI musí obsahovat token SAS, která uděluje přístup pro čtení ke kontejneru. Tento kontejner musí obsahovat objekt blob s názvem **devices.txt** obsahující data serializovaná zařízení určená k importu do registru identit. Import dat musí obsahovat informace o zařízení ve stejném formátu JSON, který **ExportImportDevice** úloha používá při vytváření **devices.txt** objektů blob. SAS token musí zahrnovat tato oprávnění:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```
* A *řetězec* identifikátor URI, který obsahuje [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) kontejner objektů blob, které chcete použít jako *výstup* z úlohy. Tato úloha vytvoří objekt blob bloku v tomto kontejneru ukládat všechny informace o chybě z dokončené import **úlohy**. SAS token musí zahrnovat tato oprávnění:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Dva parametry může ukazovat na stejném kontejneru objektů blob. Samostatné parametry jednoduše povolte větší kontrolu nad daty jako výstup kontejneru vyžaduje další oprávnění.

Následující fragment kódu jazyka C# ukazuje, jak zahájit úlohy importu:

```csharp
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Tato metoda slouží také k importu dat pro dvojče zařízení. Formát pro vložení dat je stejný jako formát ukazuje **ExportDevicesAsync** části. Tímto způsobem můžete znovu naimportovat exportovaná data. **$Metadata** je volitelný.

## <a name="import-behavior"></a>Import chování

Můžete použít **ImportDevicesAsync** možností, jak provést následující operace hromadného v registru identit:

* Hromadné registrace nové zařízení
* Hromadné odstranění stávajících zařízení
* Hromadné změny stavu (povolit nebo zakázat zařízení)
* Hromadné přiřazení nových klíčů ověřování zařízení
* Hromadné auto-nové vygenerování klíče pro ověřování zařízení
* Hromadné aktualizace dat twin

Může vykonávat jakoukoli kombinaci předchozí operace v rámci jednoho **ImportDevicesAsync** volání. Můžete například zaregistrovat nové zařízení a odstranění nebo aktualizaci existujících zařízení pracujících s ve stejnou dobu. Při použití spolu s **ExportDevicesAsync** metody úplně můžete migrovat všechna vaše zařízení z jedné služby IoT hub do jiného.

Pokud soubor importu obsahuje twin metadata, tato metadata přepíše existující twin metadata. Pokud soubor importu neobsahuje twin metadata, pak pouze `lastUpdateTime` metadat je aktualizovat pomocí aktuálního času.

Použít nepovinný **režimem importu** vlastnost importovat data serializace pro každé zařízení k řízení import proces za zařízení. **Režimem importu** vlastnost obsahuje následující možnosti:

| režimem importu | Popis |
| --- | --- |
| **createOrUpdate** |Pokud zařízení se zadaným neexistuje **id**, bude nově zaregistrovaný. <br/>Pokud už zařízení existuje, dojde k přepsání zadaný vstupní data bez ohledem na stávající informace o **značka ETag** hodnotu. <br> Uživatel Volitelně můžete zadat data twin spolu s daty zařízení. Značka etag twin,-li zadána, je zpracovat nezávisle ze zařízení etag. Pokud se neshodují s existující twin etag, do souboru protokolu se zapíše chyba. |
| **vytvoření** |Pokud zařízení se zadaným neexistuje **id**, bude nově zaregistrovaný. <br/>Pokud už zařízení existuje, je zapíše chybu do souboru protokolu. <br> Uživatel Volitelně můžete zadat data twin spolu s daty zařízení. Značka etag twin,-li zadána, je zpracovat nezávisle ze zařízení etag. Pokud se neshodují s existující twin etag, do souboru protokolu se zapíše chyba. |
| **update** |Pokud zařízení už se zadaným **id**, přepíše stávající informace o zadané vstupní data bez ohledem na **značka ETag** hodnotu. <br/>Pokud zařízení neexistuje, je zapíše chybu do souboru protokolu. |
| **updateIfMatchETag** |Pokud zařízení už se zadaným **id**, přepíše stávající informace o zadané vstupní data jenom v případě, že je **značka ETag** shodovat. <br/>Pokud zařízení neexistuje, je zapíše chybu do souboru protokolu. <br/>Pokud dojde **značka ETag** neshoda, do souboru protokolu se zapíše chyba. |
| **createOrUpdateIfMatchETag** |Pokud zařízení se zadaným neexistuje **id**, bude nově zaregistrovaný. <br/>Pokud už zařízení existuje, stávající informace o je přepsána zadaný vstupní data jenom v případě, že je **značka ETag** shodovat. <br/>Pokud dojde **značka ETag** neshoda, do souboru protokolu se zapíše chyba. <br> Uživatel Volitelně můžete zadat data twin spolu s daty zařízení. Značka etag twin,-li zadána, je zpracovat nezávisle ze zařízení etag. Pokud se neshodují s existující twin etag, do souboru protokolu se zapíše chyba. |
| **odstranění** |Pokud zařízení už se zadaným **id**, odstraní se bez ohledem na **značka ETag** hodnotu. <br/>Pokud zařízení neexistuje, je zapíše chybu do souboru protokolu. |
| **deleteIfMatchETag** |Pokud zařízení už se zadaným **id**, odstraní se pouze v případě, že dojde **značka ETag** shodovat. Pokud zařízení neexistuje, je zapíše chybu do souboru protokolu. <br/>Pokud dojde neshoda značek ETag, do souboru protokolu se zapíše chyba. |

> [!NOTE]
> Pokud data serializace nejsou explicitně definovány **režimem importu** příznak pro zařízení, nastaví se jako výchozí **createOrUpdate** během operace importu.

## <a name="import-devices-example--bulk-device-provisioning"></a>Import příklad zařízení – hromadné zřizování zařízení

Následující ukázka kódu C# ukazuje, jak vygenerovat víc identit zařízení který:

* Zahrnout ověřovací klíče.
* Informace o tomto zařízení zapisovat do objektů blob bloku.
* Importujte do registru identit zařízení.

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
JobProperties importJob = await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);

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

## <a name="import-devices-example--bulk-deletion"></a>Import zařízení příklad – hromadné odstranění

Následující příklad kódu ukazuje, jak odstranit zařízení, které jste přidali v předchozím příkladu kódu pomocí:

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

## <a name="get-the-container-sas-uri"></a>Získat identifikátor URI pro SAS kontejneru

Následující příklad kódu ukazuje, jak vygenerovat [identifikátor URI pro SAS](../storage/blobs/storage-dotnet-shared-access-signature-part-2.md) čtení, zápisu a odstranění oprávnění pro kontejner objektů blob:

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

V tomto článku jste zjistili, jak provést hromadné operace proti registru identit služby IoT hub. Další informace o správě Azure IoT Hub na následujících odkazech:

* [Metriky služby IoT Hub][lnk-metrics]
* [Monitorování operací][lnk-monitor]

Pokud chcete prozkoumat další možnosti IoT Hub, najdete v části:

* [Příručka vývojáře pro službu IoT Hub][lnk-devguide]
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge][lnk-iotedge]

Prozkoumat pomocí službu zřizování zařízení IoT Hub povolit zajišťování nula touch, za běhu, najdete v článku: 

* [Zařízení Azure IoT Hub zřizování služby][lnk-dps]


[lnk-metrics]: iot-hub-metrics.md
[lnk-monitor]: iot-hub-operations-monitoring.md

[lnk-devguide]: iot-hub-devguide.md
[lnk-iotedge]: ../iot-edge/tutorial-simulate-device-linux.md
[lnk-dps]: https://azure.microsoft.com/documentation/services/iot-dps
