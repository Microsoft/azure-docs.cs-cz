---
title: Řešení potíží s Azure Storage s diagnostiky & Message Analyzer | Microsoft Docs
description: Kurz demonstraci začátku do konce řešení potíží s Azure Storage Analytics, AzCopy a Microsoft Message Analyzer
services: storage
documentationcenter: dotnet
author: tamram
manager: timlt
ms.assetid: 643372a3-1c07-4e88-b4ef-042512a43086
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 03/15/2017
ms.author: tamram
ms.openlocfilehash: 324370ae18627a1985e6a40aec11ee2fa871e93b
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="end-to-end-troubleshooting-using-azure-storage-metrics-and-logging-azcopy-and-message-analyzer"></a>Začátku do konce řešení potíží s použitím metrik Azure Storage a protokolování, AzCopy a Message Analyzer
[!INCLUDE [storage-selector-portal-e2e-troubleshooting](../../../includes/storage-selector-portal-e2e-troubleshooting.md)]

Diagnostika a řešení potíží se klíče dovednosti pro vytváření a podpora klientských aplikací s Microsoft Azure Storage. Z důvodu distribuovaná povaha aplikaci Azure může být složitější než v tradiční prostředích diagnostice a řešení potíží s chybami a problémy s výkonem.

V tomto kurzu jsme ukazují, jak identifikovat určitým chybám, které mohou ovlivnit výkon a řešení těchto chyb od začátku do konce pomocí nástroje poskytované společností Microsoft a Azure Storage, chcete-li optimalizovat klientské aplikace.

V tomto kurzu poskytuje praktické zkoumání scénáře řešení potíží začátku do konce. Podrobné koncepční informace pro řešení potíží s aplikacemi Azure storage najdete v tématu [monitorování, Diagnostika a řešení Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md).

## <a name="tools-for-troubleshooting-azure-storage-applications"></a>Nástroje pro řešení potíží s aplikacemi Azure Storage
Řešení potíží s klientským aplikacím pomocí služby Microsoft Azure Storage, můžete kombinaci nástroje k určení, kdy došlo k problému a co může být příčinou problému. Mezi tyto nástroje patří:

* **Azure Storage Analytics**. [Azure Storage Analytics](/rest/api/storageservices/Storage-Analytics) poskytuje metriky a protokolování pro Azure Storage.
  
  * **Metriky úložiště** sleduje transakce metriky a metriky kapacity pro váš účet úložiště. Pomocí metriky, můžete určit, jak vaše aplikace pracuje podle celou řadu různých opatření. V tématu [schématu tabulky metriky Analytics úložiště](/rest/api/storageservices/Storage-Analytics-Metrics-Table-Schema) Další informace o typech metriky sledován pomocí funkce Storage Analytics.
  * **Protokolování úložiště** protokoluje každý požadavek do služby Azure Storage na straně serveru protokolu. Protokol sleduje podrobná data pro každý požadavek, včetně operaci provést, stav operace a informace o latenci. V tématu [úložiště analýzy protokolů formátu](/rest/api/storageservices/Storage-Analytics-Log-Format) Další informace o požadavku a odpovědi data, která jsou zapsána do protokolů analytika úložiště.

