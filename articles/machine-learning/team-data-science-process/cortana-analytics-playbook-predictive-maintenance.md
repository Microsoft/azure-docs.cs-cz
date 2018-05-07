---
title: Azure AI scénářem pro řešení prediktivní údržby | Microsoft Docs
description: Komplexní popis vědecké zpracování dat, která pohání řešení prediktivní údržby v několika svislé odvětví.
services: cortana-analytics
documentationcenter: ''
author: fboylu
manager: jhubbard
editor: cgronlun
ms.assetid: 2e8b66db-91eb-432b-b305-6abccca25620
ms.service: cortana-analytics
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/01/2018
ms.author: fboylu
ms.openlocfilehash: ad06617fb8c14928dca7d9ce18ad86190e8255fe
ms.sourcegitcommit: c47ef7899572bf6441627f76eb4c4ac15e487aec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/04/2018
---
# <a name="azure-ai-playbook-for-predictive-maintenance-solutions"></a>Azure AI scénářem pro řešení prediktivní údržby 
## <a name="executive-summary"></a>Shrnutí
Prediktivní údržby je jednou z nejčastěji vyžadovaná aplikací prediktivní analýzy unarguable výhod, jako třeba obrovské množství úsporu nákladů. Cílem této scénářem je poskytuje odkaz pro řešení prediktivní údržby s důrazem na případy použití hlavní.
Je připravená umožnit čtečky představu o nejběžnějších obchodní scénáře prediktivní údržby, výzvy opravňujících obchodních problémů pro takové řešení data potřebná k řešení těchto obchodních problémů, techniky prediktivního modelování pro sestavování řešení pomocí tyto údaje a osvědčené postupy s ukázkové architektury řešení.
Také popisuje, jaké jsou specifikace prediktivní modely vyvinutý jako je například funkce technikům, model vyhodnocení vývoj a výkonu. V podstatě této playbook spojuje firmy a analytické pokyny potřebné pro úspěšné vývoj a nasazení řešení prediktivní údržby. Tyto pokyny jsou připravené ke cílovou skupinu vytvoření počáteční řešení pomocí Cortana Intelligence Suite a konkrétně Azure Machine Learning, jako výchozí bod v jejich dlouhodobou strategii prediktivní údržby. Naleznete v dokumentaci týkající se Cortana Intelligence Suite a Azure Machine Learning v [Cortana Analytics](http://www.microsoft.com/server-cloud/cortana-analytics-suite/overview.aspx) a [Azure Machine Learning](https://azure.microsoft.com/services/machine-learning/) stránky.

> [!TIP]
> Technické informace pro implementaci tohoto řešení šablony najdete v tématu [technické příručce k šabloně Cortana Intelligence řešení pro prediktivní údržby](cortana-analytics-technical-guide-predictive-maintenance.md).
> Si můžete stáhnout diagram, který poskytuje přehled architektury této šablony [Architektura šablony Cortana Intelligence řešení prediktivní údržby](cortana-analytics-architecture-predictive-maintenance.md).
> 
> 

## <a name="playbook-overview-and-target-audience"></a>Cílová skupina playbook přehled a cíle
Abyste mohli využívat technická a Netechnická cílové skupiny s různými pozadí a zájmů v prostoru prediktivní údržby je organizovaná tento scénářem. Playbook obsahuje oba souhrnné informace o různých typech řešení prediktivní údržby a podrobnosti o jejich implementaci. Obsah je vyrovnáváním nahrazovat i pro cílové skupiny, které zajímá pouze Princip řešení místa a typ aplikace a také ty, kteří se zaměřujete na implementaci těchto řešení a jsou proto zájem o technické podrobnosti.

Většinu obsahu v této playbook nepředpokládá předchozí data vědecké účely vědomí nebo odborných znalostí. Některé části playbook však bude poněkud vyžadovat znalost dat vědecké účely koncepty, které je možné provést podrobné informace o nasazení. Úvodní dat na úrovni vědecké účely dovednosti vyžadovaných pro plně využívat výhod materiály v těchto částech.

První polovinu playbook obsahuje úvod do aplikace prediktivní údržby, jak k vyfiltrování prediktivní údržby řešení, kolekce běžné případy s podrobnostmi obchodního problému, používání dat, které obaluje tyto případy použití a obchodní výhody implementace těchto řešení prediktivní údržby. Tyto části nevyžadují žádné technické znalosti v doméně prediktivní analýzy.

Ve druhé polovině scénářem nabídneme typy techniky prediktivního modelování pro prediktivní údržby aplikace a jak implementovat tyto modely prostřednictvím příklady z uvedených v první polovinu playbook případy použití. Tento koncept je znázorněn přechodem provede kroky pro předzpracování dat, jako je označování dat a funkce technici, model výběr, školení nebo testování a výkonu vyhodnocení osvědčené postupy. Tyto části jsou vhodné pro technické cílovou skupinu.

## <a name="predictive-maintenance-in-iot"></a>Prediktivní údržby v IoT
Dopad výpadek neplánovanou zařízení může být velmi destruktivní pro firmy. To je důležité mít pole zařízení spuštěné, aby mohl maximalizovat využití a výkonu a pomocí minimalizace nákladná, mimo plánované výpadky. Jednoduše není dostupný v dnešních obchodní operace scény čeká na selhání proběhnout. Zůstat produktivní, vyhledejte společnosti maximalizovat výkon asset tím, že nové způsoby použití dat shromážděných z různých kanálů. Jedním ze způsobů důležité k analýze tyto informace je využívat prediktivní analýzy techniky, využívající historických vzory předpovídat budoucí výsledky. Jeden z nejčastěji používané těchto řešení prediktivní údržby, které může být obvykle nazývá definován jako ale bez omezení, tak, aby proaktivně identifikovat selhání lze sledovat prostředky v blízké budoucnosti predikci možnosti selhání prostředek a Proveďte akci, než dojde k selhání. Tato řešení rozpoznat vzory selhání určit prostředky, které jsou na největší riziko selhání. Tuto časná identifikaci problémů pomáhá nasadit prostředky omezené údržby cenově výhodnější způsobem a vylepšení kvality a zadat řetězec procesy.

S rostoucím aplikací Internet věcí (IoT) se prediktivní údržby získání roste pozornost v odvětví jako shromažďování dat a zpracování technologie používá dostatek generovat, přenášet, ukládat a analyzovat všechny typy z data v dávkách nebo v reálném čase. Tyto technologie umožňují snadný vývoj a nasazení řešení pro kompletní s řešeními pokročilou analýzu, se pravděpodobně poskytuje největší výhody řešení prediktivní údržby.

Obchodní problémy v rozsahu prediktivní Údržba domény od vysoce rizikové provozní z důvodu neočekávané chyby a omezené aspekty hlavní příčinu problémů ve složitém firemním prostředí. Většina těchto problémů může být rozdělena na které se vztahuje na tyto otázky firmy:

* Co je pravděpodobnost, že v blízké budoucnosti selže zařízení?
* Co je zbývající dobu životnosti zařízení?
* Jaké jsou příčiny chyby a jaké akce údržby měla vyřešit tyto problémy?

S využitím prediktivní údržby k odpovědi na tyto otázky, může podnikům:

* Snížit provozní riziko a sledování chyb, než k nim došlo zvýšit návratovou hodnotu na prostředky
* Omezit operace nepotřebné založené na čase údržby a řídit náklady na údržbu
* Zvýšit celkový image značky, eliminovat chybný osobnosti a výsledný ztraceny prodejní z otěr zákazníka.
* Nižší poplatky za inventáře snížením úrovně inventáře Odhadnutím toho, jaká bod
* Zjistit vzorů, které jsou připojené k různé problémy údržby

Řešení prediktivní údržby může podnikům s klíčových ukazatelů výkonu například skóre stavu monitorování v reálném čase asset podmínku, odhad zbývající životnost prostředků, doporučení pro aktivity proaktivní údržby a Odhadované pořadí data určena k nahrazení částí.

## <a name="qualification-criteria-for-predictive-maintenance"></a>Kvalifikace kritéria pro prediktivní údržby
Je nutné zdůraznit, že všechny případy použití nebo lze prediktivní údržby efektivně vyřešit obchodní problémy. Je důležité kvalifikace kritéria zahrnují problém je prediktivní ve své podstatě, který zrušte cestu akce existuje, chcete-li zabránit chybám při zjištění předem a co je nejdůležitější data s dostatečnou kvality pro podporu případ použití k dispozici. Zde zaměříme na požadavky na data pro vytváření řešení prediktivní údržby úspěšné.

Při vytváření prediktivních modelů, používáme historická data pro trénování modelu, který pak rozpoznají skrytá vzory a dále určit tyto vzory v budoucí data. Tyto modely jsou cvičení s příklady popsaného jejich funkcí a cílem předpovědi. Očekává se, že trénovaného modelu provádět předpovědi na cílovém prohlížením pouze funkce nové příklady. Je velmi důležitý, že model zaznamenat vztah mezi funkcí a cílem předpovědi. Chcete-li cvičení efektivní strojového učení modelu, potřebujeme Cvičná data, která obsahuje funkce, které mají ve skutečnosti prediktivní power směrem cíl předpovědi, což znamená, že data by měla být relevantní pro předpověď cílem se očekává přesně odhadnout.

Například pokud cíl je k předvídání selhání train souborů Wheel, jsou Cvičná data by měla obsahovat funkce související s wheel (např. telemetrie odrážející stav souborů Wheel, vzdálenost, car zatížení, atd.). Ale pokud cíl je k předvídání selhání modulu train, pravděpodobně potřebujeme další sadu Cvičná data, která má funkce související se modul. Před vytvořením prediktivní modely, Očekáváme, že firmy odborné pochopit důležitosti požadavek na data a poskytovat znalosti domény, který je potřebný pro vyberte relevantní podmnožiny dat pro analýzu.

Existují tři základní data zdroje, které vyhledáme při určení obchodního problému být vhodný pro řešení prediktivní údržby:

1. Selhání historie: V aplikacích prediktivní údržby událostí selhání jsou obvykle velmi výjimečných. Však při sestavování prediktivní modely, které předpovědi selhání, algoritmus je potřeba další vzoru normálního provozu, jakož i vzoru selhání procesu školení. Proto je nezbytné, že jsou Cvičná data obsahuje dostatečný počet příklady v obou kategorií Chcete-li další tyto dvě různé vzorce. Z tohoto důvodu je nutné, aby data mají dostatečný počet událostí selhání. Události chyb naleznete v záznamech údržby a historii nahrazení částí nebo anomálie v Cvičná data můžete také použít jako selhání identifikovat odborníky domény.
2. Historie údržby nebo opravit: Základní zdroj dat pro řešení prediktivní údržby je podrobné údržby historii asset obsahující informace o součástech nahradit, preventivní údržbě aktivuje provádět atd. Je velmi důležité zaznamenat tyto události jako tyto vliv snížení vzory a bez těchto informací způsobí, že zavádějící výsledky.
3. Počítač podmínky: Aby bylo možné odhadnout, kolik dnů (hodiny, paliva, transakce atd.) na počítač, trvá po předtím, než se nezdaří, předpokládáme, že stav počítače se sníží časem při své činnosti. Proto Očekáváme, že data tak, aby obsahovala čas různých funkcí, které zaznamenat tento vzor stárnutí a anomálie, která vede ke snížení výkonu. V aplikace či aplikace IoT telemetrická data z různých snímače představují jeden dobrým příkladem. Aby bylo možné předpovědět, pokud je počítač se bude selhat v časovém rámci, v ideálním případě data by měla zachytit ponižující trend během tohoto časového rámce před události skutečnou selhání.

Kromě toho je nutné, data, která přímo souvisí s provozních podmínek cílového prostředku předpovědí. Rozhodnutí cíle vychází z obou obchodních potřeb a data dostupnosti. Pořízení předpovědi selhání wheel train jako příklad, jsme může předvídání "Pokud kolečka má dojít k selhání" nebo "Pokud celý train budete mít k chybě". První z nich cílí více konkrétní součást, zatímco druhý cílem selhání vlaku. Druhá je zadat další obecné otázku, která vyžaduje mnohem víc rozmístěné datové prvky než první z nich, což těžší k sestavení modelu. Při pokusu o předpovědi selhání wheel podle vzhledu data podmínky vysoké úrovně train naopak nemusí být vhodný, protože neobsahuje informace na úrovni součásti. Obecně platí je více rozumný k předvídání konkrétní chyby události než ty, které další obecné jsou.

Jeden běžné otázku, která se obvykle zobrazí výzva, o data historie selhání je "jak mnoho událostí selhání je potřeba trénování modelu a kolik se považuje za"dostatek"? Neexistuje žádná zrušte odpověď na otázku stejně jako mnoho scénářů prediktivní analýzy, je obvykle kvalitu dat, která určuje, jaké jsou přijatelné. Pokud datová sada neobsahuje funkce, které jsou relevantní pro předpovědi selhání, pak i v případě, že existuje mnoho událostí selhání vytváření správný model nemusí být možné. Pravidlo je však, Další selhání události tím lepší je model a odhad počtu selhání příklady jsou požadovány je velmi kontextu a měr závislé na data. Tento problém je popsán v části pro zpracování imbalanced datové sady, kde jsme navrhovat postupy zvládnout problém nemá dostatek selhání.

## <a name="sample-use-cases"></a>Ukázka případy použití
Tato část se zaměřuje na kolekci případy použití prediktivní údržby z několika odvětví, jako je například letecký, nástroje a Transport. Každý část prochází do shromážděné z těchto oblastí případy použití a popisuje obchodních problémů, které obaluje obchodního problému a výhody řešení prediktivní údržby data.

### <a name="aerospace"></a>Letecký
#### <a name="use-case-1-flight-delay-and-cancellations"></a>Případ použití 1: Zpoždění letu a zrušení
##### <a name="business-problem-and-data-sources"></a>*Obchodní problém a zdroje dat.*
Jeden z hlavních obchodní problémy, které airlines čelí, je důležité náklady, které jsou spojené s lety se zpožďuje kvůli mechanických problémy. Pokud mechanických selhání nelze opravit, může i lety zrušit. To je velmi nákladné, jako je vytváření zpoždění problémy v plánování a operací, chybný pověsti příčiny a nespokojenosti zákazníka spolu s mnoha jiných problémů. Airlines zájem o zvlášť predikci takových mechanických selhání předem, aby se může snížit zpoždění letů nebo zrušení. Cílem řešení prediktivní údržby pro tyto případy je k předvídání pravděpodobnost letadel odložené nebo zrušit, podle příslušné zdroje dat například informace o postupu údržby historie a letu. Dvou zdrojů hlavní data pro tento případ použití jsou letu nožičky a protokoly stránky. Data pohybující se větev zahrnuje data o podrobnosti postupu letu, jako je například datum a čas odeslání a přijetí, odeslání a přijetí letištích atd. Data protokolu stránky obsahuje řadu chyb a údržbě kódy, které se zaznamenávají pracovníky údržby.

##### <a name="business-value-of-the-predictive-model"></a>*Obchodní hodnotu prediktivního modelu*
Použití dostupných historických dat, prediktivního modelu bylo vytvořeno prostřednictvím několika klasifikační algoritmus k předvídání typ mechanických problém, což vede k zpoždění nebo zrušení letu během dalších 24 hodin. Tím, že tento předpovědi, můžete provedeny akce potřebné údržby zmírnit riziko, když je prováděn servis letadel a tím zabránit možné zpoždění nebo zrušení. Pomocí webové služby Azure Machine Learning, prediktivní modely lze bez problémů a snadno integrovat do airlines' existující operační platformy. 

#### <a name="use-case-2-aircraft-component-failure"></a>Použití případ 2: Selhání součásti letadla
##### <a name="business-problem-and-data-sources"></a>*Obchodní problém a zdroje dat.*
Letecké motory jsou velmi citlivé a nákladné části zařízení a modul část náhrady patří mezi nejběžnější úlohy údržby v odvětví letecká společnost. Řešení údržby pro airlines vyžadují pečlivě správu uložených dostupnost součásti, doručení a plánování. Schopnost shromažďovat, že intelligence na spolehlivost součást vede k podstatné snížení na investice náklady. Hlavní datový zdroj pro tento případ použití je telemetrická data z několika senzory v letadla informacemi na podmínku letadla shromažďují. Záznamy údržby byly také umožňuje identifikovat, kdy došlo k selhání komponent a bylo provedeno nahrazení.

##### <a name="business-value-of-the-predictive-model"></a>Obchodní hodnotu prediktivního modelu
Klasifikace více třídy modelu bylo vytvořeno který předpovídá pravděpodobnost selhalo, protože některé součásti během příští měsíc. Díky využití architektury těchto řešení, airlines může snížit náklady na opravu součásti, zvýšit dostupnost součásti uložené, snížit inventáře úrovně související prostředky a zlepšit plánování údržby.

### <a name="utilities"></a>Veřejné služby
#### <a name="use-case-1-atm-cash-dispense-failure"></a>Případ použití 1: Selhání obejít bez peněžních ATM
##### <a name="business-problem-and-data-sources"></a>*Obchodní problém a zdroje dat.*
Vedení v asset náročné odvětví často stavu, zda je primární provozní riziko a jejich podniky neočekávané selhání jejich prostředků. Jako příklad selhání stroje například peněžními automaty v bankovnictví odvětví velmi problém je běžný, ke kterému dochází často. Těchto typů problémů vytvořit řešení prediktivní údržby velmi žádoucí pro operátory takové stroje. V tomto případě použití je problém předpovědi vypočítat pravděpodobnost, že získá ATM peněžních odvolání transakce přerušena z důvodu selhání ve enumenátor peněžních například uvíznutí papíru nebo selhání část. Zdroje hlavních dat pro tento případ jsou odečty snímačů, která shromažďují měření při peněžních poznámky jsou právě distribuován a také záznamy údržby shromážděných v čase. Data snímače zahrnuty odečty snímačů na každou transakci byla dokončena a také odečty snímačů za každou Poznámka distribuován. Měření odečty poskytuje senzor například mezery mezi poznámky, tloušťka, mějte na paměti příchodem vzdálenost atd. Data údržby zahrnuty kódy chyb a opravit informace. Ty se používají k identifikaci případů selhání.

##### <a name="business-value-of-the-predictive-model"></a>*Obchodní hodnotu prediktivního modelu*
Dva prediktivní modely byly vytvořené k předvídání selhání transakcí stažení peněžních a chyby v jednotlivých poznámek distribuován během transakce. Tím, že je možné předem předvídat selhání transakce, peněžními automaty zpracováním proaktivně k zabránění výskytu chyby. Navíc se poznámka předpovědi selhání, pokud transakce je pravděpodobně selhat, než je dokončena z důvodu Poznámka obejít bez selhání, je nejlepší a zastavit proces a varovat před odběratele pro neúplné transakce nikoli čekajících na službu údržby doručení Po této chybě dojde, což může vést k větší nespokojenosti zákazníka.

#### <a name="use-case-2-wind-turbine-failures"></a>Použití případ 2: Selhání turbína větru
##### <a name="business-problem-and-data-sources"></a>*Obchodní problém a zdroje dat.*
S raise prostředí sledování větru turbín se staly jedním z hlavních zdrojů energie generace a jejich obvykle náklady milionů dolarů. Jeden z klíčových součástí větru turbín je generátor motoru, který je vybavený s mnoha snímače, které pomáhá monitorovat turbína podmínky a stav. Odečty snímačů obsahovat například střední čas nutný k selhání součástí turbína větru cenné informace, které lze použít k vytvoření prediktivního modelu k předvídání kritické klíčových ukazatelů výkonu (KPI). Data pro tento případ použití pocházejí z více turbín větru, které se nacházejí ve třech umístěních různé farmy. Měření z blízko sto senzorů z každé turbína nebyly zaznamenány každých 10 sekund jeden rok. Tyto odečty zahrnují měření například teploty, generátor rychlost, turbína energie a generátor zrušení.

##### <a name="business-value-of-the-predictive-model"></a>*Obchodní hodnotu prediktivního modelu*
Prediktivní modely měla vytvořená tak, aby odhad zbývající dobu životnosti generátory a senzory teploty. Odhadnutím toho, jaká je pravděpodobnost selhání, se technici údržby zaměřit na podezřelé turbín, které by mohly brzy nepodaří doplňují režimů založené na čase údržby.
Kromě toho prediktivní modely přepněte náhled na úroveň příspěvku na různé faktory, které je pravděpodobnost selhání, která pomáhá obchodní získat lepší přehled o hlavní příčinu problémů.

#### <a name="use-case-3-circuit-breaker-failures"></a>Případ použití 3: selhání jistič
##### <a name="business-problem-and-data-sources"></a>*Obchodní problém a zdroje dat.*
Elektrické energie a plynu operace, které zahrnují výrobou, distribucí a prodej elektrické energie vyžadují významné množství údržby zajistit power řádky provozní za všech okolností zaručit doručení energie pro domácnosti. Selhání těchto operací je velmi důležité, protože téměř každé entity se provádí power problémy v oblasti, které k nim dojde. Moduly okruh dělení jsou důležité pro takové operace, protože se jedná o zařízení, která vyjmout elektrický aktuální v případě problémů a krátké okruhů, abyste zabránili škody power řádcích. Pro tento případ použití obchodního problému je k předvídání jistič selhání daného údržby protokoly, historie příkazů a technických specifikací.

Tři hlavní datové zdroje pro tento případ jsou protokoly údržby, které zahrnují preventivní a systematické opravné akce, provozních dat, které obsahuje příkazy pro automatickou a ruční odeslání okruh funkce, jako rozdělování pro akce Otevřít a zavřít a technical Specifikace data o vlastnostech každé jistič například roku vytvořit, umístění, modelu, atd.

##### <a name="business-value-of-the-predictive-model"></a>*Obchodní hodnotu prediktivního modelu*
Řešení prediktivní údržby pomáhají omezit náklady opravit a zvýšit životní cyklus vybavení, jako jsou moduly okruh dělení. Tyto modely také pomáhá zlepšit kvalitu power sítě, protože modely poskytování upozornění dopředu, která snižují neočekávané selhání, které vedly k méně narušení služby.

#### <a name="use-case-4-elevator-door-failures"></a>Případ použití 4: Hodnocení dveře selhání
##### <a name="business-problem-and-data-sources"></a>*Obchodní problém a zdroje dat.*
Většina velkým organizacím hodnocení obvykle mají miliony výtahy systémem po celém světě. K získání konkurenční výhody, budou se zaměřit na spolehlivost, což je nejdůležitějším zákazníkům. Kreslení na potenciálně Internet věcí, připojením jejich výtahy do cloudu a shromažďování dat ze senzorů hodnocení a systémů, je to možné transformovat data do cenné business intelligence, což významně zvyšuje operations prostřednictvím nabídky Prediktivní a preemptivní údržby, není něco, které jsou k dispozici konkurenty ještě. Požadavek na tomto případě je poskytnout znalostní báze knowledge base prediktivní aplikace, který bude předpovídat potenciálně způsobuje selhání dveře. Požadovaná data pro tuto implementaci se skládá ze tří částí, které jsou statické funkce hodnocení (například identifikátory smlouvy frekvence údržby, typ sestavení, atd.), informace o využití (například počet cyklů dveře, průměrná dveře zavřít čas atd.) a selhání historie (tj. záznamů historických selhání a jejich příčin).

Více třídami logistic regresní model byl vytvořený pomocí Azure Machine Learning k vyřešení problému předpovědi, s použitím integrované statické funkce a data o využití jako funkce a příčiny záznamů historických selhání jako třída popisky. Tento model prediktivní je spotřebovávána aplikace na mobilní zařízení, které používá pole technici k vám pomůže zlepšit efektivitu práce. Když se připravuje technik a zadává dostane v lokalitě k opravě hodnocení, si mohou odkazovat na tuto aplikaci doporučené příčiny a doporučené kurzy akce údržby opravit tak rychlý jako možné hodnocení dveře.

### <a name="transportation-and-logistics"></a>Transport a logistiky
#### <a name="use-case-1-brake-disc-failures"></a>Případ použití 1: Selhání disku brzdy
##### <a name="business-problem-and-data-sources"></a>*Obchodní problém a zdroje dat.*
Zásady údržby typické pro vozidel obsahují opravné a preventivní údržby. Opravné údržby znamená, že je nástroj opravit po došlo k selhání, což může způsobit vážné komplikace ovladačem v důsledku neočekávané selhání a na návštěvy mechanic ke znehodnocení části čas. Většina vozidel také podléhají preventivní údržbě zásad, které vyžaduje provedení některé kontroly podle plánu, který nevyžaduje v úvahu skutečný stav subsystémy Auto. Žádný z těchto postupů se úspěšná ve plně odstraňuje problémy. Případ použití konkrétní tady je brzdy disk selhání předpovědi podle údaje shromážděné prostřednictvím nainstalovaných v systému můžete zadat Auto, která uchovává informace o historických řízení vzory a jiných podmínek, které je vystaven Auto. Nejdůležitější zdroj dat pro tento případ je senzor data, která měřit, například zrychlení, brzdění vzory, řídí vzdálenosti, rychlosti atd. Tyto informace vázány jiné statické informace, jako je funkce car nápovědy sestavení dobrou sada prognostické, které mohou být používány prediktivního modelu. Další sadu základní informace, které je selhání data, která je odvodit z databáze pořadí část (umožňuje zachovat data k výměně za chodu část objednávek a počty jako aut jsou probíhá údržba v dealerům).

##### <a name="business-value-of-the-predictive-model"></a>*Obchodní hodnotu prediktivního modelu*
Obchodní hodnotu prediktivní přístupu je podstatné. Prediktivní údržby systému můžete naplánovat návštěvy obchodník podle prediktivního modelu. Model může být založen na senzorických informace, které je představující aktuální podmínku Auto a podporovat jeho historie. Tuto metodu můžete minimalizovat riziko neočekávaných chyb, které může také dojít před další periodické údržby.
Může také snížit množství nepotřebné preventivní údržby. Ovladač můžete proaktivně informováni, že změnu částí může být nutné za pár týdnů a zadejte obchodník s touto informací. Obchodník může pak připraví balíček jednotlivých údržby ovladače předem.

#### <a name="use-case-2-subway-train-door-failures"></a>Případ použití 2: Podzemní dráha train dveře selhání
##### <a name="business-problem-and-data-sources"></a>*Obchodní problém a zdroje dat.*
Jedním z hlavních důvodů zpoždění a problémy na operace podzemní dráha je selhání dveře train automobilů. Predikci Pokud automobilu train může mít selhání dveře nebo schopnost prognózy počet dnů do další door nezdaří, je velmi důležité předvídání. Poskytuje možnost optimalizovat train dveře údržby a snížit vlaku výpadkům.

#### <a name="data-sources"></a>Zdroje dat
Jsou tři zdroje dat v tomto případě použijte 

* **cvičení data události**, který je na staré záznamy train událostí 
* **data údržby** například údržby typy, typy pořadí pracovních a kódy s prioritou  
* **zaznamenává chyby**.

##### <a name="business-value-of-the-predictive-model"></a>*Obchodní hodnotu prediktivního modelu*
Dva modely byly vytvořené k předvídání další pravděpodobnost selhání den pomocí binární klasifikace a počet dnů do selhání pomocí regrese. Podobně jako v předchozích případech, vytvořit modely strávíte příležitostí ke zlepšení kvality služeb a vzájemně doplňuje režimy pravidelné údržby zvýšit spokojenost zákazníků.

## <a name="data-preparation"></a>Příprava dat
### <a name="data-sources"></a>Zdroje dat
Společné prvky dat pro prediktivní údržby problémy lze shrnout takto:

* Selhání historie: historie selhání počítače nebo součást v rámci počítače.
* Historie údržby: Oprava historie počítače, např. kódy chyb, předchozí aktivity údržby nebo součást nahrazení.
* Počítače podmínky a využití: provozních podmínek počítače např dat shromážděných ze senzorů.
* Počítač funkce: funkce počítače, například modul velikost, značka a model, umístění.
* Operátor funkce: funkce operátoru, například pohlaví, zkušenosti.

Je možné, obvykle případ selhání historie je součástí údržby historie jako ve formě speciální chybové kódy nebo pořadí data pro náhradní díly. V takových případech můžete z dat údržby extrahovat selhání. Kromě toho mohou mít jiné organizační domény různých dalších zdrojů dat, které ovlivňují selhání vzorců, které zde nejsou uvedeny úplné. Tyto mělo by být určené v součinnosti s odborníky domény při vytváření prediktivních modelů.

Některé příklady výše datové prvky z případy použití jsou:

Selhání historie: letu zpoždění data, letadla součást selhání data a typy, selhání transakce za stažení peněžních ATM, selhání dveře train nebo hodnocení, brzdy disku nahrazení pořadí data, větru turbína selhání data a jistič příkaz selhání.

Historie údržby: protokoly chyb letu, chyba ATM transakcích cvičení údržby záznamů, včetně typu údržby, krátký popis atd. a jistič údržby záznamy.

Počítače podmínky a využití: letu tras a časy, data shromážděná z letecké motory odečty snímačů z transakcí ATM cvičení data událostí, odečty snímačů z větru turbín, silech a připojené aut.

Počítač funkce: jistič technických specifikací například napětí úrovně, informace o zeměpisné poloze nebo car funkce jako je například Ujistěte se, modelu, modul velikost, můžete zadat typy zařízení produkční atd.

Zadaný výše uvedených zdrojů dat, jsou dva hlavní datové typy, které v doméně prediktivní údržby se sledují dočasná data a statických dat.
Selhání historie, počítač podmínky, opravte historie, téměř vždy historie využití přijde s časovými razítky-určující dobu každá položka dat kolekce. Funkce počítačů a operátor funkce jsou obecně statické vzhledem k tomu obvykle popisují technických specifikací počítače nebo operátor na vlastnosti. Je možné, aby v průběhu času mění tyto funkce a v takovém případě by měl být považován za časovým razítkem zdroje dat.

### <a name="merging-data-sources"></a>Slučování zdroje dat
Před získáním do libovolného typu funkce inženýrství nebo označování procesu, je potřeba nejdřív připravit naše data ve formuláři potřebné pro vytvoření funkce z. Konečným cílem je generovat záznam pro každou časovou jednotku pro každý prostředek s popisky a funkce být uloženy v strojového učení algoritmu. Chcete-li připravit tento vyčistit závěrečné sady dat, třeba vzít některé kroky předběžného zpracování. Prvním krokem je rozdělit trvání shromažďování dat do jednotky doby, kdy každý záznam patří do časovou jednotku pro určitý prostředek. Shromažďování dat je možné také rozdělit do jiné jednotky jako je například akce, ale pro jednoduchost používáme jednotky doby pro zbytek vysvětlení.

Měrné jednotky pro dobu může být v sekund, minut, hodin, dnů, měsíce, cykly, miles nebo transakcí v závislosti na efektivitu Příprava dat a změny zjištěnými v podmínky prostředku z časovou jednotku dalších nebo jinými faktory konkrétní k doméně. Jinými slovy časovou jednotku nemá být, že stejně jako hodnota frekvence shromažďování dat jako v mnoha případech data nemusí zobrazit žádný rozdíl z jedné jednotky na druhý. Například pokud teploty hodnoty byly shromažďovaných každých 10 sekund, výběr časovou jednotku 10 sekund pro celou analýzu zvýšení kapacity počet příklady bez zadání jakýchkoli dalších informací. Lepší strategie může být použití průměr přes hodinu jako příklad.

Příklad schémata obecných datových zdrojů dat. popsané v předchozí části jsou:

Zaznamenává údržby: Toto jsou záznamy akce údržby provést. Data nezpracovaná údržby obvykle dodává s ID prostředku a časové razítko s informacemi o jaké aktivity údržby prováděly v daném čase. V případě takové nezpracovaná data aktivity údržby nutné přeložit do kategorií sloupců s každou kategorii odpovídající typ akce údržby. Schéma základní data pro záznamy údržby bude zahrnovat sloupce asset ID, čas a údržby akce.

Selhání záznamů: Toto jsou záznamy, které patří k cíli předpovědi, který nazýváme selhání nebo důvod selhání. To mohou být specifické chybové kódy nebo události chyb definované konkrétní obchodní podmínky. V některých případech data obsahují více kódy chyb některé z nich odpovídají selhání, které vás zajímají. Ne všechny chyby jsou cílem předpovědi, takže další chyby jsou obvykle používány k vytváření funkcí, které mohou souviset s chybami. Schéma základní data pro záznamy selhání by mělo zahrnovat asset ID, čas a selhání nebo sloupce Důvod selhání důvod je k dispozici.

Počítač podmínky: Jedná se o ideálně sledování v reálném čase data o provozních podmínek data. Například selhání dveře otevírání dveří a uzavírací časy jsou dobrou indikátory o aktuálním stavu dveří. Schéma základní data pro počítače podmínek bude zahrnovat asset ID, čas a podmínky hodnoty sloupců.

Data počítače a operátor: počítač a operátor data mohou být sloučeny do jedno schéma pro identifikaci, který prostředek byl provozují operátoru společně s vlastností prostředku a operátor. Například automobilu obvykle vlastníkem je ovladač s atributy, jako je stáří, řídí prostředí atd. Pokud se tato data se změní v čase, tato data musí rovněž zahrnovat sloupec čas a považovat za čas různých dat pro funkci generování. Schéma základní data pro počítače podmínky by zahrnují ID prostředku, funkce asset, operátor ID a operátor funkce.

Poslední tabulku před označování a funkce generování může být generována vlevo propojení počítače podmínky tabulky se záznamy selhání na ID prostředku a čas. Tato tabulka pak lze propojit se záznamy údržby na ID prostředku a čas polí a nakonec se počítač a operátor funkce na ID prostředku. První levé spojení opustí hodnoty null pro selhání sloupce, když je počítač v normálním provozu, tyto můžete nebude splněn hodnotou indikátor pro běžné operace. V tomto sloupci selhání se používá k vytvoření popisky pro prediktivní model.

### <a name="feature-engineering"></a>Návrh funkcí
Prvním krokem při modelování je funkce inženýrství. Rada funkce generace je koncepčně popsat a abstraktní podmínka stavu počítače a v daném okamžiku pomocí historická data, která nebyla shromážděna až který bodu v čase. V další části poskytujeme přehled typu technik, které lze použít pro prediktivní údržby a jak se provádí každý technika štítky. Přesný postup, který se má použít, závisí na data a obchodní problém. Však funkce engineering metod popsaných níže slouží jako základní pro vytváření funkcí. Níže probereme prodleva funkce, které musí zkonstruovat z datových zdrojů, které obsahují časová razítka a také statické funkce, které jsou vytvořené z statických dat zdroje a příklady z případy použití.

#### <a name="lag-features"></a>Funkce Lag funkce
Jak už bylo zmíněno dříve, v prediktivní údržby, historická data obvykle dodává s čas kolekce pro jednotlivá data označující časová razítka. Vytvoření funkce z dat, která se dodává s označen časovým razítkem data mnoha způsoby. V této části probereme některé z těchto metod pro prediktivní údržby. Ale jsme nejsou omezeny tyto metody samostatně. Vzhledem k tomu, že funkce inženýrství se považuje za jeden nejvíce tvůrčí oblasti prediktivního modelování, může být mnoho dalších způsobů, jak vytvořit funkce. Zde jsou některé obecné postupy.

##### <a name="rolling-aggregates"></a>*Vrácení agregace*
Pro každý záznam prostředek jsme vyberte okno postupného velikosti "W", což je počet jednotek dobu, po kterou bychom rádi výpočetní historických agregace pro. Potom výpočetní vrácení agregační funkcí s použitím W období před datem záznamů. Několik příkladů vrácení agregace můžete vrácení, počty, znamená, standardních odchylek, extrémních podle standardních odchylek, měří CUSUMŮV, minimální a maximální hodnoty pro okno. Jiné zajímavé technika je zaznamenat změny trendu, špičky a úrovně změny pomocí algoritmů, které zjišťovat anomálie v dat pomocí algoritmů detekce anomálií.

Pro demonstrační, viz obrázek 1, kde jsme představují hodnoty čidel zaznamenány pro určitý prostředek pro každou jednotku času blue řádků a označit postupného průměrná funkci výpočtu pro W = 3 pro záznamy na t<sub>1</sub> a t<sub>2</sub> které jsou označeny oranžová a zelená seskupení v uvedeném pořadí.

![Obrázek 1. Vrácení agregační funkce](./media/cortana-analytics-playbook-predictive-maintenance/rolling-aggregate-features.png)

Obrázek 1. Vrácení agregační funkce

Jako příklady pro letadla selhání součásti, hodnoty čidel z poslední týden, poslední tři dny a poslední den měla použít k vytvoření postupného znamená, směrodatná odchylka a funkce sum. Podobně pro znamená ATM selhání, nezpracovaná senzor hodnoty a vrací medián, rozsah, standardních odchylek počet extrémních nad rámec tři standardních odchylek, horní a dolní CUMSUM funkce byly použity.

Pro předpověď zpoždění letu počty chyby, které kódy od minulého týdne jste použili k vytvoření funkce. Počet událostí na posledního dne, počet událostí za předchozí dva týdny a odchylku počtu událostí za posledních 15 dnů byly train dveře selhání, použít k vytvoření funkce lag. Stejné počítání byl použit pro události související s údržbou.

Kromě toho výběrem W je moc velké (např.) letopočty), je možné podívejte se na celou historii prostředek, jako je například počítání všechny záznamy údržby selhání atd. až se čas záznamu. Tato metoda se používá pro počítání jistič selhání poslední tři roky. Pro selhání train také všechny události údržby měla počítají vytvořit funkci pro zachycení dlouhodobé údržby účinky.

