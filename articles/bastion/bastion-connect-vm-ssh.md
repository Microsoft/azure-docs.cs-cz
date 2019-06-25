---
title: Připojte se k virtuálnímu počítači s Linuxem pomocí Azure Bastionu | Dokumentace Microsoftu
description: V tomto článku se dozvíte, jak připojení pro virtuální počítač s Linuxem pomocí Azure Bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: 572043598c71a400e154c5c2e9e6c2f1e9b4ab49
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191795"
---
# <a name="connect-using-ssh-to-a-linux-virtual-machine-using-azure-bastion-preview"></a>Připojení pomocí protokolu SSH pro virtuální počítač s Linuxem pomocí Azure Bastionu (Preview)

Tento článek vám ukáže, jak chcete snadno a bezpečně do virtuálních počítačů s Linuxem ve službě Azure virtual network přes SSH. K virtuálnímu počítači se můžete připojit přímo z webu Azure Portal. Při použití Azure Bastionu virtuální počítače nevyžadují žádného klienta, agenta ani další software. Další informace o Azure Bastionu, najdete v článku [přehled](bastion-overview.md).

Bastionu Azure můžete použít pro připojení k virtuální počítač s Linuxem pomocí protokolu SSH. Uživatelské jméno a heslo a klíčů SSH můžete použít pro ověřování. Můžete připojit k virtuálnímu počítači s klíči SSH pomocí:

* Soukromý klíč, který je zadat ručně
* Soubor, který obsahuje informace o soukromém klíči

Privátní klíč SSH musí být ve formátu, který začíná `"-----BEGIN RSA PRIVATE KEY-----"` a končí `"-----END RSA PRIVATE KEY-----"`.

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že jste nastavili Azure Bastion host pro virtuální síť, ve kterém se tento virtuální počítač nachází. Další informace najdete v tématu [vytvoření Azure Bastion host](bastion-create-host-portal.md). Po Bastionu service je zřízení a nasazení ve virtuální síti, můžete se připojit k všech virtuálních počítačů v této virtuální síti. V této verzi preview použijete-li Bastionu se pokud chcete připojit, předpokládá, že se pomocí protokolu RDP pro připojení k virtuálnímu počítači Windows a SSH pro připojení virtuálních počítačů s Linuxem.

Aby bylo možné navázat připojení, následující role jsou povinné:

* Role čtenáře na virtuálním počítači
* Role čtenáře na síťovou kartu s privátní IP adresa virtuálního počítače
* Role Čtenář u prostředku Azure Bastionu

## <a name="username"></a>Připojení: Pomocí uživatelského jména a hesla


1. V [webu Azure portal](https://aka.ms/BastionHost) Bastionu verze Preview, přejděte k virtuálnímu počítači, který chcete připojit k a potom klikněte na **připojit**. Virtuální počítač by měl být virtuální počítač s Linuxem pomocí připojení SSH.
1. Po kliknutí na Connect, zobrazí se bočním panelu, která obsahuje tři karty – protokol RDP, SSH a Bastionu. Pokud Bastionu byla zřízena pro virtuální síť, je ve výchozím nastavení aktivní kartě Bastionu. Pokud Bastionu nepovedlo zřídit virtuální sítě, přečtěte si téma [nakonfigurovat Bastionu](bastion-create-host-portal.md). Pokud nevidíte **Bastionu** uvedené, nebyly otevřít na portálu preview. Otevřít na portálu pomocí [tento odkaz](https://aka.ms/BastionHost).

      ![Připojení virtuálního počítače](./media/bastion-connect-vm-ssh/bastion.png)

1. Zadejte uživatelské jméno a heslo pro SSH k virtuálnímu počítači.
1. Klikněte na tlačítko **připojit** tlačítko po zadání klíče.

## <a name="privatekey"></a>Připojení: Ručně zadejte privátní klíč

1. V [webu Azure portal](https://aka.ms/BastionHost) Bastionu verze Preview, přejděte k virtuálnímu počítači, který chcete připojit k a potom klikněte na **připojit**. Virtuální počítač by měl být virtuální počítač s Linuxem pomocí připojení SSH.
1. Po kliknutí na Connect, zobrazí se bočním panelu, která obsahuje tři karty – protokol RDP, SSH a Bastionu. Pokud Bastionu byla zřízena pro virtuální síť, je ve výchozím nastavení aktivní kartě Bastionu. Pokud Bastionu nepovedlo zřídit virtuální sítě, přečtěte si téma [nakonfigurovat Bastionu](bastion-create-host-portal.md). Pokud nevidíte **Bastionu** uvedené, nebyly otevřít na portálu preview. Otevřít na portálu pomocí [tento odkaz](https://aka.ms/BastionHost).

      ![Připojení virtuálního počítače](./media/bastion-connect-vm-ssh/bastion.png)

1. Zadejte uživatelské jméno a vyberte **privátní klíč SSH**.
1. Do textového pole zadejte váš privátní klíč **privátní klíč SSH** (nebo ji vložíte přímo).
1. Klikněte na tlačítko **připojit** tlačítko po zadání klíče.

## <a name="ssh"></a>Připojení: Pomocí souboru privátního klíče

1. V [webu Azure portal](https://aka.ms/BastionHost) Bastionu verze Preview, přejděte k virtuálnímu počítači, který chcete připojit k a potom klikněte na **připojit**. Virtuální počítač by měl být virtuální počítač s Linuxem pomocí připojení SSH.

    ![Připojení virtuálního počítače](./media/bastion-connect-vm-ssh/connect.png)

1. Po kliknutí na Connect, zobrazí se bočním panelu, která obsahuje tři karty – protokol RDP, SSH a Bastionu. Pokud Bastionu byla zřízena pro virtuální síť, je ve výchozím nastavení aktivní kartě Bastionu. Pokud Bastionu nepovedlo zřídit virtuální sítě, přečtěte si téma [nakonfigurovat Bastionu](bastion-create-host-portal.md). Pokud nevidíte **Bastionu** uvedené, nebyly otevřít na portálu preview. Otevřít na portálu pomocí [tento odkaz](https://aka.ms/BastionHost).

    ![Připojení virtuálního počítače](./media/bastion-connect-vm-ssh/bastion.png)

1. Zadejte uživatelské jméno a vyberte **privátní klíč SSH z místního souboru**.
1. Klikněte na tlačítko **Procházet** tlačítko (ikona složky v místním souboru).
1. Vyhledat soubor a potom klikněte na **otevřít**.
1. Klikněte na tlačítko **připojit** pro připojení k virtuálnímu počítači. Po kliknutí na Connect, přístup k tomuto virtuálnímu počítači přes SSH přímo otevře na portálu Azure portal. Toto připojení není v HTML5 pomocí portu 443 na Bastionu služby přes privátní IP adresa vašeho virtuálního počítače.

## <a name="next-steps"></a>Další postup

Přečtěte si [Bastionu – nejčastější dotazy](bastion-faq.md)