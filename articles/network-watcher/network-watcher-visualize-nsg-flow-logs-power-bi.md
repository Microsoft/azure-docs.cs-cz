---
title: Skupiny zabezpečení sítě Azure visualizing protokolů toku s využitím Power BI | Dokumentace Microsoftu
description: Tato stránka popisuje, jak vizualizace protokolů toku NSG s Power BI.
services: network-watcher
documentationcenter: na
author: mattreatMSFT
manager: vitinnan
editor: ''
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: mareat
ms.openlocfilehash: 8f5bb54e12348fd915b2c4413bbacdc083a2a879
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2018
ms.locfileid: "42058692"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Skupina zabezpečení sítě visualizing protokolů toku s využitím Power BI

Protokoly toku skupin zabezpečení sítě umožňují zobrazit informace o příchozí a odchozí přenosy na skupiny zabezpečení sítě. Tok tyto protokoly zobrazení odchozí a příchozí toků na základě pravidel za NIC toku se vztahuje na, 5 řazené kolekce členů informace o toku (zdrojová a cílová IP, zdrojový/cílový Port, protokol), a pokud provoz byl povolen nebo odepřen.

Může být obtížné získat přehled o datech protokolování tok tak, že ručně soubor protokolu. V tomto článku zajišťuje řešení k vizualizaci protokolů toku s vaší aktuální a další informace o provozu v síti.

## <a name="scenario"></a>Scénář

V následujícím scénáři jsme Power BI desktopu připojit k účtu úložiště, který jsme nakonfigurovali jako jímka pro naše data NSG Flow protokolování. Poté, co nám připojit se k naší účet úložiště, Power BI soubory ke stažení a analyzuje protokoly a poskytují vizuální znázornění provoz, který je protokolován podle skupin zabezpečení sítě.

Pomocí zadané v šabloně, kterou můžete prozkoumat vizuály:

* Hlavní vyjádření
* Data toku Time Series podle směru a rozhodnutí pravidel
* Toky podle adres MAC síťového rozhraní
* Toky podle NSG a pravidel
* Toky podle cílového portu

Šablony, kterou lze upravit, můžete ho upravit tak, že nová data, vizuály, přidat nebo upravit dotazy tak, aby odpovídala vašim potřebám.

## <a name="setup"></a>Nastavení

