---
title: Výuka a nasazení Custom Speechch služeb pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: V tomto článku se naučíte, jak naučit a nasazovat modely Custom Speech. Výukový model pro převod řeči na text může zlepšit přesnost rozpoznávání pro základní model Microsoftu nebo vlastní model. Model je vyškolený pomocí přepisů a souvisejících textů v popisku.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/11/2020
ms.author: trbye
ms.openlocfilehash: 34c0703ee7c335ca904a21bcce6ed44abc6dc13f
ms.sourcegitcommit: dc342bef86e822358efe2d363958f6075bcfc22a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2020
ms.locfileid: "94555783"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Výuka a nasazení Custom Speechho modelu

V tomto článku se naučíte, jak naučit a nasazovat modely Custom Speech. Školení pro model převodu řeči na text může zlepšit přesnost rozpoznávání pro základní model společnosti Microsoft. Model je vyškolený pomocí přepisů a souvisejících textů v popisku. Tyto datové sady spolu s dříve nahranými zvukovými daty se používají k vylepšení a výukového modelu řeči a textu.

## <a name="use-training-to-resolve-accuracy-issues"></a>Řešení problémů s přesností pomocí školení

Pokud narazíte na problémy s rozpoznáváním se základním modelem, může použití přepisů a souvisejících dat pomocí popisků v rámci učení vlastního modelu pomoci zlepšit přesnost. Pomocí této tabulky můžete určit, kterou datovou sadu použít k vyřešení těchto problémů:

| Případ použití | Datový typ |
| -------- | --------- |
| Vylepšete přesnost rozpoznávání v oboru a gramatikě specifické pro konkrétní odvětví, jako je lékařské terminologie nebo IT žargonu. | Související text (věty/projevy) |
| Definujte fonetickou a zobrazovanou formu slova nebo termínu, který má nestandardní výslovnost, například názvy produktů nebo akronymy. | Související text (výslovnost) |
| Vylepšete přesnost rozpoznávání pro mluvené styly, zvýraznění nebo konkrétní šum na pozadí. | Audio + přepisy s popiskem |

## <a name="train-and-evaluate-a-model"></a>Trénování a vyhodnocení modelu

Prvním krokem při výuce modelu je odeslání školicích dat. Použijte [přípravu a testování dat](how-to-custom-speech-test-data.md) pro podrobné pokyny k přípravě přepisů a souvisejících textu (projevy a výslovnosti) s lidským označením. Po nahrání školicích dat postupujte podle těchto pokynů a spusťte školení modelu:

1. Přihlaste se k [portálu Custom Speech](https://speech.microsoft.com/customspeech).
2. Přejít na **text > řeči Custom Speech > [název projektu] > školení**.
3. Klikněte na **výuka model**.
4. V dalším kroku poskytněte školení **název** a **Popis**.
5. V rozevírací nabídce **scénář a model standardních hodnot** vyberte scénář, který nejlépe odpovídá vaší doméně. Pokud si nejste jisti, který scénář zvolíte, vyberte možnost **Obecné**. Základní model je výchozím bodem pro školení. Nejnovější model je obvykle nejlepší volbou.
6. Na stránce **Vybrat školicí data** vyberte jednu nebo více zvukových a uživatelem označených datových sad, které chcete použít pro školení.
7. Po dokončení školení se můžete rozhodnout pro provádění testování přesnosti u nově vyučeného modelu. Tento krok je volitelný.
8. Vyberte **vytvořit** a sestavte vlastní model.

V tabulce školení se zobrazí nová položka, která odpovídá nově vytvořenému modelu. V tabulce se zobrazí také stav: zpracování, úspěch, selhalo.

Podívejte se na [postupy](how-to-custom-speech-evaluate-data.md) při vyhodnocování a zlepšování Custom Speech přesnosti modelu. Pokud se rozhodnete vyzkoušet přesnost testování, je důležité vybrat akustickou datovou sadu, která se liší od ta, kterou jste použili u svého modelu, abyste získali realistickou představu o výkonu modelu.

## <a name="deploy-a-custom-model"></a>Nasazení vlastního modelu

Po nahrání a kontrole dat, vyhodnocení přesnosti a školení vlastního modelu můžete nasadit vlastní koncový bod pro použití s aplikacemi, nástroji a produkty. 

Pokud chcete vytvořit nový vlastní koncový bod, přihlaste se k [portálu Custom Speech](https://speech.microsoft.com/customspeech) a v horní části stránky vyberte **nasazení** v nabídce Custom Speech. Pokud se jedná o první spuštění, všimnete si, že v tabulce nejsou uvedeny žádné koncové body. Po vytvoření koncového bodu použijete tuto stránku ke sledování každého nasazeného koncového bodu.

V dalším kroku vyberte **přidat koncový bod** a zadejte **název** a **Popis** vlastního koncového bodu. Pak vyberte vlastní model, který byste chtěli přidružit k tomuto koncovému bodu. Na této stránce můžete také povolit protokolování. Protokolování umožňuje monitorovat provoz koncového bodu. Pokud je tato zakázaná, provoz se neuloží.

![Postup nasazení modelu](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Nezapomeňte přijmout podmínky použití a podrobnosti o cenách.

V dalším kroku vyberte **vytvořit**. Tato akce vrátí na stránku **nasazení** . Tabulka teď obsahuje položku, která odpovídá vašemu vlastnímu koncovému bodu. Stav koncového bodu zobrazuje aktuální stav. Vytvoření instance nového koncového bodu pomocí vlastních modelů může trvat až 30 minut. Když se změní stav nasazení na **dokončeno** , bude koncový bod připravený k použití.

Po nasazení koncového bodu se název koncového bodu zobrazí jako odkaz. Kliknutím na odkaz zobrazíte informace, které jsou specifické pro váš koncový bod, jako je klíč koncového bodu, adresa URL koncového bodu a ukázkový kód.

## <a name="view-logging-data"></a>Zobrazit data protokolování

Data protokolování jsou k dispozici ke stažení v části **koncový bod > podrobnosti**.
> [!NOTE]
>Data protokolování jsou k dispozici po dobu 30 dnů v úložišti vlastněných společností Microsoft a budou odebrána později. V případě, že účet úložiště vlastněné zákazníkem je propojený s předplatným služby rozpoznávání služeb, data protokolování se automaticky neodstraní.

## <a name="next-steps"></a>Další kroky

* Naučte [se používat vlastní model](how-to-specify-source-language.md).

## <a name="additional-resources"></a>Další zdroje

- [Příprava a testování dat](how-to-custom-speech-test-data.md)
- [Kontrola dat](how-to-custom-speech-inspect-data.md)
- [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
