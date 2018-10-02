---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 09/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: f9974aae1e5996ffeaa6cde690a5e10ccba4cc32
ms.sourcegitcommit: 7bc4a872c170e3416052c87287391bc7adbf84ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48019008"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Nasazení image s podmínkami Marketplace

Určité Image virtuálního počítače na webu Azure Marketplace máte další licenci, a podmínky nákupu, musí přijmout, než bude možné nasadit prostřednictvím kódu programu.  

Nasazení virtuálního počítače z těchto bitové kopie, musíte přijmout podmínky na obrázku a povolit programové nasazení. Stačí provést jednou v rámci vašeho předplatného. Potom pokaždé, když nasazujete virtuální počítač prostřednictvím kódu programu z image, musíte také zadat *koupit plán* parametry.

V následujících částech zobrazit postup:

* Zjistěte, jestli Marketplace image má další licenční podmínky 
* Přijměte podmínky prostřednictvím kódu programu
* Zadejte parametry nákupu plánu, při nasazení virtuálního počítače prostřednictvím kódu programu

