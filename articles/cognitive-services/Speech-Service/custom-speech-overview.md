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
ms.openlocfilehash: f2dca513b4413f1761ea1b21bf71f90bb27d3053
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100388647"
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
> Pokud máte v úmyslu vytvořit vlastní model se **zvukovými daty**, vyberte jednu z následujících oblastí, která má vyhrazený hardware k dispozici pro školení. Tím se zkrátí doba potřebná k učení modelu.

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

> [!NOTE]
> Ujistěte se prosím, že jste vytvořili předplatné Standard (S0). Bezplatné předplatné (F0) se nepodporuje.

Po vytvoření účtu Azure a předplatného služby Speech se budete muset přihlásit ke službě [Speech Studio](https://speech.microsoft.com/customspeech) a připojit své předplatné.

1. Přihlaste se ke službě [Speech Studio](https://aka.ms/custom-speech).
1. Vyberte předplatné, ve kterém potřebujete pracovat, a vytvořte projekt řeči.
1. Pokud chcete upravit své předplatné, vyberte v horní nabídce tlačítko ozubeného kola.

## <a name="how-to-create-a-project"></a>Jak vytvořit projekt

Obsah, který je podobný datům, modelům, testům a koncovým bodům, je uspořádán do *projektů* v aplikaci [Speech Studio](https://speech.microsoft.com/customspeech). Každý projekt je specifický pro doménu a zemi/jazyk. Můžete například vytvořit projekt pro volání Center, která používají angličtinu v USA.

Pokud chcete vytvořit svůj první projekt, vyberte **Převod řeči na text/vlastní řeč** a pak vyberte **Nový projekt**. Postupujte podle pokynů průvodce a vytvořte projekt. Po vytvoření projektu byste měli vidět čtyři karty: **data**, **testování**, **školení** a **nasazení**. Pomocí odkazů uvedených v [následujících krocích](#next-steps) se dozvíte, jak používat jednotlivé karty.

> [!IMPORTANT]
> Služba [Speech Studio](https://aka.ms/custom-speech) , která se dřív jmenovala jako Custom Speech Portal, se nedávno aktualizovala! Pokud jste vytvořili předchozí data, modely, testy a publikované koncové body na portálu CRIS.ai nebo pomocí rozhraní API, je potřeba vytvořit nový projekt na novém portálu pro připojení k těmto starým entitám.

## <a name="model-lifecycle"></a>Životní cyklus modelu

Custom Speech používá *základní modely* i *vlastní modely*. Každý jazyk má jeden nebo více základních modelů. Obecně platí, že když se do služby normálního rozpoznávání řeči uvolní nový model řeči, naimportuje se také do služby Custom Speech jako nový základní model. Aktualizují se každých 3 až 6 měsíců. Starší modely obvykle jsou méně užitečné v průběhu času, protože nejnovější model obvykle má vyšší přesnost.

Naproti tomu vlastní modely se vytvářejí tak, že se vybraný základní model přizpůsobí konkrétnímu scénáři zákazníka. Můžete používat konkrétní vlastní model po dlouhou dobu, jakmile budete mít ten, který vyhovuje vašim potřebám. Doporučujeme, abyste pravidelně aktualizovali na nejnovější základní model a v průběhu času znovu provedli další data. 

K dalším klíčovým podmínkám souvisejícím s životním cyklem modelu patří:

* **Přizpůsobení**: Vytvoření základního modelu a jeho přizpůsobení do vaší domény/scénáře pomocí textových dat nebo zvukových dat.
* **Dekódování**: použití modelu a provádění rozpoznávání řeči (dekódování zvuků do textu).
* **Koncový bod**: uživatelsky specifické nasazení buď základního modelu, nebo vlastního modelu, který je přístupný *jenom* pro daného uživatele.

### <a name="expiration-timeline"></a>Časová osa vypršení platnosti

Nové modely a nové funkce budou k dispozici a starší, ale méně přesné modely jsou vyřazeny, a to v následujících časových osách pro model a vypršení platnosti koncového bodu:

**Základní modely** 

* Přizpůsobení: k dispozici po dobu jednoho roku. Po importu modelu je možné vytvořit vlastní modely v jednom roce. Po jednom roce se musí vytvořit nové vlastní modely z novější verze základního modelu.  
* Dekódování: k dispozici po dobu dvou let po importu. Takže můžete vytvořit koncový bod a pomocí tohoto modelu Batch přepisovat dva roky. 
* Koncové body: k dispozici na stejné časové ose jako dekódování.

**Vlastní modely**

* Dekódování: je k dispozici po dobu dvou let po vytvoření modelu. Takže můžete použít vlastní model po dobu dvou let (Batch/reálný/testování) po jeho vytvoření. Po dvou letech *byste si měli svůj model přeškolit* , protože základní model se obvykle už pro přizpůsobení nepoužívá.  
* Koncové body: k dispozici na stejné časové ose jako dekódování.

Když vyprší platnost základního modelu nebo vlastního modelu, vždycky se vrátí k *nejnovější verzi základního modelu*. Vaše implementace tak nebude nikdy přerušena, ale může být méně přesné pro *vaše konkrétní data* , pokud je dosaženo vypršení vlastních modelů. Vypršení platnosti modelu můžete zobrazit na následujících místech Custom Speech oblasti aplikace Speech Studio:

* Souhrn školení modelů
* Podrobnosti školení modelů
* Souhrn nasazení
* Podrobnosti o nasazení

Můžete také kontrolovat data vypršení platnosti přes [`GetModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetModel) [`GetBaseModel`](https://westus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModel) rozhraní API vlastního rozpoznávání řeči pod `deprecationDates` vlastností v odpovědi JSON.

Počítejte s tím, že model můžete upgradovat na vlastní koncový bod řeči bez výpadků změnou modelu používaného koncovým bodem v části nasazení v nástroji Speech Studio nebo pomocí vlastního rozhraní API pro rozpoznávání řeči.

## <a name="next-steps"></a>Další kroky

* [Příprava a testování dat](./how-to-custom-speech-test-and-train.md)
* [Kontrola dat](how-to-custom-speech-inspect-data.md)
* [Vyhodnotit a vylepšit přesnost modelu](how-to-custom-speech-evaluate-data.md)
* [Trénování a nasazení modelu](how-to-custom-speech-train-model.md)
