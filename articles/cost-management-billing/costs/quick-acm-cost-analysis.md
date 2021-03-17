---
title: 'Rychlý start: Prozkoumání nákladů na Azure s využitím analýzy nákladů'
description: Tento rychlý start vám pomůže prozkoumat a analyzovat náklady organizace na Azure pomocí analýzy nákladů.
author: bandersmsft
ms.author: banders
ms.date: 03/10/2021
ms.topic: quickstart
ms.service: cost-management-billing
ms.subservice: cost-management
ms.reviewer: micflan
ms.custom: contperf-fy21q2
ms.openlocfilehash: 86f7bbbb52b1596e20fb1f3a67e42b2dd27c501a
ms.sourcegitcommit: 225e4b45844e845bc41d5c043587a61e6b6ce5ae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/11/2021
ms.locfileid: "103012595"
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

Informace o přiřazování přístupu k datům služby Azure Cost Management najdete v článku [Přiřazení přístupu k datům](./assign-access-acm-data.md).

Pokud máte nové předplatné, nemůžete rovnou využívat funkce služby Cost Management. Může trvat až 48 hodin, než budete moct využívat všechny funkce služby Cost Management.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

- Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="review-costs-in-cost-analysis"></a>Kontrola nákladů v analýze nákladů

Pokud si chcete zkontrolovat náklady v analýze nákladů, otevřete požadovaný obor na webu Azure Portal a v nabídce vyberte **Analýza nákladů**. Přejděte například na **Předplatná**, vyberte předplatné ze seznamu a pak v nabídce vyberte **Analýza nákladů**. Pomocí možnosti **Obor** můžete v analýze nákladů přepnout na jiný obor.

Vybraný obor se použije v celé službě Cost Management v zájmu konsolidace dat a k řízení přístupu k informacím o nákladech. Při použití oborů nevybíráte více oborů. Místo toho vyberete rozsáhlejší obor, který zahrnuje ostatní obory, a potom vyfiltrujete potřebné vnořené obory. Tomuto přístupu je důležité rozumět, protože někteří lidé nemusí mít přístup k jednomu nadřazenému oboru, který pokrývá více vnořených oborů.

