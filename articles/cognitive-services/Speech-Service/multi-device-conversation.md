---
title: Konverzace na více zařízeních (náhled) – služba řeči
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 03/11/2020
ms.author: trbye
ms.openlocfilehash: 7c30ee2ef4a6ab0cd4241cac921a59eeadf5ce17
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81401051"
---
# <a name="what-is-multi-device-conversation-preview"></a>Co je konverzace s více zařízeními (náhled)?

**Konverzace na více zařízeních** usnadňuje vytvoření hlasové nebo textové konverzace mezi více klienty a koordinaci zpráv odeslaných mezi nimi.

Při **konverzaci na více zařízeních**můžete:

- Připojte více klientů ke stejné konverzaci a spravujte odesílání a přijímání zpráv mezi nimi.
- Snadno přepisujte zvuk z každého klienta a pošlete přepis ostatním s volitelným překladem.
- Snadno odesílejte textové zprávy mezi klienty s volitelným překladem.

Můžete vytvořit funkci nebo řešení, které funguje v celé řadě zařízení. Každé zařízení může nezávisle odesílat zprávy (přepisy zvuku nebo rychlé zprávy) do všech ostatních zařízení.

Zatímco [**přepis konverzace**](conversation-transcription.md) funguje na jednom zařízení s vícekanálovým mikrofonním polem, **konverzace s více zařízeními** je vhodná pro scénáře s více zařízeními, z nichž každý má jeden mikrofon.

>[!IMPORTANT]
> Konverzace na více zařízeních **nepodporuje** odesílání zvukových souborů mezi klienty: pouze přepis a/nebo překlad.

## <a name="key-features"></a>Klíčové funkce

