---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 10/22/2019
ms.openlocfilehash: 5a66212122745d0f4426e48e9487e9d674cec53f
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73489932"
---
>[!IMPORTANT]
>Prostředky, které jste vytvořili jako předpoklady, můžete použít pro jiné kurzy Azure Machine Learning a články s postupy.

### <a name="delete-everything"></a>Odstranit vše

Pokud neplánujete použít nic, co jste vytvořili, odstraňte celou skupinu prostředků, aby se vám neúčtovaly žádné poplatky:

1. V Azure Portal na levé straně okna vyberte **skupiny prostředků** .
 
   ![Odstranění skupiny prostředků na webu Azure Portal](./media/aml-ui-cleanup/delete-resources.png)

1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili.

1. Na pravé straně okna vyberte tlačítko se třemi tečkami ( **...** ).

1. Vyberte **Odstranit skupinu prostředků**.

Odstraněním skupiny prostředků se odstraní také všechny prostředky, které jste vytvořili v návrháři.  

### <a name="delete-only-the-compute-target"></a>Odstranit jenom výpočetní cíl

Cíl výpočtů, který jste zde vytvořili, *automaticky automaticky škáluje* na nula uzlů, pokud se nepoužívá. Tím se minimalizují poplatky. Pokud chcete odstranit cíl výpočtů, proveďte tyto kroky:

1. V [Azure Portal](https://portal.azure.com)otevřete pracovní prostor.

    ![Odstranit cíl výpočtů](./media/aml-ui-cleanup/delete-compute-target.png)

1. V části **výpočty** v pracovním prostoru vyberte prostředek.

1. Vyberte **Odstranit**.

### <a name="delete-individual-assets"></a>Odstranění jednotlivých assetů

V návrháři, kde jste vytvořili experiment, odstraňte jednotlivé prostředky tak, že je vyberete a pak vyberete tlačítko **Odstranit** .

![Odstranit prostředky](./media/aml-ui-cleanup/delete-asset.png)

Z vašeho pracovního prostoru můžete zrušit registraci datových sad tak, že vyberete každou datovou sadu a vyberete **zrušit registraci**.

![Zrušit registraci datové sady](./media/aml-ui-cleanup/unregister-dataset.png)


