---
title: Azure Functions spolehlivé zpracování událostí
description: Vyhněte se chybějícím zprávám centra událostí v Azure Functions
author: craigshoemaker
ms.topic: conceptual
ms.date: 10/01/2020
ms.author: cshoe
ms.openlocfilehash: fd784bb184ff9432efc569ac9fd40de93eec0b53
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93379583"
---
# <a name="azure-functions-reliable-event-processing"></a>Azure Functions spolehlivé zpracování událostí

Zpracování událostí je jedním z nejběžnějších scénářů přidružených k architektuře bez serveru. Tento článek popisuje, jak vytvořit spolehlivý procesor zpráv s Azure Functions, abyste se vyhnuli ztrátě zpráv.

## <a name="challenges-of-event-streams-in-distributed-systems"></a>Výzvy k datovým proudům událostí v distribuovaných systémech

Vezměte v úvahu systém, který odesílá události s konstantní frekvencí 100 událostí za sekundu. V této míře může více instancí paralelních funkcí během několika minut spotřebovat příchozí události 100 každou sekundu.

Je ale možné, že existují následující méně optimální podmínky:

- Co když Vydavatel události pošle poškozenou událost?
- Co když instance Functions narazí na neošetřené výjimky?
- Co když systém pro příjem dat přejde do režimu offline?

Jak se tyto situace zpracovávají při zachování propustnosti aplikace?

Díky frontám se spolehlivé zasílání zpráv přináší přirozeně. Když se spáruje s triggerem Functions, funkce vytvoří zámek ve zprávě fronty. Pokud se zpracování nepovede, je zámek uvolněn, aby bylo možné opakovat zpracování jiné instance. Zpracování poté pokračuje, dokud není zpráva úspěšně vyhodnocena, nebo přidána do fronty poškození.

I když může jedna zpráva fronty zůstat v cyklu opakování, ostatní paralelní spouštění budou dál muset vyřadit zbývající zprávy do fronty. Výsledkem je, že celková propustnost zůstává do značné míry neovlivněna jednou chybnou zprávou. Fronty úložiště ale nezaručují řazení a nejsou optimalizované pro požadavky na vysokou propustnost, které vyžaduje Event Hubs.

Naproti tomu Azure Event Hubs nezahrnuje pojem zamykání. Aby bylo možné povolit funkce jako vysokou propustnost, více uživatelů a možnosti opakovaného přehrávání, Event Hubs události se chovají podobně jako přehrávač videa. Události jsou čteny z jednoho bodu v datovém proudu na oddíl. Z ukazatele můžete číst vpřed nebo zpět z tohoto umístění, ale je nutné zvolit, zda chcete přesunout události ke zpracování.

Pokud se v datovém proudu objeví chyby, pokud se rozhodnete zachovat ukazatel ve stejném místě, bude zpracování událostí zablokované, dokud nebude ukazatel pokročilý. Jinými slovy, pokud je ukazatel zastaven, aby se zabývat problémy, které zpracovávají jednu událost, nezpracované události začnou piling.

Azure Functions se vyhnete zablokování tím, že přechází ukazatel datového proudu bez ohledu na úspěch nebo neúspěch. Vzhledem k tomu, že se ukazatel stále mění, vaše funkce potřebují patřičně řešit chyby.

## <a name="how-azure-functions-consumes-event-hubs-events"></a>Jak Azure Functions spotřebovává Event Hubs události

Azure Functions spotřebovává události centra událostí při procházení následujících kroků:

1. V Azure Storage pro každý oddíl centra událostí se vytvoří a uloží ukazatel.
2. Při přijetí nových zpráv (ve výchozím nastavení v dávce) se hostitel pokusí aktivovat funkci s dávkou zpráv.
3. Pokud funkce dokončí provádění (s výjimkou nebo bez ní), přesun ukazatele a kontrolní bod se uloží do účtu úložiště.
4. Pokud podmínky brání dokončení provádění funkce, hostitel neprojde ukazatelem průběhu. Pokud ukazatel není pokročilý, pak později zkontroluje, jestli zpracovává stejné zprávy.
5. Opakování kroků 2 – 4

