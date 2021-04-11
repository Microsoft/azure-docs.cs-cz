---
title: Ruční migrace z virtuálního počítače s Windows (Classic) – Azure
description: Postup ruční migrace z virtuálního počítače s Windows (Classic) na virtuální počítač s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 09/11/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 350b7306b6d39b42fbfebcc6566762df305b11b4
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106445971"
---
# <a name="migrate-manually-from-windows-virtual-desktop-classic"></a>Ruční migrace z virtuálního počítače s Windows (Classic)

Virtuální plocha Windows (Classic) vytvoří své prostředí služby s rutinami prostředí PowerShell, rozhraními REST API a objekty služby. "Objekt" ve službě virtuálních počítačů s Windows je to, co vytvoří virtuální počítač s Windows. Objekty služby zahrnují klienty, fondy hostitelů, skupiny aplikací a hostitele relací.

Virtuální plocha Windows (Classic) ale není integrovaná s Azure. Bez Integrace Azure nejsou žádné objekty, které vytváříte, automaticky spravované Azure Portal, protože nejsou připojené k vašemu předplatnému Azure.

Nedávná hlavní aktualizace Windows Virtual desktopu označí posun ve službě směrem k plné integraci Azure. Objekty, které vytvoříte ve virtuálním počítači Windows, se automaticky spravují pomocí Azure Portal.

V tomto článku se dozvíte, proč byste měli zvážit migraci na nejnovější verzi virtuálního klienta Windows. Potom vám řekneme, jak ručně migrovat z virtuálního počítače s Windows (Classic) na nejnovější aktualizaci virtuálního klienta Windows.

## <a name="why-migrate"></a>Proč migrovat?

Hlavní aktualizace mohou být nepohodlné, zejména ty, které je třeba provést ručně. Existují však některé důvody, proč nemůžete automaticky migrovat:

- Existující objekty služby vytvořené s klasickým vydáním nemají v Azure žádné reprezentace. Rozsah jejich oboru nepřekračuje služba Windows Virtual Desktop.
- S nejnovější aktualizací se změnilo ID aplikace služby, aby se odebral souhlas pro aplikace způsobem, který měl pro virtuální počítač s Windows (Classic). Nebudete moct vytvářet nové objekty Azure s virtuálním počítačem s Windows, pokud se neověřují pomocí nového ID aplikace.

Bez ohledu na nepříjemnosti je stále důležité migrovat se mimo klasickou verzi. Tady je seznam toho, co můžete udělat po migraci:

- Spravujte virtuální plochu Windows prostřednictvím Azure Portal.
- Přiřazení skupin uživatelů Azure Active Directory (AD) ke skupinám aplikací.
- Pomocí vylepšené funkce Log Analytics můžete řešit potíže s nasazením.
- Ke správě přístupu pro správu použijte Azure – nativní řízení přístupu na základě rolí (Azure RBAC).

## <a name="when-should-i-migrate"></a>Kdy bych měl migrovat?

Při dotazování, pokud byste měli provést migraci, byste měli také vzít v úvahu aktuální a budoucí situaci nasazení.

K dispozici je několik scénářů, zejména v případech, kdy doporučujeme provést ruční migraci:

- Máte nastavený testovací fond hostitelů s malým počtem uživatelů.
- Máte pracovní fond produkčního prostředí s malým počtem uživatelů, ale plánujete, abyste si mohli podobu zvýšit až na stovky uživatelů.
- Máte jednoduchou instalaci, kterou je možné snadno replikovat. Například pokud vaše virtuální počítače používají image z galerie.

> [!IMPORTANT]
> Pokud používáte pokročilou konfiguraci, která trvala dlouhou dobu při stabilizaci nebo má mnoho uživatelů, nedoporučujeme ručně migrovat.

## <a name="prepare-for-migration"></a>Příprava migrace

Než začnete, musíte se ujistit, že je vaše prostředí připravené na migraci.

V této části je potřeba zahájit proces migrace:

- Předplatné Azure, kde vytvoříte nové objekty služby Azure.
- Ujistěte se, že jste přiřazeni k následujícím rolím:
    
    - Přispěvatel
    - Správce uživatelských přístupů
    
    Role přispěvatele umožňuje vytvářet objekty Azure v rámci vašeho předplatného a role správce přístupu uživatelů umožňuje přiřadit uživatele do skupin aplikací.

## <a name="how-to-migrate-manually"></a>Ruční migrace

Teď, když jste připravili proces migrace, je čas ke skutečné migraci.

Postup ruční migrace z virtuálního počítače s Windows (Classic) na virtuální počítač s Windows:

1. Podle pokynů v části [Vytvoření fondu hostitelů pomocí Azure Portal](create-host-pools-azure-marketplace.md) vytvořte všechny objekty na nejvyšší úrovni s Azure Portal.
2. Pokud chcete přenášet virtuální počítače, které už používáte, postupujte podle pokynů v části [registrace virtuálních počítačů do fondu hostitelů virtuálních](create-host-pools-powershell.md#register-the-virtual-machines-to-the-windows-virtual-desktop-host-pool) počítačů s Windows a ručně je zaregistrujte do nového fondu hostitelů, který jste vytvořili v kroku 1.
3. Vytvořte nové skupiny aplikací RemoteApp.
4. Publikujte uživatele nebo skupiny uživatelů na novou plochu a skupiny aplikací RemoteApp.
5. Aktualizujte zásady podmíněného přístupu tak, aby umožňovaly nové objekty podle pokynů v tématu [nastavení vícefaktorového ověřování](set-up-mfa.md).

Aby nedocházelo k výpadkům, měli byste nejdřív zaregistrovat stávající hostitele relací do fondů hostitelů s Azure Resource Manager integrovaných v malých skupinách v čase. Potom pomalu přenesete uživatele do nových skupin aplikací integrovaných v Azure Resource Manager.

## <a name="next-steps"></a>Další kroky

Po dokončení migrace si přečtěte [naše kurzy](create-host-pools-azure-marketplace.md)a zjistěte, jak funguje virtuální plocha Windows. Přečtěte si o rozšířených možnostech správy v [rozšíření existujícího fondu hostitelů](expand-existing-host-pool.md) a [Přizpůsobení vlastností protokolu RDP](customize-rdp-properties.md).

Další informace o objektech služby najdete v [prostředí virtuálních počítačů s Windows](environment-setup.md).
