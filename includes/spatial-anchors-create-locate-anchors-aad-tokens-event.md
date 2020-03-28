---
ms.openlocfilehash: 7219a457a2631f9ff6beee06eff34bce0ff5a23f
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "67174709"
---
Stejně jako u přístupových tokenů, pokud není nastaven token Azure AD, musíte zpracovat událost TokenRequired nebo implementovat metodu tokenRequired v protokolu delegáta.

Událost můžete zpracovat synchronně nastavením vlastnosti argumentů události.
