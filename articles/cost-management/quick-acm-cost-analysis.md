---
title: Rychlý start – prozkoumejte Azure náklady pomocí cost analysis | Dokumentace Microsoftu
description: Tento rychlý start vám pomůže prozkoumat a analyzovat náklady organizace na Azure pomocí analýzy nákladů.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 06/11/2019
ms.topic: quickstart
ms.service: cost-management
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: d07a52a3cd07c69b9b7e2ef43331ddd9fb455b10
ms.sourcegitcommit: 441e59b8657a1eb1538c848b9b78c2e9e1b6cfd5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67827817"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Rychlý start: Zkoumání a analýzy nákladů pomocí analýzy nákladů

Než budete moci správně řídit a optimalizovat náklady na Azure, musíte porozumět tomu, kde se náklady ve vaší organizaci generují. Také je užitečné vědět, kolik peněz služby nákladů a efektivnějších jaké prostředí a systémy. Náhled do celého spektra nákladů je důležitý, abyste přesně porozuměli výdajovým schématům organizace. Útraty vzorů můžete použít k vynucení náklady kontrolní mechanismy, jako je rozpočtů.

V tomto rychlém startu použijete analýzu nákladů a prozkoumáte a analyzujete náklady organizace. Můžete se podívat na agregované náklady na úrovni organizace, abyste porozuměli tomu, kde se náklady v průběhu času generují, a mohli identifikovat trendy útrat. Můžete si také zobrazit kumulované náklady v průběhu času, abyste mohli odhadnout měsíční, čtvrtletní a dokonce i roční trendy nákladů oproti rozpočtu. Rozpočet vám pomůže dodržet finanční omezení. Slouží také k zobrazení denních nebo měsíčních nákladů, abyste mohli izolovat nepravidelnosti v útratách. Dále si můžete stáhnout data aktuální sestavy, abyste je mohli dále analyzovat nebo použít v externím systému.

V tomto rychlém startu se naučíte:

- Kontrola nákladů v analýze nákladů
- Přizpůsobit zobrazení nákladů
- Stažení dat analýzy nákladů


## <a name="prerequisites"></a>Požadavky

Analýza nákladů podporuje různé druhy typů účtu Azure. Chcete-li zobrazit úplný seznam typů podporovaných účtů, najdete v článku [datům Cost managementu pochopit](understand-cost-mgt-data.md). K zobrazení dat na náklady, potřebujete alespoň přístup čtení ke svému účtu Azure.

