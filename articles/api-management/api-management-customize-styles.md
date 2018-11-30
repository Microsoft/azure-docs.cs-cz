---
title: Přizpůsobení stylu stránky na portálu pro vývojáře ve službě Azure API Management | Microsoft Docs
description: Podle kroků v tomto rychlém startu můžete přizpůsobit styl elementů na portálu pro vývojáře ve službě Azure API Management.
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
ms.openlocfilehash: c3c79e7bd68e7c96b57ea66951c080b7d952ee8c
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52441753"
---
# <a name="customize-the-style-of-the-developer-portal-pages"></a>Přizpůsobení stylu stránek portálu pro vývojáře

Existují tři nejběžnější způsoby, jak přizpůsobit portál pro vývojáře ve službě Azure API Management:
 
* [Úprava obsahu statických stránek a elementů rozložení stránek](api-management-modify-content-layout.md)
* Aktualizace stylů použitých pro elementy stránek napříč portálem pro vývojáře (vysvětlení obsahuje tato příručka)
* [Úprava šablon použitých pro stránky generované portálem](api-management-developer-portal-templates.md) (například dokumentace rozhraní API, produkty, ověřování uživatelů)

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Přizpůsobení stylu stránek elementů na stránkách portálu pro **vývojáře**
> * Zobrazení změn

![přizpůsobení stylu](./media/modify-developer-portal-style/developer_portal.png)

## <a name="prerequisites"></a>Požadavky

+ Seznamte se s [terminologií služby Azure API Management](api-management-terminology.md).
+ Projděte si následující rychlý start: [Vytvoření instance Azure API Managementu](get-started-create-service-instance.md).
+ Projděte si také následující kurz: Navíc kurzu: [Import a publikování vašeho prvního rozhraní API](import-and-publish.md).

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="customize-the-developer-portal"></a>Přizpůsobení portálu pro vývojáře

1. Vyberte **Přehled**.
2. V horní části okna **Přehled** klikněte na tlačítko **Portál pro vývojáře**. Případně můžete kliknout na odkaz **Adresa URL portálu pro vývojáře**.
3. V levé horní části obrazovky se zobrazí ikona se dvěma štětci. Najeďte na tuto ikonu myší a otevřete nabídku přizpůsobení portálu.

    ![přizpůsobení stylu](./media/modify-developer-portal-style/modify-developer-portal-style01.png)
4. V nabídce vyberte **Styly** a otevřete podokno pro přizpůsobení stylů.

    Na stránce se zobrazí všechny elementy, které můžete přizpůsobit pomocí **stylů**.
5. Do pole **Změna hodnot proměnných pro přizpůsobení vzhledu portálu pro vývojáře** zadejte headings-color.

    Na stránce se zobrazí element **@headings-color**. Tato proměnná řídí barvu textu.

    ![přizpůsobení stylu](./media/modify-developer-portal-style/modify-developer-portal-style02.png)
    
6. Klikněte na pole pro proměnnou **@headings-color**. 
    
    Otevře se rozevírací nabídka editoru barev.
7. V rozevírací nabídce editoru barev vyberte novou barvu.

    > [!TIP]
    > Pro všechny změny je k dispozici náhled v reálném čase. V horní části podokna přizpůsobení se zobrazí indikátor průběhu. Po několika sekundách se barva textu v záhlaví změní na nově vybranou barvu.

8. V levém dolním rohu nabídky podokna přizpůsobení vyberte **Publikovat**.
9. Vyberte **Publikovat vlastní nastavení** a zpřístupněte změny veřejnosti.

## <a name="view-your-change"></a>Zobrazení změn

1. Přejděte na portál pro vývojáře.
2. Zobrazí se provedené změny.

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Přizpůsobení stylu stránek elementů na stránkách portálu pro **vývojáře**
> * Zobrazení změn

Mohli byste mít také zájem naučit se, jak [přizpůsobit portál pro vývojáře Azure API Management pomocí šablon](api-management-developer-portal-templates.md).