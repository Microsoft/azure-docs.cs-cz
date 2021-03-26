---
title: Kontrolní seznam předběžného certifikace pro IoT Edge modul nabízí v Azure Marketplace
description: Seznamte se s konkrétními požadavky na certifikaci pro publikování IoT Edge modulu nabídky v Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/01/2021
ms.openlocfilehash: 31c19f62f0328fca05562eaa2f19b7a79c0f3e15
ms.sourcegitcommit: f0a3ee8ff77ee89f83b69bc30cb87caa80f1e724
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2021
ms.locfileid: "105562694"
---
# <a name="pre-certification-checklist-for-iot-edge-modules"></a>Kontrolní seznam předběžného certifikace pro moduly IoT Edge

> [!NOTE]
> Před odesláním pro certifikace jsme vysoce doporučili vydavatelům, kteří kontrolují tuto kontrolní seznam a ověřují funkčnost modulu. Tím se urychlí proces certifikace tím, že se sníží nutnost změn a opětovné odeslání.

## <a name="validation-of-image"></a>Ověření obrázku

Jakmile je obrázek modulu Edge připravený k odeslání, proveďte tyto kroky, abyste zajistili, že bitová kopie funguje, jak Microsoft očekává.

### <a name="steps-to-perform-in-the-azure-portal"></a>Kroky, které je potřeba provést v Azure Portal

1. Otevřete [Azure Portal](https://partner.microsoft.com/).
1. Vytvořte skupinu prostředků.
1. Vytvořit IoT Hub.
1. Vytvořte zařízení IoT Edge.
1. Zkopírujte připojovací řetězec a uložte ho do poznámkového bloku.
1. Vyberte možnost nastavit **moduly na hraničním zařízení**, které jste vytvořili.
1. Přidejte ACR podrobnosti, kde se nachází nejnovější verze image.
1. Vyberte **přidat IoT Edge modul** a zadejte:
    - Identifikátor URI image v nastavení modulu
    - Proměnná prostředí (stejná jako ta, co se přidalo do partnerského centra)
    - Možnosti vytvoření kontejneru (stejné jako to, co se přidá do partnerského centra)
    - Nastavení s dvojitou shodou modulu (stejné jako při přidání do partnerského centra)
1. Přidejte trasy (stejně jako ty, co se přidá do partnerského centra).
1. Vyberte **Zkontrolovat a vytvořit**.

Hraniční moduly se nasazují na hraničním zařízení vytvořeném v Azure.

### <a name="steps-to-perform-on-the-device"></a>Kroky, které je potřeba provést na zařízení

#### <a name="device-details"></a>Podrobnosti o zařízení

Certifikační tým používá k ověření imagí v různých architekturách následující hardware:

- U imagí x64 má virtuální počítač Azure velikost konfigurace jako standard D2s v3, na kterém běží Ubuntu Server 18.04/Ubuntu Server 16,04.
- Pro bitové kopie Azure Resource Manager 32, Malina 3 – Model B.
- V případě imagí Azure Resource Manager 64 se nachází NVIDIA Jetson nano 4Gb.

#### <a name="steps"></a>Postup

1. Ujistěte se, že k vytvořeným zařízením nebo VIRTUÁLNÍm počítačům máte k dispozici prostřednictvím výstupu.
1. Stáhněte do zařízení [modul Runtime IoT Edge](../iot-edge/how-to-install-iot-edge.md) .
1. Aktualizujte připojovací řetězec zkopírovaný v kroku 5 do souboru config. yaml.
1. Restartujte modul Edge pomocí `sudo systemctl restart iotedge` .
1. Ověřte, jestli je modul nasazený na zařízení se systémem `sudo iotedge list` ; měl by být ve spuštěném stavu.
1. Zajistěte, aby protokoly modulu nasazeného se nedošlo k `sudo iotedge logs “Module Name“ -f` chybám. Pokud jsou k dispozici známé chyby, popište tuto skutečnost v partnerském centru **poznámky pro kontrolora** před odesláním nabídky.

## <a name="metadata-validation"></a>Ověřování metadat

Zkontrolujte:

- Nejnovější značka je uvedena v partnerském centru i Azure Container Registry.
- Do popisu nabídky se přidá minimální požadavek na hardware.
- Uživatelské jméno a heslo ke službě Azure Container Registry se aktualizuje a přidají v partnerském centru.
- V *případě potřeby* přesnost požadované **vlastnosti s dvojitým** plněním
- V *případě potřeby* přesnost požadovaných **proměnných prostředí** .
- Přesnost požadovaných **možností vytvoření** , je- *li k dispozici*.
- Připojovací řetězec pro správu potenciálních zákazníků je přítomen.
- Zásada ochrany osobních údajů je k dispozici
- Podmínky použití k dispozici
- Přidat podporovaný odkaz IoT Edge zařízení z [katalogu zařízení Azure IoT](https://devicecatalog.azure.com/devices?certificationBadgeTypes=IoTEdgeCompatible) 

## <a name="next-steps"></a>Další kroky

- [Nasazení modulů z komerčního tržiště](../iot-edge/how-to-deploy-modules-portal.md#deploy-from-azure-marketplace)
- [Publikování modulu Edge v partnerském centru](./partner-center-portal/azure-iot-edge-module-creation.md)
- [Nasadit modul IoT Edge](../iot-edge/quickstart-linux.md)