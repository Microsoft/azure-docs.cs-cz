---
title: 'Kurz: Vytvoření aplikace pro analýzu inteligentních dat pomocí IoT Central'
description: 'Kurz: Naučte se vytvářet aplikace pro monitorování inteligentních měřičů pomocí šablon aplikací Azure IoT Central.'
author: op-ravi
ms.author: omravi
ms.date: 12/11/2020
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: d9b5af45ab9a1003cc25e8b1ea2059b83bc715c4
ms.sourcegitcommit: d1b0cf715a34dd9d89d3b72bb71815d5202d5b3a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2021
ms.locfileid: "99833299"
---
# <a name="tutorial-create-and-walk-through-the-smart-meter-monitoring-app-template"></a>Kurz: vytvoření a procházení šablony aplikace pro monitorování inteligentního měření 

Tento kurz vás provede procesem vytvoření aplikace pro monitorování inteligentního měření, která zahrnuje ukázkový model zařízení se simulovanými daty. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření aplikace inteligentního měření zdarma
> * Procházení aplikací – přes
> * Vyčištění prostředků


Pokud předplatné nemáte, [Vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Požadavky
- Žádné
- Doporučuje se předplatné Azure, ale není to nutné.

## <a name="create-a-smart-meter-monitoring-app"></a>Vytvoření aplikace pro inteligentní monitorování měřičů 

Tuto aplikaci můžete vytvořit ve třech jednoduchých krocích:

1. Otevřete [Azure IoT Central domovskou stránku](https://apps.azureiotcentral.com) a kliknutím na **sestavit** vytvořte novou aplikaci. 
1. Vyberte kartu **energie** a klikněte na **vytvořit aplikaci** na dlaždici aplikace **monitorování inteligentního měření** .

    > [!div class="mx-imgBorder"]
    > ![Sestavit aplikaci](media/tutorial-iot-central-smart-meter/smart-meter-build.png)
    

1. Při **Vytvoření aplikace** se otevře formulář **nové aplikace** . Vyplňte požadované podrobnosti, jak je znázorněno na následujícím obrázku:
    * **Název aplikace**: vyberte název aplikace IoT Central. 
    * **Adresa URL**: vyberte adresu URL IoT Central, platforma ověří její jedinečnost.
    * **7. den bezplatné zkušební verze**: Pokud už máte předplatné Azure, doporučuje se výchozí nastavení. Pokud nemáte předplatné Azure, začněte s bezplatnou zkušební verzí.
    * **Informace o fakturaci**: aplikace je zadarmo. K zřizování prostředků vaší aplikace se vyžaduje adresář, předplatné Azure a podrobnosti o oblasti.
    * V dolní části stránky klikněte na tlačítko **vytvořit** a vaše aplikace se vytvoří za minutu.

        ![Formulář nové aplikace](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app.png)

        ![Informace o fakturaci formuláře nové aplikace](media/tutorial-iot-central-smart-meter/smart-meter-create-new-app-billinginfo.png)

### <a name="verify-the-application-and-simulated-data"></a>Ověření aplikace a simulovaných dat

Nově vytvořená aplikace inteligentního měření je vaše aplikace a můžete ji kdykoli upravit. Pojďme se ujistit, že je aplikace nasazená a funguje podle očekávání, než ji upravíte.

Pokud chcete ověřit simulaci vytváření a dat aplikace, navštivte **řídicí panel**. Pokud vidíte dlaždice s některými daty, bylo nasazení aplikace úspěšné. Simulace dat může trvat několik minut, než se data vygenerují, takže dejte 1-2 minut. 

## <a name="application-walk-through"></a>Procházení aplikací – přes
Po úspěšném nasazení šablony aplikace se zobrazí ukázka zařízení inteligentního měřiče, modelu zařízení a řídicího panelu. 

Adatum je fiktivní energetická společnost, která monitoruje a spravuje inteligentní měřiče. Na řídicím panelu monitorování inteligentního měření vidíte vlastnosti inteligentního měřiče, data a ukázkové příkazy. Umožňuje operátorům a pracovníkům podpory proaktivně provádět následující aktivity před tím, než se povede na incidenty podpory: 
* Přečtěte si nejnovější informace o měřiči a umístění nainstalované na mapě.
* Proaktivně kontrolovat síť měřičů a stav připojení 
* Monitorování minimální a maximální hodnoty napětí pro stav sítě 
* Projděte si trendy energie, napájení a napětí a zachyťte všechny vzory neobvyklé. 
* Sledování celkové spotřeby energie pro účely plánování a fakturace
* Operace s příkazy a ovládacími prvky, jako je třeba měřič připojení a verze firmwaru aktualizace. V šabloně se na příkazových tlačítkách zobrazují možné funkce a neodesílají skutečné příkazy. 

> [!div class="mx-imgBorder"]
> ![Řídicí panel monitorování inteligentního měření](media/tutorial-iot-central-smart-meter/smart-meter-dashboard.png)

### <a name="devices"></a>Zařízení
Aplikace přichází s ukázkovým zařízením inteligentního měření. Podrobnosti o zařízení uvidíte kliknutím na kartu **zařízení** .

> [!div class="mx-imgBorder"]
> ![Zařízení inteligentního měření](media/tutorial-iot-central-smart-meter/smart-meter-devices.png)

Kliknutím na odkaz ukázkový **SM0123456789** zařízení zobrazíte podrobnosti o zařízení. Můžete aktualizovat vlastnosti zařízení s možností zápisu na stránce **aktualizace vlastností** a vizualizovat aktualizované hodnoty na řídicím panelu.

> [!div class="mx-imgBorder"]
> ![Vlastnosti inteligentního měřiče](media/tutorial-iot-central-smart-meter/smart-meter-device-properties.png)

### <a name="device-template"></a>Šablona zařízení
Pokud chcete zobrazit model zařízení inteligentního měření, klikněte na kartu **šablony zařízení** . Model má předem definované rozhraní pro data, vlastnosti, příkazy a zobrazení.

> [!div class="mx-imgBorder"]
> ![Šablony zařízení inteligentního měření](media/tutorial-iot-central-smart-meter/smart-meter-device-template.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud se rozhodnete, že tuto aplikaci nebudete používat, odstraňte aplikaci pomocí následujících kroků:

1. V levém podokně otevřete kartu Správa.
1. Vyberte nastavení aplikace a klikněte na tlačítko Odstranit v dolní části stránky. 

    > [!div class="mx-imgBorder"]
    > ![Odstranit aplikaci](media/tutorial-iot-central-smart-meter/smart-meter-delete-app.png)

## <a name="next-steps"></a>Další kroky

Další informace o architektuře aplikací inteligentního měření najdete tady:

> [!div class="nextstepaction"]
> [Architektura aplikace inteligentního měření](./concept-iot-central-smart-meter-app.md)
