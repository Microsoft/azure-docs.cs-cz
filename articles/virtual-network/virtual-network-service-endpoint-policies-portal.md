---
title: Vytvořit a přidružit zásady koncových bodů služeb – web Azure portal | Dokumentace Microsoftu
description: V tomto článku zjistěte, jak nastavit a přidružená služba Zásady koncového bodu pomocí webu Azure portal.
services: virtual-network
documentationcenter: virtual-network
author: anithaa
manager: narayan
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: virtual-network
ms.workload: infrastructure
ms.date: 09/18/2018
ms.author: anithaa
ms.openlocfilehash: 3c28181e1d1a4fd1cdac530ed8f393c7e0c7ea92
ms.sourcegitcommit: 707bb4016e365723bc4ce59f32f3713edd387b39
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2018
ms.locfileid: "49426292"
---
# <a name="create-change-or-delete-service-endpoint-policy-using-the-azure-portal"></a>Vytvoření, změna nebo odstranění zásady koncového bodu služby pomocí webu Azure portal

Zásady koncového bodu služby umožňují filtrovat provoz virtuální sítě ke konkrétním prostředkům Azure prostřednictvím koncových bodů služby. Pokud nejste obeznámeni se zásadami koncových bodů služby, přečtěte si téma [přehled zásad koncového bodu služby](virtual-network-service-endpoint-policies-overview.md) Další informace.

 V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvoření zásady koncového bodu služby
> * Vytvoření definice zásad koncového bodu služby
> * Vytvořte virtuální síť s podsítí
> * Přidružení zásady koncového bodu služby k podsíti

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure 

Přihlaste se k webu Azure Portal na adrese http://portal.azure.com.

## <a name="create-a-service-endpoint-policy"></a>Vytvoření zásady koncového bodu služby

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. V podokně hledání zadejte "zásady koncového bodu služby" a vyberte **služby koncového bodu (Preview)** a pak vyberte **vytvořit**.
3. Zadejte nebo vyberte následující informace v **základy** 

   - Předplatné: Vyberte své předplatné pro zásady.    
   - Skupina prostředků: vyberte **vytvořit nový** a zadejte *myResourceGroup*.     
   - Název: myEndpointPolicy
   - Umístění: Střed USA – západ     
 
   ![Vytvoření koncového bodu služby Základy zásad](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-startpane.PNG)
   
4. Zadejte nebo vyberte následující informace v **definic zásad**

   - Klikněte na tlačítko **+ přidat prostředek**, zadejte nebo vyberte následující informace, přijměte výchozí nastavení pro zbývající nastavení a klikněte na tlačítko **přidat**.  
   - Obor: Vyberte **jeden účet** nebo **všechny účty v předplatném** nebo **všechny účty ve skupině prostředků**.    
   - Předplatné: Vyberte své předplatné pro účet úložiště. Účty zásad a úložiště může být v různých předplatných.   
   - Skupina prostředků: Vyberte skupinu prostředků. Povinné, pokud je obor nastaven jako "Všechny účty ve skupině prostředků" nebo "Jeden účet".  
   - Zdroj: mystorageaccountportal    
   - Klikněte na tlačítko **+ přidat prostředek** přidat další prostředky.
   
   ![Vytvoření definice zásad koncového bodu služby](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-policydefinitionspane.PNG)
   
5. Volitelné: Zadejte nebo vyberte následující informace v **značky**
   
   - Klíč: Vyberte svůj klíč pro zásady. Příklad: oddělení     
   - Hodnota: Zadejte hodnotu pár klíče. Příklad: Finance

6. Vyberte **zkontrolovat a vytvořit**. Ověřit informace a klikněte na tlačítko **vytvořit**. Chcete-li provést další změny, klikněte na tlačítko **předchozí**. 

   ![Vytvořit koncový bod služby konečné ověření zásad](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-create-finalcreatereview.PNG)
  
 
## <a name="view-endpoint-policies"></a>Zobrazit zásady koncového bodu 

