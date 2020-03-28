---
title: Fond hostitelů Virtuálních desktopů Windows Azure Marketplace – Azure
description: Jak vytvořit fond hostitelů virtuální plochy Windows pomocí Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 03/09/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: d5165b160ffc196416052a56aaa0d93c05db56bc
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79238619"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Kurz: Vytvoření fondu hostitelů pomocí Azure Marketplace

V tomto kurzu se dozvíte, jak vytvořit fond hostitelů v rámci klienta virtuální plochy Windows pomocí nabídky Microsoft Azure Marketplace.

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích klienta Windows Virtual Desktop. Každý fond hostitelů může obsahovat skupinu aplikací, se kterou mohou uživatelé pracovat stejně jako na fyzické ploše.

Mezi úkoly v tomto kurzu patří:

> [!div class="checklist"]
>
> * Vytvořte fond hostitelů ve virtuální ploše Windows.
> * Vytvořte skupinu prostředků s virtuálními počítači v předplatném Azure.
> * Připojte virtuální servery k doméně služby Active Directory.
> * Zaregistrujte virtuální počítače pomocí virtuální plochy Windows.

## <a name="prerequisites"></a>Požadavky

* Tenant ve virtuální ploše. Předchozí [kurz](tenant-setup-azure-active-directory.md) vytvoří klienta.
* [Modul PowerShell pro virtuální plochu systému Windows](/powershell/windows-virtual-desktop/overview/).

Jakmile budete mít tento modul, spusťte následující rutinu pro přihlášení ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Spuštění nabídky Azure Marketplace pro zřízení nového fondu hostitelů

Spuštění nabídky Azure Marketplace pro zřízení nového fondu hostitelů:

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.
1. Do vyhledávacího okna Marketplace zadejte **virtuální plochu Windows.**
1. Vyberte **Windows Virtual Desktop – Zřídit fond hostitelů**a pak vyberte **Vytvořit**.

Poté podle pokynů v další části zadejte informace pro příslušné karty.

### <a name="basics"></a>Základy

Zde je to, co děláte pro kartu **Základy:**

1. Vyberte **předplatné**.
1. Ve **skupině Prostředků**vyberte vytvořit **nový** a zadejte název pro novou skupinu prostředků.
1. Vyberte **oblast**.
1. Zadejte název fondu hostitelů, který je jedinečný v tenantovi virtuální plochy Windows.
1. Vyberte **typ plochy**. Pokud vyberete **osobní**, každý uživatel, který se připojí k tomuto fondu hostitelů, bude trvale přiřazen k virtuálnímu počítači.
1. Zadejte uživatele, kteří se mohou přihlásit ke klientům Virtuální desktopwindows a získat přístup k ploše. Použijte seznam oddělený čárkami. Chcete-li například přiřadit `user1@contoso.com` `user2@contoso.com` a získat přístup, zadejte*`user1@contoso.com,user2@contoso.com`*
1. V **umístění metadat služby**vyberte stejné umístění jako virtuální síť, která je připojena k serveru Služby Active Directory.

   >[!IMPORTANT]
   >Pokud používáte čistě Azure Active Directory Domain Services (Azure AD DS) a Azure Active Directory (Azure AD) řešení, ujistěte se, že nasazení fondu hostitelů ve stejné oblasti jako vaše Azure AD DS, aby se zabránilo chybám připojení domény a pověření.

1. Vyberte **další: Konfigurace virtuálních počítačů**.

### <a name="configure-virtual-machines"></a>Konfigurace virtuálních počítačů

Na kartě **Konfigurace virtuálních počítačů:**

