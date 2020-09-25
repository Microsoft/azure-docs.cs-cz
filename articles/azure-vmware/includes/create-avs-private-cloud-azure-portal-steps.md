---
title: Nasazení řešení Azure VMware
description: Postup nasazení řešení Azure VMware pomocí Azure Portal.
ms.topic: include
ms.date: 09/21/2020
ms.openlocfilehash: 4fa2925cf284871c4a168d2b69d4fbd3a81de878
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91254622"
---
<!-- Used in avs-deployment.md and tutorial-create-private-cloud.md -->

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).

1. Vyberte **vytvořit nový prostředek**. Do textového pole **Hledat na Marketplace** zadejte `Azure VMware Solution` a ze seznamu vyberte **Řešení Azure VMware** . V okně **Řešení Azure VMware** vyberte **vytvořit** .

1. Na kartě **základy** zadejte hodnoty polí. V následující tabulce jsou uvedeny vlastnosti polí.

   | Pole   | Hodnota  |
   | ---| --- |
   | **Předplatné** | Předplatné, které plánujete použít pro nasazení.|
   | **Skupina prostředků** | Skupina prostředků pro prostředky privátního cloudu. |
   | **Umístění** | Vyberte umístění, jako je například **východní USA**.|
   | **Název prostředku** | Název vašeho privátního cloudu řešení Azure VMware. |
   | **Skladová jednotka (SKU)** | Vyberte následující hodnotu SKU: AV36 |
   | **Dvou** | Počet hostitelů, kteří mají být přidáni do clusteru privátního cloudu. Výchozí hodnota je 3, což může být vyvoláno nebo sníženo po nasazení.  |
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