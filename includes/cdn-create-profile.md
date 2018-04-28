---
title: zahrnout soubor
description: zahrnout soubor
services: cdn
author: SyntaxC4
ms.service: cdn
ms.topic: include
ms.date: 04/13/2018
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: 0db5f571324694f0518ffc4e92af985e5125d755
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/18/2018
---
## <a name="create-a-new-cdn-profile"></a>Vytvoření nového profilu CDN

Profil CDN představuje kontejner pro koncové body CDN a určuje cenovou úroveň.

1. V levém horním rohu portálu Azure Portal vyberte **Vytvořit prostředek**.
    
    Otevře se podokno **Nový**.
   
2. Vyberte **Web + mobilní zařízení** a potom vyberte **CDN**.
   
    ![Výběr prostředku CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Otevře se podokno **Profil CDN**.

    Použijte nastavení uvedená v tabulce pod obrázkem.
   
    ![Nový profil CDN](./media/cdn-create-profile/cdn-new-profile.png)

    | Nastavení  | Hodnota |
    | -------- | ----- |
    | **Název** | Jako název profilu zadejte *my-cdn-profile-123*. Tento název musí být globálně jedinečný. Pokud se už používá, můžete zadat jiný. |
    | **Předplatné** | V rozevíracím seznamu vyberte předplatné Azure.|
    | **Skupina prostředků** | Vyberte **Vytvořit nový** a jako název skupiny prostředků zadejte *my-resource-group-123*. Tento název musí být globálně jedinečný. Pokud se už používá, můžete zadat jiný. | 
    | **Umístění skupiny prostředků** | V rozevíracím seznamu vyberte **Střed USA**. |
    | **Cenová úroveň** | V rozevíracím seznamu vyberte **Verizon úrovně Standard**. |
    | **Vytvořte teď nový koncový bod CDN** | Tuto možnost ponechejte nevybranou. |  
   
3. Vyberte **Připnout na řídicí panel** a uložte vytvořený profil na řídicí panel.
    
4. Vyberte **Vytvořit** a vytvořte profil. 

