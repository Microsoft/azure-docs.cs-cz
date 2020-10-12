---
title: Povolit připojení prohlížeče na Azure DevTest Labs virtuálních počítačích
description: DevTest Labs se teď integruje s Azure bastionu, jako vlastník testovacího prostředí, můžete povolit přístup ke všem virtuálním počítačům testovacího prostředí prostřednictvím prohlížeče.
ms.topic: article
ms.date: 06/26/2020
ms.openlocfilehash: 28a3c11f3df578265f9746a173fcb3029a132b26
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88870489"
---
# <a name="enable-browser-connection-on-azure-devtest-labs-virtual-machines"></a>Povolit připojení prohlížeče na Azure DevTest Labs virtuálních počítačích 
DevTest Labs se integruje s [Azure bastionu](../bastion/index.yml), což vám umožní připojit se k virtuálním počítačům přes prohlížeč. Nejdřív musíte povolit připojení prohlížeče na virtuálních počítačích testovacího prostředí.

Jako vlastník testovacího prostředí můžete povolit přístup ke všem virtuálním počítačům testovacího prostředí prostřednictvím prohlížeče. Nepotřebujete dalšího klienta, agenta ani software. Azure bastionu poskytuje zabezpečené a bezproblémové připojení RDP/SSH k virtuálním počítačům přímo v Azure Portal přes TLS. Když se připojíte přes Azure bastionu, virtuální počítače nepotřebují veřejnou IP adresu. Další informace najdete v tématu [co je Azure bastionu?](../bastion/bastion-overview.md)


Tento článek popisuje, jak povolit připojení prohlížeče na virtuálních počítačích testovacího prostředí.

## <a name="prerequisites"></a>Požadavky 
- Buď nasaďte hostitele bastionu v existující virtuální síti testovacího prostředí **(nebo)** připojte testovací prostředí k bastionu nakonfigurované virtuální síti.
Informace o tom, jak nasadit hostitele bastionu ve virtuální síti, najdete v tématu  [Vytvoření hostitele Azure bastionu](../bastion/bastion-create-host-portal.md). Při vytváření hostitele bastionu vyberte virtuální síť testovacího prostředí. 
- Uživatel testovacího prostředí musí mít roli **Čtenář** na hostiteli bastionu a virtuální síti s nakonfigurovaným bastionu. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Vytvoření druhé dílčí sítě ve virtuální síti bastionu
Nejprve je třeba vytvořit druhou podsíť ve virtuální síti bastionu, protože AzureBastionSubnet nepovoluje vytváření prostředků, které nejsou bastionu. Vytvořte další podsíť ve virtuální síti bastionu, jak je znázorněno na následujícím obrázku:

![Druhá podsíť ve službě Azure bastionu Virtual Network](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Povolit vytvoření virtuálního počítače v podsíti
Nyní povolte vytváření virtuálních počítačů v této podsíti pomocí následujících kroků: 

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. V levé navigační nabídce vyberte **všechny služby** . 
1. Ze seznamu vyberte **DevTest Labs**. 
1. V seznamu cvičení vyberte *testovací prostředí*. 

    > [!NOTE]
    > Azure bastionu je teď všeobecně dostupný v následujících oblastech: Západní USA, Východní USA, Západní Evropa, Střed USA – jih, Austrálie – východ a Japonsko – východ. Proto vytvořte testovací prostředí v jedné z těchto oblastí, pokud v testovacím prostředí není jedna z nich. 
    
1. V části **Nastavení** v levé nabídce vyberte **Konfigurace a zásady** . 
1. Vyberte **virtuální sítě**.
1. Na panelu nástrojů vyberte **Přidat** . 
1. Vyberte **virtuální síť** , která má nasazeného hostitele bastionu. 
1. Vyberte podsíť pro virtuální počítače, nikoli **AzureBastionSubnet**, druhý, který jste vytvořili dříve. Zavřete stránku a znovu ji otevřete, Pokud nevidíte podsíť v seznamu dole. 

    ![Povolit vytvoření virtuálního počítače v podsíti](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Vyberte možnost **použít při vytváření virtuálního počítače** . 
1. Na panelu nástrojů vyberte **Uložit**. 
1. Pokud máte starou virtuální síť pro testovací prostředí, odeberte ji tak, že vyberete **...*  a **odeberte**. 

## <a name="enable-browser-connection"></a>Povolit připojení prohlížeče 

Jakmile v testovacím prostředí máte nakonfigurovanou virtuální síť bastionu, můžete jako vlastníka testovacího prostředí povolit připojení prohlížeče k virtuálním počítačům testovacího prostředí.

Pokud chcete povolit připojení prohlížeče k virtuálním počítačům testovacího prostředí, postupujte podle těchto kroků:

1. V Azure Portal přejděte do *testovacího prostředí*.
1. Vyberte **Konfigurace a zásady**.
1. V **Nastavení**vyberte **browser Connect (připojit**). Pokud tuto možnost nevidíte, zavřete stránku **zásady konfigurace** a znovu ji otevřete. 

    ![Povolit připojení prohlížeče](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Další kroky
V následujícím článku se dozvíte, jak se připojit k virtuálním počítačům pomocí prohlížeče: [Připojte se k virtuálním počítačům přes prohlížeč](connect-virtual-machine-through-browser.md) .
