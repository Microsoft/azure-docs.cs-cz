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
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "71174938"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Nasazení image s podmínkami Marketplace

Některé image virtuálních počítačů v Azure Marketplace mají další licenční a nákupní podmínky, které musíte přijmout, než je můžete nasadit programově.  

Pokud chcete nasadit virtuální počítač z takové image, musíte přijmout podmínky image a povolit programové nasazení. To je potřeba udělat jenom jednou pro každé předplatné. Potom budete muset pokaždé, když nasadíte virtuální počítač programově z image, taky zadat parametry *plánu nákupu* .

Následující části ukazují, jak:

* Zjistěte, jestli má image Marketplace další licenční smlouvy. 
* Přijměte podmínky programově
* Zadání parametrů plánu nákupu při programovém nasazení virtuálního počítače

