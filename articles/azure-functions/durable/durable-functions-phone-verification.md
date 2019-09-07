---
title: Lidská interakce a časové limity v Durable Functions – Azure
description: Naučte se, jak zpracovávat lidské interakce a časové limity v rozšíření Durable Functions pro Azure Functions.
services: functions
author: ggailey777
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4d8955517450ce3b4efdf30e2790e4be678dfc7b
ms.sourcegitcommit: 97605f3e7ff9b6f74e81f327edd19aefe79135d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/06/2019
ms.locfileid: "70735186"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Ukázka lidské interakce v Durable Functions-telefon ověřování

Tato ukázka předvádí, jak sestavit [Durable Functions](durable-functions-overview.md) orchestrace, která zahrnuje lidskou interakci. Kdykoli je skutečná osoba zapojena do automatizovaného procesu, proces musí být schopný odesílat oznámení osobě a přijímat odpovědi asynchronně. Také musí umožnit, aby osoba nebyla k dispozici. (V této poslední části se stanou důležité časové limity.)

Tato ukázka implementuje systém ověřování pro telefon založený na SMS. Tyto typy toků se často používají při ověřování telefonního čísla zákazníka nebo služby Multi-Factor Authentication (MFA). Jedná se o účinný příklad, protože celá implementace se provádí pomocí několika malých funkcí. Není vyžadováno žádné externí úložiště dat, například databáze.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Přehled scénáře

Ověřování pomocí telefonu se používá k ověření, že koncoví uživatelé vaší aplikace nejsou odesilatelé nevyžádané pošty a že se k nim říkají. Multi-Factor Authentication je běžný případ použití pro ochranu uživatelských účtů proti hackerům. Výzvou k implementaci vlastního ověřování na základě telefonu je, že vyžaduje **stavovou interakci** s lidmi. Koncovému uživateli se obvykle poskytuje nějaký kód (například číslo se čtyřmi číslicemi) a musí reagovat **v rozumném časovém intervalu**.

Běžné Azure Functions jsou bezstavové (stejně jako mnoho dalších koncových bodů cloudu na jiných platformách), takže tyto typy interakcí zahrnují explicitní správu stavu externě v databázi nebo v jiném trvalém úložišti. Kromě toho musí být interakce rozdělena do více funkcí, které mohou být koordinovány dohromady. Například potřebujete alespoň jednu funkci pro rozhodování o kódu, zachovat ho někam a poslat ho do telefonu uživatele. Kromě toho potřebujete alespoň jednu další funkci pro příjem odpovědi od uživatele a nějakým způsobem ji namapovat zpátky na původní volání funkce, aby bylo možné provést ověření kódu. Časový limit je také důležitým aspektem pro zajištění zabezpečení. To může mít poměrně složitý přístup.

Složitost tohoto scénáře se výrazně zkracuje při použití Durable Functions. Jak vidíte v této ukázce, může funkce Orchestrator spravovat stavovou interakci snadno a bez zahrnutí externích úložišť dat. Vzhledem k tomu, že funkce nástroje Orchestrator jsou *trvalé*, jsou tyto interaktivní toky také vysoce spolehlivé.

## <a name="configuring-twilio-integration"></a>Konfigurace integrace Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funkce

Tento článek vás provede následujícími funkcemi v ukázkové aplikaci:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

Následující části popisují konfiguraci a kód, který se používá ke C# skriptování a JavaScriptu. Kód pro vývoj v aplikaci Visual Studio se zobrazí na konci článku.

## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Orchestrace ověření SMS (ukázkový kód Visual Studio Code a Azure Portal)

Funkce **E4_SmsPhoneVerification** používá standardní *funkci Function. JSON* pro funkce Orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Zde je kód, který implementuje funkci:

### <a name="c-script"></a>C#Pravidel

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

Po spuštění tato funkce Orchestrator provede následující akce:

1. Načte telefonní číslo, na které bude *odesílat* oznámení SMS.
2. Zavolá **E4_SendSmsChallenge** k odeslání zprávy SMS uživateli a vrátí zpět očekávaný kód výzvy se čtyřmi číslicemi.
3. Vytvoří trvalý časovač, který spouští 90 sekund od aktuálního času.
4. Paralelně s časovačem počká na událost **SmsChallengeResponse** od uživatele.

