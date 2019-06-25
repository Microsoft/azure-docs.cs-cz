---
title: Po přihlášení, zobrazí se chybová zpráva na stránce aplikace | Dokumentace Microsoftu
description: Jak řešit problémy pomocí přihlášení Azure AD při aplikaci vrátí chybovou zprávu.
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
ms.openlocfilehash: d41ec1f510b028a2ffe2554bfcbd77bc439c4e79
ms.sourcegitcommit: a52d48238d00161be5d1ed5d04132db4de43e076
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2019
ms.locfileid: "67272950"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Na stránce aplikace zobrazí chybová zpráva po přihlášení uživatele

V tomto scénáři Azure Active Directory (Azure AD) přihlásí uživatel. Ale aplikace zobrazí chybovou zprávu a nebude nechat uživatele dokončit tok přihlášení. Problém je, že aplikace nepřijala odpověď, která vydala Azure AD.

Existuje několik možných důvodů, proč aplikaci nepřijala odpověď ze služby Azure AD. Pokud chybová zpráva není identifikují co je v odpovědi nebyl nalezen, zkuste následující:

-   Pokud aplikace je Galerie Azure AD, ověřte, že jste postupovali podle kroků v [ladění založené na SAML jednotného přihlašování k aplikacím ve službě Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).

-   Pomocí některého nástroje, například [Fiddler](https://www.telerik.com/fiddler) k zaznamenání požadavku a odpovědi a tokenu SAML.

-   Poslat odpověď SAML na dodavatele aplikace a požádejte ho, co chybí.

## <a name="attributes-are-missing-from-the-saml-response"></a>Atributy chybí odpověď SAML

Chcete-li přidat atribut v konfiguraci Azure AD, odeslaný v odpovědi služby Azure AD, postupujte takto:

1. Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. V horní části navigačního podokna na levé straně vyberte **všechny služby** otevřít rozšíření Azure AD.

3. Typ **Azure Active Directory** do vyhledávacího pole filtrovat a pak vyberte **Azure Active Directory**.

4. Vyberte **podnikové aplikace** v navigačním podokně Azure AD.

5. Vyberte **všechny aplikace** zobrazení seznamu aplikací.

   > [!NOTE]
   > Pokud nevidíte aplikaci, kterou chcete, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací**. Nastavte **zobrazit** možnost "Všech aplikací."

6. Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

7. Po dokončení načítání aplikace, vyberte **jednotného přihlašování** v navigačním podokně.

8. V **atributy uživatele** vyberte **zobrazit a upravit všechny ostatní atributy uživatele**. Tady můžete změnit atributy, které budou odesílat do aplikace v tokenu SAML, když se uživatelé přihlásí.

   Chcete-li přidat atribut:

   1. Vyberte **přidat atribut**. Zadejte **název**a vyberte **hodnota** z rozevíracího seznamu.

   1.  Vyberte **Uložit**. Zobrazí se vám nový atribut v tabulce.

9. Konfiguraci uložte.

   Při příštím přihlášení uživatele k aplikaci Azure AD pošle odpověď SAML nového atributu.

## <a name="the-app-doesnt-identify-the-user"></a>Aplikace nemá identifikaci uživatele

Přihlášení do aplikace selže, protože odpověď SAML chybí atribut jako je například roli. Nebo se nezdaří, protože jiný formát nebo hodnotu očekává, že aplikace **NameID** atribut (identifikátor uživatele).

Pokud používáte [Azure AD automatické zřizování uživatelů](https://docs.microsoft.com/azure/active-directory/manage-apps/user-provisioning) vytvořit, spravovat a odebírat uživatele v aplikaci, ověřte, že uživatel zřízený aplikace SaaS. Další informace najdete v tématu [žádní uživatelé se nezřizují k aplikaci Galerie Azure AD](https://docs.microsoft.com/azure/active-directory/manage-apps/application-provisioning-config-problem-no-users-provisioned).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Přidání atributu do konfigurace aplikace Azure AD

Chcete-li změnit hodnotu identifikátoru uživatele, postupujte podle těchto kroků:

1. Otevřít [ **webu Azure portal** ](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. Vyberte **všechny služby** v horní části navigačního podokna na levé straně otevřete rozšíření Azure AD.

3. Typ **Azure Active Directory** do vyhledávacího pole filtrovat a pak vyberte **Azure Active Directory**.

4. Vyberte **podnikové aplikace** v navigačním podokně Azure AD.

5. Vyberte **všechny aplikace** zobrazení seznamu aplikací.

   > [!NOTE]
   > Pokud nevidíte aplikaci, kterou chcete, použijte **filtr** ovládacího prvku v horní části **seznam všech aplikací**. Nastavte **zobrazit** možnost "Všech aplikací."

6. Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

7. Po dokončení načítání aplikace, vyberte **jednotného přihlašování** v navigačním podokně.

8. V části **atributy uživatele**, vyberte jedinečný identifikátor pro uživatele **identifikátor uživatele** rozevíracího seznamu.

## <a name="change-the-nameid-format"></a>Změna formátu NameID

Pokud aplikace očekává, že jiný formát pro **NameID** atribut (identifikátor uživatele), najdete v článku [úpravy nameID](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-claims-customization.md#editing-nameid) změnit formát ID názvu položky.

Azure AD vybere formát **NameID** atribut (identifikátor uživatele) na základě hodnoty, která je vybrána nebo formátu, který je požadován aplikací v SAML AuthRequest. Další informace najdete v tématu v části "NameIDPolicy" [jednotné přihlašování – protokol SAML](https://docs.microsoft.com/azure/active-directory/develop/single-sign-on-saml-protocol#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Aplikace očekává, že jiný podpis metody pro odpověď SAML

Chcete-li změnit, které části tokenu SAML jsou digitálně podepsané pomocí Azure AD, postupujte podle těchto kroků:

1. Otevřít [webu Azure portal](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. Vyberte **všechny služby** v horní části navigačního podokna na levé straně otevřete rozšíření Azure AD.

3. Typ **Azure Active Directory** do vyhledávacího pole filtrovat a pak vyberte **Azure Active Directory**.

4. Vyberte **podnikové aplikace** v navigačním podokně Azure AD.

5. Vyberte **všechny aplikace** zobrazení seznamu aplikací.

   > [!NOTE]
   > Pokud nevidíte aplikaci, která má, použít **filtr** ovládacího prvku v horní části **seznam všech aplikací**. Nastavte **zobrazit** možnost "Všech aplikací."

6. Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

7. Po načtení aplikace, vyberte **jednotného přihlašování** v navigačním podokně.

8. V části **podpisový certifikát SAML**vyberte **zobrazit pokročilé nastavení podepisování certifikátu**.

9. Vyberte **podepisování možnost** , který aplikace očekává, že z těchto možností:

   * **Podepsat odpověď SAML**
   * **Podepsat odpověď SAML a kontrolní výraz**
   * **Podepsat kontrolní výraz SAML**

   Při příštím přihlášení uživatele k aplikaci Azure AD podepíše součástí odpověď SAML, který jste vybrali.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Aplikace bude podpisový algoritmus SHA-1

Ve výchozím nastavení Azure AD podepisuje tokenu SAML s použitím algoritmu nejvyšší zabezpečení. Doporučujeme, abyste podpisový algoritmus neměnit *SHA-1* Pokud aplikace nevyžaduje SHA-1.

Chcete-li změnit podpisový algoritmus, postupujte takto:

1. Otevřít [webu Azure portal](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. Vyberte **všechny služby** v horní části navigačního podokna na levé straně otevřete rozšíření Azure AD.

3. Typ **Azure Active Directory** do vyhledávacího pole filtrovat a pak vyberte **Azure Active Directory**.

4. Vyberte **podnikové aplikace** v navigačním podokně Azure AD.

5. Vyberte **všechny aplikace** zobrazení seznamu vašich aplikací.

   > [!NOTE]
   > Pokud nevidíte aplikaci, která má, použít **filtr** ovládacího prvku v horní části **seznam všech aplikací**. Nastavte **zobrazit** možnost "Všech aplikací."

6. Vyberte aplikaci, kterou chcete konfigurovat pro jednotné přihlašování.

7. Po dokončení načítání aplikace, vyberte **jednotného přihlašování** z navigačního podokna na levé straně aplikace.

8. V části **podpisový certifikát SAML**vyberte **zobrazit pokročilé nastavení podepisování certifikátu**.

9. Vyberte **SHA-1** jako **podpisový algoritmus**.

   Při příštím přihlášení uživatele k aplikaci Azure AD podepíše tokenu SAML s využitím algoritmu SHA-1.

## <a name="next-steps"></a>Další postup
[Ladění založené na SAML jednotného přihlašování k aplikacím ve službě Azure AD](https://azure.microsoft.com/documentation/articles/active-directory-saml-debugging).
