---
title: Vytvoření aplikace Azure IoT Central | Microsoft Docs
description: Vytvořte novou aplikaci Azure IoT Central pro správu chladících prodejních zařízení. Zobrazte telemetrická data vygenerovaná z vašich simulovaných zařízení.
author: tbhagwat3
ms.author: tanmayb
ms.date: 04/15/2018
ms.topic: quickstart
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 51c6753b1e4f2b08e93214abfcd7e18cb2e66613
ms.sourcegitcommit: 65b399eb756acde21e4da85862d92d98bf9eba86
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/22/2018
ms.locfileid: "36318309"
---
# <a name="create-an-azure-iot-central-application"></a>Vytvoření aplikace Azure IoT Central

Jako _tvůrce_ můžete pomocí uživatelského rozhraní Azure IoT Central definovat vaši aplikaci Azure IoT Central. V tomto rychlém startu se naučíte:

- Vytvořit aplikaci Azure IoT Central, který obsahuje ukázkovou _šablonu zařízení_ a simulovaná _zařízení_
- Zobrazit funkce šablony zařízení **Refrigerated Vending Machine** (Chladící prodejní automat) ve vaší aplikaci
- Zobrazit telemetrická data a analýzy ze simulovaných zařízení **Refrigerator** (Chladící box).

V tomto rychlém startu zobrazíte simulované zařízení **Refrigerator** (Chladící box) ze šablony zařízení. Simulované zařízení:

* Odesílá do vaší aplikace telemetrická data, jako je například teploty a tlak.
* Hlásí vaší aplikaci hodnoty vlastností zařízení, jako je upozornění na pohyb.
* Má nastavení zařízení, jako je třeba rychlost ventilátoru, která můžete nastavit v aplikaci.

Aby bylo možné otestovat aplikaci před připojením skutečného zařízení, aplikace Azure IoT Central umožňuje vygenerovat simulované zařízení ze šablony zařízení.

## <a name="create-the-application"></a>Vytvoření aplikace

K dokončení tohoto rychlého startu musíte vytvořit aplikaci Azure IoT Central z aplikační šablony **Sample Contoso**.

