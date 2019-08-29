---
title: Jak nakonfigurovat jednotné přihlašování k heslům pro aplikaci Galerie Azure AD | Microsoft Docs
description: Jak nakonfigurovat aplikaci pro zabezpečené jednotné přihlašování založené na heslech, když je už uvedená v galerii aplikací Azure AD
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/11/2017
ms.author: mimart
ms.collection: M365-identity-device-management
ROBOTS: NOINDEX
ms.openlocfilehash: 9fb33c4110a590539c85364885da9a27853f6bd0
ms.sourcegitcommit: aaa82f3797d548c324f375b5aad5d54cb03c7288
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2019
ms.locfileid: "70146892"
---
# <a name="configure-password-single-sign-on-for-an-azure-ad-gallery-application"></a>Konfigurace jednotného přihlašování k heslům pro aplikaci Galerie Azure AD

Když přidáte aplikaci z [Galerie aplikací Azure Active Directory (Azure AD)](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis), můžete zvolit, jak chcete, aby se uživatelé k této aplikaci přihlásili. Tuto volbu můžete kdykoli nakonfigurovat výběrem **jednotného přihlašování** v podnikové aplikaci v [Azure Portal](https://portal.azure.com/).

Jedna z dostupných možností jednotného přihlašování (SSO) je [jednotné přihlašování založené](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis)na heslech. Jedná se o skvělý způsob, jak rychle zahájit integraci aplikací do Azure AD. Umožňuje následující akce:

-   Bezpečně ukládá a přehrává uživatelská jména a hesla pro aplikaci, kterou jste integrací se službou Azure AD.

-   Poskytuje podporu pro aplikace, které vyžadují více přihlašovacích polí nad rámec pouze polí uživatelské jméno a heslo.

-   Umožňuje přizpůsobit popisky polí uživatelské jméno a heslo, které se uživatelům zobrazí na [přístupovém panelu aplikace](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) při zadání přihlašovacích údajů.

-   Umožňuje uživatelům zadat vlastní uživatelská jména a hesla pro všechny existující účty aplikací, které ručně zadají na [přístupovém panelu aplikací](https://docs.microsoft.com/azure/active-directory/active-directory-saas-access-panel-introduction) .

-   Umožňuje členovi obchodní skupiny používat funkci [samoobslužného přístupu k aplikacím](https://docs.microsoft.com/azure/active-directory/active-directory-self-service-application-access) k určení uživatelských jmen a hesel přiřazených uživateli.

-   Umožňuje správci zadat uživatelská jména a hesla přiřazená uživateli pomocí funkce aktualizovat přihlašovací údaje, když [přiřadí uživatele k aplikaci](#assign-a-user-to-an-application-directly) .

-   Umožňuje správci pomocí funkce aktualizovat přihlašovací údaje zadat sdílené uživatelské jméno nebo heslo pro skupinu osob, když [přiřadí skupinu k aplikaci](#assign-an-application-to-a-group-directly) .

Následující část popisuje, jak můžete povolit [jednotné přihlašování založené](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis) na heslech k aplikaci, která už je v [galerii aplikací Azure AD](https://docs.microsoft.com/azure/active-directory/active-directory-appssoaccess-whatis).

## <a name="overview-of-required-steps"></a>Přehled požadovaných kroků
Pokud chcete nakonfigurovat aplikaci z Galerie Azure AD, musíte provést tyto kroky:

-   [Přidání aplikace z Galerie Azure AD](#add-an-application-from-the-azure-ad-gallery)

-   [Konfigurace aplikace pro jednotné přihlašování k heslům](#configure-the-application-for-password-single-sign-on)

-   Přiřaďte aplikaci uživateli nebo skupině:

    -   [Přiřazení uživatele k aplikaci přímo](#assign-a-user-to-an-application-directly)

    -   [Přiřazení aplikace ke skupině přímo](#assign-an-application-to-a-group-directly)

## <a name="add-an-application-from-the-azure-ad-gallery"></a>Přidání aplikace z Galerie Azure AD

Pokud chcete přidat aplikaci z Galerie Azure AD, postupujte podle těchto kroků:

1.  Otevřete [Azure Portal](https://portal.azure.com)a přihlaste se jako **globální správce** nebo spolusprávce.

2.  Otevřete **rozšíření Azure Active Directory** výběrem možnosti **všechny služby** v horní části nabídky na levé straně.

3.  Do vyhledávacího pole zadejte **Azure Active Directory** a pak vyberte položku **Azure Active Directory** .

4.  V nabídce Azure AD na levé straně vyberte **podnikové aplikace** .

5.  V pravém horním rohu podokna **podnikové aplikace** vyberte tlačítko **Přidat** .

6.  Do pole **Zadejte název** v části **Přidat z Galerie** zadejte název aplikace.

7.  Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

8.  Před přidáním aplikace můžete změnit její název v poli **název** .

9.  Chcete-li přidat aplikaci, vyberte možnost **Přidat** .

Po krátké době uvidíte podokno konfigurace aplikace.

## <a name="configure-the-application-for-password-single-sign-on"></a>Konfigurace aplikace pro jednotné přihlašování k heslům

Pro konfiguraci jednotného přihlašování pro aplikaci použijte následující postup:

1. Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se jako **globální správce** nebo **spolusprávce**.

2. Otevřete **rozšíření Azure Active Directory** výběrem možnosti **všechny služby** v horní části nabídky na levé straně.

3. Do vyhledávacího pole zadejte **Azure Active Directory** a pak vyberte položku **Azure Active Directory** .

4. V nabídce Azure Active Directory na levé straně vyberte **podnikové aplikace** .

5. Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

   Pokud nevidíte požadovanou aplikaci, použijte ovládací prvek **filtru** v horní části **všech aplikací**a nastavte možnost **Zobrazit** pro **všechny aplikace**.

6. Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

7. Po načtení aplikace vyberte v nabídce aplikace na levé straně **jednotné přihlašování** .

8. Vyberte režim **přihlašování založený na hesle** .

9. [Přiřaďte uživatele k aplikaci](#assign-a-user-to-an-application-directly).

10. Přihlašovací údaje pro uživatele můžete zadat taky tak, že vyberete řádek uživatele, vyberete **Aktualizovat přihlašovací údaje**a potom zadáte uživatelské jméno a heslo. V opačném případě se uživatelům zobrazí výzva k zadání přihlašovacích údajů při spuštění aplikace.

## <a name="assign-a-user-to-an-application-directly"></a>Přiřazení uživatele k aplikaci přímo

Chcete-li přiřadit jednoho nebo více uživatelů k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se jako **globální správce**.

2. Otevřete **rozšíření Azure Active Directory** výběrem možnosti **všechny služby** v horní části nabídky na levé straně.

3. Do vyhledávacího pole zadejte **Azure Active Directory** a pak vyberte položku **Azure Active Directory** .

4. V nabídce Azure Active Directory na levé straně vyberte **podnikové aplikace** .

5. Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

   Pokud nevidíte požadovanou aplikaci, použijte ovládací prvek **filtru** v horní části **všech aplikací**a nastavte možnost **Zobrazit** pro **všechny aplikace**.

6. Vyberte aplikaci, ke které chcete přiřadit uživatele.

7. Po načtení aplikace vyberte **Uživatelé a skupiny** v nabídce aplikace na levé straně.

8. Kliknutím na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** otevřete podokno **Přidat přiřazení** .

9. V podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

10. Do vyhledávacího pole **Hledat podle jména nebo e-mailové adresy** zadejte celé jméno nebo e-mailovou adresu uživatele.

11. Najeďte myší na uživatele v seznamu a potom zaškrtněte políčko vedle profilové fotografie nebo loga uživatele a přidejte je do **vybraného** seznamu.

12. Volitelné: Chcete-li přidat více než jednoho uživatele, zadejte do pole **Hledat podle jména nebo e-mailové adresy** jiné celé jméno nebo e-mailovou adresu a zaškrtněte políčko pro daného uživatele, aby je přidal do **vybraného** seznamu.

13. Až budete s výběrem uživatelů hotovi, použijte tlačítko **Vybrat** a přidejte je do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. Volitelné: Pomocí příkazu **Vybrat roli** v podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným uživatelům.

15. Vyberte **přiřadit** a přiřaďte aplikaci k vybraným uživatelům.

## <a name="assign-an-application-to-a-group-directly"></a>Přiřazení aplikace ke skupině přímo

Chcete-li přiřadit jednu nebo více skupin k aplikaci přímo, postupujte podle následujících kroků:

1. Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se jako **globální správce**.

2. Otevřete **rozšíření Azure Active Directory** výběrem možnosti **všechny služby** v horní části nabídky na levé straně.

3. Do vyhledávacího pole zadejte **Azure Active Directory** a pak vyberte položku **Azure Active Directory** .

4. V nabídce Azure AD na levé straně vyberte **podnikové aplikace** .

5. Výběrem **všech aplikací** zobrazíte seznam všech aplikací.

   Pokud nevidíte požadovanou aplikaci, použijte ovládací prvek **filtru** v horní části **všech aplikací** a nastavte možnost **Zobrazit** pro **všechny aplikace**.

6. Vyberte aplikaci, ke které chcete přiřadit uživatele.

7. Po načtení aplikace vyberte **Uživatelé a skupiny** v nabídce aplikace na levé straně.

8. Kliknutím na tlačítko **Přidat** v horní části seznamu **uživatelů a skupin** otevřete podokno **Přidat přiřazení** .

9. V podokně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .

10. Do pole **Hledat podle jména nebo e-mailové adresy** zadejte úplný název skupiny, kterou chcete přiřadit.

11. Najeďte myší na **skupinu** v seznamu a potom zaškrtněte políčko vedle profilové fotografie nebo loga skupiny a přidejte skupinu do **vybraného** seznamu.

12. Volitelné: Chcete-li přidat více než jednu skupinu, zadejte do vyhledávacího pole **Hledat podle názvu nebo e-mailové adresy** jiný název celé skupiny a potom zaškrtněte příslušné políčko, chcete-li přidat tuto skupinu do **vybraného** seznamu.

13. Až budete s výběrem skupin hotovi, použijte tlačítko **Vybrat** a přidejte je do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. Volitelné: Pomocí příkazu **Vybrat roli** v podokně **Přidat přiřazení** vyberte roli, kterou chcete přiřadit vybraným skupinám.

15. Vyberte **přiřadit** a přiřaďte aplikaci k vybraným skupinám.

Po krátké době by uživatelé měli být schopni spouštět tyto aplikace z přístupového panelu.

## <a name="next-steps"></a>Další kroky
[Pomocí proxy aplikací zajistěte, aby vaše aplikace poskytovaly jednotné přihlašování](application-proxy-configure-single-sign-on-with-kcd.md).
