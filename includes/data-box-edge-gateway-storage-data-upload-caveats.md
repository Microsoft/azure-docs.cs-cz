---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/14/2019
ms.author: alkohli
ms.openlocfilehash: 8bc4b78d262cf5f30076e90b40b92c4f3237924a
ms.sourcegitcommit: d2329d88f5ecabbe3e6da8a820faba9b26cb8a02
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/16/2019
ms.locfileid: "56333968"
---
Při jejich přesunu do Azure se vztahují následující upozornění na data.

- Doporučujeme vám, že více než jedno zařízení by neměl zapisovat do stejného kontejneru.
- Pokud máte existující objekt Azure (například soubor nebo objekt blob) v cloudu se stejným názvem jako objekt, který je kopírování, bude zařízení přepsat soubor v cloudu.
- Hierarchii prázdný adresář (bez jakékoli soubory) vytvořené v rámci sdílení složek nebylo odesláno na kontejnery objektů blob.
- Pro velké soubory doporučujeme použít příkaz robocopy, protože opakování operace kopírování pro občasné chyby.
- Sdílené složky přidružené kontejner úložiště Azure nahraje objekty BLOB, které se neshodují s typem objektů BLOB, které jsou definovány pro sdílené složky v době vytvoření, nejsou aktualizovány tyto objekty BLOB. Například vytvořit sdílenou složku objektů blob bloku v zařízení. Přidružení sdílené složky cloudového kontejneru, který obsahuje objekty BLOB stránky. Obnovte tuto sdílenou složku ke stažení souborů. Upravte některé aktualizovat soubory, které jsou už uložené jako objekty BLOB stránky v cloudu. Zobrazí se chyby nahrávání.
