---
title: Interakce s lidmi a časové toky v trvalých funkcích – Azure
description: Zjistěte, jak zpracovat lidské interakce a časové toky v rozšíření trvalé funkce pro funkce Azure.
ms.topic: conceptual
ms.date: 12/07/2018
ms.author: azfuncdf
ms.openlocfilehash: 4e0f71369bc02fdce5625d9c74e1d52264ed86be
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80335746"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Interakce s člověkem v odolných funkcích – ukázka ověření telefonu

Tato ukázka ukazuje, jak vytvořit [orchestraci trvalé funkce,](durable-functions-overview.md) která zahrnuje lidské interakce. Vždy, když je skutečná osoba zapojena do automatizovaného procesu, musí být proces schopen odesílat oznámení osobě a přijímat odpovědi asynchronně. Musí také umožnit, aby osoba nebyla k dispozici. (Tato poslední část je místo, kde jsou důležité časové opony.)

Tato ukázka implementuje systém ověřování telefonu založený na serveru SMS. Tyto typy toků se často používají při ověřování telefonního čísla zákazníka nebo pro vícefaktorové ověřování (MFA). Je to výkonný příklad, protože celá implementace se provádí pomocí několika malých funkcí. Není vyžadováno žádné externí úložiště dat, například databáze.

[!INCLUDE [durable-functions-prerequisites](../../../includes/durable-functions-prerequisites.md)]

## <a name="scenario-overview"></a>Přehled scénáře

Ověření telefonu se používá k ověření, že koncoví uživatelé vaší aplikace nejsou spammeři a že jsou tím, za koho se považují. Vícefaktorové ověřování je běžný případ použití pro ochranu uživatelských účtů před hackery. Výzvou s implementací vlastního ověření telefonu je, že vyžaduje **stavovou interakci** s lidskou bytostí. Koncový uživatel je obvykle k dispozici nějaký kód (například 4-místné číslo) a musí reagovat **v přiměřené době**.

Běžné funkce Azure jsou bezstavové (stejně jako mnoho jiných koncových bodů cloudu na jiných platformách), takže tyto typy interakcí zahrnují explicitní správu stavu externě v databázi nebo v jiném trvalém úložišti. Kromě toho musí být interakce rozdělena do více funkcí, které mohou být koordinovány společně. Například potřebujete alespoň jednu funkci pro rozhodování o kódu, jeho někde trvalé a jeho odeslání do telefonu uživatele. Kromě toho potřebujete alespoň jednu další funkci pro příjem odpovědi od uživatele a nějak mapovat zpět na volání původní funkce, aby bylo možné provést ověření kódu. Časový čas je také důležitým aspektem pro zajištění zabezpečení. To může dostat poměrně složité rychle.

Složitost tohoto scénáře je výrazně snížena při použití trvalé funkce. Jak uvidíte v této ukázce, funkce orchestrator může snadno spravovat stavové interakce a bez zapojení jakékoli externí úložiště dat. Vzhledem k tomu, že funkce orchestrator jsou *trvanlivé*, tyto interaktivní toky jsou také vysoce spolehlivé.

## <a name="configuring-twilio-integration"></a>Konfigurace integrace Twilio

[!INCLUDE [functions-twilio-integration](../../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funkce

Tento článek vás provede následujícími funkcemi v ukázkové aplikaci:

* `E4_SmsPhoneVerification`: [Funkce orchestrator,](durable-functions-bindings.md#orchestration-trigger) která provádí proces ověření telefonu, včetně správy časových opovenek a opakování.
* `E4_SendSmsChallenge`: [Funkce aktivity,](durable-functions-bindings.md#activity-trigger) která odešle kód prostřednictvím textové zprávy.

### <a name="e4_smsphoneverification-orchestrator-function"></a>E4_SmsPhoneVerification funkce orchestrátoru

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=17-70)]

> [!NOTE]
> Zpočátku to nemusí být zřejmé, ale tato funkce orchestrátoru je zcela deterministická. Je deterministický, `CurrentUtcDateTime` protože vlastnost se používá k výpočtu doby vypršení platnosti časovače a vrátí stejnou hodnotu při každém přehrání v tomto okamžiku v kódu orchestrator. Toto chování je důležité `winner` zajistit, že stejné `Task.WhenAny`výsledky z každé opakované volání .

# <a name="javascript"></a>[Javascript](#tab/javascript)

Funkce **E4_SmsPhoneVerification** používá standardní *funkci.json* pro funkce orchestratoru.

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/function.json)]

