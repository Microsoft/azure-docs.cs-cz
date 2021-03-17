---
title: Monitorování Azure FXT Edge Fileru
description: Jak monitorovat stav hardwaru pro mezipaměť hybridního úložiště Azure FXT Edge souborového
author: ekpgh
ms.service: fxt-edge-filer
ms.topic: how-to
ms.date: 06/20/2019
ms.author: rohogue
ms.openlocfilehash: 86e1d74d5e4ab9f6e799c73bcf0d807d0d874f21
ms.sourcegitcommit: 8d8deb9a406165de5050522681b782fb2917762d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/20/2020
ms.locfileid: "92219713"
---
# <a name="monitor-azure-fxt-edge-filer-hardware-status"></a>Monitorovat stav hardwaru Azure FXT Edge souborového

Systém Azure FXT Edge pro hybridní úložiště souborového má na skříni více stavových světel, které správcům pomůžou pochopit, jak hardware funguje.

## <a name="system-health-status"></a>Stav systému

Chcete-li monitorovat operace mezipaměti na vyšší úrovni, použijte stránku **řídicí** panel softwarového ovládacího panelu, jak je popsáno v [Průvodci řídicí panel v Ovládacích panelech](https://azure.github.io/Avere/legacy/dashboard/4_7/html/ops_dashboard_index.html) .

## <a name="hardware-status-leds"></a>Stav hardwaru – diody

Tato část popisuje různé stavové indikátory, které jsou součástí hardwaru Azure FXT Edge souborového.

### <a name="hard-drive-status-leds"></a>Indikátory stavu pevného disku

![Obrázek horního, vodorovného a popisku popisku 2 (levý horní roh), 1 (dolní roh) a 3 (pravá strana)](media/fxt-monitor/fxt-drive-callouts.png)

Každý operátor jednotky má dvě stavy LED: Indikátor aktivity (1) a indikátor stavu (2).

* Indikátory LED aktivity (1), pokud je jednotka používána.
* Indikátor LED stavu (2) indikuje stav jednotky pomocí kódů v následující tabulce.

| Stav LED jednotky stavu              | Význam  |
|-------------------------------------|----------------------------------------------------------|
| Blikají zeleně dvakrát za sekundu      | Identifikace jednotky *nebo* <br> Příprava jednotky k odebrání  |
| Vypnuto (unlit)                         | Systém nedokončil spuštění *nebo* <br>Jednotka je připravena k odebrání. |
| Blikají zeleně, žlutě a vypnuto       | Předpokládá se selhání jednotky.   |
| Blikají žlutě čtyřikrát za sekundu | Selhání jednotky   |
| Plná zelená                         | Jednotka je online. |

Na pravé straně jednotky (3) je označena kapacita jednotky a další informace.

Čísla jednotek se tisknou na místo mezi jednotkami. Ve službě Azure FXT Edge souborového je jednotka 0 levou horní jednotkou a jednotka 1 je přímo pod ní. Číslování pokračuje v tomto vzoru.

![Fotografie jedné pozice pevného disku na skříni FXT, která zobrazuje čísla jednotek a popisky kapacity](media/fxt-drives-photo.png)

## <a name="left-control-panel"></a>Levý ovládací panel

Levý ovládací panel má různé indikátory LED stavu (1) a velký indikátor stavu systému (2).

![levý panel stavu s 1 indikátory stavu označení na levé straně a 2 označení velkého světla indikátoru stavu systému na pravé straně](media/fxt-monitor/fxt-control-panel-left.jpg)

### <a name="control-panel-status-indicators"></a>Indikátory stavu ovládacích panelů

Indikátory stavu na levé straně zobrazují plnou žlutou žlutou světlo, pokud dojde k chybě v tomto systému. Následující tabulka popisuje možné příčiny a řešení chyb.

Pokud k této chybě došlo i po vyzkoušení těchto řešení, [požádejte](fxt-support-ticket.md) o pomoc podporu.

| Ikona | Popis | Chybový stav | Možná řešení |
|----------------|---------------|--------------------|----------------------|
| ![ikona jednotky](media/fxt-monitor/fxt-hd-icon.jpg) | Stav jednotky | Chyba jednotky | Zkontrolujte protokol událostí systému, abyste zjistili, jestli má jednotka chybu, nebo <br>Spusťte příslušný test online diagnostiky. Restartujte systém a spusťte Embedded Diagnostics (ePSA), nebo <br>Pokud jsou jednotky nakonfigurované v poli RAID, restartujte systém a zadejte program pro konfiguraci hostitelského adaptéru. |
|![ikona teploty](media/fxt-monitor/fxt-temp-icon.jpg) | Stav teploty | Tepelná chyba – například ventilátor se nezdařil nebo je okolní teplota mimo rozsah | Podívejte se na následující adresovatelné podmínky: <br>Chladicí ventilátor chybí nebo se nezdařil. <br>V systému se odeberou titulní stranu systému, modul Air Shroud, modul pro vyplňování paměti nebo zálomená závorka. <br>Okolní teplota je příliš vysoká. <br>Externí tok v toku je zablokován. |
|![ikona elektřiny](media/fxt-monitor/fxt-electric-icon.jpg) | Stav elektroinstalace | Chyba elektrického napájení – například napětí mimo rozsah, selhání PSU nebo regulátor napětí při selhání |  Ověřte protokol událostí systému nebo systémové zprávy pro konkrétní problém. Pokud dojde k potížím s PSU, podívejte se na indikátor stavu PSU a v případě potřeby znovu zasedací PSU. |
|![ikona paměti](media/fxt-monitor/fxt-memory-icon.jpg) | Stav paměti | Chyba paměti | Ověřte protokol událostí systému nebo systémové zprávy pro umístění vadné paměti. Přemístěte modul paměti. |
|![Ikona PCIe](media/fxt-monitor/fxt-pcie-icon.jpg) | Stav PCIe | Chyba karty PCIe | Restartujte systém. aktualizovat ovladače karty PCIe; Přeinstalujte kartu. |

### <a name="system-health-status-indicator"></a>Indikátor stavu systému

Velké tlačítko rozsvícené na pravé straně levého ovládacího panelu indikuje celkový stav systému a používá se také jako indikátor lokátoru jednotky v režimu ID systému.

Stisknutím tlačítka stav systému a ID přepnete mezi režimem ID systému a režimem stavu systému.

|Stav systému stav | Stav |
|-------------------------------------------|-----------------------------------------------|
| Plná modrá | Normální operace: systém je zapnutý, pracuje normálně a režim ID systému není aktivní. <br/>Pokud chcete přepnout do režimu ID systému, stiskněte tlačítko Stav systému a ID. |
| Modré blikání | Režim ID systému je aktivní. Pokud chcete přepnout do režimu stavu systému, stiskněte tlačítko Stav systému a systémové ID. |
| Plná oranžová | Systém je v režimu bezpečného selhání. Pokud potíže potrvají, [obraťte se na zákaznickou službu a podporu Microsoftu](fxt-support-ticket.md). |
| Blikající oranžová | Systémová chyba. V protokolu událostí systému vyhledejte konkrétní chybové zprávy. Informace o událostech a chybových zprávách generovaných systémovým firmwarem a agenty, které monitorují systémové součásti, najdete na stránce pro vyhledávání kódů chyb na adrese qrl.dell.com. |