Než začnete, musíte mít síťové zabezpečení skupiny tok protokolování povoleno na jeden nebo více skupin zabezpečení sítě ve vašem účtu. Pokyny pro povolení zabezpečení sítě protokoly toků, přečtěte si následující článek: [Úvod k protokolování toků pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

Také musíte mít nainstalovaného na vašem počítači a dostatek volného místa na vašem počítači ke stažení a načíst data protokolu, který existuje ve vašem účtu úložiště klienta Power BI Desktopu.

![Diagram Visia][1]

### <a name="steps"></a>Kroky

1. Stáhnout a otevřít následující šablony Power BI v aplikaci Power BI Desktopu [protokoly toku Network Watcher PowerBI šablony](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Zadejte požadované parametry dotazu
    1. **StorageAccountName** – Určuje, že název účtu úložiště obsahující protokoly toků NSG, které chcete načíst a vizualizace.
    1. **NumberOfLogFiles** – určuje počet souborů protokolů, které chcete stáhnout a vizualizace v Power BI. Pokud je zadán 50, například 50 nejnovějších souborů protokolu. FF máme 2 skupin zabezpečení sítě povolené a nakonfigurované k odeslání protokolů toku NSG k tomuto účtu a pak můžete zobrazit posledních 25 hodin protokolů.

    ![hlavní Power BI][2]

1. Zadejte přístupový klíč účtu úložiště. Platné přístupové klíče můžete najít tak, že přejdete do účtu úložiště v Azure portal a potom zvolíte **přístupové klíče** z nabídky nastavení. Klikněte na tlačítko **připojit** pak použít změny.

    ![Přístupové klíče][3]

    ![přístupový klíč 2][4]

4.  Protokoly jsou stáhnout a analyzovat a mohou nyní využívat předem vytvořených vizuálů.

## <a name="understanding-the-visuals"></a>Principy vizuály

Součástí šablony jsou sadu vizuálů, které pomáhají smysl protokolů toku NSG. Následující obrázky znázorňují vzorek vypadá řídicí panel, když naplněný daty. Níže najdete Zkoumáme, každý vizuál podrobněji 

![Power BI][5]
 
Ukazuje visual horní vyjádření zadané IP adresy, které inicializovali většina připojení za období. Velikost pole odpovídá relativní počet připojení. 

![toptalkers][6]

Následující grafy řady času zobrazit počet toků za období. Horní grafu je segmentované podle směr toku a čím nižší je segmentované podle rozhodnutí provedli (povolit nebo zakázat). Se tímto vizuálem můžete prozkoumat trendy váš provoz v průběhu času a přímé všechny neobvyklé špičky nebo odmítnout v provozu nebo provozu segmentace.

![flowsoverperiod][7]

Následující grafy zobrazit toků na síťové rozhraní s horní segmentované podle směr toku a nižší segmentované podle rozhodnutí provedli. Tyto informace můžete získat přehled o které z vašich virtuálních počítačů předány nejvíce relativně k ostatním, a pokud se přenosy konkrétnímu virtuálnímu počítači povolený nebo zakázaný.

![flowspernic][8]

Následující kolečko prstencový graf zobrazuje přehled toky podle cílového portu. Pomocí těchto informací můžete zobrazit nejčastěji používané cílové porty používané v tomto stanoveném období.

![Prstencový][9]

Následující pruhový graf znázorňuje tok podle NSG a pravidel. Pomocí těchto informací uvidíte zodpovědná za největším provozem a rozdělení provozu skupiny Nsg na skupinu zabezpečení sítě pravidlem.

![barchart][10]
 
Následující informační grafy se zobrazují informace o skupinách Nsg v protokoly toků zachytávaných v průběhu doby a datum protokolu nejdřívější zachytit. Tyto informace vám napoví, jaké skupiny zabezpečení sítě jsou zaznamenána a rozsah toků.

![infochart1][11]

![infochart2][12]

Tato šablona obsahuje následující průřezů, aby bylo možné zobrazit pouze data, která vás nejvíce zajímají. Můžete filtrovat podle skupin prostředků, skupin zabezpečení sítě a pravidel. Můžete také filtrovat podle informace 5 řazené kolekce členů, rozhodnutí a čas, kdy protokol byl zapsán.

![průřezy][13]

## <a name="conclusion"></a>Závěr

Jsme ukázali v tomto případě, že používáte protokoly toku skupin zabezpečení sítě tak, že sledovací proces sítě a Power BI k dispozici, jsme schopní vizualizace a pochopení provoz. Power BI pomocí poskytnuté šabloně, stáhne protokoly přímo ze služby storage a zpracuje je místně. Čas potřebný k načtení šablony se liší v závislosti na počtu požadované soubory a stáhnout celková velikost souborů.

Nebojte se, že jste tuto šablonu pro vaše potřeby přizpůsobit. Existuje mnoho mnoho způsobů, že můžete používat Power BI s provádění příkazu tok protokoly skupiny zabezpečení sítě. 

## <a name="notes"></a>Poznámky

* Protokoly ve výchozím nastavení se ukládají v `https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Pokud existují další data v jiném adresáři, dotazy, které o přijetí změn a zpracovávat je nutné upravit.

* Zadaná šablona není doporučeno používat s více než 1 GB protokolů.

* Pokud máte velké množství protokoly, doporučujeme, aby měli prošetřit řešení pomocí jiného úložiště dat, jako je Data Lake nebo SQL server.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak vizualizovat vaše protokoly toků NSG se zásobníkem Elastick návštěvou [vizualizovat Azure Network Watcher NSG protokolů toku s využitím opensourcových nástrojů](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

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
