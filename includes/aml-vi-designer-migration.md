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
ms.openlocfilehash: e3cb977871af2e6cd7a59dd48505090dd29e8a76
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/02/2020
ms.locfileid: "75541800"
---
1. Přihlaste se k [Azure Machine Learning Studiu](https://ml.azure.com).

1. Upgradujte pracovní prostor na Enterprise Edition.

    Po upgradu budou všechny experimenty vizuálního rozhraní převedeny na koncepty kanálu v návrháři.
    
    > [!NOTE]
    > Nemusíte upgradovat na edici Enterprise, aby bylo možné převést webové služby vizuálního rozhraní na koncové body v reálném čase.
    
1. Pokud chcete zobrazit seznam konceptů kanálu, vyberte v pracovním prostoru část návrháře. 
    
    Převedené webové služby se dají najít tak, že přejdete na **koncové**body koncových bodů  >  **v reálném čase**.

1. Vyberte koncept kanálu, který chcete otevřít.

    V případě chyby během procesu převodu se zobrazí chybová zpráva s pokyny k vyřešení problému. 

### <a name="known-issues"></a>Známé problémy

 Níže jsou známé problémy s migrací, které je potřeba řešit ručně:

- **Importovat data** nebo **exportovat datové** moduly
        
    Pokud v experimentu máte modul **importu dat** nebo **exportu dat** , budete muset zdroj dat aktualizovat tak, aby používal úložiště dat. Informace o tom, jak vytvořit úložiště dat, najdete v tématu [Jak získat přístup k datům ve službě Azure Storage](../articles/machine-learning/how-to-access-data.md). Informace o vašem účtu cloudového úložiště se přidaly do komentářů k vašemu usnadnění **importu dat** nebo modulu **exportu dat** . 
      