---
title: Vytvoření tenanta ve virtuální ploše Windows (Classic) – Azure
description: Popisuje, jak nastavit klienty virtuálních počítačů s Windows (Classic) v Azure Active Directory.
author: Heidilohr
ms.topic: tutorial
ms.date: 03/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: f41584194f8f5e8afde630405116b8b169cb5656
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/29/2020
ms.locfileid: "91542032"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-classic"></a>Kurz: vytvoření tenanta ve virtuální ploše Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows.

Vytvoření tenanta ve Windows Virtual desktopu je prvním krokem k vytvoření řešení virtualizace plochy. Tenant je skupina jednoho nebo více fondů hostitelů. Každý fond hostitelů se skládá z několika hostitelů relací, které běží jako virtuální počítače v Azure a jsou zaregistrované ve službě Virtual Desktop systému Windows. Každý fond hostitelů se taky skládá z jedné nebo několika skupin aplikací, které se používají k publikování prostředků vzdálené plochy a vzdálených aplikací pro uživatele. S klientem můžete vytvářet fondy hostitelů, vytvářet skupiny aplikací, přiřazovat uživatele a provádět připojení prostřednictvím služby.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Udělte Azure Active Directory oprávnění ke službě Virtual Desktop systému Windows.
> * Přiřaďte roli aplikace TenantCreator uživateli ve vašem tenantovi Azure Active Directory.
> * Vytvořte klienta virtuální plochy Windows.

## <a name="what-you-need-to-set-up-a-tenant"></a>Co potřebujete k nastavení tenanta

Než začnete s nastavováním tenanta virtuálních klientů Windows, ujistěte se, že máte tyto věci:

