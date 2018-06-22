---
title: Vizualizace typy provozu sítě s otevřeným zdrojem nástroje a sledovací proces sítě Azure | Microsoft Docs
description: Tato stránka popisuje, jak použít zachytáváním paketů sledovací proces sítě s Capanalysis k vizualizaci vzory přenosů dat do a z virtuálních počítačů.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 7b1e1383e8e244a7cdb30be1e08514a6a4dd7b14
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/21/2018
ms.locfileid: "36302229"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Vizualizace vzorky síťového provozu do a z virtuálních počítačů pomocí nástroje open source

Zachycení paketu obsahovat dat sítě, která vám umožní provádět forenzní sítě a hloubkové kontroly paketů. Existuje mnoho otevře source nástroje, které můžete použít k analýze paketu zachycení a získáte přehled o vaší síti. Jeden takový nástroj je CapAnalysis, nástroj pro vizualizaci zachycení na open source paketů. Vizualizace dat zachytávání paketů je cenné způsob, jak rychle odvozena Statistika na vzory a anomálie v rámci vaší sítě. Vizualizace také umožňují sdílení tyto přehledy snadné použití způsobem.

Sledovací proces sítě Azure poskytuje možnost pro zachycení dat tím, že můžete provádět zachycení paketů ve vaší síti. Tento článek poskytuje procházení prostřednictvím jak vizualizovat a získáte přehled o z paketu zaznamená CapAnalysis pomocí sledovací proces sítě.

## <a name="scenario"></a>Scénář

Můžete mít jednoduché webové aplikace nasazené na virtuálním počítači v Azure chcete použít open source nástroje, která bude vizualizovat jeho síťový provoz pro rychlou identifikaci vzorů toku a možné anomálie. S sledovací proces sítě můžete získat zachytáváním paketů prostředí sítě a uložte ho přímo na vašem účtu úložiště. CapAnalysis můžete ingestování zachytáváním paketů přímo z storage blob a vizualizovat její obsah.

![scénář][1]

## <a name="steps"></a>Kroky

### <a name="install-capanalysis"></a>Nainstalujte CapAnalysis

K instalaci CapAnalysis na virtuálním počítači, můžete odkazovat na oficiální pokynů https://www.capanalysis.net/ca/how-to-install-capanalysis.
V pořadí CapAnalysis vzdálený přístup, musíte otevřít port 9877 na vašem virtuálním počítači tak, že přidáte nové pravidlo příchozí zabezpečení. Další informace o vytváření pravidla ve skupinách zabezpečení sítě, naleznete [vytvořit pravidla v existující skupině](../virtual-network/manage-network-security-group.md#create-a-security-rule). Jakmile pravidlo byl úspěšně přidán, byste měli mít přístup k CapAnalysis z `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Sledovací proces sítě Azure použijte pro spuštění relace zachytávání paketů

Sledovací proces sítě umožňuje zaznamenat pakety sledovat provoz do/z virtuálního počítače. Můžete se podívat do postupujte podle pokynů v [zachytávali sledovací proces sítě spravovat pakety](network-watcher-packet-capture-manage-portal.md) pro spuštění relace zachytávání paketů. Zachytáváním paketů mohou být uloženy v blob storage CapAnalysis přístup.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Nahrajte do CapAnalysis zachytávání paketů
Můžete nahrát přímo zachytáváním paketů, provedenou sledovací proces sítě pomocí kartě "Import z adresy URL" a použít odkaz na objekt blob úložiště, kde je uložený zachytáváním paketů.

Pokud poskytuje odkaz na CapAnalysis, ujistěte se, že jste připojení SAS token na adresu URL úložiště objektů blob.  To uděláte, přejděte do sdíleného přístupového podpisu z účtu úložiště, určit povolené oprávnění a klikněte na tlačítko Generovat SAS vytvořit token. Pak můžete připojit SAS token na adresu URL objektu blob úložiště zachytávání paketů.

Výsledná adresa URL bude vypadat podobně jako následující adresu URL: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Analýza paketů zaznamená

CapAnalysis nabízí různé možnosti pro vizualizaci zachytáváním paketů, každý poskytnete analysis z hlediska různých. S tyto souhrny visual můžete pochopit trendy provoz vaší sítě a rychle rozpoznat všechny neobvyklé aktivity. V následujícím seznamu jsou uvedeny některé z těchto funkcí:

1. Tok tabulky

    Tato tabulka obsahuje seznam toky dat paket, časové razítko přidružené na toky a různých protokolů, které jsou přidružené k toku, jakož i zdrojové a cílové IP.

    ![stránka capanalysis toku][5]

1. Přehled protokolu

    V tomto podokně můžete rychle zobrazit distribuci síťového provozu přes různé protokoly a zeměpisných oblastí.

    ![Přehled protokolu capanalysis][6]

1. Statistika

    V tomto podokně umožňuje můžete prohlédnout statistiku provozu sítě – počet bajtů odeslaných a přijatých ze zdrojové a cílové IP adresy, toky pro jednotlivé zdrojové a cílové IP adresy, používá protokol pro různé toky a pro dobu trvání toků.

    ![capanalysis statistiky][7]

1. geomap

    V tomto podokně poskytuje zobrazení mapy síťových přenosů, pomocí barev škálování objem provozu z každé země. Můžete vybrat zvýrazněné zemích, chcete-li zobrazit další toku statistické údaje, třeba podíl data odeslané a přijaté z IP adresy v dané země.

    ![geomap][8]

1. Filtry

    CapAnalysis poskytuje sadu filtrů pro rychlou analýzu konkrétní paketů. Můžete například filtrovat data podle protokolu a získáte přehled o konkrétní na tuto podmnožinu provozu.

    ![Filtry][11]

    Navštivte [ https://www.capanalysis.net/ca/#about ](https://www.capanalysis.net/ca/#about) Další informace o všech CapAnalysis možnosti.

## <a name="conclusion"></a>Závěr

Funkce zachytávání paketů sledovací proces sítě umožňuje zaznamenat data potřebná k provedení forenzních sítě a až lépe porozumíte síťový provoz. V tomto scénáři jsme vám ukázal, jak paketu obrazovek z sledovací proces sítě lze snadno integrovat s open-source vizualizace nástroje. S využitím open source nástroje, jako je například CapAnalysis k vizualizaci pakety zachycení, můžete provádění hloubkové kontroly paketů a rychlou identifikaci trendů v rámci síťového provozu.

## <a name="next-steps"></a>Další postup

Další informace o toku protokolů NSG, navštivte [protokolů NSG toku](network-watcher-nsg-flow-logging-overview.md)

Zjistěte, jak toku protokolů NSG s Power BI vizualizovat navštivte stránky [vizualizovat NSG toků protokoly s Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
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