Podívejte se na video [Jak používat Cost Management na webu Azure Portal](https://www.youtube.com/watch?v=mfxysF-kTFA), kde najdete další informace o použití analýzy nákladů. Další videa najdete v [kanálu služby Cost Management na YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/mfxysF-kTFA]

Počáteční zobrazení analýzy nákladů zahrnuje následující oblasti.

**Zobrazení kumulovaných nákladů:** Představuje předdefinovanou konfiguraci zobrazení analýzy nákladů. Pro každé zobrazení jsou k dispozici nastavení rozsahu dat, úrovně podrobností, seskupení a filtrování. Výchozí zobrazení ukazuje kumulované náklady pro aktuální fakturační období, ale můžete ho změnit na jiná integrovaná zobrazení.

**Skutečné náklady:** Zobrazuje celkové náklady na využití a nákupy za aktuální měsíc, jak postupně nabíhají a objeví se ve vaší faktuře.

**Prognóza:** Zobrazuje celkové předpokládané náklady na časové období, které si zvolíte.

**Rozpočet:** Zobrazuje plánovaný limit útraty pro vybraný obor (pokud je k dispozici).

**Kumulované podrobnosti:** Zobrazuje celkové agregované denní náklady od začátku fakturačního období. Po vytvoření rozpočtu pro fakturační účet nebo předplatné můžete rychle zobrazit trend útraty oproti rozpočtu. Po najetí myší na datum se zobrazí kumulované náklady pro daný den.

**Kontingenční grafy (prstencové):** Poskytují dynamické přehledy a rozdělení celkových nákladů podle společné sady standardních vlastností. Zobrazují náklady za aktuální měsíc od největších po nejmenší. Kontingenční grafy můžete kdykoli změnit výběrem jiného pivotu. Náklady jsou ve výchozím nastavení kategorizovány podle služby (kategorie měřiče), umístění (oblasti) a podřízeného oboru. Registrační účty například spadají pod fakturační účty, skupiny prostředků pod předplatná a prostředky pod skupiny prostředků.

![Počáteční zobrazení analýzy nákladů na webu Azure Portal](./media/quick-acm-cost-analysis/cost-analysis-01.png)

### <a name="understand-forecast"></a>Vysvětlení prognózy

Na základě vašeho nedávného využití prognózy nákladů ukazují odhadované náklady pro vybrané časové období. Pokud je v analýze nákladů nastavený rozpočet, můžete zobrazit, kdy předpovědi výdajů pravděpodobně překročí prahovou hodnotu rozpočtu. Model prognózy může předpovědět budoucí náklady až do roku. Vyberte filtry pro zobrazení podrobných předpokládaných nákladů pro vybranou dimenzi.

Model prognózy je založen na regresním modelu časové řady. K přesné předpovědi nákladů vyžaduje aktuální data o využití a nákladech nejméně za 10 dnů. Pro konkrétní časové období model prognózy vyžaduje stejný objem školicích dat. To znamená, že například projekce na tři měsíce vyžaduje data o využití a nákladech nejméně za tři měsíce.

## <a name="customize-cost-views"></a>Přizpůsobení zobrazení nákladů

Analýza nákladů má čtyři předdefinovaná zobrazení optimalizovaná pro nejběžnější cíle:

Zobrazení | Odpovídá na dotazy typu
--- | ---
Kumulované náklady | Kolik jsme za tento měsíc zatím utratili? Dodržím rozpočet?
Náklady na den | Vyskytl se za posledních 30 dnů nějaký nárůst v nákladech na den?
Náklady podle služby | Jak se liší naše využití v posledních třech fakturách?
Náklady podle prostředků | Které prostředky jsou zatím za tento měsíc nejdražšími náklady?
Podrobnosti o faktuře | Jaké poplatky mám na poslední faktuře?

![Volič zobrazení ukazující příklad výběru na aktuální měsíc](./media/quick-acm-cost-analysis/view-selector.png)

Existuje ale mnoho případů, kdy budete potřebovat podrobnější analýzu. Přizpůsobení začíná nahoře na stránce, kde můžete vybrat datum.

Analýza nákladů zobrazuje standardně data pro aktuální měsíc. Pomocí voliče dat můžete rychle přepínat na běžné rozsahy kalendářních dat. Mezi příklady patří posledních sedm dní, poslední měsíc, aktuální rok nebo vlastní rozsah kalendářních dat. Předplatná s průběžnými platbami také zahrnují rozsahy dat na základě fakturačního období, které není vázáno na kalendářní měsíc, jako je aktuální fakturační období nebo poslední faktura. Pomocí odkazů **< PŘEDCHOZÍ** a **DALŠÍ >** v horní části nabídky můžete přejít na předchozí nebo další období. Například odkaz **< PŘEDCHOZÍ** může přepnout z období **Posledních 7 dní** na období **Před 8–14 dny** a pak **Před 15–21 dny**. Nezapomeňte, že když vybíráte vlastní rozsah kalendářních dat, můžete vybrat až celý rok (např. 1. ledna až 31. prosince).

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

Označování podporuje většina prostředků Azure. Některé značky ale nejsou k dispozici ve službě Cost Management a při fakturaci. Navíc se nepodporují značky skupin prostředků. Údaje vázané na značky zahrnují data o využití nahlášená až *po* nastavení značky u prostředku. Značky se nedají použít pro retroaktivní shrnutí.

Pokud vás zajímá, jak se dá pomocí zásad značek Azure zlepšit viditelnost dat o nákladech, podívejte se na video o [kontrole zásad značek pomocí služby Azure Cost Management](https://www.youtube.com/watch?v=nHQYcYGKuyw).

Tady je zobrazení nákladů na služby Azure za aktuální měsíc.

![Seskupené denní kumulované zobrazení s příkladem nákladů na služby Azure za poslední měsíc](./media/quick-acm-cost-analysis/grouped-daily-accum-view.png)

Ve výchozím nastavení zobrazuje analýza nákladů všechny náklady na využití a nákupy, jak postupně nabíhají a jak je uvidíte ve své faktuře. Označují se také jako **skutečné náklady**. Zobrazení skutečných nákladů je ideální pro kontrolu vaší faktury. Špičky v nákladech za nákupy ale můžou být alarmující, pokud si dáváte pozor na anomálie v útratách a jiné změny v nákladech. Pokud chcete špičky způsobené náklady na nákupy rezervací zploštit, přepněte na **amortizační náklady**.

![Přepnutím ze skutečných nákladů na amortizační je možné zobrazit náklady na nákupy rezervací rozprostřené do celého období a přidělené k prostředkům, které rezervaci využily.](./media/quick-acm-cost-analysis/metric-picker.png)

Amortizační náklady rozdělují nákupy rezervací do denních částek a rozprostřou je přes celou dobu trvání období rezervace. Například místo nákupu v hodnotě 365 Kč z 1. ledna se tak každý den od 1. ledna do 31. prosince zobrazí nákup v hodnotě 1 Kč. Kromě základní amortizace jsou tyto náklady také přerozděleny a přidruženy ke konkrétním prostředkům, které rezervaci využily. Pokud by se například denní poplatek ve výši 1 Kč rozdělil mezi dva virtuální počítače, zobrazily by se vám za tento den dva poplatky ve výši 0,50 Kč. Pokud část rezervace nebude některý den využitá, zobrazí se vám jeden poplatek ve výši 0,50 Kč přidružený k příslušnému virtuálnímu počítači a další poplatek ve výši 0,50 Kč s typem poplatku `UnusedReservation`. Nevyužité náklady na rezervaci se dají zobrazit jenom v případě, kdy se zobrazují amortizované náklady.

Vzhledem k rozdílům v započítávání nákladů je důležité mít na vědomí, že v zobrazení skutečných a amortizačních nákladů se zobrazí rozdílné celkové částky. Obecně platí, že celkové náklady v měsících s nákupy rezervací se při přepnutí na amortizační náklady sníží a v měsících po nákupech rezervací se zvýší. Amortizace je v současné době dostupná jenom pro nákupy rezervací a není dostupná pro nákupy přes Azure Marketplace.

Na následujícím obrázku jsou uvedené názvy skupin prostředků. Seskupením podle značek můžete zobrazit celkové náklady pro jednotlivé značky nebo můžete zobrazit všechny značky pro konkrétní prostředek pomocí zobrazení **Náklady podle prostředků**.

![Úplná data pro aktuální zobrazení se zobrazenými názvy skupin prostředků](./media/quick-acm-cost-analysis/full-data-set.png)

Při seskupování nákladů podle konkrétního atributu se zobrazí 10 nejvýznamnějších přispěvatelů k nákladům, od nejvyššího k nejnižšímu. Pokud je přispěvatelů více než 10, zobrazí se devět nejvýznamnějších a potom skupina **Ostatní**, která představuje všechny zbývající skupiny dohromady. Při seskupování podle značek se zobrazí skupina **Neoznačeno** pro náklady, které nemají použitý klíč značky. Skupina **Neoznačeno** je vždycky poslední, i když jsou náklady bez značky vyšší než náklady se značkami. Pokud existuje 10 nebo více hodnot značek, budou náklady bez značky součástí skupiny **Ostatní**. Přepnutím do tabulkového zobrazení a změnou úrovně podrobností na **Žádné** můžete zobrazit všechny hodnoty seřazené od nejvyšší částky po nejnižší.

Prostředky virtuálních počítačů, sítě a úložiště typu Classic nesdílejí podrobná data o fakturaci. Při seskupování nákladů jsou sloučené jako **klasické služby**.

Kontingenční grafy pod hlavním grafem zobrazují různá seskupení, která vám poskytnou širší přehled o celkových nákladech za vybrané časové období pro nastavené filtry. Výběrem vlastnosti nebo značky můžete zobrazit agregované náklady podle libovolné dimenze.

![Příklad kontingenčních grafů](./media/quick-acm-cost-analysis/pivot-charts.png)

Pro jakékoli zobrazení si můžete zobrazit celou datovou sadu. Data se zobrazí podle toho, jaké výběry nebo filtry použijete. Pokud si chcete zobrazit celou datovou sadu, vyberte seznam **Typ grafu** a potom vyberte zobrazení **Tabulka**.

![Data pro aktuální zobrazení v tabulkovém zobrazení](./media/quick-acm-cost-analysis/chart-type-table-view.png)

## <a name="saving-and-sharing-customized-views"></a>Ukládání a sdílení přizpůsobených zobrazení

Přizpůsobená zobrazení si můžete uložit a nasdílet je ostatním tak, že připnete analýzu nákladů na řídicí panel na webu Azure Portal nebo zkopírujete odkaz na analýzu nákladů.

Podívejte se na video věnované [sdílení a ukládání zobrazení v Azure Cost Managementu](https://www.youtube.com/watch?v=kQkXXj-SmvQ), kde se dozvíte víc o tom, jak tento portál využívat ke sdílení znalostí o nákladech v rámci vaší organizace. Další videa najdete v [kanálu služby Cost Management na YouTube](https://www.youtube.com/c/AzureCostManagement).

>[!VIDEO https://www.youtube.com/embed/kQkXXj-SmvQ]

Pokud chcete analýzu nákladů připnout, vyberte ikonu připínáčku v pravém horním rohu nebo hned vedle <Subscription Name> | Analýza nákladů. Při připnutí analýzy nákladů se uloží jenom hlavní zobrazení grafu nebo tabulky. Nasdílením řídicího panelu můžete udělit přístup k dlaždici ostatním uživatelům. Tímto způsobem se nasdílí jenom konfigurace řídicího panelu a ostatním uživatelům se neudělí přístup k podkladovým datům. Pokud nemáte přístup k nákladům, ale máte přístup ke sdílenému řídicímu panelu, zobrazí se vám zpráva o odepření přístupu.

Pokud chcete nasdílet odkaz na analýzu nákladů, vyberte **Sdílet** v horní části okna. Zobrazí se vlastní adresa URL, která otevře toto konkrétní zobrazení pro tento konkrétní obor. Pokud nemáte přístup k nákladům a použijete tuto adresu URL, zobrazí se vám zpráva o odepření přístupu.

## <a name="download-usage-data"></a>Stažení dat o využití

### <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Nastávají situace, kdy si potřebujete stáhnout data pro další analýzy, sloučit je se svými vlastními daty nebo je integrovat do svých vlastních systémů. Služba Cost Management nabízí několik různých možností. Pokud potřebujete rychlý obecný souhrn (podobný tomu, co získáte v analýze nákladů), můžete si jako výchozí bod vytvořit zobrazení, jaké potřebujete. Pak si ho stáhněte výběrem možnosti **Exportovat** a pak **Stáhnout data do souboru CSV** nebo **Stáhnout data do Excelu**. Soubor stažený do Excelu poskytuje další kontext o zobrazení, které jste použili k vygenerování staženého souboru, například obor, konfiguraci dotazů, celkovou částku a datum generování.

Pokud potřebujete úplnou, neagregovanou datovou sadu, stáhněte si ji z fakturačního účtu. Pak v seznamu služeb v levém navigačním podokně portálu přejděte na **Správa nákladů a fakturace**. V případě potřeby vyberte svůj fakturační účet. Přejděte na **Využití a poplatky** a vyberte ikonu **Stáhnout** pro požadované fakturační období.

### <a name="azure-cli"></a>[Azure CLI](#tab/azure-cli)

Začněte přípravou prostředí pro rozhraní příkazového řádku Azure:

[!INCLUDE [azure-cli-prepare-your-environment-no-header.md](../../../includes/azure-cli-prepare-your-environment-no-header.md)]

Po přihlášení použijte příkaz [az costmanagement query](/cli/azure/ext/costmanagement/costmanagement#ext_costmanagement_az_costmanagement_query) k dotazu na informace o využití vašeho předplatného od začátku měsíce:

```azurecli
az costmanagement query --timeframe MonthToDate --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000"
```

Dotaz můžete také zúžit pomocí parametru **--DataSet-Filter** nebo dalších parametrů:

```azurecli
az costmanagement query --timeframe MonthToDate --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" \
   --dataset-filter "{\"and\":[{\"or\":[{\"dimension\":{\"name\":\"ResourceLocation\",\"operator\":\"In\",\"values\":[\"East US\",\"West Europe\"]}},{\"tag\":{\"name\":\"Environment\",\"operator\":\"In\",\"values\":[\"UAT\",\"Prod\"]}}]},{\"dimension\":{\"name\":\"ResourceGroup\",\"operator\":\"In\",\"values\":[\"API\"]}}]}"
```

Jako parametr **--dataset-filter** je možné použít řetězec JSON nebo `@json-file`.

Máte také možnost použít příkaz [az costmanagement export](/cli/azure/ext/costmanagement/costmanagement/export) a vyexportovat údaje o využití do účtu služby Azure Storage. Data si potom můžete stáhnout odtud.

1. Vytvořte skupinu prostředků nebo použijte stávající skupinu prostředků. Skupinu prostředků vytvoříte pomocí příkazu [az group create](/cli/azure/group#az_group_create):

   ```azurecli
   az group create --name TreyNetwork --location "East US"
   ```

1. Vytvořte účet úložiště pro příjem exportů, nebo použijte už existující účet úložiště. Účet vytvoříte příkazem [az storage account create](/cli/azure/storage/account#az_storage_account_create):

   ```azurecli
   az storage account create --resource-group TreyNetwork --name cmdemo
   ```

1. Pro vytvoření exportu spusťte příkaz [az costmanagement export create](/cli/azure/ext/costmanagement/costmanagement/export#ext_costmanagement_az_costmanagement_export_create):

   ```azurecli
   az costmanagement export create --name DemoExport --type Usage \
   --scope "subscriptions/00000000-0000-0000-0000-000000000000" --storage-account-id cmdemo \
   --storage-container democontainer --timeframe MonthToDate --storage-directory demodirectory
   ```

---

## <a name="clean-up-resources"></a>Vyčištění prostředků

- Pokud jste připnuli vlastní zobrazení pro analýzu nákladů a už ho nepotřebujete, přejděte na řídicí panel, na který jste ho připnuli, a toto zobrazení odstraňte.
- Pokud jste si stáhli soubory s daty o využití a už je nepotřebujete, nezapomeňte je odstranit.

## <a name="next-steps"></a>Další kroky

Pokračujte prvním kurzem, ve kterém se dozvíte, jak vytvořit a spravovat rozpočty.

> [!div class="nextstepaction"]
> [Vytváření a správa rozpočtů](tutorial-acm-create-budgets.md)
