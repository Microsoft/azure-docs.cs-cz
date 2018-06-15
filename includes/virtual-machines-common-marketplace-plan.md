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
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2018
ms.locfileid: "29743366"
---
## <a name="deploy-an-image-with-marketplace-terms"></a>Nasazení bitové kopie s podmínkami Marketplace.

Některé Image virtuálních počítačů v Azure Marketplace mají další licence a nákup podmínky, že musíte přijmout, než bude možné nasadit prostřednictvím kódu programu.  

K nasazení virtuálních počítačů z takových bitové kopie, musíte přijmout podmínky obrázku a povolit programové nasazení. Stačí tento jednou v rámci vašeho předplatného. Potom pokaždé, když nasazujete virtuální počítač z bitové kopie, prostřednictvím kódu programu budete taky muset zadat *koupit plán* parametry.

V následujících částech jsou zobrazit postup:

* Zjistěte, zda má image pořízenou prostřednictvím Marketplace další licenční podmínky 
* Přijměte podmínky prostřednictvím kódu programu
* Zadejte parametry plán nákupu při nasazení virtuálního počítače prostřednictvím kódu programu

