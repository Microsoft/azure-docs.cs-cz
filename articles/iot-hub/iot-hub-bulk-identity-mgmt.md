---
title: Import a export identit zařízení Služby Azure IoT Hub | Dokumenty společnosti Microsoft
description: Jak pomocí sady Azure IoT service SDK spouštět hromadné operace proti registru identit k importu a exportu identit zařízení. Operace importu umožňují vytvářet, aktualizovat a odstraňovat identity zařízení hromadně.
author: robinsh
manager: philmea
ms.service: iot-hub
services: iot-hub
ms.topic: conceptual
ms.date: 10/02/2019
ms.author: robinsh
ms.openlocfilehash: 2a0394e6e7c17e0a4954bbdddb1d5b2811959746
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79371575"
---
# <a name="import-and-export-iot-hub-device-identities-in-bulk"></a>Hromadný import a export identit zařízení služby IoT Hub

Každé centrum IoT má registr identit, který můžete použít k vytvoření prostředků pro jednotlivé zařízení ve službě. Registr identit také umožňuje řídit přístup ke koncovým bodům směřujícím k zařízení. Tento článek popisuje, jak importovat a exportovat identity zařízení hromadně do a z registru identit. Chcete-li zobrazit pracovní ukázku v jazyce C# a zjistěte, jak můžete tuto funkci použít při klonování rozbočovače do jiné oblasti, přečtěte si téma [Jak klonovat centrum IoT Hub](iot-hub-how-to-clone.md).

> [!NOTE]
> IoT Hub nedávno přidal podporu virtuální sítě v omezeném počtu oblastí. Tato funkce zabezpečuje operace importu a exportu a eliminuje potřebu předávat klíče pro ověřování.  Zpočátku je podpora virtuálnísítě dostupná pouze v těchto oblastech: *WestUS2*, *EastUS*a *SouthCentralUS*. Další informace o podpoře virtuálních sítí a volání rozhraní API k jeho implementaci najdete v [tématu Podpora služby IoT Hub pro virtuální sítě](virtual-network-support.md).

Operace importu a exportu probíhají v kontextu *úloh,* které umožňují provádět operace hromadné služby proti centru IoT.

Třída **RegistryManager** zahrnuje metody **ExportDevicesAsync** a **ImportDevicesAsync,** které používají rozhraní **Job** Framework. Tyto metody umožňují exportovat, importovat a synchronizovat celý registr identit centra IoT.

Toto téma popisuje použití **Třídy RegistryManager** **a** job systému k provádění hromadného importu a exportu zařízení do a z registru identit služby IoT hub. Můžete také použít službu Azure IoT Hub Deviceprovisioning Service k povolení zero-touch, just-in-time zřizování do jednoho nebo více center IoT bez nutnosti lidského zásahu. Další informace naleznete v [dokumentaci ke službě zřizování](/azure/iot-dps).

[!INCLUDE [iot-hub-basic](../../includes/iot-hub-basic-whole.md)]

## <a name="what-are-jobs"></a>Co jsou práce?

Operace registru identity používají systém **úloh,** pokud operace:

* Má potenciálně dlouhou dobu provádění ve srovnání se standardními operacemi za běhu.

* Vrátí uživateli velké množství dat.

Místo jednoho volání rozhraní API čekání nebo blokování na výsledek operace operace asynchronně vytvoří **job** pro tento ioT hub. Operace pak okamžitě vrátí **JobProperties** objektu.

Následující fragment kódu Jazyka C# ukazuje, jak vytvořit úlohu exportu:

```csharp
// Call an export job on the IoT Hub to retrieve all devices
JobProperties exportJob = await 
  registryManager.ExportDevicesAsync(containerSasUri, false);
```

> [!NOTE]
> Chcete-li použít třídu **RegistryManager** v kódu Jazyka C#, přidejte do projektu balíček **Microsoft.Azure.Devices** NuGet. Třída **RegistryManager** je v oboru názvů **Microsoft.Azure.Devices.**

Třídu **RegistryManager** můžete použít k dotazování stavu **úlohy** pomocí vrácených metadat **JobProperties.** Chcete-li vytvořit instanci třídy **RegistryManager,** použijte metodu **CreateFromConnectionString.**

```csharp
RegistryManager registryManager =
  RegistryManager.CreateFromConnectionString("{your IoT Hub connection string}");
```

Pokud chcete najít připojovací řetězec pro vaše centrum IoT, na webu Azure Portal:

