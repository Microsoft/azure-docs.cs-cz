---
author: PatrickFarley
ms.service: cognitive-services
ms.subservice: custom-vision
ms.topic: include
ms.date: 07/17/2019
ms.author: pafarley
ms.openlocfilehash: 07e7cc991f127bf4bb4f466c0108962786e45bce
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "96018347"
---
Všimněte si posuvníku **prahová hodnota pravděpodobnosti** v levém podokně karty **výkon** . Toto je úroveň jistoty, kterou předpovědi potřebuje mít, aby byla považována za správnou (za účelem výpočtu přesnosti a odvolání). 

Při interpretaci volání předpovědi s prahovou hodnotou vysoké pravděpodobnosti mají v úmyslu vracet výsledky s vysokou přesností na základě nákladů na odvolání &mdash; , které zjištěné klasifikace jsou správné, ale mnoho zůstává nerozpoznané. Prahová hodnota nízké pravděpodobnosti znamená, že je &mdash; v této sadě zjištěn opak, ale v rámci této sady existuje více falešně pozitivních hodnot. V takovém případě byste měli nastavit prahovou hodnotu pravděpodobnosti podle konkrétních potřeb vašeho projektu. Až budete později dostávat výsledky předpovědi na straně klienta, měli byste použít stejnou prahovou hodnotu pravděpodobnosti, jakou jste použili tady.