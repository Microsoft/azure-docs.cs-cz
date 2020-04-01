---
title: 'Kurz: Vytvoření aplikace pro monitorování solárních trestů pomocí IoT Central'
description: 'Kurz: Naučte se, jak vytvořit aplikaci solárního panelu pomocí šablon aplikací Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d5ea3d3420cb598693ccaede7ee10d2f8c4fd839
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77025771"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Kurz: Vytvoření a procházení šablony aplikace pro monitorování solárního panelu 



Tento kurz vás provede procesem vytváření aplikace pro monitorování solárních panelů, která obsahuje ukázkový model zařízení se simulovanými daty. V tomto kurzu se naučíte:


> [!div class="checklist"]
> * Vytvořte aplikaci solárních panelů zdarma
> * Ochození aplikace
> * Vyčištění prostředků


Pokud nemáte předplatné, [vytvořte si bezplatný zkušební účet.](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Požadavky
- Žádný
- Předplatné Azure se doporučuje, ale není nutné vyzkoušet


## <a name="create-a-solar-panel-monitoring-app"></a>Vytvoření aplikace pro monitorování solárních panelů 

Tuto aplikaci můžete vytvořit ve třech jednoduchých krocích:

1. Otevřete [domovskou stránku Azure IoT Central](https://apps.azureiotcentral.com) a kliknutím na **Build** vytvořte novou aplikaci. 

2. Vyberte **karta Energie** a v části Dlaždice **aplikace pro monitorování solárního panelu** klikněte na **Vytvořit aplikaci.** 

    > [!div class="mx-imgBorder"]
    > ![Vytvořit aplikaci](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. **Vytvořit aplikaci** se otevře Nový formulář **žádosti.** Vyplňte požadované údaje, jak je znázorněno na obrázku níže:
    * **Název aplikace**: Vyberte název aplikace IoT Central. 
    * **URL**: Vyberte adresu URL IoT Central, platforma ověří svou jedinečnost.
    * **7denní bezplatná zkušební verze**: Pokud už máte předplatné Azure, doporučujeme výchozí nastavení. Pokud nemáte předplatné Azure, začněte s bezplatnou zkušební verzí.
    * **Fakturační informace**: Samotná aplikace je zdarma. Podrobnosti o adresáři, předplatnéazure a oblasti jsou nutné k zajištění prostředků pro vaši aplikaci.
    * V dolní části stránky klikněte na **tlačítko Vytvořit** a aplikace se vytvoří za minutu.
        ![Nový formulář žádosti](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)
        
        ![Informace o fakturaci nového formuláře žádosti](media/tutorial-iot-central-solar-panel/solar-panel-create-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Ověření aplikace a simulovaných dat

Nově vytvořená aplikace pro solární panely je vaše aplikace a můžete ji kdykoli upravit. Před úpravou zajistíme, že je aplikace nasazená a funguje podle očekávání.

Chcete-li ověřit vytváření aplikací a simulaci dat, přejděte na **řídicí panel**. Pokud vidíte dlaždice s některými daty, nasazení aplikace bylo úspěšné. Simulace dat může trvat několik minut, než se data vygenerují, takže jí dejte 1-2 minuty. 

## <a name="application-walk-through"></a>Ochození aplikace
Po úspěšném nasazení šablony aplikace je dodávána s ukázkovým zařízením inteligentního měřiče, modelem zařízení a řídicím panelem.

Adatum je fiktivní energetická společnost, která monitoruje a spravuje solární panely. Na řídicí misondě pro monitorování solárního panelu se zobrazí vlastnosti solárního panelu, data a ukázkové příkazy. Umožňuje operátorům a podpůrným týmům proaktivně provádět následující činnosti, než se změní na incidenty podpory:
* Prohlédněte si nejnovější informace o panelu a jeho nainstalované umístění na mapě
* Proaktivní kontrola stavu panelu a stavu připojení
* Projděte si trendy výroby energie a teploty, abyste zachytili všechny neobvyklé vzorce
* Sledování celkové výroby energie pro účely plánování a fakturace
* Příkazové a řídicí operace, jako je aktivace panelu a aktualizace verze firmwaru. V šabloně příkazová tlačítka zobrazují možné funkce a neposílají skutečné příkazy.

> [!div class="mx-imgBorder"]
> ![Přístrojová deska pro monitorování solárních panelů](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Zařízení
Aplikace je dodávána se vzorkem solárního panelu. Podrobnosti o zařízení můžete zobrazit kliknutím na kartu **Zařízení.**

> [!div class="mx-imgBorder"]
> ![Solární panelzařízení](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Kliknutím na ukázkový odkaz **SP0123456789** zobrazíte podrobnosti o zařízení. Na stránce **Aktualizovat vlastnosti** můžete aktualizovat zapisovatelné vlastnosti zařízení a vizualizovat aktualizované hodnoty na řídicím panelu. 

> [!div class="mx-imgBorder"]
> ![Vlastnosti solárních panelů](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Šablona zařízení
Kliknutím na kartu **Šablony zařízení** zobrazíte model zařízení na solárních panelech. Model má předem definovat rozhraní pro data, vlastnost, příkazy a zobrazení.

> [!div class="mx-imgBorder"]
> ![Šablona zařízení solárních panelů](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud se rozhodnete tuto aplikaci nadále používat, odstraňte aplikaci pomocí následujících kroků:

1. V levém podokně otevřete kartu Správa.
2. Vyberte Nastavení aplikace a v dolní části stránky klikněte na Tlačítko Odstranit. 

    > [!div class="mx-imgBorder"]
    > ![Odstranit aplikaci](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png)


## <a name="next-steps"></a>Další kroky
* Další informace o architektuře aplikací pro solární panely naleznete [v článku o konceptu](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Vytvořte šablony aplikací solárních panelů zdarma: [aplikace pro solární panely](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring)
* Další informace o IoT Central, viz [Přehled IoT Central](https://docs.microsoft.com/azure/iot-central/)

