---
title: Správa uživatelů v Azure FarmBeats
description: Tento článek popisuje, jak spravovat uživatele v Azure FarmBeats.
author: uhabiba04
ms.topic: article
ms.date: 12/02/2019
ms.author: v-umha
ms.openlocfilehash: 6ccc29422c6abf1120d13c05d10125efd0871ca6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79502060"
---
# <a name="manage-users"></a>Správa uživatelů

Azure FarmBeats zahrnuje správu uživatelů pro uživatele, kteří jsou součástí instance Azure Active Directory (Azure AD). Můžete přidat uživatele do instance Azure FarmBeats pro přístup k rozhraní API, zobrazení generovaných map a přístup k telemetrii senzoru z farmy.

## <a name="prerequisites"></a>Požadavky

- Je vyžadována instalace Azure FarmBeats. Další informace najdete [v tématu Instalace Azure FarmBeats](install-azure-farmbeats.md).
- E-mailová ID uživatelů, které chcete přidat nebo odebrat z instance Azure FarmBeats.

## <a name="manage-azure-farmbeats-users"></a>Správa uživatelů Azure FarmBeats

Azure FarmBeats používá Azure AD pro ověřování, řízení přístupu a role. Můžete přidat uživatele v tenantovi Azure AD jako uživatelé v Azure FarmBeats.

> [!NOTE]
> Pokud uživatel není klientazure AD uživatelem, postupujte podle pokynů v části **Přidat uživatele Azure AD** k dokončení nastavení.

Azure FarmBeats podporuje dva typy uživatelských rolí:

 - **Správce:** Úplný přístup k rozhraním API Azure FarmBeats Datahub. Uživatelé v této roli můžete dotazovat všechny objekty Azure FarmBeats Datahub a provádět všechny operace z FarmBeats Accelerator.
 - **Jen pro**čtení : Přístup jen pro čtení k dathubovým apim FarmBeats. Uživatelé mohou zobrazit rozhraní API Datahub, řídicí panely akcelerátoru a mapy. Uživatelé s přístupem jen pro čtení nemohou provádět operace, jako je generování map, přidružování zařízení nebo vytváření farem.

## <a name="add-users-to-azure-farmbeats"></a>Přidání uživatelů do Azure FarmBeats

Přidání uživatelů do Azure FarmBeats:

1. Přihlaste se k akcelerátoru a vyberte ikonu **Nastavení.**
2. Vyberte **možnost Řízení přístupu**.

    ![Podokno Nastavení farem](./media/create-farms-in-azure-farmbeats/settings-users-1.png)

3. Zadejte ID e-mailu uživatele, ke kterým chcete udělit přístup.
4. Vyberte požadovanou **roli, správce** nebo **jen pro čtení**.
5. Vyberte **Přidat roli**.

Přidaný uživatel má teď přístup k Azure FarmBeats (Datahub i Accelerator).

## <a name="delete-users-from-azure-farmbeats"></a>Odstranění uživatelů z Azure FarmBeats

Odebrání uživatelů ze systému Azure FarmBeats:

1. Přihlaste se k akcelerátoru a vyberte ikonu **Nastavení.**
2. Vyberte **možnost Řízení přístupu**.
3. Vyberte **Odstranit**.

   Uživatel je odstraněn ze systému. Obdržíte následující potvrzovací zprávu:

   ![Potvrzovací zpráva Azure FarmBeats](./media/create-farms-in-azure-farmbeats/manage-users-2.png)

## <a name="add-azure-ad-users"></a>Přidání uživatelů Azure AD

> [!NOTE]
> Uživatelé Azure FarmBeats musí existovat v tenantovi Azure AD, než je přiřadíte k aplikacím a rolím. Pokud uživatel neexistuje v tenantovi Azure AD, postupujte podle pokynů v této části. Přeskočte pokyny, pokud uživatel již existuje v tenantovi Azure AD.

Postupujte podle pokynů pro přidání uživatelů do Azure AD:

1. Přihlaste se k [portálu Azure](https://portal.azure.com/).
2. V pravém horním rohu vyberte svůj účet a pak přepněte na klienta Azure AD, který je přidružený k FarmBeats.
3. Vyberte**možnost Uživatelé** **služby Azure active directory** > .

    Zobrazí se seznam uživatelů Služby Azure AD.

4. Chcete-li přidat uživatele do adresáře, vyberte **možnost Nový uživatel**. Chcete-li přidat externího uživatele, vyberte **možnost Nový uživatel typu Host**.

    ![Podokno Všichni uživatelé](./media/create-farms-in-azure-farmbeats/manage-users-3.png)

5. Vyberte jméno nového uživatele a vyplňte požadovaná pole pro tohoto uživatele.
6. Vyberte **Vytvořit**.

Informace o správě uživatelů Azure AD najdete v tématu [Přidání nebo odstranění uživatelů ve službě Azure AD](https://docs.microsoft.com/azure/active-directory/fundamentals/add-users-azure-active-directory/).

## <a name="next-steps"></a>Další kroky

Úspěšně jste přidali uživatele do instance Azure FarmBeats. Nyní se naučte vytvářet [a spravovat farmy](manage-farms-in-azure-farmbeats.md#create-farms).
