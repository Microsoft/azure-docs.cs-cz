---
title: Správa uživatelů
description: Popisuje, jak spravovat uživatele.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 37dacf0adfc9e3dcbab963cef50e2ee5209df314
ms.sourcegitcommit: c38a1f55bed721aea4355a6d9289897a4ac769d2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/05/2019
ms.locfileid: "74852351"
---
# <a name="manage-users"></a>Správa uživatelů

Azure FarmBeats zahrnuje správu uživatelů pro lidi, kteří jsou součástí vaší Azure Active Directory (Azure AD). Pro přístup k rozhraním API budete moct přidat uživatele do instance Azure FarmBeats, zobrazit vygenerované mapy a telemetrii senzorů z farmy.

## <a name="prerequisites"></a>Předpoklady

- Vyžaduje se nasazení služby Azure FarmBeats. Další informace o tom, jak nastavit Azure FarmBeats, najdete v tématu věnovaném [nasazení FarmBeats](prepare-for-deployment.md) .
- ID e-mailu uživatelů, které chcete přidat nebo odebrat z instance služby Azure FarmBeats.

## <a name="manage-azure-farmbeats-users"></a>Správa uživatelů Azure FarmBeats

Azure FarmBeats používá pro ověřování, řízení přístupu a role službu Azure AD. Uživatele v tenantovi Azure AD je možné přidat jako uživatele v Azure FarmBeats.

> [!NOTE]
> Pokud se uživatel, který se pokoušíte přidat, nenachází v tenantovi Azure AD, dokončete instalaci podle pokynů v části **Přidání uživatelů Azure AD** a potom pokračujte v nastavování jako uživatelé Azure FarmBeats.

**Role**

V Azure FarmBeats ještě existují dva druhy rolí uživatelů:

 - **Správce** – veškerý přístup k rozhraním API služby Azure FarmBeats data hub. Uživatelé v této roli můžou zadat dotaz na všechny objekty centra dat Azure FarmBeats a provádět všechny operace z akcelerátoru FarmBeats.
 - Jen **pro čtení** – přístup jen pro čtení k rozhraním API pro FarmBeats data hub. Uživatelé mohou zobrazit rozhraní API centra dat, řídicí panely akcelerátoru a mapy. Uživatel s rolí "jen pro čtení" nebude moci provádět žádné operace, jako je generování map, přidružení zařízení nebo vytváření farem.


## <a name="add-user-to-azure-farmbeats"></a>Přidat uživatele do Azure FarmBeats

Pokud chcete přidat uživatele do Azure FarmBeats, 
1.  Přihlaste se k akcelerátoru a klikněte na ikonu nastavení.
2.  Klikněte na Access Control

    ![Beats farmy projektu](./media/create-farms/settings-users-1.png)

3.  Zadejte ID e-mailu uživatele, kterému chcete udělit přístup.
4.  Vybrat požadovanou roli – správce nebo jen pro čtení
5.  Klikněte na Přidat roli.

Přidaní uživatelé teď budou mít přístup k Azure FarmBeats (data hub i akcelerátor).

## <a name="delete-user-from-azure-farmbeats"></a>Odstranění uživatele z Azure FarmBeats

Pokud chcete odebrat uživatele ze systému Azure FarmBeats, můžete
1.  Přihlaste se k akcelerátoru a klikněte na ikonu nastavení.
2.  Klikněte na Access Control
3.  Klikněte na ikonu Odstranit u ID e-mailu uživatele, kterého chcete odebrat.

Uživatel je odebrán ze systému. K potvrzení úspěšné operace se zobrazí následující zpráva.


![Beats farmy projektu](./media/create-farms/manage-users-2.png)


## <a name="add-azure-ad-users"></a>Přidání uživatelů Azure AD

> [!NOTE]
> Budete muset provést následující kroky, jenom když se uživatel, který se pokoušíte poskytnout přístup ke službě Azure FarmBeats, nenachází v tenantovi Azure AD. Pokud uživatel již existuje, není nutné provádět tyto kroky.

FarmBeats uživatelé musí existovat v tenantovi Azure AD, aby je bylo možné přiřadit k aplikacím a rolím. Pokud chcete přidat uživatele do služby Azure AD, použijte následující postup:
1.  Přihlaste se na web [Azure Portal](https://portal.azure.com/).
2.  V pravém horním rohu vyberte svůj účet a přepněte se na tenanta Azure AD, který je přidružený k FarmBeats.
3.  Vyberte **Azure Active Directory > uživatelé**. Zobrazí se seznam uživatelů v adresáři.
4.  Pokud chcete do adresáře přidat uživatele, vyberte **Nový uživatel**. U externích uživatelů vyberte **Nový uživatel typu Host**.

    ![Beats farmy projektu](./media/create-farms/manage-users-3.png)

5.  Vyplňte požadovaná pole pro nového uživatele. Vyberte **Create** (Vytvořit).

Další podrobnosti o správě uživatelů v rámci služby Azure AD najdete v dokumentaci k [Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/) .

## <a name="next-steps"></a>Další kroky

Nasadili jste Azure FarmBeats. Nyní se naučíte, jak [vytvářet farmy](manage-farms.md#create-farms).