##### <a name="tumbling-aggregates"></a>*Přeskakující agregace*
Pro každý s popiskem záznam majetku vybereme okno velikosti "W -<sub>tisíc</sub>" kde tisíc je číslo nebo windows velikost "W", kterou chcete vytvořit funkce lag pro. "k" mohou být zachyceny jako velký počet pro zachycení dlouhodobé vzory snížení nebo malé množství k zachycení krátkodobé účinky. Používáme k přeskakující windows W -<sub>tisíc</sub> , W -<sub>(k-1)</sub>,..., W -<sub>2</sub> , W -<sub>1</sub> k vytvoření agregační funkce pro období před datem záznam a čas (viz obrázek 2). Tyto jsou také vrácení windows na úrovni záznam pro časovou jednotku, která není zaznamenaná na obrázku 2, ale na nápad je stejný jako v obrázek 1, pokud t<sub>2</sub> slouží také k předvedení postupného účinek.

![Obrázek 2. Přeskakujícího agregační funkce](./media/cortana-analytics-playbook-predictive-maintenance/tumbling-aggregate-features.png)

Obrázek 2. Přeskakujícího agregační funkce

Jako příklad větru turbín, W = 1 a tisíc = 3 měsíce jste použili k vytvoření funkce lag pro každou z posledních 3 měsíců pomocí horní a dolní odlehlé hodnoty.

