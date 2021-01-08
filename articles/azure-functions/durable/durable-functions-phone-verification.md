---
title: Lidská interakce a časové limity v Durable Functions – Azure
description: Naučte se, jak zpracovávat lidské interakce a časové limity v rozšíření Durable Functions pro Azure Functions.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: cba3cd0fd5d8727c4ffa4d1b42d7cd9250f21032
ms.sourcegitcommit: e46f9981626751f129926a2dae327a729228216e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98028299"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Ukázka lidské interakce v Durable Functions-telefon ověřování

Tato ukázka předvádí, jak sestavit [Durable Functions](durable-functions-overview.md) orchestrace, která zahrnuje lidskou interakci. Kdykoli je skutečná osoba zapojena do automatizovaného procesu, proces musí být schopný odesílat oznámení osobě a přijímat odpovědi asynchronně. Také musí umožnit, aby osoba nebyla k dispozici. (V této poslední části se stanou důležité časové limity.)

Tato ukázka implementuje systém ověřování pro telefon založený na SMS. Tyto typy toků se často používají při ověřování telefonního čísla zákazníka nebo služby Multi-Factor Authentication (MFA). Jedná se o účinný příklad, protože celá implementace se provádí pomocí několika malých funkcí. Není vyžadováno žádné externí úložiště dat, například databáze.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Přehled scénáře

Ověřování pomocí telefonu se používá k ověření, že koncoví uživatelé vaší aplikace nejsou odesilatelé nevyžádané pošty a že se k nim říkají. Multi-Factor Authentication je běžný případ použití pro ochranu uživatelských účtů proti hackerům. Výzvou k implementaci vlastního ověřování na základě telefonu je, že vyžaduje **stavovou interakci** s lidmi. Koncovému uživateli se obvykle poskytuje nějaký kód (například číslo se čtyřmi číslicemi) a musí reagovat **v rozumném časovém intervalu**.

Běžné Azure Functions jsou bezstavové (stejně jako mnoho dalších koncových bodů cloudu na jiných platformách), takže tyto typy interakcí zahrnují explicitní správu stavu externě v databázi nebo v jiném trvalém úložišti. Kromě toho musí být interakce rozdělena do více funkcí, které mohou být koordinovány dohromady. Například potřebujete alespoň jednu funkci pro rozhodování o kódu, zachovat ho někam a poslat ho do telefonu uživatele. Kromě toho potřebujete alespoň jednu další funkci pro příjem odpovědi od uživatele a nějakým způsobem ji namapovat zpátky na původní volání funkce, aby bylo možné provést ověření kódu. Časový limit je také důležitým aspektem pro zajištění zabezpečení. Může rychle docházet k poměrně složitému zobrazení.

Složitost tohoto scénáře se výrazně zkracuje při použití Durable Functions. Jak vidíte v této ukázce, může funkce Orchestrator spravovat stavovou interakci snadno a bez zahrnutí externích úložišť dat. Vzhledem k tomu, že funkce nástroje Orchestrator jsou *trvalé*, jsou tyto interaktivní toky také vysoce spolehlivé.

## <a name="configuring-twilio-integration"></a>Konfigurace integrace Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funkce

Tento článek vás provede následujícími funkcemi v ukázkové aplikaci:

