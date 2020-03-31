---
title: Povolení připojení prohlížeče na virtuálních počítačích Azure DevTest Labs | Dokumenty společnosti Microsoft
description: DevTest Labs se teď integruje s Azure Bastion a jako vlastník testovacího prostředí můžete povolit přístup ke všem testovacím virtuálním počítačům prostřednictvím prohlížeče.
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
ms.date: 12/09/2019
ms.author: takamath
ms.openlocfilehash: e2dd642139ae082cc0d0838e61399c549d2d812a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74970759"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Povolení připojení prohlížeče na virtuálních počítačích testovacího prostředí 
DevTest Labs se integruje s [Azure Bastion](https://docs.microsoft.com/azure/bastion/), který umožňuje připojení k virtuálním počítačům prostřednictvím prohlížeče. Nejprve je třeba povolit připojení prohlížeče na virtuálních počítačích testovacího prostředí.

Jako vlastník testovacího prostředí můžete povolit přístup ke všem virtuálním počítačům testovacího prostředí prostřednictvím prohlížeče. Nepotřebujete dalšího klienta, agenta nebo software. Azure Bastion poskytuje zabezpečené a bezproblémové připojení RDP/SSH k vašim virtuálním počítačům přímo na portálu Azure přes SSL. Když se připojíte přes Azure Bastion, vaše virtuální počítače nepotřebují veřejnou IP adresu. Další informace najdete v tématu [Co je Azure Bastion?](../bastion/bastion-overview.md)


Tento článek ukazuje, jak povolit připojení prohlížeče na virtuálních počítačích testovacího prostředí.

## <a name="prerequisites"></a>Požadavky 
Buď nasaďte hostitele Bastion ve virtuální síti vaší stávající laboratoře **(OR),** připojte testovací prostředí k virtuální síti nakonfigurované baštou. 

Informace o tom, jak nasadit hostitele bašty ve virtuální síti, najdete v [tématu Vytvoření hostitele Bašty Azure](../bastion/bastion-create-host-portal.md). Při vytváření hostitele Bastion vyberte virtuální síť testovacího prostředí. 

Nejprve je třeba vytvořit druhou podsíť ve virtuální síti Bastion, protože AzureBastionSubnet neumožňuje vytváření prostředků bez bastionu v něm. 

## <a name="create-a-second-sub-net-in-the-bastion-virtual-network"></a>Vytvoření druhé dílčí sítě ve virtuální síti Bastion
Virtuální počítače testovacího prostředí nelze vytvořit v podsíti Azure Bastion. Vytvořte další podsíť ve virtuální síti Bastion, jak je znázorněno na následujícím obrázku:

![Druhá podsíť ve virtuální síti Azure Bastion](./media/connect-virtual-machine-through-browser/second-subnet.png)

## <a name="enable-vm-creation-in-the-subnet"></a>Povolení vytváření virtuálních virtuálních mích v podsíti
Teď povolte vytváření virtuálních stránek v této podsíti takto: 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levé navigační nabídce vyberte **Všechny služby.** 
1. Ze seznamu vyberte **DevTest Labs**. 
1. Ze seznamu testovacích prostředí vyberte *testovací prostředí*. 

    > [!NOTE]
    > Azure Bastion je teď obecně dostupná v následujících oblastech: Západní USA, Východní USA, Západní Evropa, Jižní Střed USA, Austrálie – východ a Japonsko – východ. Takže vytvořte testovací prostředí v jedné z těchto oblastí, pokud vaše laboratoř není v jedné z nich. 
    
1. V **levé** nabídce vyberte Konfigurace **a zásady.** 
1. Vyberte **Virtuální sítě**.
1. Na panelu nástrojů vyberte **Přidat.** 
1. Vyberte **virtuální síť,** ve které je nasazen ý hostitel Bastion. 
1. Vyberte podsíť pro virtuální počítače, ne **AzureBastionSubnet**, druhý, který jste vytvořili dříve. Zavřete stránku a znovu ji otevřete, pokud podsíť v seznamu v dolní části nevidíte. 

    ![Povolení vytváření virtuálních virtuálních mích v podsíti](./media/connect-virtual-machine-through-browser/enable-vm-creation-subnet.png)
1. Vyberte **Použít při vytváření virtuálních strojů.** 
1. Na panelu nástrojů vyberte **Uložit**. 
1. Pokud máte starou virtuální síť pro laboratoř, odstraňte ji výběrem **...*  a **odebrat**. 

## <a name="enable-browser-connection"></a>Povolení připojení prohlížeče 

Jakmile máte virtuální síť nakonfigurovanou baštou uvnitř testovacího prostředí, jako vlastník testovacího prostředí můžete povolit připojení prohlížeče na virtuálních počítačích testovacího prostředí.

Chcete-li povolit připojení prohlížeče na virtuálních počítačích testovacího prostředí, postupujte takto:

1. Na webu Azure Portal přejděte do *testovacího prostředí*.
1. Vyberte **možnost Konfigurace a zásady**.
1. V **nastavení**vyberte **možnost Připojení prohlížeče**. Pokud tuto možnost nevidíte, zavřete stránku **Zásady konfigurace** a znovu ji otevřete. 

    ![Povolení připojení prohlížeče](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Další kroky
V následujícím článku se dozvíte, jak se připojit k virtuálním počítačům pomocí prohlížeče: [Připojení k virtuálním počítačům prostřednictvím prohlížeče](connect-virtual-machine-through-browser.md)
