---
title: Kontrolní nástroj ArrInspector
description: Uživatelská příručka k nástroji ArrInspector
author: florianborn71
ms.author: flborn
ms.date: 03/09/2020
ms.topic: article
ms.openlocfilehash: 300e0ff26d643ae0263d21e604cb26da37a18841
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "97723821"
---
# <a name="the-arrinspector-inspection-tool"></a>Kontrolní nástroj ArrInspector

ArrInspector je webový nástroj, který slouží ke kontrole spuštěné relace vzdáleného vykreslování Azure. Je určen k použití pro účely ladění, ke kontrole struktury vykreslené scény, zobrazení zpráv protokolu a monitorování výkonu v reálném čase na serveru.

![ArrInspector](./media/arr-inspector.png)

## <a name="connecting-to-the-arrinspector"></a>Připojení k ArrInspector

Jakmile obdržíte název hostitele (končícího `mixedreality.azure.com` ) vašeho serveru ARR, připojte se pomocí [ConnectToArrInspectorAsync](../../how-tos/frontend-apis.md#connect-to-arr-inspector). Tato funkce vytvoří `StartArrInspector.html` na zařízení, na kterém je aplikace spuštěná. Pokud chcete spustit ArrInspector, otevřete tento soubor v počítači přes prohlížeč (Edge, Firefox nebo Chrome). Soubor je platný pouze po dobu 24 hodin.

Pokud aplikace, která volá, `ConnectToArrInspectorAsync` je už na počítači spuštěná:

* Pokud používáte integraci Unity, může se vám automaticky spustit.
* V opačném případě najdete soubor ve *složkách uživatele \\ LocalAppData \\ [your_app] \\ AC \\ tempo*.

Pokud aplikace běží na HoloLens:

1. Přístup k HoloLens pomocí [portálu zařízení s Windows](/windows/mixed-reality/using-the-windows-device-portal)
1. Přejít do *systémového > Průzkumník souborů*.
1. Přejděte do *složky uživatele \\ LocalAppData \\ [your_app] \\ AC \\ TEMP*.
1. Uložte *StartArrInspector.html* do počítače.
1. Otevřete *StartArrInspector.html* a načtěte ArrInspector relace.

## <a name="the-performance-panel"></a>Panel výkonu

![Panel výkonu](./media/performance-panel.png)

Tento panel zobrazuje grafy všech hodnot výkonu jednotlivých rámců vystavených serverem. Hodnoty aktuálně zahrnují dobu snímku, FPS, využití procesoru a paměti, statistiky paměti, jako je celkové využití paměti RAM, počty objektů atd.

Chcete-li vizualizovat jeden z těchto parametrů, klikněte na tlačítko **Přidat nové** a vyberte jednu z dostupných hodnot zobrazených v dialogovém okně. Tato akce přidá nový rolovací graf na panel a vyhodnotí hodnoty v reálném čase. Napravo můžete zobrazit *minimální*, *maximální* a *aktuální* hodnotu.

Graf můžete posunout tak, že přetáhnete jeho obsah myší. posouvání je ale možné horizontálně, jenom když je ArrInspector ve stavu pozastaveno.

Podržení klávesy CTRL při přetahování, umožňuje přiblížení. Horizontální přiblížení se dá také ovládat posuvníkem v dolní části.

Svislá oblast je ve výchozím nastavení vypočítána na základě aktuálně zobrazených hodnot a minimální a maximální hodnoty jsou zobrazeny v textových polích na pravé straně. Pokud jsou hodnoty nastaveny ručně, buď jejich zadáním přímo do textového pole nebo posouváním/přiblížením, budou tyto hodnoty použity v grafu. Chcete-li obnovit automatickou svislou rámců, klikněte na ikonu v pravém horním rohu.

![svislý rozsah](./media/vertical-range.png)

## <a name="the-log-panel"></a>Panel protokolu

![Panel protokolu](./media/log-panel.png)

Na panelu protokol se zobrazuje seznam zpráv protokolu generovaných na straně serveru. Při připojení se zobrazí až 200 předchozích zpráv protokolu a vytisknou se nové zprávy, když k nim dojde.

Seznam můžete filtrovat podle typu protokolu `[Error/Warning/Info/Debug]` pomocí tlačítek v horní části.
![Tlačítka filtru protokolu](./media/log-filter.png)

## <a name="the-timing-data-capture-panel"></a>Panel pro zachycení časových dat

![Zachycení dat časování](./media/timing-data-capture.png)

Tento panel slouží k zaznamenání informací o časování ze serveru a jeho stažení. Soubor používá [formát JSON pro trasování Chrome](https://docs.google.com/document/d/1CvAClvFfyA5R-PhYUmn5OOQtYMH4h6I0nSsKchNAySU/edit). Chcete-li zkontrolovat data, otevřete v adrese URL Chrome `Chrome://tracing` a přetáhněte stažený soubor na stránku. Data časování se průběžně shromažďují v kruhové paměti s pevnou velikostí. Po zapsání zaznamená zachytávání jenom informace o bezprostředním minulosti, což znamená několik sekund až pár minut.

## <a name="the-scene-inspection-panel"></a>Panel pro kontrolu scény

![Panel pro kontrolu scény](./media/scene-inspection-panel.png)

Tento panel zobrazuje strukturu vykreslené scény. Hierarchie objektů je na levé straně, obsah vybraného objektu je napravo. Panel je jen pro čtení a aktualizuje se v reálném čase.

## <a name="the-vm-debug-information-panel"></a>Panel ladicí informace virtuálního počítače

![Panel ladicí informace virtuálního počítače](./media/state-debugger-panel.png)

Tento panel nabízí některé funkce ladění.

### <a name="restart-service"></a>Restartovat službu

Tlačítko **restartovat službu** spustí na virtuálním počítači, ke kterému je připojen arrInspector, modul runtime. Připojí se odpojený klient a stránka arrInspector se musí znovu načíst, aby se mohla připojit k restartované službě.

### <a name="collect-debug-information"></a>Shromažďovat informace o ladění

Tlačítko **shromáždit informace o ladění pro virtuální počítač** otevře dialogové okno, které umožňuje aktivovat instanci ARR ke shromáždění informací o ladění na virtuálním počítači:

![Dialogové okno informace o ladění virtuálního počítače](./media/state-debugger-dialog.png)

Ladicí informace pomáhají týmu vzdáleného vykreslování Azure analyzovat všechny problémy, ke kterým dojde v běžící instanci ARR. Dialogové okno obsahuje textové pole, které poskytuje další podrobnosti, například kroky pro reprodukování problému.

Po kliknutí na tlačítko **Spustit shromažďování** dojde k zavření dialogového okna a zahájení procesu shromažďování. Shromažďování informací na virtuálním počítači může trvat několik minut.

![Probíhá shromažďování informací o ladění virtuálního počítače](./media/state-debugger-panel-in-progress.png)

Po dokončení shromažďování se v okně ArrInspector zobrazí oznámení. Toto oznámení obsahuje ID, které identifikuje tuto konkrétní kolekci. Nezapomeňte toto ID uložit, abyste ho mohli předat týmu vzdáleného vykreslování Azure.

![Úspěšné shromažďování informací o ladění virtuálních počítačů](./media/state-debugger-snackbar-success.png)

> [!IMPORTANT]
> Nemůžete si stáhnout nebo jinak získat přístup k informacím o ladění virtuálních počítačů. K shromážděným datům má přístup jenom tým vzdáleného vykreslování Azure. Musíte nás kontaktovat a poslat ID kolekce společně, abychom prošetřili problém, který vidíte.

## <a name="pause-mode"></a>Režim pozastavení

V pravém horním rohu vám přepínač umožní pozastavit živou aktualizaci panelů. Tento režim může být užitečný k pečlivému prověření určitého stavu.

![Režim pozastavení](./media/pause-mode.png)

Při opětovném povolení živé aktualizace se resetují všechny panely.