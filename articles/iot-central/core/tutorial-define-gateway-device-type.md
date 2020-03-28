---
title: Definování nového typu zařízení brány v Azure IoT Central | Dokumenty společnosti Microsoft
description: Tento kurz ukazuje, jako tvůrce, jak definovat nový typ zařízení brány IoT ve vaší aplikaci Azure IoT Central.
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 7bb386d39f53331e77bee4d863c431c2e3655f5c
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79298831"
---
# <a name="define-a-new-iot-gateway-device-type-in-your-azure-iot-central-application"></a>Definování nového typu zařízení brány IoT ve vaší aplikaci Azure IoT Central

Tento kurz ukazuje, jak jako tvůrce používat šablonu zařízení brány k definování zařízení brány v aplikaci IoT Central. Potom nakonfigurujete několik podřizovaných zařízení, která se připojují k aplikaci IoT Central prostřednictvím zařízení brány. 

V tomto kurzu vytvoříte šablonu zařízení brány **inteligentní budovy.** Zařízení **brány inteligentní budovy** má vztahy s jinými zařízeními pro příjem dat.

![Diagram vztahu mezi zařízením brány a zařízeními pro příjem dat](./media/tutorial-define-gateway-device-type/gatewaypattern.png)

Kromě toho, že zařízení pro příjem dat umožňuje komunikaci s vaší aplikací IoT Central, může zařízení brány také:

