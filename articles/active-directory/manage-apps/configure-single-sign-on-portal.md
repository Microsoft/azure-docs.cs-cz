---
title: Konfigurace jednotného přihlašování – Azure Active Directory | Microsoft Docs
description: Tento kurz využívá Azure Portal ke konfiguraci jednotného přihlašování založeného na SAML pro aplikaci s využitím Azure Active Directory (Azure AD).
services: active-directory
author: CelesteDG
manager: mtillman
ms.service: active-directory
ms.subservice: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 04/08/2019
ms.author: celested
ms.reviewer: arvinh,luleon
ms.collection: M365-identity-device-management
ms.openlocfilehash: a3d96799e69e2fdef3a4ffd1a436727e6a58da79
ms.sourcegitcommit: b8a8d29fdf199158d96736fbbb0c3773502a092d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/15/2019
ms.locfileid: "59565984"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>Kurz: Konfigurace založené na SAML jednotného přihlašování pro aplikaci pomocí Azure Active Directory

Tento kurz využívá [Azure Portal](https://portal.azure.com) ke konfiguraci jednotného přihlašování založeného na SAML pro aplikaci s využitím Azure Active Directory (Azure AD). Pomocí tohoto kurzu, když [kurzu specifické pro aplikaci](../saas-apps/tutorial-list.md) není k dispozici. 

Tento kurz využívá Azure Portal k provedení následujících kroků:

> [!div class="checklist"]
> * Výběr režimu jednotného přihlašování založeného na SAML
> * Konfigurace domény a adres URL specifických pro aplikaci
> * Konfigurace atributů uživatele
> * Vytvoření podpisového certifikátu SAML
> * Přiřazení uživatelů k aplikaci
> * Konfigurace jednotného přihlašování založeného na SAML pro aplikaci
> * Test nastavení SAML

## <a name="before-you-begin"></a>Než začnete

1. Pokud aplikace nebyl přidán do vašeho tenanta Azure AD, přečtěte si téma [rychlý start: Přidání aplikace do svého tenanta Azure AD](add-application-portal.md).

2. Požádejte dodavatele aplikace informace popsaného v [nakonfigurujte základní možnosti SAML](#configure-basic-saml-options).

3. K otestování kroky v tomto kurzu používejte mimo produkční prostředí. Pokud nemáte neprodukční prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

4. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce cloudové aplikace nebo aplikace správce pro vašeho tenanta Azure AD.

## <a name="select-a-single-sign-on-mode"></a>Výběr režimu jednotného přihlašování

Po přidání aplikace do svého tenanta Azure AD, budete připraveni ke konfiguraci jednotného přihlašování pro aplikaci.

Otevření nastavení jednotného přihlašování:

1. V [webu Azure portal](https://portal.azure.com), v levém navigačním panelu vyberte **Azure Active Directory**. 

2. V části **spravovat** v **Azure Active Directory** navigační panel, který se zobrazí, vyberte **podnikové aplikace**. Zobrazí se náhodného vzorku aplikací ve vašem tenantovi Azure AD. 

3. V **typ aplikace** nabídce vyberte možnost **všechny aplikace**a pak vyberte **použít**.

4. Zadejte název aplikace, pro kterou chcete nakonfigurovat jednotné přihlašování. Například můžete zadat **GitHub-test** ke konfiguraci aplikace, které jste přidali v kroku [přidat aplikaci](add-application-portal.md) rychlý start.  

     ![Snímek obrazovky zobrazující panelu hledání aplikací.](media/configure-single-sign-on-portal/azure-portal-application-search.png)

5. Zvolte aplikaci, pro kterou chcete nakonfigurovat jednotné přihlašování.

6. V části **spravovat** vyberte **jednotného přihlašování**. 

7. Vyberte **SAML** Konfigurace jednotného přihlašování. **Nastavte si jednotné přihlašování pomocí SAML - Preview** se zobrazí stránka.

## <a name="configure-basic-saml-options"></a>Nakonfigurujte základní možnosti SAML

Konfigurace domény a adres URL:

1. Obraťte se na dodavatele aplikace, aby vám poskytl správné informace pro následující nastavení:

    | Nastavení konfigurace | Iniciováno zprostředkovatelem přihlašování | Iniciováno pomocí IdP | Popis |
    |:--|:--|:--|:--|
    | Identifikátor (ID entity) | Vyžaduje se pro některé aplikace | Vyžaduje se pro některé aplikace | Jednoznačně identifikuje aplikaci, pro kterou se konfiguruje jednotné přihlašování. Azure AD odešle identifikátor aplikace jako parametr cílové skupiny tokenu SAML. Abyste ověřili, že se očekává aplikace. Tato hodnota se také zobrazuje jako ID entity ve všech metadatech SAML poskytovaných aplikací.|
    | Adresa URL odpovědi | Nepovinné | Požaduje se | Určuje, kde aplikace očekává přijetí tokenu SAML. Adresa URL odpovědi se také označuje jako adresa URL ACS (Assertion Consumer Service). |
    | Přihlašovací adresa URL | Požaduje se | Nezadávejte | Když uživatel otevře tuto adresu URL, poskytovatel služeb ho přesměruje do Azure AD, kde se uživatel ověří a přihlásí. Azure AD používá adresu URL ke spuštění aplikace z Office 365 nebo Azure AD přístupového panelu. Když je pole prázdné, Azure AD spoléhá na zprostředkovatele identity spusťte jednotného přihlašování, když uživatel spustí aplikaci.|
    | Stav přenosu | Nepovinné | Nepovinné | Určuje pro aplikaci, kam má přesměrovat uživatele po dokončení ověřování. Obvykle hodnota není platná adresa URL pro aplikaci. Nicméně některé aplikace používají toto pole odlišně. Další informace vám sdělí dodavatel aplikace.
    | Adresa URL – odhlášení | Nepovinné | Nepovinné | Používá k odeslání odpovědi odhlašování SAML zpět do aplikace.


2. Chcete-li upravit základní možnosti konfigurace SAML, vyberte **upravit** ikonu (tužky) v pravém horním rohu **základní konfiguraci SAML** oddílu.

     ![Konfigurace certifikátů](media/configure-single-sign-on-portal/basic-saml-configuration-edit-icon.png)

3. Do příslušných polí na stránce zadejte informace získaný od dodavatele aplikace v kroku 1.

4. V horní části stránky vyberte **Uložit**.

## <a name="configure-user-attributes-and-claims"></a>Nakonfigurovat atributy uživatele a deklarace identity 

Můžete řídit, jaké informace o službě Azure AD pošle aplikaci v tokenu SAML, když se uživatel přihlásí. Tyto informace můžete řídit konfigurací atributy uživatele. Můžete například nakonfigurovat služba Azure AD posílala jméno uživatele, e-mailu a ID zaměstnance do aplikace, když se uživatel přihlásí. 

Pro správné fungování jednotného přihlašování můžou být tyto atributy povinné nebo volitelné. Další informace najdete v [kurzu pro konkrétní aplikaci](../saas-apps/tutorial-list.md) nebo se obraťte na dodavatele aplikace.

1. Chcete-li upravit atributy uživatele a deklarace identity, vyberte **upravit** ikonu (tužky) v pravém horním rohu **atributy uživatele a deklarace identity** oddílu.

   **Hodnota identifikátoru název** je nastavena výchozí hodnotu *user.principalname*. Identifikátor uživatele jednoznačně identifikuje každého uživatele v rámci aplikace. Pokud je například uživatelským jménem i jedinečným identifikátorem e-mailová adresa, nastavte tuto hodnotu na *user.mail*.

2. Změnit **hodnota identifikátoru název**, vyberte **upravit** ikonu (tužky) **hodnota identifikátoru název** pole. Abyste provedli odpovídající změny ve formátu identifikátoru a zdroje, podle potřeby. Jakmile budete hotovi, uložte změny. Další informace o přizpůsobování deklarací identity, najdete v článku [přizpůsobení deklarací identity vystavených v tokenu SAML pro podnikové aplikace](../develop/active-directory-saml-claims-customization.md) článek.

3. Chcete-li přidat deklaraci identity, **přidat novou deklaraci** v horní části stránky. Zadejte **název** a vyberte odpovídající zdroj. Pokud vyberete **atribut** zdroje, budete muset zvolit **zdrojový atribut** chcete použít. Pokud vyberete **překlad** zdroje, budete muset zvolit **transformace** a **parametr 1** chcete použít.

4. Vyberte **Uložit**. Nová deklarace identity se zobrazí v tabulce.
 
## <a name="generate-a-saml-signing-certificate"></a>Generování podpisového certifikátu SAML

Azure AD k podepisování tokenů SAML, které odesílá do aplikace, používá certifikát. 

1. Chcete-li vygenerovat nový certifikát, vyberte **upravit** ikonu (tužky) v pravém horním rohu **podpisový certifikát SAML** části.

2. V **podpisový certifikát SAML** vyberte **nový certifikát**.

3. V řádku nový certifikát, který se zobrazí, nastavte **datum vypršení platnosti**. Další informace o dostupných možností konfigurace, najdete v článku [pokročilé možnosti podepisování certifikátů](certificate-signing-options.md) článku.

4. Vyberte **Uložit** v horní části **podpisový certifikát SAML** oddílu. 

## <a name="assign-users-to-the-application"></a>Přiřazení uživatelů k aplikaci

Je vhodné otestovat jednotné přihlašování s několika uživateli nebo skupinami před zavedením aplikace pro vaši organizaci.

> [!NOTE]
>
> Tyto kroky se použijí k **uživatelů a skupin** oddíl konfigurace na portálu. Po dokončení, bude nutné přejít zpět **jednotného přihlašování** části k dokončení tohoto kurzu.

Přiřazení uživatele nebo skupiny k aplikaci:

1. Pokud ještě není otevřeno, otevřete aplikaci na portálu.
2. V levém navigačním panelu pro aplikaci, vyberte **uživatelů a skupin**.
3. Vyberte **přidat uživatele**.
4. V **přidat přiřazení** vyberte **uživatelů a skupin**.
5. Chcete-li vyhledat konkrétního uživatele, zadejte uživatelské jméno do **vyberte člena nebo Pozvěte externího uživatele** pole. Potom vyberte profilové fotky uživatele nebo loga a klikněte na tlačítko **vyberte**. 
6. V **přidat přiřazení** vyberte **přiřadit**. Až budete hotovi, vybraným uživatelům se zobrazí v **uživatelů a skupin** seznamu.

## <a name="set-up-the-application-to-use-azure-ad"></a>Nastavení aplikace, které používají Azure AD

Už jste téměř hotovi.  V posledním kroku budete muset nastavit aplikaci pro použití služby Azure AD jako zprostředkovatele identity SAML. 

1. Přejděte dolů k položce **nastavení <applicationName>**  oddílu. Pro účely tohoto kurzu je volána v této části **nastavení testu Githubu**. 
2. Zkopírujte hodnotu z každého řádku v této části. Pak vložte odpovídající řádek v každé hodnotě **základní konfiguraci SAML** oddílu. Například zkopírovat **přihlašovací adresa URL** hodnotu z **nastavení testu Githubu** části a vložte ho do **přihlašovací adresa URL** pole **základní konfiguraci SAML**  oddílu a tak dále.
3. Když všechny hodnoty jste vložili do příslušných polí, vyberte **Uložit**.

## <a name="test-single-sign-on"></a>Test jednotného přihlašování

Jste připraveni vaše nastavení testu.  

1. Otevřete nastavení jednotného přihlašování pro vaši aplikaci. 
2. Přejděte **ověření jednotného přihlašování s <applicationName>**  oddílu. Pro účely tohoto kurzu je volána v této části **nastavení testu Githubu**.
3. Vyberte **Test**. Zobrazí se možnosti testování.
4. Vyberte **přihlásit jako aktuální uživatel**. Tento test umožňuje nejprve zjistit, zda se jednotné přihlašování funguje pro vás na správce.

Pokud dojde k chybě, zobrazí se chybová zpráva. Proveďte následující kroky:

1. Zkopírujte podrobnosti a vložte je do pole **Jak chyba vypadá?**.

    ![Získání postupu řešení](media/configure-single-sign-on-portal/error-guidance.png)

2. Vyberte **pomoc rozlišení**. Zobrazit kořenové příčiny a řešení doprovodné materiály.  V tomto příkladu se uživateli přiřadí k aplikaci.

3. Přečtěte si pokyny k řešení a potom, pokud je to možné, pokud chcete problém vyřešit.

4. Spusťte test znovu, dokud se úspěšně nedokončí.

## <a name="next-steps"></a>Další postup
V tomto kurzu jste nakonfigurovali jednotné přihlašování – nastavení pro aplikaci. Po dokončení konfigurace jste k aplikaci přiřadili uživatele a nakonfigurovali jste aplikaci tak, aby používala jednotné přihlašování na základě SAML. Když jste to všechno dokončili, ověřili jste, jestli přihlašování SAML funguje správně.

Provedli jste tyto akce:
> [!div class="checklist"]
> * Výběr SAML jako režimu jednotného přihlašování
> * Kontaktování dodavatele aplikace kvůli konfiguraci domény a adres URL
> * Konfigurace atributů uživatele
> * Vytvoření podpisového certifikátu SAML
> * Ruční přiřazení uživatelů nebo skupin k aplikaci
> * Nakonfigurována pro použití služby Azure AD jako zprostředkovatele identity SAML
> * Test jednotného přihlašování založeného na SAML

Chcete-li zavedení aplikace více uživatelům ve vaší organizaci, použijte automatické zřizování uživatelů.

> [!div class="nextstepaction"]
> [Informace o přiřazování uživatelů s využitím automatického zřizování](configure-automatic-user-provisioning-portal.md)


