---
title: Rozbalení stávajícího fondu hostitelů s novými hostiteli relací – Azure
description: Postup rozšíření stávajícího fondu hostitelů s novými hostiteli relací na virtuálním počítači s Windows
author: Heidilohr
ms.topic: how-to
ms.date: 10/09/2020
ms.author: helohr
manager: femila
ms.openlocfilehash: 9c73d7434a002a5efc7d058095eb9743a7f3ebf8
ms.sourcegitcommit: 56b0c7923d67f96da21653b4bb37d943c36a81d6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/06/2021
ms.locfileid: "106446821"
---
# <a name="expand-an-existing-host-pool-with-new-session-hosts"></a>Rozšíření existujícího fondu hostitelů s novými hostiteli relací

>[!IMPORTANT]
>Tento obsah se vztahuje na virtuální plochu Windows s Azure Resource Manager objekty virtuálních klientů Windows. Pokud používáte virtuální plochu Windows (Classic) bez Azure Resource Manager objektů, přečtěte si [Tento článek](./virtual-desktop-fall-2019/expand-existing-host-pool-2019.md).

Při vyčerpání využití v rámci fondu hostitelů možná budete muset rozšířit svůj stávající fond hostitelů o nové hostitele relací, aby bylo možné nové zatížení zpracovat.

V tomto článku se dozvíte, jak můžete rozšířit existující fond hostitelů o nové hostitele relací.

## <a name="what-you-need-to-expand-the-host-pool"></a>Co potřebujete k rozšíření fondu hostitelů

Než začnete, ujistěte se, že jste vytvořili fond hostitelů a virtuální počítače hostitele relace (VM) pomocí jedné z následujících metod:

- [Azure Portal](./create-host-pools-azure-marketplace.md)
- [Vytvoření fondu hostitelů pomocí PowerShellu](./create-host-pools-powershell.md)

Při prvním vytvoření fondu hostitelů a hostitele relací budete také potřebovat následující informace:

- Velikost virtuálního počítače, obrázek a předpona názvu
- Přihlašovací údaje správce domény k doméně
- Název virtuální sítě a název podsítě

## <a name="add-virtual-machines-with-the-azure-portal"></a>Přidání virtuálních počítačů pomocí Azure Portal

Rozšíření fondu hostitelů přidáním virtuálních počítačů:

1. Přihlaste se k webu Azure Portal.

2. Vyhledejte a vyberte **virtuální počítač s Windows**.

3. V nabídce na levé straně obrazovky vyberte **fondy hostitelů** a pak vyberte název fondu hostitelů, do kterého chcete přidat virtuální počítače.

4. V nabídce na levé straně obrazovky vyberte **hostitelé relace** .

5. Vyberte **+ Přidat** a začněte vytvářet fond hostitelů.

6. Ignorujte kartu základy a místo toho vyberte kartu **Podrobnosti o virtuálním počítači** . Tady můžete zobrazit a upravit podrobnosti o virtuálním počítači, který chcete přidat do fondu hostitelů.

7. Vyberte skupinu prostředků, ve které chcete vytvořit virtuální počítače, a pak vyberte oblast. Můžete zvolit aktuální oblast, kterou používáte, nebo novou oblast.

8. Zadejte počet hostitelů relací, které chcete přidat do fondu hostitelů, do **počtu virtuálních počítačů**. Pokud například rozšíříte fond hostitelů o pět hostitelů, zadejte **5**.

    >[!NOTE]
    >I když je možné upravit image a prefix virtuálních počítačů, nedoporučujeme je upravovat, pokud máte virtuální počítače s různými imagemi ve stejném fondu hostitelů. Upravte image a předponu jenom v případě, že plánujete odebrání virtuálních počítačů se staršími imagí z ovlivněného fondu hostitelů.

9. Pro **informace o virtuální síti** vyberte virtuální síť a podsíť, ke kterým chcete virtuální počítače připojit. Můžete vybrat stejnou virtuální síť, kterou existující počítače používají, nebo vybrat jiný, který je vhodnější pro oblast vybranou v kroku 7.

10. Pro **účet správce** zadejte uživatelské jméno a heslo domény služby Active Directory přidružené k virtuální síti, kterou jste vybrali. Tyto přihlašovací údaje se použijí pro připojení virtuálních počítačů k virtuální síti.

      >[!NOTE]
      >Zajistěte, aby názvy správců splňovaly informace, které jsou zde uvedeny. A že na účtu není povolený žádný MFA.

11. Vyberte kartu **značka** , pokud máte jakékoli značky, pomocí kterých chcete seskupit virtuální počítače. V opačném případě tuto kartu přeskočte.

12. Vyberte kartu **Revize + vytvořit** . Zkontrolujte volby a pokud vše vypadá dobře, vyberte **vytvořit**.

## <a name="next-steps"></a>Další kroky

Teď, když jste rozšířili stávající fond hostitelů, se můžete přihlásit k klientovi virtuální plochy Windows a otestovat ho jako součást uživatelské relace. K relaci se můžete připojit pomocí kteréhokoli z následujících klientů:

- [Připojení s desktopovým klientem Windows](./connect-windows-7-10.md)
- [Připojení k webovému klientovi](./connect-web.md)
- [Práce s klientem Android](./connect-android.md)
- [Připojení s klientem macOS](./connect-macos.md)
- [Připojení s klientem iOS](./connect-ios.md)
