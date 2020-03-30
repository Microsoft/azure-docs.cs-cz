---
title: Vytvoření skupiny umístění bezkontaktní komunikace pomocí portálu
description: Přečtěte si, jak vytvořit skupinu umístění bezkontaktní komunikace pomocí portálu Azure.
services: virtual-machines
author: cynthn
manager: gwallace
ms.service: virtual-machines
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 10/30/2019
ms.author: cynthn
ms.openlocfilehash: 8512d9b701242dc686d49bbe56e8a2059f14ee3d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73180248"
---
# <a name="create-a-proximity-placement-group-using-the-portal"></a>Vytvoření skupiny umístění bezkontaktní komunikace pomocí portálu

Chcete-li získat virtuální chod co nejblíže a dosáhnout nejnižší možné latence, měli byste je nasadit v [rámci skupiny umístění bez kontaktní místo](co-location.md#proximity-placement-groups).

Skupina umístění bezkontaktní komunikace je logické seskupení, které se používá k zajištění, že výpočetní prostředky Azure jsou fyzicky umístěny blízko sebe. Skupiny umístění bezkontaktní komunikace jsou užitečné pro úlohy, kde je požadavek nízké latence.


## <a name="create-the-proximity-placement-group"></a>Vytvoření skupiny umístění bezkontaktní komunikace

1. Do hledání zadejte **skupinu umístění bezkontaktní** komunikace.
1. V části **Služby** ve výsledcích hledání vyberte **skupiny umístění Bezkontaktní umístění**.
1. Na stránce **Skupiny umístění bezkontaktníumístění** vyberte **Přidat**.
1. Na kartě **Základy** v části **Podrobnosti o projektu**zkontrolujte, jestli je vybráno správné předplatné.
1. Ve **skupině Prostředků** vyberte buď možnost Vytvořit **nový** pro vytvoření nové skupiny, nebo vyberte existující skupinu prostředků z rozevíracího přehledu.
1. V **oblasti** vyberte umístění, kde chcete vytvořit skupinu umístění bezkontaktní komunikace.
1. V **pouzdřování umístění název skupiny** zadejte název a pak vyberte **Zkontrolovat + vytvořit**.
1. Po průchodu ověření vyberte **Vytvořit** a vytvořte skupinu umístění bezkontaktní komunikace.

    ![Snímek obrazovky s vytvořením skupiny umístění bezkontaktní komunikace](./media/ppg/ppg.png)


## <a name="create-a-vm"></a>Vytvoření virtuálního počítače

1. Při vytváření virtuálního virtuálního montovana na portálu přejděte na kartu **Upřesnit.** 
1. Ve výběru **skupiny umístění Bezkontaktní** umístění vyberte správnou skupinu umístění. 

    ![Snímek obrazovky se skupinou umístění bezkontaktní komunikace při vytváření nového virtuálního virtuálního soudu na portálu](./media/ppg/vm-ppg.png)

1. Po dokončení provádění všech ostatních požadovaných výběrů vyberte **Zkontrolovat + vytvořit**.
1. Až projde ověřením, vyberte **Vytvořit** a nasaďte virtuální ho ve skupině umístění.




## <a name="next-steps"></a>Další kroky

Azure [PowerShell](proximity-placement-groups.md) můžete taky použít k vytvoření skupin umístění bezkontaktní komunikace.