Zde je kód, který implementuje funkci:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

> [!NOTE]
> Zpočátku to nemusí být zřejmé, ale tato funkce orchestrátoru je zcela deterministická. Je deterministický, `currentUtcDateTime` protože vlastnost se používá k výpočtu doby vypršení platnosti časovače a vrátí stejnou hodnotu při každém přehrání v tomto okamžiku v kódu orchestrator. Toto chování je důležité `winner` zajistit, že stejné `context.df.Task.any`výsledky z každé opakované volání .

---

Po spuštění tato funkce orchestrator provádí následující akce:

1. Získá telefonní číslo, na které bude *odesílat* oznámení SMS.
2. Volání **E4_SendSmsChallenge** odeslat SMS zprávu uživateli a vrátí zpět očekávaný 4místný kód výzvy.
3. Vytvoří trvalý časovač, který aktivuje 90 sekund od aktuálního času.
4. Souběžně s časovačem čeká na událost **SmsChallengeResponse** od uživatele.

Uživatel obdrží SMS zprávu se čtyřmístným kódem. Mají 90 sekund odeslat stejný čtyřmístný kód zpět do instance funkce orchestrator k dokončení procesu ověření. Pokud odešlou nesprávný kód, dostanou další tři pokusy o jeho nápravu (ve stejném okně 90 sekund).

> [!WARNING]
> Časovače je důležité [zrušit,](durable-functions-timers.md) pokud je již nepotřebujete, jako ve výše uvedeném příkladu, když je přijata odpověď na výzvu.

## <a name="e4_sendsmschallenge-activity-function"></a>E4_SendSmsChallenge funkce aktivity

Funkce **E4_SendSmsChallenge** používá vazbu Twilio k odeslání zprávy SMS se čtyřmístným kódem koncovému uživateli.

# <a name="c"></a>[C #](#tab/csharp)

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs?range=72-89)]

> [!NOTE]
> Budete muset nainstalovat `Microsoft.Azure.WebJobs.Extensions.Twilio` balíček Nuget ke spuštění ukázkového kódu.

# <a name="javascript"></a>[Javascript](#tab/javascript)

*Funkce.json* je definována takto:

[!code-json[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/function.json)]

A tady je kód, který generuje čtyřmístný kód výzvy a odešle SMS zprávu:

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

---

## <a name="run-the-sample"></a>Spuštění ukázky

Pomocí funkcí spouštěných protokolem HTTP, které jsou součástí ukázky, můžete spustit orchestraci odesláním následujícího požadavku HTTP POST:

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

Funkce orchestrator obdrží zadané telefonní číslo a okamžitě mu odešle SMS zprávu s &mdash; náhodně generovaným čtyřmístným ověřovacím kódem, například *2168*. Funkce pak čeká 90 sekund na odpověď.

Chcete-li odpovědět pomocí kódu, `{eventName}` můžete použít `SmsChallengeResponse` [ `RaiseEventAsync` (.NET) nebo `raiseEvent` (JavaScript)](durable-functions-instance-management.md) uvnitř jiné funkce nebo vyvolat **sendEventUrl** HTTP POST webhook u výše uvedené odpovědi 202, který nahradí názvem události:

```
POST http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Pokud toto odeslání odešlete před vypršením časovače, orchestrace se dokončí a `output` pole je nastaveno na `true`, což znamená úspěšné ověření.

```
GET http://{host}/runtime/webhooks/durabletask/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```

```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Pokud necháte časovač vyprší nebo pokud zadáte nesprávný kód čtyřikrát, můžete dotaz `false` na stav a zobrazit výstup funkce orchestrace, což znamená, že ověření telefonu se nezdařilo.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="next-steps"></a>Další kroky

Tato ukázka prokázala některé pokročilé funkce trvalé `WaitForExternalEvent` funkce, zejména a `CreateTimer` api. Viděli jste, jak je lze `Task.WaitAny` kombinovat s implementací spolehlivého systému časového odčasového času, který je často užitečný pro interakci se skutečnými lidmi. Další informace o použití trvanlivé funkce čtením řady článků, které nabízejí podrobné pokrytí konkrétních témat.

> [!div class="nextstepaction"]
> [Přejděte k prvnímu článku v sérii](durable-functions-bindings.md)
