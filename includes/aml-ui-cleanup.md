---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 05/06/2019
ms.openlocfilehash: 623e993dfbe6bbb3297fa6470865ab1a04f55b37
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66123163"
---
>[!IMPORTANT]
>Splnění požadavků pro ostatní kurzy služby Azure Machine Learning a články s postupy můžete použít prostředky, které jste vytvořili.


### <a name="delete-everything"></a>Odstranit vše

Pokud nemáte v úmyslu použít cokoli, co jste vytvořili, odstraníte celou skupinu prostředků, tak nebudou účtovat žádné poplatky:

1. Na webu Azure Portal, vyberte **skupiny prostředků** na levé straně okna.
 
   ![Odstranění skupiny prostředků na webu Azure Portal](./media/aml-ui-cleanup/delete-resources.png)

1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili.

1. Na pravé straně okna, vyberte tlačítko se třemi tečkami ( **...** ).

1. Vyberte **Odstranit skupinu prostředků**.

Odstraněním skupiny prostředků se odstraní také všechny prostředky, které jste vytvořili v vizuální rozhraní.  

### <a name="delete-only-the-compute-target"></a>Odstranit pouze cílové výpočetní prostředí

Cílové výpočetní prostředí, který jste vytvořili v tomto poli *automaticky pravidla automatického škálování provedou* na 0 uzlů, když se nepoužívá. Cílem je minimalizovat náklady. Pokud chcete odstranit cílové výpočetní prostředí, proveďte tyto kroky:

1. V [webu Azure portal](https://portal.azure.com), otevřete pracovní prostor.

    ![Odstranit cílové výpočetní prostředí](./media/aml-ui-cleanup/delete-compute-target.png)

1. V **Compute** části pracovního prostoru, vyberte prostředek.

1. Vyberte **Odstranit**.

### <a name="delete-individual-assets"></a>Odstranit jednotlivé prostředky

Ve vizuální rozhraní, ve které jste vytvořili experimentu, odstranit jednotlivé prostředky tak, že je vyberete a pak vyberete **odstranit** tlačítko.

![Odstranit experimentů](./media/aml-ui-cleanup/delete-experiment.png)
