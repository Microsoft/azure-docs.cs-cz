---
title: Použití řídicího panelu propojené továrny – Azure | Microsoft Docs
description: Tento článek popisuje, jak pomocí funkcí řídicího panelu propojená továrna monitorovat a spravovat zařízení v průmyslových zařízeních IoT.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 797bd97940aa49db87b4ca4dd96f140208cd69b6
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96011213"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Použití funkcí v řídicím panelu akcelerátoru řešení propojené továrny

> [!IMPORTANT]
> I když aktualizujeme Tento článek, přečtěte si nejaktuálnější obsah v tématu [Azure Data IoT](https://azure.github.io/Industrial-IoT/) .

[Nasazení cloudového řešení pro správu zařízení IoT v průmyslu](quickstart-connected-factory-deploy.md) vám ukáže, jak přejít na řídicí panel a reagovat na alarmy. V této příručce se dozvíte několik dalších funkcí řídicího panelu, pomocí kterých můžete monitorovat a spravovat zařízení v průmyslových zařízeních IoT.

## <a name="apply-filters"></a>Použití filtrů

Informace zobrazené na řídicím panelu můžete filtrovat buď na panelu **umístění továrny** , nebo na panelu **alarmů** :

1. Kliknutím na ikonu **trychtýře** na panelu umístění továren nebo na panelu alarmů zobrazte seznam dostupných filtrů.

1. Zobrazí se panel filtry:

    [![Filtry v akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Vyberte filtr, který požadujete, a klikněte na **použít**. Je také možné zadat do polí filtru bezplatný text.

1. Filtr se pak použije. Další ikona trychtýřového grafu označuje, že se používá filtr:

    [![Použije se filtr akcelerátoru řešení propojené továrny.](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Aktivní filtr nemá vliv na zobrazené hodnoty celkové efektivity zařízení a klíčového ukazatele výkonu, filtruje pouze obsah seznamu.

1. Pokud chcete filtr vymazat, klikněte na trychtýř a na panelu filtru klikněte na **Vymazat** .

## <a name="browse-an-opc-ua-server"></a>Procházení serveru OPC UA

Když nasadíte akcelerátor řešení, automaticky zřídíte sadu simulovaných serverů OPC UA, které můžete procházet z řídicího panelu. Simulované servery usnadňují experimentování s akcelerátorem řešení bez nutnosti nasazovat reálné servery.

1. Klikněte na **ikonu prohlížeče** na navigačním panelu řídicího panelu:

    [![Prohlížeč serveru v akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. V seznamu vyberte jeden ze serverů, který ukazuje servery nasazené v akcelerátoru řešení:

    [![Seznam serverů akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Klikněte na **Připojit**, zobrazí se dialogové okno zabezpečení. Pro simulaci je bezpečné kliknout na **pokračovat**.

1. Pokud chcete rozbalit některý z uzlů ve stromu serveru, klikněte na něj. Uzly, které publikují telemetrii, mají vedle sebe značku zaškrtnutí:

    [![Strom serveru v akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Pokud chcete s uzlem provést operaci čtení, zápisu, publikování nebo volání, klikněte na něj pravým tlačítkem myši. Akce, které máte k dispozici, závisí na vašich oprávněních a atributech uzlu. Možnost čtení otevře místní panel se zobrazenou hodnotou konkrétního uzlu. Možnost zápisu otevře místní panel, na kterém můžete zadat novou hodnotu. Možnost volání zobrazí uzel, kde můžete zadat parametry volání.

## <a name="publish-a-node"></a>Publikování uzlu

Při procházení *simulovaného serveru OPC UA* máte také možnost publikovat nové uzly. V řešení můžete analyzovat telemetrii z těchto uzlů. Tyto *simulované servery OPC UA* usnadňují experimentování s akcelerátorem řešení bez nutnosti nasazovat skutečná zařízení:

1. Ve stromové struktuře prohlížeče serveru OPC UA přejděte k uzlu, který chcete publikovat.

1. Klikněte na uzel pravým tlačítkem myši. Klikněte na **publikovat**:

    [![Uzel publikování akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. Otevře se místní panel s oznámením o úspěšném publikování. Uzel se zobrazí v zobrazení na úrovni stanice se znakem zaškrtnutí vedle něj:

    [![Úspěšnost publikování akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Příkazy a ovládání

Propojená továrna umožňuje ovládat a odesílat příkazy do průmyslových zařízení přímo z cloudu. Tuto funkci můžete použít při reakci na alarmy vygenerované zařízením. Například můžete odeslat příkaz do zařízení, aby se otevřel ventil pro uvolnění tlaku. Dostupné příkazy najdete v uzlu **StationCommands** ve stromové struktuře serveru OPC UA. V tomto scénáři otevřete ventil pro uvolnění tlaku na montážní stanici výrobní linky v Mnichově. Chcete-li používat funkce příkazu a ovládacího prvku, musíte být v roli **správce** pro nasazení akcelerátoru řešení:

1. Ve stromové struktuře prohlížeče serveru OPC UA vyhledejte uzel **StationCommands** pro Mnichov, výrobní linii 0 a montážní stanici.

1. Vyberte příkaz, který chcete použít. Klikněte pravým tlačítkem na uzel **OpenPressureReleaseValve** . Klikněte na **zavolat**:

    [![Příkaz volání v akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Zobrazí se místní panel s informacemi o tom, jakou metodu se chystáte volat, a všechny podrobnosti parametru. Klikněte na **zavolat**:

    [![Parametry volání akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. Místní panel se aktualizuje a zobrazí informaci o úspěšném zavolání metody. Ověřit, že bylo volání úspěšné, můžete také přečtením hodnoty uzlu tlaku, který se aktualizovat v důsledku volání.

    [![Úspěšné volání v akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Informace pro pokročilé uživatele

Když nasadíte akcelerátor řešení, proces nasazení vytvoří ve vybraném předplatném Azure několik prostředků. Tyto prostředky můžete zobrazit na webu [Azure Portal](https://portal.azure.com). Proces nasazení vytváří **skupinu prostředků**. Její název bude vycházet z názvu, který jste vybrali pro akcelerátor řešení:

[![Skupina prostředků akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Nastavení každého prostředku se zobrazí, když jej vyberete v seznamu ve skupině prostředků.

Zdrojový kód pro akcelerátor řešení můžete zobrazit také v úložišti GitHub [Azure-IoT-Connected-Factory](https://github.com/Azure/azure-iot-connected-factory) .

Až budete hotovi, můžete z předplatného Azure na webu [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) odstranit akcelerátor řešení. Tento web umožňuje snadno odstranit všechny prostředky, které se zřídily při vytváření akcelerátoru řešení.

> [!NOTE]
> Abyste měli jistotu, že odstraníte všechno související s akcelerátorem řešení, odstraňte ho na webu [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) . Skupinu prostředků na portálu neodstraňujte.

## <a name="next-steps"></a>Další kroky

Když jste teď nasadili fungující akcelerátor řešení, můžete pokračovat v seznamování se s akcelerátory řešení IoT přečtením následujících článků:

* [Konfigurace akcelerátoru řešení propojené továrny](iot-accelerators-connected-factory-configure.md)
* [Oprávnění na webu azureiotsolutions.com](iot-accelerators-permissions.md)
