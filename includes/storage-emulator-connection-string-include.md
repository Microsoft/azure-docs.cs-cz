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
Emulátor úložiště podporuje jeden pevný účet a známý ověřovací klíč pro ověřování pomocí sdíleného klíče. Tento účet a klíč jsou pouze pověření sdíleného klíče povolená pro použití s emulátorem úložiště. Jsou to tyto:

```
Account name: devstoreaccount1
Account key: Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==
```

> [!NOTE]
> Ověřovací klíč podporovaný emulátorem úložiště je určen pouze k testování funkcí ověřovacího kódu klienta. Neslouží žádnému bezpečnostnímu účelu. S emulátorem úložiště nelze použít účet produkčního úložiště a klíč. Vývojový účet byste neměli používat s produkčními daty.
> 
> Emulátor úložiště podporuje připojení pouze přes PROTOKOL HTTP. Protokol HTTPS je však doporučený protokol pro přístup k prostředkům v produkčním účtu úložiště Azure.
> 

#### <a name="connect-to-the-emulator-account-using-a-shortcut"></a>Připojení k účtu emulátoru pomocí zástupce
Nejjednodušší způsob, jak se připojit k emulátoru úložiště z aplikace, je nakonfigurovat připojovací `UseDevelopmentStorage=true`řetězec v konfiguračním souboru aplikace, který odkazuje na zástupce . Tady je příklad připojovacího řetězce k emulátoru úložiště v souboru *app.config:* 

```xml
<appSettings>
  <add key="StorageConnectionString" value="UseDevelopmentStorage=true" />
</appSettings>
```

#### <a name="connect-to-the-emulator-account-using-the-well-known-account-name-and-key"></a>Připojení k účtu emulátoru pomocí známého názvu účtu a klíče
Chcete-li vytvořit připojovací řetězec, který odkazuje na název a klíč účtu emulátoru, musíte zadat koncové body pro každou službu, kterou chcete použít z emulátoru v připojovacím řetězci. To je nezbytné, aby připojovací řetězec odkazoval na koncové body emulátoru, které se liší od koncových bodů pro účet produkčního úložiště. Například hodnota připojovacího řetězce bude vypadat takto:

```
DefaultEndpointsProtocol=http;AccountName=devstoreaccount1;
AccountKey=Eby8vdM02xNOcqFlqUwJPLlmEtlCDXJ1OUzFT50uSRZ6IFsuFq2UVErCz4I6tq/K1SZFPTOtr/KBHBeksoGMGw==;
BlobEndpoint=http://127.0.0.1:10000/devstoreaccount1;
TableEndpoint=http://127.0.0.1:10002/devstoreaccount1;
QueueEndpoint=http://127.0.0.1:10001/devstoreaccount1;
```

Tato hodnota je shodná s `UseDevelopmentStorage=true`výše uvedenou zkratkou .

#### <a name="specify-an-http-proxy"></a>Určení proxy serveru HTTP
Můžete také zadat proxy HTTP, který se má použít při testování služby proti emulátoru úložiště. To může být užitečné pro sledování požadavků HTTP a odpovědí při ladění operací proti službě úložiště. Chcete-li zadat proxy `DevelopmentStorageProxyUri` server, přidejte možnost do připojovacího řetězce a nastavte jeho hodnotu na identifikátor URI proxy serveru. Zde je například připojovací řetězec, který odkazuje na emulátor úložiště a konfiguruje proxy server HTTP:

```
UseDevelopmentStorage=true;DevelopmentStorageProxyUri=http://myProxyUri
```