* ID tenanta [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pro uživatele virtuálních klientů Windows.
* Účet globálního správce v rámci tenanta Azure Active Directory.
   * To platí také pro organizace poskytovatele Cloud Solution Provider (CSP), které vytvářejí tenanta virtuálních klientů Windows pro své zákazníky. Pokud jste v organizaci CSP, musíte být schopni se přihlásit jako globální správce instance Azure Active Directory zákazníka.
   * Účet správce musí být nahlášený z klienta Azure Active Directory, ve kterém se snažíte vytvořit klienta virtuální plochy Windows. Tento proces nepodporuje Azure Active Directory účty B2B (Guest).
   * Účet správce musí být pracovní nebo školní účet.
* Předplatné Azure.

Musíte mít ID tenanta, účet globálního správce a předplatné Azure připravené, aby proces popsaný v tomto kurzu mohl správně fungovat.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Udělení oprávnění k virtuálnímu počítači s Windows

Pokud jste již pro tuto instanci Azure Active Directory udělena oprávnění pro virtuální plochu Windows, přeskočte tuto část.

Udělování oprávnění službě Windows Virtual Desktop umožňuje IT dotazování Azure Active Directory pro úlohy správy a koncových uživatelů.

Udělení oprávnění ke službě:

1. Otevřete prohlížeč a zahajte postup souhlasu správce do [aplikace serveru virtuálních počítačů s Windows](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Pokud spravujete zákazníka a potřebujete udělit souhlas správce pro adresář zákazníka, zadejte následující adresu URL do prohlížeče a nahraďte {tenant} názvem domény zákazníka Azure AD. Pokud například organizace zákazníka zaregistrovala název domény služby Azure AD contoso.onmicrosoft.com, nahraďte {tenant} contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Přihlaste se na stránku pro vyjádření souhlasu s virtuálním počítačem s Windows pomocí účtu globálního správce. Pokud jste například používali organizaci Contoso, váš účet může být admin@contoso.com nebo admin@contoso.onmicrosoft.com .
3. Vyberte **Přijmout**.
4. Počkejte jednu minutu, aby služba Azure AD mohla nahrávat souhlas.
5. Otevřete prohlížeč a zahajte postup souhlasu správce s [klientskou aplikací pro virtuální počítače s Windows](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > Pokud spravujete zákazníka a potřebujete udělit souhlas správce pro adresář zákazníka, zadejte následující adresu URL do prohlížeče a nahraďte {tenant} názvem domény zákazníka Azure AD. Pokud například organizace zákazníka zaregistrovala název domény služby Azure AD contoso.onmicrosoft.com, nahraďte {tenant} contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Přihlaste se na stránku souhlasu s virtuálním počítačem s Windows jako globální správce, jak jste to provedli v kroku 2.
7. Vyberte **Přijmout**.

## <a name="assign-the-tenantcreator-application-role"></a>Přiřazení role aplikace TenantCreator

Přiřazení Azure Active Directoryho uživatele role aplikace TenantCreator umožňuje tomuto uživateli vytvořit klienta virtuálního počítače s Windows přidruženého k instanci Azure Active Directory. K přiřazení role TenantCreator budete muset použít svůj globální účet správce.

Přiřazení role aplikace TenantCreator:

1. Chcete-li spravovat roli aplikace TenantCreator, otevřete [Azure Portal](https://portal.azure.com) . Vyhledejte a vyberte **podnikové aplikace**. Pokud pracujete s více Azure Active Directory klienty, je osvědčeným postupem otevření privátní relace prohlížeče a zkopírování a vložení adres URL do panelu Adresa.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s vyhledáváním podnikových aplikací v Azure Portal](../media/azure-portal-enterprise-applications.png)

2. V rámci **podnikových aplikací**vyhledejte **virtuální počítač s Windows**. V předchozí části uvidíte dvě aplikace, které jste zadali jako souhlas. Z těchto dvou aplikací vyberte **virtuální klient Windows**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s výsledky hledání při hledání "virtuální plochy Windows" v "podnikových aplikacích" Zvýrazní se aplikace s názvem virtuální plocha Windows.](../media/tenant-enterprise-app.png)

3. Vyberte **Uživatelé a skupiny**. Může se stát, že správce, který udělil souhlas s aplikací, je již uveden s přiřazenou **výchozí rolí přístupu** . Nestačí vytvořit klienta virtuální plochy Windows. Pokračujte podle těchto pokynů a přidejte roli **TenantCreator** uživateli.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky uživatelů a skupin přiřazený ke správě podnikové aplikace "virtuální plocha Windows" Snímek obrazovky zobrazuje pouze jedno přiřazení, které je pro "výchozí přístup".](../media/tenant-default-access.png)

4. Vyberte **Přidat uživatele**a pak na kartě **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
5. Vyhledejte uživatelský účet, který vytvoří tenanta pro virtuální počítače s Windows. Pro jednoduchost může to být účet globálního správce.
   - Pokud používáte poskytovatele identity Microsoftu, jako je contosoadmin@live.com nebo contosoadmin@outlook.com , možná se nebudete moct přihlásit k virtuálnímu počítači s Windows. Doporučujeme použít účet specifický pro doménu, jako je admin@contoso.com nebo admin@contoso.onmicrosoft.com místo.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s výběrem uživatele, který chcete přidat jako "TenantCreator".](../media/tenant-assign-user.png)

   > [!NOTE]
   > Musíte vybrat uživatele (nebo skupinu, která obsahuje uživatele), která je vytvořená z této instance Azure Active Directory. Nemůžete zvolit uživatele typu Host (B2B) nebo instanční objekt.

6. Vyberte uživatelský účet, klikněte na tlačítko **Vybrat** a pak vyberte **přiřadit**.
7. Na stránce **virtuální počítač s Windows – uživatelé a skupiny** ověřte, že vidíte novou položku s přiřazenou rolí **TenantCreator** k uživateli, který vytvoří tenanta virtuální plochy Windows.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky uživatelů a skupin přiřazený ke správě podnikové aplikace "virtuální plocha Windows" Snímek obrazovky teď obsahuje druhou položku uživatele přiřazenou k roli "TenantCreator".](../media/tenant-tenant-creator-added.png)

Než budete pokračovat v vytváření tenanta virtuálních klientů Windows, budete potřebovat dvě části informací:

   - Vaše ID tenanta Azure Active Directory (nebo **ID adresáře**)
   - ID vašeho předplatného Azure

