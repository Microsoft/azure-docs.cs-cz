---
title: Výuka a nasazení Custom Speechch služeb pro rozpoznávání řeči
titleSuffix: Azure Cognitive Services
description: Naučte se, jak naučit a nasazovat modely Custom Speech. Školení pro model převodu řeči na text může zlepšit přesnost rozpoznávání pro základní model Microsoft nebo pro vlastní model.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 02/12/2021
ms.author: trbye
ms.openlocfilehash: 4da93503c32e380adb82028e7c5e11dddb247d6f
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100373364"
---
# <a name="train-and-deploy-a-custom-speech-model"></a>Trénování a nasazení modelu Custom Speech

V tomto článku se naučíte, jak naučit a nasazovat modely Custom Speech. Školení pro model převodu řeči na text může zlepšit přesnost rozpoznávání pro základní model Microsoft. Pro výuku modelu použijete přepisy a související text s lidským popiskem. Tyto datové sady, společně s dříve nahranými zvukovými daty, se používají k vylepšení a výukového modelu řeči a textu.

## <a name="use-training-to-resolve-accuracy-problems"></a>Řešení problémů s přesností pomocí školení

Pokud narazíte na problémy s rozpoznáváním pomocí základního modelu, můžete použít přepisy a související data s popisem, abyste mohli naučit vlastní model a lépe zlepšovat přesnost. Pomocí této tabulky můžete určit, kterou datovou sadu použít k řešení problémů:

| Případ použití | Datový typ |
| -------- | --------- |
| Vylepšete přesnost rozpoznávání na základě konkrétního slovníku a gramatiky specifické pro obor, jako je lékařské terminologie nebo IT žargonu | Související text (věty/projevy) |
| Definujte fonetickou a zobrazovanou formu slova nebo termínu, který má nestandardní výslovnost, jako jsou názvy produktů nebo akronymy. | Související text (výslovnost) |
| Zlepšení přesnosti rozpoznávání pro mluvené styly, zvýraznění nebo konkrétní šum na pozadí | Audio + přepisy s popiskem |

## <a name="train-and-evaluate-a-model"></a>Trénování a vyhodnocení modelu

Prvním krokem při výuce modelu je odeslání školicích dat. Podrobné pokyny k přípravě přepisů a souvisejícího textu (projevy a výslovnost) najdete v tématu [Příprava a testování dat](./how-to-custom-speech-test-and-train.md) . Po nahrání školicích dat postupujte podle těchto pokynů a spusťte školení modelu:

1. Přihlaste se k [portálu Custom Speech](https://speech.microsoft.com/customspeech). Pokud máte v úmyslu vytvořit model se zvukovým a uživatelem označenými přepisnou datovou sadou, vyberte předplatné pro rozpoznávání řeči v [oblasti s vyhrazeným hardwarem](custom-speech-overview.md#set-up-your-azure-account) pro školení.
2. Přejít na **text na řeč**  >  **Custom Speech**  >  **[název projektu]**  >  **školení**.
3. Vyberte **model výuky**.
4. Poskytněte školení **název** a **Popis**.
5. V seznamu **scénář a základní model** vyberte scénář, který nejlépe odpovídá vaší doméně. Pokud si nejste jistí, který scénář si vybrat, vyberte **Obecné**. Základní model je výchozím bodem pro školení. Nejnovější model je obvykle nejlepší volbou.
6. Na stránce **Vybrat data školení** zvolte jednu nebo více souvisejících textových datových sad nebo zvuk + přepisně označené datové sady, které chcete použít pro školení.

> [!NOTE]
> Při učení nového modelu začněte s souvisejícím textem. školení se zvukem + s popisem přepisu může trvat mnohem delší dobu **(až do [několika dní](how-to-custom-speech-evaluate-data.md#add-audio-with-human-labeled-transcripts)**).

> [!NOTE]
> Ne všechny základní modely podporují školení se zvukem. Pokud základní model ho nepodporuje, služba řeči bude používat jenom text z přepisů a zvuk bude ignorovat. Seznam základních modelů, které podporují školení se zvukovými daty, najdete v tématu [jazyková podpora](language-support.md#speech-to-text) .

> [!NOTE]
> V případech, kdy změníte základní model používaný pro školení a máte zvuk v datové sadě školení, *vždy* ověřte, zda nový vybraný základní model [podporuje školení se zvukovými daty](language-support.md#speech-to-text). Pokud dřív použitý základní model nepodporoval školení se zvukovými daty a datová sada pro školení obsahuje zvuk, může se výrazně zvýšit doba školení s novým základním modelem a může se stát, **že budete moct** snadno přejít z několika hodin na několik dní. To platí hlavně v **případě, že** vaše předplatné služby Speech není v [oblasti s vyhrazeným hardwarem](custom-speech-overview.md#set-up-your-azure-account) pro školení.
>
> Pokud se setkáte s problémem popsaným v předchozím odstavci, můžete rychle zkrátit dobu školení tím, že snížíte velikost zvuku v datové sadě nebo zcela odeberete a necháte jenom text. Tato možnost se důrazně doporučuje, pokud vaše předplatné služby Speech **není v** [oblasti s vyhrazeným hardwarem](custom-speech-overview.md#set-up-your-azure-account) pro školení.

7. Po dokončení školení můžete provádět testování přesnosti u nově vyučeného modelu. Tento krok je volitelný.
8. Vyberte **vytvořit** a sestavte vlastní model.

V tabulce **školení** se zobrazí nová položka, která odpovídá novému modelu. V tabulce se zobrazí také stav: **zpracování**, **úspěch** nebo **Chyba**.

Podívejte se na [postupy](how-to-custom-speech-evaluate-data.md) při vyhodnocování a zlepšování Custom Speech přesnosti modelu. Pokud se rozhodnete přesnost testování, je důležité vybrat akustickou datovou sadu, která je odlišná od modelu, který jste použili s modelem, a získat tak realistickou představu o výkonu modelu.

> [!NOTE]
> Základní modely i vlastní modely lze použít pouze do určitého data (viz [životní cyklus modelu](custom-speech-overview.md#model-lifecycle)). Speech Studio zobrazuje toto datum ve sloupci **vypršení platnosti** pro každý model a koncový bod. Po uplynutí této žádosti do koncového bodu nebo ke dávkovému přepisu může dojít k selhání nebo návratu do základního modelu.
>
> Přeškolujte svůj model pomocí nejstaršího základního modelu, který vám umožní využít vylepšení přesnosti a vyhnout se tomu, že váš model vyprší.

## <a name="deploy-a-custom-model"></a>Nasazení vlastního modelu

Po nahrání a kontrole dat, vyhodnocení přesnosti a výuku vlastního modelu můžete nasadit vlastní koncový bod pro použití s aplikacemi, nástroji a produkty. 

Pokud chcete vytvořit vlastní koncový bod, přihlaste se k [portálu Custom Speech](https://speech.microsoft.com/customspeech). V nabídce **Custom Speech** v horní části stránky vyberte **nasazení** . Pokud se jedná o první spuštění, všimnete si, že v tabulce nejsou uvedeny žádné koncové body. Po vytvoření koncového bodu použijete tuto stránku ke sledování každého nasazeného koncového bodu.

V dalším kroku vyberte **přidat koncový bod** a zadejte **název** a **Popis** vlastního koncového bodu. Pak vyberte vlastní model, který chcete přidružit ke koncovému bodu.  Můžete také povolit protokolování z této stránky. Protokolování umožňuje monitorovat provoz koncového bodu. Pokud je protokolování zakázané, nebude se provoz ukládat.

![Snímek obrazovky zobrazující stránku nového koncového bodu](./media/custom-speech/custom-speech-deploy-model.png)

> [!NOTE]
> Nezapomeňte přijmout podmínky použití a podrobnosti o cenách.

V dalším kroku vyberte **vytvořit**. Tato akce vrátí na stránku **nasazení** . Tabulka teď obsahuje položku, která odpovídá vašemu vlastnímu koncovému bodu. Stav koncového bodu zobrazuje aktuální stav. Vytvoření instance nového koncového bodu pomocí vlastních modelů může trvat až 30 minut. Když se změní stav nasazení na **dokončeno**, bude koncový bod připravený k použití.

Po nasazení koncového bodu se název koncového bodu zobrazí jako odkaz. Kliknutím na odkaz zobrazíte informace specifické pro váš koncový bod, jako je klíč koncového bodu, adresa URL koncového bodu a vzorový kód. Před tímto datem si poznamenejte datum vypršení platnosti a aktualizujte model koncového bodu, aby se zajistila nepřerušovaná služba.

## <a name="view-logging-data"></a>Zobrazit data protokolování

Data protokolování jsou k dispozici pro export, pokud přejdete na stránku koncového bodu v části **nasazení**.
> [!NOTE]
>Data protokolování jsou k dispozici po dobu 30 dnů v úložišti vlastněných společností Microsoft. Bude odebrána později. Pokud je účet úložiště ve vlastnictví zákazníka propojený s předplatným Cognitive Services, data protokolování se automaticky neodstraní.

## <a name="next-steps"></a>Další kroky

* [Naučte se používat vlastní model.](how-to-specify-source-language.md)

## <a name="additional-resources"></a>Další zdroje informací

- [Příprava a testování dat](./how-to-custom-speech-test-and-train.md)
- [Kontrola dat](how-to-custom-speech-inspect-data.md)
- [Vyhodnocení dat](how-to-custom-speech-evaluate-data.md)
