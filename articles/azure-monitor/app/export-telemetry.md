---
title: Průběžný export telemetrie z Application Insights | Microsoft Docs
description: Exportujte data o využití a diagnostiku do úložiště v Microsoft Azure a Stáhněte si z něj.
ms.topic: conceptual
ms.date: 03/25/2020
ms.openlocfilehash: f6afe42e483ab7ad5810169fc301946c75308c29
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80298290"
---
# <a name="export-telemetry-from-application-insights"></a>Export telemetrie z Application Insights
Chcete udržet telemetrii déle než standardní doba uchovávání? Nebo ji zpracujete specializovaným způsobem? Pro tuto dobu je ideální pro průběžný export. Události, které vidíte na portálu Application Insights, se dají exportovat do úložiště v Microsoft Azure ve formátu JSON. Odtud si můžete stáhnout svá data a napsat kód, který budete potřebovat k jeho zpracování.  

Před nastavením průběžného exportu je možné zvážit několik možností:

* Tlačítko Exportovat v horní části karty metriky nebo hledání umožňuje přenést tabulky a grafy do excelové tabulky.

* [Analýza](../../azure-monitor/app/analytics.md) poskytuje výkonný dotazovací jazyk pro telemetrii. Může také exportovat výsledky.
* Pokud hledáte [data v Power BI](../../azure-monitor/app/export-power-bi.md ), můžete to udělat bez použití průběžného exportu.
* [Přístup k datům REST API](https://dev.applicationinsights.io/) umožňuje programově přistupovat k telemetrie.
* Můžete také získat přístup k nastavení [průběžného exportu prostřednictvím PowerShellu](https://docs.microsoft.com/powershell/module/az.applicationinsights/new-azapplicationinsightscontinuousexport).

Po průběžném exportu kopíruje vaše data do úložiště (kde může zůstat tak dlouho, jak budete chtít), je stále k dispozici v Application Insights pro obvyklou [dobu uchování](../../azure-monitor/app/data-retention-privacy.md).

## <a name="continuous-export-advanced-storage-configuration"></a>Průběžný export – Pokročilá konfigurace úložiště

Průběžný export **nepodporuje** následující funkce a konfigurace služby Azure Storage:

* Použití [bran firewall VNet/Azure Storage](https://docs.microsoft.com/azure/storage/common/storage-network-security) ve spojení s úložištěm objektů BLOB v Azure.

* [Neměnné úložiště](https://docs.microsoft.com/azure/storage/blobs/storage-blob-immutable-storage) pro úložiště objektů BLOB v Azure

* [Azure Data Lake Storage Gen2](https://docs.microsoft.com/azure/storage/blobs/data-lake-storage-introduction).

## <a name="create-a-continuous-export"></a><a name="setup"></a>Vytvoření průběžného exportu

1. V Application Insights prostředek pro aplikaci v části konfigurovat na levé straně otevřete průběžný export a klikněte na **Přidat**:

2. Vyberte datové typy telemetrie, které chcete exportovat.

3. Vytvořte nebo vyberte [účet služby Azure Storage](../../storage/common/storage-introduction.md) , do kterého chcete ukládat data. Další informace o cenových možnostech úložiště najdete na [stránce oficiálních cen](https://azure.microsoft.com/pricing/details/storage/).

     Klikněte na Přidat, exportovat cíl, účet úložiště a pak buď vytvořte nové úložiště, nebo vyberte existující úložiště.

    > [!Warning]
    > Ve výchozím nastavení bude umístění úložiště nastaveno na stejnou geografickou oblast jako prostředek Application Insights. Pokud ukládáte do jiné oblasti, můžete účtovat poplatky za přenos.

4. Vytvořte nebo vyberte kontejner v úložišti.

Po vytvoření exportu začne pokračovat. Dostanete jenom data, která dorazí po vytvoření exportu.

Může to trvat přibližně hodinu, než se data objeví v úložišti.

Po dokončení prvního exportu najdete v kontejneru úložiště objektů BLOB v Azure strukturu podobnou následující: (Tato operace se bude lišit v závislosti na shromažďovaných datech.)

|Název | Popis |
|:----|:------|
| [Dostupnost](export-data-model.md#availability) | Oznamuje [webové testy dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md).  |
| [Událostí](export-data-model.md#events) | Vlastní události generované [TrackEvent ()](../../azure-monitor/app/api-custom-events-metrics.md#trackevent). 
| [Výjimky](export-data-model.md#exceptions) |Oznamuje [výjimky](../../azure-monitor/app/asp-net-exceptions.md) na serveru a v prohlížeči.
| [Zprávy](export-data-model.md#trace-messages) | Odesílá [TrackTrace](../../azure-monitor/app/api-custom-events-metrics.md#tracktrace)a [adaptéry protokolování](../../azure-monitor/app/asp-net-trace-logs.md).
| [Metriky](export-data-model.md#metrics) | Vygenerováno voláními rozhraní API metriky.
| [Čítače výkonu](export-data-model.md) | Čítače výkonu shromážděné nástrojem Application Insights.
| [Žádosti](export-data-model.md#requests)| Odesílá [TrackRequest](../../azure-monitor/app/api-custom-events-metrics.md#trackrequest). Standardní moduly tuto metodu používají k hlášení doby odezvy serveru měřenou na serveru.| 

### <a name="to-edit-continuous-export"></a>Úprava průběžného exportu

Klikněte na průběžný export a vyberte účet úložiště, který chcete upravit.

### <a name="to-stop-continuous-export"></a>Zastavení průběžného exportu

Pokud chcete export zastavit, klikněte na zakázat. Když znovu kliknete na tlačítko Povolit, export se restartuje s novými daty. Data, která se dostanou na portálu, se nezobrazí, když je export zakázaný.

Pokud chcete export zastavit trvale, odstraňte ho. Tím nedojde k odstranění dat ze služby Storage.

### <a name="cant-add-or-change-an-export"></a>Nelze přidat nebo změnit export?
* K přidání nebo změně exportu potřebujete přístupová práva vlastníka, přispěvatele nebo Application Insights přispěvatele. [Přečtěte si o rolích][roles].

## <a name="what-events-do-you-get"></a><a name="analyze"></a>Jaké události získáte?
Exportovaná data jsou nezpracovaná telemetrie z vaší aplikace, s výjimkou toho, že přidáváme data o umístění, která počítáme z IP adresy klienta.

Data Zahozená [vzorkováním](../../azure-monitor/app/sampling.md) nejsou obsažena v exportovaných datech.

Jiné počítané metriky nejsou zahrnuty. Nebudete třeba exportovat průměrné využití procesoru, ale vyexportujeme nezpracované telemetrie, ze které se vypočítá průměr.

Data také obsahují výsledky všech [webových testů dostupnosti](../../azure-monitor/app/monitor-web-app-availability.md) , které jste nastavili.

> [!NOTE]
> **Kontrol.** Pokud vaše aplikace odesílá spoustu dat, může funkce vzorkování fungovat a odeslat jenom zlomek vygenerované telemetrie. [Další informace o vzorkování.](../../azure-monitor/app/sampling.md)
>
>

## <a name="inspect-the-data"></a><a name="get"></a>Kontrola dat
Úložiště si můžete prohlédnout přímo na portálu. V levém horním rohu klikněte na domů v horní části, kde se říká "služby Azure" vybrat **účty úložiště**, vyberte název účtu úložiště, na stránce Přehled vyberte **objekty blob** v části služby a nakonec vyberte název kontejneru.

Chcete-li zkontrolovat službu Azure Storage v aplikaci Visual Studio, otevřete **zobrazení**, **Průzkumník cloudu**. (Pokud nemáte příkaz nabídky, musíte nainstalovat sadu Azure SDK: otevřete dialogové okno **Nový projekt** , rozbalte položku Visual C#/Cloud a vyberte možnost **získat Microsoft Azure SDK pro .NET**.)

Po otevření úložiště objektů BLOB se zobrazí kontejner se sadou souborů objektů BLOB. Identifikátor URI jednotlivých souborů odvozený od názvu prostředku Application Insights, jeho klíče instrumentace, typu telemetrie/data a času. (Název prostředku je malými písmeny a klíč instrumentace vynechává pomlčky.)

![Kontrola úložiště objektů BLOB pomocí vhodného nástroje](./media/export-telemetry/04-data.png)

Datum a čas jsou UTC a jsou v době, kdy byla telemetrie uložena do úložiště – ne za čas, který byl vygenerován. Takže pokud napíšete kód pro stažení dat, může se pomocí dat pohybovat lineárně.

Tady je forma cesty:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Kde

* `blobCreationTimeUtc`je čas, kdy byl objekt BLOB vytvořen v interním přípravném úložišti.
* `blobDeliveryTimeUtc`je čas, kdy se objekt BLOB kopíruje do cílového úložiště pro export

## <a name="data-format"></a><a name="format"></a>Formát dat
* Každý objekt BLOB je textový soubor, který obsahuje více řádků oddělených znakem \n. Obsahuje telemetrii zpracovávanou za časové období zhruba půl minuty.
* Každý řádek představuje datový bod telemetrie, jako je například požadavek nebo zobrazení stránky.
* Každý řádek je neformátovaný dokument JSON. Pokud chcete na svém místě, otevřete ho v aplikaci Visual Studio a vyberte upravit, Upřesnit, formát souboru:

![Zobrazení telemetrie s vhodným nástrojem](./media/export-telemetry/06-json.png)

Doba trvání se nachází v taktech, kde 10 000 taktes = 1 ms. Například tyto hodnoty zobrazují čas 1 MS k odeslání požadavku z prohlížeče, 3 MS pro příjem a 1,8 s pro zpracování stránky v prohlížeči:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Podrobný odkaz na datový model pro typy a hodnoty vlastností.](export-data-model.md)

## <a name="processing-the-data"></a>Zpracování dat
V malém měřítku můžete napsat nějaký kód, který bude odčítat vaše data, číst je do tabulky a tak dále. Příklad:

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

Větší ukázku kódu najdete v tématu [použití role pracovního procesu][exportasa].

## <a name="delete-your-old-data"></a><a name="delete"></a>Odstranit stará data
Zodpovídáte za správu kapacity úložiště a odstranění starých dat v případě potřeby.

## <a name="if-you-regenerate-your-storage-key"></a>Pokud znovu vygenerujete klíč úložiště...
Pokud změníte klíč na své úložiště, průběžný export přestane fungovat. V účtu Azure se zobrazí oznámení.

Otevřete kartu průběžný export a upravte export. Upravte cíl exportu, ale stačí nechat vybrat stejné úložiště. Potvrďte kliknutím na tlačítko OK.

Průběžný export se restartuje.

## <a name="export-samples"></a>Exportovat ukázky

* [Export do SQL s použitím Stream Analytics][exportasa]
* [Ukázka 2 Stream Analytics](export-stream-analytics.md)

V případě větších škálování zvažte clustery [HDInsight](https://azure.microsoft.com/services/hdinsight/) -Hadoop v cloudu. HDInsight poskytuje různé technologie pro správu a analýzu velkých objemů dat a můžete ji použít ke zpracování dat exportovaných z Application Insights.

## <a name="q--a"></a>Otázky a odpovědi
* *Ale stačí jednorázové stažení grafu.*  

    Ano, můžete to udělat. V horní části karty klikněte na **exportovat data**.
* *Nastavil (a) export, ale ve Storu nejsou žádná data.*

    Dostala se Application Insights od vaší aplikace získat veškerou telemetrii od chvíle, kdy jste nastavili export? Dostanete jenom nová data.
* *Při pokusu o nastavení exportu byl odepřen přístup.*

    Pokud účet vlastní vaše organizace, musíte být členem skupin vlastníci nebo přispěvatelé.
* *Můžu exportovat přímo do vlastního místního úložiště?*

    Ne, je nám líto. Náš Exportní modul v tuto chvíli funguje jenom s Azure Storage.  
* *Existuje nějaké omezení množství dat, které jste umístili do Storu?*

    Ne. Data budeme uchovávat, dokud neodstraníte export. Zastavíme se, pokud máme vnější omezení pro úložiště objektů blob, ale to je poměrně velké. Můžete určit, kolik úložiště používáte.  
* *Kolik objektů BLOB se má v úložišti zobrazit?*

  * Pro každý datový typ, který jste vybrali k exportu, se vytvoří nový objekt BLOB každou minutu (pokud jsou k dispozici data).
  * Pro aplikace s vysokým provozem jsou navíc přiděleny další jednotky oddílu. V takovém případě každá jednotka vytvoří objekt BLOB každou minutu.
* *Znovu vygeneroval (a) klíč do úložiště nebo změnil název kontejneru a teď export nefunguje.*

    Upravte export a otevřete kartu exportovat cíl. ponechte stejné úložiště jako předtím a kliknutím na OK potvrďte. Export se restartuje. Pokud byla změna během posledních několika dní, ztratíte data.
* *Můžu pozastavit export?*

    Ano. Klikněte na Zakázat.

## <a name="code-samples"></a>Ukázky kódů

* [Ukázka Stream Analytics](export-stream-analytics.md)
* [Export do SQL s použitím Stream Analytics][exportasa]
* [Podrobný odkaz na datový model pro typy a hodnoty vlastností.](export-data-model.md)

<!--Link references-->

[exportasa]: ../../azure-monitor/app/code-sample-export-sql-stream-analytics.md
[roles]: ../../azure-monitor/app/resources-roles-access-control.md
