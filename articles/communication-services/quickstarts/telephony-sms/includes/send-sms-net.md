---
title: zahrnout soubor
description: zahrnout soubor
services: azure-communication-services
author: dademath
manager: nimag
ms.service: azure-communication-services
ms.subservice: azure-communication-services
ms.date: 07/28/2020
ms.topic: include
ms.custom: include file
ms.author: dademath
ms.openlocfilehash: 33ea02d3fe54ce5c909b1b7477447bda00dcf06b
ms.sourcegitcommit: 6a4687b86b7aabaeb6aacdfa6c2a1229073254de
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/06/2020
ms.locfileid: "91757118"
---
Začněte s komunikačními službami Azure pomocí klientské knihovny služby Communications C# pro SMS k posílání zpráv SMS.

Po dokončení tohoto rychlého startu dojde v účtu Azure k malým nákladům na několik centů nebo méně.

<!--**TODO: update all these reference links as the resources go live**

[API reference documentation](../../../references/overview.md) | [Library source code](https://github.com/Azure/azure-sdk-for-net-pr/tree/feature/communication/sdk/communication/Azure.Communication.Sms#todo-update-to-public) | [Package (NuGet)](#todo-nuget) | [Samples](#todo-samples)-->

## <a name="prerequisites"></a>Předpoklady

- Účet Azure s aktivním předplatným. [Vytvořte si účet zdarma](https://azure.microsoft.com/free/?WT.mc_id=A261C142F). 
- Nejnovější verze [klientské knihovny .NET Core](https://dotnet.microsoft.com/download/dotnet-core) pro váš operační systém.
- Aktivní prostředek komunikační služby a připojovací řetězec. [Vytvořte prostředek služby Communications](../../create-communication-resource.md).
- Telefonní číslo s povoleným SMS. [Získejte telefonní číslo](../get-phone-number.md).

### <a name="prerequisite-check"></a>Kontrola požadovaných součástí

- V terminálu nebo příkazovém okně spusťte `dotnet` příkaz a ověřte, zda je nainstalována knihovna klienta .NET.
- Chcete-li zobrazit telefonní čísla přidružená ke zdroji komunikačních služeb, přihlaste se k [Azure Portal](https://portal.azure.com/), vyhledejte prostředek komunikačních služeb a otevřete kartu **telefonní čísla** v levém navigačním podokně.

## <a name="setting-up"></a>Nastavení

### <a name="create-a-new-c-application"></a>Vytvoření nové aplikace v C#

V okně konzoly (například cmd, PowerShell nebo bash) použijte `dotnet new` příkaz k vytvoření nové aplikace konzoly s názvem `SmsQuickstart` . Tento příkaz vytvoří jednoduchý projekt C# "Hello World" s jedním zdrojovým souborem: **program.cs**.

```console
dotnet new console -o SmsQuickstart
```

Změňte adresář na nově vytvořenou složku aplikace a použijte `dotnet build` příkaz pro zkompilování aplikace.

```console
cd SmsQuickstart
dotnet build
```

### <a name="install-the-package"></a>Instalace balíčku

Stále v adresáři aplikace nainstalujte knihovnu klienta služby Azure Communications Services pro sadu .NET pomocí `dotnet add package` příkazu.

```console
dotnet add package Azure.Communication.Sms --version 1.0.0-beta.1
```

Přidejte `using` do horní části **program.cs** direktivu, aby zahrnovala `Azure.Communication` obor názvů.

```csharp

using Azure.Communication;
using Azure.Communication.Sms;

```

## <a name="object-model"></a>Objektový model

Následující třídy a rozhraní zpracovávají některé hlavní funkce klientské knihovny SMS služby Azure Communications Services pro jazyk C#.

| Název                                       | Popis                                                                                                                                                       |
| ------------------------------------------ | ----------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| SmsClient     | Tato třída je potřebná pro všechny funkce SMS. Vytvoří se jeho instance s informacemi o předplatném a použije se k posílání zpráv SMS.                           |
| SendSmsOptions | Tato třída poskytuje možnosti konfigurace vytváření sestav o doručení. Pokud je enable_delivery_report nastavené na hodnotu true, vygeneruje se po úspěšném doručení událost. |

## <a name="authenticate-the-client"></a>Ověření klienta

 Otevřete **program.cs** v textovém editoru a nahraďte tělo `Main` metody kódem pro inicializaci `SmsClient` s připojovacím řetězcem. Následující kód načte připojovací řetězec pro prostředek z proměnné prostředí s názvem `COMMUNICATION_SERVICES_CONNECTION_STRING` . Naučte se [Spravovat připojovací řetězec prostředku](../../create-communication-resource.md#store-your-connection-string).


```csharp
// This code demonstrates how to fetch your connection string
// from an environment variable.
string connectionString = Environment.GetEnvironmentVariable("COMMUNICATION_SERVICES_CONNECTION_STRING");

SmsClient smsClient = new SmsClient(connectionString);
```

## <a name="send-an-sms-message"></a>Odeslání zprávy SMS

Odešle zprávu SMS voláním metody Send. Přidejte tento kód na konec `Main` metody v **program.cs**:

```csharp
smsClient.Send(
    from: new PhoneNumber("<leased-phone-number>"),
    to: new PhoneNumber("<to-phone-number>"),
    message: "Hello World via SMS",
    new SendSmsOptions { EnableDeliveryReport = true } // optional
);
```

Měli byste nahradit `<leased-phone-number>` telefonním číslem s povoleným serverem SMS přidruženým k vašemu prostředku komunikačních služeb a `<to-phone-number>` telefonním číslem, na které chcete poslat zprávu.

`EnableDeliveryReport`Parametr je volitelný parametr, který můžete použít ke konfiguraci vytváření sestav o doručení. To je užitečné ve scénářích, kdy chcete generovat události při doručování zpráv SMS. Nastavování sestav doručení pro zprávy SMS najdete v rychlém startu pro [zpracování událostí SMS](../handle-sms-events.md) .

## <a name="run-the-code"></a>Spuštění kódu

Spusťte aplikaci z adresáře aplikace pomocí `dotnet run` příkazu.

```console
dotnet run
```
