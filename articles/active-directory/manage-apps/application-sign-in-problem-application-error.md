---
title: Po přihlášení se zobrazí chybová zpráva na stránce aplikace | Microsoft Docs
description: Jak vyřešit problémy s přihlášením pomocí Azure AD, když aplikace vrátí chybovou zprávu
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: troubleshooting
ms.date: 07/11/2017
ms.author: kenwith
ms.reviewer: asteen
ms.collection: M365-identity-device-management
ms.openlocfilehash: c96209f33491645510d8592997c418472d4f227c
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99258808"
---
# <a name="an-app-page-shows-an-error-message-after-the-user-signs-in"></a>Na stránce aplikace se po přihlášení uživatele zobrazí chybová zpráva

V tomto scénáři Azure Active Directory (Azure AD) přihlásí uživatele v. Aplikace ale zobrazí chybovou zprávu a neumožní uživateli dokončit přihlašovací tok. Problémem je, že aplikace nepřijala odpověď, kterou vystavila služba Azure AD.

Existuje několik možných důvodů, proč aplikace nepřijala odpověď z Azure AD. Pokud chybová zpráva jasně neidentifikuje, co v odpovědi chybí, vyzkoušejte následující:

-   Pokud je aplikace Galerie Azure AD, ověřte, že jste postupovali podle kroků v tématu [Postup ladění jednotného přihlašování založeného na SAML k aplikacím v Azure AD](./debug-saml-sso-issues.md).

