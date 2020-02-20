---
title: Rychlý start – Prozkoumání nákladů na Azure pomocí analýzy nákladů | Microsoft Docs
description: Tento rychlý start vám pomůže prozkoumat a analyzovat náklady organizace na Azure pomocí analýzy nákladů.
author: bandersmsft
ms.author: banders
ms.date: 02/11/2020
ms.topic: quickstart
ms.service: cost-management-billing
ms.reviewer: micflan
ms.custom: seodec18
ms.openlocfilehash: bc19c6358ff116b3d7299673783e8fbd6e8cd4f5
ms.sourcegitcommit: 333af18fa9e4c2b376fa9aeb8f7941f1b331c11d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2020
ms.locfileid: "77201046"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Rychlý start: Prozkoumání a analýza nákladů pomocí analýzy nákladů

Než budete moci správně řídit a optimalizovat náklady na Azure, musíte porozumět tomu, kde se náklady ve vaší organizaci generují. Je také užitečné vědět, kolik vás stojí služby a podpora prostředí a systémů. Náhled do celého spektra nákladů je důležitý, abyste přesně porozuměli výdajovým schématům organizace. Výdajová schémata můžou sloužit k prosazování mechanismů řízení nákladů, jako jsou rozpočty.

V tomto rychlém startu použijete analýzu nákladů a prozkoumáte a analyzujete náklady organizace. Můžete se podívat na agregované náklady na úrovni organizace, abyste porozuměli tomu, kde se náklady v průběhu času generují, a mohli identifikovat trendy útrat. Můžete si také zobrazit kumulované náklady v průběhu času, abyste mohli odhadnout měsíční, čtvrtletní a dokonce i roční trendy nákladů oproti rozpočtu. Rozpočet vám pomůže dodržet finanční omezení. Slouží také k zobrazení denních nebo měsíčních nákladů, abyste mohli izolovat nepravidelnosti v útratách. Dále si můžete stáhnout data aktuální sestavy, abyste je mohli dále analyzovat nebo použít v externím systému.

V tomto rychlém startu se naučíte:

- Kontrola nákladů v analýze nákladů
- Přizpůsobit zobrazení nákladů
- Stažení dat analýzy nákladů


## <a name="prerequisites"></a>Požadavky

