---
title: Kontrolní nástroj ArrInspector
description: Uživatelská příručka nástroje ArrInspector
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: e3acfc15b0c12822e48009bef4aabadac701fb2d
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2020
ms.locfileid: "80680073"
---
# <a name="the-arrinspector-inspection-tool"></a>Kontrolní nástroj ArrInspector

ArrInspector je webový nástroj používaný ke kontrole spuštěné relace vzdáleného vykreslování Azure. Je určen pro účely ladění, ke kontrole struktury vykreslené scény, zobrazení zpráv protokolu a sledování živého výkonu na serveru.

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Připojení k inspektoru ArrInspector

Jakmile získáte název hostitele (končící `mixedreality.azure.com`) serveru ARR, připojte se pomocí [connectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector). Tato funkce `StartArrInspector.html` vytvoří na zařízení, na kterém je aplikace spuštěna. Chcete-li spustit program ArrInspector, otevřete tento soubor v prohlížeči (Edge, Firefox nebo Chrome) v počítači. Soubor je platný pouze po dobu 24 hodin.

Pokud aplikace, `ConnectToArrInspectorAsync` která volá, už běží na počítači:

* Pokud používáte integraci Unity, může se automaticky spustit za vás.
* V opačném případě najdete soubor v *uživatelských\\složkách\\\\LocalAppData\\[your_app] AC Temp*.

Pokud aplikace běží na HoloLens:

1. Přístup k HoloLens pomocí [portálu zařízení Windows](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal).
1. Přejděte do *Průzkumníka souborů system >*.
1. Přejděte do *položky User\\Folders LocalAppData\\[your_app]\\\\AC Temp*.
1. Uložit *StartArrInspector.html* do počítače.
1. Otevřete *soubor StartArrInspector.html,* chcete-li načíst arrinspector relace.

## <a name="the-performance-panel"></a>Panel Výkon

![Panel výkonnosti](./media/performance-panel.png)

Tento panel zobrazuje grafy všech hodnot výkonu na snímek vystavených serverem. Hodnoty v současné době zahrnují čas snímků, FPS, využití CPU a paměti, statistiky paměti, jako je celkové využití paměti RAM, počty objektů atd.

Chcete-li vizualizovat jeden z těchto parametrů, klepněte na tlačítko **Přidat nový** a vyberte jednu z dostupných hodnot zobrazených v dialogovém okně. Tato akce přidá do panelu nový posunovací graf, který sleduje hodnoty v reálném čase. Na jeho pravé straně můžete vidět *minimální*, *maximální* a *aktuální* hodnotu.

Posouvání grafu přetažením jeho obsahu myší, ale posouvání vodorovně je možné pouze v případě, že je ArrInspector v pozastaveném stavu.

Podržením klávesy CTRL při tažení můžete zvětšit. Horizontální zoom lze také ovládat pomocí posuvníku v dolní části.

Svislý rozsah je ve výchozím nastavení vypočítán na základě aktuálně zobrazených hodnot a min a max hodnoty jsou zobrazeny v textových polích vpravo. Pokud jsou hodnoty nastaveny ručně, buď zadáním přímo do textového pole, nebo posouváním/přiblížením, graf tyto hodnoty použije. Chcete-li obnovit automatické svislé rámování, klepněte na ikonu v pravém horním rohu.

![svislý rozsah](./media/vertical-range.png)

## <a name="the-log-panel"></a>Panel Protokol

![Panel protokolu](./media/log-panel.png)

Panel protokolu zobrazuje seznam zpráv protokolu generovaných na straně serveru. Při připojení se zobrazí až 200 předchozích zpráv protokolu a bude tisknout nové, jakmile k nim dojde.

