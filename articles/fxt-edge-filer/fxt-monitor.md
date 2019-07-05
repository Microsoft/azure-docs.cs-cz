---
title: Monitorování Filer Azure FXT hrany
description: Jak monitorovat stav hardwaru pro mezipaměť Azure FXT hrany Filer hybridní úložiště
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: v-erkell
ms.openlocfilehash: e7395c69d99884a5c662e545a69778ed195aec55
ms.sourcegitcommit: 5bdd50e769a4d50ccb89e135cfd38b788ade594d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2019
ms.locfileid: "67543136"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitorování Azure FXT hrany Filer stavu hardwaru

Systém Azure FXT hrany Filer hybridní úložiště mezipaměti obsahuje více indikátory stavu integrované do skříně správcům porozumět fungování hardwaru.

## <a name="system-health-status"></a>Stav systému

K monitorování operace s mezipamětí na vyšší úrovni, použijte software ovládací panely **řídicí panel** stránce, jak je popsáno v [Průvodce řídicí panel ovládacího prvku Panel](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>Stav hardwarových indikátorů LED

Tato část vysvětluje různé indikátory stavu součástí Azure FXT hrany Filer hardwaru.

### <a name="hard-drive-status-leds"></a>Stav disku indikátorů LED

![Obrázek popředí pevného disku, horizontální, bublinové popisky popisky 2 (levého horního rohu), 1 (levého dolního rohu) a 3 (pravá strana)](media/fxt-monitor/fxt-drive-callouts.png)

Má dva LED stav každého disku dopravce: indikátorem aktivity (1) a indikátor stavu (2). 

* Indikátory aktivity LED (1), pokud na jednotce se používá.  
* Indikátor LED (2) označuje podmínku, která jednotky pomocí kódů v následující tabulce.

| Jednotka stav Indikátor stavu              | Význam  |
|-------------------------------------|----------------------------------------------------------|
| Bliká zelené dvakrát za sekundu      | Identifikace disku *nebo* <br> Příprava jednotky pro odebrání  |
| Vypnuto (nesvítí)                         | Systém se nedokončila spuštění *nebo* <br>Verze je připravená k odebrání |
| Bliká zelené, žluté a vypnout       | Očekává se selhání jednotky   |
| Bliká žlutou čtyřikrát za sekundu | Jednotce se nezdařil   |
| Plné zelená                         | Jednotka není online |

Pravé straně jednotky (3) je označena s na jednotku kapacity a další informace.

Jednotka čísla se vytisknou na mezeru mezi jednotky. V Azure FXT hrany vyfiltrovat disk 0 je jednotka vlevo nahoře a jednotka 1 je přímo pod ním. Číslování dál v tomto vzoru. 

![fotografie jeden pevný disk ve skříni FXT zobrazující čísla a kapacitu popisek jednotky](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Levého ovládacího prvku panel

Levé přední ovládacího panelu má různé indikátory stavu LED (1) a indikátor stavu velké podsvícené systému (2). 

![levé stavový panel s 1 označování indikátory stavu na levé straně a označování indikátor stavu rozsáhlém systému světla na pravé straně 2](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Indikátory stavu ovládacího prvku panel 

Indikátory stavu vlevo zobrazit solid oranžové světlo, pokud dojde k chybě v daném systému. Následující tabulka popisuje možné příčiny a řešení pro chyby. 

Pokud chyba stále ještě máte po vyzkoušení těchto řešení, obraťte se na podporu o pomoc. 

| Ikona | Popis | Chybový stav | Možná řešení |
|----------------|---------------|--------------------|----------------------|
| ![ikona jednotky](media/fxt-monitor/fxt-hd-icon.jpg) | Stav disku | Chyba jednotky | Zkontrolujte protokol událostí systému k určení, pokud na jednotce došlo k chybě, nebo <br>Spustit test odpovídající online diagnostiky; restartování systému a Diagnostika embedded (ePSA), nebo <br>Pokud jednotky jsou nakonfigurované v pole RAID, restartování systému a zadejte program pro nástroj Konfigurace hostitele adaptéru |
|![Ikona teploty](media/fxt-monitor/fxt-temp-icon.jpg) | Stav teploty | Teplotní chyba – například ventilátoru selhal nebo okolní teploty je mimo rozsah. | Zkontrolujte následující adresovatelný podmínky: <br>Chladicí ventilátory chybí nebo se nezdařilo <br>V systému titulní air shroud paměti modulu prázdné nebo zpět Odebereme přednastavené závorky <br>Teplota je příliš vysoká. <br>Externí vzduchu je nelze blokovat. |
|![Ikona k elektrické energie](media/fxt-monitor/fxt-electric-icon.jpg) | Elektrické stav | Chyba elektrické – například napětí mimo rozsah, nepovedlo PSU nebo regulační neúspěšné napětí |  Zkontrolujte protokol událostí systému nebo systémové zprávy pro konkrétní problém. Pokud dojde k problému PSU, zkontrolujte stav PSU LED a v případě potřeby upevnění PSU. | 
|![Ikona paměti](media/fxt-monitor/fxt-memory-icon.jpg) | Stav paměti | Chyba paměti | Zkontrolujte protokol událostí systému nebo systémové zprávy pro umístění paměti se nezdařilo. upevnění paměti modulu. |
|![Ikona PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | Stav PCIe | Chyba PCIe karty | Restartování systému; aktualizace ovladačů karty PCIe; Přeinstalujte karty |


### <a name="system-health-status-indicator"></a>Indikátor stavu stavu systému

Po velké tlačítko v pravém rohu levého ovládacího panelu indikuje celkový stav systému a také slouží jako jednotka Lokátor světla v režimu ID systému.

Stiskněte tlačítko ID přepínat mezi režimem ID systému a stav systému a stav systému.

|Stav systému stavu | Podmínka |
|-------------------------------------------|-----------------------------------------------|
| Plné modrá | Normálního provozu: systém zapnutý, normálně, a režim ID systému není aktivní. <br/>Stisknutím klávesy stavu systému a ID tlačítko, pokud chcete přepnout do režimu ID systému. |
| Bliká vám kontrolka modrá | Režim ID systému je aktivní. Stisknutím klávesy stavu systému a systém ID tlačítko, pokud chcete přepnout na režim stavu systému. |
| Plné oranžová | Systém je v pohotovostním režimu. Pokud se problém nevyřeší, obraťte se na Microsoft zákaznický servis a podporu. |
| Bliká vám kontrolka oranžová | Selhání systému. Zkontrolujte protokol událostí systému pro specifické chybové zprávy. Informace o události a chybové zprávy generované firmware systému a agenty, které monitorují součásti systému naleznete na stránce vyhledávání kódu chyby na qrl.dell.com. |


