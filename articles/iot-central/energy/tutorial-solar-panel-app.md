---
title: 'Kurz: Vytvoření aplikace pro monitorování v panelu slunečního navýšení pomocí Azure IoT Central'
description: 'Kurz: Naučte se vytvářet aplikace v programu slunečního panelu pomocí šablon aplikací IoT Central Azure.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 509e31919dd974da253cd0478a70f889cc060fae
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99831786"
---
# <a name="tutorial-create-and-explore-the-solar-panel-monitoring-app-template"></a>Kurz: vytvoření a prozkoumání šablony aplikace pro monitorování v panelu slunečního příjímat 

V tomto kurzu Vás provedeme procesem vytvoření aplikace pro monitorování v panelu slunečního prostředí, která zahrnuje Vzorový model zařízení se simulovanými daty. V tomto kurzu se naučíte:


> [!div class="checklist"]
> * Vytvoření aplikace v panelu slunečního příplatku zdarma
> * Procházení aplikací
> * Vyčištění prostředků


Pokud předplatné nemáte, [vytvořte bezplatný zkušební účet](https://azure.microsoft.com/free).

## <a name="prerequisites"></a>Požadavky

Pro dokončení tohoto kurzu nejsou k dispozici žádné požadavky. Doporučuje se předplatné Azure, ale nevyžaduje se to.


## <a name="create-a-solar-panel-monitoring-app"></a>Vytvoření aplikace pro monitorování solárních panelů 

Tuto aplikaci můžete vytvořit ve třech jednoduchých krocích:

1. Přejít na [Azure IoT Central](https://apps.azureiotcentral.com). Chcete-li vytvořit novou aplikaci, vyberte možnost **sestavit**. 

1. Vyberte kartu **energie** . V části **sledování panelu slunečního** výběru vyberte **vytvořit aplikaci**. 

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky s možnostmi sestavení Azure IoT Central.](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
1. V dialogovém okně **Nová aplikace** vyplňte požadované podrobnosti a pak vyberte **vytvořit**:
    * **Název aplikace**: vyberte název aplikace IoT Central pro Azure. 
    * **Adresa URL**: vyberte adresu URL pro Azure IoT Central. Platforma ověřuje svoji jedinečnost.
    * **Cenový plán**: Pokud už předplatné Azure máte, doporučuje se výchozí nastavení. Pokud nemáte předplatné Azure, začněte s bezplatnou zkušební verzí.
    * **Informace o fakturaci**: aplikace je zadarmo. K zřizování prostředků vaší aplikace se vyžaduje adresář, předplatné Azure a podrobnosti o oblasti.
        ![Snímek obrazovky nové aplikace](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Snímek obrazovky s informacemi o fakturaci](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Ověření aplikace a simulovaných dat

Novou aplikaci v panelu slunečního příběhu můžete kdykoli upravit. Prozatím zajistěte, aby byla aplikace nasazená a fungovala podle očekávání, než ji upravíte.

Pokud chcete ověřit simulaci vytváření a dat aplikace, navštivte **řídicí panel**. Pokud vidíte dlaždice s některými daty, bylo nasazení aplikace úspěšné. Simulace dat může trvat několik minut, než se data vygenerují. 

## <a name="application-walk-through"></a>Procházení aplikací – přes
Po úspěšném nasazení šablony aplikace budete chtít aplikaci prozkoumat trochu. Všimněte si, že se dodává s ukázkovým zařízením inteligentního měření, modelem zařízení a řídicím panelem.

Adatum je fiktivní energetická společnost, která monitoruje a spravuje sluneční panely. Na řídicím panelu monitorování na panelu slunečního zobrazení uvidíte vlastnosti, data a ukázkové příkazy pro sluneční panel. Tento řídicí panel vám nebo vašemu týmu podpory umožňuje proaktivně provádět následující aktivity, aby všechny problémy vyžadovaly další podporu:
* Projděte si nejnovější informace o panelu a jeho umístění nainstalované na mapě.
* Ověřte stav panelu a stav připojení.
* Projděte si trendy v oblasti energetické generace a teplot a zachyťte všechny vzory neobvyklé.
* Sledujte celkovou generaci energie pro účely plánování a fakturace.
* Aktivujte panel a v případě potřeby aktualizujte verzi firmwaru. V šabloně se na příkazových tlačítkách zobrazují možné funkce a neodesílají skutečné příkazy.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky s řídicím panelem šablony monitorování na panelu slunečního vstupu](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Zařízení
Aplikace přichází s ukázkovým zařízením slunečního zařízení. Pokud chcete zobrazit podrobnosti o zařízení, vyberte **zařízení**.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky se šablonou monitorování na panelu slunečního zařízení](media/tutorial-iot-central-solar-panel/solar-panel-device.png)

Vyberte ukázkové zařízení **SP0123456789**. Na kartě **Vlastnosti aktualizace** můžete aktualizovat vlastnosti daného zařízení s možností zápisu a zobrazit vizuál aktualizovaných hodnot na řídicím panelu. 

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky se šablonou monitorování na panelu inflace karta Vlastnosti aktualizace.](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Šablona zařízení
Chcete-li zobrazit model zařízení slunečního panelu, vyberte kartu **šablony zařízení** . Model má předdefinovaná rozhraní pro data, vlastnosti, příkazy a zobrazení.

> [!div class="mx-imgBorder"]
> ![Snímek obrazovky šablony monitorování na panelu slunečního zařízení](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud se rozhodnete, že tuto aplikaci nebudete nadále používat, odstraňte aplikaci pomocí následujících kroků:

1. V levém podokně vyberte **Správa**.
1. Vyberte **nastavení aplikace**  >  **Odstranit**. 

    > [!div class="mx-imgBorder"]
    > ![Snímek obrazovky se správou šablony monitorování panelu slunečního vstupu](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)

## <a name="next-steps"></a>Další kroky
 
> [!div class="nextstepaction"]
> [Architektura aplikace panelu Azure IoT Central-slunečního panelu](./concept-iot-central-solar-panel-app.md)

