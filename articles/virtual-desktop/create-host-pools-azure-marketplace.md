---
title: Vytvoření fondu hostitelů virtuálních počítačů s Windows pomocí Azure Marketplace – Azure
description: Postup vytvoření fondu hostitelů virtuálních počítačů s Windows pomocí Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 08/30/2019
ms.author: helohr
ms.openlocfilehash: c0b93529872de774e1a6e915ef8254c5c0e0a1a9
ms.sourcegitcommit: 5f0f1accf4b03629fcb5a371d9355a99d54c5a7e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/30/2019
ms.locfileid: "71676715"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Kurz: Vytvoření fondu hostitelů pomocí Azure Marketplace

Fondy hostitelů jsou kolekce jednoho nebo více identických virtuálních počítačů v prostředích klienta virtuálních počítačů s Windows. Každý fond hostitelů může obsahovat skupinu aplikací, se kterou můžou uživatelé interaktivně pracovat, jako by na fyzickém počítači.

V tomto kurzu se dozvíte, jak vytvořit fond hostitelů v rámci tenanta virtuálních klientů Windows pomocí nabídky Microsoft Azure Marketplace. Mezi tyto úlohy patří:

> [!div class="checklist"]
> * Vytvořte fond hostitelů na virtuálním počítači s Windows.
> * Vytvořte skupinu prostředků s virtuálními počítači v předplatném Azure.
> * Připojte virtuální počítače k doméně služby Active Directory.
> * Zaregistrujte virtuální počítače na virtuálním počítači s Windows.

Než začnete, [Stáhněte a importujte modul PowerShellu virtuálního počítače s Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) , který chcete použít v relaci PowerShellu, pokud jste to ještě neudělali. Potom spuštěním následující rutiny se přihlaste ke svému účtu:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

## <a name="sign-in-to-azure"></a>Přihlaste se k Azure

