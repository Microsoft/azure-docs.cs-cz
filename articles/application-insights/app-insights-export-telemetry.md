---
title: Průběžný export telemetrie z Application Insights | Dokumentace Microsoftu
description: Exportovat data o využití a diagnostiku do úložiště v Microsoft Azure a stáhněte ho odtud.
services: application-insights
documentationcenter: ''
author: mrbullwinkle
manager: carmonm
ms.assetid: 5b859200-b484-4c98-9d9f-929713f1030c
ms.service: application-insights
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: mbullwin
ms.openlocfilehash: f19da07fb8299d2b11410309b76a1d7900d1cb93
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/01/2018
ms.locfileid: "52727436"
---
# <a name="export-telemetry-from-application-insights"></a>Export telemetrie z Application Insights
Chceme zajistit telemetrických dat po dobu delší než doba uchování standard? Nebo zpracovat nějakým způsobem specializované? Průběžný Export je ideální pro to. Události, které se zobrazí na portálu Application Insights je možné exportovat do úložiště v Microsoft Azure ve formátu JSON. Tam si můžete stáhnout data a zápis, cokoli, co kód potřebují ke zpracování.  

Před nastavením průběžný export je některé alternativy, které můžete chtít zvážit:

* Tlačítko pro Export v horní části okna metrik nebo hledání vám umožní přesunout tabulky a grafy do Excelové tabulky.

