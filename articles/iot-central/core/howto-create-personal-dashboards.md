---
title: Vytvoření osobních řídicích panelů Azure IoT Central | Microsoft Docs
description: Jako uživatel se naučíte vytvářet a spravovat osobní řídicí panely.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: conceptual
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: 9da7efad816a466eb9d2902e36a95c5ae0fa626b
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72950662"
---
# <a name="create-and-manage-multiple-dashboards"></a>Vytvoření a správa několika řídicích panelů

**Řídicí panel** je stránka, která se načte při prvním přechodu do aplikace. **Tvůrce** v aplikaci definuje výchozí řídicí panel aplikace pro všechny uživatele. Tento výchozí řídicí panel můžete nahradit vlastním přizpůsobeným řídicím panelem aplikace. Můžete mít několik řídicích panelů, které zobrazují různá data a mezi nimi přepínat. 

Pokud jste **správcem** aplikace, můžete také vytvořit až 10 řídicích panelů na úrovni aplikace pro sdílení s dalšími uživateli aplikace. Pouze **Správci** mají možnost vytvářet, upravovat a odstraňovat řídicí panely na úrovni aplikace. 

## <a name="create-dashboard"></a>Vytvořit řídicí panel

Následující snímek obrazovky ukazuje řídicí panel v aplikaci vytvořené z vlastní šablony **aplikace** . Výchozí řídicí panel aplikace můžete nahradit osobním řídicím panelem, nebo pokud jste správce, na jiný řídicí panel na úrovni aplikace. Provedete to tak, že v levém horním rohu stránky vyberete **+ Nový** .
 
> [!div class="mx-imgBorder"]
> Řídicí panel ![pro aplikace založené na šabloně vlastní aplikace](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Výběr **+ Nový** otevře Editor řídicích panelů. V editoru můžete řídicímu panelu dát název a vybrat položky z knihovny. Knihovna obsahuje prvky dlaždic a řídicích panelů, které můžete použít k přizpůsobení řídicího panelu.

> [!div class="mx-imgBorder"]
> ](media/howto-create-personal-dashboards/dashboard-library.png) knihovny řídicích panelů ![

Pokud jste **správcem** aplikace, budete mít možnost přepnout, pokud chcete vytvořit řídicí panel na úrovni aplikace nebo řídicí panel na úrovni aplikace. Pokud vytvoříte řídicí panel osobní úrovně, budete ho moct zobrazit jenom vy. Pokud vytvoříte řídicí panel na úrovni aplikace, bude jej každý uživatel aplikace moci zobrazit. Po zadání nadpisu a výběru typu řídicího panelu, který chcete vytvořit, můžete uložit a přidat dlaždice později. Nebo pokud jste nyní připraveni a Přidali jste šablonu zařízení a instanci zařízení, můžete pokračovat a vytvořit svou první dlaždici. 

> [!div class="mx-imgBorder"]
> ![ve formuláři Konfigurace podrobností o zařízení s podrobnostmi o teplotní](media/howto-create-personal-dashboards/device-details.png)

Můžete například přidat dlaždici **telemetrie** pro aktuální teplotu zařízení. Postupujte následovně:
1. Vybrat **šablonu zařízení**
1. Vyberte **instanci zařízení** pro zařízení, které chcete zobrazit na dlaždici řídicího panelu. Pak se zobrazí seznam vlastností zařízení, které se dají na dlaždici použít.
1. Pokud chcete vytvořit dlaždici na řídicím panelu, klikněte na **teplotu** a přetáhněte ji do oblasti řídicího panelu. Můžete také kliknout na zaškrtávací políčko vedle **teploty** a kliknout na **zkombinovat**. Následující snímek obrazovky ukazuje, jak vybrat šablonu zařízení a instanci zařízení a pak na řídicím panelu vytvořit dlaždici telemetrie s teplotou.
1. Vyberte **Uložit** v levém horním rohu a uložte dlaždici na řídicí panel.

> [!div class="mx-imgBorder"]
> karta řídicího panelu ![s podrobnostmi pro dlaždici teploty](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Když teď zobrazíte svůj osobní řídicí panel, zobrazí se nová dlaždice s nastavením **teploty** pro zařízení:

> [!div class="mx-imgBorder"]
> karta řídicího panelu ![s podrobnostmi pro dlaždici teploty](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Můžete prozkoumat další typy dlaždic v knihovně, abyste zjistili, jak dále přizpůsobit osobní řídicí panely.

Další informace o tom, jak používat dlaždice v Azure IoT Central, najdete v tématu [Přidání dlaždic do řídicího panelu](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Správa řídicích panelů

Můžete mít několik osobních řídicích panelů a mezi nimi přepínat nebo si vybrat jeden z výchozích řídicích panelů aplikací:

> [!div class="mx-imgBorder"]
> ![přepínání mezi řídicími panely](media/howto-create-personal-dashboards/switch-dashboards.png)

Můžete upravit osobní řídicí panely a odstranit všechny řídicí panely, které už nepotřebujete. Pokud jste **správce**, máte také možnost upravovat nebo odstraňovat řídicí panely na úrovni aplikace i.

> [!div class="mx-imgBorder"]
> ![odstranění řídicích panelů](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se seznámili s vytvářením a správou osobních řídicích panelů, se můžete [seznámit s tím, jak spravovat předvolby aplikace](howto-manage-preferences.md) .
