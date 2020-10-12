---
title: Nasazení služby Azure VMware Solution
description: Postup nasazení řešení Azure VMware pomocí Azure Portal.
ms.topic: include
ms.date: 09/28/2020
ms.openlocfilehash: 4d0881721cd8c13d1b6c9fb3a29e4cdb6d6a753f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "91578307"
---
<!-- Used in deploy-azure-vmware-solution.md and tutorial-create-private-cloud.md -->

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).

1. Vyberte **vytvořit nový prostředek**. Do textového pole **Hledat na Marketplace** zadejte `Azure VMware Solution` a ze seznamu vyberte **Řešení Azure VMware** . V okně **Řešení Azure VMware** vyberte **vytvořit**.

1. Na kartě **základy** zadejte hodnoty polí. V následující tabulce jsou uvedeny vlastnosti polí.

   | Pole   | Hodnota  |
   | ---| --- |
   | **Předplatné** | Předplatné, které plánujete použít pro nasazení.|
   | **Skupina prostředků** | Skupina prostředků pro prostředky privátního cloudu. |
   | **Umístění** | Vyberte umístění, jako je například **východní USA**.|
   | **Název prostředku** | Název vašeho privátního cloudu řešení Azure VMware. |
   | **Skladová jednotka (SKU)** | Vyberte následující hodnotu SKU: AV36 |
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

   :::image type="content" source="../media/tutorial-create-private-cloud/validate-deployment.png" alt-text="Na kartě základy zadejte hodnoty polí." border="true":::