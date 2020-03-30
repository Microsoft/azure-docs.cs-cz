---
title: Správa vývojářských účtů pomocí skupin ve správě rozhraní Azure API
titleSuffix: Azure API Management
description: Zjistěte, jak spravovat vývojářské účty pomocí skupin ve správě rozhraní Azure API
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 182f0ea93ddfb2ab64de1c15b36958537fa5431f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75430709"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Vytváření skupin a jejich používání ke správě účtů vývojářů ve službě Azure API Management

Ve službě API Management se ke správě viditelnosti produktů pro vývojáře používají skupiny. Produkty jsou nejprve viditelné pro skupiny a pak vývojáři v těchto skupinách můžete zobrazit a přihlásit k odběru produktů, které jsou přidruženy ke skupinám. 

Služba API Management má následující neměnné systémové skupiny:

* **Správci** – členy této skupiny jsou správci předplatného Azure. Správci spravují instance služby API Management, vytváření rozhraní API, operace a produkty, které používají vývojáři.
* **Vývojáři** – do této skupiny patří ověření uživatelé portálu pro vývojáře. Vývojáři jsou zákazníci, kteří vytvářejí aplikace pomocí vašich rozhraní API. Vývojáři mají přístup k portálu pro vývojáře a vytvářejí aplikace, které volají operace rozhraní API.
* **Hosté** – do této skupiny patří neověření uživatelé portálu pro vývojáře, například potenciální zákazníci, kteří navštěvují portál pro vývojáře v instanci služby API Management. Můžete jim udělit omezený přístup jenom ke čtení, například k zobrazení rozhraní API bez možnosti jeho volání.

Kromě těchto systémových skupin můžou správci vytvářet vlastní skupiny nebo [využívat externí skupiny v přidružených klientech služby Azure Active Directory][leverage external groups in associated Azure Active Directory tenants]. Vlastní a externí skupiny můžete používat společně se systémovými skupinami, a nastavovat tak vývojářům viditelnost produktů s rozhraním API a přístup k nim. Můžete například vytvořit jednu vlastní skupinu pro vývojáře spojené s konkrétní partnerskou organizací a povolit jim přístup k rozhraním API z produktu, který obsahuje jenom příslušná rozhraní API. Uživatel může být členem několika skupin.

Tato příručka ukazuje, jak správci instance správy rozhraní API mohou přidávat nové skupiny a přidružit je k produktům a vývojářům.

Kromě vytváření a správy skupin na portálu pro vydavatele můžete vytvářet a spravovat své skupiny pomocí entity [Skupina](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) rozhraní API PRO správu rozhraní API.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Požadavky

Dokončení úkolů v tomto článku: [Vytvoření instance Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-group"></a><a name="create-group"> </a>Vytvoření skupiny

Tato část ukazuje, jak přidat novou skupinu do účtu api management.

1. Vyberte kartu **Skupiny** nalevo od obrazovky.
2. Klepněte na tlačítko **+Přidat**.
3. Zadejte jedinečný název skupiny a volitelný popis.
4. Stiskněte **Vytvořit**.

    ![Přidání nové skupiny](./media/api-management-howto-create-groups/groups001.png)

Jakmile je skupina vytvořena, je přidána do seznamu **Skupiny.** <br/>Chcete-li upravit **název** nebo **popis** skupiny, klepněte na název skupiny a **na nastavení**.<br/>Chcete-li skupinu odstranit, klepněte na název skupiny a stiskněte **klávesu Delete**.

Nyní, když je skupina vytvořena, může být přidružena k produktům a vývojářům.

## <a name="associate-a-group-with-a-product"></a><a name="associate-group-product"> </a>Přidružení skupiny k produktu

1. Vyberte kartu **Produkty** vlevo.
2. Klikněte na název požadovaného produktu.
3. Stiskněte **ovládací prvek Přístup**.
4. Klikněte na **+ Přidat skupinu**.

    ![Přidružení skupiny k produktu](./media/api-management-howto-create-groups/groups002.png)
5. Vyberte skupinu, kterou chcete přidat.

    ![Přidružení skupiny k produktu](./media/api-management-howto-create-groups/groups003.png)

    Chcete-li odebrat skupinu z produktu, klepněte na tlačítko **Odstranit**.

    ![Odstranění skupiny](./media/api-management-howto-create-groups/groups004.png)

Jakmile je produkt přidružen ke skupině, mohou vývojáři v této skupině produkt zobrazit a přihlásit se k jeho odběru.

> [!NOTE]
> Pokud chcete přidat skupiny Azure Active Directory, přečtěte [si, jak autorizovat vývojářské účty pomocí Azure Active Directory ve správě rozhraní Azure API](api-management-howto-aad.md).

## <a name="associate-groups-with-developers"></a><a name="associate-group-developer"> </a>Přidružení skupin k vývojářům

Tato část ukazuje, jak přidružit skupiny k členům.

1. Vyberte kartu **Skupiny** nalevo od obrazovky.
2. Vyberte možnost **Členové**.

    ![Přidání člena](./media/api-management-howto-create-groups/groups005.png)
3. Stiskněte **+Přidat** a vyberte člena.

    ![Přidání člena](./media/api-management-howto-create-groups/groups006.png)
4. Stiskněte **klávesu Select**.

Po přidání přidružení mezi vývojáře a skupinu jej můžete zobrazit na kartě **Uživatelé.**

## <a name="next-steps"></a><a name="next-steps"> </a>Další kroky

* Jakmile je vývojář přidán do skupiny, může zobrazit a přihlásit se k odběru produktů přidružených k této skupině. Další informace najdete v [tématu Jak vytvořit a publikovat produkt ve správě rozhraní Azure API][How create and publish a product in Azure API Management],
* Kromě vytváření a správy skupin na portálu pro vydavatele můžete vytvářet a spravovat své skupiny pomocí entity [Skupina](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) rozhraní API PRO správu rozhraní API.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
