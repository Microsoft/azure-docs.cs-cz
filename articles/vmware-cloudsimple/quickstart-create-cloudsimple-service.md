---
title: Řešení Azure VMware podle CloudSimple rychlý start – vytvoření služby
description: Zjistěte, jak vytvořit službu CloudSimple zřízení uzlů a uzly rezervovat
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5732ea726bdecc10d0757224870ee5d8be83a2b2
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/17/2019
ms.locfileid: "67164223"
---
# <a name="quickstart---create-service"></a>Rychlý start – vytvoření služby

Abyste mohli začít, vytvořte řešení VMware Azure podle CloudSimple na webu Azure Portal.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>Řešení VMware podle CloudSimple – přehled služeb

Služba CloudSimple umožňuje využívat řešení VMware Azure podle CloudSimple.  Vytváření služby vám umožní zřízení uzly rezerva uzly a vytváření privátních cloudů.  Přidat službu CloudSimple v každé oblasti Azure, kde je k dispozici služba CloudSimple.  Služba definuje hraniční síť řešení VMware Azure podle CloudSimple.  Tato hraniční síť se používá pro služby, které zahrnují připojení k Internetu, VPN a ExpressRoute do privátních cloudů.

Chcete-li přidat službu CloudSimple, musíte vytvořit podsíť brány. Podsíť brány se používá při vytváření hraniční sítě a vyžaduje o velikosti/28 blok CIDR. Adresní prostor podsítě brány musí být jedinečný. Nesmí překrývat s žádným z vaší místní síti adresní prostory nebo adresní prostor virtuální sítě Azure.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="enable-microsoftvmwarecloudsimple-resource-provider"></a>Povolit Microsoft.VMwareCloudSimple poskytovatele prostředků

Postupem uvedeným níže povolte poskytovatele prostředků pro službu CloudSimple.

1. Vyberte **Všechny služby**.
2. Vyhledejte a vyberte **předplatná**.

    ![Vybrat předplatná](media/cloudsimple-service-select-subscriptions.png)

3. Vyberte předplatné, ve kterém chcete povolit službu CloudSimple
4. Klikněte na **poskytovatelů prostředků** pro předplatné
5. Použití **Microsoft.VMwareCloudSimple** k filtrování poskytovatele prostředků
6. Vyberte **Microsoft.VMwareCloudSimple** poskytovatele prostředků a klikněte na kartu **zaregistrovat**

    ![Registrace poskytovatele prostředků](media/cloudsimple-service-enable-resource-provider.png)

## <a name="create-the-service"></a>Vytvoření služby

1. Vyberte **Všechny služby**.
2. Vyhledejte **CloudSimple služby**.

    ![Search CloudSimple Service](media/create-cloudsimple-service-search.png)

3. Vyberte **CloudSimple služby**.
4. Klikněte na tlačítko **přidat** k vytvoření nové služby.

    ![Přidat službu CloudSimple](media/create-cloudsimple-service-add.png)

5. Vyberte předplatné, ve kterém chcete vytvořit službu CloudSimple.
6. Vyberte skupinu prostředků pro službu. Chcete-li přidat novou skupinu prostředků, klikněte na tlačítko **vytvořit nový**.
7. Zadejte název k identifikaci služby.
8. Zadejte CIDR služby brány. Zadejte o velikosti/28 podsíť, která se nepřekrývá s žádným z místních podsítích, Azure podsítě nebo plánované CloudSimple podsítě. Po vytvoření služby nelze změnit CIDR.

    ![Vytvoření služby CloudSimple](media/create-cloudsimple-service.png)

9. Klikněte na **OK**.

Služba je vytvořen a přidán do seznamu služeb.

## <a name="provision-nodes"></a>Zřizování uzlů

Nastavení platit jako můžete přejít kapacity pro prostředí CloudSimple privátního cloudu, nejdříve zřídit uzly na webu Azure Portal.

1. Vyberte **Všechny služby**.
2. Vyhledejte **CloudSimple uzly**.

    ![Hledání CloudSimple uzly](media/create-cloudsimple-node-search.png)

3. Vyberte **CloudSimple uzly**.
4. Klikněte na tlačítko **přidat** vytvoření uzlů.

    ![Přidání uzlů CloudSimple](media/create-cloudsimple-node-add.png)

5. Vyberte předplatné, ve které chcete zřídit CloudSimple uzly.
6. Vyberte skupinu prostředků pro uzly. Chcete-li přidat novou skupinu prostředků, klikněte na tlačítko **vytvořit nový**.
7. Zadejte předponu k identifikaci uzlů.
8. Vyberte umístění pro prostředky uzlu.
9. Vyberte vyhrazenou umístění pro hostování prostředky uzlu.
10. Vyberte typ uzlu. Můžete použít [CS28 nebo CS36 možnost](cloudsimple-node.md). Druhou možnost zahrnuje maximální kapacitu výpočetní a paměťové prostředky.
11. Vyberte počet uzlů na zřizování.
12. Vyberte **zkontrolovat a vytvořit**.
13. Zkontrolujte nastavení. Chcete-li nastavení upravit, klikněte na tlačítko **předchozí**.
14. Vyberte **Vytvořit**.

## <a name="next-steps"></a>Další postup

* [Vytvoření privátního cloudu a konfigurace prostředí](quickstart-create-private-cloud.md)
* Další informace o [CloudSimple služby](https://docs.azure.cloudsimple.com/cloudsimple-service)
