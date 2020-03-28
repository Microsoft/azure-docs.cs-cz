---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "68423566"
---
Všimněte si jezdce **Prahová hodnota pravděpodobnosti** v levém podokně karty **Výkon.** To je úroveň spolehlivosti, že předpověď musí mít, aby byly považovány za správné (pro účely výpočtu přesnosti a odvolání). 

Při interpretaci předpověď volání s vysokou prahovou hodnotou pravděpodobnosti mají tendenci&mdash;vracet výsledky s vysokou přesností na úkor odvolání zjištěné klasifikace jsou správné, ale mnoho zůstávají nezjištěna. Prahová hodnota s nízkou&mdash;pravděpodobností provádí opačnou většinu skutečných klasifikací, ale v rámci této sady je více falešných poplachů. S ohledem na to byste měli nastavit prahovou hodnotu pravděpodobnosti podle konkrétních potřeb projektu. Později, když obdržíte výsledky předpovědi na straně klienta, měli byste použít stejnou hodnotu prahovou hodnotu pravděpodobnosti, jako u vás.