Přihlaste se k [Azure Portal](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Spuštění nabídky Azure Marketplace pro zřízení nového fondu hostitelů

Spuštění nabídky Azure Marketplace pro zřízení nového fondu hostitelů:

1. Vyberte **+** nebo **+ vytvořit prostředek**.
2. V okně hledání na Marketplace zadejte **Windows Virtual Desktop** .
3. Vyberte **virtuální počítač s Windows – zřídit fond hostitelů**a pak vyberte **vytvořit**.

Potom postupujte podle pokynů v následující části a zadejte informace pro příslušné listy.

### <a name="basics"></a>Práce

Tady je přehled toho, co je pro okno **základy** :

1. Zadejte název fondu hostitelů, který je jedinečný v rámci tenanta virtuálních klientů Windows.
2. Vyberte vhodnou možnost pro osobní plochu. Pokud vyberete **Ano**, každý uživatel, který se připojí k tomuto fondu hostitelů, se trvale přiřadí k virtuálnímu počítači.
3. Zadejte čárkami oddělený seznam uživatelů, kteří se můžou přihlásit k klientům virtuální plochy Windows a získat přístup k ploše po dokončení nabídky Azure Marketplace. Například pokud chcete přiřadit user1@contoso.com a přístup user2@contoso.com, zadejte "user1@contoso.com, user2@contoso.com."
4. Vyberte **vytvořit nový** a zadejte název nové skupiny prostředků.
5. V poli **umístění**vyberte stejné umístění jako virtuální síť, která má připojení k serveru služby Active Directory.
6. Vyberte **OK**.

>[!IMPORTANT]
>Pokud používáte čistě Azure Active Directory Domain Services a Azure Active Directory řešení, nezapomeňte nasadit fond hostitelů ve stejné oblasti jako Azure Active Directory Domain Services, abyste se vyhnuli chybám připojení k doméně a přihlašovacím údajům.

### <a name="configure-virtual-machines"></a>Konfigurace virtuálních počítačů

Pro okno **Konfigurace virtuálních počítačů** :

1. Buď přijměte výchozí hodnoty, nebo upravte počet a velikost virtuálních počítačů.
2. Zadejte předponu názvů virtuálních počítačů. Pokud například zadáte název "prefix", virtuální počítače budou nazývány "prefix-0," prefix-1, "atd.
3. Vyberte **OK**.

### <a name="virtual-machine-settings"></a>Nastavení virtuálního počítače

Pro okno **nastavení virtuálního počítače** :

>[!NOTE]
> Pokud se připojujete k virtuálním počítačům do prostředí Azure Active Directory Domain Services (Azure služba AD DS), ujistěte se, že je uživatel připojení k doméně také členem [skupiny správců AAD řadiče domény](../active-directory-domain-services/tutorial-create-instance.md#configure-an-administrative-group).

1. V části **zdroj obrázku**vyberte zdroj a zadejte příslušné informace, jak ho najít a jak ho uložit. Pokud se rozhodnete nepoužívat spravované disky, vyberte účet úložiště, který obsahuje soubor. VHD.
2. Zadejte hlavní uživatelské jméno a heslo pro účet domény, který se připojí k virtuálním počítačům k doméně služby Active Directory. Stejné uživatelské jméno a heslo se vytvoří na virtuálních počítačích jako místní účet. Tyto místní účty můžete později obnovit.
3. Vyberte virtuální síť, která má připojení k serveru služby Active Directory, a pak zvolte podsíť, která bude hostovat virtuální počítače.
4. Vyberte **OK**.

### <a name="windows-virtual-desktop-tenant-information"></a>Informace o tenantovi virtuálních klientů Windows

V okně **informace o Tenantovi virtuálních počítačů s Windows** :

1. V poli **název skupiny tenantů virtuálních klientů Windows**zadejte název skupiny tenantů, která obsahuje vašeho tenanta. Pokud jste nezadali konkrétní název skupiny tenantů, ponechte ho jako výchozí.
2. Do pole **název tenanta virtuálních klientů Windows**zadejte název tenanta, do kterého budete tento fond hostitelů vytvářet.
3. Zadejte typ přihlašovacích údajů, které chcete použít k ověření jako vlastník klienta vzdálené plochy Windows. Pokud jste dokončili [kurz vytvoření objektů služby a přiřazení rolí pomocí PowerShellu](./create-service-principal-role-powershell.md), vyberte **instanční objekt**. Po zobrazení **ID tenanta služby Azure AD** zadejte id instance Azure Active Directory, která obsahuje instanční objekt.
4. Zadejte přihlašovací údaje pro účet správce tenanta. Podporují se jenom instanční objekty s přihlašovacími údaji hesla.
5. Vyberte **OK**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Dokončení instalace a vytvoření virtuálního počítače

Pro poslední dvě okna:

1. V okně **Souhrn** zkontrolujte informace o instalaci. Pokud potřebujete něco změnit, vraťte se do příslušného okna a před pokračováním proveďte změnu. Pokud informace vypadají vpravo, vyberte **OK**.
2. V okně **koupit** si přečtěte další informace o nákupu z Azure Marketplace.
3. Vyberte **vytvořit** a nasaďte fond hostitelů.

V závislosti na počtu virtuálních počítačů, které vytváříte, může tento proces trvat 30 minut nebo i déle.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>Volitelné Přiřazení dalších uživatelů ke skupině desktopových aplikací

Po dokončení nabídky Azure Marketplace můžete přiřadit více uživatelů do skupiny desktopové aplikace ještě před zahájením testování klientů s úplnými relacemi na virtuálních počítačích. Pokud jste již přidali výchozí uživatele do nabídky Azure Marketplace a nechcete přidat další, můžete tuto část přeskočit.

Pokud chcete přiřadit uživatele do skupiny desktopové aplikace, musíte nejdřív otevřít okno PowerShellu. Potom budete muset zadat následující dvě rutiny.

Spuštěním následující rutiny se přihlaste do prostředí virtuálních počítačů s Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Přidat uživatele do skupiny desktopových aplikací pomocí této rutiny:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Hlavní název uživatele (UPN) by měl odpovídat identitě uživatele v Azure Active Directory (například user1@contoso.com). Pokud chcete přidat více uživatelů, musíte tuto rutinu spustit pro každého uživatele.

Po dokončení těchto kroků se uživatelé přidaní do skupiny desktopových aplikací můžou přihlásit k virtuální ploše Windows pomocí podporovaných klientů vzdálené plochy a zobrazit prostředek pro plochu relace.

Tady jsou aktuálně Podporovaní klienti:

- [Klient služby Vzdálená plocha pro Windows 7 a Windows 10](connect-windows-7-and-10.md)
- [Webový klient pro virtuální počítače s Windows](connect-web.md)

>[!IMPORTANT]
>Pro lepší zabezpečení prostředí virtuálních počítačů s Windows v Azure doporučujeme na svých virtuálních počítačích neotevírat port 3389 pro příchozí spojení. Virtuální počítač s Windows nevyžaduje pro přístup k virtuálním počítačům fondu hostitelů otevřený příchozí port 3389. Pokud musíte pro účely řešení potíží otevřít port 3389, doporučujeme použít [přístup k virtuálnímu počítači za běhu](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Další kroky

Teď, když jste provedli fond hostitelů a přiřadili uživatelům přístup k jeho ploše, můžete naplnit fond hostitelů pomocí aplikací RemoteApp. Další informace o správě aplikací ve virtuálním počítači s Windows najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Kurz správy skupin aplikací](./manage-app-groups.md)
