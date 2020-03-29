---
title: Projev na text nejčastější chátých otázek
titleSuffix: Azure Cognitive Services
description: Získejte odpovědi na nejčastější dotazy týkající se služby Řeč na text.
services: cognitive-services
author: PanosPeriorellis
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/4/2019
ms.author: panosper
ms.openlocfilehash: a279aebdd19ebd3a41ddad0c1c279937e00838c2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77168466"
---
# <a name="speech-to-text-frequently-asked-questions"></a>Projev na text nejčastější chátých otázek

Pokud v těchto nejčastějších dotazech nemůžete najít odpovědi na své otázky, podívejte se na [další možnosti podpory](support.md).

## <a name="general"></a>Obecné

**Otázka: Jaký je rozdíl mezi modelem směrného plánu a vlastním modelem převodu na text?**

**A**: Základní model byl trénovaný pomocí dat vlastněných microsoftem a je již nasazen v cloudu. Vlastní model můžete použít k přizpůsobení modelu tak, aby lépe vyhovoval určitému prostředí, které má určitý okolní hluk nebo jazyk. Tovární podlahy, auta nebo hlučné ulice by vyžadovaly přizpůsobený akustický model. Témata jako biologie, fyzika, radiologie, názvy produktů a vlastní zkratky by vyžadovaly přizpůsobený jazykový model.

**Otázka: Kde mám začít, pokud chci použít základní model?**

**A**: Nejprve získejte [klíč předplatného](get-started.md). Pokud chcete provést volání REST k předem nasazeným modelům směrného plánu, přečtěte si informace o [souborech REST API](rest-apis.md). Chcete-li použít websockets, [stáhněte si sadu SDK](speech-sdk.md).

**Otázka: Musím vždy vytvořit vlastní řečový model?**

**A:** Ne. Pokud vaše aplikace používá obecný, každodenní jazyk, nemusíte přizpůsobit model. Pokud se vaše aplikace používá v prostředí, kde je malý nebo žádný šum na pozadí, není nutné přizpůsobit model.

Můžete nasadit směrný plán a přizpůsobené modely na portálu a pak spustit testy přesnosti proti nim. Tuto funkci můžete použít k měření přesnosti modelu směrného plánu oproti vlastnímu modelu.

**Otázka: Jak poznám, že je zpracování datové sady nebo modelu dokončeno?**

**A**: V současné době stav modelu nebo datové sady v tabulce je jediný způsob, jak zjistit. Po dokončení zpracování je stav **succeeded**.

**Otázka: Mohu vytvořit více než jeden model?**

**A:** Neexistuje žádný limit na počet modelů, které můžete mít ve své sbírce.

**Otázka: Uvědomil jsem si, že jsem udělal chybu. Jak zruším import dat nebo vytváření modelů, které právě probíhá?**

**Odpověď:** V současné době nelze vrátit zpět proces akustické nebo jazykové adaptace. Importovaná data a modely můžete odstranit, pokud jsou v terminálovém stavu.

**Otázka: Jaký je rozdíl mezi modelem vyhledávání a diktování a konverzačním modelem?**

**A**: Můžete si vybrat z více než jednoho základního modelu ve službě Řeč. Konverzační model je užitečný pro rozpoznávání řeči, která je mluvená v konverzačním stylu. Tento model je ideální pro přepis telefonních hovorů. Model Vyhledávání a diktování je ideální pro hlasem spouštěné aplikace. Univerzální model je nový model, jehož cílem je řešit oba scénáře. Univerzální model je aktuálně na nebo nad úroveň kvality konverzační model ve většině národních prostředí.

**Otázka: Lze aktualizovat svůj stávající model (stohování modelu)?**

**A**: Existující model nelze aktualizovat. Jako řešení zkombinujte starou datovou sadu s novou datovou sadou a znovu se přizpůsobte.

Stará datová sada a nová datová sada musí být sloučeny do jednoho souboru ZIP (pro akustická data) nebo do souboru TXT (pro jazyková data). Po dokončení úpravy je třeba nový aktualizovaný model znovu nasadit, aby získal nový koncový bod.

**Otázka: Je moje nasazení automaticky aktualizováno, pokud je k dispozici nová verze směrného plánu?**

**A**: Nasazení nebudou automaticky aktualizována.

Pokud jste upravili a nasadili model se směrným plánem V1.0, toto nasazení zůstane tak, jak je. Zákazníci mohou vyřadit z provozu nasazený model, znovu se přizpůsobit pomocí novější verze směrného plánu a znovu nasadit.

**Otázka: Mohu stáhnout model a spustit jej místně?**

