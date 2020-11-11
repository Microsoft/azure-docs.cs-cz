---
title: Nastavení předplatného Azure Marketplace pro hostované testovací jednotky
description: Vysvětluje, jak nastavit předplatné Azure Marketplace pro Dynamics 365 for Customer Engagement a Dynamics 365 for Operations test disks.
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: trkeya
ms.author: trkeya
ms.date: 11/09/2020
ms.openlocfilehash: 6f74f3b1f5aad153903ba5d290a290973203a875
ms.sourcegitcommit: b4880683d23f5c91e9901eac22ea31f50a0f116f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/11/2020
ms.locfileid: "94489382"
---
# <a name="set-up-an-azure-marketplace-subscription-for-hosted-test-drives"></a>Nastavení předplatného Azure Marketplace pro hostované testovací jednotky

Tento článek vysvětluje, jak nastavit předplatné Azure Marketplace a **dynamics 365 pro zákaznickou zapojení** nebo **Dynamics 365 pro provozní** prostředí pro testovací jednotky.

## <a name="set-up-for-dynamics-365-for-customer-engagement"></a>Nastavení pro Dynamics 365 pro zákaznickou zapojení

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s účtem správce.
2. Pomocí ikony účtu v pravém horním rohu ověřte, že jste v tenantovi přidruženém k instanci testovací jednotky Dynamics 365. Pokud nejste ve správném tenantovi, vyberte ikonu účtu a přepněte se do správného tenanta.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Výběr správného tenanta.":::

