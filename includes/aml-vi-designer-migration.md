---
title: zahrnout soubor
description: zahrnout soubor
services: machine-learning
ms.service: machine-learning
ms.custom: include file
ms.topic: include
author: xiaoharper
ms.author: zhanxia
ms.date: 10/18/2019
ms.openlocfilehash: 08833f869a7838fe893b8e941072078f89033c2b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73517849"
---
1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com).

1. Upgradujte pracovní prostor na Enterprise Edition.

    Po upgradu budou všechny experimenty vizuálního rozhraní převedeny na koncepty kanálu v návrháři.
    
    > [!NOTE]
    > Nemusíte upgradovat na edici Enterprise, aby bylo možné převést webové služby vizuálního rozhraní na koncové body v reálném čase.
    
1. Pokud chcete zobrazit seznam konceptů kanálu, vyberte v pracovním prostoru část návrháře. 
    
    Převedené webové služby můžete najít tak, že přejdete do **koncových bodů** > **koncových bodů v reálném čase**.

1. Vyberte koncept kanálu, který chcete otevřít.

    V případě chyby během procesu převodu se zobrazí chybová zpráva s pokyny k vyřešení problému. 

### <a name="known-issues"></a>Známé problémy

 Níže jsou známé problémy s migrací, které je potřeba řešit ručně:

- **Importovat data** nebo **exportovat datové** moduly
        
    Pokud v experimentu máte modul **importu dat** nebo **exportu dat** , budete muset zdroj dat aktualizovat tak, aby používal úložiště dat. Informace o tom, jak vytvořit úložiště dat, najdete v tématu [Jak získat přístup k datům ve službě Azure Storage](../articles/machine-learning/service/how-to-access-data.md). Informace o vašem účtu cloudového úložiště se přidaly do komentářů k vašemu usnadnění **importu dat** nebo modulu **exportu dat** . 
      