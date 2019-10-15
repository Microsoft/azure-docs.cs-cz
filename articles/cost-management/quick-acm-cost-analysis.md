---
title: Rychlý Start – Prozkoumejte náklady na Azure s analýzou nákladů | Microsoft Docs
description: Tento rychlý start vám pomůže prozkoumat a analyzovat náklady organizace na Azure pomocí analýzy nákladů.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 10/14/2019
ms.topic: quickstart
ms.service: cost-management
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: f2e1d19f69b426cee870d2ede489b7c458404704
ms.sourcegitcommit: 0576bcb894031eb9e7ddb919e241e2e3c42f291d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2019
ms.locfileid: "72374773"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Rychlý Start: zkoumání a analýza nákladů pomocí analýzy nákladů

Než budete moci správně řídit a optimalizovat náklady na Azure, musíte porozumět tomu, kde se náklady ve vaší organizaci generují. Je také užitečné zjistit, kolik peněz vaše služby stojí, a podporovat, která prostředí a systémy. Náhled do celého spektra nákladů je důležitý, abyste přesně porozuměli výdajovým schématům organizace. Pomocí vzorů útraty můžete vymáhat mechanismy řízení nákladů, jako jsou rozpočty.

V tomto rychlém startu použijete analýzu nákladů a prozkoumáte a analyzujete náklady organizace. Můžete se podívat na agregované náklady na úrovni organizace, abyste porozuměli tomu, kde se náklady v průběhu času generují, a mohli identifikovat trendy útrat. Můžete si také zobrazit kumulované náklady v průběhu času, abyste mohli odhadnout měsíční, čtvrtletní a dokonce i roční trendy nákladů oproti rozpočtu. Rozpočet vám pomůže dodržet finanční omezení. Slouží také k zobrazení denních nebo měsíčních nákladů, abyste mohli izolovat nepravidelnosti v útratách. Dále si můžete stáhnout data aktuální sestavy, abyste je mohli dále analyzovat nebo použít v externím systému.

V tomto rychlém startu se naučíte:

- Zkontrolovat náklady v analýze nákladů
- Přizpůsobit zobrazení nákladů
- Stažení dat analýzy nákladů


## <a name="prerequisites"></a>Předpoklady

Analýza nákladů podporuje různé druhy typů účtů Azure. Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md). Chcete-li zobrazit data o nákladech, potřebujete alespoň oprávnění ke čtení pro váš účet Azure.

