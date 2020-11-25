---
title: Vizualizace protokolů toku Azure NSG – Power BI
titleSuffix: Azure Network Watcher
description: Naučte se používat Power BI k vizualizaci protokolů toku skupin zabezpečení sítě, které vám umožní zobrazit informace o provozu IP v Azure Network Watcher.
services: network-watcher
documentationcenter: na
author: damendo
ms.service: network-watcher
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: damendo
ms.openlocfilehash: a4c97b8baaf5265f984622e2dc47e6c8c714172a
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/25/2020
ms.locfileid: "95995722"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Vizualizace protokolů toku skupin zabezpečení sítě pomocí Power BI

Protokoly toku skupin zabezpečení sítě umožňují zobrazit informace o příchozích a odchozích přenosech IP adres ve skupinách zabezpečení sítě. Tyto protokoly toku ukazují odchozí a příchozí toky pro každé pravidlo. síťové rozhraní, ke kterému se tok vztahuje, je 5 – informace o toku (zdrojová nebo cílová IP adresa, zdrojový/cílový port, protokol) a pokud byl provoz povolen nebo odepřen.

Může být obtížné získat přehledy o datech protokolování, a to ručním prohledáváním souborů protokolu. V tomto článku poskytujeme řešení pro vizualizaci nejaktuálnějších protokolů toků a další informace o provozu ve vaší síti.

> [!Warning]  
> Následující kroky fungují s protokoly toku verze 1. Podrobnosti najdete v tématu [Úvod do protokolování toků pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md). Následující pokyny nebudou fungovat s verzí 2 souborů protokolu bez úprav.

## <a name="scenario"></a>Scénář

V následujícím scénáři připojíme Power BI plochu k účtu úložiště, který jsme nakonfigurovali jako jímku pro naše data protokolování toku NSG. Po připojení k našemu účtu úložiště Power BI stáhne a analyzuje protokoly, aby poskytovaly vizuální znázornění přenosů protokolovaných skupinami zabezpečení sítě.

Pomocí vizuálů dodaných v šabloně můžete prozkoumávat:

* Hlavní rozhovory
* Data toku časových řad podle směru a rozhodnutí o pravidle
* Toky podle adresy MAC síťového rozhraní
* Toky podle NSG a rule
* Toky podle cílového portu

Poskytnutá šablona je upravitelná, takže ji můžete upravovat, abyste mohli přidat nová data, vizuály nebo upravit dotazy tak, aby vyhovovaly vašim potřebám.

## <a name="setup"></a>Nastavení

