---
title: zahrnout soubor
description: zahrnout soubor
services: cdn
author: dksimpson
ms.service: cdn
ms.topic: include
ms.date: 04/04/2018
ms.author: rli; v-deasim
ms.custom: include file
ms.openlocfilehash: 692364e9b2e78b3bd1f63137148dfbc680364737
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2018
---
## <a name="create-a-new-cdn-profile"></a>Vytvoření nového profilu CDN

Profil CDN je kontejner pro koncové body CDN a určuje cenová úroveň.

1. Na portálu Azure, v levém horním vyberte **vytvořit prostředek**.
    
    **Nový** podokně se zobrazí.
   
2. Vyberte **Web + mobilní**, pak **CDN**.
   
    ![Vyberte prostředek CDN](./media/cdn-create-profile/cdn-new-resource.png)

    **Profil CDN** podokně se zobrazí.

    Použití nastavení uvedeného v tabulce podle bitovou kopii.
   
    ![Nový profil CDN](./media/cdn-create-profile/cdn-new-profile.png)

    | Nastavení  | Hodnota |
    | -------- | ----- |
    | **Název** | Zadejte *my-cdn profil-123* pro název profilu. Tento název musí být globálně jedinečný; Pokud se už používá, můžete zadat jiný. |
    | **Předplatné** | V rozevíracím seznamu vyberte předplatné Azure.|
    | **Skupina prostředků** | Vyberte **vytvořit nový** a zadejte *my-resource skupiny-123* pro váš název skupiny prostředků. Tento název musí být globálně jedinečný; Pokud se už používá, můžete zadat jiný. | 
    | **Umístění skupiny prostředků** | Vyberte **střed USA** z rozevíracího seznamu. |
    | **Cenová úroveň** | Vyberte **Standard Verizon** z rozevíracího seznamu. |
    | **Teď vytvořit nový koncový bod CDN** | Nechte nezaškrtnuté. |  
   
3. Vyberte **připnout na řídicí panel** k uložení profilu na řídicí panel po jejím vytvoření.
    
4. Vyberte **vytvořit** chcete vytvořit profil. 

