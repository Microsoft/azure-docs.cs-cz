---
title: Vizualizace protokolů toku Azure NSG – Power BI
titleSuffix: Azure Network Watcher
description: Tato stránka popisuje, jak vizualizovat protokoly toku nsg pomocí Power BI.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: 955e13b88037aa42b59707698549b1c980720990
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840600"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Vizualizace protokolů toku skupiny zabezpečení sítě pomocí Power BI

Protokoly toku skupiny zabezpečení sítě umožňují zobrazit informace o příchozím přenosu dat a odchozím přenosech IP ve skupinách zabezpečení sítě. Tyto protokoly toku zobrazují odchozí a příchozí toky na základě pravidla, nic toku se vztahuje na, 5-n-tice informace o toku (Zdroj/Cíl IP, Zdrojový/cílový port, protokol), a pokud byl povolen provoz nebo odepřen.

Může být obtížné získat přehled o datech protokolování toku ručním prohledáváním souborů protokolu. V tomto článku poskytujeme řešení pro vizualizaci nejnovějších protokolů toku a informace o provozu v síti.

> [!Warning]  
> Následující kroky pracují s protokoly toku verze 1. Podrobnosti naleznete [v tématu Úvod k protokolování toku pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md). Následující pokyny nebudou fungovat s verzí 2 souborů protokolu bez úprav.

## <a name="scenario"></a>Scénář

V následujícím scénáři připojíme desktop Power BI k účtu úložiště, který jsme nakonfigurovali jako jímku pro naše data protokolování toku nsg. Po připojení k našemu účtu úložiště Power BI stáhne a analyzuje protokoly, aby poskytoval vizuální reprezentaci provozu, který zaznamenává skupiny zabezpečení sítě.

Pomocí vizuálů dodaných v šabloně můžete prozkoumat:

* Nejlepší talkers
* Data toku časových řad podle směru a rozhodnutí o pravidlech
* Toky podle adresy MAC síťového rozhraní
* Toky podle nSG a pravidla
* Toky podle cílového portu

Poskytnutá šablona je upravitelná, takže ji můžete upravit tak, aby přidávala nová data, vizuály nebo upravovala dotazy tak, aby vyhovovaly vašim potřebám.

## <a name="setup"></a>Nastavení

