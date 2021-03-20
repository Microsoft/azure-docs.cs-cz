---
title: Zotavení po havárii Dynamics AX s Azure Site Recovery
description: Přečtěte si, jak nastavit zotavení po havárii pro Dynamics AX pomocí Azure Site Recovery
author: sideeksh
manager: rochakm
ms.topic: how-to
ms.date: 11/27/2018
ms.openlocfilehash: dfa3c108d00aeba9c7d42e96e7a40736a087a508
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "86133823"
---
# <a name="set-up-disaster-recovery-for-a-multitier-dynamics-ax-application"></a>Nastavení zotavení po havárii pro aplikaci s více vrstvami Dynamics AX   




 Dynamics AX je jedním z nejoblíbenějších řešení ERP používaných podniky ke standardizaci procesů napříč umístěními, správě prostředků a zjednodušení dodržování předpisů. Vzhledem k tomu, že aplikace je pro organizaci kritická, v případě havárie by měla být aplikace spuštěná v minimálním čase.

V dnešní době neposkytuje Dynamics AX žádné možnosti zotavení po havárii. Dynamics AX se skládá z mnoha součástí serveru, jako je třeba aplikační server Windows, Azure Active Directory, Azure SQL Database, SharePoint Server a služba Reporting Services. Chcete-li spravovat zotavení po havárii každé z těchto komponent ručně, není pouze nákladné, ale také náchylné k chybám.

Tento článek vysvětluje, jak můžete vytvořit řešení zotavení po havárii pro aplikaci Dynamics AX pomocí [Azure Site Recovery](site-recovery-overview.md). Zahrnuje také plánované a neplánované testovací převzetí služeb při selhání pomocí plánu obnovení jedním kliknutím, podporovaných konfigurací a požadavků.



## <a name="prerequisites"></a>Předpoklady

Implementace zotavení po havárii pro aplikaci Dynamics AX pomocí Site Recovery vyžaduje následující předpoklady:

• Nastavte místní nasazení Dynamics AX.

• Vytvoření trezoru Site Recovery v předplatném Azure.

• Pokud je Azure vaším webem pro obnovení, spusťte na virtuálních počítačích Nástroj pro vyhodnocení připravenosti na virtuální počítače Azure. Musí být kompatibilní se službami Azure Virtual Machines a Site Recovery.

## <a name="site-recovery-support"></a>Podpora Site Recovery

Pro účely vytvoření tohoto článku jsme používali virtuální počítače VMware s Dynamics AX 2012 R3 v systému Windows Server 2012 R2 Enterprise. Vzhledem k tomu, že Site Recovery replikace je nezávislá aplikace, očekáváme, že zde uvedená doporučení se budou uchovávat v následujících scénářích.

### <a name="source-and-target"></a>Zdroj a cíl

**Scénář** | **Do sekundární lokality** | **Do Azure**
--- | --- | ---
**Hyper-V** | Yes | Yes
**VMware** | Yes | Yes
**Fyzický server** | Yes | Yes

## <a name="enable-disaster-recovery-of-the-dynamics-ax-application-by-using-site-recovery"></a>Povolení zotavení po havárii aplikace Dynamics AX pomocí Site Recovery
### <a name="protect-your-dynamics-ax-application"></a>Ochrana aplikace Dynamics AX
Aby bylo možné povolit úplnou replikaci a obnovení aplikací, musí být všechny součásti aplikace Dynamics AX chráněny.

### <a name="1-set-up-active-directory-and-dns-replication"></a>1. nastavení replikace Active Directory a DNS

Služba Active Directory je požadována na webu pro zotavení po havárii, aby mohla aplikace Dynamics AX fungovat. Na základě složitosti místního prostředí zákazníka doporučujeme následující dvě možnosti.

**Možnost 1**

Zákazník má malý počet aplikací a jeden řadič domény pro celou místní lokalitu a plánuje převzetí služeb při selhání celé lokality dohromady. Pomocí Site Recovery replikace doporučujeme replikovat počítač řadiče domény do sekundární lokality (platí pro scénáře site-to-site a Site-to-Azure).

**Možnost 2**

