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
ms.openlocfilehash: d73a8d743a948cbd94a3af81fe2e77c45c0eeb67
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "82996292"
---
## <a name="create-a-new-cdn-profile"></a>Vytvoření nového profilu CDN

Profil CDN představuje kontejner pro koncové body CDN a určuje cenovou úroveň.

1. V Azure Portal vyberte **vytvořit prostředek** (vlevo nahoře). Otevře se podokno **Nový**.
   
1. Vyhledejte a vyberte **CDN**a pak vyberte **vytvořit**:
   
    ![Výběr prostředku CDN](./media/cdn-create-profile/cdn-new-resource.png)

    Zobrazí se podokno **profil CDN** .

1. Zadejte tyto hodnoty:
   
    | Nastavení  | Hodnota |
    | -------- | ----- |
    | **Název** | Jako název profilu zadejte *CDN-Profile-123* . Tento název musí být globálně jedinečný; Pokud se už používá, zadejte jiný název. |
    | **Předplatné** | V rozevíracím seznamu vyberte předplatné Azure. |
    | **Skupina prostředků** | Vyberte **vytvořit novou** a jako název skupiny prostředků zadejte *CDNQuickstart-RG* , nebo vyberte **použít existující** a zvolte *CDNQuickstart-RG* , pokud už tuto skupinu máte. | 
    | **Umístění skupiny prostředků** | V rozevíracím seznamu vyberte umístění v blízkosti. |
    | **Cenová úroveň** | V rozevíracím seznamu vyberte **Standardní možnost Akamai** . (Čas nasazení pro úroveň Akamai je přibližně jedna minuta. Vrstva Microsoft trvá přibližně 10 minut a vrstvy Verizon trvá přibližně 90 minut.) |
    | **Vytvořte teď nový koncový bod CDN** | Tuto možnost ponechejte nevybranou. |  
   
    ![Nový profil CDN](./media/cdn-create-profile/cdn-new-profile.png)

1. Vyberte **Vytvořit** a vytvořte profil.

