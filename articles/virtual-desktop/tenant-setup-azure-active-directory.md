---
title: Vytvoření klienta ve Windows Virtual Desktop – Azure
description: Popisuje, jak nastavit klienty virtuální plochy Windows ve službě Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 12/17/2019
ms.author: helohr
manager: lizross
ms.openlocfilehash: 9f9f7f709d31967e892900ccb25657a5963c0379
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79370215"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop"></a>Kurz: Vytvoření klienta ve Virtuální ploše Windows

Vytvoření klienta ve Windows Virtual Desktop je prvním krokem k vytvoření řešení virtualizace plochy. Tenant je skupina jednoho nebo více hostitelských fondů. Každý fond hostitelů se skládá z více hostitelů relací, kteří běží jako virtuální počítače v Azure a jsou registrováni ve službě Windows Virtual Desktop. Každý fond hostitelů se také skládá z jedné nebo více skupin aplikací, které se používají k publikování prostředků vzdálené plochy a vzdálených aplikací uživatelům. Pomocí tenanta můžete vytvářet fondy hostitelů, vytvářet skupiny aplikací, přiřazovat uživatele a navazovat připojení prostřednictvím služby.

V tomto kurzu získáte informace o těchto tématech:

> [!div class="checklist"]
> * Udělte službě Virtuální desktopová služba Azure Active Directory oprávnění.
> * Přiřaďte roli aplikace TenantCreator uživateli ve vašem tenantovi Služby Azure Active Directory.
> * Vytvořte klienta virtuální plochy Windows.

## <a name="what-you-need-to-set-up-a-tenant"></a>Co potřebujete k nastavení klienta

Než začnete nastavovat klienta Virtuální plochy Windows, ujistěte se, že máte tyto věci:

