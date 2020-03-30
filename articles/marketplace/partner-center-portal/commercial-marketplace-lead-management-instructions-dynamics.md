---
title: Správa potenciálních zákazníků pro Dynamics 365 for Customer Engagement | Azure Marketplace
description: Nakonfigurujte správu potenciálních zákazníků pro Dynamics 365 pro customer engagement.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: dsindona
ms.openlocfilehash: 8af6b3a451d20bcc9cab3fa4adb9643f82b85e49
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80288814"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Konfigurace správy potenciálních zákazníků pro Dynamics 365 pro customer engagement

Tento článek popisuje, jak nastavit Dynamics 365 for Customer Engagement (dříve Dynamics CRM Online), přečtěte si další informace o změně [zde](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) pro zpracování prodejních zájemců z nabídky marketplace. 

>[!Note]
>Tyto pokyny jsou specifické pro prostředí Microsoft hostovaného cloudu Dynamics 365 for Customer Engagement. Připojení přímo k prostředí Dynamics on-prem není aktuálně podporováno, existují další možnosti pro příjem zájemců, jako je konfigurace [koncového bodu https](./commercial-marketplace-lead-management-instructions-https.md) nebo [tabulky Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) pro příjem zájemců.

## <a name="prerequisites"></a>Požadavky

Následující uživatelská oprávnění jsou potřebné pro dokončení kroků v tomto článku:

* Abyste mohli nainstalovat řešení a postupovat podle těchto pokynů, musíte být správcem instance Dynamics 365 for Customer Engagement.
* Chcete-li vytvořit nový účet služby pro službu pro potenciální zákazníky, která slouží k odesílání zájemců z nabídek marketplace, musíte být správcem klienta.
* Musíte mít přístup k portálu pro správu Office 365.
* Musíte mít přístup k portálu Azure.

## <a name="install-the-solution"></a>Instalace řešení

1.  Stáhněte si [řešení Microsoft Marketplace Lead Writer](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) a uložte ho místně do počítače.

2.  Otevřete Dynamics 365 for Customer Engagement tak, že přejdete na adresu URL instance Dynamics (například `https://tenant.crm.dynamics.com`).