Toto chování odhalí několik důležitých bodů:

- *Neošetřené výjimky mohou způsobit ztrátu zpráv.* Spuštění, které způsobí výjimku, bude pokračovat v průběhu ukazatele.  Nastavení [zásady opakování](./functions-bindings-error-pages.md#retry-policies-preview) bude zpozdit průběh ukazatele, dokud nebude vyhodnocena celá zásada opakování.
- *Funkce garantuje alespoň jedno doručení.* Váš kód a závislé systémy budou pravděpodobně potřebovat [účet pro skutečnost, že stejnou zprávu lze přijmout dvakrát](./functions-idempotent.md).

## <a name="handling-exceptions"></a>Zpracování výjimek

Obecně platí, že každá funkce by měla obsahovat [blok try/catch](./functions-bindings-error-pages.md) na nejvyšší úrovni kódu. Konkrétně všechny funkce, které spotřebovávají Event Hubs události, by měly mít `catch` blok. Tímto způsobem, když je vyvolána výjimka, blok catch zpracovává chybu před průběhem ukazatele.

### <a name="retry-mechanisms-and-policies"></a>Mechanismy a zásady opakování

Některé výjimky jsou přechodným charakterem a po opakovaném pokusu o operaci později se nezobrazují. Z tohoto důvodu je prvním krokem vždy opakování operace.  Můžete využít [zásady opakování](./functions-bindings-error-pages.md#retry-policies-preview) aplikace Function App nebo vytvořit logiku opakování v rámci provádění funkce.

Zavedení chování pro zpracování chyb do vašich funkcí vám umožní definovat základní i pokročilé zásady opakování. Můžete například implementovat zásadu, která následuje za pracovním postupem, a to podle následujících pravidel:

- Zkuste vložit zprávu třikrát (může se jednat o prodlevu mezi opakovanými pokusy).
- Pokud případný výsledek všech opakovaných pokusů selže, přidejte do fronty zprávu, aby zpracování mohlo pokračovat na datovém proudu.
- Poškozené nebo nezpracované zprávy jsou následně zpracovávány později.

> [!NOTE]
> [Polly](https://github.com/App-vNext/Polly) je příklad odolnosti a knihovny pro zpracování s přechodnou chybou pro aplikace v jazyce C#.

## <a name="non-exception-errors"></a>Chyby bez výjimky

K nějakým problémům dojde i v případě, že chyba není k dispozici. Zvažte například selhání, ke kterému dojde v průběhu provádění. V takovém případě, pokud funkce nedokončí provádění, ukazatel posunu nikdy nepostupuje. Pokud ukazatel nebude pokračovat, bude jakákoli instance, která se spustí po neúspěšném spuštění, nadále přečetla stejné zprávy. Tato situace poskytuje záruku "nejméně jednou".

Záruka, že se každá zpráva zpracovává aspoň jednou, znamená, že některé zprávy mohou být zpracovány více než jednou. Vaše aplikace Function App musí tuto možnost znát a musí být postaveny na [principech idempotence](./functions-idempotent.md).

## <a name="stop-and-restart-execution"></a>Zastavení a restartování provádění

I když může být přijatelné několik chyb, co když vaše aplikace dochází k významným chybám? Je možné, že budete chtít zastavit aktivaci na události, dokud systém nedosáhne stavu v pořádku. Možnost pozastavit zpracování se často dosahuje pomocí vzoru pro přestávku okruhu. Vzorek přerušení obvodu umožňuje aplikaci přerušit okruh procesu události a později ho obnovit.

K implementaci přerušení okruhu v procesu události se vyžadují dvě části:

- Sdílený stav napříč všemi instancemi, který sleduje a monitoruje stav okruhu
- Hlavní proces, který může spravovat stav okruhu (otevřené nebo uzavřené)

Podrobnosti implementace se můžou lišit, ale sdílet stav mezi instancemi, které potřebujete úložného mechanismu. Můžete se rozhodnout uložit stav do Azure Storage, mezipaměť Redis nebo jakýkoli jiný účet, který je přístupný pro kolekci funkcí.

[Azure Logic Apps](../logic-apps/logic-apps-overview.md) nebo [trvalé funkce](./durable/durable-functions-overview.md) jsou přirozenou možností správy pracovního postupu a stavu okruhu. Další služby můžou fungovat stejně, ale v tomto příkladu se používají Logic Apps. Pomocí Logic Apps můžete pozastavit a restartovat provádění funkce a poskytnout tak ovládací prvek potřebný k implementaci vzorku pro přestávku okruhu.

### <a name="define-a-failure-threshold-across-instances"></a>Definice prahové hodnoty selhání napříč instancemi

Aby bylo možné přihlédnout k různým instancím, které zpracovávají události současně, je nutné zachovat sdílený externí stav pro monitorování stavu okruhu.

Pravidlo, které můžete použít k implementaci, může vyhovět těmto akcím:

- Pokud v rámci všech instancí existuje více než 100 případných chyb, přerušit okruh a zastavte spouštění nových zpráv.

Podrobnosti implementace se budou lišit podle vašich potřeb, ale obecně můžete vytvořit systém, který:

1. Protokoluje chyby do účtu úložiště (Azure Storage, Redis atd.).
1. Když se zaprotokoluje nové selhání, zkontrolujte počet vydaných položek a podívejte se, jestli se prahová hodnota splní (například více než 100 za posledních 30 sekund).
1. Pokud je prahová hodnota splněna, vygeneruje událost, která Azure Event Grid informuje systém o přerušení okruhu.

### <a name="managing-circuit-state-with-azure-logic-apps"></a>Správa stavu okruhu pomocí Azure Logic Apps

Následující popis představuje jeden ze způsobů, jak můžete vytvořit aplikaci logiky Azure pro zastavení zpracování aplikace Functions.

Azure Logic Apps přináší Integrované konektory k různým službám, funkce pro stavové orchestraci a je přirozenou volbou pro správu stavu okruhu. Po zjištění, že okruh potřebuje přerušit, můžete vytvořit aplikaci logiky, která implementuje následující pracovní postup:

1. Aktivace pracovního postupu Event Grid a zastavení funkce Azure (pomocí konektoru prostředků Azure)
1. Odeslání e-mailu s oznámením, který obsahuje možnost restartovat pracovní postup

Příjemce e-mailu může prozkoumat stav okruhu a v případě potřeby restartovat okruh přes odkaz v oznamovacím e-mailu. Když pracovní postup restartuje funkci, zprávy se zpracují z posledního kontrolního bodu centra událostí.

Při použití tohoto přístupu se neztratí žádné zprávy, všechny zprávy jsou zpracovávány v daném pořadí a okruh můžete v případě potřeby rozdělit na dlouhou dobu.

## <a name="resources"></a>Zdroje informací

- [Ukázky spolehlivých zpracování událostí](https://github.com/jeffhollan/functions-csharp-eventhub-ordered-processing)
- [Přerušení okruhu trvalých entit Azure](https://github.com/jeffhollan/functions-durable-actor-circuitbreaker)

## <a name="next-steps"></a>Další kroky

Další informace naleznete v následujících zdrojích:

- [Zpracování chyb Azure Functions](./functions-bindings-error-pages.md)
- [Automatizace změny velikosti nahraných obrázků s využitím služby Event Grid](../event-grid/resize-images-on-storage-blob-upload-event.md?toc=%2Fazure%2Fazure-functions%2Ftoc.json&tabs=dotnet)
- [Vytvoření funkce, která se integruje s Azure Logic Apps](./functions-twitter-email.md)
