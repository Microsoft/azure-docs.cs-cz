---
title: Rozbalení stávajícího fondu hostitelů s novými hostiteli relací – Azure
description: Postup rozšíření stávajícího fondu hostitelů s novými hostiteli relací na virtuálním počítači s Windows
services: virtual-desktop
author: Heidilohr
ms.service: virtual-desktop
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: helohr
manager: lizross
ms.openlocfilehash: 8a60d34c73eed5d71599a90e463138186e21fdf7
ms.sourcegitcommit: f01c2142af7e90679f4c6b60d03ea16b4abf1b97
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/10/2020
ms.locfileid: "84677895"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Rozšíření existujícího fondu hostitelů s novými hostiteli relací

>[!IMPORTANT]
>Tento obsah se vztahuje na jarní 2020 aktualizaci s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows na verzi 2019 bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md).
>
> V současnosti je ve verzi Public Preview na jaře 2020 aktualizace virtuálních počítačů s Windows. Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučujeme ji používat pro produkční úlohy. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. 
> Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

Při vyčerpání využití v rámci fondu hostitelů možná budete muset rozšířit svůj stávající fond hostitelů o nové hostitele relací, aby bylo možné nové zatížení zpracovat.

V tomto článku se dozvíte, jak můžete rozšířit existující fond hostitelů o nové hostitele relací.

## <a name="what-you-need-to-expand-the-host-pool"></a>Co potřebujete k rozšíření fondu hostitelů

Než začnete, ujistěte se, že jste vytvořili fond hostitelů a virtuální počítače hostitele relace (VM) pomocí jedné z následujících metod:

- [portál Azure](./create-host-pools-azure-marketplace.md)
- [Vytvoření fondu hostitelů pomocí PowerShellu](./create-host-pools-powershell.md)

Při prvním vytvoření fondu hostitelů a hostitele relací budete také potřebovat následující informace:

- Velikost virtuálního počítače, obrázek a předpona názvu
- Přihlašovací údaje správce domény k doméně
- Název virtuální sítě a název podsítě

## <a name="add-virtual-machines-with-the-azure-portal"></a>Přidání virtuálních počítačů pomocí Azure Portal

Rozšíření fondu hostitelů přidáním virtuálních počítačů:

1. Přihlaste se k portálu Azure.

2. Vyhledejte a vyberte **virtuální počítač s Windows**.

3. V nabídce na levé straně obrazovky vyberte **fondy hostitelů**a pak vyberte název fondu hostitelů, do kterého chcete přidat virtuální počítače.

4. V nabídce na levé straně obrazovky vyberte **hostitelé relace** .

5. Vyberte **+ Přidat** a začněte vytvářet fond hostitelů.

6. Ignorujte kartu základy a místo toho vyberte kartu **Podrobnosti o virtuálním počítači** . Tady můžete zobrazit a upravit podrobnosti o virtuálním počítači, který chcete přidat do fondu hostitelů.

7. Vyberte skupinu prostředků, ve které chcete vytvořit virtuální počítače, a pak vyberte oblast. Můžete zvolit aktuální oblast, kterou používáte, nebo novou oblast.
   
8. Zadejte počet hostitelů relací, které chcete přidat do fondu hostitelů, do **počtu virtuálních počítačů**. Pokud například rozšíříte fond hostitelů o pět hostitelů, zadejte **5**.
   
    >[!NOTE]
    >Velikost ani bitovou kopii virtuálních počítačů nemůžete upravit, protože je důležité zajistit, aby všechny virtuální počítače ve fondu hostitelů měly stejnou velikost.
    
9. Pro **informace o virtuální síti**vyberte virtuální síť a podsíť, ke kterým chcete virtuální počítače připojit. Můžete vybrat stejnou virtuální síť, kterou existující počítače používají, nebo vybrat jiný, který je vhodnější pro oblast vybranou v kroku 7.

10. Pro **účet správce**zadejte uživatelské jméno a heslo domény služby Active Directory přidružené k virtuální síti, kterou jste vybrali. Tyto přihlašovací údaje se použijí pro připojení virtuálních počítačů k virtuální síti.

      >[!NOTE]
      >Zajistěte, aby názvy správců splňovaly informace, které jsou zde uvedeny. A že na účtu není povolený žádný MFA.

11. Vyberte kartu **značka** , pokud máte jakékoli značky, pomocí kterých chcete seskupit virtuální počítače. V opačném případě tuto kartu přeskočte. 

12. Vyberte kartu **Revize + vytvořit** . Zkontrolujte volby a pokud vše vypadá dobře, vyberte **vytvořit**. 

## <a name="next-steps"></a>Další kroky

Teď, když jste rozšířili stávající fond hostitelů, se můžete přihlásit k klientovi virtuální plochy Windows a otestovat ho jako součást uživatelské relace. K relaci se můžete připojit pomocí kteréhokoli z následujících klientů:

- [Připojení s desktopovým klientem Windows](./connect-windows-7-and-10.md)
- [Připojení k webovému klientovi](./connect-web.md)
- [Práce s klientem Android](./connect-android.md)
- [Připojení s klientem macOS](./connect-macos.md)
- [Připojení s klientem iOS](./connect-ios.md)
