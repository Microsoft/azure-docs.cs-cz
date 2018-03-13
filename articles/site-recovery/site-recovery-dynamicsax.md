---
title: "Pomocí Azure Site Recovery replikovat vícevrstvé nasazení Dynamics AX | Microsoft Docs"
description: "Tento článek popisuje, jak se budou replikovat a chránit Dynamics AX pomocí Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: asgang
manager: rochakm
editor: 
ms.assetid: 9126f5e8-e9ed-4c31-b6b4-bf969c12c184
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/09/2018
ms.author: asgang
ms.openlocfilehash: b390f6c62a6ddf8c800f79b42a36dac2c4f4c908
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/12/2018
---
# <a name="replicate-a-multitier-dynamics-ax-application-by-using-azure-site-recovery"></a>Pomocí Azure Site Recovery replikovat vícevrstvých aplikací Dynamics AX

## <a name="overview"></a>Přehled


 Dynamics AX je jednou z nejčastěji používané řešení ERP používané podniky standardizovat procesy ve umístěních, spravovat prostředky a zjednodušit dodržování předpisů. Protože aplikace je důležité pro organizaci, v případě havárie, aplikace musí být spuštěná ve minimální hodnota času.

V současné době Dynamics AX neposkytuje žádné po havárii se na poli Možnosti obnovení. Dynamics AX se skládá z mnoha součásti serveru, například Server objekt aplikace systému Windows, Azure Active Directory, Azure SQL Database, SharePoint Server a služby Reporting Services. Ke správě po havárii obnovení každou z těchto součástí ručně není pouze nákladná, ale také chyby, které jsou náchylné k chybám.

Tento článek vysvětluje, jak můžete vytvořit řešení zotavení po havárii pro vaši aplikaci Dynamics AX pomocí [Azure Site Recovery](site-recovery-overview.md). Také vysvětluje testovací plánované/neplánované převzetí služeb při selhání pomocí plán obnovení jedním kliknutím, požadavky a podporované konfigurace.



## <a name="prerequisites"></a>Požadavky

Implementace zotavení po havárii pro aplikaci Dynamics AX pomocí Site Recovery vyžaduje splnění následujících předpokladů:

• Nastavit místní nasazení Dynamics AX.

• Vytvoření trezoru Site Recovery v předplatné Azure.

• Pokud je váš web obnovení Azure spustit nástroj hodnocení připravenosti virtuální počítač Azure na virtuálních počítačích. Musí být kompatibilní se službami Azure Virtual Machines a Site Recovery.

## <a name="site-recovery-support"></a>Podpora pro obnovení lokality

Pro účely vytváření tohoto článku, můžeme použít virtuální počítače VMware s Dynamics AX 2012 R3 na Windows Server 2012 R2 Enterprise. Protože replikace obnovení lokality je nezávislá na aplikace, Očekáváme, že doporučení uvedená tady k uchování pro následující scénáře.

### <a name="source-and-target"></a>Zdroj a cíl

**Scénář** | **Sekundární lokality** | **Do Azure**
--- | --- | ---
**Hyper-V** | Ano | Ano
**VMware** | Ano | Ano
**Fyzický server** | Ano | Ano

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Povolit obnovení po havárii aplikace Dynamics AX pomocí Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Ochrana aplikace Dynamics AX
Chcete-li aplikace dokončena a replikace a obnovení, musí být chráněny jednotlivé komponenty Dynamics AX.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Nastavení replikace služby Active Directory a DNS

Na lokalitě pro obnovení po havárii pro aplikaci Dynamics AX do funkce se vyžaduje služby Active Directory. Doporučujeme následující dvě možnosti, které jsou založené na složitosti zákazníka v místním prostředí.

**možnost 1**

Zákazník má malý počet aplikací a jeden řadič domény pro celou lokalitu na místě a plány na převzetí služeb při selhání celá lokalita společně. Doporučujeme použít replikace Site Recovery replikovat počítače řadiče domény do sekundární lokality (platí pro scénáře site-to-site a site Azure).

