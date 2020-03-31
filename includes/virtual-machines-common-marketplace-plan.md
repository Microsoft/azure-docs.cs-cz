---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: cynthn
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: 8e0e549f88caf4a541642bab77faf54b5e536b29
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "71174938"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Nasazení bitové kopie s podmínkami Marketplace

Některé image virtuálních počítačů na Azure Marketplace mají další licenční a nákupní podmínky, které musíte přijmout, než je budete moct nasadit programově.  

Chcete-li nasadit virtuální ho virtuálního počítače z takové image, budete muset přijmout podmínky image a povolit programové nasazení. To budete muset udělat jen jednou za předplatné. Potom pokaždé, když nasadíte virtuální ho susky programově z bitové kopie, budete také muset zadat parametry *plánu nákupu.*

Následující části ukazují, jak:

* Zjistěte, jestli má obrázek Marketplace další licenční podmínky 
* Přijmout termíny programově
* Při programovém nasazení virtuálního montovana zadejte parametry plánu nákupu.

