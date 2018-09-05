---
title: Pomocí Azure Site Recovery replikovat vícevrstvé nasazení Dynamics AX | Dokumentace Microsoftu
description: Tento článek popisuje, jak replikovat a ochraně Dynamics AX pomocí Azure Site Recovery
services: site-recovery
documentationcenter: ''
author: asgang
manager: rochakm
editor: ''
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/06/2018
ms.author: asgang
ms.openlocfilehash: eb6f7d9b34e00ce1efd8c871439c2504e5f550d5
ms.sourcegitcommit: 31241b7ef35c37749b4261644adf1f5a029b2b8e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/04/2018
ms.locfileid: "43669438"
---
# <a name="replicate-a-multitier-dynamics-ax-application-by-using-azure-site-recovery"></a>Pomocí Azure Site Recovery replikovat vícevrstvé aplikace Dynamics AX

## <a name="overview"></a>Přehled


 Dynamics AX je jedním z nejoblíbenějších řešení ERP pro podniky standardizovat procesy v lokalitách, Správa prostředků a zjednodušit dodržování předpisů. Protože aplikace je zásadní pro organizace, v případě havárie, by aplikace měla být zprovoznění minimální včas.

V současné době Dynamics AX neposkytuje každé havárie out-of-the-box funkcím pro obnovení. Dynamics AX se skládá z mnoha součásti serveru, jako je například Server objekt aplikace Windows, Azure Active Directory, Azure SQL Database, SharePoint Server a služby Reporting Services. Ke správě po havárii obnovení všechny tyto komponenty ručně není pouze nákladná, ale také náchylné k chybám.

Tento článek vysvětluje, jak vytvořit řešení zotavení po havárii pro vaši aplikaci Dynamics AX pomocí [Azure Site Recovery](site-recovery-overview.md). Obsahuje také testovací plánované/neplánované převzetí služeb při selhání použít plán obnovení jedním kliknutím, podporované konfigurace a požadavky.



## <a name="prerequisites"></a>Požadavky

Implementace zotavení po havárii pro aplikace Dynamics AX pomocí Site Recovery se vyžaduje následující požadavky:

• Nastavení v místním nasazení Dynamics AX.

• Vytvoříte trezor Site Recovery v předplatném Azure.

• Azure svůj záložní web, je-li spustit nástroj pro vyhodnocení připravenosti na virtuální počítač Azure na virtuálních počítačích. Musí být kompatibilní se službou Azure Virtual Machines a služby Site Recovery services.

## <a name="site-recovery-support"></a>Site Recovery podporu

Pro účely vytváření tohoto článku, můžeme použít virtuální počítače VMware s Dynamics AX 2012 R3 na Windows Server 2012 R2 Enterprise. Protože replikace služby site recovery je nezávislý na aplikace, Očekáváme, že doporučení uvedená tady pro uložení v následujících scénářích.

### <a name="source-and-target"></a>Zdroj a cíl

**Scénář** | **Do sekundární lokality** | **Do Azure**
--- | --- | ---
**Hyper-V** | Ano | Ano
**VMware** | Ano | Ano
**Fyzický server** | Ano | Ano

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Povolit aplikaci Dynamics AX zotavení po havárii s využitím Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Ochrana aplikací Dynamics AX
Chcete-li aplikace dokončena a replikace a obnovení, musí být chráněny každou komponentu aplikace Dynamics AX.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Nastavení replikace služby Active Directory a DNS

Služby Active Directory je vyžadován v lokalitě pro obnovení po havárii pro aplikace Dynamics AX do funkce. Doporučujeme následující dvě možnosti, které jsou založeny na složitosti zákazníka v místním prostředí.

**Možnost 1**

Zákazník má malý počet aplikací a jeden řadič domény pro celý místní lokality a plány na převzetí služeb při selhání celé lokality společně. Doporučujeme použít replikace Site Recovery replikovat počítače řadiče domény do sekundární lokality (k dispozici pro scénáře site-to-site a site do Azure).

