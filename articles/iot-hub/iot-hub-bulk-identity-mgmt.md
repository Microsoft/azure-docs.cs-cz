---
title: Importovat export identit zařízení Azure IoT Hub | Microsoft Docs
description: Jak používat sadu SDK služby Azure IoT k provádění hromadných operací s registrem identit k importu a exportu identit zařízení. Operace importu umožňují hromadně vytvářet, aktualizovat a odstraňovat identity zařízení.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.openlocfilehash: 3b498911999e224003710eced8e0c34989672800
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/08/2019
ms.locfileid: "73809195"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>Hromadné import a export identit zařízení IoT Hub

Každé centrum IoT má registr identit, který můžete použít k vytvoření prostředků pro jednotlivé zařízení ve službě. Registr identit také umožňuje řídit přístup k koncovým bodům orientovaným na zařízení. Tento článek popisuje, jak hromadně importovat a exportovat identity zařízení do registru identit a z něj. Pokud chcete zobrazit pracovní ukázku v C# nástroji a zjistit, jak můžete tuto schopnost využít při klonování rozbočovače do jiné oblasti, přečtěte si téma [postup klonování IoT Hub](iot-hub-how-to-clone.md).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

Operace importu a exportu se provádějí v kontextu *úloh* , které umožňují provádět operace hromadné služby ve službě IoT Hub.

Třída **RegistryManager** zahrnuje metody **ExportDevicesAsync** a **ImportDevicesAsync** , které používají architekturu **úloh** . Tyto metody umožňují exportovat, importovat a synchronizovat celý registr identit služby IoT Hub.

Toto téma popisuje použití třídy **RegistryManager** a systému **úloh** k provádění hromadných importů a exportů zařízení do registru identit služby IoT Hub a z něj. Azure IoT Hub Device Provisioning Service můžete použít také k povolení nulového dotykového zřizování pro jedno nebo více rozbočovačů IoT bez nutnosti zásahu člověka. Další informace najdete v dokumentaci ke [službě zřizování](/azure/iot-dps).


## <a name="what-are-jobs"></a>Co jsou úlohy?

Operace registru identity používají při operaci systém **úloh** :

* Má potenciálně dlouhou dobu spuštění v porovnání se standardními běhovými operacemi.

* Vrátí uživateli velké množství dat.

Místo jednoho volání rozhraní API, které čeká nebo zablokuje na výsledku operace, operace asynchronně vytvoří **úlohu** pro toto centrum IoT. Operace pak okamžitě vrátí objekt **JobProperties** .

Následující C# fragment kódu ukazuje, jak vytvořit úlohu exportu:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Pokud chcete ve svém C# kódu použít třídu RegistryManager, přidejte do projektu balíček NuGet **Microsoft. Azure. Devices** . Třída **RegistryManager** je v oboru názvů **Microsoft. Azure. Devices** .

Třídu **RegistryManager** můžete použít k dotazování stavu **úlohy** pomocí vrácených metadat **JobProperties** . Chcete-li vytvořit instanci třídy **RegistryManager** , použijte metodu **CreateFromConnectionString** .

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Pokud chcete najít připojovací řetězec pro Centrum IoT, v Azure Portal:

- Přejděte do svého centra IoT.

- Vyberte **zásady sdíleného přístupu**.

- Vyberte zásadu, která vezme v úvahu potřebná oprávnění.

- Zkopírujte ConnectionString z panelu na pravé straně obrazovky.

Následující C# fragment kódu ukazuje, jak se má dotazovat každých pět sekund, aby se zjistilo, jestli se úloha dokončila:

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

## <a name="device-importexport-job-limits"></a>Omezení úloh importu/exportu zařízení

U všech IoT Hub vrstev je současně povolena pouze 1 úloha importu nebo exportu aktivního zařízení. IoT Hub také má omezení pro počet operací úloh. Další informace najdete v tématu [kvóty a omezení IoT Hub referenčních](iot-hub-devguide-quotas-throttling.md)informací.

## <a name="export-devices"></a>Exportovat zařízení

