---
title: Vytvoření privátního cloudu řešení Azure VMware
description: Postup vytvoření privátního cloudu řešení Azure VMware pomocí Azure Portal.
ms.topic: include
ms.date: 02/17/2021
ms.openlocfilehash: 983dccfaa9ea43955bfecc68bbbe432c579d51d1
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653125"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. Přihlaste se na [Azure Portal](https://portal.azure.com).

1. Vyberte **vytvořit nový prostředek**. Do textového pole **Hledat na Marketplace** zadejte `Azure VMware Solution` a ze seznamu vyberte **Řešení Azure VMware** . V okně **Řešení Azure VMware** vyberte **vytvořit**.

1. Na kartě **základy** zadejte hodnoty polí. V následující tabulce jsou uvedeny vlastnosti polí.

   | Pole   | Hodnota  |
   | ---| --- |
   | **Předplatné** | Předplatné, které plánujete použít pro nasazení.|
   | **Skupina prostředků** | Skupina prostředků pro prostředky privátního cloudu. |
   | **Umístění** | Vyberte umístění, jako je například **východní USA**.|
   | **Název prostředku** | Název vašeho privátního cloudu řešení Azure VMware. |
   | **SKU** | Vyberte následující hodnotu SKU: AV36 |
   | **Hostitelé** | Počet hostitelů, kteří mají být přidáni do clusteru privátního cloudu. Výchozí hodnota je 3, což může být vyvoláno nebo sníženo po nasazení.  |
   | **heslo správce vCenter** | Zadejte heslo správce cloudu. |
   | **Heslo správce NSX-T** | Zadejte heslo správce NSX-T. |
   | **Blok adresy** | Zadejte blok IP adres pro síť CIDR privátního cloudu, například 10.175.0.0/22. |
   | **Virtual Network** | Vyberte Virtual Network nebo vytvořte novou pro privátní cloud řešení Azure VMware.  |

   :::image type="content" source="../media/tutorial-create-private-cloud/create-private-cloud.png" alt-text="Na kartě základy zadejte hodnoty polí." border="true":::

1. Po dokončení vyberte **zkontrolovat + vytvořit**. Na další obrazovce ověřte zadané informace. Pokud jsou informace správné, vyberte **vytvořit**.

   > [!NOTE]
   > Tento krok trvá zhruba dvě hodiny. 

1. Ověřte, že nasazení proběhlo úspěšně. Přejděte do skupiny prostředků, kterou jste vytvořili, a vyberte svůj privátní cloud.  Po dokončení nasazení se zobrazí stav **dokončeno** . 

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Ověřte, že nasazení proběhlo úspěšně." border="true":::