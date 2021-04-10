---
title: Pochopení podpory pro nepřipojené aktualizace zařízení pomocí mezipaměti připojené k Microsoftu | Microsoft Docs
titleSuffix: Device Update for Azure IoT Hub
description: Vysvětlení podpory pro nepřipojené aktualizace zařízení pomocí mezipaměti připojené k Microsoftu
author: andyriv
ms.author: andyriv
ms.date: 2/16/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: e2b27934f58402ecfb7dabf5560dc43e45f3f7dd
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679262"
---
# <a name="understand-support-for-disconnected-device-updates"></a>Vysvětlení podpory pro odpojené aktualizace zařízení

V případě transparentní brány může jedno nebo více zařízení předat zprávy pomocí jediného zařízení brány, které udržuje připojení k Azure IoT Hub. V těchto případech nemusí mít podřízená zařízení připojení k Internetu nebo nemusí stahovat obsah z Internetu. Modul Microsoft Connect cache ve verzi Preview IoT Edge poskytne aktualizace zařízení pro zákazníky Azure IoT Hub s funkcemi inteligentní mezipaměti v síti, která umožňuje aktualizace na základě bitové kopie a balíčků zařízení se systémem Linux na bázi operačního systému Linux za a IoT Edge bránu (zařízení IoT pro příjem dat) a také šetří šířku pásma pro aktualizace zařízení pro zákazníky Azure IoT Hub.

## <a name="how-does-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub-work"></a>Jak aplikace Microsoft propojili mezipaměť pro aktualizace zařízení pro Azure IoT Hub funguje?

Mezipaměť propojená Microsoftem je inteligentní a transparentní mezipaměť pro obsah publikovaný pro aktualizace zařízení pro Azure IoT Hub obsah a dá se přizpůsobit pro ukládání obsahu do mezipaměti z jiných zdrojů, jako jsou úložiště balíčků. Mezipaměť propojená Microsoftem je studená mezipaměť, kterou vyžadují klientské požadavky na přesné rozsahy souborů požadované klientem Optimalizace doručení a neobsahují předem počáteční obsah. Diagram a podrobný popis níže vysvětluje, jak funguje mezipaměť připojená Microsoftem v rámci aktualizace zařízení pro infrastrukturu Azure IoT Hub.

>[!Note]
>Při definování tohoto toku se předpokládá, že IoT Edge brána má připojení k Internetu. V případě scénáře pro příjem IoT Edge brány (vnořený hraniční) se dá "Content Delivery Network" (CDN) považovat za MCC hostovanou v nadřazené IoT Edge bráně.

  :::image type="content" source="media/connected-cache-overview/disconnected-device-update.png" alt-text="Odpojená aktualizace zařízení" lightbox="media/connected-cache-overview/disconnected-device-update.png":::

1. Mezipaměť propojená Microsoftem je nasazená jako modul IoT Edge na server Prem.
2. Aktualizace zařízení pro klienty Azure IoT Hub jsou nakonfigurovaná tak, aby stahoval obsah z mezipaměti připojené od Microsoftu na základě atributu GatewayHostName připojovacího řetězce zařízení pro zařízení IoT list **nebo** parent_hostname nastaveného v souboru config. toml pro IoT Edge podřízená zařízení.
3. Aktualizace zařízení pro klienty Azure IoT Hub v obou případech obdrží příkazy pro stažení obsahu aktualizace z aktualizace zařízení pro službu Azure IoT Hub a žádosti o aktualizaci obsahu na propojenou mezipaměť Microsoftu místo CDN. Mezipaměť propojená Microsoftem je ve výchozím nastavení nakonfigurovaná tak, aby naslouchala na portu HTTP 80, a klient Optimalizace doručení vytváří požadavek na obsah na portu 80, aby bylo nadřazenému objektu na tomto portu nakonfigurované naslouchání.  V tuto chvíli se podporuje jenom protokol HTTP.
4. Server Microsoft Connected cache stahuje obsah z CDN, semen do místní mezipaměti uložených na disku a doručuje obsah do aktualizace zařízení pro klienta Azure IoT Hub.
   
>[!Note]
>Při použití aktualizací založených na balíčku bude server připojené mezipaměti od Microsoftu nakonfigurovaný správcem s požadovaným názvem hostitele balíčku.

5. Následné žádosti z jiných aktualizací zařízení pro klienty Azure IoT Hub pro stejný obsah aktualizace teď přijde z mezipaměti a mezipaměť připojená od Microsoftu nevytvoří požadavky na CDN pro stejný obsah.

### <a name="supporting-industrial-iot-iiot-with-parentchild-hosting-scenarios"></a>Podpora průmyslových IoT (IIoT) s využitím nadřazených a podřízených scénářů hostování

Když je server s připojenou mezipamětí nebo podřízená brána IoT Edge hostuje, bude nakonfigurovaná pro vyžádání obsahu aktualizace z nadřazené IoT Edge brány, která je hostitelem serveru mezipaměti připojené k Microsoftu. To se vyžaduje pro tolik úrovní, kolik je potřeba, než se dostanete k nadřazené IoT Edge bráně, která hostuje server mezipaměti s připojením Microsoftu, který má přístup k Internetu. Z internetového serveru připojeného k Internetu se obsah vyžádá z CDN, kde se obsah doručí zpátky do podřízené IoT Edge brány, která původně požadovala obsah. Obsah bude uložen na disku na všech úrovních.

## <a name="access-to-the-microsoft-connected-cache-preview-for-device-update-for-azure-iot-hub"></a>Přístup k verzi Preview služby Microsoft Connect cache pro aktualizace zařízení pro Azure IoT Hub

Modul Microsoft Connected cache IoT Edgeed je vydaný jako verze Preview pro zákazníky, kteří nasazují řešení pomocí aktualizace zařízení pro Azure IoT Hub. Přístup k verzi Preview je prostřednictvím pozvánky. [Vyžádejte si přístup](https://aka.ms/MCCForDeviceUpdateForIoT) ke službě Microsoft Internet Cache Preview pro aktualizaci zařízení pro IoT vypnout a poskytněte požadované informace, pokud byste chtěli získat přístup k modulu.
