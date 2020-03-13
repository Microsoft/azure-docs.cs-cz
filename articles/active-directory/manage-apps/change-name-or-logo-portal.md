---
title: Změna názvu nebo loga podnikové aplikace v Azure AD
description: Jak změnit název nebo logo vlastní podnikové aplikace v Azure Active Directory
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/05/2019
ms.author: mimart
ms.reviewer: asteen
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: d23849df75d1ab239eb269b462abb21df196e7e5
ms.sourcegitcommit: d322d0a9d9479dbd473eae239c43707ac2c77a77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2020
ms.locfileid: "79138497"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Změna názvu nebo loga podnikové aplikace v Azure Active Directory

Můžete snadno změnit název nebo logo vlastní podnikové aplikace v Azure Active Directory (Azure AD). Abyste mohli provádět tyto změny, musíte mít příslušná oprávnění a musíte být autorem vlastní aplikace.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Návody změnit název nebo logo podnikové aplikace?

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) pomocí účtu, který je globálním správcem adresáře. Zobrazí se stránka **Centrum pro správu Azure Active Directory** .
2. V levém podokně vyberte **Podnikové aplikace**. Zobrazí se seznam podnikových aplikací.
3. Vyberte aplikaci. Zobrazí se stránka s přehledem aplikace.
4. V podokně s přehledem aplikace vyberte v části **Spravovat** záhlaví možnost **vlastnosti**. Zobrazí se stránka **vlastnosti** .
5. Pokud chcete změnit název, vyberte pole **název** , zadejte nový název a stiskněte klávesu **ENTER**.
6. Pokud chcete změnit logo, vyhledejte pole **logo** a vyberte ikonu složky vedle pole **Vybrat soubor** , které se nachází pod obrázkem aktuálního loga aplikace.

   ![Výběr příkazu Properties](./media/change-name-or-logo-portal/change-logo.png)

   V opačném případě, pokud neměníte logo, pokračujte na krok 8.
7. V okně pro výběr souborů vyberte soubor, který chcete umístit jako nové logo. Název souboru se zobrazí v poli pod aktuálním obrázkem loga.

   > [!NOTE]
   > Azure vyžaduje, aby obrázek loga byl soubor PNG a platila omezení šířky, výšky a velikosti souboru. Vlastní loga musí mít velikost přesně 215 &times; 215 pixelů a musí být ve formátu PNG. Pro zobrazení nejvhodnějších pro uživatele doporučujeme použít pozadí s plnou barvou bez transparentnosti v logu aplikace.
8. Vyberte **Save** (Uložit). Pokud jste zvolili nové logo, obrázek pole **loga** se změní podle nového souboru loga.

## <a name="next-steps"></a>Další kroky

* [Rychlý Start: zobrazení skupin a členů vaší organizace v Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](assign-user-or-group-access-portal.md)
* [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace](remove-user-or-group-access-portal.md)
* [Zakázání přihlášení uživatelů pro podnikovou aplikaci](disable-user-sign-in-portal.md)
