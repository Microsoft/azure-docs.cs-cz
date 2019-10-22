---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/16/2019
ms.openlocfilehash: 0071b0df2c2e173eced1722372f88b1de2708afa
ms.sourcegitcommit: e0e6663a2d6672a9d916d64d14d63633934d2952
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/21/2019
ms.locfileid: "72692393"
---
>[!IMPORTANT]
>Prostředky, které jste vytvořili jako předpoklady, můžete použít pro jiné kurzy Azure Machine Learning služby a články s postupy.

### <a name="delete-everything"></a>Odstranit vše

Pokud neplánujete použít nic, co jste vytvořili, odstraňte celou skupinu prostředků, aby se vám neúčtovaly žádné poplatky:

1. V Azure Portal na levé straně okna vyberte **skupiny prostředků** .
 
   ![Odstranění skupiny prostředků na webu Azure Portal](./media/aml-ui-cleanup/delete-resources.png)

1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili.

1. Na pravé straně okna vyberte tlačítko se třemi tečkami ( **...** ).

1. Vyberte **Odstranit skupinu prostředků**.

Odstraněním skupiny prostředků se odstraní také všechny prostředky, které jste vytvořili v rámci vizuálního rozhraní.  

### <a name="delete-only-the-compute-target"></a>Odstranit jenom výpočetní cíl

Cíl výpočtů, který jste zde vytvořili, *automaticky automaticky škáluje* na nula uzlů, pokud se nepoužívá. Tím se minimalizují poplatky. Pokud chcete odstranit cíl výpočtů, proveďte tyto kroky:

1. V [Azure Portal](https://portal.azure.com)otevřete pracovní prostor.

    ![Odstranit cíl výpočtů](./media/aml-ui-cleanup/delete-compute-target.png)

1. V části **výpočty** v pracovním prostoru vyberte prostředek.

1. Vyberte **Odstranit**.

### <a name="delete-individual-assets"></a>Odstranění jednotlivých assetů

Ve vizuálním rozhraní, kde jste vytvořili experiment, odstraňte jednotlivé prostředky tak, že je vyberete a pak vyberete tlačítko **Odstranit** . Z vašeho pracovního prostoru můžete zrušit registraci datových sad tak, že vyberete každou datovou sadu a vyberete **zrušit registraci**.

![Odstranit prostředky](./media/aml-ui-cleanup/delete-asset.png)