#### <a name="static-features"></a>Statické funkce
Jedná se o technických specifikací vybavení, jako je například datum výroby, číslo modelu, umístění atd. Funkce opoždění jsou většinou číselné ve své podstatě, bude statické funkcí obvykle kategorií proměnných v modelech. Jako příklad jistič vlastnosti, například napětí, aktuální a specifikace power společně s typy transformer používaly zdrojů energie atd. Brzdy selhání disku kola typ můžete zadat, jak je, pokud jsou slitiny nebo oceli používaly jako statické funkce.

Během generování funkce je třeba provést některé důležité kroky jako je například zpracování chybějící hodnoty a normalizace. Chybí hodnota imputace a také normalizaci data, která není tady popisovaných mnoha způsoby. Je však vhodné zkuste různé metody, které chcete zobrazit, pokud je možné zvýšit výkonnost předpovědi.

Poslední funkce tabulky po funkce technici kroky popsané v předchozí části by měla vypadat přibližně následující schéma příklad dat po časovou jednotku za den:

| ID assetu | Čas | Funkce sloupců | Štítek |
| --- | --- | --- | --- |
| 1 |1 den | | |
| 1 |Den 2 | | |
| ... |... | | |
| 2 |1 den | | |
| 2 |Den 2 | | |
| ... |... | | |

