---
title: Zřizování aktualizace zařízení pro Azure IoT Hub agenta | Microsoft Docs
description: Zřizování aktualizace zařízení pro agenta Azure IoT Hub
author: ValOlson
ms.author: valls
ms.date: 2/16/2021
ms.topic: how-to
ms.service: iot-hub-device-update
ms.openlocfilehash: 79bac3f057412f3973121f48cd735f72d0a97d04
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101679229"
---
# <a name="device-update-agent"></a>Agent aktualizace zařízení

Aktualizace zařízení pro IoT Hub podporuje dvě formy aktualizací – založené na bitových kopiích a na balíčcích. 

* Aktualizace obrázků poskytují v konečném stavu zařízení vyšší úroveň spolehlivosti. Obvykle je snazší replikovat výsledky aktualizace image mezi předprodukčním prostředím a produkčním prostředím, protože nepředstavuje stejné výzvy jako balíčky a jejich závislosti. Z důvodu atomické povahy může jeden model převzetí služeb při selhání A/B také snadno přijmout. 
* Aktualizace založené na balíčku jsou cílené aktualizace, které mění pouze konkrétní součást nebo aplikaci na zařízení. Což vede ke snížení spotřeby šířky pásma a pomáhá zkrátit čas na stažení a instalaci aktualizace. Aktualizace balíčků obvykle umožňují méně výpadkům zařízení při použití aktualizace a vyhněte se režii při vytváření imagí. 

Pomocí odkazů níže můžete sestavovat, spouštět a upravovat agenta aktualizací zařízení.

## <a name="build-the-device-update-agent"></a>Sestavení agenta aktualizace zařízení

Podle pokynů [Sestavte](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-build-agent-code.md) agenta aktualizace zařízení ze zdroje.

## <a name="run-the-device-update-agent"></a>Spustit agenta aktualizace zařízení

Po úspěšném sestavení agenta je čas [Spustit](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-run-agent.md) agenta.

## <a name="modifying-the-device-update-agent"></a>Změna agenta aktualizace zařízení

Nyní proveďte potřebné změny pro zahrnutí agenta do vaší image.  Pokyny najdete v tématu Postup [Úpravy](https://github.com/Azure/iot-hub-device-update/blob/main/docs/agent-reference/how-to-modify-the-agent-code.m) agenta aktualizace zařízení.

### <a name="troubleshooting-guide"></a>Průvodce odstraňováním potíží

Pokud narazíte na problémy, přečtěte si příručku aktualizace zařízení IoT Hub [Průvodce řešením potíží](troubleshoot-device-update.md) , která vám pomůže odblokovat případné problémy a shromažďovat potřebné informace pro poskytování Microsoftu.

## <a name="next-steps"></a>Další kroky

Pomocí níže uvedených předem sestavených imagí a binárních souborů můžete snadno vydemonstrační aktualizaci zařízení IoT Hub.  

[Aktualizace Image: Začínáme s malinou PI 3 B + referenční obrázek Yocto](device-update-raspberry-pi.md)

[Aktualizace obrázku: Začínáme s použitím agenta pro reference na simulátor Ubuntu (18,04 x64)](device-update-simulator.md)

[Aktualizace balíčku: Začínáme pomocí agenta balíčku Ubuntu Server 18,04 x64](device-update-ubuntu-agent.md)

