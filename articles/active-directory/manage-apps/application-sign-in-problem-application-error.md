---
title: Po přihlášení se na stránce aplikace zobrazí chybová zpráva | Dokumenty společnosti Microsoft
description: Jak vyřešit problémy s přihlášením azure ad, když aplikace vrátí chybovou zprávu.
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
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: 9b8d20b31e96973a492355f0515d0532deea0ac9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77185481"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Stránka aplikace zobrazuje chybovou zprávu poté, co se uživatel přihlásí

V tomto scénáři Azure Active Directory (Azure AD) přihlásí uživatele. Ale aplikace zobrazí chybovou zprávu a neumožňuje uživateli dokončit tok přihlášení. Problém je, že aplikace nepřijala odpověď, kterou vydal ad Azure.

Existuje několik možných důvodů, proč aplikace nepřijala odpověď z Azure AD. Pokud chybová zpráva jasně neidentifikuje, co v odpovědi chybí, vyzkoušejte následující:

-   Pokud je aplikace galerie Azure AD, ověřte, že jste postupovali podle kroků v [části Jak ladit jednotné přihlašování saml na základě aplikace ve službě Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Pomocí nástroje, jako [je Šumař,](https://www.telerik.com/fiddler) zachyťte požadavek SAML, odpověď a token.

-   Odešlete odpověď SAML dodavateli aplikace a zeptejte se ho, co chybí.

## <a name="attributes-are-missing-from-the-saml-response"></a>V odpovědi SAML chybí atributy.

Pokud chcete přidat atribut v konfiguraci Azure AD, který se bude odesílat v odpovědi Azure AD, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. V horní části navigačního podokna na levé straně vyberte **všechny služby** pro otevření rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **službu Azure Active Directory** a pak vyberte **Službu Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **Podnikové aplikace.**

5. Výběrem **možnosti Všechny aplikace** zobrazíte seznam svých aplikací.

   > [!NOTE]
   > Pokud požadovanou aplikaci nevidíte, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace**. Nastavte možnost **Zobrazit** na "Všechny aplikace".

6. Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

7. Po načtení aplikace vyberte v navigačním podokně **jednotné přihlašování.**

8. V části **Atributy uživatele** vyberte **Zobrazit a upravit všechny ostatní atributy uživatele**. Zde můžete změnit atributy, které chcete odeslat do aplikace v tokenu SAML, když se uživatelé přihlásí.

   Přidání atributu:

   1. Vyberte **Přidat atribut**. Zadejte **název**a v rozevíracím seznamu vyberte **Hodnotu.**

   1.  Vyberte **Uložit**. Nový atribut se zobrazí v tabulce.

9. Konfiguraci uložte.

   Při příštím přihlášení uživatele k aplikaci, Azure AD odešle nový atribut v odpovědi SAML.

## <a name="the-app-doesnt-identify-the-user"></a>Aplikace neidentifikuje uživatele

Přihlášení k aplikaci se nezdaří, protože odpověď SAML chybí atribut, jako je například role. Nebo se nezdaří, protože aplikace očekává jiný formát nebo hodnotu pro **nameid** (identifikátor uživatele) atribut.

Pokud používáte [azure ad automatizované zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) k vytváření, údržbě a odebírat uživatele v aplikaci, ověřte, že uživatel byl zřízen do aplikace SaaS. Další informace najdete v tématu [Žádní uživatelé jsou zřizovány do aplikace Galerie Azure AD](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Přidání atributu do konfigurace aplikace Azure AD

Chcete-li změnit hodnotu identifikátor uživatele, postupujte takto:

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. Vyberte **Všechny služby** v horní části navigačního podokna na levé straně otevřete rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **službu Azure Active Directory** a pak vyberte **Službu Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **Podnikové aplikace.**

5. Výběrem **možnosti Všechny aplikace** zobrazíte seznam svých aplikací.

   > [!NOTE]
   > Pokud požadovanou aplikaci nevidíte, použijte ovládací prvek **Filtr** v horní části **seznamu Všechny aplikace**. Nastavte možnost **Zobrazit** na "Všechny aplikace".

6. Vyberte aplikaci, kterou chcete nakonfigurovat pro službu SSO.

7. Po načtení aplikace vyberte v navigačním podokně **jednotné přihlašování.**

8. V části **Atributy uživatele**vyberte jedinečný identifikátor uživatele z rozevíracího seznamu **Identifikátor uživatele.**

## <a name="change-the-nameid-format"></a>Změna formátu NameID

Pokud aplikace očekává jiný formát atributu **NameID** (Identifikátor uživatele), přečtěte si informace o změně formátu NameID [v tématu Úprava nameid.](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization#editing-nameid)

Azure AD vybere formát pro **NameID** atribut (Identifikátor uživatele) na základě hodnoty, která je vybrána nebo formát, který je požadováno aplikací v SAML AuthRequest. Další informace naleznete v části NameIDPolicy [protokolu SAML jednotného přihlášení](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Aplikace očekává jinou metodu podpisu pro odpověď SAML

Chcete-li změnit, které části tokenu SAML jsou digitálně podepsané službou Azure AD, postupujte takto:

1. Otevřete [portál Azure](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. Vyberte **Všechny služby** v horní části navigačního podokna na levé straně otevřete rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **službu Azure Active Directory** a pak vyberte **Službu Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **Podnikové aplikace.**

5. Výběrem **možnosti Všechny aplikace** zobrazíte seznam svých aplikací.

   > [!NOTE]
   > Pokud požadovanou aplikaci nevidíte, použijte ovládací **prvek Filtr** v horní části seznamu **Všechny aplikace**. Nastavte možnost **Zobrazit** na "Všechny aplikace".

6. Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

7. Po načtení aplikace vyberte v navigačním podokně **jednotné přihlašování.**

8. V části **PODPISOVÝ Certifikát SAML**vyberte **Zobrazit upřesňující nastavení podepisování certifikátů**.

9. Vyberte **možnost podepisování,** kterou aplikace očekává z těchto možností:

   * **Podepsat odpověď SAML**
   * **Podepsat odpověď a kontrolní výraz SAML**
   * **Podepsat kontrolní výraz SAML**

   Při příštím přihlášení uživatele k aplikaci, Azure AD podepíše část odpovědi SAML, které jste vybrali.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Aplikace očekává, že algoritmus podepisování SHA-1

Ve výchozím nastavení Azure AD podepisuje token SAML pomocí algoritmu nejbezpečnější. Doporučujeme, abyste neměnili podpisový algoritmus na *SHA-1,* pokud aplikace nevyžaduje SHA-1.

Chcete-li změnit algoritmus podepisování, postupujte takto:

1. Otevřete [portál Azure](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. Vyberte **Všechny služby** v horní části navigačního podokna na levé straně otevřete rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **službu Azure Active Directory** a pak vyberte **Službu Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **Podnikové aplikace.**

5. Chcete-li zobrazit seznam aplikací, vyberte **možnost Všechny aplikace.**

   > [!NOTE]
   > Pokud požadovanou aplikaci nevidíte, použijte ovládací **prvek Filtr** v horní části seznamu **Všechny aplikace**. Nastavte možnost **Zobrazit** na "Všechny aplikace".

6. Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

7. Po načtení aplikace vyberte **Jednotné přihlašování** z navigačního podokna na levé straně aplikace.

8. V části **PODPISOVÝ Certifikát SAML**vyberte **Zobrazit upřesňující nastavení podepisování certifikátů**.

9. Jako **podpisový algoritmus**vyberte **možnost SHA-1** .

   Při příštím přihlášení uživatele k aplikaci, Azure AD podepíše token SAML pomocí algoritmu SHA-1.

## <a name="next-steps"></a>Další kroky
[Jak ladit saml založené jednotné přihlašování k aplikacím v Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
