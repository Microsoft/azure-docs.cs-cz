---
title: Azure Marketplace fondu hostitelů Windows Virtual Desktop (Classic) – Azure
description: Postup vytvoření fondu hostitelů pro virtuální počítače s Windows (Classic) pomocí Azure Marketplace.
author: Heidilohr
ms.topic: how-to
ms.date: 03/31/2021
ms.author: helohr
manager: femila
ms.openlocfilehash: fa271ba87f68d46452633d2b426c7fd805f8566b
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445155"
---
# <a name="tutorial-create-a-host-pool-in-windows-virtual-desktop-classic"></a>Kurz: Vytvoření fondu hostitelů ve virtuální ploše Windows (Classic)

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows (Classic), která nepodporuje Azure Resource Manager objektů virtuálních klientů Windows. Pokud se snažíte spravovat Azure Resource Manager objektů virtuálních klientů Windows, přečtěte si [Tento článek](../create-host-pools-azure-marketplace.md).

V tomto kurzu se naučíte, jak vytvořit fond hostitelů v rámci tenanta virtuálních klientů Windows pomocí nabídky Microsoft Azure Marketplace.

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích klienta virtuálních počítačů s Windows. Každý fond hostitelů může obsahovat skupinu aplikací, se kterou můžou uživatelé interaktivně pracovat, jako by na fyzickém počítači.

Úkoly v tomto kurzu zahrnují:

> [!div class="checklist"]
>
> * Vytvořte fond hostitelů na virtuálním počítači s Windows.
> * Vytvořte skupinu prostředků s virtuálními počítači v předplatném Azure.
> * Připojte virtuální počítače k doméně služby Active Directory.
> * Zaregistrujte virtuální počítače na virtuálním počítači s Windows.

## <a name="prerequisites"></a>Požadavky

* Tenant na virtuálním počítači. Předchozí [kurz](tenant-setup-azure-active-directory.md) vytvoří tenanta.
* [Modul PowerShell pro virtuální počítače s Windows](/powershell/windows-virtual-desktop/overview/)

