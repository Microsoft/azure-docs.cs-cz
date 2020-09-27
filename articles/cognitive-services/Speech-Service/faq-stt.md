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
ms.openlocfilehash: 0fe5b1350fdcf53eeda11f6de430d8d02a24fdbf
ms.sourcegitcommit: 4313e0d13714559d67d51770b2b9b92e4b0cc629
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/27/2020
ms.locfileid: "91397755"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Časté otázky týkající se řeči na text

Pokud nemůžete najít odpovědi na vaše otázky v těchto nejčastějších dotazech, podívejte se na [Další možnosti podpory](support.md).

## <a name="general"></a>Obecné

**Otázka: Jaký je rozdíl mezi směrným modelem a vlastním převodem textu na textový model?**

Odpověď **: základní**model byl vyučen pomocí dat vlastněných společností Microsoft a již je nasazen v cloudu. Vlastní model můžete použít k přizpůsobení modelu, aby lépe vyhovoval konkrétnímu prostředí, které má určitý okolní hluk nebo jazyk. Podlahová výroba, automobily nebo převozy na vysokou úroveň by vyžadovala upravený akustický model. Témata, jako jsou biologie, fyzika, radiologie, názvy produktů a vlastní akronymy, vyžadují upravený jazykový model.

**Otázka: kde se mám začít používat základní model?**

