---
title: Začínáme se službou Custom Speech-Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech je sada online nástrojů, která vám umožní vyhodnotit a vylepšit přesnost řeči na text pro vaše aplikace, nástroje a produkty. Vše, co bude trvat, je několik testovací zvukové soubory. Pomocí odkazů níže můžete začít vytvářet vlastní prostředí pro text v textu.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: trbye
ms.openlocfilehash: b9ac473c3864b4be06c978e7d6a3555bb4550d2a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88056835"
---
# <a name="what-is-custom-speech"></a>Co je Custom Speech?

[Custom Speech](https://aka.ms/customspeech) je sada online nástrojů, která vám umožní vyhodnotit a vylepšit přesnost řeči na text od Microsoftu pro vaše aplikace, nástroje a produkty. Vše, co bude trvat, je několik testovací zvukové soubory. Pomocí odkazů níže můžete začít vytvářet vlastní prostředí pro text v textu.

## <a name="whats-in-custom-speech"></a>Co je v Custom Speech?

Abyste mohli provádět cokoli s Custom Speech, budete potřebovat účet Azure a předplatné služby Speech. Jakmile budete mít účet, můžete si nechat data, naučit a testovat vaše modely, kontrolovat kvalitu rozpoznávání, hodnotit přesnost a nakonec nasazovat a používat vlastní model řeči na text.

Tento diagram zvýrazňuje části, které tvoří [portál Custom Speech](https://aka.ms/customspeech). Pomocí odkazů níže můžete získat další informace o jednotlivých krocích.

![Zvýrazní různé komponenty, které tvoří portál Custom Speech.](./media/custom-speech/custom-speech-overview.png)

1. [Přihlaste se k odběru a vytvořte projekt](#set-up-your-azure-account) – vytvořte účet Azure a přihlaste se k odběru služby Speech. Toto jednotné předplatné vám umožní přístup k převodu řeči na text, převod textu na řeč, rozpoznávání řeči a [Custom Speech Portal](https://speech.microsoft.com/customspeech). Pak pomocí svého předplatného služby Speech vytvořte svůj první Custom Speech projekt.

2. [Nahrání testovacích](how-to-custom-speech-test-data.md) dat – nahrání testovacích dat (zvukové soubory) k vyhodnocení nabídky pro vaše aplikace, nástroje a produkty od Microsoftu na text

3. [Kontrola kvality rozpoznávání](how-to-custom-speech-inspect-data.md) – pomocí [Custom Speechového portálu](https://speech.microsoft.com/customspeech) můžete přehrát nahraný zvuk a zkontrolovat kvalitu rozpoznávání řeči vašich testovacích dat. Kvantitativní měření najdete v tématu [Kontrola dat](how-to-custom-speech-inspect-data.md).

4. [Vyhodnotit přesnost](how-to-custom-speech-evaluate-data.md) – vyhodnotit přesnost modelu řeči na text. [Portál Custom Speech](https://speech.microsoft.com/customspeech) poskytne *Četnost chyb slov*, která se dá použít k určení, jestli je potřeba další školení. Pokud jste spokojeni s přesností, můžete použít rozhraní API služby Speech přímo. Pokud byste chtěli zlepšit přesnost relativních průměrů 5% až 20%, použijte na portálu kartu **školení** k nahrání dalších školicích dat, jako jsou přepisy popisků a související text.

5. [Zlepšení přesnosti](how-to-custom-speech-improve-accuracy.md) – vyberte další školicí data strategická za účelem zlepšení kvality modelu řeči na text v závislosti na vašem scénáři.

6. Vylepšete [model](how-to-custom-speech-train-model.md) – zvyšte přesnost vašeho modelu řeči na text tím, že poskytnete písemné přepisy (10 – 1000 hodin) a související text (<200 MB) spolu s daty testovacího testu. Tato data pomáhají naučit model řeči na text. Po školení, opětovném testování a pokud jste s výsledkem spokojeni, můžete model nasadit.

7. [Nasazení modelu](how-to-custom-speech-deploy-model.md) – vytvořte vlastní koncový bod pro model řeči a text a používejte ho ve svých aplikacích, nástrojích nebo produktech.

## <a name="set-up-your-azure-account"></a>Nastavení účtu Azure

Aby bylo možné použít [portál Custom Speech](https://speech.microsoft.com/customspeech) k vytvoření vlastního modelu, je nutné předplatné služby Speech Service. Podle těchto pokynů vytvořte standardní předplatné služby Speech: [Vytvořit předplatné pro rozpoznávání řeči](get-started.md#new-resource).

> [!NOTE]
> Ujistěte se prosím, že jste vytvořili standardní odběry (S0), bezplatné předplatné (F0) se nepodporuje.

Po vytvoření účtu Azure a předplatného služby Speech se budete muset přihlásit k [portálu Custom Speech](https://speech.microsoft.com/customspeech) a připojit své předplatné.

1. Získejte klíč předplatného služby Speech z Azure Portal.
2. Přihlaste se k [portálu Custom Speech](https://aka.ms/custom-speech).
3. Vyberte předplatné, na kterém je potřeba pracovat, a vytvořte projekt řeči.
4. Pokud chcete upravit předplatné, použijte ikonu **ozubeného kola** , která se nachází v horním navigačním panelu.

## <a name="how-to-create-a-project"></a>Jak vytvořit projekt

Obsah, který je podobný datům, modelům, testům a koncovým bodům, je uspořádán do **projektů** na [portálu Custom Speech](https://speech.microsoft.com/customspeech). Každý projekt je specifický pro doménu a zemi/jazyk. Můžete například vytvořit projekt pro volání Center, která používají angličtinu v USA.

Pokud chcete vytvořit svůj první projekt, vyberte **Speech-to-text/Custom Speech**a pak klikněte na **Nový projekt**. Postupujte podle pokynů průvodce a vytvořte projekt. Po vytvoření projektu by se vám měly zobrazit čtyři karty: **data**, **testování**, **školení**a **nasazení**. Pomocí odkazů uvedených v [následujících krocích](#next-steps) se dozvíte, jak používat jednotlivé karty.

> [!IMPORTANT]
> [Portál Custom Speech](https://aka.ms/custom-speech) se nedávno aktualizoval. Pokud jste vytvořili předchozí data, modely, testy a publikované koncové body na portálu CRIS.ai nebo pomocí rozhraní API, je potřeba vytvořit nový projekt na novém portálu pro připojení k těmto starým entitám.

## <a name="next-steps"></a>Další kroky

* [Příprava a testování dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
* [Trénování modelu](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)
