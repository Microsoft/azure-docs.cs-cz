---
title: Vytvoření osobních řídicích panelů Azure IoT Central | Dokumenty společnosti Microsoft
description: Jako uživatel se dozvíte, jak vytvářet a spravovat osobní řídicí panely.
author: mavoge
ms.author: mavoge
ms.date: 10/17/2019
ms.topic: how-to
ms.service: iot-central
services: iot-central
manager: philmea
ms.openlocfilehash: db886006ff5b9adf3de0932951f6cce4958e8ebd
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80158125"
---
# <a name="create-and-manage-multiple-dashboards"></a>Vytvoření a správa více řídicích panelů

**Řídicí panel** je stránka, která se načte při prvním přechodu do aplikace. **Tvůrce** ve vaší aplikaci definuje výchozí řídicí panel aplikace pro všechny uživatele. Tento výchozí řídicí panel můžete nahradit vlastním přizpůsobeným řídicím panelem aplikace. Můžete mít několik řídicích panelů, které zobrazují různá data a přepínají mezi nimi. 

Pokud jste **správcem** aplikace, můžete také vytvořit až 10 řídicích panelů na úrovni aplikace, které můžete sdílet s ostatními uživateli aplikace. Pouze **správci** mají možnost vytvářet, upravovat a odstraňovat řídicí panely na úrovni aplikace. 

## <a name="create-dashboard"></a>Vytvořit řídicí panel

Následující snímek obrazovky ukazuje řídicí panel v aplikaci vytvořené ze šablony **vlastní aplikace.** Výchozí řídicí panel aplikace můžete nahradit osobním řídicím panelem, nebo pokud jste správce, jiný řídicí panel na úrovni aplikace. Chcete-li tak učinit, vyberte v levém horním rohu stránky možnost **+ Nový.**
 
> [!div class="mx-imgBorder"]
> ![Řídicí panel pro aplikace založený na šabloně "Vlastní aplikace"](media/howto-create-personal-dashboards/dashboard-custom-app.png)

Výběrem **možnosti + Nový** otevřete editor řídicích panelů. V editoru můžete řídicí panel pojmenovat a vybrat položky z knihovny. Knihovna obsahuje dlaždice a základní prvky řídicího panelu, které můžete použít k přizpůsobení řídicího panelu.

> [!div class="mx-imgBorder"]
> ![Knihovna řídicího panelu](media/howto-create-personal-dashboards/dashboard-library.png)

Pokud jste **správcem** aplikace, budete mít možnost přepínat, pokud chcete vytvořit řídicí panel osobní úrovně nebo řídicí panel na úrovni aplikace. Pokud vytvoříte řídicí panel osobní úrovně, uvidíte ho pouze vy. Pokud vytvoříte řídicí panel na úrovni aplikace, každý uživatel aplikace bude moci zobrazit. Po zadání názvu a výběru typu řídicího panelu, který chcete vytvořit, můžete dlaždice uložit a přidat později. Nebo pokud jste připraveni a přidali jste šablonu zařízení a instanci zařízení, můžete pokračovat a vytvořit první dlaždici. 

> [!div class="mx-imgBorder"]
> ![Konfigurace podrobností o zařízení" formulář s podrobnostmi o teplotě](media/howto-create-personal-dashboards/device-details.png)

Můžete například přidat dlaždici **Telemetrie** pro aktuální teplotu zařízení. Postupujte následovně:
1. Výběr **šablony zařízení**
1. Vyberte **instanci zařízení** pro zařízení, které chcete zobrazit na dlaždici řídicího panelu. Pak uvidíte seznam vlastností zařízení, které lze použít na dlaždici.
1. Chcete-li vytvořit dlaždici na řídicím panelu, klikněte na **Teplota** a přetáhněte ji do oblasti řídicího panelu. Můžete také kliknout na zaškrtávací políčko vedle **položky Teplota** a klepnout na tlačítko **Kombinovat**. Následující snímek obrazovky ukazuje výběr šablony zařízení a instance zařízení a poté vytvoření dlaždice Teplotní telemetrie na řídicím panelu.
1. V levém horním rohu vyberte **Uložit,** chcete-li dlaždici uložit na řídicí panel.

> [!div class="mx-imgBorder"]
> ![Dashboard" karta s podrobnostmi pro dlaždici Teplota](media/howto-create-personal-dashboards/temperature-tile-edit.png)

Nyní, když si prohlížíte svůj osobní řídicí panel, uvidíte novou dlaždici s nastavením **Teplota** pro zařízení:

> [!div class="mx-imgBorder"]
> ![Dashboard" karta s podrobnostmi pro dlaždici Teplota](media/howto-create-personal-dashboards/temperature-tile-complete.png)

Můžete prozkoumat další typy dlaždic v knihovně a zjistit, jak dále přizpůsobit osobní řídicí panely.

Další informace o tom, jak používat dlaždice v Azure IoT Central, najdete v tématu [Přidání dlaždic na řídicí panel](howto-add-tiles-to-your-dashboard.md).

## <a name="manage-dashboards"></a>Správa řídicích panelů

Můžete mít několik osobních řídicích panelů a přepínat mezi nimi nebo si vybrat z jednoho z výchozích řídicích panelů aplikací:

> [!div class="mx-imgBorder"]
> ![Přepínání mezi řídicími panely](media/howto-create-personal-dashboards/switch-dashboards.png)

Můžete upravit své osobní řídicí panely a odstranit všechny řídicí panely, které už nepotřebujete. Pokud jste **správce**, máte také možnost upravovat nebo odstraňovat řídicí panely na úrovni aplikace.

> [!div class="mx-imgBorder"]
> ![Odstranění řídicích panelů](media/howto-create-personal-dashboards/delete-dashboards.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili vytvářet a spravovat osobní řídicí panely, se můžete [naučit spravovat předvolby aplikací.](howto-manage-preferences.md)
