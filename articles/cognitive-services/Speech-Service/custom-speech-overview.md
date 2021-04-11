---
title: Přehled Custom Speech – služba Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech je sada online nástrojů, která vám umožní vyhodnotit a vylepšit přesnost řeči na text v aplikacích, nástrojích a produktech od Microsoftu.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.custom: contperf-fy21q2; references_regions
ms.openlocfilehash: 5ffae530bcd8a7274b4b75c447591cf619012661
ms.sourcegitcommit: 77d7639e83c6d8eb6c2ce805b6130ff9c73e5d29
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2021
ms.locfileid: "106387056"
---
# <a name="what-is-custom-speech"></a>Co je Custom Speech?

[Custom Speech](https://aka.ms/customspeech) je sada nástrojů založených na uživatelském rozhraní, které vám umožní vyhodnotit a vylepšit přesnost řeči na text pro vaše aplikace a produkty od Microsoftu. Vše, co bude trvat, je několik testovací zvukové soubory. Podle odkazů v tomto článku můžete začít vytvářet vlastní prostředí pro práci s mluveným textem.

## <a name="whats-in-custom-speech"></a>Co je v Custom Speech?

Abyste mohli provádět cokoli s Custom Speech, budete potřebovat účet Azure a předplatné služby Speech. Po použití účtu můžete data využít, naučit a testovat vaše modely, kontrolovat kvalitu rozpoznávání, hodnotit přesnost a nakonec nasazovat a používat vlastní model řeči na text.

Tento diagram zvýrazňuje části, které tvoří [Custom Speech oblasti sady Speech Studio](https://aka.ms/customspeech). Pomocí odkazů níže můžete získat další informace o jednotlivých krocích.

![Diagram, který zvýrazní komponenty, které tvoří Custom Speech oblasti sady Speech Studio.](./media/custom-speech/custom-speech-overview.png)

1. [Přihlaste se k odběru a vytvořte projekt](#set-up-your-azure-account). Vytvořte účet Azure a přihlaste se k odběru služby Speech. Toto jednotné předplatné vám umožní přístup k převodu řeči na text, převod textu na řeč, rozpoznávání řeči a [rozpoznávání](https://speech.microsoft.com/customspeech)řeči. Pak pomocí svého předplatného služby Speech vytvořte svůj první Custom Speech projekt.

1. [Nahrajte testovací data](./how-to-custom-speech-test-and-train.md). Nahrajte testovací data (zvukové soubory), abyste v aplikaci, nástrojích a produktech vyhodnotili nabídku Microsoft Speech to-text pro vaše aplikace, nástroje a produkty.

1. [Kontrola kvality rozpoznávání](how-to-custom-speech-inspect-data.md). Pomocí sady [Speech Studio](https://speech.microsoft.com/customspeech) můžete nahrajte nahraný zvuk a zkontrolovat kvalitu rozpoznávání řeči vašich testovacích dat. Kvantitativní měření najdete v tématu [Kontrola dat](how-to-custom-speech-inspect-data.md).

1. [Vyhodnotit a zlepšit přesnost](how-to-custom-speech-evaluate-data.md). Vyhodnotit a vylepšit přesnost modelu řeči na text. [Rozpoznávání řeči v studiu](https://speech.microsoft.com/customspeech) vám nabídne *Četnost chyb slov*, kterou můžete použít k určení, jestli je potřeba další školení. Pokud jste spokojeni s přesností, můžete použít rozhraní API služby Speech přímo. Pokud chcete zlepšit přesnost relativních průměrů 5 až 20%, použijte na portálu kartu **školení** k nahrání dalších školicích dat, jako jsou přepisy popisků a související text.

1. [Výuka a nasazení modelu](how-to-custom-speech-train-model.md) Vylepšete přesnost vašeho modelu řeči na text tím, že zadáte psané přepisy (10 až 1 000 hodin) a související text (<200 MB) spolu s daty testovacího testu. Tato data pomáhají naučit model řeči na text. Po školení proveďte test. Pokud jste s výsledkem spokojeni, můžete model nasadit do vlastního koncového bodu.

## <a name="set-up-your-azure-account"></a>Nastavení účtu Azure

Musíte mít účet Azure a předplatné služby Speech ještě předtím, než budete moct pomocí nástroje [Speech Studio](https://speech.microsoft.com/customspeech) vytvořit vlastní model. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Ujistěte se prosím, že jste vytvořili předplatné Standard (S0). Bezplatné předplatné (F0) se nepodporuje.

Pokud máte v úmyslu vytvořit vlastní model se **zvukovými daty**, vyberte jednu z následujících oblastí, která má vyhrazený hardware k dispozici pro školení. Tím se zkrátí doba potřebná k výuce modelu a umožní vám využít více zvuků pro školení. V těchto oblastech bude služba řeči používat až 20 hodin zvukového školení. v ostatních oblastech bude používat jenom až 8 hodin.

* Austrálie – východ
* Střední Kanada
* Indie – střed
* East US
* USA – východ 2
* USA – středosever
* Severní Evropa
* Středojižní USA
* Southeast Asia
* Spojené království – jih
* USA (Gov) – Arizona
* USA (Gov) – Virginia
* West Europe
* Západní USA 2

Po vytvoření účtu Azure a předplatného služby Speech se budete muset přihlásit ke službě [Speech Studio](https://speech.microsoft.com/customspeech) a připojit své předplatné.

1. Přihlaste se ke službě [Speech Studio](https://aka.ms/custom-speech).
1. Vyberte předplatné, ve kterém potřebujete pracovat, a vytvořte projekt řeči.
1. Pokud chcete upravit své předplatné, vyberte v horní nabídce tlačítko ozubeného kola.

## <a name="how-to-create-a-project"></a>Jak vytvořit projekt

Obsah, který je podobný datům, modelům, testům a koncovým bodům, je uspořádán do *projektů* v aplikaci [Speech Studio](https://speech.microsoft.com/customspeech). Každý projekt je specifický pro doménu a zemi/jazyk. Můžete například vytvořit projekt pro volání Center, která používají angličtinu v USA.

Pokud chcete vytvořit svůj první projekt, vyberte **Převod řeči na text/vlastní řeč** a pak vyberte **Nový projekt**. Postupujte podle pokynů průvodce a vytvořte projekt. Po vytvoření projektu byste měli vidět čtyři karty: **data**, **testování**, **školení** a **nasazení**. Pomocí odkazů uvedených v [následujících krocích](#next-steps) se dozvíte, jak používat jednotlivé karty.

> [!IMPORTANT]
> Služba [Speech Studio](https://aka.ms/custom-speech) , která se dřív jmenovala jako Custom Speech Portal, se nedávno aktualizovala! Pokud jste vytvořili předchozí data, modely, testy a publikované koncové body na portálu CRIS.ai nebo pomocí rozhraní API, je potřeba vytvořit nový projekt na novém portálu pro připojení k těmto starým entitám.

## <a name="model-and-endpoint-lifecycle"></a>Životní cyklus modelu a koncového bodu

Starší modely obvykle jsou méně užitečné v průběhu času, protože nejnovější model obvykle má vyšší přesnost. Proto platí, že základní modely i vlastní modely a koncové body vytvořené prostřednictvím portálu podléhají vypršení platnosti po 1 roce pro úpravu a 2 roky pro dekódování. Podrobný popis najdete v článku o [životním cyklu modelu a koncového bodu](./how-to-custom-speech-model-and-endpoint-lifecycle.md) .

## <a name="next-steps"></a>Další kroky

* [Příprava a testování dat](./how-to-custom-speech-test-and-train.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnotit a vylepšit přesnost modelu](how-to-custom-speech-evaluate-data.md)
* [Trénování a nasazení modelu](how-to-custom-speech-train-model.md)
