---
title: Průběžné export telemetrie z Application Insights | Microsoft Docs
description: Exportovat data o využití a diagnostiku do úložiště v Microsoft Azure a stažení z ní.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.devlang: na
ms.topic: article
ms.date: 02/23/2017
ms.author: mbullwin
ms.openlocfilehash: 05d271eb7d046819bb8fc2be20623cba0000d8f4
ms.sourcegitcommit: 59914a06e1f337399e4db3c6f3bc15c573079832
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/19/2018
---
# <a name="export-telemetry-from-application-insights"></a>Export telemetrie z Application Insights
Chcete zachovat telemetrie po dobu delší než doba uchování standardní? Nebo ji zpracovat nějakým způsobem specializované? Průběžné Export je ideální pro tento. Události, které vidíte na portálu služby Application Insights je možné exportovat do úložiště v Microsoft Azure ve formátu JSON. Odtud můžete stáhnout vaše data a zápis, ať kódu je třeba zpracovat.  

Pomocí průběžné exportovat mohou být účtovány dalších poplatků. Zkontrolujte vaše [cenový model](http://azure.microsoft.com/pricing/details/application-insights/).

Před nastavením průběžné export existují nějaké alternativy, které můžete vzít v úvahu:

* Tlačítko Export v horní části okna metriky nebo vyhledávání umožňuje přenos tabulky a grafy tabulky aplikace Excel.

* [Analýza](app-insights-analytics.md) poskytuje účinný dotazovací jazyk pro telemetrie. Je také možné exportovat výsledky.
* Pokud se pro díváte [zkoumat data v Power BI](app-insights-export-power-bi.md), můžete to udělat bez použití průběžné exportovat.
* [REST API přístup k datům](https://dev.applicationinsights.io/) vám umožní přístup k vaší telemetrie prostřednictvím kódu programu.
* Můžete také přístup k nastavení [průběžné exportu přes Powershell](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/new-azurermapplicationinsightscontinuousexport?view=azurermps-5.7.0).

Po průběžné exportovat zkopíruje data do úložiště (kde může zůstat pro, dokud se vám líbí), bude stále k dispozici ve službě Application Insights pro obvykle [dobu uchování](app-insights-data-retention-privacy.md).

## <a name="setup"></a> Vytvoření průběžné exportu
1. V prostředku Application Insights pro aplikace, otevřete průběžné exportovat a zvolte **přidat**:

    ![Posuňte se dolů a klikněte na tlačítko průběžné Export](./media/app-insights-export-telemetry/01-export.png)

2. Zvolte telemetrii datové typy, které chcete provést export.

3. Vytvořte nebo vyberte [účtu úložiště Azure](../storage/common/storage-introduction.md) místo, kam chcete data uložit.

    > [!Warning]
    > Ve výchozím umístění úložiště bude nastaveno na stejné zeměpisné oblasti jako prostředek Application Insights. Pokud uchováváte v jiné oblasti, mohou být účtovány poplatky za přenos.

    ![Klikněte na tlačítko Přidat, Export cílový účet úložiště a vytvořit nové úložiště nebo vyberte stávající úložiště](./media/app-insights-export-telemetry/02-add.png)

4. Vytvořte nebo vyberte kontejner v úložišti:

    ![Klikněte na tlačítko Vybrat typy událostí](./media/app-insights-export-telemetry/create-container.png)

Po vytvoření export, začne přejdete. Zobrazí jenom data, která dorazí po vytvoření exportu.

Může být zpoždění o jednu hodinu, než se data zobrazí v úložišti.

### <a name="to-edit-continuous-export"></a>Chcete-li upravit průběžné exportu

Pokud chcete později změnit typy událostí, stačí upravte exportu:

![Klikněte na tlačítko Vybrat typy událostí](./media/app-insights-export-telemetry/05-edit.png)

### <a name="to-stop-continuous-export"></a>Zastavit průběžné exportu

Export, klikněte na tlačítko zakázat. Když kliknete znovu povolit, export restartuje se nová data. Nezískáte data, která dorazila na portálu exportu bylo zakázáno.

K zastavení exportu trvale, odstraňte jej. Díky tomu nedojde k odstranění dat z úložiště.

### <a name="cant-add-or-change-an-export"></a>Nelze přidat nebo změnit exportu?
* Pokud chcete přidat nebo změnit exportuje, je třeba vlastník, Přispěvatel nebo Application Insights Přispěvatel přístupová práva. [Další informace o rolích][roles].

## <a name="analyze"></a> Jaké události získáte?
Exportovaná data je nezpracovaná telemetrická data, které obdržíme z vaší aplikace, s tím rozdílem, že přidáme data o umístění, které jsme vypočítat z IP adresy klienta.

Data, která byla zahozena podle [vzorkování](app-insights-sampling.md) není součástí exportovaná data.

Další počítané metriky nejsou zahrnuty. Například nemáte exportu průměrné využití procesoru, ale nezpracovaná telemetrická data, ze kterého průměr se vypočítává exportu.

Data také zahrnuje všechny výsledky [testy dostupnosti webu](app-insights-monitor-web-app-availability.md) který jste nastavili.

> [!NOTE]
> **Vzorkování.** Pokud vaše aplikace odešle velké množství dat, může funkci vzorkování pracovat a odesílat pouze část telemetrická generovaným. [Přečtěte si další informace o vzorkování.](app-insights-sampling.md)
>
>

## <a name="get"></a> Kontrolovat data
Si můžete prohlédnout úložiště přímo na portálu. Klikněte na tlačítko **Procházet**, vyberte svůj účet úložiště a pak otevřete **kontejnery**.

Chcete-li prověřit úložiště Azure v sadě Visual Studio, otevřete **zobrazení**, **Průzkumník cloudu**. (Pokud nemáte tohoto příkazu v nabídce, budete muset nainstalovat sadu Azure SDK: Otevřete **nový projekt** dialogové okno, rozbalte položku Visual C# / cloudu a zvolte **získat Microsoft Azure SDK for .NET**.)

Když otevřete váš úložišti objektů blob, uvidíte kontejner s určitou sadu souborů objektů blob. Identifikátor URI každého souboru odvozená od vašeho název prostředku Application Insights, jeho klíč instrumentace, telemetrie – typ nebo datum a čas. (Název prostředku je psaný malými písmeny a klíč instrumentace vynechá pomlčky.)

![Zkontrolujte úložišti objektů blob pomocí vhodného nástroje](./media/app-insights-export-telemetry/04-data.png)

Datum a čas UTC se a jsou při telemetrii byl uložen v úložišti - není čas, který byl vygenerován. Proto pokud můžete napsat kód pro stažení dat, ho můžete přesunout lineárně přes data.

Tady je formu cesty:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Kde

* `blobCreationTimeUtc` je čas vytvoření objektů blob v interní pracovní úložiště
* `blobDeliveryTimeUtc` je čas při objekt blob je zkopírovat do cílového úložiště exportu

## <a name="format"></a> Formát dat
* Každý objekt blob je textový soubor, který obsahuje více ' \n'-separated řádků. Obsahuje telemetrii zpracovaných za časové období zhruba poloviční minuta.
* Každý řádek představuje bod telemetrická data například zobrazení požadavku nebo stránky.
* Každý řádek je neformátovaný dokument JSON. Pokud chcete nacházejí a stare na to, otevřete v sadě Visual Studio a zvolte upravte, Upřesnit, formát souboru:

![Zobrazení telemetrie pomocí vhodného nástroje](./media/app-insights-export-telemetry/06-json.png)

Dobách trvání jsou v rysky, kde 10 000 značek = 1 ms. Například tyto hodnoty zobrazit čas 1 ms odesílat požadavky z prohlížeče, 3 ms pro příjem a 1.8 s zpracovat stránku v prohlížeči:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Podrobný datový model referenční informace pro vlastnost typů a hodnot.](app-insights-export-data-model.md)

## <a name="processing-the-data"></a>Zpracování dat
V malém měřítku můžete napsat kód, který vyžádá od sebe vaše data, přečtěte si ho do tabulky a tak dále. Příklad:

    private IEnumerable<T> DeserializeMany<T>(string folderName)
    {
      var files = Directory.EnumerateFiles(folderName, "*.blob", SearchOption.AllDirectories);
      foreach (var file in files)
      {
         using (var fileReader = File.OpenText(file))
         {
            string fileContent = fileReader.ReadToEnd();
            IEnumerable<string> entities = fileContent.Split('\n').Where(s => !string.IsNullOrWhiteSpace(s));
            foreach (var entity in entities)
            {
                yield return JsonConvert.DeserializeObject<T>(entity);
            }
         }
      }
    }

Větší ukázky kódu, najdete v části [pomocí rolí pracovního procesu][exportasa].

## <a name="delete"></a>Odstranit stará data
Upozorňujeme, že jste zodpovědní za správu vaše kapacita úložiště a odstraňování stará data v případě potřeby.

## <a name="if-you-regenerate-your-storage-key"></a>Pokud byste znovu generovali klíče účtu úložiště...
Pokud změníte klíč do úložiště, průběžné export přestanou fungovat. Uvidíte oznámení v účtu Azure.

Otevřete okno průběžné exportovat a upravovat export. Upravit cílový exportovat, ale nechte vybrané stejné úložiště. Klikněte na tlačítko OK potvrďte volbu.

![Upravit průběžné exportu, otevření a zavření thí cíl pro export.](./media/app-insights-export-telemetry/07-resetstore.png)

Průběžné exportu se restartuje.

## <a name="export-samples"></a>Export – ukázky

* [Exportovat do SQL pomocí služby Stream Analytics][exportasa]
* [Stream Analytics ukázka 2](app-insights-export-stream-analytics.md)

Na větších měřítek, vezměte v úvahu [HDInsight](https://azure.microsoft.com/services/hdinsight/) -clusterů systému Hadoop v cloudu. HDInsight nabízí celou řadu technologií pro správu a analýze velkých objemů dat, a můžete ho použít ke zpracování dat, která byla exportována z Application Insights.

## <a name="q--a"></a>Dotazy a odpovědi
* *Ale všechny, které mají být je jednorázové stažení grafu.*  

    Ano, můžete to udělat. V horní části okna klikněte na tlačítko **Export dat**.
* *Mám nastavit exportu, ale nejsou žádná data v tomto úložišti.*

    Application Insights zobrazila všechny telemetrie z vaší aplikace. vzhledem k tomu, že nastavíte exportu? Obdržíte jenom nová data.
* *I při pokusu o nastavení exportu, ale byl odepřen přístup*

    Pokud vaše organizace vlastní účet, musíte být členem skupiny Vlastníci nebo přispěvatele.
* *Můžete exportovat přímo do vlastní místní úložiště?*

    Ne, je nám líto. Naše export modul v současné době funkční s Azure storage v tuto chvíli.  
* *Je k dispozici žádné omezení množství dat, která jste uložili v tomto úložišti?*

    Ne. Jsme budete zachovat předání dat dokud neodstraníte exportu. Pokud jsme dosáhl vnější limity pro úložiště objektů blob, ale který je poměrně velký budete zastavení. Je to na řízení jak velké úložiště používáte.  
* *Kolik objekty BLOB může zobrazit v úložišti?*

  * Pro každý datový typ, který jste vybrali pro export do nového objektu blob se vytvoří každou minutu (pokud jsou k dispozici data).
  * Pro aplikace s intenzivní provoz, navíc další oddíl jednotky jsou přiděleny. V takovém případě jednotlivých jednotek vytvoří objekt blob každou minutu.
* *I na můj úložiště znovu vygenerovat klíč nebo změnit název kontejneru a nyní exportu nefunguje.*

    Upravte exportu a otevřete okno cílové export. Nechte na stejné úložiště jako předtím vybrali a klikněte na tlačítko OK potvrďte volbu. Export se restartuje. Pokud tato změna byla během posledních několik dní, abyste neztratili data.
* *Můžete pozastavit exportu?*

    Ano. Klikněte na tlačítko zakázat.

## <a name="code-samples"></a>Ukázky kódů

* [Ukázka Stream Analytics](app-insights-export-stream-analytics.md)
* [Exportovat do SQL pomocí služby Stream Analytics][exportasa]
* [Podrobný datový model referenční informace pro vlastnost typů a hodnot.](app-insights-export-data-model.md)

<!--Link references-->

[exportasa]: app-insights-code-sample-export-sql-stream-analytics.md
[roles]: app-insights-resources-roles-access-control.md
