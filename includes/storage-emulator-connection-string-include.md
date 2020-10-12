---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 07/17/2020
ms.author: tamram
ms.openlocfilehash: 85e7cb86217340e77a6f597a357c3de1f91fb8d0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "87070553"
---
Azurite podporuje jeden pevný účet a známý ověřovací klíč pro ověřování pomocí sdíleného klíče. Tento účet a klíč jsou jedinými přihlašovacími údaji ke sdíleným klíčům povoleným pro použití s Azurite. Jsou to tyto:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Ověřovací klíč podporovaný funkcí Azurite je určen pouze pro testování funkcí kódu ověřování klienta. Neslouží k žádnému účelu zabezpečení. V Azurite nemůžete použít svůj účet a klíč produkčního úložiště. Nepoužívejte vývojový účet s provozními daty.
> 
> Azurite podporuje připojení pouze prostřednictvím protokolu HTTP. Protokol HTTPS je ale doporučeným protokolem pro přístup k prostředkům v produkčním účtu úložiště Azure.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Připojení k účtu emulátoru pomocí zástupce
Nejjednodušší způsob, jak se připojit k Azurite z vaší aplikace, je nakonfigurovat připojovací řetězec v konfiguračním souboru vaší aplikace, který odkazuje na zástupce `UseDevelopmentStorage=true` . Tady je příklad připojovacího řetězce, který se Azurite v souboru *app.config* : 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Připojte se k účtu emulátoru pomocí známého názvu a klíče účtu.
Chcete-li vytvořit připojovací řetězec, který odkazuje na název a klíč účtu emulátoru, je nutné zadat koncové body pro každou ze služeb, které chcete použít z emulátoru v připojovacím řetězci. To je nezbytné, aby se připojovací řetězec odkazoval na koncové body emulátoru, které se liší od těch, které jsou pro účet úložiště v produkčním prostředí. Například hodnota připojovacího řetězce bude vypadat takto:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Tato hodnota je stejná jako u zkratky zobrazené výše `UseDevelopmentStorage=true` .
