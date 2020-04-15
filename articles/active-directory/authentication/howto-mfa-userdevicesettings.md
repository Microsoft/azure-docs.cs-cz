---
title: Správa uživatelských nastavení pro vícefaktorové ověřování Azure – Azure Active Directory
description: Zjistěte, jak můžete nakonfigurovat uživatelská nastavení Služby Azure Active Directory pro vícefaktorové ověřování Azure
services: multi-factor-authentication
ms.service: active-directory
ms.subservice: authentication
ms.topic: how-to
ms.date: 04/13/2020
ms.author: iainfou
author: iainfoulds
manager: daveba
ms.reviewer: michmcla
ms.collection: M365-identity-device-management
ms.openlocfilehash: 048224a55c2bbcbc99281d070d88d34e2dc77168
ms.sourcegitcommit: 7e04a51363de29322de08d2c5024d97506937a60
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2020
ms.locfileid: "81309757"
---
# <a name="manage-user-settings-for-azure-multi-factor-authentication"></a>Správa uživatelských nastavení pro vícefaktorové ověřování Azure

Chcete-li pomoci spravovat uživatele Azure Multi-Factor Authentication, můžete vyžadovat, aby uživatelé resetovali své heslo, znovu se zaregistrovali pro vícefaktorové ověřování nebo odvolali existující relace Vícefaktorové ověřování. Pro uživatele, kteří definovali hesla aplikací, můžete také tyto hesla odstranit, což způsobí selhání staršího ověřování v těchto aplikacích. Tyto akce mohou být nezbytné, pokud potřebujete poskytnout pomoc uživateli nebo chcete obnovit jeho stav zabezpečení.

## <a name="manage-user-authentication-options"></a>Správa možností ověřování uživatelů

Pokud je vám přiřazena role *Správce ověřování,* můžete od uživatelů požadovat, aby resetovali své heslo, znovu se zaregistrovali pro vícefaktorové ověřování nebo odvolali existující relace Vícefaktorové ověřování z objektu uživatele. Chcete-li spravovat uživatelská nastavení, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Vlevo vyberte **možnost Uživatelé** > **služby** > Azure Active Directory**Všichni uživatelé**.
1. Vyberte uživatele, u kterýchcete provést akci, a vyberte **možnost Metody ověřování**. V horní části okna zvolte jednu z následujících možností pro uživatele:
   - **Resetovat heslo** resetuje heslo uživatele a přiřadí dočasné heslo, které musí být změněno při příštím přihlášení.
   - **Vyžadovat znovu zaregistrovat Vícefaktorové** ověřování umožňuje, aby při příštím přihlášení uživatele, jsou požádáni o nastavení nové metody ověřování Vícefaktorové ověřování.
   - **Odvolat relace vícefaktorové ověřování** vymaže uživatele zapamatované relace Vícefaktorové ověřování a vyžaduje, aby provedli vícefaktorové ověřování při příštím vyžadovaném zásadami na zařízení.

   ![Správa metod ověřování z webu Azure Portal](./media/howto-mfa-userdevicesettings/manage-authentication-methods-in-azure.png)

## <a name="delete-users-existing-app-passwords"></a>Odstranění existujících hesel aplikací uživatelů

V případě potřeby můžete odstranit všechna hesla aplikací, která uživatel vytvořil. Aplikace, které nejsou v prohlížeči a které byly přidruženy k těmto heslům aplikací, přestanou fungovat, dokud se nevytvoří nové heslo aplikace. K provedení této akce jsou vyžadována oprávnění *globálního správce.*

Chcete-li odstranit hesla aplikací uživatele, proveďte následující kroky:

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. Na levé straně vyberte možnost**Uživatelé služby** >  **Azure Active Directory** > **Všichni uživatelé**.
1. Vyberte **vícefaktorové ověřování**. Chcete-li zobrazit tuto možnost nabídky, může být nutné posunout doprava. Výběrem ukázkového snímku obrazovky níže zobrazíte celé okno portálu Azure a umístění nabídky:[![](media/howto-mfa-userstates/selectmfa-cropped.png "Vyberte vícefaktorové ověřování z okna Uživatelé ve službě Azure AD.")](media/howto-mfa-userstates/selectmfa.png#lightbox)
1. Zaškrtněte políčko vedle uživatele nebo uživatelů, které chcete spravovat. Vpravo se zobrazí seznam možností rychlého kroku.
1. Vyberte **Spravovat nastavení uživatelů**, zaškrtněte políčko Odstranit všechna existující hesla aplikací **generovaná vybranými uživateli**, jak je znázorněno v následujícím příkladu: ![Odstranit všechna existující hesla aplikací.](./media/howto-mfa-userdevicesettings/deleteapppasswords.png)
1. Vyberte **uložit**a **zavřete**.

## <a name="next-steps"></a>Další kroky

Tento článek pomohl nakonfigurovat jednotlivá uživatelská nastavení. Pokud chcete nakonfigurovat nastavení služby Azure Multi-Factor Authentication, přečtěte si informace [o konfiguraci nastavení azure multifaktorového ověřování.](howto-mfa-mfasettings.md)

Pokud vaši uživatelé potřebují pomoc, podívejte se na [uživatelskou příručku pro Azure Multi-Factor Authentication](../user-help/multi-factor-authentication-end-user.md).