* ID [klienta Služby Azure Active Directory](https://azure.microsoft.com/services/active-directory/) pro uživatele Virtuální plochy Windows.
* Účet globálního správce v rámci klienta Služby Azure Active Directory.
   * To platí také pro organizace zprostředkovatele cloudových řešení (CSP), které vytvářejí klienta virtuální plochy Windows pro své zákazníky. Pokud jste v organizaci CSP, musíte být schopni přihlásit jako globální správce instance Azure Active Directory zákazníka.
   * Účet správce musí pocházet z tenanta Služby Azure Active Directory, ve kterém se pokoušíte vytvořit klienta virtuální plochy Windows. Tento proces nepodporuje účty Azure Active Directory B2B (guest).
   * Účet správce musí být pracovní nebo školní účet.
* Předplatné Azure.

Musíte mít připravené ID klienta, účet globálního správce a předplatné Azure, aby proces popsaný v tomto kurzu mohl správně fungovat.

## <a name="grant-permissions-to-windows-virtual-desktop"></a>Udělení oprávnění virtuální ploše systému Windows

Pokud jste již udělili oprávnění k virtuální ploše Windows pro tuto instanci služby Azure Active Directory, tuto část přeskočte.

Udělení oprávnění službě Virtuální desktop Windows jí umožňuje dotaz ovat službu Azure Active Directory na úkoly správy a koncových uživatelů.

Udělení oprávnění služby:

1. Otevřete prohlížeč a začněte s tokem souhlasu správce do [aplikace Windows Virtual Desktop server](https://login.microsoftonline.com/common/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   > [!NOTE]
   > Pokud spravujete zákazníka a potřebujete udělit souhlas správce pro adresář zákazníka, zadejte do prohlížeče následující adresu URL a nahraďte {tenant} názvem domény Azure AD zákazníka. Pokud například organizace zákazníka zaregistrovala název domény Azure AD contoso.onmicrosoft.com, nahraďte {tenant} contoso.onmicrosoft.com.
   >```
   >https://login.microsoftonline.com/{tenant}/adminconsent?client_id=5a0aa725-4958-4b0c-80a9-34562e23f3b7&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

2. Přihlaste se na stránku souhlasu virtuální plochy Windows pomocí účtu globálního správce. Pokud jste například byli v organizaci Contoso, admin@contoso.com admin@contoso.onmicrosoft.commůže být váš účet nebo .
3. Vyberte **Přijmout**.
4. Počkejte jednu minutu, aby Azure AD můžete zaznamenat souhlas.
5. Otevřete prohlížeč a začněte s tokem souhlasu správce do [klientské aplikace Virtuální desktop windows](https://login.microsoftonline.com/common/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback).
   >[!NOTE]
   > Pokud spravujete zákazníka a potřebujete udělit souhlas správce pro adresář zákazníka, zadejte do prohlížeče následující adresu URL a nahraďte {tenant} názvem domény Azure AD zákazníka. Pokud například organizace zákazníka zaregistrovala název domény Azure AD contoso.onmicrosoft.com, nahraďte {tenant} contoso.onmicrosoft.com.
   >```
   > https://login.microsoftonline.com/{tenant}/adminconsent?client_id=fa4345a4-a730-4230-84a8-7d9651b86739&redirect_uri=https%3A%2F%2Frdweb.wvd.microsoft.com%2FRDWeb%2FConsentCallback
   >```

6. Přihlaste se na stránku souhlasu virtuální plochy Windows jako globální správce, stejně jako v kroku 2.
7. Vyberte **Přijmout**.

## <a name="assign-the-tenantcreator-application-role"></a>Přiřazení role aplikace TenantCreator

Přiřazení uživatele Služby Azure Active Directory k roli aplikace TenantCreator umožňuje tomuto uživateli vytvořit klienta virtuální plochy Windows přidruženého k instanci Služby Azure Active Directory. Budete muset použít účet globálního správce k přiřazení role TenantCreator.

Přiřazení role aplikace TenantCreator:

1. Přejděte na [portál Azure](https://portal.azure.com) a spravujte roli aplikace TenantCreator. Vyhledejte a vyberte **podnikové aplikace**. Pokud pracujete s více klienty Služby Azure Active Directory, je osvědčeným postupem otevřít soukromou relaci prohlížeče a zkopírovat a vložit adresy URL do adresního řádku.

   ![Snímek obrazovky s hledáním podnikových aplikací na webu Azure Portal](media/azure-portal-enterprise-applications.png)
2. V **rámci podnikových aplikací**vyhledejte **službu Windows Virtual Desktop**. Dvě aplikace, které jste poskytli souhlas, se zobrazí v předchozí části. Z těchto dvou aplikací vyberte **Windows Virtual Desktop**.
   
   ![Snímek obrazovky s výsledky hledání při hledání "Windows Virtual Desktop" v "Podnikové aplikace." Aplikace s názvem "Windows Virtual Desktop" je zvýrazněna.](media/tenant-enterprise-app.png)
3. Vyberte **Možnost Uživatelé a skupiny**. Může se zobrazit, že správce, který udělil souhlas s aplikací, je již uveden s přiřazenou rolí **Výchozí přístup.** To nestačí k vytvoření klienta virtuální plochy systému Windows. Pokračujte podle těchto pokynů a přidejte roli **TenantCreator** k uživateli.
   
   ![Snímek obrazovky s uživateli a skupinami přiřazenými ke správě podnikové aplikace "Windows Virtual Desktop". Snímek obrazovky zobrazuje pouze jedno přiřazení, které je pro "Výchozí přístup".](media/tenant-default-access.png)
4. Vyberte **Přidat uživatele**a pak na kartě **Přidat přiřazení** vyberte **Uživatelé a skupiny.**
5. Vyhledejte uživatelský účet, který vytvoří klienta virtuální plochy Windows. Pro jednoduchost to může být účet globálního správce.
   - Pokud používáte poskytovatele identity Microsoft, jako se mi líbí contosoadmin@live.com nebo contosoadmin@outlook.com, je možné, že se nebudete moci přihlásit k virtuální ploše Windows. Doporučujeme použít účet specifický admin@contoso.com pro admin@contoso.onmicrosoft.com doménu, jako je nebo místo toho.

   ![Snímek obrazovky s výběrem uživatele, který má být přidat jako "TenantCreator".](media/tenant-assign-user.png)
   > [!NOTE]
   > Musíte vybrat uživatele (nebo skupinu, která obsahuje uživatele), který pochází z této instance služby Azure Active Directory. Nelze vybrat hosta (B2B) uživatele nebo instanční ho.

6. Vyberte uživatelský účet, zvolte tlačítko **Vybrat** a pak vyberte **Přiřadit**.
7. Na stránce **Windows Virtual Desktop – Uživatelé a skupiny** ověřte, zda se uvástovánová položka s rolí **TenantCreator** přiřazenou uživateli, který vytvoří klienta virtuální plochy Windows.

   ![Snímek obrazovky s uživateli a skupinami přiřazenými ke správě podnikové aplikace "Windows Virtual Desktop". Snímek obrazovky nyní obsahuje druhou položku uživatele přiřazeného k roli "TenantCreator".](media/tenant-tenant-creator-added.png)

Než budete pokračovat v vytváření klienta virtuální plochy Windows, potřebujete dvě informace:

   - ID klienta služby Azure Active Directory (nebo **ID adresáře)**
   - ID předplatného Azure

Vyhledání ID klienta Služby Azure Active Directory (nebo **ID adresáře):**
1. Ve stejné relaci [portálu Azure](https://portal.azure.com) vyhledejte a vyberte **Službu Azure Active Directory**.

   ![Snímek obrazovky s výsledky hledání pro "Azure Active Directory" na webu Azure Portal. Výsledek hledání v části "Služby" je zvýrazněn.](media/tenant-search-azure-active-directory.png)
2. Posuňte se dolů, dokud nenajdete **vlastnosti**, a pak je vyberte.
3. Vyhledejte **ID adresáře**a vyberte ikonu schránky. Vložte jej do praktického umístění, abyste ji později mohli použít jako hodnotu **AadTenantId.**

   ![Snímek obrazovky s vlastnostmi služby Azure Active Directory Myš se pohybuje nad ikonou schránky pro "Directory ID" pro kopírování a vkládání.](media/tenant-directory-id.png)

Jak najít ID předplatného Azure:
1. Ve stejné relaci [portálu Azure](https://portal.azure.com) vyhledejte a vyberte **předplatná**.
   
   ![Snímek obrazovky s výsledky hledání pro "Azure Active Directory" na webu Azure Portal. Výsledek hledání v části "Služby" je zvýrazněn.](media/tenant-search-subscription.png)
2. Vyberte předplatné Azure, které chcete použít k přijímání oznámení služby Windows Virtual Desktop.
3. Vyhledejte **ID předplatného**a najeďte na hodnotu, dokud se nezobrazí ikona schránky. Vyberte ikonu schránky a vložte ji do praktického umístění, abyste ji později mohli použít jako hodnotu **AzureSubscriptionId.**
   
   ![Snímek obrazovky s vlastnostmi předplatného Azure. Myš se pohybuje nad ikonou schránky pro "ID předplatného", kterou chcete zkopírovat a vložit.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-tenant"></a>Vytvoření klienta virtuální plochy Windows

Teď, když jste udělili službě Windows Virtual Desktop oprávnění k dotazování služby Azure Active Directory a přiřadili roli TenantCreator k uživatelskému účtu, můžete vytvořit klienta virtuální plochy Windows.

Nejprve [si stáhněte a importujte modul Virtuální plochy Windows,](/powershell/windows-virtual-desktop/overview/) který chcete použít v relaci PowerShellu, pokud jste tak ještě neučinili.

Přihlaste se k virtuální ploše Windows pomocí uživatelského účtu TenantCreator s touto rutinou:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Poté vytvořte nového klienta Virtuální plochy Windows přidruženého k tenantovi Služby Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Nahraďte hodnoty v závorkách hodnotami relevantními pro vaši organizaci a tenanta. Název, který zvolíte pro nového klienta virtuální plochy Windows, by měl být globálně jedinečný. Řekněme například, že jste Windows Virtual Desktop TenantCreator pro organizaci Contoso. Rutina, kterou byste spustit bude vypadat takto:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

Je vhodné přiřadit přístup pro správu druhému uživateli v případě, že se někdy ocitnete uzamčeni ze svého účtu, nebo jdete na dovolenou a potřebujete někoho, kdo bude ve vaší nepřítomnosti fungovat jako správce tenanta. Chcete-li druhému uživateli přiřadit přístup správce, `<TenantName>` `<Upn>` spusťte následující rutinu s názvem klienta a hlavní název uživatele druhého uživatele.

```powershell
New-RdsRoleAssignment -TenantName <TenantName> -SignInName <Upn> -RoleDefinitionName "RDS Owner"
```

## <a name="next-steps"></a>Další kroky

Po vytvoření tenanta budete muset vytvořit instanční objekt ve službě Azure Active Directory a přiřadit mu roli v rámci virtuální plochy Windows. Instanční objekt vám umožní úspěšně nasadit nabídku Windows Virtual Desktop Azure Marketplace k vytvoření fondu hostitelů. Další informace o fondech hostitelů najdete v kurzu pro vytvoření fondu hostitelů ve virtuální ploše windows.

> [!div class="nextstepaction"]
> [Vytvoření objektů služby a přiřazení rolí pomocí PowerShellu](./create-service-principal-role-powershell.md)
