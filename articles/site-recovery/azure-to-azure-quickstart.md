---
title: Nastavení zotavení po havárii virtuálního počítače Azure do sekundární oblasti pomocí Azure Site Recovery
description: Pomocí služby Azure Site Recovery můžete rychle nastavit zotavení po havárii do jiné oblasti Azure pro virtuální počítač Azure.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: e26c2a1f24a88dc979f4ec68de65afc618740c00
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "80371881"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Úvodní příručka: Nastavení zotavení po havárii do sekundární oblasti Azure pro virtuální počítač Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá k vaší strategii kontinuity podnikání a zotavení po havárii (BCDR) tím, že udržuje vaše obchodní aplikace online během plánovaných a neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure (VM), včetně replikace, převzetí služeb při selhání a obnovení.

Tento rychlý start popisuje, jak nastavit zotavení po havárii pro virtuální počítač Azure replikací do sekundární oblasti Azure. Obecně platí, že výchozí nastavení se používají k povolení replikace.

## <a name="prerequisites"></a>Požadavky

K dokončení tohoto kurzu potřebujete předplatné Azure a virtuální počítač.

- Pokud nemáte účet Azure s aktivním předplatným, můžete [si ho zdarma vytvořit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Doporučuje se virtuální virtuální připojení s minimálně 1 GB paměti RAM. [Přečtěte si další informace](/azure/virtual-machines/windows/quick-create-portal) o tom, jak vytvořit virtuální hod.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k [portálu Azure](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Povolení replikace virtuálního počítače Azure

Následující kroky umožňují replikaci virtuálních zařízení do sekundárního umístění.

1. Na webu Azure Portal vyberte z nabídky **Home** > **Virtual machines** virtuální počítač, který chcete replikovat.
1. V **operaci** vyberte **zotavení po havárii**.
1. V **oblasti Základní** > **cíl**vyberte cílovou oblast.
1. Chcete-li zobrazit nastavení replikace, vyberte **možnost Revize + Zahájení replikace**. Pokud potřebujete změnit všechny výchozí hodnoty, vyberte **Upřesnit nastavení**.
1. Chcete-li spustit úlohu, která umožňuje replikaci virtuálního zařízení, vyberte **možnost Zahájit replikaci**.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Povolte replikaci.":::

## <a name="verify-settings"></a>Ověření nastavení

Po dokončení úlohy replikace můžete zkontrolovat stav replikace, upravit nastavení replikace a otestovat nasazení.

1. V nabídce portálu Azure vyberte **Virtuální počítače** a vyberte virtuální počítač, který jste replikovali.
1. V **operaci** vyberte **zotavení po havárii**.
1. Chcete-li zobrazit podrobnosti replikace z **přehledu,** vyberte **položku Základy**. Další podrobnosti jsou zobrazeny v **oblasti Stav a stav**, Připravenost převzetí služeb při **selhání**a mapa **zobrazení Infrastruktura.**

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Stav replikace.":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Chcete-li zastavit replikaci virtuálního virtuálního serveru v primární oblasti, je nutné zakázat replikaci:

- Automaticky se vyčistí nastavení replikace zdroje.
- Rozšíření site recovery nainstalované na virtuálním počítači během replikace se neodebere.
- Fakturace obnovení webu pro virtuální ho virtuálního zařízení se zastaví.

Chcete-li replikaci zakázat, postupujte takto:

1. V nabídce portálu Azure vyberte **Virtuální počítače** a vyberte virtuální počítač, který jste replikovali.
1. V **operaci** vyberte **zotavení po havárii**.
1. V části **Přehled**vyberte **zakázat replikaci**.
1. Pokud chcete odinstalovat rozšíření Site Recovery, přejděte na rozšíření **nastavení** > virtuálního**počítače**.

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Zakažte replikaci.":::

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste replikovali jeden virtuální počítač do sekundární oblasti. Dále nastavte replikaci pro více virtuálních počítačích Azure.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii pro virtuální počítače Azure](azure-to-azure-tutorial-enable-replication.md)
