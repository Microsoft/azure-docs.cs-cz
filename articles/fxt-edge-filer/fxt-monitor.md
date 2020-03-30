---
title: Monitorování Azure FXT Edge Fileru
description: Jak sledovat stav hardwaru pro mezipaměť hybridního úložiště Azure FXT Edge Filer
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: conceptual
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 3f422339af2040ad81c585c0e193e6cb3667b135
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72254883"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Sledování stavu hardwaru Azure FXT Edge Filer

Systém hybridní mezipaměti Azure FXT Edge Filer má několik stavových světel integrovaných do šasi, které správcům pomáhají pochopit, jak hardware funguje.

## <a name="system-health-status"></a>Stav systému

Chcete-li sledovat operace mezipaměti na vyšší úrovni, použijte stránku **řídicího panelu** ovládacího panelu softwaru, jak je popsáno v [Průvodci řídicím panelem ovládacího panelu.](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html)

## <a name="hardware-status-leds"></a>Hardwarové stavové LED diody

Tato část vysvětluje různé stavové kontrolky integrované do hardwaru Azure FXT Edge Filer.

### <a name="hard-drive-status-leds"></a>Led diody stavu pevného disku

![obrázek předního, vodorovného pevného disku s popisky popisků 2 (levý horní roh), 1 (levý dolní roh) a 3 (pravá strana)](media/fxt-monitor/fxt-drive-callouts.png)

Každý nosič pohonu má dvě stavové LED diody: indikátor aktivity (1) a indikátor stavu (2). 

* Led dioda aktivity (1) se rozsvítí, když je jednotka používána.  
* Stavová LED dioda (2) indikuje stav jednotky pomocí kódů v následující tabulce.

| Stav jednotky STAV LED stavu              | Význam  |
|-------------------------------------|----------------------------------------------------------|
| Bliká zeleně dvakrát za sekundu      | Identifikace jednotky *nebo* <br> Příprava jednotky k odebrání  |
| Vypnuto (neosvětlené)                         | Systém nebyl dokončen při spuštění *nebo* <br>Disk je připraven k odebrání |
| Bliká zeleně, oranžově a vypnutě       | Předpokládá se selhání jednotky.   |
| Bliká žlutě čtyřikrát za sekundu | Jednotka se nezdařila.   |
| Plná zelená                         | Disk je online |

Pravá strana pohonu (3) je označena kapacitou pohonu a dalšími informacemi.

Čísla jednotek jsou vytištěna na prostoru mezi jednotkami. V Azure FXT Edge Filer je jednotka 0 levá horní jednotka a jednotka 1 je přímo pod ní. Číslování pokračuje v tomto vzoru. 

![fotografie jedné pozice na pevném disku v šasi FXT, zobrazující čísla disků a popisky kapacity](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Levý ovládací panel

Levý přední ovládací panel má různé indikátory STAVOVÉ LED diody (1) a velký osvětlený indikátor stavu systému (2). 

![levý stavový panel s 1 indikátory stavu označení vlevo a 2 označením velkého indikátoru stavu systému na pravé straně](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Indikátory stavu ovládacího panelu 

Indikátory stavu vlevo zobrazují plné oranžové světlo, pokud je v tomto systému chyba. Níže uvedená tabulka popisuje možné příčiny a řešení chyb. 

Pokud se chyba stále zobrazuje i po vyzkoušení těchto řešení, [požádejte o](fxt-support-ticket.md) pomoc podporu. 

| Ikona | Popis | Chybový stav | Možná řešení |
|----------------|---------------|--------------------|----------------------|
| ![ikona jednotky](media/fxt-monitor/fxt-hd-icon.jpg) | Stav jednotky | Chyba jednotky | Zkontrolujte protokol systémových událostí a zjistěte, zda jednotka nezaznamenala chybu nebo <br>Spusťte příslušný test online diagnostiky. restartovat systém a spustit integrovanou diagnostiku (ePSA) nebo <br>Pokud jsou jednotky konfigurovány v poli RAID, restartujte systém a zadejte nástroj nástroje konfigurace hostitelského adaptéru |
|![ikona teploty](media/fxt-monitor/fxt-temp-icon.jpg) | Teplotní stav | Tepelná chyba - například selhal ventilátor nebo okolní teplota je mimo rozsah | Zkontrolujte následující adresovatelné podmínky: <br>Chladicí ventilátor chybí nebo selhal <br>Kryt systému, vzduchový kryt, prázdný paměťový modul nebo zadní plnicí držák je odstraněn <br>Okolní teplota je příliš vysoká <br>Vnější proudění vzduchu je ucpané |
|![ikona elektřiny](media/fxt-monitor/fxt-electric-icon.jpg) | Elektrický stav | Elektrická chyba - například napětí mimo rozsah, selhání napájecího zem nebo selhání regulátoru napětí |  Konkrétní problém naleznete v protokolu systémových událostí nebo systémových zprávách. Pokud se jedná o problém s napájecím unám, zkontrolujte indikátor STAVU napájecího, napájecího, v případě potřeby znovu usazen napájecí hod. | 
|![ikona paměti](media/fxt-monitor/fxt-memory-icon.jpg) | Stav paměti | Chyba paměti | Zkontrolujte, zda se v protokolu systémových událostí nebo systémových zprávách nepodařilo umístění neúspěšné paměti. paměťový modul znovu usadit. |
|![Ikona aplikace PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | Stav PCIe | Chyba karty PCIe | Restartujte systém. aktualizace ovladačů karet PCIe; Přeinstalujte kartu |


### <a name="system-health-status-indicator"></a>Ukazatel stavu systému

Velké rozsvícené tlačítko vpravo od levého ovládacího panelu indikuje celkový stav systému a také se používá jako světlo lokátoru jednotky v režimu ID systému.

Stisknutím tlačítka Stav systému a ID přepnete mezi režimem ID systému a režimem stavu systému.

|Stav systému | Podmínka |
|-------------------------------------------|-----------------------------------------------|
| Plná modrá | Normální provoz: systém je zapnutý, pracuje normálně a režim ID systému není aktivní. <br/>Chcete-li přepnout do režimu ID systému, stiskněte tlačítko Stav systému a ID. |
| Bliká modře | Režim ID systému je aktivní. Chcete-li přepnout do režimu stavu systému, stiskněte tlačítko Stav systému a ID systému. |
| Plná oranžová | Systém je v nouzovém režimu. Pokud problém přetrvává, [obraťte se na oddělení služeb zákazníkům a podpory společnosti Microsoft](fxt-support-ticket.md). |
| Bliká oranžově | Porucha systému. Zkontrolujte, zda protokol systémových událostí nezobrazuje konkrétní chybové zprávy. Informace o událostech a chybových zprávách generovaných firmwaru systému a agenty, kteří monitorují systémové součásti, naleznete na stránce Vyhledávání kódu chyby na qrl.dell.com. |


