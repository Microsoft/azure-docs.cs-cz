---
title: Přidání zařízení Azure IoT Edge do Azure IoT Central | Microsoft Docs
description: Jako operátor přidejte zařízení Azure IoT Edge do Azure IoT Central
author: rangv
ms.author: rangv
ms.date: 10/22/2019
ms.topic: tutorial
ms.service: iot-central
services: iot-central
ms.custom: mvc
manager: peterpr
ms.openlocfilehash: 568e0a9c21a9d5d9a5186f0a7311c659b8573b6e
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/25/2019
ms.locfileid: "72957045"
---
# <a name="tutorial-add-an-azure-iot-edge-device-to-your-azure-iot-central-application-preview-features"></a>Kurz: Přidání zařízení Azure IoT Edge do aplikace Azure IoT Central (funkce ve verzi Preview)

[!INCLUDE [iot-central-pnp-original](../../../includes/iot-central-pnp-original-note.md)]

V tomto kurzu se dozvíte, jak přidat a nakonfigurovat *zařízení Azure IoI Edge* do IoT Central aplikace Microsoft Azure. V tomto kurzu jsme zvolili Azure IoT Edge povolený virtuální počítač Linux z Azure Marketplace.

Tento kurz se skládá ze dvou částí:

* Jako první se naučíte, jak si Cloud nejdřív zřídí Azure IoT Edge zařízení.
* Naučíte se, jak se zařízení poprvé zřídí Azure IoT Edge zařízení.

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidat nové zařízení Azure IoT Edge
> * Konfigurace zařízení Azure IoT Edge, aby bylo možné zajistit používání klíče SAS
> * Zobrazení řídicích panelů, stavu modulu v IoT Central
> * Odeslání příkazů do modulu běžícím na zařízení Azure IoT Edge
> * Nastavení vlastností modulu běžícího na Azure IoT Edgem zařízení

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete aplikaci Azure IoT Central. V tomto rychlém startu [vytvoříte aplikaci Azure IoT Central](quick-deploy-iot-central-pnp.md?toc=/azure/iot-central-pnp/toc.json&bc=/azure/iot-central-pnp/breadcrumb/toc.json).

## <a name="enable-azure-iot-edge-enrollment-group"></a>Povolit Azure IoT Edge skupinu registrace
Povolte klíče SAS pro Azure IoT Edge skupinu registrací ze stránky Správa.

![Šablona zařízení-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/groupenrollment.png)

## <a name="cloud-first-azure-iot-edge-device-provisioning"></a>Nejprve cloud Azure IoT Edge zřizování zařízení   
V této části vytvoříte nové zařízení Azure IoT Edge pomocí **šablony senzoru prostředí** a zřídíte zařízení. V levém navigačním panelu klikněte na zařízení a pak klikněte na šablonu senzoru prostředí. 

![Šablona zařízení-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/deviceexplorer.png)

Klikněte na **+ Nový** a zadejte ID a název zařízení, které vám to vyhovuje. 

![Šablona zařízení-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfdevicecredentials.png)

Zařízení přejde do **registrovaného** režimu.

![Šablona zařízení-Azure IoT Edge](./media/tutorial-add-edge-as-leaf-device/cfregistered.png)

## <a name="deploy-an-azure-iot-edge-enabled-linux-vm"></a>Nasazení virtuálního počítače Linux s povoleným Azure IoT Edge

>Poznámka: můžete zvolit, že se má použít libovolný počítač nebo zařízení. Operační systém: Linux nebo Windows)

Pro tento kurz jsme zvolili virtuální počítač Linux s podporou Azure IoT, který se dá vytvořit v Azure. Přejdete na [Azure Marketplace](https://azuremarketplace.microsoft.com/en-us/marketplace/apps/microsoft_iot_edge.iot_edge_vm_ubuntu?tab=Overview) a kliknete na tlačítko **získat nyní** . 

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplace.png)

Klikněte na **pokračovat** .

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfmarketplacecontinue.png)


Budete se muset Azure Portal. Klikněte na tlačítko **vytvořit** .

![Azure Marketplace](./media/tutorial-add-edge-as-leaf-device/cfubuntu.png)

Vyberte předplatné, vytvořte novou skupinu prostředků, pokud chcete, vyberte USA – západ 2 pro dostupnost virtuálního počítače zadejte uživatel a heslo. Zapamatovat uživatele, bude nutné zadat heslo pro budoucí kroky. Klikněte na tlačítko **zkontrolovat + vytvořit** .

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvm.png)

