---
title: Nepřetržitý export telemetrie z Application Insights | Dokumenty společnosti Microsoft
description: Exportujte diagnostická data a data o využití do úložiště v Microsoft Azure a stáhněte si je odtud.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: f6afe42e483ab7ad5810169fc301946c75308c29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80298290"
---
# <a name="export-telemetry-from-application-insights"></a>Export telemetrie z Application Insights
Chcete zachovat telemetrii déle, než je standardní doba uchovávání? Nebo to zpracovat nějakým specializovaným způsobem? Kontinuální export je ideální pro toto. Události, které vidíte na portálu Application Insights, se dají exportovat do úložiště v Microsoft Azure ve formátu JSON. Odtud si můžete stáhnout data a napsat jakýkoli kód, který potřebujete ke zpracování.  

Před nastavením nepřetržitého exportu je třeba zvážit některé alternativy:

* Tlačítko Exportovat v horní části karty metriky nebo hledání umožňuje přenášet tabulky a grafy do excelové tabulky.

* [Služba Analytics](../../azure-monitor/app/analytics.md) poskytuje výkonný dotazovací jazyk pro telemetrii. Může také exportovat výsledky.
* Pokud chcete data [prozkoumat v Power BI](../../azure-monitor/app/export-power-bi.md ), můžete to udělat bez použití průběžného exportu.
* Rozhraní [REST API pro přístup k datům](https://dev.applicationinsights.io/) umožňuje programově přistupovat k telemetrii.
* Můžete také přistupovat k nastavení [nepřetržitého exportu přes Powershell](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Po nepřetržitém exportu zkopíruje data do úložiště (kde může zůstat tak dlouho, jak chcete), je stále k dispozici v Application Insights pro obvyklé [období uchovávání](../../azure-monitor/app/data-retention-privacy.md).

## <a name="continuous-export-advanced-storage-configuration"></a>Konfigurace rozšířeného úložiště průběžného exportu

Nepřetržitý export **nepodporuje** následující funkce/konfigurace úložiště Azure:

* Použití [bran firewall virtuální sítě/Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security) ve spojení s úložištěm objektů blob Azure.

* [Neměnné úložiště](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) pro úložiště objektů blob Azure.

* [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="create-a-continuous-export"></a><a name="setup"></a>Vytvoření nepřetržitého exportu

1. V prostředku Application Insights pro vaši aplikaci v části Konfigurace na levé straně otevřete nepřetržitý export a zvolte **Přidat**:

2. Vyberte datové typy telemetrie, které chcete exportovat.

3. Vytvořte nebo vyberte [účet úložiště Azure,](../../storage/common/storage-introduction.md) do kterého chcete data ukládat. Další informace o možnostech stanovení cen úložiště najdete na [oficiální stránce s cenami](https://azure.microsoft.com/pricing/details/storage/).

     Klikněte na Přidat, Exportovat cíl, Účet úložiště a pak buď vytvořte nové úložiště, nebo zvolte existující úložiště.

    > [!Warning]
    > Ve výchozím nastavení bude umístění úložiště nastaveno na stejnou geografickou oblast jako váš prostředek Application Insights. Pokud ukládáte v jiné oblasti, mohou vám být účtovány poplatky za převod.

4. Vytvořte nebo vyberte kontejner v úložišti.

Po vytvoření exportu začne exportovat. Data, která dorazí až po vytvoření exportu, získáte pouze data.

Může dojít ke zpoždění přibližně hodinu před tím, než se data zobrazí v úložišti.

Po dokončení prvního exportu najdete v kontejneru úložiště objektů Blob Azure podobnou následující struktuře: (To se bude lišit v závislosti na datech, která shromažďujete.)

|Name (Název) | Popis |
|:----|:------|
| [Dostupnost](export-data-model.md#availability) | Hlásí [webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md).  |
| [Událost](export-data-model.md#events) | Vlastní události generované [TrackEvent()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent). 
| [Výjimky](export-data-model.md#exceptions) |Hlásí [výjimky](../../azure-monitor/app/asp-net-exceptions.md) na serveru a v prohlížeči.
| [Zprávy](export-data-model.md#trace-messages) | Odesláno [službou TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)a [adaptéry pro protokolování](../../azure-monitor/app/asp-net-trace-logs.md).
| [Metriky](export-data-model.md#metrics) | Generováno voláním rozhraní API metriky.
| [Čítače výkonu](export-data-model.md) | Čítače výkonu shromážděné application insights.
| [Žádosti](export-data-model.md#requests)| Odesláno [trackrequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Standardní moduly používají k hlášení doby odezvy serveru měřené na serveru.| 

### <a name="to-edit-continuous-export"></a>Úprava průběžného exportu

Klikněte na nepřetržitý export a vyberte účet úložiště, který chcete upravit.

### <a name="to-stop-continuous-export"></a>Zastavení nepřetržitého exportu

Chcete-li export zastavit, klepněte na tlačítko Zakázat. Po opětovném klepnutí na tlačítko Povolit se export restartuje s novými daty. Nezískáte data, která přišla na portál, když byl export zakázán.

Chcete-li export zastavit trvale, odstraňte jej. Tím neodstraníte data z úložiště.

### <a name="cant-add-or-change-an-export"></a>Nelze přidat nebo změnit export?
* Chcete-li přidat nebo změnit exporty, potřebujete přístupová práva vlastníka, přispěvatele nebo přispěvatele aplikačních přehledů. [Další informace o rolích][roles].

## <a name="what-events-do-you-get"></a><a name="analyze"></a>Jaké události dostanete?
Exportovaná data jsou nezpracovaná telemetrická data, kterou obdržíme z vaší aplikace, s tím rozdílem, že přidáváme údaje o poloze, které vypočítáváme z IP adresy klienta.

Data, která byla zahozena [odběrem vzorků,](../../azure-monitor/app/sampling.md) nejsou do exportovaných dat zahrnuta.

Ostatní vypočtené metriky nejsou zahrnuty. Například neexportujeme průměrné využití procesoru, ale exportujeme nezpracovaná telemetrická data, ze které se průměr vypočítá.

Data také zahrnují výsledky všech [webových testů dostupnosti,](../../azure-monitor/app/monitor-web-app-availability.md) které jste nastavili.

> [!NOTE]
> **Odběru vzorků.** Pokud vaše aplikace odešle velké množství dat, funkce vzorkování může fungovat a odeslat pouze zlomek generované telemetrie. [Další informace o vzorkování.](../../azure-monitor/app/sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a>Kontrola dat
Úložiště můžete zkontrolovat přímo na portálu. Klikněte domů v nabídce zcela vlevo, v horní části, kde je uvedeno "Služby Azure" vyberte **Účty úložiště**, vyberte název účtu úložiště, na stránce přehledu vyberte **Objekty blobv** v rámci služeb a nakonec vyberte název kontejneru.

Chcete-li zkontrolovat úložiště Azure ve Visual Studiu, otevřete **zobrazení**, **Průzkumníka Cloudu**. (Pokud tento příkaz nabídky nemáte, musíte nainstalovat sadu Azure SDK: Otevřete dialogové okno **Nový projekt,** rozbalte visual c#/cloud a zvolte **Získat sadu Microsoft Azure SDK pro .NET**.)

Když otevřete úložiště objektů blob, uvidíte kontejner se sadou souborů objektů blob. Identifikátor URI každého souboru odvozeného od názvu prostředku Application Insights, jeho instrumentačního klíče, typu telemetrie typu/data a času. (Název prostředku je všechna malá písmena a klíč instrumentace vynese pomlčky.)

![Zkontrolujte úložiště objektů blob pomocí vhodného nástroje](./media/export-telemetry/04-data.png)

Datum a čas jsou UTC a jsou, když telemetrie byla uložena v úložišti - není čas, který byl vygenerován. Takže pokud napíšete kód pro stažení dat, může se lineárně pohybovat přes data.

Zde je forma cesty:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Kde

* `blobCreationTimeUtc`je doba, kdy byl objekt blob vytvořen v interním pracovním úložišti
* `blobDeliveryTimeUtc`je čas, kdy je objekt Blob zkopírován do úložiště cíle exportu

## <a name="data-format"></a><a name="format"></a>Formát dat
* Každý objekt blob je textový soubor, který obsahuje více řádků oddělených \n. Obsahuje telemetrii zpracovu ji po dobu zhruba půl minuty.
* Každý řádek představuje datový bod telemetrie, jako je požadavek nebo zobrazení stránky.
* Každý řádek je neformátovaný dokument JSON. Pokud chcete sedět a zírat na něj, otevřete ho v Visual Studiu a zvolte Upravit, Upřesnit, Formát souboru:

![Zobrazení telemetrie pomocí vhodného nástroje](./media/export-telemetry/06-json.png)

Doba trvání je v klíšťachách, kde 10 000 značek = 1 ms. Tyto hodnoty například zobrazují čas 1 ms pro odeslání požadavku z prohlížeče, 3 ms pro jeho přijetí a 1,8 s pro zpracování stránky v prohlížeči:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Podrobný odkaz datového modelu pro typy vlastností a hodnoty.](export-data-model.md)

## <a name="processing-the-data"></a>Zpracování údajů
V malém měřítku můžete napsat nějaký kód, který roztáhne data, přečte je do tabulky a tak dále. Například:

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

Větší ukázku kódu naleznete [v tématu použití role pracovního procesu][exportasa].

## <a name="delete-your-old-data"></a><a name="delete"></a>Odstranění starých dat
Jste zodpovědní za správu kapacity úložiště a odstranění starých dat v případě potřeby.

## <a name="if-you-regenerate-your-storage-key"></a>Pokud znovu vygenerujete klíč úložiště...
Pokud změníte klíč k úložišti, nepřetržitý export přestane fungovat. Ve svém účtu Azure se zobrazí oznámení.

Otevřete kartu Nepřetržitý export a upravte export. Upravte cíl exportu, ale stačí ponechat stejné úložiště vybrané. Potvrďte to klepnutím na tlačítko OK.

Nepřetržitý export se restartuje.

## <a name="export-samples"></a>Export vzorků

* [Export do SQL s použitím Stream Analytics][exportasa]
* [Ukázka analýzy streamu 2](export-stream-analytics.md)

Ve větších měřítkách zvažte [hdinsight](https://azure.microsoft.com/services/hdinsight/) - hadoop clustery v cloudu. HDInsight poskytuje celou řadu technologií pro správu a analýzu velkých objemů dat a můžete je použít ke zpracování dat, která byla exportována z Application Insights.

## <a name="q--a"></a>Otázky a odpovědi
* *Ale vše, co chci, je jednorázové stažení grafu.*  

    Ano, to zvládneš. V horní části karty klikněte na **Exportovat data**.
* *Nastavil jsem export, ale v mém obchodě nejsou žádná data.*

    Líbilo se Application Insights přijímat telemetrie z vaší aplikace od nastavení exportu? Budete dostávat pouze nová data.
* *Pokusil(a) jsem se nastavit export, ale byl odepřen přístup*

    Pokud je účet vlastněn vaší organizací, musíte být členem skupin vlastníků nebo přispěvatelů.
* *Mohu exportovat přímo do svého místního obchodu?*

    Ne, omlouvám se. Náš exportní modul v současné době funguje jenom s úložištěm Azure v tuto chvíli.  
* *Existuje nějaké omezení množství dat, které jste vložili do mého obchodu?*

    Ne. Budeme přiodesílání dat pokračovat, dokud export nesmažete. Zastavíme, pokud narazíme na vnější hranice pro ukládání objektů blob, ale to je docela obrovské. Je na vás, abyste měli kontrolu nad tím, kolik úložného prostoru používáte.  
* *Kolik objektů BLOB mám vidět ve skladu?*

  * Pro každý datový typ, který jste vybrali k exportu, se každou minutu vytvoří nový objekt blob (pokud jsou data k dispozici).
  * Kromě toho jsou přiděleny pro aplikace s vysokým provozem další jednotky oddílů. V tomto případě každá jednotka vytvoří objekt blob každou minutu.
* *Po obnovení klíče k úložišti nebo změně názvu kontejneru a export nyní nefunguje.*

    Upravte export a otevřete kartu cíl exportu. Ponechte vybrané stejné úložiště jako dříve a potvrďte klepnutím na OK. Export se restartuje. Pokud ke změně došlo v posledních několika dnech, neztratíte data.
* *Mohu export pozastavit?*

    Ano. Klikněte na Zakázat.

## <a name="code-samples"></a>Ukázky kódů

* [Ukázka analýzy streamu](export-stream-analytics.md)
* [Export do SQL s použitím Stream Analytics][exportasa]
* [Podrobný odkaz datového modelu pro typy vlastností a hodnoty.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