Zákazník má velký počet aplikací a provozuje doménovou strukturu služby Active Directory a plánuje převzetí služeb při selhání několika aplikacemi najednou. Doporučujeme nastavit další řadič domény v lokalitě pro obnovení po havárii (sekundární lokalita nebo v Azure).

 Další informace najdete v tématu [nastavení řadiče domény k dispozici na webu pro zotavení po havárii](site-recovery-active-directory.md). Ve zbývající části tohoto dokumentu předpokládáme, že řadič domény je k dispozici na webu pro zotavení po havárii.

### <a name="2-set-up-sql-server-replication"></a>2. nastavení replikace SQL Server
Technické pokyny týkající se Doporučené možnosti ochrany úrovně SQL najdete v tématu věnovaném [replikaci aplikací pomocí SQL Server a Azure Site Recovery](site-recovery-sql.md).

### <a name="3-enable-protection-for-the-dynamics-ax-client-and-application-object-server-vms"></a>3. povolení ochrany pro virtuální počítače klienta Dynamics AX a server aplikačního objektu aplikace
Proveďte relevantní konfiguraci Site Recovery na základě toho, jestli jsou virtuální počítače nasazené na [Hyper-V](./hyper-v-azure-tutorial.md) nebo [VMware](./vmware-azure-tutorial.md).

> [!TIP]
> Doporučujeme nakonfigurovat frekvenci konzistentní vzhledem k selháním na 15 minut.
>

Následující snímek znázorňuje stav ochrany virtuálních počítačů Dynamics-Component ve scénáři ochrany mezi lokalitami VMware a Azure.

![Chráněné položky](./media/site-recovery-dynamics-ax/protecteditems.png)

### <a name="4-configure-networking"></a>4. konfigurace sítě
**Konfigurace výpočetních a síťových nastavení virtuálních počítačů**

Pro virtuální počítače klienta a aplikačního objektu aplikace Dynamics AX nakonfigurujte nastavení sítě v Site Recovery tak, aby se sítě virtuálních počítačů po převzetí služeb při selhání připojily ke správné síti pro obnovení po havárii. Ujistěte se, že síť pro obnovení po havárii pro tyto vrstvy je směrovatelný do vrstvy SQL.

Můžete vybrat virtuální počítač v replikovaných položkách a nakonfigurovat tak nastavení sítě, jak je znázorněno na následujícím snímku:

* Pro servery aplikačního serveru vyberte správnou skupinu dostupnosti.

* Pokud používáte statickou IP adresu, zadejte IP adresu, kterou má virtuální počítač převzít, do textového pole **cílová IP adresa** .

    ![Nastavení sítě](./media/site-recovery-dynamics-ax/vmpropertiesaos1.png)


### <a name="5-create-a-recovery-plan"></a>5. vytvoření plánu obnovení

Můžete vytvořit plán obnovení v Site Recovery pro automatizaci procesu převzetí služeb při selhání. Přidejte vrstvu aplikace a webovou vrstvu do plánu obnovení. Sestavujte je v různých skupinách, aby se front-end ukončil před vrstvou aplikace.

1. Ve svém předplatném vyberte trezor Site Recovery a vyberte dlaždici **plány obnovení** .

2. Vyberte **+ plán obnovení** a zadejte název.

3. Vyberte **zdroj** a **cíl**. Cílem může být Azure nebo sekundární lokalita. Pokud zvolíte Azure, musíte zadat model nasazení.

    ![Vytvoření plánu obnovení](./media/site-recovery-dynamics-ax/recoveryplancreation1.png)

4. Vyberte aplikační objektový server a klientské virtuální počítače pro plán obnovení a pak vyberte ✓.

    ![Vybrat položky](./media/site-recovery-dynamics-ax/selectvms.png)

    Příklad plánu obnovení:

    ![Podrobnosti o plánu obnovení](./media/site-recovery-dynamics-ax/recoveryplan.png)

Můžete přizpůsobit plán obnovení pro aplikaci Dynamics AX přidáním následujících kroků. Po přidání všech kroků se v předchozím snímku zobrazuje kompletní plán obnovení.


* **SQL Server kroky při převzetí služeb při selhání**: informace o krocích obnovení specifických pro SQL Server najdete v tématu [aplikace replikace s SQL Server a Azure Site Recovery](site-recovery-sql.md).

