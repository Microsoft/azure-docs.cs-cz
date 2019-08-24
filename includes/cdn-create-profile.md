---
title: zahrnout soubor
description: zahrnout soubor
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 05/24/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 8aa6cb3f10b86a6821cd93190ecc2135508739cb
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67593979"
---
## <a name="create-a-new-cdn-profile"></a>Vytvoření nového profilu CDN

Profil CDN představuje kontejner pro koncové body CDN a určuje cenovou úroveň.

1. V levém horním rohu portálu Azure Portal vyberte **Vytvořit prostředek**. 
    
    Otevře se podokno **Nový**.
   
2. Vyberte **Web + mobilní zařízení** a potom vyberte **CDN**.
   
    ![Výběr prostředku CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Otevře se podokno **Profil CDN**.

3. K nastavení profilu CDN použijte hodnoty z následující tabulky:
   
    | Nastavení  | Value |
    | -------- | ----- |
    | **Název** | Jako název profilu zadejte *my-cdn-profile-123*. Tento název musí být globálně jedinečný. Pokud se už používá, můžete zadat jiný název. |
    | **Předplatné** | V rozevíracím seznamu vyberte předplatné Azure. |
    | **Skupina prostředků** | Vyberte **Vytvořit nový** a jako název skupiny prostředků zadejte *my-resource-group-123*. Pokud už se používá, můžete zadat jiný název nebo můžete vybrat **Použít existující** a v rozevíracím seznamu zvolit **my-resource-group-123**. | 
    | **Umístění skupiny prostředků** | V rozevíracím seznamu vyberte **Střední USA**. |
    | **Cenová úroveň** | V rozevíracím seznamu vyberte **Verizon úrovně Standard**. |
    | **Vytvořte teď nový koncový bod CDN** | Tuto možnost ponechejte nevybranou. |  
   
    ![Nový profil CDN](./media/cdn-create-profile/cdn-new-profile.png)

4. Vyberte **Připnout na řídicí panel** a uložte vytvořený profil na řídicí panel.
    
5. Vyberte **Vytvořit** a vytvořte profil. 

    Profily **Azure CDN Standard od Microsoftu** se obvykle dokončí do dvou hodin. 

