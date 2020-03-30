---
title: Zotavení po havárii dynamics AX s azure site recovery
description: Přečtěte si, jak nastavit zotavení po havárii pro Dynamics AX pomocí Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: 0b32f00374aa8ce6c41415e28f319e3e7d5abddb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75941596"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Nastavení zotavení po havárii pro vícevrstvou aplikaci Dynamics AX   




 Dynamics AX je jedním z nejoblíbenějších ERP řešení používaných podniky ke standardizaci procesů napříč místy, správě zdrojů a zjednodušení dodržování předpisů. Vzhledem k tomu, že aplikace je kritická pro organizaci, v případě havárie by měla být aplikace spuštěna v minimálním čase.

Dynamics AX dnes neposkytuje žádné možnosti zotavení po havárii imperací. Dynamics AX se skládá z mnoha součástí serveru, jako je například Windows Application Object Server, Azure Active Directory, Azure SQL Database, SharePoint Server a Reporting Services. Chcete-li spravovat zotavení po havárii každé z těchto součástí ručně je nejen nákladné, ale také náchylné k chybám.

Tento článek vysvětluje, jak můžete vytvořit řešení zotavení po havárii pro vaši aplikaci Dynamics AX pomocí [Azure Site Recovery](site-recovery-overview.md). Zahrnuje také plánované/neplánované převzetí služeb při selhání pomocí plánu obnovení jedním kliknutím, podporovaných konfigurací a předpokladů.



## <a name="prerequisites"></a>Požadavky

Implementace zotavení po havárii pro aplikaci Dynamics AX pomocí site recovery vyžaduje následující předpoklady:

• Nastavení místního nasazení Dynamics AX.

• Vytvořte trezor obnovení webu v předplatném Azure.

• Pokud je Azure vaším místem pro obnovení, spusťte nástroj azure virtual machine readiness assessment na virtuálních počítačích. Musí být kompatibilní se službami Azure Virtual Machines a Site Recovery.

## <a name="site-recovery-support"></a>Podpora Site Recovery

Pro účely vytvoření tohoto článku jsme v systému Windows Server 2012 R2 Enterprise použili virtuální počítače VMware s dynamics AX 2012 R3. Vzhledem k tomu, že replikace obnovení sítě je aplikace nezávislá, očekáváme, že doporučení zde k dispozici pro následující scénáře.

### <a name="source-and-target"></a>Zdroj a cíl

**Scénář** | **Do sekundární lokality** | **Do Azure**
--- | --- | ---
**Hyper-V** | Ano | Ano
**Vmware** | Ano | Ano
**Fyzický server** | Ano | Ano

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Povolení zotavení po havárii aplikace Dynamics AX pomocí site recovery
### <a name="protect-your-dynamics-ax-application"></a>Ochrana aplikace Dynamics AX
Chcete-li povolit úplnou replikaci a obnovení aplikace, musí být každá součást aplikace Dynamics AX chráněna.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. Nastavení replikace služby Active Directory a DNS

Služba Active Directory je v lokalitě pro zotavení po havárii vyžadována, aby aplikace Dynamics AX fungovala. Doporučujeme následující dvě možnosti na základě složitosti místního prostředí zákazníka.

**Možnost 1**

