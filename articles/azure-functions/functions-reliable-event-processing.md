---
title: Spolehlivé zpracování událostí Azure Functions
description: Vyhněte se chybějícím zprávám centra událostí ve funkcích Azure
author: craigshoemaker
ms.topic: conceptual
ms.date: 09/12/2019
ms.author: cshoe
ms.openlocfilehash: e4f35495d8a01146068cffb9159c29c46c3c0d29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75561863"
---
# <a name="azure-functions-reliable-event-processing"></a>Spolehlivé zpracování událostí Azure Functions

Zpracování událostí je jedním z nejběžnějších scénářů spojených s architekturou bez serveru. Tento článek popisuje, jak vytvořit spolehlivý procesor zpráv s Funkcemi Azure, aby nedošlo ke ztrátě zpráv.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Výzvy toků událostí v distribuovaných systémech

Zvažte systém, který odesílá události konstantní rychlostí 100 událostí za sekundu. Při této rychlosti během několika minut více paralelních functions instance může spotřebovat příchozí 100 událostí každou sekundu.

Nicméně, některý z následujících méně optimální podmínky jsou možné:

- Co když vydavatel události odešle poškozenou událost?
- Co když vaše instance Functions narazí na neošetřené výjimky?
- Co když se navazující systém přepne do poproudu?

Jak zvládáte tyto situace při zachování propustnost vaší aplikace?

Díky frontám přichází spolehlivé zasílání zpráv přirozeně. Při spárování s function trigger, funkce vytvoří zámek na zprávu fronty. Pokud se zpracování nezdaří, zámek je uvolněna povolit jiné instance opakovat zpracování. Zpracování pak pokračuje, dokud je zpráva úspěšně vyhodnocena nebo je přidána do fronty jedů.

I v případě, že jedna zpráva fronty může zůstat v cyklu opakování, ostatní paralelní spuštění nadále zachovat dequeueing zbývající zprávy. Výsledkem je, že celková propustnost zůstává do značné míry nedotčena jednou chybnou zprávou. Fronty úložiště však nezaručují řazení a nejsou optimalizovány pro vysoké požadavky na propustnost vyžadované event huby.

Naproti tomu Azure Event Hubs neobsahuje koncept uzamčení. Události event hubů se chovají spíše jako přehrávač videa, aby bylo možné povolit funkce, jako je vysoká propustnost, více skupin spotřebitelů a schopnost přehrání. Události jsou čteny z jednoho bodu v datovém proudu na oddíl. Z ukazatele můžete číst dopředu nebo dozadu z tohoto umístění, ale musíte se rozhodnout přesunout ukazatel pro události ke zpracování.

Pokud dojde k chybám v datovém proudu, pokud se rozhodnete zachovat ukazatel na stejném místě, zpracování událostí je blokován, dokud ukazatel je upřesnit. Jinými slovy, pokud je ukazatel zastaven, aby se vypořádal s problémy při zpracování jedné události, začnou se hromadit nezpracované události.

Azure Functions zabraňuje zablokování tím, že posouvá ukazatel datového proudu bez ohledu na úspěch nebo selhání. Vzhledem k tomu, že ukazatel stále postupuje, vaše funkce je třeba odpovídajícím způsobem řešit selhání.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Jak Funkce Azure využívá události centra událostí

Funkce Azure spotřebovává události centra událostí při procházení následující kroky:

1. Ukazatel se vytvoří a trvalé ve službě Azure Storage pro každý oddíl centra událostí.
2. Při přijetí nových zpráv (ve výchozím nastavení v dávce) se hostitel pokusí aktivovat funkci s dávkou zpráv.
3. Pokud funkce dokončí spuštění (s nebo bez výjimky) ukazatel posune a kontrolní bod je uložen do účtu úložiště.
4. Pokud podmínky zabránit dokončení spuštění funkce, hostitel se nezdaří k pokroku ukazatele. Pokud ukazatel není upřesňován, pozdější kontroly skončí zpracování stejné zprávy.
5. Opakování kroků 2–4

Toto chování odhaluje několik důležitých bodů:

- *Neošetřené výjimky mohou způsobit ztrátu zpráv.* Spuštění, které mají za následek výjimku bude pokračovat v průběhu ukazatele.
- *Funkce zaručují alespoň jednou dodání.* Váš kód a závislé systémy může být nutné, aby [účet za skutečnost, že stejná zpráva mohla být přijata dvakrát](./functions-idempotent.md).

## <a name="handling-exceptions"></a>Zpracování výjimek

Obecně platí, že každá funkce by měla obsahovat [blok try/catch](./functions-bindings-error-pages.md) na nejvyšší úrovni kódu. Konkrétně všechny funkce, které spotřebovávají `catch` události Event Hubs by měl mít blok. Tímto způsobem při vyvolání výjimky blok catch zpracovává chybu před ukazatelem postupuje.

### <a name="retry-mechanisms-and-policies"></a>Mechanismy a zásady opakování

Některé výjimky jsou přechodné povahy a nezobrazují se znovu, když se o chvíli později znovu pokusí o operaci. To je důvod, proč prvním krokem je vždy opakovat operaci. Pravidla zpracování opakování můžete napsat sami, ale jsou tak běžná, že je k dispozici řada nástrojů. Pomocí těchto knihoven můžete definovat robustní zásady opakování, které mohou také pomoci zachovat pořadí zpracování.

Zavedení knihoven pro zpracování chyb do vašich funkcí umožňuje definovat základní i pokročilé zásady opakování. Můžete například implementovat zásadu, která se řídí pracovním postupem ilustrovanými následujícími pravidly:

- Pokuste se vložit zprávu třikrát (potenciálně se zpožděním mezi opakováním).
- Pokud je konečný výsledek všech opakování selhání, přidejte zprávu do fronty, takže zpracování může pokračovat v datovém proudu.
- Poškozené nebo nezpracované zprávy jsou pak zpracovány později.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) je příkladem odolnosti a přechodně zpracování chyb knihovny pro aplikace Jazyka C#.

Při práci s předdodržujíc knihovny tříd c# [filtry výjimek](https://docs.microsoft.com/dotnet/csharp/language-reference/keywords/try-catch) umožňují spustit kód vždy, když dojde k neošetřené výjimce.

Ukázky, které ukazují, jak používat filtry výjimek jsou k dispozici v azure [webjobs sdk](https://github.com/Azure/azure-webjobs-sdk/wiki) repo.

## <a name="non-exception-errors"></a>Chyby bez výjimky

Některé problémy vznikají i v případě, že není k dispozici chyba. Zvažte například selhání, ke kterému dochází uprostřed spuštění. V tomto případě pokud funkce nedokončí spuštění, ukazatel posunu není nikdy pokročila. Pokud ukazatel není předem, pak všechny instance, která běží po neúspěšném spuštění nadále číst stejné zprávy. Tato situace poskytuje záruku "alespoň jednou".

Ujištění, že každá zpráva je zpracována alespoň jednou znamená, že některé zprávy mohou být zpracovány více než jednou. Vaše funkce aplikace musí být vědomi této možnosti a musí být postaveny na [principech idempotence](./functions-idempotent.md).

## <a name="stop-and-restart-execution"></a>Zastavení a restartování provádění

I když několik chyb může být přijatelné, co když vaše aplikace dojde k významným selháním? Můžete chtít zastavit aktivaci událostí, dokud systém nedosáhne stavu v pořádku. Mít možnost pozastavit zpracování je často dosaženo se vzorem jističe. Vzor jističe umožňuje vaší aplikaci "přerušit okruh" procesu události a pokračovat později.

K implementaci jističe v procesu události jsou vyžadovány dva kusy:

- Sdílený stav ve všech instancích pro sledování a sledování stavu okruhu
- Hlavní proces, který může spravovat stav obvodu (otevřený nebo uzavřený)

Podrobnosti implementace se mohou lišit, ale chcete-li sdílet stav mezi instancemi, potřebujete mechanismus úložiště. Můžete se rozhodnout ukládat stav ve službě Azure Storage, mezipaměť Redis nebo jakýkoli jiný účet, který je přístupný z kolekce funkcí.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) nebo [trvalé entity](./durable/durable-functions-overview.md) jsou přirozené vhodné pro správu pracovního postupu a stavu okruhu. Ostatní služby mohou fungovat stejně dobře, ale aplikace logiky se používají pro tento příklad. Pomocí aplikace logiky můžete pozastavit a restartovat spuštění funkce, která vám poskytne ovládací prvek potřebný k implementaci vzoru jističe.

### <a name="define-a-failure-threshold-across-instances"></a>Definování prahové hodnoty selhání napříč instancemi

Chcete-li účet pro více instancí zpracování událostí současně, trvalé sdílené externí stav je potřeba sledovat stav okruhu.

Pravidlo, které můžete implementovat, může vynucovat, že:

- Pokud existuje více než 100 případné selhání během 30 sekund ve všech případech, pak přerušit okruh a zastavit spouštění na nové zprávy.

Podrobnosti implementace se budou lišit podle vašich potřeb, ale obecně můžete vytvořit systém, který:

1. Protokolovat selhání účtu úložiště (Azure Storage, Redis atd.)
1. Při nové selhání je zaznamenána, zkontrolujte rolling count zobrazíte, pokud je splněna prahová hodnota (například více než 100 v posledních 30 sekundách).
1. Pokud je splněna prahová hodnota, vyzařujte událost do služby Azure Event Grid, která systému říká, aby přerušoval okruh.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Správa stavu okruhu pomocí aplikací Azure Logic Apps

Následující popis zvýrazňuje jeden způsob, jak můžete vytvořit aplikaci Logika Azure zastavit aplikace funkce ze zpracování.

Azure Logic Apps je dodáván s integrovanými konektory pro různé služby, funkce stavové orchestrations a je přirozenou volbou pro správu stavu okruhu. Po zjištění, že se musí přerušit okruh, můžete vytvořit aplikaci logiky pro implementaci následujícího pracovního postupu:

1. Aktivace pracovního postupu Event Grid a zastavení funkce Azure (pomocí konektoru azure resource connector)
1. Odeslání e-mailu s oznámením, který obsahuje možnost restartování pracovního postupu

Příjemce e-mailu může prozkoumat stav obvodu a v případě potřeby restartovat okruh pomocí odkazu v e-mailu s oznámením. Jako pracovní postup restartuje funkci, zprávy jsou zpracovány z posledního kontrolního bodu centra událostí.

Pomocí tohoto přístupu jsou ztraceny žádné zprávy, všechny zprávy jsou zpracovány v pořadí a můžete přerušit okruh tak dlouho, jak je to nutné.

## <a name="resources"></a>Prostředky

- [Spolehlivé vzorky zpracování událostí](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Azure Durable Functions Jistič](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Další kroky

Další informace najdete v následujících materiálech:

- [Zpracování chyb Azure Functions](./functions-bindings-error-pages.md)
- [Automatizace změny velikosti nahraných obrázků s využitím služby Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Vytvoření funkce, která se integruje s Azure Logic Apps](./functions-twitter-email.md)
