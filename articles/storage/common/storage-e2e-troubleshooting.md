---
title: Poradce při potížích s datovými operacemi pomocí diagnostiky a analyzátoru zpráv
titleSuffix: Azure Storage
description: Kurz demonstrující komplexní řešení potíží pomocí Azure Storage Analytics, AzCopy a Microsoft Message Analyzer
author: normesta
ms.service: storage
ms.topic: conceptual
ms.date: 12/20/2019
ms.author: normesta
ms.reviewer: cbrooks
ms.subservice: common
ms.openlocfilehash: 69983502fb7d099f474fb1c4c084f5d381a173e9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76314755"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Kompletní řešení potíží pomocí metrik a protokolování služby Azure Storage, nástrojů AzCopy a Message Analyzer

[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnostika a řešení potíží je klíčovou dovedností pro vytváření a podporu klientských aplikací pomocí úložiště Microsoft Azure. Vzhledem k distribuované povaze aplikace Azure může být diagnostika a řešení chyb a problémů s výkonem složitější než v tradičních prostředích.

V tomto kurzu ukážeme, jak identifikovat určité chyby, které mohou ovlivnit výkon, a řešení těchto chyb od začátku do konce pomocí nástrojů poskytovaných společností Microsoft a Azure Storage za účelem optimalizace klientské aplikace.

Tento kurz poskytuje praktické zkoumání scénáře řešení potíží od konce. Podrobný koncepční průvodce odstraňováním potíží s aplikacemi úložiště Azure najdete v tématu [Monitorování, diagnostika a řešení potíží s úložištěm Microsoft Azure](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Nástroje pro řešení potíží s aplikacemi Azure Storage

Chcete-li řešit potíže s klientskými aplikacemi používajícími Microsoft Azure Storage, můžete pomocí kombinace nástrojů určit, kdy došlo k problému a jaká může být příčina problému. Mezi tyto nástroje patří:

* **Azure Storage Analytics**. [Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) poskytuje metriky a protokolování pro Azure Storage.

  * **Metriky úložiště** sledují metriky transakcí a kapacity pro váš účet úložiště. Pomocí metriky můžete určit, jak si vaše aplikace vede podle různých měřítek. Další informace o typech metrik sledovaných službou Storage Analytics najdete v tématu Schéma [tabulky metrik analýzy úložiště.](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema)
  * **Protokolování úložiště** protokoluje každý požadavek na služby Azure Storage do protokolu na straně serveru. Protokol sleduje podrobná data pro každý požadavek, včetně provedené operace, stavu operace a informací o latenci. Další informace o datech požadavků a odpovědí, které do protokolů zapisuje služba Storage Analytics, najdete v tématu [Formát protokolu služby](/rest/api/storageservices/Storage-Analytics-Log-Format) Storage Analytics.

* **Portál Azure**. Metriky a protokolování pro svůj účet úložiště můžete nakonfigurovat na [webu Azure Portal](https://portal.azure.com). Můžete také zobrazit grafy a grafy, které ukazují, jak vaše aplikace funguje v průběhu času a nakonfigurovat výstrahy upozornit, pokud aplikace provádí jinak, než bylo očekáváno pro zadanou metriku.

    Informace o konfiguraci monitorování na webu Azure Portal najdete [v tématu Sledování účtu úložiště na webu Azure Portal.](storage-monitor-storage-account.md)
* **AzCopy**. Protokoly serveru pro Azure Storage jsou uloženy jako objekty BLOB, takže můžete pomocí AzCopy zkopírovat objekty BLOB protokolu do místního adresáře pro analýzu pomocí analyzátoru zpráv společnosti Microsoft. Další informace o [AzCopy naleznete v tématu Přenos dat pomocí nástroje příkazového řádku AzCopy.](storage-use-azcopy.md)
* **Analyzátor zpráv společnosti Microsoft**. Analyzátor zpráv je nástroj, který využívá soubory protokolu a zobrazuje data protokolu ve vizuálním formátu, který usnadňuje filtrování, vyhledávání a skupinové log dat do užitečných sad, které můžete použít k analýze chyb a problémů s výkonem. Další informace o nástroji Analyzátor zpráv naleznete v [příručce Microsoft Message Analyzer.](https://technet.microsoft.com/library/jj649776.aspx)

## <a name="about-the-sample-scenario"></a>O ukázkovém scénáři

V tomto kurzu se podíváme na scénář, kde metriky Azure Storage označuje nízkou procentuální úspěšnost pro aplikaci, která volá úložiště Azure. Metrika nízké procentuální úspěšnosti (zobrazená jako **PercentSuccess** na [portálu Azure](https://portal.azure.com) a v tabulkách metrik) sleduje operace, které jsou úspěšné, ale vracejí stavový kód HTTP, který je větší než 299. V souborech protokolu úložiště na straně serveru jsou tyto operace zaznamenány se stavem transakce **ClientOtherErrors**. Další podrobnosti o metriku nízké procento úspěšnosti naleznete v [tématu Metriky zobrazit nízké PercentSuccess nebo analytické položky protokolu mají operace se stavem transakce ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Operace azure storage může vrátit http stavové kódy větší než 299 jako součást jejich normální funkce. Ale tyto chyby v některých případech naznačují, že je možné optimalizovat klientské aplikace pro lepší výkon.

V tomto scénáři budeme považovat nízkou procentuální úspěšnost za nižší než 100 %. Můžete si však vybrat jinou úroveň metriky podle svých potřeb. Doporučujeme, abyste během testování aplikace vytvořit základní tolerance pro klíčové metriky výkonu. Na základě testování můžete například určit, že vaše aplikace by měla mít konzistentní procentuální úspěšnost 90 % neboli 85 %. Pokud data metrik ukazují, že se aplikace odchyluje od tohoto čísla, můžete zjistit, co může být příčinou zvýšení.

Pro náš ukázkový scénář, jakmile zjistíme, že metrika míry úspěšnosti procenta je nižší než 100 %, prověříme protokoly, abychom našli chyby, které korelují s metrikami, a použijeme je k tomu, abychom zjistili, co způsobuje nižší procentuální úspěšnost. Podíváme se konkrétně na chyby v řadě 400. Pak budeme podrobněji zkoumat chyby 404 (nenalezeno).

### <a name="some-causes-of-400-range-errors"></a>Některé příčiny chyb v rozsahu 400

Příklady níže ukazují vzorkování některých chyb rozsahu 400 pro požadavky proti azure blob storage a jejich možné příčiny. Každá z těchto chyb, stejně jako chyby v rozsahu 300 a 500 rozsahu, může přispět k nízké procentuúspěšnosti.

Všimněte si, že níže uvedené seznamy nejsou zdaleka kompletní. Podrobnosti o obecných chybách úložiště Azure a o chybách specifických pro každou službu úložiště najdete v tématu [Stavové kódy a kódy](https://msdn.microsoft.com/library/azure/dd179382.aspx) chyb v msdn.

#### <a name="status-code-404-not-found-examples"></a>Stavový kód 404 (nebyl nalezen) Příklady

Vyvolá se, když se nezdaří operace čtení proti kontejneru nebo objektu blob, protože objekt blob nebo kontejner nebyl nalezen.

* Vyvolá se, pokud byl kontejner nebo objekt blob odstraněn jiným klientem před tímto požadavkem.
* Vyvolá se, pokud používáte volání rozhraní API, které vytvoří kontejner nebo objekt blob po kontrole, zda existuje. CreateIfNotExists API provést head volání nejprve zkontrolovat existenci kontejneru nebo objektblou; Pokud neexistuje, je vrácena chyba 404 a pak je provedeno druhé volání PUT k zápisu kontejneru nebo objektu blob.

#### <a name="status-code-409-conflict-examples"></a>Příklady stavového kódu 409 (konflikt)

* Vyvolá se, pokud pomocí vytvořit rozhraní API vytvořit nový kontejner nebo objekt blob, bez kontroly existence první a kontejner nebo objekt blob s tímto názvem již existuje.
* Vyvolá se, pokud je kontejner odstraňován a pokusíte se vytvořit nový kontejner se stejným názvem před dokončením operace odstranění.
* Vyvolá se, pokud zadáte zapůjčení kontejneru nebo objektu blob a již je k dispozici zapůjčení.

#### <a name="status-code-412-precondition-failed-examples"></a>Stavový kód 412 (předběžná podmínka se nezdařila) Příklady

* Vyvolá se v případě, že nebyla splněna podmínka zadaná podmíněnou hlavičkou.
* Vyvolá se v případě, že zadané ID zapůjčení neodpovídá ID zapůjčení kontejneru nebo objektu blob.

## <a name="generate-log-files-for-analysis"></a>Generovat soubory protokolu pro analýzu

V tomto kurzu použijeme Nástroj pro analýzu zpráv pro práci se třemi různými typy souborů protokolu, i když můžete pracovat s některou z těchto:

* **Protokol serveru**, který se vytvoří, když povolíte protokolování úložiště Azure. Protokol serveru obsahuje data o každé operaci volána proti jedné ze služeb Azure Storage – objekt blob, fronty, tabulky a souboru. Protokol serveru označuje, která operace byla volána a jaký stavový kód byl vrácen, a další podrobnosti o požadavku a odpovědi.
* **Protokol klienta .NET**, který je vytvořen při povolení protokolování na straně klienta z aplikace .NET. Protokol klienta obsahuje podrobné informace o tom, jak klient připravuje požadavek a přijímá a zpracovává odpověď.
* **Protokol trasování sítě HTTP**, který shromažďuje data na http/HTTPS požadavky a data odpovědi, včetně pro operace proti Azure Storage. V tomto kurzu budeme generovat trasování sítě prostřednictvím analyzátoru zpráv.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurace protokolování a metrik na straně serveru

Nejprve budeme muset nakonfigurovat protokolování azure storage a metriky, takže máme data ze strany služby k analýze. Protokolování a metriky můžete konfigurovat různými způsoby – prostřednictvím [portálu Azure](https://portal.azure.com), pomocí PowerShellu nebo programově. Podrobnosti o konfiguraci protokolování a metrik y najdete v [tématech Povolit metriky](storage-analytics-metrics.md#enable-metrics-using-the-azure-portal) a [Povolit protokolování.](storage-analytics-logging.md#enable-storage-logging)

### <a name="configure-net-client-side-logging"></a>Konfigurace protokolování na straně klienta rozhraní .NET

Chcete-li nakonfigurovat protokolování na straně klienta pro aplikaci .NET, povolte diagnostiku .NET v konfiguračním souboru aplikace (web.config nebo app.config). Podrobnosti [najdete v tématu Protokolování na straně klienta na straně klienta .NET s klientskou knihovnou úložiště](https://msdn.microsoft.com/library/azure/dn782839.aspx) a [protokolování na straně klienta pomocí sady Microsoft Azure Storage SDK pro jazyk Java](https://msdn.microsoft.com/library/azure/dn782844.aspx) na msdn.

Protokol na straně klienta obsahuje podrobné informace o tom, jak klient připravuje požadavek a přijímá a zpracovává odpověď.

Klientská knihovna úložiště ukládá data protokolu na straně klienta do umístění určeného v konfiguračním souboru aplikace (web.config nebo app.config).

### <a name="collect-a-network-trace"></a>Shromáždění trasování v síti

Analyzátor zpráv můžete použít ke shromažďování trasování sítě HTTP/HTTPS, když je klientská aplikace spuštěna. Analyzátor zpráv používá [Šumař](https://www.telerik.com/fiddler) na back-endu. Před shromažďováním trasování v síti doporučujeme nakonfigurovat Šumař tak, aby zaznamenával nešifrovaný přenos https:

1. Nainstalujte [Fiddler](https://www.telerik.com/download/fiddler).
2. Spusťte Šumař.
3. Vybrat **nástroje | Fiddler Možnosti**.
4. V dialogovém okně Možnosti se ujistěte, že jsou **vybrány protokoly Capture HTTPS CONNECTs** a **Decrypt HTTPS Traffic,** jak je znázorněno níže.

![Konfigurovat možnosti šumavy](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Pro účely kurz, shromažďovat a ukládat trasování sítě nejprve v Analyzátor zpráv, pak vytvořit relaci analýzy analyzovat trasování a protokoly. Shromažďování trasování v síti v analyzátoru zpráv:

1. V analyzátoru zpráv vyberte **Soubor | Rychlá trasa | Nešifrovaný protokol HTTPS**.
2. Trasování začne okamžitě. **Chcete-li** trasování zastavit, chcete-li trasování zastavit, abychom ho mohli nakonfigurovat pouze pro sledování provozu úložiště.
3. Vyberte **Upravit,** chcete-li upravit relaci vektorizace.
4. Vyberte odkaz **Konfigurovat** vpravo od poskytovatele **Microsoft-Pef-WebProxy** ETW.
5. V dialogovém okně **Upřesnit nastavení** klikněte na kartu **Zprostředkovatel.**
6. V poli **Filtr název hostitele** zadejte koncové body úložiště oddělené mezerami. Můžete například zadat koncové body následujícím způsobem; změna `storagesample` názvu účtu úložiště:

    `storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net`

7. Ukončete dialogové okno a kliknutím na **Restartzačněte** shromažďovat trasování s filtrem název hostitele na místě, takže do trasování je zahrnut pouze síťový provoz Azure Storage.

> [!NOTE]
> Po dokončení shromažďování trasování v síti důrazně doporučujeme vrátit nastavení, která jste pravděpodobně změnili v Fiddleru k dešifrování provozu HTTPS. V dialogovém okně Volby šumiva odznačte zaškrtávací **políčka Zachytávat HTTPS CONNECTy** a **Dešifrovat https traffic.**

Další podrobnosti najdete [v tématu Použití funkcí trasování sítě](https://technet.microsoft.com/library/jj674819.aspx) na technetu.

## <a name="review-metrics-data-in-the-azure-portal"></a>Kontrola dat metrik na webu Azure Portal

Jakmile vaše aplikace běží po určitou dobu, můžete zkontrolovat metriky grafy, které se zobrazí na [portálu Azure](https://portal.azure.com) sledovat, jak vaše služba byla výkon.

Nejdřív přejděte na svůj účet úložiště na webu Azure Portal. Ve výchozím nastavení se v okně účtu zobrazí graf monitorování s metrikou **Procento úspěchu.** Pokud jste graf dříve upravili tak, aby zobrazoval různé metriky, přidejte metriku **Procento úspěšnosti.**

Nyní se v grafu monitorování zobrazí **procento úspěšnosti** spolu s dalšími metrikami, které jste přidali. Ve scénáři budeme zkoumat další analýzou protokolů v Analyzátor zpráv, procento úspěšnosti je poněkud nižší než 100%.

Další podrobnosti o přidávání a přizpůsobení grafů metrik najdete v [tématu Přizpůsobení grafů metrik](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Může chvíli trvat, než se data metrik zobrazí na webu Azure Portal po povolení metrik úložiště. Důvodem je, že hodinové metriky pro předchozí hodinu se nezobrazují na webu Azure Portal, dokud neuplyne aktuální hodina. Metriky minut se taky momentálně nezobrazují na webu Azure Portal. Takže v závislosti na tom, kdy povolíte metriky, může trvat až dvě hodiny, než se zobrazí data metrik.
>
>

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Kopírování protokolů serveru do místního adresáře pomocí azcopy

Azure Storage zapisuje data protokolu serveru do objektů BLOB, zatímco metriky se zapisují do tabulek. Objekty BLOB protokolu jsou k `$logs` dispozici ve známém kontejneru pro váš účet úložiště. Objekty BLOB protokolu jsou pojmenovány hierarchicky podle roku, měsíce, dne a hodiny, takže můžete snadno vyhledat rozsah času, který chcete prozkoumat. Například v `storagesample` účtu kontejner pro objekty BLOB protokolu pro 01/02/2015 od 8 `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`do 9 hodin, je . Jednotlivé objekty BLOB v tomto kontejneru `000000.log`jsou pojmenovány postupně, počínaje .

Pomocí nástroje příkazového řádku AzCopy můžete stáhnout tyto soubory protokolu na straně serveru do umístění podle vašeho výběru v místním počítači. Pomocí následujícího příkazu můžete například stáhnout soubory protokolu pro operace objektů blob, ke kterým `C:\Temp\Logs\Server`došlo 2. nahraďte `<storageaccountname>` název svého účtu úložiště:

```azcopy
azcopy copy 'http://<storageaccountname>.blob.core.windows.net/$logs/blob/2015/01/02' 'C:\Temp\Logs\Server'  --recursive
```

AzCopy je k dispozici ke stažení na stránce [Azure Ke stažení.](https://azure.microsoft.com/downloads/) Podrobnosti o používání azcopy viz [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md).

Další informace o stahování protokolů na straně serveru naleznete [v tématu Stahování dat protokolu protokolování úložiště](https://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Analýza dat protokolu pomocí nástroje Microsoft Message Analyzer

Microsoft Message Analyzer je nástroj pro zachycení, zobrazení a analýzu protokolu zasílání zpráv provoz, události a další zprávy systému nebo aplikace v řešení potíží a diagnostických scénářů. Analyzátor zpráv také umožňuje načítat, agregovat a analyzovat data z protokolu a uložených trasovacích souborů. Další informace o nástroji Analyzátor zpráv naleznete v [příručce Microsoft Message Analyzer Operating Guide](https://technet.microsoft.com/library/jj649776.aspx).

Analyzátor zpráv obsahuje prostředky pro Azure Storage, které vám pomůžou analyzovat protokoly serveru, klienta a sítě. V této části budeme diskutovat o tom, jak pomocí těchto nástrojů k řešení problému nízké procento úspěchu v protokolech úložiště.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Stažení a instalace Analyzátoru zpráv a prostředků úložiště Azure

1. Stáhněte [si nástroj Pro analýzu zpráv](https://www.microsoft.com/download/details.aspx?id=44226) ze služby Stažení softwaru a spusťte instalační program.
2. Spusťte analyzátor zpráv.
3. V nabídce **Nástroje** vyberte **Správce majetku**. V dialogovém okně **Správce prostředků** vyberte **Položky Ke stažení**a pak filtrujte v Azure **Storage**. Uvidíte prostředky úložiště Azure, jak je znázorněno na obrázku níže.
4. Kliknutím na **Synchronizovat všechny zobrazené položky** nainstalujte prostředky úložiště Azure. Mezi dostupná aktiva patří:
   * **Pravidla barev úložiště Azure:** Pravidla barev azure storage umožňují definovat speciální filtry, které používají barevné, textové a styly písma ke zvýraznění zpráv, které obsahují konkrétní informace v trasování.
   * **Grafy úložišť Azure:** Grafy Azure Storage jsou předdefinované grafy, které data protokolu serveru grafu. Všimněte si, že chcete-li použít grafy azure storage v tomto okamžiku, můžete pouze načíst protokol serveru do mřížky analýzy.
   * **Analyzátory úložišť Azure:** Analyzátory Azure Storage analyzují protokoly klienta Azure Storage, serveru a PROTOKOLU HTTP, aby je zobrazily v analytické mřížce.
   * **Filtry úložiště Azure:** Filtry Azure Storage jsou předdefinovaná kritéria, která můžete použít k dotazování dat v analýze mřížky.
   * **Rozložení Zobrazení úložiště Azure:** Rozložení zobrazení úložiště Azure jsou předdefinované rozložení sloupců a seskupení v analýze mřížky.
5. Po instalaci datových zdrojů restartujte nástroj Message Analyzer.

![Správce datových zdrojů analyzátoru zpráv](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Nainstalujte všechny prostředky Azure Storage zobrazené pro účely tohoto kurzu.
>
>

### <a name="import-your-log-files-into-message-analyzer"></a>Import souborů protokolu do nástroje Analyzátor zpráv

Všechny uložené soubory protokolu (na straně serveru, na straně klienta a v síti) můžete importovat do jedné relace v nástroji Microsoft Message Analyzer pro analýzu.

1. V nabídce **Soubor** v nástroji Microsoft Message Analyzer klepněte na tlačítko **Nová relace**a potom klepněte na tlačítko **Prázdná relace**. V dialogovém okně **Nová relace** zadejte název relace analýzy. V panelu **Podrobnosti relace** klikněte na tlačítko **Soubory.**
2. Chcete-li načíst data trasování v síti vygenerovaná analyzátorem zpráv, klepněte na **tlačítko Přidat soubory**, vyhledejte umístění, do kterého jste uložili soubor Matp z relace trasování webu, vyberte soubor Matp a klepněte na tlačítko **Otevřít**.
3. Chcete-li načíst data protokolu na straně serveru, klepněte na **tlačítko Přidat soubory**, vyhledejte umístění, kde jste stáhli protokoly na straně serveru, vyberte soubory protokolu pro časový rozsah, který chcete analyzovat, a klepněte na tlačítko **Otevřít**. Potom v panelu **Podrobnosti relace** nastavte rozevírací nabídka **Konfigurace textového protokolu** pro každý soubor protokolu na straně serveru na **AzureStorageLog,** abyste zajistili, že Analyzátor zpráv Microsoft může správně analyzovat soubor protokolu.
4. Chcete-li načíst data protokolu na straně klienta, klepněte na tlačítko **Přidat soubory**, vyhledejte umístění, do kterého jste uložili protokoly na straně klienta, vyberte soubory protokolu, které chcete analyzovat, a klepněte na tlačítko **Otevřít**. Potom v panelu **Podrobnosti relace** nastavte rozevírací seznam **Konfigurace textového protokolu** pro každý soubor protokolu na straně klienta na **AzureStorageClientDotNetV4,** abyste zajistili, že analyzátor zpráv Microsoft může správně analyzovat soubor protokolu.
5. Klepnutím na **tlačítko Start** v dialogovém okně **Nová relace** načtěte a analyzujete data protokolu. Data protokolu se zobrazí v mřížce analýzy analyzátoru zpráv.

Následující obrázek ukazuje ukázkovou relaci nakonfigurovanou se serverovými, klientskými a síťovými soubory protokolu trasování.

![Konfigurace relace analyzátoru zpráv](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Všimněte si, že Analyzátor zpráv načte soubory protokolu do paměti. Pokud máte velkou sadu dat protokolu, budete chtít filtrovat, aby bylo možné získat nejlepší výkon z analyzátoru zpráv.

Nejprve určete časový rámec, který máte zájem o revizi, a udržujte tento časový rámec co nejmenší. V mnoha případech budete chtít zkontrolovat období minut nebo hodin maximálně. Importujte nejmenší sadu protokolů, které vyhovují vašim potřebám.

Pokud máte stále velké množství dat protokolu, můžete zadat filtr relace pro filtrování dat protokolu před jejich načtením. V poli **Filtr relace** vyberte tlačítko **Knihovna** a zvolte předdefinovaný filtr; Například zvolte **Globální časový filtr I** z filtrů Azure Storage pro filtrování v časovém intervalu. Potom můžete upravit kritéria filtru a určit počáteční a koncové časové razítko pro interval, který chcete zobrazit. Můžete také filtrovat na konkrétní stavový kód; Můžete například načíst pouze položky protokolu, kde je stavový kód 404.

Další informace o importu dat protokolu do nástroje Microsoft Message Analyzer naleznete v [tématu Načítání dat zpráv](https://technet.microsoft.com/library/dn772437.aspx) na webu TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>Použití ID požadavku klienta ke korelaci dat souboru protokolu

Klientská knihovna úložiště Azure automaticky generuje jedinečné ID požadavku klienta pro každý požadavek. Tato hodnota je zapsána do protokolu klienta, protokolu serveru a trasování sítě, takže ji můžete použít ke korelaci dat ve všech třech protokolech v rámci analyzátoru zpráv. Další informace o ID žádosti o klienta naleznete v tématu [ID žádosti](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) klienta.

Následující části popisují, jak pomocí předkonfigurovaných a vlastních zobrazení rozložení korelovat a seskupit data na základě ID požadavku klienta.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Výběr rozložení zobrazení, které se má zobrazit v mřížce analýzy.

Prostředky úložiště pro analyzátor zpráv zahrnují rozložení zobrazení úložiště Azure, což jsou předem nakonfigurovaná zobrazení, která můžete použít k zobrazení dat s užitečnými seskupeními a sloupci pro různé scénáře. Můžete také vytvořit vlastní rozložení zobrazení a uložit je pro opakované použití.

Na obrázku níže je zobrazena nabídka **Rozložení zobrazení,** která je k dispozici výběrem **zobrazení rozložení** na pásu karet panelu nástrojů. Rozložení zobrazení pro Azure Storage jsou seskupené pod uzla **Azure Storage** v nabídce. Ve vyhledávacím `Azure Storage` poli můžete hledat a filtrovat jenom v rozloženích zobrazení Azure Storage. Můžete také vybrat hvězdičku vedle rozložení zobrazení, aby byla oblíbená, a zobrazit ji v horní části nabídky.

![Nabídka Zobrazit rozložení](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Chcete-li začít, vyberte **Seskupeno podle ClientRequestID a modul**. Toto rozložení zobrazení seskupuje data protokolu ze všech tří protokolů nejprve podle ID požadavku klienta, potom podle zdrojového souboru protokolu (nebo **modulu** v analyzátoru zpráv). V tomto zobrazení můžete přejít k podrobnostem konkrétního ID požadavku klienta a zobrazit data ze všech tří souborů protokolu pro toto ID požadavku klienta.

Následující obrázek znázorňuje toto zobrazení rozložení použité na ukázková data protokolu se zobrazenou podmnožinou sloupců. Můžete vidět, že pro konkrétní ID požadavku klienta analysis grid zobrazuje data z protokolu klienta, protokolu serveru a trasování sítě.

![Rozložení Zobrazení úložiště Azure](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Různé soubory protokolu mají různé sloupce, takže když jsou data z více souborů protokolu zobrazena v mřížce analýzy, některé sloupce nemusí obsahovat žádná data pro daný řádek. Například na obrázku výše řádky protokolu klienta nezobrazují žádná data pro **časové razítko**, **TimeElapsed**, **Zdroj**a **Cíl** sloupce, protože tyto sloupce neexistují v protokolu klienta, ale existují v síti trasování. Podobně sloupec **časové razítko** zobrazuje data časového razítka z protokolu serveru, ale žádná data se zobrazí pro sloupce **TimeElapsed**, **Source**a **Destination,** které nejsou součástí protokolu serveru.
>
>

Kromě použití rozložení zobrazení Azure Storage můžete také definovat a uložit vlastní rozložení zobrazení. Můžete vybrat další požadovaná pole pro seskupení dat a uložit seskupení také jako součást vlastního rozložení.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Použití barevných pravidel v mřížce analýzy

Prostředky úložiště také obsahují barevná pravidla, která nabízejí vizuální prostředky k identifikaci různých typů chyb v analýze mřížky. Předdefinovaná pravidla barev platí pro chyby PROTOKOLU HTTP, takže se zobrazí pouze pro protokol serveru a trasování v síti.

Pokud chcete použít pravidla barev, vyberte na pásu karet panelu nástrojů **položku Pravidla barev.** V nabídce se zobrazí pravidla barev azure storage. Pro kurz vyberte **chyby klienta (StatusCode mezi 400 a 499)**, jak je znázorněno na obrázku níže.

![Rozložení Zobrazení úložiště Azure](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Kromě použití pravidel barev azure storage můžete také definovat a uložit vlastní pravidla barev.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Seskupení a filtrování dat protokolu za účelem nalezení chyb v rozsahu 400

Dále seskupili a filtrovat data protokolu najít všechny chyby v rozsahu 400.

1. Vyhledejte sloupec **StatusCode** v analýze, klikněte pravým tlačítkem myši na záhlaví sloupce a vyberte **Seskupit**.
2. Dále seskupte ve sloupci **ClientRequestId.** Uvidíte, že data v analýze mřížky je nyní uspořádána podle stavového kódu a ID žádosti klienta.
3. Pokud nástroj Filtr zobrazení ještě není zobrazen, zobrazí okno nástroje Filtr zobrazení. Na pásu karet panelu nástrojů vyberte **Nástroj windows**a potom **zobrazit filtr**.
4. Chcete-li filtrovat data protokolu tak, aby se zobrazily pouze chyby v rozsahu 400, přidejte do okna **Zobrazit filtr** následující kritéria filtru a klepněte na **tlačítko Použít**:

    `(AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)`

Na obrázku níže jsou uvedeny výsledky tohoto seskupení a filtru. Rozbalení **pole ClientRequestID** pod seskupení pro stavový kód 409, například ukazuje operaci, která vyústila v tento stavový kód.

![Rozložení Zobrazení úložiště Azure](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Po použití tohoto filtru uvidíte, že řádky z protokolu klienta jsou vyloučeny, protože protokol klienta neobsahuje sloupec **StatusCode.** Nejprve zkontrolujeme protokoly trasování serveru a sítě, abychom našli chyby 404, a pak se vrátíme do protokolu klienta a prozkoumáme klientské operace, které k nim vedly.

> [!NOTE]
> Můžete filtrovat na **sloupec StatusCode** a stále zobrazovat data ze všech tří protokolů, včetně protokolu klienta, pokud přidáte výraz do filtru, který obsahuje položky protokolu, kde je stavový kód null. Chcete-li vytvořit tento výraz filtru, použijte:
>
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
>
> Tento filtr vrátí všechny řádky z protokolu klienta a pouze řádky z protokolu serveru a protokolu HTTP, kde je stavový kód větší než 400. Pokud ji použijete na rozložení zobrazení seskupené podle ID požadavku klienta a modulu, můžete prohledávat nebo procházet položky protokolu a najít ty, kde jsou reprezentovány všechny tři protokoly.

### <a name="filter-log-data-to-find-404-errors"></a>Filtrování dat protokolu najít 404 chyb

Prostředky úložiště zahrnují předdefinované filtry, které můžete použít k zúžení dat protokolu a najít chyby nebo trendy, které hledáte. Dále použijeme dva předdefinované filtry: jeden, který filtruje protokoly trasování serveru a sítě pro chyby 404 a jeden, který filtruje data v zadaném časovém rozsahu.

1. Pokud nástroj Filtr zobrazení ještě není zobrazen, zobrazí okno nástroje Filtr zobrazení. Na pásu karet panelu nástrojů vyberte **Nástroj windows**a potom **zobrazit filtr**.
2. V okně Zobrazit filtr vyberte **Knihovna**a vyhledejte `Azure Storage` filtry Azure Storage. Vyberte filtr pro **zprávy 404 (Nenalezeno) ve všech protokolech**.
3. Znovu zobrazte nabídku **Knihovna** a vyhledejte a vyberte **filtr globálního času**.
4. Upravte časová razítka zobrazená ve filtru na oblast, kterou chcete zobrazit. To pomůže zúžit rozsah dat k analýze.
5. Filtr by měl vypadat podobně jako v níže uvedeném příkladu. Kliknutím na **Použít** aplikujte filtr na mřížku analýzy.

    `((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)`

    ![Rozložení Zobrazení úložiště Azure](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analýza dat protokolu

Nyní, když jste seskupili a filtrovali data, můžete zkontrolovat podrobnosti o jednotlivých požadavcích, které generovaly chyby 404. V aktuálním rozložení zobrazení jsou data seskupena podle ID požadavku klienta a potom podle zdroje protokolu. Vzhledem k tomu, že filtrujeme požadavky, kde pole StatusCode obsahuje 404, zobrazí se pouze data trasování serveru a sítě, nikoli data protokolu klienta.

Na obrázku níže je uveden konkrétní požadavek, kde operace získat objekt blob přinesl 404, protože objekt blob neexistoval. Všimněte si, že některé sloupce byly odebrány ze standardního zobrazení za účelem zobrazení příslušných dat.

![Filtrované protokoly trasování a sítě](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Dále budeme korelovat toto ID požadavku klienta s daty protokolu klienta, abychom zjistili, jaké akce klient podnikal, když došlo k chybě. Můžete zobrazit nové zobrazení mřížky analýzy pro tuto relaci a zobrazit data protokolu klienta, která se otevře na druhé kartě:

1. Nejprve zkopírujte hodnotu pole **ClientRequestId** do schránky. To lze provést výběrem jednoho řádku, vyhledáním pole **ClientRequestId,** kliknutím pravým tlačítkem myši na hodnotu dat a výběrem **možnosti Kopírovat "ClientRequestId"**.
2. Na pásu karet panelu nástrojů vyberte **Nový prohlížeč**a pak vyberte **Analysis Grid,** abyste otevřeli novou kartu. Nová karta zobrazuje všechna data v souborech protokolu bez seskupení, filtrování nebo pravidel barev.
3. Na pásu karet panelu nástrojů vyberte **Zobrazit rozložení**a v části **Úložiště Azure** vyberte Všechny **sloupce klienta .NET.** Toto rozložení zobrazení zobrazuje data z protokolu klienta, stejně jako protokoly trasování serveru a sítě. Ve výchozím nastavení je seřazeno podle sloupce **MessageNumber.**
4. Dále vyhledejte v protokolu klienta ID požadavku klienta. Na pásu karet panelu nástrojů vyberte **najít zprávy**a v poli **Najít** zadejte vlastní filtr na ID požadavku klienta. Použijte tuto syntaxi pro filtr a zadejte vlastní ID požadavku klienta:

    `*ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"`

Analyzátor zpráv vyhledá a vybere první položku protokolu, kde kritéria hledání odpovídají ID požadavku klienta. V protokolu klienta existuje několik položek pro každé ID požadavku klienta, takže je můžete chtít seskupit do pole **ClientRequestId,** abyste je usnadnili jejich zobrazení. Na obrázku níže jsou uvedeny všechny zprávy v protokolu klienta pro zadané ID požadavku klienta.

![Protokol klienta zobrazující 404 chyb](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Pomocí dat zobrazených v rozloženích zobrazení na těchto dvou kartách můžete analyzovat data požadavku a zjistit, co mohlo chybu způsobit. Můžete se také podívat na požadavky, které předcházely tomuto, abyste zjistili, zda předchozí událost mohla vést k chybě 404. Můžete například zkontrolovat položky protokolu klienta předcházející tomuto ID požadavku klienta a určit, zda byl objekt blob odstraněn nebo zda byla chyba způsobena klientskou aplikací volající rozhraní API CreateIfNotExists v kontejneru nebo objektu blob. V protokolu klienta najdete adresu objektu blob v poli **Popis.** v protokolech trasování serveru a sítě se tyto informace zobrazí v poli **Souhrn.**

Jakmile znáte adresu objektu blob, který přinesl chybu 404, můžete dále prozkoumat. Pokud hledáte položky protokolu pro další zprávy spojené s operacemi na stejném objektu blob, můžete zkontrolovat, zda klient dříve odstraněné entity.

## <a name="analyze-other-types-of-storage-errors"></a>Analýza jiných typů chyb úložiště

Nyní, když jste obeznámeni s použitím Analyzátorzpráv k analýze dat protokolu, můžete analyzovat další typy chyb pomocí rozložení zobrazení, pravidla barev a vyhledávání a filtrování. V následujících tabulkách jsou uvedeny některé problémy, se kterými se můžete setkat, a kritéria filtru, která můžete použít k jejich vyhledání. Další informace o konstrukci filtrů a jazyk filtrování analyzátoru zpráv naleznete v [tématu Filtrování dat zpráv](https://technet.microsoft.com/library/jj819365.aspx).

| Vyšetřovat... | Použít výraz filtru... | Výraz se vztahuje na protokol (klient, server, síť, vše) |
| --- | --- | --- |
| Neočekávaná zpoždění při doručování zpráv ve frontě |AzureStorageClientDotNetV4.Description obsahuje "Opakování neúspěšné operace." |Klient |
| Http Zvýšení PercentThrottlingError |http. Response.StatusCode == 500 &#124;&#124; HTTP. Response.StatusCode == 503 |Network (Síť) |
| Zvýšení chyby PercentTimeoutError |http. Response.StatusCode == 500 |Network (Síť) |
| Zvýšení percenttimeouterror (vše) |*StatusCode == 500 |Všechny |
| Zvýšení chyby v síti PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level < 2 |Klient |
| Zprávy HTTP 403 (Zakázáno) |http. Response.StatusCode == 403 |Network (Síť) |
| Zprávy HTTP 404 (Nenalezeno) |http. Response.StatusCode == 404 |Network (Síť) |
| 404 (vše) |*StatusCode == 404 |Všechny |
| Problém s autorizací sdíleného přístupového podpisu (SAS) |AzureStorageLog.RequestStatus == "Chyba autorizace SAS" |Network (Síť) |
| Zprávy HTTP 409 (Konflikt) |http. Response.StatusCode == 409 |Network (Síť) |
| 409 (vše) |*StatusCode == 409 |Všechny |
| Nízké PercentSuccess nebo analytické položky protokolu mají operace se stavem transakce ClientOtherErrors |AzureStorageLog.RequestStatus == "ClientOtherError" |Server |
| Nagle Varování |((AzureStorageLog.EndToEndLatencyMS – AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) a (AzureStorageLog.RequestPacketSize <1460) a (AzureStorageLog.EndToEndLatencyMS – AzureStorageLog.ServerLatencyMS >= 200) |Server |
| Rozsah času v protokolech serveru a sítě |#Timestamp >= 2014-10-20T16:36:38 a #Timestamp <= 2014-10-20T16:36:39 |Server, Síť |
| Rozsah času v protokolech serveru |AzureStorageLog.Timestamp >= 2014-10-20T16:36:38 a AzureStorageLog.Timestamp <= 2014-10-20T16:36:39 |Server |

## <a name="next-steps"></a>Další kroky

Další informace o řešení potíží s komplexními scénáři ve službě Azure Storage najdete v těchto zdrojích informací:

* [Monitorování, diagnostika a řešení problémů s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)
* [Storage Analytics](https://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md)
* [Provozní příručka nástroje Microsoft Message Analyzer](https://technet.microsoft.com/library/jj649776.aspx)