3. Ověřte, jestli je k dispozici licence pro **plán zákaznických rezervací 365 pro zákazníky** .

    [![Kontroluje se licence na plán.](media/test-drive/check-plan-license.png)](media/test-drive/check-plan-license.png#lightbox)

4. Vytvořte v Azure aplikaci Azure Active Directory (AD). AppSource bude tuto aplikaci používat ke zřízení a zrušení zřízení uživatele testovací jednotky ve vašem tenantovi.
    1. V podokně filtru vyberte možnost **Azure Active Directory**.
    2. Vyberte **Registrace aplikací**.

        [![Výběr registrací aplikací](media/test-drive/app-registrations.png)](media/test-drive/app-registrations.png#lightbox)

    3. Vyberte **Nová registrace**.
    4. Zadejte vhodný název aplikace.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Registrace aplikace.":::

    5. V části Podporované typy účtů vyberte možnost **účet v libovolném adresáři organizace a na osobních účtech Microsoft**.
    6. Vyberte **vytvořit** a počkejte, než se vaše aplikace vytvoří.
    7. Po vytvoření aplikace si poznamenejte **ID aplikace** zobrazené na obrazovce Přehled. Tuto hodnotu budete potřebovat později při konfiguraci testovacího disku.
    8. Pokud chcete přidat identifikátor URI pro přesměrování nativeclient, vyberte okno **ověřování** . V části **konfigurace platformy** vyberte možnost **Přidat platformu**  >  **mobilní**  >  **desktopovou** aplikaci. Zvolte identifikátor URI pro přesměrování **nativeclient** a vyberte **Konfigurovat**.

        :::image type="content" source="./media/test-drive/configure-desktop-devices.png" alt-text="Přidávání identifikátoru URI pro přesměrování nativeclient":::

    9. V části **Spravovat aplikaci** vyberte **oprávnění rozhraní API**.
    10. Vyberte **Přidat oprávnění** a pak **Microsoft Graph API**.
    11. Vyberte kategorii oprávnění **aplikace** a pak **adresář. Read. All** a **Directory. pro čtení. všechna** oprávnění.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Nastavení oprávnění aplikace.":::

    12. Chcete-li přidat aplikaci **Dynamics CRM – přístup uživatelů k zosobnění** pro seznam povolených aplikací Azure AD, vyberte možnost **Přidat oprávnění** znovu.

        :::image type="content" source="./media/test-drive/request-api-permissions.png" alt-text="Požaduje se oprávnění aplikace.":::

    13. Po přidání oprávnění vyberte **udělení souhlasu správce Microsoftu**.
    14. V okně Výstraha zprávy vyberte **Ano**.

        [![Zobrazuje oprávnění aplikace byla úspěšně udělena.](media/test-drive/api-permissions-confirmation-customer.png)](media/test-drive/api-permissions-confirmation-customer.png#lightbox)

    15. Pro vygenerování tajného klíče pro Aplikace Azure AD:
        1. V **možnosti spravovat aplikaci** vyberte **certifikát a tajné klíče**.
        2. V části tajné klíče klienta vyberte **nový tajný klíč klienta**.
        3. Zadejte popis, jako je například *testovací jednotka* , a vyberte vhodnou dobu trvání. Po vypršení platnosti klíče se testovací jednotka pozastaví, a to tak, že budete muset vygenerovat a zadat AppSource nový klíč.
        4. Výběrem **Přidat** vygenerujete tajný klíč aplikace Azure. Tuto hodnotu zkopírujte, protože bude skrytá, jakmile lave toto okno. Tuto hodnotu budete potřebovat později při konfiguraci testovacího disku.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Přidání tajného klíče klienta.":::

5. V některých případech trvá synchronizace uživatele ze služby Azure AD s instancí CRM déle, než se očekávalo. Pro pomoc s tímto jsme přidali proces vynucení synchronizace, ale vyžaduje, aby aplikace Azure AD byla povolená partnerským centrem. Provedete to tak, že si přečtěte téma [synchronizace uživatelů s instancí Engagement zákazníka](https://github.com/microsoft/AppSource/blob/master/Microsoft%20Hosted%20Test%20Drive/CDS_Utility_to_ForceUserSync_in_CRM_Instance.md).
6. Přidejte do aplikace roli instančního objektu, aby aplikace Azure AD mohla odebrat uživatele z vašeho tenanta Azure.
    1. Otevřete příkazový řádek PowerShellu na úrovni správy.
    2. Install-Module MSOnline (Pokud není nainstalován MSOnline, spusťte tento příkaz).
    3. Connect-MsolService (zobrazí se místní okno, přihlaste se pomocí nově vytvořeného tenanta organizace).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47dB-91af-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId – RoleMemberType servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Přihlaste se ke svému účtu.":::

7. Přidejte výše vytvořenou aplikaci Azure jako uživatele aplikace do instance služby Test Drive CRM.
    1. Přidat nového uživatele v **Azure Active Directory**. K vytvoření tohoto uživatele se vyžadují jenom hodnoty **název** a **uživatelské jméno** (patřící ke stejnému tenantovi) a ostatní pole ponechte jako výchozí. Zkopírujte hodnotu uživatelské jméno.
    2. Přihlaste se do **instance CRM** a vyberte **Nastavení**  >  **Security**  >  **Uživatelé** zabezpečení.
    3. Změňte zobrazení na **uživatele aplikace**.

        :::image type="content" source="./media/test-drive/application-users.png" alt-text="Nastavení informací o účtu pro uživatele.":::

    4. Přidejte nového uživatele (Ujistěte se, že je formulář pro uživatele aplikace).
    5. Do polí **primární e-mail** a **uživatelské jméno** zadejte stejné uživatelské jméno. Přidejte do **ID aplikace** **Azure ApplicationId** .
    6. Zadejte libovolný **úplný název**.
    7. Vyberte **Uložit**.
    8. Vyberte **Spravovat role**.
    9. Přiřaďte vlastní roli zabezpečení nebo roli OOB, která obsahuje oprávnění ke čtení, zápisu a přiřazování rolí, jako je například *správce systému*.

        :::image type="content" source="./media/test-drive/security-roles-selection.png" alt-text="Výběr oprávnění role.":::

    10. Přiřaďte uživateli aplikace vlastní roli zabezpečení, kterou jste vytvořili pro testovací jednotku.

## <a name="set-up-for-dynamics-365-for-operations"></a>Nastavení pro Dynamics 365 pro operace

1. Přihlaste se k [Azure Portal](https://portal.azure.com/) s účtem správce.
2. Pomocí ikony účtu v pravém horním rohu ověřte, že jste v tenantovi přidruženém k instanci testovací jednotky Dynamics 365. Pokud nejste ve správném tenantovi, vyberte ikonu účtu a přepněte se do správného tenanta.

    :::image type="content" source="./media/test-drive/select-correct-tenant.png" alt-text="Výběr správného tenanta.":::

3. Vytvořte Aplikace Azure AD v Azure. AppSource bude tuto aplikaci používat ke zřízení a zrušení zřízení uživatele testovací jednotky ve vašem tenantovi.
    1. V podokně filtru vyberte možnost **Azure Active Directory**.
    2. Vyberte **Registrace aplikací**.

        :::image type="content" source="./media/test-drive/app-registrations.png" alt-text="Výběr registrace aplikace":::

    3. Vyberte **Nová registrace**.
    4. Zadejte vhodný název aplikace.

        :::image type="content" source="./media/test-drive/register-an-application.png" alt-text="Registrace aplikace.":::

    5. V části Podporované typy účtů vyberte možnost **účet v libovolném adresáři organizace a na osobních účtech Microsoft**.
    6. Vyberte **vytvořit** a počkejte, než se vaše aplikace vytvoří.
    7. Po vytvoření aplikace si poznamenejte **ID aplikace** zobrazené na obrazovce Přehled. Tuto hodnotu budete potřebovat později při konfiguraci testovacího disku.
    8. V části **Spravovat aplikaci** vyberte **oprávnění rozhraní API**.
    9. Vyberte **Přidat oprávnění** a pak **Microsoft Graph API**.
    10. Vyberte kategorii oprávnění **aplikace** a pak **adresář. Read. All** a **Directory. pro čtení. všechna** oprávnění.

        :::image type="content" source="./media/test-drive/microsoft-graph.png" alt-text="Nastavení oprávnění aplikace.":::

    11. Vyberte **Přidat oprávnění**.
    12. Po přidání oprávnění vyberte **udělení souhlasu správce Microsoftu**.
    13. V okně Výstraha zprávy vyberte **Ano**.

        [![Zobrazuje úspěšně udělená oprávnění aplikace.](media/test-drive/api-permissions-confirmation-operations.png)](media/test-drive/api-permissions-confirmation-operations.png#lightbox)

    14. Pro vygenerování tajného klíče pro Aplikace Azure AD:
        1. V **možnosti spravovat aplikaci** vyberte **certifikát a tajné klíče**.
        2. V části tajné klíče klienta vyberte **nový tajný klíč klienta**.
        3. Zadejte popis, jako je například *testovací jednotka* , a vyberte vhodnou dobu trvání. Po vypršení platnosti klíče se testovací jednotka pozastaví, a to tak, že budete muset vygenerovat a zadat AppSource nový klíč.
        4. Výběrem **Přidat** vygenerujete tajný klíč aplikace Azure. Tuto hodnotu zkopírujte, protože bude skrytá, jakmile lave toto okno. Tuto hodnotu budete potřebovat později při konfiguraci testovacího disku.

            :::image type="content" source="./media/test-drive/add-client-secret.png" alt-text="Přidání tajného klíče klienta.":::

4. Přidejte do aplikace roli instančního objektu, aby aplikace Azure AD mohla odebrat uživatele z vašeho tenanta Azure.
    1. Otevřete příkazový řádek PowerShellu na úrovni správy.
    2. Install-Module MSOnline (Pokud není nainstalován MSOnline, spusťte tento příkaz).
    3. Connect-MsolService (zobrazí se místní okno, přihlaste se pomocí nově vytvořeného tenanta organizace).
    4. $applicationId = **<YOUR_APPLICATION_ID>**.
    5. $sp = Get-MsolServicePrincipal-AppPrincipalId $applicationId.
    6. Add-MsolRoleMember-RoleObjectId fe930be7-5e62-47dB-91af-98c3a49a38b1-RoleMemberObjectId $sp. ObjectId – RoleMemberType servicePrincipal.

        :::image type="content" source="./media/test-drive/sign-in-to-account.png" alt-text="Přihlaste se ke svému účtu.":::

5. Nyní přidejte výše uvedenou aplikaci do **Dynamics 365 pro operace** , aby aplikace mohla spravovat uživatele.
    1. Najděte svou instanci **Dynamics 365 for Operations** .
    2. V levém horním rohu klikněte na nabídku se třemi řádky (hamburgerovou ").
    3. Vyberte **Správa systému**.
    4. Vyberte **Azure Active Directory aplikace**.
    5. Vyberte **+ Nový**.
    6. Zadejte **ID klienta aplikace Azure AD** , které bude provádět akce prováděné jménem uživatele.

    > [!NOTE]
    > ID uživatele, jehož jménem budou provedeny akce (obvykle správce systému instance nebo uživatel, který má oprávnění k přidání dalších uživatelů).

    [![ID uživatele, jehož jménem budou provedeny akce (obvykle správce systému instance nebo uživatel, který má oprávnění k přidání dalších uživatelů).](media/test-drive/system-admin-user-id.png)](media/test-drive/system-admin-user-id.png#lightbox)
<!--
## Next steps

- [Commercial marketplace partner and customer usage attribution](azure-partner-customer-usage-attribution.md) -->
