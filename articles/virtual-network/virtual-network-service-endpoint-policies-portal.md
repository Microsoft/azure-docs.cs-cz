---
title: Vytvoření a přidružení zásad koncového bodu služby – Azure Portal
titlesuffix: Azure Virtual Network
description: V tomto článku se dozvíte, jak nastavit a přiřadit zásady koncového bodu služby pomocí Azure Portal.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: how-to
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: bdf0e87c92a55d0dbb5bbe34334a6de4580cb350
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96004955"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Vytvoření, změna nebo odstranění zásad koncového bodu služby pomocí Azure Portal

Zásady koncového bodu služby umožňují filtrovat provoz virtuální sítě na konkrétní prostředky Azure prostřednictvím koncových bodů služby. Pokud nejste obeznámeni se zásadami koncového bodu služby, přečtěte si další informace v tématu [Přehled zásad koncového bodu služby](virtual-network-service-endpoint-policies-overview.md) .

 V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zásad koncového bodu služby
> * Vytvoření definice zásady koncového bodu služby
> * Vytvoření virtuální sítě s podsítí
> * Přidružení zásady koncového bodu služby k podsíti

Pokud ještě nemáte předplatné Azure, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Vytvoření zásad koncového bodu služby

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Do podokna hledání zadejte "zásady koncového bodu služby" a vyberte **zásady koncového bodu služby** a pak vyberte **vytvořit**.

![Vytvoření zásad koncového bodu služby](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. V části **základy** zadejte nebo vyberte následující informace. 

   - Předplatné: vyberte předplatné pro zásady
   - Skupina prostředků: vyberte **vytvořit novou** a zadejte *myResourceGroup* .
   - Název: myEndpointPolicy
   - Umístění: Střed USA
 
   ![Vytvoření základních zásad pro zásady koncového bodu služby](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. V části **prostředky** vyberte **+ Přidat** a v podokně **Přidat prostředek** zadejte nebo vyberte následující informace.

   - Služba: k dispozici je jenom **Microsoft. Storage** se zásadami koncového bodu služby.
   - Rozsah: vyberte jeden z **jednoho účtu**, **všechny účty v předplatném** a **všechny účty ve skupině prostředků** .
   - Předplatné: vyberte předplatné pro účet úložiště. Zásady a účty úložiště můžou být v různých předplatných.
   - Skupina prostředků: vyberte skupinu prostředků. Povinné, pokud je obor nastavený na hodnotu "všechny účty ve skupině prostředků" nebo "jeden účet".  
   - Prostředek: Vyberte prostředek Azure Storage pod vybraným předplatným nebo skupinou prostředků.
   - Kliknutím na tlačítko **Přidat** v dolní části dokončíte přidávání prostředků.

   ![Definice zásad koncového bodu služby – prostředek](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Přidejte další prostředky tak, že podle potřeby zopakujete výše uvedené kroky.

5. Volitelné: zadejte nebo vyberte následující informace ve **značkách**:
   
   - Klíč: Vyberte klíč pro zásadu. Ex: oddělení     
   - Hodnota: zadejte dvojici hodnot pro klíč. Např. finance

6. Vyberte **Zkontrolovat a vytvořit**. Ověřte informace a klikněte na **vytvořit**. Chcete-li provést další úpravy, klikněte na tlačítko **Předchozí**. 

   ![Vytvořit konečná ověření zásad koncového bodu služby](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Zobrazit zásady koncového bodu 

1. V poli *všechny služby* na portálu začněte zadávat *zásady koncového bodu služby*. Vyberte **zásady koncového bodu služby**.
2. V části **předplatná** vyberte své předplatné a skupinu prostředků, jak je znázorněno na následujícím obrázku.

   ![Zobrazit zásady](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Vyberte zásadu a kliknutím na **definice zásad** Zobrazte nebo přidejte další definice zásad.

   ![Zobrazit definice zásad](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Vyberte **přidružené podsítě** pro zobrazení podsítí, ke kterým jsou zásady přidružené. Pokud ještě není žádná podsíť přidružená, postupujte podle pokynů v následujícím kroku.

   ![Přidružené podsítě](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Přidružení zásady k podsíti

>[!WARNING] 
> Před přidružením zásad k dané podsíti zajistěte, aby se všechny prostředky, ke kterým se přistupovalo z podsítě, přidaly do definice zásady. Jakmile je zásada přidružená, budou se přes koncové body služby moct povolit jenom přístup k *uvedeným* prostředkům. 
>
> Také zajistěte, aby v podsíti neexistovaly žádné spravované služby Azure, které jsou přidruženy k zásadě koncového bodu služby.

- Předtím, než budete moci přidružit zásadu k podsíti, je nutné vytvořit virtuální síť a podsíť. Nápovědu k tomu najdete v článku o [vytvoření Virtual Network](./quick-create-portal.md) .

- Jakmile budete mít virtuální síť a podsíť nastavenou, musíte pro Azure Storage nakonfigurovat Virtual Network koncové body služby. V okně Virtual Network vyberte **koncové body služby** a v dalším podokně vyberte **Microsoft. Storage** a v části **podsítě** vyberte požadovanou virtuální síť nebo podsíť.

- V případě, že jste již vytvořili zásady koncového bodu služby před konfigurací koncového bodu služby pro tuto podsíť, můžete v rozevíracím seznamu vybrat zásadu koncového bodu služby, jak je uvedeno níže.

    ![Přidružit podsíť při vytváření koncového bodu služby](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- NEBO pokud přiřazujete zásady koncového bodu služby po konfiguraci koncových bodů služby, můžete se rozhodnout přidružit podsíť z okna zásad koncového bodu služby, a to tak, že přejdete k podoknu **přidružené podsítě** , jak je uvedeno níže.

    ![Přidružit podsíť přes SEP](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>Přístup k prostředkům Azure Storage ve všech oblastech bude omezený na zásady koncového bodu služby z této podsítě.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili zásadu koncového bodu služby a přidružíte ji k podsíti. Další informace o zásadách koncového bodu služby najdete v tématu [Přehled zásad koncového bodu služby.](virtual-network-service-endpoint-policies-overview.md)