Přejděte na stránku [správce aplikací](https://aka.ms/iotcentral) Azure IoT Central. Potom zadejte e-mailovou adresu a heslo, které používáte pro přístup k vašemu předplatnému Azure:

![Zadání účtu organizace](media/quick-deploy-iot-central/sign-in.png)

Pokud chcete začít vytvářet novou aplikaci Azure IoT Central, zvolte **New Application** (Nová aplikace):

![Stránka správce aplikací Azure IoT Central](media/quick-deploy-iot-central/iotcentralhome.png)

Vytvoření nové aplikace Azure IoT Central:

1. Zvolte platební plán **Free Trial Application**.
1. Zvolte popisný název, jako je třeba **Contoso IoT**. Azure IoT Central pro vás vygeneruje jedinečnou předponu URL. Tuto předponu URL můžete změnit, aby byla snáze zapamatovatelná.
1. Zvolte šablonu aplikace **Sample Contoso**.
1. Potom zvolte **Create** (Vytvořit).

![Stránka vytvoření aplikace Azure IoT Central](media/quick-deploy-iot-central/iotcentralcreate.png)

## <a name="navigate-to-the-application"></a>Přechod k aplikaci

Až bude vaše aplikace hotová, zobrazí se její **domovská stránka**. Pokud chcete tuto domovskou stránku upravit, můžete v horní části přepnout _Design Mode_ (Režim návrhu). Adresa URL aplikace je adresa URL, kterou jste zadali v předchozím kroku:

![Stránka Application Builder (Tvůrce aplikací)](media/quick-deploy-iot-central/apphome.png)

Pomocí _levé navigační nabídky_ můžete získat přístup k různým oblastem vaší nové aplikace Azure IoT Central:

![Levá navigační nabídka](media/quick-deploy-iot-central/navbar.png)

Pokud chcete zobrazit zařízení a šablony zařízení ve vaší aplikaci, v levé navigační nabídce zvolte **Device Explorer**. Ukázková aplikace zahrnuje šablonu zařízení **Refrigerated Vending Machine** (Chladící prodejní automat). Z této šablony zařízení jsou už vygenerovaná tři simulovaná zařízení:

![Device Explorer](media/quick-deploy-iot-central/deviceexplorer.png)

## <a name="view-the-device-template-and-devices"></a>Zobrazení zařízení a šablon zařízení

Pomocí následujících kroků zobrazíte chladící zařízení vytvořené ze šablony **Refrigerated Vending Machine**. Šablona zařízení definuje:

* _Měření_ odesílaná ze zařízení, jako jsou teplotní telemetrická data.
* _Nastavení_, která umožňují ovládat zařízení, jako je rychlost ventilátoru.
* _Vlastnosti_, které ukládají informace o zařízení, jako je sériové číslo.
* [Pravidla](howto-create-telemetry-rules.md), která umožňují automatizovat akce na základě chování zařízení.
* Přizpůsobitelný _řídicí panel_, který zobrazuje informace o zařízení.

Na základě šablony zařízení můžete vytvářet simulovaná i skutečná zařízení.

### <a name="measurements"></a>Měření

Zobrazí se stránka **Měření** pro zařízení **Refrigerator 1**. Uvidíte seznam měření odeslaných ze simulovaného zařízení. Na této stránce se zobrazuje také přizpůsobitelný graf viditelných měření:

![Stránka měření](media/quick-deploy-iot-central/measurements.png)

Můžete přepínat viditelnost jednotlivých prvků a přizpůsobit graf. Aktuální graf zobrazuje telemetrická data ze simulovaného zařízení. Pokud máte příslušná oprávnění, můžete do šablony zařízení přidat nová měření.

> [!NOTE]
> Pravděpodobně budete muset chvíli počkat, než se simulovaná data zobrazí v grafu.

### <a name="settings"></a>Nastavení

Zvolte **Settings** (Nastavení). Na stránce **Settings** (Nastavení) můžete ovládat zařízení. Můžete například upravit rychlost ventilátoru chladícího zařízení:

![Nastavení](media/quick-deploy-iot-central/settings.png)

Jakmile zařízení rozpozná změnu nastavení, nastavení se zobrazí jako **synced** (Synchronizováno).

### <a name="properties"></a>Vlastnosti

Zvolte **Properties** (Vlastnosti). Na stránce **Properties** (Vlastnosti) můžete:

* Spravovat informace o vašem zařízení, jako je jméno zákazníka
* Zobrazovat hodnoty vlastností hlášené zařízením, jako je upozornění na pohyb

![Vlastnosti](media/quick-deploy-iot-central/properties.png)

### <a name="dashboard"></a>Řídicí panel

Zvolte **Dashboard** (Řídicí panel). Řídicí panel je přizpůsobitelné zobrazení informací o vašem zařízení, jako jsou měření, vlastnosti a klíčové ukazatele výkonu:

![Řídicí panel](media/quick-deploy-iot-central/dashboard.png)

## <a name="view-analytics"></a>Zobrazení analýzy

Předchozí část vám ukázala, jak zobrazit informace o jednotlivých zařízeních. K zobrazení konsolidovaných informací z několika zařízení můžete využít [sady zařízení](howto-use-device-sets.md) a [analýzy](howto-create-analytics.md).

Sady zařízení používají dotazy k dynamickému výběru zařízení, která odpovídají kritériím. Například sada zařízení **Machines in Seattle** vybírá chladící zařízení umístěná v Seattlu. Pokud chcete zobrazit skupinu zařízení **Machines in Seattle**, v levé navigační nabídce zvolte **Device Sets** (Sady zařízení) a potom zvolte **Machines in Seattle**:

![Sada zařízení Machines in Seattle](media/quick-deploy-iot-central/deviceset.png)

Analytická data pro zařízení ve skupině zařízení můžete zobrazit na stránce **Analytics** (Analýza):

![Analýza pro zařízení v Seattlu](media/quick-deploy-iot-central/analytics.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste vytvořili předem naplněnou aplikaci Azure IoT Central, která obsahuje šablonu zařízení **Refrigerated Vending Machine** a simulovaná zařízení. Pokud se jako tvůrce chcete dozvědět víc o definování vlastních šablon zařízení, projděte si téma věnované [definování nové šablony zařízení ve vaší aplikaci](tutorial-define-device-type.md).
