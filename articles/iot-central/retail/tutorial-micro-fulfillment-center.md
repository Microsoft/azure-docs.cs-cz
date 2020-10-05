---
title: Výukový kurz šablony aplikace pro Micro-vyplňování | Microsoft Docs
description: Kurz týkající se šablony aplikace centra vyplňování pro Azure IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: tutorial
ms.date: 01/09/2020
ms.openlocfilehash: 28afa98313ef1c9577e90ed161a1d20cbf5faf75
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "91440199"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Kurz: nasazení a procházení šablonou aplikace centra pro pořizování

V tomto kurzu použijete šablonu aplikace Azure IoT Central Micro-splní centrum k sestavení maloobchodního řešení. Naučíte se, jak nasadit šablonu, co je součástí IT a co byste chtěli udělat dál.

## <a name="prerequisites"></a>Předpoklady
K dokončení této série kurzů potřebujete předplatné Azure. Volitelně můžete použít bezplatnou 7 dní zkušební verzi. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace do Azure](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Vytvoření aplikace 
V této části vytvoříte novou aplikaci Azure IoT Central ze šablony. Tuto aplikaci použijete v celé sérii kurzů k sestavení kompletního řešení.

Vytvoření nové aplikace Azure IoT Central:

1. Přejít na web [Azure IoT Central Správce aplikací](https://aka.ms/iotcentral) .
1. Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte pro přístup k němu. V opačném případě se přihlaste pomocí účet Microsoft:

   ![Snímek obrazovky s dialogovým oknem pro přihlášení účet Microsoft](./media/tutorial-in-store-analytics-create-app/sign-in.png)

1. Pokud chcete začít vytvářet novou aplikaci Azure IoT Central, vyberte **New Application** (Nová aplikace).

1. Vyberte **Retail (maloobchod**).  Na stránce prodej se zobrazí několik šablon maloobchodních aplikací.

Vytvoření nové aplikace centra pro pořizování softwaru, která používá funkce verze Preview:  
1. Vyberte šablonu aplikace **centra pro pořizování** . Tato šablona obsahuje šablony zařízení pro všechna zařízení použitá v tomto kurzu. Šablona také poskytuje řídicí panel operátora pro podmínky monitorování v rámci vašeho centra splnění a podmínky pro vaše robotické dopravce. 

    ![Snímek obrazovky Azure IoT Central sestavení stránky vaší aplikace IoT](./media/tutorial-micro-fulfillment-center-app/iotc-retail-homepage-mfc.png)
    
1. Volitelně můžete zvolit popisný **název aplikace**. Šablona aplikace je založena na fiktivní společnosti Northwind Traders. 

    >[!NOTE]
    >Použijete-li popisný název aplikace, je stále nutné použít jedinečnou hodnotu pro adresu URL aplikace.

1. Pokud máte předplatné Azure, zadejte svůj adresář, předplatné Azure a oblast. Pokud předplatné nemáte, můžete povolit 7 dní bezplatnou zkušební verzi a doplnit požadované kontaktní údaje.  

    Další informace o adresářích a předplatných najdete v tématu rychlý Start [k vytvoření aplikace](../preview/quick-deploy-iot-central.md) .

1. Vyberte **Vytvořit**.

    ![Snímek obrazovky se stránkou nové aplikace Azure IoT Central](./media/tutorial-micro-fulfillment-center-app/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Procházení aplikací 

Po úspěšném nasazení šablony aplikace se zobrazí **řídicí panel Northwind Traders Micro-splní centrum**. Northwind Traders je fiktivní prodejce, který má v této aplikaci IoT Central Azure spravované centrum pro mikroplnění. Na tomto řídicím panelu operátora uvidíte informace a telemetrii o zařízeních v této šabloně spolu se sadou příkazů, úloh a akcí, které můžete provést. Řídicí panel je logicky rozdělen do dvou částí. Na levé straně můžete monitorovat podmínky prostředí v rámci struktury plnění a na pravé straně můžete monitorovat stav robota v rámci daného zařízení.  

Z řídicího panelu můžete:
   * Podívejte se na telemetrii zařízení, jako je počet vyskladnění, počet zpracovaných objednávek a vlastnosti, jako je například stav systému struktury.  
   * Zobrazení plánu a umístění automatických dopravců v rámci struktury plnění.
   * Příkazy triggeru, jako je resetování systému řízení, aktualizace firmwaru dopravce a změna konfigurace sítě.

     ![Snímek obrazovky s horní polovinou řídicího panelu pro Northwind Traders Micro Center](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard1.png)
   * Podívejte se na příklad řídicího panelu, který může operátor použít k monitorování podmínek v rámci centra plnění. 
   * Monitorujte stav datových částí, které jsou spuštěny na zařízení brány v rámci centra plnění.    

     ![Snímek obrazovky Botton poloviny řídicího panelu pro Northwind Traders Micro.](./media/tutorial-micro-fulfillment-center-app/mfc-dashboard2.png)

## <a name="device-template"></a>Šablona zařízení
Pokud vyberete kartu šablony zařízení, uvidíte, že existují dva různé typy zařízení, které jsou součástí šablony: 
   * **Robotský dopravce**: Tato šablona zařízení představuje definici fungujícího autodopravce, který je nasazený ve struktuře plnění, a provádí příslušné operace úložiště a načítání. Pokud vyberete šablonu, uvidíte, že robot posílá data zařízení, jako je například teplota a poloha osy, a vlastnosti, jako je například stav autodopravce. 
   * **Monitorování podmínky struktury**: Tato šablona zařízení představuje kolekci zařízení, která umožňuje monitorovat stav prostředí, a také zařízení brány, které hostuje různé hraniční úlohy pro zajištění výkonu vašeho centra pro splnění. Zařízení odesílá data telemetrie, například teplotu, počet vyskladnění a počet objednávek. Odesílá taky informace o stavu a stavu výpočetních úloh běžících ve vašem prostředí. 

     ![Šablony zařízení centra pro vyplňování](./media/tutorial-micro-fulfillment-center-app/device-templates.png)

Pokud vyberete kartu skupiny zařízení, zjistíte také, že tyto šablony zařízení mají pro ně automaticky vytvořené skupiny zařízení.

## <a name="rules"></a>Pravidla
Na kartě **pravidla** se zobrazí ukázkové pravidlo, které existuje v šabloně aplikace pro monitorování podmínek teploty pro robota. Toto pravidlo můžete použít k upozornění operátoru, pokud je konkrétní robot v zařízení přehřátý, a je potřeba ho převést do režimu offline kvůli údržbě. 

Pomocí ukázkového pravidla jako inspiraci definujte pravidla, která jsou vhodnější pro vaše obchodní funkce.

![Snímek obrazovky s kartou Rules](./media/tutorial-micro-fulfillment-center-app/rules.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete tuto aplikaci nadále používat, odstraňte šablonu aplikace. Přejít na **Administration**  >  **nastavení aplikace**pro správu a vyberte **Odstranit**.

![Snímek obrazovky se stránkou nastavení aplikace centra pro doplňování](./media/tutorial-micro-fulfillment-center-app/delete.png)

## <a name="next-steps"></a>Další kroky

Další informace:
> [!div class="nextstepaction"]
> [Architektura řešení pro Micro-vyplňování softwaru](./architecture-micro-fulfillment-center.md)
* Další informace o [šablonách maloobchodního prodeje v Azure IoT Central](./overview-iot-central-retail.md)
* Další informace o dalších [IoT Central přehled Azure](../preview/overview-iot-central.md)