## <a name="modeling-techniques"></a>Techniky modelování
Prediktivní údržby je velmi bohaté doména často zaměstnávající obchodní otázky, které může použijí z mnoha různých úhlů prediktivního modelování perspektivy. V následujících částech poskytujeme hlavní techniky, které se používají k modelu různých obchodních otázek, které můžete zodpovězeny s řešení prediktivní údržby. I když existují podobnosti, každý model má svou vlastní způsob vytváření popisky, které jsou podrobně popsány. Jako prostředek doprovodné najdete šablona prediktivní údržby, který je součástí ukázkových experimentů uvedené v Azure Machine Learning. Odkazy na online materiálu pro tuto šablonu jsou uvedeny v části prostředky. Uvidíte, jak některé funkce engineering techniky popsané výše a modelování techniku, která je popsána v následujících částech jsou použity k předpovědi selhání motoru letadla pomocí Azure Machine Learning.

### <a name="binary-classification-for-predictive-maintenance"></a>Binární klasifikace pro prediktivní údržby
Binární klasifikace pro prediktivní údržby se používá k předvídání pravděpodobnost, že zařízení selže a v budoucích časovém intervalu. Časové období je dáno a na základě obchodní pravidla a data po ruce. Některé běžné časová období jsou minimální doba potřebná k nákupu náhradní díly nahradit pravděpodobně součásti poškození nebo čas potřebný k nasazení údržby prostředků k provedení údržby rutiny opravit problém, který může dojít v daném časovém období. Říkáme tohoto období budoucí horizon "X".

