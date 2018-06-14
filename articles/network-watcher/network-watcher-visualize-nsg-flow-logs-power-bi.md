---
title: Skupina zabezpečení sítě Azure visualizing toku protokoly s Power BI | Microsoft Docs
description: Tato stránka popisuje, jak k vizualizaci protokolů NSG toku s Power BI.
services: network-watcher
documentationcenter: na
author: jimdial
manager: timlt
editor: ''
ms.assetid: 1e4f95fa-f5f0-4e03-bc25-008fbfc4934c
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: jdial
ms.openlocfilehash: 19bd7ed4bab915d7918a192a046653666cfaa498
ms.sourcegitcommit: c7215d71e1cdeab731dd923a9b6b6643cee6eb04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2017
ms.locfileid: "24878577"
---
# <a name="visualizing-network-security-group-flow-logs-with-power-bi"></a>Skupina zabezpečení sítě visualizing toku protokoly s Power BI

Skupina zabezpečení sítě toku protokolů umožňují zobrazit informace o příchozí a odchozí provoz IP na skupiny zabezpečení sítě. Tyto toku zobrazit protokoly odchozí a příchozí tok na základě pravidla na síťový adaptér tok se vztahuje na, 5-n-tice informace o toku (zdrojové nebo cílové IP adresy, zdrojový nebo cílový Port, protokol), a pokud byl provoz povolené nebo zakázané.

Může být složité a získáte přehled o data protokolování toku tak, že ručně soubory protokolu. V tomto článku jsme poskytují řešení vizualizovat nejnovější toku protokolů a další informace o přenosu v síti.

## <a name="scenario"></a>Scénář

V následujícím scénáři jsme Power BI desktop připojit k účtu úložiště, které jsme nakonfigurovali jako jímky pro naše data NSG toku protokolování. Když jsme se připojit k naší účtu úložiště, Power BI soubory ke stažení a analyzuje protokoly zajistit vizuální reprezentace přenos, který je přihlášen pomocí skupin zabezpečení sítě.

Pomocí vizuály zadané v šabloně, které můžete zkontrolovat:

* Horní Talkers
* Časových řad toku dat rozhodnutím směr a pravidla
* Toky podle adresy MAC rozhraní sítě
* Toky NSG a pravidla
* Toky podle cílový Port

Šablona zadaná je upravitelné, takže můžete upravit tak, aby nová data, vizuály, přidat nebo upravit dotazy, aby vyhovovaly potřebám vaší.

## <a name="setup"></a>Nastavení

Než začnete, musíte mít síťové zabezpečení skupiny toku povoleným protokolováním na jeden nebo více skupin zabezpečení sítě ve vašem účtu. Pokyny k povolení zabezpečení sítě toku protokolů, najdete v následujícím článku: [Úvod do toku protokolování pro skupiny zabezpečení sítě](network-watcher-nsg-flow-logging-overview.md).

Musí také mít Power BI Desktop klient nainstalován na váš počítač a dostatek volného místa v počítači ke stažení a načíst data protokolu, která existuje v účtu úložiště.

![Diagram aplikace Visio][1]

### <a name="steps"></a>Kroky

