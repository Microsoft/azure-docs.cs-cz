---
title: zahrnout soubor
description: zahrnout soubor
services: cdn
author: SyntaxC4
ms.service: azure-cdn
ms.topic: include
ms.date: 04/30/2020
ms.author: cfowler
ms.custom: include file
ms.openlocfilehash: c6352ee9d29e4e45aa4be449046a0715fee06047
ms.sourcegitcommit: 16887168729120399e6ffb6f53a92fde17889451
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/13/2021
ms.locfileid: "98165905"
---
## <a name="create-a-new-cdn-profile"></a>Vytvoření nového profilu CDN

Profil CDN představuje kontejner pro koncové body CDN a určuje cenovou úroveň.

1. V Azure Portal vyberte **vytvořit prostředek** (vlevo nahoře). Otevře se podokno **Nový**.
   
1. Vyhledejte a vyberte **CDN** a pak vyberte **vytvořit**:
   
    ![Výběr prostředku CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Zobrazí se podokno **profil CDN** .

1. Zadejte tyto hodnoty:
   
    | Nastavení  | Hodnota |
    | -------- | ----- |
    | **Název** | Jako název profilu zadejte *CDN-Profile-123* . Tento název musí být globálně jedinečný; Pokud se už používá, zadejte jiný název. |
    | **Předplatné** | V rozevíracím seznamu vyberte předplatné Azure. |
    | **Skupina prostředků** | Vyberte **vytvořit novou** a jako název skupiny prostředků zadejte *CDNQuickstart-RG* , nebo vyberte **použít existující** a zvolte *CDNQuickstart-RG* , pokud už tuto skupinu máte. | 
    | **Umístění skupiny prostředků** | V rozevíracím seznamu vyberte umístění v blízkosti. |
    | **Cenová úroveň** | V rozevíracím seznamu vyberte **Standardní možnost Akamai** . (Čas nasazení pro úroveň Akamai je přibližně jedna minuta. Vrstva Microsoft trvá přibližně 10 minut a vrstvy Verizon trvá přibližně 30 minut.) |
    | **Vytvořte teď nový koncový bod CDN** | Tuto možnost ponechejte nevybranou. |  
   
    ![Nový profil CDN](./media/cdn-create-profile/cdn-new-profile.png)

1. Vyberte **Vytvořit** a vytvořte profil.

