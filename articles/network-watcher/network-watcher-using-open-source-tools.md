---
title: Vizualizace vzorů síťového provozu pomocí nástrojů s otevřeným zdrojovým kódem
titleSuffix: Azure Network Watcher
description: Tato stránka popisuje, jak pomocí zachytávání paketů Sledování sítě s Capanalysis vizualizovat vzory provozu do a z virtuálních počítačů.
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
ms.openlocfilehash: f36db28b58cd57b6407019b378a82632aa6c6228
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "76840651"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Vizualizace vzorců síťového provozu do a z virtuálních počítačů pomocí nástrojů s otevřeným zdrojovým kódem

Sběr paketů obsahuje síťová data, která umožňují provádět forenzní analýzy sítě a hloubkovou kontrolu paketů. Existuje mnoho otevře zdrojové nástroje, které můžete použít k analýze zachytávání paketů získat přehled o vaší síti. Jedním z takových nástrojů je CapAnalysis, open-source paket zachytit vizualizační nástroj. Vizualizace dat sběru paketů je cenný způsob, jak rychle odvodit přehledy o vzorcích a anomáliích v rámci sítě. Vizualizace také poskytují způsob sdílení těchto poznatků snadno spotřebním způsobem.

Azure Network Watcher poskytuje možnost zachytit data tím, že umožňuje provádět zachycení paketů ve vaší síti. Tento článek poskytuje návod, jak vizualizovat a získat poznatky ze sběru paketů pomocí CapAnalysis s Network Watcher.

## <a name="scenario"></a>Scénář

Máte jednoduchou webovou aplikaci nasazenou na virtuálním počítači v Azure, která chce použít open source nástroje k vizualizaci síťového provozu, abyste mohli rychle identifikovat vzory toku a případné anomálie. Pomocí sledování sítě můžete získat zachycení paketů síťového prostředí a přímo jej uložit do účtu úložiště. CapAnalysis pak můžete ingestovat zachycení paketu přímo z objektu blob úložiště a vizualizovat jeho obsah.

![scénář][1]

## <a name="steps"></a>Kroky

### <a name="install-capanalysis"></a>Instalace CapAnalysis

Chcete-li nainstalovat CapAnalysis na virtuální mačká, můžete se podívat na oficiální pokyny zde https://www.capanalysis.net/ca/how-to-install-capanalysis.
Chcete-li přístup CapAnalysis vzdáleně, musíte otevřít port 9877 na vašem virtuálním počítači přidáním nové příchozí pravidlo zabezpečení. Další informace o vytváření pravidel ve skupinách zabezpečení sítě naleznete v části [Vytvoření pravidel v existující skupině zabezpečení sítě](../virtual-network/manage-network-security-group.md#create-a-security-rule). Po úspěšném přidání pravidla byste měli mít přístup k CapAnalysis z`http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Spuštění relace sběru paketů pomocí sledovacího programu sítě Azure

Sledování sítě umožňuje zachytit pakety pro sledování provozu ve virtuálním počítači a z jeho virtuálního počítače. Můžete odkazovat na pokyny na [Spravovat zachytávání paketů s Sledovací mše sítě](network-watcher-packet-capture-manage-portal.md) spustit relaci sběru paketů. Sběr paketů lze uložit do objektu blob úložiště, ke kterým má přístup CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Nahrání sběru paketů do CapAnalysis
Můžete přímo nahrát sběr paketů pořízené sledovacím programem sítě pomocí karty Import z adresy URL a poskytnutím odkazu na objekt blob úložiště, ve kterém je uloženo zachycení paketu.

Při poskytování odkazu na CapAnalysis nezapomeňte připojit token SAS k adrese URL objektu blob úložiště.  Chcete-li to provést, přejděte na sdílený přístupový podpis z účtu úložiště, určete povolená oprávnění a stisknutím tlačítka Generovat SAS vytvořte token. Potom můžete připojit token SAS k adrese URL objektu blob úložiště zachytávání paketů.

Výsledná adresa URL bude vypadat podobně jako následující adresa URL:http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Analýza zachycení paketů

CapAnalysis nabízí různé možnosti pro vizualizaci zachycení paketů, z nichž každá poskytuje analýzu z jiné perspektivy. Pomocí těchto vizuálních souhrnů můžete porozumět trendům síťového provozu a rychle rozpoznat neobvyklou aktivitu. Některé z těchto funkcí jsou uvedeny v následujícím seznamu:

1. Tabulky toku

    Tato tabulka obsahuje seznam toků v datech paketů, časové razítko přidružené k tokům a různé protokoly přidružené k toku, stejně jako zdrojové a cílové IP adresy.

    ![stránka toku capanalysis][5]

1. Přehled protokolu

    Toto podokno umožňuje rychle zobrazit distribuci síťového provozu v různých protokolech a zeměpisných oblastech.

    ![capanalysis protokol přehled][6]

1. Statistika

    Toto podokno umožňuje zobrazit statistiky síťového provozu – bajty odeslané a přijaté ze zdrojových a cílových IP adresy, toky pro každý zdrojový a cílový IP adresy, protokol používaný pro různé toky a dobu trvání toků.

    ![capanalysis statistiky][7]

1. Geomapa

    Toto podokno poskytuje zobrazení mapy síťového provozu s barevnými změnami podle objemu provozu z každé země nebo oblasti. Můžete vybrat zvýrazněné země nebo oblasti a zobrazit další statistiky toku, jako je podíl dat odeslaných a přijatých z IP služeb v této zemi nebo oblasti.

    ![geomapa][8]

1. Filtry

    CapAnalysis poskytuje sadu filtrů pro rychlou analýzu konkrétních paketů. Můžete například filtrovat data podle protokolu, abyste získali konkrétní přehledy o této podmnožině provozu.

    ![filtry][11]

    Navštivte [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about) další informace o všech možnostech CapAnalysis.

## <a name="conclusion"></a>Závěr

Funkce zachytávání paketů sledovacího programu sledování sítě umožňuje zachytit data potřebná k provedení forenzníanalýzy sítě a lepšímu pochopení síťového provozu. V tomto scénáři jsme ukázali, jak lze snadno integrovat zachycení paketů z nástroje Sledování sítě s nástroji pro vizualizaci s otevřeným zdrojovým kódem. Pomocí open source nástrojů, jako je CapAnalysis, k vizualizaci sběrů paketů můžete provádět hloubkovou kontrolu paketů a rychle identifikovat trendy v rámci síťového provozu.

## <a name="next-steps"></a>Další kroky

Další informace o protokolech toku nsg, navštivte [protokoly toku nsg](network-watcher-nsg-flow-logging-overview.md)

Přečtěte si, jak vizualizovat protokoly toku nsg pomocí Power BI na stránce [Visualize Protokoly toků nsg pomocí Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
<!--Image references-->

[1]: ./media/network-watcher-using-open-source-tools/figure1.png
[2]: ./media/network-watcher-using-open-source-tools/figure2.png
[3]: ./media/network-watcher-using-open-source-tools/figure3.png
[4]: ./media/network-watcher-using-open-source-tools/figure4.png
[5]: ./media/network-watcher-using-open-source-tools/figure5.png
[6]: ./media/network-watcher-using-open-source-tools/figure6.png
[7]: ./media/network-watcher-using-open-source-tools/figure7.png
[8]: ./media/network-watcher-using-open-source-tools/figure8.png
[9]: ./media/network-watcher-using-open-source-tools/figure9.png
[10]: ./media/network-watcher-using-open-source-tools/figure10.png
[11]: ./media/network-watcher-using-open-source-tools/figure11.png
