---
title: Kurz – definování nového typu zařízení brány v Azure IoT Central | Microsoft Docs
description: V tomto kurzu se dozvíte jako tvůrce, jak v aplikaci IoT Central Azure definovat nový typ zařízení brány IoT Gateway.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: cdace22072e4122150c878d43b99765cd6fd59e1
ms.sourcegitcommit: bfa7d6ac93afe5f039d68c0ac389f06257223b42
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106490773"
---
# <a name="tutorial---define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Kurz – definování nového typu zařízení brány IoT v aplikaci Azure IoT Central

*Tento článek se týká tvůrců řešení a vývojářů zařízení.*

V tomto kurzu se dozvíte jako tvůrce řešení, jak použít šablonu zařízení brány k definování zařízení brány v IoT Central aplikaci. Potom nakonfigurujete několik podřízených zařízení, která se připojují k vaší IoT Central aplikaci prostřednictvím zařízení brány. 

V tomto kurzu vytvoříte šablonu zařízení s bránou **Smart budova** . Zařízení **inteligentní brány pro sestavování** má relace s jinými zařízeními pro příjem dat.

![Diagram vztahů mezi zařízením brány a zařízeními pro příjem dat](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

I když chcete, aby zařízení komunikovala s aplikací IoT Central, může také zařízení brány:

* Odeslat svou vlastní telemetrii, například teplotu.
* Reagují na aktualizace vlastností s možností zápisu provedené operátorem. Například operátor může změnit interval odesílání telemetrie.
* Odpovězte na příkazy, jako je třeba restartování zařízení.

> [!div class="checklist"]
> Vytvoření šablon zařízení pro příjem dat vytvoření šablony zařízení brány publikování šablony zařízení vytvoření simulovaných zařízení

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete [vytvořit aplikaci Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="create-downstream-device-templates"></a>Vytvoření šablon pro příjem zařízení

Tento kurz používá šablony zařízení pro zařízení se **senzorem S1** a zařízení **snímače RS40** pro vygenerování simulovaných zařízení pro příjem dat.

Vytvoření šablony zařízení pro zařízení se **senzorem S1** :

1. V levém podokně vyberte **šablony zařízení**. Pak vyberte **+** , pokud chcete začít přidávat šablonu.

1. Posuňte se dolů, dokud se nezobrazí dlaždice zařízení se **senzorem S1** . Vyberte dlaždici a potom vyberte **Další: přizpůsobit**.

1. Na stránce **Kontrola** vyberte **vytvořit** a přidejte do své aplikace šablonu zařízení. 

Vytvoření šablony zařízení pro zařízení **RS40 pro senzory** :

1. V levém podokně vyberte **šablony zařízení**. Pak vyberte **+** , pokud chcete začít přidávat šablonu.

1. Posuňte se dolů, dokud se nezobrazí dlaždice zařízení ***RS40 pro snímače obsazení** . Vyberte dlaždici a potom vyberte **Další: přizpůsobit**.

1. Na stránce **Kontrola** vyberte **vytvořit** a přidejte do své aplikace šablonu zařízení. 

Nyní máte šablony zařízení pro dva typy pro stejné zařízení:

![Šablony zařízení pro podřízená zařízení](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Vytvoření šablony zařízení brány

V tomto kurzu vytvoříte šablonu zařízení pro zařízení brány od úplného začátku. Tuto šablonu použijete později k vytvoření simulovaného zařízení brány ve vaší aplikaci.

Přidání nové šablony zařízení brány do vaší aplikace:

1. V levém podokně vyberte **šablony zařízení**. Pak vyberte **+** , pokud chcete začít přidávat šablonu.

1. Na stránce **Vybrat typ šablony** vyberte dlaždici **zařízení IoT** a potom vyberte **Další: přizpůsobit**.

1. Na stránce **přizpůsobit zařízení** zaškrtněte políčko **zařízení brány** .

1. Na stránce **Kontrola** vyberte **vytvořit**. 

1. Jako název šablony zadejte **zařízení inteligentního sestavení brány** .

1. Na stránce **vytvořit model schopností** vyberte **vlastní** dlaždici.

1. Tuto možnost vyberte **+** , pokud chcete přidat rozhraní.  Vyberte standardní rozhraní **informace o zařízení** .

### <a name="add-relationships"></a>Přidat relace

Dále přidáte relace do šablon pro šablony zařízení pro příjem dat:

1. V šabloně **zařízení s bránou Smart budova** vyberte možnost **relace**.

1. Vyberte **+ Přidat relaci**. Jako zobrazovaný název zadejte **senzor prostředí** a jako cíl vyberte **S1 senzor** .

1. Vyberte **+ Přidat relaci** znovu. Jako zobrazované jméno zadejte **snímač obsazení** a jako cíl vyberte **senzor obsazení RS40** .

1. Vyberte **Uložit**.

![Šablona zařízení inteligentního sestavování, zobrazení relací](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Přidání vlastností cloudu

Šablona zařízení brány může obsahovat vlastnosti cloudu. Vlastnosti cloudu existují jenom v aplikaci IoT Central a nikdy se neodesílají do zařízení nebo se z něj nepřijímají.

Chcete-li přidat vlastnosti cloudu do šablony **zařízení služby inteligentního sestavování** .

1. V šabloně **zařízení s bránou Smart budova** vyberte **vlastnosti cloudu**.

1.  Informace v následující tabulce použijte k přidání dvou vlastností cloudu do šablony zařízení brány.

    | Zobrazované jméno      | Sémantický typ | Schéma |
    | ----------------- | ------------- | ------ |
    | Last Service Date (Datum poslední údržby) | Žádné          | Date (Datum)   |
    | Název zákazníka     | Žádné          | Řetězec |

2. Vyberte **Uložit**.

### <a name="create-views"></a>Vytváření zobrazení

Jako tvůrce můžete aplikaci přizpůsobit tak, aby zobrazovala relevantní informace o zařízení snímače životního prostředí pro operátora. Vlastní nastavení umožňuje operátorovi spravovat zařízení senzorů pro životní prostředí připojená k aplikaci. Můžete vytvořit dva typy zobrazení pro operátora pro práci se zařízeními:

* Formuláře pro zobrazení a úpravy vlastností zařízení a cloudu.
* Řídicí panely k vizualizaci zařízení.

Chcete-li vygenerovat výchozí zobrazení pro šablonu **zařízení inteligentního sestavení brány** :

1. V šabloně **zařízení inteligentní budova brány** vyberte **zobrazení**.

1. Vyberte možnost **Generovat výchozí zobrazení** a ujistěte se, že jsou vybrané všechny možnosti.

1. Vyberte **generovat zobrazení výchozích řídicích panelů**.

## <a name="publish-the-device-template"></a>Publikování šablony zařízení

Než budete moct vytvořit simulované zařízení brány nebo připojit zařízení reálné brány, budete muset publikovat šablonu zařízení.

Publikování šablony zařízení brány:

1. Na stránce **šablony zařízení** vyberte šablonu **zařízení inteligentního sestavení brány** .

2. Vyberte **Publikovat**.

3. V dialogovém okně **publikovat šablonu zařízení** klikněte na tlačítko **publikovat**.

Po publikování je šablona zařízení zobrazená na stránce **zařízení** a v operátoru. V publikované šabloně zařízení nemůžete upravovat model zařízení bez vytváření nové verze. V publikované šabloně zařízení ale můžete dělat aktualizace vlastností cloudu, přizpůsobení a zobrazení. Tyto aktualizace nezpůsobí vytvoření nové verze. Po provedení změn vyberte **publikovat**  , aby se tyto změny převedly do vašeho operátoru.

## <a name="create-the-simulated-devices"></a>Vytvoření simulovaných zařízení

V tomto kurzu se používá simulovaná zařízení pro příjem dat a simulované zařízení brány.

Postup vytvoření simulovaného zařízení brány:

1. Na stránce **zařízení** vyberte v seznamu šablon zařízení možnost **zařízení inteligentního sestavení brány** .

1. Tuto možnost vyberte **+** , pokud chcete začít přidávat nové zařízení.

1. Ponechte vygenerované **ID zařízení** a **název zařízení**. Ujistěte se, že je **simulovaný** přepínač **zapnutý**. Vyberte **Vytvořit**.

Postup vytvoření simulovaných zařízení pro příjem dat:

1. Na stránce **zařízení** vyberte v seznamu šablon zařízení možnost **senzor obsazenosti RS40** .

1. Tuto možnost vyberte **+** , pokud chcete začít přidávat nové zařízení.

1. Ponechte vygenerované **ID zařízení** a **název zařízení**. Ujistěte se, že je **simulovaný** přepínač **zapnutý**. Vyberte **Vytvořit**.

1. Na stránce **zařízení** vyberte v seznamu šablon zařízení možnost **senzor S1** .

1. Tuto možnost vyberte **+** , pokud chcete začít přidávat nové zařízení.

1. Ponechte vygenerované **ID zařízení** a **název zařízení**. Ujistěte se, že je **simulovaný** přepínač **zapnutý**. Vyberte **Vytvořit**.

![Simulovaná zařízení ve vaší aplikaci](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Přidání vztahů zařízení pro příjem dat do zařízení brány

Teď, když máte simulovaná zařízení ve vaší aplikaci, můžete vytvořit vztahy mezi zařízeními pro příjem dat a zařízením brány:

1. Na stránce **zařízení** vyberte v seznamu šablon zařízení možnost **senzor S1** a potom vyberte simulované zařízení **S1 snímače S1** .

1. Vyberte **připojit k bráně**.

1. V dialogovém okně **připojit k bráně** vyberte šablonu **zařízení pro inteligentní sestavení** a potom vyberte simulovanou instanci, kterou jste předtím vytvořili.

1. Vyberte **připojit**.

1. Na stránce **zařízení** vyberte v seznamu šablon zařízení **senzor obsazenosti RS40** a pak vyberte vaše simulované zařízení **RS40 pro obsazení** .

1. Vyberte **připojit k bráně**.

1. V dialogovém okně **připojit k bráně** vyberte šablonu **zařízení pro inteligentní sestavení** a potom vyberte simulovanou instanci, kterou jste předtím vytvořili.

1. Vyberte **připojit**.

Simulovaná zařízení pro příjem dat jsou teď připojená k vašemu simulovanému zařízení brány. Pokud přejdete do zobrazení **zařízení pro příjem dat** pro vaše zařízení brány, uvidíte související zařízení pro příjem dat:

![Zobrazení pro podřízené zařízení](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Vyberte šablonu zařízení brány a instanci zařízení brány a pak vyberte **připojit**.

## <a name="clean-up-resources"></a>Vyčištění prostředků

[!INCLUDE [iot-central-clean-up-resources](../../../includes/iot-central-clean-up-resources.md)]

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vytvořte novou bránu IoT jako šablonu zařízení.
* Vytvořte vlastnosti cloudu.
* Vytvořte vlastní nastavení.
* Definujte vizualizaci pro telemetrii zařízení.
* Přidejte relace.
* Publikujte šablonu zařízení.

Jako vývojář zařízení se můžete seznámit s postupem:

> [!div class="nextstepaction"]
> [Přidání zařízení Azure IoT Edge do aplikace Azure IoT Central](tutorial-add-edge-as-leaf-device.md)