Informace o přiřazování přístupu k Azure Cost Management datům najdete v tématu [přiřazení přístupu k datům](assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Zkontrolovat náklady v analýze nákladů

Pokud chcete zkontrolovat náklady v analýze nákladů, otevřete obor v Azure Portal a v nabídce vyberte **Analýza nákladů** . Například přejděte na **odběry**, v seznamu vyberte předplatné a v nabídce vyberte **Analýza nákladů** . Pomocí možnosti **Obor** můžete v analýze nákladů přepnout na jiný obor. Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

Rozsah, který vyberete, se používá v celém Cost Management k zajištění konsolidace dat a řízení přístupu k informacím o nákladech. Při použití oborů nevybíráte více oborů. Místo toho můžete vybrat větší rozsah, který ostatní zavede do, a pak vyfiltrovat podle vložených oborů, které potřebujete. Tento přístup je důležitý pro pochopení, že někteří lidé nemusí mít přístup k jednomu nadřazenému oboru, který pokrývá více vnořených oborů.

Základní zobrazení analýzy nákladů zahrnuje následující oblasti.

**Souhrnné zobrazení nákladů**: představuje předdefinovanou konfiguraci zobrazení analýzy nákladů. Každé zobrazení zahrnuje nastavení rozsahu dat, členitosti, seskupení podle a filtrování. Výchozí zobrazení zobrazuje akumulované náklady na aktuální fakturační období, ale můžete je změnit na další integrovaná zobrazení. Další informace najdete v tématu [přizpůsobení zobrazení nákladů](#customize-cost-views).

**Skutečné náklady**: zobrazuje celkové náklady na využití a nákup pro aktuální měsíc, jak se účtují a zobrazí se na faktuře.

**FORECAST**: zobrazuje celkové předpokládané náklady na zvolené časové období. (Prognóza je ve verzi Preview.)

**Rozpočet**: zobrazuje plánovaný limit útraty pro vybraný obor, pokud je k dispozici.

**Akumulovaná členitost**: zobrazuje celkový počet denních nákladů na začátku fakturačního období. Po [vytvoření rozpočtu](tutorial-acm-create-budgets.md) pro fakturační účet nebo předplatné můžete rychle zobrazit trend útraty oproti rozpočtu. Po najetí myší na datum se zobrazí kumulované náklady pro daný den.

**Pivoted (prstenec) grafy**: zadejte dynamické pivoty, rozčlenění celkových nákladů na společnou sadu standardních vlastností. Pro aktuální měsíc zobrazuje největší až nejnižší náklady. Kontingenční grafy můžete kdykoli změnit výběrem jiného pivotu. Náklady se ve výchozím nastavení kategorizují podle služby (kategorie měřiče), umístění (oblasti) a podřízeného oboru. Například účty registrace se nacházejí v části fakturační účty, skupiny prostředků jsou v rámci předplatných a prostředky jsou v rámci skupin prostředků.

![Počáteční zobrazení analýzy nákladů v Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Přizpůsobit zobrazení nákladů

Analýza nákladů má čtyři předdefinovaná zobrazení optimalizovaná pro nejběžnější cíle:

Zobrazit | Odpovědi na dotazy jako
--- | ---
Akumulované náklady | Kolik jsem doposud strávil tento měsíc? Dodržím rozpočet?
Denní náklady | V posledních 30 dnech bylo zvýšení nákladů za den?
Náklady podle služby | Jak se má měsíční využití v posledních třech fakturách lišit?
Náklady podle prostředků | Které prostředky jsou pro tento měsíc nejdražšími náklady?

![Selektor zobrazení znázorňující vzorový výběr pro tento měsíc](./media/quick-acm-cost-analysis/view-selector.png)

Existuje ale mnoho případů, kdy budete potřebovat podrobnější analýzu. Přizpůsobení začíná nahoře na stránce, kde můžete vybrat datum.

Analýza nákladů zobrazuje standardně data pro aktuální měsíc. Použijte selektor data pro rychlé přepínání na běžné rozsahy kalendářních dat. Mezi příklady patří posledních sedm dní, poslední měsíc, aktuální rok nebo vlastní rozsah kalendářních dat. Předplatná s průběžnými platbami také zahrnují rozsahy dat na základě fakturačního období, které není vázáno na kalendářní měsíc, jako je aktuální fakturační období nebo poslední faktura. Pomocí odkazů **< předchozí** a **Další >** v horní části nabídky můžete přejít na předchozí nebo další tečku v uvedeném pořadí. Například **< předchozí** se přepne z **posledních 7 dní** do **8-14 dnů před uplynutím** **15-21 dnů**.

![Selektor data znázorňující vzorový výběr pro tento měsíc](./media/quick-acm-cost-analysis/date-selector.png)

Ve výchozím nastavení analýza nákladů zobrazuje **kumulované** náklady. Akumulované náklady zahrnují všechny náklady za každý den a za předchozí dny, pro neustálé rostoucí zobrazení denních celkových nákladů. Toto zobrazení je optimalizované tak, aby zobrazilo váš trend oproti rozpočtu za vybraný časový rozsah.

Pomocí zobrazení grafu prognóza Identifikujte potenciální porušení rozpočtu. Pokud dojde k potenciálnímu narušení rozpočtu, zobrazí se v červeném důsledku přečerpání. V grafu se zobrazí také symbol indikátoru. Najetí myší na symbol znázorňuje Odhadované datum porušení rozpočtu.

![Příklad znázorňující možné porušení rozpočtu](./media/quick-acm-cost-analysis/budget-breach.png)

K dipozici máte také **denní** zobrazení, abyste se mohli podívat na náklady v jednotlivých dnech. Denní zobrazení nezobrazuje trend růstu. Toto zobrazení je navrženo tak, aby zobrazovalo nepravidelnosti, protože ze dne na den dochází k prudkému nárůstu nebo poklesu nákladů. Pokud jste vybrali rozpočet, zobrazuje se v denním zobrazení také odhad denního rozpočtu.

Až budou denní náklady konzistentně vyšší než odhadovaný denní rozpočet, můžete očekávat, že překročíte měsíční rozpočet. Odhad denního rozpočtu je prostředkem, který vám umožňuje vizualizovat rozpočet na nižší úrovni. Pokud denní náklady kolísají, je srovnání odhadovaného denního rozpočtu s měsíčním rozpočtem méně přesné.

Tady je denní přehled nedávné útraty s prognózou útraty, která je zapnutá.
@no__t – zobrazení 0Daily znázorňující příklad denních nákladů na aktuální měsíc @ no__t-1

Když vypínáte prognózu útraty, neuvidíte předpokládané útraty pro budoucí data. Při pohledu na náklady za minulá časová období nezobrazuje předpověď nákladů náklady.

Obecně můžete očekávat, že se data nebo oznámení pro spotřebované prostředky budou zobrazovat během 8 až 12 hodin.


**Seskupit podle** společných vlastností k rozdělení nákladů a identifikaci hlavních přispěvatelů. Chcete-li seskupit podle značek prostředků, například vyberte klíč značky, podle kterého chcete seskupovat. Náklady jsou rozděleny podle každé hodnoty značky a navíc se jedná o další segment pro prostředky, u kterých se tato značka nepoužívá.

Většina [prostředků Azure podporuje označování](../azure-resource-manager/tag-support.md). Některé značky však nejsou k dispozici v Cost Management a fakturaci. Značky skupin prostředků se navíc nepodporují. Cost Management podporuje pouze značky prostředků od data, kdy jsou značky aplikovány přímo na prostředek. Podívejte se na [téma Postup kontroly zásad značek pomocí Azure cost management](https://www.youtube.com/watch?v=nHQYcYGKuyw) videa, kde se dozvíte, jak používat zásady značek Azure ke zlepšení viditelnosti nákladových dat.

Tady je přehled nákladů na službu Azure pro aktuální měsíc.

![Seskupené denní souhrnné zobrazení znázorňující příklad nákladů na službu Azure za minulý měsíc](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Ve výchozím nastavení zobrazuje analýza nákladů náklady na využití a nákup při jejich navýšení a zobrazí se na faktuře, označované také jako **skutečné náklady**. Zobrazení skutečných nákladů je ideální pro sjednocení vaší faktury. Provozní špičky si ale můžou vyvarovat, když si zachováte anomálie a další změny v nákladech. Pokud chcete shrnout špičky způsobené náklady na nákup rezervací, přepněte se na **náklady na amortizaci**.

![Změna mezi skutečnými a dlouhodobými náklady na zobrazení nákupů rezervací v rámci období a přidělených k prostředkům, které použily rezervaci](./media/quick-acm-cost-analysis/metric-picker.png)

Náklady na amortizaci přerušují nákupy rezervací do denních bloků a rozšíří je po celou dobu trvání období rezervace. Například místo zobrazení nákupu $365 od 1. ledna se každý den zobrazí $1 nákup od 1. ledna do 31. prosince. Kromě základní amortizace jsou tyto náklady také přerozděleny a přidruženy pomocí konkrétních prostředků, které rezervaci použily. Například pokud byl $1 tento denní poplatek rozdělen mezi dva virtuální počítače, zobrazí se vám za tento den poplatky za $2 0,50. V případě, že část rezervace není pro daný den využívána, zobrazí se vám za $1 0,50 poplatků spojených s příslušným virtuálním počítačem a další poplatek za $0,50 s typem poplatků `UnusedReservation`. Všimněte si, že nevyužité náklady na rezervaci se dají zobrazit jenom v případě, že se prohlíží s náklady

Vzhledem ke změně způsobu, jakým jsou náklady zastoupeny, je důležité si uvědomit, že zobrazení skutečných nákladů a amortizace se zobrazí v různých celkových číslech. Obecně platí, že celkové náklady v měsících s nákupem rezervace se po prohlédnutí s náklady na nákup sníží a v měsících dojde k vyššímu počtu měsíců po nákupu rezervací. Amortizace je dostupná jenom pro nákupy rezervací a neplatí pro Azure Marketplace nákupy v tuto chvíli.

Následující obrázek ukazuje názvy skupin prostředků. Můžete seskupit podle značky pro zobrazení celkových nákladů na jednu značku nebo pomocí zobrazení **náklady podle prostředků** Zobrazit všechny značky pro konkrétní prostředek.

![Úplná data pro aktuální zobrazení, ve kterém se zobrazují názvy skupin prostředků](./media/quick-acm-cost-analysis/full-data-set.png)

Když seskupete náklady podle konkrétního atributu, zobrazí se prvních 10 přispěvatelů nákladů od nejvyšších po nejnižší. Pokud je jich více než 10, jsou přispěvatelé s horním devíti náklady zobrazeni se skupinou **dalších** , která představuje všechny zbývající skupiny v kombinaci. Při seskupování podle značek se zobrazí **neoznačená** skupina pro náklady, které nemají použit klíč značek. Bez **příznaku** je vždy poslední, i když náklady bez příznaku jsou vyšší než náklady na značku. Náklady bez příznaku budou součástí **dalších**, pokud existují 10 nebo více hodnot značek. Přepněte do zobrazení tabulky a změňte členitost na **none** , aby se zobrazily všechny hodnoty seřazené od nejvyšší po nejnižší náklady.

Klasické virtuální počítače, sítě a prostředky úložiště nesdílejí podrobná data o fakturaci. Při seskupování nákladů se sloučí jako **klasické služby** .

Kontingenční grafy v hlavním grafu zobrazují různá seskupení, což vám poskytne přehled o celkových nákladech za vybrané časové období a filtry. Vyberte vlastnost nebo značku pro zobrazení agregovaných nákladů podle libovolné dimenze.

![Příklad znázorňující kontingenční grafy](./media/quick-acm-cost-analysis/pivot-charts.png)

Celou datovou sadu můžete zobrazit pro jakékoli zobrazení. Podle toho, jaké výběry nebo filtry použijete, ovlivní zobrazená data. Chcete-li zobrazit celou datovou sadu, vyberte seznam **typ grafu** a pak vyberte možnost zobrazení **tabulky** .

![Data pro aktuální zobrazení v tabulkovém zobrazení](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>Principy možností seskupování a filtrování

V následující tabulce jsou uvedeny některé nejběžnější možnosti seskupování a filtrování a jejich použití.

| Vlastnost | When to use |
| --- | --- |
| **Fakturační období** | Náklady na rozbalení podle faktury za měsíc. Tato možnost je důležitá pro předplatná s průběžnými platbami a vývoj/testování, která nejsou vázaná na kalendářní měsíce. Účty EA/MCA můžou použít kalendářní měsíce ve výběru data nebo v měsíčních členitosti k provedení stejného cíle. |
| **Typ poplatků** | Rozdělení využití, nákup, refundace a nevyužité náklady na rezervaci Nákupy a refundace rezervací jsou k dispozici pouze v případě, že se používají náklady na akce a nikoli při použití nákladů na amortizaci. Nevyužité náklady na rezervaci jsou dostupné jenom při pohledu na probíhající se náklady na amortizaci. |
| **Cloud** | Náklady na rozdělení AWS a Azure Náklady na AWS jsou k dispozici pouze ze skupin pro správu, externích fakturačních účtů a externích předplatných. |
| **Oddělení** / **fakturaci** | Náklady na rozdělení jsou rozdělené podle oddělení EA nebo podle faktury MCA. Tato možnost je dostupná jenom u fakturačních účtů EA/MCA a na fakturačních profilech MCA. |
| **Registrační účet** | Snížit náklady podle vlastníka účtu EA. Tato možnost je dostupná jenom pro fakturační účty a oddělení EA. |
| **Frekvence** | Rozdělte náklady založené na využití, jednorázové a opakované. |
| **Měřiče** | Snížení nákladů podle měření využití Azure Tato možnost je dostupná jenom pro využití Azure. Všechny nákupy a využití Marketplace se zobrazí jako **Neurčeno** nebo **Nepřiřazeno**. |
| **Typ vydavatele** | Rozdělte náklady na AWS, Azure a Marketplace. |
| **Výhrad** | Náklady rozdělte dolů podle rezervací. Jakékoli použití, které nezahrnuje rezervaci, se ukáže jako **neurčené**. |
| **Prostředek** | Rozdělte náklady podle prostředku. Všechny nákupy se zobrazí jako **neurčené**, protože se použijí na úrovni fakturačního účtu EA/PAYG nebo na úrovni fakturačního profilu MCA.  |
| **Skupina prostředků** | Rozdělte náklady podle skupiny prostředků. Tato možnost je k dispozici pouze pro neklasické použití. Klasické využití prostředků se zobrazí jako **jiné**a nákupy se zobrazí jako **neurčené**. |
| **Typ prostředku** | Rozdělte náklady podle typu prostředku. Tato možnost je k dispozici pouze pro neklasické použití. Klasické využití prostředků se zobrazí jako **jiné**a nákupy se zobrazí jako **neurčené**. |
| **Název služby** nebo **kategorie měřiče** | Snížit náklady podle služby Azure. Tato možnost je dostupná jenom pro využití Azure. Všechny nákupy a využití Marketplace se zobrazí jako **Neurčeno** nebo **Nepřiřazeno**. |
| **Úroveň služby** nebo **podkategorie měřiče** | Rozdělte náklady podle podklasifikace měřičů využití Azure. Tato možnost je dostupná jenom pro využití Azure. Všechny nákupy a využití Marketplace se zobrazí jako **Neurčeno** nebo **Nepřiřazeno**. |
| **Předplatné** | Rozdělte náklady podle předplatného. Všechny nákupy ukazují, jak **nejsou zadány**. |
| **Tag** | Snížit náklady podle hodnot značek pro určitý klíč značky. |

Další informace o pojmech najdete v tématu [vysvětlení podmínek používaných v souboru využití Azure a poplatků](../billing/billing-understand-your-usage.md).


## <a name="saving-and-sharing-customized-views"></a>Ukládání a sdílení přizpůsobených zobrazení

Uložte a sdílejte přizpůsobená zobrazení s ostatními tím, že připnete analýzu nákladů na řídicí panel Azure Portal nebo nakopírujete odkaz na analýzu nákladů.

Pokud chcete nákladovou analýzu připnout, vyberte ikonu připnutí v pravém horním rohu. K analýze nákladů na připnutí se uloží pouze hlavní zobrazení grafu nebo tabulky. Nasdílejte řídicí panel tak, aby ostatním uživatelům měl přístup k dlaždici. Všimněte si, že tato sdílená složka sdílí jenom konfiguraci řídicího panelu a neuděluje ostatním přístup k podkladovým datům. Pokud nemáte přístup k nákladům, ale máte přístup ke sdílenému řídicímu panelu, zobrazí se zpráva "přístup byl odepřen".

Pokud chcete sdílet odkaz na analýzu nákladů, vyberte v horní části okna **sdílet** . Zobrazí se vlastní adresa URL, která otevře toto konkrétní zobrazení pro tento konkrétní obor. Pokud nemáte přístup s náklady a získáte tuto adresu URL, zobrazí se zpráva "přístup byl odepřen".

Další informace o tom, jak udělit přístup k nákladům na jednotlivé podporované obory, najdete v informacích o [oborech a práci s](understand-work-scopes.md)nimi.

## <a name="automation-and-offline-analysis"></a>Automatizace a offline Analýza

K dispozici jsou situace, kdy potřebujete stáhnout data pro další analýzy, sloučit je s vlastními daty nebo je integrovat do vlastních systémů. Cost Management nabízí několik různých možností. Pokud potřebujete souhrn ad hoc vysoké úrovně, jako je například to, co dostanete v rámci analýzy nákladů, můžete jako výchozí bod vytvořit zobrazení, které potřebujete. Pak ho stáhněte výběrem možnosti **exportovat** a vybrat **stáhnout data do sdíleného svazku clusteru** nebo **stáhnout data do Excelu**. Stažení z Excelu poskytuje další kontext pro zobrazení, které jste použili k vygenerování stahování, jako je rozsah, konfigurace dotazů, celkový počet a Datum generování.

Pokud potřebujete úplnou, neagregovanou datovou sadu, stáhněte ji z fakturačního účtu. Pak v seznamu služeb v levém navigačním podokně portálu přejdete na **cost management + fakturace**. Vyberte fakturační účet, pokud je k dispozici. Přejít na **využití + poplatky**a potom vyberte ikonu **Stáhnout** pro požadované fakturační období.

Využijte podobný přístup pro automatizaci příjmu dat o nákladech. Použijte [rozhraní API](/rest/api/cost-management/query) pro lepší analýzu s dynamickým filtrováním, seskupením a agregací nebo použijte [rozhraní UsageDetails API](/rest/api/consumption/usageDetails) pro celou neagregovanou datovou sadu. Verze těchto rozhraní API pro obecné dostupnosti (GA) je 2019-01-01. Pomocí **2019-04-01-Preview** získáte přístup k verzi Preview rezervací a nákupů na webu Marketplace v těchto rozhraních API.

Následuje například agregované zobrazení nákladů na amortizaci v členění podle typu nákladů (využití, nákup nebo refundace), typu vydavatele (Azure nebo Marketplace), skupiny prostředků (prázdné pro nákupy) a rezervace (prázdné, pokud není k dispozici).

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

Pokud potřebujete skutečné náklady na zobrazení nákupu při jejich navýšení, změňte **typ**/**metrika** na **ActualCost**. Další informace o těchto rozhraních API najdete v dokumentaci k rozhraní API pro [dotazy](/rest/api/cost-management/query) a [UsageDetails](/rest/api/consumption/usageDetails) . Všimněte si, že publikované dokumenty jsou pro verzi GA. Fungují ale stejně jako verze rozhraní API *2019-04-01-Preview* mimo nový atribut Type/metric a změnily názvy vlastností. (Další informace o názvech vlastností naleznete níže.)

Cost Management rozhraní API fungují napříč všemi obory nad prostředky: Skupina prostředků, předplatné a skupina pro správu prostřednictvím přístupu do Azure RBAC, fakturačních účtů EA (registrace), oddělení a registračních účtů prostřednictvím přístupu na portál EA. Přečtěte si další informace o oborech, včetně toho, jak určit ID oboru nebo jak spravovat přístup, v tématu [pochopení a práce s obory](understand-work-scopes.md).

## <a name="next-steps"></a>Další kroky

Pokračujte prvním kurzem, ve kterém se dozvíte, jak vytvořit a spravovat rozpočty.

> [!div class="nextstepaction"]
> [Vytváření a správa rozpočtů](tutorial-acm-create-budgets.md)