Chcete-li použít binární klasifikace, musíme určit dva typy příklady, které říkáme kladné a záporné. Každý příkladem je záznam, který patří do časovou jednotku pro určitý prostředek koncepčně popisující a poskytuje abstrakci jeho provozních podmínek až tuto časovou jednotku prostřednictvím funkce engineering pomocí historických a jiných zdrojů dat, které jsou popsané výše. V kontextu binární klasifikace pro prediktivní údržby, kladné typ označuje selhání (popisek 1) a záporná typ označuje normální provozní podmínky (Popisek = 0) kde popisky jsou typu kategorií. Cílem je nalezení model, který identifikuje každý nový příklad jako pravděpodobně nezdaří a fungovat normálně během následujících X jednotkami času.

#### <a name="label-construction"></a>Popisek konstrukce
Chcete-li vytvořit prediktivní model odpověď na otázku "co je pravděpodobnost, že asset selže v dalším X jednotkami času?", označování se provádí pořízení X záznamů před selháním prostředek a označování je jako "o na selhání" (popisek = 1) při označování všechny  Další záznamy "normální" (Popisek = 0). Tato metoda popisky jsou kategorií proměnné (viz obrázek 3).

![Obrázek 3. Označování pro binární klasifikaci](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-binary-classification.png)

Obrázek 3. Označování pro binární klasifikaci

