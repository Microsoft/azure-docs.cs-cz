---
title: zahrnout soubor
description: zahrnout soubor
services: cdn
author: SyntaxC4
ms.service: cdn
ms.topic: include
ms.date: 05/24/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: c58b226c0f3bd63cb2a54bd3d8c91eb750a26f0a
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "60684617"
---
## <a name="create-a-new-cdn-profile"></a>Vytvoření nového profilu CDN

Profil CDN představuje kontejner pro koncové body CDN a určuje cenovou úroveň.

1. V levém horním rohu portálu Azure Portal vyberte **Vytvořit prostředek**. 
    
    Otevře se podokno **Nový**.
   
2. Vyberte **Web + mobilní zařízení** a potom vyberte **CDN**.
   
    ![Výběr prostředku CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Otevře se podokno **Profil CDN**.

3. K nastavení profilu CDN použijte hodnoty z následující tabulky:
   
    | Nastavení  | Hodnota |
    | -------- | ----- |
    | **Název** | Jako název profilu zadejte *my-cdn-profile-123*. Tento název musí být globálně jedinečný. Pokud se už používá, můžete zadat jiný název. |
    | **Předplatné** | V rozevíracím seznamu vyberte předplatné Azure. |
    | **Skupina prostředků** | Vyberte **Vytvořit nový** a jako název skupiny prostředků zadejte *my-resource-group-123*. Tento název musí být globálně jedinečný. Pokud už se používá, můžete zadat jiný název nebo můžete vybrat **Použít existující** a v rozevíracím seznamu zvolit **my-resource-group-123**. | 
    | **Umístění skupiny prostředků** | V rozevíracím seznamu vyberte **Střed USA**. |
    | **Cenová úroveň** | V rozevíracím seznamu vyberte **Verizon úrovně Standard**. |
    | **Vytvořte teď nový koncový bod CDN** | Tuto možnost ponechejte nevybranou. |  
   
    ![Nový profil CDN](./media/cdn-create-profile/cdn-new-profile.png)

4. Vyberte **Připnout na řídicí panel** a uložte vytvořený profil na řídicí panel.
    
5. Vyberte **Vytvořit** a vytvořte profil. 

    Profily **Azure CDN Standard od Microsoftu** se obvykle dokončí do dvou hodin. 