-   K zachycení požadavku, odpovědi a tokenu SAML použijte nástroj, jako je [Fiddler](https://www.telerik.com/fiddler) .

-   Odešlete odpověď SAML dodavateli aplikace a požádejte je o to, co chybí.

## <a name="attributes-are-missing-from-the-saml-response"></a>V odpovědi SAML chybí atributy.

Chcete-li přidat atribut v konfiguraci služby Azure AD, který bude odeslán v odpovědi Azure AD, postupujte podle následujících kroků:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. V horní části navigačního podokna na levé straně vyberte **všechny služby** a otevřete rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **Azure Active Directory** a pak vyberte **Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **podnikové aplikace** .

5. Výběrem **všech aplikací** zobrazíte seznam aplikací.

   > [!NOTE]
   > Pokud nevidíte aplikaci, kterou chcete, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace**. Nastavte možnost **Zobrazit** na všechny aplikace.

6. Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

7. Po načtení aplikace vyberte v navigačním podokně **jednotné přihlašování** .

8. V části **atributy uživatele** vyberte možnost **Zobrazit a upravit všechny ostatní atributy uživatele**. Tady můžete změnit atributy, které se mají posílat do aplikace v tokenu SAML, když se uživatelé přihlásí.

   Přidání atributu:

   1. Vyberte **Přidat atribut**. Zadejte **název** a v rozevíracím seznamu vyberte **hodnotu** .

   1.  Vyberte **Uložit**. V tabulce se zobrazí nový atribut.

9. Konfiguraci uložte.

   Až se uživatel příště přihlásí k aplikaci, Azure AD pošle nový atribut v odpovědi SAML.

## <a name="the-app-doesnt-identify-the-user"></a>Aplikace neidentifikuje uživatele.

Přihlášení k aplikaci se nepovede, protože v odpovědi SAML chybí atribut, jako je například role. Nebo se nezdařila, protože aplikace očekává jiný formát nebo hodnotu atributu **NameId** (uživatelský identifikátor).

Pokud používáte [automatizované zřizování uživatelů Azure AD](../app-provisioning/user-provisioning.md) k vytváření, údržbě a odebírání uživatelů v aplikaci, ověřte, jestli je uživatel zřízený do aplikace SaaS. Další informace najdete v článku o tom, že [se žádní uživatelé nezřídí do aplikace Galerie Azure AD](../app-provisioning/application-provisioning-config-problem-no-users-provisioned.md).

## <a name="add-an-attribute-to-the-azure-ad-app-configuration"></a>Přidání atributu do konfigurace aplikace Azure AD

Chcete-li změnit hodnotu identifikátoru uživatele, použijte následující postup:

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. V horní části navigačního podokna na levé straně vyberte **všechny služby** a otevřete rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **Azure Active Directory** a pak vyberte **Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **podnikové aplikace** .

5. Výběrem **všech aplikací** zobrazíte seznam aplikací.

   > [!NOTE]
   > Pokud nevidíte aplikaci, kterou chcete, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace**. Nastavte možnost **Zobrazit** na všechny aplikace.

6. Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

7. Po načtení aplikace vyberte v navigačním podokně **jednotné přihlašování** .

8. V části **atributy uživatele** vyberte jedinečný identifikátor uživatele z rozevíracího seznamu **identifikátor uživatele** .

## <a name="change-the-nameid-format"></a>Změna formátu NameID

Pokud aplikace očekává jiný formát atributu **NameId** (uživatelský identifikátor), přečtěte si článek [Úprava NameId](../develop/active-directory-saml-claims-customization.md#editing-nameid) a změňte formát NameId.

Azure AD vybere formát pro atribut **NameId** (identifikátor uživatele) na základě hodnoty, která je vybrána, nebo formátu, který požaduje aplikace v AuthRequest SAML. Další informace najdete v části "NameIDPolicy" tématu [protokol SAML jednotného přihlašování](../develop/single-sign-on-saml-protocol.md#nameidpolicy).

## <a name="the-app-expects-a-different-signature-method-for-the-saml-response"></a>Aplikace očekává pro odpověď SAML jinou metodu podpisu.

Chcete-li změnit, které části tokenu SAML jsou digitálně podepsány službou Azure AD, postupujte podle následujících kroků:

1. Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. V horní části navigačního podokna na levé straně vyberte **všechny služby** a otevřete rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **Azure Active Directory** a pak vyberte **Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **podnikové aplikace** .

5. Výběrem **všech aplikací** zobrazíte seznam aplikací.

   > [!NOTE]
   > Pokud nevidíte aplikaci, kterou chcete použít, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace**. Nastavte možnost **Zobrazit** na všechny aplikace.

6. Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

7. Po načtení aplikace vyberte v navigačním podokně **jednotné přihlašování** .

8. V části **podpisový certifikát SAML** vyberte  **Zobrazit upřesňující nastavení podepisování certifikátů**.

9. Vyberte **možnost podepisování** , kterou aplikace očekává mezi tyto možnosti:

   * **Podepsat odpověď SAML**
   * **Podepsat odpověď a kontrolní výraz SAML**
   * **Podepsat kontrolní výraz SAML**

   Až se uživatel příště přihlásí k aplikaci, služba Azure AD podepíše část odpovědi SAML, kterou jste vybrali.

## <a name="the-app-expects-the-sha-1-signing-algorithm"></a>Aplikace očekává podpisový algoritmus SHA-1.

Služba Azure AD ve výchozím nastavení podepisuje tokeny SAML pomocí nejbezpečnějšího algoritmu. Pokud aplikace nevyžaduje SHA-1, Doporučujeme neměnit podpisový algoritmus na *SHA-1* .

Chcete-li změnit podpisový algoritmus, použijte následující postup:

1. Otevřete [Azure Portal](https://portal.azure.com/) a přihlaste se jako globální správce nebo spolusprávce.

2. V horní části navigačního podokna na levé straně vyberte **všechny služby** a otevřete rozšíření Azure AD.

3. Do vyhledávacího pole filtru zadejte **Azure Active Directory** a pak vyberte **Azure Active Directory**.

4. V navigačním podokně Azure AD vyberte **podnikové aplikace** .

5. Výběrem **všech aplikací** zobrazíte seznam aplikací.

   > [!NOTE]
   > Pokud nevidíte aplikaci, kterou chcete použít, použijte ovládací prvek **filtru** v horní části **seznamu všechny aplikace**. Nastavte možnost **Zobrazit** na všechny aplikace.

6. Vyberte aplikaci, kterou chcete nakonfigurovat pro jednotné přihlašování.

7. Po načtení aplikace vyberte v navigačním podokně na levé straně aplikace **jednotné přihlašování** .

8. V části **podpisový certifikát SAML** vyberte **Zobrazit upřesňující nastavení podepisování certifikátů**.

9. Jako **podpisový algoritmus** vyberte **SHA-1** .

   Až se uživatel příště přihlásí k aplikaci, služba Azure AD podepíše token SAML pomocí algoritmu SHA-1.

## <a name="next-steps"></a>Další kroky
[Jak ladit jednotné přihlašování založené na SAML pro aplikace ve službě Azure AD](./debug-saml-sso-issues.md).