Zpoždění letů a zrušení X vydán jako jeden den k předvídání zpoždění v dalších 24 hodin. Všechny lety, které jsou v rámci 24 hodin, než selhání byly označeny jako hodnotami 1. ATM peněžních obejít bez chyb, dva modely binární klasifikace byly vytvořené k předvídání pravděpodobnost selhání transakce v příštích 10 minutách a také k předvídání pravděpodobnost selhání v dalších 100 poznámky distribuován. Všechny transakce, které došlo během posledních 10 minut selhání jsou označeny jako 1 pro první model. A všechny poznámky distribuován v rámci poslední 100 poznámky k selhání byly označený jako 1 pro druhý modelu. Pro jistič selhání je předpovědi pravděpodobnost, že další jistič příkaz se nezdaří, ve kterém je případ X zvolen jako jeden příkaz budoucí úlohy. Pro train dveře selhání binární klasifikace model byl vytvořený k předvídání selhání během následujících 7 dní. Selhání turbína větru jste vybrali X jako 3 měsíce.

Větru turbína a cvičení dveře případech slouží také k analýze regrese k předvídání zbývající dobu životnosti pomocí stejných dat, ale s využitím různých označování strategie, který je vysvětlen v další části.

### <a name="regression-for-predictive-maintenance"></a>Regrese pro prediktivní údržby
Modely Regrese v prediktivní údržby se používají k výpočtu zbývající dobu životnosti (RUL) z prostředek, který je definován jako množství času, který asset je funkční, než dojde k další chybě. Stejné jako binární klasifikace, každý příkladem je záznam, který patří do časovou jednotku "Y" pro určitý prostředek. V souvislosti s regrese se však cílem je najít model, který vypočítá zbývající životnost každý nový příklad jako průběžné číslo, které je doba, zbývající před selháním. Říkáme tohoto období některé více Y. Každý příklad má také zbývající dobu životnosti, což může být vypočítána množství času, například před selháním další zbývající.

#### <a name="label-construction"></a>Popisek konstrukce
Zadaný na otázku "co je zbývající dobu životnosti zařízení?
", popisků pro regresní model konstruovat tak, že každý záznam před selháním označování je pomocí výpočtu, kolik jednotek času zůstat před selháním Další. Tato metoda popisky jsou průběžné proměnné (viz obrázek 4).

![Obrázek 4. Označování pro regresní](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-regression.png)

Obrázek 4. Označování pro regresní

Jiné než binární klasifikace pro regresní, prostředky bez jakýchkoliv potíží v datech nelze použít pro modelování označování se provádí v souvislosti s aplikací bod selhání a jeho výpočtu není možné bez znalosti, jak dlouho zůstal naživu asset před došlo k chybě. Tento problém je nejlépe řešit jiné statistické technika nazývají základními analýzy.
Nebudete budeme zabývat základními analýzy v této scénářem z důvodu možných komplikací, ke kterým může nastat při použití techniku případy použití prediktivní údržby, které se týkají různých čas dat pomocí pravidelných intervalech.

### <a name="multi-class-classification-for-predictive-maintenance"></a>Klasifikace více tříd pro prediktivní údržby
Klasifikace více tříd pro prediktivní údržby můžete použít k předvídání dvě budoucí výsledky. První z nich je prostředek přiřadit jednoho z několika možných období času, který se pro každý prostředek poskytnout časový rozsah a k selhání. Druhá je identifikace pravděpodobnost selhání v budoucí období kvůli jednomu několik příčin. Umožňuje údržby pracovníky, kteří jsou vybaveny tento znalostní báze pro zpracování problémy předem. Jiné technika modelování více tříd se zaměřuje na určení s největší pravděpodobností hlavní příčinu základě selhání. To umožňuje doporučení, která má být poskytnut pro akce nejvyšší údržby, které mají být provedeny, pokud chcete problém selhání.
Tak, že seřazený seznam kořenové způsobí, že a přidružené akce opravy  
Technici může být efektivnější v trvá jejich první akce opravit po selhání.

#### <a name="label-construction"></a>Popisek konstrukce
Zadané dva dotazy, které jsou "co je pravděpodobnost, že prostředek v další jednotky"aZ"času selže tam, kde"a"je počet období" a "co je pravděpodobnost, že asset selže v dalším X jednotkami času z důvodu problému" P<sub>i</sub>" kde "i" je počet možných příčinách označování se provádí následujícím způsobem pro tyto techniky.

Pro první otázku označování se provádí tak, že záznamy aZ před selháním prostředek označování je pomocí sad času (3Z, 2Z, Z) jako jejich popisky při označování všechny záznamy jako "normální" (Popisek = 0). Tato metoda je popisek kategorií proměnná (viz obrázek 5).

![Obrázek 5. Označování pro více třídami klasifikaci pro předpověď časové selhání](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-failure-time-prediction.png)

Obrázek 5. Označování pro více třídami klasifikaci pro předpověď časové selhání

Pro druhou otázku označování se provádí pořízení X záznamů před selháním prostředek a označování je jako "o nezdaří z důvodu problému P<sub>i</sub>" (popisek = P<sub>i</sub>) při označování všechny záznamy jako "normální" (Popisek = 0). Tato metoda popisky jsou kategorií proměnné (viz obrázek 6).

![Obrázek 6. Označování pro více třídami klasifikaci pro kořenové příčina předpovědi](./media/cortana-analytics-playbook-predictive-maintenance/labelling-for-multiclass-classification-for-root-cause-prediction.png)

Obrázek 6. Označování pro více třídami klasifikaci pro kořenové příčina předpovědi

Model přiřadí pravděpodobnost selhání kvůli každý P<sub>i</sub> i pravděpodobnost bez chyby. Tyto pravděpodobnostech lze provést řazení podle rozsahem umožňující předpovědi problémů, které jsou v budoucnu dochází nejčastěji. Případ použití selhání součásti letadla se strukturovaná jako více třídami klasifikace problému. To umožňuje předpovědi pravděpodobností selhalo, protože dvě různé zatížení ventil součásti vyskytující se v příští měsíc.

Pro akce údržby doporučujeme po selhání, označování nevyžaduje budoucí horizon má být vybráno. Je to proto, že model není v budoucnu predikci selhání, ale jeho je právě předpovídá nejpravděpodobnější příčiny po selhání bylo provedeno. Hodnocení dveře selhání spadají do třetí případě, kdy cílem předpovídat příčinu selhání zadané historická data týkající se provozních podmínek. Tento model je poté použít k předvídání, že pravděpodobně hlavní příčiny po došlo k selhání. Klíčovou výhodou tohoto modelu je, že pomáhá používáním technici snadno diagnostikovat a opravit problémy, které byste jinak potřebovali za roky prostředí.

## <a name="training-validation-and-testing-methods-in-predictive-maintenance"></a>Školení, ověřování a testování metody v prediktivní údržby
V prediktivní údržby, podobně jako jiné řešení místo označen časovým razítkem data, která obsahuje typické trénování a testování běžné musí vzít v úvahu generalize různých aspektů čas pro lepší neviditelný budoucí data.