Zjištění ID tenanta Azure Active Directory (nebo **ID adresáře**):
1. Ve stejné [Azure Portal](https://portal.azure.com) relaci vyhledejte a vyberte **Azure Active Directory**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s výsledky hledání "Azure Active Directory" v Azure Portal. Výsledek hledání v části služby je zvýrazněný.](../media/tenant-search-azure-active-directory.png)

2. Posuňte se dolů, dokud nenajdete **vlastnosti**a pak je vyberte.
3. Vyhledejte **ID adresáře**a potom vyberte ikonu schránky. Vložte ho do užitečného umístění, abyste ho později mohli použít jako hodnotu **AadTenantId** .

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s vlastnostmi Azure Active Directory. Myš najede myší na ikonu schránky pro položku "ID adresáře", která se má zkopírovat a vložit.](../media/tenant-directory-id.png)

Pokud chcete zjistit ID vašeho předplatného Azure:
1. Ve stejné [Azure Portal](https://portal.azure.com) relaci vyhledejte a vyberte **odběry**.

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky s výsledky hledání "Azure Active Directory" v Azure Portal. Výsledky hledání pro "služby" se zvýrazní.](../media/tenant-search-subscription.png)

2. Vyberte předplatné Azure, které chcete použít pro příjem oznámení služby Windows Virtual Desktop.
3. Vyhledejte **ID předplatného**a umístěte ukazatel myši na hodnotu, dokud se nezobrazí ikona schránky. Vyberte ikonu schránky a vložte ji do užitečného umístění, abyste ji mohli použít později jako hodnotu **AzureSubscriptionId** .

   > [!div class="mx-imgBorder"]
   > ![Snímek obrazovky vlastností předplatného Azure Myš najede myší na ikonu schránky pro "ID předplatného" ke zkopírování a vložení.](../media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Vytvoření tenanta virtuálních klientů Windows

Teď, když jste udělili oprávnění služby Virtual Desktop systému Windows k dotazování Azure Active Directory a přiřadili roli TenantCreator uživatelskému účtu, můžete vytvořit klienta virtuální plochy Windows.

Nejdřív [Stáhněte a importujte modul Virtual desktopu Windows](/powershell/windows-virtual-desktop/overview/) pro použití v relaci PowerShellu, pokud jste to ještě neudělali.

Přihlaste se k virtuálnímu počítači s Windows pomocí uživatelského účtu TenantCreator s touto rutinou:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Pak vytvořte nového tenanta virtuálních počítačů s Windows, který je přidružený k Azure Active Directory tenant:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Hodnoty v závorkách nahraďte hodnotami, které jsou relevantní pro vaši organizaci a tenanta. Název, který zvolíte pro nového tenanta virtuálních klientů Windows, by měl být globálně jedinečný. Řekněme například, že jste TenantCreator pro virtuální počítače s Windows pro organizaci Contoso. Rutina, kterou spustíte, by vypadala takto:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

K druhému uživateli je vhodné přiřadit přístup pro správu v případě, že jste se k vašemu účtu někdy odhlásili nebo jste na dovolenou a potřebujete někoho, aby ve vaší absenci pracoval jako správce tenanta. Chcete-li přiřadit přístup správce k druhému uživateli, spusťte následující rutinu s `<TenantName>` `<Upn>` názvem vašeho tenanta a hlavním jménem druhého uživatele.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Další kroky
Po vytvoření tenanta budete muset vytvořit instanční objekt v Azure Active Directory a přiřadit mu roli v rámci virtuálního klienta Windows. Instanční objekt vám umožní úspěšně nasadit virtuální plochu Windows Azure Marketplace nabídku a vytvořit fond hostitelů. Další informace o fondech hostitelů najdete v kurzu vytváření fondu hostitelů na virtuálním počítači s Windows.

> [!div class="nextstepaction"]
> [Vytvoření objektů služby a přiřazení rolí pomocí PowerShellu](create-service-principal-role-powershell.md)