**Možnost 2**

Zákazník má velký počet aplikací a běží doménové struktury služby Active Directory a plány na převzetí služeb při selhání několik aplikací najednou. Doporučujeme, abyste nastavili další řadič domény v lokalitě pro obnovení po havárii (sekundární lokality nebo v Azure).

 Další informace najdete v tématu [zpřístupnit řadič domény v lokalitě pro obnovení po havárii](site-recovery-active-directory.md). Pro zbývající část tohoto dokumentu předpokládáme, že řadič domény je k dispozici v lokalitě pro obnovení po havárii.

### <a name="2-set-up-sql-server-replication"></a>2. Nastavení replikace systému SQL Server
Technické pokyny na doporučené možnosti pro ochranu vrstvě SQL najdete v tématu [replikovat aplikace s SQL serverem a Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Povolit ochranu pro Dynamics AX klienta a virtuální počítače serveru aplikace objektu
Provést příslušné konfigurace Site Recovery na tom, zda jsou virtuální počítače nasazené na základě [technologie Hyper-V](site-recovery-hyper-v-site-to-azure.md) nebo [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Doporučujeme vám, že nakonfigurujete konzistentní při selhání četnost 15 minut.
>

Následující snímek ukazuje stav ochrany součást Dynamics virtuálních počítačů v případě ochrany lokality Azure VMware.

![Chráněné položky](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Konfigurace sítí
**Konfigurace virtuálního počítače výpočetních a síťových nastavení**

Pro klienta Dynamics AX a virtuálních počítačů aplikace objekt serveru konfigurace nastavení sítě v Site Recovery, tak, aby získat sítě virtuálních počítačů připojený k síti pro obnovení po havárii správné po převzetí služeb při selhání. Zajistěte, aby byl síti pro obnovení po havárii pro tyto vrstvy směrovat vrstvě SQL.

Virtuální počítač v replikované položky konfigurace nastavení sítě, můžete vybrat, jak ukazuje následující snímek:

* U serverů, aplikační objekt Server vyberte skupiny dostupnosti správný.

* Pokud používáte statickou IP adresu, zadejte IP, který chcete virtuální počítač má provést **cílová IP adresa** textové pole.

    ![Nastavení sítě ](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png).


### <a name="5-create-a-recovery-plan"></a>5. Vytvoření plánu obnovení

Ve službě Site Recovery pro automatizaci procesu převzetí služeb při selhání můžete vytvořit plán obnovení. Přidejte vrstvou aplikaci a webovou vrstvu v plánu obnovení. Pořadí je v různých skupinách, aby front-endu ukončí před vrstvy aplikace.

1. Vyberte trezor Site Recovery v rámci vašeho předplatného a vyberte **plány obnovení** dlaždici.

2. Vyberte **+ plán obnovení**a zadejte název.

3. Vyberte **zdroj** a **cíl**. Cílem může být Azure nebo sekundární lokality. Pokud si zvolíte Azure, musíte zadat modelu nasazení.

    ![Vytvoření plánu obnovení](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Vyberte objekt aplikační Server a klientských virtuálních počítačů pro plán obnovení a vyberte ✓.

    ![Vyberte položky](./media/site-recovery-dynamics-ax/selectvms.png)

    Příklad plánu obnovení:

    ![Podrobnosti o plánu obnovení](./media/site-recovery-dynamics-ax/recoveryplan.png)

Plán obnovení pro aplikaci Dynamics AX můžete přizpůsobit přidáním následujících kroků. Předchozí snímek zobrazuje plán dokončení obnovení po přidání všech kroků.


* **Postup převzetí služeb při selhání systému SQL Server**: informace o krocích obnovení konkrétní k systému SQL server najdete v tématu [replikace aplikace s SQL serverem a Azure Site Recovery](site-recovery-sql.md).

* **Převzetí služeb při selhání skupiny 1**: převzít služby virtuálních počítačů serveru objekt aplikace.
Ujistěte se, že je vybraný bod obnovení co nejblíže k databázi PIT, ale ne před jeho.

* **Skript**: Přidat nástroj pro vyrovnávání zatížení (pouze E-A).
Přidejte skript (přes Azure Automation) po skupině virtuální počítač Server objekt aplikace zobrazí, můžete do ní přidejte služby Vyrovnávání zatížení. K provedení této úlohy můžete použít skript. Další informace najdete v tématu [jak přidat nástroj pro vyrovnávání zatížení pro zotavení po havárii vícevrstvých aplikací](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **2. skupina převzetí služeb při selhání**: převzít služby Dynamics AX klientské virtuální počítače. Při selhání se webová úroveň virtuální počítače jako součást plánu obnovení.


### <a name="perform-a-test-failover"></a>Provedení testu převzetí služeb

Další informace specifické pro služby Active Directory během testovacího převzetí služeb při selhání najdete v části "Řešení zotavení po havárii služby Active Directory" doprovodný průvodce.

Další informace specifické pro systém SQL server během testovacího převzetí služeb při selhání najdete v tématu [replikovat aplikace s SQL serverem a Azure Site Recovery](site-recovery-sql.md).

1. Přejděte na portál Azure a vyberte svůj trezor Site Recovery.

2. Vyberte plán obnovení, které jsou vytvořené pro Dynamics AX.

3. Vyberte **testovací převzetí služeb při selhání**.

4. Vyberte virtuální síť, ke spuštění procesu testovací převzetí služeb při selhání.

5. Po sekundární prostředí zapnutý, můžete provést vaše ověření.

6. Po dokončení se k ověření, vyberte **dokončení ověření** a vyčištění prostředí testovacího převzetí služeb při selhání.

Další informace o provádění převzetí služeb při selhání najdete v tématu [testovací převzetí služeb při selhání do Azure ve službě Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Provedení převzetí služeb při selhání

1. Přejděte na portál Azure a vyberte svůj trezor Site Recovery.

2. Vyberte plán obnovení, které jsou vytvořené pro Dynamics AX.

3. Vyberte **převzetí služeb při selhání**a vyberte **převzetí služeb při selhání**.

4. Vyberte cílové síti a vyberte **✓** zahájíte proces převzetí služeb při selhání.

Další informace o provádění převzetí služeb při selhání najdete v tématu [převzetí služeb při selhání ve službě Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Provést navrácení služeb po obnovení

Důležité informace o konkrétní k systému SQL Server během navrácení služeb po obnovení, najdete v části [replikovat aplikace s SQL serverem a Azure Site Recovery](site-recovery-sql.md).

1. Přejděte na portál Azure a vyberte svůj trezor Site Recovery.

2. Vyberte plán obnovení, které jsou vytvořené pro Dynamics AX.

3. Vyberte **převzetí služeb při selhání**a vyberte **převzetí služeb při selhání**.

4. Vyberte **změnit směr**.

5. Vyberte požadované možnosti: synchronizace dat a vytvoření virtuálního počítače.

6. Vyberte **✓** ke spuštění procesu navrácení služeb po obnovení.


Další informace o provádění navrácení služeb po obnovení najdete v tématu [navrácení služeb po obnovení virtuálních počítačů VMware z Azure do místní](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Souhrn
Pomocí Site Recovery můžete vytvořit plán obnovení po havárii dokončení automatizované pro vaši aplikaci Dynamics AX. V případě přerušení můžete spustit převzetí služeb při selhání během několika sekund z libovolného místa a zprovoznění aplikace v minutách.

## <a name="next-steps"></a>Další postup
Další informace o ochraně podnikové úlohy pomocí Site Recovery najdete v tématu [jaké úlohy mohu ochránit?](site-recovery-workload.md).