### <a name="cross-validation"></a>Křížové ověření
Mnoho algoritmy strojového učení závisí na několika hyperparameters, který může významně změní výkonu modelu. Optimální hodnoty těchto hyperparameters nejsou automaticky vypočítávají při tréninku modelu, ale musí být určena vědecký pracovník data. Nalezení správné hodnoty hyperparameters několika způsoby. Nejběžnější ten je "tisíc násobek křížové ověření" což rozdělí příklady náhodně na složení "k". Pro každou sadu hodnot hyperparameters je algoritmus učení časy spuštění kB. Při každé iteraci v příkladech v aktuální násobek jsou použity jako sada ověření, zbytek příklady jsou použity jako sada školení. Algoritmus vlaky přes příklady školení a metriky výkonu se vypočítávají přes příklady ověření. Na konci této smyčky pro každou sadu hodnot hyperparameter jsme výpočetní průměr hodnot metriky výkonu tisíc a zvolte hyperparameter hodnoty, které mají nejlepšího výkonu dosáhnete průměrná.

Jak je uvedeno nahoře, v prediktivní údržby problémy, data se zaznamenává jako časové řady událostí, které pocházejí z několika zdrojů dat. Tyto záznamy lze provést řazení podle času zobrazení popisků záznam nebo příklad. Proto pokud jsme rozdělit datovou sadu náhodně na školení a ověření, některé příklady školení jsou později v čase než některé příklady ověření. Výsledkem je odhad budoucích výkonu hyperparameter hodnot na základě dat, které byly přijaty předtím, než byla cvičení modelu. Tyto odhady může být příliš optimistickou metodu, zejména v případě, že časové řady nejsou stojící a jejich chování časem změnit. V důsledku toho může být vybraný hyperparameter hodnoty optimální.

Lepší způsob hledání správné hodnoty hyperparameters je rozdělit příklady na školení a ověření způsobem závislá na čase, nastavte tak, že jsou všechny příklady ověření v čase pozdější, než všechny příklady školení. Potom pro každou sadu hodnot hyperparameters učení algoritmu přes trénovací sada, měřit výkon modelu přes stejnou sadu ověření a zvolte hyperparameter hodnoty, které se zobrazí nejlepší výkon. Pokud data časové řady není stojící a vyvíjí v průběhu času, hodnoty hyperparameter zvolí train a ověření rozdělení vést k lepší budoucí "modelu výkon než s hodnotami náhodně vybere podle křížové ověření.

Poslední model je generován cvičení algoritmu učení přes celou dat pomocí nejlepší hyperparameter hodnoty, které se nacházejí pomocí školení a ověření rozdělené nebo křížové ověření.

### <a name="testing-for-model-performance"></a>Testování výkonu modelu
Po sestavení modelu potřebujeme odhad jeho budoucí výkon na nová data. Nejjednodušší odhad může být výkon modelu přes Cvičná data. Ale tento odhad příliš optimistickou metodu, protože model přesně podle data, která se používá k zjištění přibližné hodnoty výkonu. Lepší odhad může být metriky výkonu hodnot hyperparameter vypočítán na sadu ověření nebo metriku výkonu průměrná vypočítaný z křížové ověření. Ze stejných důvodů jako výše uvedená, jsou tyto odhady příliš optimistické ale. Potřebujeme realističtější přístupy pro měření výkonu modelu.

Jedním ze způsobů je náhodně rozdělení dat do školení, ověřování a testování sad. Nastaví školení a ověřování se používají a vyberte hodnoty hyperparameters trénování modelu s nimi. Výkon modelu se měří v testovací sady.

Dalším způsobem, což je relevantní pro prediktivní údržby, je rozdělit příklady na školení, ověření a testovacích sad způsobem závislá na čase, tak, aby všechny testovací příklady jsou v čase pozdější, než všechny příklady školení a ověření. Po rozdělení se provádějí měření generování a výkonu v modelu stejné, jak je popsáno výše.

Po místě a usnadňuje předpovědi časové řady obou přístupů generovat podobné Odhady budoucí výkonu. Ale když časové řady-stojícího vozidla nebo snadno předpovědět, druhý postup bude generovat realističtější odhad budoucích výkonu než první z nich.

### <a name="time-dependent-split"></a>Rozdělení závislá na čase
Jako osvědčený postup v této části jsme trvat bližší pohled na tom, jak implementovat rozdělení závislá na čase. Jsme popisují obousměrný rozdělení závislá na čase mezi výukových a testovacích sad, ale přesně stejné logiky, která má být použita pro závislá na čase rozdělení pro školení a ověření sady.

Předpokládejme, že máme označen časovým razítkem události například měření z různých snímače datového proudu. Funkce školení a příklady test, jakož i jejich popisky jsou definované ve stanovených časových rámců, které obsahují více událostí.
Například pro binární klasifikaci, jak je popsáno v části funkce techniků a modelování techniky, funkce jsou na základě vytvořit posledních událostí a popisky jsou na základě vytvořit budoucí události v rámci "X" jednotky čas v budoucnosti. Proto označování časový rámec příkladu obsahuje později pak časový rámec jeho funkcí. Pro rozdělení závislá na čase jsme vyberte bod v čase, kdy jsme trénování modelu s ujít hyperparameters až pomocí historických dat, které odkazují. Pokud chcete zabránit úniku budoucí popisky, které jsou nad rámec oříznutím školení do Cvičná data, vybereme možnost nejnovější časové rozmezí pro popisek školení příklady jako X jednotky před datem oříznutím školení. Na obrázku 7 každý plný kroužek představuje řádek v datové sadě poslední funkci, pro který funkce a popisky se vypočítávají podle metody popsané výše. Který zadána obrázek zobrazuje záznamy, které patří do trénování a testování sad při implementaci závislá na čase rozdělení pro X = 2 a W = 3:

![Na obrázku 7. Závislá na čase rozdělení pro binární klasifikaci](./media/cortana-analytics-playbook-predictive-maintenance/time-dependent-split-for-binary-classification.png)

Na obrázku 7. Závislá na čase rozdělení pro binární klasifikaci

Zelená čtverce představují záznamy patřící do časové jednotky, které lze použít pro školení. Jak je popsáno výše, každý příklad školení v tabulce konečné funkce je generován prohlížení po 3 období pro funkci generace a 2 budoucí období pro označování před oříznutím den školení. Jsme příklady nepoužívejte v školení nastavená, pokud libovolná součást 2 budoucí období například je nad rámec oříznutím školení vzhledem k tomu, že předpokládáme, že jsme nemají viditelnost nad rámec oříznutím školení. Z důvodu omezení černé příklady představují záznamy konečné s popiskem datovou sadu, která se nesmí použít v trénovací datové sady. Tyto záznamy se nesmí použít data, která buď protože jsou před oříznutím školení a jejich označování stanovených časových rámců závisí částečně na školení časový rámec, který by neměl být tak, jak bychom rádi zcela oddělit označování časové rámce pro testování trénování a testování, aby se zabránilo úniku informací popisku.

Tento postup umožňuje překrývají údajů použitých pro funkci generování mezi trénování a testování příklady, které se blíží oříznutím školení. V závislosti na dostupnost dat lze provést i závažnější oddělení pomocí není některé z příkladů v testovací sadě, které jsou v rámci jednotky doby W rozhraní školení.

Z našich pracovních jsme zjištěno, že problém úniku více vážně týká regrese modely použité pro predikci zbývající dobu životnosti a pomocí náhodného rozdělení vede k extrémně overfitting. Podobně regrese problémy rozdělení musí být tak, aby záznamy patřící k prostředkům s chybami před školení oříznutí slouží pro sadu školení a prostředky, s chybami po prahová hodnota by měla použít pro testování sady.

Obecné metody, další důležité osvědčených postupů pro rozdělení dat pro trénování a testování je použití rozdělení podle ID prostředku tak, aby žádné prostředky, které byly používány v školení se používají pro testování, protože Rada testování je Ujistěte se, že když nového prostředku je použít t o zpřístupnění předpovědi na, modelu poskytuje realistické výsledky.

### <a name="handling-imbalanced-data"></a>Zpracování imbalanced dat
V klasifikaci problémy Pokud existují další příklady jednu třídu než ostatní, data se říká, že imbalanced. V ideálním případě by rádi bychom mít dostatek zástupců každá třída v Cvičná data, abyste mohli rozlišit mezi různými třídami. Pokud jedna třída je menší než 10 % dat, budeme moct, data se imbalanced a říkáme třídu menšinových underrepresented datové sady. V mnoha případech se nám najít výrazně, kde je jedna třída vážně underrepresented imbalanced datasetu ve srovnání s ostatními například pouze tvořící 0,001 % datových bodů. Třída nevyváženosti k potížím v mnoha oblastech včetně odhalování podvodů, vniknutí sítě a prediktivní údržby kde selhání jsou obvykle výjimečných výskytů v životnost prostředky, které tvoří příklady menšinových tříd.

