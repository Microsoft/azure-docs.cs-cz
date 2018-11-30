---
title: Správa účtů vývojářů pomocí skupin ve službě Azure API Management | Dokumentace Microsoftu
description: Další informace o správě vývojářských účtů pomocí skupin ve službě Azure API Management
services: api-management
documentationcenter: ''
author: vladvino
manager: cfowler
editor: ''
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/13/2018
ms.author: apimpm
ms.openlocfilehash: 5392cf5463dd0b11d1ce53856c8e4e2e788892b0
ms.sourcegitcommit: 5aed7f6c948abcce87884d62f3ba098245245196
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/28/2018
ms.locfileid: "52442409"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Vytvoření a použití skupin pro správu účtů pro vývojáře ve službě Azure API Management

Ve službě API Management se ke správě viditelnosti produktů pro vývojáře používají skupiny. Produkty jsou první dostupná pro skupiny a potom vývojáři v těchto skupinách můžete zobrazit a přihlásit k odběru produktů, které jsou spojeny skupinami. 

Služba API Management má následující neměnné systémové skupiny:

* **Správci** – členy této skupiny jsou správci předplatného Azure. Správci spravují instance služby API Management, vytváření rozhraní API, operace a produkty, které používají vývojáři.
* **Vývojáři** – do této skupiny patří ověření uživatelé portálu pro vývojáře. Vývojáři jsou zákazníci, kteří vytvářejí aplikace pomocí vašich rozhraní API. Vývojáři mají přístup k portálu pro vývojáře a vytvářejí aplikace, které volají operace rozhraní API.
* **Hosté** – do této skupiny patří neověření uživatelé portálu pro vývojáře, například potenciální zákazníci, kteří navštěvují portál pro vývojáře v instanci služby API Management. Můžete jim udělit omezený přístup jenom ke čtení, například k zobrazení rozhraní API bez možnosti jeho volání.

Kromě těchto systémových skupin můžou správci vytvářet vlastní skupiny nebo [využívat externí skupiny v přidružených tenantů Azure Active Directory][leverage external groups in associated Azure Active Directory tenants]. Vlastní a externí skupiny můžete používat společně se systémovými skupinami, a nastavovat tak vývojářům viditelnost produktů s rozhraním API a přístup k nim. Můžete například vytvořit jednu vlastní skupinu pro vývojáře spojené s konkrétní partnerskou organizací a povolit jim přístup k rozhraním API z produktu, který obsahuje jenom příslušná rozhraní API. Uživatel může být členem několika skupin.

Tato příručka ukazuje, jak přidávat nové skupiny a přiřaďte je k produktům a vývojáři správci instance API managementu.

Vedle vytváření a Správa skupin v portálu pro vydavatele, můžete vytvořit a spravovat skupiny pomocí REST API služby API Management [skupiny](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entity.

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Požadavky

Dokončení úkolů v tomto článku: [vytvoření instance Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-group"> </a>Vytvoření skupiny

Tato část ukazuje, jak přidat novou skupinu ke svému účtu API Management.

1. Vyberte **skupiny** karty na levé straně obrazovky.
2. Klikněte na tlačítko **+ přidat**.
3. Zadejte jedinečný název pro skupinu a volitelně také popis.
4. Stiskněte **Vytvořit**.

    ![Přidat novou skupinu](./media/api-management-howto-create-groups/groups001.png)

Jakmile se vytvoří skupina, přidá se do **skupiny** seznamu. <br/>Chcete-li upravit **název** nebo **popis** skupiny, klikněte na název skupiny a **nastavení**.<br/>Pokud chcete odstranit skupinu, klikněte na název skupiny a stiskněte klávesu **odstranit**.

Teď, když se vytvoří skupina, dá se přidružit s produkty a vývojáři.

## <a name="associate-group-product"> </a>Přidružení skupiny s produktem

1. Vyberte **produkty** karty na levé straně.
2. Klikněte na název požadovaného produktu.
3. Stisknutím klávesy **řízení přístupu**.
4. Klikněte na tlačítko **+ přidat skupinu**.

    ![Přidružení skupiny s produktem](./media/api-management-howto-create-groups/groups002.png)
5. Vyberte skupinu, kterou chcete přidat.

    ![Přidružení skupiny s produktem](./media/api-management-howto-create-groups/groups003.png)

    Chcete-li odebrat skupiny z produktu, klikněte na tlačítko **odstranit**.

    ![Odstranění skupiny](./media/api-management-howto-create-groups/groups004.png)

Jakmile produkt je přiřadit ke skupině, vývojářům v této skupině můžou zobrazovat a odebírat produktu.

> [!NOTE]
> Přidání skupin Azure Active Directory naleznete v tématu [autorizace vývojářských účtů pomocí Azure Active Directory ve službě Azure API Management](api-management-howto-aad.md).

## <a name="associate-group-developer"> </a>Přidružení skupin k vývojářům

Tato část ukazuje, jak přidružit členy skupiny.

1. Vyberte **skupiny** karty na levé straně obrazovky.
2. Vyberte **členy**.

    ![Přidat člena](./media/api-management-howto-create-groups/groups005.png)
3. Stisknutím klávesy **+ přidat** a vyberte člena.

    ![Přidat člena](./media/api-management-howto-create-groups/groups006.png)
4. Stisknutím klávesy **vyberte**.

Po přidání přidružení mezi vývojáři a skupiny můžete zobrazit ho **uživatelé** kartu.

## <a name="next-steps"></a>Další kroky

* Jakmile vývojář se přidá do skupiny, mohou zobrazit a přihlásit k odběru produktů přidružené k této skupině. Další informace najdete v tématu [vytvoření a publikování produktu ve službě Azure API Management][How create and publish a product in Azure API Management],
* Vedle vytváření a Správa skupin v portálu pro vydavatele, můžete vytvořit a spravovat skupiny pomocí REST API služby API Management [skupiny](https://docs.microsoft.com/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) entity.

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
