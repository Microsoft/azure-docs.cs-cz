---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/07/2019
ms.author: alkohli
ms.openlocfilehash: ad7ceffed61665a729d4391b5f0ff2935375c253
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55967171"
---
Při jejich přesunu do Azure se vztahují následující upozornění na data.

- Doporučujeme vám, že více než jedno zařízení by neměl zapisovat do stejného kontejneru.
- Pokud máte existující objekt Azure (například soubor nebo objekt blob) v cloudu se stejným názvem jako objekt, který je kopírování, bude zařízení přepsat soubor v cloudu.
- Hierarchii prázdný adresář (bez jakékoli soubory) vytvořené v rámci sdílení složek nebylo odesláno na kontejnery objektů blob.
- Pro velké soubory doporučujeme použít příkaz robocopy, protože opakování operace kopírování pro občasné chyby.
