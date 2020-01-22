---
title: Rychlý Start – Prozkoumejte náklady na Azure s analýzou nákladů | Microsoft Docs
description: Tento rychlý start vám pomůže prozkoumat a analyzovat náklady organizace na Azure pomocí analýzy nákladů.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 12/04/2019
ms.topic: quickstart
ms.service: cost-management-billing
manager: micflan
ms.custom: seodec18
ms.openlocfilehash: f053b30d344e5372617a5bf98c087056c4fe2911
ms.sourcegitcommit: 7221918fbe5385ceccf39dff9dd5a3817a0bd807
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/21/2020
ms.locfileid: "76294146"
---
# <a name="quickstart-explore-and-analyze-costs-with-cost-analysis"></a>Rychlý Start: zkoumání a analýza nákladů pomocí analýzy nákladů

Než budete moci správně řídit a optimalizovat náklady na Azure, musíte porozumět tomu, kde se náklady ve vaší organizaci generují. Je také užitečné zjistit, kolik peněz vaše služby stojí, a podporovat, která prostředí a systémy. Náhled do celého spektra nákladů je důležitý, abyste přesně porozuměli výdajovým schématům organizace. Pomocí vzorů útraty můžete vymáhat mechanismy řízení nákladů, jako jsou rozpočty.

V tomto rychlém startu použijete analýzu nákladů a prozkoumáte a analyzujete náklady organizace. Můžete se podívat na agregované náklady na úrovni organizace, abyste porozuměli tomu, kde se náklady v průběhu času generují, a mohli identifikovat trendy útrat. Můžete si také zobrazit kumulované náklady v průběhu času, abyste mohli odhadnout měsíční, čtvrtletní a dokonce i roční trendy nákladů oproti rozpočtu. Rozpočet vám pomůže dodržet finanční omezení. Slouží také k zobrazení denních nebo měsíčních nákladů, abyste mohli izolovat nepravidelnosti v útratách. Dále si můžete stáhnout data aktuální sestavy, abyste je mohli dále analyzovat nebo použít v externím systému.

V tomto rychlém startu se naučíte:

- Kontrola nákladů v analýze nákladů
- Přizpůsobit zobrazení nákladů
- Stažení dat analýzy nákladů


## <a name="prerequisites"></a>Požadavky

Analýza nákladů podporuje různé druhy typů účtů Azure. Úplný seznam podporovaných typů účtů najdete v tématu [pochopení cost management dat](understand-cost-mgt-data.md). Chcete-li zobrazit data o nákladech, potřebujete alespoň oprávnění ke čtení pro váš účet Azure.

Informace o přiřazování přístupu k Azure Cost Management datům najdete v tématu [přiřazení přístupu k datům](../../cost-management/assign-access-acm-data.md).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Kontrola nákladů v analýze nákladů

Pokud chcete zkontrolovat náklady v analýze nákladů, otevřete obor v Azure Portal a v nabídce vyberte **Analýza nákladů** . Například přejděte na **odběry**, v seznamu vyberte předplatné a v nabídce vyberte **Analýza nákladů** . Pomocí možnosti **Obor** můžete v analýze nákladů přepnout na jiný obor. Další informace o oborech najdete v tématu [pochopení a práce s obory](understand-work-scopes.md).

Rozsah, který vyberete, se používá v celém Cost Management k zajištění konsolidace dat a řízení přístupu k informacím o nákladech. Při použití oborů nevybíráte více oborů. Místo toho můžete vybrat větší rozsah, který ostatní zavede do, a pak vyfiltrovat podle vložených oborů, které potřebujete. Tento přístup je důležitý pro pochopení, že někteří lidé nemusí mít přístup k jednomu nadřazenému oboru, který pokrývá více vnořených oborů.

Základní zobrazení analýzy nákladů zahrnuje následující oblasti.