Pro [Enterprise Agreement (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/) zákazníků, můžete musí mít alespoň čtení přístup k jednomu nebo více z následujících oborů zobrazení datům služby cost.

- Fakturační účet
- Oddělení
- Registrační účet
- Skupina pro správu
- Předplatné
- Resource group

Další informace o přiřazování přístupu k datům Azure Cost Management najdete v tématu [přiřazení přístupu k datům](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Kontrola nákladů v analýze nákladů

Pokud chcete zkontrolovat svoje náklady v analýzy nákladů, otevřete obor v Azure portal a vyberte **analýza nákladů** v nabídce. Například, přejděte na **předplatná**, vyberte předplatné, ze seznamu a pak vyberte **analýza nákladů** v nabídce. Použití **oboru** obranné přepnout do jiného oboru v analýzy nákladů. Další informace o oborech najdete v tématu [pochopení a práci s obory](understand-work-scopes.md).

Obor, který jste vybrali v průběhu Cost Management slouží k poskytování konsolidace a řízení přístupu k datům na informace o nákladech. Při použití oborů nevybíráte více oborů. Místo toho vyberte větší rozsah, který ostatní až vrátit, a potom vyfiltrovat vnořené obory, které potřebujete. Tento přístup je důležité pochopit, protože někteří uživatelé nemají přístup k jedné nadřazeném oboru, které zahrnuje i více vnořené obory.

Zobrazení analýzy počáteční náklady se týká následujících oblastí.

**Nahromadění zobrazení nákladů**: Představuje konfiguraci zobrazení analýzy předdefinované náklady. Každé zobrazení obsahuje rozsah kalendářních dat, členitosti, Seskupit podle a nastavení filtru. Výchozí zobrazení ukazuje celkové náklady pro aktuální fakturační období, ale můžete změnit na jiné předdefinovaná zobrazení. Další informace najdete v tématu [přizpůsobit zobrazení nákladů](#customize-cost-views).

**Skutečné náklady**: Zobrazí celkové náklady na využití a možnostech zakoupení pro aktuální měsíc tak, jak se operace a zobrazí na faktuře.

**Prognózy**: Zobrazí celkový počet předpokládaných nákladech za časové období, které zvolíte. (Prognózy je ve verzi preview.)

**Rozpočet**: Zobrazuje plánované limit útraty pro vybraný obor, pokud je k dispozici.

**Celkové členitosti**: Zobrazí celkový počet celkové denní náklady, od začátku fakturačního období. Po [vytvoření rozpočtu](tutorial-acm-create-budgets.md) pro fakturační účet nebo předplatné můžete rychle zobrazit trend útraty oproti rozpočtu. Po najetí myší na datum se zobrazí kumulované náklady pro daný den.

**Kontingenční grafy (prstencový)** : Zadejte dynamické pivotů rozdělení celkových nákladů společnou sadu standardních vlastností. Zobrazí největší nejnižší náklady pro aktuální měsíc. Kontingenční grafy můžete kdykoli změnit výběrem jiného pivotu. Náklady jsou rozdělené podle služby (kategorie měřiče), umístění (oblast) a podřízeném oboru ve výchozím nastavení. Například registračních účtů jsou za fakturační účty, skupiny prostředků jsou v rámci předplatných a prostředky jsou v rámci skupiny prostředků.

![Počáteční zobrazení analýzu nákladů na webu Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Přizpůsobení zobrazení nákladů

Analýza nákladů obsahuje čtyři integrované zobrazení, optimalizovaný pro nejčastěji používané cíle:

Zobrazení | Odpovězte na otázky jako
--- | ---
Celkové náklady | Kolik jsem strávil zatím tento měsíc? Dodržím rozpočet?
Denní náklady | Existuje byly všechny zvýšení nákladů na každý den po dobu posledních 30 dnů?
Náklady podle služeb | Měsíční využití za poslední tři faktury se liší, jak má?
Náklady podle prostředků | Které prostředky náklady na maximum, pokud tento měsíc?

![Výběr zobrazení zobrazen výběr příklad pro tento měsíc](./media/quick-acm-cost-analysis/view-selector.png)

Existuje ale mnoho případů, kdy budete potřebovat podrobnější analýzu. Přizpůsobení začíná nahoře na stránce, kde můžete vybrat datum.

Analýza nákladů zobrazuje standardně data pro aktuální měsíc. Pomocí modulu pro výběr data pro běžné rozsahy kalendářních dat rychle přepínat. Mezi příklady patří posledních sedmi dnů, poslední měsíc, do aktuálního roku nebo vlastní rozsah. Předplatná s průběžnými platbami také obsahovat rozsahy kalendářních dat podle vašeho fakturačního období, který není vázán ke kalendářnímu měsíci, jako je aktuální fakturační období nebo poslední faktury. Použití **< předchozí** a **Další >** odkazy v horní části nabídky pro přechod na předchozí nebo další období, v uvedeném pořadí. Například **< předchozí** přepne z **posledních 7 dnů** k **8-14 dny** nebo **15-21 dnů před**.

![Výběr data zobrazen výběr příklad pro tento měsíc](./media/quick-acm-cost-analysis/date-selector.png)

Ve výchozím nastavení analýza nákladů zobrazuje **kumulované** náklady. Celkové náklady zahrnují všechny náklady na každý den a předchozích dnech pohled na stále rostoucí náklady na denní agregace. Toto zobrazení je optimalizované tak, aby zobrazilo váš trend oproti rozpočtu za vybraný časový rozsah.

Pomocí zobrazení grafu prognózy identifikovat potenciální narušení rozpočtu. Když je na potenciální porušení dokáže upozornit rozpočtu, předpokládané nadměrných nákladů se zobrazí červeně. Symbol indikátor se navíc zobrazí v grafu. Ukazatel myši na symbol ukazuje odhadované datum rozpočtu porušení zabezpečení.

![Příklad zobrazující potenciální rozpočet porušení dokáže upozornit](./media/quick-acm-cost-analysis/budget-breach.png)

K dipozici máte také **denní** zobrazení, abyste se mohli podívat na náklady v jednotlivých dnech. Denní zobrazení nezobrazuje trend růstu. Toto zobrazení je navrženo tak, aby zobrazovalo nepravidelnosti, protože ze dne na den dochází k prudkému nárůstu nebo poklesu nákladů. Pokud jste vybrali rozpočtu, denní zobrazení také ukazuje odhad každodenní rozpočtu.

Pokud denní náklady jsou konzistentně nad odhadované denní rozpočtu, můžete očekávat, že budete překročí měsíční rozpočtu. Odhadované denní rozpočet je způsob pro lepší vizualizaci vašemu rozpočtu na nižší úrovni. Pokud denní náklady kolísají, je srovnání odhadovaného denního rozpočtu s měsíčním rozpočtem méně přesné.

Tady je denní přehled poslední útraty pomocí útraty prognózy zapnuté.
![Denní zobrazení příklad denních nákladů pro aktuální měsíc](./media/quick-acm-cost-analysis/daily-view.png)

Při vypnutí útraty prognózy, se nezobrazí předpokládané útraty pro budoucí data. Navíc při pohledu na náklady pro období posledních náklady předpovědi nezobrazí náklady.

Obecně můžete očekávat zobrazíte data nebo oznámení o spotřebovaných prostředků v rámci 8 až 12 hodin.


**Seskupit podle** společné vlastnosti rozdělit náklady a identifikovat hlavní přispěvatelé. Seskupit podle značek prostředků, například, vyberte klíč značky, které chcete seskupit. Náklady jsou rozdělené podle každá hodnota značky, další segment pro prostředky, které nemají dané klíčové slovo použít.

Většina [prostředky Azure podporují označování](../azure-resource-manager/tag-support.md). Nicméně některé značky nejsou k dispozici ve službě Cost Management a fakturace. Kromě toho skupiny značky prostředku se nepodporují. Služba Cost Management podporuje pouze značky prostředků ode dne, kdy jsou značky použít přímo na prostředek. Podívejte [kontrola zásady pro značky ve službě Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw) videa Další informace o použití zásad Azure značky – kvůli zlepšení viditelnosti náklady na data.

Tady je přehled náklady na službu Azure pro aktuální měsíc.

![Seskupené denní souhrnné zobrazení příklad náklady na služby Azure za poslední měsíc](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Ve výchozím nastavení, analýzy nákladů ukazuje všechny náklady na využití a možnostech zakoupení tak, jak nabíhají a bude zobrazovat na vaší faktuře, označované také jako **skutečné náklady**. Zobrazení skutečné náklady je ideální pro odsouhlasení faktuře. Koupit si poraďte se špičkami náklady na však může být znepokojující, když vám uchování přehled útraty anomálie a další změny v nákladech. Shrnout, provozní špičky zapříčiněné náklady na nákup rezervace, přepněte na **Amortizovaných nákladů**. 

![Změna mezi skutečnými a amortizované náklady zobrazíte rezervace nákupy rozšíření napříč termín a přidělovat k prostředkům, které používají rezervace](./media/quick-acm-cost-analysis/metric-picker.png)

Amortizované náklady boří nákup rezervace do každodenní bloků a šíří přes dobu trvání období rezervace. Například místo zobrazení 365 $ zakoupit na 1. ledna, zobrazí se vám 1 USD každý den zakoupit od 1. ledna do 31. prosince. Kromě základní amortizace tyto náklady jsou také nevyčerpané a související s použitím konkrétní prostředky, které používají rezervace. Například když tento denní poplatek 1 USD byla rozdělena mezi dvěma virtuálními počítači, uvidíte poplatky za dvě 0,50 USD za daný den. Pokud součást rezervace se nepoužívá pro den, zobrazí se jedné 0,50 USD účtovat přidružené k příslušné virtuální počítač a jiné 0,50 USD účtovat poplatek za typu `UnusedReservation`. Všimněte si, že náklady na nevyužité rezervace můžete zobrazit pouze při zobrazení amortizovaných nákladů.

Z důvodu změny v zastoupení náklady je důležité si uvědomit, že skutečné náklady a zobrazení amortizované náklady se zobrazí celkový počet různý. Obecně platí nižší celkové náklady na měsících, které mají nákup rezervace při prohlížení amortizované náklady a zvýší měsíců od nákupu rezervace. Amortizace je k dispozici pouze pro nákup rezervace a neplatí pro nákupy na Azure Marketplace v současnosti.

Následující obrázek ukazuje názvy skupin prostředků. Můžete seskupit podle značky, které chcete zobrazit celkové náklady za značku nebo použít **náklady podle prostředků** zobrazení, abyste viděli všechny značky pro konkrétní prostředek.

![Úplná data pro aktuální zobrazení zobrazuje názvy skupin prostředků](./media/quick-acm-cost-analysis/full-data-set.png)

Při seskupování náklady podle konkrétní atribut Nejaktivnější přispěvatelé 10 náklady zobrazí od nejvyšší k nejnižší. Pokud existuje více než 10, horní devět nákladům přispívá jsou uvedeny v **ostatní** skupiny. Tato skupina obsahuje všechny zbývající skupiny společně. Když jsou seskupeny podle klíčových slov, **Untagged** nákladů, které nemají klíč značky, použije se zobrazí skupiny. **Neoznačených** je vždy poslední, i když jsou vyšší než náklady na označené neoznačených nákladů. Neoznačených nákladů bude součástí **ostatní**, pokud existuje 10 nebo více hodnot značek.

Klasické virtuální počítače, sítě a prostředky úložiště Nesdílejte podrobné fakturačních údajů. Při sloučení jako **klasické služby** při seskupování náklady.

Kontingenční grafy v rámci hlavní grafu zobrazit různé seskupení, které poskytují širší přehled o celkové náklady pro vybrané časové období a filtry. Vyberte vlastnost nebo značek zobrazíte souhrnné náklady podle dimenzí.

![Příklad zobrazující kontingenční grafy](./media/quick-acm-cost-analysis/pivot-charts.png)

Můžete zobrazit úplnou datovou sadu pro všechna zobrazení. Podle toho, která vybrané možnosti nebo filtry, které použijete vliv na data zobrazí. Chcete-li zobrazit úplnou datovou sadu, vyberte **typ grafu** seznamu a pak vyberte **tabulky** zobrazení.

![Data pro aktuální zobrazení v tabulkovém zobrazení](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>Seskupení principy a možnosti filtrování

V následující tabulce jsou uvedeny některé nejběžnější seskupování a filtrování možností a kdy byste měli použít.

| Vlastnost | Kdy je použít |
| --- | --- |
| **Fakturační období** | Rozdělit náklady podle kategorie month faktury. Tato možnost je důležité pro předplatná s průběžnými platbami a vývoj/testování, které nejsou vázány na kalendářních měsících. Účty EA/MCA slouží k dosažení stejného cíle kalendářních měsících výběr data nebo měsíčních intervalech. |
| **Poplatek za typ** | Rozdělte využití, nákupu, refundace a nevyužité rezervace náklady. Rezervace nákupů a reklamací jsou k dispozici jenom při použití akce náklady a ne už při použití amortizované náklady. Nevyužité rezervace náklady jsou k dispozici pouze při prohlížení amortizované náklady. |
| **Cloud** | Rozdělte náklady podle AWS a Azure. Náklady na AWS jsou k dispozici pouze ze skupin pro správu, externí fakturační účty a externí odběry. |
| **Oddělení** / **části faktury** | Rozdělte náklady podle oddělení EA nebo MCA části faktury. Tato možnost je dostupná jenom pro EA/MCA fakturační účty a MCA fakturace profily. |
| **Účet pro zápis** | Rozdělte náklady podle vlastníka účtu EA. Tato možnost je dostupná jenom pro EA fakturační účty a oddělení. |
| **Frekvence** | Rozdělte náklady podle použití, jednorázové a opakované. |
| **Měření** | Rozdělit náklady podle měřiče využití Azure. Tato možnost je dostupná jenom za využití Azure. Všechny nákupy a využití webu Marketplace se budou zobrazovat jako **nezadáte** nebo **nepřiřazené**. |
| **Typ vydavatele** | Rozdělte náklady na AWS, Azure a Web Marketplace. |
| **Rezervace** | Rozdělit náklady podle rezervace. Veškeré využití, který neobsahuje rezervace se zobrazí jako **nezadáte**. |
| **Prostředek** | Rozdělit náklady podle prostředků. Všechny nákupy se zobrazí jako **nezadáte**, protože jsou aplikována na fakturační účet EA/průběžné platby nebo MCA fakturace úroveň profilu.  |
| **Skupina prostředků** | Rozdělte náklady podle skupin prostředků. Tato možnost je dostupná jenom pro který není klasický využití. Využití prostředků Classic se zobrazí jako **jiných**, a nákupy se zobrazí jako **nezadáte**. |
| **Typ prostředku** | Rozdělte náklady podle typu prostředku. Tato možnost je dostupná jenom pro který není klasický využití. Využití prostředků Classic se zobrazí jako **jiných**, a nákupy se zobrazí jako **nezadáte**. |
| **Název služby** nebo **kategorie měřiče** | Rozdělit náklady podle služeb Azure. Tato možnost je dostupná jenom za využití Azure. Všechny nákupy a využití webu Marketplace se budou zobrazovat jako **nezadáte** nebo **nepřiřazené**. |
| **Úroveň služeb** nebo **podkategorie měřiče** | Rozdělit náklady podle dílčí klasifikace v rámci měření využití Azure. Tato možnost je dostupná jenom za využití Azure. Všechny nákupy a využití webu Marketplace se budou zobrazovat jako **nezadáte** nebo **nepřiřazené**. |
| **Předplatné** | Rozdělit náklady podle předplatného. Všechny nákupy na Zobrazit jako **nezadáte**. |
| **Tag** | Rozdělit náklady podle hodnot značek pro konkrétní značku klíč. |

Další informace o podmínkách najdete v tématu [pochopit termíny používané ve službě Azure file využití a poplatků](../billing/billing-understand-your-usage.md).


## <a name="saving-and-sharing-customized-views"></a>Ukládání a sdílení přizpůsobených zobrazení

Ukládání a sdílení přizpůsobených zobrazení s ostatními připnutím analýzu nákladů na řídicím panelu portálu Azure nebo zkopírováním odkaz na analýzu nákladů. 

Pokud chcete připnout analýzy nákladů, vyberte ikonu připínáčku v pravém horním rohu. Připnutí analýzy nákladů se uloží pouze hlavní graf nebo tabulku zobrazení. Sdílejte řídicí panel, aby poskytnout ostatním přístup k dlaždici. Mějte na paměti, že to sdílí pouze konfigurace řídicího panelu a nebude ostatní udělit přístup na podkladová data. Pokud nemáte přístup k náklady, ale mají přístup k sdíleného řídicího panelu, zobrazí se vám zpráva "přístup byl odepřen".

Chcete-li sdílet odkaz na analýza nákladů, vyberte **sdílet** v horní části okna. Vlastní adresa URL se zobrazí, tím se otevře toto konkrétní zobrazení pro tento konkrétní obor. Pokud nemáte náklady na přístup a tuto adresu URL získat, zobrazí se zpráva "přístup byl odepřen". 

Další informace o udělení přístupu k náklady pro každý obor podporované, najdete v tématu [pochopení a práci s obory](understand-work-scopes.md).

## <a name="automation-and-offline-analysis"></a>Automatizace a offline analýzu

Existují situace, kdy potřebujete ke stahování dat k další analýze ho sloučit s vlastními daty, nebo integrovat do vlastních systémů. Služba Cost Management nabízí několik různých možností. Pokud potřebujete ad hoc podrobný souhrn, například získání v rámci analýzy nákladů sestavení jako výchozí bod, zobrazení, které potřebujete. Pak si stáhnout tak, že vyberete **exportovat** a vyberete **stahování dat do sdíleného svazku clusteru** nebo **stahování dat do Excelu**. Excelový soubor ke stažení obsahuje další kontext pro zobrazení, které jste použili pro generování stahování, stejně jako obor, konfigurace, celkový počet, dotazování a datum vygenerované.

Pokud byste potřebovali úplná, neagregovaným datovou sadu, můžete ji stáhněte z fakturační účet. V seznamu služeb v levém navigačním podokně na portálu přejděte k **Správa nákladů a fakturace**. Vyberte fakturační účet, pokud je k dispozici. Přejděte na **a poplatky za využití**a pak vyberte **Stáhnout** ikonu požadovaného fakturačního období.

Využijte podobný přístup k automatizaci příjem dat na náklady. Použít [rozhraní API pro dotazy](/rest/api/cost-management/query) pro podrobnější analýzu s dynamické filtrování, seskupení a agregace nebo použití [UsageDetails API](/rest/api/consumption/usageDetails) úplné, neagregovaným datové sady. Obecné dostupnosti (GA) verzi tato rozhraní API je 2019-01-01. Použití **2019-04-01-preview** získat přístup k verzi preview rezervace a nákupy v rámci těchto rozhraní API. 

Například tady je souhrnný náhled amortizované náklady na rozdělené podle typu Poplatek (využití, nákupu nebo refundace), vydavatele typu (Azure či webu Marketplace), skupinu prostředků (prázdná pro nákupy) a rezervace (prázdný, pokud není k dispozici).

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

A pokud potřebujete agregaci a dáváte přednost úplné, nezpracovaná datová sada:

```
GET https://management.azure.com/{scope}/providers/Microsoft.Consumption/usageDetails?metric=AmortizedCost&$filter=properties/usageStart+ge+'2019-04-01'+AND+properties/usageEnd+le+'2019-04-30'&api-version=2019-04-01-preview
```

Pokud potřebujete skutečné náklady, abyste mohli zobrazit nákupy, jako jsou operace, změňte **typ**/**metrika** k **ActualCost**. Další informace o těchto rozhraních API najdete v tématu [dotazu](/rest/api/cost-management/query) a [UsageDetails](/rest/api/consumption/usageDetails) dokumentace k rozhraní API. Všimněte si, že publikované dokumentace pro všeobecně dostupné verze. Ale oba fungují pro stejný *2019-04-01-preview* verze rozhraní API mimo nový atribut typu/metriky a názvy změněných vlastností. (Přečtěte si víc o názvy vlastností, které níže).
 
Pracovní rozhraní API pro správu nákladů napříč všechny obory nad prostředky: Skupina prostředků, předplatné a skupinu pro správu prostřednictvím Azure RBAC přístup, EA fakturační účty (registraci), oddělení a registračních účtů prostřednictvím přístup na portál EA. Další informace o oborech, jak určit své ID oboru nebo spravovat přístup, v [pochopení a práci s obory](understand-work-scopes.md).

## <a name="next-steps"></a>Další postup

Pokračujte prvním kurzem, ve kterém se dozvíte, jak vytvořit a spravovat rozpočty.

> [!div class="nextstepaction"]
> [Vytváření a správa rozpočtů](tutorial-acm-create-budgets.md)
