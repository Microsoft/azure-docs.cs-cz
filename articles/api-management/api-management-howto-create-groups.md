---
title: Správa vývojářských účtů pomocí skupin v Azure API Management
titleSuffix: Azure API Management
description: Naučte se spravovat účty pro vývojáře pomocí skupin v Azure API Management. Vytvořte skupiny a přidružte je k produktům nebo vývojářům.
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
ms.openlocfilehash: ea674981036b4be292329a4b30b43180ed26d642
ms.sourcegitcommit: 30505c01d43ef71dac08138a960903c2b53f2499
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/15/2020
ms.locfileid: "92092779"
---
# <a name="how-to-create-and-use-groups-to-manage-developer-accounts-in-azure-api-management"></a>Vytváření skupin a jejich používání ke správě účtů vývojářů ve službě Azure API Management

Ve službě API Management se ke správě viditelnosti produktů pro vývojáře používají skupiny. Produkty jsou nejprve viditelné pro skupiny a potom mohou vývojáři v těchto skupinách zobrazit a přihlásit se k odběru produktů, které jsou přidruženy ke skupinám. 

Služba API Management má následující neměnné systémové skupiny:

* **Správci** – členy této skupiny jsou správci předplatného Azure. Správci spravují instance služby API Management, vytváření rozhraní API, operace a produkty, které používají vývojáři.
* **Vývojáři** – do této skupiny patří ověření uživatelé portálu pro vývojáře. Vývojáři jsou zákazníci, kteří vytvářejí aplikace pomocí vašich rozhraní API. Vývojáři mají přístup k portálu pro vývojáře a vytvářejí aplikace, které volají operace rozhraní API.
* **Hosté** – do této skupiny patří neověření uživatelé portálu pro vývojáře, například potenciální zákazníci, kteří navštěvují portál pro vývojáře v instanci služby API Management. Můžete jim udělit omezený přístup jenom ke čtení, například k zobrazení rozhraní API bez možnosti jeho volání.

Kromě těchto systémových skupin můžou správci vytvářet vlastní skupiny nebo [využívat externí skupiny v přidružených klientech služby Azure Active Directory][leverage external groups in associated Azure Active Directory tenants]. Vlastní a externí skupiny můžete používat společně se systémovými skupinami, a nastavovat tak vývojářům viditelnost produktů s rozhraním API a přístup k nim. Můžete například vytvořit jednu vlastní skupinu pro vývojáře spojené s konkrétní partnerskou organizací a povolit jim přístup k rozhraním API z produktu, který obsahuje jenom příslušná rozhraní API. Uživatel může být členem několika skupin.

V této příručce se dozvíte, jak můžou správci instance API Management přidávat nové skupiny a přidružit je k produktům a vývojářům.

Kromě vytváření a správy skupin na portálu vydavatele můžete vytvořit a spravovat skupiny pomocí entity API Management REST API [skupiny](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) .

[!INCLUDE [premium-dev-standard-basic.md](../../includes/api-management-availability-premium-dev-standard-basic.md)]

## <a name="prerequisites"></a>Předpoklady

Dokončení úloh v tomto článku: [vytvoření instance služby Azure API Management](get-started-create-service-instance.md).

[!INCLUDE [api-management-navigate-to-instance.md](../../includes/api-management-navigate-to-instance.md)]

## <a name="create-a-group"></a><a name="create-group"> </a>Vytvoření skupiny

V této části se dozvíte, jak přidat novou skupinu do účtu API Management.

1. Vyberte kartu **skupiny** vlevo na obrazovce.
2. Klikněte na **+ Přidat**.
3. Zadejte jedinečný název skupiny a případně i její popis.
4. Stiskněte **Vytvořit**.

    ![Přidání nové skupiny](./media/api-management-howto-create-groups/groups001.png)

Po vytvoření skupiny se přidá do seznamu **skupiny** . <br/>Chcete-li upravit **název** nebo **Popis** skupiny, klikněte na název skupiny a **Nastavení**.<br/>Skupinu odstraníte tak, že kliknete na název skupiny a stisknete **Odstranit**.

Teď, když je skupina vytvořená, může být přidružená k produktům a vývojářům.

## <a name="associate-a-group-with-a-product"></a><a name="associate-group-product"> </a>Přidružení skupiny k produktu

1. Vyberte kartu **produkty** vlevo.
2. Klikněte na název požadovaného produktu.
3. Stiskněte **řízení přístupu**.
4. Klikněte na **+ Přidat skupinu**.

    ![Snímek obrazovky, který zvýrazní tlačítko Přidat skupinu.](./media/api-management-howto-create-groups/groups002.png)
5. Vyberte skupinu, kterou chcete přidat.

    ![Snímek obrazovky, který zobrazuje vybranou skupinu a zvýrazní tlačítko pro výběr.](./media/api-management-howto-create-groups/groups003.png)

    Chcete-li odebrat skupinu z produktu, klikněte na tlačítko **Odstranit**.

    ![Odstranění skupiny](./media/api-management-howto-create-groups/groups004.png)

Jakmile je produkt přidružený ke skupině, můžou vývojáři v této skupině Zobrazit a přihlásit se k odběru produktu.

> [!NOTE]
> Pokud chcete přidat Azure Active Directory skupiny, přečtěte si téma [ověření účtů vývojářů pomocí Azure Active Directory ve službě Azure API Management](api-management-howto-aad.md).

## <a name="associate-groups-with-developers"></a><a name="associate-group-developer"> </a>Přidružení skupin k vývojářům

V této části se dozvíte, jak přidružit skupiny ke členům.

1. Vyberte kartu **skupiny** vlevo na obrazovce.
2. Vyberte možnost **Členové**.

    ![Přidání člena](./media/api-management-howto-create-groups/groups005.png)
3. Stiskněte **+ Přidat** a vyberte člena.

    ![Snímek obrazovky, který zvýrazní tlačítko Přidat, vybraného uživatele a tlačítko vybrat.](./media/api-management-howto-create-groups/groups006.png)
4. Stiskněte **Vybrat**.

Po přidání přidružení mezi vývojářem a skupinou si ho můžete zobrazit na kartě **Uživatelé** .

## <a name="next-steps"></a><a name="next-steps"> </a>Další kroky

* Jakmile se vývojář přidá do skupiny, může zobrazit a přihlásit se k odběru produktů přidružených k této skupině. Další informace najdete v tématu [jak vytvořit a publikovat produkt v Azure API Management][How create and publish a product in Azure API Management],
* Kromě vytváření a správy skupin na portálu vydavatele můžete vytvořit a spravovat skupiny pomocí entity API Management REST API [skupiny](/rest/api/apimanagement/apimanagementrest/azure-api-management-rest-api-group-entity) .

[Create a group]: #create-group
[Associate a group with a product]: #associate-group-product
[Associate groups with developers]: #associate-group-developer
[Next steps]: #next-steps

[How create and publish a product in Azure API Management]: api-management-howto-add-products.md

[Get started with Azure API Management]: get-started-create-service-instance.md
[Create an API Management service instance]: get-started-create-service-instance.md
[leverage external groups in associated Azure Active Directory tenants]: api-management-howto-aad.md