Zákazník má malý počet aplikací a jeden řadič domény pro celou místní lokalitu a plánuje společně převést na služby při selhání celé lokality. Doporučujeme použít replikaci site recovery k replikaci počítače řadiče domény do sekundární lokality (platí pro scénáře lokality k lokalitě i mezi lokalitami.

**Možnost č. 2**

Zákazník má velký počet aplikací a je spuštěna doménové struktury služby Active Directory a plánuje převzetí služeb při selhání několik aplikací najednou. Doporučujeme nastavit další řadič domény v lokalitě pro zotavení po havárii (sekundární lokalita nebo v Azure).

 Další informace naleznete [v tématu Zpřístupnění řadiče domény v lokalitě pro zotavení po havárii](site-recovery-active-directory.md). Pro zbytek tohoto dokumentu předpokládáme, že řadič domény je k dispozici v lokalitě pro zotavení po havárii.

### <a name="2-set-up-sql-server-replication"></a>2. Nastavení replikace serveru SQL Server
Technické pokyny k doporučené možnosti ochrany vrstvy SQL najdete v tématu [Replikace aplikací pomocí SQL Server a Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. Povolení ochrany pro virtuální servery klienta Dynamics AX a aplikačního objektového serveru
Proveďte příslušnou konfiguraci site recovery na základě toho, jestli jsou virtuální počítače nasazené na [technologie Hyper-V](site-recovery-hyper-v-site-to-azure.md) nebo [VMware](site-recovery-vmware-to-azure.md).

> [!TIP]
> Doporučujeme nakonfigurovat frekvenci konzistentní s havárií na 15 minut.
>

Následující snímek ukazuje stav ochrany virtuálních počítačů komponenty Dynamics ve scénáři ochrany v lokalitě VMware azure.

![Chráněné položky](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. Konfigurace sítě
**Konfigurace výpočetního a síťového nastavení virtuálního počítače**

Pro virtuální počítače klienta Dynamics AX a server aplikačního objektu nakonfigurujte nastavení sítě v site recovery tak, aby se sítě virtuálních zařízení po převzetí služeb při selhání připojily ke správné síti zotavení po havárii. Ujistěte se, že síť pro zotavení po havárii pro tyto úrovně je směrovatelná na úroveň SQL.

Virtuální počítače v replikovaných položkách můžete vybrat a nakonfigurovat nastavení sítě, jak je znázorněno na následujícím snímku:

* U serverů aplikačního objektového serveru vyberte správnou sadu dostupnosti.

* Pokud používáte statickou IP adresu, zadejte ip adresu, kterou má virtuální montovna převzít v textovém poli **Cílová IP** adresa.

    ![Nastavení sítě](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. Vytvoření plánu obnovy

V obnovení webu můžete vytvořit plán obnovení a automatizovat proces převzetí služeb při selhání. Přidejte úroveň aplikace a webovou vrstvu do plánu obnovení. Objednejte je v různých skupinách tak, aby front-end vypnout před vrstvou aplikace.

1. V předplatném vyberte trezor Site Recovery a vyberte dlaždici **Plány obnovení.**

2. Vyberte **+ Plán obnovení**a zadejte název.

3. Vyberte **zdroj** a **cíl**. Cíl může být Azure nebo sekundární lokalita. Pokud zvolíte Azure, musíte zadat model nasazení.

    ![Vytvoření plánu obnovení](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Vyberte aplikační objektový server a virtuální chod klienta pro plán obnovení a vyberte ✓.

    ![Vybrat položky](./media/site-recovery-dynamics-ax/selectvms.png)

    Příklad plánu obnovení:

    ![Podrobnosti o plánu obnovení](./media/site-recovery-dynamics-ax/recoveryplan.png)

Plán obnovení aplikace Dynamics AX můžete přizpůsobit přidáním následujících kroků. Předchozí snímek zobrazuje úplný plán obnovení po přidání všech kroků.


* **Kroky převzetí služeb při selhání serveru SQL Server**: Informace o krocích obnovení specifických pro server SQL naleznete v [tématu Replikace aplikací s SQL Server a Obnovení webu Azure](site-recovery-sql.md).

* **Skupina 1 převzetí služeb při selhání**: Převzetí služeb při selhání virtuálních měn aplikačního objektového serveru.
Ujistěte se, že vybraný bod obnovení je co nejblíže k databázi PIT, ale ne před ním.

* **Skript**: Přidejte balancer (pouze E-A).
Přidejte skript (přes Azure Automation) po skupině virtuálních zařízení aplikačního objektového serveru přijde k přidání vyrovnávání zatížení k němu. K tomuto úkolu můžete použít skript. Další informace naleznete v [tématu Jak přidat systém vyrovnávání zatížení pro vícevrstvé zotavení po havárii aplikace](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Skupina s podporou převzetí služeb při selhání 2**: Převzetí služeb při selhání virtuálních virtuálních připojení klienta Dynamics AX. Převzetí služeb při selhání virtuálních stránek webové vrstvy jako součást plánu obnovení.


### <a name="perform-a-test-failover"></a>Provedení zkušebního převzetí služeb při selhání

Další informace specifické pro službu Active Directory během převzetí služeb při selhání testu naleznete v průvodci doprovodnou příručkou řešení zotavení po havárii služby Active Directory.

Další informace specifické pro SQL server během převzetí služeb při selhání testu naleznete v [tématu Replikace aplikací pomocí serveru SQL Server a obnovení webu Azure](site-recovery-sql.md).

1. Přejděte na portál Azure a vyberte trezor obnovení webu.

2. Vyberte plán obnovení vytvořený pro dynamics AX.

3. Vyberte **Testovací převzetí služeb při selhání**.

4. Vyberte virtuální síť a spusťte proces převzetí služeb při selhání testu.

5. Po sekundární prostředí je nahoru, můžete provést ověření.

6. Po dokončení ověření vyberte **ověření dokončena** a testovací prostředí převzetí služeb při selhání je vyčištěno.

Další informace o provádění testu převzetí služeb při selhání, najdete v [tématu Test převzetí služeb při selhání na Azure v site recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Provedení převzetí služeb při selhání

1. Přejděte na portál Azure a vyberte trezor obnovení webu.

2. Vyberte plán obnovení vytvořený pro dynamics AX.

3. Vyberte **možnost Převzetí služeb při selhání**a možnost Převzetí služeb při **selhání**.

4. Vyberte cílovou síť a **vyberte ✓** pro spuštění procesu převzetí služeb při selhání.

Další informace o převzetí služeb při selhání naleznete v tématu [Převzetí služeb při selhání v tématu Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Navrácení služeb po obnovení

Důležité informace specifické pro SQL Server během navrácení služeb po obnovení naleznete v [tématu Replikace aplikací s SQL Server a Azure Site Recovery](site-recovery-sql.md).

1. Přejděte na portál Azure a vyberte trezor obnovení webu.

2. Vyberte plán obnovení vytvořený pro dynamics AX.

3. Vyberte **možnost Převzetí služeb při selhání**a možnost Převzetí služeb při **selhání**.

4. Vyberte **Změnit směr**.

5. Vyberte příslušné možnosti: synchronizace dat a vytváření virtuálních zařízení.

6. Výběrem **možnosti ✓** spustíte proces navrácení služeb po selhání.


Další informace o navrácení služeb po službě navrácení služeb po selhání najdete v tématu [Navrácení služeb po selhání virtuální počítače VMware z Azure do místního](site-recovery-failback-azure-to-vmware.md).

## <a name="summary"></a>Souhrn
Pomocí site recovery můžete vytvořit kompletní automatizovaný plán zotavení po havárii pro vaši aplikaci Dynamics AX. V případě přerušení můžete zahájit převzetí služeb při selhání během několika sekund z libovolného místa a získat aplikaci do provozu během několika minut.

## <a name="next-steps"></a>Další kroky
Další informace o ochraně podnikových úloh pomocí site recovery najdete v [tématu Jaké úlohy mohu chránit?](site-recovery-workload.md).
