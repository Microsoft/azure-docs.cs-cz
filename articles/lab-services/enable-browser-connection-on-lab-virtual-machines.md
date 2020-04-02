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
ms.date: 08/19/2019
ms.author: takamath
ms.openlocfilehash: 86304fc7776f49c999924b8609f2d26120cee372
ms.sourcegitcommit: 980c3d827cc0f25b94b1eb93fd3d9041f3593036
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/02/2020
ms.locfileid: "80549082"
---
# <a name="enable-browser-connection-on-lab-virtual-machines"></a>Povolení připojení prohlížeče na virtuálních počítačích testovacího prostředí 

DevTest Labs se integruje s [Azure Bastion](https://docs.microsoft.com/azure/bastion/), který umožňuje připojení k virtuálním počítačům prostřednictvím prohlížeče. Nejprve je třeba povolit připojení prohlížeče na virtuálních počítačích testovacího prostředí.

Jako vlastník testovacího prostředí můžete povolit přístup ke všem virtuálním počítačům testovacího prostředí prostřednictvím prohlížeče. Nepotřebujete dalšího klienta, agenta nebo software. Azure Bastion poskytuje zabezpečené a bezproblémové připojení RDP/SSH k vašim virtuálním počítačům přímo na portálu Azure přes TLS. Když se připojíte přes Azure Bastion, vaše virtuální počítače nepotřebují veřejnou IP adresu. Další informace najdete v tématu [Co je Azure Bastion?](../bastion/bastion-overview.md)

> [!NOTE]
> Povolení připojení prohlížeče na virtuálních počítačích testovacího prostředí je ve verzi Preview.

Tento článek ukazuje, jak povolit připojení prohlížeče na virtuálních počítačích testovacího prostředí.

## <a name="prerequisites"></a>Požadavky 
Buď nasaďte hostitele Bastion ve virtuální síti vaší stávající laboratoře **(OR),** připojte vaše testovací prostředí pomocí virtuální sítě nakonfigurované na bastionu. 

Informace o tom, jak nasadit hostitele bašty ve virtuální síti, najdete v [tématu vytvoření hostitele Bašty Azure (preview).](../bastion/bastion-create-host-portal.md) Při vytváření hostitele Bastion vyberte virtuální síť testovacího prostředí. 

Informace o tom, jak propojit testovací prostředí s virtuální sítí nakonfigurovanou baštou, najdete [v tématu Konfigurace virtuální sítě v laboratořích Azure DevTest Labs](devtest-lab-configure-vnet.md). Vyberte virtuální síť, která má nasazeného hostitele Bastion a **AzureBastionSubnet** v něm. Zde jsou podrobné kroky: 

1. Přihlaste se k [portálu Azure](https://portal.azure.com).
1. V levé navigační nabídce vyberte **Všechny služby.** 
1. Ze seznamu vyberte **DevTest Labs**. 
1. Ze seznamu testovacích prostředí vyberte *testovací prostředí*. 

    > [!NOTE]
    > Azure Bastion je momentálně ve verzi preview. Je omezena na následující oblasti: Západní USA, Východní USA, Západní Evropa, Střední usa – jih, Austrálie – východ a Japonsko – východ. Takže vytvořte testovací prostředí v jedné z těchto oblastí, pokud vaše laboratoř není v jedné z nich. 
1. V **levé** nabídce vyberte Konfigurace **a zásady.** 
1. Vyberte **Virtuální sítě**.
1. Na panelu nástrojů vyberte **Přidat.** 
1. Vyberte **virtuální síť,** která má nasazeného hostitele Bastion. 
1. Vyberte podsíť: **AzureBastionSubnet**. 

    ![Podsíť](./media/enable-browser-connection-lab-virtual-machines/subnet.png)
1. Vyberte **Použít při vytváření virtuálních strojů.** 
1. Na panelu nástrojů vyberte **Uložit**. 
1. Pokud máte starou virtuální síť pro testovací prostředí, odeberte ji výběrem **...*  a **odebrat**. 

## <a name="enable-browser-connection"></a>Povolení připojení prohlížeče 

Jakmile máte nakonfigurovanou virtuální síť v rámci testovacího prostředí, jako vlastník testovacího prostředí můžete povolit připojení prohlížeče na virtuálních počítačích testovacího prostředí.

Chcete-li povolit připojení prohlížeče na virtuálních počítačích testovacího prostředí, postupujte takto:

1. Na webu Azure Portal přejděte do *testovacího prostředí*.
1. Vyberte **možnost Konfigurace a zásady**.
1. V **nastavení**vyberte **Připojit prohlížeč (Náhled).**

![Povolení připojení prohlížeče](./media/enable-browser-connection-lab-virtual-machines/browser-connect.png)

## <a name="next-steps"></a>Další kroky
V následujícím článku se dozvíte, jak se připojit k virtuálním počítačům pomocí prohlížeče: [Připojení k virtuálním počítačům prostřednictvím prohlížeče](connect-virtual-machine-through-browser.md)