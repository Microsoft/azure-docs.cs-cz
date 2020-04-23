---
title: 'Úvodní příručka: Vytvoření služby VMware CloudSimple'
titleSuffix: Azure VMware Solution by CloudSimple
description: Zjistěte, jak vytvořit službu CloudSimple, nákupní uzly a rezervovat uzly
author: sharaths-cs
ms.author: dikamath
ms.date: 08/16/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: d7c137a75c0a021aa8bca3aec23da6c4d1ada300
ms.sourcegitcommit: af1cbaaa4f0faa53f91fbde4d6009ffb7662f7eb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/22/2020
ms.locfileid: "81868016"
---
# <a name="quickstart---create-azure-vmware-solution-by-cloudsimple-service"></a>Úvodní příručka – vytvoření řešení Azure VMware pomocí služby CloudSimple

Pokud chcete začít, vytvořte řešení Azure VMware pomocí CloudSimple na webu Azure Portal.

## <a name="vmware-solution-by-cloudsimple---service-overview"></a>VMware Solution by CloudSimple - Přehled služeb

Služba CloudSimple umožňuje využívat řešení Azure VMware od CloudSimple.  Vytvoření služby umožňuje zřizování uzlů, rezervace uzlů a vytváření privátních cloudů.  Službu CloudSimple přidáte v každé oblasti Azure, kde je služba CloudSimple dostupná.  Služba definuje hraniční síť řešení Azure VMware podle CloudSimple.  Tato hraniční síť se používá pro služby, které zahrnují vpn, ExpressRoute a připojení k Internetu do privátních cloudů.

Chcete-li přidat službu CloudSimple, musíte vytvořit podsíť brány. Podsíť brány se používá při vytváření hraniční sítě a vyžaduje blok CIDR /28. Adresní prostor podsítě brány musí být jedinečný. Nemůže se překrývat s žádným místním adresním prostorem sítě nebo adresním prostorem virtuální sítě Azure.

## <a name="before-you-begin"></a>Před zahájením

Přidělit blok CIDR /28 pro podsíť brány.  Podsíť brány je vyžadována pro službu CloudSimple a je jedinečná pro oblast, ve které je vytvořena. Podsíť brány se používá pro řešení Azure VMware síťovými službami CloudSimple a vyžaduje blok CIDR /28. Adresní prostor podsítě brány musí být jedinečný. Nesmí se překrývat s žádnou sítí, která komunikuje s prostředím CloudSimple.  Sítě, které komunikují s CloudSimple patří místní sítě a virtuální sítě Azure.

Zkontrolujte [požadavky sítě](cloudsimple-network-checklist.md). 

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-the-service"></a>Vytvoření služby

1. Vyberte **Všechny služby**.
2. Vyhledejte **službu CloudSimple**.

    ![Hledat cloudovou jednoduchou službu](media/create-cloudsimple-service-search.png)

3. Vyberte **možnost CloudSimple Services**.
4. Chcete-li vytvořit novou službu, klepněte na tlačítko **Přidat.**

    ![Přidat cloudovou službu](media/create-cloudsimple-service-add.png)

5. Vyberte předplatné, ve kterém chcete vytvořit službu CloudSimple.
6. Vyberte skupinu prostředků pro službu. Chcete-li přidat novou skupinu prostředků, klepněte na tlačítko **Vytvořit nový**.
7. Zadejte název pro identifikaci služby.
8. Zadejte CIDR pro bránu služby. Zadejte podsíť /28, která se nepřekrývá s žádnou místní podsítí, podsítěmi Azure nebo plánovanými podsítěmi CloudSimple. Po vytvoření služby nelze změnit CIDR.

    ![Vytvoření služby CloudSimple](media/create-cloudsimple-service.png)

9. Klikněte na tlačítko **OK**.

Služba je vytvořena a přidána do seznamu služeb.

## <a name="provision-nodes"></a>Zřizování uzlů

Pokud chcete nastavit průběžnou kapacitu pro prostředí CloudSimple Private Cloud, nejprve zřizujte uzly na webu Azure Portal.

1. Vyberte **Všechny služby**.
2. Vyhledejte **cloudové jednoduché uzly**.

    ![Hledat cloudové jednoduché uzly](media/create-cloudsimple-node-search.png)

3. Vyberte **možnost CloudSimple Uzly**.
4. Chcete-li vytvořit uzly, klepněte na **tlačítko Přidat.**

    ![Přidat cloudové jednoduché uzly](media/create-cloudsimple-node-add.png)

5. Vyberte předplatné, kde chcete zřídit cloudové jednoduché uzly.
6. Vyberte skupinu prostředků pro uzly. Chcete-li přidat novou skupinu prostředků, klepněte na tlačítko **Vytvořit nový**.
7. Zadejte předponu pro identifikaci uzlů.
8. Vyberte umístění prostředků uzlu.
9. Vyberte vyhrazené umístění pro hostování prostředků uzlu.
10. Vyberte [typ uzlu](cloudsimple-node.md).
11. Vyberte počet uzlů, které chcete zřídit.
12. Vyberte **zkontrolovat + vytvořit**.
13. Zkontrolujte nastavení. Chcete-li změnit všechna nastavení, klepněte na tlačítko **Předchozí**.
14. Vyberte **Vytvořit**.

## <a name="next-steps"></a>Další kroky

* [Vytvoření privátního cloudu a konfigurace prostředí](quickstart-create-private-cloud.md)
* Další informace o [službě CloudSimple](https://docs.microsoft.com/azure/vmware-cloudsimple/cloudsimple-service)
