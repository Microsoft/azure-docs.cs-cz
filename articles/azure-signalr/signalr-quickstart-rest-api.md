---
title: Rychlý Start – REST API služby signalizace Azure
description: Naučte se používat REST API se službou Azure Signal Service následující ukázky. Vyhledejte podrobnosti REST API specifikace.
author: sffamily
ms.service: signalr
ms.topic: quickstart
ms.date: 11/13/2019
ms.author: zhshang
ms.openlocfilehash: dfcbb00ec20797248f41cc1676809f3198d51527
ms.sourcegitcommit: 2aeb2c41fd22a02552ff871479124b567fa4463c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2021
ms.locfileid: "107866156"
---
# <a name="quickstart-broadcast-real-time-messages-from-console-app"></a>Rychlý start: Vysílání zpráv v reálném čase z konzoly aplikace

Ve službě Azure SignalR je k dispozici [rozhraní REST API](https://github.com/Azure/azure-signalr/blob/dev/docs/rest-api.md) pro podporu scénářů komunikace ze serveru do klienta, jako je například vysílání. Můžete použít libovolný programovací jazyk, který může provádět volání rozhraní REST API. Můžete posílat zprávy do všech připojených klientů, do konkrétního klienta podle názvu nebo skupiny klientů.

V tomto rychlém startu se dozvíte, jak odesílat zprávy z aplikace příkazového řádku do aplikací připojených klientů v jazyce C#.

## <a name="prerequisites"></a>Požadavky

Toto Rychlé zprovoznění je možné spustit v systémech macOS, Windows nebo Linux.

* [Sada .NET Core SDK](https://dotnet.microsoft.com/download)
* Textový editor nebo editor kódu podle vašeho výběru.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsapi)prosím o tom.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese <https://portal.azure.com/> pomocí svého účtu Azure.

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsapi)prosím o tom.

[!INCLUDE [Create instance](includes/signalr-quickstart-create-instance.md)]

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsapi)prosím o tom.

## <a name="clone-the-sample-application"></a>Klonování ukázkové aplikace

Zatímco se nasazuje služba, přejděme na přípravu kódu. Naklonujte [ukázkovou aplikaci z GitHubu](https://github.com/aspnet/AzureSignalR-samples.git), nastavte připojovací řetězec služby SignalR Service a spusťte aplikaci místně.

1. Otevřete okno terminálu Git. Přejděte do složky, kam chcete klonovat ukázkový projekt.

1. Ukázkové úložiště naklonujete spuštěním následujícího příkazu. Tento příkaz vytvoří na vašem počítači kopii ukázkové aplikace.

    ```bash
    git clone https://github.com/aspnet/AzureSignalR-samples.git
    ```
Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsapi)prosím o tom.

## <a name="build-and-run-the-sample"></a>Sestavení a spuštění ukázky

Tato ukázka je konzolová aplikace, na které vidíte používání služby Azure SignalR. Jsou v ní k dispozici dva režimy:

- Režim serveru: Volání rozhraní REST API služby Azure SignalR pomocí jednoduchých příkazů
- Režim klienta: Připojení ke službě Azure SignalR a přijímání zpráv ze serveru

Můžete také zjistit, jak vygenerovat přístupový token k ověření pomocí služby Azure SignalR.

### <a name="build-the-executable-file"></a>Vytvoření spustitelného souboru

Jako příklad použijeme macOS osx.10.13-x64. K dispozici jsou [reference](/dotnet/core/rid-catalog) k vytváření na jiných platformách.

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

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsapi)prosím o tom.

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

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsapi)prosím o tom.

## <a name="usage"></a>Využití

Po spuštění serveru použijte příkaz k odeslání zprávy:

```
send user <User Id>
send users <User List>
send group <Group Name>
send groups <Group List>
broadcast
```

Můžete spustit více klientů s různými názvy.

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsapi)prosím o tom.

## <a name="integration-with-third-party-services"></a><a name="usage"> </a>Integrace se službami třetích stran

Služba Azure SignalR umožňuje v systému integraci služeb třetích stran.

### <a name="definition-of-technical-specifications"></a>Definice technických specifikací

Následující tabulka uvádí všechny podporované verze rozhraní REST API k datu vydání tohoto článku. U každé verze také najdete definiční soubor.