Po ověření klikněte na **vytvořit** .

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmvalidated.png)

Vytvoření prostředků trvá několik minut. Klikněte na přejít k **prostředku** .

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvmdeploymentcomplete.png)

### <a name="provision-vm-as-azure-iot-edge-device"></a>Zřízení virtuálního počítače jako zařízení Azure IoT Edge 

V části Podpora a řešení potíží v levém navigačním panelu klikněte na Sériová konzola

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfserialconsole.png)

Zobrazí se obrazovka podobná této:

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsole.png)

Po zobrazení výzvy stiskněte klávesu ENTER a zadejte uživatelské jméno a heslo a stiskněte klávesu ENTER. 

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolelogin.png)

Pokud chcete spustit příkaz jako správce nebo root, spusťte příkaz: **sudo Su –**

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfsudo.png)

Zkontroluje Azure IoT Edge verzi modulu runtime. Aktuální verze GA je 1.0.8

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleversion.png)

Nainstalujte Editor vim nebo použijte nano, pokud je to vaše preference. 

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolevim.png)

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfvim.png)

Upravit soubor Azure IoT Edge config. yaml

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsoleconfig.png)

Posuňte se dolů a odkomentujte část připojovacího řetězce v souboru YAML. 

**Uložení**

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioning.png)

**Po** (stiskněte klávesu ESC a stiskněte malé písmeno a, abyste mohli začít upravovat)

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfmanualprovisioningcomments.png)

Odkomentujte symetrickou část klíče souboru YAML. 

**Uložení**

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmcomments.png)

**Konci**

![Virtuální počítač s Ubuntu](./media/tutorial-add-edge-as-leaf-device/cfconsolesymmuncomments.png)

Přejít na IoT Central a získat ID oboru, ID zařízení a symetrický klíč zařízení Azure IoT Edge ![připojit](./media/tutorial-add-edge-as-leaf-device/cfdeviceconnect.png)

Přejít do pole Linux a nahradit ID oboru, ID registrace a ID zařízení a symetrickým klíčem

Stiskněte klávesu **ESC** a zadejte **: WQ!** a stisknutím klávesy **ENTER** změny uložte.

Restartujte Azure IoT Edge pro zpracování změn a stiskněte klávesu **ENTER** .

![Připojení zařízení](./media/tutorial-add-edge-as-leaf-device/cfrestart.png)

Typ: **seznam iotedge**, který bude trvat několik minut, se zobrazí tři moduly nasazené

![Připojení zařízení](./media/tutorial-add-edge-as-leaf-device/cfconsolemodulelist.png)


## <a name="iot-central-device-explorer"></a>IoT Central Průzkumník zařízení 

V IoT Central se vaše zařízení přesune do zřízeného stavu.

![Připojení zařízení](./media/tutorial-add-edge-as-leaf-device/cfprovisioned.png)

Karta moduly zobrazí stav zařízení a modulu v IoT Central 

![Připojení zařízení](./media/tutorial-add-edge-as-leaf-device/cfiotcmodulestatus.png)


Vlastnosti cloudu se zobrazí ve formuláři (ze šablony zařízení, kterou jste vytvořili v předchozím postupu). Zadejte hodnoty a klikněte na **Uložit**. 

![Připojení zařízení](./media/tutorial-add-edge-as-leaf-device/deviceinfo.png)

Dlaždice řídicího panelu

![Připojení zařízení](./media/tutorial-add-edge-as-leaf-device/dashboard.png)

V tomto kurzu jste se naučili:

* Přidat nové zařízení Azure IoT Edge
* Konfigurace zařízení Azure IoT Edge, aby bylo možné zajistit používání klíče SAS
* Zobrazení řídicích panelů, stavu modulu v IoT Central
* Odeslání příkazů do modulu běžícím na zařízení Azure IoT Edge
* Nastavení vlastností modulu běžícího na Azure IoT Edgem zařízení

## <a name="next-steps"></a>Další kroky

Teď, když jste se naučili, jak pracovat s Azure IoT Edge zařízení v IoT Central, je tady doporučený další krok:

<!-- Next how-tos in the sequence -->

Postup konfigurace transparentní brány najdete v tomto kurzu.

> [!div class="nextstepaction"]
> [Konfigurace transparentní brány](../../iot-edge/how-to-create-transparent-gateway.md)
