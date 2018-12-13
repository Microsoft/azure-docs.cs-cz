---
title: Rychlý start – rozhraní REST API služby Azure SignalR
description: Rychlý start pro použití rozhraní REST API služby Azure SignalR.
author: sffamily
ms.service: signalr
ms.devlang: dotnet
ms.topic: quickstart
ms.date: 06/13/2018
ms.author: zhshang
ms.openlocfilehash: fdbdbe77c6541d62acef0d23d599d9687f5301b1
ms.sourcegitcommit: 1c1f258c6f32d6280677f899c4bb90b73eac3f2e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2018
ms.locfileid: "53251857"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Rychlý start: Vysílání zpráv v reálném čase z konzoly aplikace

Ve službě Azure SignalR je k dispozici [rozhraní REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) pro podporu scénářů komunikace ze serveru do klienta, jako je například vysílání. Můžete použít libovolný programovací jazyk, který může provádět volání rozhraní REST API. Můžete posílat zprávy do všech připojených klientů, do konkrétního klienta podle názvu nebo skupiny klientů.

V tomto rychlém startu se dozvíte, jak odesílat zprávy z aplikace příkazového řádku do aplikací připojených klientů v jazyce C#.

## <a name="prerequisites"></a>Požadavky

Toto Rychlé zprovoznění je možné spustit v systémech macOS, Windows nebo Linux.

* [Sada .NET Core SDK](https://www.microsoft.com/net/download/core)
* Textový editor nebo editor kódu podle vašeho výběru.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com/> pomocí svého účtu Azure.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Zatímco se nasazuje služba, přejděme na přípravu kódu. Naklonujte [ukázkovou aplikaci z GitHubu](https://github.com/aspnet/AzureSignalR-samples.git), nastavte připojovací řetězec služby SignalR a spusťte aplikaci místně.

1. Otevřete okno terminálu Git. Přejděte do složky, kam chcete klonovat ukázkový projekt.

1. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

Tato ukázka je konzolová aplikace, na které vidíte používání služby Azure SignalR. Jsou v ní k dispozici dva režimy:

- Režim serveru: Volání rozhraní REST API služby Azure SignalR pomocí jednoduchých příkazů
- Režim klienta: Připojení ke službě Azure SignalR a přijímání zpráv ze serveru

Můžete také zjistit, jak vygenerovat přístupový token k ověření pomocí služby Azure SignalR.

### <a name="build-the-executable-file"></a>Vytvoření spustitelného souboru

Jako příklad použijeme macOS osx.10.13-x64. K dispozici jsou [reference](https://docs.microsoft.com/dotnet/core/rid-catalog) k vytváření na jiných platformách.

```bash
cd AzureSignalR-samples/samples/Serverless/

dotnet publish -c Release -r osx.10.13-x64
```

### <a name="start-a-client"></a>Spuštění klienta

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="start-a-server"></a>Spuštění serveru

```bash
cd bin/Release/netcoreapp2.1/osx.10.13-x64/

Serverless server -c "<ConnectionString>" -h <HubName>
```

## <a name="run-the-sample-without-publishing"></a>Spuštění ukázky bez publikování

Server nebo klienta můžete také spustit s použitím následujícího příkazu

```bash
# Start a server
dotnet run -- server -c "<ConnectionString>" -h <HubName>

# Start a client
dotnet run -- client <ClientName> -c "<ConnectionString>" -h <HubName>
```

### <a name="use-user-secrets-to-specify-connection-string"></a>Použití uživatelských tajných kódů při zadání připojovacího řetězce

V kořenovém adresáři vzorku můžete spustit `dotnet user-secrets set Azure:SignalR:ConnectionString "<ConnectionString>"`. Potom už nebudete možnost `-c "<ConnectionString>"` potřebovat.

## <a name="usage"></a>Využití

Po spuštění serveru, použijte příkaz k odeslání zprávy:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Můžete spustit více klientů s různými názvy.

## <a name="usage"> </a>Integrace se službami třetích stran

Služba Azure SignalR umožňuje v systému integraci služeb třetích stran.

### <a name="definition-of-technical-specifications"></a>Definice technických specifikací

Následující tabulka uvádí všechny podporované verze rozhraní REST API k datu vydání tohoto článku. U každé verze také najdete definiční soubor.

Verze | Stav rozhraní API | Dveře | Specifikace
--- | --- | --- | ---
`1.0-preview` | K dispozici. | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | K dispozici. | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

Dostupná rozhraní API pro jednotlivé verze najdete v následujícím seznamu.

Rozhraní API | `1.0-preview` | `1.0`
--- | --- | ---
[Všesměrové vysílání pro všechny](#broadcast) | :heavy_check_mark: | :heavy_check_mark:
[Všesměrové vysílání do skupiny](#broadcast-group) | :heavy_check_mark: | :heavy_check_mark:
Všesměrové vysílání do některých skupin | :heavy_check_mark: (Zastaralé) | `N / A`
[Odesílání konkrétním uživatelům](#send-user) | :heavy_check_mark: | :heavy_check_mark:
Odesílání některým uživatelům | :heavy_check_mark: (Zastaralé) | `N / A`
[Přidání uživatele do skupiny](#add-user-to-group) | `N / A` | :heavy_check_mark:
[Odebrání uživatele ze skupiny](#remove-user-from-group) | `N / A` | :heavy_check_mark:

<a name="broadcast"> </a>
### <a name="broadcast-to-everyone"></a>Všesměrové vysílání pro všechny

Verze | Metoda HTTP v rozhraní API | Adresa URL požadavku | Text požadavku
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>` | Stejný jako výše uvedený

<a name="broadcast-group"> </a>
### <a name="broadcast-to-a-group"></a>Všesměrové vysílání do skupiny

Verze | Metoda HTTP v rozhraní API | Adresa URL požadavku | Text požadavku
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/group/<group-name>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>` | Stejný jako výše uvedený

<a name="send-user"> </a>
### <a name="sending-to-specific-users"></a>Odesílání konkrétním uživatelům

Verze | Metoda HTTP v rozhraní API | Adresa URL požadavku | Text požadavku
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Stejný jako výše uvedený

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Přidání uživatele do skupiny

Verze | Metoda HTTP v rozhraní API | Adresa URL požadavku
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Odebrání uživatele ze skupiny

Verze | Metoda HTTP v rozhraní API | Adresa URL požadavku
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<userid>`

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]
