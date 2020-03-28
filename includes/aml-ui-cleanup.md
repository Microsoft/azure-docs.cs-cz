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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75659864"
---
>[!IMPORTANT]
>Můžete použít prostředky, které jste vytvořili jako předpoklady pro další kurzy Azure Machine Learning a články s postupy.

### <a name="delete-everything"></a>Odstranit vše

Pokud neplánujete použít nic, co jste vytvořili, odstraňte celou skupinu prostředků, abyste vám nevznikly žádné poplatky.

1. Na webu Azure Portal vyberte **skupiny prostředků** na levé straně okna.
 
   ![Odstranění skupiny prostředků na webu Azure Portal](./media/aml-ui-cleanup/delete-resources.png)

1. V seznamu vyberte skupinu prostředků, kterou jste vytvořili.

1. Vyberte **Odstranit skupinu prostředků**.

Odstraněním skupiny prostředků také odstraníte všechny prostředky, které jste vytvořili v návrháři. 

### <a name="delete-individual-assets"></a>Odstranění jednotlivých datových zdrojů

V návrháři, kde jste experiment vytvořili, odstraňte jednotlivé datové zdroje tak, že je vyberete a pak vyberete tlačítko **Odstranit.**

Výpočetní cíl, který jste zde *vytvořili, se automaticky automaticky škáluje* na nulové uzly, když se nepoužívá. Tato akce je provedena za účelem minimalizace poplatků.Pokud chcete odstranit cíl výpočtu, postupujte takto:

![Odstranění datových zdrojů](./media/aml-ui-cleanup/delete-asset.png)

Můžete zrušit registraci datových sad z pracovního prostoru výběrem jednotlivých datových sad a výběrem **možnosti Zrušit registraci**.

![Zrušení registrace datové sady](./media/aml-ui-cleanup/unregister-dataset1225.png)

Pokud chcete datovou sadu odstranit, přejděte na účet úložiště pomocí portálu Azure nebo Průzkumníka úložiště Azure a ručně odstraňte tyto prostředky.


