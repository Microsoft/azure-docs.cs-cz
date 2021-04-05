---
title: Konverzace s více zařízeními (Preview) – služba Speech
titleSuffix: Azure Cognitive Services
description: Konverzace s více zařízeními usnadňují vytváření hlasu nebo textové konverzace mezi několika klienty a sladění zpráv, které jsou mezi nimi posílány.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 7b38bc390b28788003c2de757f45e8ecdfd5c89a
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "98932419"
---
# <a name="what-is-multi-device-conversation-preview"></a>Co je konverzace s více zařízeními (Preview)?

**Konverzace s více zařízeními** usnadňují vytváření hlasových nebo textových konverzací mezi více klienty a koordinaci zpráv odesílaných mezi nimi.

**Konverzace s více zařízeními** vám umožní:

- Připojit více klientů ke stejné konverzaci a spravovat odesílání a přijímání zpráv mezi nimi.
- Snadno přepisovat zvuk od každého klienta a předávejte přepis ostatním uživatelům s volitelným překladem.
- Snadné odesílání textových zpráv mezi klienty s volitelným překladem.

Můžete vytvořit funkci nebo řešení, které funguje v poli zařízení. Každé zařízení může nezávisle odesílat zprávy (přepisovat zvukové a rychlé zprávy) na všechna ostatní zařízení.

Vzhledem k tomu, že [**přepis konverzace**](conversation-transcription.md) funguje na jednom zařízení s vícekanálovým polem mikrofonu, **konverzace s více** zařízeními jsou vhodné pro scénáře s více zařízeními, z nichž každý má jeden mikrofon.

>[!IMPORTANT]
> Konverzace s více **zařízeními nepodporuje** Posílání zvukových souborů mezi klienty: jenom přepis a/nebo překlad.

## <a name="key-features"></a>Klíčové funkce

