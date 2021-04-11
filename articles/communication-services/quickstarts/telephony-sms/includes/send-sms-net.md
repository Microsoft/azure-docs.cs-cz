---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: peiliu
manager: rejooyan
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 03/11/2021
ms.topic: include
ms.custom: include file
ms.author: peiliu
ms.openlocfilehash: 5fd209c612f90e3912e244daf60d20edf30a08c6
ms.sourcegitcommit: 5fd1f72a96f4f343543072eadd7cdec52e86511e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2021
ms.locfileid: "106113425"
---
Začínáme s komunikačními službami Azure pomocí služby Communications Services C# SMS SDK k posílání zpráv SMS.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Požadavky

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Nejnovější verze [.NET Core SDK](https://dotnet.microsoft.com/download/dotnet-core) pro váš operační systém.
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Telefonní číslo s povoleným SMS. [Získejte telefonní číslo](../get-phone-number.md).

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

- V terminálu nebo příkazovém okně spusťte `dotnet` příkaz a ověřte, zda je nainstalována sada .NET SDK.
- Chcete-li zobrazit telefonní čísla přidružená ke zdroji komunikačních služeb, přihlaste se k [Azure Portal](https://portal.azure.com/), vyhledejte prostředek komunikačních služeb a otevřete kartu **telefonní čísla** v levém navigačním podokně.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `SmsQuickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: **program. cs**.

```console
dotnet new console -o SmsQuickstart
```

Změňte adresář na nově vytvořenou složku aplikace a použijte `dotnet build` příkaz pro zkompilování aplikace.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instalace balíčku

Stále v adresáři aplikace nainstalujte pomocí příkazu balíček služby SMS SDK pro službu Azure Communications Services pro .NET `dotnet add package` .

```console
dotnet add package Azure.Communication.Sms --version 1.0.0
```

Přidejte do `using` horní části **programu. cs** direktivu, aby zahrnovala `Azure.Communication` obor názvů.

```csharp

using System;
using System.Collections.Generic;

using Azure;
using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce služby Azure Communications Services SMS SDK pro jazyk C#.

| Název                                       | Description                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Tato třída je potřebná pro všechny funkce SMS. Vytvoří se jeho instance s informacemi o předplatném a použije se k posílání zpráv SMS.                           |
| SmsSendOptions | Tato třída poskytuje možnosti konfigurace vytváření sestav o doručení. Pokud je enable_delivery_report nastavené na hodnotu true, vygeneruje se po úspěšném doručení událost. |
| SmsSendResult               | Tato třída obsahuje výsledek ze služby SMS.                                          |

## <a name="authenticate-the-client"></a>Ověření klienta

 V textovém editoru otevřete **program program. cs** a nahraďte tělo `Main` metody kódem pro inicializaci `SmsClient` pomocí připojovacího řetězce. Následující kód načte připojovací řetězec pro prostředek z proměnné prostředí s názvem `COMMUNICATION_SERVICES_CONNECTION_STRING` . Naučte se [Spravovat připojovací řetězec prostředku](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-a-11-sms-message"></a>Poslat zprávu SMS 1:1

Chcete-li odeslat zprávu SMS jednomu příjemci, zavolejte `Send` `SendAsync` funkci nebo z SmsClient. Přidejte tento kód na konec `Main` metody v **programu program. cs**:

```csharp
SmsSendResult sendResult = smsClient.Send(
    from: "<from-phone-number>",
    to: "<to-phone-number>",
    message: "Hello World via SMS"
);

Console.WriteLine($"Sms id: {sendResult.MessageId}");
```
Měli byste nahradit `<from-phone-number>` telefonním číslem s povoleným serverem SMS přidruženým k vašemu prostředku komunikačních služeb a `<to-phone-number>` telefonním číslem, na které chcete poslat zprávu.

> [!WARNING]
> Všimněte si, že telefonní čísla by měla být zadána ve formátu E. 164 mezinárodní standard. (např.: + 14255550123).

## <a name="send-a-1n-sms-message-with-options"></a>Odeslat zprávu o 1: N SMS s možnostmi
Chcete-li odeslat zprávu SMS seznamu příjemců, zavolejte `Send` `SendAsync` funkci or ze SmsClient se seznamem telefonních čísel příjemců. K určení, zda má být povolena zpráva o doručení a nastavena vlastní značky, můžete také předat volitelné parametry.

```csharp
Response<IReadOnlyList<SmsSendResult>> response = smsClient.Send(
    from: "<from-phone-number>",
    to: new string[] { "<to-phone-number-1>", "<to-phone-number-2>" },
    message: "Weekly Promotion!",
    options: new SmsSendOptions(enableDeliveryReport: true) // OPTIONAL
    {
        Tag = "marketing", // custom tags
    });

IEnumerable<SmsSendResult> results = response.Value;
foreach (SmsSendResult result in results)
{
    Console.WriteLine($"Sms id: {result.MessageId}");
    Console.WriteLine($"Send Result Successful: {result.Successful}");
}
```

Měli byste nahradit `<from-phone-number>` telefonním číslem s podporou serveru SMS, který je přidružený k vašemu prostředku komunikačních služeb, a `<to-phone-number-1>` `<to-phone-number-2>` s telefonními čísly, na které chcete poslat zprávu.

> [!WARNING]
> Všimněte si, že telefonní čísla by měla být zadána ve formátu E. 164 mezinárodní standard. (např.: + 14255550123).

`enableDeliveryReport`Parametr je volitelný parametr, který můžete použít ke konfiguraci vytváření sestav o doručení. To je užitečné ve scénářích, kdy chcete generovat události při doručování zpráv SMS. Nastavování sestav doručení pro zprávy SMS najdete v rychlém startu pro [zpracování událostí SMS](../handle-sms-events.md) .

`Tag` slouží k použití značky pro sestavu doručení.

## <a name="run-the-code"></a>Spuštění kódu

Spusťte aplikaci z adresáře aplikace pomocí `dotnet run` příkazu.

```console
dotnet run
```

## <a name="sample-code"></a>Příklad kódu

Ukázkovou aplikaci si můžete stáhnout z [GitHubu](https://github.com/Azure-Samples/communication-services-dotnet-quickstarts/tree/main/SendSMS) .
