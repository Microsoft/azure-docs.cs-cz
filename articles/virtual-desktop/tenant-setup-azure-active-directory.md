---
title: Vytvořit tenanta ve Windows Virtual Desktop Preview – Azure
description: Popisuje, jak nastavit službu Windows Virtual Desktop Preview tenantů ve službě Azure Active Directory.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/21/2019
ms.author: helohr
ms.openlocfilehash: 3d418d9f18c98e1b6fdf39924ab41dae77fba291
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204751"
---
# <a name="tutorial-create-a-tenant-in-windows-virtual-desktop-preview"></a>Kurz: Vytvořit tenanta v náhledu virtuální plochy Windows

Vytvoření tenanta v náhledu virtuální plochy Windows je prvním krokem k vytváření řešení virtualizace plochy. Tenant je skupina jednoho nebo několika fondů hostitele. Každý hostitel fond se skládá z více hostitelů relací, spuštěná jako virtuální počítače v Azure a zaregistrován ke službě Windows virtuálního klienta. Každý hostitel fond obsahuje také jeden nebo více skupin aplikací, které se používají k publikování zdrojů Vzdálená plocha a Vzdálená aplikace pro uživatele. S tenantem můžete vytvářet fondy hostitele, vytvořit skupiny aplikací, přiřazení uživatelů a připojovat prostřednictvím služby.

V tomto kurzu se dozvíte, jak:

> [!div class="checklist"]
> * Oprávnění grant Azure Active Directory ve službě Windows virtuálního klienta.
> * Přiřazení role aplikace TenantCreator na uživatele ve vašem tenantovi Azure Active Directory.
> * Vytvoření tenanta virtuální plochy Windows.

Zde je, co potřebujete k nastavení tenanta virtuální plochy Windows:

