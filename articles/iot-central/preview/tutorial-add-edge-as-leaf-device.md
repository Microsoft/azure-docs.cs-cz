---
title: Přidání zařízení Azure IoT Edge do Azure IoT Central | Microsoft Docs
description: Jako operátor přidejte do aplikace Azure IoT Central Azure IoT Edge zařízení.
author: rangv
ms.author: rangv
ms.date: 12/09/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: e5d60c77e9bdc0733c12bca891eb6c3e33a1fceb
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2019
ms.locfileid: "74979066"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application"></a>Kurz: Přidání zařízení Azure IoT Edge do aplikace Azure IoT Central

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

V tomto kurzu se dozvíte, jak do aplikace Azure IoT Central přidat a nakonfigurovat zařízení Azure IoT Edge. V tomto kurzu jsme zvolili virtuální počítač se systémem Linux IoT Edge s podporou Azure Marketplace.

Tento kurz se skládá ze dvou částí:

* Jako první se naučíte, jak si Cloud nejdřív zřídí IoT Edge zařízení.
* Pak se naučíte, jak si zařízení IoT Edge zřídit jako první.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat nové zařízení IoT Edge
> * Konfigurace zařízení IoT Edge pro usnadnění zřizování pomocí klíče sdíleného přístupového podpisu (SAS)
> * Zobrazení řídicích panelů a stavu modulů v IoT Central
> * Odeslání příkazů do modulu běžícím na zařízení IoT Edge
> * Nastavení vlastností modulu běžícího na IoT Edgem zařízení

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete aplikaci Azure IoT Central. [V tomto rychlém startu vytvoříte aplikaci Azure IoT Central](./quick-deploy-iot-central.md).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Povolit Azure IoT Edge skupinu registrace
Na stránce **Správa** povolte klíče SAS pro skupinu registrace Azure IoT Edge.

