---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 2ae72045ae18d84eac2a6d619d94e3a9e49415ae
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "67175005"
---
## <a name="issue-custom-image-provisioning-errors"></a>Problém: Vlastní obrázek; zřizování chyb
Chyby zřizování vznikají, pokud nahrajete nebo zachytíte zobecněnou image virtuálního zařízení jako specializovanou image virtuálního uživatele nebo naopak. První způsobí chybu časového času zřizování a druhý způsobí selhání zřizování. Chcete-li nasadit vlastní bitovou kopii bez chyb, musíte zajistit, aby se typ bitové kopie během procesu sběru nezměnil.

V následující tabulce jsou uvedeny možné kombinace generalizovaných a specializovaných bitových kopií, typ chyby, se kterými se setkáte, a co je třeba udělat pro opravu chyb.

