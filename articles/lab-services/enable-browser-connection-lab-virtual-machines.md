---
title: Povolit připojení prohlížeče na virtuálních počítačích s Azure DevTest Labs | Microsoft Docs
description: DevTest Labs se teď integruje s Azure bastionu, jako vlastník testovacího prostředí, můžete povolit přístup ke všem virtuálním počítačům testovacího prostředí prostřednictvím prohlížeče.
services: devtest-lab,virtual-machines,lab-services
documentationcenter: na
author: tanmayeekamath
manager: femila
editor: ''
ms.service: lab-services
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 1df671cf6ff71b53c5bb7ba9c23f123e56777356
ms.sourcegitcommit: 36e9cbd767b3f12d3524fadc2b50b281458122dc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/20/2019
ms.locfileid: "69642497"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Povolit připojení prohlížeče na virtuálních počítačích testovacího prostředí 

DevTest Labs se integruje s [Azure bastionu](https://docs.microsoft.com/azure/bastion/), což vám umožní připojit se k virtuálním počítačům přes prohlížeč. Nejdřív musíte povolit připojení prohlížeče na virtuálních počítačích testovacího prostředí.

Jako vlastník testovacího prostředí můžete povolit přístup ke všem virtuálním počítačům testovacího prostředí prostřednictvím prohlížeče. Nepotřebujete dalšího klienta, agenta ani software. Azure bastionu poskytuje zabezpečené a bezproblémové připojení RDP/SSH k virtuálním počítačům přímo v Azure Portal přes SSL. Když se připojíte přes Azure bastionu, virtuální počítače nepotřebují veřejnou IP adresu. Další informace najdete v tématu [co je Azure bastionu?](../bastion/bastion-overview.md)

> [!NOTE]
> Povolení připojení prohlížeče na virtuálních počítačích testovacího prostředí je ve verzi Preview.

Tento článek popisuje, jak povolit připojení prohlížeče na virtuálních počítačích testovacího prostředí.

## <a name="prerequisites"></a>Požadavky 
Buď nasaďte hostitele bastionu v existující virtuální síti testovacího prostředí **(nebo)** připojte testovací prostředí s nakonfigurovanou virtuální sítí bastionu. 

Informace o tom, jak nasadit hostitele bastionu ve virtuální síti, najdete v tématu [Vytvoření hostitele Azure bastionu (Preview)](../bastion/bastion-create-host-portal.md). Při vytváření hostitele bastionu vyberte virtuální síť testovacího prostředí. 

Informace o tom, jak připojit testovací prostředí k virtuální síti s nakonfigurovaným bastionu, najdete v tématu [Konfigurace virtuální sítě v Azure DevTest Labs](devtest-lab-configure-vnet.md). Vyberte virtuální síť, která má nasazeného hostitele bastionu, a **AzureBastionSubnet** . Tady je podrobný postup: 

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com).
1. V levé navigační nabídce vyberte **všechny služby** . 
1. Ze seznamu vyberte **DevTest Labs**. 
1. V seznamu cvičení vyberte *testovací prostředí*. 

    > [!NOTE]
    > Služba Azure bastionu je aktuálně ve verzi Preview. Je omezeno na následující oblasti: Západní USA, Východní USA, Západní Evropa, Střed USA – jih, Austrálie – východ a Japonsko – východ. Proto vytvořte testovací prostředí v jedné z těchto oblastí, pokud v testovacím prostředí není jedna z nich. 
1. V části **Nastavení** v levé nabídce vyberte **Konfigurace a zásady** . 
1. Vyberte **virtuální sítě**.
1. Na panelu nástrojů vyberte **Přidat** . 
1. Vyberte **virtuální síť** , která má nasazeného hostitele bastionu. 
1. Vyberte podsíť: **AzureBastionSubnet**. 

    ![Subnet](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Vyberte možnost **použít při vytváření virtuálního počítače** . 
1. Na panelu nástrojů vyberte **Uložit**. 
1. Pokud máte starou virtuální síť pro testovací prostředí, odeberte ji tak, že vyberete * *...*  a **odeberte**. 

## <a name="enable-browser-connection"></a>Povolit připojení prohlížeče 

Jakmile budete mít bastionu nakonfigurovanou virtuální síť v rámci testovacího prostředí, můžete jako vlastníka testovacího prostředí povolit připojení prohlížeče k virtuálním počítačům testovacího prostředí.

Pokud chcete povolit připojení prohlížeče k virtuálním počítačům testovacího prostředí, postupujte podle těchto kroků:

1. V Azure Portal přejděte do testovacího *prostředí*.
1. Vyberte **Konfigurace a zásady**.
1. V **Nastavení**vyberte **prohlížeč připojit (Preview)** .

    ![Povolit připojení prohlížeče](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Další kroky
V následujícím článku se dozvíte, jak se připojit k virtuálním počítačům pomocí prohlížeče: [Připojení k virtuálním počítačům přes prohlížeč](connect-virtual-machine-through-browser.md)
