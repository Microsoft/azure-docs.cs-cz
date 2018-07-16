---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines-windows, virtual-machines-linux
author: dlepow
ms.service: multiple
ms.topic: include
ms.date: 02/28/2018
ms.author: danlep
ms.custom: include file
ms.openlocfilehash: a3c5290eb0179fe5842c495c2e08f22580d02bda
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38941020"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Nasazení image s podmínkami Marketplace

Určité Image virtuálního počítače na webu Azure Marketplace máte další licenci, a podmínky nákupu, musí přijmout, než bude možné nasadit prostřednictvím kódu programu.  

Nasazení virtuálního počítače z těchto bitové kopie, musíte přijmout podmínky na obrázku a povolit programové nasazení. Stačí provést jednou v rámci vašeho předplatného. Potom pokaždé, když nasazujete virtuální počítač prostřednictvím kódu programu z image, musíte také zadat *koupit plán* parametry.

V následujících částech zobrazit postup:

* Zjistěte, jestli Marketplace image má další licenční podmínky 
* Přijměte podmínky prostřednictvím kódu programu
* Zadejte parametry nákupu plánu, při nasazení virtuálního počítače prostřednictvím kódu programu

