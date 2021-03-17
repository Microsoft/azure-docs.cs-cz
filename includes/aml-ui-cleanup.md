---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: sgilley
ms.author: sgilley
ms.date: 11/06/2019
ms.openlocfilehash: 754c9799ed4c2fd90cbcf1e9717b0be21edd54cb
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "75659864"
---
>[!IMPORTANT]
>Prostředky, které jste vytvořili jako předpoklady, můžete použít pro jiné kurzy Azure Machine Learning a články s postupy.

### <a name="delete-everything"></a>Odstranit vše

Pokud neplánujete použít nic, co jste vytvořili, odstraňte celou skupinu prostředků, aby se vám neúčtovaly žádné poplatky.

1. V Azure Portal na levé straně okna vyberte **skupiny prostředků** .
 
   ![Odstranění skupiny prostředků na webu Azure Portal](./media/aml-ui-cleanup/delete-resources.png)

1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili.

1. Vyberte **Odstranit skupinu prostředků**.

Odstraněním skupiny prostředků se odstraní také všechny prostředky, které jste vytvořili v návrháři. 

### <a name="delete-individual-assets"></a>Odstranění jednotlivých assetů

V návrháři, kde jste vytvořili experiment, odstraňte jednotlivé prostředky tak, že je vyberete a pak vyberete tlačítko **Odstranit** .

Cíl výpočtů, který jste zde vytvořili, *automaticky automaticky škáluje* na nula uzlů, pokud se nepoužívá. Tato akce se provádí kvůli minimalizaci nákladů.Pokud chcete odstranit cíl výpočtů, proveďte tyto kroky:

![Odstranit prostředky](./media/aml-ui-cleanup/delete-asset.png)

Můžete zrušit registraci datových sad z pracovního prostoru tak, že vyberete každou datovou sadu a vyberete **zrušit registraci**.

![Zrušit registraci datové sady](./media/aml-ui-cleanup/unregister-dataset1225.png)

Datovou sadu odstraníte tak, že přejdete do účtu úložiště pomocí Azure Portal nebo Průzkumník služby Azure Storage a ručně odstraníte tyto prostředky.


