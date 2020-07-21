---
title: Kurz – informace o přístupu k privátnímu cloudu
description: Přečtěte si, jak získat přístup k privátnímu cloudu řešení Azure VMware (AVS)
ms.topic: tutorial
ms.date: 05/04/2020
ms.openlocfilehash: 32dc5c173789996d2559eda1153b1509e10e5984
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2020
ms.locfileid: "86497960"
---
# <a name="tutorial-learn-how-to-access-an-azure-vmware-solution-avs-private-cloud"></a>Kurz: Naučte se získat přístup k privátnímu cloudu řešení Azure VMware (AVS)

Během období Preview vám neumožňuje vaše privátní cloud spravovat pomocí místního serveru vCenter. Pomocí pole pro přesun budete muset provést další nastavení a připojení k místní instanci vCenter. 

V tomto kurzu vytvoříte virtuální počítač s Windows pro pole přechodu ve skupině prostředků, kterou jste vytvořili v předchozím kurzu kurzu [: konfigurace sítě pro privátní cloud VMware v Azure](tutorial-configure-networking.md) a přihlášení do vCenter. Toto je virtuální počítač ve stejné virtuální síti, kterou jste vytvořili, a poskytuje přístup k vCenter a NSX Manageru. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Vytvořit virtuální počítač s Windows, který se má použít pro připojení k serveru vCenter
> * Přihlaste se k vCenter z virtuálního počítače.

## <a name="create-a-new-windows-virtual-machine"></a>Vytvoření nového virtuálního počítače s Windows

Ve skupině prostředků vyberte **+ Přidat** , vyhledejte a vyberte **Microsoft Windows 10**a pak klikněte na **vytvořit**.

:::image type="content" source="./media/tutorial-access-private-cloud/ss8-azure-w10vm-create.png" alt-text="Přidání nového virtuálního počítače s Windows 10 pro JumpBox" border="true":::

Zadejte požadované informace do polí a pak vyberte **zkontrolovat + vytvořit**. Další informace o polích naleznete v následující tabulce.

| Pole | Hodnota |
| --- | --- |
| **Předplatné** | Tato hodnota se už naplní předplatným, ke kterému patří skupina prostředků. |
| **Skupina prostředků** | Tato hodnota je pro aktuální skupinu prostředků již vyplněna. Mělo by se jednat o skupinu prostředků, kterou jste vytvořili v předchozím kurzu. |
| **Název virtuálního počítače** | Zadejte jedinečný název virtuálního počítače. |
| **Oblast** | Vyberte zeměpisnou polohu virtuálního počítače. |
| **Možnosti dostupnosti** | Ponechte vybranou výchozí hodnotu. |
| **Image** | Vyberte image virtuálního počítače. |
| **Velikost** | Ponechte výchozí hodnotu velikosti. |
| **Typ ověřování**  | Vyberte **heslo**. |
| **Uživatelské jméno** | Zadejte uživatelské jméno pro přihlášení k virtuálnímu počítači. |
| **Heslo** | Zadejte heslo pro přihlášení k virtuálnímu počítači. |
| **Potvrzení hesla** | Zadejte heslo pro přihlášení k virtuálnímu počítači. |
| **Veřejné příchozí porty** | Vyberte **Žádná**. Pokud vyberete možnost žádné, můžete použít [přístup JIT](../security-center/security-center-just-in-time.md#jit-configure) k řízení přístupu k virtuálnímu počítači pouze v případě, že k němu chcete přistupovat.  |

Po zadání příslušných informací klikněte na tlačítko **zkontrolovat + vytvořit**. Po úspěšném ověření vyberte **vytvořit** a spusťte tak proces vytváření virtuálního počítače.

:::image type="content" source="./media/tutorial-access-private-cloud/ss11-review-create-wjb01.png" alt-text="Vytvoření nového virtuálního počítače s Windows 10 pro JumpBox" border="true":::

## <a name="connect-to-the-local-vcenter-of-your-private-cloud"></a>Připojení k místnímu vCenter vašeho privátního cloudu

Z pole pro skok Přihlaste se k vSphere klientovi pomocí jednotného přihlašování VMware vCenter. Přihlaste se k klientovi vSphere pomocí uživatelského jména správce cloudu. Přijměte bezpečnostní riziko a pokračujte, až se zobrazí upozornění na potenciální bezpečnostní riziko. Přihlaste se k VMware vCenter pomocí přihlašovacích údajů jednotného přihlašování a ověřte, jestli se uživatelské rozhraní úspěšně zobrazilo.

V Azure Portal vyberte svůj privátní cloud a potom v zobrazení **Přehled** vyberte **Identita > výchozí**. Zobrazí se adresy URL a přihlašovací údaje pro správce privátního cloudu vCenter a NSX-T.

:::image type="content" source="./media/tutorial-access-private-cloud/ss4-display-identity.png" alt-text="Zobrazit adresy URL a přihlašovací údaje privátního cloudu vCenter a NSX Manageru" border="true":::

Přejděte k virtuálnímu počítači, který jste vytvořili v předchozím kroku, a připojte se k virtuálnímu počítači. Podrobné pokyny, jak se připojit k virtuálnímu počítači, najdete v tématu [připojení k virtuálnímu počítači](../virtual-machines/windows/connect-logon.md#connect-to-the-virtual-machine) .

Na virtuálním počítači s Windows otevřete prohlížeč a přejděte na adresu URL správce vCenter a NSX-T na dvou kartách. Na kartě vCenter zadejte `cloudadmin@vmcp.local` přihlašovací údaje uživatele z předchozího kroku.

:::image type="content" source="./media/tutorial-access-private-cloud/ss5-vcenter-login.png" alt-text="Přihlášení k serveru vCenter privátního cloudu" border="true":::

:::image type="content" source="./media/tutorial-access-private-cloud/ss6-vsphere-client-home.png" alt-text="portál vCenter" border="true":::

Na druhé kartě prohlížeče se přihlaste ke Správci NSX-T.

:::image type="content" source="./media/tutorial-access-private-cloud/ss10-nsx-manager-home.png" alt-text="Domů Správce NSX místního privátního cloudu" border="true":::

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvořit virtuální počítač s Windows, který se má použít pro připojení k serveru vCenter
> * Přihlaste se k vCenter z virtuálního počítače.

Přejděte k dalšímu kurzu, kde se dozvíte, jak škálovat privátní cloud služby AVS.

> [!div class="nextstepaction"]
> [Škálování privátního cloudu služby AVS](tutorial-scale-private-cloud.md)
