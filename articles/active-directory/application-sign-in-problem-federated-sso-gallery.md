---
title: Potíže při přihlašování do Galerie aplikace nakonfigurovaná pro federované jednotné přihlašování | Microsoft Docs
description: Pokyny pro konkrétní chyby při přihlášení k aplikaci, kterou jste nakonfigurovali pro na základě SAML federované jednotné přihlašování s Azure AD
services: active-directory
documentationcenter: ''
author: barbkess
manager: mtillman
ms.assetid: ''
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/11/2017
ms.author: barbkess
ms.reviewer: asteen
ms.openlocfilehash: f8c17b8c14b63007c3b623e5ffb60c0a2567cb72
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/23/2018
ms.locfileid: "36333645"
---
# <a name="problems-signing-in-to-a-gallery-application-configured-for-federated-single-sign-on"></a>Potíže při přihlašování do Galerie aplikace nakonfigurovaná pro federované jednotné přihlašování

Chcete-li vyřešit problém, musíte ověřit konfiguraci aplikace ve službě Azure AD následujícím způsobem:

-   Jste provedli všechny kroky konfigurace pro aplikaci Galerie Azure AD.

-   Identifikátor a adresa URL odpovědi, které jsou nakonfigurované v AAD shodovat se očekávaných hodnot v aplikaci

-   Přiřadili jste uživatele k aplikaci

## <a name="application-not-found-in-directory"></a>Aplikace nebyla nalezena v adresáři

*Chyba AADSTS70001: Aplikace s identifikátorem 'https://contoso.com' nebyl nalezen v adresáři*.

**Možná příčina**

Vystavitel, který odešle atribut z aplikace do služby Azure AD v žádosti SAML neodpovídá identifikátoru hodnotu nakonfigurovanou v aplikaci Azure AD.

**řešení**

Ujistěte se, že vystavitel atribut v požadavku SAML ho je odpovídající identifikátor hodnotu nakonfigurovanou v Azure AD:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete konfigurovat jednotné přihlašování

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  Přejděte na **domény a adresy URL** části. Ověřte, zda je hodnota v textovém poli identifikátor odpovídající hodnota pro identifikátor hodnota zobrazená v chybě.

Jakmile jste aktualizovali hodnota identifikátoru ve službě Azure AD a odešle hodnotu ho je odpovídající aplikace v žádosti SAML, byste měli moct přihlásit k aplikaci.

## <a name="the-reply-address-does-not-match-the-reply-addresses-configured-for-the-application"></a>Adresa pro odeslání odpovědi neodpovídá adresy odpovědět, nakonfigurované pro aplikaci.

*Chyba AADSTS50011: Adresa pro odeslání odpovědi sehttps://contoso.com' neodpovídá adresy odpovědět, nakonfigurované pro aplikaci*

**Možná příčina**

Hodnota AssertionConsumerServiceURL v žádosti SAML neodpovídá hodnotu adresa URL odpovědi nebo vzor nakonfigurovat ve službě Azure AD. Hodnota AssertionConsumerServiceURL v žádosti SAML je adresa URL se zobrazí v chybě.

**řešení**

Ujistěte se, že hodnota AssertionConsumerServiceURL v žádosti SAML ho je adresa URL odpovědi odpovídající hodnotu nakonfigurovanou v Azure AD.

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete konfigurovat jednotné přihlašování

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  Přejděte na **domény a adresy URL** části. Ověřte nebo aktualizujte hodnotu v textovém poli Adresa URL odpovědi odpovídat hodnotě AssertionConsumerServiceURL v žádosti SAML.  
    * Pokud nevidíte textového pole Adresa URL odpovědi, vyberte **zobrazit upřesňující nastavení adresy URL** zaškrtávací políčko.

Poté, co jste aktualizovali hodnota adresa URL odpovědi ve službě Azure AD a odešle hodnotu ho je odpovídající aplikace v žádosti SAML, byste měli moct přihlásit k aplikaci.

## <a name="user-not-assigned-a-role"></a>Není přiřazen k roli uživatele

*Chyba AADSTS50105: Přihlášeného uživatele,brian@contoso.com' není přiřazen do rolí pro aplikace*.

**Možná příčina**

Uživateli nebyl udělen přístup k aplikaci ve službě Azure AD.

**řešení**

Jeden nebo více uživatelů přiřadit přímo k aplikaci, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete přiřadit uživatele k ze seznamu.

7.  Po načtení aplikace, klikněte na **uživatelů a skupin** z levé navigační nabídky.

8.  Klikněte na tlačítko **přidat** tlačítko na **uživatelů a skupin** seznamu otevřete **přidat přiřazení** podokně.

9.  Klikněte na tlačítko **uživatelů a skupin** pro výběr **přidat přiřazení** podokně.

10. Zadejte **celý název** nebo **e-mailová adresa** uživatele vás zajímá přiřazení do **hledat podle jména nebo e-mailové adresy** vyhledávacího pole.

11. Najeďte myší **uživatele** v seznamu na nich **políčko**. Klikněte na zaškrtávací políčko vedle profilové fotky nebo logo pro přidání uživatelů do uživatele **vybrané** seznamu.

12. **Volitelné:** Pokud byste chtěli **přidat více než jeden uživatel**, zadejte v jiném **celý název** nebo **e-mailová adresa** do **vyhledávání podle názvu nebo e-mailová adresa** pole pro vyhledávání a klikněte na zaškrtávací políčko, chcete-li přidat tento uživatel **vybrané** seznamu.

13. Po dokončení výběru uživatelů klikněte na **vyberte** tlačítko, které chcete přidat do seznamu uživatelů a skupin, které chcete přiřadit k aplikaci.

