---
title: Vytvoření a přidružení zásad koncového bodu služby – portál Azure
titlesuffix: Azure Virtual Network
description: V tomto článku se dozvíte, jak nastavit a přidružené zásady koncového bodu služby pomocí portálu Azure.
services: virtual-network
documentationcenter: virtual-network
author: RDhillon
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 02/21/2020
ms.author: rdhillon
ms.openlocfilehash: d26fd2fec5f9d5ab8e9d82ff2c6bd83b11c72e99
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77651149"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Vytvoření, změna nebo odstranění zásad koncového bodu služby pomocí webu Azure Portal

Zásady koncového bodu služby umožňují filtrovat provoz virtuální sítě na konkrétní prostředky Azure přes koncové body služby. Pokud nejste obeznámeni se zásadami koncového bodu služby, další informace najdete v [tématu Přehled zásad koncového bodu služby.](virtual-network-service-endpoint-policies-overview.md)

 V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zásadkoncového bodu služby
> * Vytvoření definice zásad koncového bodu služby
> * Vytvoření virtuální sítě s podsítí
> * Přidružení zásady koncového bodu služby k podsíti

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Vytvoření zásadkoncového bodu služby

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. V podokně hledání zadejte "zásady koncového bodu služby" a vyberte **zásady koncového bodu služby** a pak vyberte **Vytvořit**.

![Vytvořit zásadu koncového bodu služby](./media/virtual-network-service-endpoint-policies-portal/create-sep-resource.png)

3. Zadání nebo výběr následujících informací v **části Základy** 

   - Předplatné : Vyberte předplatné pro zásady
   - Skupina prostředků : Vyberte **Vytvořit nový** a zadejte *myResourceGroup*
   - Název : myEndpointPolicy
   - Umístění : Centrální USA
 
   ![Vytvořit základy zásad koncového bodu služby](./media/virtual-network-service-endpoint-policies-portal/create-sep-basics.png)

4. Vyberte **+ Přidat** v části **Zdroje** a zadejte nebo vyberte následující informace v **podokně Přidat zdroje.**

   - Služba : Se zásadami koncového bodu služby je k dispozici pouze **Microsoft.Storage.**
   - Obor: Vyberte jeden z **jednoho účtu**, všechny účty **v předplatném** a **všechny účty ve skupině prostředků**
   - Předplatné : Vyberte si předplatné pro účet úložiště. Účty zásad a úložiště mohou být v různých předplatných.
   - Skupina prostředků : Vyberte skupinu prostředků. Povinné, pokud je obor nastaven jako "Všechny účty ve skupině prostředků" nebo "Jeden účet".  
   - Prostředek: Vyberte prostředek úložiště Azure v rámci vybraného předplatného nebo skupiny prostředků.
   - Klikněte na **tlačítko Přidat** dole a dokončete přidání zdroje.

   ![Definice zásad koncového bodu služby – prostředek](./media/virtual-network-service-endpoint-policies-portal/create-sep-add-resource.png)

   - Přidání dalších zdrojů opakováním výše uvedených kroků podle potřeby

5. Volitelné: Zadejte nebo vyberte následující informace ve **značkách**:
   
   - Klíč : Vyberte klíč pro zásadu. Ex: Oddělení     
   - Hodnota : Zadejte dvojici hodnot klíče. Ex: Finance

6. Vyberte **zkontrolovat + vytvořit**. Ověřte informace a klepněte na tlačítko **Vytvořit**. Chcete-li provést další úpravy, klepněte na tlačítko **Předchozí**. 

   ![Vytvoření ověření konečné zásady koncového bodu služby](./media/virtual-network-service-endpoint-policies-portal/create-sep-review-create.png)
  
## <a name="view-endpoint-policies"></a>Zobrazení zásad koncového bodu 

1. V poli *Všechny služby* na portálu začněte psát *zásady koncového bodu služby*. Vyberte **zásady koncového bodu služby**.
2. V části **Předplatná**vyberte své předplatné a skupinu prostředků, jak je znázorněno na následujícím obrázku.

   ![Zobrazit zásady](./media/virtual-network-service-endpoint-policies-portal/sep-view.png)
       
3. Vyberte zásadu a kliknutím na **Definice zásad** zobrazte nebo přidejte další definice zásad.

   ![Zobrazit definice zásad](./media/virtual-network-service-endpoint-policies-portal/sep-policy-definition.png)

4. Výběrem **přidružené podsítě** zobrazíte podsítě, které jsou přidruženy. Pokud ještě není přidružena žádná podsíť, postupujte podle pokynů v dalším kroku.

   ![Přidružené podsítě](./media/virtual-network-service-endpoint-policies-portal/sep-associated-subnets.png)
 
5. Přidružení zásady k podsíti

>[!WARNING] 
> Ujistěte se, že všechny prostředky přístupné z podsítě jsou přidány do definice zásad před ačleněním zásady k dané podsíti. Jakmile je zásada přidružena, bude povolen pouze přístup k *povoleným prostředkům uvedeným* prostředkem přes koncové body služby. 
>
> Také zajistit, že neexistují žádné spravované služby Azure v podsíti, která je přidružena k zásadám koncového bodu služby

- Před přidružením zásady k podsíti je třeba vytvořit virtuální síť a podsíť. Nápovědu k tomuto článku vytvořit virtuální síť naleznete v článku [Vytvořit virtuální síť.](./quick-create-portal.md)

- Jakmile budete mít virtuální síť a podsíť jsou nastaveny, budete muset nakonfigurovat koncové body služby virtuální sítě pro Azure Storage. V okně Virtuální síť vyberte **Koncové body služby**a v dalším podokně vyberte **Microsoft.Storage** a v části **Podsítě** vyberte požadovanou virtuální síť nebo podsíť.

- Nyní můžete buď vybrat zásady koncového bodu služby z rozevíracího seznamu ve výše uvedeném podokně, pokud jste již vytvořili zásady koncového bodu služby před konfigurací koncového bodu služby pro podsíť, jak je znázorněno níže

    ![Přidružení podsítě při vytváření koncového bodu služby](./media/virtual-network-service-endpoint-policies-portal/vnet-config-service-endpoint-add-sep.png)

- Nebo pokud přidružíte zásady koncového bodu služby po konfiguraci koncových bodů služby, můžete se rozhodnout přidružit podsíť v rámci okna Zásady koncového bodu služby tak, že přejdete do podokna **Přidružené podsítě,** jak je znázorněno níže.

    ![Přidružení podsítě pomocí sep](./media/virtual-network-service-endpoint-policies-portal/sep-edit-subnet-association.png)

>[!WARNING] 
>Přístup k prostředkům úložiště Azure ve všech oblastech bude omezen podle zásad koncového bodu služby z této podsítě.

## <a name="next-steps"></a>Další kroky
V tomto kurzu jste vytvořili zásadu koncového bodu služby a přidružili ji k podsíti. Další informace o zásadách koncového bodu služby najdete v [tématu přehled zásad koncového bodu služby.](virtual-network-service-endpoint-policies-overview.md)
