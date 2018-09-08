---
title: Lidská interakce a vypršení časového limitu pro v Durable Functions – Azure
description: Zjistěte, jak zpracovat lidské interakce a vypršení časového limitu pro v rozšíření Durable Functions pro službu Azure Functions.
services: functions
author: kashimiz
manager: jeconnoc
keywords: ''
ms.service: azure-functions
ms.devlang: multiple
ms.topic: conceptual
ms.date: 07/11/2018
ms.author: azfuncdf
ms.openlocfilehash: 54c0c8e07ee4a248565b4d71562400c8f427fa77
ms.sourcegitcommit: af60bd400e18fd4cf4965f90094e2411a22e1e77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/07/2018
ms.locfileid: "44092916"
---
# <a name="human-interaction-in-durable-functions---phone-verification-sample"></a>Lidská interakce v Durable Functions – Ukázka ověření telefonu

Tato ukázka předvádí, jak vytvářet [Durable Functions](durable-functions-overview.md) Orchestrace, která zahrnuje lidské interakce. Pokaždé, když se skutečná osoba zapojena do automatizovaného procesu, proces musí být schopen odeslat oznámení uživateli a přijímat odpovědi asynchronně. Musíte také povolit možnost, není k dispozici osobě. (Tento poslední část je, kde vypršení časového limitu na významu nabývají.)

Tato ukázka implementuje služby SMS na telefonu ověřovacího systému. Tyto typy toků se často používají při ověřování na základě telefonní číslo nebo ověřování službou Multi-Factor Authentication (MFA). Výkonné příklad důvodem je, že celý implementace se provádí pomocí několika malých funkcí. Žádné externím úložišti, jako je například databáze, je povinný.

## <a name="prerequisites"></a>Požadavky

* [Nainstalujte Durable Functions](durable-functions-install.md).
* Dokončení [Hello pořadí](durable-functions-sequence.md) návodu.

## <a name="scenario-overview"></a>Přehled scénáře

Ověření telefonu se používá k ověření, že koncoví uživatelé vaší aplikace nejsou spammery a který říká, že jsou jsou. Ověřování službou Multi-Factor Authentication je běžným případem použití k ochraně před hackery uživatelské účty. Problém s implementací vlastní ověření pomocí telefonu je, že vyžaduje **stavové zásahu** s lidmi. Koncový uživatel se většinou poskytuje nějaký kód (například číslo 4 číslice) a musí odpovídat **v rozumném čase**.

Použít běžné funkce Azure jsou bezstavové (jako jsou mnoho dalších koncové body cloudu na jiných platformách), tak tyto druhy interakce zahrnují explicitně Správa externě v databázi nebo některé jiné trvalá úložiště stavu. Kromě toho interakce musí rozdělit do více funkcí, které lze koordinovat společně. Například budete potřebovat alespoň jedna funkce pro rozhodnutí týkající se kód, uchování někde a odesílá je do telefonu uživatele. Kromě toho budete potřebovat alespoň jeden další funkci pro příjem odpověď od uživatele a nějakým způsobem mapovat zpět na původní volání funkce, aby bylo možné provést ověření kódu. Vypršení časového limitu je také k důležitým aspektům, abyste zajistili bezpečnost. To může být složitější poměrně rychle.

Při použití Durable Functions, se výrazně snižuje složitost tento scénář. Jak uvidíte v této ukázce, funkce orchestrátoru můžete snadno a bez zahrnující všechny externích datových úložištích spravovat stavové zásahu. Protože jsou funkcí nástroje orchestrator *trvalý*, tyto interaktivní toky jsou také vysoce spolehlivé.

## <a name="configuring-twilio-integration"></a>Konfigurace integrace platformy Twilio

[!INCLUDE [functions-twilio-integration](../../includes/functions-twilio-integration.md)]

## <a name="the-functions"></a>Funkce

Tento článek vás provede následující funkce v ukázkové aplikaci:

* **E4_SmsPhoneVerification**
* **E4_SendSmsChallenge**

Následující části popisují konfiguraci a kód, který se používají pro skriptovací C# a JavaScript. Kód pro vývoj sady Visual Studio se zobrazí na konci tohoto článku.
 
## <a name="the-sms-verification-orchestration-visual-studio-code-and-azure-portal-sample-code"></a>Orchestrace ověření SMS (Visual Studio Code a Azure portal ukázkový kód) 

**E4_SmsPhoneVerification** funkce používá standardní *function.json* pro funkce nástroje orchestrator.

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/function.json)]

Tady je kód, který implementuje funkce:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SmsPhoneVerification/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SmsPhoneVerification/index.js)]

Po zahájení této funkce orchestrátoru provede následující akce:

1. Získá telefonní číslo, ke kterému bude *odeslat* oznámení zprávou SMS.
2. Volání **E4_SendSmsChallenge** odeslat zprávu SMS na uživatele a vrátí zpět kód očekávané výzvy 4 číslice.
3. Vytvoří trvalý časovače této aktivační události 90 sekund od aktuálního času.
4. Paralelně s časovač, čeká **SmsChallengeResponse** události od uživatele.

