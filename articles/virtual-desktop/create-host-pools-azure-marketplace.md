---
title: Vytvoření fondu hostitele Preview virtuální plochy Windows pomocí webu Azure Marketplace – Azure
description: Postup vytvoření fondu hostitele Preview virtuální plochy Windows pomocí webu Azure Marketplace.
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: tutorial
ms.date: 04/05/2019
ms.author: helohr
ms.openlocfilehash: f692303140db1441aa34aacef62523d7f596dba1
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204738"
---
# <a name="tutorial-create-a-host-pool-by-using-the-azure-marketplace"></a>Kurz: Vytvoření fondu hostitele pomocí webu Azure Marketplace

Hostitel fondy jsou kolekce jednoho nebo víc stejných virtuálních počítačů v prostředí klienta Windows virtuální plochy, ve verzi Preview. Každý hostitel fond může obsahovat skupinu aplikací, které mohou uživatelé komunikovat s stejně jako ve fyzických stolním počítači.

Tento kurz popisuje, jak vytvořit fond hostitele v rámci tenanta virtuální plochy Windows pomocí Microsoft Azure Marketplace nabídku. Mezi úlohy patří:

> [!div class="checklist"]
> * Vytvoření hostitelů fondu ve virtuální plochy Windows.
> * Vytvořte skupinu prostředků s virtuálními počítači v rámci předplatného Azure.
> * Připojte virtuální počítače k doméně služby Active Directory.
> * Registrace virtuálních počítačů pomocí virtuální plochy Windows.

