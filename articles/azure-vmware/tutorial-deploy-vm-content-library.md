---
title: 'Kurz: Vytvoření knihovny obsahu pro nasazení virtuálních počítačů v řešení Azure VMware (AVS)'
description: V tomto kurzu Azure VMware Solution (AVS) vytvoříte knihovnu obsahu pro nasazení virtuálního počítače v privátním cloudu služby AVS.
ms.topic: tutorial
ms.date: 07/16/2020
ms.openlocfilehash: d86f9cb92dcb8a852b9b178a5e160da0d1a3a692
ms.sourcegitcommit: cd0a1ae644b95dbd3aac4be295eb4ef811be9aaa
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/19/2020
ms.locfileid: "88612788"
---
# <a name="tutorial-create-a-content-library-to-deploy-vms-in-azure-vmware-solution-avs"></a>Kurz: Vytvoření knihovny obsahu pro nasazení virtuálních počítačů v řešení Azure VMware (AVS)

Knihovna obsahu uchovává a spravuje obsah ve formě položek knihovny. Jedna položka knihovny se skládá z jednoho nebo více souborů, které použijete k nasazení virtuálních počítačů (VM). 
 
V tomto kurzu se naučíte:
> [!div class="checklist"]
> * Vytvoření knihovny obsahu
> * Nahrání image ISO do knihovny obsahu
> * Nasazení virtuálního počítače pomocí ISO v knihovně obsahu

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu je nutný segment logického přepínače NSX-T a spravovaná služba DHCP.  Podrobnosti najdete v tématu [Správa DHCP v řešení Azure VMware (AVS) ve verzi Preview](manage-dhcp.md) .

## <a name="create-a-content-library"></a>Vytvoření knihovny obsahu

1. Z místního klienta vSphere vyberte **nabídku > knihovny obsahu**.

   ![Výběr nabídky – > knihoven obsahu](./media/content-library/vsphere-menu-content-libraries.png)

1. Kliknutím na tlačítko **Přidat** vytvoříte novou knihovnu obsahu.

   ![Kliknutím na tlačítko Přidat vytvoříte novou knihovnu obsahu.](./media/content-library/create-new-content-library.png)

1. Zadejte název a potvrďte IP adresu serveru vCenter a pak vyberte **Další**.

   ![Zadejte název a poznámky, které zvolíte, a pak vyberte Další.](./media/content-library/new-content-library-step1.png)

1. Vyberte **místní knihovnu obsahu** a vyberte **Další**.

   ![V tomto příkladu vytvoříme místní knihovnu obsahu, vyberte Další.](./media/content-library/new-content-library-step2.png)

1. Vyberte úložiště dat, do kterého se bude ukládat vaše knihovna obsahu, a pak vyberte **Další**.

   ![Vyberte úložiště dat, které chcete hostovat v knihovně obsahu, vyberte Další.](./media/content-library/new-content-library-step3.png)

1. Zkontrolujte a ověřte nastavení knihovny obsahu a pak vyberte **Dokončit**.

   ![Ověřte nastavení a vyberte Dokončit.](./media/content-library/new-content-library-step4.png)

## <a name="upload-an-iso-image-to-the-content-library"></a>Nahrání image ISO do knihovny obsahu

Teď, když je knihovna obsahu vytvořená, můžete přidat bitovou kopii ISO pro nasazení virtuálního počítače do clusteru privátního cloudu. 

1. Z klienta vSphere vyberte **nabídku > knihovny obsahu**.

1. Klikněte pravým tlačítkem na knihovnu obsahu, kterou chcete použít pro novou normu ISO a vyberte **importovat položku**.

1. Proveďte jednu z následujících akcí a naimportujte položku knihovny pro zdroj tak, že vyberete **importovat**:
   1. Vyberte adresu URL a zadejte adresu URL pro stažení ISO.

   1. Vyberte **místní soubor** , který se má nahrát z místního systému.

   > [!TIP]
   > Volitelně můžete pro cíl definovat vlastní název položky a poznámky.

1. Otevřete knihovnu a vyberte kartu **Další typy** , abyste ověřili, že vaše ISO se úspěšně nahrály.


## <a name="deploy-a-vm-to-a-private-cloud-cluster"></a>Nasazení virtuálního počítače do clusteru privátního cloudu

1. Z klienta vSphere vyberte **nabídku > hostitelé a clustery**.

1. Na levém panelu rozbalte strom a vyberte cluster.

1. Vyberte **akce > nový virtuální počítač**.

1. Projděte si průvodce a upravte požadovaná nastavení.

1. Vyberte možnost **Nová jednotka CD/DVD > soubor ISO knihovny obsahu pro klientské zařízení >**.

1. Vyberte soubor ISO nahraný v předchozí části a pak vyberte **OK**.

1. Zaškrtněte políčko **připojit** , aby byl ISO připojen v čase napájení.

1. Vyberte **nová síť > vyberte rozevírací seznam > procházet**.

1. Vyberte **logický přepínač (segment)** a vyberte **OK**.

1. Upravte jakákoli další nastavení hardwaru a vyberte **Další**.

1. Ověřte nastavení a vyberte **Dokončit**.


## <a name="next-steps"></a>Další kroky

Pokud plánujete použít rozšíření HCX (Hybrid Cloud Extension) k migraci úloh virtuálních počítačů do privátního cloudu, použijte postup [Řešení Azure VMware pro instalaci HCX](hybrid-cloud-extension-installation.md) .

<!-- LINKS - external-->

<!-- LINKS - internal -->