Verze | Stav rozhraní API | Dveře | Specifikace
--- | --- | --- | ---
`1.0-preview` | K dispozici. | 5002 | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1-preview.json)
`1.0` | K dispozici. | Standard | [Swagger](https://github.com/Azure/azure-signalr/tree/dev/docs/swagger/v1.json)

Dostupná rozhraní API pro jednotlivé verze najdete v následujícím seznamu.

Rozhraní API | 1,0 – Preview | 1.0
--- | --- | ---
[Všesměrové vysílání pro všechny](#broadcast) | **&#x2713;** | **&#x2713;**
[Všesměrové vysílání do skupiny](#broadcast-group) | **&#x2713;** | **&#x2713;**
Všesměrové vysílání do některých skupin | **&#x2713;** (zastaralé) | `N / A`
[Odeslat uživateli](#send-user) | **&#x2713;** | **&#x2713;**
Odesílání některým uživatelům | **&#x2713;** (zastaralé) | `N / A`
[Přidání uživatele do skupiny](#add-user-to-group) | `N / A` | **&#x2713;**
[Odebrání uživatele ze skupiny](#remove-user-from-group) | `N / A` | **&#x2713;**
[Kontrolovat existenci uživatele](#check-user-existence) | `N / A` | **&#x2713;**
[Odebrání uživatele ze všech skupin](#remove-user-from-all-groups) | `N / A` | **&#x2713;**
[Odeslat do připojení](#send-connection) | `N / A` | **&#x2713;**
[Přidání připojení ke skupině](#add-connection-to-group) | `N / A` | **&#x2713;**
[Odebrání připojení ze skupiny](#remove-connection-from-group) | `N / A` | **&#x2713;**
[Zavřít připojení klienta](#close-connection) | `N / A` | **&#x2713;**
[Service Health](#service-health) | `N / A` | **&#x2713;**

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
### <a name="sending-to-a-user"></a>Odesílání uživateli

Verze | Metoda HTTP v rozhraní API | Adresa URL požadavku | Text požadavku
--- | --- | --- | ---
`1.0-preview` | `POST` | `https://<instance-name>.service.signalr.net:5002/api/v1-preview/hub/<hub-name>/user/<user-id>` | `{"target": "<method-name>", "arguments": [...]}`
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>` | Stejný jako výše uvedený

<a name="add-user-to-group"> </a>
### <a name="adding-a-user-to-a-group"></a>Přidání uživatele do skupiny

Verze | Metoda HTTP v rozhraní API | Adresa URL požadavku
--- | --- | ---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="remove-user-from-group"> </a>
### <a name="removing-a-user-from-a-group"></a>Odebrání uživatele ze skupiny

Verze | Metoda HTTP v rozhraní API | Adresa URL požadavku
--- | --- | ---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>`

<a name="check-user-existence"> </a>
### <a name="check-user-existence-in-a-group"></a>Kontrolovat existenci uživatele ve skupině

Verze rozhraní API | Metoda HTTP v rozhraní API | Adresa URL požadavku
---|---|---
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups/<group-name>`
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/users/<user-id>` 

Stavový kód odpovědi | Description
---|---
`200` | Uživatel existuje
`404` | Uživatel neexistuje.

<a name="remove-user-from-all-groups"> </a>
### <a name="remove-a-user-from-all-groups"></a>Odebrání uživatele ze všech skupin

Verze rozhraní API | Metoda HTTP v rozhraní API | Adresa URL požadavku
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/users/<user-id>/groups`

<a name="send-connection"> </a>
### <a name="send-message-to-a-connection"></a>Odeslat zprávu na připojení

Verze rozhraní API | Metoda HTTP v rozhraní API | Adresa URL požadavku | Text požadavku
---|---|---|---
`1.0` | `POST` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>` | `{ "target":"<method-name>", "arguments":[ ... ] }`

<a name="add-connection-to-group"> </a>
### <a name="add-a-connection-to-a-group"></a>Přidání připojení ke skupině

Verze rozhraní API | Metoda HTTP v rozhraní API | Adresa URL požadavku
---|---|---
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `PUT` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="remove-connection-from-group"> </a>
### <a name="remove-a-connection-from-a-group"></a>Odebrání připojení ze skupiny

Verze rozhraní API | Metoda HTTP v rozhraní API | Adresa URL požadavku
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/groups/<group-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>/groups/<group-name>`

<a name="close-connection"> </a>
### <a name="close-a-client-connection"></a>Zavřít připojení klienta

Verze rozhraní API | Metoda HTTP v rozhraní API | Adresa URL požadavku
---|---|---
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>`
`1.0` | `DELETE` | `https://<instance-name>.service.signalr.net/api/v1/hubs/<hub-name>/connections/<connection-id>?reason=<close-reason>`

<a name="service-health"> </a>
### <a name="service-health"></a>Service Health

Verze rozhraní API | Metoda HTTP v rozhraní API | Adresa URL požadavku
---|---|---                             
`1.0` | `GET` | `https://<instance-name>.service.signalr.net/api/v1/health`

Stavový kód odpovědi | Description
---|---
`200` | Služba je dobrá
`5xx` | Chyba služby

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsapi)prosím o tom.

[!INCLUDE [Cleanup](includes/signalr-quickstart-cleanup.md)]

Máte problémy? Vyzkoušejte si [příručku pro odstraňování potíží](signalr-howto-troubleshoot-guide.md) nebo [dejte nám](https://aka.ms/asrs/qsapi)prosím o tom.

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste zjistili, jak pomocí REST API vysílat zprávy v reálném čase od služby signalizace klientům. V dalším kroku se dozvíte, jak vyvíjet a nasazovat Azure Functions pomocí vazby služby signalizace, která je postavená na REST API.

> [!div class="nextstepaction"]
> [Vývoj Azure Functions pomocí vazeb služby signalizace Azure](signalr-quickstart-azure-functions-csharp.md)
