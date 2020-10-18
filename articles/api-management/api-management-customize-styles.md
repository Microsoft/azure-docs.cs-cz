---
title: Přizpůsobení stylu stránky na API Management starší verze portálu pro vývojáře
titleSuffix: Azure API Management
description: Podle kroků v tomto rychlém startu můžete přizpůsobit styl elementů na portálu pro vývojáře ve službě Azure API Management.
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.custom: mvc
ms.topic: tutorial
ms.date: 11/04/2019
ms.author: apimpm
ms.openlocfilehash: 9a654454c1ef3d5ea1ff7e6eb525bb153f7875c1
ms.sourcegitcommit: 419c8c8061c0ff6dc12c66ad6eda1b266d2f40bd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/18/2020
ms.locfileid: "92168201"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Přizpůsobení stylu stránek portálu pro vývojáře

Existují tři nejběžnější způsoby přizpůsobení portálu pro vývojáře v Azure API Management:
 
* [Úprava obsahu statických stránek a elementů rozložení stránek](api-management-modify-content-layout.md)
* Aktualizace stylů použitých pro elementy stránek napříč portálem pro vývojáře (vysvětlení obsahuje tato příručka)
* [Úprava šablon použitých pro stránky generované portálem](api-management-developer-portal-templates.md) (například dokumentace rozhraní API, produkty, ověřování uživatelů)

V tomto článku se dozvíte, jak přizpůsobit styl prvků na stránkách staršího portálu pro **vývojáře** a jak zobrazit vaše změny.

![Snímek obrazovky, který ukazuje, kde změníte nastavení na starší verzi portálu pro vývojáře.](./media/modify-developer-portal-style/developer_portal.png)

[!INCLUDE [api-management-portal-legacy.md](../../includes/api-management-portal-legacy.md)]

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Předpoklady

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Dokončete následující rychlý Start: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).
+ Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

## <a name="customize-the-developer-portal"></a>Přizpůsobení portálu pro vývojáře

1. Vyberte **Přehled**.
2. V horní části okna **Přehled** klikněte na tlačítko **portál pro vývojáře (starší verze)** .
3. V levé horní části obrazovky se zobrazí ikona se dvěma štětci. Najeďte na tuto ikonu myší a otevřete nabídku přizpůsobení portálu.

    ![Snímek obrazovky, který zvýrazní ikonu dvěma štětci štětce.](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. V nabídce vyberte **Styly** a otevřete podokno pro přizpůsobení stylů.

    Na stránce se zobrazí všechny elementy, které můžete přizpůsobit pomocí **stylů**.
5. Do pole **Změna hodnot proměnných pro přizpůsobení vzhledu portálu pro vývojáře** zadejte headings-color.

    Element ** \@ záhlaví-barva** se zobrazí na stránce. Tato proměnná řídí barvu textu.

    ![přizpůsobení stylu](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Klikněte na pole pro proměnnou ** \@ Nadpis-barva** . 
    
    Otevře se rozevírací nabídka editoru barev.
7. V rozevírací nabídce editoru barev vyberte novou barvu.

    > [!TIP]
    > Pro všechny změny je k dispozici náhled v reálném čase. V horní části podokna přizpůsobení se zobrazí indikátor průběhu. Po několika sekundách se barva textu v záhlaví změní na nově vybranou barvu.

8. V levém dolním rohu nabídky podokna přizpůsobení vyberte **Publikovat**.
9. Vyberte **Publikovat vlastní nastavení** a zpřístupněte změny veřejnosti.

## <a name="view-your-change"></a>Zobrazení změn

1. Přejděte na portál pro vývojáře.
2. Zobrazí se provedené změny.

## <a name="next-steps"></a>Další kroky

Mohli byste mít také zájem naučit se, jak [přizpůsobit portál pro vývojáře Azure API Management pomocí šablon](api-management-developer-portal-templates.md).