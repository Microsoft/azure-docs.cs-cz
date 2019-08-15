---
title: Vytvoření tenanta ve Windows Virtual Desktop Preview – Azure
description: Popisuje, jak nastavit klienty Windows Virtual Desktop Preview v Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 062b815315d7bcdd5d55a86c2447a0b21295e8b6
ms.sourcegitcommit: fe50db9c686d14eec75819f52a8e8d30d8ea725b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/14/2019
ms.locfileid: "69014091"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Kurz: Vytvoření tenanta ve verzi Preview pro virtuální počítač s Windows

Vytvoření tenanta ve verzi Preview ve Windows Virtual Desktop je prvním krokem k vytvoření řešení virtualizace plochy. Tenant je skupina jednoho nebo více fondů hostitelů. Každý fond hostitelů se skládá z několika hostitelů relací, které běží jako virtuální počítače v Azure a jsou zaregistrované ve službě Virtual Desktop systému Windows. Každý fond hostitelů se taky skládá z jedné nebo několika skupin aplikací, které se používají k publikování prostředků vzdálené plochy a vzdálených aplikací pro uživatele. S klientem můžete vytvářet fondy hostitelů, vytvářet skupiny aplikací, přiřazovat uživatele a provádět připojení prostřednictvím služby.

V tomto kurzu se dozvíte, jak:

> [!div class="checklist"]
> * Udělte Azure Active Directory oprávnění ke službě Virtual Desktop systému Windows.
> * Přiřaďte roli aplikace TenantCreator uživateli ve vašem tenantovi Azure Active Directory.
> * Vytvořte klienta virtuální plochy Windows.

Tady je seznam toho, co potřebujete k nastavení tenanta pro virtuální počítače s Windows:

