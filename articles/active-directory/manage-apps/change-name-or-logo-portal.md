---
title: Změna názvu nebo loga podnikové aplikace ve službě Azure AD
description: Jak změnit název nebo logo vlastní podnikové aplikace ve službě Azure Active Directory
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79138497"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Změna názvu nebo loga podnikové aplikace ve službě Azure Active Directory

Je snadné změnit název nebo logo pro vlastní podnikovou aplikaci ve službě Azure Active Directory (Azure AD). K provedení těchto změn musíte mít příslušná oprávnění a musíte být tvůrcem vlastní aplikace.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Jak změním název nebo logo podnikové aplikace?

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) pomocí účtu, který je globálním správcem adresáře. Zobrazí se stránka **Centra pro správu Služby Azure Active Directory.**
2. V levém podokně vyberte **Podnikové aplikace**. Zobrazí se seznam podnikových aplikací.
3. Vyberte aplikaci. Zobrazí se stránka s přehledem aplikace.
4. V podokně přehled aplikace vyberte v části **Spravovat** nadpis **Vlastnosti**. Zobrazí se stránka **Vlastnosti.**
5. Pokud chcete změnit název, zaškrtněte pole **Název,** zadejte nový název a stiskněte **Enter**.
6. Pokud chcete změnit logo, najděte pole **Logo** a vyberte ikonu složky vedle pole **Vybrat soubor,** které je pod aktuálním obrázkem loga aplikace.

   ![Výběr příkazu vlastností](./media/change-name-or-logo-portal/change-logo.png)

   V opačném případě, pokud neměníte logo, přejděte ke kroku 8.
7. Ve výběru souborů vyberte požadovaný soubor jako nové logo. Název souboru se zobrazí v poli pod aktuálním obrázkem loga.

   > [!NOTE]
   > Azure vyžaduje, aby image loga byla souborPNG a vztahuje se na omezení šířky, výšky a velikosti souboru. Vlastní loga musí mít &times; velikost přesně 215 215 pixelů a musí být ve formátu PNG. Doporučujeme použít jednobarevné pozadí bez průhlednosti loga aplikace, aby se uživatelům co nejlépe zobrazovaly.
8. Vyberte **Uložit**. Pokud jste zvolili nové logo, obrázek pole **Logo** se změní tak, aby odrážel nový soubor loga.

## <a name="next-steps"></a>Další kroky

* [Rychlý start: Zobrazení skupin a členů vaší organizace ve službě Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Přiřazení uživatele nebo skupiny k podnikové aplikaci](assign-user-or-group-access-portal.md)
* [Odebrání přiřazení uživatele nebo skupiny z podnikové aplikace](remove-user-or-group-access-portal.md)
* [Zakázání přihlášení uživatelů k podnikové aplikaci](disable-user-sign-in-portal.md)