Než začnete, musíte mít v účtu povoleno protokolování toku skupiny zabezpečení sítě v jedné nebo více skupinách zabezpečení sítě. Pokyny k povolení protokolů toku zabezpečení sítě naleznete v následujícím článku: [Úvod do protokolování toku pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

Musíte mít v počítači nainstalovaného klienta Power BI Desktop a dostatek volného místa v počítači, abyste mohli stáhnout a načíst data protokolu, která existují ve vašem účtu úložiště.

![Diagram visia][1]

### <a name="steps"></a>Kroky

1. Stažení a otevření následující šablony Power BI v [šabloně toku Power](https://aka.ms/networkwatcherpowerbiflowlogstemplate) BI Desktop Application Network Watcher PowerBI
1. Zadejte požadované parametry dotazu
   1. **StorageAccountName** – Určuje název účtu úložiště obsahující protokoly toku skupiny NSG, které chcete načíst a vizualizovat.
   1. **NumberOfLogFiles** – Určuje počet souborů protokolu, které chcete stáhnout a vizualizovat v Power BI. Například pokud 50 je zadán, 50 nejnovější soubory protokolu. Pokud máme 2 nsgs povoleno a nakonfigurován tak, aby odesílat protokoly toku nsg na tento účet, pak posledních 25 hodin protokolů lze zobrazit.

      ![napájení BI hlavní][2]

1. Zadejte přístupový klíč pro svůj účet úložiště. Platné přístupové klíče najdete tak, že přejdete ke svému účtu úložiště na webu Azure portal a vyberete **přístupové klávesy** z nabídky Nastavení. Klikněte na **Připojit** a potom aplikujte změny.

    ![přístupové klávesy][3]

    ![přístupový klíč 2][4]

4. Vaše protokoly jsou staženy a analyzovány a nyní můžete využít předem vytvořené vizuály.

## <a name="understanding-the-visuals"></a>Pochopení vizuálů

V šabloně je k dispozici sada vizuálů, které pomáhají pochopit data protokolu toku nsg. Následující obrázky ukazují ukázku toho, jak řídicí panel vypadá, když je naplněn daty. Níže prozkoumáme každý vizuál podrobněji 

![Power BI][5]
 
Vizuál Top Talkers zobrazuje ip adresy, které iniciovaly nejvíce připojení za zadané období. Velikost polí odpovídá relativnímu počtu připojení. 

![toptalkers][6]

Následující grafy časových řad zobrazují počet toků za období. Horní graf je segmentován směrem toku a dolní je segmentována podle provedeného rozhodnutí (povolit nebo odepřít). Pomocí tohoto vizuálu můžete prozkoumat trendy provozu v průběhu času a odhalit jakékoli abnormální špičky nebo pokles návštěvnosti nebo segmentace provozu.

![období přepojení][7]

Následující grafy ukazují toky na síťové rozhraní, přičemž horní segment je segmentován podle směru toku a dolní segmentace podle rozhodnutí. Pomocí těchto informací můžete získat přehled o tom, které z vašich virtuálních počítačů komunikovaly nejvíce ve srovnání s ostatními a pokud je povolen nebo odepřen provoz na konkrétní virtuální počítač.

![průtokové pernic][8]

Následující graf dvojkolí zobrazuje rozpis toků podle cílového portu. Pomocí těchto informací můžete zobrazit nejčastěji používané cílové porty používané v zadaném období.

![prstencový graf][9]

Následující pruhový graf znázorňuje tok podle nsg a pravidla. Pomocí těchto informací můžete zobrazit sítě nsg odpovědné za největší provoz a rozdělení provozu na nsg podle pravidla.

![barchart][10]
 
Následující informační grafy zobrazují informace o sítích nsg, které jsou přítomny v protokolech, počet toků zachycených za období a datum nejstaršího zachyceného protokolu. Tyto informace poskytují představu o tom, jaké jsou protokolovány soubory nsg a rozsah dat toků.

![informační mapa1][11]

![informační mapa2][12]

Tato šablona obsahuje následující průřezy, které umožňují zobrazit pouze data, která vás nejvíce zajímají. Můžete filtrovat podle skupin prostředků, skupin nsg a pravidel. Můžete také filtrovat na 5 n-tice informace, rozhodnutí a čas zápisu protokolu.

![Průřezy][13]

## <a name="conclusion"></a>Závěr

V tomto scénáři jsme ukázali, že pomocí protokolů toku skupiny zabezpečení sítě, které poskytují Služby sledování sítě a Power BI, jsme schopni vizualizovat a pochopit provoz. Pomocí poskytnuté šablony Power BI stáhne protokoly přímo z úložiště a zpracuje je místně. Doba načítání šablony se liší v závislosti na počtu požadovaných souborů a celkové velikosti stažených souborů.

Neváhejte a přizpůsobit tuto šablonu pro vaše potřeby. Existuje mnoho způsobů, jak můžete Power BI používat s protokoly toku skupiny zabezpečení sítě. 

## <a name="notes"></a>Poznámky

* Protokoly jsou ve výchozím nastavení uloženy`https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Pokud v jiném adresáři existují jiná data, musí být upraveny dotazy na vyžádat a zpracovat data.

* Poskytnutá šablona se nedoporučuje používat s více než 1 GB protokolů.

* Pokud máte velké množství protokolů, doporučujeme prozkoumat řešení pomocí jiného úložiště dat, jako je Data Lake nebo SQL server.

## <a name="next-steps"></a>Další kroky

Naučte se vizualizovat protokoly toku nsg pomocí elastického zásobníku pomocí [visualize protokolů toku NSG služby Azure Network Watcher pomocí nástrojů s otevřeným zdrojovým kódem](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

[1]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure1.png
[2]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure2.png
[3]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure3.png
[4]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure4.png
[5]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure5.png
[6]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure6.png
[7]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure7.png
[8]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure8.png
[9]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure9.png
[10]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure10.png
[11]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure11.png
[12]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure12.png
[13]: ./media/network-watcher-visualize-nsg-flow-logs-power-bi/figure13.png