Seznam můžete filtrovat na základě `[Error/Warning/Info/Debug]` typu protokolu pomocí tlačítek v horní části.
![Tlačítka filtru protokolů](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>Panel Zachycení časování dat

![Časování sběru dat](./media/timing-data-capture.png)

Tento panel slouží k zachycení informací o časování ze serveru a ke stažení. Soubor používá [formát Chrome Tracing JSON](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit). Chcete-li data zkontrolovat, otevřete Chrome na adrese URL `Chrome://tracing` a přetáhněte stažený soubor na stránku. Časování data jsou průběžně shromažďovány v pevné velikosti ring-buffer. Při zápisu zahrnuje zachycení pouze informace o bezprostřední minulosti, což znamená několik sekund až několik minut.

## <a name="the-scene-inspection-panel"></a>Panel Pro kontrolu scény

![Panel pro kontrolu scény](./media/scene-inspection-panel.png)

Tento panel zobrazuje strukturu vykreslené scény. Hierarchie objektů je vlevo, obsah vybraného objektu je vpravo. Panel je jen pro čtení a je aktualizován v reálném čase.

## <a name="the-vm-debug-information-panel"></a>Panel Informace o ladění virtuálního měn

![Informační panel o ladění virtuálních vod](./media/state-debugger-panel.png)

Tento panel nabízí některé funkce ladění.

### <a name="restart-service"></a>Restartovat službu

Tlačítko **Restartovat službu** restartuje dobu runtime na virtuálním počítači, ke kterému je arrInspector připojen. Každý připojený klient bude odpojen a stránka arrInspector musí být znovu načtena, aby se připojila k restartování služby.

### <a name="collect-debug-information"></a>Shromažďování informací o ladění

Tlačítko **Shromáždit informace o ladění pro virtuální počítač** otevře dialogové okno, které umožňuje aktivovat instanci ARR pro shromažďování informací o ladění na virtuálním počítači:

![Dialogové okno Informace o ladění virtuálního provozu](./media/state-debugger-dialog.png)

Ladicí informace pomáhá týmu vzdáleného vykreslování Azure analyzovat všechny problémy, ke kterým dochází v spuštěné instanci ARR. Dialogové okno obsahuje textové pole, které poskytuje další podrobnosti, například kroky k reprodukci problému.

Po kliknutí na tlačítko **Spustit shromažďování** se dialogové okno zavře a začne proces sběru. Shromažďování informací o virtuálním počítači může trvat několik minut.

![Probíhá shromažďování informací o ladění virtuálního provozu](./media/state-debugger-panel-in-progress.png)

Po dokončení kolekce obdržíte oznámení v okně ArrInspector. Toto oznámení obsahuje ID, které identifikuje tuto konkrétní kolekci. Nezapomeňte uložit toto ID předat týmu Azure vzdálené vykreslování.

![Úspěch kolekce informací o ladění virtuálního týmu](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> Nelze stáhnout nebo jinak přistupovat k informacím o ladění virtuálního mísy. Ke shromážděným datům má přístup pouze tým vzdáleného vykreslování Azure. Musíte nás kontaktovat a poslat ID sbírky spolu, abychom prošetřili problém, který vidíte.

## <a name="pause-mode"></a>Režim pozastavení

V pravém horním rohu přepínač umožňuje pozastavit živou aktualizaci panelů. Tento režim může být užitečné pečlivě zkontrolovat konkrétní stav.

![Režim pozastavení](./media/pause-mode.png)

Při opětovném povolení živé aktualizace se všechny panely resetují.

## <a name="host-configuration"></a>Konfigurace hostitele

Ve výchozím nastavení se nástroj připojuje k serveru ARR, který je spuštěn na stejném hostiteli, který obsluhuje arrinspector. Můžete jej však nakonfigurovat tak, aby kontroloval jiný server, za předpokladu, že je spuštěna instance ARR s otevřeným portem nástrojů.

Chcete-li tak učinit, přezouvejte hlavní nabídku na levé straně záhlaví a vyberte *konfiguraci hostitele*. Klikněte na **Přidat nového hostitele**a zadejte název a název hostitele. Pro *název hostitele* používejte pouze `.mixedreality.azure.com`název hostitele končící `http://` na , nezahrnejte ani port.

![Konfigurace hostitele](./media/host-configuration.png)

Chcete-li rychle přepnout z jednoho hostitele na druhého, použijte rozbalovací rozbalovací soubor v pravém horním rohu.

![Kombinace hostitele](./media/host-switch-combo.png)

Seznam hostitelů je uložen v místním úložišti prohlížeče, takže bude zachován při opětovném otevření stejného prohlížeče.
