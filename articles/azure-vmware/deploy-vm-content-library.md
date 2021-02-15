---
title: Vytvoření knihovny obsahu pro nasazení virtuálních počítačů v řešení Azure VMware
description: Vytvořte knihovnu obsahu pro nasazení virtuálního počítače v privátním cloudu řešení Azure VMware.
ms.topic: how-to
ms.date: 02/03/2021
ms.openlocfilehash: a50b12ef8e139bf7de171398fd28f74fc3f310c9
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100382000"
---
# <a name="create-a-content-library-to-deploy-vms-in-azure-vmware-solution"></a>Vytvoření knihovny obsahu pro nasazení virtuálních počítačů v řešení Azure VMware

Knihovna obsahu uchovává a spravuje obsah ve formě položek knihovny. Jedna položka knihovny se skládá z jednoho nebo více souborů, které použijete k nasazení virtuálních počítačů (VM). 

V tomto článku Vás provedeme postupem vytvoření knihovny obsahu.  Pak provedeme nasazením virtuálního počítače pomocí Image ISO z knihovny obsahu.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu je nutný segment NSX-T (logický přepínač) a spravovaná služba DHCP.  Další informace najdete v článku [Správa DHCP v Azure VMware Solution](manage-dhcp.md) .

## <a name="create-a-content-library"></a>Vytvoření knihovny obsahu

1. Z místního klienta vSphere vyberte **nabídku > knihovny obsahu**.

   ![Výběr nabídky – > knihoven obsahu](./media/content-library/vsphere-menu-content-libraries.png)

1. Vyberte tlačítko **Přidat** a vytvořte novou knihovnu obsahu.

   ![Vyberte tlačítko Přidat a vytvořte novou knihovnu obsahu.](./media/content-library/create-new-content-library.png)

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

Teď, když jste se seznámili s vytvářením knihovny obsahu pro nasazení virtuálních počítačů v řešení Azure VMware, se můžete setkat s těmito informacemi:

- [Nasazení a konfigurace HCX VMware](tutorial-deploy-vmware-hcx.md) pro migraci úloh virtuálních počítačů do privátního cloudu.
- [Správa životního cyklu virtuálních počítačů řešení Azure VMware](lifecycle-management-of-azure-vmware-solution-vms.md).

<!-- LINKS - external-->

<!-- LINKS - internal -->