1. Buď přijměte výchozí hodnoty, nebo přizpůsobte počet a velikost virtuálních počítačů.

    >[!NOTE]
    >Pokud se konkrétní velikost virtuálního počítače, kterou hledáte, nezobrazuje ve voliči velikosti, je to proto, že jsme ji ještě nezadali do nástroje Azure Marketplace. Chcete-li požádat o velikost, vytvořte požadavek nebo přehlaste existující požadavek ve [fóru Windows Virtual Desktop UserVoice](https://windowsvirtualdesktop.uservoice.com/forums/921118-general).

1. Zadejte předponu pro názvy virtuálních počítačů. Pokud například zadáte *předponu*, virtuální počítače se budou nazývat **prefix-0**, **prefix-1**a tak dále.
1. Vyberte **Další: Nastavení virtuálního počítače**.

### <a name="virtual-machine-settings"></a>Nastavení virtuálního počítače

Na kartě **Nastavení virtuálního počítače:**

1. V **části Zdroj obrazu**vyberte zdroj a zadejte příslušné informace o tom, jak jej najít a jak jej uložit. Možnosti se liší pro úložiště objektů Blob, spravované image a galerie.

   Pokud se rozhodnete spravované disky nepoužívat, vyberte účet úložiště, který obsahuje soubor *VHD.*
1. Zadejte hlavní jméno uživatele a heslo. Tento účet musí být účet domény, který připojí virtuální počítače k doméně služby Active Directory. Stejné uživatelské jméno a heslo budou vytvořeny na virtuálních počítačích jako místní účet. Tyto místní účty můžete obnovit později.

   >[!NOTE]
   > Pokud připojujete virtuální počítače k prostředí Azure AD DS, ujistěte se, že váš uživatel připojení k doméně je členem [skupiny Správci řadiče domény AAD](../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group).
   >
   > Účet musí být také součástí spravované domény Azure AD DS nebo klienta Azure AD. Účty z externích adresářů přidružených k vašemu tenantovi Azure AD se během procesu připojení k doméně nemohou správně ověřit.

1. Vyberte **virtuální síť,** která je připojena k serveru Služby Active Directory, a pak zvolte podsíť pro hostování virtuálních počítačů.
1. Vyberte **další: Informace o virtuální ploše systému Windows**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informace o klientovi virtuální plochy Windows

Na kartě **Informace o klientovi virtuální plochy Windows:**

1. Pro **název skupiny klienta Virtuální plocha Windows**zadejte název skupiny klientů, která obsahuje vašeho klienta. Ponechte ji jako výchozí, pokud jste zadali konkrétní název skupiny klientů.
1. Pro **název klienta Virtuální plochy Windows**zadejte název klienta, ve kterém budete vytvářet tento fond hostitelů.
1. Zadejte typ pověření, která chcete použít k ověření jako vlastník a klient a rds virtuální plochy Windows. Zadejte hlavní název nebo instanční objekt a heslo.

   Pokud jste dokončili [vytvořit instanční objekty a přiřazení rolí pomocí kurzu PowerShellu](./create-service-principal-role-powershell.md), vyberte **Instanční objekt**.

1. Pro **instanční objekt**, pro **ID klienta Azure AD**, zadejte účet správce klienta pro instanci Azure AD, která obsahuje instanční objekt. Podporovány jsou pouze instanční objekty s pověřením hesla.
1. Vyberte **další: Kontrola + vytvoření**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Kompletní nastavení a vytvoření virtuálního počítače

V **části Revize a vytvoření**zkontrolujte informace o nastavení. Pokud potřebujete něco změnit, vraťte se a proveďte změny. Až budete připraveni, vyberte **Vytvořit** a nasaďte svůj hostitelský fond.

V závislosti na tom, kolik virtuálních počítačů vytváříte, může tento proces trvat 30 minut nebo déle.

>[!IMPORTANT]
> Chcete-li zabezpečit prostředí Virtuální desktopwindows v Azure, doporučujeme neotevírat příchozí port 3389 na virtuálních počítačích. Virtuální plocha Windows nevyžaduje otevřený vstupní port 3389 pro přístup uživatelů k virtuálním počítačům fondu hostitelů.
>
> Pokud je nutné otevřít port 3389 pro účely řešení potíží, doporučujeme použít přístup just-in-time. Další informace naleznete v [tématu Zabezpečení portů pro správu s přístupem za dobu .](../security-center/security-center-just-in-time.md)

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Nepovinné) Přiřazení dalších uživatelů ke skupině aplikací klasické pracovní plochy

Po azure marketplace dokončí vytváření fondu, můžete přiřadit více uživatelů do skupiny aplikací pro stolní počítače. Pokud nechcete přidávat další, tuto část přeskočte.

Přiřazení uživatelů ke skupině aplikací klasické pracovní plochy:

1. Otevřete okno PowerShellu.

1. Chcete-li se přihlásit do prostředí Virtuální plochy systému Windows, spusťte následující příkaz:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Přidejte uživatele do skupiny aplikací klasické pracovní plochy pomocí tohoto příkazu:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   Hlavní číslo uživatele by se mělo shodovat s identitou *user1@contoso.com*uživatele ve službě Azure AD, například . Pokud chcete přidat více uživatelů, spusťte příkaz pro každého uživatele.

Uživatelé, které přidáte do skupiny aplikací plochy, se mohou přihlásit k virtuální ploše systému Windows s podporovanými klienty vzdálené plochy a zobrazit prostředek pro plochu relace.

Zde jsou aktuální podporovaní klienti:

* [Klient vzdálené plochy pro Windows 7 a Windows 10](connect-windows-7-and-10.md)
* [Webový klient virtuální plochy windows](connect-web.md)

## <a name="next-steps"></a>Další kroky

Vytvořili jste fond hostitelů a přiřadili jste uživatelům přístup k ploše. Fond hostitelů můžete naplnit programy RemoteApp. Další informace o správě aplikací ve Windows Virtual Desktop najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Kurz Správa skupin aplikací](./manage-app-groups.md)
