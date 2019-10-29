---
title: Vytvoření antitrestné aplikace pro monitorování pomocí IoT Central | Microsoft Docs
description: Naučte se, jak vytvořit aplikaci v programu slunečního panelu pomocí šablon aplikací Azure IoT Central.
author: op-ravi
ms.author: omravi
ms.date: 10/23/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
manager: abjork
ms.openlocfilehash: b408d2cc0754cc1f2dd8671d037d4f9d348e883b
ms.sourcegitcommit: d47a30e54c5c9e65255f7ef3f7194a07931c27df
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2019
ms.locfileid: "73027635"
---
# <a name="tutorial-create-and-walk-through-the-solar-panel-monitoring-app-template"></a>Kurz: vytvoření a Projděte si šablonu aplikace pro monitorování panelu slunečního příběhu 

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

V tomto kurzu Vás provedeme procesem vytvoření aplikace pro monitorování na panelu slunečního prostředí, která zahrnuje Vzorový model zařízení se simulovanými daty. V tomto kurzu se naučíte:


> [!div class="checklist"]
> * Vytvoření aplikace v panelu slunečního příplatku zdarma
> * Procházení aplikací – přes
> * Vyčištění prostředků


Pokud předplatné nemáte, [Vytvořte si bezplatný zkušební účet](https://azure.microsoft.com/free)

## <a name="prerequisites"></a>Předpoklady
- Žádné
- Doporučuje se předplatné Azure, ale není to nutné.


## <a name="create-a-solar-panel-monitoring-app"></a>Vytvoření aplikace pro monitorování slunečního panelu 

Tuto aplikaci můžete vytvořit ve třech jednoduchých krocích:

1. Otevřete [Azure IoT Central domovskou stránku](https://apps.azureiotcentral.com) a kliknutím na **sestavit** vytvořte novou aplikaci. 

2. Vyberte kartu **energie** a klikněte na **vytvořit aplikaci** na dlaždici monitorování aplikace na **panelu inflace** . 

    [!div class="mx-imgBorder"]
    ![Build App](media/tutorial-iot-central-solar-panel/solar-panel-build.png)
  
3. Při **Vytvoření aplikace** se otevře formulář **nové aplikace** . Vyplňte požadované podrobnosti, jak je znázorněno na následujícím obrázku:
    * **Název aplikace**: vyberte název aplikace IoT Central. 
    * **Adresa URL**: vyberte adresu URL IoT Central, platforma ověří její jedinečnost.
    * **7. den bezplatné zkušební verze**: Pokud už máte předplatné Azure, doporučuje se výchozí nastavení. Pokud nemáte předplatné Azure, začněte s bezplatnou zkušební verzí.
    * **Informace o fakturaci**: aplikace je zadarmo. K zřizování prostředků vaší aplikace se vyžaduje adresář, předplatné Azure a podrobnosti o oblasti.
    * V dolní části stránky klikněte na tlačítko **vytvořit** a vaše aplikace se vytvoří za minutu.
        > [!div class="mx-imgBorder"]
        > ![formulář nové aplikace](media/tutorial-iot-central-solar-panel/solar-panel-create-app.png)


### <a name="verify-the-application-and-simulated-data"></a>Ověření aplikace a simulovaných dat

Nově vytvořená aplikace s přípanelem slunečníku je vaše aplikace a můžete ji kdykoli upravit. Pojďme se ujistit, že je aplikace nasazená a funguje podle očekávání, než ji upravíte.

Pokud chcete ověřit simulaci vytváření a dat aplikace, navštivte **řídicí panel**. Pokud vidíte dlaždice s některými daty, bylo nasazení aplikace úspěšné. Simulace dat může trvat několik minut, než se data vygenerují, takže dejte 1-2 minut. 

## <a name="application-walk-through"></a>Procházení aplikací – přes
Po úspěšném nasazení šablony aplikace se zobrazí ukázka zařízení inteligentního měřiče, modelu zařízení a řídicího panelu.

Adatum je fiktivní energetická společnost, která monitoruje a spravuje sluneční panely. Na řídicím panelu monitorování na panelu slunečního zobrazení uvidíte vlastnosti, data a ukázkové příkazy pro sluneční panel. Umožňuje operátorům a pracovníkům podpory proaktivně provádět následující aktivity před tím, než se povede na incidenty podpory:
* Přečtěte si nejnovější informace o panelu a jeho umístění nainstalované na mapě.
* Proaktivně kontrolovat stav panelů a stav připojení
* Přečtěte si trendy v oblasti energetiky a teploty, abyste mohli zachytit všechny neobvyklé vzory.
* Sledovat celkové energetické generace pro účely plánování a fakturace
* Operace s příkazy a ovládacími prvky, jako je například panel aktivace a verze aktualizovaného firmwaru. V šabloně se na příkazových tlačítkách zobrazují možné funkce a neodesílají skutečné příkazy.

[!div class="mx-imgBorder"]
řídicí panel monitorování ![ho slunečního panelu](media/tutorial-iot-central-solar-panel/solar-panel-dashboard.png)

### <a name="devices"></a>Zařízení
Aplikace přichází s ukázkovým zařízením slunečního zařízení. Podrobnosti o zařízení uvidíte kliknutím na kartu **zařízení** .

[!div class="mx-imgBorder"]
zařízení ![slunečního panelu](media/tutorial-iot-central-solar-panel/solar-panel-device.png)


Kliknutím na odkaz ukázkový **SP0123456789** zařízení zobrazíte podrobnosti o zařízení. Na stránce **aktualizovat vlastnosti** můžete aktualizovat vlastnosti zařízení s možností zápisu a vizualizovat aktualizované hodnoty na řídicím panelu. 

[!div class="mx-imgBorder"]
Vlastnosti panelu ![ho slunečníku](media/tutorial-iot-central-solar-panel/solar-panel-device-properties.png)


### <a name="device-template"></a>Šablona zařízení
Kliknutím na kartu **šablony zařízení** zobrazíte model zařízení slunečního panelu. Model má předem definované rozhraní pro data, vlastnosti, příkazy a zobrazení.

[!div class="mx-imgBorder"]
Šablona zařízení panelu ![slunečního pří](media/tutorial-iot-central-solar-panel/solar-panel-device-templates.png)


## <a name="clean-up-resources"></a>Vyčištění prostředků
Pokud se rozhodnete, že tuto aplikaci nebudete používat, odstraňte aplikaci pomocí následujících kroků:

1. V levém podokně otevřete kartu Správa.
2. Vyberte nastavení aplikace a klikněte na tlačítko Odstranit v dolní části stránky. 

    [!div class="mx-imgBorder"]
    ![odstranit](media/tutorial-iot-central-solar-panel/solar-panel-delete-app.png) aplikace


## <a name="next-steps"></a>Další kroky
* Další informace o architektuře aplikací na panelu slunečního příčtěte si [článek o konceptu](https://docs.microsoft.com/azure/iot-central/energy/concept-iot-central-solar-panel-app)
* Vytváření šablon aplikací pro sluneční použití, zdarma: [aplikace v panelu slunečního](https://apps.azureiotcentral.com/build/new/solar-panel-monitoring) programu
* Další informace o IoT Central najdete v tématu [IoT Central Overview](https://docs.microsoft.com/azure/iot-central/) .