Pomocí metody **ExportDevicesAsync** exportujte celý registr identit služby IoT hub do [Azure Storage](../storage/index.yml) kontejneru objektů BLOB pomocí [sdíleného přístupového podpisu](../storage/common/storage-security-guide.md#authorization).

Tato metoda umožňuje vytvářet spolehlivé zálohy informací o zařízení v kontejneru objektů blob, který ovládáte.

Metoda **ExportDevicesAsync** vyžaduje dva parametry:

* *Řetězec* , který obsahuje identifikátor URI kontejneru objektů BLOB. Tento identifikátor URI musí obsahovat token SAS, který uděluje přístup pro zápis do kontejneru. Úloha vytvoří objekt blob bloku v tomto kontejneru, do kterého se uloží Serializovaná data zařízení pro export. Token SAS musí zahrnovat tato oprávnění:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* *Logická hodnota* , která označuje, zda chcete vyloučit ověřovací klíče z dat exportu. Pokud je **hodnota false**, jsou ve výstupu exportu zahrnuty ověřovací klíče. V opačném případě jsou klíče exportovány jako **hodnota null**.

Následující C# fragment kódu ukazuje, jak iniciovat úlohu exportu zahrnující klíče ověřování zařízení v datech exportu a pak dotaz na dokončení:

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

Úloha ukládá svůj výstup do poskytnutého kontejneru objektů BLOB jako objekt blob bloku s názvem **Devices. txt**. Výstupní data obsahují data serializovaných zařízení JSON s jedním zařízením na jeden řádek.

Následující příklad ukazuje výstupní data:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Pokud má zařízení vlákna s dvojitou kapacitou, vyexportují se spolu s daty zařízení i tato dvojitá data. Následující příklad ukazuje tento formát. Všechna data z řádku "twinETag" až do konce jsou dvojitá data.

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

Pokud potřebujete přístup k těmto datům v kódu, můžete tato data snadno deserializovat pomocí třídy **ExportImportDevice** . Následující C# fragment kódu ukazuje, jak číst informace o zařízení, které byly dříve exportovány do objektu blob bloku:

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

Metoda **ImportDevicesAsync** ve třídě **RegistryManager** umožňuje provádět operace hromadného importu a synchronizace v registru identit služby IoT Hub. Podobně jako metoda **ExportDevicesAsync** , metoda **ImportDevicesAsync** používá architekturu **úloh** .

Pomocí metody **ImportDevicesAsync** se postarat, protože kromě zřizování nových zařízení v registru identit můžete také aktualizovat a odstranit stávající zařízení.

> [!WARNING]
> Operaci importu nelze vrátit zpět. Před provedením hromadných změn v registru identit vždy zálohujte svá stávající data pomocí metody **ExportDevicesAsync** do jiného kontejneru objektů BLOB.

Metoda **ImportDevicesAsync** přijímá dva parametry:

* *Řetězec* , který obsahuje identifikátor URI [Azure Storage](../storage/index.yml) kontejneru objektů blob, který se má použít jako *vstup* do úlohy. Tento identifikátor URI musí obsahovat token SAS, který uděluje přístup pro čtení kontejneru. Tento kontejner musí obsahovat objekt BLOB s názvem **Devices. txt** , který obsahuje Serializovaná data zařízení pro import do vašeho registru identity. Data importu musí obsahovat informace o zařízení ve stejném formátu JSON, který úloha **ExportImportDevice** používá při vytváření objektu BLOB **zařízení. txt** . Token SAS musí zahrnovat tato oprávnění:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* *Řetězec* , který obsahuje identifikátor URI [Azure Storage](https://azure.microsoft.com/documentation/services/storage/) kontejneru objektů blob, který má být použit jako *výstup* z úlohy. Úloha vytvoří objekt blob bloku v tomto kontejneru, do kterého budou uloženy informace o chybě z dokončené **úlohy**importu. Token SAS musí zahrnovat tato oprávnění:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Tyto dva parametry mohou ukazovat na stejný kontejner objektů BLOB. Samostatné parametry jednoduše umožňují větší kontrolu nad daty, protože výstupní kontejner vyžaduje další oprávnění.

Následující C# fragment kódu ukazuje, jak spustit úlohu importu:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Tato metoda se dá použít i k importu dat pro vlákna zařízení. Formát datového vstupu je stejný jako formát uvedený v oddílu **ExportDevicesAsync** . Tímto způsobem můžete exportovaná data znovu importovat. **$Metadata** je volitelná.

## <a name="import-behavior"></a>Chování při importu

Metodu **ImportDevicesAsync** můžete použít k provedení následujících hromadných operací v registru identit:

* Hromadná registrace nových zařízení
* Hromadné odstranění stávajících zařízení
* Hromadné změny stavu (povolení nebo zakázání zařízení)
* Hromadné přiřazování nových klíčů pro ověřování zařízení
* Hromadné automatické vygenerování klíčů pro ověřování zařízení
* Hromadná aktualizace dvojitých dat

V rámci jednoho volání **ImportDevicesAsync** můžete provést libovolnou kombinaci předchozích operací. Můžete například zaregistrovat nová zařízení a odstranit nebo aktualizovat existující zařízení současně. Při použití spolu s metodou **ExportDevicesAsync** můžete všechna vaše zařízení migrovat z jednoho centra IoT do jiného.

Pokud soubor importu obsahuje dvojitá metadata, tato metadata přepisují existující vlákna s dvojitými metadaty. Pokud soubor importu neobsahuje zdvojená metadata, aktualizují se pouze metadata `lastUpdateTime`, a to pomocí aktuálního času.

Použijte volitelnou vlastnost **importMode** v části Import dat serializace pro každé zařízení a řízení procesu importu pro jednotlivá zařízení. Vlastnost **importMode** má následující možnosti:

| importMode | Popis |
| --- | --- |
| **createOrUpdate** |Pokud zařízení se zadaným **ID**neexistuje, je nově zaregistrováno. <br/>Pokud zařízení už existuje, stávající informace se přepíší zadanými vstupními daty bez ohledu na hodnotu **ETag** . <br> Uživatel může volitelně zadat dvojitá data spolu s daty zařízení. Značka ETag vlákna, je-li zadána, je zpracována nezávisle na ETag zařízení. Pokud dojde k neshodě se stávající značkou ETag, zapíše se do souboru protokolu chyba. |
| **vytvoření** |Pokud zařízení se zadaným **ID**neexistuje, je nově zaregistrováno. <br/>Pokud zařízení už existuje, zapíše se do souboru protokolu chyba. <br> Uživatel může volitelně zadat dvojitá data spolu s daty zařízení. Značka ETag vlákna, je-li zadána, je zpracována nezávisle na ETag zařízení. Pokud dojde k neshodě se stávající značkou ETag, zapíše se do souboru protokolu chyba. |
| **Update** |Pokud zařízení se zadaným **ID**už existuje, existující informace se přepíší zadanými vstupními daty bez ohledu na hodnotu **ETag** . <br/>Pokud zařízení neexistuje, do souboru protokolu se zapíše chyba. |
| **updateIfMatchETag** |Pokud zařízení se zadaným **ID**už existuje, existující informace se přepíší zadanými vstupními daty jenom v případě, že se vyskytuje shoda **ETag** . <br/>Pokud zařízení neexistuje, do souboru protokolu se zapíše chyba. <br/>Pokud se **značka ETag** neshoduje, do souboru protokolu se zapíše chyba. |
| **createOrUpdateIfMatchETag** |Pokud zařízení se zadaným **ID**neexistuje, je nově zaregistrováno. <br/>Pokud zařízení už existuje, existující informace se přepíší zadanými vstupními daty jenom v případě, že se vyskytuje shoda **ETag** . <br/>Pokud se **značka ETag** neshoduje, do souboru protokolu se zapíše chyba. <br> Uživatel může volitelně zadat dvojitá data spolu s daty zařízení. Značka ETag vlákna, je-li zadána, je zpracována nezávisle na ETag zařízení. Pokud dojde k neshodě se stávající značkou ETag, zapíše se do souboru protokolu chyba. |
| **odstranění** |Pokud zařízení se zadaným **ID**už existuje, odstraní se bez ohledu na hodnotu **ETag** . <br/>Pokud zařízení neexistuje, do souboru protokolu se zapíše chyba. |
| **deleteIfMatchETag** |Pokud zařízení se zadaným **ID**už existuje, odstraní se jenom v případě, že se vyskytuje shoda **ETag** . Pokud zařízení neexistuje, do souboru protokolu se zapíše chyba. <br/>Pokud se značka ETag neshoduje, do souboru protokolu se zapíše chyba. |

> [!NOTE]
> Pokud data serializace explicitně nedefinují příznak **importMode** pro zařízení, výchozí hodnota je **createOrUpdate** během operace importu.

## <a name="import-devices-example--bulk-device-provisioning"></a>Příklad importu zařízení – Hromadná zřizování zařízení

Následující C# ukázka kódu ukazuje, jak generovat více identit zařízení, které:

* Zahrňte ověřovací klíče.
* Zapište informace o zařízení do objektu blob bloku.
* Importujte zařízení do registru identity.

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

## <a name="import-devices-example--bulk-deletion"></a>Příklad importu zařízení – hromadné odstranění

Následující ukázka kódu ukazuje, jak odstranit zařízení, která jste přidali, pomocí předchozí ukázky kódu:

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

Následující ukázka kódu ukazuje, jak vygenerovat [identifikátor URI SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) s oprávněním ke čtení, zápisu a odstranění pro kontejner objektů BLOB:

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

## <a name="next-steps"></a>Další kroky

V tomto článku jste zjistili, jak provádět hromadné operace s registrem identit ve službě IoT Hub. Mnohé z těchto operací, včetně postupu přesunutí zařízení z jednoho centra na jiný, se používají v [části Správa zařízení zaregistrovaných do služby IoT Hub v tématu Postup klonování IoT Hub](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub). 

Článek klonování obsahuje pracovní ukázku, která je k němu přidružená, která je umístěná C# v ukázkách IoT na této stránce: [ukázky C#Azure IoT pro ](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/), s projektem, který je ImportExportDevicesSample. Můžete si stáhnout ukázku a vyzkoušet ji. k dispozici jsou pokyny, [jak Naklonovat IoT Hub](iot-hub-how-to-clone.md) článek.

Další informace o správě IoT Hub Azure najdete v následujících článcích:

* [IoT Hub metriky](iot-hub-metrics.md)
* [Protokoly IoT Hub](iot-hub-monitor-resource-health.md)

Chcete-li dále prozkoumat možnosti IoT Hub, přečtěte si:

* [IoT Hub příručka pro vývojáře](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení pomocí Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Pokud chcete prozkoumat použití IoT Hub Device Provisioning Service k povolení nulového dotykového zřizování za běhu, přečtěte si téma: 

* [Služba Azure IoT Hub Device Provisioning](/azure/iot-dps)