* [Analytics](app-insights-analytics.md) poskytuje výkonný dotazovací jazyk pro telemetrii. Můžete také exportovat výsledky.
* Pokud ale chcete [zkoumání dat v Power BI](app-insights-export-power-bi.md), můžete to udělat bez použití průběžného exportu.
* [Rozhraní REST API pro přístup k datům](https://dev.applicationinsights.io/) umožňuje programový přístup k vaší telemetrie.
* Také přístup k nastavení [průběžný export prostřednictvím prostředí Powershell](https://docs.microsoft.com/powershell/module/azurerm.applicationinsights/new-azurermapplicationinsightscontinuousexport?view=azurermps-5.7.0).

Poté, co průběžný Export zkopíruje data do služby storage (ve kterém může zůstat pro dlouho budete chtít), je stále k dispozici ve službě Application Insights pro obvyklého [dobu uchování](app-insights-data-retention-privacy.md).

## <a name="setup"></a> Vytvoření průběžný Export
1. V prostředku Application Insights pro vaši aplikaci, otevřete průběžný Export a zvolte **přidat**:

2. Vyberte datové typy, které chcete exportovat telemetrii.

3. Vytvořte nebo vyberte [účtu služby Azure storage](../storage/common/storage-introduction.md) kam chcete uložit data. Další informace o cenových možnostech úložiště najdete [oficiální stránce s cenami](https://azure.microsoft.com/pricing/details/storage/).

    > [!Warning]
    > Ve výchozím umístění úložiště bude nastaveno na stejné zeměpisné oblasti jako váš prostředek Application Insights. Pokud uchováváte v jiné oblasti, může se účtují poplatky za přenos.

    ![Klikněte na tlačítko Přidat, exportovat cílový účet úložiště a vytvořit nové úložiště nebo zvolte existující úložiště](./media/app-insights-export-telemetry/02-add.png)

4. Vytvořte nebo vyberte kontejner ve službě storage:

    ![Klikněte na tlačítko Vybrat typy událostí](./media/app-insights-export-telemetry/create-container.png)

Po vytvoření export, začne přechod. Získáte jenom data, která dorazí po vytvoření exportu.

Může existovat zpoždění zhruba do hodiny, než se data zobrazí ve službě storage.

### <a name="to-edit-continuous-export"></a>Chcete-li upravit průběžný export.

Pokud chcete později změnit typy událostí, stačí upravte export:

![Klikněte na tlačítko Vybrat typy událostí](./media/app-insights-export-telemetry/05-edit.png)

### <a name="to-stop-continuous-export"></a>Chcete-li zastavit průběžný export.

Export, klikněte na tlačítko zakázat. Když kliknete znovu povolit, exportu se restartuje s novými daty. Nebudete získáte data, která přišla v portálu pro export bylo zakázáno.

Export zastavit trvale, odstraňte ho. Tím nedojde k odstranění dat z úložiště.

### <a name="cant-add-or-change-an-export"></a>Nelze přidat nebo změnit export?
* Chcete-li přidat nebo změnit exporty, je nutné vlastníkem, přispěvatelem nebo přispěvatele Application Insights přístupová práva. [Další informace o rolích][roles].

## <a name="analyze"></a> Jaké události získáte?
Exportovaná data se nezpracovaná telemetrická data, které z vašich aplikací, s tím rozdílem, že přidáme data o poloze, které můžeme vypočítat z IP adresy klienta.

Data, která má byla zahozena [vzorkování](app-insights-sampling.md) není součástí exportovaná data.

Počítané ani jiné metriky nejsou zahrnuty. Například můžeme není exportovat průměrné využití procesoru, ale můžeme exportovat nezpracovaná telemetrická data, ze kterého je průměr vypočítaný.

Data také obsahuje výsledky libovolného [testy dostupnosti webu](app-insights-monitor-web-app-availability.md) , který jste nastavili.

> [!NOTE]
> **Vzorkování.** Pokud vaše aplikace odešle velké množství dat, může funkce vzorkování pracovat a odesílat pouze zlomek generované telemetrická data. [Přečtěte si další informace o vzorkování.](app-insights-sampling.md)
>
>

## <a name="get"></a> Kontrolovat data
Můžete si prohlédnout úložiště přímo na portálu. Klikněte na tlačítko **Procházet**, vyberte svůj účet úložiště a pak otevřete **kontejnery**.

Chcete-li prověřit úložiště Azure v sadě Visual Studio, otevřete **zobrazení**, **Průzkumníka cloudu**. (Pokud nemáte k dispozici tento příkaz nabídky, je potřeba nainstalovat sadu Azure SDK: Otevřít **nový projekt** dialogového okna, rozbalte Visual C# / cloudu a zvolte **získat Microsoft Azure SDK for .NET**.)

Při otevření úložiště objektů blob uvidíte kontejner pomocí sady souborů objektů blob. Identifikátor URI každého souboru je odvozen z název prostředku Application Insights, jeho klíč instrumentace, telemetrie – typ/data/času. (Název prostředku je jenom malá písmena a pomlčky vynechá Instrumentační klíč.)

![Zkontrolujte úložiště objektů blob s vhodnou nástrojem](./media/app-insights-export-telemetry/04-data.png)

Datum a čas jsou UTC a když telemetrická data se ukládají v úložišti – bez času, který byl vygenerován. Proto pokud píšete kód k stáhnout data, ho můžete procházet lineárně data.

Tady je forma cesta:

    $"{applicationName}_{instrumentationKey}/{type}/{blobDeliveryTimeUtc:yyyy-MM-dd}/{ blobDeliveryTimeUtc:HH}/{blobId}_{blobCreationTimeUtc:yyyyMMdd_HHmmss}.blob"

Kde

* `blobCreationTimeUtc` je čas vytvoření objektu blob do interní pracovní úložiště
* `blobDeliveryTimeUtc` je čas při zkopíruje objekt blob do cílového úložiště exportu

## <a name="format"></a> Formát dat
* Každý objekt blob je textový soubor, který obsahuje více "\n'-separated řádků. Obsahuje telemetrii zpracovaných za časové období přibližně polovina minuty.
* Každý řádek představuje datový bod telemetrie jako je například žádosti nebo zobrazení stránky.
* Každý řádek je neformátovaný dokumentu JSON. Pokud chcete nacházejí a stare na to, otevřete ho v sadě Visual Studio a zvolte možnost upravte, Upřesnit, soubor ve formátu:

![Zobrazit telemetrická data pomocí vhodného nástroje](./media/app-insights-export-telemetry/06-json.png)

Dob trvání jsou uvedenou v impulzech, kde 10 000 značek = 1 ms. Například tyto hodnoty zobrazit čas 1 ms odeslat požadavek z prohlížeče, 3 ms pro příjem a 1.8 s ke zpracování stránky v prohlížeči:

    "sendRequest": {"value": 10000.0},
    "receiveRequest": {"value": 30000.0},
    "clientProcess": {"value": 17970000.0}

[Podrobný datový model referenční informace pro typy vlastností a hodnot.](app-insights-export-data-model.md)

## <a name="processing-the-data"></a>Zpracování dat
V malém měřítku můžete napsání kódu pro vaše data o přijetí změn od sebe, čtení do tabulky a tak dále. Příklad:

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

Větší ukázky kódu, naleznete v tématu [pomocí role pracovního procesu][exportasa].

## <a name="delete"></a>Odstranit stará data
Všimněte si, že jste zodpovědní za správu vaší kapacity úložiště a odstraňování starých dat v případě potřeby.

## <a name="if-you-regenerate-your-storage-key"></a>Pokud znovu vygenerovat klíče účtu úložiště...
Pokud změníte klíč do úložiště, průběžný export přestanou fungovat. Zobrazí se vám oznámení v účtu Azure.

Otevře se okno průběžný Export a upravit export. Upravit cíl exportu, ale ponechte vybrané stejného úložiště. Klikněte na tlačítko OK pro potvrzení.

![Upravit průběžný export a otevření a zavření thí cíl exportu.](./media/app-insights-export-telemetry/07-resetstore.png)

Průběžný export se restartuje.

## <a name="export-samples"></a>Export ukázky

* [Export do SQL s použitím Stream Analytics][exportasa]
* [Stream Analytics příklad 2](app-insights-export-stream-analytics.md)

Na velkých měřítek, vezměte v úvahu [HDInsight](https://azure.microsoft.com/services/hdinsight/) -clusterů systému Hadoop v cloudu. HDInsight poskytuje širokou škálu technologií pro správu a analýzu velkých objemů dat, a můžete ji třeba použít ke zpracování dat, který byl exportován z Application Insights.

## <a name="q--a"></a>Dotazy a odpovědi
* *Ale všechno, co chci, aby se jednorázové stažení grafu.*  

    Ano, můžete to udělat. V horní části okna klikněte na tlačítko **exportovat Data**.
* *Nastavit export, ale neexistuje žádná data v úložišti.*

    Application Insights obdrželi žádnou telemetrii z vaší aplikace od nastavení exportu? Jenom se zobrazí nová data.
* *Jsem se pokusil nastavit export, ale byl odepřen přístup*

    Pokud vaše organizace vlastní účet, musíte být členem skupiny vlastníky nebo přispěvateli.
* *Můžu exportovat přímo na vlastní v místním úložišti?*

    Ne, je nám líto. Náš modul pro export momentálně funguje jenom s Azure storage v tuto chvíli.  
* *Je nějak omezený na množství dat, které v mém úložišti?*

    Ne. Jsme budete udržovat doručením (push) dat dokud je neodstraníte exportu. Pokud se nedosáhne vnější limity pro úložiště objektů blob, ale to je poměrně velký zastavíme. To je vám k řízení jak velké úložiště, můžete použít.  
* *Jak velký počet objektů BLOB zobrazit ve službě storage?*

  * Pro každý datový typ, který jste vybrali pro export nový objekt blob se vytvoří každou minutu (pokud jsou k dispozici data).
  * Pro aplikace s intenzivním provozem, navíc další oddíl jednotky jsou přiděleny. V tomto případě každá jednotka vytvoří objekt blob každou minutu.
* *Můžu znovu vygenerovat klíč do úložiště nebo změnit název kontejneru a nyní exportu nefunguje.*

    Export upravit a otevře se okno cíl exportu. Ponechat stejné úložiště jako předtím vybrali a klikněte na tlačítko OK pro potvrzení. Export se restartuje. Pokud se tato změna byla během posledních několik dní, nebude přijít o data.
* *Můžete pozastavit exportu?*

    Ano. Klikněte na zakázat.

## <a name="code-samples"></a>Ukázky kódů

* [Ukázka Stream Analytics](app-insights-export-stream-analytics.md)
* [Export do SQL s použitím Stream Analytics][exportasa]
* [Podrobný datový model referenční informace pro typy vlastností a hodnot.](app-insights-export-data-model.md)

<!--Link references-->

[exportasa]: app-insights-code-sample-export-sql-stream-analytics.md
[roles]: app-insights-resources-roles-access-control.md
