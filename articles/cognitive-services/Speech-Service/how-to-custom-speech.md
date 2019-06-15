---
title: Začínáme s Custom Speech – hlasové služby
titlesuffix: Azure Cognitive Services
description: Vlastní převod řeči je sada online nástrojů, které vám umožní vyhodnotit a zvýšit přesnost řeči na text od Microsoftu pro aplikace, nástroje a produkty. Všechno potřebné, abyste mohli začít několik zvukových souborů testu. Pomocí odkazů níže a začněte vytvářet vlastní prostředí speech to text.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 05/02/2019
ms.author: erhopf
ms.openlocfilehash: f9b9fc0a2939f601cbddafb6ac400130e794da2b
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "67060884"
---
# <a name="what-is-custom-speech"></a>Co je vlastní řeči?

[Custom Speech](https://aka.ms/custom-speech) je sada online nástrojů, které vám umožní vyhodnotit a zvýšit přesnost řeči na text od Microsoftu pro aplikace, nástroje a produkty. Všechno potřebné, abyste mohli začít několik zvukových souborů testu. Pomocí odkazů níže a začněte vytvářet vlastní prostředí speech to text.

## <a name="whats-in-custom-speech"></a>Co jsou vlastní převod řeči?

Předtím, než je možné provést cokoli, co se vlastní řeč, budete potřebovat účet Azure a hlasové služby předplatného. Jakmile máte účet, můžete pro přípravu dat, trénování a testování vašich modelů, kontrolovat kvalitu rozpoznávání, vyzkoušejte přesnost a nakonec nasazení a pomocí vlastního modelu řeči na text.

Tento diagram zvýrazňuje částí, které tvoří portál Custom Speech. Další informace o každém kroku pomocí odkazů níže.

![Ukazuje různé součásti tvořící portál Custom Speech.](./media/custom-speech/custom-speech-overview.png)

1. [Přihlášení odběru a vytvořte projekt](#set-up-your-azure-account) – vytvoření účtu Azure a odběru hlasové služby. Toto jednotné předplatné poskytuje přístup k převodu řeči na text, převodu textu na řeč, překlad řeči a portál Custom Speech. Pak pomocí svého předplatného služby řeči, vytvořte svůj první projekt vlastní řeči.

2. [Nahrát testovací data](how-to-custom-speech-test-data.md) -nahrát testovací data (zvukové soubory) pro vyhodnocení od Microsoftu speech-to-text nabídky pro vaše aplikace, nástroje a produkty.

3. [Kontrolovat kvalitu rozpoznávání](how-to-custom-speech-inspect-data.md) -použít portál Custom Speech přehrávat nahrané zvuk a kontrolovat kvalitu rozpoznávání řeči testovací data. Kvantitativní měření, naleznete v tématu [kontrolovat data,](how-to-custom-speech-inspect-data.md).

4. [Vyzkoušejte přesnost](how-to-custom-speech-evaluate-data.md) -vyhodnotit jeho přesnost modelu řeči na text. Portál Custom Speech poskytne *míra chyb aplikace Word*, který slouží k určení, jestli se vyžaduje další školicí. Pokud jste spokojeni s přesností, můžete přímo použít rozhraní API pro rozpoznávání řeči služeb. Pokud chcete zlepšit přesnost relativní průměr o 5-20 %, použijte **školení** karta na portálu a odeslat další trénovací data, jako jsou lidské s názvem bez přípony záznamy o studiu a související text.

5. [Trénování modelu](how-to-custom-speech-train-model.md) – zlepšit přesnost modelu řeči na text poskytnutím písemné záznamy o studiu (10 – 1 000 hodin) a související text (< 200 MB) společně s vaší zvuk testovací data. Tato data pomáhají pro trénování modelu řeči na text. Po školení, testování, a pokud jste spokojeni s výsledkem, svůj model dokážete nasadit.

6. [Nasazení modelu](how-to-custom-speech-deploy-model.md) – vytvoření vlastního koncového bodu pro váš model řeči na text a jeho použití v aplikacích, nástroje nebo produkty.

## <a name="set-up-your-azure-account"></a>Nastavení účtu Azure

Vyžaduje se předplatné hlasové služby, než portál Custom Speech můžete použít k vytvoření vlastního modelu. Postupujte podle těchto pokynů a vytvořte standardní předplatné hlasové služby: [Vytvoření odběru řeči](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#new-azure-account).

> [!NOTE]
> Nezapomeňte vytvořit standardní předplatná (S0), předplatných bezplatné zkušební verze (F0) nejsou podporovány.

Po vytvoření účtu Azure a hlasové služby předplatného, budete muset přihlásit na portál Custom Speech a připojit vaše předplatné.

1. Získejte klíč předplatného hlasové služby z webu Azure portal.
2. Přihlaste se do [portál Custom Speech](https://aka.ms/custom-speech).
3. Vyberte předplatné, které je potřeba pracovat a vytvořte projekt řeči.
4. Pokud chcete změnit vaše předplatné, použijte **ozubeného kola** ikona nachází v horním navigačním panelu.

## <a name="how-to-create-a-project"></a>Vytvoření projektu

Obsah, jako jsou data, modely, testy a koncové body jsou uspořádány do **projekty** portálu vlastní řeči. Každý projekt je specifický pro doménu a země/jazyka. Například můžete vytvořit projekt pro volání centra, která používá angličtinu ve Spojených státech.

Chcete-li vytvořit svůj první projekt, vyberte **řeči Speech na text/vlastní**, pak klikněte na tlačítko **nový projekt**. Postupujte podle pokynů v Průvodci vytvořením projektu. Po vytvoření projektu, byste měli vidět čtyři karty: **Data**, **testování**, **školení**, a **nasazení**. Pomocí odkazů uvedených v [další kroky](#next-steps) na další informace o použití každé kartě.

## <a name="next-steps"></a>Další postup

* [Připravit a otestovat vašich dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnocení vašich dat](how-to-custom-speech-evaluate-data.md)
* [Vyzkoušejte svůj model](how-to-custom-speech-train-model.md)
* [Nasazení modelu](how-to-custom-speech-deploy-model.md)