3.  Přístup k nastavení výběrem ikony ozubeného kola a **upřesnit nastavení** na horní navigační liště.
 
    ![Dynamika – upřesňující nastavení](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Jakmile se dostanete na stránku Nastavení, přejděte do nabídky Nastavení z horního navigačního panelu a vyberte **Řešení**.

    >[!Note]
    >Pokud možnosti v dalším snímání obrazovky nevidíte, nemáte oprávnění, která potřebujete k pokračování. Obraťte se na správce v instanci Dynamics 365 for Customer Engagement.

    ![Dynamics 365 - Řešení](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Na stránce Řešení vyberte **Importovat** a přejděte na místo, kam jste uložili řešení *Microsoft Marketplace Lead Writer,* které jste stáhli v kroku 1.

    ![Dynamics 365 pro zapojení zákazníků – import](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Dokončete import řešení pomocí průvodce importem řešení.

## <a name="configure-user-permissions"></a>Konfigurace uživatelských oprávnění

Chcete-li zapsat zájemce do instance Dynamics 365 for Customer Engagement, musíte s námi sdílet účet služby a konfigurovat oprávnění pro účet.

Pomocí následujících kroků vytvořte účet služby a přiřaďte oprávnění. Můžete použít **Azure Active Directory** nebo Office **365**.

>[!Note]
>Na základě vybrané možnosti ověřování můžete přeskočit na odpovídající pokyny na základě vaší volby. Viz [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) nebo Office [365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Tuto možnost doporučujeme, protože získáte další výhodu, že nikdy nebudete muset aktualizovat své uživatelské jméno / heslo, abyste získali potenciální zákazníky. Chcete-li použít možnost Služby Azure Active Directory, zadejte ID aplikace, klíč aplikace a ID adresáře z aplikace služby Active Directory.

Pomocí následujících kroků nakonfigurujte službu Azure Active Directory for Dynamics 365 for Customer Engagement.

1. Přihlaste se k [portálu Azure portal](https://portal.azure.com/)a pak z levé navigace vyberte službu Azure Active Directory.

2. V levém navigačním panelu služby Azure Active Directory vyberte **Vlastnosti** a zkopírujte hodnotu **ID adresáře** na této stránce. Uložte tuto hodnotu, protože se jedná o hodnotu *ID adresáře,* kterou je třeba zadat na portálu pro publikování, abyste získali zájemce pro vaši nabídku marketplace.

    ![Azure Active Directory – vlastnosti](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. V levém navigačním panelu **služby** Azure Active Directory vyberte Registrace aplikací a na této stránce vyberte **Nová registrace.**
4. Zadejte název názvu aplikace. Zadejte smysluplný název aplikace.
5. V části Podporované typy účtů vyberte **Účty v libovolném organizačním adresáři**.
6. V části Přesměrovat **Web** identifikátor URI vyberte `https://contosoapp1/auth`web a zadejte identifikátor URI (například). 
7. Vyberte **Zaregistrovat**.

    ![Registrace aplikace](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Teď, když je vaše aplikace zaregistrována, přeziňte stránku přehledu aplikace a zkopírujte hodnotu **ID aplikace (klienta)** na této stránce. Uložte tuto hodnotu, protože se jedná o hodnotu *ID aplikace (klienta),* kterou musíte zadat na portálu pro publikování a v dynamics, abyste získali zájemce pro vaši nabídku marketplace.

    ![ID aplikace (klienta)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. Z levé navigace aplikace vyberte **Certifikáty a tajné kódy** a na této stránce vyberte **Nový tajný klíč klienta.** Zadejte smysluplný popis tajného klíče klienta a v části Platnost vyberte možnost **Nikdy.** Chcete-li vytvořit tajný klíč klienta, vyberte **přidat.**

    ![Aplikace - Certifikace a tajemství](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Jakmile je tajný klíč klienta úspěšně vytvořen, **zkopírujte hodnotu tajného klíče klienta**. Hodnotu nebudete moci načíst po přechodu od stránky. Uložte tuto hodnotu, protože se jedná o hodnotu *tajného klíče klienta,* kterou musíte zadat na portálu pro publikování, abyste získali zájemce pro vaši nabídku na marketplace. 
11. V levém navigačním panelu aplikací vyberte **oprávnění rozhraní API** a pak vyberte Přidat **oprávnění**.
12. Vyberte rozhraní Microsoft API a pak jako rozhraní API vyberte **Dynamics CRM.**
13. V části *Jaký typ oprávnění aplikace vyžaduje*, zkontrolujte, zda je **vybraná delegovaná oprávnění.** Zkontrolujte oprávnění **pro službu user_impersonation** access common data service jako *uživatelé organizace*. Vyberte **Přidat oprávnění**.

    ![Přidání oprávnění](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Po dokončení kroků 1-13 na webu Azure Portal přejděte na instanci Dynamics 365 for Customer Engagement tak, že přejdete na adresu URL (například). `https://tenant.crm.dynamics.com`
15. Přístup k nastavení výběrem ikony ozubeného kola a **upřesnit nastavení** na horním navigačním panelu.
16. Jakmile se dostanete na stránku Nastavení, přejděte z horního navigačního panelu do nabídky Nastavení a vyberte **Zabezpečení**.
17. Jakmile se na stránce Zabezpečení, vyberte **Uživatelé**.  Na stránce Uživatelé vyberte rozevírací seznam Povoleno uživatelé a přepněte na **položku Uživatelé aplikací**.
18. Chcete-li vytvořit nového uživatele, vyberte **možnost Nový.** 

    ![Vytvoření nového uživatele](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. V **aplikaci Nový uživatel**zkontrolujte, zda je vybrána možnost UŽIVATEL: UŽIVATEL APLIKACE. Zadejte uživatelské jméno, celé jméno a e-mailovou adresu uživatele, který chcete použít s tímto připojením. Vložte také **ID aplikace** pro aplikaci, kterou jste vytvořili na webu Azure Portal z kroku 8. Chcete-li dokončit přidání uživatele, vyberte **Uložit a Zavřít.**

    ![Nový uživatel](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. Přejděte na "Nastavení zabezpečení" v tomto článku dokončit konfiguraci připojení pro tohoto uživatele.

### <a name="office-365"></a>Office 365

Pokud službu Azure Active Directory používat nechcete, můžete nového uživatele zaregistrovat v *Centru pro správu Microsoftu 365*. Budete muset aktualizovat své uživatelské jméno / heslo každých 90 dní, abyste pokračovali v získávání potenciálních zákazníků.

Pomocí následujících kroků nakonfigurujte Řešení Office 365 pro Dynamics 365 pro customer engagement.

1. Přihlaste se k [Centru pro správu Microsoftu 365](https://admin.microsoft.com).

2. Vyberte **Přidat uživatele**.

    ![Centrum pro správu Microsoftu 365 – přidání uživatele](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Vytvořte nového uživatele pro službu autora zájemce. Nakonfigurujte tahle nastavení:

    * Zadání uživatelského jména
    * Zadejte heslo a zrušte výběr možnosti "Provést změnu hesla tohoto uživatele při prvním přihlášení".
    * Jako roli uživatele vyberte možnost "Uživatel (bez přístupu správce)".
    * Vyberte "Dynamics 365 Customer Engagement plan" jako licenci produktu zobrazenou v dalším zachycení obrazovky. Licence, kterou si vyberete, vám bude účtována. 

Uložte tyto hodnoty, protože se jedná o hodnoty *uživatelského jména a hesla,* které je třeba zadat na portálu pro publikování, abyste získali zájemce pro vaši nabídku marketplace.

![Centrum pro správu Microsoftu 365 – nový uživatel](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Nastavení zabezpečení

Posledním krokem je povolit uživateli, který jste vytvořili, zapsat zájemce.

1. Otevřete Dynamics 365 for Customer Engagement tak, že přejdete na adresu URL instance Dynamics (například `https://tenant.crm.dynamics.com`).
2. Přístup k nastavení výběrem ikony ozubeného kola a **upřesnit nastavení** na horní navigační liště.
3. Jakmile se dostanete na stránku Nastavení, přejděte z horního navigačního panelu do nabídky Nastavení a vyberte **Zabezpečení**.
4. Na stránce Zabezpečení vyberte **Uživatelé** a vyberte uživatele, kterého jste vytvořili v části Konfigurovat uživatelská oprávnění v tomto dokumentu, a pak vyberte **Spravovat role**. 

    ![Správa rolí](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Vyhledejte název role "Microsoft Marketplace Lead Writer" a vyberte jej, chcete-li přiřadit uživateli roli.

    ![Správa rolí uživatele](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Tato role je vytvořena řešením, které jste importovali, a má oprávnění pouze k zápisu zájemců a ke sledování verze řešení, aby byla zajištěna kompatibilita.

6. Přejděte zpět na stránku Zabezpečení a vyberte **role zabezpečení**. Vyhledejte roli "Microsoft Marketplace Lead Writer" a vyberte ji.

    ![Role zabezpečení](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. Jakmile jste v roli zabezpečení, vyberte kartu **Základní záznamy.**

    ![Zapisovač hlavních zákazníků na webu Microsoft Marketplace – základní záznamy](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Nyní přejděte na kartu **Přizpůsobení.**

    ![Microsoft Marketplace Lead Writer – přizpůsobení](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Uložit a zavřít**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Konfigurace nabídky pro odesílání zájemců do aplikace Dynamics 365 for Customer Engagement

Až budete připraveni nakonfigurovat informace o správě zájemců pro vaši nabídku na portálu pro publikování, postupujte podle následujících kroků:

1. Přejděte na stránku **Nastavení nabídky** pro vaši nabídku.
2. V části Správa zájemců vyberte **Připojit.**

    ![Připojení ke správě potenciálních zákazníků](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. V rozbalovacím okně Podrobnosti o připojení vyberte **dynamics 365 pro customer engagement** pro cíl zájemce.

    ![Podrobnosti o připojení – cíl zájemce](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Zadejte adresu URL instance Dynamics `https://contoso.crm4.dynamics.com` **365,** například .
5. Vyberte metodu **ověřování**, Službu Azure Active Directory nebo Office 365. 
6. Pokud jste vybrali službu Azure Active Directory, zadejte `23456052-aaaa-bbbb-8662-1234df56788f` **ID aplikace (klienta)** (například), **ID adresáře** `12345678-8af1-4asf-1234-12234d01db47`(příklad: ) a **tajný klíč klienta** (příklad: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Podrobnosti o připojení – Služba Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Pokud jste vybrali Office 365, zadejte **uživatelské jméno** (příklad: `contoso@contoso.onmicrosoft.com`) a Heslo (příklad: `P@ssw0rd`).

    ![Podrobnosti o připojení – uživatelské jméno](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>Před přijetím zájemců o nabídku je nutné dokončit konfiguraci zbývající nabídky a publikovat ji.
