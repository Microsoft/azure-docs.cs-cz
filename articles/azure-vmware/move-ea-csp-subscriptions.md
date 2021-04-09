---
title: Přesunutí předplatných řešení Azure VMware pro EA a CSP
description: Přečtěte si, jak přesunout privátní cloud z jednoho předplatného do jiného. Pohyb je možné provést z různých důvodů, jako je fakturace.
ms.topic: how-to
ms.date: 03/15/2021
ms.openlocfilehash: 608f46dbd84d6bb899a3e7fcd1f8a63b3a5e85fb
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "103555387"
---
# <a name="move-ea-and-csp-azure-vmware-solution-subscriptions"></a>Přesunutí předplatných řešení Azure VMware pro EA a CSP

V tomto článku se dozvíte, jak přesunout privátní cloud z jednoho předplatného do jiného. Pohyb je možné provést z různých důvodů, jako je fakturace. 

>[!IMPORTANT]
>Měli byste mít alespoň práva přispěvatele u zdrojového i cílového předplatného. Virtuální síť a bránu virtuální sítě nejde přesunout z jednoho předplatného do jiného. Navíc přesun předplatných nemá žádný vliv na správu a úlohy, jako jsou virtuální počítače vCenter, NSX a zatížení.

1. Přihlaste se k Azure Portal a vyberte privátní cloud, který chcete přesunout.

1. Vyberte odkaz **předplatné (změnit)** .

   :::image type="content" source="media/private-cloud-overview-subscription-id.png" alt-text="Snímek obrazovky zobrazující podrobnosti o privátním cloudu":::

1. Zadejte podrobnosti o předplatném pro **cíl** a vyberte **Další**.

   :::image type="content" source="media/move-resources-subscription-target.png" alt-text="Snímek obrazovky cílového prostředku" lightbox="media/move-resources-subscription-target.png":::

1. Potvrďte ověření prostředků, které jste vybrali pro přesun, a vyberte **Další**. 

   :::image type="content" source="media/confirm-move-resources-subscription-target.png" alt-text="Snímek obrazovky znázorňující přesunutí prostředku" lightbox="media/confirm-move-resources-subscription-target.png":::

1. Zaškrtnutím políčka zjistíte, že přidružené nástroje a skripty nebudou fungovat, dokud je neaktualizujete tak, aby používaly nová ID prostředků. Pak vyberte **přesunout**.

   :::image type="content" source="media/review-move-resources-subscription-target.png" alt-text="Snímek obrazovky zobrazující souhrn vybraného prostředku, který se přesouvá " lightbox="media/review-move-resources-subscription-target.png":::

   Po dokončení přesunu prostředků se zobrazí oznámení. Nové předplatné se zobrazí v přehledu privátního cloudu.

   :::image type="content" source="media/moved-subscription-target.png" alt-text="Snímek obrazovky s novým předplatným" lightbox="media/moved-subscription-target.png":::

