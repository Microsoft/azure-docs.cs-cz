---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 02/21/2019
ms.author: alkohli
ms.openlocfilehash: 8c87e14071b3bb40421ab655c172df739570e295
ms.sourcegitcommit: a4efc1d7fc4793bbff43b30ebb4275cd5c8fec77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2019
ms.locfileid: "56659978"
---
Při jejich přesunu do Azure se vztahují následující upozornění na data.

- Doporučujeme vám, že více než jedno zařízení by neměl zapisovat do stejného kontejneru.
- Pokud máte existující objekt Azure (například soubor nebo objekt blob) v cloudu se stejným názvem jako objekt, který je kopírování, bude zařízení přepsat soubor v cloudu.
- Hierarchii prázdný adresář (bez jakékoli soubory) vytvořené v rámci sdílení složek nebylo odesláno na kontejnery objektů blob.
- Můžete zkopírovat data s využitím přetahování a vyřadit pomocí Průzkumníka souborů nebo pomocí příkazového řádku. Pokud se agregovaná velikost kopírování souborů je větší než 10 GB, doporučujeme že použít program hromadného kopírování například Robocopy nebo rsync. Nástrojů hromadné kopírování operaci kopírování pro občasné chyby a poskytuje tak dodatečnou odolnost.
- Sdílené složky přidružené kontejner úložiště Azure nahraje objekty BLOB, které se neshodují s typem objektů BLOB, které jsou definovány pro sdílené složky v době vytvoření, nejsou aktualizovány tyto objekty BLOB. Například vytvořit sdílenou složku objektů blob bloku v zařízení. Přidružení sdílené složky cloudového kontejneru, který obsahuje objekty BLOB stránky. Obnovte tuto sdílenou složku ke stažení souborů. Upravte některé aktualizovat soubory, které jsou už uložené jako objekty BLOB stránky v cloudu. Zobrazí se chyby nahrávání.
