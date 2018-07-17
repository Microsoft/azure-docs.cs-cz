---
title: Pomocí řídicího panelu připojené továrny – Azure | Dokumentace Microsoftu
description: Naučte se používat funkce řídicího panelu pro propojenou továrnu.
author: dominicbetts
manager: timlt
ms.service: iot-accelerators
services: iot-accelerators
ms.topic: conceptual
ms.date: 07/10/2018
ms.author: dobett
ms.openlocfilehash: 4af93b5667181941585cc1ac89f5696379962026
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/17/2018
ms.locfileid: "39075974"
---
# <a name="use-features-in-the-connected-factory-solution-accelerator-dashboard"></a>Použití funkcí na řídicím panelu akcelerátor řešení připojená továrna

[Nasadit cloudové řešení ke správě mých průmyslová zařízení IoT](quickstart-connected-factory-deploy.md) rychlý start vám ukázal, jak přejít řídicí panel a reagovat na alarmy. Tato příručka ukazuje některé další řídicí panel funkce, která vám pomůže monitorovat a spravovat vaše průmyslová zařízení IoT.

## <a name="apply-filters"></a>Použití filtrů

Můžete filtrovat informace zobrazené na řídicím panelu buď v **umístění továren** panelu nebo **alarmy** panelu:

1. Kliknutím na ikonu **trychtýře** na panelu umístění továren nebo na panelu alarmů zobrazte seznam dostupných filtrů.