1. Stáhněte si a nainstalujte následující šablony Power BI v aplikaci Power BI Desktop [PowerBI sledovací proces sítě toku protokoly šablony](https://aka.ms/networkwatcherpowerbiflowlogstemplate)
1. Zadejte požadované parametry dotazu
    1. **StorageAccountName** – Určuje název účtu úložiště obsahující protokoly toku NSG, které chcete načíst a vizualizaci.
    1. **NumberOfLogFiles** – určuje počet souborů protokolu, které chcete stáhnout a vizualizovat v Power BI. Pokud je zadán 50, například 50 nejnovějších souborů protokolu. Vypnuto máme 2 skupiny Nsg povolené a nakonfigurované k odeslání protokolů toku NSG k tomuto účtu a potom je možné zobrazit posledních 25 hodin protokolů.

    ![hlavní Power BI][2]

1. Zadejte přístupový klíč účtu úložiště. Platný přístupových klíčů můžete najít tak, že přejdete na váš účet úložiště ve službě Azure portálu a vyberete **přístupové klíče** z nabídky nastavení. Klikněte na tlačítko **Connect** pak změny.

    ![Přístupové klávesy][3]

    ![přístupový klíč 2][4]

4.  Protokoly jsou stáhnout a analyzovat a mohou nyní využívat předem vytvořené vizuální prvky.

## <a name="understanding-the-visuals"></a>Principy vizuálech

Zadané v šabloně jsou sady vizuální prvky, které pomáhají smysl data protokolu toku NSG. Následující obrázky znázorňují ukázka co řídicí panel vypadá, když naplněný daty. Níže budeme každý visual podrobněji prozkoumat 

![powerbi][5]
 
Horní Talkers visual ukazuje zadané IP adresy, které spustili většina připojení za období. Velikost polí odpovídá relativní počet připojení. 

![toptalkers][6]

Následující grafů řady čas zobrazit toků za období. Horní grafu je oddělených směr toku a čím nižší je oddělených rozhodnutí provedené (povolit nebo zakázat). Tento vizuál můžete prozkoumat vaše provoz trendů v čase a sledujte jakékoli neobvyklé špičky nebo odmítnout v provozu nebo segmentace přenosů dat.

![flowsoverperiod][7]

Následující zobrazení grafů toky na síťové rozhraní s horní oddělených směr toku a dolní oddělených rozhodnutí provedeny. Pomocí těchto informací je možné získat přehledy, které virtuální počítače předány nejvíce relativně k ostatním, a pokud provoz na konkrétním virtuálním počítači se povoluje nebo odepírá.

![flowspernic][8]

Následující tabulka wheel prstenec ukazuje rozpis toky podle cílový Port. Pomocí těchto informací můžete zobrazit nejčastěji používané cílové porty použité v zadaném období.

![prstenec][9]

Následující pruhový graf zobrazuje tok NSG a pravidla. Tyto informace a uvidíte odpovědná za většina provozu a rozpis provozu skupiny Nsg na skupinu NSG pravidlem.

![barchart][10]
 
Následující informační grafy zobrazují informace o přítomen v protokolech, toků zachytit přes období a datum nejdřívější protokolu zachytit skupiny Nsg. Tyto informace získáte představu o jaké skupiny Nsg se protokolována a rozsah kalendářních dat toky.

![infochart1][11]

![infochart2][12]

Tato šablona zahrnuje následující průřezy, aby bylo možné zobrazit pouze data, která vás zajímají nejvíc. Můžete filtrovat podle skupin prostředků, skupiny Nsg a pravidla. Můžete také filtrovat podle informace 5 řazené kolekce členů, rozhodnutí a době, kdy byla zapsána do protokolu.

![průřezy][13]

## <a name="conclusion"></a>Závěr

Můžeme vám ukázal, v tomto scénáři, pomocí protokolů toku skupiny zabezpečení sítě poskytuje sledovací proces sítě a Power BI vám budeme schopni vizualizaci a porozumění provoz. Pomocí zadané šablony Power BI stáhne protokoly přímo ze služby storage a zpracovává je místně. Čas potřebný k načtení šablony se liší v závislosti na počtu požadované soubory a stahovat celková velikost souborů.

Nebojte se, že tato šablona pro vaše potřeby přizpůsobit. Existuje mnoho mnoho způsobů, můžete Power BI s sítě toku protokolů skupiny zabezpečení. 

## <a name="notes"></a>Poznámky

* Protokoly ve výchozím nastavení se ukládají do`https://{storageAccountName}.blob.core.windows.net/insights-logs-networksecuritygroupflowevent/`

    * Pokud existuje další data z jiného adresáře je třeba upravit jejich dotazy pro vyžádání obsahu a proces data.

* Zadaná šablona se nedoporučuje pro použití s více než 1 GB protokolů.

* Pokud máte velké množství protokoly, doporučujeme vám, že byste prozkoumat řešení pomocí jiného úložiště dat jako Data Lake nebo SQL server.

## <a name="next-steps"></a>Další kroky

Zjistěte, jak k vizualizaci toku protokolů NSG se zásobníkem Elastick navštivte stránky [protokolů toku vizualizovat NSG sledovací proces sítě Azure pomocí nástroje s otevřeným zdrojem](network-watcher-visualize-nsg-flow-logs-open-source-tools.md)

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