* [Azure Active Directory](https://azure.microsoft.com/services/active-directory/) tenant ID pro Windows Virtual Desktop users.
* Účet globálního správce tenanta Azure Active Directory.
   * To platí i pro organizace Cloud Solution Provider (CSP), které vytváříte virtuální plochy Windows tenanta pro své zákazníky. Pokud jste v organizaci zprostředkovatele kryptografických služeb, musíte být schopni se přihlásit jako globální správce Azure Active Directory instance zákazníka.
   * Účet správce musí pocházet z tenanta Azure Active Directory, ve které se snažíte vytvořit tenanta virtuální plochy Windows. Tento proces nepodporuje Azure Active Directory s B2B účty (Host).
   * Účet správce musí být pracovní nebo školní účet.
* Předplatné Azure.

## <a name="grant-azure-active-directory-permissions-to-the-windows-virtual-desktop-preview-service"></a>Oprávnění grant Azure Active Directory na službu Windows Virtual Desktop Preview

Pokud jste už udělili oprávnění k virtuálnímu klientovi Windows pro tuto instanci služby Azure Active Directory, můžete tuto část přeskočte.

Udělení oprávnění pro virtuální plochy Windows služba umožní dotazování služby Azure Active Directory pro správu a úlohách pro koncové uživatele.

Udělení oprávnění služby:

1. Otevřete prohlížeč a připojte se k [stránka pro odsouhlasení podmínek virtuální plochy Windows](https://rdweb.wvd.microsoft.com).
2. Pro **souhlas možnost** > **aplikace Server**, zadejte název tenanta Azure Active Directory nebo ID adresáře a pak vyberte **odeslat**.
        
   ID je pro zákazníky, kteří poskytovatele Cloud Solution Provider, Microsoft ID zákazníka z portálu pro partnery. Podnikoví zákazníci ID se nachází v části **Azure Active Directory** > **vlastnosti** > **ID adresáře**.
3. Přihlaste se k virtuálnímu klientovi Windows stránka pro odsouhlasení podmínek pomocí účtu globálního správce. Například pokud jste v rámci organizace Contoso, váš účet může být admin@contoso.com nebo admin@contoso.onmicrosoft.com.  
4. Vyberte **Přijmout**.
5. Vyčkat, než jedna minuta.
6. Přejděte zpět [stránka pro odsouhlasení podmínek virtuální plochy Windows](https://rdweb.wvd.microsoft.com).
7. Přejděte na **souhlas možnost** > **klientskou aplikaci**zadejte stejný název tenanta Azure Active Directory nebo ID adresáře a pak vyberte **odeslat**.
8. Přihlaste se k virtuálnímu klientovi Windows stránka pro odsouhlasení podmínek jako globální správce, protože jste v kroku 3.
9. Vyberte **Přijmout**.

## <a name="assign-the-tenantcreator-application-role-to-a-user-in-your-azure-active-directory-tenant"></a>Přiřazení role aplikace TenantCreator uživatel ve vašem tenantovi Azure Active Directory

Přiřazení uživatele služby Azure Active Directory umožňuje TenantCreator aplikační role tohoto uživatele k vytvoření virtuálního klienta Windows tenanta přidruženého k instanci služby Azure Active Directory. Bude potřeba přiřadit roli TenantCreator pomocí účtu globálního správce.

Přiřazení role aplikace TenantCreator:

1. Otevřete prohlížeč a připojte se k [webu Azure portal](https://portal.azure.com) pomocí účtu globálního správce.
   
   Pokud pracujete s více tenanty Azure Active Directory, je osvědčeným postupem je otevřete relaci prohlížeče privátní a zkopírujte a vložte adresy URL do adresního řádku.
2. Na panelu hledání na webu Azure portal vyhledejte **podnikové aplikace** a vyberte položku, která se zobrazí v části **služby** kategorie.
3. V rámci **podnikové aplikace**, vyhledejte **virtuální plochy Windows**. Uvidíte dvě aplikace, že jste zadali souhlas pro v předchozí části. Tyto dvě aplikace, vyberte **virtuální plochy Windows**.
   ![Snímek obrazovky s výsledky hledání při hledání "Windows Virtual Desktop" v "Podnikových aplikací." Aplikace s názvem "Windows Virtual Desktop" je zvýrazněn.](media/tenant-enterprise-app.png)
4. Vyberte **Uživatelé a skupiny**. Může se zobrazit, že je správce, který udělení souhlasu aplikace již označené **výchozího přístupu k** přiřazenou roli. Toto není dostatečné pro vytvoření virtuálního klienta Windows tenanta. Tyto pokyny pro přidání pokračovat **TenantCreator** role pro uživatele.
   ![Snímek obrazovky uživatelé a skupiny přiřazené k spravovat podniková aplikace "Windows Virtual Desktop". Na snímku obrazovky vidíte pouze jeden přiřazení, která je pro "Přístup k výchozím."](media/tenant-default-access.png)
5. Vyberte **přidat uživatele**a pak vyberte **uživatelů a skupin** v **přidat přiřazení** okno.
6. Vyhledejte uživatelský účet, který vytvoří váš tenant virtuální plochy Windows. Pro jednoduchost může to být účet globálního správce.

   ![Snímek obrazovky s kliknutím na uživatele přidat jako "TenantCreator."](media/tenant-assign-user.png)

   > [!NOTE]
   > Je nutné vybrat uživatele (nebo skupinu, která obsahuje uživatele), který pochází z této instance služby Azure Active Directory. Nemůžete zvolit uživatele typu Host (B2B) nebo hlavní název služby.

7. Vyberte uživatelský účet, zvolte **vyberte** tlačítko a pak vyberte **přiřadit**.
8. Na **virtuální plochy Windows – uživatelé a skupiny** stránce, ověřte, jestli se nové položky se **TenantCreator** role přiřazené uživateli, který se vytvoří virtuální plochy Windows tenanta.
   ![Snímek obrazovky uživatelé a skupiny přiřazené k spravovat podniková aplikace "Windows Virtual Desktop". Na snímku obrazovky nyní obsahuje druhý záznam uživatele, přiřazenou roli "TenantCreator".](media/tenant-tenant-creator-added.png)

Než budete pokračovat ve vytvoření vašeho tenanta virtuální plochy Windows, budete potřebovat dva druhy údajů:
- Vaše ID tenanta Azure Active Directory (nebo **ID adresáře**)
- ID vašeho předplatného Azure

K vyhledání ID vašeho tenanta Azure Active Directory (nebo **ID adresáře**):
1. Ve stejné relaci Azure portal vyhledejte **Azure Active Directory** panel hledání a vyberte položku, která se zobrazí v části **služby** kategorie.
   ![Snímek obrazovky s výsledky hledání "Azure Active Directory" na webu Azure Portal. Výsledek hledání v části "Služba" se zvýrazní.](media/tenant-search-azure-active-directory.png)
2. Posuňte se dolů, dokud nenajdete **vlastnosti**a pak ho vyberte.
3. Vyhledejte **ID adresáře**a potom vyberte ikonu schránky. Vložte ho do umístění po ruce, můžete ho použít jako novější **AadTenantId** hodnotu.
   ![Snímek obrazovky vlastností služby Azure Active Directory. Myš je přesunutá na schránky ikonu pro "ID adresáře" zkopírovat a vložit.](media/tenant-directory-id.png)

K vyhledání ID vašeho předplatného Azure:
1. Ve stejné relaci Azure portal vyhledejte **předplatná** panel hledání a vyberte položku, která se zobrazí v části **služby** kategorie.
   ![Snímek obrazovky s výsledky hledání "Azure Active Directory" na webu Azure Portal. Výsledek hledání v části "Služba" se zvýrazní.](media/tenant-search-subscription.png)
2. Vyberte předplatné Azure, kterou chcete použít pro příjem oznámení o službách virtuální plochy Windows.
3. Vyhledejte **ID předplatného**a pak najeďte myší hodnotu, dokud se nezobrazí ikona schránky. Vyberte ikonu schránky a vložte ho do umístění po ruce, můžete ho použít jako novější **AzureSubscriptionId** hodnotu.
   ![Snímek obrazovky vlastností předplatného Azure. Myš je přesunutá na schránky ikonu pro "ID předplatného" zkopírovat a vložit.](media/tenant-subscription-id.png)

## <a name="create-a-windows-virtual-desktop-preview-tenant"></a>Vytvoření klienta Windows virtuální plochy, ve verzi Preview

Teď, když máte oprávnění služby virtuální plochy Windows k dotazování služby Azure Active Directory a přiřazenou roli TenantCreator uživatelský účet, můžete vytvořit klienta virtuální plochy Windows.

Nejprve je potřeba [stáhnout a naimportovat modul virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) použít v relaci Powershellu, pokud jste tak již neučinili.

Přihlaste se k virtuálnímu klientovi Windows s použitím TenantCreator uživatelský účet s touto rutinou:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Potom vytvořte nového tenanta virtuální plochy Windows přidružené k tenantovi Azure Active Directory:

```powershell
New-RdsTenant -Name <TenantName> -AadTenantId <DirectoryID> -AzureSubscriptionId <SubscriptionID>
```

Hodnoty v závorkách nahraďte hodnotami, které jsou relevantní pro vaši organizaci a tenanta. Název, který jste vybrali pro váš nový tenant virtuální plochy Windows musí být globálně jedinečný. Řekněme například, že jste to skutečně TenantCreator virtuální plochy Windows pro organizaci Contoso. Rutiny, které by spuštění bude vypadat takto:

```powershell
New-RdsTenant -Name Contoso -AadTenantId 00000000-1111-2222-3333-444444444444 -AzureSubscriptionId 55555555-6666-7777-8888-999999999999
```

## <a name="next-steps"></a>Další postup

Po vytvoření vašeho tenanta, bude potřeba vytvořit instanční objekt v Azure Active Directory a přiřaďte ho roli v rámci virtuální plochy Windows. Služby, které vám umožní úspěšně nasadit Windows Virtual Desktop nabídek Azure Marketplace a vytvořit tak fond hostitele. Další informace o fondech hostitele, pokračujte ke kurzu pro vytváření hostitele fondu ve virtuální plochy Windows.

> [!div class="nextstepaction"]
> [Vytvoření objektů služby a přiřazení rolí pomocí PowerShellu](./create-service-principal-role-powershell.md)