Analýza nákladů podporuje různé typy účtů Azure. Úplný seznam podporovaných typů účtů si můžete prohlédnout v článku [Vysvětlení dat služby Cost Management](understand-cost-mgt-data.md). Pokud chcete zobrazit data o nákladech, potřebujete přinejmenším oprávnění ke čtení k vašemu účtu Azure.

Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](../../cost-management/assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Kontrola nákladů v analýze nákladů

Pokud si chcete zkontrolovat náklady v analýze nákladů, otevřete požadovaný obor na webu Azure Portal a v nabídce vyberte **Analýza nákladů**. Přejděte například na **Předplatná**, vyberte předplatné ze seznamu a pak v nabídce vyberte **Analýza nákladů**. Pomocí možnosti **Obor** můžete v analýze nákladů přepnout na jiný obor. Další informace o oborech najdete v článku o [principech oborů a práci s nimi](understand-work-scopes.md).

Vybraný obor se použije v celé službě Cost Management v zájmu konsolidace dat a k řízení přístupu k informacím o nákladech. Při použití oborů nevybíráte více oborů. Místo toho vyberete rozsáhlejší obor, který zahrnuje ostatní obory, a potom vyfiltrujete potřebné vnořené obory. Tomuto přístupu je důležité rozumět, protože někteří lidé nemusí mít přístup k jednomu nadřazenému oboru, který pokrývá více vnořených oborů.

Počáteční zobrazení analýzy nákladů zahrnuje následující oblasti.

**Zobrazení kumulovaných nákladů:** Představuje předdefinovanou konfiguraci zobrazení analýzy nákladů. Pro každé zobrazení jsou k dispozici nastavení rozsahu dat, úrovně podrobností, seskupení a filtrování. Výchozí zobrazení ukazuje kumulované náklady za aktuální fakturační období, ale můžete přepnout do jiných předdefinovaných zobrazení. Další informace najdete v oddílu [Přizpůsobení zobrazení nákladů](#customize-cost-views).

**Skutečné náklady:** Zobrazuje celkové náklady na využití a nákupy za aktuální měsíc, jak postupně nabíhají a objeví se ve vaší faktuře.

**Prognóza:** Zobrazuje celkové předpokládané náklady na časové období, které si zvolíte. (Funkce Prognóza je ve verzi Preview.)

**Rozpočet:** Zobrazuje plánovaný limit útraty pro vybraný obor (pokud je k dispozici).

**Kumulované podrobnosti:** Zobrazuje celkové agregované denní náklady od začátku fakturačního období. Po [vytvoření rozpočtu](tutorial-acm-create-budgets.md) pro fakturační účet nebo předplatné můžete rychle zobrazit trend útraty oproti rozpočtu. Po najetí myší na datum se zobrazí kumulované náklady pro daný den.

**Kontingenční grafy (prstencové):** Poskytují dynamické přehledy a rozdělení celkových nákladů podle společné sady standardních vlastností. Zobrazují náklady za aktuální měsíc od největších po nejmenší. Kontingenční grafy můžete kdykoli změnit výběrem jiného pivotu. Náklady jsou ve výchozím nastavení kategorizovány podle služby (kategorie měřiče), umístění (oblasti) a podřízeného oboru. Registrační účty například spadají pod fakturační účty, skupiny prostředků pod předplatná a prostředky pod skupiny prostředků.

![Počáteční zobrazení analýzy nákladů na webu Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

### <a name="understand-forecast"></a>Vysvětlení prognózy

Prognóza nákladů ukazuje projekci odhadovaných nákladů za vybrané časové období. Tento model je založený na modelu regrese časových řad. K přesné předpovědi nákladů vyžaduje aktuální data o využití a nákladech nejméně za 10 dnů. Pro konkrétní časové období model prognózy vyžaduje stejný objem školicích dat. To znamená, že například projekce na tři měsíce vyžaduje data o využití a nákladech nejméně za tři měsíce.

K zajištění projekce nákladů na jeden rok tento model využívá maximálně šest měsíců školicích dat. Ke změně predikce potřebuje školicí data nejméně za sedm dnů. Předpověď je založená na výrazných změnách, jako jsou prudké nárůsty a poklesy, ve vzorech nákladů a využití. Negeneruje individuální projekce pro jednotlivé položky ve vlastnostech umožňujících **seskupování**. Poskytuje jenom prognózu celkových kumulovaných nákladů. Pokud používáte více měn, model poskytuje prognózu nákladů jenom v USD.


## <a name="customize-cost-views"></a>Přizpůsobení zobrazení nákladů

Analýza nákladů má čtyři předdefinovaná zobrazení optimalizovaná pro nejběžnější cíle:

Zobrazení | Odpovídá na dotazy typu
--- | ---
Kumulované náklady | Kolik jsme za tento měsíc zatím utratili? Dodržím rozpočet?
Náklady na den | Vyskytl se za posledních 30 dnů nějaký nárůst v nákladech na den?
Náklady podle služby | Jak se liší naše využití v posledních třech fakturách?
Náklady podle prostředků | Které prostředky jsou zatím za tento měsíc nejdražšími náklady?

![Volič zobrazení ukazující příklad výběru na aktuální měsíc](./media/quick-acm-cost-analysis/view-selector.png)

Existuje ale mnoho případů, kdy budete potřebovat podrobnější analýzu. Přizpůsobení začíná nahoře na stránce, kde můžete vybrat datum.

Analýza nákladů zobrazuje standardně data pro aktuální měsíc. Pomocí voliče dat můžete rychle přepínat na běžné rozsahy kalendářních dat. Mezi příklady patří posledních sedm dní, poslední měsíc, aktuální rok nebo vlastní rozsah kalendářních dat. Předplatná s průběžnými platbami také zahrnují rozsahy dat na základě fakturačního období, které není vázáno na kalendářní měsíc, jako je aktuální fakturační období nebo poslední faktura. Pomocí odkazů **< PŘEDCHOZÍ** a **DALŠÍ >** v horní části nabídky můžete přejít na předchozí nebo další období. Například odkaz **< PŘEDCHOZÍ** může přepnout z období **Posledních 7 dní** na období **Před 8–14 dny** a pak **Před 15–21 dny**.

![Volič dat ukazující příklad výběru na aktuální měsíc](./media/quick-acm-cost-analysis/date-selector.png)

Ve výchozím nastavení analýza nákladů zobrazuje **kumulované** náklady. Kumulované náklady zahrnují všechny náklady za každý den plus předchozí dny v neustále rostoucím zobrazení denně agregovaných nákladů. Toto zobrazení je optimalizované tak, aby zobrazilo váš trend oproti rozpočtu za vybraný časový rozsah.

Pomocí zobrazení grafu prognózy můžete identifikovat potenciální nedodržení rozpočtu. Pokud se vyskytne potenciální nedodržení rozpočtu, zobrazí se předpokládané nadměrné výdaje červeně. V grafu se také zobrazí upozorňující symbol. Při najetí myší na symbol se zobrazí předpokládané datum překročení rozpočtu.

![Příklad ukazující potenciální nedodržení rozpočtu](./media/quick-acm-cost-analysis/budget-breach.png)

K dipozici máte také **denní** zobrazení, abyste se mohli podívat na náklady v jednotlivých dnech. Denní zobrazení nezobrazuje trend růstu. Toto zobrazení je navrženo tak, aby zobrazovalo nepravidelnosti, protože ze dne na den dochází k prudkému nárůstu nebo poklesu nákladů. Pokud jste vybrali rozpočet, denní zobrazení také zobrazuje odhad vašeho denního rozpočtu.

Pokud vaše denní náklady neustále překračují odhadovaný denní rozpočet, můžete očekávat, že překročíte měsíční rozpočet. Odhadovaný denní rozpočet představuje prostředek, který vám pomůže vizualizovat rozpočet na nižší úrovni. Pokud denní náklady kolísají, je srovnání odhadovaného denního rozpočtu s měsíčním rozpočtem méně přesné.

Tady je denní zobrazení nedávných útrat se zapnutou prognózou útrat.
![Denní zobrazení s příkladem denních nákladů za aktuální měsíc](./media/quick-acm-cost-analysis/daily-view.png)

Pokud je prognóza útrat vypnutá, neuvidíte předpokládané útraty na budoucí data. Také pokud si zobrazíte náklady za minulá časová období, nebude zobrazená prognóza nákladů.

Obecně můžete očekávat, že se data nebo oznámení o využitých prostředcích budou zobrazovat během 8 až 12 hodin.


Možnost **Seskupit podle** nabízí běžné vlastnosti, pomocí kterých můžete rozdělit náklady a identifikovat hlavní přispěvatele. Pokud třeba chcete zobrazit seskupení podle značek prostředků, vyberte klíč značky, podle kterého chcete seskupovat. Náklady se rozdělí podle jednotlivých hodnot značky a bude u nich další segment pro prostředky, které danou značku nemají.

[Označování podporuje většina prostředků Azure.](../../azure-resource-manager/management/tag-support.md) Některé značky ale nejsou k dispozici ve službě Cost Management a při fakturaci. Navíc se nepodporují značky skupin prostředků. Údaje vázané na značky zahrnují data o využití nahlášená až *po* nastavení značky u prostředku. Značky se nedají použít pro retroaktivní shrnutí.

Pokud vás zajímá, jak se dá pomocí zásad značek Azure zlepšit viditelnost dat o nákladech, podívejte se na video o [kontrole zásad značek pomocí služby Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw).

Tady je zobrazení nákladů na služby Azure za aktuální měsíc.

![Seskupené denní kumulované zobrazení s příkladem nákladů na služby Azure za poslední měsíc](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Ve výchozím nastavení zobrazuje analýza nákladů všechny náklady na využití a nákupy, jak postupně nabíhají a jak je uvidíte ve své faktuře. Označuje se to také jako **skutečné náklady**. Zobrazení skutečných nákladů je ideální pro kontrolu vaší faktury. Špičky v nákladech za nákupy ale můžou být alarmující, pokud si dáváte pozor na anomálie v útratách a jiné změny v nákladech. Pokud chcete špičky způsobené náklady na nákupy rezervací zploštit, přepněte na **amortizační náklady**.

![Přepnutím ze skutečných nákladů na amortizační je možné zobrazit náklady na nákupy rezervací rozprostřené do celého období a přidělené k prostředkům, které rezervaci využily.](./media/quick-acm-cost-analysis/metric-picker.png)

Amortizační náklady rozdělují nákupy rezervací do denních částek a rozprostřou je přes celou dobu trvání období rezervace. Například místo nákupu v hodnotě 365 Kč z 1. ledna se tak každý den od 1. ledna do 31. prosince zobrazí nákup v hodnotě 1 Kč. Kromě základní amortizace jsou tyto náklady také přerozděleny a přidruženy ke konkrétním prostředkům, které rezervaci využily. Pokud by se například denní poplatek ve výši 1 Kč rozdělil mezi dva virtuální počítače, zobrazily by se vám za tento den dva poplatky ve výši 0,50 Kč. Pokud část rezervace nebude některý den využitá, zobrazí se vám jeden poplatek ve výši 0,50 Kč přidružený k příslušnému virtuálnímu počítači a další poplatek ve výši 0,50 Kč s typem poplatku `UnusedReservation`. Upozorňujeme, že náklady na nevyužité rezervace se dají zobrazit jenom v zobrazení amortizačních nákladů.

Vzhledem k rozdílům v započítávání nákladů je důležité mít na vědomí, že v zobrazení skutečných a amortizačních nákladů se zobrazí rozdílné celkové částky. Obecně platí, že celkové náklady v měsících s nákupy rezervací se při přepnutí na amortizační náklady sníží a v měsících po nákupech rezervací se zvýší. Amortizace je v současné době dostupná jenom pro nákupy rezervací a není dostupná pro nákupy přes Azure Marketplace.

Na následujícím obrázku jsou uvedené názvy skupin prostředků. Seskupením podle značek můžete zobrazit celkové náklady pro jednotlivé značky nebo můžete zobrazit všechny značky pro konkrétní prostředek pomocí zobrazení **Náklady podle prostředků**.

![Úplná data pro aktuální zobrazení se zobrazenými názvy skupin prostředků](./media/quick-acm-cost-analysis/full-data-set.png)

Při seskupování nákladů podle konkrétního atributu se zobrazí 10 nejvýznamnějších přispěvatelů k nákladům, od nejvyššího k nejnižšímu. Pokud je přispěvatelů více než 10, zobrazí se devět nejvýznamnějších a potom skupina **Ostatní**, která představuje všechny zbývající skupiny dohromady. Při seskupování podle značek se zobrazí skupina **Neoznačeno** pro náklady, které nemají použitý klíč značky. Skupina **Neoznačeno** je vždycky poslední, i když jsou náklady bez značky vyšší než náklady se značkami. Pokud existuje 10 nebo více hodnot značek, budou náklady bez značky součástí skupiny **Ostatní**. Přepnutím do tabulkového zobrazení a změnou úrovně podrobností na **Žádné** můžete zobrazit všechny hodnoty seřazené od nejvyšší částky po nejnižší.

Prostředky virtuálních počítačů, sítě a úložiště typu Classic nesdílejí podrobná data o fakturaci. Při seskupování nákladů jsou sloučené jako **klasické služby**.

Kontingenční grafy pod hlavním grafem zobrazují různá seskupení, která vám poskytnou širší přehled o celkových nákladech za vybrané časové období pro nastavené filtry. Výběrem vlastnosti nebo značky můžete zobrazit agregované náklady podle libovolné dimenze.

![Příklad kontingenčních grafů](./media/quick-acm-cost-analysis/pivot-charts.png)

Pro jakékoli zobrazení si můžete zobrazit celou datovou sadu. Data se zobrazí podle toho, jaké výběry nebo filtry použijete. Pokud si chcete zobrazit celou datovou sadu, vyberte seznam **Typ grafu** a potom vyberte zobrazení **Tabulka**.

![Data pro aktuální zobrazení v tabulkovém zobrazení](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>Vysvětlení možností seskupování a filtrování

V následující tabulce jsou uvedené některé nejběžnější možnosti seskupování a filtrování a situace vhodné k jejich použití.

| Vlastnost | Kdy je použít | Poznámky |
| --- | --- | --- |
| **Zóny dostupnosti** | Chcete rozdělit náklady na služby AWS podle zóny dostupnosti. | Dá se použít jenom pro obory služby AWS a skupiny pro správu. Údaje o Azure nezahrnují zónu dostupnosti a budou se zobrazovat jako **Nepoužitelné**. |
| **Fakturační období** | Chcete rozdělit náklady na průběžně placené služby podle měsíce, ve kterém byly (nebo budou) fakturované. | Pomocí možnosti **Fakturační období** můžete získat přesný rozpis fakturovaných poplatků za průběžně placené služby. Pokud filtrujte podle vlastního rozsahu dat, zahrňte 2 dny před fakturačním obdobím a po něm. Při omezení na přesná data fakturačního období by se částky nemusely shodovat s fakturou. Takto si můžete zobrazit náklady ze všech faktur ve fakturačním období. K vyfiltrování konkrétní faktury použijte **ID faktury**. Platí jenom pro předplatná s průběžnými platbami, protože účty EA a MCA se fakturují podle kalendářních měsíců. Účty EA nebo MCA můžou stejného cíle dosáhnout tak, že jako datum vyberou kalendářní měsíc nebo zvolí měsíční úroveň podrobností. |
| **Typ poplatku** | Chcete rozdělit náklady na využití, nákupy, refundace a nevyužité rezervace. | Nákupy rezervací a refundace jsou k dispozici jenom při použití skutečných nákladů (ne při použití amortizačních nákladů). Náklady na nevyužité rezervace jsou dostupné jenom v zobrazení amortizačních nákladů. |
| **Oddělení** | Chcete rozdělit náklady podle oddělení ve smlouvě EA. | Je k dispozici jenom pro účty EA a skupiny pro správu. Předplatná s průběžnými platbami nemají oddělení a budou se zobrazovat jako **Nepoužitelné** nebo **Nepřiřazeno**. |
| **Registrační účet** | Chcete rozdělit náklady podle vlastníka účtu EA. | Je k dispozici jenom pro fakturační účty EA, oddělení a skupiny pro správu. Předplatná s průběžnými platbami nemají registrační účty EA a budou se zobrazovat jako **Nepoužitelné** nebo **Nepřiřazeno**. |
| **Frekvence** | Chcete rozdělit náklady založené na využití, jednorázové náklady a opakované náklady. | |
| **ID faktury** | Chcete rozdělit náklady podle faktury, ve které byly fakturované. | Nefakturované poplatky ještě nemají ID faktury a náklady u účtů EA neobsahují podrobnosti o faktuře. Tyto náklady se tedy budou zobrazovat jako **Nepoužitelné**.  |
| **Měřič** | Chcete rozdělit náklady podle měřiče využití. | Nákupy a využití Marketplace se zobrazí jako **Nepoužitelné**. Nákupy můžete identifikovat pomocí možnosti **Typ poplatku** a poplatky za Marketplace pomocí možnosti **Typ vydavatele**. |
| **Operace** | Chcete rozdělit náklady na služby AWS podle operace. | Dá se použít jenom pro obory služby AWS a skupiny pro správu. Údaje o Azure nezahrnují operaci a budou se zobrazovat jako **Nepoužitelné**. U nich použijte možnost **Měřič**. |
| **Cenový model** | Chcete rozdělit náklady na vyžádání, rezervace nebo spotové využití. | Nákupy se zobrazují jako **Na vyžádání**. Pokud se vám zobrazí údaj **Nepoužitelné**, můžete rezervace nebo využití na vyžádání určit seskupením podle **Rezervace**. K identifikaci nákupů můžete využít **Typ poplatku**.
| **Poskytovatel** | Chcete rozdělit náklady za služby AWS a Azure. | Je k dispozici jenom pro skupiny pro správu. |
| **Typ vydavatele** | Chcete rozdělit náklady za služby AWS, Azure a Marketplace. |  |
| **Rezervace** | Chcete rozdělit náklady podle rezervace. | Veškeré nákupy nebo využití, které nejsou přidružené k rezervaci, se zobrazí jako **Nepoužitelné**. Nákupy přes Azure, AWS nebo Marketplace můžete identifikovat seskupením podle **typu vydavatele**. |
| **Prostředek** | Chcete rozdělit náklady podle prostředku. | Nákupy se zobrazují jako **Nepoužitelné**, protože se aplikují na úrovni fakturačního účtu EA/PAYG nebo na úrovni fakturačního profilu MCA a nejsou přidružené ke konkrétnímu prostředku. Nákupy přes Azure, AWS nebo Marketplace můžete identifikovat seskupením podle **typu vydavatele**. |
| **Skupina prostředků** | Chcete rozdělit náklady podle skupiny prostředků. | Nákupy, prostředky tenanta nepřidružené k předplatným, prostředky předplatných nenasazené do skupiny prostředků a klasické prostředky nemají skupinu prostředků a budou se zobrazovat jako **ostatní**, **klasické služby**, **$system** nebo **Nepoužitelné**. |
| **Typ prostředku** | Chcete rozdělit náklady podle typu prostředku. | Nákupy a klasické služby nemají typ prostředku Azure Resource Manageru a budou se zobrazovat jako **ostatní**, **klasické služby** nebo **Nepoužitelné**. |
| **Umístění prostředku** | Chcete rozdělit náklady podle umístění nebo oblasti. | Nákupy a využití Marketplace se můžou zobrazovat jako **nepřiřazeno**, **neznámé**, **nenamapované** nebo **Nepoužitelné**. |
| **Název služby** nebo **Kategorie měřiče** | Chcete rozdělit náklady podle služby Azure. | Nákupy a využití Marketplace se zobrazí jako **Nepoužitelné** nebo **nepřiřazeno**. |
| **Úroveň služby** nebo **Podkategorie měřiče** | Chcete rozdělit náklady podle dílčí klasifikace měřičů využití Azure. | Nákupy a využití Marketplace se zobrazí jako **Nepoužitelné** nebo **nepřiřazeno**. |
| **Předplatné** | Chcete rozdělit náklady podle předplatného Azure a propojeného účtu AWS. | Nákupy a prostředky tenanta se můžou zobrazit jako **Nepoužitelné**. |
| **Tag** | Chcete rozdělit náklady podle hodnot značky pro určitý klíč značky. | Značky nejsou k dispozici pro nákupy, prostředky tenanta nepřidružené k předplatným, prostředky předplatných nenasazené do skupiny prostředků nebo klasické prostředky. Upozorňujeme, že některé služby nezahrnují značky do údajů o využití. Přečtěte si také další informace o [podpoře značek pro jednotlivé typy prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support). |

Další informace o použitých termínech najdete v článku s [vysvětlením termínů používaných v souboru o využití Azure a poplatcích](../understand/understand-usage.md).


## <a name="saving-and-sharing-customized-views"></a>Ukládání a sdílení přizpůsobených zobrazení

Přizpůsobená zobrazení si můžete uložit a nasdílet je ostatním tak, že připnete analýzu nákladů na řídicí panel na webu Azure Portal nebo zkopírujete odkaz na analýzu nákladů.

Pokud chcete analýzu nákladů připnout, vyberte ikonu připínáčku v pravém horním rohu. Při připnutí analýzy nákladů se uloží jenom hlavní zobrazení grafu nebo tabulky. Nasdílením řídicího panelu můžete udělit přístup k dlaždici ostatním uživatelům. Upozorňujeme, že tímto se nasdílí jenom konfigurace řídicího panelu a ostatním uživatelům se neudělí přístup k podkladovým datům. Pokud nemáte přístup k nákladům, ale máte přístup ke sdílenému řídicímu panelu, zobrazí se vám zpráva o odepření přístupu.

Pokud chcete nasdílet odkaz na analýzu nákladů, vyberte **Sdílet** v horní části okna. Zobrazí se vlastní adresa URL, která otevře toto konkrétní zobrazení pro tento konkrétní obor. Pokud nemáte přístup k nákladům a použijete tuto adresu URL, zobrazí se vám zpráva o odepření přístupu.

Další informace o tom, jak udělit přístup k nákladům na jednotlivé podporované obory, najdete v článku o [principech oborů a práci s nimi](understand-work-scopes.md).

## <a name="automation-and-offline-analysis"></a>Automatizace a offline analýza

Nastávají situace, kdy si potřebujete stáhnout data pro další analýzy, sloučit je se svými vlastními daty nebo je integrovat do svých vlastních systémů. Služba Cost Management nabízí několik různých možností. Pokud potřebujete ad hoc souhrn vysoké úrovně (podobný tomu, co získáte v analýze nákladů), můžete si jako výchozí bod vytvořit zobrazení, jaké potřebujete. Pak si ho stáhněte výběrem možnosti **Exportovat** a pak **Stáhnout data do souboru CSV** nebo **Stáhnout data do Excelu**. Soubor stažený do Excelu poskytuje další kontext o zobrazení, které jste použili k vygenerování staženého souboru, například obor, konfiguraci dotazů, celkovou částku a datum generování.

Pokud potřebujete úplnou, neagregovanou datovou sadu, stáhněte si ji z fakturačního účtu. Pak v seznamu služeb v levém navigačním podokně portálu přejděte na **Správa nákladů a fakturace**. V případě potřeby vyberte svůj fakturační účet. Přejděte na **Využití a poplatky** a vyberte ikonu **Stáhnout** pro požadované fakturační období.

Podobný přístup můžete využít k automatizaci přijímání dat o nákladech. Můžete využít [rozhraní API Query](/rest/api/cost-management/query) pro podrobnější analýzu s dynamickým filtrováním, seskupováním a agregací nebo [rozhraní API UsageDetails](/rest/api/consumption/usageDetails) pro celou neagregovanou datovou sadu. Všeobecně dostupná verze (GA) těchto rozhraní API je 2019-01-01. S verzí **2019-04-01-preview** získáte v těchto rozhraních API přístup k rezervacím a nákupům na Marketplace.

Níže je příklad agregovaného zobrazení amortizačních nákladů rozdělených podle typu poplatku (využití, nákup nebo refundace), typu vydavatele (Azure nebo Marketplace), skupiny prostředků (prázdná pro nákupy) a rezervace (prázdná, pokud není k dispozici).

```
POST https://management.azure.com/{scope}/providers/Microsoft.CostManagement/query?api-version=2019-04-01-preview
Content-Type: application/json

{
  "type": "AmortizedCost",
  "timeframe": "Custom",
  "timePeriod": { "from": "2019-04-01", "to": "2019-04-30" },
  "dataset": {
    "granularity": "None",
    "aggregation": {
      "totalCost": { "name": "PreTaxCost", "function": "Sum" }
    },
    "grouping": [
      { "type": "dimension", "name": "ChargeType" },
      { "type": "dimension", "name": "PublisherType" },
      { "type": "dimension", "name": "Frequency" },
      { "type": "dimension", "name": "ResourceGroup" },
      { "type": "dimension", "name": "SubscriptionName" },
      { "type": "dimension", "name": "SubscriptionId" },
      { "type": "dimension", "name": "ReservationName" },
      { "type": "dimension", "name": "ReservationId" },
    ]
  },
}
```

A pokud nepotřebujete agregaci a dáváte přednost celé nezpracované datové sadě:

```
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

Pokud potřebujete skutečné náklady se zobrazením nákupů tak, jak postupně nabíhají, změňte **type**/**metric** na **ActualCost**. Další informace o těchto rozhraních API najdete v jejich dokumentaci: [Query](/rest/api/cost-management/query) a [UsageDetails](/rest/api/consumption/usageDetails). Upozorňujeme, že publikované dokumenty jsou pro verzi GA. Obě rozhraní API ale fungují stejně i ve verzi *2019-04-01-preview*, až na nový atribut type/metric a změněné názvy vlastností. (Další informace o názvech vlastností najdete níže.)

Rozhraní API služby Cost Management fungují napříč všemi obory pro tyto prostředky: skupiny prostředků, předplatná a skupiny pro správu prostřednictvím přístupu přes Azure RBAC, fakturační účty EA (registrace), oddělení a registrační účty prostřednictvím přístupu na portál EA. Další informace o oborech, včetně toho, jak určit ID oboru nebo spravovat přístup, najdete v článku o [principech oborů a práci s nimi](understand-work-scopes.md).

## <a name="next-steps"></a>Další kroky

Pokračujte prvním kurzem, ve kterém se dozvíte, jak vytvořit a spravovat rozpočty.

> [!div class="nextstepaction"]
> [Vytváření a správa rozpočtů](tutorial-acm-create-budgets.md)