14. **Volitelné:** klikněte na tlačítko **vybrat roli** selektor v **přidat přiřazení** podokně vyberte roli přiřadit uživatele, který jste vybrali.

15. Klikněte **přiřadit** tlačítko přiřadit aplikace pro vybraného uživatele.

Po krátké době uživatele, které jste vybrali moci spustit tyto aplikace pomocí metody popsané v části popis řešení.

## <a name="not-a-valid-saml-request"></a>Není platný SAML požadavku na

*Chyba AADSTS75005: Žádosti není platný zprávu protokolu typu Saml2.*

**Možná příčina**

Azure AD nepodporuje požadavek SAML, který aplikace odeslala pro jednotné přihlášení. Jsou některé běžné problémy:

-   Chybí povinná pole v požadavku SAML

-   Metoda požadavku kódovaný SAML

**řešení**

1.  Zaznamenejte žádost SAML. postupujte podle kurzu [ladění na základě SAML jednotného přihlašování k aplikacím v Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging) se dozvíte, jak chcete zaznamenat žádost SAML.

2.  Obraťte se na dodavatele aplikace a sdílené složky:

   -   Žádost SAML

   -   [Protokol požadavky pro Azure AD jedním přihlašování SAML](https://docs.microsoft.com/azure/active-directory/develop/active-directory-single-sign-on-protocol-reference)

Se musí ověřit podporují implementace Azure AD SAML pro jednotné přihlašování.

## <a name="no-resource-in-requiredresourceaccess-list"></a>Žádný prostředek v seznamu requiredResourceAccess

*Chyba AADSTS65005: klientská aplikace vyžaduje přístup k prostředku, 00000002-0000-0000-c000-000000000000'. Tento požadavek se nezdařil, protože klient nebyl zadán tento prostředek ve svém seznamu requiredResourceAccess*.

**Možná příčina**

Objekt aplikace je poškozen.

**Řešení: možnost 1**

Problém vyřešit, přidejte hodnotu jedinečný identifikátor v konfiguraci služby Azure AD. Pokud chcete přidat hodnotu identifikátoru, postupujte následujícím způsobem:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, které jste nakonfigurovali jednotné přihlašování.

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky

8.  V části **domény a adresy URL** část, zkontrolujte na **zobrazit upřesňující nastavení adresy URL**.

9.  v **identifikátor** textového pole zadejte jedinečný identifikátor pro aplikaci.

10. **Uložit** konfigurace.


**Možnost řešení 2**

Pokud možnost 1 výše pro vás nefunguje, zkuste odebrat aplikaci z adresáře. Potom přidat a změnit konfiguraci aplikace, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

  * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete konfigurovat jednotné přihlašování

7.  Klikněte na tlačítko **odstranit** v levé horní aplikace **přehled** podokně.

8.  Aktualizujte Azure AD a přidat aplikaci z Galerie Azure AD. Potom nakonfigurujte aplikaci

<span id="_Hlk477190176" class="anchor"></span>Po překonfigurování aplikaci, byste měli být moct přihlásit k aplikaci.

## <a name="certificate-or-key-not-configured"></a>Certifikát nebo klíče není nakonfigurováno

*Chyba AADSTS50003: Žádné podpisový klíč nakonfigurován.*

**Možná příčina**

Objekt aplikace je poškozený a Azure AD nerozpoznal certifikát nakonfigurovaný pro aplikaci.

**řešení**

Pokud chcete odstranit a vytvořit nový certifikát, postupujte podle následujících kroků:

1.  Otevřete [ **portál Azure** ](https://portal.azure.com/) a přihlaste se jako **globálního správce** nebo **ko-správce.**

2.  Otevřete **rozšíření Azure Active Directory** kliknutím **všechny služby** v horní části hlavní levé navigační nabídce.

3.  Zadejte **"Azure Active Directory**" v filtru vyhledávacího pole a vyberte **Azure Active Directory** položky.

4.  Klikněte na tlačítko **podnikové aplikace, které** z levé navigační nabídce Azure Active Directory.

5.  Klikněte na tlačítko **všechny aplikace** Chcete-li zobrazit seznam všech aplikací.

 * Pokud aplikaci chcete, aby se zobrazí tady nevidíte, pomocí **filtru** ovládací prvek v horní části **seznam všech aplikací** a nastavte **zobrazit** možnost k **všechny Aplikace.**

6.  Vyberte aplikaci, kterou chcete konfigurovat jednotné přihlašování

7.  Po načtení aplikace, klikněte na **jednotného přihlašování** z levé navigační nabídky.

8.  Klikněte na tlačítko **vytvořit nový certifikát** pod **SAML podpisový certifikát** části.

9.  Vyberte datum vypršení platnosti. Potom klikněte na **uložit.**

10. Zkontrolujte **aktivujte nový certifikát** přepsat aktivní certifikát. Potom klikněte na **Uložit** v horní části podokna a přijměte aktivovat certifikát výměny.

11. V části **SAML podpisový certifikát** klikněte na tlačítko **odebrat** odebrat **nepoužitý** certifikátu.

## <a name="problem-when-customizing-the-saml-claims-sent-to-an-application"></a>Problém při přizpůsobení SAML deklarace identity odeslat do aplikace

Naučte se přizpůsobovat deklarací identity atributu SAML odeslaných do vaší aplikace, najdete v tématu [deklarací mapování v Azure Active Directory](https://docs.microsoft.com/azure/active-directory/active-directory-claims-mapping) Další informace.

## <a name="next-steps"></a>Další postup
[Postup ladění na základě SAML jednotného přihlašování k aplikacím v Azure AD](https://docs.microsoft.com/azure/active-directory/develop/active-directory-saml-debugging)
