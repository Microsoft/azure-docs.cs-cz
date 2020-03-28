---
title: 'Kurz: Vytvoření aplikace pro analýzu inteligentních měřičů pomocí IoT Central'
description: 'Kurz: Naučte se, jak vytvořit aplikaci pro monitorování inteligentních měřičů pomocí šablon aplikací Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 11/12/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: 9e954e9c1a7efa43a19849b1c5b40284ec84eeed
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "77015996"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Kurz: Vytvoření a procházení šablony aplikace pro sledování inteligentního měřiče 



Tento kurz vás provede procesem vytváření aplikace pro sledování inteligentních měřičů, která obsahuje ukázkový model zařízení se simulovanými daty. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořte aplikaci Smart Meter zdarma
> * Ochození aplikace
> * Vyčištění prostředků


Pokud nemáte předplatné, [vytvořte si bezplatný zkušební účet.](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Požadavky
- Žádný
- Předplatné Azure se doporučuje, ale není nutné vyzkoušet

## <a name="create-a-smart-meter-monitoring-app"></a>Vytvoření aplikace pro inteligentní monitorování měřičů 

Tuto aplikaci můžete vytvořit ve třech jednoduchých krocích:

1. Otevřete [domovskou stránku Azure IoT Central](https://apps.azureiotcentral.com) a kliknutím na **Build** vytvořte novou aplikaci. 

2. Vyberte **karta Energie** a v části Dlaždice aplikace **Pro sledování inteligentního měřiče** klikněte na **Vytvořit aplikaci.**

    > [!div class="mx-imgBorder"]
    > ![Vytvořit aplikaci](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

3. **Vytvořit aplikaci** otevře nový formulář **žádosti.** Vyplňte požadované údaje, jak je znázorněno na obrázku níže:
    * **Název aplikace**: Vyberte název aplikace IoT Central. 
    * **URL**: Vyberte adresu URL IoT Central, platforma ověří svou jedinečnost.
    * **7denní bezplatná zkušební verze**: Pokud už máte předplatné Azure, doporučujeme výchozí nastavení. Pokud nemáte předplatné Azure, začněte s bezplatnou zkušební verzí.
    * **Fakturační informace**: Samotná aplikace je zdarma. Podrobnosti o adresáři, předplatnéazure a oblasti jsou nutné k zajištění prostředků pro vaši aplikaci.
    * V dolní části stránky klikněte na **tlačítko Vytvořit** a aplikace se vytvoří za minutu.

        ![Nový formulář žádosti](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![Informace o fakturaci nového formuláře žádosti](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)


### <a name="verify-the-application-and-simulated-data"></a>Ověření aplikace a simulovaných dat

Nově vytvořená aplikace inteligentního měřiče je vaše aplikace a můžete ji kdykoli upravit. Před úpravou zajistíme, že je aplikace nasazená a funguje podle očekávání.

Chcete-li ověřit vytváření aplikací a simulaci dat, přejděte na **řídicí panel**. Pokud vidíte dlaždice s některými daty, nasazení aplikace bylo úspěšné. Simulace dat může trvat několik minut, než se data vygenerují, takže jí dejte 1-2 minuty. 

## <a name="application-walk-through"></a>Ochození aplikace
Po úspěšném nasazení šablony aplikace je dodávána s ukázkovým zařízením inteligentního měřiče, modelem zařízení a řídicím panelem. 

Adatum je fiktivní energetická společnost, která monitoruje a spravuje inteligentní měřiče. Na řídicím panelu pro monitorování inteligentních měřičů se zobrazují vlastnosti inteligentních měřičů, data a ukázkové příkazy. Umožňuje operátorům a podpůrným týmům proaktivně provádět následující činnosti, než se změní na incidenty podpory: 
* Prohlédněte si nejnovější informace o měřiči a jeho nainstalované umístění na mapě
* Proaktivně kontrolujte síť měřičů a stav připojení 
* Monitor Min a Max hodnoty napětí pro stav sítě 
* Zkontrolujte trendy v oblasti energie, energie a napětí, abyste zachytili všechny neobvyklé vzorce 
* Sledování celkové spotřeby energie pro účely plánování a fakturace
* Příkazové a řídicí operace, jako je opětovné připojení měřiče a aktualizace verze firmwaru. V šabloně příkazová tlačítka zobrazují možné funkce a neposílají skutečné příkazy. 

> [!div class="mx-imgBorder"]
> ![Řídicí panel pro monitorování inteligentních měřičů](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Zařízení
Aplikace je dodávána se vzorkem inteligentního měřicího zařízení. Podrobnosti o zařízení můžete zobrazit kliknutím na kartu **Zařízení.**

> [!div class="mx-imgBorder"]
> ![Inteligentní měřicí přístroje](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Kliknutím na ukázkový odkaz na zařízení **SM0123456789** zobrazíte podrobnosti o zařízení. Můžete aktualizovat zapisovatelné vlastnosti zařízení na stránce **Aktualizovat vlastnosti** a vizualizovat aktualizované hodnoty na řídicím panelu.

> [!div class="mx-imgBorder"]
> ![Inteligentní vlastnosti měřiče](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Šablona zařízení
Kliknutím na kartu **Šablony zařízení** zobrazíte model zařízení inteligentního měřiče. Model má předem definovat rozhraní pro data, vlastnost, příkazy a zobrazení.

> [!div class="mx-imgBorder"]
> ![Šablony zařízení inteligentního měřiče](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud se rozhodnete tuto aplikaci nadále používat, odstraňte aplikaci pomocí následujících kroků:

1. V levém podokně otevřete kartu Správa.
2. Vyberte Nastavení aplikace a v dolní části stránky klikněte na Tlačítko Odstranit. 

    > [!div class="mx-imgBorder"]
    > ![Odstranit aplikaci](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)


## <a name="next-steps"></a>Další kroky
* Další informace o architektuře aplikací inteligentních měřičů naleznete [v koncepčním článku](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-smart-meter-app)
* Vytvářejte šablony aplikací inteligentních měřičů zdarma: [aplikace smart meter](https://apps.azureiotcentral.com/build/new/smart-meter-monitoring)
* Další informace o IoT Central, viz [Přehled IoT Central](https://docs.microsoft.com/azure/iot-central/)