* **Převzetí služeb při selhání skupiny 1**: převzít služby virtuálních počítačů aplikačního objektového serveru
Ujistěte se, že vybraný bod obnovení je co nejblíže k databázi PIT, ale ne před ním.

* **Skript**: přidejte Nástroj pro vyrovnávání zatížení (jenom E-A).
Po přidání nástroje pro vyrovnávání zatížení do něj přidejte skript (prostřednictvím Azure Automation), aby se přidala skupina virtuálních počítačů aplikačního serveru. K provedení této úlohy můžete použít skript. Další informace najdete v tématu [Postup přidání nástroje pro vyrovnávání zatížení pro zotavení po havárii s více vrstvami aplikací](https://azure.microsoft.com/blog/cloud-migration-and-disaster-recovery-of-load-balanced-multi-tier-applications-using-azure-site-recovery/).

* **Převzetí služeb při selhání – skupina 2**: převzetí služeb při selhání virtuálních počítačů klienta Dynamics AX Virtuální počítače webové vrstvy převezmete za součást plánu obnovení.


### <a name="perform-a-test-failover"></a>Provedení testovacího převzetí služeb při selhání

Další informace specifické pro službu Active Directory během testovacího převzetí služeb při selhání najdete v doprovodné příručce řešení pro zotavení po havárii služby Active Directory.

Další informace týkající se SQL serveru během testovacího převzetí služeb při selhání najdete v tématu věnovaném [replikaci aplikací pomocí SQL Server a Azure Site Recovery](site-recovery-sql.md).

1. Přejít na Azure Portal a vyberte svůj trezor Site Recovery.

2. Vyberte plán obnovení vytvořený pro aplikaci Dynamics AX.

3. Vyberte **Testovací převzetí služeb při selhání**.

4. Vyberte virtuální síť a spusťte proces testovacího převzetí služeb při selhání.

5. Po ukončení sekundárního prostředí můžete provádět své ověření.

6. Po dokončení ověření vyberte **ověřování dokončeno** a prostředí testovacího převzetí služeb při selhání se vyčistí.

Další informace o provádění testovacího převzetí služeb při selhání najdete v tématu [Test převzetí služeb při selhání do Azure v Site Recovery](site-recovery-test-failover-to-azure.md).

### <a name="perform-a-failover"></a>Provedení převzetí služeb při selhání

1. Přejít na Azure Portal a vyberte svůj trezor Site Recovery.

2. Vyberte plán obnovení vytvořený pro aplikaci Dynamics AX.

3. Vyberte **převzetí služeb při selhání** a vyberte **převzít**.

4. Vyberte cílovou síť a vyberte **✓** pro spuštění procesu převzetí služeb při selhání.

Další informace o převzetí služeb při selhání najdete [v tématu převzetí služeb při selhání v Site Recovery](site-recovery-failover.md).

### <a name="perform-a-failback"></a>Navrácení služeb po obnovení

Informace specifické pro SQL Server během navrácení služeb po obnovení najdete v tématu věnovaném [replikaci aplikací pomocí SQL Server a Azure Site Recovery](site-recovery-sql.md).

1. Přejít na Azure Portal a vyberte svůj trezor Site Recovery.

2. Vyberte plán obnovení vytvořený pro aplikaci Dynamics AX.

3. Vyberte **převzetí služeb při selhání** a vyberte **převzít**.

4. Vyberte možnost **změnit směr**.

5. Vyberte vhodné možnosti: synchronizace dat a vytváření virtuálních počítačů.

6. Vyberte **✓** a spusťte proces navrácení služeb po obnovení.


Další informace o navrácení služeb po obnovení najdete v tématu [navrácení služeb po obnovení VMware z Azure do místního](./vmware-azure-failback.md)prostředí.

## <a name="summary"></a>Souhrn
Pomocí Site Recovery můžete pro aplikaci Dynamics AX vytvořit úplný automatizovaný plán zotavení po havárii. V případě přerušení můžete převzít služby při selhání v řádu sekund odkudkoli a spustit aplikaci v řádu minut.

## <a name="next-steps"></a>Další kroky
Další informace o ochraně podnikových úloh pomocí Site Recovery najdete v tématu [Jaké úlohy je možné chránit?](site-recovery-workload.md).
