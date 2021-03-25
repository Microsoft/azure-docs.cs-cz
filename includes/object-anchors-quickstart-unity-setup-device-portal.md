---
author: craigktreasure
ms.service: azure-object-anchors
ms.topic: include
ms.date: 04/03/2020
ms.author: crtreasu
ms.openlocfilehash: 81c4e95660eb2875cd1b03bdfa670aeabadedc3f
ms.sourcegitcommit: ed7376d919a66edcba3566efdee4bc3351c57eda
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2021
ms.locfileid: "105105017"
---
### <a name="set-up-the-windows-device-portal"></a>Nastavení portálu pro zařízení s Windows

Pokud se chcete připojit k HoloLens přes Wi-Fi, postupujte takto:

1. Nejdřív [Připojte HoloLens k Wi-Fi](/hololens/hololens-network).

2. Pak na zařízení Získejte IP adresu v části **nastavení > Network & Internet > Wi-Fi > Upřesnit možnosti**.

3. Z webového prohlížeče na počítači, přejít na `https://<YOUR_HOLOLENS_IP_ADDRESS>` . V prohlížeči se zobrazí následující zpráva: "došlo k potížím s certifikátem zabezpečení tohoto webu". Tato zpráva je způsobená tím, že certifikát vystavený pro portál zařízení je certifikát podepsaný svým držitelem. Chybu certifikátu můžete ignorovat a pokračovat.

Další informace o nastavení portálu pro zařízení s Windows najdete [tady](/windows/mixed-reality/using-the-windows-device-portal) .