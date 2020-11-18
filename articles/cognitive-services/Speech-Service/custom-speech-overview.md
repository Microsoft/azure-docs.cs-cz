---
title: Přehled Custom Speech – služba Speech
titleSuffix: Azure Cognitive Services
description: Custom Speech je sada online nástrojů, která vám umožní vyhodnotit a vylepšit přesnost řeči na text pro vaše aplikace, nástroje a produkty. Vše, co bude trvat, je několik testovací zvukové soubory. Pomocí odkazů níže můžete začít vytvářet vlastní prostředí pro text v textu.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 6dbe97d615753f0a90c8ba80aa7afa6dafa15eb2
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658464"
---
# <a name="what-is-custom-speech"></a>Co je Custom Speech?

[Custom Speech](https://aka.ms/customspeech) je sada nástrojů založených na uživatelském rozhraní, které vám umožní vyhodnotit a vylepšit přesnost řeči na text pro vaše aplikace a produkty od Microsoftu. Vše, co bude trvat, je několik testovací zvukové soubory. Pomocí odkazů níže můžete začít vytvářet vlastní prostředí pro text v textu.

## <a name="whats-in-custom-speech"></a>Co je v Custom Speech?

Abyste mohli provádět cokoli s Custom Speech, budete potřebovat účet Azure a předplatné služby Speech. Jakmile budete mít účet, můžete si nechat data, naučit a testovat vaše modely, kontrolovat kvalitu rozpoznávání, hodnotit přesnost a nakonec nasazovat a používat vlastní model řeči na text.

Tento diagram zvýrazňuje části, které tvoří [portál Custom Speech](https://aka.ms/customspeech). Pomocí odkazů níže můžete získat další informace o jednotlivých krocích.

![Zvýrazní různé komponenty, které tvoří portál Custom Speech.](./media/custom-speech/custom-speech-overview.png)

1. [Přihlaste se k odběru a vytvořte projekt](#set-up-your-azure-account) – vytvořte účet Azure a přihlaste se k odběru služby Speech. Toto jednotné předplatné vám umožní přístup k převodu řeči na text, převod textu na řeč, rozpoznávání řeči a [Custom Speech Portal](https://speech.microsoft.com/customspeech). Pak pomocí svého předplatného služby Speech vytvořte svůj první Custom Speech projekt.

1. [Nahrání testovacích](how-to-custom-speech-test-data.md) dat – nahrání testovacích dat (zvukové soubory) k vyhodnocení nabídky pro vaše aplikace, nástroje a produkty od Microsoftu na text

1. [Kontrola kvality rozpoznávání](how-to-custom-speech-inspect-data.md) – pomocí [Custom Speechového portálu](https://speech.microsoft.com/customspeech) můžete přehrát nahraný zvuk a zkontrolovat kvalitu rozpoznávání řeči vašich testovacích dat. Kvantitativní měření najdete v tématu [Kontrola dat](how-to-custom-speech-inspect-data.md).

1. [Vyhodnotit a vylepšit přesnost](how-to-custom-speech-evaluate-data.md) – vyhodnotit a vylepšit přesnost modelu řeči na text. [Portál Custom Speech](https://speech.microsoft.com/customspeech) poskytne *Četnost chyb slov*, která se dá použít k určení, jestli je potřeba další školení. Pokud jste spokojeni s přesností, můžete použít rozhraní API služby Speech přímo. Pokud byste chtěli zlepšit přesnost relativních průměrů 5% až 20%, použijte na portálu kartu **školení** k nahrání dalších školicích dat, jako jsou přepisy popisků a související text.

1. [Výuka a nasazení modelu](how-to-custom-speech-train-model.md) – Vylepšete přesnost vašeho modelu řeči na text tím, že zadáte písemné přepisy (10 – 1000 hodin) a související text (<200 MB) spolu s daty testovacího testu. Tato data pomáhají naučit model řeči na text. Po školení, opětovném testování a pokud jste s výsledkem spokojeni, můžete model nasadit do vlastního koncového bodu.

## <a name="set-up-your-azure-account"></a>Nastavení účtu Azure

Předtím, než budete moci použít [portál Custom Speech](https://speech.microsoft.com/customspeech) k vytvoření vlastního modelu, je nutné předplatné služby Azure Account a Speech Service. Pokud účet a předplatné nemáte, [Vyzkoušejte službu Speech Service zdarma](overview.md#try-the-speech-service-for-free).

> [!NOTE]
> Ujistěte se prosím, že jste vytvořili standardní odběry (S0), bezplatné předplatné (F0) se nepodporuje.

Po vytvoření účtu Azure a předplatného služby Speech se budete muset přihlásit k [portálu Custom Speech](https://speech.microsoft.com/customspeech) a připojit své předplatné.

1. Přihlaste se k [portálu Custom Speech](https://aka.ms/custom-speech).
1. Vyberte předplatné, na kterém je potřeba pracovat, a vytvořte projekt řeči.
1. Pokud chcete upravit předplatné, použijte ikonu **ozubeného kola** , která se nachází v horním navigačním panelu.

## <a name="how-to-create-a-project"></a>Jak vytvořit projekt

Obsah, který je podobný datům, modelům, testům a koncovým bodům, je uspořádán do **projektů** na [portálu Custom Speech](https://speech.microsoft.com/customspeech). Každý projekt je specifický pro doménu a zemi/jazyk. Můžete například vytvořit projekt pro volání Center, která používají angličtinu v USA.

Pokud chcete vytvořit svůj první projekt, vyberte **Speech-to-text/Custom Speech** a pak klikněte na **Nový projekt**. Postupujte podle pokynů průvodce a vytvořte projekt. Po vytvoření projektu by se vám měly zobrazit čtyři karty: **data**, **testování**, **školení** a **nasazení**. Pomocí odkazů uvedených v [následujících krocích](#next-steps) se dozvíte, jak používat jednotlivé karty.

> [!IMPORTANT]
> [Portál Custom Speech](https://aka.ms/custom-speech) se nedávno aktualizoval. Pokud jste vytvořili předchozí data, modely, testy a publikované koncové body na portálu CRIS.ai nebo pomocí rozhraní API, je potřeba vytvořit nový projekt na novém portálu pro připojení k těmto starým entitám.

## <a name="model-lifecycle"></a>Životní cyklus modelu

Vlastní řeč používá **základní modely** i **vlastní modely**. Každý jazyk má jeden nebo více **základních modelů**. Obecně platí, že když se do služby normálního rozpoznávání řeči uvolní nový model řeči, naimportuje se také do služby Custom Speech jako nový **základní model**. Obvykle se aktualizují každých 3-6 měsíců a starší modely se v průběhu času stanou méně užitečné, protože nejnovější model obvykle má vyšší přesnost.

Naproti tomu **vlastní modely** se vytvářejí tak, že se vybraný základní model přizpůsobí konkrétnímu scénáři zákazníka. Až se vám dokončí vaše potřeby, můžete po delší dobu používat konkrétní vlastní model, ale doporučujeme, abyste pravidelně aktualizovali na nejnovější základní model a přepracovali s dalšími daty.

K dalším klíčovým podmínkám souvisejícím s životním cyklem modelu patří:

* **Přizpůsobení**: přebírání základního modelu a jeho přizpůsobení do vaší domény/scénáře pomocí textových dat nebo zvukových dat
* **Dekódování**: použití modelu a provádění rozpoznávání řeči (dekódování zvuků do textu)
* **Koncový bod**: uživatelsky specifické nasazení buď základního modelu, nebo vlastního modelu, který je přístupný *jenom* pro daného uživatele.

### <a name="expiration-timeline"></a>Časová osa vypršení platnosti

Nové modely a nové funkce budou k dispozici a starší, ale méně přesné modely jsou vyřazeny, a to v následujících časových osách pro model a vypršení platnosti koncového bodu:

**Základní modely** 

* Přizpůsobení: k dispozici po dobu 1 roku. Po importu modelu je k vytvoření vlastních modelů k dispozici po dobu 1 roku. Po 1 roce se musí vytvořit nové vlastní modely z novější verze základního modelu.  
* Dekódování: k dispozici po 2 letech po importu. To znamená, že můžete vytvořit koncový bod a pomocí tohoto modelu Batch přepisovat 2 roky. 
* Koncové body: k dispozici na stejné časové ose jako dekódování

**Vlastní modely**

* Dekódování: k dispozici po 2 letech po vytvoření modelu. To znamená, že můžete použít vlastní model po dobu 2 let (Batch/reálný/testování) po jeho vytvoření. Po 2 letech **byste měli svůj model přeškolit**, protože většina často základní model bude pro přizpůsobení zastaralá.  
* Koncové body: k dispozici na stejné časové ose jako dekódování

Když vyprší platnost základního modelu nebo vlastního modelu, vždycky se vrátí k **nejnovější verzi základního modelu**. Vaše implementace proto nebude nikdy přerušena, ale může být méně přesné pro *vaše konkrétní data* , pokud je dosaženo vypršení vlastních modelů. Vypršení platnosti modelu můžete zobrazit na následujících místech na portálu Custom Speech:

* Souhrn školení modelů
* Podrobnosti školení modelů
* Souhrn nasazení
* Podrobnosti o nasazení

Můžete také kontrolovat data vypršení platnosti přes [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) rozhraní API vlastního rozpoznávání řeči pod `deprecationDates` vlastností v odpovědi JSON.

Všimněte si, že model můžete upgradovat na vlastní koncový bod řeči bez výpadků změnou modelu používaného koncovým bodem v části nasazení na vlastním portálu pro rozpoznávání řeči nebo prostřednictvím vlastního rozhraní Speech API.

## <a name="next-steps"></a>Další kroky

* [Příprava a testování dat](how-to-custom-speech-test-data.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnotit a vylepšit přesnost modelu](how-to-custom-speech-evaluate-data.md)
* [Trénování a nasazení modelu](how-to-custom-speech-train-model.md)
