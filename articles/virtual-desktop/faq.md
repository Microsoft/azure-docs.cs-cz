---
title: Nejčastější dotazy k virtuálním plochám Windows – Azure
description: Nejčastější dotazy a osvědčené postupy pro virtuální počítače s Windows
author: Heidilohr
ms.topic: conceptual
ms.date: 10/15/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 3bdb38b8a9590cf6191c75fdef024543c2b1c190
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101720269"
---
# <a name="windows-virtual-desktop-faq"></a>Windows Virtual Desktop – nejčastější dotazy

Tento článek obsahuje odpovědi na nejčastější dotazy a vysvětluje osvědčené postupy pro virtuální počítače s Windows.

## <a name="what-are-the-minimum-admin-permissions-i-need-to-manage-objects"></a>Jaká jsou minimální oprávnění správce potřebuji ke správě objektů?

Pokud chcete vytvořit fondy hostitelů a jiné objekty, musíte mít přiřazenou roli Přispěvatel pro předplatné nebo skupinu prostředků, se kterou právě pracujete.

Abyste mohli publikovat skupiny aplikací pro uživatele nebo skupiny uživatelů, musíte mít ve skupině aplikací přiřazenou roli správce přístupu uživatele.

Chcete-li správce omezit pouze na správu uživatelských relací, jako je například odesílání zpráv uživatelům, odhlašování uživatelů a tak dále, můžete vytvořit vlastní role. Například:

```powershell
"actions": [
"Microsoft.Resources/deployments/operations/read",
"Microsoft.Resources/tags/read",
"Microsoft.Authorization/roleAssignments/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/usersessions/*",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/read",
"Microsoft.DesktopVirtualization/hostpools/sessionhosts/write"
],
"notActions": [],
"dataActions": [],
"notDataActions": []
}
```

## <a name="does-windows-virtual-desktop-support-split-azure-active-directory-models"></a>Podporuje virtuální počítač s Windows rozdělení Azure Active Directory modelů?

Když je uživatel přiřazen ke skupině aplikací, služba provede jednoduché přiřazení role Azure. V důsledku toho musí být Azure Active Directory uživatele (AD) a služba Azure AD skupiny aplikací ve stejném umístění. Všechny objekty služby, jako jsou fondy hostitelů, skupiny aplikací a pracovní prostory, musí být ve stejné službě Azure AD jako uživatel.

Pokud synchronizujete službu Active Directory se službou Azure AD ve stejné virtuální síti (VNET), můžete vytvořit virtuální počítače v jiné službě Azure AD.

## <a name="what-are-location-restrictions"></a>Jaká jsou omezení umístění?

Všechny prostředky služby mají přiřazené umístění. Umístění fondu hostitelů určuje, ve které zeměpisné oblasti se ukládají metadata služby pro fond hostitelů. Skupina aplikací nemůže existovat bez hostitelského fondu. Pokud přidáváte aplikace do skupiny aplikací RemoteApp, budete také potřebovat hostitele relace k určení aplikací nabídky Start. U každé akce skupiny aplikací budete také potřebovat přístup k datům ve fondu hostitelů. Aby se zajistilo, že se data mezi více umístěními nepřenášejí, umístění skupiny aplikací by mělo být stejné jako fond hostitelů.

Pracovní prostory také musí být ve stejném umístění jako jejich skupiny aplikací. Vždy, když se pracovní prostor aktualizuje, spolu s ním se aktualizuje související skupina aplikací. Podobně jako u skupin aplikací vyžaduje služba, aby byly všechny pracovní prostory přidružené ke skupinám aplikací vytvořeným ve stejném umístění.

## <a name="how-do-you-expand-an-objects-properties-in-powershell"></a>Jak rozbalíte vlastnosti objektu v PowerShellu?

Když spustíte rutinu PowerShellu, zobrazí se jenom název a umístění prostředku.

Například:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg

Location Name   Type
-------- ----   ----
westus   0224hp Microsoft.DesktopVirtualization/hostpools
```

Chcete-li zobrazit všechny vlastnosti prostředku, přidejte buď `format-list` nebo `fl` na konec rutiny.

Například:

```powershell
Get-AzWvdHostPool -Name 0224hp -ResourceGroupName 0224rg |fl
```

Chcete-li zobrazit konkrétní vlastnosti, přidejte názvy specifických vlastností za `format-list` nebo `fl` .

Například:

```powershell
Get-AzWvdHostPool -Name demohp -ResourceGroupName 0414rg |fl CustomRdpProperty

CustomRdpProperty : audiocapturemode:i:0;audiomode:i:0;drivestoredirect:s:;redirectclipboard:i:1;redirectcomports:i:0;redirectprinters:i:1;redirectsmartcards:i:1;screen modeid:i:2;
```

## <a name="does-windows-virtual-desktop-support-guest-users"></a>Podporuje virtuální plocha Windows i uživatele typu Host?

Virtuální desktop Windows nepodporuje uživatelské účty hosta Azure AD. Řekněme například, že skupina uživatelů typu host má Microsoft 365 E3 pro uživatele, Windows E3 pro jednotlivé uživatele nebo licence WIN VDA ve vlastní společnosti, ale uživatelé typu Host v Azure AD jiné společnosti. Druhá společnost bude spravovat uživatelské objekty uživatelů hosta v Azure AD i ve službě Active Directory, jako jsou místní účty.

Vlastní licence nemůžete použít pro zvýhodnění třetí strany. Virtuální desktop Windows taky v současnosti nepodporuje účet Microsoft (MSA).

## <a name="why-dont-i-see-the-client-ip-address-in-the-wvdconnections-table"></a>Proč se mi nezobrazuje IP adresa klienta v tabulce WVDConnections?

V současné době není spolehlivý způsob, jak shromažďovat IP adresy webového klienta, takže tuto hodnotu do tabulky nezahrneme.

## <a name="how-does-windows-virtual-desktop-handle-backups"></a>Jak virtuální plocha Windows zpracovává zálohy?

V Azure je k dispozici několik možností pro zpracování zálohování. Můžete použít zálohování, Site Recovery a snímky Azure.

## <a name="does-windows-virtual-desktop-support-third-party-collaboration-apps"></a>Podporuje virtuální plocha Windows i aplikace pro spolupráci třetích stran?

Virtuální plocha Windows je teď optimalizovaná pro týmy. Microsoft v současné době nepodporuje aplikace pro spolupráci třetích stran, jako je přiblížení. Organizace třetích stran zodpovídají za poskytování pravidel kompatibility svým zákazníkům. Virtuální počítač s Windows taky nepodporuje Skype pro firmy.

## <a name="can-i-change-from-pooled-to-personal-host-pools"></a>Můžu změnit z fondu na fondy osobních hostitelů?

Po vytvoření fondu hostitelů nemůžete změnit jeho typ. Můžete ale přesunout všechny virtuální počítače, které zaregistrujete do fondu hostitelů, na jiný typ fondu hostitelů.

## <a name="whats-the-largest-profile-size-fslogix-can-handle"></a>Co je největší velikost profilu FSLogix může zvládnout?

Omezení nebo kvóty v FSLogix závisí na prostředcích infrastruktury úložiště, které se používají k ukládání souborů VHD (X) profilů uživatele.

Následující tabulka obsahuje příklad toho, jak všechny prostředky, které profil FSLogix potřebuje k podpoře jednotlivých uživatelů. Požadavky se můžou výrazně lišit v závislosti na uživateli, aplikacích a aktivitě na jednotlivých profilech.

| Prostředek | Požadavek |
|---|---|
| Ustálený stav IOPS | 10 |
| Přihlášení nebo odhlášení IOPS | 50 |

Příkladem v této tabulce je jeden uživatel, ale můžete ho použít k odhadu požadavků na celkový počet uživatelů ve vašem prostředí. Například budete potřebovat přibližně 1 000 IOPS pro uživatele 100 a kolem 5 000 IOPS během přihlašování a odhlašování.

## <a name="is-there-a-scale-limit-for-host-pools-created-in-the-azure-portal"></a>Existuje limit škálování pro fondy hostitelů vytvořené v Azure Portal?

Tyto faktory mohou ovlivnit omezení škálování pro fondy hostitelů:

- Šablona Azure je omezená na 800 objektů. Další informace najdete v tématu [limity, kvóty a omezení předplatného a služeb Azure](../azure-resource-manager/management/azure-subscription-service-limits.md#template-limits). Každý virtuální počítač také vytváří asi šest objektů, což znamená, že při každém spuštění šablony můžete vytvořit okolo 132 virtuálních počítačů.

- Existují omezení počtu jader, které můžete vytvořit pro jednotlivé oblasti a pro každé předplatné. Pokud máte například předplatné smlouva Enterprise, můžete vytvořit 350 jader. Abyste zjistili, kolik virtuálních počítačů můžete vytvořit při každém spuštění šablony, budete muset rozdělit 350 buď na výchozí počet jader na virtuální počítač, nebo na vlastní limit. Další informace najdete na [Virtual Machines omezení – Azure Resource Manager](../azure-resource-manager/management/azure-subscription-service-limits.md#virtual-machines-limits---azure-resource-manager).

- Název předpony virtuálního počítače a počet virtuálních počítačů je kratší než 15 znaků. Další informace najdete v tématu [pravidla a omezení pro pojmenování prostředků Azure](../azure-resource-manager/management/resource-name-rules.md#microsoftcompute).

## <a name="can-i-manage-windows-virtual-desktop-environments-with-azure-lighthouse"></a>Můžu spravovat prostředí virtuálních počítačů s Windows pomocí Azure Lighthouse?

Azure Lighthouse plně nepodporuje správu prostředí virtuálních ploch Windows. Vzhledem k tomu, že Lighthouse v současné době nepodporuje správu uživatelů klienta Azure AD, zákazníci se pořád musí přihlašovat ke službě Azure AD, kterou zákazníci používají ke správě uživatelů.

Nemůžete použít také předplatná izolovaného prostoru (sandbox) s virtuálními počítači s Windows. Další informace najdete v tématu [integrace účtu izolovaného prostoru (sandbox)](/partner-center/develop/set-up-api-access-in-partner-center#integration-sandbox-account).

Nakonec, pokud jste povolili poskytovatele prostředků z účtu vlastníka CSP, účty zákazníků CSP nebudou moct měnit poskytovatele prostředků.

## <a name="how-often-should-i-turn-my-vms-on-to-prevent-registration-issues"></a>Jak často mám svoje virtuální počítače zapnout, aby se zabránilo problémům s registrací?

Po registraci virtuálního počítače do fondu hostitelů v rámci služby Virtual Desktop systému Windows Agent pravidelně aktualizuje token virtuálního počítače vždy, když je virtuální počítač aktivní. Certifikát pro registrační token je platný po dobu 90 dnů. Z důvodu tohoto limitu 90 doporučujeme spustit virtuální počítače každých 90 dnů. Zapnutím virtuálního počítače v rámci tohoto časového limitu zabráníte jeho registračnímu tokenu v vypršení platnosti nebo se stane neplatným. Pokud jste virtuální počítač spustili po 90 dnech a dochází k problémům s registrací, postupujte podle pokynů v [Průvodci odstraňováním potíží s agentem virtuálního počítače s Windows](troubleshoot-agent.md#your-issue-isnt-listed-here-or-wasnt-resolved) a odeberte virtuální počítač z fondu hostitelů, přeinstalujte agenta a znovu ho zaregistrujte do fondu.
