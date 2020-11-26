---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 08/30/2020
ms.author: alkohli
ms.openlocfilehash: 0b6a6cbf51ef2ff1f1ef53b53a2b84c7a4f9510d
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96185527"
---
1. V [Azure Portal](https://portal.azure.com/)vyberte svůj prostředek Azure Stack Edge a pak přejít na **Přehled**. Vaše zařízení by mělo být online.

2. Na panelu příkazů zařízení vyberte **+ Přidat účet úložiště** . 

   ![Přidat účet úložiště](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-1.png)

3. V podokně **Přidat účet úložiště Edge** určete následující nastavení:

    a. Jedinečný název účtu úložiště Edge na vašem zařízení. Názvy účtů úložiště můžou obsahovat jenom malá písmena a číslice. Speciální znaky nejsou povoleny. Název účtu úložiště musí být v rámci zařízení (ne přes zařízení) jedinečný.

    b. Volitelný popis informací o datech, která účet úložiště uchovává.  
    
    c. Ve výchozím nastavení se hraniční účet úložiště mapuje na účet Azure Storage v cloudu a data z účtu úložiště se automaticky přiodesílají do cloudu. Zadejte účet služby Azure Storage, na který je namapován váš účet Edge Storage.  

    d. Dále vytvořte nový kontejner nebo vyberte z existujícího kontejneru v účtu úložiště Azure. Všechna data ze zařízení, která jsou zapsaná do hraničního účtu úložiště, se automaticky nahrají do vybraného kontejneru úložiště v namapovaném Azure Storage účtu.

    <!--![Add a storage account](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-2.png)-->

    e. Po zadání všech možností účtu úložiště vyberte **Přidat** a vytvořte účet hraničního úložiště. Po úspěšném vytvoření účtu hraničního úložiště se zobrazí oznámení. Nový hraniční účet úložiště se pak zobrazí v seznamu účtů úložiště v Azure Portal. 

    
4. Pokud vyberete tento nový účet úložiště a přejdete na **přístupové klíče**, můžete najít koncový bod služby BLOB Service a odpovídající název účtu úložiště. Tyto informace zkopírujte spolu s přístupovými klíči, abyste se mohli připojit k hraničnímu účtu úložiště.

    ![Přidat účet úložiště 2](media/azure-stack-edge-gateway-add-storage-account/add-storage-account-4.png)

    Přístupové klíče získáte tak, že se [připojíte k místním rozhraním API zařízení pomocí Azure Resource Manager](../articles/databox-online/azure-stack-edge-j-series-connect-resource-manager.md). 
