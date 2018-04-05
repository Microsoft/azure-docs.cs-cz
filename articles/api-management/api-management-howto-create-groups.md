---
title: Spravovat účty pro vývojáře používání skupin v Azure API Management | Microsoft Docs
description: Zjistěte, jak chcete spravovat účty pro vývojáře používání skupin v Azure API Management
services: api-management
documentationcenter: ''
author: juliako
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 184c8fff35a59715407989589d6d69b3dc2481e0
ms.sourcegitcommit: 20d103fb8658b29b48115782fe01f76239b240aa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/03/2018
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Postup vytvoření a používání skupin Správa účtů pro vývojáře ve službě Azure API Management
Ve službě API Management se ke správě viditelnosti produktů pro vývojáře používají skupiny. Produkty se první dostupná pro skupiny, a pak mohou vývojáři v těchto skupinách zobrazovat a odebírat produkty, které jsou přidružené skupiny. 

Služba API Management má následující neměnné systémové skupiny:

* **Správci** – členy této skupiny jsou správci předplatného Azure. Správci spravují instance služby API Management, vytváření rozhraní API, operace a produkty, které používají vývojáři.
* **Vývojáři** – do této skupiny patří ověření uživatelé portálu pro vývojáře. Vývojáři jsou zákazníci, kteří vytvářejí aplikace pomocí vašich rozhraní API. Vývojáři mají přístup k portálu pro vývojáře a vytvářejí aplikace, které volají operace rozhraní API.
* **Hosté** – do této skupiny patří neověření uživatelé portálu pro vývojáře, například potenciální zákazníci, kteří navštěvují portál pro vývojáře v instanci služby API Management. Můžete jim udělit omezený přístup jenom ke čtení, například k zobrazení rozhraní API bez možnosti jeho volání.

Kromě těchto systémových skupin můžou správci vytvářet vlastní skupiny nebo [využívat externí skupiny v přidružených klientech služby Azure Active Directory][leverage external groups in associated Azure Active Directory tenants]. Vlastní a externí skupiny můžete používat společně se systémovými skupinami, a nastavovat tak vývojářům viditelnost produktů s rozhraním API a přístup k nim. Můžete například vytvořit jednu vlastní skupinu pro vývojáře spojené s konkrétní partnerskou organizací a povolit jim přístup k rozhraním API z produktu, který obsahuje jenom příslušná rozhraní API. Uživatel může být členem několika skupin.

Tato příručka ukazuje, jak přidat nové skupiny a přiřadit je k produktů a vývojáři správci instance API Management.

Kromě vytváření a Správa skupin na portálu vydavatele, můžete vytvořit a spravovat vaše skupiny pomocí rozhraní API REST API správy [skupiny](https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entity.

## <a name="prerequisites"></a>Požadavky

Dokončení úkolů v tomto článku: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>Vytvoření skupiny

V této části ukazuje, jak přidat novou skupinu ke svému účtu API Management.

1. Vyberte **skupiny** karty na levé straně obrazovky.
2. Klikněte na tlačítko **+ přidat**.
3. Zadejte jedinečný název skupiny a její nepovinný popis.
4. Stiskněte **Vytvořit**.

    ![Přidat novou skupinu](./media/api-management-howto-create-groups/groups001.png)

Po vytvoření skupiny je přidán do **skupiny** seznamu. <br/>Chcete-li upravit **název** nebo **popis** skupiny, klikněte na název skupiny a **nastavení**.<br/>Chcete-li odstranit skupinu, klikněte na název skupiny a stiskněte klávesu **odstranit**.

Teď, když je skupina vytvořená, může být přidružen produktů a vývojáři.

## <a name="associate-group-product"> </a>Spojit skupinu s produktem

1. Vyberte **produkty** karty na levé straně.
2. Klikněte na název požadovaného produktu.
3. Stiskněte klávesu **řízení přístupu**.
4. Klikněte na tlačítko **+ přidat skupinu**.

    ![Spojit skupinu s produktem](./media/api-management-howto-create-groups/groups002.png)
5. Vyberte skupinu, kterou chcete přidat.

    ![Spojit skupinu s produktem](./media/api-management-howto-create-groups/groups003.png)

    Chcete-li odebrat skupinu z produktu, klikněte na tlačítko **odstranit**.

    ![Odstranění skupiny](./media/api-management-howto-create-groups/groups004.png)

Jakmile produkt je přidružena ke skupině, vývojáři v této skupině můžou zobrazovat a odebírat produktu.

> [!NOTE]
> Chcete-li přidat skupiny Azure Active Directory, přečtěte si téma [autorizace vývojářským účtům pomocí služby Azure Active Directory v Azure API Management](api-management-howto-aad.md).

## <a name="associate-group-developer"> </a>Přidružení skupin k vývojářům

V této části ukazuje, jak přidružit členy skupiny.

1. Vyberte **skupiny** karty na levé straně obrazovky.
2. Vyberte **členy**.

    ![Přidat člena](./media/api-management-howto-create-groups/groups005.png)
3. Stiskněte klávesu **+ přidat** a vyberte člena.

    ![Přidat člena](./media/api-management-howto-create-groups/groups006.png)
4. Stiskněte klávesu **vyberte**.

Po přidání přidružení mezi na vývojáře a skupiny můžete zobrazit v **uživatelé** kartě.

## <a name="next-steps"></a>Další kroky

* Jakmile vývojář se přidá do skupiny, se můžou zobrazovat a odebírat produkty, které jsou přidružené k této skupině. Další informace najdete v tématu [vytvoření a publikování produktu v Azure API Management][How create and publish a product in Azure API Management],
* Kromě vytváření a Správa skupin na portálu vydavatele, můžete vytvořit a spravovat vaše skupiny pomocí rozhraní API REST API správy [skupiny](https://docs.microsoft.com/en-us/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entity.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
