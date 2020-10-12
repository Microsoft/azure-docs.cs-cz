---
title: Správa uživatelů v Azure FarmBeats
description: Tento článek popisuje, jak spravovat uživatele v Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 6ccc29422c6abf1120d13c05d10125efd0871ca6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "79502060"
---
# <a name="manage-users"></a>Správa uživatelů

Azure FarmBeats zahrnuje správu uživatelů pro uživatele, kteří jsou součástí vaší instance Azure Active Directory (Azure AD). Můžete přidat uživatele do instance Azure FarmBeats pro přístup k rozhraním API, zobrazit vygenerovaná mapování a získat přístup ke telemetrie senzorů z farmy.

## <a name="prerequisites"></a>Požadavky

- Vyžaduje se instalace Azure FarmBeats. Další informace najdete v tématu [instalace Azure FarmBeats](install-azure-farmbeats.md).
- ID e-mailu uživatelů, které chcete přidat nebo odebrat z instance služby Azure FarmBeats.

## <a name="manage-azure-farmbeats-users"></a>Správa uživatelů Azure FarmBeats

Azure FarmBeats používá pro ověřování, řízení přístupu a role Azure AD. Uživatele v tenantovi Azure AD můžete přidat jako uživatele v Azure FarmBeats.

> [!NOTE]
> Pokud uživatel není uživatelem tenanta Azure AD, dokončete instalaci podle pokynů v části **Přidání uživatelů Azure AD** .

Azure FarmBeats podporuje dva typy rolí uživatelů:

 - **Správce**: úplný přístup k rozhraním API Azure FarmBeats DataHub. Uživatelé v této roli můžou zadávat dotazy na všechny objekty Azure FarmBeats DataHub a provádět všechny operace z akcelerátoru FarmBeats.
 - Jen **pro čtení**: přístup jen pro čtení k rozhraním FarmBeats DataHub API. Uživatelé mohou zobrazit rozhraní API DataHub, řídicí panely akcelerátoru a mapy. Uživatelé s přístupem jen pro čtení nemůžou provádět operace, jako je generování map, přidružení zařízení nebo vytváření farem.

## <a name="add-users-to-azure-farmbeats"></a>Přidání uživatelů do Azure FarmBeats

Přidání uživatelů do Azure FarmBeats:

1. Přihlaste se k akcelerátoru a potom vyberte ikonu **Nastavení** .
2. Vyberte **Access Control**.

    ![Podokno nastavení farmy](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Zadejte ID e-mailu uživatele, kterému chcete udělit přístup.
4. Vyberte požadovanou roli, **správce** nebo **jen pro čtení**.
5. Vyberte **Přidat roli**.

Přidaný uživatel má teď přístup k Azure FarmBeats (DataHub a akcelerátor).

## <a name="delete-users-from-azure-farmbeats"></a>Odstranění uživatelů z Azure FarmBeats

Odebrání uživatelů ze systému Azure FarmBeats:

1. Přihlaste se k akcelerátoru a potom vyberte ikonu **Nastavení** .
2. Vyberte **Access Control**.
3. Vyberte **Odstranit**.

   Uživatel je odstraněn ze systému. Zobrazí se následující potvrzovací zpráva:

   ![Potvrzovací zpráva Azure FarmBeats](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Přidání uživatelů Azure AD

> [!NOTE]
> Uživatelé Azure FarmBeats musí existovat v tenantovi Azure AD ještě předtím, než je přiřadíte k aplikacím a rolím. Pokud uživatel v tenantovi Azure AD neexistuje, postupujte podle pokynů v této části. Pokud uživatel už existuje v tenantovi Azure AD, přeskočte pokyny.

Pokud chcete přidat uživatele do Azure AD, postupujte podle pokynů:

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com/).
2. V pravém horním rohu vyberte svůj účet a pak přepněte na tenanta Azure AD, který je přidružený k FarmBeats.
3. Vyberte **Azure Active Directory**  >  **Uživatelé**.

    Zobrazí se seznam uživatelů Azure AD.

4. Pokud chcete do adresáře přidat uživatele, vyberte **Nový uživatel**. Chcete-li přidat externího uživatele, vyberte možnost **Nový uživatel typu Host**.

    ![Podokno všichni uživatelé](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Vyberte jméno nového uživatele a pak vyplňte požadovaná pole pro daného uživatele.
6. Vyberte **Vytvořit**.

Informace o správě uživatelů Azure AD najdete v tématu [Přidání nebo odstranění uživatelů v Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/).

## <a name="next-steps"></a>Další kroky

Úspěšně jste přidali uživatele do instance služby Azure FarmBeats. Nyní se naučíte [vytvářet a spravovat farmy](manage-farms-in-azure-farmbeats.md#create-farms).