V případě třída nevyváženosti výkon většinu standardních algoritmů učení ohrožené jako jejich cílem minimalizovat celkové míra chyb. Například pro datovou sadu s příklady 99 % záporné tříd a příklady kladné tříd 1 %, jsme se připojíte 99 % přesnost jednoduše označování všechny instance jako záporné. To však misclassifies všechny kladné příklady v tak algoritmus není užitečné i když metrika přesnost je velmi vysoká. V důsledku toho nejsou dostatečná v případě imbalanced learning konvenční vyhodnocení metriky, například celkový přesnost na míra chyb. Další metriky, například přesnost, odvolání, F1 skóre a náklady na upravenou křivek ROC se používají pro hodnocení v případě imbalanced datové sady, která je popsána v části vyhodnocení metriky.

Existují však některé metody, které pomáhají nápravě třída nevyváženosti problém. Dva hlavní ty, které jsou vzorkování techniky a náklady citlivé učení.

#### <a name="sampling-methods"></a>Metody vzorkování
Použití vzorkování metody v imbalanced učení se skládá z úpravy datové sady některé mechanismy Chcete-li poskytovat datovou sadu s vyrovnáváním. I když existují spoustu různých odběry vzorků, většina z nich splněny následující jsou náhodných oversampling a v části vzorkování.

Jednoduše stanovené, náhodné oversampling je výběr z náhodného vzorku z menšinových třída, která replikuje tyto příklady a jejich přidáním do trénovací datové sady. To zvyšuje počet celkový příklady v třídě menšinových a nakonec vyvážit počet příklady různých tříd. Jeden nebezpečí oversampling je, že více instancí některé příklady může způsobit třídění k příliš konkrétní vedoucí k overfitting.
To by způsobilo školení vysokou přesnost, ale může být velmi nízký výkon na neviditelný testování data. Naopak náhodných pod vzorkování je vyberete z náhodného vzorku většina třídy a odebrání těchto příkladů z trénovací datové sady. Odebrání příklady z třídy většina může způsobit třídění přijít o důležité koncepty týkající se většinou třídy. Hybridní vzorkování, kde je třída menšinových převzorkované a většina třída je v rámci vzorků ve stejnou dobu je přijatelná jiná možnost. Existuje mnoho dalších sofistikovanější techniky vzorkování jsou k dispozici a efektivní vzorkování metody pro třídu nevyváženosti je oblast oblíbených research příjem konstantní pozornost a příspěvky z mnoha kanály. Použití různých technik při rozhodování o co nejúčinnější těm, které jsou obvykle zleva vědecký pracovník data prozkoumat a experimentovat a vysoce závisí na vlastnosti dat. Kromě toho je důležité, abyste měli jistotu, že metody vzorkování platí jenom pro školení nastavit však není test.

#### <a name="cost-sensitive-learning"></a>Náklady citlivé učení
V prediktivní údržby, chyb, které tvoří menšinových – třída jsou důležitější sledovat než normální příklady a proto se zaměřuje se na výkon algoritmus na selhání je obvykle fokus. To se obvykle označuje jako nerovné ztráta nebo asymetrický náklady misclassifying elementy různých tříd, ve kterém může nesprávně predikci kladnou jako záporné náklady více než naopak. Požadované třídění byste měli mít poskytnou přesnost předpovědi vysoké nad třídu menšinových bez vážně kompromisů v přesnost pro většinu třídu.

Existuje několik způsobů, které jde tohoto dosáhnout. Přiřazením vysoké náklady na chybnou menšinových třídy a pokusu minimalizovat celkové náklady, problém různé ztratí se mohou efektivně řešit. Některé algoritmy strojového učení použít tento nápad ze své podstaty například SVMs (Support Vector počítače), kde lze začlenit náklady kladné a záporné příklady během doby školení. Podobně zvýšení skóre metody se používají a obvykle zobrazit dobrý výkon v případě imbalanced data, jako například boosted rozhodovací strom algoritmy.

## <a name="evaluation-metrics"></a>Vyhodnocení metriky
Jak už bylo zmíněno dříve, třída nevyváženosti způsobí, že nízký výkon jako algoritmy mívají klasifikovat většina – příklady tříd lepší v výdajů minoritního třída případů chyba celkový chybnou je mnohem vyšší, pokud třída většina správně označené. To způsobí, že sazby nízkou odvolání a větší problém se změní, pokud je velmi vysoké náklady na falešné výstrahy pro jejich podnikání. Přesnost je nejoblíbenější metrika používá k popisu třídění výkonu. Ale jak jsme vysvětlili výše přesnost je neefektivní a nereflektuje skutečné výkon třídění funkce, jako je velmi citlivé na rozdělování dat. Místo toho další metriky vyhodnocování se používají k vyhodnocení imbalanced learning problémy. V těchto případech by měl být přesnost, odvolání a skóre F1 počátečních metrik prohlédnout si při hodnocení výkonu modelu prediktivní údržby. V prediktivní údržby odvolání sazby označují, kolik selhání v testovací sadě byly identifikovány správně modelem. Vyšší rychlosti pro vyvolání znamená, že je model určen při zachytávání true selhání úspěšný. Metrika přesnost má vztah k počet falešných poplachů kde nižší sazby přesnost odpovídají vyšší falešných poplachů. F1 skóre zvažuje přesnost i pro vyvolání sazby s nejlepší hodnota je 1 a nejhorších se 0.

Kromě toho pro binární klasifikaci, decile tabulky a navýšení grafy jsou velmi informativní při hodnocení výkonu. Budou se zaměříte jenom na kladné – třída (počet selhání) a zadejte složitější přehled o výkonu algoritmus než co je vidět na základě právě pevného operační bodu na křivka ROC (příjemce operační vlastnosti).
Decile tabulky se získají řazení příklady testovací podle jejich předpokládaných pravděpodobnosti selhání počítaný modelem před prahování při rozhodování o poslední popisek. Seřazené ukázky jsou pak seskupené v deciles (tj. 10 % ukázky s největší pravděpodobností a pak 20 %, 30 % a tak dále). Poměr mezi true kladné počet jednotlivých decile a jeho náhodné směrného plánu (tj 0.1, 0.2..) computing připravila jeden odhadnout, jak výkon algoritmu změny v každé decile. Navýšení grafy se používají k vykreslení hodnot decile podle vykreslení decile true kladné míra versus páry náhodných true kladné rychlost pro všechny deciles. První deciles obvykle představují fokus výsledky, protože tady vidíte největší zvýšení. První deciles se také dají považovat za reprezentativní pro "na riziko" Pokud se používá pro prediktivní údržby.

## <a name="sample-solution-architecture"></a>Ukázkové architektury řešení
Při nasazení řešení prediktivní údržby, jsme mají zájem o komplexní řešení, která poskytuje nepřetržitou cyklus přijímání dat, úložiště dat pro trénování modelu, funkce generování, předpovědi a vizualizace výsledků společně s výstrahu generování mechanismus například prostředek řídicí panel monitorování. Chceme datový kanál, který poskytuje budoucí přehledy uživateli průběžné automatizované způsobem. Příklad prediktivní údržby architekturu pro takové IoT datový kanál je znázorněno na obrázku 8 níže. V architektuře se shromažďují v reálném čase telemetrie do centra událostí, která ukládá data streamování. Tato data se požita stream analytics v reálném čase zpracování dat, jako je například funkce generování. Funkce slouží k volání webové služby prediktivní model a výsledky jsou zobrazené na řídicím panelu. Ve stejnou dobu je ingestovaný data také uložena v databázi historických a sloučit s externími zdroji dat, jako například místní data základny vytvořit příklady školení pro modelování.
Stejné datové sklady lze použít pro dávkové vyhodnocování příkladů a ukládání výsledků, které se dá znovu použít k poskytování prediktivní sestavy na řídicím panelu.

![Obrázek 8. Příklad Architektura řešení prediktivní údržby](./media/cortana-analytics-playbook-predictive-maintenance/example-solution-architecture-for-predictive-maintenance.png)

Obrázek 8. Příklad Architektura řešení prediktivní údržby

Další informace o jednotlivých součástí architektury naleznete [Azure](https://azure.microsoft.com/) dokumentaci.

