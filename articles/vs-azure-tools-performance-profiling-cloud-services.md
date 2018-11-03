---
title: Testování výkonu cloudové služby | Dokumentace Microsoftu
description: Testování výkonu cloudové služby pomocí profileru sady Visual Studio
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
ms.openlocfilehash: aa8416bb6883a1aa76ddd370bf7061d7013904eb
ms.sourcegitcommit: ada7419db9d03de550fbadf2f2bb2670c95cdb21
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/02/2018
ms.locfileid: "50969479"
---
# <a name="testing-the-performance-of-a-cloud-service"></a>Testování výkonu cloudové služby
## <a name="overview"></a>Přehled
Testování výkonu cloudové služby následujícími způsoby:

* Pomocí diagnostiky Azure shromažďovat informace o požadavcích a připojení a ke kontrole Statistika webu, která ukazuje, jak služba provádí z hlediska zákazníků. Chcete-li začít používat, naleznete v tématu [konfigurace diagnostiky pro Azure Cloud Services a Virtual Machines](http://go.microsoft.com/fwlink/p/?LinkId=623009).
* Pomocí profileru sady Visual Studio můžete získat podrobné analýzy výpočetní aspektů jak je služba spuštěna. Toto téma popisuje, jak můžete použít profilování k měření výkonu služby běží v Azure. Informace o tom, jak měřit výkon, protože je služba spuštěna místně v emulátoru compute pomocí profileru, naleznete v tématu [testování výkonu Azure Cloud Service místně v výpočetní emulátor pomocí Visual Studio Profiler](http://go.microsoft.com/fwlink/p/?LinkId=262845).

## <a name="choosing-a-performance-testing-method"></a>Volba metody pro testování výkonu
### <a name="use-azure-diagnostics-to-collect"></a>Pomocí diagnostiky Azure shromažďovat:
* Statistika na webové stránky nebo služby, jako jsou požadavky a připojení.
* Statistické údaje o rolích, jako je například jak často se role restartuje.
* Informace o využití paměti, jako je například procento času, který přijímá systému uvolňování paměti nebo paměti celkové sady spuštěné roli.

### <a name="use-the-visual-studio-profiler-to"></a>Pomocí profileru sady Visual Studio:
* Určení, které funkce zaberou nejvíce času.
* Změřit, jak dlouho trvá každé části výpočetně náročné aplikace.
* Porovnejte sestavy výkonu podrobné pro dvě verze služby.
* Přidělení paměti podrobnější než úroveň přidělení paměti jednotlivých analyzujte.
* Analýza souběžnosti problémy v kódu s více vlákny.

Při použití profiler může shromažďovat data, při spuštění cloudové služby místně nebo v Azure.

### <a name="collect-profiling-data-locally-to"></a>Shromažďování dat profilace místně na:
* Testování výkonu součást cloudové služby, jako je například provádění konkrétní pracovní roli, která nevyžaduje realistické simulované zatížení.
* Testování výkonu cloudové služby izolovaně, řízených podmínek.
* Testování výkonu cloudové služby, než ho nasadíte do Azure.
* Testování výkonu cloudové služby soukromě, nenaruší stávající nasazení.
* Testování výkonu služby, aniž by platili za provoz v Azure.

### <a name="collect-profiling-data-in-azure-to"></a>Shromažďování dat profilace v Azure:
* Testování výkonu cloudové služby v rámci simulované nebo skutečné zatížení.
* Pomocí metody instrumentace shromažďování dat profilování, podle postupu popsaného v tomto tématu později.
* Testování výkonu služby ve stejném prostředí jako při spuštění služby v produkčním prostředí.

Obvykle simulovat zatížení pro test cloudových služeb v rámci normální nebo podmínek vysoké zátěže.

## <a name="profiling-a-cloud-service-in-azure"></a>Profilace cloudové služby v Azure
Při publikování cloudové služby v sadě Visual Studio můžete Profilovat službu a nastavení profilování, které vám poskytnou informace, které chcete. Pro každou instanci role spuštění relace profilování. Další informace o tom, jak publikovat službu ze sady Visual Studio najdete v tématu [publikování do cloudové služby Azure ze sady Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx).

Další informace o výkonu profilace v sadě Visual Studio najdete v tématu [Průvodce začátečníka profilací výkonu](https://msdn.microsoft.com/library/azure/ms182372.aspx) a [Analýza výkonu aplikace pomocí nástrojů pro profilaci](https://msdn.microsoft.com/library/azure/z9z62c29.aspx).

> [!NOTE]
> Můžete povolit IntelliTrace nebo profilování při publikování vaší cloudové služby. Nelze povolit obojí.
> 
> 

### <a name="profiler-collection-methods"></a>Metody kolekce Profiler
Můžete použít jinou kolekci metody profilace, podle vašeho problémy s výkonem:

* **Vzorkování procesoru** – tato metoda shromažďuje statistiky aplikace, které jsou užitečné při počáteční analýze problémy s využitím procesoru. Vzorkování procesoru je navržený způsob spuštění většina vyšetřování výkonu. V aplikaci, která profilujete při shromažďování dat vzorkování procesoru je malý vliv.
* **Instrumentace** – tato metoda shromažďuje podrobných dat časování, které jsou užitečné pro přesně zacílenou analýzu a analyzovat problémy s výkonem vstupu a výstupu. Metoda instrumentace zaznamenává každou položku, ukončení a volání funkce z funkcí v modulu během profilování. Tato metoda je užitečná pro shromažďování podrobných informací o časování o části kódu a pochopení dopadu vstupních a výstupních operací na výkon aplikace. Tato metoda je zakázaná pro počítač s operačním systémem 32-bit. Tato možnost je dostupná pouze při spuštění cloudové služby v Azure, ne místně v emulátoru služby compute.
* **Přidělení paměti .NET** – tato metoda pomocí metoda profilování vzorkování shromažďuje data o přidělování paměti rozhraní .NET Framework. Shromážděná data zahrnují počet a velikost přidělené objekty.
* **Souběžnost** – tato metoda shromažďuje data kolize prostředků a data spuštění procesů a vláken, která je užitečné při analýze více procesů a vícevláknové aplikace. Za použití metody souběžnosti shromažďuje data pro každou událost, pozastaví spuštění kódu, například když vlákno čeká uzamčen přístup k prostředku aplikace k uvolnění. Tato metoda je užitečná pro analýzu vícevláknových aplikacích.
* Můžete také povolit **profilace interakce vrstev**, který poskytuje další informace o spuštění s úspěšností synchronní ADO.NET volání funkce víceúrovňových aplikací, které komunikují po jedné nebo víc databází. Shromažďování dat interakce vrstev s některou z metod profilace. Další informace o profilování interakce vrstev, naleznete v tématu [zobrazení interakcí vrstev](https://msdn.microsoft.com/library/azure/dd557764.aspx).

## <a name="configuring-profiling-settings"></a>Konfigurace nastavení profilace
Následující obrázek ukazuje, jak konfigurovat nastavení profilování v dialogovém okně publikovat aplikaci Azure.

![Konfigurace nastavení profilace](./media/vs-azure-tools-performance-profiling-cloud-services/IC526984.png)

> [!NOTE]
> Povolit **povolit profilaci** zaškrtávací políčko, musí mít profiler nainstalované na místním počítači, který používáte k publikování cloudové služby. Profiler je ve výchozím nastavení nainstalován při instalaci sady Visual Studio.
> 
> 

### <a name="to-configure-profiling-settings"></a>Chcete-li konfigurovat nastavení profilování
1. V Průzkumníku řešení otevřete místní nabídku pro projekt Azure a klikněte na tlačítko **publikovat**. Podrobné pokyny o tom, jak publikovat cloudovou službu, naleznete v tématu [publikování cloudové služby pomocí nástroje Azure](http://go.microsoft.com/fwlink/p?LinkId=623012).
2. V **publikování aplikaci Azure** dialogové okno, zvolili **Upřesnit nastavení** kartu.
3. Chcete-li povolit profilaci, vyberte **povolit profilaci** zaškrtávací políčko.
4. Chcete-li konfigurovat nastavení profilování, zvolte **nastavení** hypertextový odkaz. Zobrazí se dialogové okno nastavení profilace.
5. Z **jakou metodu profilace chcete použít** přepínačů, zvolte typ profilace, že potřebujete.
6. Chcete-li shromažďovat data profilace interakce vrstev, vyberte **povolit profilaci interakce vrstev** zaškrtávací políčko.
7. Chcete-li uložit nastavení, zvolte **OK** tlačítko.
   
    Při publikování této aplikace, tato nastavení slouží k vytvoření relace profilování pro každou roli.

## <a name="viewing-profiling-reports"></a>Zobrazení zprávy o profilování
Relace profilování se vytvoří pro každou instanci role v cloudové službě. Chcete-li zobrazit sestavy profilování každé relace ze sady Visual Studio, můžete zobrazit v okně Průzkumníka serveru a pak zvolte možnost Azure Compute node vybrat instanci role. Pak můžete zobrazit sestavy profilování jak je znázorněno na následujícím obrázku.

![Zobrazit sestavu profilace z Azure](./media/vs-azure-tools-performance-profiling-cloud-services/IC748914.png)

### <a name="to-view-profiling-reports"></a>Chcete-li zobrazit sestavy profilování
1. Chcete-li zobrazit okno Průzkumníka serveru v sadě Visual Studio, zvolte v panelu nabídky zobrazení Průzkumníka serveru.
2. Vyberte uzel Azure Compute a pak vyberte uzel Azure nasazení pro cloudovou službu, kterou jste vybrali profil při publikování ze sady Visual Studio.
3. Chcete-li zobrazit sestavy profilování pro instanci, zvolte roli ve službě, otevřete místní nabídku pro konkrétní instanci a klikněte na tlačítko **zobrazit sestavu profilace**.
   
    Sestavu souboru .vsp se teď stáhne z Azure a stav souboru ke stažení se zobrazí v protokolu aktivit Azure. Po dokončení stahování sestavy profilování se zobrazí na kartě v editoru sady Visual Studio s názvem <Role name> *<Instance Number>* <identifier>.vsp. Souhrnná data pro sestavy se zobrazí.
4. K zobrazení různých zobrazení sestavy v seznamu aktuální zobrazení, zvolte typ zobrazení, které chcete. Další informace najdete v tématu [zobrazeních sestav nástrojů pro profilaci](https://msdn.microsoft.com/library/azure/bb385755.aspx).

## <a name="next-steps"></a>Další postup
[Ladění cloudové služby](https://msdn.microsoft.com/library/azure/ee405479.aspx)

[Publikování do cloudové služby Azure ze sady Visual Studio](https://msdn.microsoft.com/library/azure/ee460772.aspx)

