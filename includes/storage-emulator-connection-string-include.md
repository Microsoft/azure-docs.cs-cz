---
author: tamram
ms.service: storage
ms.topic: include
ms.date: 10/26/2018
ms.author: tamram
ms.openlocfilehash: 8c577db3e9f2bff9e86c3a7c37274630f90dd680
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165310"
---
Emulátor úložiště podporuje jeden účet pevné a dobře známý ověřovací klíč pro ověřování pomocí sdíleného klíče. Tento účet a klíče jsou pouze přihlašovací údaje sdíleného klíče pro použití s emulátorem úložiště. Jsou to tyto:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Ověřovací klíč nepodporuje emulátor úložiště je určena pouze pro testování funkce kód pro ověřování klientů. Neslouží jakýkoli účel zabezpečení. Produkčního účtu úložiště a klíč nelze použít s emulátorem úložiště. S použitím provozních dat byste neměli používat účet vývoje.
> 
> Emulátor úložiště podporuje pouze připojení přes protokol HTTP. Ale HTTPS je protokol doporučené pro přístup k prostředkům v produkčním prostředí účtu Azure storage.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Připojit k emulátoru účtu pomocí zástupce
Nejjednodušší způsob, jak se připojit k emulátoru úložiště z vaší aplikace je ke konfiguraci připojovacího řetězce v konfiguračním souboru aplikace, která odkazuje místní `UseDevelopmentStorage=true`. Tady je příklad připojovacího řetězce k emulátoru úložiště v *app.config* souboru: 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Připojit k emulátoru účtu pomocí dobře známý název a klíč
Chcete-li vytvořit připojovací řetězec, který odkazuje na emulátor název účtu a klíč, musíte zadat koncové body pro každou ze služeb, které chcete použít z emulátoru serveru v připojovacím řetězci. To je nezbytné, aby připojovací řetězec bude odkazovat na emulátor koncové body, které jsou jiné než požadavky na účet úložiště v produkčním prostředí. Například hodnota připojovací řetězec bude vypadat takto:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Tato hodnota se shoduje s místní výše uvedenému `UseDevelopmentStorage=true`.

#### <a name="specify-an-http-proxy"></a>Zadejte proxy server HTTP
Můžete také zadat proxy server HTTP pro použití při testování vaší služby emulátoru úložiště. To může být užitečné pro sledování požadavků a odpovědí HTTP během ladění operace služby úložiště. Chcete-li zadat proxy server, přidejte `DevelopmentStorageProxyUri` možnost připojovacího řetězce a nastavte její hodnotu na identifikátor URI proxy serveru. Tady je například připojovací řetězec, který odkazuje na emulátor úložiště a nakonfiguruje server proxy protokolu HTTP:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