- Přejděte do svého centra IoT.

- Vyberte **zásady sdíleného přístupu**.

- Vyberte zásadu s ohledem na potřebná oprávnění.

- Zkopírujte připojovací řetězec z panelu na pravé straně obrazovky.

Následující fragment kódu jazyka C# ukazuje, jak každých pět sekund dotazování, abyste zjistili, zda byla úloha dokončena při provádění:

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

> [!NOTE]
> Pokud váš účet úložiště má konfigurace brány firewall, které omezují připojení služby IoT Hub, zvažte použití [výjimky microsoftu důvěryhodné první strany](./virtual-network-support.md#egress-connectivity-to-storage-account-endpoints-for-routing) (k dispozici ve vybraných oblastech pro centra IoT hubs identitou spravované služby).


## <a name="device-importexport-job-limits"></a>Limity úloh importu a exportu zařízení

Pro všechny úrovně služby IoT Hub je povolena pouze 1 aktivní úloha importu nebo exportu zařízení. IoT Hub má také limity pro provoz úloh. Další informace najdete [v tématu Reference – Kvóty ioT Hubu a omezení](iot-hub-devguide-quotas-throttling.md).

## <a name="export-devices"></a>Exportzařízení

Pomocí metody **ExportDevicesAsync** exportujte celý registr identit centra IoT hub do kontejneru objektů blob služby Azure Storage pomocí sdíleného přístupového podpisu (SAS). Další informace o sdílených přístupových podpisech najdete v tématu [Udělení omezeného přístupu k prostředkům úložiště Azure pomocí sdílených přístupových podpisů (SAS).](../storage/common/storage-sas-overview.md)

Tato metoda umožňuje vytvářet spolehlivé zálohy informací o zařízení v kontejneru objektů blob, který řídíte.

Metoda **ExportDevicesAsync** vyžaduje dva parametry:

* *Řetězec,* který obsahuje identifikátor URI kontejneru objektů blob. Tento identifikátor URI musí obsahovat token SAS, který uděluje přístup pro zápis do kontejneru. Úloha vytvoří objekt blob bloku v tomto kontejneru pro uložení serializovaných dat exportu zařízení. Token SAS musí obsahovat tato oprávnění:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

* *Logická hodnota,* která označuje, zda chcete vyloučit ověřovací klíče z exportních dat. Pokud **false**, ověřovací klíče jsou zahrnuty do výstupu exportu. V opačném případě jsou klíče exportovány jako **null**.

Následující fragment kódu jazyka C# ukazuje, jak iniciovat úlohu exportu, která zahrnuje ověřovací klíče zařízení v exportních datech a následné dotazování na dokončení:

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

Úloha ukládá svůj výstup do zadaný kontejner objektů blob jako objekt blob bloku s názvem **devices.txt**. Výstupní data se skládají z serializovaných dat zařízení JSON s jedním zařízením na řádek.

Následující příklad ukazuje výstupní data:

```json
{"id":"Device1","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device2","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device3","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device4","eTag":"MA==","status":"disabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
{"id":"Device5","eTag":"MA==","status":"enabled","authentication":{"symmetricKey":{"primaryKey":"abc=","secondaryKey":"def="}}}
```

Pokud má zařízení data dvojčete, data dvojčete se také exportují společně s daty zařízení. Následující příklad ukazuje tento formát. Všechna data z řádku "twinETag" až do konce jsou data dvojčat.

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

Pokud potřebujete přístup k těmto datům v kódu, můžete tato data snadno rekonstruovat pomocí třídy **ExportImportDevice.** Následující fragment kódu Jazyka C# ukazuje, jak číst informace o zařízení, které byly dříve exportovány do objektu blob bloku:

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

Metoda **ImportDevicesAsync** ve třídě **RegistryManager** umožňuje provádět operace hromadného importu a synchronizace v registru identit y centra IoT. Podobně jako metoda **ExportDevicesAsync** používá metoda **ImportDevicesAsync** rozhraní **Job** Framework.

Dbejte na to, abyste použili metodu **ImportDevicesAsync,** protože kromě zřizování nových zařízení v registru identit může také aktualizovat a odstranit stávající zařízení.

> [!WARNING]
> Operaci importu nelze vrátit zpět. Vždy zálohovat existující data pomocí **ExportDevicesAsync** metoda do jiného kontejneru objektů blob před provedete hromadné změny registru identit.

Metoda **ImportDevicesAsync** má dva parametry:

* *Řetězec,* který obsahuje identifikátor URI kontejneru objektů blob [azure storage](../storage/index.yml) použít jako *vstup* do úlohy. Tento identifikátor URI musí obsahovat token SAS, který uděluje přístup pro čtení do kontejneru. Tento kontejner musí obsahovat objekt blob s názvem **devices.txt,** který obsahuje serializovaná data zařízení pro import do registru identit. Data importu musí obsahovat informace o zařízení ve stejném formátu JSON, který používá **úloha ExportImportDevice** při vytváření objektu blob **device.txt.** Token SAS musí obsahovat tato oprávnění:

   ```csharp
   SharedAccessBlobPermissions.Read
   ```

* *Řetězec,* který obsahuje identifikátor URI kontejneru objektů blob [azure storage](https://azure.microsoft.com/documentation/services/storage/) použít jako *výstup* z úlohy. Úloha vytvoří objekt blob bloku v tomto kontejneru pro uložení všech chybových informací z dokončené **úlohy importu**. Token SAS musí obsahovat tato oprávnění:

   ```csharp
   SharedAccessBlobPermissions.Write | SharedAccessBlobPermissions.Read 
     | SharedAccessBlobPermissions.Delete
   ```

> [!NOTE]
> Tyto dva parametry mohou překážet na stejný kontejner objektů blob. Samostatné parametry jednoduše umožňují větší kontrolu nad daty, protože výstupní kontejner vyžaduje další oprávnění.

Následující fragment kódu Jazyka C# ukazuje, jak iniciovat úlohu importu:

```csharp
JobProperties importJob = 
   await registryManager.ImportDevicesAsync(containerSasUri, containerSasUri);
```

Tuto metodu lze také použít k importu dat pro dvojče zařízení. Formát pro vstup dat je stejný jako formát zobrazený v části **ExportDevicesAsync.** Tímto způsobem můžete exportovaná data znovu importovat. $metadata **$metadata** je volitelné.

## <a name="import-behavior"></a>Chování importu

Metodu **ImportDevicesAsync** můžete použít k provedení následujících hromadných operací v registru identit:

* Hromadná registrace nových zařízení
* Hromadné odstranění stávajících zařízení
* Hromadné změny stavu (povolení nebo zakázání zařízení)
* Hromadné přiřazení nových ověřovacích klíčů zařízení
* Hromadná automatická regenerace ověřovacích klíčů zařízení
* Hromadná aktualizace dat dvojčete

Můžete provést libovolnou kombinaci předchozích operací v rámci jednoho volání **ImportDevicesAsync.** Můžete například zaregistrovat nová zařízení a současně odstranit nebo aktualizovat stávající zařízení. Při použití spolu s **ExportDevicesAsync** metoda, můžete zcela migrovat všechna zařízení z jednoho centra IoT do jiného.

Pokud soubor importu obsahuje metadata dvojčat, tato metadata přepíší existující metadata dvojčete. Pokud soubor importu neobsahuje metadata dvojčete, budou aktualizována pouze `lastUpdateTime` metadata podle aktuálního času.

Pomocí volitelné **vlastnosti importMode** v importu serializačních dat pro každé zařízení můžete řídit proces importu na zařízení. Vlastnost **importMode** má následující možnosti:

| importMode | Popis |
| --- | --- |
| **createOrUpdate** |Pokud zařízení neexistuje se zadaným **ID**, je nově zaregistrováno. <br/>Pokud zařízení již existuje, existující informace jsou přepsány zadanými vstupními daty bez ohledu na hodnotu **ETag.** <br> Uživatel může volitelně zadat data dvojčete spolu s daty zařízení. Dvojče etag, pokud je zadán, je zpracována nezávisle na zařízení etag. Pokud dojde k neshodě s etag existující dvojče, je do souboru protokolu zapsána chyba. |
| **Vytvořit** |Pokud zařízení neexistuje se zadaným **ID**, je nově zaregistrováno. <br/>Pokud zařízení již existuje, je do souboru protokolu zapsána chyba. <br> Uživatel může volitelně zadat data dvojčete spolu s daty zařízení. Dvojče etag, pokud je zadán, je zpracována nezávisle na zařízení etag. Pokud dojde k neshodě s etag existující dvojče, je do souboru protokolu zapsána chyba. |
| **update** |Pokud zařízení již existuje se zadaným **ID**, existující informace jsou přepsány zadanými vstupními daty bez ohledu na hodnotu **ETag.** <br/>Pokud zařízení neexistuje, je do souboru protokolu zapsána chyba. |
| **aktualizovatIfMatchETag** |Pokud zařízení již existuje se zadaným **ID**, existující informace jsou přepsány zadanými vstupními daty pouze v případě, že existuje shoda **ETag.** <br/>Pokud zařízení neexistuje, je do souboru protokolu zapsána chyba. <br/>Pokud je neshoda **ETag,** je zapsána chyba do souboru protokolu. |
| **createOrUpdateIfMatchETag** |Pokud zařízení neexistuje se zadaným **ID**, je nově zaregistrováno. <br/>Pokud zařízení již existuje, existující informace jsou přepsány zadanými vstupními daty pouze v případě, že existuje shoda **ETag.** <br/>Pokud je neshoda **ETag,** je zapsána chyba do souboru protokolu. <br> Uživatel může volitelně zadat data dvojčete spolu s daty zařízení. Dvojče etag, pokud je zadán, je zpracována nezávisle na zařízení etag. Pokud dojde k neshodě s etag existující dvojče, je do souboru protokolu zapsána chyba. |
| **delete** |Pokud zařízení již existuje se zadaným **ID**, je odstraněno bez ohledu na hodnotu **ETag.** <br/>Pokud zařízení neexistuje, je do souboru protokolu zapsána chyba. |
| **odstranitIfMatchETag** |Pokud zařízení již existuje se zadaným **ID**, je odstraněno pouze v případě, že existuje shoda **ETag.** Pokud zařízení neexistuje, je do souboru protokolu zapsána chyba. <br/>Pokud je neshoda ETag, je zapsána chyba do souboru protokolu. |

> [!NOTE]
> Pokud serializace data není explicitně definovat **importMode** příznak pro zařízení, je výchozí **vytvořitOrUpdate** během operace importu.

## <a name="import-devices-example--bulk-device-provisioning"></a>Příklad importu zařízení – zřizování sypkých zařízení

Následující ukázka kódu jazyka C# ukazuje, jak generovat více identit zařízení, které:

* Zahrnout ověřovací klíče.
* Napište informace o zařízení do objektu blob bloku.
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

## <a name="import-devices-example--bulk-deletion"></a>Příklad importu zařízení – hromadné odstranění

Následující ukázka kódu ukazuje, jak odstranit zařízení, která jste přidali pomocí předchozí ukázky kódu:

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

## <a name="get-the-container-sas-uri"></a>Získání kontejneru Identifikátor URI SAS

Následující ukázka kódu ukazuje, jak generovat [identifikátor URI SAS](../storage/common/storage-dotnet-shared-access-signature-part-1.md) s oprávněními ke čtení, zápisu a odstraňování pro kontejner objektů blob:

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

V tomto článku jste se dozvěděli, jak provádět hromadné operace proti registru identit v centru IoT. Mnohé z těchto operací, včetně způsobu přesunu zařízení z jednoho rozbočovače do druhého, se používají v [části Správa registrovaných v části Centrum IoT v části Jak klonovat centrum IoT Hub](iot-hub-how-to-clone.md#managing-the-devices-registered-to-the-iot-hub). 

Klonování článek má pracovní ukázku k němu spojené, který je umístěn v ukázkách IoT C# na této stránce: [Azure IoT Ukázky pro C#](https://azure.microsoft.com/resources/samples/azure-iot-samples-csharp/), s projektem je ImportExportDevicesSample. Můžete si stáhnout vzorek a vyzkoušet; v článku Jak klonovat centrum [IoT Hub](iot-hub-how-to-clone.md) jsou pokyny.

Další informace o správě služby Azure IoT Hub najdete v následujících článcích:

* [Metriky IoT Hubu](iot-hub-metrics.md)
* [Protokoly ioT hubu](iot-hub-monitor-resource-health.md)

Další informace o možnostech IoT Hubu najdete v následujících tématech:

* [Průvodce vývojáři ioT Hubu](iot-hub-devguide.md)
* [Nasazení AI do hraničních zařízení s použitím Azure IoT Edge](../iot-edge/tutorial-simulate-device-linux.md)

Chcete-li prozkoumat pomocí služby zřizování zařízení služby IoT Hub a povolit zřizování s nulovým dotykem a just-in-time, přečtěte si následující: 

* [Služba Azure IoT Hub Device Provisioning](/azure/iot-dps)