* **Portál Azure**. Můžete nakonfigurovat protokolování a metriky pro svůj účet úložiště [portál Azure](https://portal.azure.com). Můžete také zobrazit grafy, které ukazují, jak se vaše aplikace provádí v čase a konfigurovat výstrahy pro upozornění, pokud aplikace provede odlišně, než se očekávalo příslušné metriky.
  
    V tématu [monitorování účtu úložiště na portálu Azure](storage-monitor-storage-account.md) informace o konfiguraci monitorování na portálu Azure.
* **AzCopy**. Protokoly serveru pro Azure Storage jsou uloženy jako objekty BLOB, takže AzCopy můžete kopírovat objekty BLOB protokolu do místního adresáře pro analýzu pomocí Microsoft Message Analyzer. V tématu [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md) Další informace o AzCopy.
* **Microsoft Message Analyzer**. Message Analyzer je nástroj, který využívá soubory protokolu a zobrazí data protokolu ve visual formátu, který lze snadno filtru, vyhledávání a data protokolu skupiny do užitečné sad, které můžete použít k analýze chyb a problémů s výkonem. V tématu [Microsoft zpráva analyzátor operační průvodce](http://technet.microsoft.com/library/jj649776.aspx) Další informace o Message Analyzer.

## <a name="about-the-sample-scenario"></a>O ukázkovém scénáři
V tomto kurzu podíváme na scénář, kde metrik Azure Storage udává míra nízkou procenta úspěšnosti pro aplikaci, která volá úložiště Azure. Metrika míra nízkou procenta úspěšnosti (zobrazené jako **PercentSuccess** v [portál Azure](https://portal.azure.com) a v tabulkách metriky) sleduje operace, která úspěšné, ale které vrátí stavový kód HTTP, která je větší než 299. V souborech protokolu úložiště na straně serveru, se zaznamenávají tyto operace se stavem transakce **ClientOtherErrors**. Další podrobnosti o metriku nízkou procenta úspěšnosti najdete v tématu [metriky ukazují nízkou PercentSuccess nebo položky protokolu analýzy mít operací s stav transakce ClientOtherErrors](storage-monitoring-diagnosing-troubleshooting.md#metrics-show-low-percent-success).

Operace úložiště v Azure může vrátit stavové kódy HTTP větší než 299 v rámci své normální fungování. Ale tyto chyby v některých případech indikovat, že může být schopné optimalizovat klientskou aplikaci pro zlepšení výkonu.

V tomto scénáři popíšeme míra nízkou procenta úspěšnosti být nic nižší než 100 %. Můžete zvolit jiné metriky úroveň, ale podle svých potřeb. Doporučujeme vám, že během testování vaší aplikace, vytvoříte tolerance směrného plánu pro vaše klíčové metriky výkonu. Například lze určit, na základě testování, že aplikace má mít míra konzistentní procenta úspěšnosti 90 % nebo 85 %. Pokud vaše data metriky ukazuje, že aplikace je odchylují od toto číslo potom můžete prozkoumat co může být příčinou zvýšení.

Pro naše vzorový scénář jsme se po jsme jste vytvořili, že procento úspěšnosti rychlost metrika je nižší než 100 %, zkontrolujte protokoly najít chybách, ke kterým korelovat k metrikám a využít a pokuste se zjistit příčinu nižší procenta úspěšnosti. Podíváme konkrétně chyby v 400 rozsahu. Potom jsme budete prozkoumat přesněji chyb 404 (není nalezena).

### <a name="some-causes-of-400-range-errors"></a>Některé příčiny chyb 400 rozsahu
Následující příklady jsou uvedeny vzorky chybami 400 rozsah pro požadavky na Azure Blob Storage a jejich možné příčiny. Některé z těchto chyb, jakož i chyby v rozsahu 300 a 500 rozsahu, může přispívat k nízkou procenta úspěšnosti.

Všimněte si, že následující seznamy daleko od dokončení. V tématu [stavu a kódy chyb](http://msdn.microsoft.com/library/azure/dd179382.aspx) na webu MSDN podrobnosti o obecných chyb Azure Storage a informace o chybách, které jsou specifické pro jednotlivé služby úložiště.

**Příklady stavový kód 404 (není nalezena)**

Nastane, když operace čtení pro kontejner nebo objekt blob se nezdaří, protože nebyl nalezen objekt blob nebo kontejneru.

* Nastane, pokud jiný klient před tento požadavek byl odstraněn z kontejneru nebo objektů blob.
* Nastane, pokud používáte volání rozhraní API, která vytvoří kontejner nebo objekt blob po zjišťování, zda existuje. Rozhraní API CreateIfNotExists zkontrolujte volání HEAD nejprve zkontrolujte existenci kontejner nebo objekt blob; Pokud neexistuje, bude vrácena chyba 404 a pak druhý PUT Přišla žádost o zápis kontejner nebo objekt blob.

**Stav 409 (konflikt) příklady kódu**

* Nastane, pokud použijete k vytvoření rozhraní API bez kontroly existence nejprve vytvořit nový kontejner nebo objekt blob, a kontejner nebo objekt blob s tímto názvem již existuje.
* Nastane, pokud se odstraňuje kontejner a pokusíte vytvořit nový kontejner se stejným názvem, před dokončením operace odstranění.
* V případě zadejte zapůjčení na kontejner nebo objekt blob a je už existuje zapůjčení.

**Stavový kód 412 (předběžnou se nezdařilo) příklady**

* Nastane, když nebyla splněna podmínka uvedená v podmíněného záhlaví.
* Nastane, když je zadané ID zapůjčení neodpovídá ID zapůjčení na kontejner nebo objekt blob.

## <a name="generate-log-files-for-analysis"></a>Generovat soubory protokolů pro analýzu
V tomto kurzu použijeme Message Analyzer pro práci s tři různé typy souborů protokolu, i když může zvolit pro práci s některého z těchto:

* **Serveru protokolu**, který je vytvořen, když povolíte protokolování Azure Storage. Protokol server obsahuje data o každé operaci volána pro jeden z služby Azure Storage – objekt blob, fronty, tabulky a soubor. Protokol serveru určuje, kterou operaci byla volána a jaký kód stavu je vrácený, stejně jako další podrobnosti o žádosti a odpovědi.
* **Protokolu klientů .NET**, který je vytvořen, když povolíte protokolování na straně klienta z v rámci aplikace .NET. Protokol klienta zahrnuje podrobné informace o tom, jak klient připraví žádosti a obdrží a zpracuje odpověď.
* **Protokolu trasování sítě HTTP**, který shromažďuje data na data požadavku a odpovědi protokolu HTTP nebo HTTPS, včetně pro operace Azure Storage. V tomto kurzu budete se vygeneruje trasování sítě prostřednictvím Message Analyzer.

### <a name="configure-server-side-logging-and-metrics"></a>Konfigurace protokolování na straně serveru a metriky
První budeme potřebovat ke konfiguraci protokolování Azure Storage a metriky, tak, aby jsme data z aplikace klienta k analýze. Protokolování a metriky v mnoha různými způsoby – můžete nakonfigurovat přes [portál Azure](https://portal.azure.com), pomocí prostředí PowerShell, nebo prostřednictvím kódu programu. V tématu [povolení metriky úložiště a zobrazení dat metrik](http://msdn.microsoft.com/library/azure/dn782843.aspx) a [povolení protokolování úložiště a přístup k datům protokolu](http://msdn.microsoft.com/library/azure/dn782840.aspx) na webu MSDN pro podrobné informace o konfiguraci protokolování a metriky.

**Prostřednictvím portálu Azure**

Ke konfiguraci účtu pomocí protokolování a metriky pro úložiště [portál Azure](https://portal.azure.com), postupujte podle pokynů v [monitorování účtu úložiště na portálu Azure](storage-monitor-storage-account.md).

> [!NOTE]
> Není možné nastavit minutu metriky pomocí portálu Azure. Doporučujeme však, že je nastavena pro účely tohoto kurzu a zkoumání problémů s výkonem s vaší aplikací. Můžete nastavit minutu metriky pomocí prostředí PowerShell, jak je uvedeno níže, nebo programově pomocí klientské knihovny pro úložiště.
> 
> Všimněte si, že na portálu Azure nemůže zobrazit minutu metriky, pouze hodinové metriky.
> 
> 

**Via PowerShell**

Začínáme s prostředí PowerShell pro Azure, najdete v tématu [postup instalace a konfigurace prostředí Azure PowerShell](/powershell/azure/overview).

1. Použití [Add-AzureAccount](/powershell/module/azure/add-azureaccount?view=azuresmps-3.7.0) rutiny Azure uživatelský účet přidat do okna prostředí PowerShell:
   
    ```powershell
    Add-AzureAccount
    ```

2. V **Přihlaste se k Microsoft Azure** okno, zadejte e-mailovou adresu a heslo spojené s vaším účtem. Azure přihlašovací údaje ověří, uloží je a pak zavře okno.
3. Nastavte výchozí účet úložiště k účtu úložiště, který používáte pro tento kurz spuštěním těchto příkazů v okně prostředí PowerShell:
   
    ```powershell
    $SubscriptionName = 'Your subscription name'
    $StorageAccountName = 'yourstorageaccount'
    Set-AzureSubscription -CurrentStorageAccountName $StorageAccountName -SubscriptionName $SubscriptionName
    ```

4. Povolte protokolování úložiště pro službu objektů Blob:
   
    ```powershell
    Set-AzureStorageServiceLoggingProperty -ServiceType Blob -LoggingOperations Read,Write,Delete -PassThru -RetentionDays 7 -Version 1.0
    ```

5. Metriky pro úložiště pro službu Blob, a zkontrolujte, zda nastavení Povolit **- MetricsType** k `Minute`:
   
    ```powershell
    Set-AzureStorageServiceMetricsProperty -ServiceType Blob -MetricsType Minute -MetricsLevel ServiceAndApi -PassThru -RetentionDays 7 -Version 1.0
    ```

### <a name="configure-net-client-side-logging"></a>Konfigurovat protokolování klienta rozhraní .NET
Konfigurace protokolování na straně klienta pro aplikace .NET, zapněte diagnostiku .NET v konfiguračním souboru aplikace (web.config nebo app.config). V tématu [klienta protokolování s knihovnou klienta úložiště .NET](http://msdn.microsoft.com/library/azure/dn782839.aspx) a [klienta protokolování s úložiště Microsoft Azure SDK pro jazyk Java](http://msdn.microsoft.com/library/azure/dn782844.aspx) na webu MSDN podrobnosti.

Protokol klienta zahrnuje podrobné informace o tom, jak klient připraví žádosti a obdrží a zpracuje odpověď.

Klientská knihovna pro úložiště ukládá data protokolu na straně klienta v umístění určeném v konfiguračním souboru aplikace (web.config nebo app.config).

### <a name="collect-a-network-trace"></a>Shromažďovat trasování sítě
Message Analyzer můžete použít ke shromažďování při trasování sítě protokolu HTTP nebo HTTPS, když běží klientské aplikace. Message Analyzer používá [Fiddler](http://www.telerik.com/fiddler) na back-end. Než budete shromažďovat trasování sítě, doporučujeme nakonfigurovat aplikaci Fiddler k zaznamenání nezašifrované komunikaci přes protokol HTTPS:

1. Nainstalujte [Fiddler](http://www.telerik.com/download/fiddler).
2. Spusťte aplikaci Fiddler.
3. Vyberte **nástroje | Možnosti Fiddler**.
4. V dialogovém okně Možnosti ověřte, že **zaznamenat umožňuje připojení HTTPS** a **dešifrovat provoz HTTPS** jsou obě vybrané, jak je uvedeno níže.

![Konfigurace možností Fiddler](./media/storage-e2e-troubleshooting/fiddler-options-1.png)

Pro tento kurz shromažďovat a uložit síťové trasování nejprve Message Analyzer a pak vytvořit relaci analysis k analýze trasování a protokol. Chcete-li shromažďovat trasování sítě v Message Analyzer:

1. V Message Analyzer, vyberte **souboru | Rychlé trasování | Bez šifrování HTTPS**.
2. Trasování bude zahájeno okamžitě. Vyberte **Zastavit** tak, že jsme ho nakonfigurovat pro přenosy dat úložiště trasování pouze Zastavit trasování.
3. Vyberte **upravit** Upravit relace trasování.
4. Vyberte **konfigurace** odkaz na pravé straně **Microsoft-Pef-WebProxy** zprostředkovatele trasování událostí pro Windows.
5. V **Upřesnit nastavení** dialogové okno, klikněte na tlačítko **zprostředkovatele** kartě.
6. V **filtru Hostname** pole, zadejte vaše koncové body úložiště, oddělené mezerami. Například můžete zadat koncové body takto: Změnit `storagesample` na název účtu úložiště:

    ```   
    storagesample.blob.core.windows.net storagesample.queue.core.windows.net storagesample.table.core.windows.net
    ```

7. Ukončit dialogové okno a klikněte na tlačítko **restartujte** spustíte shromažďování trasování pomocí filtru název hostitele v místě, aby pouze provoz sítě úložiště Azure je součástí trasování.

> [!NOTE]
> Po dokončení shromažďování síťové trasování, důrazně doporučujeme vrátit zpět nastavení, která můžete měnit v aplikaci Fiddler k dešifrování komunikaci přes protokol HTTPS. V dialogovém okně Možnosti aplikaci Fiddler, zrušte výběr **zaznamenat umožňuje připojení HTTPS** a **dešifrovat provoz HTTPS** zaškrtávací políčka.
> 
> 

V tématu [pomocí funkce trasování sítě](http://technet.microsoft.com/library/jj674819.aspx) na webu Technet podrobnosti.

## <a name="review-metrics-data-in-the-azure-portal"></a>Zkontrolujte data metriky na portálu Azure
Po spuštění vaší aplikace pro určitou dobu, můžete zkontrolovat grafy metriky, které se zobrazují v [portál Azure](https://portal.azure.com) sledovat, jak má byla provádění služby.

První přejděte na svůj účet úložiště na portálu Azure. Ve výchozím nastavení, monitorování grafu s **procento úspěšnosti** metrika se zobrazí v okně účtu. Pokud jste dříve upravit graf zobrazíte jiné metriky, přidejte **procento úspěšnosti** metriku.

Nyní uvidíte **procento úspěšnosti** v monitorování grafu, společně s další metriky jste mohli přidat. Ve scénáři, který jsme vám další šetření tak, že analýza protokolů v Message Analyzer je procento úspěšnosti trochu nižší než 100 %.

Další informace o přidání a přizpůsobení metriky grafy najdete v tématu [přizpůsobení metriky grafy](storage-monitor-storage-account.md#customize-metrics-charts).

> [!NOTE]
> To může trvat nějakou dobu metriky dat, aby se po povolení metrik úložiště se zobrazí na portálu Azure. To je proto hodinové metriky pro do předchozí hodiny nejsou zobrazeny na portálu Azure, dokud neuplyne do aktuální hodiny. Navíc minutu metriky aktuálně nezobrazují, na portálu Azure. Proto podle toho, když povolíte metriky, může trvat až dvě hodiny se zobrazí data metriky.
> 
> 

## <a name="use-azcopy-to-copy-server-logs-to-a-local-directory"></a>Pomocí AzCopy kopírovat protokoly serveru do místního adresáře
Úložiště Azure do objektů BLOB, zapisuje data protokolu serveru v průběhu metriky se zapisují do tabulky. Objekty BLOB protokolu jsou k dispozici v dobře známé `$logs` kontejner pro váš účet úložiště. Objekty BLOB protokolu jsou pojmenované hierarchicky podle rok, měsíc, den a hodina, aby mohli snadno najít rozsahu doba, kterou chcete prozkoumat. Například v `storagesample` je účet, kontejner pro objekty BLOB protokolu pro 01/02/2015 od 8 – 9: `https://storagesample.blob.core.windows.net/$logs/blob/2015/01/08/0800`. Jednotlivé objekty BLOB v tomto kontejneru jsou pojmenované postupně, počínaje `000000.log`.

Nástroj příkazového řádku AzCopy můžete stáhnout tyto soubory protokolu na straně serveru k umístění podle vaší volby na místním počítači. Například následující příkaz můžete stáhnout soubory protokolu pro operace objektů blob, jež proběhly 2 leden 2015 ke složce `C:\Temp\Logs\Server`; nahradit `<storageaccountname>` s názvem účtu úložiště a `<storageaccountkey>` vaším přístupovým klíčem účet:

```azcopy
AzCopy.exe /Source:http://<storageaccountname>.blob.core.windows.net/$logs /Dest:C:\Temp\Logs\Server /Pattern:"blob/2015/01/02" /SourceKey:<storageaccountkey> /S /V
```
AzCopy je k dispozici ke stažení na [Azure stáhne](https://azure.microsoft.com/downloads/) stránky. Podrobnosti o použití nástroje AzCopy najdete v tématu [přenos dat pomocí nástroje příkazového řádku Azcopy](storage-use-azcopy.md).

Další informace o stahování protokolů na straně serveru najdete v tématu [stáhnout protokolování úložiště dat protokolu](http://msdn.microsoft.com/library/azure/dn782840.aspx#DownloadingStorageLogginglogdata).

## <a name="use-microsoft-message-analyzer-to-analyze-log-data"></a>Použít Microsoft Message Analyzer analyzovat data protokolu
Microsoft Message Analyzer je nástroj pro zachytávání, zobrazení a analýza protokolu zasílání zpráv provoz, události a další scénáře řešení potíží a diagnostické zprávy systému nebo aplikace. Message Analyzer také umožňuje načíst, agregují a analyzovat data z protokolu a uložit trasovací soubory. Další informace o Message Analyzer najdete v tématu [Microsoft zpráva analyzátor operační průvodce](http://technet.microsoft.com/library/jj649776.aspx).

Message Analyzer obsahuje prostředky pro úložiště Azure, které umožňují analyzovat serveru, klienta a protokoly sítě. V této části probereme, jak používat tyto nástroje k vyřešení problému s nízkou procenta úspěšnosti v protokolech úložiště.

### <a name="download-and-install-message-analyzer-and-the-azure-storage-assets"></a>Stáhněte a nainstalujte Message Analyzer a prostředky úložiště Azure
1. Stáhněte si [Message Analyzer](http://www.microsoft.com/download/details.aspx?id=44226) z webu služby Stažení softwaru a spusťte instalační program.
2. Spusťte Message Analyzer.
3. Z **nástroje** nabídce vyberte možnost **správce inventáře**. V **správce inventáře** dialogovém okně, vyberte **stáhne**, vyfiltrujte v **Azure Storage**. Úložiště prostředků Azure, uvidíte, jak je znázorněno na obrázku níže.
4. Klikněte na tlačítko **všechny zobrazené položky synchronizace** instalace prostředků úložiště Azure. K dispozici prostředky zahrnují:
   * **Pravidla ve službě Azure Storage barev:** pravidel barvy úložiště Azure umožňují definovat speciální filtry, které pomocí barev, text a písma styly zvýrazněte zprávy, které obsahují konkrétní informace trasování.
   * **Grafy úložiště Azure:** grafy úložiště Azure jsou předdefinované grafy graf data protokolu serveru. Poznámka: Pokud chcete používat Azure Storage grafy v tuto chvíli, může pouze načíst protokol serveru v mřížce analýzy.
   * **Azure Storage analyzátory:** analyzátory Azure Storage analyzovat protokoly HTTP klienta Azure Storage, serveru a chcete-li je zobrazit v mřížce analýzy.
   * **Filtry úložiště Azure:** úložiště Azure filtry jsou předdefinované kritéria, která slouží k dotazování na data v mřížce analýzy.
   * **Rozložení zobrazení Azure Storage:** zobrazení rozložení úložiště Azure jsou předdefinovaného zobrazení sloupců a seskupení v mřížce analýzy.
5. Po instalaci prostředky, restartujte Message Analyzer.

![Správce Asset analyzátor zpráv](./media/storage-e2e-troubleshooting/mma-start-page-1.png)

> [!NOTE]
> Nainstalujte všechny prostředky Azure Storage pro účely tohoto kurzu.
> 
> 

### <a name="import-your-log-files-into-message-analyzer"></a>Import souborů protokolu do Message Analyzer
Můžete importovat všechny uložené soubory protokolů (na straně serveru, klienta a sítě) do jedné relace v Microsoft Message Analyzer pro analýzu.

1. Na **soubor** nabídky v Microsoft Message Analyzer, klikněte na tlačítko **novou relaci**a potom klikněte na **prázdné relace**. V **novou relaci** dialogové okno, zadejte název pro relaci analýzy. V **relace podrobnosti** panelu a potom klikněte na **soubory** tlačítko.
2. Chcete-li načíst data trasování sítě generované Message Analyzer, klikněte na **přidat soubory**, přejděte do umístění, kam jste uložili soubor .matp z webové relace trasování, vyberte soubor .matp a klikněte na tlačítko **otevřete**.
3. Chcete-li načíst data protokolu na straně serveru, klikněte na **přidat soubory**, přejděte do umístění, kam jste stáhli svoje protokoly na straně serveru, vyberte soubory protokolu pro časový rozsah, který chcete analyzovat a klikněte na tlačítko **otevřete**. Potom v **relace podrobnosti** panelu, nastavte **konfigurace protokolů Text** rozevíracího seznamu pro každý soubor protokolu na straně serveru k **AzureStorageLog** zajistit, že Microsoft Message Analyzer správně můžete analyzovat soubor protokolu.
4. Chcete-li načíst data protokolu na straně klienta, klikněte na **přidat soubory**, přejděte do umístění, kam jste uložili svoje protokoly na straně klienta, vyberte soubory protokolu, které chcete analyzovat a klikněte na tlačítko **otevřete**. Potom v **relace podrobnosti** panelu, nastavte **konfigurace protokolů Text** rozevíracího seznamu pro každý soubor protokolu na straně klienta pro **AzureStorageClientDotNetV4** zajistit, že Microsoft Message Analyzer správně můžete analyzovat soubor protokolu.
5. Klikněte na tlačítko **spustit** v **novou relaci** dialogovém okně můžete načíst a analyzovat data protokolu. Data protokolu se zobrazí v mřížce Analysis analyzátor zpráv.

Následující obrázek ukazuje relaci příklad nakonfigurované serveru, klienta a soubory protokolu trasování sítě.

![Konfigurace relace analyzátor zpráv](./media/storage-e2e-troubleshooting/configure-mma-session-1.png)

Všimněte si, že Message Analyzer načte soubory protokolů do paměti. Pokud máte velké sady dat protokolu, můžete filtrovat zajistí, že nejlepší výkon ze Message Analyzer.

Nejprve určit dobu, která vás zajímá kontrola a udržovat tento časový rámec co nejmenší. V řadě případů budete chtít zkontrolovat období minut nebo hodin maximálně. Importujte nejmenší sadu protokoly, které bude vyhovovat vašim potřebám.

Pokud máte velké množství dat protokolu, můžete chtít zadat relace filtr data protokolu, než můžete načíst. V **filtru relace** vyberte **knihovny** tlačítko Vybrat předdefinovaný filtr; například zvolit **globální doba filtru I** ze služby Azure Storage filtry k filtrování v časovém intervalu. Potom můžete upravit kritéria filtru, která chcete zadat počáteční a koncovou časové razítko pro interval, který chcete zobrazit. Můžete také filtrovat podle kódu stavu; Například můžete načíst pouze ty položky protokolu, kde je stavový kód 404.

Další informace o importování dat protokolu do Microsoft Message Analyzer najdete v tématu [Data načítání zpráv](http://technet.microsoft.com/library/dn772437.aspx) na webu TechNet.

### <a name="use-the-client-request-id-to-correlate-log-file-data"></a>ID žádosti klienta použít ke korelaci dat souboru protokolu
Klientská knihovna pro úložiště Azure automaticky vygeneruje ID žádosti klienta jedinečný pro každý požadavek. Tato hodnota jsou zapsány do protokolu klientů, protokol serveru a síťové trasování, můžete ho použít ke korelaci dat napříč všechny tři protokoly v rámci Message Analyzer. V tématu [ID žádosti klienta](storage-monitoring-diagnosing-troubleshooting.md#client-request-id) pro další informace o klientovi vyžadovat ID.

V níže uvedených částech popisují způsob použití předem nakonfigurovaná a vlastní rozložení zobrazení ke korelaci a data skupiny podle ID klienta požadavku.

### <a name="select-a-view-layout-to-display-in-the-analysis-grid"></a>Vyberte zobrazení rozložení pro zobrazení v mřížce analýzy
Úložiště prostředků pro Message Analyzer zahrnují zobrazení rozložení úložiště Azure, které jsou předem nakonfigurovaná zobrazení, které můžete použít k zobrazení dat pomocí užitečné seskupení a sloupců pro různé scénáře. Můžete také vytvořit vlastní zobrazení rozložení a uložit pro opakované použití.

Obrázek níže znázorňuje **rozložení zobrazení** nabídce výběrem **rozložení zobrazení** na pásu karet panelu nástrojů. Zobrazení rozložení pro Azure Storage jsou seskupené v rámci **Azure Storage** uzlu v nabídce. Můžete vyhledat `Azure Storage` do vyhledávacího pole pro filtrování Azure Storage zobrazit pouze rozložení. Můžete také vybrat hvězdičky vedle rozložení zobrazení zobrazí v horní nabídce a nastavit jej jako oblíbené.

![Nabídka Zobrazit rozložení](./media/storage-e2e-troubleshooting/view-layout-menu.png)

Vyberte **seskupené podle ClientRequestID a modul**. Toto zobrazení rozložení skupiny protokolovat data ze všech tří protokolů nejprve podle ID žádosti klienta a pak podle zdrojového souboru protokolu (nebo **modulu** v Message Analyzer). K tomuto zobrazení můžete rozbalit ID žádosti klienta konkrétní a vidět data z všechny tři soubory protokolů pro tento požadavek klienta ID.

Obrázek níže znázorňuje toto zobrazení rozložení použit ukázková data protokolu, s podmnožinu sloupců zobrazí. Uvidíte, že pro ID žádosti konkrétního klienta, zobrazuje mřížky analýzy dat z protokolu klienta, serveru protokolu a trasování sítě.

![Zobrazení rozložení úložiště Azure](./media/storage-e2e-troubleshooting/view-layout-client-request-id-module.png)

> [!NOTE]
> Různých protokolových souborech mají různé sloupce, takže pokud se zobrazí data z více souborů protokolu v mřížce analýzy, některé sloupce nesmí obsahovat žádná data pro daný řádek. Například na obrázku výše, řádky protokolu klienta nezobrazovat žádná data pro **časové razítko**, **TimeElapsed**, **zdroj**, a **cílové** sloupců, protože tyto sloupce nejsou k dispozici v protokolu klienta, ale existují v síťové trasování. Podobně **časové razítko** sloupec zobrazuje časové razítko data z protokolu serveru, ale nezobrazí se žádná data pro **TimeElapsed**, **zdroj**, a **cílové** sloupce, které nejsou součástí protokolu serveru.
> 
> 

Kromě použití rozložení zobrazení Azure Storage, můžete také definovat a uložit vlastní rozložení zobrazení. Můžete vybrat další požadované pole pro seskupování dat a uložit seskupení jako součást vaší vlastní rozložení.

### <a name="apply-color-rules-to-the-analysis-grid"></a>Použít pravidla barev do mřížky analýzy
Prostředky úložiště obsahovat také barvu pravidla, která nabízí že vizuál znamená k identifikaci různých typů chyb v mřížce analýzy. Předdefinované barev pravidla platí pro chyby protokolu HTTP, aby se zobrazovaly pouze pro server protokolu a síťové trasování.

Chcete-li použít pravidel barvy, vyberte **pravidel barvy** na pásu karet panelu nástrojů. Uvidíte pravidel barvy Azure Storage v nabídce. Pro tento kurz, vyberte **chyby klienta (StatusCode mezi 400 a 499)**, jak je znázorněno na obrázku níže.

![Zobrazení rozložení úložiště Azure](./media/storage-e2e-troubleshooting/color-rules-menu.png)

Kromě použití pravidel barvy Azure Storage, můžete definovat a uložit vlastní pravidla barvy.

### <a name="group-and-filter-log-data-to-find-400-range-errors"></a>Skupiny a filtrování dat protokolu hledání 400-range chyb
V dalším kroku jsme budete skupiny a filtrování dat protokolu najít všechny chyby v 400 rozsahu.

1. Vyhledejte **StatusCode** sloupce v mřížce analýzy, klikněte pravým tlačítkem na sloupci nadpis a vyberte **skupiny**.
2. V dalším kroku skupina na **ClientRequestId** sloupce. Uvidíte, že v mřížce Analysis teď uspořádání stavový kód a ID klienta požadavku.
3. Zobrazí okno nástroje filtr zobrazení, pokud již není zobrazen. Na pásu karet panelu nástrojů vyberte **nástroj Windows**, pak **filtr zobrazení**.
4. Chcete-li filtrovat data protokolu pro zobrazení pouze chyby 400-range, přidejte následující kritéria filtru k **filtr zobrazení** a klikněte na **použít**:

    ```   
    (AzureStorageLog.StatusCode >= 400 && AzureStorageLog.StatusCode <=499) || (HTTP.StatusCode >= 400 && HTTP.StatusCode <= 499)
    ```

Následující obrázek ukazuje výsledky této seskupení a filtru. Rozšíření **ClientRequestID** pole pod seskupení pro stavový kód 409, například uvádí operace, která tento kód stavu.

![Zobrazení rozložení úložiště Azure](./media/storage-e2e-troubleshooting/400-range-errors1.png)

Po použití tento filtr, uvidíte, že řádky z protokolu klienta jsou vyloučeny, jako klienta protokolu nezahrnuje **StatusCode** sloupce. Přečtěte serveru a protokoly trasování sítě najít chyb 404, a potom se vrátí do klienta protokolu prozkoumat operací klienta, které vedly k nim.

> [!NOTE]
> Můžete filtrovat podle **StatusCode** sloupce a stále zobrazí data ze všech tří protokolů, včetně protokolu klientů, pokud přidáte výraz filtru, který obsahuje položky protokolu, kde má hodnotu null stavový kód. Chcete-li vytvořit tento výraz filtru, použijte:
> 
> <code>&#42;StatusCode >= 400 or !&#42;StatusCode</code>
> 
> Tento filtr vrátí všechny řádky z klienta protokolu a pouze řádky ze serveru protokolu a protokolu HTTP kde je větší než 400 stavový kód. Pokud ho použít k zobrazení rozložení seskupené podle ID žádosti klienta a modul, můžete vyhledávat nebo přejděte přes položky protokolu těch, které jsou najít, kde jsou všechny tři protokoly zastoupení.   
> 
> 

### <a name="filter-log-data-to-find-404-errors"></a>Data protokolu filtru k vyhledání chyb 404
Prostředky úložiště zahrnují předdefinované filtry, které můžete použít, chcete-li zúžit data protokolu k vyhledání chyby nebo trendů, které hledáte. V dalším kroku jsme budete používat dvě předdefinované filtry: ten, který filtruje serveru a síťové protokoly trasování pro chyb 404 a jeden, který filtruje data v zadaném časovém rozmezí.

1. Zobrazí okno nástroje filtr zobrazení, pokud již není zobrazen. Na pásu karet panelu nástrojů vyberte **nástroj Windows**, pak **filtr zobrazení**.
2. V okně filtru zobrazení vyberte **knihovny**a vyhledejte na `Azure Storage` najít úložiště Azure filtrů. Vyberte filtr pro **404 (Nenalezeno) zpráv ve všech protokolech**.
3. Zobrazení **knihovny** nabídky znovu a vyhledejte a vyberte **globální filtr času**.
4. Upravte časová razítka vidět ve filtru na rozsah, který si přejete zobrazit. To vám pomůže Chcete-li zúžit rozsah dat pro analýzu.
5. Filtr měla vypadat podobně jako v příkladu níže. Klikněte na tlačítko **použít** použít filtr k mřížce analýzy.

    ```   
    ((AzureStorageLog.StatusCode == 404 || HTTP.StatusCode == 404)) And
    (#Timestamp >= 2014-10-20T16:36:38 and #Timestamp <= 2014-10-20T16:36:39)
    ```

    ![Zobrazení rozložení úložiště Azure](./media/storage-e2e-troubleshooting/404-filtered-errors1.png)

### <a name="analyze-your-log-data"></a>Analyzovat data protokolu
Teď, když máte seskupené a filtrovat data, můžete zkontrolovat podrobnosti o jednotlivých požadavků, které generuje chyby 404. V aktuální rozložení zobrazení data jsou seskupena podle ID žádosti klienta, pak zdrojem protokolu. Vzhledem k tomu, že jsme jsou filtrování požadavků kde pole StatusCode obsahuje 404, uvidíme, jenom server a data trasování sítě, ne data protokolu klienta.

Následující obrázek ukazuje konkrétního požadavku, kde operace získání objektu Blob poskytuje 404, protože objekt blob neexistuje. Všimněte si, že některé sloupce byly odebrány z standardní zobrazení relevantní data mohla zobrazit.

![Filtrované serveru a protokoly trasování sítě](./media/storage-e2e-troubleshooting/server-filtered-404-error.png)

Toto ID žádosti klienta jsme v dalším kroku budete souviset s dat protokolu klienta a zjistit, jaké akce trvala klienta, pokud došlo k chybě. Můžete zobrazit nové zobrazení mřížky analýzy pro tuto relaci prohlížet klientská data protokolu, který se otevře v druhé kartě:

1. Nejdřív zkopírujte hodnotu **ClientRequestId** pole do schránky. Můžete to provedete výběr buď řádek, hledání **ClientRequestId** pole, pravým tlačítkem myši na hodnotu data a zvolením **kopie 'ClientRequestId'**.
2. Na pásu karet panelu nástrojů vyberte **nový prohlížeč**, pak vyberte **Analysis mřížky** otevřete novou kartu. Nové kartě se zobrazují všechna data v souboru protokolu, bez seskupování, filtrování nebo pravidel barvy.
3. Na pásu karet panelu nástrojů vyberte **rozložení zobrazení**, pak vyberte **všechny sloupce klienta rozhraní .NET** pod **Azure Storage** části. Toto rozložení zobrazení zobrazuje data z klienta protokolu a také v protokolech trasování serveru a sítě. Ve výchozím nastavení je seřazen na **MessageNumber** sloupce.
4. V dalším kroku klienta protokolu vyhledejte ID žádosti klienta. Na pásu karet panelu nástrojů vyberte **najít zprávy**, pak zadání vlastního filtru na ID žádosti klienta v **najít** pole. Pro filtr, zadání vlastní ID žádosti klienta použijte následující syntaxi:

    ```
    *ClientRequestId == "398bac41-7725-484b-8a69-2a9e48fc669a"
    ```

Message Analyzer vyhledá a vybere první položka kde kritéria hledání odpovídá ID žádosti klienta. V protokolu klientů, existuje několik záznamů pro každý ID žádosti klienta, možná budete chtít seskupovat je na **ClientRequestId** pole, aby bylo snazší je zobrazíte všechny společně. Následující obrázek ukazuje všechny zprávy v protokolu klienta pro zadaného klienta žádosti.

![Zobrazuje klienta protokolu chyb 404](./media/storage-e2e-troubleshooting/client-log-analysis-grid1.png)

Data zobrazená v zobrazení rozložení v těchto dvou karet můžete analyzovat data požadavku k určení, co způsobilo chybu. Můžete také prohlédnout požadavků, které před následujícímu chcete zobrazit, pokud je předchozí událost může vedly k chybu 404. Můžete například zkontrolovat položky protokolu klienta předcházející tohoto ID klienta požadavku k určení, zda objekt blob může být odstraněna, nebo pokud byla chyba způsobena klientská aplikace volání rozhraní API CreateIfNotExists na kontejner nebo objekt blob. V protokolu klienta, můžete najít objekt blob adresu v **popis** pole; v serveru a protokoly trasování sítě, tyto informace se zobrazí v **Souhrn** pole.

Jakmile znáte adresu objektu blob, který poskytuje chybu 404, můžete dále prozkoumat. Pokud hledáte položky protokolu další zprávy týkající se operací na stejný objekt blob, můžete zkontrolovat, jestli klient dříve odstraňovat entity.

## <a name="analyze-other-types-of-storage-errors"></a>Analýza jiné typy chyb úložiště
Teď, když jste obeznámeni s používáním Message Analyzer k analýze dat protokolu, můžete analyzovat jiné typy chyb pomocí zobrazení rozložení, pravidel barvy a hledání nebo filtrování. Následující tabulky uvádí některé problémy, ke kterým může dojít a kritéria filtru, které můžete použít k vyhledání je. Další informace o vytváření filtrů a Message Analyzer filtrování jazyka najdete v tématu [filtrování Data zpráv](http://technet.microsoft.com/library/jj819365.aspx).

| K prozkoumání... | Použijte výraz filtru... | Výraz se vztahuje na protokolu (klient, Server, sítě, všechny) |
| --- | --- | --- |
| Neočekávané zpoždění při doručování zpráv ve frontě |AzureStorageClientDotNetV4.Description obsahuje "Bude opakován operace se nezdařila." |Klient |
| Zvýšení HTTP PercentThrottlingError |HTTP. Response.StatusCode == 500 &#124; &#124; HTTP. Response.StatusCode == 503 |Síť |
| Nárůst PercentTimeoutError |HTTP.Response.StatusCode   == 500 |Síť |
| Nárůst PercentTimeoutError (všechny) |* StatusCode == 500 |Vše |
| Nárůst PercentNetworkError |AzureStorageClientDotNetV4.EventLogEntry.Level   < 2 |Klient |
| HTTP 403 (zakázáno) zprávy |HTTP.Response.StatusCode   == 403 |Síť |
| HTTP 404 (Nenalezeno) zprávy |HTTP.Response.StatusCode   == 404 |Síť |
| 404 (všechny) |* StatusCode == 404 |Vše |
| Sdíleného přístupového podpisu (SAS) autorizace problém |AzureStorageLog.RequestStatus == "SASAuthorizationError" |Síť |
| HTTP 409 (konflikt) zprávy |HTTP.Response.StatusCode   == 409 |Síť |
| 409 (všechny) |* StatusCode == 409 |Vše |
| Nízká PercentSuccess nebo analytics položky protokolu mít operací s stav transakce ClientOtherErrors |AzureStorageLog.RequestStatus == "ClientOtherError" |Server |
| Nagle upozornění |((AzureStorageLog.EndToEndLatencyMS-AzureStorageLog.ServerLatencyMS) > (AzureStorageLog.ServerLatencyMS * 1.5)) a (AzureStorageLog.RequestPacketSize < 1460) a (AzureStorageLog.EndToEndLatencyMS - AzureStorageLog.ServerLatencyMS > = 200) |Server |
| Doba v protokolech serveru a sítě |#Timestamp > = 2014-10-20T16:36:38 a #Timestamp < = 2014-10-20T16:36:39 |Server, Network |
| Rozsah čas v protokolech serveru |AzureStorageLog.Timestamp > = 2014-10-20T16:36:38 a AzureStorageLog.Timestamp < = 2014-10-20T16:36:39 |Server |

## <a name="next-steps"></a>Další kroky
Další informace o odstraňování potíží začátku do konce scénáře ve službě Azure Storage naleznete v následujících zdrojích:

* [Monitorování, diagnostika a řešení problémů s Microsoft Azure Storage](storage-monitoring-diagnosing-troubleshooting.md)
* [Storage Analytics](http://msdn.microsoft.com/library/azure/hh343270.aspx)
* [Monitorování účtu úložiště na portálu Azure](storage-monitor-storage-account.md)
* [Přenos dat pomocí nástroje příkazového řádku AzCopy](storage-use-azcopy.md)
* [Microsoft Message Analyzer operační Průvodce](http://technet.microsoft.com/library/jj649776.aspx)