Uživatel obdrží zprávu SMS s čtyřmístný kód. Mají 90 sekund se má odeslat zpět do instance funkce nástroje orchestrator pro dokončení procesu ověření, že stejný kód 4 číslice. Pokud odesílání chybný kód, vývojáři získají další tři pokusí získat přímo (v rámci stejné 90sekundové okno).

> [!NOTE]
> Nemusí být zřejmé v první, ale tento orchestrator funkce je zcela deterministický. Je to proto, `CurrentUtcDateTime` vlastnost se používá k výpočtu čas vypršení platnosti časovač a tato vlastnost vrací stejnou hodnotu na každé opakování v tuto chvíli v kódu produktu orchestrator. To je důležité zajistit, aby stejné `winner` výsledkem každého opakovaná volání `Task.WhenAny`.

> [!WARNING]
> Je důležité [zrušení časovače](durable-functions-timers.md) Pokud už nepotřebujete jim vypršení platnosti, stejně jako v příkladu výše, když je přijata odpověď na výzvu.

## <a name="send-the-sms-message"></a>Odeslat zprávu SMS

**E4_SendSmsChallenge** funkce používá vazbu Twilio odeslat zprávu SMS s 4 6místným číselným kódem pro koncového uživatele. *Function.json* je definovaná následujícím způsobem:

[!code-json[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/function.json)]

A tady je kód, který generuje kód 4 číslice výzvy a odešle zprávu SMS:

### <a name="c"></a>C#

[!code-csharp[Main](~/samples-durable-functions/samples/csx/E4_SendSmsChallenge/run.csx)]

### <a name="javascript-functions-v2-only"></a>JavaScript (jenom funkce v2)

[!code-javascript[Main](~/samples-durable-functions/samples/javascript/E4_SendSmsChallenge/index.js)]

To **E4_SendSmsChallenge** funkce získá volat pouze jednou, i když dojde k chybě procesu nebo získá znovu přehrát. To je dobré, protože nechcete, aby koncový uživatel získání více zpráv SMS. `challengeCode` Vrátit hodnota se automaticky ukládají, aby funkce orchestrátoru vždy věděli, co je správný kód.

## <a name="run-the-sample"></a>Spuštění ukázky

Použití ukázka funkcí aktivovanou protokolem HTTP, můžete spustit orchestraci odesláním následujících požadavku HTTP POST:

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

   > [!NOTE]
   > V současné době JavaScript Orchestrace starter funkce nemohou vracet instanci správy identifikátorů URI. Tato funkce bude přidána v novější verzi.

Funkce orchestrátoru přijímá zadané telefonní číslo a okamžitě ji odešle zpráva SMS s náhodně generované 4 číslice ověřovací kód &mdash; například *2168*. Funkce potom odpověď 90 sekund čeká.

Chcete-li odpovědět kódem, můžete použít `RaiseEventAsync` uvnitř jiného funkci nebo volání **sendEventUrl** HTTP POST webhooku odkazuje v odpovědi 202 výše, nahrazení `{eventName}` s názvem události, `SmsChallengeResponse`:

```
POST http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1/raiseEvent/SmsChallengeResponse?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
Content-Length: 4
Content-Type: application/json

2168
```

Pokud odešlete to předtím, než čas vyprší, dokončení orchestraci a `output` je nastaveno na `true`, označující úspěšné ověření.

```
GET http://{host}/admin/extensions/DurableTaskExtension/instances/741c65651d4c40cea29acdd5bb47baf1?taskHub=DurableFunctionsHub&connection=Storage&code={systemKey}
```
```
HTTP/1.1 200 OK
Content-Length: 144
Content-Type: application/json; charset=utf-8

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":true,"createdTime":"2017-06-29T19:10:49Z","lastUpdatedTime":"2017-06-29T19:12:23Z"}
```

Pokud necháte časovač vypršení platnosti, nebo pokud zadáte čtyřikrát chybný kód, můžete zadat dotaz na stav a zobrazit `false` Orchestrace funkce výstupu, že neúspěšná ověření pomocí telefonu.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
Content-Length: 145

{"runtimeStatus":"Completed","input":"+1425XXXXXXX","output":false,"createdTime":"2017-06-29T19:20:49Z","lastUpdatedTime":"2017-06-29T19:22:23Z"}
```

## <a name="visual-studio-sample-code"></a>Visual Studio ukázkový kód

Tady je Orchestrace jako jeden soubor jazyka C# v sadě Visual Studio projekt:

[!code-csharp[Main](~/samples-durable-functions/samples/precompiled/PhoneVerification.cs)]

## <a name="next-steps"></a>Další postup

Tato ukázka má jsme vám ukázali některé pokročilé funkce Durable Functions, zejména `WaitForExternalEvent` a `CreateTimer`. Už víte, jak tyto mohou být kombinovány s `Task.WaitAny` implementovat systém spolehlivé časového limitu, který je často užitečné pro interakci s skuteční lidé. Další informace o tom, jak používat Durable Functions načtením série článků, které nabízejí podrobné pokrytí konkrétní témata.

> [!div class="nextstepaction"]
> [Přejít na první článek v sérii](durable-functions-bindings.md)
