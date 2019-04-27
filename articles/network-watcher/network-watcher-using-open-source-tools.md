---
title: Vizualizace provozu sítě pomocí opensourcových nástrojů a Azure Network Watcher | Dokumentace Microsoftu
description: Tato stránka popisuje, jak pomocí služby Network Watcher zachytávání paketů Capanalysis vizualizace provozu do a z vašich virtuálních počítačů.
services: network-watcher
documentationcenter: na
author: KumudD
manager: twooley
editor: ''
ms.assetid: 936d881b-49f9-4798-8e45-d7185ec9fe89
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: kumud
ms.openlocfilehash: 7c239bbf577645ddb8ab12c525d1b3a8832421df
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60680485"
---
# <a name="visualize-network-traffic-patterns-to-and-from-your-vms-using-open-source-tools"></a>Vizualizace provozu sítě do a z virtuálních počítačů pomocí opensourcových nástrojů

Zachytávání paketů obsahovat data sítě, která umožňují sítě forenzní analýzy a hloubkové kontroly paketů. Existuje mnoho otevře source nástroje, které lze použít k analýze zachytávání paketů a získejte přehled o vaší síti. Jeden takový nástroj je CapAnalysis, nástroj pro vizualizaci zachycení paketů open source. Vizualizace dat zachytávání paketů se výhodný způsob, jak rychle vyvoďte z nich na vzory a anomálie v rámci vaší sítě. Vizualizace také poskytují způsob sdílení těchto přehledů snadno využitelným způsobem.

Azure Network Watcher dává možnost k zaznamenání dat tím, že můžete provádět zachytávání paketů ve vaší síti. Tento článek poskytuje procházení pomocí toho, jak vizualizovat a zkoumat velké paketů zachycuje pomocí CapAnalysis pomocí služby Network Watcher.

## <a name="scenario"></a>Scénář

Budete mít jednoduchou webovou aplikaci nasadit na virtuální počítač v Azure použít open source nástroje pro vizualizaci jeho síťový provoz rychle identifikovat vzory v toku a je to možné anomálie. Pomocí služby Network Watcher můžete získat zachytávání paketů síťového prostředí a uložit je přímo na váš účet úložiště. CapAnalysis můžete ingestovat zachytávání paketů přímo z úložiště objektů blob a jeho obsah vizualizace.

![scénář][1]

## <a name="steps"></a>Kroky

### <a name="install-capanalysis"></a>Nainstalujte CapAnalysis

K instalaci CapAnalysis na virtuálním počítači, můžete odkazovat na oficiální pokyny https://www.capanalysis.net/ca/how-to-install-capanalysis.
V pořadí CapAnalysis vzdálený přístup, budete muset otevřít port 9877 na vašem virtuálním počítači tak, že přidáte nové pravidlo příchozího zabezpečení. Další informace o vytváření pravidel ve skupinách zabezpečení sítě, najdete v tématu [vytvoření pravidel v existující skupině NSG](../virtual-network/manage-network-security-group.md#create-a-security-rule). Po úspěšném přidání pravidlo byste měli mít přístup k CapAnalysis z `http://<PublicIP>:9877`

### <a name="use-azure-network-watcher-to-start-a-packet-capture-session"></a>Azure Network Watcher můžete využít ke spuštění relace zachytávání paketů

Network Watcher umožňuje při zachytávání paketů sledujte provoz do a z virtuálního počítače. Můžete použít pokyny v [zachycuje spravovat paketů pomocí služby Network Watcher](network-watcher-packet-capture-manage-portal.md) pro spuštění relace zachytávání paketů. Zachytávání paketů mohou být uloženy v objektu blob úložiště, ke kterým přistupují CapAnalysis.

### <a name="upload-a-packet-capture-to-capanalysis"></a>Nahrajte do CapAnalysis zachytávání paketů
Můžete přímo nahrát zachytávání paketů přijatých sledovací proces sítě na kartě "Import z adresy URL" a poskytnutí odkazu na objekt blob úložiště zachytávání paketů se mají ukládat.

Při zadávání odkazu na CapAnalysis, ujistěte se, že připojte SAS token k adrese URL objektu blob úložiště.  Chcete-li to provést, přejděte do sdíleného přístupového podpisu z účtu úložiště, určení povolených oprávnění a stiskněte tlačítko Generovat SAS k vytvoření tokenu. Můžete pak připojte SAS token k adrese URL paketů zachycení úložiště objektů blob.

Výsledná adresa URL bude vypadat podobně jako následující adresa URL: http://storageaccount.blob.core.windows.net/container/location?addSASkeyhere


### <a name="analyzing-packet-captures"></a>Zaznamená analýza paketů

CapAnalysis nabízí různé možnosti, jak vizualizovat zachytávání paketů, každý poskytuje analýzy z hlediska různých. Pomocí těchto vizuální souhrny můžete pochopit trendy provozu vaší sítě a rychle odhalovat případné problémy žádnou neobvyklou aktivitu. V následujícím seznamu jsou uvedeny některé z těchto funkcí:

1. Tok tabulky

    Tato tabulka obsahuje seznam toků v datový paket, časové razítko přidružené k toků a různé protokoly, které jsou přidružené k toku, a jako zdrojových a cílových IP.

    ![stránka flow capanalysis][5]

1. Přehled protokolu

    V tomto podokně můžete rychle zobrazit distribuci síťového provozu přes různé protokoly a geografickými oblastmi.

    ![Přehled protokolu capanalysis][6]

1. Statistika

    V tomto podokně umožňuje můžete zobrazit statistiku provozu sítě – počet bajtů odeslaných a přijatých ze zdrojových a cílových IP adres, pracovních postupů pro jednotlivé zdrojové a cílové IP adresy, používá protokol pro různé toky a doba trvání toků.

    ![capanalysis statistiky][7]

1. Geografickou mapu.

    V tomto podokně poskytuje zobrazení mapy síťového provozu s barvami škálovaný objem přenosů z každé země. Můžete vybrat zvýrazněné zemí, chcete-li zobrazit další tok statistiky, jako je objem dat odeslaných a přijatých z IP adres v dané zemi.

    ![geografickou mapu.][8]

1. Filtry

    CapAnalysis poskytuje sadu filtrů pro rychlou analýzu konkrétní paketů. Můžete například filtrovat data podle protokolu získáte konkrétní přehledy tuto dílčí provozu.

    ![Filtry][11]

    Navštivte [ https://www.capanalysis.net/ca/#about ](https://www.capanalysis.net/ca/#about) získat další informace o všech CapAnalysis možnosti.

## <a name="conclusion"></a>Závěr

Funkce zachytávání paketů ve službě Network Watcher umožňuje zaznamenat data potřebná k provedení forenzní sítě a až lépe porozumíte provozu vaší sítě. V tomto scénáři jsme ukázali, jak zachytávání paketů z Network Watcher lze snadno integrovat s open source vizualizačních nástrojů. S využitím opensourcových nástrojů, jako je například CapAnalysis k vizualizaci zachycení paketů, můžete provádějí hloubkové kontroly paketů a rychle odhalovat trendy v rámci provozu vaší sítě.

## <a name="next-steps"></a>Další postup

Další informace o protokoly toků NSG najdete v tématu [protokolů toku NSG](network-watcher-nsg-flow-logging-overview.md)

Zjistěte, jak vizualizovat vaše protokoly toků NSG s Power BI návštěvou [toků NSG vizualizaci protokolů pomocí Power BI](network-watcher-visualize-nsg-flow-logs-power-bi.md)
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