* Odešlete vlastní telemetrii, například teplotu.
* Reagujte na aktualizace vlastností, které lze zapisovat, provedené operátorem. Operátor může například změnit interval odesílání telemetrie.
* Reagujte na příkazy, jako je restartování zařízení.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu, je třeba [vytvořit aplikaci Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="create-downstream-device-templates"></a>Vytvoření šablon zařízení pro příjem dat

Tento kurz používá šablony zařízení pro zařízení **S1 Sensor** a zařízení **RS40 Occupancy Sensor** pro generování simulovaných podřízené zařízení.

Vytvoření šablony zařízení pro zařízení **se snímačem S1:**

1. V levém podokně vyberte **Položku Šablony zařízení**. Pak **+** vyberte, chcete-li začít přidávat šablonu.

1. Posuňte se dolů, dokud neuvidíte dlaždici zařízení **S1 Sensor.** Vyberte dlaždici a pak vyberte **Další: Přizpůsobit**.

1. Na stránce **Revize** vyberte **Vytvořit** a přidejte do aplikace šablonu zařízení. 

Vytvoření šablony zařízení pro zařízení ***RS40 Occupancy Sensor:**

1. V levém podokně vyberte **Položku Šablony zařízení**. Pak **+** vyberte, chcete-li začít přidávat šablonu.

1. Posuňte se dolů, dokud neuvidíte dlaždici zařízení ***RS40 Occupancy Sensor.** Vyberte dlaždici a pak vyberte **Další: Přizpůsobit**.

1. Na stránce **Revize** vyberte **Vytvořit** a přidejte do aplikace šablonu zařízení. 

Nyní máte šablony zařízení pro dva typy zařízení pro příjem dat:

![Šablony zařízení pro zařízení pro příjem dat](./media/tutorial-define-gateway-device-type/downstream-device-types.png)


## <a name="create-a-gateway-device-template"></a>Vytvoření šablony zařízení brány

V tomto kurzu vytvoříte šablonu zařízení pro zařízení brány od začátku. Tuto šablonu později použijete k vytvoření simulovaného zařízení brány ve vaší aplikaci.

Přidání nové šablony zařízení brány do aplikace:

1. V levém podokně vyberte **Položku Šablony zařízení**. Pak **+** vyberte, chcete-li začít přidávat šablonu.

1. Na stránce **Vybrat typ šablony** vyberte dlaždici Zařízení **IoT** a pak vyberte **Další: Přizpůsobit**.

1. Na stránce **Přizpůsobit zařízení** zaškrtněte políčko **Brána zařízení.**

1. Na stránce **Revize** vyberte **Vytvořit**. 

1. Jako název šablony zadejte **zařízení brány inteligentní budovy.**

1. Na stránce **Vytvořit model schopností** vyberte **vlastní** dlaždici.

1. Výběrem **+** přidáte rozhraní.  Zvolte standardní rozhraní **Informace o zařízení.**

### <a name="add-relationships"></a>Přidání relací

Dále přidáte relace do šablon pro šablony zařízení pro příjem dat:

1. V šabloně **zařízení Brány inteligentní budovy** vyberte **relace**.

1. Vyberte **+ Přidat relaci**. Jako zobrazovaný název zadejte **senzor prostředí** a jako cíl vyberte **snímač S1.**

1. Vyberte **+ Přidat relaci** znovu. Jako zobrazované jméno zadejte **snímač obsazenosti** a jako cíl vyberte **rs40 occupancy sensor.**

1. Vyberte **Uložit**.

![Šablona zařízení brány inteligentní budovy, zobrazující relace](./media/tutorial-define-gateway-device-type/relationships.png)

### <a name="add-cloud-properties"></a>Přidání vlastností cloudu

Šablona zařízení brány může obsahovat vlastnosti cloudu. Vlastnosti cloudu existují pouze v aplikaci IoT Central a nikdy se neodesílají nebo nepřijímají ze zařízení.

Přidání vlastností cloudu do šablony **zařízení brány Smart Building.**

1. V šabloně **zařízení Brány inteligentní budovy** vyberte **Vlastnosti cloudu**.

1.  Pomocí informací v následující tabulce můžete do šablony zařízení brány přidat dvě vlastnosti cloudu.

    | Zobrazované jméno      | Sémantický typ | Schéma |
    | ----------------- | ------------- | ------ |
    | Last Service Date (Datum poslední údržby) | Žádný          | Datum   |
    | Jméno zákazníka     | Žádný          | Řetězec |

2. Vyberte **Uložit**.

### <a name="create-views"></a>Vytváření zobrazení

Jako tvůrce můžete přizpůsobit aplikaci tak, aby zobrazovala operátorovi relevantní informace o zařízení senzoru prostředí. Vaše vlastní nastavení umožňují obsluze spravovat zařízení environmentálních senzorů připojených k aplikaci. Pro operátora můžete vytvořit dva typy zobrazení, které chcete použít k interakci se zařízeními:

* Formuláře pro zobrazení a úpravu vlastností zařízení a cloudu.
* Řídicí panely pro vizualizaci zařízení.

Jak generovat výchozí zobrazení pro šablonu **zařízení brány inteligentní budovy:**

1. V šabloně **zařízení Brány inteligentní budovy** vyberte **Zobrazení**.

1. Vyberte **Generovat výchozí dlaždici zobrazení** a ujistěte se, že jsou vybrány všechny možnosti.

1. Vyberte **Generovat výchozí zobrazení řídicího panelu**.

## <a name="publish-the-device-template"></a>Publikování šablony zařízení

Než budete moct vytvořit simulované zařízení brány nebo připojit skutečné zařízení brány, musíte publikovat šablonu zařízení.

Jak publikovat šablonu zařízení brány:

1. Na stránce Šablony zařízení vyberte šablonu zařízení Brány inteligentní **budovy.** **Smart Building gateway device**

2. Vyberte **Publikovat**.

3. V **dialogovém** okně Publikovat šablonu zařízení zvolte **Publikovat**.

Po publikování šablony zařízení se zobrazí na stránce **Zařízení** a operátorovi. V publikované šabloně zařízení nelze upravit model schopností zařízení bez vytvoření nové verze. Můžete však aktualizovat vlastnosti cloudu, vlastní nastavení a zobrazení v publikované šabloně zařízení. Tyto aktualizace nezpůsobí novou verzi, která má být vytvořena. Po provedení jakýchkoli změn vyberte **Publikovat** a tyto změny přenesete na operátora.

## <a name="create-the-simulated-devices"></a>Vytvoření simulovaných zařízení

Tento kurz používá simulovaná zařízení pro příjem dat a simulované zařízení brány.

Vytvoření simulovaného zařízení brány:

1. Na stránce **Zařízení** vyberte zařízení **Brány inteligentní budovy** v seznamu šablon zařízení.

1. Výběrem **+** vyberte, chcete-li začít přidávat nové zařízení.

1. Zachovat generované **ID zařízení** a **název zařízení**. Ujistěte se, že **je** **zapnutý simulovaný** přepínač . Vyberte **Vytvořit**.

Vytvoření simulovaných navazujících zařízení:

1. Na stránce **Zařízení** vyberte v seznamu šablon zařízení **senzor obsazenosti RS40.**

1. Výběrem **+** vyberte, chcete-li začít přidávat nové zařízení.

1. Zachovat generované **ID zařízení** a **název zařízení**. Ujistěte se, že **je** **zapnutý simulovaný** přepínač . Vyberte **Vytvořit**.

1. Na stránce **Zařízení** vyberte v seznamu šablon zařízení **snímač S1.**

1. Výběrem **+** vyberte, chcete-li začít přidávat nové zařízení.

1. Zachovat generované **ID zařízení** a **název zařízení**. Ujistěte se, že **je** **zapnutý simulovaný** přepínač . Vyberte **Vytvořit**.

![Simulovaná zařízení ve vaší aplikaci](./media/tutorial-define-gateway-device-type/simulated-devices.png)

### <a name="add-downstream-device-relationships-to-a-gateway-device"></a>Přidání vztahů podřízeného zařízení do zařízení brány

Teď, když máte simulovaná zařízení ve vaší aplikaci, můžete vytvořit vztahy mezi zařízeními pro příjem dat a zařízením brány:

1. Na stránce **Zařízení** vyberte v seznamu šablon zařízení **snímač S1** a pak vyberte simulované zařízení **snímače S1.**

1. Vyberte **Připojit k bráně**.

1. V **dialogovém** okně Připojit k bráně vyberte šablonu **zařízení brány inteligentní budovy** a pak vyberte simulovanou instanci, kterou jste vytvořili dříve.

1. Vyberte **Připojit .**

1. Na stránce **Zařízení** vyberte v seznamu šablon zařízení **senzor obsazenosti RS40** a pak vyberte simulované zařízení **SNÍMAČ Obsazení RS40.**

1. Vyberte **Připojit k bráně**.

1. V **dialogovém** okně Připojit k bráně vyberte šablonu **zařízení brány inteligentní budovy** a pak vyberte simulovanou instanci, kterou jste vytvořili dříve.

1. Vyberte **Připojit .**

Obě simulovaná zařízení pro příjem dat jsou nyní připojena k simulovanému zařízení brány. Pokud přejdete do zobrazení **Zařízení pro příjem dat** pro zařízení brány, uvidíte související zařízení pro příjem dat:

![Zobrazení zařízení pro příjem dat](./media/tutorial-define-gateway-device-type/downstream-device-view.png)

Vyberte šablonu zařízení brány a instanci zařízení brány a vyberte **Připojit .**

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

* Vytvořte novou bránu IoT jako šablonu zařízení.
* Vytvořte vlastnosti cloudu.
* Vytvořte vlastní nastavení.
* Definujte vizualizaci pro telemetrii zařízení.
* Přidejte vztahy.
* Publikujte šablonu zařízení.

> [!NOTE]
> Generování kódu založené na kódu VS není aktuálně podporováno pro zařízení brány modelovaná v IoT Central.

Dále můžete:

> [!div class="nextstepaction"]
> [Připojení zařízení](tutorial-connect-device.md)