**A**: Modely nelze stáhnout a spustit místně.

**Otázka: Jsou moje požadavky zaznamenány?**

**A**: Máte na výběr při vytváření nasazení pro vypnutí trasování. V tomto okamžiku nebude zaznamenán žádný zvuk nebo přepisy. V opačném případě se požadavky obvykle zaznamenávají v Azure v zabezpečeném úložišti.

**Otázka: Jsou mé požadavky omezeny?**

**A**: Rozhraní REST API omezuje požadavky na 25 za 5 sekund. Podrobnosti naleznete na našich stránkách pro [řeč na text](speech-to-text.md).

**Otázka: Jak se mi účtují za dvoukanálový zvuk?**

**A**: Pokud odešlete každý kanál samostatně (každý kanál ve svém vlastním souboru), bude vám účtována doba trvání každého souboru. Pokud odešlete jeden soubor s každým kanálem multiplexem dohromady, bude vám účtována doba trvání jednoho souboru. Podrobnosti o cenách najdete na [stránce s cenami azure cognitive services](https://azure.microsoft.com/pricing/details/cognitive-services/speech-services/).

> [!IMPORTANT]
> Pokud máte další obavy o ochranu osobních údajů, které vám zakazují používat vlastní službu Speech, obraťte se na jeden z kanálů podpory.

## <a name="increasing-concurrency"></a>Zvýšení souběžnosti

**Otázka: Co když potřebuji vyšší souběžnost pro nařízený model, než co je nabízeno na portálu?**

**A**: Můžete vertikálně navýšit kapacitu modelu v krocích po 20 souběžných požadavků.

S požadovanými informacemi vytvořte žádost o podporu na [portálu podpory Azure](https://ms.portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/overview). Nezveřejňujte informace o žádném z veřejných kanálů (GitHub, Stackoverflow, ...) uvedených na [stránce podpory](support.md).

Chcete-li zvýšit souběžnost pro ***vlastní model***, potřebujeme následující informace:

- Oblast, kde je model nasazen,
- ID koncového bodu nasazeného modelu:
  - Dostal jsem se na [portál vlastní řeči](https://aka.ms/customspeech),
  - přihlášení (v případě potřeby),
  - vyberte svůj projekt a nasazení,
  - vyberte koncový bod, pro který potřebujete zvýšení souběžnosti,
  - zkopírujte `Endpoint ID`.

Chcete-li zvýšit souběžnost pro ***základní model***, potřebujeme následující informace:

- Oblast vašich služeb,

a to buď

- přístupový token pro vaše předplatné (viz [zde](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-speech-to-text#how-to-get-an-access-token)),

– nebo –

- ID prostředku pro vaše předplatné:
  - Přejděte na [portál Azure](https://portal.azure.com),
  - vyberte `Cognitive Services` do vyhledávacího pole,
  - ze zobrazených služeb vyberte službu Řeči, pro kterou chcete zvýšit souběžnost,
  - zobrazit `Properties` pro tuto službu,
  - zkopírujte `Resource ID`celý .

## <a name="importing-data"></a>Importing data

**Otázka: Jaký je limit velikosti datové sady a proč je limitem?**

**A**: Aktuální limit pro datovou sadu je 2 GB. Limit je způsoben omezením velikosti souboru pro nahrávání http.

**Otázka: Mohu soubory scvokovat do zipu, abych mohl nahrát větší textový soubor?**

**A:** Ne. V současné době jsou povoleny pouze nekomprimované textové soubory.

**Otázka: Sestava dat říká, že byly neúspěšné projevy. V čem je problém?**

**A**: Nenahrání 100 procent projevy v souboru není problém. Pokud drtivá většina projevy v akustické nebo jazykové datové sady (například více než 95 procent) jsou úspěšně importovány, datová sada může být použitelné. Doporučujeme však pokusit se pochopit, proč projevy se nezdařilo a opravit problémy. Nejčastější problémy, jako jsou chyby formátování, lze snadno opravit.

## <a name="creating-an-acoustic-model"></a>Vytvoření akustického modelu

**Otázka: Kolik akustických dat potřebuji?**

**A**: Doporučujeme začít s mezi 30 minut a jednu hodinu akustických dat.

**Otázka: Jaké údaje mám shromažďovat?**

**A**: Shromažďovat data, která je co nejblíže k scénáři aplikace a případ použití, jak je to možné. Shromažďování dat by mělo odpovídat cílové aplikaci a uživatelům z hlediska zařízení nebo zařízení, prostředí a typů reproduktorů. Obecně byste měli shromažďovat data z co nejširšího okruhu řečníků.

**Otázka: Jak mám shromažďovat akustická data?**

**A**: Můžete vytvořit samostatnou aplikaci pro shromažďování dat nebo použít software pro nahrávání zvuku mimo polici. Můžete také vytvořit verzi aplikace, která protokoluje zvuková data a potom data použije.

**Otázka: Musím přepsat adaptační data sám?**

**A:** Ano! Můžete přepsat sami nebo použít profesionální transkripční službu. Někteří uživatelé dávají přednost profesionálním přepisovatelům a jiní používají crowdsourcing nebo přepisy dělají sami.

## <a name="accuracy-testing"></a>Testování přesnosti

**Otázka: Lze provést offline testování vlastního akustického modelu pomocí vlastního jazykového modelu?**

**A:** Ano, stačí vybrat vlastní jazykový model v rozevírací nabídce při nastavovat offline test.

**Otázka: Lze provést offline testování vlastního jazykového modelu pomocí vlastního akustického modelu?**

**A:** Ano, stačí vybrat vlastní akustický model v rozevírací nabídce při nastavovat offline test.

**Otázka: Co je chybová frekvence slov (WER) a jak se počítá?**

**A**: WER je metrika hodnocení pro rozpoznávání řeči. WER se počítá jako celkový počet chyb, který zahrnuje vložení, odstranění a nahrazení, děleno celkovým počtem slov v přepisu odkazu. Další informace naleznete v tématu [chybovost slov](https://en.wikipedia.org/wiki/Word_error_rate).

**Otázka: Jak zjistím, zda jsou výsledky testu přesnosti dobré?**

**A**: Výsledky ukazují porovnání mezi modelem základní linie a modelem, který jste přizpůsobili. Měli byste se snažit porazit základní model, aby přizpůsobení stálo za to.

**Otázka: Jak zjistím WER základního modelu, abych zjistil, zda došlo ke zlepšení?**

**A**: Výsledky offline testu ukazují přesnost směrného plánu vlastního modelu a zlepšení oproti směrnému plánu.

## <a name="creating-a-language-model"></a>Vytvoření jazykového modelu

**Otázka: Kolik textových dat je potřeba nahrát?**

**A**: Záleží na tom, jak odlišné slovní zásoby a fráze používané ve vaší aplikaci jsou z výchozí jazykové modely. Pro všechna nová slova je užitečné poskytnout co nejvíce příkladů použití těchto slov. Pro běžné fráze, které se používají ve vaší aplikaci, včetně frází v datech jazyka je také užitečné, protože říká systému také naslouchat pro tyto termíny. Je běžné mít alespoň 100 a obvykle několik set nebo více projevy v datové sadě jazyka. Také pokud některé typy dotazů se očekává, že bude běžnější než ostatní, můžete vložit více kopií běžných dotazů v datové sadě.

**Otázka: Mohu jen nahrát seznam slov?**

**A**: Nahráním seznamu slov přidáte slova do slovní zásoby, ale nebude učit systém, jak se slova obvykle používají. Poskytnutím úplné nebo částečné projevy (věty nebo fráze věcí, které uživatelé pravděpodobně řeknou), jazykový model může naučit nová slova a jak jsou používány. Vlastní jazykový model je vhodná nejen pro přidávání nových slov do systému, ale také pro úpravu pravděpodobnosti známých slov pro vaši aplikaci. Poskytování úplné projevy pomáhá systému lépe učit.

## <a name="tenant-model-custom-speech-with-office-365-data"></a>Model klienta (vlastní řeč s daty Office 365)

**Otázka: Jaké informace jsou zahrnuty v modelu klienta a jak se vytvoří?**

**A:** Model klienta je sestaven pomocí [e-mailů a dokumentů veřejné skupiny,](https://support.office.com/article/learn-about-office-365-groups-b565caa1-5c40-40ef-9915-60fdb2d97fa2) které může zobrazit kdokoli ve vaší organizaci.

**Otázka: Jaké prostředí řeči vylepšuje model klienta?**

**A:** Když je model klienta povolen, vytvořen a publikován, slouží ke zlepšení rozpoznávání pro všechny podnikové aplikace vytvořené pomocí služby Speech; které také předat token AAD uživatele označující členství v rozlehlé síti.

Prostředí řeči integrované v Office 365, jako je diktování a titulky v PowerPointu, se nezmění při vytváření modelu tenanta pro aplikace služby Speech.

## <a name="next-steps"></a>Další kroky

- [Řešení potíží](troubleshooting.md)
- [Poznámky k verzi](releasenotes.md)