Než začnete, [stáhněte a naimportujte modul Powershellu virtuální plochy Windows](https://docs.microsoft.com/powershell/windows-virtual-desktop/overview) použít v relaci Powershellu, pokud jste tak již neučinili.

## <a name="sign-in-to-azure"></a>Přihlásit se k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="run-the-azure-marketplace-offering-to-provision-a-new-host-pool"></a>Spustit na webu Azure Marketplace nabízí ke zřízení nového fondu hostitele

Pokud chcete spustit na webu Azure Marketplace nabízí ke zřízení nového fondu hostitele:

1. Vyberte **+** nebo **+ vytvořit prostředek**.
2. Zadejte **virtuální plochy Windows** v okně hledání Marketplace.
3. Vyberte **virtuální plochy Windows – zřízení fondu hostitele**a pak vyberte **vytvořit**.

Postupujte podle pokynů k zadání informací pro příslušné oken.

### <a name="basics"></a>Základy

Zde je, co můžete udělat pro **Základy** okno:

1. Zadejte název fondu hostitele, který je jedinečný v rámci tenanta virtuální plochy Windows.
2. Vyberte příslušnou možnost pro osobní počítač. Pokud vyberete **Ano**, každý uživatel, který se připojuje k tomuto fondu hostitele bude trvale přiřazené k virtuálnímu počítači.
3. Zadejte čárkou oddělený seznam uživatelů, kteří můžou přihlásit k klienty virtuální plochy Windows a přístup k ploše po na webu Azure Marketplace nabízí dokončí. Například, pokud chcete přiřadit user1@contoso.com a user2@contoso.com přístup, zadejte "user1@contoso.com,user2@contoso.com."
4. Vyberte **vytvořit nový** a zadejte název pro novou skupinu prostředků.
5. Pro **umístění**, vybrat stejné umístění jako virtuální síť, která má připojení k serveru služby Active Directory.
6. Vyberte **OK**.

### <a name="configure-virtual-machines"></a>Konfigurace virtuálních počítačů

Pro **konfigurace virtuálních počítačů** okno:

1. Buď přijměte výchozí hodnoty nebo přizpůsobit počet a velikost virtuálních počítačů.
2. Zadejte předponu pro názvy virtuálních počítačů. Například pokud zadáte název "předponu", virtuálních počítačů bude volat "předponu-0," "předponu-1" a tak dále.
3. Vyberte **OK**.

### <a name="virtual-machine-settings"></a>Nastavení virtuálního počítače

Pro **nastavení virtuálního počítače** okno:

>[!NOTE]
> Pokud jste se zapojili vaše virtuální počítače do prostředí Azure Active Directory Domain Services (Azure AD DS), ujistěte se, že vaše doména uživatel připojit také členem skupiny [skupiny Správci AAD DC](https://docs.microsoft.com/azure/active-directory-domain-services/active-directory-ds-getting-started-admingroup#task-3-configure-administrative-group).

1. Pro **zdroj obrázku**, vyberte zdroj a zadejte příslušné informace pro vás bude a uložte ho. Pokud jste vybrali možnost použít spravované disky, vyberte účet úložiště, který obsahuje soubor .vhd.
2. Zadejte hlavní název uživatele a heslo pro účet domény, ke které se připojí virtuální počítače k doméně služby Active Directory. Toto uživatelské jméno a heslo bude vytvořena na virtuálních počítačích jako místní účet. Tyto místní účty můžete později obnovit.
3. Vyberte virtuální síť, která má připojení k serveru služby Active Directory a pak zvolte podsíť k hostování virtuálních počítačů.
4. Vyberte **OK**.

### <a name="windows-virtual-desktop-preview-tenant-information"></a>Informace o tenantovi Preview virtuální plochy Windows

Pro **informace o tenantovi virtuální plochy Windows** okno:

1. Pro **název skupiny tenanta virtuální plochy Windows**, zadejte název pro skupinu tenanta, který obsahuje váš tenant. Ponechte jako výchozí, pokud byly poskytnuty konkrétní název skupiny pro tenanta.
2. Pro **název tenanta virtuální plochy Windows**, zadejte název tenanta, pokud vytvoříte tento fond hostitele.
3. Určení typu přihlašovacích údajů, které chcete použít k ověření jako tenant Windows virtuálního klienta vzdálené plochy vlastníka. Pokud jste dokončili [vytvoření instančních objektů a přiřazení role pomocí prostředí PowerShell kurzu](./create-service-principal-role-powershell.md)vyberte **instanční objekt služby**. Když **ID tenanta Azure AD** se zobrazí, zadejte ID pro instanci služby Azure Active Directory, která obsahuje objekt služby.
4. Zadejte přihlašovací údaje pro účet správce tenanta. Podporovány jsou pouze instanční objekty s přihlašovacími údaji heslo.
5. Vyberte **OK**.

## <a name="complete-setup-and-create-the-virtual-machine"></a>Dokončete instalaci a vytvoření virtuálního počítače

Pro poslední dvě okna:

1. Na **Souhrn** okno, projděte si informace o nastavení. Pokud potřebujete něco změnit, přejděte zpět na odpovídající okna a provedení změny před pokračováním. Pokud informace vypadá dobře, vyberte **OK**.
2. Na **koupit** okno, přečtěte si další informace o nákupu z webu Azure Marketplace.
3. Vyberte **vytvořit** k nasazení hostitele fondu.

V závislosti na tom, kolik virtuálních počítačů vytváříte tento proces může trvat 30 minut nebo déle.

## <a name="optional-assign-additional-users-to-the-desktop-application-group"></a>(Volitelné) Přiřadit dalším uživatelům ke skupině pro aplikace klasické pracovní plochy

Po dokončení nabídky na Azure Marketplace můžete přiřadit více uživatelů na skupinu aplikací klasické pracovní plochy před začátkem testování celé relace plochy na virtuálních počítačích. Pokud jste už přidali uživatele výchozí v rámci nabídky Azure Marketplace a nechcete ho přidat více, můžete tuto část přeskočit.

Pokud chcete přiřadit ke skupině pro aplikace klasické pracovní plochy uživatele, musíte nejdřív otevřete okno Powershellu. Potom budete muset zadat následující dvě rutiny.

Spuštěním následující rutiny pro přihlášení k prostředí virtuálního klienta Windows:

```powershell
Add-RdsAccount -DeploymentUrl "https://rdbroker.wvd.microsoft.com"
```

Přidáte uživatele do skupiny aplikace klasické pracovní plochy pomocí této rutiny:

```powershell
Add-RdsAppGroupUser <tenantname> <hostpoolname> "Desktop Application Group" -UserPrincipalName <userupn>
```

Hlavním názvem uživatele by měl odpovídat identitu uživatele ve službě Azure Active Directory (například user1@contoso.com). Pokud chcete přidat více uživatelů, musíte tuto rutinu spustíte pro každého uživatele.

Po dokončení těchto kroků můžete uživatelé přidaní do skupiny aplikací klasické pracovní plochy přihlášení k virtuálnímu klientovi Windows pomocí podporované klienty vzdálené plochy a zobrazit zdroj pro relaci plochy.

Tady jsou aktuální Podporovaní klienti:

- [Klient služby Vzdálená plocha pro Windows 7 a Windows 10](connect-windows-7-and-10.md)
- [Virtuální Desktop Windows webového klienta](connect-web.md)

>[!IMPORTANT]
>Pomáhají zabezpečit vaše prostředí virtuálního klienta Windows v Azure, doporučujeme, abyste že na virtuálních počítačích není otevřít příchozí port 3389. Virtuální Desktop Windows nevyžaduje otevřít příchozí port 3389 pro uživatele pro přístup k fondu hostitele virtuálních počítačů. Pokud musíte otevřít port 3389 pro účely odstraňování potíží, doporučujeme vám použít [přístup k virtuálním počítačům just-in-time](https://docs.microsoft.com/azure/security-center/security-center-just-in-time).

## <a name="next-steps"></a>Další postup

Teď, když jste provedli hostitele fondu a přiřazení uživatelé pro přístup k jeho ploše, která můžete naplnit váš fond hostitele s aplikace RemoteApp. Další informace o tom, jak spravovat aplikace v virtuální plochy Windows, najdete v tomto kurzu:

> [!div class="nextstepaction"]
> [Správa kurz skupiny aplikací](./manage-app-groups.md)
