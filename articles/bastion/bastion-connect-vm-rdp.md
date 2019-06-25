---
title: Připojte se k virtuálnímu počítači s Windows pomocí Azure Bastionu | Dokumentace Microsoftu
description: V tomto článku zjistěte, jak se připojit k virtuální počítač Azure s Windows s použitím Azure Bastionu.
services: bastion
author: cherylmc
ms.service: bastion
ms.topic: conceptual
ms.date: 06/03/2019
ms.author: cherylmc
ms.openlocfilehash: c8a4b09a27325f31e548d1b345b2932c6ab6315c
ms.sourcegitcommit: 156b313eec59ad1b5a820fabb4d0f16b602737fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/18/2019
ms.locfileid: "67191886"
---
# <a name="connect-to-a-windows-virtual-machine-using-azure-bastion-preview"></a>Připojte se k virtuálnímu počítači s Windows pomocí Azure Bastionu (Preview)

Tento článek popisuje, jak snadno a bezpečně protokolu RDP pro virtuální počítače Windows v Azure virtuální sítě pomocí Azure Bastionu. K virtuálnímu počítači se můžete připojit přímo z webu Azure Portal. Při použití Azure Bastionu virtuální počítače nevyžadují žádného klienta, agenta ani další software. Další informace o Azure Bastionu, najdete v článku [přehled](bastion-overview.md).

> [!IMPORTANT]
> Tato verze Public Preview se poskytuje bez smlouvy o úrovni služeb a neměla by se používat pro úlohy v produkčním prostředí. Některé funkce nemusí být podporované, můžou mít omezené možnosti nebo nemusí být dostupné ve všech umístěních Azure. Podrobnosti najdete v [dodatečných podmínkách použití systémů Microsoft Azure Preview](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).
>

## <a name="before-you-begin"></a>Než začnete

Ujistěte se, že jste nastavili Azure Bastion host pro virtuální síť, ve kterém se tento virtuální počítač nachází. Další informace najdete v tématu [vytvoření Azure Bastion host](bastion-create-host-portal.md). Po Bastionu service je zřízení a nasazení ve virtuální síti, můžete se připojit k všech virtuálních počítačů v této virtuální síti. V této verzi preview Bastionu předpokládá, že se pomocí protokolu RDP pro připojení k virtuálnímu počítači Windows a SSH pro připojení virtuálních počítačů s Linuxem. Informace o připojení k virtuálnímu počítači s Linuxem najdete v tématu [připojit k virtuálnímu počítači - Linux](bastion-connect-vm-ssh.md).

Aby bylo možné navázat připojení, následující role jsou povinné:

* Role čtenáře na virtuálním počítači
* Role čtenáře na síťovou kartu s privátní IP adresa virtuálního počítače
* Role Čtenář u prostředku Azure Bastionu

## <a name="rdp"></a>Připojení pomocí protokolu RDP

1. V [webu Azure portal](https://aka.ms/BastionHost) Bastionu verze Preview, přejděte k virtuálnímu počítači, který chcete připojit k a potom klikněte na **připojit**. Virtuální počítač by měl být virtuální počítače s Windows pomocí připojení ke vzdálené ploše.

    ![Připojení virtuálního počítače](./media/bastion-connect-vm-rdp/connect.png)

1. Po kliknutí na Connect, zobrazí se bočním panelu, která obsahuje tři karty – protokol RDP, SSH a Bastionu. Pokud Bastionu byla zřízena pro virtuální síť, je ve výchozím nastavení aktivní kartě Bastionu. Pokud zřizujete neměli Bastion pro virtuální síť, můžete kliknout na odkaz a nakonfigurujte Bastionu. Pokyny ke konfiguraci, najdete v části [nakonfigurovat Bastionu](bastion-create-host-portal.md). Pokud nevidíte **Bastionu** uvedené, nebyly otevřít na portálu preview. Otevřete portál pro použití této funkce [náhledu odkazu](https://aka.ms/BastionHost).

    ![Připojení virtuálního počítače](./media/bastion-connect-vm-rdp/bastion.png)

1. Na kartě Bastionu, uživatelské jméno a heslo pro virtuální počítač, pak klikněte na tlačítko **připojit**. Otevře připojení RDP k tomuto virtuálnímu počítači prostřednictvím Bastionu se přímo na portálu Azure portal (přes HTML5) pomocí portu 443 a službu Bastionu.

    ![Připojení virtuálního počítače](./media/bastion-connect-vm-rdp/443rdp.png)
 
## <a name="next-steps"></a>Další postup

Přečtěte si [Bastionu – nejčastější dotazy](bastion-faq.md)