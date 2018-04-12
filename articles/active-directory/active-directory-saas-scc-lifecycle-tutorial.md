---
title: 'Kurz: Azure Active Directory integrace s životního cyklu SCC | Microsoft Docs'
description: Další informace o použití životního cyklu SCC službou Azure Active Directory umožňující jednotné přihlašování, automatického zřizování a další!
services: active-directory
author: jeevansd
documentationcenter: na
manager: mtillman
ms.assetid: 9748bf38-ffc3-4d51-a1ae-207ce57104fa
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/03/2018
ms.author: jeedes
ms.openlocfilehash: 943698311d10b479879fd92d2482d827650e2b0c
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/11/2018
---
# <a name="tutorial-azure-active-directory-integration-with-scc-lifecycle"></a>Kurz: Azure Active Directory integrace s SCC životního cyklu
Cílem tohoto kurzu je zobrazit integraci Azure a SCC životního cyklu.  

Scénář uvedených v tomto kurzu se předpokládá, že už máte následující položky:

* Platné předplatné Azure
* Životního cyklu SCC jednotné přihlašování (SSO) povolené předplatné

Po dokončení tohoto kurzu, bude moct jednotné přihlašování do aplikace ve vaší lokalitě společnosti životního cyklu SCC (služba Zprostředkovatel iniciované přihlašování) nebo pomocí uživatele Azure AD, které jste přiřadili SCC životního cyklu [Úvod do přístup Panel](active-directory-saas-access-panel-introduction.md).

Scénář uvedených v tomto kurzu se skládá z následujících stavební bloky:

1. Povolení integrace aplikace pro životní cyklus SCC
2. Konfigurace jednotného přihlašování (SSO)
3. Konfiguraci zřizování uživatelů
4. Přiřazení uživatelů

![Scénář](./media/active-directory-saas-scc-lifecycle-tutorial/IC794120.png "scénář")

## <a name="enable-the-application-integration-for-scc-lifecycle"></a>Povolit integraci aplikace pro životní cyklus SCC
Cílem této části se popisují postup povolení integrace aplikace pro životní cyklus SCC.

**Pokud chcete povolit integraci aplikace pro životní cyklus SCC, proveďte následující kroky:**

1. V portálu Azure classic, v levém navigačním podokně klikněte na **služby Active Directory**.
   
    ![Active Directory](./media/active-directory-saas-scc-lifecycle-tutorial/IC700993.png "Active Directory")
2. Z **Directory** seznamu, vyberte adresář, pro který chcete povolit integraci adresáře.
3. Chcete-li otevřít zobrazení aplikací, v zobrazení adresáře, klikněte na tlačítko **aplikace** v horní nabídce.
   
    ![Aplikace](./media/active-directory-saas-scc-lifecycle-tutorial/IC700994.png "aplikace")
4. Klikněte na tlačítko **přidat** v dolní části stránky.
   
    ![Přidat aplikaci](./media/active-directory-saas-scc-lifecycle-tutorial/IC749321.png "přidat aplikaci")
5. Na **co chcete udělat** dialogové okno, klikněte na tlačítko **přidat aplikaci z Galerie**.
   
    ![Přidání aplikace z gallerry](./media/active-directory-saas-scc-lifecycle-tutorial/IC749322.png "přidat aplikaci z gallerry")
6. V **vyhledávacího pole**, typ **životního cyklu SCC**.
   
    ![Galerie aplikací](./media/active-directory-saas-scc-lifecycle-tutorial/IC794121.png "galerii aplikací")
7. V podokně výsledků vyberte **životního cyklu SCC**a potom klikněte na **Complete** tuto aplikaci přidat.
   
    ![Životní cyklus SCC](./media/active-directory-saas-scc-lifecycle-tutorial/IC795082.png "SCC životního cyklu")
   
## <a name="configure-single-sign-on"></a>Konfigurovat jednotné přihlašování

Cílem této části se popisují, jak uživatelům povolit ověřování na životní cyklus SCC ke svému účtu ve službě Azure AD využívající federaci na základě protokolu SAML.

