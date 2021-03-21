---
title: Vizualizace vzorů síťových přenosů pomocí nástrojů Open Source
titleSuffix: Azure Network Watcher
description: Tato stránka popisuje, jak použít Network Watcher zachytávání paketů s Capanalysis k vizualizaci vzorců provozu do a z vašich virtuálních počítačů.
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
ms.openlocfilehash: 0503f6910186b42bf381c662c7942e37c28bfdf0
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101709508"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Vizualizace vzorů síťových přenosů z vašich virtuálních počítačů a z nich pomocí Open-source nástrojů

Zachycení paketů obsahují síťová data, která umožňují provádět forenzní sítě a hloubkovou kontrolu paketů. Existuje mnoho nástrojů pro vytváření zdrojů, pomocí kterých můžete analyzovat zachycení paketů a získat přehled o vaší síti. Jedním z těchto nástrojů je CapAnalysis, což je open source nástroj pro vizualizaci zachytávání paketů. Vizualizace dat zachycení paketů představuje cenný způsob, jak rychle odvodit přehledy o vzorech a anomáliích ve vaší síti. Vizualizace také poskytují prostředky ke sdílení těchto přehledů snadno účinným způsobem.

Azure Network Watcher poskytuje možnost zachytit data tím, že vám umožní zachytávání paketů v síti. Tento článek poskytuje přehled o tom, jak vizualizovat a získávat poznatky z zachycení paketů pomocí CapAnalysis s využitím Network Watcher.

## <a name="scenario"></a>Scenario

Máte jednoduchou webovou aplikaci nasazenou na virtuálním počítači v Azure, chcete použít Open Source nástroje k vizualizaci svého síťového provozu, abyste mohli rychle identifikovat modely toků a případné anomálie. Pomocí Network Watcher můžete získat zachycení paketů v síťovém prostředí a přímo ho uložit do svého účtu úložiště. CapAnalysis může následně ingestovat zachytávání paketů přímo z objektu BLOB úložiště a vizualizovat jeho obsah.

![scénář][1]

## <a name="steps"></a>Postup

### <a name="install-capanalysis"></a>Nainstalovat CapAnalysis

Pokud chcete nainstalovat CapAnalysis na virtuální počítač, můžete si tady přečtěte oficiální pokyny https://www.capanalysis.net/ca/how-to-install-capanalysis .
V zájmu vzdáleného přístupu k CapAnalysis je potřeba na svém VIRTUÁLNÍm počítači otevřít port 9877 přidáním nového příchozího pravidla zabezpečení. Další informace o vytváření pravidel ve skupinách zabezpečení sítě najdete v tématu [Vytvoření pravidel v existující NSG](../virtual-network/manage-network-security-group.md#create-a-security-rule). Po úspěšném přidání pravidla byste měli mít přístup k CapAnalysis z `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Spuštění relace zachytávání paketů pomocí Azure Network Watcher

Network Watcher umožňuje zachytit pakety pro sledování provozu na virtuálním počítači a z něj. Postup, jak spustit relaci zachycení paketů, najdete v pokynech v tématu [Správa zachytávání paketů pomocí Network Watcher](network-watcher-packet-capture-manage-portal.md) . Zachytávání paketů může být uloženo v objektu BLOB úložiště, aby k němu měl získat pøístup CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Nahrání zachytávání paketů do CapAnalysis
Pomocí karty importovat z adresy URL můžete přímo nahrát zachytávání paketů pořízené sledovacím procesem sítě a poskytnout odkaz na objekt BLOB úložiště, ve kterém je zachytávání paketů uložené.

Při zadání odkazu na CapAnalysis nezapomeňte připojit token SAS k adrese URL objektu BLOB úložiště.  Provedete to tak, že přejdete na podpis sdíleného přístupu z účtu úložiště, určíte povolená oprávnění a vytvoříte token stisknutím tlačítka generovat SAS. Pak můžete připojit token SAS k adrese URL objektu BLOB úložiště pro zachytávání paketů.

Výsledná adresa URL bude vypadat přibližně podobně jako následující adresa URL: `http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere`


### <a name="analyzing-packet-captures"></a>Analýza zachycení paketů

CapAnalysis nabízí různé možnosti pro vizualizaci zachytávání paketů, z nichž každá zajišťuje analýzu z jiné perspektivy. Pomocí těchto vizuálních souhrnů můžete pochopit trendy síťových přenosů a rychle odhalit jakoukoli neobvyklou aktivitu. V následujícím seznamu jsou uvedeny některé z těchto funkcí:

1. Tabulky toků

    Tato tabulka obsahuje seznam toků v datech paketů, časové razítko přidružené ke tokům a různé protokoly, které jsou přidružené k tomuto toku, a také zdrojovou a cílovou IP adresu.

    ![Stránka capanalysis Flow][5]

1. Přehled protokolu

    V tomto podokně můžete rychle zobrazit distribuci síťového provozu přes různé protokoly a geografické oblasti.

    ![Přehled protokolu capanalysis][6]

1. Statistika

    V tomto podokně můžete zobrazit statistiku síťového provozu – bajty odeslané a přijímané ze zdrojových a cílových IP adres, toky pro každou zdrojovou a cílovou IP adresu, protokol, který se používá pro různé toky, a dobu trvání toků.

    ![statistiky capanalysis][7]

1. Geografickou mapu

    V tomto podokně se zobrazuje mapa síťových přenosů s barevným škálováním na objem přenosů z každé země nebo oblasti. Můžete vybrat zvýrazněné země nebo oblasti a zobrazit tak další statistiku toků, například poměr dat odesílaných a přijatých z IP adres v dané zemi nebo oblasti.

    ![geografickou mapu][8]

1. Filtry

    CapAnalysis poskytuje sadu filtrů pro rychlou analýzu konkrétních paketů. Můžete například zvolit filtrování dat podle protokolu, aby bylo možné získat konkrétní přehledy o této podmnožině provozu.

    ![filtry][11]

    [https://www.capanalysis.net/ca/#about](https://www.capanalysis.net/ca/#about)Další informace o funkcích všech CapAnalysis najdete na webu.

## <a name="conclusion"></a>Závěr

Funkce zachytávání paketů Network Watcher umožňuje zachytit data potřebná k provádění síťových forenzní a lépe porozumět síťovému provozu. V tomto scénáři jsme ukázali, jak je možné zachytávání paketů z Network Watcher snadno integrovat s open source nástroji pro vizualizaci. Díky použití open source nástrojů, jako je CapAnalysis, k vizualizaci zachycení paketů, můžete provádět hloubkovou kontrolu paketů a rychle identifikovat trendy v rámci síťového provozu.

## <a name="next-steps"></a>Další kroky

Další informace o protokolech toku NSG najdete v [protokolech toků NSG](network-watcher-nsg-flow-logging-overview.md) .

Naučte se vizualizovat protokoly toku NSG pomocí Power BI tím, že navštívíte [vizualizace NSG toků protokolů s Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
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