Odpověď **: nejdřív**Získejte [klíč předplatného](overview.md#try-the-speech-service-for-free). Chcete-li provést volání REST do přednasazených standardních modelů, přečtěte si téma [rozhraní REST API](rest-apis.md). Pokud chcete použít objekty WebSockets, [Stáhněte si sadu SDK](speech-sdk.md).

**Otázka: Musím vždy vytvořit vlastní model řeči?**

Odpověď **: ne**. Pokud vaše aplikace používá obecný a každodenní jazyk, nemusíte model upravovat. Pokud se vaše aplikace používá v prostředí, kde je málo nebo bez hluku na pozadí, nemusíte model upravovat.

Na portálu můžete nasadit základní a přizpůsobené modely a pak pro ně spustit testy přesnosti. Pomocí této funkce lze změřit přesnost směrného modelu oproti vlastnímu modelu.

**Otázka: Jak poznám, že zpracování pro datovou sadu nebo model je dokončeno?**

Odpověď **: v**současné době je stav modelu nebo datové sady v tabulce jediným způsobem, jak znát. Po dokončení zpracování se stav **podařilo**.

**Otázka: mohu vytvořit více než jeden model?**

Odpověď **: neexistuje**žádné omezení počtu modelů, které v kolekci můžete mít.

**Otázka: jsem mi udělal chybu. Návody zrušit import dat nebo vytvoření modelu, který probíhá?**

Odpověď **: v**současné době nemůžete vrátit zpět akustický nebo přizpůsobený proces úprav jazyka. Importovaná data a modely můžete odstranit, pokud jsou ve stavu terminálu.

**Otázka: Jaký je rozdíl mezi modelem vyhledávání a diktování a modelem konverzace?**

Odpověď **: můžete**vybrat z více než jednoho směrného modelu ve službě Speech Service. Model konverzace je užitečný pro rozpoznávání řeči, která se používá ve stylu konverzace. Tento model je ideální pro zdlouhavého přepisování telefonní hovory. Model hledání a diktování je ideální pro aplikace aktivované hlasem. Univerzální model je nový model, který se zaměřuje na řešení obou scénářů. Univerzální model je aktuálně na úrovni kvality v rámci prostředí ve většině místních hodnot nebo nad ním.

**Otázka: mohu aktualizovat existující model (skládání modelů)?**

Odpověď **: nelze**aktualizovat existující model. Jako řešení Zkombinujte starou datovou sadu s novou datovou sadou a proveďte novou úpravu.

Starou datovou sadu a novou datovou sadu musí být sloučeny do jednoho souboru. zip (pro akustická data) nebo do souboru. txt (pro data jazyka). Po dokončení přizpůsobení je potřeba znovu nasadit nový aktualizovaný model, aby se získal nový koncový bod.

**Otázka: je-li k dispozici nová verze směrného plánu, je moje nasazení automaticky aktualizováno?**

Odpověď **: nasazení**nebudou automaticky aktualizována.

Pokud jste přizpůsobeni a nasadili model pomocí směrného plánu V 1.0, zůstane nasazení tak tak, jak je. Zákazníci mohou vyřadit nasazený model z provozu a znovu je upravit pomocí novější verze směrného plánu a znovu nasadit.

**Otázka: můžu stáhnout svůj model a spustit ho místně?**

**A**: modely nelze stáhnout a spustit místně.

**Otázka: jsou protokolovány moje požadavky?**

Odpověď **: ve**výchozím nastavení se požadavky neprotokolují (ani zvuk ani přepis). V případě potřeby můžete vybrat *obsah protokolu z této možnosti koncového bodu* , když [vytvoříte vlastní koncový bod](how-to-custom-speech-deploy-model.md) , který povolí trasování. Požadavky se pak budou protokolovat v Azure v zabezpečeném úložišti.

**Otázka: jsou omezeny moje požadavky?**

**A**Odpověď: viz [kvóty a limity služby Speech Services](speech-services-quotas-and-limits.md).

**Otázka: jak se mi účtuje zvuk Dual Channel?**

Odpověď **: Pokud**odešlete každý kanál samostatně (každý kanál ve vlastním souboru), bude se vám účtovat doba trvání každého souboru. Pokud odešlete jeden soubor s každým multiplexem kanálu, bude se vám účtovat doba trvání jednoho souboru. Podrobnosti o cenách najdete na [stránce s cenami za Azure Cognitive Services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Pokud máte k dispozici další aspekty ochrany osobních údajů, které vám zabrání v používání služby Custom Speech Service, obraťte se na jeden z kanálů podpory.

## <a name="increasing-concurrency"></a>Zvýšení souběžnosti
Viz [kvóty a limity služby Speech Services](speech-services-quotas-and-limits.md).


## <a name="importing-data"></a>Importing data

**Otázka: Jaký je limit velikosti datové sady a proč je limit?**

Odpověď **: omezení**je způsobeno omezením velikosti souboru pro NAHRÁNÍ protokolu HTTP. Skutečný limit najdete v tématu [kvóty a limity služby Speech Services](speech-services-quotas-and-limits.md) .

**Otázka: můžu odeslat textové soubory, aby mohl nahrát větší textový soubor?**

Odpověď **: ne**. V současné době jsou povoleny pouze nekomprimované textové soubory.

**Otázka: v sestavě dat se zobrazí zpráva, že došlo k chybě projevy. Jaký je problém?**

**O**: selhání nahrání 100% projevy v souboru není problém. Pokud je velká většina projevy v akustické nebo jazykové datové sadě (například více než 95 procent) úspěšně naimportována, lze datovou sadu použít. Doporučujeme však, abyste se seznámili s tím, proč se projevy nezdařila, a opravili problémy. Nejběžnější problémy, jako jsou například chyby formátování, lze snadno opravit.

## <a name="creating-an-acoustic-model"></a>Vytvoření akustického modelu

**Otázka: kolik akustických dat potřebuji?**

Odpověď **: doporučujeme**začít od 30 minut a jedné hodiny akustických dat.

**Otázka: jaká data mám shromažďovat?**

Odpověď **: Shromážděte**data, která jsou blízko scénáře použití aplikace, a případ použití. Shromažďování dat by se mělo shodovat s cílovou aplikací a uživateli z pohledu zařízení nebo zařízení, prostředí a typů mluvčích. Obecně byste měli shromažďovat data z co největší škály mluvčích mluvčích.

**Otázka: Jak mám shromažďovat akustická data?**

Odpověď: můžete vytvořit samostatnou aplikaci pro shromažďování dat nebo použít software pro záznam **zvuku z provozu**. Můžete také vytvořit verzi aplikace, která zaznamená zvuková data a následně data použije.

**Otázka: Potřebuji přepisovat přizpůsobit data?**

Odpověď **: Ano**! Můžete ji přepisovat sami nebo použít profesionální přepisovou službu. Někteří uživatelé upřednostňují profesionální transcribers a jiné používají crowdsourcingový nebo mají vlastní přepisy.

## <a name="accuracy-testing"></a>Testování přesnosti

**Otázka: je možné provést offline testování mého vlastního akustického modelu pomocí vlastního jazykového modelu?**

Odpověď **: Ano**, stačí vybrat vlastní jazykový model v rozevírací nabídce při nastavení offline testu.

**Otázka: je možné provést offline testování vlastního jazykového modelu pomocí vlastního akustického modelu?**

Odpověď **: Ano**, stačí vybrat vlastní akustický model v rozevírací nabídce při nastavení offline testu.

**Otázka: co je počet chyb aplikace Word (WER) a jak je vypočítána?**

Odpověď **: wer**je metrika vyhodnocení pro rozpoznávání řeči. Služba WER se počítá jako celkový počet chyb, včetně vložení, odstranění a nahrazení, dělený celkovým počtem slov v přepisu odkazu. Další informace najdete v tématu [Četnost chyb aplikace Word](https://en.wikipedia.org/wiki/Word_error_rate).

**Otázka: Návody určení, zda jsou výsledky testu přesnosti dobré?**

Odpověď **: výsledky**znázorňují porovnání mezi směrným modelem a modelem, který jste přizpůsobili. Měli byste se zaměřit na přesměrovat základní model, aby bylo přizpůsobení vhodné.

**Otázka: Návody určení služby WER základního modelu, aby bylo možné zjistit, zda došlo k vylepšení?**

Odpověď **: výsledky**offline testu ukazují přesnost směrného plánu vlastního modelu a zlepšení oproti směrnému plánu.

## <a name="creating-a-language-model"></a>Vytvoření jazykového modelu

**Otázka: kolik textových dat Potřebuji nahrávat?**

Odpověď **: závisí**na tom, jak se liší slovní a slovní fráze používané ve vaší aplikaci od počátečních jazykových modelů. Pro všechna nová slova je vhodné zadat tolik příkladů, kolik jich může použití těchto slov. Pro běžné fráze, které jsou používány ve vaší aplikaci, včetně frází v jazykových datech, je také užitečné, protože systém oznamuje, že se tyto výrazy naslouchají. Je běžné mít minimálně 100 a obvykle několik stovek nebo více projevy v datové sadě jazyka. Pokud se očekává, že některé typy dotazů budou běžnější než jiné, můžete do datové sady vložit více kopií běžných dotazů.

**Otázka: mohu nahrát jenom seznam slov?**

**A**Odpověď: nahráním seznamu slov budou do slovníku přidána slova, ale nezpůsobí systém, jak jsou slova obvykle používána. Poskytnutím úplných nebo částečných projevy (vět nebo frází věcí, které uživatelé nejspíš říkají), se může jazykový model naučit nová slova a jejich použití. Vlastní jazykový model je dobrý, nejen pro přidávání nových slov do systému, ale také pro úpravu pravděpodobnosti známých slov pro vaši aplikaci. Poskytování úplných projevy pomáhá lépe se seznámit se systémem.

## <a name="tenant-model-custom-speech-with-microsoft-365-data"></a>Model tenanta (Custom Speech s daty Microsoft 365)

**Otázka: Jaké informace jsou součástí modelu tenanta a jak se vytvoří?**

**A:** Model tenanta se sestavuje pomocí e-mailů a dokumentů [veřejných skupin](https://support.microsoft.com/office/learn-about-microsoft-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) , které může kdokoli ve vaší organizaci vidět.

**Otázka: Jaké prostředí pro rozpoznávání řeči vylepšuje model tenanta?**

**A:** Když je model tenanta povolený, vytvořený a publikovaný, používá se ke zlepšení rozpoznávání pro všechny podnikové aplikace vytvořené pomocí služby Speech. Tím se také předá uživatelský token Azure AD, který indikuje členství v podniku.

Prostředí pro rozpoznávání řeči, která jsou integrovaná do Microsoft 365, jako je například popis diktování a aplikace PowerPoint, se při vytváření modelu tenanta pro aplikace služby Speech nemění.

## <a name="next-steps"></a>Další kroky

- [Řešení potíží](troubleshooting.md)
- [Zpráva k vydání verze](releasenotes.md)