Uživatel obdrží zprávu SMS s kódem se čtyřmi číslicemi. Mají 90 sekund k odeslání stejného čtyřmístného kódu zpátky do instance funkce Orchestrator, aby se dokončil proces ověření. Pokud odešle nesprávný kód, získá další tři pokusy o jeho navýšení (v rámci stejného 90 druhého okna).

> [!NOTE]
> V první době nemusí být zřejmé, ale tato funkce Orchestrator je zcela deterministické. Důvodem je, že `CurrentUtcDateTime` vlastnosti (.NET) `currentUtcDateTime` a (JavaScript) se používají k výpočtu času vypršení platnosti časovače. Tyto vlastnosti vrátí stejnou hodnotu pro každé přehrání v tomto okamžiku v kódu Orchestrator. To je důležité, aby se zajistilo `winner` , že stejné výsledky z každého `Task.WhenAny` opakovaného volání metody `context.df.Task.any` (.NET) nebo (JavaScript).

> [!WARNING]
> Je důležité [Zrušit časovače](durable-functions-timers.md) , pokud už nepotřebujete, aby vyprší platnost, jako v příkladu výše, pokud je odpověď na výzvu přijata.

## <a name="send-the-sms-message"></a>Odeslat zprávu SMS

Funkce **E4_SendSmsChallenge** používá vazbu Twilio k odeslání zprávy SMS s kódem se čtyřmi číslicemi koncovému uživateli. *Funkce Function. JSON* je definována takto:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

A zde je kód, který generuje kód výzvy se čtyřmi číslicemi a pošle zprávu SMS:

### <a name="c-script"></a>C#Pravidel

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-2x-only"></a>JavaScript (jenom funkce 2. x)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

Tato funkce **E4_SendSmsChallenge** se volá jenom jednou, a to i v případě, že proces selže nebo se přehraje. To je dobré, protože nechcete, aby koncový uživatel načítají více zpráv SMS. `challengeCode` Návratová hodnota je automaticky trvale zachovaná, takže funkce Orchestrator vždycky ví, co je to správný kód.

## <a name="run-the-sample"></a>Spuštění ukázky

Pomocí funkcí aktivovaných protokolem HTTP, které jsou součástí ukázky, můžete zahájit orchestraci odesláním následující žádosti HTTP POST:

```
POST http://{host}/orchestrators/E4_SmsPhoneVerification
Content-Length: 14
Content-Type: application/json

"+1425XXXXXXX"
```

```
HTTP/1.1 202 Accepted
Content-Length: 695
Content-Type: application/json; charset=utf-8
Location: http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

Funkce Orchestrator obdrží poskytnuté telefonní číslo a okamžitě pošle zprávu SMS s náhodně generovaným ověřovacím kódem &mdash; se čtyřmi číslicemi, například *2168*. Funkce pak pro odpověď vyčká 90 sekund.

Pro odpověď s kódem můžete použít [ `RaiseEventAsync` (.NET) `raiseEvent` nebo (JavaScript)](durable-functions-instance-management.md) uvnitř jiné funkce nebo vyvolat Webhook **sendEventUrl** http post, na který odkazuje odpověď 202, nahrazuje `{eventName}` názvem `SmsChallengeResponse`událost:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Pokud tuto zprávu odešlete před vypršením platnosti časovače, orchestrace se `output` dokončí a pole `true`se nastaví na hodnotu, což indikuje úspěšné ověření.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Pokud necháte vypršení platnosti časovače, nebo pokud zadáte špatný kód čtyřikrát, můžete zadat dotaz na stav a zobrazit `false` výstup funkce orchestrace, což značí, že se nezdařilo ověření telefonu.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Vzorový kód sady Visual Studio

Toto je orchestrace jako jeden C# soubor v projektu sady Visual Studio:

> [!NOTE]
> Pro spuštění ukázkového kódu níže `Microsoft.Azure.WebJobs.Extensions.Twilio` budete muset nainstalovat balíček NuGet.

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Další postup

Tato ukázka ukázala některé pokročilé funkce Durable Functions, zejména `WaitForExternalEvent` a. `CreateTimer` Viděli jste, jak se dají kombinovat s `Task.WaitAny` implementací systému spolehlivého časového limitu, který je často užitečný pro interakci se skutečnými lidmi. Další informace o tom, jak používat Durable Functions, najdete v řadě článků, které nabízejí podrobné pokrytí konkrétních témat.

> [!div class="nextstepaction"]
> [Přejít na první článek v řadě](durable-functions-bindings.md)
