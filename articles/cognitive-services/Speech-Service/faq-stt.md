---
title: Časté otázky týkající se řeči na text
titleSuffix: Azure Cognitive Services
description: Získejte odpovědi na nejčastější dotazy týkající se rozpoznávání řeči s textovou službou.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 08/20/2020
ms.author: panosper
ms.openlocfilehash: a6f75a80fd73844c975b332db8a0e8919cde9f0d
ms.sourcegitcommit: 3af12dc5b0b3833acb5d591d0d5a398c926919c8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2021
ms.locfileid: "98072169"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Časté otázky týkající se řeči na text

Pokud nemůžete najít odpovědi na vaše otázky v těchto nejčastějších dotazech, podívejte se na [Další možnosti podpory](../cognitive-services-support-options.md?context=%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext%253fcontext%253d%2fazure%2fcognitive-services%2fspeech-service%2fcontext%2fcontext).

## <a name="general"></a>Obecné

**Otázka: Jaký je rozdíl mezi směrným modelem a vlastním převodem textu na textový model?**

Odpověď **: základní** model byl vyučen pomocí dat vlastněných společností Microsoft a již je nasazen v cloudu. Vlastní model můžete použít k přizpůsobení modelu, aby lépe vyhovoval konkrétnímu prostředí, které má určitý okolní hluk nebo jazyk. Podlahová výroba, automobily nebo převozy na vysokou úroveň by vyžadovala upravený akustický model. Témata, jako jsou biologie, fyzika, radiologie, názvy produktů a vlastní akronymy, vyžadují upravený jazykový model. Pokud vytvoříte vlastní model, měli byste začít se souvisejícím textem, abyste vylepšili rozpoznávání speciálních pojmů a frází.

**Otázka: kde se mám začít používat základní model?**