**Souhrnné zobrazení nákladů**: představuje předdefinovanou konfiguraci zobrazení analýzy nákladů. Každé zobrazení zahrnuje nastavení rozsahu dat, členitosti, seskupení podle položky a filtrování. Výchozí zobrazení ukazuje kumulované náklady pro aktuální fakturační období, ale můžete ho změnit na jiná integrovaná zobrazení. Další informace najdete v tématu [přizpůsobení zobrazení nákladů](#customize-cost-views).

**Skutečné náklady**: zobrazuje celkové náklady na využití a nákup pro aktuální měsíc, jak se účtují a zobrazí se na faktuře.

**FORECAST**: zobrazuje celkové předpokládané náklady na zvolené časové období. (Prognóza je ve verzi Preview.)

**Rozpočet**: zobrazuje plánovaný limit útraty pro vybraný obor, pokud je k dispozici.

**Akumulovaná členitost**: zobrazuje celkový počet denních nákladů na začátku fakturačního období. Po [vytvoření rozpočtu](tutorial-acm-create-budgets.md) pro fakturační účet nebo předplatné můžete rychle zobrazit trend útraty oproti rozpočtu. Po najetí myší na datum se zobrazí kumulované náklady pro daný den.

**Pivoted (prstenec) grafy**: zadejte dynamické pivoty, rozčlenění celkových nákladů na společnou sadu standardních vlastností. Zobrazují náklady od nejvyšších po nejnižší pro aktuální měsíc. Kontingenční grafy můžete kdykoli změnit výběrem jiného pivotu. Náklady jsou standardně zařazeny do těchto kategorií: služba (kategorie měřiče), umístění (oblast) a podřízený obor. Například registrační účty spadají pod fakturační účty, skupiny prostředků pod předplatná a prostředky pod skupiny prostředků.

![Počáteční zobrazení analýzu nákladů na webu Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

## <a name="customize-cost-views"></a>Přizpůsobit zobrazení nákladů

Analýza nákladů má čtyři předdefinovaná zobrazení optimalizovaná pro nejběžnější cíle:

Zobrazit | Odpovědi na dotazy jako
--- | ---
Akumulované náklady | Kolik jsem doposud tento měsíc utratil? Dodržím rozpočet?
Denní náklady | Došlo v posledních 30 dnech ke zvýšení nákladů za den?
Náklady podle služby | Jak se má měsíční využití v posledních třech fakturách lišit?
Náklady podle prostředků | Které prostředky mají zatím v tomto měsíci nejvyšší náklady?

![Selektor zobrazení znázorňující vzorový výběr pro tento měsíc](./media/quick-acm-cost-analysis/view-selector.png)

Existuje ale mnoho případů, kdy budete potřebovat podrobnější analýzu. Přizpůsobení začíná nahoře na stránce, kde můžete vybrat datum.

Analýza nákladů zobrazuje standardně data pro aktuální měsíc. Pomocí výběru kalendářního data můžete rychle přepínat na běžné rozsahy kalendářních dat. Patří sem například posledních sedm dní, poslední měsíc, aktuální rok nebo vlastní rozsah kalendářních dat. Předplatná s průběžnými platbami také zahrnují rozsahy dat na základě fakturačního období, které není vázáno na kalendářní měsíc, jako je aktuální fakturační období nebo poslední faktura. Pomocí odkazů **< předchozí** a **Další >** v horní části nabídky můžete přejít na předchozí nebo další tečku v uvedeném pořadí. Například **< předchozí** se přepne z **posledních 7 dní** do **8-14 dnů před uplynutím** **15-21 dnů**.

![Výběr data zobrazen výběr příklad pro tento měsíc](./media/quick-acm-cost-analysis/date-selector.png)

Ve výchozím nastavení analýza nákladů zobrazuje **kumulované** náklady. Kumulované náklady zahrnují všechny náklady za každý den plus předchozí dny a tvoří neustále rostoucí zobrazení denních celkových nákladů. Toto zobrazení je optimalizované tak, aby zobrazilo váš trend oproti rozpočtu za vybraný časový rozsah.

Pomocí zobrazení grafu prognózy můžete určit potenciální překročení rozpočtu. Pokud dojde k potenciálnímu narušení rozpočtu, zobrazí se v červeném důsledku přečerpání. V grafu se také zobrazí symbolická značka. Najetím myší na symbol znázorníte odhadované datum překročení rozpočtu.

![Příklad znázorňující možné porušení rozpočtu](./media/quick-acm-cost-analysis/budget-breach.png)

K dipozici máte také **denní** zobrazení, abyste se mohli podívat na náklady v jednotlivých dnech. Denní zobrazení nezobrazuje trend růstu. Toto zobrazení je navrženo tak, aby zobrazovalo nepravidelnosti, protože ze dne na den dochází k prudkému nárůstu nebo poklesu nákladů. Pokud jste vybrali rozpočet, denní zobrazení také zobrazuje odhad denního rozpočtu.

Pokud vaše denní náklady neustále překračují odhadovaný denní rozpočet, můžete očekávat, že překročíte měsíční rozpočet. Odhadovaný denní rozpočet představuje prostředek, který vám pomůže vizualizovat rozpočet na nižší úrovni. Pokud denní náklady kolísají, je srovnání odhadovaného denního rozpočtu s měsíčním rozpočtem méně přesné.

Tady je denní přehled nedávné útraty s prognózou útraty, která je zapnutá.
![denní zobrazení znázorňující příklad denních nákladů pro aktuální měsíc](./media/quick-acm-cost-analysis/daily-view.png)

Když vypínáte prognózu útraty, neuvidíte předpokládané útraty pro budoucí data. Při pohledu na náklady za minulá časová období nezobrazuje předpověď nákladů náklady.

Obecně můžete očekávat, že se data nebo oznámení pro spotřebované prostředky budou zobrazovat během 8 až 12 hodin.


**Seskupit podle** společných vlastností k rozdělení nákladů a identifikaci hlavních přispěvatelů. Chcete-li seskupit podle značek prostředků, například vyberte klíč značky, podle kterého chcete seskupovat. Náklady jsou rozděleny podle každé hodnoty značky a navíc se jedná o další segment pro prostředky, u kterých se tato značka nepoužívá.

Většina [prostředků Azure podporuje označování](../../azure-resource-manager/management/tag-support.md). Některé značky však nejsou k dispozici v Cost Management a fakturaci. Značky skupin prostředků se navíc nepodporují. Podpora značek se vztahuje na použití hlášené *po* použití značky u prostředku. Značky se nepoužijí zpětně pro kumulativní náklady.

Podívejte se na [téma Postup kontroly zásad značek pomocí Azure cost management](https://www.youtube.com/watch?v=nHQYcYGKuyw) videa, kde se dozvíte, jak používat zásady značek Azure ke zlepšení viditelnosti nákladových dat.

Tady je přehled nákladů na službu Azure pro aktuální měsíc.

![Seskupené denní souhrnné zobrazení příklad náklady na služby Azure za poslední měsíc](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Ve výchozím nastavení zobrazuje analýza nákladů náklady na využití a nákup při jejich navýšení a zobrazí se na faktuře, označované také jako **skutečné náklady**. Zobrazení skutečných nákladů je ideální pro sjednocení vaší faktury. Nákupní špičky v nákladech ovšem můžou být varováním, když dáváte pozor na výkyvy v útratě a jiné změny v nákladech. Pokud chcete shrnout špičky způsobené náklady na nákup rezervací, přepněte se na **náklady na amortizaci**.

![Změna mezi skutečnými a dlouhodobými náklady na zobrazení nákupů rezervací v rámci období a přidělených k prostředkům, které použily rezervaci](./media/quick-acm-cost-analysis/metric-picker.png)

Amortizované náklady rozloží nákupy rezervací do denních bloků a rozšíří je na celou dobu trvání období rezervace. Například místo zobrazení nákupu $365 od 1. ledna se každý den zobrazí $1 nákup od 1. ledna do 31. prosince. Kromě základní amortizace jsou tyto náklady také přerozděleny a přidruženy pomocí konkrétních prostředků, které rezervaci použily. Například pokud byl $1 tento denní poplatek rozdělen mezi dva virtuální počítače, zobrazí se vám za tento den poplatky za $2 0,50. Pokud část rezervace není pro daný den využívána, zobrazí se vám za $1 0,50 poplatků spojených s příslušným virtuálním počítačem a další poplatek za $0,50 s typem poplatků `UnusedReservation`. Všimněte si, že nevyužité náklady na rezervaci se dají zobrazit jenom v případě, že se prohlíží s náklady

Vzhledem ke změně způsobu, jakým jsou náklady znázorněny, je důležité si uvědomit, že zobrazení skutečných nákladů a amortizovaných nákladů ukáží různá celková čísla. Obecně platí, že celkové náklady v měsících s nákupem rezervace se po zobrazení amortizovaných nákladů sníží a v měsících po nákupu rezervace se zvýší. Amortizace je dostupná jenom pro nákupy rezervací a neplatí pro Azure Marketplace nákupy v tuto chvíli.

Následující obrázek ukazuje názvy skupin prostředků. Můžete seskupit podle značky pro zobrazení celkových nákladů na jednu značku nebo pomocí zobrazení **náklady podle prostředků** Zobrazit všechny značky pro konkrétní prostředek.

![Úplná data pro aktuální zobrazení zobrazuje názvy skupin prostředků](./media/quick-acm-cost-analysis/full-data-set.png)

Když seskupete náklady podle konkrétního atributu, zobrazí se prvních 10 přispěvatelů nákladů od nejvyšších po nejnižší. Pokud je jich více než 10, jsou přispěvatelé s horním devíti náklady zobrazeni se skupinou **dalších** , která představuje všechny zbývající skupiny v kombinaci. Při seskupování podle značek se zobrazí **neoznačená** skupina pro náklady, které nemají použit klíč značek. Bez **příznaku** je vždy poslední, i když náklady bez příznaku jsou vyšší než náklady na značku. Náklady bez příznaku budou součástí **dalších**, pokud existují 10 nebo více hodnot značek. Přepněte do zobrazení tabulky a změňte členitost na **none** , aby se zobrazily všechny hodnoty seřazené od nejvyšší po nejnižší náklady.

Klasické virtuální počítače, sítě a prostředky úložiště nesdílejí podrobná data o fakturaci. Při seskupování nákladů se sloučí jako **klasické služby** .

Kontingenční grafy v hlavním grafu zobrazují různá seskupení, což vám poskytne přehled o celkových nákladech za vybrané časové období a filtry. Vyberte vlastnost nebo značek zobrazíte souhrnné náklady podle dimenzí.

![Příklad znázorňující kontingenční grafy](./media/quick-acm-cost-analysis/pivot-charts.png)

Celou datovou sadu můžete zobrazit pro jakékoli zobrazení. Podle toho, jaké výběry nebo filtry použijete, ovlivní zobrazená data. Chcete-li zobrazit celou datovou sadu, vyberte seznam **typ grafu** a pak vyberte možnost zobrazení **tabulky** .

![Data pro aktuální zobrazení v tabulkovém zobrazení](./media/quick-acm-cost-analysis/chart-type-table-view.png)


## <a name="understanding-grouping-and-filtering-options"></a>Principy možností seskupování a filtrování

V následující tabulce jsou uvedeny některé nejběžnější možnosti seskupování a filtrování a jejich použití.

| Vlastnost | When to use | Poznámky |
| --- | --- | --- |
| **Zóny dostupnosti** | Rozdělte AWS náklady podle zóny dostupnosti. | Platí pouze pro obory AWS a skupiny pro správu. Data Azure nezahrnují zónu dostupnosti a budou se **zobrazovat jako nedostupná.** |
| **Fakturační období** | Rozdělte PAYG náklady za měsíc, které byly (nebo budou) fakturované. | K získání přesné reprezentace fakturovaných poplatků za PAYG použijte **fakturační období** . Pokud filtrování odchází k vlastnímu časovému období, uveďte 2 dny před a po fakturačním období. Omezení na přesné datum fakturačního období se neshoduje s fakturou. Zobrazí náklady ze všech faktur ve fakturačním období. Použijte **ID faktury** k filtrování dolů na konkrétní fakturu. Platí jenom pro předplatná PAYG, protože EA a MCA se účtují podle kalendářních měsíců. Účty EA/MCA můžou použít kalendářní měsíce ve výběru data nebo v měsíčních členitosti k provedení stejného cíle. |
| **Typ poplatků** | Rozdělení využití, nákup, refundace a nevyužité náklady na rezervaci | Nákupy a refundace rezervací jsou k dispozici pouze v případě, že se používají skutečné náklady, a ne při použití nákladů na amortizaci. Nevyužité náklady na rezervaci jsou dostupné jenom při pohledu na probíhající se náklady na amortizaci. |
| **Department** | Snížit náklady podle oddělení EA. | K dispozici pouze pro EA a skupiny pro správu. Předplatná PAYG neobsahují oddělení a zobrazí se jako **nepoužité** nebo **Nepřiřazené**. |
| **Registrační účet** | Snížit náklady podle vlastníka účtu EA. | Dostupné jenom pro fakturační účty, oddělení a skupiny pro správu EA. Předplatná PAYG nemají účty pro registraci EA a budou se zobrazovat jako **nepoužité** nebo **Nepřiřazené**. |
| **Frekvence** | Rozdělte náklady založené na využití, jednorázové a opakované. | |
| **ID faktury** | Snížit náklady pomocí fakturované faktury. | Nefakturované poplatky ještě nemají ID faktury a náklady na EA nezahrnují podrobnosti o faktuře a budou se zobrazovat jako **nepoužitelné**.  |
| **Měřiče** | Snížit náklady podle měřiče využití. | Nákupy a používání Marketplace se zobrazí jako **nepoužitelné**. Pokud chcete identifikovat poplatky na webu Marketplace, přečtěte si téma **typ poplatků za** účelem identifikace nákupu a **typu vydavatele** . |
| **Operace** | Rozdělte AWS náklady podle operace. | Platí pouze pro obory AWS a skupiny pro správu. Data Azure nezahrnují operaci a budou se zobrazovat jako **nepoužitá** – **měřič** použijte místo toho. |
| **Cenový model** | Snížení nákladů na vyžádání, rezervace nebo použití v místě | Nákupy se zobrazí jako **OnDemand**. Pokud se vám zobrazí možnost **Nepoužito**, seskupovat podle **rezervace** , abyste zjistili, jestli je využití rezervace nebo využití na vyžádání a **typ poplatků** k identifikaci nákupů.
| **Poskytovatel** | Náklady na rozdělení AWS a Azure | K dispozici pouze pro skupiny pro správu. |
| **Typ vydavatele** | Rozdělte náklady na AWS, Azure a Marketplace. |  |
| **Výhrad** | Náklady rozdělte dolů podle rezervací. | Jakékoli použití nebo nákupy, které nejsou přidružené k rezervaci, se zobrazí jako **nedostupné**. Seskupit podle **typu vydavatele** a Identifikujte si jiné nákupy Azure, AWS nebo Marketplace. |
| **Prostředek** | Rozdělte náklady podle prostředku. | Nákupy se ukážou jako **nepoužité**, protože se používají na úrovni fakturačního účtu EA/PAYG nebo na úrovni fakturačního profilu MCA a nejsou přidružené ke konkrétnímu prostředku. Seskupit podle **typu vydavatele** a Identifikujte si jiné nákupy Azure, AWS nebo Marketplace. |
| **Skupina prostředků** | Rozdělte náklady podle skupiny prostředků. | Nákupy, prostředky tenanta nepřidružené k předplatným, prostředky předplatného nenasazené do skupiny prostředků a klasické prostředky nemají skupinu prostředků a budou se zobrazovat jako **ostatní**, **klasické služby**, **$System**nebo **nepoužitelné**. |
| **Typ prostředku** | Rozdělte náklady podle typu prostředku. | Nákupy a klasické služby nemají Azure Resource Manager typ prostředku a budou se zobrazovat jako **ostatní**, **klasické služby**nebo **nepoužitelné**. |
| **Umístění prostředku** | Náklady na rozdělení jsou podle umístění nebo oblasti. | Nákupy a používání Marketplace se můžou zobrazovat jako **Nepřiřazené**, **neznámé**, **nemapované**nebo **nepoužitelné**. |
| **Název služby** nebo **kategorie měřiče** | Snížit náklady podle služby Azure. | Nákupy a používání Marketplace se zobrazí jako **nepoužitelné** nebo **Nepřiřazené**. |
| **Úroveň služby** nebo **podkategorie měřiče** | Rozdělte náklady podle podklasifikace měřičů využití Azure. | Nákupy a používání Marketplace se zobrazí jako **nepoužitelné** nebo **Nepřiřazené**. |
| **Předplatné** | Rozdělte náklady podle předplatného Azure a propojeného účtu AWS. | Nákupy a prostředky tenanta se můžou ukázat jako **nepoužitelné**. |
| **Tag** | Snížit náklady podle hodnot značek pro určitý klíč značky. | Značky nejsou k dispozici pro nákupy, prostředky tenanta nepřidružené k předplatným, prostředky předplatného nenasazené do skupiny prostředků nebo klasické prostředky. Všimněte si, že některé služby neobsahují značky v datech o využití. Přečtěte si další informace o [podpoře značek pro jednotlivé typy prostředků](https://docs.microsoft.com/azure/azure-resource-manager/management/tag-support). |

Další informace o pojmech najdete v tématu [vysvětlení podmínek používaných v souboru využití Azure a poplatků](../understand/understand-usage.md).


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

Pokud potřebujete skutečné náklady na zobrazení nákupu při jejich nabíhání, změňte **typ**/**metrika** na **ActualCost**. Další informace o těchto rozhraních API najdete v dokumentaci k rozhraní API pro [dotazy](/rest/api/cost-management/query) a [UsageDetails](/rest/api/consumption/usageDetails) . Všimněte si, že publikované dokumenty jsou pro verzi GA. Fungují ale stejně jako verze rozhraní API *2019-04-01-Preview* mimo nový atribut Type/metric a změnily názvy vlastností. (Další informace o názvech vlastností naleznete níže.)

Cost Management rozhraní API fungují napříč všemi obory nad prostředky: Skupina prostředků, předplatné a skupina pro správu prostřednictvím přístupu do Azure RBAC, fakturačních účtů EA (registrace), oddělení a registračních účtů prostřednictvím přístupu na portál EA. Přečtěte si další informace o oborech, včetně toho, jak určit ID oboru nebo jak spravovat přístup, v tématu [pochopení a práce s obory](understand-work-scopes.md).

## <a name="next-steps"></a>Další kroky

Pokračujte prvním kurzem, ve kterém se dozvíte, jak vytvořit a spravovat rozpočty.

> [!div class="nextstepaction"]
> [Vytváření a správa rozpočtů](tutorial-acm-create-budgets.md)
