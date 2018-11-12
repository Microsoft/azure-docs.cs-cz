---
title: Publikování verzí rozhraní API pomocí služby Azure API Management | Microsoft Docs
description: Pomocí kroků v tomto kurzu se naučíte publikovat několik verzí pomocí služby API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 06/15/2018
ms.author: apimpm
ms.openlocfilehash: f9b63beeb6979d7dec5fc2cf2d91a3327ba7c43c
ms.sourcegitcommit: 6135cd9a0dae9755c5ec33b8201ba3e0d5f7b5a1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/31/2018
ms.locfileid: "50415232"
---
# <a name="publish-multiple-versions-of-your-api"></a>Publikování několika verzí rozhraní API 

Občas je nepraktické, aby všichni volající vašeho rozhraní API používali naprosto stejnou verzi. Když volající chtějí upgradovat na novější verzi, chtějí mít možnost to udělat snadno pochopitelným způsobem. Ve službě Azure API Management je možné toho docílit s využitím **verzí**. Další informace najdete v tématu [Verze a revize](https://blogs.msdn.microsoft.com/apimanagement/2017/09/14/versions-revisions/).

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání nové verze stávajícího rozhraní API
> * Výběr schématu verze
> * Přidání verze do produktu
> * Zobrazení verze na portálu pro vývojáře

![Verze zobrazená na portálu pro vývojáře](media/api-management-getstarted-publish-versions/azure_portal.PNG)

## <a name="prerequisites"></a>Požadavky

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Projděte si následující rychlý start: [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md).
+ Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="add-a-new-version"></a>Přidání nové verze

![Místní nabídka rozhraní API – přidání verze](media/api-management-getstarted-publish-versions/AddVersionMenu.png)

1. V seznamu rozhraní API vyberte rozhraní **Demo Conference API**.
2. Vyberte místní nabídku (**...**) vedle něj.
3. Vyberte **+ Přidat verzi**.

> [!TIP]
> Verze je možné povolit také při vytváření nového rozhraní API – vyberte **Chcete vytvořit verzi tohoto rozhraní API?** na obrazovce **Přidat rozhraní API**.

## <a name="choose-a-versioning-scheme"></a>Výběr schématu vytváření verzí

Azure API Management umožňuje zvolit způsob, jakým umožníte volajícím určit požadovanou verzi rozhraní API. Verzi rozhraní API, která se má použít, zadáte výběrem **schématu vytváření verzí**. Toto schéma může být **cesta, hlavička nebo řetězec dotazu**. V následujícím příkladu se k výběru schématu vytváření verzí používá cesta.

![Obrazovka Přidat verzi](media/api-management-getstarted-publish-versions/AddVersion.PNG)

1. Jako **schéma vytváření verzí** ponechte vybranou možnost **cesta**.
2. Přidejte **v1** jako **název** a **identifikátor verze**.

    > [!TIP]
    > Pokud jako schéma vytváření verzí vyberete **hlavičku** nebo **řetězec dotazu**, je potřeba zadat další hodnotu – název hlavičky nebo parametr řetězce dotazu.

3. Vyberte **Vytvořit** a nastavte svou novou verzi.
4. V seznamu rozhraní API se teď pod položkou **Demo Conference API** zobrazí dvě různá rozhraní API – **Original** (Původní) a **v1**.

    ![Verze uvedené pod rozhraním API na webu Azure Portal](media/api-management-getstarted-publish-versions/VersionList.PNG)

    > [!Note]
    > Pokud přidáte verzi k rozhraní bez správy verzí, automaticky se vytvoří verze **Original**, která odpovídá na výchozí adrese URL. Tím se zajistí, že proces přidání verze negativně neovlivní žádné stávající volající. Pokud vytvoříte nové rozhraní API, které má už od začátku povolené verze, verze Original se nevytvoří.

5. Teď můžete upravit a nakonfigurovat verzi **v1** jako rozhraní API, které je oddělené od verze **Original**. Změny jedné verze nemají vliv na jinou verzi.

## <a name="add-the-version-to-a-product"></a>Přidání verze do produktu

Aby se volajícím zobrazila nová verze, musí se přidat do **produktu**.

![Produkty API Managementu](media/api-management-getstarted-publish-versions/08-AddMultipleVersions-03-AddVersionToProduct.png)

1. Na stránce modelu nasazení Classic vyberte **Produkty**.
2. Vyberte **Neomezeno**.
3. Vyberte **Rozhraní API**.
4. Vyberte **Přidat**.
5. Vyberte **Demo Conference API, verze v1**.
6. Klikněte na **Vybrat**.

## <a name="browse-the-developer-portal-to-see-the-version"></a>Zobrazení verze na portálu pro vývojáře

1. V horní nabídce vyberte **Portál pro vývojáře**.
2. Vyberte **Rozhraní API** a všimněte si, že pro rozhraní **Demo Conference API** se zobrazí verze **Original** a **v1**.
3. Vyberte **v1**.
4. Všimněte si **adresy URL požadavku** první operace na seznamu. Ukazuje, že cesta URL rozhraní API zahrnuje **v1**.

    ![Místní nabídka rozhraní API – přidání verze](media/api-management-getstarted-publish-versions/developer_portal.png)

## <a name="next-steps"></a>Další kroky

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přidání nové verze stávajícího rozhraní API
> * Výběr schématu verze 
> * Přidání verze do produktu
> * Zobrazení verze na portálu pro vývojáře

Přejděte k dalšímu kurzu:

> [!div class="nextstepaction"]
> [Přizpůsobení stylu stránek portálu pro vývojáře](api-management-customize-styles.md)