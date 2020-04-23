---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "67175211"
---
Emulátor úložiště podporuje jeden pevný účet a známý ověřovací klíč pro ověřování pomocí sdíleného klíče. Tento účet a klíč jsou jedinými přihlašovacími údaji ke sdíleným klíčům povoleným pro použití s emulátorem úložiště. Jsou to tyto:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Ověřovací klíč podporovaný emulátorem úložiště je určen pouze pro testování funkčnosti kódu ověřování klienta. Neslouží k žádnému účelu zabezpečení. V emulátoru úložiště nemůžete použít svůj účet a klíč produkčního úložiště. Nepoužívejte vývojový účet s provozními daty.
> 
> Emulátor úložiště podporuje pouze připojení prostřednictvím protokolu HTTP. Protokol HTTPS je ale doporučeným protokolem pro přístup k prostředkům v produkčním účtu úložiště Azure.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Připojení k účtu emulátoru pomocí zástupce
Nejjednodušší způsob, jak se připojit k emulátoru úložiště z vaší aplikace, je nakonfigurovat připojovací řetězec v konfiguračním souboru vaší aplikace, který odkazuje na zástupce `UseDevelopmentStorage=true`. Tady je příklad připojovacího řetězce k emulátoru úložiště v souboru *App. config* : 

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
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Tato hodnota je stejná jako u zkratky zobrazené výše `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>Zadat proxy server HTTP
Můžete také zadat proxy server HTTP, který se použije při testování služby proti emulátoru úložiště. To může být užitečné při sledování požadavků a odpovědí HTTP při ladění operací proti službám úložiště. Chcete-li zadat proxy server, `DevelopmentStorageProxyUri` přidejte do připojovacího řetězce možnost a nastavte jeho hodnotu na identifikátor URI proxy serveru. Tady je například připojovací řetězec, který odkazuje na emulátor úložiště a nakonfiguruje proxy HTTP:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

