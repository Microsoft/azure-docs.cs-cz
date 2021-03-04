---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 44fd3b2bc89ac53e7a7c0293961098509d3f5c76
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "102044578"
---
### <a name="set-up-the-windows-device-portal"></a>Nastavení portálu pro zařízení s Windows

Pokud se chcete připojit k HoloLens přes Wi-Fi, postupujte takto:

1. Nejdřív [Připojte HoloLens k Wi-Fi](https://docs.microsoft.com/hololens/hololens-network).

2. Pak na zařízení Získejte IP adresu v části **nastavení > Network & Internet > Wi-Fi > Upřesnit možnosti**.

3. Z webového prohlížeče na počítači, přejít na `https://<YOUR_HOLOLENS_IP_ADDRESS>` . V prohlížeči se zobrazí následující zpráva: "došlo k potížím s certifikátem zabezpečení tohoto webu". Tato zpráva je způsobená tím, že certifikát vystavený pro portál zařízení je certifikát podepsaný svým držitelem. Chybu certifikátu můžete ignorovat a pokračovat.

Další informace o nastavení portálu pro zařízení s Windows najdete [tady](https://docs.microsoft.com/windows/mixed-reality/using-the-windows-device-portal) .
