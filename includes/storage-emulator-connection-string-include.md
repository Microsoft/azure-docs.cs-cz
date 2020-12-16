---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 37fba0101365e425110c2943264c8c0e8c511329
ms.sourcegitcommit: 77ab078e255034bd1a8db499eec6fe9b093a8e4f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/16/2020
ms.locfileid: "97582541"
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

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Připojení k účtu emulátoru pomocí zástupce
Nejjednodušší způsob, jak se připojit k emulátoru z vaší aplikace, je nakonfigurovat připojovací řetězec v konfiguračním souboru vaší aplikace, který odkazuje na zástupce `UseDevelopmentStorage=true` . Tady je příklad připojovacího řetězce k emulátoru v souboru *app.config* : 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

Je ekvivalentní s úplným zadáním názvu účtu, klíče účtu a koncových bodů pro každou službu emulátoru, kterou chcete použít v připojovacím řetězci. To je nezbytné, aby se připojovací řetězec odkazoval na koncové body emulátoru, které se liší od těch, které jsou pro účet úložiště v produkčním prostředí. Například hodnota připojovacího řetězce bude vypadat takto:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```