- **Přepis v reálném čase** – všichni obdrží přepis konverzace, takže můžou sledovat text v reálném čase nebo ho uložit pro pozdější účely.
- **Překlad v reálném čase** – s více než 70 [podporovanými jazyky](language-support.md#text-languages) pro překlad textu můžou uživatelé překládat konverzaci na jejich preferované jazyky.
- **Čitelné přepisy** – přepis a překlad se snadno sleduje, a to s využitím interpunkce a konců vět.
- **Hlasový nebo textový vstup** – každý uživatel může mluvit nebo psát na svém vlastním zařízení, v závislosti na podporovaných možnostech jazyka pro vybraný jazyk účastníka. Další informace najdete v tématu [Podpora jazyků](language-support.md#speech-to-text).
- **Předávání zpráv** – služba konverzace s více zařízeními bude distribuovat zprávy odesílané jedním klientem do všech ostatních, v jazycích podle jejich výběru.
- **Identifikace zprávy** – každá zpráva, kterou uživatelé obdrží v konverzaci, bude označená přezdívkou uživatele, který ho odeslal.

## <a name="use-cases"></a>Případy použití

### <a name="lightweight-conversations"></a>Odlehčené konverzace

Vytvoření konverzace a připojení k ní je snadné. Jeden uživatel bude fungovat jako hostitel a vytvoří konverzaci, která vytvoří náhodný kód konverzace s pěti písmeny a kód QR. Všichni ostatní uživatelé se můžou ke konverzaci připojit zadáním kódu konverzace nebo kontrolou kódu QR. 

Vzhledem k tomu, že se uživatelé připojují prostřednictvím kódu konverzace a nevyžadují, aby mohli sdílet kontaktní údaje, je snadné vytvořit rychlá konverzace na místě.

### <a name="inclusive-meetings"></a>Uzavřené schůzky

Přepis a překlady v reálném čase můžou přispět k usnadnění přístupu k konverzacím pro lidi, kteří komunikují s různými jazyky nebo jsou neslyšící nebo sluchové. Každá osoba se může také aktivně zúčastnit konverzace, když mluví jejich preferovaný jazyk nebo odesílá rychlé zprávy.

### <a name="presentations"></a>Prezentace

Můžete také zadat titulky pro prezentace a přednášky na obrazovkách i na vlastních zařízeních členů cílové skupiny. Poté, co se cílová skupina připojí k kódu konverzace, uvidí přepis v upřednostňovaném jazyce na vlastním zařízení.

## <a name="how-it-works"></a>Jak to funguje

Všichni klienti použijí sadu Speech SDK k vytvoření nebo připojení konverzace. Sada Speech SDK spolupracuje se službou konverzace s více zařízeními, která spravuje životnost konverzace, včetně seznamu účastníků, zvolených jazyků a zpráv každého klienta.  

Každý klient může posílat zvukové nebo rychlé zprávy. Služba bude používat rozpoznávání řeči k převodu zvuku na text a posílání rychlých zpráv tak, jak jsou. Pokud klienti zvolí jiné jazyky, služba převede všechny zprávy na konkrétní jazyky každého klienta.

![Diagram s přehledem konverzací s více zařízeními](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Přehled konverzace, hostitele a účastníka

**Konverzace** je relace, kterou jeden uživatel spustí, aby se připojili k ostatním zúčastněným uživatelům. Všichni klienti se připojují k konverzacím pomocí **kódu konverzace** s pěti písmeny.

Každá konverzace vytvoří metadata, která zahrnují:
-    Časová razítka při zahájení a ukončení konverzace
-    Seznam všech účastníků v konverzaci, včetně přezdívky jednotlivých uživatelů a jejich primárního jazyka pro hlasový vstup nebo textové zadání.


V konverzaci existují dva typy uživatelů:  **hostitel** a **účastník**.

**Hostitel** je uživatel, který spouští konverzaci a který funguje jako správce dané konverzace.
- Každá konverzace může mít jenom jednoho hostitele.
- Hostitel musí být připojen ke konverzaci po dobu trvání konverzace. Pokud hostitel opustí konverzaci, konverzace skončí pro všechny ostatní účastníky.
- Hostitel má několik dalších ovládacích prvků pro správu konverzace: 
    - Uzamknout konverzaci – zabránit dalším účastníkům v přidávání
    - Ztlumení všech účastníků – zabrání ostatním účastníkům v posílání zpráv do konverzace, ať už přepisu z řeči nebo rychlých zpráv.
    - Ztlumit jednotlivé účastníky
    - Zrušit ztlumení všech účastníků
    - Zrušit ztlumení jednotlivých účastníků

**Účastník** je uživatel, který se připojí ke konverzaci.
- Účastník může stejnou konverzaci opustit a znovu se připojit, aniž by se musela ukončit konverzace pro ostatní účastníky.
- Účastníci nemůžou konverzaci uzamknout nebo ztlumit nebo zrušit ztlumení ostatních

> [!NOTE]
> Každá konverzace může mít až 100 účastníků, z nichž 10 může být v libovolnou dobu hovořit.

## <a name="language-support"></a>Podpora jazyků

Při vytváření nebo spojování konverzace musí každý uživatel zvolit **primární jazyk**: jazyk, ve kterém budou mluvit a odesílat rychlé zprávy, a také jazyk, ve kterém uvidí zprávy jiných uživatelů.

Existují dva druhy jazyků: **Převod řeči na text** a **text**:
- Pokud uživatel zvolí jazyk **řeči na text** jako svůj primární jazyk, bude moci v konverzaci použít hlasový vstup a text.

- Pokud uživatel zvolí jazyk **pouze text** , bude moci používat textové zadání a odesílat rychlé zprávy v konverzaci. Jazyky pouze s textem jsou jazyky, které jsou podporovány pro překlad textu, ale nikoli pro převod řeči na text. Dostupné jazyky můžete zobrazit na stránce [Podpora jazyků](./language-support.md) .

Od jejich primárního jazyka může každý účastník také určit další jazyky pro překlad konverzace.

Níže je uveden přehled toho, co uživatel bude moci dělat v konverzaci s více zařízeními na základě zvoleného primárního jazyka.


| Co může uživatel dělat v konverzaci | Převod řeči na text | Pouze text |
|-----------------------------------|----------------|------|
| Použití vstupu řeči | ✔️ | ❌ |
| Odesílání rychlých zpráv | ✔️ | ✔️ |
| Posunutí konverzace | ✔️ | ✔️ |

> [!NOTE]
> Seznam dostupných jazyků pro převod řeči na text a text naleznete v tématu [podporované jazyky](./language-support.md).



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Překlad konverzací v reálném čase](quickstarts/multi-device-conversation.md)