Odpověď **: nejdřív** Získejte [klíč předplatného](overview.md#try-the-speech-service-for-free). Chcete-li provést volání REST do přednasazených standardních modelů, přečtěte si téma [rozhraní REST API](./overview.md#reference-docs). Pokud chcete použít objekty WebSockets, [Stáhněte si sadu SDK](speech-sdk.md).

**Otázka: Musím vždy vytvořit vlastní model řeči?**

Odpověď **: ne**. Pokud vaše aplikace používá obecný a každodenní jazyk, nemusíte model upravovat. Pokud se vaše aplikace používá v prostředí, kde je málo nebo bez hluku na pozadí, nemusíte model upravovat.

Na portálu můžete nasadit základní a přizpůsobené modely a pak pro ně spustit testy přesnosti. Pomocí této funkce lze změřit přesnost směrného modelu oproti vlastnímu modelu.

**Otázka: Jak poznám, že zpracování pro datovou sadu nebo model je dokončeno?**

Odpověď **: v** současné době je stav modelu nebo datové sady v tabulce jediným způsobem, jak znát. Po dokončení zpracování se stav **podařilo**.

**Otázka: mohu vytvořit více než jeden model?**

Odpověď **: neexistuje** žádné omezení počtu modelů, které v kolekci můžete mít.

**Otázka: jsem mi udělal chybu. Návody zrušit import dat nebo vytvoření modelu, který probíhá?**

Odpověď **: v** současné době nemůžete vrátit zpět akustický nebo přizpůsobený proces úprav jazyka. Importovaná data a modely můžete odstranit, pokud jsou ve stavu terminálu.

**Otázka: získám několik výsledků pro každou frázi s detailním výstupním formátem. Který z nich mám použít?**

Odpověď **: vždy** Vezměte první výsledek, i když jiný výsledek ("N-nejlepší") může mít vyšší hodnotu spolehlivosti. Služba rozpoznávání řeči považuje první výsledek za nejlepší. Pokud není rozpoznáno rozpoznávání řeči, může být také prázdným řetězcem.

Další výsledky jsou pravděpodobně horší a nemusí mít úplná velká písmena a použitá interpunkce. Tyto výsledky jsou nejužitečnější ve zvláštních scénářích, jako je například poskytnutí možnosti výběru oprav ze seznamu nebo manipulace s nesprávně rozpoznanými příkazy.

**Otázka: Proč existují odlišné základní modely?**

Odpověď **: můžete** vybrat z více než jednoho základního modelu ve službě Speech. Každý název modelu obsahuje datum, kdy bylo přidáno. Když začnete s školením vlastního modelu, získáte nejlepší přesnost pomocí nejnovějšího modelu. Starší základní modely jsou stále k dispozici po nějakou dobu, kdy je k dispozici nový model. Můžete dál používat model, se kterým jste pracovali, až do jeho vyřazení (viz [životní cyklus modelu](custom-speech-overview.md#model-lifecycle)). Pro lepší přesnost se stále doporučuje přepnout na nejnovější základní model.

**Otázka: mohu aktualizovat existující model (skládání modelů)?**

Odpověď **: nelze** aktualizovat existující model. Jako řešení Zkombinujte starou datovou sadu s novou datovou sadou a proveďte novou úpravu.

Starou datovou sadu a novou datovou sadu musí být sloučeny do jednoho souboru. zip (pro akustická data) nebo do souboru. txt (pro data jazyka). Po dokončení přizpůsobení je potřeba znovu nasadit nový aktualizovaný model, aby se získal nový koncový bod.

**Otázka: je-li k dispozici nová verze základního modelu, je moje nasazení automaticky aktualizováno?**

Odpověď **: nasazení** nebudou automaticky aktualizována.

Pokud jste model přizpůsobené a nasadili, zůstane nasazení tak tak, jak je. Nasazený model můžete vyřadit z provozu, znovu ho upravit pomocí novější verze základního modelu a znovu nasadit pro lepší přesnost.

Základní modely i vlastní modely budou po nějakou dobu vyřazeny (viz [životní cyklus modelu](custom-speech-overview.md#model-lifecycle)).

**Otázka: můžu stáhnout svůj model a spustit ho místně?**

Odpověď **: můžete** spustit vlastní model místně v [kontejneru Docker](speech-container-howto.md?tabs=cstt).

**Otázka: mohu zkopírovat nebo přesunout své datové sady, modely a nasazení do jiné oblasti nebo předplatného?**

Odpověď **: můžete** použít [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription) ke zkopírování vlastního modelu do jiné oblasti nebo předplatného. Datové sady nebo nasazení nelze zkopírovat. Datovou sadu můžete znovu naimportovat do jiného předplatného a vytvořit koncové body pomocí kopií modelu.

**Otázka: jsou protokolovány moje požadavky?**

Odpověď **: ve** výchozím nastavení se požadavky neprotokolují (ani zvuk ani přepis). V případě potřeby můžete při [vytváření vlastního koncového bodu](./how-to-custom-speech-train-model.md)vybrat *obsah protokolu z této možnosti koncového bodu* . Můžete také povolit protokolování zvuku v [sadě Speech SDK](speech-sdk.md) na základě jednotlivých požadavků bez vytvoření vlastního koncového bodu. V obou případech budou výsledky zvuku a rozpoznávání požadavků uloženy v zabezpečeném úložišti. U předplatných, která používají úložiště vlastněná společností Microsoft, budou k dispozici po dobu 30 dnů.

Pokud používáte vlastní koncový bod s *obsahem protokolu z tohoto koncového bodu* , můžete soubory protokolu exportovat na stránce nasazení v nástroji Speech Studio. Pokud je povoleno protokolování zvuku prostřednictvím sady SDK, zavolejte [rozhraní API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/GetBaseModelLogs) pro přístup k souborům.

**Otázka: jsou omezeny moje požadavky?**

Odpověď: viz [kvóty a limity služby Speech Services](speech-services-quotas-and-limits.md).

**Otázka: jak se mi účtuje zvuk Dual Channel?**

Odpověď **: Pokud** odešlete každý kanál samostatně (každý kanál ve vlastním souboru), bude se vám účtovat doba trvání každého souboru. Pokud odešlete jeden soubor s každým multiplexem kanálu, bude se vám účtovat doba trvání jednoho souboru. Podrobnosti o cenách najdete na [stránce s cenami za Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Pokud máte k dispozici další aspekty ochrany osobních údajů, které vám zabrání v používání služby Custom Speech Service, obraťte se na jeden z kanálů podpory.

## <a name="increasing-concurrency"></a>Zvýšení souběžnosti
Viz [kvóty a limity služby Speech Services](speech-services-quotas-and-limits.md).


## <a name="importing-data"></a>Importing data

**Otázka: Jaký je limit velikosti datové sady a proč je limit?**

Odpověď **: omezení** je způsobeno omezením velikosti souboru pro NAHRÁNÍ protokolu HTTP. Skutečný limit najdete v tématu [kvóty a limity služby Speech Services](speech-services-quotas-and-limits.md) . Data můžete rozdělit do více datových sad a vybrat všechny z nich pro výuku modelu.

**Otázka: můžu odeslat textové soubory, aby mohl nahrát větší textový soubor?**

Odpověď **: ne**. V současné době jsou povoleny pouze nekomprimované textové soubory.

**Otázka: v sestavě dat se zobrazí zpráva, že došlo k chybě projevy. Jaký je problém?**

**O**: selhání nahrání 100% projevy v souboru není problém. Pokud je velká většina projevy v akustické nebo jazykové datové sadě (například více než 95 procent) úspěšně naimportována, lze datovou sadu použít. Doporučujeme však, abyste se seznámili s tím, proč se projevy nezdařila, a opravili problémy. Nejběžnější problémy, jako jsou například chyby formátování, lze snadno opravit.

## <a name="creating-an-acoustic-model"></a>Vytvoření akustického modelu

**Otázka: kolik akustických dat potřebuji?**

Odpověď **: doporučujeme** začít od 30 minut a jedné hodiny akustických dat.

**Otázka: jaká data mám shromažďovat?**

Odpověď **: Shromážděte** data, která jsou blízko scénáře použití aplikace, a případ použití. Shromažďování dat by se mělo shodovat s cílovou aplikací a uživateli z pohledu zařízení nebo zařízení, prostředí a typů mluvčích. Obecně byste měli shromažďovat data z co největší škály mluvčích mluvčích.

**Otázka: Jak mám shromažďovat akustická data?**

Odpověď: můžete vytvořit samostatnou aplikaci pro shromažďování dat nebo použít software pro záznam **zvuku z provozu**. Můžete také vytvořit verzi aplikace, která zaznamená zvuková data a následně data použije.

**Otázka: Potřebuji přepisovat přizpůsobit data?**

Odpověď **: Ano**. Můžete ji přepisovat sami nebo použít profesionální přepisovou službu. Někteří uživatelé upřednostňují profesionální transcribers a jiné používají crowdsourcingový nebo mají vlastní přepisy.

**Otázka: jak dlouho bude trvat vlastní model se zvukovými daty?**

Odpověď **: školení** modelu se zvukovými daty může být zdlouhavý proces. V závislosti na množství dat může vytvoření vlastního modelu trvat několik dní. Pokud nemůže být dokončena do jednoho týdne, služba může přerušit operaci školení a ohlásit model jako neúspěšný.

Pro dosažení rychlejších výsledků použijte jednu z [oblastí](custom-speech-overview.md#set-up-your-azure-account) , kde je k dispozici vyhrazený hardware pro školení. Obecně platí, že služba zpracovává přibližně 10 hodin zvukových dat za den v oblastech s takovým hardwarem. Může zpracovat pouze 1 hodinu zvukových dat za den v jiných oblastech. Plně vyškolený model můžete zkopírovat do jiné oblasti pomocí [REST API](https://centralus.dev.cognitive.microsoft.com/docs/services/speech-to-text-api-v3-0/operations/CopyModelToSubscription). Školení s pouhým textem je mnohem rychlejší a obvykle se dokončí během několika minut.

Některé základní modely nejde přizpůsobit pomocí zvukových dat. V případě, že služba bude pouze používat text přepisu pro účely školení a ignorování zvukových dat. Školení se pak dokončí mnohem rychleji a výsledky budou stejné jako školení jenom s textem.

## <a name="accuracy-testing"></a>Testování přesnosti

**Otázka: co je počet chyb aplikace Word (WER) a jak je vypočítána?**

Odpověď **: wer** je metrika vyhodnocení pro rozpoznávání řeči. Služba WER se počítá jako celkový počet chyb, včetně vložení, odstranění a nahrazení, dělený celkovým počtem slov v přepisu odkazu. Další informace najdete v tématu [vyhodnocení přesnosti Custom Speech](how-to-custom-speech-evaluate-data.md#evaluate-custom-speech-accuracy).

**Otázka: Návody určení, zda jsou výsledky testu přesnosti dobré?**

Odpověď **: výsledky** znázorňují porovnání mezi směrným modelem a modelem, který jste přizpůsobili. Měli byste se zaměřit na přesměrovat základní model, aby bylo přizpůsobení vhodné.

**Otázka: Návody určení služby WER základního modelu, aby bylo možné zjistit, zda došlo k vylepšení?**

Odpověď **: výsledky** offline testu ukazují přesnost směrného plánu vlastního modelu a zlepšení oproti směrnému plánu.

## <a name="creating-a-language-model"></a>Vytvoření jazykového modelu

**Otázka: kolik textových dat Potřebuji nahrávat?**

Odpověď **: závisí** na tom, jak se liší slovní a slovní fráze používané ve vaší aplikaci od počátečních jazykových modelů. Pro všechna nová slova je vhodné zadat tolik příkladů, kolik jich může použití těchto slov. Pro běžné fráze, které jsou používány ve vaší aplikaci, včetně frází v jazykových datech, je také užitečné, protože systém oznamuje, že se tyto výrazy naslouchají. Je běžné mít minimálně 100 a obvykle několik stovek nebo více projevy v datové sadě jazyka. Pokud se očekává, že některé typy dotazů budou běžnější než jiné, můžete do datové sady vložit více kopií běžných dotazů.

**Otázka: mohu nahrát jenom seznam slov?**

Odpověď: nahráním seznamu slov budou do slovníku přidána slova, ale nezpůsobí systém, jak jsou slova obvykle používána. Poskytnutím úplných nebo částečných projevy (vět nebo frází věcí, které uživatelé nejspíš říkají), se může jazykový model naučit nová slova a jejich použití. Vlastní jazykový model je dobrý, nejen pro přidávání nových slov do systému, ale také pro úpravu pravděpodobnosti známých slov pro vaši aplikaci. Poskytování úplných projevy pomáhá lépe se seznámit se systémem.

## <a name="tenant-model-custom-speech-with-microsoft-365-data"></a>Model tenanta (Custom Speech s daty Microsoft 365)

**Otázka: Jaké informace jsou součástí modelu tenanta a jak se vytvoří?**

**A:** Model tenanta se sestavuje pomocí e-mailů a dokumentů [veřejných skupin](https://support.microsoft.com/office/learn-about-microsoft-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) , které může kdokoli ve vaší organizaci vidět.

**Otázka: Jaké prostředí pro rozpoznávání řeči vylepšuje model tenanta?**

**A:** Když je model tenanta povolený, vytvořený a publikovaný, používá se ke zlepšení rozpoznávání pro všechny podnikové aplikace vytvořené pomocí služby Speech. Tím se také předá uživatelský token Azure AD, který indikuje členství v podniku.

Prostředí pro rozpoznávání řeči, která jsou integrovaná do Microsoft 365, jako je například popis diktování a aplikace PowerPoint, se při vytváření modelu tenanta pro aplikace služby Speech nemění.

## <a name="next-steps"></a>Další kroky

- [Řešení potíží](troubleshooting.md)
- [Poznámky k verzi](releasenotes.md)