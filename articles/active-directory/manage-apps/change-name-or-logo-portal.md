---
title: Změna názvu nebo loga podniková aplikace v Azure Active Directory | Dokumentace Microsoftu
description: Jak změnit název nebo loga pro vlastní podnikové aplikace v Azure Active Directory
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
ms.openlocfilehash: 30da8d6843c27c42d4d99adef50b9ad98a131c95
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "65780922"
---
# <a name="change-the-name-or-logo-of-an-enterprise-application-in-azure-active-directory"></a>Změna názvu nebo loga podniková aplikace v Azure Active Directory

Je snadné, chcete-li změnit název nebo loga pro vlastní podnikové aplikace v Azure Active Directory (Azure AD). Musíte mít příslušná oprávnění k provedení těchto změn a musíte být tvůrcem vlastní aplikace.

## <a name="how-do-i-change-an-enterprise-applications-name-or-logo"></a>Jak mohu změnit název nebo loga podniková aplikace?

1. Přihlaste se k [portálu Azure Active Directory](https://aad.portal.azure.com/) pomocí účtu, který má oprávnění globálního správce adresáře. **Centra pro správu Azure Active Directory** se zobrazí stránka.
2. V levém podokně vyberte **Podnikové aplikace**. Zobrazí se seznam podnikových aplikací.
3. Vyberte aplikaci. Zobrazí se stránka s přehledem aplikace.
4. V podokně s přehledem aplikace v rámci **spravovat** záhlaví, vyberte **vlastnosti**. **Vlastnosti** zobrazí se stránka.
5. Pokud chcete změnit název, vyberte **název** pole, zadejte nový název a stiskněte klávesu **Enter**.
6. Pokud chcete změnit logo, vyhledejte **Logo** pole a vyberte ikonu složky vedle **vyberte soubor** pole, která je nižší než aktuální obrázek loga aplikace.

   ![Vyberte příkaz Vlastnosti](./media/change-name-or-logo-portal/change-logo.png)

   Jinak pokud nejsou změnu loga, přejděte ke kroku 8.
7. Při výběru souboru vyberte požadovaný soubor jako nové logo. Název souboru se zobrazí v rozevíracím seznamu pod aktuální obrázek loga.

   > [!NOTE]
   > Azure vyžaduje obrázek loga soubor PNG a platí omezení na šířku, výšku a velikost souboru.
8. Vyberte **Uložit**. Pokud jste si zvolili nové logo **Logo** změny image pole tak, aby odrážely nový soubor loga.

## <a name="next-steps"></a>Další postup

* [Rychlé zprovoznění: Zobrazení skupiny a členové vaší organizace v Azure Active Directory](../fundamentals/active-directory-groups-view-azure-portal.md)
* [Přiřadit uživatele nebo skupiny k podnikové aplikace](assign-user-or-group-access-portal.md)
* [Odebrání uživatele nebo skupiny přiřazení podnikové aplikace](remove-user-or-group-access-portal.md)
* [Zakázat přihlášení uživatele pro podnikové aplikace](disable-user-sign-in-portal.md)
