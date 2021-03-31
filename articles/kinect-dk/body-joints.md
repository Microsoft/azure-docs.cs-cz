---
title: Spoje sledování textu v Azure Kinect
description: Seznamte se s tělem, spoji, společnými souřadnicemi a společnou hierarchií v Azure Kinect DK.
author: qm13
ms.author: quentinm
ms.reviewer: cedmonds, abalan
ms.prod: kinect-dk
ms.date: 06/26/2019
ms.topic: conceptual
keywords: Kinect, přenos, tělo, sledování, společné, hierarchie, kosti, připojení
ms.openlocfilehash: 4cf6ac13a93d0674f9fa144abcc3153a2d7c3350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "85277204"
---
# <a name="azure-kinect-body-tracking-joints"></a>Spoje sledování textu v Azure Kinect

Sledování textu v Azure Kinect může sledovat několik lidských těl současně. Každý text obsahuje ID pro dočasnou korelaci mezi snímky a koosnovou kinematiky. Počet subjektů zjištěných v každém snímku lze získat pomocí `k4abt_frame_get_num_bodies()` .

## <a name="joints"></a>Spár

Společná poloha a orientace jsou odhady vzhledem k referenčnímu obrazu senzoru globální hloubky. Pozice je určena v milimetrech. Orientace se vyjadřuje jako normalizovaná Quaternion.

## <a name="joint-coordinates"></a>Společné souřadnice

Poloha a orientace každého společného formuláře se svým vlastním systémem souřadnicového systému. Všechny společné systémy souřadnic jsou absolutní souřadnice souřadnic, které odpovídají souřadnicovým systémům hloubkové kamery.

> [!NOTE]
> Společné souřadnice jsou v orientaci osy. Orientace osy se běžně používá u komerčních avatary, herních strojů a softwaru pro vykreslování. Použití orientace osy zjednodušuje zrcadlení pohybů, např. vyvolání obou zbraní o 20 stupňů.

![Společné souřadnice](./media/concepts/joint-coordinates.png)

## <a name="joint-hierarchy"></a>Společná hierarchie

Kostra zahrnuje 32 spojů se společným hierarchií od středu těla k okrajům. Každé připojení (kostní) propojuje nadřazený spoj s podřízeným kloubem. Obrázek znázorňuje společná umístění a spojení vzhledem k lidskému obsahu.

![Společná hierarchie](./media/concepts/joint-hierarchy.png)

V následující tabulce je uveden výčet standardních společných připojení.

|Index |Název společného     | Nadřazený společný   |
|------|---------------|----------------|
| 0    |PÁNVe         | -              |
| 1    |SPINE_NAVAL    | PÁNVe         |
| 2    |SPINE_CHEST    | SPINE_NAVAL    |
| 3    |ZÚŽEN           | SPINE_CHEST    |
| 4    |CLAVICLE_LEFT  | SPINE_CHEST    |
| 5    |SHOULDER_LEFT  | CLAVICLE_LEFT  |
| 6    |ELBOW_LEFT     | SHOULDER_LEFT  |
| 7    |WRIST_LEFT     | ELBOW_LEFT     |
| 8    |HAND_LEFT      | WRIST_LEFT     |
| 9    |HANDTIP_LEFT   | HAND_LEFT      |
| 10   |THUMB_LEFT     | WRIST_LEFT     |
| 11   |CLAVICLE_RIGHT | SPINE_CHEST    |
| 12   |SHOULDER_RIGHT | CLAVICLE_RIGHT |
| 13   |ELBOW_RIGHT    | SHOULDER_RIGHT |
| 14   |WRIST_RIGHT    | ELBOW_RIGHT    |
| 15   |HAND_RIGHT     | WRIST_RIGHT    |
| 16   |HANDTIP_RIGHT  | HAND_RIGHT     |
| 17   |THUMB_RIGHT    | WRIST_RIGHT    |
| 18   |HIP_LEFT       | PÁNVe         |
| 19   |KNEE_LEFT      | HIP_LEFT       |
| 20   |ANKLE_LEFT     | KNEE_LEFT      |
| 21   |FOOT_LEFT      | ANKLE_LEFT     |
| 22   |HIP_RIGHT      | PÁNVe         |
| 23   |KNEE_RIGHT     | HIP_RIGHT      |
| 24   |ANKLE_RIGHT    | KNEE_RIGHT     |
| 25   |FOOT_RIGHT     | ANKLE_RIGHT    |
| 26   |HEAD           | ZÚŽEN           |
| 27   |ŽLUTÝ           | HEAD           |
| 28   |EYE_LEFT       | HEAD           |
| 29   |EAR_LEFT       | HEAD           |
| 30   |EYE_RIGHT      | HEAD           |
| 31   |EAR_RIGHT      | HEAD           |

## <a name="next-steps"></a>Další kroky

[Indexová mapa sledování pohybu těla](body-index-map.md)