* `E4_SmsPhoneVerification`: [Funkce Orchestrator](durable-functions-bindings.md#orchestration-trigger) , která provádí proces ověření telefonu, včetně správy časových limitů a opakovaných pokusů.
* `E4_SendSmsChallenge`: [Funkce Activity](durable-functions-bindings.md#activity-trigger) , která odesílá kód prostřednictvím textové zprávy.

### <a name="e4_smsphoneverification-orchestrator-function"></a>E4_SmsPhoneVerification funkce Orchestrator

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> Nemusí být zpočátku zřejmé, ale tento produkt Orchestrator nerušuje [omezení deterministické orchestrace](durable-functions-code-constraints.md). Je deterministický `CurrentUtcDateTime` , protože vlastnost se používá k výpočtu času vypršení platnosti časovače a vrací stejnou hodnotu pro každé přehrání v tomto okamžiku v kódu Orchestrator. Toto chování je důležité, aby bylo zajištěno, že budou stejné `winner` výsledky z každého opakovaného volání `Task.WhenAny` .

# <a name="javascript"></a>[JavaScript](#tab/javascript)

Funkce **E4_SmsPhoneVerification** používá standardní *function.js* pro funkce nástroje Orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

Zde je kód, který implementuje funkci:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> Nemusí být zpočátku zřejmé, ale tento produkt Orchestrator nerušuje [omezení deterministické orchestrace](durable-functions-code-constraints.md). Je deterministický `currentUtcDateTime` , protože vlastnost se používá k výpočtu času vypršení platnosti časovače a vrací stejnou hodnotu pro každé přehrání v tomto okamžiku v kódu Orchestrator. Toto chování je důležité, aby bylo zajištěno, že budou stejné `winner` výsledky z každého opakovaného volání `context.df.Task.any` .

# <a name="python"></a>[Python](#tab/python)

Funkce **E4_SmsPhoneVerification** používá standardní *function.js* pro funkce nástroje Orchestrator.

[!code-json[Main](~/samples-durable-functions-python/samples/human_interaction/E4_SmsPhoneVerification/function.json)]

Zde je kód, který implementuje funkci:

[!code-python[Main](~/samples-durable-functions-python/samples/human_interaction/E4_SmsPhoneVerification/\_\_init\_\_.py)]

> [!NOTE]
> Nemusí být zpočátku zřejmé, ale tento produkt Orchestrator nerušuje [omezení deterministické orchestrace](durable-functions-code-constraints.md). Je deterministický `currentUtcDateTime` , protože vlastnost se používá k výpočtu času vypršení platnosti časovače a vrací stejnou hodnotu pro každé přehrání v tomto okamžiku v kódu Orchestrator. Toto chování je důležité, aby bylo zajištěno, že budou stejné `winner` výsledky z každého opakovaného volání `context.df.Task.any` .

---

Po spuštění tato funkce Orchestrator provede následující akce:

1. Načte telefonní číslo, na které bude *odesílat* oznámení SMS.
2. Zavolá **E4_SendSmsChallenge** k odeslání zprávy SMS uživateli a vrátí zpět očekávaný kód výzvy se čtyřmi číslicemi.
3. Vytvoří trvalý časovač, který spouští 90 sekund od aktuálního času.
4. Paralelně s časovačem počká na událost **SmsChallengeResponse** od uživatele.

Uživatel obdrží zprávu SMS s kódem se čtyřmi číslicemi. Mají 90 sekund k odeslání stejného kódu se čtyřmi číslicemi zpátky do instance funkce Orchestrator, aby se dokončil proces ověření. Pokud odešle nesprávný kód, získá další tři pokusy o jeho navýšení (v rámci stejného 90 druhého okna).

> [!WARNING]
> Je důležité [Zrušit časovače](durable-functions-timers.md) , pokud už nepotřebujete, aby vyprší platnost, jako v příkladu výše, pokud je odpověď na výzvu přijata.

## <a name="e4_sendsmschallenge-activity-function"></a>Funkce aktivity E4_SendSmsChallenge

Funkce **E4_SendSmsChallenge** používá vazbu Twilio k odeslání zprávy SMS s kódem se čtyřmi číslicemi koncovému uživateli.

# <a name="c"></a>[C#](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> `Microsoft.Azure.WebJobs.Extensions.Twilio`Pro spuštění ukázkového kódu budete muset nainstalovat balíček NuGet.

# <a name="javascript"></a>[JavaScript](#tab/javascript)

*function.jsv* je definován následujícím způsobem:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

A zde je kód, který generuje kód výzvy se čtyřmi číslicemi a odesílá zprávu SMS:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

# <a name="python"></a>[Python](#tab/python)

*function.jsv* je definován následujícím způsobem:

[!code-json[Main](~/samples-durable-functions-python/samples/human_interaction/SendSMSChallenge/function.json)]

A zde je kód, který generuje kód výzvy se čtyřmi číslicemi a odesílá zprávu SMS:

[!code-python[Main](~/samples-durable-functions-python/samples/human_interaction/SendSMSChallenge/\_\_init\_\_.py)]

---

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
Location: http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}

{"id":"741c65651d4c40cea29acdd5bb47baf1","statusQueryGetUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","sendEventPostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/{eventName}?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}","terminatePostUri":"http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/terminate?reason={text}&taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}"}
```

Funkce Orchestrator obdrží poskytnuté telefonní číslo a okamžitě pošle zprávu SMS s náhodně generovaným ověřovacím kódem se čtyřmi číslicemi &mdash; , například *2168*. Funkce pak pro odpověď vyčká 90 sekund.

Chcete-li odpovědět s kódem, můžete použít [ `RaiseEventAsync` (.NET) nebo `raiseEvent` (JavaScript)](durable-functions-instance-management.md) uvnitř jiné funkce nebo vyvolat Webhook **sendEventUrl** http POST, na který odkazuje odpověď 202, nahraďte `{eventName}` názvem události `SmsChallengeResponse` :

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Pokud tuto zprávu odešlete před vypršením platnosti časovače, orchestrace se dokončí a `output` pole se nastaví na hodnotu `true` , což indikuje úspěšné ověření.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
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

## <a name="next-steps"></a>Další kroky

Tato ukázka ukázala některé pokročilé funkce Durable Functions, zejména `WaitForExternalEvent` a `CreateTimer` rozhraní API. Viděli jste, jak se dají kombinovat s `Task.WaitAny` implementací systému spolehlivého časového limitu, který je často užitečný pro interakci se skutečnými lidmi. Další informace o tom, jak používat Durable Functions, najdete v řadě článků, které nabízejí podrobné pokrytí konkrétních témat.

> [!div class="nextstepaction"]
> [Přejít na první článek v řadě](durable-functions-bindings.md)