1. V *všechny služby* pole na portálu, začněte psát *zásad koncových bodů služeb*. Vyberte **služby koncového bodu Policies(Preview)**.
2. V části **předplatná**, vyberte svoje předplatné a skupinu prostředků, jak je znázorněno na následujícím obrázku

   ![Zobrazit zásady](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicies.PNG)
       
3. Vyberte zásadu a klikněte na **definice zásad** zobrazit nebo přidat další definice zásad.

   ![Zobrazení definic zásad](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-viewpolicy-adddefinitions.PNG)

4. Vyberte **přidružené podsítě** zobrazíte podsítí tato zásada je přidružená. K přidružení zásady k podsíti, klikněte na "Navigovat do virtuální sítě ve stejné oblasti".

   ![Zobrazit přidružené podsítě](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-view-associatedsubnets.PNG)
 
## <a name="associate-a-policy-to-a-subnet"></a>Přidružte zásadu k podsíti

>[!WARNING] 
> Ujistěte se, že všechny prostředky, které jsou přístupné z podsítě pro vybranou službu, se přidají do zásad před přiřazení zásad. Po přidružení zásady jenom přístup k prostředkům podle zásad bude možné pro koncový bod oblastí pro službu. 

Než budete moct přidružit zásadu k podsíti, budete muset vytvořit virtuální síť a podsíť a pak můžete přiřadit zásady tak, aby podsíť:

1. V levém horním rohu webu Azure Portal vyberte **+ Vytvořit prostředek**.
2. Vyberte **Sítě** a pak vyberte **Virtuální síť**.
3. V části **Vytvořit virtuální síť** zadejte nebo vyberte následující informace, u zbývajících nastavení přijměte výchozí hodnoty a pak vyberte **Vytvořit**:
   - Název: myVirtualNetwork      
   - Adresní prostor: 10.0.0.0/16      
   - Předplatné: Vyberte své předplatné. Zásada by měla být ve stejném předplatném jako virtuální síť     
   - Skupina prostředků: vyberte **použít existující** a pak vyberte *myResourceGroup*     
   - Umístění: Střed USA – západ     
   - Název podsítě: privátní     
   - Rozsah adres: 10.0.0.0/24
     
4. Do pole **Hledat prostředky, služby a dokumenty** v horní části portálu začněte zadávat *myVirtualNetwork*. Jakmile se ve výsledcích hledání zobrazí virtuální síť **myVirtualNetwork**, vyberte ji.
5. V části **nastavení**vyberte **podsítě** a pak vyberte **privátní**.
6. Jak je znázorněno na následujícím obrázku, vyberte **koncové body služby**vyberte **Microsoft.Storage**vyberte **zásad koncových bodů služeb**vyberte  **myEndpointPolicy**a pak vyberte **Uložit**:

   ![Přidružení zásady](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-associatepolicies.PNG)

>[!WARNING] 
>Přístup k prostředkům služby v jiných oblastech bude možné z této podsítě podle skupiny zabezpečení sítě (Nsg). Pokud chcete omezit přístup jenom koncový bod oblastí, omezte na pouze provoz služeb v oblasti koncový bod skupin zabezpečení sítě. Další informace o tom, jak vytvořit skupiny Nsg s značky služeb v jedné oblasti, naleznete v tématu [značky služeb skupiny zabezpečení sítě Azure.](manage-network-security-group.md?toc=%2fcreate-a-security-rule%2f.json)

V následujícím příkladu se skupina zabezpečení sítě omezuje přístup k pouze prostředků služby Azure Storage v WestCentralUS a WestUS2, s pravidlem "Odepřít všechny" jako pravidla s nižší prioritou.

![Odmítnout všechny skupiny zabezpečení sítě](./media/virtual-network-service-endpoint-policies-portal/virtual-network-endpoint-policies-nsg-rules.PNG)


## <a name="next-steps"></a>Další postup
V tomto kurzu jste vytvořili zásady koncového bodu služby a přidruženou k podsíti. Další informace o zásadách koncového bodu služby, najdete v článku [přehled zásad koncového bodu služby.](virtual-network-service-endpoint-policies-overview.md)

