---
title: Testování výkonu cloudové služby | Microsoft Docs
description: Testování výkonu pomocí sady Visual Studio profiler cloudové služby
services: visual-studio-online
documentationcenter: n/a
author: mikejo
manager: douge
editor: ''
ms.assetid: 7a5501aa-f92c-457c-af9b-92ea50914e24
ms.service: visual-studio-online
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: multiple
ms.workload: na
ms.date: 11/11/2016
ms.author: mikejo
ms.openlocfilehash: 74ffd70bdb204cee75b86a1e922ac67873a88758
ms.sourcegitcommit: 34e0b4a7427f9d2a74164a18c3063c8be967b194
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2018
ms.locfileid: "30292148"
---
# <a name="testing-the-performance-of-a-cloud-service"></a>Testování výkonu cloudové služby
## <a name="overview"></a>Přehled
Výkon cloudové služby můžete otestovat následujícími způsoby:

* Pomocí Azure Diagnostics ke shromažďování informací o připojení a požadavky a zkontrolovat lokality statistiky, které ukazují, jak službu provádí z hlediska zákazníka. Chcete-li začít používat, přečtěte si téma [konfigurace diagnostiky pro Azure Cloud Services a virtuálních počítačů](http://go.microsoft.com/fwlink/p/?LinkId=623009).
* Visual Studio profiler použijte k získání podrobné analýzu výpočetní aspektů jak služba bude spuštěna. Toto téma popisuje, jak je můžete profileru k měření výkonu služby běží v Azure. Informace o tom, jak používat profileru k měření výkonu služby běží místně v emulátoru výpočetní najdete v tématu [testování výkonu služby Azure Cloud Service místně v výpočetní emulátor pomocí Visual Studio Profiler](http://go.microsoft.com/fwlink/p/?LinkId=262845).

## <a name="choosing-a-performance-testing-method"></a>Výběr výkonu testování – metoda
### <a name="use-azure-diagnostics-to-collect"></a>Pomocí Azure Diagnostics lze shromažďovat:
* Statistika na webové stránky nebo služby, například požadavky a připojení.
* Statistika na rolí, například jak často se restartuje roli.
* Informace o využití paměti, jako je například procento čas, který přebírá uvolňování nebo paměť celkové sada spuštěné roli.

### <a name="use-the-visual-studio-profiler-to"></a>Pomocí sady Visual Studio profiler na:
* Určete, které funkce zaberou nejvíce času.
* Měří jak dlouho trvá jednotlivých součástí výpočetně náročné program.
* Porovnejte výkon podrobné sestavy pro dvě verze služby.
* Přidělení paměti podrobněji, než je úroveň přidělení paměti jednotlivých analyzujte.
* Analýza problémů souběžnosti v kódu s více vlákny.

Při použití profileru spuštění cloudové služby místně nebo v Azure můžete shromáždit data.

### <a name="collect-profiling-data-locally-to"></a>Shromážděte data profilování místně na:
* Testování výkonu součástí cloudové služby, jako je například spuštění konkrétního pracovního role, která nevyžaduje realistické simulované zatížení.
* Testování výkonu cloudové služby v izolaci za kontrolovaných podmínek.
* Testování výkonu cloudové služby, než ji nasadit do Azure.
* Testování výkonu Cloudová služba soukromě, bez narušení existující nasazení.
* Testování výkonu služby, aniž by docházelo k poplatky za běžící v Azure.

### <a name="collect-profiling-data-in-azure-to"></a>Data profilování v Azure a shromážděte:
* Testování výkonu cloudové služby v simulovaném ani skutečné zatížení.
* Pomocí metody instrumentace shromažďování dat o profilování, jak toto téma popisuje později.
* Testování výkonu služby ve stejném prostředí, jako když je služba spuštěna v produkčním prostředí.

Obvykle můžete simulovat zátěžového testu cloudové služby v rámci normální nebo zátěžové požadavky.

## <a name="profiling-a-cloud-service-in-azure"></a>Profilace cloudové služby v Azure
Když publikujete cloudové služby ze sady Visual Studio, můžete profilu služby a zadejte profilování nastavení, které poskytují informace, které chcete. Pro každou instanci role spuštění profilování relace. Další informace o tom, jak publikovat službu ze sady Visual Studio najdete v tématu [publikování do cloudové služby Azure ze sady Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Bližší informace o profilace výkonu v sadě Visual Studio naleznete v tématu [Průvodce začátečníka profilací výkonu](https://msdn.microsoft.com/library/azure/ms182372.aspx) a [Analýza výkonu aplikace pomocí nástrojů pro profilaci](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

> [!NOTE]
> Můžete povolit IntelliTrace nebo profilace při publikování cloudové služby. Nelze povolit obě.
> 
> 

### <a name="profiler-collection-methods"></a>Kolekce metod profileru
Můžete použít jinou kolekci metody pro profilování, založené na problémy s výkonem:

* **Vzorkování procesoru** -tato metoda shromažďuje statistiky aplikace, které jsou užitečné pro počáteční analýzy problémy s využitím procesoru. Vzorkování procesoru je navržené způsob spuštění většina vyšetřování výkonu. Je nízký dopad na aplikaci, která jsou profilace při shromažďování dat vzorkování procesoru.
* **Instrumentace** -tato metoda shromažďuje podrobné časování data, která je užitečný pro přesně zacílené analýzy a k analýze problémy s výkonem vstupu a výstupu. Metoda instrumentace zaznamenává každou položku, ukončení a volání funkce funkce v modulu během profilace spustit. Tato metoda je užitečná pro shromažďování časování podrobné informace o části kódu a porozumění vlivu vstupních a výstupních operací na výkon aplikace. Tato metoda je zakázána pro počítač s operačním systémem 32-bit. Tato možnost je dostupná pouze při spuštění cloudové služby v Azure, ne místně v emulátoru služby výpočty v.
* **Přidělení paměti .NET** -tato metoda shromažďuje údaje o přidělení paměti rozhraní .NET Framework s použitím vzorkování metoda profilování. Shromážděná data zahrnují počet a velikost přidělené objektů.
* **Concurrency** -tato metoda shromažďuje data kolizí prostředku a proces a přístup z více vláken provádění data, která jsou užitečné při analýze aplikace Vícevláknová a více procesy. Metoda souběžného zpracování shromažďuje data pro všechny události, že bloky provádění kódu, například když vlákno čeká uzamčena přístup k prostředku aplikace na uvolnění. Tato metoda je užitečná pro analýzu vícevláknové aplikace.
* Můžete také povolit **profilace interakce vrstvy**, který poskytuje další informace o provádění časy synchronní ADO.NET volání funkcí víceúrovňových aplikací, které komunikují s jednu nebo více databází. Můžete shromáždit dat interakce vrstev s některou z metod profilování. Další informace o vytváření profilů interakce vrstvy najdete v tématu [zobrazení interakcí vrstev](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Profilování nastavení konfigurace.
Následující obrázek ukazuje, jak nakonfigurovat nastavení profilování z dialogového okna publikovat aplikace Azure.

![Konfigurace nastavení pro profilaci](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

> [!NOTE]
> Chcete-li povolit **povolit profilace** zaškrtávací políčko, musí mít profileru nainstalovaná v místním počítači, který používáte k publikování cloudové služby. Ve výchozím nastavení je profileru nainstalována při instalaci sady Visual Studio.
> 
> 

### <a name="to-configure-profiling-settings"></a>Ke konfiguraci nastavení pro profilaci
1. V Průzkumníku řešení otevřete místní nabídky projektu Azure a pak zvolte **publikovat**. Podrobné pokyny o tom, jak publikovat cloudové služby najdete v tématu [publikování cloudové služby pomocí nástroje Azure](http://go.microsoft.com/fwlink/p?LinkId=623012).
2. V **publikování aplikaci Azure** dialogové okno, zvolili **Upřesnit nastavení** kartě.
3. Chcete-li profilace, vyberte **povolit profilace** zaškrtávací políčko.
4. Chcete-li nakonfigurovat nastavení profilování, zvolte **nastavení** hypertextový odkaz. Zobrazí se dialogové okno nastavení profilace.
5. Z **profilace jakou metodu chcete použít** možnost tlačítka, vyberte typ profilace, je nutné.
6. Chcete-li shromažďovat data profilace sledováním interakce vrstev, vyberte **povolit profilace interakce vrstvy** zaškrtávací políčko.
7. Chcete-li uložit nastavení, klikněte **OK** tlačítko.
   
    Při publikování této aplikace, tato nastavení slouží k vytvoření relace profilování pro každou roli.

## <a name="viewing-profiling-reports"></a>Zobrazení sestavy profilaci
Relace profilování se vytvoří pro každou instanci role v rámci cloudové služby. K zobrazení sestav profilace každé relace ze sady Visual Studio, můžete zobrazit okno Průzkumníka serveru a pak vyberte uzel Azure Compute vyberte instanci role. Potom můžete zobrazit sestavy profilaci jak je znázorněno na následujícím obrázku.

![Zobrazit sestavy profilaci z Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Chcete-li zobrazit sestav profilace
1. Chcete-li zobrazit okno Průzkumníka serveru v sadě Visual Studio, zvolte v řádku nabídek zobrazení Průzkumníka serveru.
2. Zvolte Azure výpočetní uzel a pak vyberte uzel nasazení Azure pro cloudovou službu, která jste vybrali profil, když jste publikovali ze sady Visual Studio.
3. K zobrazení sestav profilace pro instanci, vyberte roli v rámci služby, otevřete místní nabídku pro konkrétní instanci a zvolte **zobrazit sestavu profilace**.
   
    Sestavy, soubor .vsp je nyní stáhne z Azure a stavu stahování, zobrazí se v protokolu aktivit Azure. Po dokončení stahování profilování sestavy se zobrazí na kartě v editoru pro sadu Visual Studio s názvem <Role name> *<Instance Number>* <identifier>.vsp. Zobrazí se souhrn dat pro sestavu.
4. Pokud chcete zobrazit různá zobrazení sestavy, v seznamu aktuální zobrazení, vyberte typ zobrazení, které chcete. Další informace najdete v tématu [zobrazeních sestav nástrojů pro profilaci](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>Další postup
[Ladění cloudové služby](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publikování do cloudové služby Azure ze sady Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

