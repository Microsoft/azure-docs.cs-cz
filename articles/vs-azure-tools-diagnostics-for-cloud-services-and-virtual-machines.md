---
title: Nastavení diagnostiky pro Azure Cloud Services a virtuálních počítačů | Microsoft Docs
description: Naučte se nastavení diagnostiky pro ladění Azure cloude služeb a virtuálních počítačů (VM) v sadě Visual Studio.
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
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: 34c667b0a594682e4d099e7bff64bfdb336b850b
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
ms.locfileid: "30292536"
---
# <a name="set-up-diagnostics-for-azure-cloud-services-and-virtual-machines"></a>Nastavení diagnostiky pro Azure Cloud Services a virtuálních počítačů
Když budete potřebovat řešení služby Azure cloudové služby nebo virtuálního počítače, můžete snadno nastavit Azure Diagnostics Visual Studio. Diagnostika zaznamená data systému a protokolování dat na virtuálních počítačů a instancí virtuálního počítače se systémem cloudové služby. Diagnostická data se přenáší na účet úložiště, který zvolíte. Další informace o diagnostiku protokolování v Azure, najdete v části [povolit protokolování diagnostiky pro webové aplikace v Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

V tomto článku jsme ukazují, jak pomocí sady Visual Studio k zapnutí a nastavení Azure Diagnostics před a po nasazení. Zjistěte, jak nastavit diagnostiky na virtuálních počítačích Azure, jak vybrat typy shromažďovat diagnostické informace a postup zobrazení informace po shromáždění zpracovat.

Nastavení Azure Diagnostics můžete použít jednu z následujících možností:

* Změnit nastavení diagnostiky v **konfigurace diagnostiky** dialogové okno v sadě Visual Studio. Nastavení se ukládají v souboru s názvem diagnostics.wadcfgx (Azure SDK 2.4 a starších soubor se nazývá diagnostics.wadcfg). Můžete také přímo upravit konfigurační soubor. Pokud soubor ručně aktualizovat, konfigurace změny vstoupí v platnost při příštím nasazení cloudu služby Azure nebo službu spustit v emulátoru.
* Pomocí Průzkumníku cloudu nebo Průzkumníka serveru změňte nastavení diagnostiky pro cloudové služby nebo virtuálního počítače, který běží v sadě Visual Studio.

## <a name="azure-sdk-26-diagnostics-changes"></a>Azure SDK 2.6 diagnostiky změny
Tyto změny použít na Azure SDK 2.6 a novější projekty v sadě Visual Studio:

* Místní emulátoru nyní podporuje diagnostiku. To znamená, že můžete shromažďovat diagnostická data a ujistěte se, že vaše aplikace vytvoří správné trasování, při vývoji a testování v sadě Visual Studio. Připojovací řetězec `UseDevelopmentStorage=true` zapne shromažďování dat diagnostiky, když spouštíte projekt cloudové služby v sadě Visual Studio pomocí emulátoru úložiště Azure. Všechny diagnostická data se shromažďují v účtu úložiště vývoj úložiště.
* Připojovací řetězec účet úložiště diagnostiky `Microsoft.WindowsAzure.Plugins.Diagnostics.ConnectionString` je uložen v souboru (.csdef) služby. V Azure SDK 2.5 je v souboru diagnostics.wadcfgx zadán účet úložiště diagnostiky.

Připojovací řetězec funguje jinak některé klíče způsoby ve verzi 2.6 Azure SDK a později versus Azure SDK 2.4 a dříve:

* Azure SDK 2.4 a starší slouží připojovací řetězec jako modul runtime modulu plug-in diagnostiky se získat informace o účtu úložiště pro přenos diagnostické protokoly.
* V Azure SDK 2.6 nebo novější Visual Studio používá připojovací řetězec diagnostiky nastavit rozšíření diagnostiky Azure s informace o účtu příslušné úložiště během publikování. Připojovací řetězec lze definovat jiným účtům úložiště pro různé služby konfigurace, které sada Visual Studio používá při publikování. Ale protože modul plug-in diagnostics není k dispozici po Azure SDK 2.5, soubor .cscfg samostatně nemůže nastavit rozšíření diagnostiky. Musíte vytvořit rozšíření samostatně pomocí nástrojů Visual Studio nebo prostředí PowerShell.
* Ke zjednodušení procesu nastavení rozšíření diagnostiky pomocí prostředí PowerShell, balíček výstup ze sady Visual Studio obsahuje veřejné konfigurace XML pro rozšíření diagnostiky pro každou roli. Visual Studio používá k naplnění informací o účtu úložiště v veřejná konfigurace diagnostiky připojovací řetězec. Veřejné konfigurační soubory se vytvoří ve složce rozšíření. Veřejné konfigurační soubory pomocí vzoru pro pojmenovávání PaaSDiagnostics. &lt;název role\>. PubConfig.xml. Všechna nasazení pomocí prostředí PowerShell můžete použít tento vzor pro mapování každé konfiguraci k roli.
* [Portál Azure](http://go.microsoft.com/fwlink/p/?LinkID=525040) používá připojovací řetězec v souboru .cscfg pro přístup k datům diagnostiky. Data se zobrazí na **monitorování** kartě. Připojovací řetězec je potřeba nastavit službu, kterou chcete zobrazit podrobné údaje z monitorování na portálu.

## <a name="migrate-projects-to-azure-sdk-26-and-later"></a>Migrace projekty k Azure SDK 2.6 a novějším
Když provádíte migraci z Azure SDK 2.5 Azure SDK 2.6 nebo novější, pokud jste měli účet úložiště diagnostiky, který je zadaný v souboru .wadcfgx, účet úložiště zůstane v tomto souboru. Abyste mohli využívat flexibilitu používání jiným účtům úložiště pro jiné úložiště, ručně přidejte připojovací řetězec do projektu. Pokud projekt jste migraci z Azure SDK 2.4 nebo starší na Azure SDK 2.6, se zachovají diagnostiky připojovací řetězce. Upozorňujeme však, změny v tom, jak jsou považovány připojovací řetězce ve verzi 2.6 SDK Azure, popsané v předchozí části.

### <a name="how-visual-studio-determines-the-diagnostics-storage-account"></a>Určuje, jak Visual Studio účet úložiště diagnostiky
* Pokud diagnostiky připojovací řetězec je zadané v souboru .cscfg, Visual Studio se používá k nastavení rozšíření diagnostiky, při publikování a vygeneruje soubory XML veřejné konfigurace během balení.
* Pokud připojovací řetězec diagnostics není zadané v souboru .cscfg, Visual Studio se vrátí k používání účtu úložiště, který je zadaný v souboru .wadcfgx nastavit rozšíření diagnostiky pro publikování a pro generování veřejné konfigurace XML soubory během balení.
* Diagnostika připojovací řetězec v souboru .cscfg má přednost před účet úložiště v souboru .wadcfgx. Pokud diagnostiky připojovací řetězec zadaný v souboru .cscfg Visual Studio používá tento připojovací řetězec a ignoruje .wadcfgx účet úložiště.

### <a name="what-does-the-update-development-storage-connection-strings-check-box-do"></a>Zaškrtněte políčko "Aktualizovat vývoj úložiště připojovací řetězce..." k čemu slouží?
**Aktualizace vývoj úložiště připojovací řetězce pro diagnostiku a ukládání do mezipaměti pomocí přihlašovacích údajů účtu úložiště Microsoft Azure při publikování do služby Microsoft Azure** zaškrtávací políčko je pohodlný způsob, jak aktualizovat jakékoli vývoj úložiště účet připojení řetězce, které zadáte během publikování účtu úložiště Azure.

Například pokud vyberete toto zaškrtávací políčko a diagnostiky připojovací řetězec Určuje `UseDevelopmentStorage=true`, při publikování tohoto projektu v Azure, Visual Studio automaticky aktualizovat připojovací řetězec diagnostiky účet úložiště, který jste zadali v Průvodce Publikovat. Ale pokud účet úložiště skutečné byl zadán jako připojovací řetězec diagnostiky, tento účet se používá místo.

## <a name="diagnostics-functionality-differences-in-azure-sdk-24-and-earlier-vs-azure-sdk-25-and-later"></a>Diagnostické funkce rozdíly v Azure SDK 2.4 a starší vs. Azure SDK, 2.5 nebo novější
Pokud provádíte upgrade projektu z Azure SDK 2.4 a dříve na Azure SDK 2.5 nebo novější, mějte na paměti následující rozdíly funkce diagnostics:

* **Rozhraní API pro konfiguraci jsou zastaralé**. Programová konfigurace diagnostiky je k dispozici v Azure SDK 2.4 a starší, ale je zastaralý v Azure SDK 2.5 nebo novější. Pokud vaše konfigurace diagnostiky je aktuálně definována v kódu, je nutné překonfigurovat nastavení těchto od začátku v migrovaných projektu pro diagnostiku na pokračovat v práci. Soubor konfigurace diagnostiky pro Azure SDK 2.4 je diagnostics.wadcfg. Diagnostika konfiguračního souboru pro Azure SDK 2.5 a později je diagnostics.wadcfgx.
* **Diagnostika pro cloudové služby aplikace lze nastavit pouze na úrovni role**. V Azure SDK 2.5 nebo novější nemůže nastavit diagnostiku pro aplikace cloudové služby na úrovni instance.
* **Pokaždé, když nasadíte aplikaci, konfiguraci diagnostiky se aktualizuje**. Pokud změníte konfiguraci diagnostiky z Průzkumníka serveru Visual Studia a pak znovu nasadit aplikace, může to způsobovat problémy parita.
* **V Azure SDK 2.5 nebo novější, výpisy stavu systému jsou nakonfigurované v konfiguračním souboru diagnostiky a není v kódu**. Pokud máte nakonfigurované v kódu výpisy stavu systému, musíte ručně přenést konfigurace z kódu do konfiguračního souboru. Výpisy stavu systému nejsou přenášeny během migrace do Azure SDK 2.6.

## <a name="turn-on-diagnostics-in-cloud-service-projects-before-you-deploy-them"></a>Zapněte diagnostiku v projekty cloudových služeb před jejich nasazením
V sadě Visual Studio můžete shromažďovat diagnostická data pro role, které jsou spuštěny v Azure při spouštění služby v emulátoru před nasazením. Všechny změny nastavení diagnostiky v sadě Visual Studio jsou uloženy v konfiguračním souboru diagnostics.wadcfgx. Tato nastavení zadejte účet úložiště, kde je uložena diagnostická data při nasazení cloudové služby.

[!INCLUDE [cloud-services-wad-warning](../includes/cloud-services-wad-warning.md)]

### <a name="to-turn-on-diagnostics-in-visual-studio-before-deployment"></a>Chcete-li na Diagnostika v sadě Visual Studio před nasazením

1. V místní nabídce pro roli, vyberte **vlastnosti**. V role **vlastnosti** dialogové okno, vyberte **konfigurace** kartě.
2. V **diagnostiky** část, ujistěte se, že **povolení diagnostiky** je zaškrtnuté políčko.
   
    ![Přístup k možnosti Povolit diagnostiky](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796660.png)
3. Chcete-li zadat účet úložiště pro data diagnostiky, vyberte tlačítko se třemi tečkami (...).
   
    ![Zadejte účet úložiště, který chcete použít](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796661.png)
4. V **vytvoření připojovacího řetězce úložiště** dialogové okno pole, určete, zda se chcete připojit pomocí emulátoru úložiště Azure, předplatné Azure, nebo ručně zadali přihlašovací údaje.
   
    ![Dialogové okno účtu úložiště](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796662.png)
   
   * Pokud vyberete **emulátor úložiště Microsoft Azure**, připojovací řetězec je nastavena na `UseDevelopmentStorage=true`.
   * Pokud vyberete **vaše předplatné**, můžete vybrat předplatné Azure, kterou chcete použít a zadejte název účtu. Chcete-li spravovat předplatné Azure, vyberte **Správa účtů**.
   * Pokud vyberete **ručně zadali přihlašovací údaje**, zadejte název a klíč účtu Azure, který chcete použít.
5. Chcete-li zobrazit **konfigurace diagnostiky** dialogové okno, vyberte **konfigurace**. S výjimkou **Obecné** a **adresáře protokolu**, každé kartě představuje zdroj dat diagnostiky, který můžete shromáždit. Výchozí hodnota **Obecné** karta nabízí následující Diagnostika možnosti shromažďování dat: **pouze chyby**, **všechny informace o**, a **vlastní plán**. Výchozí hodnota **pouze chyby** možnost používá minimálně velikost úložiště, protože nepřenese upozornění nebo trasování zpráv. **Všechny informace o** možnost přenáší informace na maximum, používá úložiště na maximum a je proto nejnákladnější možnost.
   
    ![Povolení a konfigurace Azure Diagnostika](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
6. V tomto příkladu vyberte **vlastní plán** možnost, takže můžete přizpůsobit shromážděná data.
7. V **kvóty disku v MB** pole, můžete nastavit kolik místa k přidělení ve vašem účtu úložiště pro data diagnostiky. Můžete změnit nebo přijměte výchozí hodnotu.
8. Na každé kartě diagnostická data, která chcete shromáždit, vyberte **povolit přenos z \<protokolu zadejte\>**  zaškrtávací políčko. Například, pokud chcete shromažďovat protokoly aplikací na **protokoly aplikací** vyberte **povolit přenos protokoly aplikací** zaškrtávací políčko. Zadejte také další informace, který je požadován pro každý typ dat diagnostiky. Informace o konfiguraci pro každé kartě, najdete v části **nastavení zdrojů dat diagnostiky** dále v tomto článku. 
9. Až poprvé povolíte shromažďování dat diagnostiky chcete, vyberte **OK**.
10. Spusťte projekt Azure cloud service v sadě Visual Studio jako obvykle. Při používání aplikace je uložit informace protokolu, které jste povolili k účtu úložiště Azure, který jste zadali.

## <a name="turn-on-diagnostics-on-azure-virtual-machines"></a>Zapněte diagnostiku na virtuálních počítačích Azure
V sadě Visual Studio můžete shromažďování dat diagnostiky pro virtuální počítače Azure.

### <a name="to-turn-on-diagnostics-on-azure-virtual-machines"></a>Chcete-li na Diagnostika na virtuálních počítačích Azure

1. V Průzkumníku serveru vyberte uzel, Azure a potom se připojte k předplatnému Azure, pokud jste již připojeni.
2. Rozbalte **virtuální počítače** uzlu. Můžete vytvořit nový virtuální počítač nebo vyberte existující uzel.
3. V místní nabídce pro virtuální počítač, který chcete, vyberte **konfigurace**. Zobrazí se dialogové okno Konfigurace virtuálního počítače.
   
    ![Konfigurace virtuálního počítače Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796663.png)
4. Pokud ještě není nainstalovaná, přidejte rozšíření diagnostiky agenta monitorování společnosti Microsoft. S touto příponou můžete shromáždit data diagnostiky pro virtuální počítač Azure. V části **nainstalovaná rozšíření**v **vyberte dostupné rozšíření** rozevíracího seznamu, vyberte **Microsoft Monitoring Agent Diagnostics**.
   
    ![Instalace rozšíření virtuálního počítače Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766024.png)
   
    > [!NOTE]
   > Další rozšíření diagnostiky jsou k dispozici pro virtuální počítače. Další informace najdete v tématu [rozšíření virtuálního počítače a funkce ve Windows](https://docs.microsoft.com/azure/virtual-machines/windows/extensions-features).
   > 
   > 
5. Chcete-li přidat rozšíření a zobrazte jeho **konfigurace diagnostiky** dialogové okno, vyberte **přidat**.
6. Chcete-li zadat účet úložiště, vyberte **konfigurace**a potom vyberte **OK**.
   
    Každé kartě (s výjimkou **Obecné** a **adresáře protokolu**) představuje zdroj dat diagnostiky, který můžete shromáždit.
   
    ![Povolení a konfigurace Azure Diagnostika](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758144.png)
   
    Na kartě Výchozí **Obecné**, nabízí následující možnosti shromažďování dat diagnostiky: **pouze chyby**, **všechny informace o**, a **vlastní plán**. Výchozí možnost **pouze chyby**, využívá nejmenší velikost úložiště, protože nepřenese upozornění nebo trasování zpráv. **Všechny informace o** možnost přenáší informace na maximum a je proto nejnákladnější možnost z hlediska úložiště.
7. V tomto příkladu vyberte **vlastní plán** shromážděných možnost tak data můžete přizpůsobit.
8. **Kvóty disku v MB** pole určuje, kolik místa, které chcete přidělit ve vašem účtu úložiště pro data diagnostiky. Pokud chcete, můžete změnit výchozí hodnotu.
9. Na každé kartě chcete shromažďovat diagnostická data, vyberte jeho **povolit přenos z \<protokolu zadejte\>**  zaškrtávací políčko.
   
    Například pokud chcete shromažďovat protokoly aplikací, vyberte **povolit přenos protokoly aplikací** v zaškrtávací políčko **protokoly aplikací** kartě. Zadejte také další informace, které je nutné pro každý typ dat diagnostiky. Informace o konfiguraci pro každé kartě, najdete v části **nastavení zdrojů dat diagnostiky** dále v tomto článku.
10. Když jste povolili kolekce všechna data diagnostiky, které chcete, vyberte **OK**.
11. Uložte aktualizovaný projekt.
    
    Zprávy v **protokoly aktivit Microsoft Azure** okno znamená, že virtuální počítač se aktualizovala.

## <a name="set-up-diagnostics-data-sources"></a>Nastavit zdroje dat diagnostiky
Jakmile povolíte shromažďování dat diagnostiky, můžete zvolit přesně jaké zdroje dat, který chcete shromáždit, a shromažďovaných informací. Další části popisují karty v **konfigurace diagnostiky** dialogové okno a znamená, co jednotlivé možnosti konfigurace.

### <a name="application-logs"></a>Protokoly aplikací
Protokoly aplikací mít diagnostické informace, které je produkovaný webové aplikace. Pokud chcete zaznamenat protokoly aplikací, vyberte **povolit přenos protokoly aplikací** zaškrtávací políčko. Chcete-li zvýšit nebo snížit interval mezi přenos protokoly aplikace do svého účtu úložiště, změňte **doba přenosu (min.)** hodnotu. Množství informací o zachycenou v protokolu nastavení také můžete změnit **úrovně protokolování** hodnotu. Vyberte například **podrobné** a získejte další informace, nebo vyberte **kritický** k zachycení pouze kritické chyby. Pokud máte diagnostiky specifické pro zprostředkovatele, který vysílá protokoly aplikací, můžete zaznamenávat protokoly přidáním GUID poskytovatele v **identifikátor GUID** pole.

  ![Protokoly aplikací](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758145.png)

Další informace o protokoly aplikací najdete v tématu [povolit protokolování diagnostiky pro webové aplikace v Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

### <a name="windows-event-logs"></a>Protokoly událostí Windows
Chcete-li zaznamenat protokol událostí systému Windows, vyberte **povolit přenos protokoly událostí systému Windows** zaškrtávací políčko. Chcete-li zvýšit nebo snížit interval mezi přenosu protokolů událostí na váš účet úložiště, změňte **doba přenosu (min.)** hodnotu. Zaškrtněte políčka pro typy událostí, které chcete sledovat.

![Protokoly událostí](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796664.png)

Pokud používáte Azure SDK 2.6 nebo novější a chcete určit vlastní zdroj dat, zadejte ho **\<název zdroje dat\>** textového pole a pak vyberte **přidat**. Zdroj dat je přidaný do souboru diagnostics.cfcfg.

Pokud používáte Azure SDK 2.5 a chcete zadat vlastní zdroj dat, můžete přidat jej do `WindowsEventLog` části diagnostics.wadcfgx souborů, jako v následujícím příkladu:

```
<WindowsEventLog scheduledTransferPeriod="PT1M">
   <DataSource name="Application!*" />
   <DataSource name="CustomDataSource!*" />
</WindowsEventLog>
```
### <a name="performance-counters"></a>Čítače výkonu
Informace o čítači výkonu můžete vyhledat problémová místa systému a optimalizovat výkon systému a aplikací. Další informace najdete v tématu [vytvoření a použití čítače výkonu v aplikaci Azure](https://msdn.microsoft.com/library/azure/hh411542.aspx). Chcete-li zaznamenat čítače výkonu, vyberte **povolit přenos čítače výkonu** zaškrtávací políčko. Chcete-li zvýšit nebo snížit interval mezi přenosu protokolů událostí na váš účet úložiště, změňte **doba přenosu (min.)** hodnotu. Zaškrtněte políčka pro čítače výkonu, které chcete sledovat.

![Čítače výkonu](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758147.png)

Pokud chcete sledovat čítače výkonu, který není uveden, zadejte pomocí syntaxe navrhované čítače výkonu. a pak vyberte **přidat**. Operační systém na virtuálním počítači určuje, které můžete sledovat čítače výkonu. Další informace o syntaxi najdete v tématu [zadejte cestu k čítači](https://msdn.microsoft.com/library/windows/desktop/aa373193.aspx).

### <a name="infrastructure-logs"></a>Protokoly infrastruktury
Protokoly infrastruktury mít informace o infrastrukturu Azure diagnostiky, modul RemoteAccess a RemoteForwarder modulu. Ke shromažďování informací o protokoly infrastruktury, vyberte **povolit přenos infrastruktury protokoly** zaškrtávací políčko. Chcete-li zvýšit nebo snížit interval mezi přenos infrastruktury protokoly do vašeho účtu úložiště, změňte **doba přenosu (min.)** hodnotu.

![Diagnostické protokoly infrastruktury](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC758148.png)

Další informace najdete v tématu [shromažďování dat protokolování pomocí Azure Diagnostics](https://msdn.microsoft.com/library/azure/gg433048.aspx).

### <a name="log-directories"></a>Protokol adresáře
Adresáře protokolu mají data shromážděná z adresáře protokolu pro žádosti o Internetové informační služby (IIS), neúspěšné požadavky nebo složky, které zvolíte. Když Pokud chcete zachytit protokolu adresáře, vyberte **povolit přenos protokolu adresáře** zaškrtávací políčko. Chcete-li zvýšit nebo snížit interval mezi přenos protokoly do vašeho účtu úložiště, změňte **doba přenosu (min.)** hodnotu.

Zaškrtněte políčka protokolů, které chcete shromažďovat, jako například **protokoly služby IIS** a **se nezdařilo požadavku** protokoly. Názvy kontejnerů výchozí úložiště jsou k dispozici, ale můžete změnit názvy.

Můžete zaznamenat protokoly z libovolné složky. Zadejte cestu v **protokolu z adresáře absolutní** a pak vyberte **přidat adresář**. Protokoly jsou zachyceny v zadané kontejnerech.

![Protokol adresáře](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796665.png)

### <a name="etw-logs"></a>Protokoly trasování událostí pro Windows
Pokud používáte [trasování událostí pro Windows](https://msdn.microsoft.com/library/windows/desktop/bb968803\(v=vs.85\).aspx) (ETW) a chcete zaznamenat protokoly trasování událostí pro Windows, vyberte **povolit přenos protokolů trasování událostí pro Windows** zaškrtávací políčko. Chcete-li zvýšit nebo snížit interval mezi přenos protokoly do vašeho účtu úložiště, změňte **doba přenosu (min.)** hodnotu.

Zaznamenání události ze zdroje událostí a manifestů událostí, které zadáte. Chcete-li určit zdroje událostí, v **zdroje událostí** , zadejte název a potom vyberte **přidat zdroj události**. Podobně můžete zadat manifest událostí v **událostí manifesty** a pak vyberte **přidat Manifest událostí**.

![Protokoly trasování událostí pro Windows](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766025.png)

Rozhraní framework trasování událostí pro Windows je podporována v technologii ASP.NET pomocí třídy v [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) oboru názvů. Obor názvů Microsoft.WindowsAzure.Diagnostics, který dědí z a rozšiřuje standard [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) třídy, umožňuje použití [System.Diagnostics.aspx](https://msdn.microsoft.com/library/system.diagnostics(v=vs.110)) jako protokolování Architektura v prostředí Azure. Další informace najdete v tématu [převzít kontrolu nad protokolování a trasování v Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) a [zapněte diagnostiku Azure Cloud Services a virtuálních počítačů](cloud-services/cloud-services-dotnet-diagnostics.md).

### <a name="crash-dumps"></a>Výpisy stavu systému
K zaznamenání informací o když dojde k chybě instanci role, vyberte **povolit přenos havárií výpisy** zaškrtávací políčko. (Protože ASP.NET zpracovává většina výjimky, to je obecně užitečné pouze pro role pracovního procesu.) Chcete-li zvýšit nebo snížit podíl úložiště věnované výpisy stavu systému, změňte **Directory kvóty (%)** hodnotu. Kontejner úložiště, kde jsou uloženy výpisy stavu systému a vyberte, zda chcete zaznamenat, můžete změnit **úplné** nebo **malé** výpis.

Procesů, které jsou aktuálně sledované jsou uvedeny další snímku obrazovky. Zaškrtněte políčka u procesů, které chcete zaznamenat. Pokud chcete přidat do seznamu jiný proces, zadejte název procesu a potom vyberte **přidat proces**.

![Výpisy stavu systému](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766026.png)

Další informace najdete v tématu [převzít kontrolu nad protokolování a trasování v Microsoft Azure](https://msdn.microsoft.com/magazine/ff714589.aspx) a [Microsoft Azure Diagnostics část 4: vlastní protokolování součásti a změny Azure Diagnostics 1.3](http://justazure.com/microsoft-azure-diagnostics-part-4-custom-logging-components-azure-diagnostics-1-3-changes/).

## <a name="view-the-diagnostics-data"></a>Zobrazení dat diagnostiky
Poté, co jste shromažďovaných dat diagnostiky pro cloudové služby nebo virtuálního počítače, můžete ji zobrazit.

### <a name="to-view-cloud-service-diagnostics-data"></a>Chcete-li zobrazit data diagnostiky cloudové služby
1. Nasazení cloudové služby jako obvyklé a potom ho spusťte.
2. Diagnostická data můžete zobrazit v sestavě, která generuje Visual Studio nebo v tabulkách ve vašem účtu úložiště. K zobrazení dat v sestavě, otevřete Průzkumník cloudu nebo Průzkumníka serveru, otevřete místní nabídce uzlu pro roli a pak vyberte **zobrazení diagnostických dat**.
   
    ![Zobrazení dat diagnostiky](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748912.png)
   
    Zobrazí se sestavu, která obsahuje všechna dostupná data.
   
    ![Microsoft Azure Diagnostics sestavy v sadě Visual Studio](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796666.png)
   
    Pokud není zobrazena, aby nejnovější data, můžete chtít čekat na v období uplynout.
   
    Pokud chcete okamžitě aktualizovat data, vyberte **aktualizovat** odkaz. Aby byla data automaticky aktualizován, vyberte interval v **automatického obnovení** rozevíracího seznamu. Pokud chcete exportovat Chyba dat, vyberte **exportovat do souboru CSV** tlačítko vytvořte soubor hodnot oddělených čárkami, který lze otevřít v listu aplikace Excel.
   
    V Průzkumníku cloudu nebo v Průzkumníku serveru otevřete účet úložiště, který je spojen s nasazením.
3. Otevřete v prohlížeči tabulky tabulky diagnostiky a poté zkontrolovat data, která jste shromáždili. Pro vlastní protokoly a protokoly služby IIS můžete otevřít kontejner objektů blob. Následující tabulka uvádí tabulek a kontejnery objektů blob, které obsahují data různých protokolových souborech. Kromě dat pro tento soubor protokolu, položek tabulky obsahují **EventTickCount**, **DeploymentId**, **Role**, a **RoleInstance** , který vám pomůže identifikovat, které virtuální počítač a role generované data a kdy. 
   
   | Diagnostických dat | Popis | Umístění |
   | --- | --- | --- |
   | Protokoly aplikací |Protokoly, které generuje kód voláním metod **System.Diagnostics.Trace** třídy. |WADLogsTable |
   | Protokoly událostí |Data z protokolů událostí systému Windows na virtuálních počítačích. Systém Windows ukládá informace v těchto protokolech, ale aplikace a služby také použít protokoly zprávy o chybách nebo protokolování informací. |WADWindowsEventLogsTable |
   | Čítače výkonu |Můžete shromáždit data pro všechny čítače výkonu, který je k dispozici na virtuálním počítači. Operační systém poskytuje čítače výkonu, které zahrnují mnoho statistiky, jako je čas procesoru a využití paměti. |WADPerformanceCountersTable |
   | Protokoly infrastruktury |Protokoly, které se generují z infrastruktury diagnostiky, sám sebe. |WADDiagnosticInfrastructureLogsTable |
   | Protokoly IIS |Protokoly, které zaznamenávají webových požadavků. Cloudové služby získá významný objem provozu, tyto protokoly mohou být náročná. Je vhodné pro shromažďování a uložení dat této pouze v případě potřeby ho. |Můžete najít, že se nezdařilo žádost o protokolech v kontejneru objektů blob v části wad-iis-failedreqlogs v cestě pro tohoto nasazení, role a instance. Můžete najít v části wad. Služba iis logfiles dokončení protokoly. V tabulce WADDirectories jsou vytvořeny položky pro každý soubor. |
   | Výpisy stavu systému |Poskytuje binární bitové kopie Cloudová služba proces (obvykle role pracovního procesu). |kontejner objektů blob wad. deformační výpisy |
   | Vlastní protokol soubory |Protokoluje data, která jste předdefinované. |Zadávat lze v kódu umístění vlastního protokolu souborů ve vašem účtu úložiště. Můžete například zadat kontejner objektů blob vlastní. |
4. Pokud se zkrátí dat jakéhokoli typu, můžete zkusit zvýšit vyrovnávací paměti pro data typu nebo zkrátit interval mezi přenosy dat z virtuálního počítače do svého účtu úložiště.
5. (Volitelné) Vyprázdnit data z účtu úložiště příležitostně ke snížení celkové náklady na úložiště.
6. Po provedení úplné nasazení aktualizaci souboru diagnostics.cscfg (.wadcfgx pro Azure SDK 2.5) v Azure a cloudové služby převezme žádné změny konfigurace diagnostiky. Pokud místo toho aktualizaci stávajícího nasazení, se neaktualizuje soubor .cscfg v Azure. Můžete je stále změnit nastavení diagnostiky, ale podle kroků v další části. Další informace o provádění úplné nasazení a aktualizaci stávajícího nasazení najdete v tématu [Průvodci publikováním aplikace Azure](vs-azure-tools-publish-azure-application-wizard.md).

### <a name="to-view-virtual-machine-diagnostics-data"></a>Chcete-li zobrazit data diagnostiky virtuálního počítače
1. V místní nabídce pro virtuální počítač, vyberte **zobrazení diagnostická Data**.
   
    ![Zobrazení dat diagnostiky ve virtuálním počítači Azure](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC766027.png)
   
    **Diagnostiky Souhrn** zobrazí se dialogové okno.
   
    ![Virtuální počítač Azure diagnostics souhrn](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC796667.png)  
   
    Pokud není zobrazena, aby nejnovější data, můžete chtít čekat na v období uplynout.
   
    Pokud chcete okamžitě aktualizovat data, vyberte **aktualizovat** odkaz. Aby byla data automaticky aktualizován, vyberte interval v **automatického obnovení** rozevíracího seznamu. Pokud chcete exportovat Chyba dat, vyberte **exportovat do souboru CSV** tlačítko vytvořte soubor hodnot oddělených čárkami, který lze otevřít v listu aplikace Excel.

## <a name="set-up-cloud-service-diagnostics-after-deployment"></a>Nastavení diagnostiky cloudové služby po nasazení
Pokud jste příčin problému s cloudovou službu, která je již spuštěn, můžete chtít shromažďování dat, které jste neurčili předtím, než jste původně nasadili roli. V takovém případě můžete spustit shromažďování dat změnou nastavení v Průzkumníku serveru. Můžete nastavit diagnostiky pro jednu instanci nebo pro všechny instance v roli, v závislosti na tom, jestli můžete otevřít **konfigurace diagnostiky** dialogové okno z místní nabídky pro instance nebo pro roli. Pokud nakonfigurujete uzlu role, všechny změny, které provedete se vztahují na všechny instance. Pokud nakonfigurujete uzlu instance, všechny změny, které provedete platí pouze pro tuto instanci.

### <a name="to-set-up-diagnostics-for-a-running-cloud-service"></a>Nastavení diagnostiky pro spuštěné cloudové služby
1. V Průzkumníku serveru rozbalte **cloudové služby** uzel a potom rozbalte seznam uzlů najít, role nebo instance (nebo oba), které chcete prozkoumat.
   
    ![Konfigurace diagnostiky](./media/vs-azure-tools-diagnostics-for-cloud-services-and-virtual-machines/IC748913.png)
2. V místní nabídce uzlu instance nebo role uzlu, vyberte **nastavení diagnostiky aktualizace**a potom vyberte nastavení diagnostiky, které chcete shromažďovat.
   
    Informace o nastavení konfigurace, najdete v části **nastavení zdrojů dat diagnostiky** v tomto článku. Informace o tom, jak zobrazit data diagnostiky, najdete v části **zobrazit data diagnostiky** v tomto článku.
   
    Pokud změníte shromažďování dat v Průzkumníku serveru, změny zůstávají platná, dokud plně znovu nasadit cloudové služby. Pokud použijete výchozí nastavení publikování, změny se nepřepíšou. Výchozí nastavení publikování je k aktualizaci stávajícího nasazení, nikoli udělat úplné opětovné nasazení. Aby se zajistilo, že nastavení vymazat v době nasazení, přejděte na **Upřesnit nastavení** v Průvodci publikovat a poté zrušte zaškrtnutí **aktualizaci nasazení** zaškrtávací políčko. Při opětovném s této políčko nezaškrtnete, nastavení vrátit zpět na hodnoty v souboru .wadcfgx (nebo .wadcfg) jako sada prostřednictvím **vlastnosti** editor pro roli. Po aktualizaci nasazení se zachová Azure dřívější nastavení.

## <a name="troubleshoot-azure-cloud-service-issues"></a>Řešení problémů služby Azure cloud
Pokud máte potíže s vaše projekty cloudových služeb, jako jsou role, kterou vázne "zaneprázdněn" stav, opakovaně recykluje nebo vyvolá o vnitřní chybu serveru, jsou nástroje a techniky, které můžete použít diagnostikovat a opravit potíže. Pro konkrétní příklady běžné problémy a řešení a informace o konceptech a nástroje, které můžete použít k diagnostikování a opravte tyto chyby, najdete v části [Azure PaaS výpočetní diagnostická data](http://blogs.msdn.com/b/kwill/archive/2013/08/09/windows-azure-paas-compute-diagnostics-data.aspx).

## <a name="q--a"></a>Dotazy a odpovědi
**Jaká je velikost vyrovnávací paměti a jak velké by to být?**

Na každou instanci virtuálního počítače kvóty omezit, kolik dat diagnostiky může být uložený v místním systému souborů. Kromě toho zadáte velikost vyrovnávací paměti pro každý typ dat diagnostiky, která je k dispozici. Tato velikost vyrovnávací paměti se chová jako individuální kvótu pro daný typ dat. Celkové kvóty a množství paměti, která zůstává, zjistíte v dolní části dialogového okna pro datový typ diagnostiky. Pokud zadáte větší vyrovnávací paměti nebo další typy dat, budete postupovat celkové kvóty. Celkové kvóty, můžete změnit úpravou konfiguračního souboru diagnostics.wadcfg nebo .wadcfgx. Data diagnostiky se ukládají na stejný systém souborů jako data aplikace. Pokud vaše aplikace používá velké množství místa na disku, zvýšíte nesmí celkové kvóty diagnostiky.

**Co je v období a jak dlouho by měl být?**

V období je množství času, který uplyne mezi dat zaznamená. Po každé období přenos dat se přesune ze místního systému souborů na virtuálním počítači tabulky ve vašem účtu úložiště. Pokud množství dat, které jsou shromážděny překročí kvótu před koncem období přenosu, budou zahozeny starší data. Pokud jsou ztráty dat, protože data je větší než velikost vyrovnávací paměti nebo celkové kvóty, můžete snížit v období.

**Jaké časové pásmo je časová razítka v?**

Časová razítka jsou v místním časovém pásmu datového centra, který je hostitelem cloudové služby. Používají se následující sloupce tři časové razítko v tabulkách protokolu:

* **PreciseTimeStamp**: časové razítko trasování událostí pro Windows události. To znamená, čas událost je protokolována z klienta.
* **Časové razítko**: hodnota **PreciseTimeStamp** zaokrouhlená dolů hranic frekvence odesílání. Například pokud vaše nahrávání četnost je 5 minut a události čas 00:17:12, časové razítko je 00:15:00.
* **Časové razítko**: časové razítko, ve kterém byla vytvořena entita v tabulce Azure.

**Jak lze spravovat náklady, při shromažďování diagnostických informací?**

Výchozí nastavení (**úrovně protokolování** nastavena na **chyba**, a **přenos období** nastavena na **1 minuta**) jsou navržená k minimalizaci náklady. Náklady na výpočetní zvýšit, můžete shromažďovat další data diagnostiky nebo snižte periodu přenosu. Nemáte shromažďovat více dat, než je třeba a nezapomeňte zakázat shromažďování dat, když už nepotřebujete. Můžete vždy ho znovu povolit, i v době běhu, jak je popsáno výše v tomto článku.

**Jak můžu shromáždit protokoly se nezdařilo žádost ze služby IIS?**

Ve výchozím nastavení služba IIS není shromažďovat protokoly žádosti se nezdařilo. Služba IIS můžete nastavit úpravou souboru web.config pro vaši webovou roli shromažďovat protokoly žádosti se nezdařilo.

**Nezobrazují se informace o trasování z metod RoleEntryPoint jako OnStart. Co je?**

Metody **RoleEntryPoint** se nazývají v kontextu WAIISHost.exe, není ve službě IIS. Informace o konfiguraci v souboru web.config, který obvykle netýká umožňuje trasování. Chcete-li vyřešit tento problém, přidejte souboru .config do projektu webové role a název souboru tak, aby odpovídaly sestavení výstupu, který obsahuje **RoleEntryPoint** kódu. Ve výchozím projektu webové role musí být název souboru .config WAIISHost.exe.config. Do tohoto souboru přidejte následující řádky:

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

V **vlastnosti** nastavte **kopírovat do výstupního adresáře** vlastnost **vždy Kopírovat**.

## <a name="next-steps"></a>Další postup
Další informace o diagnostiku protokolování v Azure najdete v tématu [zapněte diagnostiku Azure Cloud Services a virtuálních počítačů](cloud-services/cloud-services-dotnet-diagnostics.md) a [povolit protokolování diagnostiky pro webové aplikace v Azure App Service](app-service/web-sites-enable-diagnostic-log.md).

