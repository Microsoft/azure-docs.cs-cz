---
title: Správa zájemců pro Dynamics 365 pro zákaznickou zapojení | Azure Marketplace
description: Nakonfigurujte řízení zájemců pro Dynamics 365 pro zákaznickou zapojení.
services: Azure, Marketplace, commercial marketplace, Partner Center
author: qianw211
ms.service: marketplace
ms.topic: conceptual
ms.date: 07/30/2019
ms.author: evansma
ms.openlocfilehash: 03d67249ef1d74844a7e9019e90f6331ecfdf0c3
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70102981"
---
# <a name="configure-lead-management-for-dynamics-365-for-customer-engagement"></a>Konfigurace správy zájemců pro Dynamics 365 pro zákaznickou zapojení

Tento článek popisuje, jak nastavit Dynamics 365 for Customer Engagement (dřív Dynamics CRM Online), přečtěte si další informace [](https://docs.microsoft.com/dynamics365/customer-engagement/admin/switch-dynamics-crm-online-dynamics-365) o této změně, abyste mohli zpracovat prodejní zájemce z nabídky Marketplace. 

>[!Note]
>Tyto pokyny jsou specifické pro prostředí Microsoft Hosted Cloud Dynamics 365 for Customer Engagement. Připojení přímo k Prem prostředí Dynamics se momentálně nepodporuje. k dispozici jsou i další možnosti, jak můžete dostávat zájemce, jako je například konfigurace [koncového bodu https](./commercial-marketplace-lead-management-instructions-https.md) nebo [tabulky Azure](./commercial-marketplace-lead-management-instructions-azure-table.md) pro příjem zájemců.

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku jsou nutná následující oprávnění uživatele:

* Aby bylo možné nainstalovat řešení a postupovat podle těchto pokynů, musíte být správcem na vaší instanci Dynamics 365 for Customer Engagement.
* Abyste mohli vytvořit nový účet služby pro službu zájemce, který se používá k odesílání zájemců z nabídek Marketplace, musíte být správcem tenanta.
* Musíte mít přístup k portálu pro správu systému Office 365.
* Musíte mít přístup k Azure Portal.

## <a name="install-the-solution"></a>Instalace řešení

1.  Stáhněte si [řešení Microsoft Marketplace pro zápis zájemců](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip) a uložte ho místně do vašeho počítače.

2.  Přejděte na adresu URL vaší instance `https://tenant.crm.dynamics.com`Dynamics a otevřete Dynamics 365 pro zákaznickou zapojení.

3.  Nastavení přístupu výběrem ikony ozubeného kolečka a **rozšířeného nastavení** v horním navigačním panelu.
 
    ![Dynamics-rozšířené nastavení](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

4.  Na stránce nastavení v horním navigačním panelu přejděte do nabídky nastavení a vyberte **řešení**.

    >[!Note]
    >Pokud nevidíte možnosti na následujícím snímku obrazovky, pak nemáte oprávnění, která potřebujete, abyste mohli pokračovat. Obraťte se na správce na Dynamics 365 pro instanci služby Customer Engagement.

    ![Dynamics 365 – řešení](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

5. Na stránce řešení vyberte **importovat** a přejděte do umístění, kam jste uložili Microsoft Marketplace řešení pro *zápis zájemců* , které jste stáhli v kroku 1.

    ![Dynamics 365 for Customer Engagement – import](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

6. Import řešení dokončíte pomocí Průvodce importem řešení.

## <a name="configure-user-permissions"></a>Konfigurace uživatelských oprávnění

Pokud chcete psát zájemce do vaší instance Dynamics 365 for Customer Engagement, musíte s námi sdílet účet služby a nakonfigurovat oprávnění pro tento účet.

Pomocí následujícího postupu vytvořte účet služby a přiřaďte oprávnění. Můžete použít **Azure Active Directory** nebo **Office 365**.

>[!Note]
>Na základě vámi vybrané možnosti ověřování můžete přeskočit na odpovídající pokyny podle vašeho výběru. Viz [Azure Active Directory](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#azure-active-directory) nebo [Office 365](https://docs.microsoft.com/azure/marketplace/partner-center-portal/commercial-marketplace-lead-management-instructions-dynamics#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Tato možnost se doporučuje, protože získáte výhodu, že nikdy nepotřebujete aktualizovat uživatelské jméno nebo heslo, abyste mohli začít získávat zájemce. Pokud chcete použít možnost Azure Active Directory, zadejte ID aplikace, klíč aplikace a ID adresáře z vaší aplikace Active Directory.

Pomocí následujících kroků můžete nakonfigurovat Azure Active Directory pro Dynamics 365 pro zapojení zákazníka.

1. Přihlaste se k [Azure Portal](https://portal.azure.com/)a pak vyberte službu Azure Active Directory z levé navigační části.

2. V Azure Active Directory levém navigačním panelu vyberte **vlastnosti** a ZKOPÍRUJTE hodnotu **ID adresáře** na této stránce. Tuto hodnotu uložte, protože se jedná o hodnotu *ID adresáře* , kterou musíte zadat na portálu pro publikování a získat tak zájemce pro vaši nabídku na webu Marketplace.

    ![Azure Active Directory – vlastnosti](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

3. V Azure Active Directory levém navigačním panelu vyberte **Registrace aplikací** a pak na této stránce vyberte **Nová registrace** .
4. Zadejte název pro název aplikace. Zadejte smysluplný název aplikace.
5. V části Podporované typy účtů vyberte **účty v libovolném organizačním adresáři**.
6. V části identifikátor URI pro přesměrování vyberte **Web** a zadejte identifikátor URI ( `https://contosoapp1/auth`například). 
7. Vyberte **Zaregistrovat**.

    ![Zaregistrovat aplikaci](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

8. Teď, když je vaše aplikace zaregistrovaná, přejděte na stránku Přehled aplikace a zkopírujte na tuto stránku hodnotu **ID aplikace (klienta)** . Tuto hodnotu uložte, protože se jedná o hodnotu *ID aplikace (klienta)* , kterou je třeba zadat na portálu pro publikování a v aplikaci Dynamics a získat zájemce pro vaši nabídku na webu Marketplace.

    ![ID aplikace (klienta)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

9. V levém navigačním panelu aplikace vyberte **certifikáty a tajné klíče** a na této stránce vyberte **nový tajný klíč klienta** . Zadejte smysluplný popis tajného kódu klienta a v části konec platnosti vyberte možnost **nikdy** . Vyberte **Přidat** a vytvořte tajný klíč klienta.

    ![Aplikace – certifikace a tajné klíče](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

10. Po úspěšném vytvoření tajného klíče klienta **Zkopírujte hodnotu tajného klíče klienta**. Po opuštění stránky už nebudete moct tuto hodnotu načíst. Tuto hodnotu uložte, protože se jedná o hodnotu *tajného klíče klienta* , kterou potřebujete zadat na portálu pro publikování a získat zájemce pro vaši nabídku na webu Marketplace. 
11. V levém navigačním panelu aplikace vyberte **oprávnění rozhraní API** a pak vyberte **Přidat oprávnění**.
12. Vyberte rozhraní API Microsoftu a pak jako rozhraní API vyberte **Dynamics CRM** .
13. V části *jaký typ oprávnění vaše aplikace vyžaduje*, ujistěte se, že je vybraná možnost **delegovaná oprávnění** . Ověřte oprávnění pro přístup k **user_impersonation** *Common data Service jako uživatelé organizace*. Vyberte **Přidat oprávnění**.

    ![Přidejte oprávnění.](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

14. Po dokončení kroků 1-13 na Azure Portal přejděte k vaší instanci Dynamics 365 for Customer Engagement tak, že přejdete na adresu URL (například `https://tenant.crm.dynamics.com`).
15. Nastavení přístupu výběrem ikony ozubeného kolečka a **rozšířeného nastavení** v horním navigačním panelu.
16. Na stránce nastavení v horním navigačním panelu přejděte do nabídky nastavení a vyberte **zabezpečení**.
17. Po na stránce zabezpečení vyberte **Uživatelé**.  Na stránce Uživatelé vyberte rozevírací seznam povolených uživatelů, aby bylo možné přepnout na **uživatele aplikace**.
18. Pokud chcete vytvořit nového uživatele, vyberte **Nový** . 

    ![Vytvoření nového uživatele](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

19. V části **Nový uživatel**se ujistěte, že uživatel: Je vybrán uživatel aplikace. Zadejte uživatelské jméno, jméno a příjmení a e-mailovou adresu uživatele, kterého chcete s tímto připojením použít. Také vložte do **ID aplikace** aplikaci, kterou jste vytvořili v Azure Portal z kroku 8. Kliknutím na **Uložit a zavřít** dokončete přidávání uživatele.

    ![Nový uživatel](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

20. V části nastavení zabezpečení v tomto článku můžete dokončit konfiguraci připojení pro tohoto uživatele.

### <a name="office-365"></a>Office 365

Pokud nechcete používat Azure Active Directory, můžete zaregistrovat nového uživatele v *centru pro správu Microsoft 365*. Aby bylo možné pokračovat v získávání zájemců, budete muset aktualizovat uživatelské jméno nebo heslo každých 90 dní.

Pomocí následujících kroků můžete nakonfigurovat Office 365 pro Dynamics 365 pro zapojení zákazníka.

1. Přihlaste se k [centru pro správu Microsoft 365](https://admin.microsoft.com).

2. Vyberte **Přidat uživatele**.

    ![Centrum pro správu Microsoft 365 – přidání uživatele](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

4. Vytvořte nového uživatele pro službu zapisovače zájemců. Nakonfigurujte následující nastavení:

    * Zadejte uživatelské jméno.
    * Zadejte heslo a zrušte zaškrtnutí políčka změnit heslo tohoto uživatele při prvním přihlášení.
    * Jako roli pro uživatele vyberte uživatel (bez přístupu správce).
    * Jako produktovou licenci zobrazenou na následujícím snímku obrazovky vyberte Dynamics 365 Customer Engagement Plan. Bude se vám účtovat licence, kterou si zvolíte. 

Tyto hodnoty uložte, protože se jedná o *uživatelské jméno a heslo* , které je potřeba zadat na portálu pro publikování a získat zájemce pro vaši nabídku na webu Marketplace.

![Centrum pro správu Microsoft 365 – nový uživatel](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Nastavení zabezpečení

Posledním krokem je povolit uživatele, kterého jste vytvořili pro zápis zájemců.

1. Přejděte na adresu URL vaší instance `https://tenant.crm.dynamics.com`Dynamics a otevřete Dynamics 365 pro zákaznickou zapojení.
2. Nastavení přístupu výběrem ikony ozubeného kolečka a **rozšířeného nastavení** v horním navigačním panelu.
3. Na stránce nastavení v horním navigačním panelu přejděte do nabídky nastavení a vyberte **zabezpečení**.
4. Po na stránce zabezpečení vyberte **Uživatelé** a vyberte uživatele, kterého jste vytvořili v části Konfigurace oprávnění uživatele v tomto dokumentu, a pak vyberte **Spravovat role**. 

    ![Správa rolí](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

5. Vyhledejte název role Microsoft Marketplace vedoucí zapisovač a vyberte ji pro přiřazení uživatele k roli.

    ![Správa rolí uživatelů](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!Note]
    >Tato role je vytvořena řešením, které jste importovali, a má pouze oprávnění k zápisu zájemců a ke sledování verze řešení za účelem zajištění kompatibility.

6. Přejděte zpět na stránku zabezpečení a vyberte **role zabezpečení**. Vyhledejte roli "Microsoft Marketplace vedoucí zapisovač" a vyberte ji.

    ![Role zabezpečení](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

7. V roli zabezpečení vyberte kartu **Základní záznamy** . Vyhledejte entitu nastavení uživatelského rozhraní entity Uživatel a pro tuto entitu povolte oprávnění vytvořit, číst a zapsat pro uživatele (1/4 žluté kružnice), a to tak, že na ně kliknete do každého odpovídajícího kruhu.

    ![Microsoft Marketplace zápisy potenciálních zákazníků – základní záznamy](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

8. Teď přejděte na kartu **vlastní nastavení** . Vyhledávejte entitu "systémová úloha" a umožňují oprávnění ke čtení, zápisu a AppendTo organizacím (plnou zelenou) pro tuto entitu kliknutím čtyřikrát do každého z příslušných kruhů.

    ![Microsoft Marketplace zapisovače potenciálních zákazníků – přizpůsobení](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

9. **Uložte a zavřete**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-for-customer-engagement"></a>Konfigurace nabídky pro odeslání zájemců do Dynamics 365 pro zákaznickou zapojení

Až budete připraveni ke konfiguraci informací o správě zájemce pro vaši nabídku na portálu pro publikování, postupujte podle následujících kroků:

1. Přejděte na stránku **nastavení nabídky** pro vaši nabídku.
2. V části Správa zájemců vyberte **připojit** .

    ![Připojení ke správě zájemců](./media/commercial-marketplace-lead-management-instructions-dynamics/connect-lead-management.png)

3. V místním okně Podrobnosti připojení vyberte **Dynamics 365 pro zapojení zákazníka** pro cíl zájemce.

    ![Podrobnosti o připojení – cíl realizace](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

4. Zadejte **adresu URL instance Dynamics 365** , například `https://contoso.crm4.dynamics.com`.
5. Vyberte metodu **ověřování**, Azure Active Directory nebo Office 365. 
6. Pokud jste vybrali Azure Active Directory, zadejte **ID aplikace** (například `23456052-aaaa-bbbb-8662-1234df56788f`:), **ID adresáře** (příklad `12345678-8af1-4asf-1234-12234d01db47`:) a **tajný kód klienta** (například: `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=`).

    ![Podrobnosti o připojení – Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

7. Pokud jste vybrali možnost Office 365, zadejte **uživatelské jméno** (například: `contoso@contoso.onmicrosoft.com`) a heslo (například: `P@ssw0rd`).

    ![Podrobnosti o připojení – uživatelské jméno](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

>[!Note]
>Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku.