Než začnete, musíte mít povolené protokolování toku skupin zabezpečení sítě v jedné nebo několika skupinách zabezpečení sítě ve vašem účtu. Pokyny k povolení protokolů toku zabezpečení sítě najdete v následujícím článku: [Úvod do protokolování toku pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

Musíte mít také nainstalovaného klienta Power BI Desktop v počítači a dostatek volného místa na svém počítači ke stažení a načtení dat protokolu, která existují v účtu úložiště.

![Diagram Visia][1]

### <a name="steps"></a>Postup

1. Stáhněte a otevřete následující šablonu Power BI v šabloně Power BI Desktop aplikace [Network Watcher šablona protokolů Flow PowerBI](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Zadejte požadované parametry dotazu.
   1. **StorageAccountName** – Určuje název účtu úložiště obsahujícího protokoly toku NSG, které chcete načíst a vizualizovat.
   1. **NumberOfLogFiles** – určuje počet souborů protokolu, které byste chtěli stáhnout a vizualizovat v Power BI. Například, pokud je zadána 50, soubor posledních protokolů 50. Pokud máme 2 skupin zabezpečení sítěy povolené a nakonfigurované pro odesílání protokolů NSG Flow do tohoto účtu, můžete si prohlédnout posledních 25 hodin protokolů.

      ![Power BI – hlavní][2]

1. Zadejte přístupový klíč pro váš účet úložiště. Platné přístupové klíče můžete najít tak, že přejdete na svůj účet úložiště v Azure Portal a vyberete **přístupové klíče** z nabídky nastavení. Klikněte na **připojit** a pak použít změny.

    ![přístupové klíče][3]

    ![přístupový klíč 2][4]

4. Vaše protokoly se stahují a analyzují a teď můžete využít předem vytvořené vizuály.

## <a name="understanding-the-visuals"></a>Princip vizuálů

V šabloně jsou k dispozici sady vizuálů, které vám pomůžou vydávat smysl dat protokolu toku NSG. Následující obrázky znázorňují ukázku toho, jak řídicí panel vypadá po naplnění daty. Níže podíváme se na každý vizuál podrobněji. 

![Power BI][5]
 
Vizuál v horních rozhovorech zobrazuje IP adresy, které iniciují nejvíc připojení za zadané období. Velikost polí odpovídá relativnímu počtu připojení. 

![toptalkers][6]

Následující grafy časových řad zobrazují počet toků v průběhu období. Horní graf je rozdělen podle směru toku a nižší je rozdělen podle rozhodnutí učiněného (povolit nebo odepřít). V tomto vizuálu můžete sledovat trendy provozu v průběhu času a zabývat se případnými neobvyklými špičkami nebo poklesem provozu nebo segmentace provozu.

![flowsoverperiod][7]

Následující grafy znázorňují toky pro každé síťové rozhraní s horním segmentem podle směru toku a dolní segmentace podle toho, jaké rozhodnutí bylo provedeno. Pomocí těchto informací můžete získat přehled o tom, které virtuální počítače budou co nejvíc informovat s ostatními, a jestli je provoz na konkrétním virtuálním počítači povolený nebo zakázaný.

![flowspernic][8]

Následující graf prstence znázorňuje rozdělení toků podle cílového portu. Pomocí těchto informací můžete zobrazit nejčastěji používané cílové porty používané v zadaném období.

![prstencový graf][9]

Následující pruhový graf znázorňuje tok podle NSG a rule. S těmito informacemi můžete vidět skupin zabezpečení sítě zodpovědný za většinu provozu a rozpis provozu na NSG podle pravidla.

![barchart][10]
 
V následujících informativních grafech se zobrazují informace o skupin zabezpečení sítě, které jsou k dispozici v protokolech, počtu toků zachycených za období a datu dřívějšího zaznamenaného protokolu. Tyto informace vám poskytnou představu o tom, co skupin zabezpečení sítě se protokoluje, a rozsah dat toků.

![infochart1][11]

![infochart2][12]

Tato šablona obsahuje následující průřezy, které vám umožní zobrazit jenom data, která vás zajímají. Můžete filtrovat podle svých skupin prostředků, skupin zabezpečení sítě a pravidel. Můžete také filtrovat 5 – informace o řazené kolekci členů, rozhodnutí a čas, kdy byl protokol napsán.

![průřezy][13]

## <a name="conclusion"></a>Závěr

V tomto scénáři jsme ukázali, že pomocí protokolů toku skupiny zabezpečení sítě, které poskytuje Network Watcher a Power BI, jsme schopni vizualizovat a pochopit provoz. Pomocí zadané šablony Power BI stáhne protokoly přímo z úložiště a zpracuje je místně. Doba potřebná k načtení šablony se liší v závislosti na počtu požadovaných souborů a celkové velikosti stahovaných souborů.

Tuto šablonu si můžete přizpůsobit podle svých potřeb. Existuje mnoho způsobů, jak můžete použít Power BI s protokoly toku skupin zabezpečení sítě. 

## <a name="notes"></a>Poznámky

* Protokoly ve výchozím nastavení jsou uloženy v `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Pokud jiná data existují v jiném adresáři, dotazy na vyžádání a zpracování dat musí být upraveny.

* Poskytnutá šablona se nedoporučuje používat s více než 1 GB protokolů.

* Pokud máte velké množství protokolů, doporučujeme prozkoumat řešení pomocí jiného úložiště dat, jako je Data Lake nebo SQL Server.

## <a name="next-steps"></a>Další kroky

Naučte se vizualizovat protokoly toku NSG pomocí elastického zásobníku tím, že navštívíte [vizualizace Azure Network WATCHER NSG Flow log pomocí nástrojů Open Source](network-watcher-visualize-nsg-flow-logs-open-source-tools.md) .

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