* ID tenanta [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pro uživatele virtuálních klientů Windows.
* Účet globálního správce v rámci tenanta Azure Active Directory.
   * To platí také pro organizace poskytovatele Cloud Solution Provider (CSP), které vytvářejí tenanta virtuálních klientů Windows pro své zákazníky. Pokud jste v organizaci CSP, musíte být schopni se přihlásit jako globální správce instance Azure Active Directory zákazníka.
   * Účet správce musí být nahlášený z klienta Azure Active Directory, ve kterém se snažíte vytvořit klienta virtuální plochy Windows. Tento proces nepodporuje Azure Active Directory účty B2B (Guest).
   * Účet správce musí být pracovní nebo školní účet.
* Předplatné Azure.

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Udělení oprávnění Azure Active Directory službě Windows Virtual Desktop Preview

Pokud jste již pro tuto instanci Azure Active Directory udělena oprávnění pro virtuální plochu Windows, přeskočte tuto část.

Udělování oprávnění službě Windows Virtual Desktop umožňuje IT dotazování Azure Active Directory pro úlohy správy a koncových uživatelů.

Udělení oprávnění ke službě:

1. Otevřete prohlížeč a zahajte postup souhlasu správce do [aplikace serveru virtuálních počítačů s Windows](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Pokud spravujete zákazníka a potřebujete udělit souhlas správce pro adresář zákazníka, zadejte následující adresu URL do prohlížeče a nahraďte {tenant} názvem domény zákazníka Azure AD. Pokud například organizace zákazníka zaregistrovala název domény služby Azure AD contoso.onmicrosoft.com, nahraďte {tenant} contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Přihlaste se na stránku pro vyjádření souhlasu s virtuálním počítačem s Windows pomocí účtu globálního správce. Pokud jste například používali organizaci Contoso, váš účet může být admin@contoso.com nebo. admin@contoso.onmicrosoft.com  
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

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Přiřaďte roli aplikace TenantCreator uživateli ve vašem tenantovi Azure Active Directory

Přiřazení Azure Active Directoryho uživatele role aplikace TenantCreator umožňuje tomuto uživateli vytvořit klienta virtuálního počítače s Windows přidruženého k instanci Azure Active Directory. K přiřazení role TenantCreator budete muset použít svůj globální účet správce.

Přiřazení role aplikace TenantCreator:

1. Otevřete prohlížeč a připojte se k [Azure Portal](https://portal.azure.com) pomocí účtu globálního správce.
   
   Pokud pracujete s více Azure Active Directory klienty, je osvědčeným postupem otevření privátní relace prohlížeče a zkopírování a vložení adres URL do panelu Adresa.
2. Na panelu hledání v Azure Portal vyhledejte **podnikové aplikace** a vyberte položku, která se zobrazí v kategorii **služby** .
3. V rámci **podnikových aplikací**vyhledejte **virtuální počítač s Windows**. V předchozí části uvidíte dvě aplikace, které jste zadali jako souhlas. Z těchto dvou aplikací vyberte **virtuální klient Windows**.
   ![Snímek obrazovky s výsledky hledání při hledání "virtuální plochy Windows" v "podnikových aplikacích" Zvýrazní se aplikace s názvem virtuální plocha Windows.](media/tenant-enterprise-app.png)
4. Vyberte **Uživatelé a skupiny**. Může se stát, že správce, který udělil souhlas s aplikací, je již uveden s přiřazenou **výchozí rolí přístupu** . Nestačí vytvořit klienta virtuální plochy Windows. Pokračujte podle těchto pokynů a přidejte roli **TenantCreator** uživateli.
   ![Snímek obrazovky uživatelů a skupin přiřazený ke správě podnikové aplikace "virtuální plocha Windows" Snímek obrazovky zobrazuje pouze jedno přiřazení, které je pro "výchozí přístup".](media/tenant-default-access.png)
5. Vyberte **Přidat uživatele**a pak v okně **Přidat přiřazení** vyberte **Uživatelé a skupiny** .
6. Vyhledejte uživatelský účet, který vytvoří tenanta pro virtuální počítače s Windows. Pro jednoduchost může to být účet globálního správce.

   ![Snímek obrazovky s výběrem uživatele, který chcete přidat jako "TenantCreator".](media/tenant-assign-user.png)

   > [!NOTE]
   > Musíte vybrat uživatele (nebo skupinu, která obsahuje uživatele), která je vytvořená z této instance Azure Active Directory. Nemůžete zvolit uživatele typu Host (B2B) nebo instanční objekt.

7. Vyberte uživatelský účet, klikněte na tlačítko **Vybrat** a pak vyberte **přiřadit**.
8. Na stránce **virtuální počítač s Windows – uživatelé a skupiny** ověřte, že vidíte novou položku s přiřazenou rolí **TenantCreator** k uživateli, který vytvoří tenanta virtuální plochy Windows.
   ![Snímek obrazovky uživatelů a skupin přiřazený ke správě podnikové aplikace "virtuální plocha Windows" Snímek obrazovky teď obsahuje druhou položku uživatele přiřazenou k roli "TenantCreator".](media/tenant-tenant-creator-added.png)

Než budete pokračovat v vytváření tenanta virtuálních klientů Windows, budete potřebovat dvě části informací:
- Vaše ID tenanta Azure Active Directory (nebo **ID adresáře**)
- ID vašeho předplatného Azure

Zjištění ID tenanta Azure Active Directory (nebo **ID adresáře**):
1. Ve stejné Azure Portal relaci vyhledejte **Azure Active Directory** na panelu hledání a vyberte položku, která se zobrazí pod kategorií **služby** .
   ![Snímek obrazovky s výsledky hledání "Azure Active Directory" v Azure Portal. Výsledek hledání v části služby je zvýrazněný.](media/tenant-search-azure-active-directory.png)
2. Posuňte se dolů, dokud nenajdete **vlastnosti**a pak je vyberte.
3. Vyhledejte **ID adresáře**a potom vyberte ikonu schránky. Vložte ho do užitečného umístění, abyste ho později mohli použít jako hodnotu **AadTenantId** .
   ![Snímek obrazovky s vlastnostmi Azure Active Directory. Myš najede myší na ikonu schránky pro položku "ID adresáře", která se má zkopírovat a vložit.](media/tenant-directory-id.png)

Pokud chcete zjistit ID vašeho předplatného Azure:
1. Ve stejné Azure Portal relaci vyhledejte odběry na panelu hledání a vyberte položku, která se zobrazí v kategorii **služby** .
   ![Snímek obrazovky s výsledky hledání "Azure Active Directory" v Azure Portal. Výsledek hledání v části služby je zvýrazněný.](media/tenant-search-subscription.png)
2. Vyberte předplatné Azure, které chcete použít pro příjem oznámení služby Windows Virtual Desktop.
3. Vyhledejte **ID**předplatného a umístěte ukazatel myši na hodnotu, dokud se nezobrazí ikona schránky. Vyberte ikonu schránky a vložte ji do užitečného umístění, abyste ji mohli použít později jako hodnotu **AzureSubscriptionId** .
   ![Snímek obrazovky vlastností předplatného Azure Myš najede myší na ikonu schránky pro "ID předplatného" ke zkopírování a vložení.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Vytvoření tenanta verze Preview pro Windows Virtual Desktop

Teď, když jste udělili oprávnění služby Virtual Desktop systému Windows k dotazování Azure Active Directory a přiřadili roli TenantCreator uživatelskému účtu, můžete vytvořit klienta virtuální plochy Windows.

Nejdřív [Stáhněte a importujte modul Virtual desktopu Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) pro použití v relaci PowerShellu, pokud jste to ještě neudělali.

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

## <a name="next-steps"></a>Další postup

Po vytvoření tenanta budete muset vytvořit instanční objekt v Azure Active Directory a přiřadit mu roli v rámci virtuálního klienta Windows. Instanční objekt vám umožní úspěšně nasadit virtuální plochu Windows Azure Marketplace nabídku a vytvořit fond hostitelů. Další informace o fondech hostitelů najdete v kurzu vytváření fondu hostitelů na virtuálním počítači s Windows.

> [!div class="nextstepaction"]
> [Vytvoření objektů služby a přiřazení rolí pomocí PowerShellu](./create-service-principal-role-powershell.md)