1. Zobrazí se panel filtrů:

    [![Propojené továrny řešení akcelerátoru filtry](./media/iot-accelerators-connected-factory-dashboard/filterpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterpanel-expanded.png#lightbox)

1. Vyberte filtr, který mají a klikněte na **použít**. Je také možné zadat do polí filtru libovolný text.

1. Filtr se pak použije. Navíc trychtýřového grafu ikona značí, že se filtr použije:

    [![Propojené továrny řešení akcelerátoru použit filtr.](./media/iot-accelerators-connected-factory-dashboard/filterapplied-inline.png)](./media/iot-accelerators-connected-factory-dashboard/filterapplied-expanded.png#lightbox)

    > [!NOTE]
    > Aktivní filtr nemá vliv na zobrazené hodnoty celkové efektivity zařízení a klíčových ukazatelů výkonu, pouze filtruje obsah seznamu.

1. Chcete filtr vymazat, klikněte na Trychtýř a kliknutím na **vymazat** v panelu filtru.

## <a name="browse-an-opc-ua-server"></a>Procházení serveru OPC UA

Při nasazení akcelerátoru řešení automaticky zřídíte sadu simulované servery OPC UA, které můžete přejít z řídicího panelu. Simulované servery umožňují snadno experimentovat s akcelerátor řešení, aniž by bylo nutné nasazovat skutečná servery. Pokud chcete k řešení připojit skutečný server OPC UA, přečtěte si článek [připojení zařízení OPC UA na akcelerátor řešení připojená továrna](iot-accelerators-connected-factory-gateway-deployment.md) kurzu.

1. Klikněte na tlačítko **ikonu prohlížeče** na navigačním panelu řídicího panelu:

    [![Prohlížeč akcelerátoru server připojený objekt pro vytváření řešení](./media/iot-accelerators-connected-factory-dashboard/browser-inline.png)](./media/iot-accelerators-connected-factory-dashboard/browser-expanded.png#lightbox)

1. Vyberte jeden ze serverů ze seznamu, který ukazuje servery, které jsou pro vás nasazeny v akcelerátoru řešení:

    [![Seznam akcelerátoru serverů připojených objekt pro vytváření řešení](./media/iot-accelerators-connected-factory-dashboard/serverlist-inline.png)](./media/iot-accelerators-connected-factory-dashboard/serverlist-expanded.png#lightbox)

1. Klikněte na **Připojit**, zobrazí se dialogové okno zabezpečení. Pro tuto simulaci je bez obav kliknout na **pokračovat**.

1. Pokud chcete rozbalit některý z uzlů ve stromu serveru, klikněte na něj. Uzly, které publikují telemetrii se zobrazí zaškrtnutí vedle kterých je:

    [![Strom akcelerátoru serveru připojený objekt pro vytváření řešení](./media/iot-accelerators-connected-factory-dashboard/servertree-inline.png)](./media/iot-accelerators-connected-factory-dashboard/servertree-expanded.png#lightbox)

1. Pokud chcete s uzlem provést operaci čtení, zápisu, publikování nebo volání, klikněte na něj pravým tlačítkem myši. Akce, které máte k dispozici, závisí na vašich oprávněních a atributech uzlu. Možnost čtení otevře místní panel se zobrazenou hodnotou konkrétního uzlu. Možnost zápisu otevře místní panel, na kterém můžete zadat novou hodnotu. Možnost volání zobrazí uzel, kde můžete zadat parametry volání.

## <a name="publish-a-node"></a>Publikování uzlu

Při procházení *simulovaného serveru OPC UA* máte také možnost publikovat nové uzly. V řešení můžete analyzovat telemetrii z těchto uzlů. Tyto *simulované servery OPC UA* umožňují snadno experimentovat s akcelerátor řešení bez nasazování skutečných zařízení:

1. Ve stromové struktuře prohlížeče serveru OPC UA přejděte k uzlu, který chcete publikovat.

1. Klikněte na uzel pravým tlačítkem myši. Klikněte na tlačítko **publikovat**:

    [![Publikování uzlu v akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/publishnode-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishnode-expanded.png#lightbox)

1. Otevře se místní panel s oznámením o úspěšném publikování. Uzel se zobrazí v zobrazení úrovně stanice zaškrtnutí vedle něj:

    [![Úspěšné publikování v akcelerátoru řešení propojené továrny](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/publishsuccess-expanded.png#lightbox)

## <a name="command-and-control"></a>Příkazy a ovládání

Propojená továrna umožňuje ovládat a odesílat příkazy do průmyslových zařízení přímo z cloudu. Tuto funkci můžete použít při reakci na alarmy vygenerované zařízením. Můžete například odeslat příkaz do zařízení otevřete ventil pro uvolnění tlaku. Dostupné příkazy najdete v uzlu **StationCommands** ve stromové struktuře serveru OPC UA. V tomto scénáři otevřete ventil pro uvolnění tlaku na montážní stanici výrobní linky v Mnichově. Pokud chcete používat funkce příkazů a ovládání, musíte být v **správce** role pro nasazení akcelerátoru řešení:

1. Přejděte **StationCommands** uzel ve stromu prohlížeče serveru OPC UA pro Mnichově, výrobní linky 0, montážní stanici.

1. Vyberte příkaz, který chcete použít. Klikněte pravým tlačítkem myši **OpenPressureReleaseValve** uzlu. Klikněte na tlačítko **volání**:

    [![Propojené továrny řešení akcelerátoru volání – příkaz](./media/iot-accelerators-connected-factory-dashboard/callcommand-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callcommand-expanded.png#lightbox)

1. Místní panel informující o jakou metodu jste o volání a podrobnostech o parametrech. Klikněte na tlačítko **volání**:

    [![Propojené továrny řešení akcelerátoru volání parametry](./media/iot-accelerators-connected-factory-dashboard/callpanel-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callpanel-expanded.png#lightbox)

1. Místní panel se aktualizuje a zobrazí informaci o úspěšném zavolání metody. Ověřit, že bylo volání úspěšné, můžete také přečtením hodnoty uzlu tlaku, který se aktualizovat v důsledku volání.

    [![Připojené úspěšné volání akcelerátoru objekt pro vytváření řešení](./media/iot-accelerators-connected-factory-dashboard/callsuccess-inline.png)](./media/iot-accelerators-connected-factory-dashboard/callsuccess-expanded.png#lightbox)

## <a name="behind-the-scenes"></a>Informace pro pokročilé uživatele

Když nasadíte akcelerátor řešení, proces nasazení vytvoří ve vybraném předplatném Azure několik prostředků. Tyto prostředky můžete zobrazit ve službě Azure [portál](https://portal.azure.com). Proces nasazení vytváří **skupinu prostředků**. Její název bude vycházet z názvu, který jste vybrali pro akcelerátor řešení:

[![Připojené skupiny prostředků akcelerátoru objekt pro vytváření řešení](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-inline.png)](./media/iot-accelerators-connected-factory-dashboard/resourcegroup-expanded.png#lightbox)

Nastavení každého prostředku se zobrazí, když jej vyberete v seznamu ve skupině prostředků.

Můžete také zobrazit zdrojový kód pro akcelerátor řešení v [azure-iot-connected-factory](https://github.com/Azure/azure-iot-connected-factory) úložiště GitHub.

Jakmile budete hotovi, můžete odstranit akcelerátor řešení z vašeho předplatného Azure na [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) lokality. Tento web umožňuje snadno odstranit všechny prostředky, které se zřídily při vytváření akcelerátoru řešení.

> [!NOTE]
> Aby bylo zajištěno, že jste odstranili opravdu všechno spojené s akcelerátor řešení, odstraňte řešení na [azureiotsolutions.com](https://www.azureiotsolutions.com/Accelerators#dashboard) lokality. Skupinu prostředků na portálu neodstraňujte.

## <a name="next-steps"></a>Další postup

Když jste teď nasadili fungující akcelerátor řešení, můžete pokračovat v seznamování se s akcelerátory řešení IoT přečtením následujících článků:

* [Propojené továrny seznámení s akcelerátory řešení](iot-accelerators-connected-factory-sample-walkthrough.md)
* [Připojení zařízení k akcelerátoru řešení připojená továrna](iot-accelerators-connected-factory-gateway-deployment.md)
* [Oprávnění na webu azureiotsolutions.com](iot-accelerators-permissions.md)
