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
ms.date: 12/06/2018
ms.author: celested
ms.reviewer: arvinh,luleon
ms.openlocfilehash: fdb009a8f66ffe47ac7e677717152a2620beebc7
ms.sourcegitcommit: d1c5b4d9a5ccfa2c9a9f4ae5f078ef8c1c04a3b4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55960951"
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

## <a name="before-you-begin"></a>Před zahájením

1. Pokud aplikace nebyl přidán do vašeho tenanta Azure AD, přečtěte si téma [rychlý start: Přidání aplikace do svého tenanta Azure AD](add-application-portal.md).

2. Požádejte dodavatele vaší aplikace o informace popsané v části [Konfigurace domény a adres URL](#configure-domain-and-urls).

3. K testování postupů v tomto kurzu doporučujeme použít neprodukční prostředí. Pokud nemáte neprodukční prostředí Azure AD, můžete [získat měsíční zkušební verzi](https://azure.microsoft.com/pricing/free-trial/).

4. Přihlaste se k [webu Azure portal](https://portal.azure.com) jako správce cloudové aplikace nebo aplikace správce pro vašeho tenanta Azure AD.

## <a name="select-a-single-sign-on-mode"></a>Výběr režimu jednotného přihlašování

Po přidání aplikace do svého tenanta Azure AD, budete připraveni ke konfiguraci jednotného přihlašování pro aplikaci.

Otevření nastavení jednotného přihlašování:

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na levém navigačním panelu na **Azure Active Directory**. 

2. V okně **Azure Active Directory** klikněte na **Podnikové aplikace**. Otevře se okno **Všechny aplikace**, ve kterém se zobrazí náhodný vzorek aplikací ve vašem tenantovi Azure AD. 

3. V nabídce **Typ aplikace** vyberte **Všechny aplikace** a klikněte na **Použít**.

4. Zadejte název aplikace, pro kterou chcete nakonfigurovat jednotné přihlašování. Zvolte svoji vlastní aplikaci, nebo zadejte **GitHub-test** ke konfiguraci aplikace, které jste přidali v kroku [přidat aplikaci](add-application-portal.md) rychlý start.

5. Klikněte na **Jednotné přihlašování**. V části **Režim jednotného přihlašování** se jako výchozí možnost zobrazí **Jednotné přihlašování na základě SAML**. 

    ![Možnosti konfigurace](media/configure-single-sign-on-portal/config-options.png)

6. V horní části okna klikněte na **Uložit**. 

## <a name="configure-domain-and-urls"></a>Konfigurace domény a adres URL

Konfigurace domény a adres URL:

1. Obraťte se na dodavatele aplikace, aby vám poskytl správné informace pro následující nastavení:

    | Nastavení konfigurace | Iniciováno zprostředkovatelem přihlašování | Iniciováno pomocí IdP | Popis |
    |:--|:--|:--|:--|
    | Přihlašovací adresa URL | Požaduje se | Nezadávejte | Když uživatel otevře tuto adresu URL, poskytovatel služeb ho přesměruje do Azure AD, kde se uživatel ověří a přihlásí. Azure AD používá adresu URL ke spuštění aplikace z Office 365 nebo Azure AD přístupového panelu. Když je pole prázdné, Azure AD spoléhá na zprostředkovatele identity k zahájení jednotného přihlašování, když uživatel spustí aplikaci.|
    | Identifikátor (ID entity) | Vyžaduje se pro některé aplikace | Vyžaduje se pro některé aplikace | Jednoznačně identifikuje aplikaci, pro kterou se konfiguruje jednotné přihlašování. Azure AD odešle identifikátor aplikace jako parametr cílové skupiny tokenu SAML. Abyste ověřili, že se očekává aplikace. Tato hodnota se také zobrazuje jako ID entity ve všech metadatech SAML poskytovaných aplikací.|
    | Adresa URL odpovědi | Nepovinné | Požaduje se | Určuje, kde aplikace očekává přijetí tokenu SAML. Adresa URL odpovědi se také označuje jako adresa URL ACS (Assertion Consumer Service). |
    | Stav přenosu | Nepovinné | Nepovinné | Určuje pro aplikaci, kam má přesměrovat uživatele po dokončení ověřování. Obvykle je tato hodnota platná adresa URL aplikace, ale některé aplikace s tímto polem pracují jinak. Další informace vám sdělí dodavatel aplikace.

2. Zadejte požadované informace. Pokud chcete zobrazit všechna nastavení, klikněte na **Zobrazit pokročilé nastavení URL**.

    ![Možnosti konfigurace](media/configure-single-sign-on-portal/config-urls.png)

3. V horní části okna klikněte na **Uložit**.

4. Je **nastavení testu SAML** tlačítko v této části. Tento test spustíte později v části [Test jednotného přihlašování](#test-single-sign-on).

## <a name="configure-user-attributes"></a>Konfigurace atributů uživatele

Atributy uživatele umožňují řídit, jaké informace o službě Azure AD odešle aplikaci v tokenu SAML pokaždé, když uživatel přihlásí na. Azure AD může do aplikace odesílat například jméno, e-mail a ID zaměstnance uživatele. 

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

3. V **vytvořit nový certifikát** okno, nastavte **datum vypršení platnosti**a klikněte na tlačítko **Uložit**.

4. Klikněte na **Aktivovat nový certifikát**.

5. Další informace najdete v tématu [Pokročilé možnosti podepisování certifikátu](certificate-signing-options.md).

6. Pokud chcete ponechat změny, které jste zatím provedli, nezapomeňte v horní části okna **Jednotné přihlašování** kliknout na **Uložit**. 

## <a name="assign-users-to-the-application"></a>Přiřazení uživatelů k aplikaci

Microsoft doporučuje před postupným zavedením aplikace v rámci organizace otestovat jednotné přihlašování s několika uživateli nebo skupinami.

Přiřazení uživatele nebo skupiny k aplikaci:

1. Pokud ještě není otevřeno, otevřete aplikaci na portálu.
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
3. Ručně vytvořte uživatelské účty v aplikaci otestovat jednotné přihlašování. Vytvořte uživatelské účty, které jste k aplikaci přiřadili v [předchozí části](#assign-users-to-the-application). 

## <a name="test-single-sign-on"></a>Test jednotného přihlašování

Jste připraveni otestovat své nastavení.  

1. Otevřete nastavení jednotného přihlašování pro vaši aplikaci. 
2. Posuňte se do části **Konfigurace domény a adres URL**.
2. Klikněte na **Otestovat nastavení SAML**. Zobrazí se možnosti testování.

    ![Možnosti testování jednotného přihlašování](media/configure-single-sign-on-portal/test-single-sign-on.png) 

3. Klikněte na **Přihlásit se jako aktuální uživatel**. Tento test umožňuje nejprve zjistit, zda se jednotné přihlašování funguje pro vás na správce.
4. Pokud dojde k chybě, zobrazí se chybová zpráva. Zkopírujte podrobnosti a vložte je do pole **Jak chyba vypadá?**.

    ![Získání postupu řešení](media/configure-single-sign-on-portal/error-guidance.png)

5. Klikněte na **Získat postup řešení**. Zobrazit kořenové příčiny a řešení doprovodné materiály.  V tomto příkladu se uživateli přiřadí k aplikaci.

    ![Oprava chyby](media/configure-single-sign-on-portal/fix-error.png)

6. Přečtěte si postup řešení a pak v případě potřeby klikněte na **Opravit**.

7. Spusťte test znovu, dokud se úspěšně nedokončí.



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

Zavedení aplikace více uživatelům ve vaší organizaci, doporučujeme používat automatické zřizování uživatelů.

> [!div class="nextstepaction"]
>[Zjistěte, jak jako znak uživatelům automatické zřizování](configure-automatic-user-provisioning-portal.md)