Jakmile budete mít tento modul, spusťte následující rutinu, abyste se přihlásili ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na [Azure Portal](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Spuštění nabídky Azure Marketplace pro zřízení nového fondu hostitelů

Spuštění nabídky Azure Marketplace pro zřízení nového fondu hostitelů:

1. V nabídce webu Azure Portal nebo na **domovské stránce** vyberte **Vytvořit prostředek**.
1. V okně hledání na Marketplace zadejte **Windows Virtual Desktop** .
1. Vyberte **virtuální počítač s Windows – zřídit fond hostitelů** a pak vyberte **vytvořit**.

Potom postupujte podle pokynů v následující části a zadejte informace o příslušných kartách.

### <a name="basics"></a>Základy

Tady je seznam toho, co uděláte na kartě **základy** :

1. Vyberte **předplatné**.
1. V poli **Skupina prostředků** vyberte **vytvořit novou** a zadejte název nové skupiny prostředků.
1. Vyberte **oblast**.
1. Zadejte název fondu hostitelů, který je jedinečný v rámci tenanta virtuálních klientů Windows.
1. Vyberte **typ pracovní plochy**. Pokud vyberete **osobní**, každý uživatel, který se připojí k tomuto fondu hostitelů, se trvale přiřadí k virtuálnímu počítači.
1. Zadejte uživatele, kteří se mohou přihlásit k klientům virtuálních klientů Windows a přistupovat k ploše. Použijte seznam oddělený čárkami. Například pokud chcete přiřadit `user1@contoso.com` a `user2@contoso.com` přístup, zadejte *`user1@contoso.com,user2@contoso.com`*
1. V poli **umístění metadat služby** vyberte stejné umístění jako virtuální síť, která má připojení k serveru služby Active Directory.

   >[!IMPORTANT]
   >Pokud používáte řešení Pure Azure Active Directory Domain Services (Azure služba AD DS) a Azure Active Directory (Azure AD), nezapomeňte nasadit fond hostitelů ve stejné oblasti jako Azure služba AD DS, aby se předešlo chybám připojení k doméně a přihlašovacím údajům.

1. Vyberte **Další: Konfigurace virtuálních počítačů**.

### <a name="configure-virtual-machines"></a>Konfigurace virtuálních počítačů

Karta **Konfigurace virtuálních počítačů** :

1. Buď přijměte výchozí hodnoty, nebo upravte počet a velikost virtuálních počítačů.

    >[!NOTE]
    >Pokud se v selektoru velikosti nezobrazí konkrétní velikost virtuálního počítače, kterou hledáte, je to proto, že jsme ho ještě nepřipojili k nástroji Azure Marketplace.

2. Zadejte předponu názvů virtuálních počítačů. Pokud například zadáte *předponu*, virtuální počítače se budou jmenovat **prefix-0**, **prefix-1** atd.
3. Vyberte **Další: nastavení virtuálního počítače**.

### <a name="virtual-machine-settings"></a>Nastavení virtuálního počítače

Pro kartu **nastavení virtuálního počítače** :

1. V části **zdroj obrázku** vyberte zdroj a zadejte příslušné informace, jak ho najít a jak ho uložit. Vaše možnosti se liší pro úložiště objektů blob, spravovanou image a galerii.

   Pokud se rozhodnete nepoužívat spravované disky, vyberte účet úložiště, který obsahuje soubor *. VHD* .
1. Zadejte hlavní název uživatele a heslo. Tento účet musí být doménový účet, který se připojí k virtuálním počítačům k doméně služby Active Directory. Stejné uživatelské jméno a heslo se vytvoří na virtuálních počítačích jako místní účet. Tyto místní účty můžete později obnovit.

   >[!NOTE]
   > Pokud se k virtuálním počítačům připojujete do prostředí Azure služba AD DS, ujistěte se, že je váš uživatel připojení k doméně členem [skupiny Správci AAD řadiče](../../active-directory-domain-services/tutorial-create-instance-advanced.md#configure-an-administrative-group)domény.
   >
   > Účet musí být také součástí spravované domény Azure služba AD DS nebo tenanta Azure AD. Účty z externích adresářů přidružených k vašemu tenantovi Azure AD se během procesu připojení k doméně nedají správně ověřit.

1. Vyberte **virtuální síť** , která má připojení k serveru služby Active Directory, a pak zvolte podsíť, která bude hostovat virtuální počítače.
1. Vyberte **Další: informace o virtuálním počítači s Windows**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informace o tenantovi virtuálních klientů Windows

Karta **informace o Tenantovi virtuálních klientů Windows** :

1. V poli **název skupiny tenantů virtuálních klientů Windows** zadejte název skupiny tenantů, která obsahuje vašeho tenanta. Pokud jste nezadali konkrétní název skupiny tenantů, ponechte ho jako výchozí.
1. Do pole **název tenanta virtuálních klientů Windows** zadejte název tenanta, do kterého budete tento fond hostitelů vytvářet.
1. Zadejte typ přihlašovacích údajů, které chcete použít k ověření jako vlastník klienta vzdálené plochy Windows. Zadejte hlavní název uživatele nebo instanční objekt a heslo.

   Pokud jste dokončili [kurz vytvoření objektů služby a přiřazení rolí pomocí PowerShellu](create-service-principal-role-powershell.md), vyberte **instanční objekt**.

1. Pro instanční **objekt** pro **ID tenanta Azure AD** zadejte účet správce tenanta pro instanci služby Azure AD, která obsahuje instanční objekt. Podporují se jenom instanční objekty s přihlašovacími údaji hesla.
1. Vyberte **Další: Zkontrolovat a vytvořit**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Dokončení instalace a vytvoření virtuálního počítače

V části **zkontrolovat a vytvořit** zkontrolujte informace o instalaci. Pokud potřebujete něco změnit, vraťte se zpátky a proveďte změny. Až budete připraveni, vyberte **vytvořit** a nasaďte fond hostitelů.

V závislosti na počtu virtuálních počítačů, které vytváříte, může tento proces trvat 30 minut nebo i déle.

>[!IMPORTANT]
> Pro lepší zabezpečení prostředí virtuálních počítačů s Windows v Azure doporučujeme na virtuálních počítačích neotevírat port 3389. Virtuální počítač s Windows nevyžaduje pro přístup uživatelů k virtuálním počítačům fondu hostitelů otevřený příchozí port 3389.
>
> Pokud musíte pro účely odstraňování potíží otevřít port 3389, doporučujeme použít přístup za běhu. Další informace najdete v tématu [zabezpečení portů pro správu pomocí přístupu za běhu](../../security-center/security-center-just-in-time.md).

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Volitelné Přiřazení dalších uživatelů ke skupině desktopových aplikací

Až Azure Marketplace dokončí vytváření fondu, můžete přiřadit více uživatelů do skupiny desktopových aplikací. Pokud nechcete přidat další, přeskočte tuto část.

Přiřazení uživatelů ke skupině desktopové aplikace:

1. Otevřete okno PowerShellu.

1. Spusťte následující příkaz, abyste se přihlásili do prostředí virtuálních počítačů s Windows:

   ```powershell
   Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
   ```

1. Přidejte uživatele do skupiny desktopových aplikací pomocí tohoto příkazu:

   ```powershell
   Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
   ```

   Hlavní název uživatele (UPN) by měl odpovídat identitě uživatele ve službě Azure AD, například *user1@contoso.com* . Pokud chcete přidat více uživatelů, spusťte příkaz pro každého uživatele.

Uživatelé, které přidáte do skupiny aplikací pracovní plochy, se můžou přihlásit k virtuální ploše Windows pomocí podporovaných klientů vzdálené plochy a zobrazit prostředek pro plochu relace.

Tady jsou aktuálně Podporovaní klienti:

* [Klient služby Vzdálená plocha pro Windows 7 a Windows 10](connect-windows-7-10-2019.md)
* [Webový klient pro virtuální počítače s Windows](connect-web-2019.md)

## <a name="next-steps"></a>Další kroky

Vytvořili jste fond hostitelů a přiřadili uživatelům přístup k jeho ploše. Fond hostitelů můžete naplnit pomocí aplikací RemoteApp. Další informace o správě aplikací ve virtuálním počítači s Windows najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Kurz správy skupin aplikací](manage-app-groups-2019.md)
