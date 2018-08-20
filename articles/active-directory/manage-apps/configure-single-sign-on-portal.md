---
title: Konfigurace jednotného přihlašování – Azure Active Directory | Microsoft Docs
description: Tento kurz využívá Azure Portal ke konfiguraci jednotného přihlašování založeného na SAML pro aplikaci s využitím Azure Active Directory (Azure AD).
services: active-directory
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.topic: tutorial
ms.workload: identity
ms.date: 08/09/2018
ms.author: barbkess
ms.reviewer: arvinh,luleon
ms.openlocfilehash: b0180f162996c5fc4647071feaf02d42320b7c9a
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/10/2018
ms.locfileid: "40036315"
---
# <a name="tutorial-configure-saml-based-single-sign-on-for-an-application-with-azure-active-directory"></a>Kurz: Konfigurace jednotného přihlašování založeného na SAML pro aplikaci s využitím Azure Active Directory

Tento kurz využívá [Azure Portal](https://portal.azure.com) ke konfiguraci jednotného přihlašování založeného na SAML pro aplikaci s využitím Azure Active Directory (Azure AD). Podle tohoto kurzu můžete nakonfigurovat i aplikace, pro které není k dispozici [specifický kurz](../saas-apps/tutorial-list.md). 


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

1. Pokud aplikace není přidaná do vašeho tenanta Azure AD, přečtěte si [Rychlý start: Přidání aplikace do tenanta Azure AD](add-application-portal.md).

2. Požádejte dodavatele vaší aplikace o informace popsané v části [Konfigurace domény a adres URL](#configure-domain-and-urls).

3. K testování postupů v tomto kurzu doporučujeme použít neprodukční prostředí. Pokud nemáte neprodukční prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

4. Přihlaste se k webu [Azure Portal](https://portal.azure.com) jako globální správce vašeho tenanta Azure AD, správce cloudové aplikace nebo správce aplikace.

## <a name="select-a-single-sign-on-mode"></a>Výběr režimu jednotného přihlašování

Po přidání aplikace do tenanta Azure AD pro ni můžete nakonfigurovat jednotné přihlašování.

Otevření nastavení jednotného přihlašování:

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na levém navigačním panelu na **Azure Active Directory**. 

2. V okně **Azure Active Directory** klikněte na **Podnikové aplikace**. Otevře se okno **Všechny aplikace**, ve kterém se zobrazí náhodný vzorek aplikací ve vašem tenantovi Azure AD. 

3. V nabídce **Typ aplikace** vyberte **Všechny aplikace** a klikněte na **Použít**.

4. Zadejte název aplikace, pro kterou chcete nakonfigurovat jednotné přihlašování. Zvolte vlastní aplikaci nebo použijte aplikaci GitHub-test, která se přidala v rychlém startu věnovaném [přidání aplikace](add-application-portal.md).

5. Klikněte na **Jednotné přihlašování**. V části **Režim jednotného přihlašování** se jako výchozí možnost zobrazí **Jednotné přihlašování na základě SAML**. 

    ![Možnosti konfigurace](media/configure-single-sign-on-portal/config-options.png)

6. V horní části okna klikněte na **Uložit**. 

## <a name="configure-domain-and-urls"></a>Konfigurace domény a adres URL

Konfigurace domény a adres URL:

1. Obraťte se na dodavatele aplikace, aby vám poskytl správné informace pro následující nastavení:

    | Nastavení konfigurace | Iniciováno zprostředkovatelem přihlašování | Iniciováno pomocí IdP | Popis |
    |:--|:--|:--|:--|
    | Přihlašovací adresa URL | Požaduje se | Nevyplňujte | Když uživatel otevře tuto adresu URL, poskytovatel služeb ho přesměruje do Azure AD, kde se uživatel ověří a přihlásí. Azure AD na základě adresy URL spustí aplikaci z Office 365 a přístupový panel Azure AD. Pokud je toto nastavení prázdné, Azure AD provádí jednotné přihlašování iniciované pomocí IdP, když uživatel spustí aplikaci z Office 365, přístupového panelu Azure AD nebo z adresy URL pro jednotné přihlašování Azure AD.|
    | Identifikátor (ID entity) | Vyžaduje se pro některé aplikace | Vyžaduje se pro některé aplikace | Jednoznačně identifikuje aplikaci, pro kterou se konfiguruje jednotné přihlašování. Azure AD odešle identifikátor zpět do aplikace jako parametr Audience v tokenu SAML a aplikace by jej měla ověřit. Tato hodnota se také zobrazuje jako ID entity ve všech metadatech SAML poskytovaných aplikací.|
    | Adresa URL odpovědi | Nepovinné | Požaduje se | Určuje, kde aplikace očekává přijetí tokenu SAML. Adresa URL odpovědi se také označuje jako adresa URL ACS (Assertion Consumer Service). |
    | Stav přenosu | Nepovinné | Nepovinné | Určuje pro aplikaci, kam má přesměrovat uživatele po dokončení ověřování. Obvykle je tato hodnota platná adresa URL aplikace, ale některé aplikace s tímto polem pracují jinak. Další informace vám sdělí dodavatel aplikace.

2. Zadejte požadované informace. Pokud chcete zobrazit všechna nastavení, klikněte na **Zobrazit pokročilé nastavení URL**.

    ![Možnosti konfigurace](media/configure-single-sign-on-portal/config-urls.png)

3. V horní části okna klikněte na **Uložit**.

4. V této části se nachází tlačítko **Otestovat nastavení SAML**. Tento test spustíte později v části [Test jednotného přihlašování](#test-single-sign-on).

## <a name="configure-user-attributes"></a>Konfigurace atributů uživatele

Atributy uživatele umožňují určit, které informace má Azure AD odesílat do aplikace. Azure AD může do aplikace odesílat například jméno, e-mail a ID zaměstnance uživatele. Azure AD odesílá atributy uživatele do aplikace v tokenu SAML při každém přihlášení uživatele. 

Pro správné fungování jednotného přihlašování můžou být tyto atributy povinné nebo volitelné. Další informace najdete v [kurzu pro konkrétní aplikaci](../saas-apps/tutorial-list.md) nebo se obraťte na dodavatele aplikace.

1. Pokud chcete zobrazit všechny možnosti, klikněte na **Zobrazit a upravit všechny ostatní atributy uživatele**.

    ![Konfigurace atributů uživatele](media/configure-single-sign-on-portal/config-user-attributes.png)

2. Zadejte **Identifikátor uživatele**.

    Identifikátor uživatele jednoznačně identifikuje každého uživatele v rámci aplikace. Pokud je například uživatelským jménem i jedinečným identifikátorem e-mailová adresa, nastavte tuto hodnotu na *user.mail*.

3. Pokud chcete zobrazit další atributy tokenu SAML, klikněte na **Zobrazit a upravit všechny ostatní atributy uživatele**.

4. Pokud mezi **Atributy tokenu SAML** chcete přidat atribut, klikněte na **Přidat atribut**. Zadejte **Název** a v nabídce vyberte **hodnotu**.

5. Klikněte na **Uložit**. V tabulce se zobrazí nový atribut.
 
## <a name="create-a-saml-signing-certificate"></a>Vytvoření podpisového certifikátu SAML

Azure AD k podepisování tokenů SAML, které odesílá do aplikace, používá certifikát. 

1. Pokud chcete zobrazit všechny možnosti, klikněte na **Zobrazit pokročilé možnosti podepisování certifikátu**.

    ![Konfigurace certifikátů](media/configure-single-sign-on-portal/config-certificate.png)

2. Pokud chcete nakonfigurovat certifikát, klikněte na **Vytvořit nový certifikát**.

3. V okně **Vytvořit nový certifikát** nastavte datum vypršení platnosti a klikněte na **Uložit**.

4. Klikněte na **Aktivovat nový certifikát**.

5. Další informace najdete v tématu [Pokročilé možnosti podepisování certifikátu](certificate-signing-options.md).

6. Pokud chcete ponechat změny, které jste zatím provedli, nezapomeňte v horní části okna **Jednotné přihlašování** kliknout na **Uložit**. 

## <a name="assign-users-to-the-application"></a>Přiřazení uživatelů k aplikaci

Microsoft doporučuje před postupným zavedením aplikace v rámci organizace otestovat jednotné přihlašování s několika uživateli nebo skupinami.

Přiřazení uživatele nebo skupiny k aplikaci:

1. Pokud ještě není otevřená, otevřete aplikaci na portálu.
2. V levém okně aplikace klikněte na **Uživatelé a skupiny**.
3. Klikněte na **Přidat uživatele**.
4. V okně **Přidat přiřazení** klikněte na **Uživatelé a skupiny**.
5. Pokud chcete vyhledat konkrétního uživatele, zadejte jméno uživatele do pole **Vybrat**, klikněte na zaškrtávací políčko vedle profilové fotky nebo loga uživatele a klikněte na **Vybrat**. 
6. Vyhledejte své aktuální uživatelské jméno a vyberte ho. Volitelně můžete vybrat více uživatelů.
7. V okně **Přidat přiřazení** klikněte na **Přiřadit**. Po dokončení se vybraní uživatelé zobrazí v seznamu **Uživatelé a skupiny**.

## <a name="configure-the-application-to-use-azure-ad"></a>Konfigurace aplikace pro používání služby Azure AD

Už jste téměř hotovi.  Na závěr je potřeba nakonfigurovat aplikaci tak, aby používala službu Azure AD jako zprostředkovatele identity SAML. 

1. Posuňte se na konec okna **Jednotné přihlašování** pro vaši aplikaci. 

    ![Konfigurace aplikace](media/configure-single-sign-on-portal/configure-app.png)

2. Na portálu klikněte na **Konfigurovat aplikaci** a postupujte podle pokynů.
3. Pro účely testování jednotného přihlašování v aplikaci ručně vytvořte uživatelské účty. Vytvořte uživatelské účty, které jste k aplikaci přiřadili v [předchozí části](#assign-users-to-the-application).   Až budete připraveni na postupné zavedení aplikace v rámci organizace, doporučujeme využít automatické zřizování uživatelů, které v aplikaci vytvoří uživatelské účty automaticky.

## <a name="test-single-sign-on"></a>Test jednotného přihlašování

Jste připraveni otestovat své nastavení.  

1. Otevřete nastavení jednotného přihlašování pro vaši aplikaci. 
2. Posuňte se do části **Konfigurace domény a adres URL**.
2. Klikněte na **Otestovat nastavení SAML**. Zobrazí se možnosti testování.

    ![Možnosti testování jednotného přihlašování](media/configure-single-sign-on-portal/test-single-sign-on.png) 

3. Klikněte na **Přihlásit se jako aktuální uživatel**. Díky tomu nejprve zjistíte, jestli jednotné přihlašování funguje pro vás jako správce.
4. Pokud dojde k chybě, zobrazí se chybová zpráva. Zkopírujte podrobnosti a vložte je do pole **Jak chyba vypadá?**.

    ![Získání postupu řešení](media/configure-single-sign-on-portal/error-guidance.png)

5. Klikněte na **Získat postup řešení**. Zobrazí se původní příčina a postup řešení.  V tomto příkladu uživatel nebyl přiřazený k aplikaci.

    ![Oprava chyby](media/configure-single-sign-on-portal/fix-error.png)

6. Přečtěte si postup řešení a pak v případě potřeby klikněte na **Opravit**.

7. Spusťte test znovu, dokud se úspěšně nedokončí.



## <a name="next-steps"></a>Další kroky
V tomto kurzu jste pomocí webu Azure Portal nakonfigurovali pro aplikaci jednotné přihlašování s využitím Azure AD. Vyhledali jste stránku konfigurace jednotného přihlašování a nakonfigurovali jste nastavení jednotného přihlašování. Po dokončení konfigurace jste k aplikaci přiřadili uživatele a nakonfigurovali jste aplikaci tak, aby používala jednotné přihlašování na základě SAML. Když jste to všechno dokončili, ověřili jste, jestli přihlašování SAML funguje správně.

Provedli jste tyto akce:
> [!div class="checklist"]
> * Výběr SAML jako režimu jednotného přihlašování
> * Kontaktování dodavatele aplikace kvůli konfiguraci domény a adres URL
> * Konfigurace atributů uživatele
> * Vytvoření podpisového certifikátu SAML
> * Ruční přiřazení uživatelů nebo skupin k aplikaci
> * Konfigurace jednotného přihlašování pro aplikaci
> * Test jednotného přihlašování založeného na SAML

Pokud chcete aplikaci postupně zavést pro další uživatele ve vaší organizaci, doporučujeme využít automatické zřizování.

> [!div class="nextstepaction"]
>[Informace o přiřazování uživatelů s využitím automatického zřizování](configure-automatic-user-provisioning-portal.md)


