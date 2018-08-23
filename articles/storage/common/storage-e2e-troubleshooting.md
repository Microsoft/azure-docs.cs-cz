---
title: Řešení potíží s Azure Storage s Diagnostika a Message Analyzer | Dokumentace Microsoftu
description: Kurz demonstrace začátku do konce řešení potíží s Azure Storage Analytics, AzCopy a Microsoft Message Analyzer
services: storage
author: tamram
ms.service: storage
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: tamram
ms.component: common
ms.openlocfilehash: 41e7f5b4c36ad0bfed0ef5a9a31565474cf4d823
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "42054208"
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Začátku do konce řešení problémů pomocí metrik Azure Storage a protokolování, AzCopy a analyzátoru zpráv
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnostika a řešení potíží je klíčové dovednosti pro vytvoření a podpoře klientských aplikací s Microsoft Azure Storage. Z důvodu distribuovaná povaha aplikací Azure může být složitější než v tradičních prostředí Diagnostika a řešení potíží s chybami a problémy s výkonem.

V tomto kurzu jsme ukazují, jak identifikovat určité chyby, které může mít vliv na výkon a řešení těchto chyb od začátku do konce pomocí nástroje poskytované společností Microsoft a Azure Storage k optimalizaci klientské aplikace.

Tento kurz obsahuje praktické zkoumání scénáře řešení potíží začátku do konce. Podrobné koncepční pokyny k řešení potíží s aplikacemi Azure storage najdete v článku [monitorování, Diagnostika a řešení problémů s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Nástroje pro řešení potíží s aplikacemi Azure Storage
Řešení potíží s klientským aplikacím pomocí služby Microsoft Azure Storage, můžete kombinaci nástroje k určení, kdy došlo k problému a co mohou být příčinou problému. Mezi tyto nástroje patří:

* **Azure Storage Analytics**. [Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) poskytuje metriky a protokolování pro službu Azure Storage.
  
  * **Metriky úložiště** sleduje metriku transakcí a metriky kapacity účtu úložiště. Pomocí metrik, můžete určit, jaký je výkon vaší aplikace podle širokou škálu různých opatření. Zobrazit [tabulkovému schématu metrik Storage Analytics](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) pro další informace o typech metriky sledován pomocí funkce Storage Analytics.
  * **Protokolování úložiště** zaznamená každý požadavek do služby Azure Storage do protokolu na straně serveru. V protokolu se sleduje podrobná data pro každý požadavek, včetně operaci provést, stav provozu a informace o latenci. Zobrazit [formát Log Analytics úložiště](/rest/api/storageservices/Storage-Analytics-Log-Format) Další informace o požadavku a odpovědi data, která jsou zapsána do protokoly Storage Analytics.

* **Azure portal**. Můžete nakonfigurovat metrik a protokolování pro váš účet úložiště v [webu Azure portal](https://portal.azure.com). Můžete také zobrazit tabulky a grafy, které ukazují, jaký je výkon vaší aplikace v čase a nakonfigurovat výstrahy, které vás upozorní, pokud aplikace provádí odlišně, než se očekávalo zadané metriky.
  
    Zobrazit [monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md) informace o konfiguraci monitorování na webu Azure Portal.
* **AzCopy**. Protokoly serveru pro službu Azure Storage jsou uložené jako objekty BLOB, takže můžete pomocí AzCopy můžete kopírovat objekty BLOB protokolů do místního adresáře pro analýzu pomocí Microsoft Message Analyzer. Zobrazit [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md) Další informace o AzCopy.
* **Microsoft Message Analyzer**. Message Analyzer je nástroj, který využívá soubory protokolu a zobrazí data protokolu ve formátu visual, který usnadňuje filter, search a data protokolu pro skupiny do užitečné sad, které můžete použít k analýze chyb a problémů s výkonem. Zobrazit [operační příručce k Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx) Další informace o Message Analyzer.

## <a name="about-the-sample-scenario"></a>Informace o ukázkovém scénáři
Pro účely tohoto kurzu prozkoumáme scénář, ve kterém metrik Azure Storage určuje frekvence nízká Procento úspěšných pokusů pro aplikaci, která volá úložiště Azure. Metrika frekvence nízká Procento úspěšných pokusů (zobrazené jako **PercentSuccess** v [webu Azure portal](https://portal.azure.com) a v tabulkách metriky) sleduje operace, která sice podaří, ale který vrátit stavový kód HTTP, která je větší než 299. V souborech protokolů úložiště na straně serveru, se zaznamenávají tyto operace se stavem transakce **ClientOtherErrors**. Další podrobnosti o metriku s nízkou Procento úspěšných pokusů najdete v tématu [metrika ukazuje nízkou PercentSuccess nebo položky log analytics mají operací se stavem transakce ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Azure Storage operace může vrátit stavové kódy HTTP větší než 299 jako součást své normální funkce. Ale tyto chyby v některých případech označují, že je možné optimalizovat klientské aplikace za účelem vylepšení výkonu.

V tomto scénáři považujeme frekvence nízká Procento úspěšných pokusů jako cokoli pod 100 %. Můžete zvolit různé metriky úroveň, ale podle vašich potřeb. Doporučujeme vám, že během testování vaší aplikace, je vytvořit tolerance směrný plán pro vaše klíčové metriky výkonu. Například lze určit, na základě testování se, že vaše aplikace by měla mít míry konzistentní Procento úspěšných pokusů o 90 % nebo 85 %. Pokud vaše data metriky ukazuje, že aplikace je odchylují od tohoto čísla, pak můžete zjistit, co může být příčinou zvýšení.

Pro náš vzorový scénář když jsme jste vytvořili, že míra metrika Procento úspěšných pokusů je nižší než 100 %, prozkoumáme protokoly a hledat chyby, které odpovídají metriky a můžete zjistit, co je příčinou nižší procentuální úspěšnosti. Podíváme se na chyby v oblasti 400. Potom budete lépe prozkoumáme chyby 404 (Nenalezeno).

### <a name="some-causes-of-400-range-errors"></a>Některé příčiny chyby 400 rozsahu
Následující příklady jsou uvedeny vzorky některé chyby 400-range pro požadavky na úložiště objektů Blob v Azure a jejich možných příčin. Některé z těchto chyb, stejně jako chyby v oblasti 300 a 500 rozsah může přispět k nízké procentuální úspěšnosti.

Poznámka: následující seznamy jsou daleko od dokončení. Zobrazit [stavové a chybové kódy](http://msdn.microsoft.com/library/azure/dd179382.aspx) na webu MSDN pro podrobnosti o chybách obecné služby Azure Storage a o chybách, které jsou specifické pro každou ze služeb úložiště.

**Příklady stavový kód 404 (Nenalezeno)**

Nastane, pokud selže operace čtení pro kontejner nebo objekt blob, protože nebyl nalezen objekt blob nebo kontejneru.

* Nastane, pokud kontejner nebo objekt blob se odstranil jiný klient před tuto žádost.
* Nastane, pokud používáte volání rozhraní API, která vytvoří kontejner nebo objekt blob po kontrole, jestli existuje. Rozhraní API CreateIfNotExists uskutečnit volání HEAD nejprve kontroly existence kontejneru nebo objektu blob Pokud neexistuje, vrátí chybu 404 a poté je proveden zápis kontejner nebo objekt blob druhé volání PUT.

**Stavový kód 409 (konflikt) příklady**

* Nastane, pokud použijete k vytvoření nový kontejner nebo objekt blob, bez provedení kontroly existence nejprve vytvořit rozhraní API a kontejner nebo objekt blob s tímto názvem již existuje.
* Nastane, pokud se právě odstraňuje kontejneru a zkusíte vytvořit nový kontejner se stejným názvem, před dokončením operace odstranění.
* Nastane, pokud zadáte zapůjčení na kontejner nebo objekt blob, a je už existuje zapůjčení.

**Stavový kód 412 (Předběžná podmínka je neplatná) příklady**

* Nastane, pokud nebyla splněna podmínka uvedená v podmíněnou hlavičku.
* Nastane, pokud zadané ID zapůjčení se neshoduje s ID zapůjčení na kontejner nebo objekt blob.

## <a name="generate-log-files-for-analysis"></a>Generovat soubory protokolů pro analýzu
V tomto kurzu použijeme Message Analyzer pro práci s tři různé typy souborů protokolu, i když můžete se rozhodnout pracovat s některou z těchto:

* **Protokolu serveru**, který se vytvoří při povolení protokolování služby Azure Storage. Protokol serveru obsahuje údaje o každé operace s názvem proti jedné služby Azure Storage – objekt blob, fronty, tabulky a soubor. Server protokolu označuje, které operace byla volána a jaký kód stavu je vrácený, jakož i další podrobnosti o požadavku a odpovědi.
* **Protokol klienta .NET**, který se vytvoří při povolení protokolování na straně klienta z v rámci vaší aplikace .NET. Protokol klienta obsahuje podrobné informace o jak klient připraví žádosti a obdrží a zpracuje odpověď.
* **Protokol trasování sítě HTTP**, které shromažďuje údaje o dat požadavků a odpovědí HTTP/HTTPS, včetně pro operace využívající službu Azure Storage. V tomto kurzu vytvoříme trasování v síti prostřednictvím nástroje Message Analyzer.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurace protokolování na straně serveru a metriky
Nejprve jsme budete muset nakonfigurovat protokolování služby Azure Storage a metriky, takže máme data z klientské aplikace k analýze. Protokolování a metrik v mnoha různými způsoby – můžete nakonfigurovat přes [webu Azure portal](https://portal.azure.com), pomocí prostředí PowerShell, nebo prostřednictvím kódu programu. Zobrazit [povolení metrik Storage a zobrazení dat metrik](http://msdn.microsoft.com/library/azure/dn782843.aspx) a [povolení protokolování úložiště a přístup k datům protokolů](http://msdn.microsoft.com/library/azure/dn782840.aspx) na webové stránce MSDN podrobnosti o konfiguraci protokolování a metriky.

**Prostřednictvím portálu Azure portal**

Ke konfiguraci účtu pomocí protokolování a metriky pro úložiště [webu Azure portal](https://portal.azure.com), postupujte podle pokynů na adrese [monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md).

> [!NOTE]
> Není možné nastavit minutové metriky pomocí webu Azure portal. Doporučujeme však, že je nastavit pro účely tohoto kurzu a prozkoumání problémů s výkonem s vaší aplikací. Můžete nastavit minutové metriky pomocí Powershellu, jak je znázorněno níže, nebo programově pomocí klientskou knihovnu pro úložiště.
> 
> Všimněte si, že na webu Azure portal nemůže zobrazit minutové metriky, pouze hodinovou metriku.
> 
> 

**Via PowerShell**

Začínáme s prostředím PowerShell for Azure, najdete v článku [instalace a konfigurace Azure Powershellu](/powershell/azure/overview).

1. Použití [Add-AzureAccount](/powershell/module/servicemanagement/azure/add-azureaccount?view=azuresmps-3.7.0) rutiny pro přidání účtu uživatele Azure do okna prostředí PowerShell:
   
    ```powershell
    Add-AzureAccount
    ```

2. V **Přihlaste se k Microsoft Azure** okně zadejte e-mailovou adresu a heslo spojené s vaším účtem. Azure přihlašovací údaje ověří, uloží je a pak zavře okno.
3. Nastavte výchozí účet úložiště na účet úložiště, který používáte pro tento kurz spuštěním těchto příkazů v okně Powershellu:
   
    ```powershell
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. Povolení protokolování úložiště pro službu Blob service:
   
    ```powershell
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```

5. Zapnutí metrik úložiště pro službu Blob service, nezapomeňte nastavit **- MetricsType** k `Minute`:
   
    ```powershell
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>Konfigurace protokolování na straně klienta .NET
Pokud chcete nakonfigurovat protokolování na straně klienta pro aplikace .NET, povolte diagnostiku .NET v konfiguračním souboru aplikace (web.config nebo app.config). Zobrazit [Client-side protokolování pomocí klientské knihovny úložiště .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx) a [Client-side protokolování pomocí služby Microsoft Azure Storage SDK pro Javu](http://msdn.microsoft.com/library/azure/dn782844.aspx) na webové stránce MSDN o.

V protokolu na straně klienta obsahuje podrobné informace o jak klient připraví žádosti a obdrží a zpracuje odpověď.

Klientská knihovna pro úložiště ukládá data na straně klienta protokolu v umístění zadaném v konfiguračním souboru aplikace (web.config nebo app.config).

### <a name="collect-a-network-trace"></a>Shromažďovat trasování sítě
Message Analyzer můžete použít ke shromažďování trasování v síti HTTP/HTTPS, když klientská aplikace běží. Message Analyzer používá [Fiddler](http://www.telerik.com/fiddler) na back-endu. Než budete shromažďovat trasování sítě, doporučujeme, abyste nakonfigurovali aplikaci Fiddler k zaznamenání nešifrovaný provoz protokolu HTTPS:

1. Nainstalujte [Fiddler](http://www.telerik.com/download/fiddler).
2. Spuštění Fiddleru.
3. Vyberte **nástroje | Možnosti fiddleru**.
4. V dialogovém okně Možnosti, ujistěte se, že **zachycení umožňuje připojení HTTPS** a **dešifrování provozu HTTPS** jsou zaškrtnuta, jak je znázorněno níže.

![Konfigurovat možnosti Fiddleru](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Pro tento kurz shromažďovat a nejdříve uložte trasování sítě v Message Analyzer a pak vytvořte relaci analýzy k analýze trasování a protokoly. Shromažďování trasování sítě v Message Analyzer:

1. V analyzátoru zpráv, vyberte **soubor | Rychlé trasování | Nešifrované HTTPS**.
2. Trasování bude zahájeno okamžitě. Vyberte **Zastavit** Zastavit trasování tak, že jsme ho nakonfigurovat pro trasování úložiště pouze provoz.
3. Vyberte **upravit** Upravit relaci trasování.
4. Vyberte **konfigurovat** odkaz na pravé straně **Microsoft-Pef WebProxy** poskytovatele trasování událostí pro Windows.
5. V **Upřesnit nastavení** dialogového okna, klikněte na tlačítko **poskytovatele** kartu.
6. V **filtrů názvu hostitele** uveďte vašich koncových bodů úložiště, oddělené mezerami. Například můžete zadat koncové body takto: Změnit `storagesample` k názvu účtu úložiště:

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Ukončete dialogové okno a klikněte na tlačítko **restartovat** zahajte shromažďování trasování pomocí filtru název hostitele na místě, tak, aby pouze síťový provoz služby Azure Storage je součástí trasování.

> [!NOTE]
> Po dokončení shromažďování trasování v síti, důrazně doporučujeme, můžete se vrátit nastavení, která může jste změnili v aplikaci Fiddler k dešifrování provozu HTTPS. V dialogovém okně Možnosti Fiddleru zrušte výběr **zachycení umožňuje připojení HTTPS** a **dešifrování provozu HTTPS** zaškrtávací políčka.
> 
> 

Zobrazit [díky funkcím, které trasování sítě](http://technet.microsoft.com/library/jj674819.aspx) na webu Technet pro další podrobnosti.

## <a name="review-metrics-data-in-the-azure-portal"></a>Zkontrolujte data metrik na webu Azure Portal
Jakmile vaše aplikace byla spuštěna pro určitou dobu, můžete zkontrolovat grafů metrik, které se zobrazují v [webu Azure portal](https://portal.azure.com) sledovat, jak vaši službu vede.

Nejprve přejděte do účtu úložiště na webu Azure Portal. Ve výchozím nastavení, monitorování graf s **procento úspěšnosti** metrika se zobrazí v okně účtu. Pokud jste upravili dříve grafu pro zobrazení různých metrik, přidejte **procento úspěšnosti** metriku.

Nyní uvidíte **procento úspěšnosti** v monitorování grafu, spolu s dalšími metrikami jste možná přidali. Ve scénáři, které nám budete dále prozkoumat prostřednictvím analýzy protokolů v Message Analyzer procento úspěšnosti je trochu nižší než 100 %.

Podrobné informace o přidání a přizpůsobení grafů metrik najdete v článku [přizpůsobení grafů metrik](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> Může trvat nějakou dobu pro vaše data metriky se zobrazí na webu Azure Portal po povolení metrik úložiště. Je to proto hodinové metriky pro do předchozí hodiny nejsou zobrazeny na webu Azure Portal, dokud neuplyne do aktuální hodiny. Minutové metriky navíc nejsou aktuálně zobrazené na webu Azure Portal. Takže v závislosti na při povolení metriky, může trvat až dvě hodiny, pokud chcete zobrazit data metrik.
> 
> 

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Pomocí AzCopy můžete kopírovat protokoly serveru do místního adresáře
Úložiště Azure zapíše data protokolu serveru pro objekty BLOB, zatímco metriky se zapisují do tabulek. Objekty BLOB protokolů jsou k dispozici v dobře známé `$logs` kontejneru účtu úložiště. Objekty BLOB protokolů jsou pojmenovány hierarchicky za rok, měsíc, den a hodina, takže máte jednoduchý přístup k rozsahu dobu, po kterou chcete prozkoumat. Například v `storagesample` je účet, kontejner pro objekty BLOB protokolů pro 01/02/2015 z 8 až 9 am, `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Jednotlivé objekty BLOB v tomto kontejneru jsou postupně pojmenované, počínaje `000000.log`.

Nástroj příkazového řádku AzCopy můžete stahovat tyto soubory protokolu na straně serveru do umístění podle vašeho výběru v místním počítači. Například následující příkaz můžete stáhnout soubory protokolu pro operace objektů blob, které proběhly v 2. ledna 2015 do složky `C:\Temp\Logs\Server`; nahraďte `<storageaccountname>` s názvem účtu úložiště a `<storageaccountkey>` přístupovým klíčem vašeho účtu :

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
AzCopy je k dispozici ke stažení [soubory ke stažení Azure](https://azure.microsoft.com/downloads/) stránky. Podrobnosti o použití nástroje AzCopy najdete v tématu [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md).

Další informace o stahování protokolů na straně serveru, naleznete v tématu [stáhnout protokolování úložiště dat protokolu](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Umožňuje analyzovat data protokolů Microsoft Message Analyzer
Microsoft Message Analyzer je nástroj pro digitalizaci, zobrazení a analýza protokolů přenosů, události a další zprávy systému nebo aplikace v scénáře řešení potíží a diagnostiku pro zasílání zpráv. Message Analyzer také umožňuje načíst, agregaci a analýzu dat z protokolu a uložit soubory trasování. Další informace o analyzátoru zpráv, najdete v části [operační příručce k Microsoft Message Analyzer](http://technet.microsoft.com/library/jj649776.aspx).

Message Analyzer zahrnuje prostředky pro službu Azure Storage, který vám pomůže analyzovat serveru, klienta a protokoly sítě. V této části probereme, jak používat tyto nástroje k vyřešení problému s nízkou Procento úspěšných pokusů v protokolech úložiště.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Stáhněte a nainstalujte nástroje Message Analyzer a prostředky úložiště Azure
1. Stáhněte si [Message Analyzer](http://www.microsoft.com/download/details.aspx?id=44226) z Microsoft Download Center a spusťte instalační program.
2. Spuštění nástroje Message Analyzer.
3. Z **nástroje** nabídce vyberte možnost **správce inventáře**. V **správce inventáře** dialogového okna, vyberte **stáhne**, potom vyfiltrujte **služby Azure Storage**. Úložiště prostředků Azure, uvidíte, jak je znázorněno na obrázku níže.
4. Klikněte na tlačítko **synchronizace všechny položky zobrazí** instalace prostředků úložiště Azure. K dispozici prostředky patří:
   * **Pravidla služby Azure Storage barva:** pravidel barvy úložiště Azure umožňují definovat zvláštní filtry, které používají barvu, text a písmo styly zvýrazněte zprávy, které obsahují konkrétní informace o trasování.
   * **Azure Storage grafy:** grafy služby Azure Storage jsou předdefinované grafů, které grafu data protokolu serveru. Všimněte si, že určený grafy služby Azure Storage v tuto chvíli může pouze načtení protokolu serveru do mřížky analýzy.
   * **Azure Storage analyzátory:** analyzátory služby Azure Storage analyzovat klienta služby Azure Storage, server a protokoly HTTP mohla zobrazit v mřížce analýzy.
   * **Azure Storage filtry:** filtry služby Azure Storage jsou předdefinované kritéria, které můžete použít k dotazování dat v mřížce analýzy.
   * **Azure Storage zobrazení rozložení:** rozložení zobrazení služby Azure Storage jsou předdefinované rozložení a seskupení v mřížce analýzy.
5. Po instalaci prostředky, restartujte Message Analyzer.

![Správce inventáře analyzátoru zpráv](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Nainstalujte, všechny prostředky služby Azure Storage pro účely tohoto kurzu.
> 
> 

### <a name="import-your-log-files-into-message-analyzer"></a>Naimportujte soubory protokolu nástroje Message Analyzer
Můžete importovat všechny své uložené soubory protokolů (na straně serveru, na straně klienta a sítě) v rámci jedné relace Microsoft Message Analyzer pro analýzu.

1. Na **souboru** klikněte na tlačítko nabídky v Microsoft Message Analyzer **novou relaci**a potom klikněte na tlačítko **prázdné relace**. V **novou relaci** dialogové okno, zadejte název pro relaci analýzy. V **podrobnosti relace** panelu, klikněte na **soubory** tlačítko.
2. Pokud chcete načíst data trasování sítě generovaných Message Analyzer, klikněte na **přidat soubory**, přejděte do umístění, kam jste uložili soubor .matp z webové relace trasování, vyberte soubor .matp a klikněte na tlačítko **otevřete**.
3. Pokud chcete načíst data protokolu na straně serveru, klikněte na **přidat soubory**, přejděte do umístění, kam jste stáhli protokoly na straně serveru, vyberte soubory protokolu pro časový rozsah, který chcete analyzovat a klikněte na tlačítko **otevřít**. Potom v **podrobnosti relace** panelu, nastavte **konfigurace Log Text** rozevíracího seznamu pro každý soubor protokolu na straně serveru k **AzureStorageLog** zajistit tento Microsoft Message Analyzer může správně analyzovat soubor protokolu.
4. Pokud chcete načíst data protokolu na straně klienta, klikněte na **přidat soubory**, přejděte do umístění, kam jste uložili protokoly na straně klienta, vyberte soubory protokolu, které chcete analyzovat a klikněte na tlačítko **otevřít**. Potom v **podrobnosti relace** panelu, nastavte **konfigurace Log Text** rozevíracího seznamu pro každý soubor protokolu na straně klienta, aby **AzureStorageClientDotNetV4** zajistit, aby Microsoft Message Analyzer může správně analyzovat soubor protokolu.
5. Klikněte na tlačítko **Start** v **novou relaci** dialog načíst a analyzovat data protokolu. Data protokolu se zobrazí v mřížce analýzy analyzátoru zpráv.

Následující obrázek ukazuje příklad relace nakonfigurovanou serveru, klienta a soubory protokolu trasování sítě.

![Konfigurace relace analyzátoru zpráv](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Všimněte si, že Message Analyzer načte soubory protokolů do paměti. Pokud máte velké sady dat protokolu, můžete filtrovat zajistí nejlepší výkon ze Message Analyzer.

Nejprve určit časový rámec, který vás zajímá revizí a udržovat co nejmenší tohoto časového rámce. V mnoha případech můžete zkontrolovat období minut nebo hodin maximálně. Importujte nejmenší sadu protokolů, které vyhoví vašim potřebám.

Pokud máte velké množství dat protokolu, můžete chtít určit relace filtr data protokolů teprve potom ho načíst. V **filtru relace** vyberte **knihovny** tlačítko Vybrat předdefinovaný filtr, například zvolte **globální doba filtr I** ze služby Azure Storage filtry pro filtrování v časovém intervalu. Potom můžete upravit kritéria filtru pro zadejte počáteční a koncovou časové razítko pro interval, po který chcete zobrazit. Můžete také filtrovat na konkrétní stavový kód; Můžete například načíst pouze ty položky protokolu, kde je stavový kód 404.

Další informace o importu dat protokolu do Microsoft Message Analyzer, naleznete v tématu [načítání dat zprávy](http://technet.microsoft.com/library/dn772437.aspx) na webu TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>ID žádosti klienta můžete korelovat data protokolů
Klientská knihovna pro úložiště Azure automaticky vygeneruje ID žádosti klienta jedinečný pro každý požadavek. Tato hodnota jsou zapsány do protokolu klienta, serveru protokolu a trasování sítě, ve kterém můžete korelovat data mezi všechny tři protokoly v rámci nástroje Message Analyzer. Zobrazit [ID žádosti klienta](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) pro další informace o klientovi požádat o ID.

Následující části popisují způsob použití předem nakonfigurované a vlastní rozložení zobrazení ke korelaci a seskupení dat podle ID požadavku klienta.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Vyberte zobrazení rozložení pro zobrazení v mřížce analýzy
Prostředky úložiště pro Message Analyzer zahrnují zobrazení rozložení úložiště Azure, které jsou předem nakonfigurovaná zobrazení, které můžete použít k zobrazení dat s užitečné seskupení a sloupce pro různé scénáře. Můžete také vytvářet vlastní zobrazení rozložení a uložit pro další použití.

Obrázek níže ukazuje **rozložení zobrazení** nabídky, k dispozici tak, že vyberete **rozložení zobrazení** pásu panelu nástrojů. Zobrazit rozložení pro službu Azure Storage jsou seskupené podle **služby Azure Storage** uzlu v nabídce. Můžete vyhledat `Azure Storage` do vyhledávacího pole filtrovat ve službě Azure Storage zobrazit pouze rozložení. Můžete také vybrat hvězdičku vedle rozložení zobrazení ji označit jako oblíbenou a zobrazit je v horní nabídce.

![Nabídka rozložení zobrazení](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Než začneme, vyberte **seskupené podle ID žádosti klienta a modul**. Toto zobrazení rozložení skupin můžete vytvářet protokoly dat ze všech tří protokolů nejprve podle ID žádosti klienta a pak podle zdrojového souboru protokolu (nebo **modulu** v Message Analyzer). V tomto zobrazení můžete přejít na ID žádosti klienta konkrétní a zobrazit data ze všech vygenerovaných souborů tři protokolu pro tento požadavek klienta ID.

Obrázek níže ukazuje toto rozložení zobrazení použitý na ukázková data protokolu obsahující jenom určitou podmnožinu sloupců zobrazených. Uvidíte, že pro ID žádosti o konkrétního klienta, mřížky Analysis zobrazuje data z klienta protokolu, protokolů serveru a trasování sítě.

![Rozložení zobrazení služby Azure Storage](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Různých protokolových souborech mají různé sloupce, takže když data z více souborů protokolu se zobrazí v mřížce analýzy, některé sloupce nesmí obsahovat žádná data pro daný řádek. Například na obrázku výše, řádky protokolu klienta zobrazovat žádná data pro **časové razítko**, **TimeElapsed**, **zdroj**, a **cílové**sloupců, protože tyto sloupce neexistují v protokolu klienta, ale existují v trasování sítě. Podobně **časové razítko** sloupec zobrazuje časové razítko data z protokolů serveru, ale nezobrazí se žádná data pro **TimeElapsed**, **zdroj**, a  **Určení** sloupce, které nejsou součástí protokolu serveru.
> 
> 

Kromě použití rozložení zobrazení služby Azure Storage, můžete také definovat a uložit vlastní rozložení zobrazení. Můžete vybrat další požadované pole pro seskupení dat a uložit si vlastní rozložení v rámci seskupení.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Použít pravidla barev do mřížky analýzy
Prostředky úložiště zahrnují také barva pravidla, která nabízejí že znamená, že vizuál k identifikaci různých typů chyb v mřížce analýzy. Předdefinované barvy pravidla se vztahují na chyby protokolu HTTP, takže se budou zobrazovat jenom pro server protokolu a síťové trasování.

Chcete-li použít pravidla barvy, vyberte **pravidel barvy** pásu panelu nástrojů. Zobrazí se vám barvy pravidla služby Azure Storage v nabídce. Pro tento kurz vyberte **chyby klienta (StatusCode 400 až 499)**, jak je znázorněno na obrázku níže.

![Rozložení zobrazení služby Azure Storage](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Kromě použití pravidel barvy služby Azure Storage, můžete definovat a uložit vlastní pravidla barvy.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Skupiny a filtrovat data protokolu pro nalezení chyb 400-range
V dalším kroku vytvoříme seskupit a filtrovat data protokolu se najít všechny chyby v oblasti 400.

1. Vyhledejte **StatusCode** sloupce v mřížce analýzy, klikněte pravým tlačítkem na sloupec nadpis a vyberte **skupiny**.
2. V dalším kroku seskupit podle **ID žádosti klienta** sloupce. Uvidíte, že v mřížce analýzy nyní uspořádání dat podle stavový kód a ID požadavku klienta.
3. Zobrazení okna nástroje filtr zobrazení, pokud již není zobrazen. Na pásu panelu nástrojů vyberte **nástroj Windows**, pak **filtr zobrazení**.
4. Chcete-li filtrovat data protokolu, chcete-li zobrazit pouze chyby 400-range, přidejte následující kritéria filtru pro **filtr zobrazení** okna a klikněte na **použít**:

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

Následující obrázek ukazuje výsledky tohoto seskupení a filtr. Rozšíření **ID žádosti klienta** pole pod seskupení pro stavový kód 409, například zobrazuje operace, která způsobila Tento stavový kód.

![Rozložení zobrazení služby Azure Storage](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Po použití tohoto filtru, uvidíte, že řádky z protokolu klienta jsou vyloučeny, jako klienta protokolu nezahrnuje **StatusCode** sloupce. Než začneme nejprve se podíváme serveru a protokoly trasování sítě k vyhledání chyby 404, a potom se vrátí do klienta protokolu ke kontrole se operací klienta, které vedly k nim.

> [!NOTE]
> Můžete filtrovat i **StatusCode** sloupce a stále zobrazovat data ze všech tří protokolů, včetně protokolu klienta, pokud přidáte výraz filtru, který obsahuje položky protokolu, kde se stavovým kódem má hodnotu null. K vytvoření tohoto výrazu filtru, použijte:
> 
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
> 
> Tento filtr vrátí všechny řádky z klienta protokolu a z protokolu HTTP i protokol serveru pouze řádky kde je větší než 400 stavový kód. Pokud ho použít pro zobrazení rozložení seskupené podle ID žádosti klienta a modul, můžete vyhledávání nebo přejděte přes položky protokolu pro vyhledání těch, které jsou, kde jsou reprezentovány všechny tři protokoly.   
> 
> 

### <a name="filter-log-data-to-find-404-errors"></a>Data protokolu filtru k vyhledání chyby 404
Prostředky úložiště zahrnují předdefinované filtry, které vám umožní omezit data protokolu k vyhledání chyby nebo trendy, které hledáte. Dále jsme budete používat dvě předdefinované filtry: ten, který filtruje serveru a protokoly trasování sítě pro chyby 404 a ten, který filtruje data pro zadaný časový rozsah.

1. Zobrazení okna nástroje filtr zobrazení, pokud již není zobrazen. Na pásu panelu nástrojů vyberte **nástroj Windows**, pak **filtr zobrazení**.
2. V okně filtru zobrazení vyberte **knihovny**a vyhledat `Azure Storage` filtry k vyhledání služby Azure Storage. Vyberte filtr pro **404 (Nenalezeno) zpráv ve všech protokolech**.
3. Zobrazení **knihovny** nabídky znovu a vyhledejte a vyberte **globální filtr času**.
4. Upravte zobrazené ve filtru rozsahu, který chcete zobrazit časová razítka. To vám pomůže Chcete-li zúžit rozsah dat k analýze.
5. Filtr by měl vypadat přibližně jako v příkladu níže. Klikněte na tlačítko **použít** použijte filtr k mřížce analýzy.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Rozložení zobrazení služby Azure Storage](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analýza dat protokolu
Teď, když máte seskupenými a filtrovanými vaše data, můžete prozkoumat podrobnosti jednotlivých požadavků, které generují chyby 404. V aktuální rozložení zobrazení dat seskupené podle ID žádosti klienta, pak zdrojem protokolu. Protože jsme se filtrují u požadavků, kde pole StatusCode obsahuje 404, uvidíme, server a data trasování sítě, ne data protokolu klienta.

Následující obrázek ukazuje konkrétní požadavek, kde operace získání objektu Blob poskytuje 404, protože neexistuje objekt blob. Všimněte si, že některé sloupce byly odebrány ze standardní zobrazení relevantní data mohla zobrazit.

![Filtrované serveru a protokoly trasování sítě](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Dále jsme toto ID žádosti klienta budete korelovat s dat protokolu klienta a zjistit, které akce trvala klienta, když došlo k chybě. Můžete zobrazit nové zobrazení mřížky analýzy pro tuto relaci prohlížet klientská data protokolu, který se otevře na druhé kartě:

1. Nejprve zkopírujte hodnotu **ID žádosti klienta** pole do schránky. Uděláte to tak vyberete buď řádku, hledání **ID žádosti klienta** pole pravým tlačítkem myši na hodnotu data a zvolíte **kopírování "ID žádosti klienta"**.
2. Na pásu panelu nástrojů vyberte **nový prohlížeč**a pak vyberte **analýzy mřížky** otevřete novou kartu. Nová karta zobrazuje všechna data v souborech protokolu bez seskupování, filtrování nebo pravidla barvy.
3. Na pásu panelu nástrojů vyberte **rozložení zobrazení**a pak vyberte **všechny sloupce klienta .NET** pod **služby Azure Storage** oddílu. Toto rozložení zobrazení zobrazuje data z klienta protokolu, stejně jako protokoly trasování serveru a sítě. Ve výchozím nastavení je seřazená podle **MessageNumber** sloupce.
4. V dalším kroku klienta protokolu vyhledejte ID požadavku klienta. Na pásu panelu nástrojů vyberte **najít zprávy**, potom zadat vlastní filtr pro ID žádosti klienta v **najít** pole. Pro filtr, určíte vlastní ID žádosti klienta použijte následující syntaxi:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Message Analyzer vyhledá a vybere první položka protokolu, kde odpovídá kritériím hledání ID požadavku klienta. V protokolu klienta existuje několik záznamů pro každé ID žádosti klienta, můžete chtít seskupit podle **ID žádosti klienta** pole, aby bylo snazší Zobrazit všechno dohromady. Následující obrázek ukazuje všechny zprávy v protokolu klienta pro zadaného klienta ID požadavku.

![Zobrazení protokolů klienta chyby 404](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Data zobrazená v rozložení zobrazení v těchto dvou karet můžete analyzovat data požadavku k určení, co způsobilo chybu. Můžete také prohlédnout žádosti, které předcházely tohoto objektu, pokud chcete zobrazit, pokud předchozí událost může vést k chybě 404. Například můžete zkontrolovat položky protokolu klienta předcházející toto ID žádosti klienta k určení, zda objekt blob odstranit, nebo pokud došlo k chybě z důvodu klientské aplikace při volání rozhraní API CreateIfNotExists na kontejner nebo objekt blob. V protokolu klienta můžete najít adresu objektu blob **popis** pole; na serveru a protokoly trasování sítě, tyto informace se zobrazí v **Souhrn** pole.

Jakmile budete znát adresu objektu blob, který vrátil chybu 404, může prozkoumat podrobnosti. Pokud budete hledat položky protokolu pro ostatní zprávy související s operacemi na stejný objekt blob, můžete zkontrolovat, zda klient dříve odstraněné entity.

## <a name="analyze-other-types-of-storage-errors"></a>Analýza jiných typů chyb úložiště
Teď, když jste se seznámili s použitím nástroje Message Analyzer k analýze dat protokolů, můžete analyzovat jiné typy chyb s použitím zobrazení rozložení, barva pravidla a hledání a filtrování. Následující tabulky uvádí některé problémy, na které můžete narazit a kritéria filtru, která vám pomůže najít je. Další informace o vytváření filtrů a nástroje Message Analyzer filtrování jazyka najdete v tématu [filtrování Data zprávy](http://technet.microsoft.com/library/jj819365.aspx).

| K prozkoumání... | Použijte výraz filtru... | Výraz se vztahuje na protokolu (klient, Server, sítě, všechny) |
| --- | --- | --- |
| Neočekávaným zpožděním při doručování zpráv ve frontě |AzureStorageClientDotNetV4.Description obsahuje "Opakování operace se nezdařila." |Klient |
| HTTP zvýšení u PercentThrottlingError |HTTP. Response.StatusCode == 500 &#124; &#124; HTTP. Response.StatusCode == 503 |Síť |
| Zvýšení u PercentTimeoutError |HTTP. Response.StatusCode == 500 |Síť |
| Zvýšení u PercentTimeoutError (vše) |* StatusCode == 500 |Vše |
| Zvýšení u PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Klient |
| HTTP 403 (zakázáno) zprávy |HTTP. Response.StatusCode == 403 |Síť |
| HTTP 404 (Nenalezeno) zprávy |HTTP. Response.StatusCode == 404 |Síť |
| 404 (vše) |* StatusCode == 404 |Vše |
| Sdíleného přístupového podpisu (SAS) autorizace problém |AzureStorageLog.RequestStatus == "SASAuthorizationError" |Síť |
| HTTP 409 (konflikt) zprávy |HTTP. Response.StatusCode == 409 |Síť |
| 409 (vše) |* StatusCode == 409 |Vše |
| Nízká PercentSuccess nebo analýzy protokolu položky mají operací se stavem transakce ClientOtherErrors |AzureStorageLog.RequestStatus == "ClientOtherError" |Server |
| Nagle upozornění |((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) a (AzureStorageLog.RequestPacketSize < 1460) a (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200) |Server |
| Časové rozmezí, v protokolech serveru a sítě |#Timestamp > = 2014-10-20T16:36:38 a #Timestamp < = 2014-10-20T16:36:39 |Server sítě |
| Časové rozmezí, v protokolech serveru |AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 a AzureStorageLog.Timestamp < = 2014-10-20T16:36:39 |Server |

## <a name="next-steps"></a>Další postup
Další informace o odstraňování potíží začátku do konce scénáře ve službě Azure Storage naleznete v následujících zdrojích:

* [Monitorování, diagnostika a řešení problémů s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)
* [Storage Analytics](http://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitorování účtu úložiště na webu Azure Portal](storage-monitor-storage-account.md)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md)
* [Microsoft Message Analyzer Provozní příručka](http://technet.microsoft.com/library/jj649776.aspx)