- **Přepis v reálném čase** – Každý obdrží přepis konverzace, aby mohl text sledovat v reálném čase nebo jej uložit na později.
- **Překlad v reálném čase** – S více než 60 [podporovanými jazyky](language-support.md#text-languages) pro překlad textu mohou uživatelé překládat konverzaci do svého preferovaného jazyka.
- **Čitelné přepisy** - přepis a překlad jsou snadno sledovat, s interpunkcí a věty přestávky.
- **Hlasový nebo textový vstup** – Každý uživatel může mluvit nebo psát na svém vlastním zařízení v závislosti na možnostech jazykové podpory povolených pro vybraný jazyk účastníka. Viz [jazyková podpora](language-support.md#speech-to-text).
- **Přenos zpráv** – služba konverzace s více zařízeními bude distribuovat zprávy odeslané jedním klientem všem ostatním, v jazyce podle svého výběru.
- **Identifikace zprávy** – Každá zpráva, kterou uživatelé obdrží v konverzaci, bude označena přezdívkou uživatele, který ji odeslal.

## <a name="use-cases"></a>Případy použití

### <a name="lightweight-conversations"></a>Odlehčené konverzace

Vytvoření konverzace a připojení se k ní je snadné. Jeden uživatel bude fungovat jako "hostitel" a vytvoří konverzaci, která generuje náhodný pětipísmenný kód konverzace a QR kód. Všichni ostatní uživatelé se mohou připojit ke konverzaci zadáním kódu konverzace nebo skenováním QR kódu. 

Vzhledem k tomu, že se uživatelé připojují prostřednictvím kódu konverzace a nejsou povinni sdílet kontaktní informace, je snadné vytvářet rychlé konverzace na místě.

### <a name="inclusive-meetings"></a>Inkluzivní schůzky

Přepis a překlad v reálném čase mohou pomoci zpřístupnit konverzace lidem, kteří mluví různými jazyky a/nebo jsou neslyšící nebo nedoslýchaví. Každý člověk se také může aktivně účastnit konverzace tím, že mluví svým preferovaným jazykem nebo posílá rychlé zprávy.

### <a name="presentations"></a>Prezentace

Můžete také poskytnout titulky pro prezentace a přednášky jak na obrazovce, tak na vlastních zařízeních členů publika. Jakmile se posluchači připojí ke kódu konverzace, uvidí přepis v preferovaném jazyce na vlastním zařízení.

> [!NOTE]
> Chcete-li zobrazit příklad, podívejte se na [překladač prezentací](https://www.microsoft.com/translator/apps/presentation-translator/), doplněk aplikace PowerPoint, který používá konverzační službu pro více zařízení. Můžete si ho stáhnout [tady](https://www.microsoft.com/download/details.aspx?id=55024).

## <a name="how-it-works"></a>Jak to funguje

Všichni klienti budou používat sadu Speech SDK k vytvoření konverzace nebo připojení ke konverzaci. Sada Speech SDK spolupracuje se službou konverzace s více zařízeními, která spravuje životnost konverzace, včetně seznamu účastníků, vybraných jazyků každého klienta a odeslaných zpráv.  

Každý klient může odesílat zvukové nebo rychlé zprávy. Služba bude používat rozpoznávání řeči k převodu zvuku na text a odesílání rychlých zpráv tak, jak jsou. Pokud klienti zvolí různé jazyky, bude služba překládat všechny zprávy do zadaného jazyka každého klienta.

![Diagram přehledu konverzace na více zařízeních](media/scenarios/multi-device-conversation.png)

## <a name="overview-of-conversation-host-and-participant"></a>Přehled konverzace, hostitele a účastníka

**Konverzace** je relace, ke které se může připojit jeden uživatel. Všichni klienti se připojují ke konverzaci pomocí **pětipísmenového kódu konverzace**.

Každá konverzace vytvoří metadata, která zahrnují:
-    Časová razítka, kdy konverzace začala a skončila
-    Seznam všech účastníků konverzace, která zahrnuje vybranou přezdívku každého uživatele a primární jazyk pro zadávání řeči nebo textu.


V konverzaci jsou dva typy uživatelů: **hostitel** a **účastník**.

Hostitel **host** je uživatel, který zahájí konverzaci a který funguje jako správce této konverzace.
- Každá konverzace může mít pouze jednoho hostitele.
- Hostitel musí být připojen ke konverzaci po dobu trvání konverzace. Pokud hostitel opustí konverzaci, konverzace skončí pro všechny ostatní účastníky.
- Hostitel má několik dalších ovládacích prvků pro správu konverzace: 
    - Uzamknout konverzaci – zabrání dalším účastníkům připojit se
    - Ztlumit všechny účastníky – zabrání ostatním účastníkům v odesílání zpráv do konverzace, ať už přepisovaných z řeči nebo rychlých zpráv
    - Ztlumit jednotlivé účastníky
    - Zrušení ztlumení všech účastníků
    - Zrušení ztlumení jednotlivých účastníků

**Účastník** je uživatel, který se připojí ke konverzaci.
- Účastník může kdykoli opustit stejnou konverzaci a znovu se k ní připojit, aniž by ukončil konverzaci pro ostatní účastníky.
- Účastníci nemohou uzamknout konverzaci nebo ztlumit/zrušit ztlumení ostatních osob.

> [!NOTE]
> Každá konverzace může mít až 100 účastníků, z nichž 10 může být současně mluvit v daném okamžiku.

## <a name="language-support"></a>Podpora jazyků

Při vytváření konverzace nebo při popřihlášení musí každý uživatel zvolit **primární jazyk**: jazyk, ve kterém bude mluvit a odesílat rychlé zprávy, a také jazyk, ve kterém se jim budou zoašit zprávy ostatních uživatelů.

Existují dva druhy jazyků: **pouze převod řeči na text** a **text**:
- Pokud uživatel zvolí jazyk **převodu řeči na text** jako svůj primární jazyk, bude moci v konverzaci používat hlasivky i texty.

- Pokud uživatel zvolí jazyk **pouze text,** bude moci používat pouze zadávání textu a odesílat rychlé zprávy v konverzaci. Jazyky pouze pro text jsou jazyky, které jsou podporovány pro překlad textu, ale ne pro převod řeči na text. Dostupné jazyky si můžete prohlédnout na stránce [podpory jazyků.](supported-languages.md)

Kromě primárního jazyka může každý účastník také určit další jazyky pro překlad konverzace.

Níže je uveden přehled toho, co bude uživatel moci dělat v konverzaci s více zařízeními na základě zvoleného primárního jazyka.


| Co může uživatel v konverzaci dělat | Převod řeči na text | Pouze text |
|-----------------------------------|----------------|------|
| Použití hlasového vstupu | ✔️ | ❌ |
| Odesílání rychlých zpráv | ✔️ | ✔️ |
| Přeložit konverzaci | ✔️ | ✔️ |

> [!NOTE]
> Seznamy dostupných jazyků převodu řeči na text a překlad textu naleznete v [podporovaných jazycích](supported-languages.md).



## <a name="next-steps"></a>Další kroky

> [!div class="nextstepaction"]
> [Překlad konverzací v reálném čase](quickstarts/multi-device-conversation.md)
