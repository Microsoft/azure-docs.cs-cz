---
title: Použití řídicího panelu Připojené továrny – Azure | Dokumenty společnosti Microsoft
description: Tento článek popisuje, jak používat funkce řídicího panelu Připojené továrny ke sledování a správě průmyslových zařízení IoT.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: b53177d578768428665891704269e63bd8edb09e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73820182"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Použití funkcí na řídicím panelu akcelerátoru řešení Připojené továrny

Nasadit [cloudové řešení pro správu mých průmyslových zařízení IoT](quickstart-connected-factory-deploy.md) rychlý start vám ukázal, jak se orientovat na řídicím panelu a reagovat na alarmy. Tento návod vám ukáže některé další funkce řídicího panelu, které můžete použít ke sledování a správě průmyslových zařízení IoT.

## <a name="apply-filters"></a>Použití filtrů

Informace zobrazené na řídicím panelu můžete filtrovat buď v panelu **Umístění továrny,** nebo v panelu **Alarmy:**

1. Kliknutím na ikonu **trychtýře** na panelu umístění továren nebo na panelu alarmů zobrazte seznam dostupných filtrů.

1. Zobrazí se panel filtrů:

    [![Filtry v akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Vyberte filtr, který požadujete, a klepněte na **tlačítko Použít**. Do polí filtru je také možné zadat volný text.

1. Poté se použije filtr. Ikona dodatečné cesty označuje, že je použit filtr:

    [![Použitý filtr akcelerátoru řešení připojené továrny](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Aktivní filtr nemá vliv na zobrazené hodnoty OEE a Klíčových ukazatelů výkonu, pouze filtruje obsah seznamu.

1. Chcete-li filtr vymazat, klepněte na trychtýř a v panelu filtrů klepněte na **tlačítko Vymazat.**

## <a name="browse-an-opc-ua-server"></a>Procházení serveru OPC UA

Při nasazení akcelerátoru řešení automaticky zřídíte sadu simulovaných serverů OPC UA, které můžete procházet z řídicího panelu. Simulované servery usnadňují experimentování s akcelerátorem řešení bez nutnosti nasazovat skutečné servery.

1. Klikněte na **ikonu prohlížeče** na navigačním panelu řídicího panelu:

    [![Prohlížeč serveru v akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Vyberte jeden ze serverů ze seznamu, který zobrazuje servery nasazené pro vás v akcelerátoru řešení:

    [![Seznam serverů akcelerátoru řešení připojené výroby](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Klikněte na **Připojit**, zobrazí se dialogové okno zabezpečení. Pro simulaci je bezpečné klepnout na tlačítko **Pokračovat**.

1. Pokud chcete rozbalit některý z uzlů ve stromu serveru, klikněte na něj. Uzly, které publikují telemetrii, mají vedle sebe zaškrtnutí:

    [![Strom serveru v akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Pokud chcete s uzlem provést operaci čtení, zápisu, publikování nebo volání, klikněte na něj pravým tlačítkem myši. Akce, které máte k dispozici, závisí na vašich oprávněních a atributech uzlu. Možnost čtení otevře místní panel se zobrazenou hodnotou konkrétního uzlu. Možnost zápisu otevře místní panel, na kterém můžete zadat novou hodnotu. Možnost volání zobrazí uzel, kde můžete zadat parametry volání.

## <a name="publish-a-node"></a>Publikování uzlu

Při procházení *simulovaného serveru OPC UA* máte také možnost publikovat nové uzly. V řešení můžete analyzovat telemetrii z těchto uzlů. Tyto *simulované servery OPC UA* usnadňují experimentování s akcelerátorem řešení bez nasazení skutečných zařízení:

1. Ve stromové struktuře prohlížeče serveru OPC UA přejděte k uzlu, který chcete publikovat.

1. Klikněte na uzel pravým tlačítkem myši. Klikněte na **Publikovat**:

    [![Uzel publikování uzlu publikování řešení připojené továrny](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. Otevře se místní panel s oznámením o úspěšném publikování. Uzel se zobrazí v zobrazení úrovně stanice se zaškrtnutím vedle:

    [![Akcelerátor řešení připojené továrny publikuje úspěch](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Příkazy a ovládání

Propojená továrna umožňuje ovládat a odesílat příkazy do průmyslových zařízení přímo z cloudu. Tuto funkci můžete použít při reakci na alarmy vygenerované zařízením. Můžete například odeslat příkaz do zařízení otevřít uvolňovací ventil tlaku. Dostupné příkazy najdete v uzlu **StationCommands** ve stromové struktuře serveru OPC UA. V tomto scénáři otevřete ventil pro uvolnění tlaku na montážní stanici výrobní linky v Mnichově. Chcete-li použít funkci příkazu a řízení, musíte být v roli **Správce** pro nasazení akcelerátoru řešení:

1. Přejděte do uzlu **StationCommands** ve stromu prohlížeče serveru OPC UA pro Mnichov, výrobní linku 0, montážní stanici.

1. Vyberte příkaz, který chcete použít. Klepněte pravým tlačítkem myši na uzel **OpenPressureReleaseValve.** Klikněte na **Volat**:

    [![Příkaz volání v akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Zobrazí se kontextový panel informující o tom, kterou metodu se chystáte volat, a všechny podrobnosti o parametrech. Klikněte na **Volat**:

    [![Parametry akcelerátoru řešení připojené výroby](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. Místní panel se aktualizuje a zobrazí informaci o úspěšném zavolání metody. Ověřit, že bylo volání úspěšné, můžete také přečtením hodnoty uzlu tlaku, který se aktualizovat v důsledku volání.

    [![Úspěšné volání v akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Informace pro pokročilé uživatele

Když nasadíte akcelerátor řešení, proces nasazení vytvoří ve vybraném předplatném Azure několik prostředků. Tyto prostředky můžete zobrazit na webu [Azure Portal](https://portal.azure.com). Proces nasazení vytváří **skupinu prostředků**. Její název bude vycházet z názvu, který jste vybrali pro akcelerátor řešení:

[![Skupina prostředků akcelerátoru řešení připojené továrny](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Nastavení každého prostředku se zobrazí, když jej vyberete v seznamu ve skupině prostředků.

Můžete také zobrazit zdrojový kód pro akcelerátor řešení v úložišti GitHub [připojené azure-iot-factory.](https://github.com/Azure/azure-iot-connected-factory)

Až budete hotovi, můžete odstranit akcelerátor řešení z předplatného Azure na [webu azureiotsolutions.com.](https://www.azureiotsolutions.com/Accelerators#dashboard) Tento web umožňuje snadno odstranit všechny prostředky, které se zřídily při vytváření akcelerátoru řešení.

> [!NOTE]
> Chcete-li zajistit odstranění všeho, co souvisí s akcelerátorem řešení, odstraňte jej na [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) webu. Skupinu prostředků na portálu neodstraňujte.

## <a name="next-steps"></a>Další kroky

Když jste teď nasadili fungující akcelerátor řešení, můžete pokračovat v seznamování se s akcelerátory řešení IoT přečtením následujících článků:

* [Konfigurace akcelerátoru řešení připojené továrny](iot-accelerators-connected-factory-configure.md)
* [Oprávnění na webu azureiotsolutions.com](iot-accelerators-permissions.md)
