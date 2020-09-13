---
title: Správa zájemců pro Dynamics 365 Customer Engagement – Microsoft Commercial Marketplace
description: Naučte se, jak nastavit zapojení zákazníků Dynamics 365 pro správu zájemců z Microsoft AppSource a Azure Marketplace.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
author: keferna
ms.author: keferna
ms.date: 03/30/2020
ms.openlocfilehash: 31dba5489db3778d738fc2856cf6aacfd6987711
ms.sourcegitcommit: 3fc3457b5a6d5773323237f6a06ccfb6955bfb2d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/11/2020
ms.locfileid: "90030748"
---
# <a name="configure-lead-management-for-dynamics-365-customer-engagement"></a>Konfigurace správy zájemců pro Dynamics 365 Customer Engagement

Tento článek popisuje, jak nastavit zákaznickou zapojení Dynamics 365 (dříve s názvem Dynamics CRM Online). Přečtěte si další informace o změně v tématu [Konfigurace ověřování na základě serveru pomocí služby Customer Engagement a SharePointu Online](https://docs.microsoft.com/dynamics365/customerengagement/on-premises/admin/on-prem-server-based-sharepoint-online) za účelem zpracování zájemců z komerční nabídky na webu Marketplace.

>[!NOTE]
>Tyto pokyny jsou specifické pro cloudové prostředí hostované Microsoftem pro zákaznickou zapojení Dynamics 365. Připojení přímo k místnímu prostředí Dynamics není v současné době podporováno. Existují i další možnosti, jak můžete přijímat zájemce, jako je například konfigurace [koncového bodu https](./commercial-marketplace-lead-management-instructions-https.md) nebo [tabulky Azure](./commercial-marketplace-lead-management-instructions-azure-table.md).

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku jsou nutná následující uživatelská oprávnění:

* Oprávnění správce vaší instance služby Customer Engagement pro Dynamics 365, aby bylo možné nainstalovat řešení.
* Oprávnění správce tenanta pro vytvoření nového účtu služby pro službu zájemce, který slouží k odesílání zájemců z komerčních nabídek Marketplace.
* Přístup k portálu pro správu.
* Přístup k Azure Portal.

## <a name="install-the-solution"></a>Instalace řešení

1. Stáhněte si [řešení Microsoft Marketplaceho zapisovače zájemců](https://mpsapiprodwus.blob.core.windows.net/documentation/MicrosoftMarketplacesLeadIntegrationSolution_1_0_0_0_target_CRM_6.1_managed.zip)a uložte ho místně do svého počítače.

1. Kliknutím na adresu URL vaší instance Dynamics otevřete Dynamics 365 Customer Engagement `https://tenant.crm.dynamics.com` .

1. Vyberte ikonu ozubeného kolečka na horním panelu a pak vyberte **Upřesnit nastavení**.
 
    ![Položka nabídky Rozšířená nastavení Dynamics 365](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-advanced-settings.png)

1. Na stránce **Nastavení** otevřete nabídku **Nastavení** na horním panelu a vyberte možnost **řešení**.

    >[!NOTE]
    >Pokud nevidíte možnosti na následující obrazovce, nemáte oprávnění, která potřebujete, abyste mohli pokračovat. Obraťte se na správce na instanci Dynamics 365 Customer Engagement.

    ![Možnost řešení Dynamics 365](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-solutions.png)

1. Na stránce **řešení** vyberte **importovat** a přejít do umístění, kam jste uložili Microsoft Marketplace řešení pro **zápis zájemců** , které jste stáhli v kroku 1.

    ![Tlačítko Import](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-crm-import.png)

1. Import řešení dokončíte pomocí Průvodce importem řešení.

## <a name="configure-user-permissions"></a>Konfigurace uživatelských oprávnění

Pokud chcete psát zájemce do instance služby Customer Engagement pro zákazníky Dynamics 365, musíte účet služby sdílet s Microsoftem a nakonfigurovat oprávnění pro tento účet.

Pomocí následujícího postupu vytvořte účet služby a přiřaďte oprávnění. Můžete použít Azure Active Directory nebo Office 365.

>[!NOTE]
>Přejděte k odpovídajícím pokynům na základě vámi vybrané možnosti ověřování. Viz [Azure Active Directory](#azure-active-directory) nebo [Office 365](#office-365).

### <a name="azure-active-directory"></a>Azure Active Directory

Tuto možnost doporučujeme, protože nikdy nebudete muset aktualizovat své uživatelské jméno nebo heslo, aby bylo možné začít používat zájemce. Pokud chcete použít možnost Azure Active Directory, zadejte ID aplikace, klíč aplikace a ID adresáře z vaší aplikace Active Directory.

Konfigurace Azure Active Directory pro zákaznickou zapojení Dynamics 365:

1. Přihlaste se na [Azure Portal](https://portal.azure.com/). V levém podokně vyberte **Azure Active Directory**.

1. Vyberte **vlastnosti**a ZKOPÍRUJTE hodnotu **ID adresáře** na stránce **vlastností adresáře** . Tuto hodnotu uložte, protože ji budete muset poskytnout na portálu pro publikování a získat tak potenciální zákazníky pro vaši nabídku na webu Marketplace.

    ![Položka nabídky vlastností Azure Active Directory](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-properties.png)

1. V levém podokně Azure Active Directory vyberte **Registrace aplikací** a pak na této stránce vyberte **Nová registrace** .
1. Zadejte smysluplný název pro název aplikace.
1. V části **podporované typy účtů**vyberte **účty v libovolném organizačním adresáři**.
1. V části **identifikátor URI přesměrování (volitelné)** vyberte **Web** a zadejte identifikátor URI, například `https://contosoapp1/auth` . 
1. Vyberte **Zaregistrovat**.

    ![Registrovat stránku aplikace](./media/commercial-marketplace-lead-management-instructions-dynamics/register-an-application.png)

1. Teď, když je vaše aplikace zaregistrovaná, přejděte na stránku Přehled aplikace. Zkopírujte hodnotu **ID aplikace (klienta)** na této stránce. Tuto hodnotu uložte, protože ji budete muset poskytnout na portálu pro publikování a v Dynamics 365, abyste mohli dostávat zájemce pro vaši nabídku na webu Marketplace.

    ![Pole ID aplikace (klienta)](./media/commercial-marketplace-lead-management-instructions-dynamics/application-id.png)

1. V levém podokně aplikace vyberte **certifikáty & tajné klíče** a klikněte na tlačítko **nový tajný klíč klienta** . Zadejte smysluplný popis tajného kódu klienta a v části **konec platnosti**vyberte možnost **nikdy** . Vyberte **Přidat** a vytvořte tajný klíč klienta.

    ![Položka nabídky certifikáty & tajných klíčů](./media/commercial-marketplace-lead-management-instructions-dynamics/aad-certificates-secrets.png)

1. Po úspěšném vytvoření tajného klíče klienta Zkopírujte hodnotu **tajného klíče klienta** . Po opuštění stránky už nebudete moct tuto hodnotu načíst. Tuto hodnotu uložte, protože ji budete muset poskytnout na portálu pro publikování a získat tak potenciální zákazníky pro vaši nabídku na webu Marketplace. 
1. V levém podokně aplikace vyberte **oprávnění rozhraní API** a pak vyberte **+ Přidat oprávnění**.
1. Vyberte **rozhraní API Microsoftu**a pak jako rozhraní API vyberte **Dynamics CRM** .
1. V části **jaký typ oprávnění vaše aplikace vyžaduje?** Ujistěte se, že je vybraná možnost **delegovaná oprávnění** . 
1. V části **oprávnění**zaškrtněte políčko **user_impersonation** pro **přístup Common data Service jako uživatelé organizace**. Pak vyberte **Přidat oprávnění**.

    ![Tlačítko Přidat oprávnění](./media/commercial-marketplace-lead-management-instructions-dynamics/api-permissions.png)

1. Po dokončení kroků 1 až 14 v Azure Portal můžete přejít na instanci Dynamics 365 Customer Engagement tak, že přejdete na adresu URL, například `https://tenant.crm.dynamics.com` .
1. Vyberte ikonu ozubeného kolečka na horním panelu a pak vyberte **Upřesnit nastavení**.
1. Na stránce **Nastavení** otevřete nabídku **Nastavení** na horním panelu a vyberte **zabezpečení**.
1. Na stránce **zabezpečení** vyberte **Uživatelé**. Na stránce **Uživatelé** vyberte rozevírací seznam **povolených uživatelů** a pak vyberte **Uživatelé aplikace**.
1. Pokud chcete vytvořit nového uživatele, vyberte **Nový** . 

    ![Vytvoření nového uživatele](./media/commercial-marketplace-lead-management-instructions-dynamics/application-users.png)

1. V podokně **Nový uživatel** se ujistěte, že je vybraná možnost **Uživatel: uživatel aplikace** . Zadejte uživatelské jméno, jméno a příjmení a e-mailovou adresu uživatele, kterého chcete s tímto připojením použít. Také vložte do **ID aplikace** aplikaci, kterou jste vytvořili v Azure Portal z kroku 8. Vyberte **uložit & zavřít** a dokončete přidávání uživatele.

    ![Nové podokno uživatele](./media/commercial-marketplace-lead-management-instructions-dynamics/new-user-info.png)

1. V části nastavení zabezpečení v tomto článku můžete dokončit konfiguraci připojení pro tohoto uživatele.

### <a name="office-365"></a>Office 365

Pokud nechcete používat Azure Active Directory, můžete zaregistrovat nového uživatele v centru pro správu Microsoft 365. Abyste mohli pokračovat v získávání zájemců, budete muset aktualizovat uživatelské jméno a heslo každých 90 dní.

Konfigurace Office 365 pro Dynamics 365 Customer Engagement:

1. Přihlaste se k [Centru pro správu Microsoftu 365](https://admin.microsoft.com).

1. Vyberte **Přidat uživatele**.

    ![Microsoft 365 centra pro správu přidat možnost uživatele](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-add-user.png)

1. Vytvořte nového uživatele pro službu zapisovače zájemců. Nakonfigurujte tahle nastavení:

    * Zadejte uživatelské jméno.
    * Zadejte heslo a zrušte zaškrtnutí políčka **změnit heslo tohoto uživatele při prvním přihlášení** k možnosti.
    * Jako roli pro uživatele vyberte **uživatel (bez přístupu správce)** .
    * Jako licenci na produkt vyberte **plán zákaznického zapojení Dynamics 365** , jak je znázorněno na následující obrazovce. Bude se vám účtovat licence, kterou si zvolíte. 

Tyto hodnoty uložte, protože budete muset zadat **uživatelské jméno** a **heslo** na portálu pro publikování a získat tak potenciální zákazníky pro vaši nabídku na webu Marketplace.

![Microsoft 365 centrum pro správu – nové uživatelské podokno](./media/commercial-marketplace-lead-management-instructions-dynamics/ms-365-new-user.png)

## <a name="security-settings"></a>Nastavení zabezpečení

Posledním krokem je povolit uživatele, kterého jste vytvořili pro zápis zájemců.

1. Kliknutím na adresu URL vaší instance Dynamics otevřete Dynamics 365 Customer Engagement `https://tenant.crm.dynamics.com` .
1. Vyberte ikonu ozubeného kolečka na horním panelu a pak vyberte **Upřesnit nastavení**.
1. Na stránce **Nastavení** otevřete nabídku **Nastavení** na horním panelu a vyberte **zabezpečení**.
1. Na stránce **zabezpečení** vyberte **Uživatelé** a vyberte uživatele, kterého jste vytvořili v části "Konfigurace uživatelských oprávnění" v tomto dokumentu. Pak vyberte **Spravovat role**. 

    ![Karta spravovat role](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-roles.png)

1. Vyhledejte název role **Microsoft Marketplace zapisovači potenciálních zákazníků**a vyberte ji pro přiřazení uživatele k roli.

    ![Podokno Správa rolí uživatelů](./media/commercial-marketplace-lead-management-instructions-dynamics/security-manage-user-roles.png)

    >[!NOTE]
    >Tato role je vytvořena řešením, které jste importovali, a má pouze oprávnění k zápisu zájemců a ke sledování verze řešení za účelem zajištění kompatibility.

1. Vraťte se na stránku **zabezpečení** a vyberte **role zabezpečení**. Vyhledejte roli **Microsoft Marketplace zapisovači potenciálních zákazníků**a vyberte ji.

    ![Podokno role zabezpečení](./media/commercial-marketplace-lead-management-instructions-dynamics/security-roles.png)

1. V roli zabezpečení vyberte kartu **Základní záznamy** . Vyhledejte položku **Nastavení uživatelského rozhraní entity uživatele** . Pro tuto entitu povolte oprávnění vytvořit, číst a zapsat uživateli (1/4 žluté kružnice) tak, že v každém z odpovídajících kruhů kliknete jedenkrát.

    ![Karta základních záznamů Microsoft Marketplaceho zapisovače zájemců](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer.png)

1. Na kartě **vlastní nastavení** vyhledejte položku **systémová úloha** . Povolte pro tuto entitu oprávnění ke čtení, zápisu a AppendTo organizacím (plné zelené kroužky), a to tak, že v každém z příslušných kruhů kliknete čtyřikrát za časy.

    ![Microsoft Marketplace karta pro přizpůsobení zapisovače potenciálních zákazníků](./media/commercial-marketplace-lead-management-instructions-dynamics/marketplace-lead-writer-customization.png)

1. Vyberte **Uložit a zavřít**.

## <a name="configure-your-offer-to-send-leads-to-dynamics-365-customer-engagement"></a>Konfigurace nabídky pro odeslání zájemců do Dynamics 365 Customer Engagement 

Postup konfigurace informací o správě zájemců pro vaši nabídku na portálu pro publikování:

1. Pro vaši nabídku přejdete na stránku **nastavení nabídek** .
1. V části **zájemci pro zákazníky** vyberte **připojit**.

    :::image type="content" source="./media/commercial-marketplace-lead-management-instructions-dynamics/customer-leads.png" alt-text="Potenciální zákazníci":::

1. V místním okně Podrobnosti připojení vyberte možnost **Dynamics 365 Customer Engagement** pro cíl zájemce.

    ![Pole cíl zájemce](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-lead-destination.png)

1. Zadejte **adresu URL** instance Dynamics 365, například `https://contoso.crm4.dynamics.com` .

1. Vyberte metodu **ověřování**, buď Azure Active Directory, nebo sadu Office 365. 
1. Pokud jste vybrali **Azure Active Directory**, zadejte **ID aplikace (** například `23456052-aaaa-bbbb-8662-1234df56788f` ), **ID adresáře** (například) `12345678-8af1-4asf-1234-12234d01db47` a **tajný klíč klienta** (například `1234ABCDEDFRZ/G/FdY0aUABCEDcqhbLn/ST122345nBc=` ).

    ![Ověřování pomocí Azure Active Directory vybraných](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-application-id.png)

1. Pokud jste vybrali možnost **Office 365**, zadejte **uživatelské jméno** (například `contoso@contoso.onmicrosoft.com` ) a **heslo** (například `P@ssw0rd` ).

    ![Pole uživatelského jména pro Office 365](./media/commercial-marketplace-lead-management-instructions-dynamics/connection-details-authentication.png)

1. Pro **kontaktní e-maily**zadejte e-mailové adresy pro lidi ve vaší společnosti, kteří by měli dostávat e-mailová oznámení při přijetí nového zájemce. Můžete zadat více e-mailových adres tak, že je oddělíte středníky.
1. Vyberte **OK**.

Chcete-li se ujistit, že jste úspěšně připojeni k cíli zájemce, klikněte na tlačítko **ověřit** . V případě úspěchu budete mít vedoucího testu v cíli realizace.

![Kontaktní e-mailové pole](./media/commercial-marketplace-lead-management-instructions-dynamics/dynamics-connection-details.png)

>[!NOTE]
>Musíte dokončit konfiguraci zbytku nabídky a publikovat ji předtím, než budete moct získat zájemce pro tuto nabídku.
