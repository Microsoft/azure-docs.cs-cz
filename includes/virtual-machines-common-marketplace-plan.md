---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 10/09/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: 4d2235eaea457c89d01a632afa5dd5a862bec344
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67174999"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Nasazení image s podmínkami Marketplace

Některé imagí virtuálních počítačů na webu Azure Marketplace máte další licenci, a podmínky nákupu, musí přijmout, než bude možné nasadit prostřednictvím kódu programu.  

Nasazení virtuálního počítače z těchto bitové kopie, musíte přijmout podmínky na obrázku i povolit programové nasazení. Pouze budete muset provést jednou na jedno předplatné. Následně pokaždé, když nasazujete virtuální počítač prostřednictvím kódu programu z této image budete taky muset zadat *koupit plán* parametry.

V následujících částech zobrazit postup:

* Zjistěte, zda má Marketplace image další licenční podmínky 
* Přijměte podmínky prostřednictvím kódu programu
* Zadejte parametry nákupu plánu, při nasazení virtuálního počítače prostřednictvím kódu programu

