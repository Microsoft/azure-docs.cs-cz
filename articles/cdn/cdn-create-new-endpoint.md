---
title: Rychlý start – Vytvoření profilu a koncového bodu Azure CDN | Microsoft Docs
description: V tomto rychlém startu se dozvíte, jak povolit Azure CDN prostřednictvím vytvoření nového profilu a koncového bodu CDN.
services: cdn
documentationcenter: ''
author: dksimpson
manager: akucer
editor: ''
ms.assetid: 4ca51224-5423-419b-98cf-89860ef516d2
ms.service: cdn
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: quickstart
ms.date: 03/13/2018
ms.author: mazha
ms.custom: mvc
ms.openlocfilehash: 87216a861aa150c5f9a16f6193f2abf2af6d57d1
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
ms.locfileid: "34198703"
---
# <a name="quickstart-create-an-azure-cdn-profile-and-endpoint"></a>Rychlý start: Vytvoření profilu a koncového bodu Azure CDN
V tomto rychlém startu povolíte Azure Content Delivery Network (CDN) prostřednictvím vytvoření nového profilu a koncového bodu CDN. Po vytvoření profilu a koncového bodu můžete začít doručovat obsah svým zákazníkům.

[!INCLUDE [quickstarts-free-trial-note](../../includes/quickstarts-free-trial-note.md)]

## <a name="prerequisites"></a>Požadavky
Pro účely tohoto rychlého startu musíte mít vytvořený účet úložiště *mystorageacct123*, který použijete jako název počátečního hostitele. Další informace najdete v tématu [Integrace účtu úložiště Azure s Azure CDN](cdn-create-a-storage-account-with-cdn.md).

## <a name="log-in-to-the-azure-portal"></a>Přihlášení k portálu Azure Portal
Přihlaste se k webu [Azure Portal](https://portal.azure.com) pomocí svého účtu Azure.

[!INCLUDE [cdn-create-profile](../../includes/cdn-create-profile.md)]

## <a name="create-a-new-cdn-endpoint"></a>Vytvoření nového koncového bodu CDN

Po vytvoření profilu CDN ho můžete použít k vytvoření koncového bodu.

1. Na svém řídicím panelu na webu Azure Portal vyberte profil CDN, který jste vytvořili. Pokud ho nemůžete najít, vyberte **Všechny služby** a pak **Profily CDN**. Na stránce **Profily CDN** vyberte profil, který chcete použít. 
   
    Zobrazí se stránka Profil CDN.

2. Vyberte **Koncový bod**.
   
    ![Profil CDN](./media/cdn-create-new-endpoint/cdn-select-endpoint.png)
   
    Zobrazí se stránka **Přidání koncového bodu**.

    Použijte nastavení uvedená v tabulce pod obrázkem.
   
    ![Přidat koncový bod](./media/cdn-create-new-endpoint/cdn-add-endpoint.png)

    | Nastavení | Hodnota |
    | ------- | ----- |
    | **Název** | Jako název hostitele koncového bodu zadejte *my-endpoint-123*. Tento název musí být globálně jedinečný. Pokud se už používá, můžete zadat jiný. Tento název se používá pro přístup k prostředkům v mezipaměti v doméně _&lt;název_koncového_bodu&gt;_.azureedge.net.|
    | **Typ zdroje** | Vyberte **Úložiště**. | 
    | **Název počátečního hostitele** | Jako název hostitele zadejte *mystorageacct123.blob.core.windows.net*. Tento název musí být globálně jedinečný. Pokud se už používá, můžete zadat jiný. |
    | **Cesta ke zdroji** | Ponechte prázdné. |
    | **Hlavička počátečního hostitele** | Ponechte výchozí vygenerovanou hodnotu. |  
    | **Protokol** | Ponechte vybrané výchozí možnosti **HTTP** a **HTTPS**. |
    | **Počáteční port** | Ponechte výchozí hodnoty portu. | 
    | **Optimalizováno pro** | Ponechte výchozí výběr **Obecné doručování webu**. |
    
3. Pokud chcete vytvořit nový koncový bod, vyberte **Přidat**.
   
   Koncový bod se po vytvoření zobrazí v seznamu koncových bodů daného profilu.
    
   ![Koncový bod CDN](./media/cdn-create-new-endpoint/cdn-endpoint-success.png)
    
   Vzhledem k tomu, že rozšíření registrace nějakou dobu trvá, koncový bod není okamžitě dostupný pro použití: 
   - U profilů **Azure CDN Standard od Microsoftu** trvá šíření většinou 10 minut. 
   - V případě profilů **Azure CDN Standard od Akamai** je šíření obvykle hotové během jedné minuty. 
   - V případě profilů **Azure CDN od Verizonu** a **Azure CDN Premium od Verizonu** je šíření obvykle hotové během 90 minut. 

## <a name="clean-up-resources"></a>Vyčištění prostředků
V předchozích krocích jste ve skupině prostředků vytvořili profil a koncový bod CDN. Pokud chcete přejít k části [Další kroky](#next-steps) a zjistit, jak do koncového bodu přidat vlastní doménu, uložte tyto prostředky. Pokud však předpokládáte, že už tyto prostředky nebudete používat, můžete je odstranit tak, že odstraníte skupinu prostředků, abyste se vyhnuli dalším poplatkům:

1. Na webu Azure Portal v nabídce vlevo vyberte **Skupiny prostředků** a pak vyberte **my-resource-group-123**.

2. Na stránce **Skupina prostředků** vyberte **Odstranit skupinu prostředků**, do textového pole zadejte *my-resource-group-123* a pak vyberte **Odstranit**.

    Tato akce odstraní skupinu prostředků, profil a koncový bod, které jste vytvořili v rámci tohoto rychlého startu.

## <a name="next-steps"></a>Další kroky
Pokud chcete zjistit, jak do koncového bodu CDN přidat vlastní doménu, projděte si následující kurz:

> [!div class="nextstepaction"]
> [Kurz: Přidání vlastní domény do koncového bodu Azure CDN](cdn-map-content-to-custom-domain.md)


