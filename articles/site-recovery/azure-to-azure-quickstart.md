---
title: Nastavení zotavení po havárii virtuálního počítače Azure do sekundární oblasti pomocí Azure Site Recovery
description: K rychlému nastavení zotavení po havárii do jiné oblasti Azure pro virtuální počítač Azure použijte službu Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: quickstart
ms.date: 11/12/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 91674d6335ae95993bcdd59250658d562302b1dc
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/12/2019
ms.locfileid: "73954129"
---
# <a name="set-up-disaster-recovery-to-a-secondary-azure-region-for-an-azure-vm"></a>Nastavení zotavení po havárii do sekundární oblasti Azure pro virtuální počítač Azure        

Služba [Azure Site Recovery](site-recovery-overview.md) přispívá ke strategii provozní kontinuity a zotavení po havárii (BCDR) tím, že zajišťuje provoz a dostupnost obchodních aplikací během plánovaných i neplánovaných výpadků. Site Recovery spravuje a orchestruje zotavení po havárii místních počítačů a virtuálních počítačů Azure, včetně replikace, převzetí služeb při selhání a zotavení.

V tomto rychlém startu se dozvíte, jak nastavit zotavení po havárii pro virtuální počítač Azure tím, že ho replikujte do jiné oblasti Azure.

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

> [!NOTE]
> Tento článek je jako rychlý návod pro nové uživatele. Používá nejjednodušší cestu s výchozími možnostmi a minimálními úpravami.  Úplný Názorný postup najdete v [našem kurzu](azure-to-azure-tutorial-enable-replication.md).

## <a name="log-in-to-azure"></a>Přihlaste se k Azure.

Přihlaste se k webu Azure Portal na adrese https://portal.azure.com.

## <a name="enable-replication-for-the-azure-vm"></a>Povolení replikace virtuálního počítače Azure

1. V nabídce Azure Portal vyberte **virtuální počítače**, nebo na libovolné stránce vyhledejte a vyberte *virtuální počítače* . Vyberte virtuální počítač, který chcete replikovat.
2. V části **Operace** vyberte **Zotavení po havárii**.
3. V části **Konfigurace zotavení po havárii** > **Cílová oblast** vyberte cílovou oblast, do které chcete replikaci provést.
4. Pro účely tohoto rychlého startu přijměte výchozí nastavení.
5. Vyberte **zkontrolovat a spustit replikaci**. Pak výběrem **spustit replikaci** spusťte úlohu, která povolí replikaci pro virtuální počítač.

    ![povolení replikace](media/azure-to-azure-quickstart/enable-replication1.png)

## <a name="verify-settings"></a>Ověření nastavení

Po dokončení úlohy replikace můžete zkontrolovat stav replikace, upravit nastavení replikace a otestovat nasazení.

1. V nabídce Azure Portal vyberte **virtuální počítače**, nebo na libovolné stránce vyhledejte a vyberte *virtuální počítače* . Vyberte virtuální počítač, který chcete ověřit.
2. V části **Operace** vyberte **Zotavení po havárii**.

   Můžete zkontrolovat stav replikace, vytvořené body obnovení a zdrojové a cílové oblasti na mapě.

   ![Stav replikace](media/azure-to-azure-quickstart/replication-status.png)

## <a name="clean-up-resources"></a>Vyčištění prostředků

Virtuální počítač v primární oblasti se přestane replikovat, když pro něj zakážete replikaci:

- Automaticky se vyčistí nastavení replikace zdroje. Rozšíření Site Recovery nainstalované na virtuálním počítači v rámci replikace se neodebralo a je třeba je odebrat ručně. 
- Site Recovery fakturace virtuálního počítače se zastaví.

Zastavte replikaci následujícím způsobem.

1. V nabídce Azure Portal vyberte **virtuální počítače**, nebo na libovolné stránce vyhledejte a vyberte *virtuální počítače* . Vyberte virtuální počítač, který chcete upravit.
2. V případě **zotavení po havárii**vyberte **Zakázat replikaci**.

   ![Zákaz replikace](media/azure-to-azure-quickstart/disable2-replication.png)

## <a name="next-steps"></a>Další kroky

V tomto rychlém startu jste replikovali jeden virtuální počítač do sekundární oblasti. Teď zkuste replikovat víc virtuálních počítačů Azure pomocí plánu obnovení.

> [!div class="nextstepaction"]
> [Konfigurace zotavení po havárii pro virtuální počítače Azure](azure-to-azure-tutorial-enable-replication.md)
