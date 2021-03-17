---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 12/28/2020
ms.author: tamram
ms.openlocfilehash: 85cfe3b062d7d9ef3a7bdcf29ef7d2125f8f3ae4
ms.sourcegitcommit: 31d242b611a2887e0af1fc501a7d808c933a6bf6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/29/2020
ms.locfileid: "97812804"
---
Emulátor podporuje jeden pevný účet a známý ověřovací klíč pro ověřování pomocí sdíleného klíče. Tento účet a klíč jsou jedinými přihlašovacími údaji ke sdíleným klíčům povoleným pro použití s emulátorem. Jsou to tyto:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Ověřovací klíč podporovaný emulátorem je určen pouze pro testování funkcí kódu ověřování klienta. Neslouží k žádnému účelu zabezpečení. K emulátoru nemůžete použít svůj účet a klíč produkčního úložiště. Nepoužívejte vývojový účet s provozními daty.
>
> Emulátor podporuje pouze připojení prostřednictvím protokolu HTTP. Protokol HTTPS je ale doporučeným protokolem pro přístup k prostředkům v produkčním účtu úložiště Azure.
>

#### <a name="connect-to-the-emulator-account-using-the-shortcut"></a>Připojení k účtu emulátoru pomocí zástupce

Nejjednodušší způsob, jak se připojit k emulátoru z vaší aplikace, je nakonfigurovat připojovací řetězec v konfiguračním souboru vaší aplikace, který odkazuje na zástupce `UseDevelopmentStorage=true` . Zástupce je ekvivalentem úplného připojovacího řetězce pro emulátor, který určuje název účtu, klíč účtu a koncové body emulátoru pro každou službu Azure Storage Services:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Následující fragment kódu .NET ukazuje, jak lze použít zástupce z metody, která přijímá připojovací řetězec. Například konstruktor [BlobContainerClient (String, String)](/dotnet/api/azure.storage.blobs.blobcontainerclient.-ctor#Azure_Storage_Blobs_BlobContainerClient__ctor_System_String_System_String_) přijímá připojovací řetězec.

```csharp
BlobContainerClient blobContainerClient = new BlobContainerClient("UseDevelopmentStorage=true", "sample-container");
blobContainerClient.CreateIfNotExists();
```

Ujistěte se, že emulátor běží před voláním kódu ve fragmentu.
