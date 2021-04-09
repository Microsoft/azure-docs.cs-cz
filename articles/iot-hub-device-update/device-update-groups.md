---
title: Vysvětlení aktualizace zařízení pro skupiny zařízení Azure IoT Hub | Microsoft Docs
description: Pochopení způsobu používání skupin zařízení.
author: aysancag
ms.author: aysancag
ms.date: 2/09/2021
ms.topic: conceptual
ms.service: iot-hub-device-update
ms.openlocfilehash: 18388f067ccb5b8a8876aeae685664694c207613
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "101679228"
---
# <a name="device-groups"></a>Skupiny zařízení

Skupina zařízení je kolekce zařízení. Skupiny zařízení poskytují způsob, jak škálovat nasazení na mnoho zařízení. Každé zařízení v jednu chvíli patří přesně k jedné skupině zařízení.
Můžete si vytvořit několik skupin zařízení, abyste mohli uspořádat svoje zařízení. Contoso může například použít skupinu zařízení "let" pro zařízení v její testovací laboratoři a skupinu zařízení "Evaluation" pro zařízení, která jeho pole používá v provozním centru. Kromě toho může contoso zvolit seskupení svých produkčních zařízení na základě jejich geografických oblastí, aby mohli aktualizovat zařízení podle plánu, který se zarovnává s jejich místními časovými pásmy. 


## <a name="using-device-twin-tag-for-device-group-creation"></a>Použití značky zdvojeného zařízení pro vytvoření skupiny zařízení

Značky s dvojitým zařízením umožňují uživatelům seskupovat zařízení. Aby se zařízení mohla seskupovat, musí mít ADUGroup klíč a hodnotu v jejich nevlákenných zařízeních.

### <a name="device-twin-tag-format"></a>Nepřesný formát značek zařízení

```markdown
"tags": {
  "ADUGroup": "<CustomTagValue>"
}
```


## <a name="uncategorized-device-group"></a>Skupina zařízení Nezařazeno do kategorie

Nezařazeno do kategorie je rezervované slovo, které se používá k seskupení zařízení, která:
- Nepoužívejte značku ADUGroup zařízení s dvojitou značkou.
- Mít neADUGroupelné značky zařízení, ale není vytvořená skupina s tímto názvem skupiny.

Podívejte se například na zařízení s dvojitými značkami jejich zařízení:

```markdown
"deviceId": "Device1",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device2",
"tags": {
  "ADUGroup": "Group1"
}
```

```markdown
"deviceId": "Device3",
"tags": {
  "ADUGroup": "Group2"
}
```

```markdown
"deviceId": "Device4",
```

Níže jsou uvedená zařízení a možné skupiny, které jsou pro ně možné vytvořit.

|Zařízení |Group (Skupina)  |
|-----------|--------------|
|Zařízení1    |Group1|
|Device2    |Group1|
|Device3    |Skupina2|
|Device4    |Nezařazeno do kategorie|



## <a name="next-steps"></a>Další kroky

[Vytvořit skupinu zařízení](./create-update-group.md)
