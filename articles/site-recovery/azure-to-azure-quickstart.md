---
title: Nastavení zotavení po havárii virtuálního počítače Azure do sekundární oblasti pomocí Azure Site Recovery
description: K rychlému nastavení zotavení po havárii do jiné oblasti Azure pro virtuální počítač Azure použijte službu Azure Site Recovery.
ms.topic: quickstart
ms.date: 03/27/2020
ms.custom: mvc
ms.openlocfilehash: 8681ccaac30402927005e5e8802d7da2bddc2c5c
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/05/2020
ms.locfileid: "86135706"
---
# <a name="quickstart-set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Rychlý Start: nastavení zotavení po havárii do sekundární oblasti Azure pro virtuální počítač Azure

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii zajištění provozní kontinuity a zotavení po havárii (BCDR) tím, že vaše firemní aplikace bude online během plánovaných a neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure (VM), včetně replikace, převzetí služeb při selhání a obnovení.

V tomto rychlém startu se dozvíte, jak nastavit zotavení po havárii pro virtuální počítač Azure tím, že ho replikujte do sekundární oblasti Azure. Obecně platí, že k povolení replikace se používají výchozí nastavení.

## <a name="prerequisites"></a>Předpoklady

K dokončení tohoto kurzu potřebujete předplatné Azure a virtuální počítač.

- Pokud nemáte účet Azure s aktivním předplatným, můžete si [účet zdarma vytvořit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- Doporučuje se virtuální počítač s minimálně 1 GB paměti RAM. [Přečtěte si další informace](../virtual-machines/windows/quick-create-portal.md) o tom, jak vytvořit virtuální počítač.

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se na web [Azure Portal](https://portal.azure.com).

## <a name="enable-replication-for-the-azure-vm"></a>Povolení replikace virtuálního počítače Azure

Následující kroky umožňují replikaci virtuálních počítačů do sekundárního umístění.

1. V nabídce Azure Portal z nabídky **domácí**  >  **virtuální počítače** vyberte virtuální počítač, který chcete replikovat.
1. V případě **operací** vyberte **zotavení po havárii**.
1. V **Basics**  >  **cílové oblasti**základy vyberte cílovou oblast.
1. Pokud chcete zobrazit nastavení replikace, vyberte **zkontrolovat a spustit replikaci**. Pokud potřebujete změnit výchozí nastavení, vyberte **Upřesnit nastavení**.
1. Pokud chcete spustit úlohu, která umožňuje replikaci virtuálních počítačů, vyberte **spustit replikaci**.

   :::image type="content" source="media/azure-to-azure-quickstart/enable-replication1.png" alt-text="Povolte replikaci.":::

## <a name="verify-settings"></a>Ověření nastavení

Po dokončení úlohy replikace můžete zkontrolovat stav replikace, upravit nastavení replikace a otestovat nasazení.

1. V nabídce Azure Portal vyberte **virtuální počítače** a vyberte virtuální počítač, který jste replikoval.
1. V případě **operací** vyberte **zotavení po havárii**.
1. Pokud chcete zobrazit podrobnosti o replikaci z **přehledu** , vyberte **základy**. Další podrobnosti najdete v části stav **a stav**, **připravenost na převzetí služeb při selhání**a mapa **zobrazení infrastruktury** .

   :::image type="content" source="media/azure-to-azure-quickstart/replication-status.png" alt-text="Povolte replikaci.":::

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete zastavit replikaci virtuálního počítače v primární oblasti, musíte zakázat replikaci:

- Automaticky se vyčistí nastavení replikace zdroje.
- Rozšíření Site Recovery nainstalované na virtuálním počítači během replikace se neodebere.
- Site Recovery fakturace virtuálního počítače se zastaví.

Pokud chcete zakázat replikaci, proveďte tyto kroky:

1. V nabídce Azure Portal vyberte **virtuální počítače** a vyberte virtuální počítač, který jste replikoval.
1. V případě **operací** vyberte **zotavení po havárii**.
1. V **přehledu**vyberte **Zakázat replikaci**.
1. Pokud chcete odinstalovat rozšíření Site Recovery, přečtěte si rozšíření **Nastavení**virtuálního počítače  >  **Extensions**.

   :::image type="content" source="media/azure-to-azure-quickstart/disable2-replication.png" alt-text="Povolte replikaci.":::

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste replikovali jeden virtuální počítač do sekundární oblasti. V dalším kroku nastavte replikaci pro několik virtuálních počítačů Azure.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii pro virtuální počítače Azure](azure-to-azure-tutorial-enable-replication.md)
