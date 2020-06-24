---
title: Řešení potíží s operacemi s daty pomocí diagnostiky a analyzátoru zpráv
titleSuffix: Azure Storage
description: Kurz demonstrující ucelené řešení potíží s Analýza úložiště Azure, AzCopy a Microsoft Message Analyzer
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: normesta
ms.reviewer: ozgun
ms.subservice: common
ms.custom: monitoring
ms.openlocfilehash: 4b0145514a884c43ef18518cf25a2a78b1fc3aa3
ms.sourcegitcommit: ad66392df535c370ba22d36a71e1bbc8b0eedbe3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/16/2020
ms.locfileid: "84809053"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Kompletní řešení potíží pomocí metrik a protokolování služby Azure Storage, nástrojů AzCopy a Message Analyzer

[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnostikování a řešení potíží je klíčovou dovedností pro sestavování a podporu klientských aplikací pomocí Microsoft Azure Storage. Z důvodu distribuované povahy aplikace Azure může být Diagnostika a řešení potíží s chybami a problémy s výkonem složitější než v tradičních prostředích.

V tomto kurzu ukážeme, jak identifikovat určité chyby, které mohou mít vliv na výkon, a vyřešit tyto chyby z kompletního na konci pomocí nástrojů poskytovaných Microsoftem a Azure Storage, aby bylo možné optimalizovat klientskou aplikaci.

Tento kurz nabízí praktický průzkum komplexního scénáře řešení potíží. Podrobný koncepční Průvodce odstraňováním potíží s aplikacemi Azure Storage najdete v tématu [monitorování, diagnostika a řešení potíží s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Nástroje pro řešení potíží s Azure Storage aplikacemi

Pokud chcete řešit potíže s klientskými aplikacemi pomocí Microsoft Azure Storage, můžete použít kombinaci nástrojů k určení, kdy k problému došlo a jakou příčinu problému může být. Mezi tyto nástroje patří:

* **Analýza úložiště Azure**. [Analýza úložiště Azure](/rest/api/storageservices/Storage-Analytics) poskytuje metriky a protokolování pro Azure Storage.

  * **Metrika úložiště** sleduje metriky transakcí a metriky kapacity pro váš účet úložiště. Pomocí metrik můžete určit, jak vaše aplikace funguje, podle řady různých opatření. Další informace o typech metrik sledovaných pomocí Analýza úložiště najdete v tématu [schéma tabulky metriky analýza úložiště](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) .
  * **Protokolování úložiště** protokoluje každý požadavek na Azure Storage služby do protokolu na straně serveru. Protokol sleduje podrobná data pro každý požadavek, včetně provedené operace, stavu operace a informací o latenci. Další informace o datech žádosti a odpovědi, která se zapisují do protokolů pomocí Analýza úložiště, najdete v tématu [Formát protokolu analýza úložiště](/rest/api/storageservices/Storage-Analytics-Log-Format) .

* **Azure Portal**. V [Azure Portal](https://portal.azure.com)můžete nakonfigurovat metriky a protokolování pro váš účet úložiště. Můžete také zobrazit grafy a grafy, které ukazují, jak probíhá vaše aplikace v průběhu času, a nakonfigurovat výstrahy, které vás upozorní, pokud vaše aplikace funguje jinak, než se očekávalo pro zadanou metriku.

    Informace o konfiguraci monitorování v Azure Portal najdete v tématu [monitorování účtu úložiště v Azure Portal](storage-monitor-storage-account.md) .
* **AzCopy**. Protokoly serveru pro Azure Storage se ukládají jako objekty blob, takže můžete pomocí AzCopy zkopírovat objekty blob protokolů do místního adresáře pro účely analýzy pomocí nástroje Microsoft Message Analyzer. Další informace o AzCopy najdete v tématu [přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md) .
* **Microsoft Message Analyzer**. Analyzátor zpráv je nástroj, který využívá soubory protokolu a zobrazuje data protokolu ve vizuálním formátu, který usnadňuje filtrování, vyhledávání a seskupení dat protokolů do užitečných sad, které můžete použít k analýze chyb a problémů s výkonem. Další informace o analyzátoru zpráv najdete v tématu [provozní příručka nástroje Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx) .

## <a name="about-the-sample-scenario"></a>O ukázkovém scénáři

V tomto kurzu probereme scénář, ve kterém Azure Storage metrika indikuje nízkou procentuální míru úspěšnosti pro aplikaci, která volá Azure Storage. Metrika nízké míry úspěšnosti (zobrazená jako **PercentSuccess** v tabulkách [Azure Portal](https://portal.azure.com) a v tabulkách metrik) sleduje operace, které jsou úspěšné, ale vrací stavový kód HTTP, který je větší než 299. V souborech protokolu úložiště na straně serveru se tyto operace zaznamenávají se stavem transakce **ClientOtherErrors**. Další podrobnosti o metrikě s nízkým objemem procento úspěšnosti najdete v tématu [metriky zobrazit nízké PercentSuccess nebo položky protokolu analýzy, které mají operace s transakčním stavem ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Operace Azure Storage můžou vracet stavové kódy HTTP větší než 299 jako součást jejich normální funkce. Tyto chyby v některých případech ale označují, že možná budete moct optimalizovat vaši klientskou aplikaci, aby se zlepšil výkon.

V tomto scénáři budeme uvažovat o úspěšnosti s nízkou procentuální sazbou za 100%. Podle svých potřeb ale můžete zvolit jinou úroveň metriky. Doporučujeme, abyste při testování vaší aplikace navázali základní toleranci pro klíčové metriky výkonu. Můžete například určit, na základě testování, že by měla vaše aplikace mít konzistentní procento úspěšnosti 90% nebo 85%. Pokud se v datech metrik zobrazuje, že se aplikace odchyluje od tohoto čísla, můžete prozkoumat, co může být příčinou zvýšení.

Pro náš vzorový scénář, jakmile jsme zjistili, že metrika procento úspěšnosti je nižší než 100%, prověříme protokoly a zjistíme chyby, které jsou v relaci metriky, a použijete je k zjištění, co způsobuje nižší procento úspěšnosti. Podíváme se konkrétně na chyby v rozsahu 400. Pak budeme podrobněji prozkoumat chyby 404 (Nenalezeno).

### <a name="some-causes-of-400-range-errors"></a>Některé příčiny 400 chyb v rozsahu

V níže uvedených příkladech vidíte vzorkování některých chyb v rozsahu 400 pro požadavky na Azure Blob Storage a jejich možné příčiny. Některé z těchto chyb a také chyby v rozsahu 300 a 500 mohou přispět k úspěšnosti s nízkou procentní hodnotou.

Všimněte si, že níže uvedené seznamy jsou mnohem kompletní. Podrobnosti o obecných chybách Azure Storage a o chybách specifických pro jednotlivé služby úložiště najdete v tématu [stavové a chybové kódy](https://msdn.microsoft.com/library/azure/dd179382.aspx) na webu MSDN.

#### <a name="status-code-404-not-found-examples"></a>Příklady stavového kódu 404 (Nenalezeno)

Vyvolá se v případě, že operace čtení proti kontejneru nebo objektu BLOB se nezdařila, protože nebyl nalezen objekt BLOB nebo kontejner.

* Vyvolá se v případě, že před touto žádostí byl jiný klient odstraněn kontejner nebo objekt BLOB.
* Vyvolá se v případě, že používáte volání rozhraní API, které vytváří kontejner nebo objekt BLOB po kontrole, zda existuje. Rozhraní CreateIfNotExists API nejprve vyvolají hlavní volání, aby zkontrolovala existenci kontejneru nebo objektu BLOB; Pokud neexistuje, vrátí se chyba 404 a potom se provede druhé volání PUT pro zápis kontejneru nebo objektu BLOB.

#### <a name="status-code-409-conflict-examples"></a>Příklady stavového kódu 409 (konflikt)

* Nastane, pokud použijete rozhraní API pro vytvoření nového kontejneru nebo objektu blob, aniž byste nejdřív kontrolovali existenci a kontejner nebo objekt BLOB s tímto názvem už existuje.
* Vyvolá se v případě, že dojde k odstranění kontejneru a pokusíte se vytvořit nový kontejner se stejným názvem před dokončením operace odstranění.
* Vyvolá se v případě, že zadáte zapůjčení pro kontejner nebo objekt BLOB a již je k dispozici zapůjčení.

#### <a name="status-code-412-precondition-failed-examples"></a>Příklady stavového kódu 412 (neúspěšných předběžných podmínek)

* Vyvolá se v případě, že nebyla splněna podmínka určená podmíněnou hlavičkou.
* Vyvolá se v případě, že zadané ID zapůjčení neodpovídá ID zapůjčení na kontejneru nebo objektu BLOB.

## <a name="generate-log-files-for-analysis"></a>Generovat soubory protokolu pro analýzu

V tomto kurzu použijeme analyzátor zpráv pro práci se třemi různými typy souborů protokolu, i když se můžete rozhodnout, že budete pracovat s některou z těchto akcí:

* **Protokol serveru**, který se vytvoří, když povolíte protokolování Azure Storage. Protokol serveru obsahuje data o každé operaci volané pro jednu z Azure Storage Services – blob, Queue, Table a File. Protokol serveru indikuje, která operace byla volána a jaký kód stavu byl vrácen, a další podrobnosti o žádosti a odpovědi.
* **Protokol klienta .NET**, který se vytvoří, když povolíte protokolování na straně klienta v rámci aplikace .NET. Protokol klienta obsahuje podrobné informace o tom, jak klient připraví požadavek a přijímá a zpracovává odpověď.
* **Protokol trasování sítě http**, který shromažďuje data požadavků HTTP/HTTPS a data odpovědi, včetně operací pro operace s Azure Storage. V tomto kurzu vygenerujeme trasování sítě pomocí analyzátoru zpráv.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurace protokolování a metrik na straně serveru

Nejdřív bude nutné nakonfigurovat Azure Storage protokolování a metriky, aby bylo možné analyzovat data ze strany služby. Protokolování a metriky můžete nakonfigurovat různými způsoby – prostřednictvím [Azure Portal](https://portal.azure.com), pomocí PowerShellu nebo prostřednictvím kódu programu. Podrobnosti o konfiguraci protokolování a metrik najdete v tématu [Povolení metrik](storage-analytics-metrics.md#enable-metrics-by-using-the-azure-portal) a [Povolení protokolování](storage-analytics-logging.md#enable-storage-logging) .

### <a name="configure-net-client-side-logging"></a>Konfigurace protokolování na straně klienta .NET

Chcete-li konfigurovat protokolování na straně klienta pro aplikaci .NET, povolte diagnostiku rozhraní .NET v konfiguračním souboru aplikace (web.config nebo app.config). Podrobnosti najdete v tématu [protokolování na straně klienta pomocí klientské knihovny pro úložiště .NET](https://msdn.microsoft.com/library/azure/dn782839.aspx) a [protokolování na straně klienta s Microsoft Azure Storage SDK pro jazyk Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) na webu MSDN.

Protokol na straně klienta obsahuje podrobné informace o tom, jak klient připraví požadavek a přijímá a zpracovává odpověď.

Klientská knihovna pro úložiště ukládá data protokolu na straně klienta v umístění zadaném v konfiguračním souboru aplikace (web.config nebo app.config).

### <a name="collect-a-network-trace"></a>Shromáždění trasování sítě

Pomocí analyzátoru zpráv můžete shromažďovat trasování sítě HTTP/HTTPS, když je spuštěná klientská aplikace. Analyzátor zpráv používá [Fiddler](https://www.telerik.com/fiddler) na back-endu. Než shromáždíte síťové trasování, doporučujeme nakonfigurovat Fiddler pro záznam nešifrovaného provozu HTTPS:

1. Nainstalujte [Fiddler](https://www.telerik.com/download/fiddler).
2. Spusťte Fiddler.
3. Vybrat **nástroje | Fiddler možnosti**.
4. V dialogovém okně Možnosti zajistěte, aby bylo zajištěno, že **zachycení protokolu HTTPS připojení** a **dešifrování protokolu HTTPS** jsou vybrány, jak je uvedeno níže.

![Konfigurace možností Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

V tomto kurzu nejprve Shromážděte a neukládejte trasování sítě v analyzátoru zpráv a pak vytvořte relaci analýzy pro analýzu trasování a protokolů. Postup shromáždění trasování sítě v analyzátoru zpráv:

1. V nástroji analyzátor zpráv vyberte **soubor | Rychlá trasování | Nešifrovaný protokol HTTPS**.
2. Trasování se spustí hned. Výběrem **stop** zastavte trasování, aby bylo možné ho nakonfigurovat pouze pro trasování provozu úložiště.
3. Vyberte **Upravit** a upravte relaci trasování.
4. Vyberte odkaz **Konfigurovat** napravo od poskytovatele **Microsoft-PEF-WebProxy** ETW.
5. V dialogovém okně **Upřesnit nastavení** klikněte na kartu **poskytovatel** .
6. V poli **filtr názvu hostitele** zadejte koncové body úložiště oddělené mezerami. Koncovým bodům můžete například určit následujícím způsobem: Změňte `storagesample` název svého účtu úložiště:

    `storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net`

7. Zavřete dialogové okno a kliknutím na tlačítko **restartovat** zahajte shromažďování trasování se stejným filtrem názvů hostitelů, aby v trasování bylo zahrnuto pouze Azure Storage síťový provoz.

> [!NOTE]
> Až dokončíte shromažďování trasování sítě, důrazně doporučujeme, abyste vrátili zpět nastavení, která jste změnili v Fiddler k dešifrování provozu HTTPS. V dialogovém okně Možnosti Fiddler zrušte zaškrtnutí políček **zachytávání https připojení** a **dešifrování přenosu HTTPS** .

Další podrobnosti najdete v tématu [použití funkcí trasování sítě](https://technet.microsoft.com/library/jj674819.aspx) na webu TechNet.

## <a name="review-metrics-data-in-the-azure-portal"></a>Kontrola dat metrik v Azure Portal

Po spuštění vaší aplikace po určitou dobu můžete zkontrolovat grafy metrik, které se zobrazují v [Azure Portal](https://portal.azure.com) , a sledovat, jak vaše služba funguje.

Nejdřív v Azure Portal přejděte na svůj účet úložiště. Ve výchozím nastavení se v okně účtu zobrazí graf monitorování s metrikou **Procento úspěšnosti** . Pokud jste graf předtím změnili tak, aby zobrazoval různé metriky, přidejte metriku **Procento úspěšnosti** .

V grafu monitorování teď uvidíte **Procento úspěšnosti** spolu s dalšími metrikami, které jste mohli přidat. Ve scénáři, který prozkoumáme dále analýzou protokolů v analyzátoru zpráv, je procento úspěšnosti poněkud pod 100%.

Další podrobnosti o přidávání a přizpůsobení grafů metrik najdete v tématu [přizpůsobení grafů metrik](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Může trvat nějakou dobu, než se data metriky objeví v Azure Portal po povolení metrik úložiště. Důvodem je to, že hodinová metrika pro předchozí hodinu se v Azure Portal nezobrazí, dokud neuplyne aktuální hodina. V Azure Portal se v tuto chvíli nezobrazuje také minutové metriky. V závislosti na tom, kdy povolíte metriky, může trvat až dvě hodiny, než se zobrazí data metriky.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Kopírování protokolů serveru do místního adresáře pomocí AzCopy

Azure Storage zapisuje data protokolu serveru do objektů blob, zatímco metriky se zapisují do tabulek. Objekty blob protokolu jsou k dispozici ve známém `$logs` kontejneru pro váš účet úložiště. Objekty blob protokolů se pojmenují hierarchicky po rocích, měsíc, den a hodinu, takže můžete snadno najít rozsah času, který chcete prozkoumat. Například v `storagesample` účtu je kontejner pro objekty blob protokolu pro 01/02/2015 od 8-9 dop `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800` . Jednotlivé objekty BLOB v tomto kontejneru jsou pojmenovány sekvenčně, počínaje `000000.log` .

Pomocí nástroje příkazového řádku AzCopy můžete stáhnout tyto soubory protokolu na straně serveru do libovolného umístění v místním počítači. Pomocí následujícího příkazu můžete například stáhnout soubory protokolu pro operace objektů blob, které se konaly do 2. ledna 2015 do složky `C:\Temp\Logs\Server` . nahraďte `<storageaccountname>` názvem svého účtu úložiště:

```azcopy
azcopy copy 'http://<storageaccountname>.blob.core.windows.net/$logs/blob/2015/01/02' 'C:\Temp\Logs\Server'  --recursive
```

AzCopy je k dispozici ke stažení na stránce [soubory ke stažení pro Azure](https://azure.microsoft.com/downloads/) . Podrobnosti o použití AzCopy najdete v tématu [přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md).

Další informace o stahování protokolů na straně serveru najdete v tématu [stažení dat protokolu protokolování úložiště](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Analýza dat protokolu pomocí nástroje Microsoft Message Analyzer

Microsoft Message Analyzer je nástroj pro zachycení, zobrazení a analýzu přenosů zpráv protokolu, událostí a dalších zpráv o systému nebo aplikacích v rámci řešení potíží a diagnostických scénářů. Analyzátor zpráv také umožňuje načíst, agregovat a analyzovat data z protokolů a uložených trasovacích souborů. Další informace o analyzátoru zpráv najdete v tématu [provozní příručka nástroje Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx).

Analyzátor zpráv obsahuje prostředky pro Azure Storage, které vám pomůžou analyzovat protokoly serveru, klienta a sítě. V této části se podíváme, jak tyto nástroje používat k vyřešení problému s nízkým procentom úspěšnosti v protokolech úložiště.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Stažení a instalace analyzátoru zpráv a Azure Storage prostředků

1. Stáhněte si [analyzátor zpráv](https://docs.microsoft.com/message-analyzer/installing-and-upgrading-message-analyzer).
2. Spusťte analyzátor zpráv.
3. V nabídce **nástroje** vyberte **Správce prostředků**. V dialogovém okně **Správce prostředků** vyberte **položky ke stažení**a pak filtrujte **Azure Storage**. Zobrazí se Azure Storage prostředky, jak je znázorněno na obrázku níže.
4. Kliknutím na **synchronizovat všechny zobrazené položky** nainstalujete Azure Storage assety. Dostupné prostředky zahrnují:
   * **Azure Storageová pravidla barev:** Pravidla barev Azure Storage umožňují definovat speciální filtry, které používají barvy, text a styly písma k zdůraznění zpráv, které obsahují konkrétní informace v trasování.
   * **Azure Storage grafy:** Grafy Azure Storage jsou předdefinované grafy, které data protokolů serveru graphu. Všimněte si, že pro použití Azure Storagech grafů v tuto chvíli můžete načíst jenom protokol serveru do mřížky analýzy.
   * **Analyzátory Azure Storage:** Analyzátory Azure Storage analyzují protokoly Azure Storage klienta, serveru a protokolu HTTP, aby je bylo možné zobrazit v mřížce analýzy.
   * **Filtry Azure Storage:** Filtry Azure Storage jsou Předdefinovaná kritéria, která můžete použít k dotazování na data v mřížce analýzy.
   * **Azure Storage rozložení zobrazení:** Rozložení zobrazení Azure Storage jsou předdefinovaná rozložení sloupců a seskupení v mřížce analýzy.
5. Po instalaci assetů restartujte analyzátor zpráv.

![Správce assetů analyzátoru zpráv](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Pro účely tohoto kurzu nainstalujte všechny zobrazené prostředky Azure Storage.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Import souborů protokolu do analyzátoru zpráv

Všechny uložené soubory protokolů (na straně serveru, klienta a síť) můžete importovat do jedné relace v nástroji Microsoft Message Analyzer pro účely analýzy.

1. V nabídce **soubor** v nástroji Microsoft Message Analyzer klikněte na **Nová relace**a pak klikněte na **prázdná relace**. V dialogovém okně **Nová relace** zadejte název pro vaši relaci analýzy. Na panelu **Podrobnosti relace** klikněte na tlačítko **soubory** .
2. Chcete-li načíst data trasování sítě vygenerovaná analyzátorem zpráv, klikněte na tlačítko **Přidat soubory**, vyhledejte umístění, kam jste uložili soubor. matp z relace webového trasování, vyberte soubor. matp a klikněte na tlačítko **otevřít**.
3. Pokud chcete načíst data protokolu na straně serveru, klikněte na **Přidat soubory**, vyhledejte umístění, kam jste stáhli protokoly na straně serveru, vyberte soubory protokolu pro časový rozsah, který chcete analyzovat, a klikněte na **otevřít**. Pak na panelu **Podrobnosti relace** nastavte rozevírací seznam **Konfigurace textového protokolu** pro každý soubor protokolu na straně serveru na **AzureStorageLog** , aby se zajistilo, že nástroj Microsoft Message Analyzer může analyzovat soubor protokolu správně.
4. Pokud chcete načíst data protokolu na straně klienta, klikněte na **Přidat soubory**, vyhledejte umístění, kam jste uložili protokoly na straně klienta, vyberte soubory protokolů, které chcete analyzovat, a klikněte na **otevřít**. Pak na panelu **podrobností relace** nastavte rozevírací seznam **Konfigurace textového protokolu** pro každý soubor protokolu na straně klienta na **AzureStorageClientDotNetV4** , aby nástroj Microsoft Message Analyzer mohl správně analyzovat soubor protokolu.
5. V dialogovém okně **Nová relace** klikněte na **Spustit** a načtěte a analyzujte data protokolu. Data protokolu se zobrazí v mřížce analýza analyzátoru zpráv.

Následující obrázek ukazuje příklad relace nakonfigurované se soubory protokolu serveru, klienta a trasování sítě.

![Konfigurovat relaci analyzátoru zpráv](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Všimněte si, že analyzátor zpráv načítá soubory protokolu do paměti. Pokud máte rozsáhlou sadu dat protokolu, budete ji chtít filtrovat, aby se dosáhlo co nejlepšího výkonu z analyzátoru zpráv.

Nejprve určete časový rámec, který vás zajímá, a Udržujte tento časový rámec co nejmenším možným způsobem. V mnoha případech budete chtít zkontrolovat dobu v minutách nebo hodinách. Importujte nejmenší sadu protokolů, které mohou vyhovovat vašim potřebám.

Pokud stále máte velké množství dat protokolu, můžete před jeho načtením zadat filtr relace, který bude filtrovat data protokolu. V poli **Filtr relace** vyberte tlačítko **Knihovna** a vyberte předdefinovaný filtr. Můžete například zvolit **globální časový filtr** z filtrů Azure Storage k filtrování v časovém intervalu. Pak můžete upravit kritéria filtru a zadat počáteční a koncové časové razítko pro interval, který chcete zobrazit. Můžete také filtrovat podle konkrétního stavového kódu; Můžete například zvolit načtení pouze položek protokolu, kde je stavový kód 404.

Další informace o importu dat protokolu do nástroje Microsoft Message Analyzer najdete v tématu [načtení dat zpráv](https://technet.microsoft.com/library/dn772437.aspx) na webu TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Použití ID žádosti klienta k koreluji dat souboru protokolu

Klientská knihovna Azure Storage pro každý požadavek automaticky vygeneruje jedinečné ID žádosti klienta. Tato hodnota je zapsána do protokolu klienta, protokolu serveru a síťového trasování, takže ji můžete použít ke korelaci dat ve všech třech protokolech v rámci analyzátoru zpráv. Další informace o ID žádosti klienta najdete v tématu [ID žádosti klienta](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) .

Níže uvedené části popisují, jak použít předem nakonfigurovaná a vlastní zobrazení rozložení ke sladění a seskupení dat na základě ID žádosti klienta.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Vyberte rozložení zobrazení, které se má zobrazit v mřížce analýzy.

Prostředky úložiště pro analyzátor zpráv zahrnují Azure Storage zobrazení rozložení, která jsou předem nakonfigurovaná zobrazení, která můžete použít k zobrazení dat s užitečnými seskupeními a sloupci pro různé scénáře. Můžete také vytvořit vlastní rozložení zobrazení a uložit je pro opakované použití.

Následující obrázek znázorňuje nabídku **rozložení zobrazení** , která je k dispozici, výběrem možnosti **Zobrazit rozložení** na pásu karet na panelu nástrojů. Rozložení zobrazení pro Azure Storage jsou seskupena pod uzlem **Azure Storage** v nabídce. `Azure Storage`Ve vyhledávacím poli můžete hledat a filtrovat tak Azure Storage jenom rozložení zobrazení. Můžete také vybrat hvězdičku vedle rozložení zobrazení a nastavit ji jako oblíbenou a zobrazit ji v horní části nabídky.

![Zobrazit nabídku rozložení](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Pokud chcete začít, vyberte **seskupeno podle ID žádosti klienta a modul**. Toto zobrazení seskupí data protokolů ze všech tří protokolů jako první podle ID žádosti klienta, a to podle zdrojového souboru protokolu (nebo **modulu** v analyzátoru zpráv). V tomto zobrazení můžete přejít k podrobnostem konkrétního ID žádosti klienta a zobrazit data ze všech tří souborů protokolu pro toto ID žádosti klienta.

Následující obrázek ukazuje toto zobrazení rozložení použité pro ukázková data protokolu s podmnožinou zobrazených sloupců. Můžete vidět, že pro konkrétní ID žádosti klienta se v mřížce analýzy zobrazují data z protokolu klienta, protokolu serveru a trasování sítě.

![Rozložení zobrazení Azure Storage](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Různé soubory protokolu mají různé sloupce, takže když se data z více souborů protokolu zobrazují v mřížce analýzy, některé sloupce nemusí obsahovat žádná data pro daný řádek. Například na obrázku výše řádky protokolu klienta nezobrazují žádná data pro sloupce **timestamp**, **TimeElapsed**, **source**a **Destination** , protože tyto sloupce neexistují v protokolu klienta, ale existují v síťovém trasování. Podobně sloupec **časového razítka** zobrazuje data časového razítka z protokolu serveru, ale nezobrazují se žádná data pro sloupce **TimeElapsed**, **source**a **Destination** , které nejsou součástí protokolu serveru.
>
>

Kromě používání Azure Storagech rozložení zobrazení můžete také definovat a ukládat vlastní rozložení zobrazení. Můžete vybrat další požadovaná pole pro seskupování dat a uložit seskupení jako součást vlastního rozložení také.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Použití pravidel barev pro analytickou mřížku

Prostředky úložiště také obsahují barevná pravidla, která nabízejí vizuální prostředky pro identifikaci různých typů chyb v mřížce analýzy. Předdefinovaná pravidla barev se vztahují na chyby protokolu HTTP, takže se zobrazí pouze pro protokol serveru a síťové trasování.

Chcete-li použít pravidla barev, vyberte **pravidla barev** z pásu karet na panelu nástrojů. V nabídce se zobrazí pravidla barev Azure Storage. V tomto kurzu vyberte **chyby klienta (StatusCode mezi 400 a 499)**, jak je znázorněno na obrázku níže.

![Rozložení zobrazení Azure Storage](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Kromě použití pravidel barev Azure Storage můžete také definovat a ukládat vlastní pravidla barev.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Seskupení a filtrování dat protokolu pro nalezení 400 chyb rozsahu

Dále seskupme a vyfiltrujeme data protokolu pro vyhledání všech chyb v rozsahu 400.

1. V mřížce analýzy vyhledejte sloupec **StatusCode** , klikněte pravým tlačítkem myši na záhlaví sloupce a vyberte možnost **Skupina**.
2. Dále seskupte sloupec **ID žádosti klienta** . Uvidíte, že data v mřížce analýza jsou nyní uspořádána podle stavového kódu a podle ID žádosti klienta.
3. Pokud ještě není zobrazený, zobrazí se okno nástroje zobrazení filtru. Na pásu karet na panelu nástrojů vyberte **okna nástrojů**a pak **Zobrazit filtr**.
4. Pokud chcete filtrovat data protokolu tak, aby se zobrazily jenom 400 – chyby rozsahu, přidejte do okna **filtru zobrazení** následující kritéria filtru a klikněte na **použít**:

    `(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)`

Následující obrázek znázorňuje výsledky tohoto seskupení a filtru. Rozšíření pole **ID žádosti klienta** pod seskupením pro stavový kód 409, například zobrazuje operaci, která je výsledkem tohoto stavového kódu.

![Rozložení zobrazení Azure Storage](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Po použití tohoto filtru uvidíte, že jsou vyloučené řádky z protokolu klienta, protože protokol klienta neobsahuje sloupec **StatusCode** . Aby bylo možné začít, zkontrolujte protokoly serveru a sítě a vyhledejte chyby 404 a potom se vrátíme do protokolu klienta, kde Prozkoumejte operace klientů, které jim vedly.

> [!NOTE]
> Můžete filtrovat sloupec **StatusCode** a nadále zobrazovat data ze všech tří protokolů, včetně protokolu klienta, pokud přidáte výraz do filtru, který obsahuje položky protokolu, ve kterých je stavový kód null. Chcete-li vytvořit tento výraz filtru, použijte:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Tento filtr vrátí všechny řádky z protokolu klienta a pouze řádky z protokolu serveru a protokolu HTTP, kde je kód stavu větší než 400. Pokud ho použijete pro rozložení zobrazení seskupené podle ID a modulu žádosti klienta, můžete vyhledat nebo procházet záznamy v protokolu a vyhledat, kde jsou všechny tři protokoly reprezentované.

### <a name="filter-log-data-to-find-404-errors"></a>Filtrování dat protokolu pro nalezení 404 chyb

Prostředky úložiště obsahují předdefinované filtry, které můžete použít k zúžení dat protokolů a hledání chyb nebo trendů, které hledáte. V dalším kroku použijeme dva předdefinované filtry: jeden, který filtruje protokoly serveru a sítě pro chyby 404 a ten, který filtruje data v zadaném časovém rozsahu.

1. Pokud ještě není zobrazený, zobrazí se okno nástroje zobrazení filtru. Na pásu karet na panelu nástrojů vyberte **okna nástrojů**a pak **Zobrazit filtr**.
2. V okně zobrazení filtru vyberte **Knihovna**a vyhledejte `Azure Storage` Azure Storage filtry. **Ve všech protokolech vyberte filtr pro zprávy 404 (Nenalezeno)**.
3. Znovu zobrazte nabídku **Knihovna** a vyhledejte a vyberte **globální filtr času**.
4. Upravte časová razítka zobrazená ve filtru na rozsah, který chcete zobrazit. To vám pomůže zúžit rozsah dat, která se mají analyzovat.
5. Váš filtr by měl vypadat podobně jako v následujícím příkladu. Klikněte na **použít** , pokud chcete filtr použít pro analytickou mřížku.

    `((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)`

    ![Rozložení zobrazení Azure Storage](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analýza dat protokolu

Teď, když jste seskupili a vyfiltrovaná data, můžete zkontrolovat podrobnosti jednotlivých požadavků vygenerovaných chybami 404. V aktuálním rozložení zobrazení jsou data seskupená podle ID žádosti klienta a pak podle zdroje protokolu. Vzhledem k tomu, že filtrujeme žádosti, kde pole StatusCode obsahuje 404, uvidíme jenom data trasování serveru a sítě, nikoli data protokolu klienta.

Následující obrázek ukazuje konkrétní požadavek, ve kterém operace Get BLOB vrátila 404, protože objekt BLOB neexistuje. Všimněte si, že některé sloupce byly odebrány ze standardního zobrazení, aby bylo možné zobrazit relevantní data.

![Filtrované protokoly serveru a trasování sítě](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

V dalším kroku budeme korelovat toto ID žádosti klienta s daty protokolů klienta, abyste zjistili, jaké akce klient provedl v době, kdy došlo k chybě. Můžete zobrazit nové zobrazení tabulky analýzy pro tuto relaci, abyste zobrazili data protokolu klienta, která se otevírají na druhé kartě:

1. Nejprve Zkopírujte hodnotu pole **ID žádosti klienta** do schránky. To můžete provést tak, že vyberete jeden řádek, vyhledáte pole **ID žádosti klienta** , kliknete pravým tlačítkem na hodnotu dat a zvolíte **Kopírovat ' ID žádosti klienta '**.
2. Na pásu karet na panelu nástrojů vyberte **Nový prohlížeč**a pak vyberte **Analytická mřížka** . otevře se nová karta. Nová karta zobrazuje všechna data v souborech protokolu bez seskupení, filtrování nebo pravidel barev.
3. Na pásu karet na panelu nástrojů vyberte možnost **Zobrazit rozložení**a pak v části **Azure Storage** vyberte **všechny sloupce klienta rozhraní .NET** . Toto rozložení zobrazení zobrazuje data z protokolu klienta i protokoly serveru a sítě. Ve výchozím nastavení je seřazený podle sloupce **MessageNumber** .
4. Dále vyhledejte protokol klienta pro ID žádosti klienta. Na pásu karet na panelu nástrojů vyberte **Vyhledat zprávy**a potom do pole **Najít** zadejte vlastní filtr pro ID žádosti klienta. Použijte tuto syntaxi pro filtr a zadejte vlastní ID žádosti klienta:

    `*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"`

Analyzátor zpráv vyhledá a vybere první položku protokolu, ve které kritéria hledání odpovídají ID žádosti klienta. V protokolu klienta existuje několik záznamů pro každé ID žádosti klienta, takže je můžete chtít seskupit do pole **ID žádosti klienta** , aby bylo snazší je zobrazit dohromady. Následující obrázek zobrazuje všechny zprávy v protokolu klienta pro zadané ID žádosti klienta.

![Protokol klienta zobrazující chyby 404](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Pomocí dat zobrazených v rozloženích zobrazení na těchto dvou kartách můžete analyzovat data žádosti, abyste zjistili, co mohl chybu způsobit. Můžete se také podívat na žádosti, které předcházejí této události, a zjistit, jestli by předchozí událost mohla vést k chybě 404. Můžete například zkontrolovat položky protokolu klienta předcházející tomuto ID žádosti klienta, abyste zjistili, jestli byl objekt BLOB odstraněný, nebo jestli došlo k chybě kvůli tomu, že klientská aplikace zavolala rozhraní CreateIfNotExists API na kontejneru nebo objektu BLOB. V protokolu klienta můžete najít adresu objektu BLOB v poli **Popis** ; v protokolech trasování serveru a sítě se tyto informace zobrazí v poli **Shrnutí** .

Jakmile budete znát adresu objektu blob, který vrátil chybu 404, můžete prozkoumat další. Pokud prohledáte položky protokolu pro jiné zprávy přidružené k operacím ve stejném objektu blob, můžete ověřit, zda klient dříve odstranil entitu.

## <a name="analyze-other-types-of-storage-errors"></a>Analyzovat jiné typy chyb úložiště

Teď, když jste obeznámeni s tím, že k analýze dat protokolů používáte analyzátor zpráv, můžete analyzovat jiné typy chyb pomocí zobrazení rozložení, pravidel barev a vyhledávání a filtrování. V tabulkách níže jsou uvedené některé problémy, se kterými se můžete setkat, a kritéria filtru, pomocí kterých je můžete vyhledat. Další informace o sestavování filtrů a jazyku filtrování analyzátoru zpráv najdete v tématu [filtrování dat zpráv](https://technet.microsoft.com/library/jj819365.aspx).

| K prozkoumání... | Použít výraz filtru... | Výraz se vztahuje k protokolu (klient, server, síť, vše). |
| --- | --- | --- |
| Neočekávaná zpoždění při doručování zpráv ve frontě |AzureStorageClientDotNetV4. Description obsahuje "opakování neúspěšné operace". |Klient |
| Zvýšení HTTP v PercentThrottlingError |HTTP. Response. StatusCode = = 500 &#124;&#124; HTTP. Response. StatusCode = = 503 |Síť |
| Zvýšení v PercentTimeoutError |HTTP. Response. StatusCode = = 500 |Síť |
| Zvýšení v PercentTimeoutError (vše) |* StatusCode = = 500 |Vše |
| Zvýšení v PercentNetworkError |AzureStorageClientDotNetV4. EventLogEntry. Level < 2 |Klient |
| Zprávy HTTP 403 (Zakázáno) |HTTP. Response. StatusCode = = 403 |Síť |
| Zprávy HTTP 404 (Nenalezeno) |HTTP. Response. StatusCode = = 404 |Síť |
| 404 (vše) |* StatusCode = = 404 |Vše |
| Problém s autorizací sdíleného přístupového podpisu (SAS) |AzureStorageLog. stavem žádosti = = "SASAuthorizationError" |Síť |
| Zprávy HTTP 409 (Konflikt) |HTTP. Response. StatusCode = = 409 |Síť |
| 409 (vše) |* StatusCode = = 409 |Vše |
| Nízké PercentSuccess nebo položky protokolu analýzy mají operace se stavem transakce ClientOtherErrors. |AzureStorageLog. stavem žádosti = = "ClientOtherError" |Server |
| Upozornění Nagle |((AzureStorageLog. EndToEndLatencyMS-AzureStorageLog. ServerLatencyMS) > (AzureStorageLog. ServerLatencyMS * 1,5)) a (AzureStorageLog. RequestPacketSize <1460) a (AzureStorageLog. EndToEndLatencyMS-AzureStorageLog. ServerLatencyMS >= 200) |Server |
| Časový rozsah v protokolech serveru a sítě |#Timestamp >= 2014-10-20T16:36:38 a #Timestamp <= 2014-10-20T16:36:39 |Server, síť |
| Časový rozsah v protokolech serveru |AzureStorageLog. timestamp >= 2014-10-20T16:36:38 a AzureStorageLog. timestamp <= 2014-10-20T16:36:39 |Server |

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží se všemi koncovými scénáři v Azure Storage najdete v těchto zdrojích informací:

* [Monitorování, diagnostika a řešení problémů s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)
* [Storage Analytics](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md)
* [Provozní příručka nástroje Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
