---
title: Kurz pro šablonu aplikace centra pro mikroplnění | Dokumenty společnosti Microsoft
description: Výukový program o šabloně aplikace centra pro mikroplnění pro Azure IoT Central
author: avneet723
ms.author: avneets
ms.service: iot-central
ms.subservice: iot-central-retail
ms.topic: overview
ms.date: 01/09/2020
ms.openlocfilehash: 93906f582f1edc351088f8b4c453bf9ebda54f83
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "77369802"
---
# <a name="tutorial-deploy-and-walk-through-a-micro-fulfillment-center-application-template"></a>Kurz: Nasazení a procházení šablony aplikace centra pro mikroplnění

V tomto kurzu použijete šablonu aplikace Centra pro mikroplnění Azure IoT Central k vytvoření maloobchodního řešení. Dozvíte se, jak nasadit šablonu, co je v ní zahrnuto a co budete chtít udělat dál.

## <a name="prerequisites"></a>Požadavky
K dokončení této série kurzů potřebujete předplatné Azure. Volitelně můžete použít bezplatnou 7denní zkušební verzi. Pokud nemáte předplatné Azure, můžete ho vytvořit na [stránce registrace Azure](https://aka.ms/createazuresubscription).

## <a name="create-an-application"></a>Vytvoření aplikace 
V této části vytvoříte novou aplikaci Azure IoT Central ze šablony. Tuto aplikaci použijete v celé řadě kurzů k vytvoření kompletního řešení.

Vytvoření nové aplikace Azure IoT Central:

1. Přejděte na web [správce aplikací Azure IoT Central.](https://aka.ms/iotcentral)
1. Pokud máte předplatné Azure, přihlaste se pomocí přihlašovacích údajů, které používáte k přístupu. V opačném případě se přihlaste pomocí účtu Microsoft:

   ![Snímek obrazovky s přihlašovacím oknem účtu Microsoft](./media/tutorial-in-store-analytics-create-app-pnp/sign-in.png)

1. Pokud chcete začít vytvářet novou aplikaci Azure IoT Central, vyberte **New Application** (Nová aplikace).

1. Vyberte **možnost Maloobchodní**.  Maloobchodní stránka zobrazuje několik šablon maloobchodních aplikací.

Vytvoření nové aplikace centra pro mikroplnění, která používá funkce náhledu:  
1. Vyberte šablonu aplikace **Centra pro mikroplnění.** Tato šablona obsahuje šablony zařízení pro všechna zařízení použitá v kurzu. Šablona také poskytuje řídicí panel operátora pro monitorování podmínek v rámci vašeho centra plnění, stejně jako podmínky pro vaše robotické nosiče. 

    ![Snímek obrazovky s Azure IoT Central Sestavte si stránku aplikace IoT](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-homepage-mfc.png)
    
1. Volitelně můžete zvolit popisný **název aplikace**. Šablona aplikace je založena na fiktivní společnosti Northwind Traders. 

    >[!NOTE]
    >Pokud používáte popisný název aplikace, je stále nutné použít jedinečnou hodnotu adresy URL aplikace.

1. Pokud máte předplatné Azure, zadejte svůj adresář, předplatné Azure a oblast. Pokud nemáte předplatné, můžete povolit 7denní bezplatnou zkušební verzi a vyplnit požadované kontaktní informace.  

    Další informace o adresářích a předplatných najdete v [tématu Vytvoření aplikace](../preview/quick-deploy-iot-central.md) rychlý start.

1. Vyberte **Vytvořit**.

    ![Snímek obrazovky se stránkou aplikace Azure IoT Central New](./media/tutorial-micro-fulfillment-center-app-pnp/iotc-retail-create-app-mfc.png)

## <a name="walk-through-the-application"></a>Projděte si aplikaci 

Po úspěšném nasazení šablony aplikace se zobrazí **řídicí panel centra mikroplnění Northwind Traders**. Northwind Traders je fiktivní prodejce, který má centrum mikroplnění spravované v této aplikaci Azure IoT Central. Na tomto řídicím panelu operátora se zobrazí informace a telemetrie o zařízeních v této šabloně spolu se sadou příkazů, úloh a akcí, které můžete provést. Řídicí panel je logicky rozdělen do dvou částí. Na levé straně můžete sledovat podmínky prostředí v rámci struktury plnění a vpravo můžete sledovat stav robotického nosiče v zařízení.  

Z palubní desky můžete:
   * Viz telemetrie zařízení, jako je počet vyskladnění, počet zpracovaných objednávek a vlastnosti, jako je například stav systému struktury.  
   * Prohlédněte si půdorys a umístění robotických nosičů v rámci struktury plnění.
   * Aktivační události, jako je například resetování řídicího systému, aktualizace firmwaru operátora a změna konfigurace sítě.

     ![Snímek obrazovky s řídicím panelem centra mikroplnění Northwind Traders](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard1.png)
   * Podívejte se na příklad řídicího panelu, který může operátor použít ke sledování podmínek v centru plnění. 
   * Sledujte stav datových částí, které jsou spuštěny na zařízení brány v centru plnění.    

     ![Snímek obrazovky s řídicím panelem centra mikroplnění Northwind Traders](./media/tutorial-micro-fulfillment-center-app-pnp/mfc-dashboard2.png)

## <a name="device-template"></a>Šablona zařízení
Pokud vyberete kartu Šablony zařízení, uvidíte, že existují dva různé typy zařízení, které jsou součástí šablony: 
   * **Robotický nosič**: Tato šablona zařízení představuje definici funkčního robotického nosiče, který byl nasazen ve struktuře plnění a provádí příslušné operace skladování a načítání. Pokud vyberete šablonu, uvidíte, že robot odesílá data zařízení, jako je teplota a poloha osy, a vlastnosti, jako je stav robotického nosiče. 
   * **Sledování stavu struktury:** Tato šablona zařízení představuje kolekci zařízení, která umožňuje sledovat stav prostředí, stejně jako zařízení brány, které hostuje různé hraniční úlohy pro napájení centra plnění. Zařízení odesílá telemetrická data, jako je například teplota, počet vyskladnění a počet objednávek. Také odesílá informace o stavu a stavu výpočetních úloh spuštěných ve vašem prostředí. 

     ![Šablony zařízení Centra pro mikroplnění](./media/tutorial-micro-fulfillment-center-app-pnp/device-templates.png)

Pokud vyberete kartu Skupiny zařízení, uvidíte také, že pro tyto šablony zařízení jsou automaticky vytvořeny skupiny zařízení.

## <a name="rules"></a>Pravidla
Na kartě **Pravidla** se v šabloně aplikace zobrazí ukázkové pravidlo pro sledování teplotních podmínek robotického nosiče. Toto pravidlo můžete použít k upozornění obsluhy, pokud se konkrétní robot v zařízení přehřívá a je třeba jej převést do režimu offline pro servis. 

Ukázkové pravidlo použijte jako inspiraci k definování pravidel, která jsou vhodnější pro vaše obchodní funkce.

![Snímek obrazovky s kartou Pravidla](./media/tutorial-micro-fulfillment-center-app-pnp/rules.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud nebudete pokračovat v používání této aplikace, odstraňte šablonu aplikace. Přejděte do**nastavení aplikace** **pro správu** > a vyberte **odstranit**.

![Snímek obrazovky se stránkou Nastavení aplikace Centra pro mikroplnění](./media/tutorial-micro-fulfillment-center-app-pnp/delete.png)

## <a name="next-steps"></a>Další kroky
* Přečtěte si další informace o [architektuře řešení centra pro mikroplnění](./architecture-micro-fulfillment-center-pnp.md).
* Přečtěte si další informace o dalších [maloobchodních šablonách Azure IoT Central](./overview-iot-central-retail-pnp.md).
* Přečtěte si [přehled Azure IoT Central](../preview/overview-iot-central.md).
