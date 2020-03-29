---
title: Začínáme se službou Vlastní řeč – řeč
titleSuffix: Azure Cognitive Services
description: Vlastní řeč je sada online nástrojů, které vám umožní vyhodnotit a zlepšit přesnost řeči na text pro vaše aplikace, nástroje a produkty. Stačí začít, je několik testovacích zvukových souborů. Pomocí následujících odkazů začněte vytvářet vlastní prostředí převodu řeči na text.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/17/2020
ms.author: dapine
ms.openlocfilehash: e30587ed4d09ba792516ebc7b16529a6ee5ac57c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77918950"
---
# <a name="what-is-custom-speech"></a>Co je vlastní řeč?

[Vlastní řeč](https://aka.ms/customspeech) je sada online nástrojů, které vám umožní vyhodnotit a zlepšit přesnost řeči na text společnosti Microsoft pro vaše aplikace, nástroje a produkty. Stačí začít, je několik testovacích zvukových souborů. Pomocí následujících odkazů začněte vytvářet vlastní prostředí převodu řeči na text.

## <a name="whats-in-custom-speech"></a>Co je ve vlastní řeči?

Než budete moci s vlastní řečí cokoli udělat, budete potřebovat účet Azure a předplatné služby Speech. Jakmile budete mít účet, můžete připravit data, trénovat a testovat modely, kontrolovat kvalitu rozpoznávání, vyhodnotit přesnost a nakonec nasadit a používat vlastní model převodu řeči na text.

Tento diagram zvýrazní části, které tvoří [portál vlastní řeči](https://aka.ms/customspeech). Další informace o jednotlivých krocích najdete v následujících odkazech.

![Zvýrazní různé součásti, které tvoří portál vlastní řeči.](./media/custom-speech/custom-speech-overview.png)

1. [Přihlásit se k odběru a vytvořit projekt](#set-up-your-azure-account) – vytvořte účet Azure a přihlaste se ke službě Řeč. Toto sjednocené předplatné umožňuje přístup k převodu řeči na text, převodu textu na řeč, překladu řeči a [portálu vlastní řeči](https://speech.microsoft.com/customspeech). Potom pomocí předplatného služby Rozpoznávání řeči vytvořte svůj první projekt vlastní řeči.

2. [Nahrání testovacích dat](how-to-custom-speech-test-data.md) – Nahrajte testovací data (zvukové soubory) a vyhodnoťte nabídku microsoftu převod řeči na text pro vaše aplikace, nástroje a produkty.

3. [Kontrola kvality rozpoznávání](how-to-custom-speech-inspect-data.md) – Pomocí [portálu Vlastní řeč](https://speech.microsoft.com/customspeech) přehrajte nahraný zvuk a zkontrolujte kvalitu rozpoznávání řeči testovacích dat. Kvantitativní měření viz [Kontrola dat](how-to-custom-speech-inspect-data.md).

4. [Vyhodnotit přesnost](how-to-custom-speech-evaluate-data.md) – vyhodnoťte přesnost modelu převodu řeči na text. [Portál vlastní řeči](https://speech.microsoft.com/customspeech) bude poskytovat *chybovou rychlost aplikace Word*, kterou lze použít k určení, zda je vyžadováno další školení. Pokud jste spokojeni s přesností, můžete použít řešení API služby Řeči přímo. Pokud chcete zlepšit přesnost o relativní průměr o 5 % až 20 %, nakartě **Školení** na portálu nahrajte další trénovací data, například přepisy s lidským označením a související text.

5. [Trénování modelu](how-to-custom-speech-train-model.md) – Zlepšete přesnost modelu převodu řeči na text poskytnutím písemných přepisů (10–1 000 hodin) a souvisejícího textu (<200 MB) spolu s daty zvukového testu. Tato data pomáhají trénovat model převodu řeči na text. Po trénování, retest a pokud jste spokojeni s výsledkem, můžete nasadit model.

6. [Nasazení modelu](how-to-custom-speech-deploy-model.md) – vytvořte vlastní koncový bod pro model převodu řeči na text a použijte ho ve svých aplikacích, nástrojích nebo produktech.

## <a name="set-up-your-azure-account"></a>Nastavení účtu Azure

Před [vytvořením](https://speech.microsoft.com/customspeech) vlastního modelu je vyžadováno předplatné služby Rozpoznávání řeči. Podle těchto pokynů vytvořte standardní předplatné služby Rozpoznávání řeči: [Vytvoření předplatného řeči](get-started.md#new-resource).

> [!NOTE]
> Nezapomeňte vytvořit standardní předplatná (S0), bezplatné zkušební verze (F0) odběry nejsou podporovány.

Po vytvoření účtu Azure a předplatného služby Speech se budete muset přihlásit k [portálu Vlastní řeči](https://speech.microsoft.com/customspeech) a připojit své předplatné.

1. Získejte klíč předplatného služby Speech z portálu Azure.
2. Přihlaste se k [portálu Vlastní řeč](https://aka.ms/custom-speech).
3. Vyberte předplatné, na které potřebujete pracovat, a vytvořte projekt řeči.
4. Pokud chcete upravit předplatné, použijte ikonu **kolečka** umístěnou v horní navigaci.

## <a name="how-to-create-a-project"></a>Jak vytvořit projekt

Obsah, jako jsou data, modely, testy a koncové body jsou **uspořádány** do projektů na [portálu vlastní řeči](https://speech.microsoft.com/customspeech). Každý projekt je specifický pro doménu a zemi/jazyk. Můžete například vytvořit projekt pro centra volání, která používají angličtinu ve Spojených státech.

Chcete-li vytvořit první projekt, vyberte **možnost Řeč na text/Vlastní řeč**a klepněte na tlačítko Nový **projekt**. Podle pokynů průvodce vytvořte projekt. Po vytvoření projektu byste měli vidět čtyři karty: **Data**, **Testování**, **Školení**a **Nasazení**. Pomocí odkazů uvedených v [dalším postupech](#next-steps) se dozvíte, jak používat jednotlivé karty.

> [!IMPORTANT]
> [Portál Vlastní řeč](https://aka.ms/custom-speech) byl nedávno aktualizován! Pokud jste vytvořili předchozí data, modely, testy a publikované koncové body na portálu CRIS.ai nebo pomocí rozhraní API, je třeba vytvořit nový projekt v novém portálu pro připojení k těmto starým entitám.

## <a name="next-steps"></a>Další kroky

* [Příprava a testování dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
* [Trénování vašeho modelu](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)