![Snímek obrazovky se stránkou správy se zvýrazněným připojením zařízení](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="provision-a-cloud-first-azure-iot-edge-device"></a>Zřízení "Cloud First" Azure IoT Edge zařízení  
V této části vytvoříte nové zařízení IoT Edge pomocí šablony senzoru prostředí a zřídíte zařízení. Vyberte **zařízení** > **šablonu senzoru prostředí**. 

![Obrazovka stránky zařízení s zvýrazněnou šablonou senzoru prostředí](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Vyberte **+ Nová**a zadejte ID zařízení a název, který zvolíte. Vyberte **Create** (Vytvořit).

![Snímek obrazovky s dialogovým oknem vytvořit nové zařízení s ID zařízení a zvýrazněným polem pro vytvoření](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Zařízení přejde do **registrovaného** režimu.

![Snímek stránky šablony senzoru prostředí se zvýrazněným stavem zařízení](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-iot-edge-enabled-linux-vm"></a>Nasazení virtuálního počítače Linux s povoleným IoT Edge

> [!NOTE]
> Můžete zvolit, že se má použít libovolný počítač nebo zařízení. Operační systém může být Linux nebo Windows.

V tomto kurzu používáme virtuální počítač s povolenou službou Azure IoT, který je vytvořený v Azure. V [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview)vyberte **získat hned**. 

![Snímek obrazovky Azure Marketplace se zvýrazněnou možností získat hned](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Vyberte **Pokračovat**.

![Snímek obrazovky s dialogovým oknem vytvořit tuto aplikaci v Azure se zvýrazněnou možností pokračovat](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Přejdete na Azure Portal. Vyberte **Create** (Vytvořit).

![Snímek obrazovky Azure Portal se zvýrazněnou možností vytvořit](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Vyberte **předplatné**, vytvořte novou skupinu prostředků a vyberte **(US) západní USA 2** pro dostupnost virtuálního počítače. Pak zadejte informace o uživateli a hesle. Ty se budou vyžadovat v budoucích krocích, takže je nezapomeňte zapamatovat. Vyberte **Zkontrolovat a vytvořit**.

![Snímek obrazovky vytvoření stránky s podrobnostmi o virtuálním počítači s vybranými různými možnostmi](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Po ověření vyberte **vytvořit**.

![Snímek obrazovky se stránkou vytvořit virtuální počítač s prošlým ověřením a zvýrazněným vytvořením](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Vytvoření prostředků trvá několik minut. Vyberte **Přejít k prostředku**.

![Snímek stránky dokončení nasazení se zvýrazněnou možností přejít na prostředek](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-an-iot-edge-device"></a>Zřízení virtuálního počítače jako zařízení IoT Edge 

V části **Podpora a řešení potíží**vyberte **sériová konzola**.

![Snímek obrazovky s podporou a možnostmi řešení potíží s zvýrazněným Sériová konzola](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Zobrazí se obrazovka podobná následující:

![Snímek obrazovky konzoly](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Stiskněte klávesu ENTER, zadejte uživatelské jméno a heslo podle pokynů a potom stiskněte klávesu ENTER. 

![Snímek obrazovky konzoly](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Chcete-li spustit příkaz jako správce (uživatel "root"), zadejte: **sudo Su –**

![Snímek obrazovky konzoly](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Podívejte se na verzi modulu runtime IoT Edge. V době psaní tohoto zápisu je aktuální verze GA 1.0.8.

![Snímek obrazovky konzoly](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Nainstalujte Editor vim nebo v případě potřeby použijte nano. 

![Snímek obrazovky konzoly](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Snímek obrazovky konzoly](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Upravte soubor IoT Edge config. yaml.

![Snímek obrazovky konzoly](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Posuňte se dolů a odkomentujte část připojovacího řetězce souboru YAML. 

**Dříve**

![Snímek obrazovky konzoly](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Po** (stiskněte klávesu ESC a stiskněte malé písmeno a, abyste mohli začít upravovat.)

![Snímek obrazovky konzoly](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Odkomentujte část symetrického klíče souboru YAML. 

**Dříve**

![Snímek obrazovky konzoly](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Nyní**

![Snímek obrazovky konzoly](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Přejít na IoT Central. Získejte ID oboru, ID zařízení a symetrický klíč zařízení IoT Edge.
![snímku IoT Central se zvýrazněnými různými možnostmi připojení zařízení](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Přejít na počítač se systémem Linux a nahradit ID oboru a ID registrace identifikátorem zařízení a symetrickým klíčem.

Stiskněte klávesu ESC a zadejte **: WQ!** . Pokud chcete změny uložit, stiskněte klávesu ENTER.

Restartujte IoT Edge pro zpracování změn a stiskněte klávesu ENTER.

![Snímek obrazovky konzoly](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Zadejte **seznam iotedge**. Po několika minutách se zobrazí tři moduly nasazené.

![Snímek obrazovky konzoly](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central Průzkumník zařízení 

V IoT Central se vaše zařízení přesune do zřízeného stavu.

![Snímek obrazovky s možnostmi zařízení IoT Central se zvýrazněným stavem zařízení](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

Karta **moduly** zobrazuje stav zařízení a modulu v IoT Central. 

![Snímek obrazovky s kartou IoT Central moduly](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Ve formuláři se zobrazí vlastnosti cloudu ze šablony zařízení, kterou jste vytvořili v předchozích krocích. Zadejte hodnoty a vyberte **Uložit**. 

![Snímek obrazovky s formulářem pro zařízení se systémem Linux Edge](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Tady je zobrazení prezentované ve formě dlaždice řídicího panelu.

![Snímek obrazovky s dlaždicemi řídicího panelu zařízení pro Linux Edge](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak pracovat se zařízeními IoT Edge a spravovat je v IoT Central, tady je doporučený další krok:

<!-- Next how-tos in the sequence -->

> [!div class="nextstepaction"]
> [Konfigurace transparentní brány](../../iot-edge/how-to-create-transparent-gateway.md)