**Možnost 2**

Zákazník má velký počet aplikací a běží v doménové struktuře služby Active Directory a plány na převzetí služeb při selhání několik aplikací najednou. Doporučujeme, abyste nastavili další řadič domény v lokalitě pro obnovení po havárii (sekundární lokality nebo v Azure).

 Další informace najdete v tématu [zpřístupnit řadiče domény v lokalitě zotavení po havárii](site-recovery-active-directory.md). Pro zbývající část tohoto dokumentu předpokládáme, že řadič domény je k dispozici v lokalitě pro obnovení po havárii.

### <a name="2-set-up-sql-server-replication"></a>2. Nastavení replikace systému SQL Server
Technické pokyny pro doporučená volba pro ochranu na úrovni SQL, najdete v části [aplikace s využitím SQL serveru a Azure Site Recovery replikovat](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Povolení ochrany pro klientské aplikace Dynamics AX a virtuální počítače serveru aplikace objektu
Provedení položky konfigurace Site Recovery na tom, jestli jsou virtuální počítače nasazené na základě [Hyper-V](site-recovery-hyper-v-site-to-azure.md) nebo [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Doporučujeme, abyste nakonfigurovali konzistentních při chybě frekvence až 15 minut.
>

Z následujícího snímku se zobrazuje stav ochrany virtuálních počítačů součást Dynamics ve scénáři VMware Ochrana serveru do Azure.

![Chráněné položky](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Konfigurace sítí
**Konfigurace výpočetního virtuálního počítače a nastavení sítě**

Klient Dynamics AX a virtuální počítače serveru objekt aplikace konfigurace nastavení sítě v Site Recovery, tak, aby sítě virtuálních počítačů získat připojené k síti pro obnovení po havárii správné po převzetí služeb při selhání. Ujistěte se, že síť pro obnovení po havárii pro jednotlivé úrovně služeb je možné je směrovat na úrovni SQL.

Virtuální počítač v replikované položky, které chcete nakonfigurovat nastavení sítě, můžete vybrat, jak je znázorněno na následujícím snímku:

* Pro servery aplikační objekt Server vyberte skupinu dostupnosti správné.

* Pokud používáte statické IP adresy, zadejte IP adresu, která má virtuální počítač trvat, než se **cílová IP adresa** textového pole.

    ![Nastavení sítě ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Vytvoření plánu obnovení

Ve službě Site Recovery k automatizaci procesu převzetí služeb při selhání můžete vytvořit plán obnovení. Přidáte vrstvu aplikace a webové vrstvy v plánu obnovení. Uspořádat do různých skupin tak, aby front-endu ukončí před na úrovni aplikace.

1. Vyberte trezor služby Site Recovery ve vašem předplatném a vyberte **plány obnovení** dlaždici.

2. Vyberte **+ plánu obnovení**a zadejte název.

3. Vyberte **zdroj** a **cílové**. Cílem může být Azure nebo sekundární lokality. Pokud si zvolíte Azure, musíte zadat model nasazení.

    ![Vytvoření plánu obnovení](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Vyberte aplikační Server objektu a klientské virtuální počítače v plánu obnovení a vyberte ✓.

    ![Vybrat položky](./media/site-recovery-dynamics-ax/selectvms.png)

    Příklad plánu obnovení:

    ![Podrobnosti o plánu obnovení](./media/site-recovery-dynamics-ax/recoveryplan.png)

Plán obnovení pro aplikaci Dynamics AX můžete přizpůsobit tak, že přidáte následující kroky. Předchozí snímek ukazuje plán úplné obnovení poté, co přidáte všechny kroky.


* **Postup převzetí služeb při selhání systému SQL Server**: informace o postupu obnovení konkrétní k systému SQL server najdete v tématu [replikace aplikace s využitím SQL serveru a Azure Site Recovery](site-recovery-sql.md).

* **Skupiny převzetí služeb při selhání 1**: převzetí služeb při selhání virtuálních počítačů serveru objektu aplikace.
Ujistěte se, že je vybraný bod obnovení co nejblíže k databázi PIT, ale ne před ho.

* **Skript**: Přidat nástroj pro vyrovnávání zatížení (pouze E-A).
Přidáte skript (přes Azure Automation) po skupiny virtuálních počítačů serveru objekt aplikace se zobrazí na ni přidat nástroj pro vyrovnávání zatížení. Skript můžete použít k provedení této úlohy. Další informace najdete v tématu [přidání nástroje pro vyrovnávání zatížení, zotavení po havárii vícevrstvé aplikace](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **2. skupina převzetí služeb při selhání**: převzetí služeb při selhání Dynamics AX klientské virtuální počítače. Převzetí služeb při selhání virtuální počítače webové vrstvy jako součást plánu obnovení.


### <a name="perform-a-test-failover"></a>Provést testovací převzetí služeb

Další informace týkající se služby Active Directory během testovacího převzetí služeb při selhání najdete v článku doprovodná příručka "Řešení zotavení po havárii služby Active Directory".

Další informace specifické pro systém SQL server během testovacího převzetí služeb při selhání najdete v tématu [aplikace s využitím SQL serveru a Azure Site Recovery replikovat](site-recovery-sql.md).

1. Přejděte na web Azure Portal a vyberte váš trezor Site Recovery.

2. Vyberte plán obnovení, které jsou vytvořené pro Dynamics AX.

3. Vyberte **testovací převzetí služeb při selhání**.

4. Vyberte virtuální síť, chcete-li spustit proces testovacího převzetí služeb při selhání.

5. Poté, co je sekundární prostředí, můžete provádět vaše ověření.

6. Po dokončení ověření vyberte **dokončení ověření** a vyčistit testovací převzetí služeb při selhání prostředí.

Další informace o provádění testovací převzetí služeb při selhání najdete v tématu [testovací převzetí služeb při selhání do Azure v Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Převzetí služeb

1. Přejděte na web Azure Portal a vyberte váš trezor Site Recovery.

2. Vyberte plán obnovení, které jsou vytvořené pro Dynamics AX.

3. Vyberte **převzetí služeb při selhání**a vyberte **převzetí služeb při selhání**.

4. Vyberte cílovou síť a vyberte **✓** zahájíte proces převzetí služeb při selhání.

Další informace převzetí služeb při selhání, naleznete v tématu [převzetí služeb při selhání v Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Navrácení služeb po obnovení

Požadavky na konkrétní k systému SQL Server během navrácení služeb po obnovení najdete v [aplikace s využitím SQL serveru a Azure Site Recovery replikovat](site-recovery-sql.md).

1. Přejděte na web Azure Portal a vyberte váš trezor Site Recovery.

2. Vyberte plán obnovení, které jsou vytvořené pro Dynamics AX.

3. Vyberte **převzetí služeb při selhání**a vyberte **převzetí služeb při selhání**.

4. Vyberte **změnit směr**.

5. Vyberte odpovídající možnosti: synchronizace dat a vytvoření virtuálního počítače.

6. Vyberte **✓** zahájíte proces navrácení služeb po obnovení.


Další informace o provádění navrácení služeb po obnovení najdete v tématu [navrácení služeb po obnovení virtuálních počítačů VMware z Azure do místního](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Souhrn
Pomocí Site Recovery můžete vytvořit kompletní automatizované zotavení po havárii pro vaši aplikaci Dynamics AX. V případě přerušení můžete zahájit převzetí služeb při selhání během několika sekund z libovolného místa a získat aplikaci zprovoznit během několika minut.

## <a name="next-steps"></a>Další postup
Další informace o ochraně firemních procesů s využitím Site Recovery najdete v tématu [jaké úlohy mohu ochránit?](site-recovery-workload.md).