**Pokud chcete konfigurovat jednotné přihlašování, proveďte následující kroky:**

1. Na portálu Azure classic na **životního cyklu SCC** stránky integrace aplikací, klikněte na tlačítko **nakonfigurovat jednotné přihlašování** otevřete ** nakonfigurovat jednotné přihlašování ** dialogové okno.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-scc-lifecycle-tutorial/IC794122.png "nakonfigurovat jednotné přihlašování")
2. Na **jak chcete uživatelům se přihlásit životní cyklus SCC** vyberte **Microsoft Azure AD Single Sign-On**a potom klikněte na **Další**.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-scc-lifecycle-tutorial/IC794123.png "nakonfigurovat jednotné přihlašování")
3. Na **konfigurace adresy URL aplikace** stránky v **přihlašovací adresa URL** textové pole, zadejte adresu URL používají vaši uživatelé k přihlášení do aplikace životního cyklu SCC pomocí následujícího vzorce "*https://bs1.scc.com/lc7/welcome/customer/PICTtest.aspx*"a potom klikněte na **Další**.
   
    ![Konfigurovat adresu URL aplikace](./media/active-directory-saas-scc-lifecycle-tutorial/IC794124.png "konfigurovat adresu URL aplikace")
4. Na **nakonfigurovat jednotné přihlašování v cyklu SCC** klikněte na tlačítko **stáhnout metadata**a potom uložte soubor metadat místně na vašem počítači.
   
   ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-scc-lifecycle-tutorial/IC795083.png "nakonfigurovat jednotné přihlašování")
5. Předat dál tento soubor metadat týmu SCC životní cyklus podpory.
   
   >[!NOTE]
   >Jednotné přihlašování musí být povoleno tým podpory SCC životního cyklu.
   > 
   > 

6. Na portálu Azure classic, vyberte potvrzení konfigurace přihlášení a pak klikněte na tlačítko **Complete** zavřete **nakonfigurovat jednotné přihlašování** dialogové okno.
   
    ![Konfigurovat jednotné přihlašování](./media/active-directory-saas-scc-lifecycle-tutorial/IC794125.png "nakonfigurovat jednotné přihlašování")
   
## <a name="configure-user-provisioning"></a>Konfiguraci zřizování uživatelů

Pokud chcete povolit uživatelům Azure AD přihlášení do SCC životního cyklu, musí být zřízená do SCC životního cyklu. Neexistuje žádná položka akce pro konfiguraci zřizování uživatelů k SCC životního cyklu.

Když přiřazený uživatel se pokusí přihlásit SCC životního cyklu, se automaticky vytvoří účet SCC životního cyklu, v případě potřeby.

>[!NOTE]
>Můžete použít jakékoli jiné SCC životní cyklus uživatelského účtu vytvoření nástroje nebo rozhraní API poskytované životního cyklu SCC zřídit AAD uživatelské účty.
> 
> 

## <a name="assign-users"></a>Přiřazení uživatelů
Chcete-li otestovat vaši konfiguraci, přidělte uživatelům Azure AD, že které chcete povolit přístup aplikace k němu pomocí jejich přiřazení.

**Přiřazení uživatelů k SCC životního cyklu, proveďte následující kroky:**

1. Na portálu Azure classic vytvořte zkušební účet.
2. Na ** životního cyklu SCC ** stránky integrace aplikací, klikněte na tlačítko **přiřazení uživatelů**.
   
    ![Přiřazení uživatelů](./media/active-directory-saas-scc-lifecycle-tutorial/IC794126.png "přiřazení uživatelů")
3. Vyberte svého testovacího uživatele, klikněte na **přiřadit**a potom klikněte na **Ano** k potvrzení vaší přiřazení.
   
    ![Ano](./media/active-directory-saas-scc-lifecycle-tutorial/IC767830.png "Ano")

Pokud chcete otestovat nastavení jednotného přihlašování, otevřete Panel přístupu. Další podrobnosti o na přístupovém panelu najdete v tématu [Úvod k přístupovému panelu](active-directory-saas-access-panel-introduction.md).

