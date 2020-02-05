---
title: Výukový kurz šablony aplikace pro Micro-vyplňování | Microsoft Docs
description: Kurz šablony aplikace centra vyplňování pro IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 2ed6f37bc3b00397fa6331a501e1b16c8d622b5f
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027787"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Kurz: nasazení a procházení šablonou aplikace centra pro pořizování

V tomto kurzu budeme používat šablonu aplikace centra Azure IoT Central ***Micro-splníme*** k tomu, jak vytvořit maloobchodní řešení. Naučíte se, jak nasadit šablonu knihovny MFC, co je součástí pole a co byste chtěli udělat dál.

V tomto kurzu se naučíte: 
> [!div class="checklist"]
> * Vytvoření maloobchodní aplikace pomocí šablony Azure IoT Central **Micro-splní centrum**
> * Procházení aplikace 

## <a name="prerequisites"></a>Požadavky
K dokončení této série kurzů potřebujete:
* Předplatné Azure. Volitelně můžete použít bezplatnou 7 dní zkušební verzi. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Vytvoření aplikace 
V této části vytvoříte novou aplikaci Azure IoT Central ze šablony. Tuto aplikaci použijete v celé sérii kurzů k sestavení kompletního řešení.

Vytvoření nové aplikace Azure IoT Central:

1. Přejděte na web [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) .
1. Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte pro přístup k němu, jinak se přihlaste pomocí účet Microsoft:

   ![Zadání účtu organizace](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Pokud chcete začít vytvářet novou aplikaci Azure IoT Central, vyberte **New Application** (Nová aplikace).

1. Vyberte **Retail (maloobchod**).  Na stránce prodej se zobrazí několik šablon maloobchodních aplikací.

Vytvoření nové aplikace centra pro pořizování softwaru, která používá funkce verze Preview:  
1. Vyberte šablonu aplikace **centra pro pořizování** . Tato šablona obsahuje šablony zařízení pro všechna zařízení použitá v tomto kurzu. Šablona také poskytuje řídicí panel operátora pro podmínky monitorování v rámci vašeho centra plnění a podmínky pro vaše robotické dopravce. 

    > [!div class="mx-imgBorder"]
    > Typ aplikace ![Micro-doplňování](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-homepage-mfc.png)
    
1. Volitelně můžete zvolit popisný **název aplikace**.  Šablona aplikace je založena na fiktivní společnosti Northwind Traders. 

    > [!NOTE]
    > Použijete-li popisný **název aplikace**, je stále nutné použít jedinečnou hodnotu pro **adresu URL**aplikace.

1. Pokud máte předplatné Azure, zadejte svůj *adresář, předplatné Azure a oblast*. Pokud předplatné nemáte, můžete povolit **7 dní bezplatnou zkušební verzi** a dokončit požadované kontaktní údaje.  

    Další informace o adresářích a předplatných najdete v [rychlém startu k vytvoření aplikace](../preview/quick-deploy-iot-central.md).

1. Vyberte **Vytvořit**.

> [!div class="mx-imgBorder"]
> ![vytvoření aplikace pro mikroplnění](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Procházení aplikací 

### <a name="dashboard"></a>Řídicí panel 

Po úspěšném nasazení šablony aplikace se nejprve dostanou na **řídicí panel Northwind Traders Micro-splní centrum**. Produkt Northwind obchodník je fiktivní prodejce, který má v této IoT Central aplikaci spravováno centrum pro mikroplnění. Na tomto řídicím panelu operátora se zobrazí informace a telemetrie týkající se zařízení v této šabloně spolu se sadou příkazů, úloh a akcí, které můžete provést. Řídicí panel je logicky rozdělen do dvou částí (vlevo a vpravo). Na levé straně máte možnost monitorovat podmínky prostředí v rámci struktury plnění a napravit stav, ve kterém můžete monitorovat stav robota v rámci zařízení.  

Z řídicího panelu můžete:
   * Podívejte se na telemetrii zařízení, jako je počet vyskladnění, zpracovaných # objednávek a vlastnosti, jako je například stav systému struktury, atd.  
   * Zobrazení **plánu** a umístění automatických dopravců v rámci struktury plnění.
   * Příkazy triggeru, jako je resetování systému řízení, aktualizace firmwaru dopravce, změna konfigurace sítě atd.

> [!div class="mx-imgBorder"]
> ](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard1.png) řídicí panel ![Micro-doplňování
   * Podívejte se na příklad řídicího panelu, který může operátor využít k monitorování podmínek v rámci centra plnění. 
   * Monitoruje stav datových částí spuštěných v zařízení brány v centru pro splnění.    

> [!div class="mx-imgBorder"]
> ](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard2.png) řídicí panel ![Micro-doplňování

## <a name="device-template"></a>Šablona zařízení
Pokud kliknete na kartu šablony zařízení, uvidíte, že existují dva různé typy zařízení, které jsou součástí šablony: 
   * **Robotský dopravce**: Tato šablona zařízení představuje definici fungujícího autodopravce, který je nasazený ve struktuře plnění a provádí příslušné operace úložiště a načítání. Pokud kliknete na šablonu, uvidíte, že robot posílá data zařízení, například teplotu, polohu osy a vlastnosti, jako je například stav autodopravce atd. 
   * **Monitorování podmínky struktury**: Tato šablona zařízení představuje kolekci zařízení, která umožňuje monitorovat stav prostředí a zařízení brány, které hostují různé hraniční úlohy, k napájení vašeho centra pro splnění. Zařízení odesílá data telemetrie, jako je teplota, počet vyskladnění, počet objednávek atd. Kromě toho stav a stav úloh výpočetních úloh spuštěných ve vašem prostředí. 

> [!div class="mx-imgBorder"]
> ![šablony zařízení pro Micro-vyplňování centra](./media/tutorial-micro-fulfillment-center-app-pnp/device-templates.png)

Pokud kliknete na kartu skupiny zařízení, zjistíte také, že tyto šablony zařízení mají pro ně automaticky vytvořené skupiny zařízení.

## <a name="rules"></a>Pravidla
Při přechodu na kartu pravidla se zobrazí ukázkové pravidlo, které existuje v šabloně aplikace pro monitorování podmínek teploty pro robota. Toto pravidlo můžete použít k upozornění operátoru, pokud je konkrétní robot v zařízení přehřátý a je potřeba ho pro obsluhu převést do režimu offline. 

Využijte vzorové pravidlo jako inspiraci a definujte pravidla, která jsou vhodnější pro vaše obchodní funkce.

   - **Neteplý nosný operátor**: Toto pravidlo se aktivuje, pokud robot dostane prahovou hodnotu teploty v časovém intervalu. 

> [!div class="mx-imgBorder"]
> ![pravidla pro Micro-vyplňování centra](./media/tutorial-micro-fulfillment-center-app-pnp/rules.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte šablonu aplikace na stránce **správa** > **nastavení aplikace** a klikněte na **Odstranit**.

> [!div class="mx-imgBorder"]
> ](./media/tutorial-micro-fulfillment-center-app-pnp/delete.png) vyčištění aplikace ![Micro-doplňování softwaru

## <a name="next-steps"></a>Další kroky
* Další informace o [architektuře řešení pro microing Center](./architecture-micro-fulfillment-center-pnp.md)
* Další informace o jiných [šablonách IoT Central maloobchodních prodejů](./overview-iot-central-retail-pnp.md)
* Další informace o IoT Central najdete v tématu [IoT Central Overview](../preview/overview-iot-central.md) .