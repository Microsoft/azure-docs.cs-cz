---
title: Kurz – přístup k privátnímu cloudu
description: Přečtěte si, jak získat přístup k privátnímu cloudu řešení Azure VMware.
ms.topic: tutorial
ms.date: 02/22/2021
ms.openlocfilehash: 456767a9edd78a70a0aba45c7b44a2150a2217a1
ms.sourcegitcommit: f3ec73fb5f8de72fe483995bd4bbad9b74a9cc9f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/04/2021
ms.locfileid: "102044999"
---
# <a name="tutorial-access-an-azure-vmware-solution-private-cloud"></a>Kurz: přístup k privátnímu cloudu řešení Azure VMware

Řešení Azure VMware vám neumožňuje spravovat váš privátní cloud pomocí místního serveru vCenter. Pomocí pole pro přesun budete muset provést další nastavení a připojení k místní instanci vCenter. 

V tomto kurzu vytvoříte pole s odkazem ve skupině prostředků, kterou jste vytvořili v [předchozím kurzu](tutorial-configure-networking.md) , a přihlásíte se k vCenter. Pole s odkazem je virtuální počítač s Windows (VM) ve stejné virtuální síti, kterou jste vytvořili.  Poskytuje přístup k vCenter a NSX Manageru. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit virtuální počítač s Windows, který se má použít pro připojení k serveru vCenter
> * Přihlaste se k vCenter z virtuálního počítače.

## <a name="create-a-new-windows-virtual-machine"></a>Vytvoření nového virtuálního počítače s Windows

[!INCLUDE [create-avs-jump-box-steps](includes/create-jump-box-steps.md)]

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Připojení k místnímu vCenter vašeho privátního cloudu

1. V poli pro skok se přihlaste k klientovi vSphere pomocí jednotného přihlašování VMware vCenter pomocí uživatelského jména správce cloudu a Verity, že se uživatelské rozhraní úspěšně zobrazuje.

1. V Azure Portal vyberte svůj privátní cloud a pak **spravujte**  >  **identitu**. 

   Adresy URL a přihlašovací údaje uživatele pro zobrazení privátního cloudu vCenter a správce NSX-T.

   >[!TIP]
   >Pokud chcete vygenerovat nová hesla vCenter a NSX-T, vyberte **vytvořit nové heslo** .

   :::image type="content" source="media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Zobrazí adresy URL a přihlašovací údaje privátního cloudu vCenter a NSX Manageru." border="true" lightbox="media/tutorial-access-private-cloud/ss4-display-identity.png":::

1. Přejděte na virtuální počítač, který jste vytvořili v předchozím kroku, a připojte se k virtuálnímu počítači. 

   Pokud potřebujete pomáhat s připojením k VIRTUÁLNÍmu počítači, přečtěte si podrobnosti v tématu [připojení k virtuálnímu počítači](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) .

1. Na virtuálním počítači s Windows otevřete prohlížeč a přejděte na adresu URL správce vCenter a NSX-T na dvou kartách. 

1. Na kartě vCenter zadejte `cloudadmin@vmcp.local` přihlašovací údaje uživatele z předchozího kroku.

   :::image type="content" source="media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Přihlaste se k privátnímu cloudu vCenter." border="true":::

   :::image type="content" source="media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="portál vCenter." border="true":::

1. Na druhé kartě prohlížeče se přihlaste ke Správci NSX-T.

   :::image type="content" source="media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Na druhé kartě prohlížeče se přihlaste ke Správci NSX-T." border="true":::



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit virtuální počítač s Windows, který se má použít pro připojení k serveru vCenter
> * Přihlaste se k vCenter z virtuálního počítače.

Přejděte k dalšímu kurzu, kde se dozvíte, jak vytvořit virtuální síť pro nastavení místní správy pro clustery privátních cloudů.

> [!div class="nextstepaction"]
> [Vytvoření Virtual Network](tutorial-configure-networking.md)


