---
title: Nastavení diagnostiky pro Azure Cloud Services a virtual machines | Dokumentace Microsoftu
description: Zjistěte, jak nastavení diagnostiky pro ladění Azure cloude services a virtual machines (VM) v sadě Visual Studio.
services: visual-studio-online
documentationcenter: na
author: mikejo
manager: douge
editor: ''
ms.assetid: e70cd7b4-6298-43aa-adea-6fd618414c26
ms.service: multiple
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: multiple
ms.date: 06/28/2018
ms.author: mikejo
ms.openlocfilehash: 8f32165a7a54ead06d57a3d8d1b4282498dca635
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969612"
---
# <a name="set-up-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Nastavení diagnostiky pro Azure Cloud Services a virtual machines
Když budete potřebovat k řešení cloudové služby Azure nebo na virtuálním počítači, můžete použít Visual Studio snadněji nastavení Azure Diagnostics. Diagnostika zaznamená systémová data a data protokolování na virtuální počítače a instance virtuálních počítačů, na kterých běží vaše Cloudová služba. Diagnostická data se přenesou do účtu úložiště, kterou zvolíte. Další informace o diagnostice protokolování v Azure, najdete v článku [povolit protokolování diagnostiky pro webové aplikace ve službě Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

V tomto článku ukážeme, jak pomocí sady Visual Studio k zapnutí a nastavení Azure Diagnostics před a po nasazení. Naučte se nastavení diagnostiky Azure na virtuálních počítačích, vyberte typy shromažďovat diagnostické informace a postup zobrazení informace po shromáždění zpracovat.

Jeden z následujících možností můžete do nastavení diagnostiky Azure:

* Změňte nastavení diagnostiky **konfiguraci diagnostiky** dialogové okno v sadě Visual Studio. Nastavení se ukládají do souboru s názvem diagnostics.wadcfgx (Azure SDK 2.4 a starších soubor se nazývá diagnostics.wadcfg). Můžete také přímo upravit konfigurační soubor. Pokud ručně aktualizovat soubor, konfigurace změny se projeví při příštím nasazení cloudu služby Azure nebo službu spustit v emulátoru.
* Pomocí Průzkumníka cloudu nebo Průzkumníku serveru v sadě Visual Studio můžete změnit nastavení diagnostiky pro cloudové služby nebo virtuálního počítače, na kterém běží.

## <a name="azure-sdk-26-diagnostics-changes"></a>Změny diagnostiky Azure SDK 2.6
Následující změny se aplikují na novější projekty v sadě Visual Studio a Azure SDK 2.6:

* Místní emulátor nyní podporuje diagnostiku. To znamená, že můžete shromažďovat diagnostická data a ujistěte se, že vaše aplikace vytvoří správný trasování při vývoji a testování v sadě Visual Studio. Připojovací řetězec `UseDevelopmentStorage=true` zapne shromažďování dat diagnostiky, zatímco spouštíte projekt cloudové služby v sadě Visual Studio pomocí emulátoru úložiště Azure. Všechna diagnostická data se shromažďují v účtu úložiště vývojovým úložištěm.
* Připojovací řetězec účtu úložiště diagnostiky `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` je uložen v souboru (.csdef) služby. V Azure SDK 2.5 je určen účet úložiště diagnostiky ze souboru diagnostics.wadcfgx.

Připojovací řetězec funguje jinak některé klíčovými způsoby ve verzi 2.6 SDK Azure a později a Azure SDK 2.4 a dříve:

* Azure SDK 2.4 a dřívějších verzí slouží připojovací řetězec jako modul runtime Diagnostika modulu plug-in se získat informace o účtu úložiště pro přenos diagnostické protokoly.
* V Azure SDK 2.6 nebo novější Visual Studio používá připojovací řetězec diagnostiky k vytvoření rozšíření Azure Diagnostics s informacemi o příslušné úložiště účtu během publikování. Připojovací řetězec můžete použít k definování různých účtů úložiště pro jiné služby konfigurace, které sada Visual Studio použije během publikování. Ale protože Diagnostika modulu plug-in není k dispozici po Azure SDK 2.5, samotný soubor .cscfg nejde nastavit rozšíření diagnostiky. Musíte vytvořit rozšíření samostatně pomocí nástrojů, jako je Visual Studio nebo prostředí PowerShell.
* Pro zjednodušení procesu nastavení rozšíření diagnostiky pomocí Powershellu, výstup balíčku ze sady Visual Studio obsahuje veřejné konfigurace XML pro rozšíření diagnostiky pro každou roli. Visual Studio používá k naplnění informací o účtu úložiště ve veřejné konfiguraci diagnostiky připojovací řetězec. Veřejné konfigurační soubory jsou vytvořeny ve složce rozšíření. Veřejné konfiguračních souborů použijte vzor pojmenování PaaSDiagnostics. &lt;název role\>. PubConfig.xml. Všechna nasazení pomocí prostředí PowerShell můžete použít tento model pro každou konfiguraci mapování k roli.
* [Webu Azure portal](http://go.microsoft.com/fwlink/p/?LinkID=525040) používá připojovací řetězec v souboru .cscfg pro přístup k diagnostická data. Data se zobrazí na **monitorování** kartu. Nastavení služby, chcete-li zobrazit podrobné údaje o monitorování na portálu je nutný připojovací řetězec.

## <a name="migrate-projects-to-azure-sdk-26-and-later"></a>Migrovat projekty do Azure SDK 2.6 a novější
Když provádíte migraci z Azure SDK 2.5 Azure SDK 2.6 nebo novější, pokud jste měli účet úložiště diagnostiky zadané v souboru .wadcfgx, účet úložiště zůstane v tomto souboru. Využít flexibilitu vycházející z používání různých účtů úložiště pro jiné úložiště konfigurace, ručně přidejte připojovací řetězec do projektu. Pokud migrujete projektu z Azure SDK 2.4 nebo starší na Azure SDK 2.6, diagnostiky připojovací řetězce jsou zachovány. Mějte však na paměti změny v tom, jak jsou považovány připojovací řetězce v Azure SDK 2.6, je popsáno v předchozí části.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Určuje, jak Visual Studio účet úložiště diagnostiky
* Pokud v souboru .cscfg je zadaný připojovací řetězec diagnostiky, Visual Studio se používá k nastavení rozšíření diagnostiky během publikování a generuje soubory XML veřejné konfigurace při vytváření balíčku.
* Pokud připojovací řetězec diagnostiky není zadané v souboru .cscfg, Visual Studio přejde k použití účtu úložiště určený v souboru .wadcfgx nastavení diagnostické rozšíření pro publikování a generování veřejné konfigurace XML soubory při vytváření balíčku.
* Diagnostika připojovací řetězec v souboru .cscfg má přednost před účtu úložiště v souboru .wadcfgx. Pokud je Diagnostika připojovací řetězec zadaný v souboru .cscfg sady Visual Studio používá tento připojovací řetězec a ignoruje účtu úložiště v .wadcfgx.

### <a name="what-does-the-update-development-storage-connection-strings-check-box-do"></a>Co dělá políčko "Aktualizovat vývoj úložiště připojovacích řetězců..."?
**Při publikování do Microsoft Azure aktualizovat připojovací řetězce úložiště vývoj pro diagnostiku a ukládání do mezipaměti pomocí přihlašovacích údajů účtu úložiště Microsoft Azure** zaškrtávací políčko je pohodlný způsob, jak aktualizovat všechny vývojovým úložištěm připojení účtu řetězce účtu úložiště Azure, který zadáte během publikování.

Například pokud vyberete toto zaškrtávací políčko a Diagnostika připojovací řetězec Určuje `UseDevelopmentStorage=true`, při publikování projektu do Azure, Visual Studio automaticky aktualizovat diagnostiku připojovací řetězec účtu úložiště, který jste zadali v Průvodce publikováním. Pokud účet úložiště skutečné byl zadán jako připojovací řetězec diagnostiky, tento účet se ale používá místo toho.

## <a name="diagnostics-functionality-differences-in-azure-sdk-24-and-earlier-vs-azure-sdk-25-and-later"></a>Rozdíly funkcí diagnostiky v Azure SDK 2.4 a starší vs. Azure SDK 2.5 nebo novější
Pokud provádíte upgrade projektu z Azure SDK 2.4 a starší na Azure SDK 2.5 nebo novější, mějte na paměti následující rozdíly funkcí diagnostiky:

* **Rozhraní API pro konfiguraci se považují za zastaralé**. Programová konfigurace diagnostiky je k dispozici v Azure SDK 2.4 a starší, ale je zastaralé v Azure SDK 2.5 nebo novější. Pokud vaše konfigurace diagnostiky je aktuálně definován v kódu, je nutné překonfigurovat nastavení od nuly v migrovaného projektu pro diagnostiku pokračovat v práci. Konfigurační soubor diagnostiky pro Azure SDK 2.4 je diagnostics.wadcfg. Konfigurační soubor diagnostiky pro Azure SDK 2.5 a později je diagnostics.wadcfgx.
* **Diagnostika pro cloudové služby aplikace lze nastavit pouze na úrovni role**. V Azure SDK 2.5 nebo novější nelze nastavení diagnostiky pro aplikace cloud service na úrovni instance.
* **Pokaždé, když nasadíte aplikaci, konfiguraci diagnostiky se aktualizuje**. To může způsobit problémy s paritou, je-li změnit konfiguraci diagnostiky z Průzkumníka serveru Visual Studia a potom znovu nasaďte aplikaci.
* **V Azure SDK 2.5 nebo novější, výpisy stavu systému jsou nakonfigurované v konfiguračním souboru diagnostiky a ne v kódu**. Pokud máte výpisy při selhání nakonfigurovaný v kódu, musí ručně převést konfiguraci z kódu do konfiguračního souboru. Výpisy stavu systému se nepřenesou během migrace do Azure SDK 2.6.

## <a name="turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them"></a>Zapnout diagnostiku v projekty cloudových služeb, před jejich nasazením
V sadě Visual Studio můžete shromažďovat diagnostická data pro role, které běží v Azure, při spuštění služby se spustila v emulátoru před nasazením. Všechny změny nastavení diagnostiky v sadě Visual Studio jsou uloženy v konfiguračním souboru diagnostics.wadcfgx. Tato nastavení určují účet úložiště, kde je uložen diagnostická data, při nasazení cloudové služby.

[!INCLUDE [cloud-services-wad-warning](../includes/cloud-services-wad-warning.md)]

### <a name="to-turn-on-diagnostics-in-visual-studio-before-deployment"></a>Chcete-li zapnout diagnostiku v sadě Visual Studio před nasazením

1. V místní nabídce pro roli, vyberte **vlastnosti**. V této role **vlastnosti** dialogové okno, vyberte **konfigurace** kartu.
2. V **diagnostiky** části, ujistěte se, že **povolit diagnostiku** je zaškrtnuto políčko.
   
    ![Přístup k možnosti Povolit diagnostiku](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)
3. Účet úložiště pro diagnostická data, vyberte tlačítko se třemi tečkami (...).
   
    ![Zadejte účet úložiště](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)
4. V **vytvořit připojovací řetězec úložiště** dialogového okna zadejte, jestli se chcete připojit pomocí emulátoru úložiště Azure, předplatné Azure, nebo ručně zadali přihlašovací údaje.
   
    ![Dialogové okno účtu úložiště](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)
   
   * Pokud vyberete **emulátor úložiště Microsoft Azure**, připojovací řetězec je nastavený `UseDevelopmentStorage=true`.
   * Pokud vyberete **předplatného**, vyberte předplatné Azure, kterou chcete použít a zadejte název účtu. Ke správě vašich předplatných Azure, vyberte **spravovat účty**.
   * Pokud vyberete **ručně zadali přihlašovací údaje**, zadejte název a klíč účtu Azure, který chcete použít.
5. Chcete-li zobrazit **konfiguraci diagnostiky** dialogu **konfigurovat**. S výjimkou **Obecné** a **adresáře protokolů**, každá karta představuje zdroj dat diagnostiky, které můžete shromažďovat. Výchozí hodnota **Obecné** karta nabízí následující diagnostické možnosti shromažďování dat: **pouze chyby**, **všechny informace**, a **vlastní plán**. Výchozí hodnota **pouze chyby** možnost používá nejmenší velikost úložiště, protože nepřenese upozornění nebo trasování zprávy. **Všechny informace** možnost přenáší informace na maximum, používá úložiště na maximum a proto je možnost nejdražší.

   > [!NOTE]
   > Minimální podporovaná velikost pro "Disk kvóta v MB" je 4GB. Nicméně pokud shromažďujete výpisy paměti, zvětšete na vyšší hodnotu, jako je 10GB.
   >
  
    ![Povolení diagnostiky Azure a konfigurace](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
6. V tomto příkladu vyberte **vlastní plán** možnost, takže můžete přizpůsobit shromážděná data.
7. V **disková kvóta v MB** pole, můžete nastavena tom, kolik místa k přidělení ve vašem účtu úložiště pro diagnostická data. Můžete změnit nebo přijměte výchozí hodnotu.
8. Na každé kartě diagnostických dat, která chcete shromažďovat, vyberte **povolit přenos z \<typ protokolu\>**  zaškrtávací políčko. Například, pokud chcete shromažďovat protokoly aplikací **protokoly aplikací** kartu, vyberte **povolit přenos protokolů aplikace** zaškrtávací políčko. Zadejte také další informace, které vyžaduje každý typ dat diagnostiky. Informace o konfiguraci pro každou kartu, najdete v části **nastavení zdrojů dat diagnostiky** dále v tomto článku. 
9. Po povolení shromažďování všechna diagnostická data chcete vybrat **OK**.
10. Spusťte váš projekt cloudové služby Azure v sadě Visual Studio jako obvykle. Při použití aplikace, se uloží informace protokolu, které jste povolili k účtu úložiště Azure, který jste zadali.

## <a name="turn-on-diagnostics-on-azure-virtual-machines"></a>Zapnout diagnostiku na virtuálních počítačích Azure
V sadě Visual Studio můžete shromažďovat diagnostická data pro Azure virtual machines.

### <a name="to-turn-on-diagnostics-on-azure-virtual-machines"></a>Chcete-li zapnout diagnostiku na virtuálních počítačích Azure

1. V Průzkumníku serveru vyberte uzel Azure a připojte se ke svému předplatnému Azure, pokud jste ještě nepřipojili.
2. Rozbalte **virtuálních počítačů** uzlu. Můžete vytvořit nový virtuální počítač, nebo vybrat existující uzel.
3. V místní nabídce pro virtuální počítač má, vyberte **konfigurovat**. Zobrazí se dialogové okno Konfigurace virtuálního počítače.
   
    ![Konfigurace virtuálního počítače Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)
4. Pokud ještě není nainstalované, přidejte rozšíření Microsoft Monitoring Agent diagnostiky. U tohoto rozšíření může shromažďovat diagnostická data pro virtuální počítač Azure. V části **nainstalované rozšíření**v **vybrat dostupné rozšíření** rozevíracího seznamu vyberte **Microsoft Monitoring Agent diagnostiky**.
   
    ![Nainstalovat rozšíření virtuálního počítače Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)
   
    > [!NOTE]
   > Další diagnostické rozšíření jsou k dispozici pro virtuální počítače. Další informace najdete v tématu [funkcí a rozšíření virtuálních počítačů pro Windows](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).
   > 
   > 
5. Chcete-li přidat rozšíření a zobrazení jeho **konfiguraci diagnostiky** dialogu **přidat**.
6. Zadejte účet úložiště, vyberte **konfigurovat**a pak vyberte **OK**.
   
    Každá karta (s výjimkou **Obecné** a **adresáře protokolů**) představuje zdroj dat diagnostiky, které můžete shromažďovat.
   
    ![Povolení diagnostiky Azure a konfigurace](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
   
    Výchozí kartu **Obecné**, nabízí následující možnosti shromažďování dat diagnostiky: **pouze chyby**, **všechny informace**, a **vlastní plán**. Výchozí možnost **pouze chyby**, má minimální množství úložiště, protože nepřenese upozornění nebo trasování zprávy. **Všechny informace** možnost přenáší informace na maximum a je proto nejdražší možnosti z hlediska úložiště.
7. V tomto příkladu vyberte **vlastní plán** shromážděná možnosti, takže data můžete přizpůsobit.
8. **Disková kvóta v MB** pole určuje, kolik místa, které chcete přidělit ve svém účtu úložiště pro diagnostická data. Pokud chcete, můžete změnit výchozí hodnotu.
9. Na každé kartě chcete shromažďovat diagnostická data, vyberte jeho **povolit přenos z \<typ protokolu\>**  zaškrtávací políčko.
   
    Například, když budete chtít shromažďovat protokoly aplikací, vyberte **povolit přenos protokolů aplikace** zaškrtávací políčko na **protokoly aplikací** kartu. Také zadejte další informace, které je nutné pro každý typ dat diagnostiky. Informace o konfiguraci pro každou kartu, najdete v části **nastavení zdrojů dat diagnostiky** dále v tomto článku.
10. Po povolení shromažďování všechna diagnostická data, které chcete vybrat **OK**.
11. Uložte aktualizovaný projekt.
    
    Zpráva v **protokoly aktivit Microsoft Azure** okno znamená, že virtuální počítač byl aktualizován.

## <a name="set-up-diagnostics-data-sources"></a>Nastavení zdroje dat diagnostiky
Po povolení shromažďování dat diagnostiky můžete přesně k jakým zdrojům dat, které chcete shromáždit a které informace jsou shromažďovány. Tento oddíl popisuje karty v **konfiguraci diagnostiky** dialogové okno a znamená, co jednotlivé možnosti konfigurace.

### <a name="application-logs"></a>Protokoly aplikací
Protokoly aplikací mají diagnostické informace, který je vytvořen webové aplikace. Pokud chcete zaznamenat protokoly aplikací, vyberte **povolit přenos protokolů aplikace** zaškrtávací políčko. Chcete-li zvýšit nebo snížit interval mezi přenos protokolů aplikace k účtu úložiště, změňte **doba přenosu (min)** hodnotu. Také můžete změnit množství informací, které jsou zaznamenány v protokolu tak, že nastavíte **úrovně protokolování** hodnotu. Vyberte například **Verbose** získat další informace, nebo vyberte **kritický** zachytit pouze kritické chyby. Pokud máte diagnostiky specifické pro zprostředkovatele, který vysílá protokoly aplikací, protokoly můžete zachytit tak, že přidáte identifikátor GUID zprostředkovatele v **identifikátor GUID zprostředkovatele** pole.

  ![Protokoly aplikací](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

Další informace o aplikačních protokolů najdete v tématu [povolit protokolování diagnostiky pro webové aplikace ve službě Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

### <a name="windows-event-logs"></a>Protokoly událostí Windows
Chcete-li zachytit protokoly událostí Windows, vyberte **povolit přenos protokolů událostí Windows** zaškrtávací políčko. Chcete-li zvýšit nebo snížit interval mezi přenos protokolů událostí do účtu úložiště, změňte **doba přenosu (min)** hodnotu. Zaškrtněte políčka pro typy událostí, které chcete sledovat.

![Protokoly událostí](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Pokud používáte Azure SDK 2.6 nebo novější a chcete zadat vlastní zdroj dat, zadejte ho **\<název zdroje dat\>** textové pole a pak vyberte **přidat**. Zdroj dat je přidaný do souboru diagnostics.cfcfg.

Pokud používáte Azure SDK 2.5 a chcete zadat vlastní zdroj dat, můžete ji přidat `WindowsEventLog` část diagnostics.wadcfgx soubor jako v následujícím příkladu:

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Čítače výkonu
Informace o čítači výkonu můžete najít odhalit kritická místa systému a doladit výkon systému a aplikací. Další informace najdete v tématu [vytvoření a použití čítačů výkonu v aplikaci Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx). Chcete-li zachytit čítače výkonu, vyberte **povolit přenos čítačů výkonu** zaškrtávací políčko. Chcete-li zvýšit nebo snížit interval mezi přenos protokolů událostí do účtu úložiště, změňte **doba přenosu (min)** hodnotu. Zaškrtněte políčka pro čítače výkonu, které chcete sledovat.

![Čítače výkonu](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Pokud chcete sledovat čítače výkonu, která není uvedená, zadejte čítač výkonu pomocí navrhované syntaxe. a pak vyberte **přidat**. Operační systém na virtuálním počítači určuje, které můžete sledovat čítače výkonu. Další informace o syntaxi najdete v tématu [zadejte cestu čítač](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Protokoly infrastruktury
Protokoly infrastruktury obsahují informace o diagnostické infrastruktury Azure, modul RemoteAccess a RemoteForwarder modulu. Chcete-li shromažďovat informace o protokolech infrastrukturu, vyberte **povolit přenos protokolů infrastruktury** zaškrtávací políčko. Chcete-li zvýšit nebo snížit interval mezi přenosu protokolů infrastruktury do účtu úložiště, změňte **doba přenosu (min)** hodnotu.

![Protokoly infrastruktury diagnostiky](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

Další informace najdete v tématu [shromažďování dat protokolování pomocí diagnostiky Azure](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="log-directories"></a>Adresáře protokolů
Složky protokolů, které mají data shromážděná z adresáře protokolů pro požadavky na Internetové informační služby (IIS), neúspěšné požadavky nebo složky, které zvolíte. Chcete-li zachytit adresáře protokolů, vyberte **povolit přenos adresářů protokolů** zaškrtávací políčko. Chcete-li zvýšit nebo snížit interval mezi přenosu protokolů do účtu úložiště, změňte **doba přenosu (min)** hodnotu.

Zaškrtněte políčka u protokolů, které chcete shromažďovat, jako například **protokoly služby IIS** a **neúspěšných požadavků** protokoly. Názvy kontejnerů výchozí úložiště jsou k dispozici, ale můžete změnit názvy.

Můžete shromáždit protokoly z libovolné složky. Zadejte cestu **protokol z absolutního adresáře** a potom vyberte **přidat adresář**. Protokoly jsou zachyceny v zadané kontejnery.

![Adresáře protokolů](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>Protokoly trasování událostí pro Windows
Pokud používáte [události trasování pro Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803\(v=vs.85\).aspx) (ETW) a chcete zaznamenat protokoly trasování událostí pro Windows, vyberte **povolit přenos protokolů trasování událostí pro Windows** zaškrtávací políčko. Chcete-li zvýšit nebo snížit interval mezi přenosu protokolů do účtu úložiště, změňte **doba přenosu (min)** hodnotu.

Události jsou zachyceny ze zdroje událostí a manifestů událostí, které zadáte. K určení zdroje událostí, v **zdroje událostí** , zadejte název a potom vyberte **přidání zdroje událostí**. Podobně můžete určit manifest události v **manifesty událostí** a potom vyberte **přidat Manifest události**.

![Protokoly trasování událostí pro Windows](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

Rozhraní trasování událostí pro Windows se podporuje v technologii ASP.NET pomocí tříd v [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) oboru názvů. Obor názvů Microsoft.WindowsAzure.Diagnostics, který dědí z a rozšiřuje standard [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) třídy, umožňuje použití [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) jako protokolování Architektura v prostředí Azure. Další informace najdete v tématu [převzít kontrolu nad protokolování a trasování v Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) a [povolení diagnostiky v Azure Cloud Services a virtual machines](cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>Výpisy stavu systému
Chcete-li zachytit informace o při instanci role dojde k chybě, vyberte **povolit přenos výpisů stavu systému** zaškrtávací políčko. (Protože ASP.NET zpracovává většina výjimek, to je obecně užitečné pouze pro role pracovního procesu.) Chcete-li zvýšit nebo snížit procento místa věnovaný výpisy při selhání, změňte **kvóta adresáře (%)** hodnotu. Můžete změnit kontejner úložiště, kde jsou uloženy výpisy při selhání a vyberte, zda chcete zaznamenat **úplné** nebo **Mini** s výpisem paměti.

Procesy, které aktuálně nesleduje. jsou uvedeny v rámci následujícího snímku obrazovky. Zaškrtněte políčka pro procesy, které chcete zaznamenat. Jiný proces přidání do seznamu, zadejte název procesu a pak vyberte **přidat proces**.

![Výpisy stavu systému](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

Další informace najdete v tématu [převzít kontrolu nad protokolování a trasování v Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) a [Microsoft Azure Diagnostics část 4: vlastní protokolování komponent a Azure Diagnostics 1.3 změny](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## <a name="view-the-diagnostics-data"></a>Zobrazit diagnostická data
Po jste jste shromáždili diagnostická data pro cloudové služby nebo virtuálního počítače, můžete ho zobrazit.

### <a name="to-view-cloud-service-diagnostics-data"></a>Chcete-li zobrazit data diagnostiky cloudové služby
1. Nasazení cloudové služby jako obvykle a potom ho spusťte.
2. Diagnostická data můžete zobrazit v sestavě, který generuje sada Visual Studio nebo v tabulkách v účtu úložiště. K zobrazení dat v sestavě, otevřete Průzkumníka cloudu nebo Průzkumníka serveru, otevřete místní nabídku uzlu pro roli a pak vyberte **zobrazení diagnostických dat**.
   
    ![Zobrazit diagnostická data](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)
   
    Zobrazí se zpráva, která obsahuje data k dispozici.
   
    ![Microsoft Azure Diagnostics sestav v sadě Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)
   
    Pokud není zobrazena nejnovější data, bude pravděpodobně čekat na uplynutí doba přenosu.
   
    Pokud chcete okamžitě aktualizovat data, vyberte **aktualizovat** odkaz. Pokud chcete, aby automaticky aktualizuje data, vyberte v intervalu **automatické aktualizace** rozevíracího seznamu. Chcete-li exportovat údaje o chybám, vyberte **Export do souboru CSV** pro vytvoření souboru hodnot oddělených čárkami, které můžete otevřít z Excelového listu.
   
    V Průzkumníku cloudu nebo Průzkumníka serveru otevřete účet úložiště, který je spojen s nasazením.
3. Otevřete v tabulkách diagnostiky v prohlížeči tabulku a pak si projděte data, která jste shromáždili. Pro vlastní protokoly a protokoly služby IIS můžete otevřít kontejner objektů blob. V následující tabulce jsou uvedeny tabulky nebo kontejnerů objektů blob, které obsahují data různých protokolových souborech. Kromě dat pro tento soubor protokolu, položek tabulky obsahují **EventTickCount**, **DeploymentId**, **Role**, a **instance role** , abyste mohli snadno identifikovat, které virtuální počítač a role vygenerovala data a kdy. 
   
   | Diagnostická data | Popis | Umístění |
   | --- | --- | --- |
   | Protokoly aplikací |Protokoly, které váš kód generuje voláním metod **System.Diagnostics.Trace** třídy. |WADLogsTable |
   | Protokoly událostí |Data z protokolů událostí Windows na virtuálních počítačích. Windows ukládá informace v těchto protokolech, ale aplikace a služby také použít protokoly pro hlášení chyb nebo protokolování informací. |WADWindowsEventLogsTable |
   | Čítače výkonu |Shromažďování dat na všechny čítače výkonu, který je k dispozici na virtuálním počítači. Operační systém poskytuje čítače výkonu, které zahrnují mnoho statistiky, jako je paměť využití a času procesoru. |WADPerformanceCountersTable |
   | Protokoly infrastruktury |Protokoly, které jsou generovány z diagnostické infrastruktury samotný. |WADDiagnosticInfrastructureLogsTable |
   | Protokoly IIS |Protokoly, které zaznamenávají webových požadavků. Pokud vaše Cloudová služba získá významný objem provozu, tyto protokoly mohou být dlouhé. Je vhodné ke shromažďování a ukládání těchto dat, jenom když je potřebujete. |Můžete najít protokolech se nezdařil požadavek v kontejneru objektů blob v rámci wad-iis-failedreqlogs v cestě pro tohoto nasazení, roli nebo instanci. Můžete najít v části wad-iis-logfiles kompletní protokoly. V tabulce WADDirectories jsou vytvořeny položky pro každý soubor. |
   | Výpisy stavu systému |Poskytuje binární Image cloudové služby proces (obvykle role pracovního procesu). |kontejner objektů blob wad výrobce-výpisů paměti |
   | Vlastní protokoly |Protokoly dat, která je předdefinovaná. |Je můžete zadat v kódu umístění vlastního protokolu souborů ve vašem účtu úložiště. Můžete například zadat kontejner objektů blob vlastní. |
4. Pokud je oříznutá. data libovolného typu, můžete zkusit zvýšit vyrovnávací paměti pro tato data typu nebo zkrácení interval mezi přenosy dat z virtuálního počítače do účtu úložiště.
5. (Volitelné) Vymazat data z účtu úložiště a snížit celkové náklady na úložiště.
6. Po provedení úplné nasazení aktualizaci souboru diagnostics.cscfg (.wadcfgx pro Azure SDK 2.5) v Azure a vaší cloudové služby převezme všechny změny konfigurace diagnostiky. Místo toho při aktualizaci stávajícího nasazení, není aktualizován soubor .cscfg v Azure. Nastavení diagnostiky, můžete stále změnit, pomocí následujících kroků v další části. Další informace o provádění úplné nasazení a aktualizaci stávajícího nasazení najdete v tématu [Průvodce publikováním aplikace Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Chcete-li zobrazit diagnostická data virtuálního počítače
1. V místní nabídce pro virtuální počítač, vyberte **zobrazit diagnostická Data**.
   
    ![Zobrazit diagnostická data ve virtuálním počítači Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)
   
    **Souhrn diagnostiky** zobrazí se dialogové okno.
   
    ![Souhrn diagnostiky virtuálního počítače Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  
   
    Pokud není zobrazena nejnovější data, bude pravděpodobně čekat na uplynutí doba přenosu.
   
    Pokud chcete okamžitě aktualizovat data, vyberte **aktualizovat** odkaz. Pokud chcete, aby automaticky aktualizuje data, vyberte v intervalu **automatické aktualizace** rozevíracího seznamu. Chcete-li exportovat údaje o chybám, vyberte **Export do souboru CSV** pro vytvoření souboru hodnot oddělených čárkami, které můžete otevřít z Excelového listu.

## <a name="set-up-cloud-service-diagnostics-after-deployment"></a>Nastavení diagnostiky cloudové služby po nasazení
Pokud při zkoumání problému s cloudovou službou, která je již spuštěna, můžete chtít shromažďovat data, která jste neurčili, nevložily předtím, než nasadíte původně roli. V takovém případě můžete spustit shromažďování těchto dat tak, že změníte nastavení v Průzkumníku serveru. Můžete nastavení diagnostiky pro jednu instanci nebo pro všechny instance v roli, v závislosti na tom, zda můžete otevřít **konfiguraci diagnostiky** dialogové okno z místní nabídky pro instanci nebo role. Pokud nakonfigurujete roli uzlu, všechny změny, které jste provedli platí pro všechny instance. Pokud nakonfigurujete uzel instance, všechny změny, které jste provedli platí pouze pro tuto instanci.

### <a name="to-set-up-diagnostics-for-a-running-cloud-service"></a>Nastavení diagnostiky pro spuštěné cloudové služby
1. V Průzkumníku serveru rozbalte **Cloud Services** uzel a potom rozbalte seznam uzlů na vyhledání role nebo instance (nebo obojí), kterou chcete prozkoumat.
   
    ![Konfigurace diagnostiky](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)
2. V místní nabídce pro uzel instance nebo role uzlu, vyberte **aktualizace nastavení diagnostiky**a potom vyberte nastavení diagnostiky, které chcete shromažďovat.
   
    Informace o nastavení konfigurace, najdete v části **nastavení zdrojů dat diagnostiky** v tomto článku. Informace o tom, jak zobrazit diagnostická data, najdete v části **zobrazit diagnostická data** v tomto článku.
   
    Pokud změníte shromažďování dat v Průzkumníku serveru, změny zůstávají v platnosti až do plně znovu nasadit cloudovou službu. Pokud použijete výchozí nastavení publikování, změny se nepřepíšou. Výchozí nastavení publikování je k aktualizaci stávajícího nasazení, spíše než provést úplné opětovné nasazení. Aby bylo zajištěno, že nastavení zrušte v době nasazení, přejděte na **Upřesnit nastavení** v Průvodci publikovat a potom zrušte zaškrtnutí **nasazení aktualizace** zaškrtávací políčko. Při opětovném nasazování se toto políčko zaškrtnuto, nastavení vrátit zpět na hodnoty v souboru .wadcfgx (nebo .wadcfg) jako sada prostřednictvím **vlastnosti** editor pro danou roli. Při aktualizaci vašeho nasazení, Azure udržuje předchozí nastavení.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Řešení potíží s Azure cloud service
Pokud narazíte na potíže s vaší projekty cloudových služeb jako roli, která získá zablokované ve stavu "zaneprázdněnosti", opakovaně recykluje nebo vyvolá vnitřní chybě serveru, jsou nástroje a techniky, které můžete použít k diagnostikování a vyřešení problému. Pro konkrétní příklady běžné problémy a řešení a Přehled konceptů a nástroje, které můžete použít k diagnostice a oprava těchto chyb najdete v tématu [diagnostická data služby Azure PaaS compute](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>Dotazy a odpovědi
**Co je velikost vyrovnávací paměti a jak velké by to být?**

Na jednotlivých instancích virtuálních počítačů omezení kvóty, kolik diagnostická data mohou být uloženy v místním systému souborů. Kromě toho můžete zadat velikost vyrovnávací paměti pro každý typ diagnostická data, která je k dispozici. Tato velikost vyrovnávací paměti se chová jako jednotlivé kvótu pro daný typ dat. Určit celkovou kvótu a množství paměti, která zůstává, najdete v dolní části dialogového okna pro typ dat diagnostiky. Pokud chcete zadat větší vyrovnávací paměti nebo dalších typů dat, bude přístup celkové kvóty. Celkové kvótě můžete změnit úpravou konfiguračního souboru diagnostics.wadcfg nebo .wadcfgx. Diagnostická data uložená na stejný systém souborů jako data vaší aplikace. Pokud vaše aplikace používá velké množství místa na disku, zvýšíte nesmí celková kvóta diagnostiky.

**Co je v období a jak dlouho má se?**

Doba přenosu je množství času, který zachycuje uplyne mezi daty. Po každé období jsou data přenášena z místního systému souborů na virtuálním počítači do tabulek ve vašem účtu úložiště. Pokud objem shromážděných dat překročí kvótu před koncem období přenosu, starší data jsou zahozena. Pokud jsou ztráty dat, protože data je větší než velikost vyrovnávací paměti nebo celkové kvóty, můžete snížit dobu přenosu.

**Časové pásmo jsou časová razítka v?**

Časová razítka jsou v místním časovém pásmu datového centra, který je hostitelem vaší cloudové služby. Následující tři razítko sloupce času tabulky protokolů se používají:

* **PreciseTimeStamp**: časové razítko události trasování událostí pro Windows. To znamená, čas události je zaznamenána z klienta.
* **Časové razítko**: hodnota **PreciseTimeStamp** zaokrouhlená dolů hranice frekvence odesílání. Například pokud vaše frekvence odesílání je 5 minut a události času 00:17:12, časové razítko je 00:15:00.
* **Časové razítko**: časové razítko, kdy se entita vytvořila v tabulce Azure.

**Jak můžu spravovat náklady při shromažďování diagnostických informací?**

Výchozí nastavení (**úrovně protokolování** nastavena na **chyba**, a **doba přenosu** nastavena na **1 minuta**) jsou určená k minimalizaci nákladů. Vaše náklady na výpočetní výkon zvýšit, můžete shromažďovat další data diagnostiky nebo snižte periodu přenosu. Není shromažďovat více dat, než potřebujete a nezapomeňte shromažďování dat vypnout, když ho už nepotřebují. Můžete vždy povolit ho znovu, dokonce i v době běhu, jak je popsáno výše v tomto článku.

**Jak shromažďovat protokoly se nezdařil požadavek ze služby IIS?**

Ve výchozím nastavení služby IIS nebude shromažďovat protokoly požadavku se nezdařilo. Služba IIS můžete nastavit shromažďování protokolů se nezdařil požadavek úpravou souboru web.config pro webovou roli.

**Nemám k dispozici informace o trasování z RoleEntryPoint metody, jako je operace OnStart. Co je?**

Metody **RoleEntryPoint** jsou volány v kontextu WAIISHost.exe není ve službě IIS. Informace o konfiguraci v souboru web.config, který obvykle umožňuje trasování neplatí. Chcete-li tento problém vyřešit, přidejte soubor .config do projektu webové role a název souboru tak, aby odpovídaly výstupní sestavení, který obsahuje **RoleEntryPoint** kódu. Ve výchozím nastavení projekt webové role by měl být název souboru .config WAIISHost.exe.config. Do tohoto souboru přidejte následující řádky:

```
<system.diagnostics>
  <trace>
      <listeners>
          <add name “AzureDiagnostics” type=”Microsoft.WindowsAzure.Diagnostics.DiagnosticMonitorTraceListener”>
              <filter type=”” />
          </add>
      </listeners>
  </trace>
</system.diagnostics>
```

V **vlastnosti** okno, nastaveno **kopírovat do výstupního adresáře** vlastnost **vždy Kopírovat**.

## <a name="next-steps"></a>Další postup
Další informace o diagnostice protokolování v Azure najdete v tématu [povolení diagnostiky v Azure Cloud Services a virtual machines](cloud-services/cloud-services-dotnet-diagnostics.md) a [povolit protokolování diagnostiky pro webové aplikace ve službě Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

