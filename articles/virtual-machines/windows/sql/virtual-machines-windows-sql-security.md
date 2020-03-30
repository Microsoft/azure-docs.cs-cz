---
title: Důležité informace o zabezpečení pro SQL Server v Azure | Dokumenty společnosti Microsoft
description: Toto téma obsahuje obecné pokyny pro zabezpečení SQL Serveru spuštěného ve virtuálním počítači Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: f5ea0ddff38532b119d8d984f2dabd6d898b44a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77031352"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Informace o zabezpečení pro SQL Server v Azure Virtual Machines

Toto téma obsahuje celkové pokyny pro zabezpečení, které pomáhají vytvořit zabezpečený přístup k instancím SQL Serveru ve virtuálním počítači Azure (VM).

Azure splňuje několik oborových předpisů a standardů, které vám můžou umožnit vytvořit kompatibilní řešení se serverem SQL Server spuštěným ve virtuálním počítači. Informace o dodržování předpisů v Azure najdete v [tématu Azure Trust Center](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Řízení přístupu k virtuálnímu virtuálnímu montovně SQL

Při vytváření virtuálního počítače SQL Server, zvažte, jak pečlivě řídit, kdo má přístup k počítači a SQL Server. Obecně byste měli provést následující kroky:

- Omezte přístup k serveru SQL Server pouze na aplikace a klienty, kteří jej potřebují.
- Postupujte podle doporučených postupů pro správu uživatelských účtů a hesel.

V následujících částech jsou uvedeny návrhy na promyšlení těchto bodů.

## <a name="secure-connections"></a>Zabezpečená připojení

Když vytvoříte virtuální počítač SQL Server s obrazem galerie, možnost **Připojení serveru SQL Server** umožňuje výběr Local **(uvnitř virtuálního počítače),** **Soukromé (v rámci virtuální sítě)** nebo **Veřejné (Internet)**.

![Připojení k serveru SQL Server](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Chcete-li dosáhnout nejlepšího zabezpečení, zvolte pro váš scénář nejrestriktivnější možnost. Například pokud používáte aplikaci, která přistupuje sql server na stejném virtuálním počítači, pak **Local** je nejbezpečnější volbou. Pokud používáte aplikaci Azure, která vyžaduje přístup k SQL Serveru, pak **Privátní** zabezpečuje komunikaci na SQL Server pouze v rámci zadané [virtuální sítě Azure](../../../virtual-network/virtual-networks-overview.md). Pokud požadujete **veřejný** (internet) přístup k virtuálnímu počítači SQL Server, pak se ujistěte, postupujte podle dalších doporučených postupů v tomto tématu snížit oblast útoku.

Vybrané možnosti na portálu používají příchozí pravidla zabezpečení ve [skupině zabezpečení sítě](../../../virtual-network/security-overview.md) virtuálního počítače (NSG) k povolení nebo odepření síťového provozu ve virtuálním počítači. Můžete upravit nebo vytvořit nová příchozí pravidla sítě zabezpečení sítě a povolit provoz na portu serveru SQL Server (výchozí 1433). Můžete také zadat konkrétní adresy IP, které mohou komunikovat přes tento port.

![Pravidla skupiny zabezpečení sítě](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Kromě pravidel sítě zabezpečení sítě k omezení síťového provozu můžete také použít bránu Windows Firewall ve virtuálním počítači.

Pokud používáte koncové body s klasickým modelem nasazení, odeberte všechny koncové body ve virtuálním počítači, pokud je nepoužíváte. Pokyny k použití acl s koncovými body naleznete [v tématu Správa acl na koncovém bodu](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). To není nutné pro virtuální chody, které používají Správce prostředků.

Nakonec zvažte povolení šifrovaných připojení pro instanci databázového stroje SQL Server ve vašem virtuálním počítači Azure. Konfigurace instance serveru SQL s podepsaným certifikátem. Další informace naleznete v [tématu Povolení šifrovaných připojení k databázovém stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) a [syntaxi připojovacího řetězce](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="encryption"></a>Šifrování

Spravované disky nabízejí šifrování na straně serveru a šifrování disku Azure. [Šifrování na straně serveru](/azure/virtual-machines/windows/disk-encryption) poskytuje šifrování v klidovém stavu a chrání vaše data, aby splňovala závazky organizace v oblasti zabezpečení a dodržování předpisů. [Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) používá technologii Bitlocker nebo DM-Crypt a integruje se s Azure Key Vault k šifrování operačního systému i datových disků. 

## <a name="use-a-non-default-port"></a>Použití jiného než výchozího portu

Ve výchozím nastavení SQL Server naslouchá na dobře známém portu 1433. Chcete-li zvýšit zabezpečení, nakonfigurujte sql server pro naslouchání na nevýchozím portu, například 1401. Pokud zřídíte image galerie SQL Serveru na webu Azure Portal, můžete tento port zadat v okně **nastavení serveru SQL Server.**

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Chcete-li nakonfigurovat po zřizování, máte dvě možnosti:

- Pro virtuální počítače Správce prostředků můžete vybrat **zabezpečení** z [prostředku virtuálních počítačů SQL](virtual-machines-windows-sql-manage-portal.md#access-the-sql-virtual-machines-resource). To poskytuje možnost změnit port.

  ![Změna portu TCP na portálu](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Pro klasické virtuální počítače nebo pro virtuální počítače SQL Server, které nebyly zřízeny s portálem, můžete ručně nakonfigurovat port vzdáleným připojením k virtuálnímu počítače. Postup konfigurace naleznete v [tématu Konfigurace serveru pro naslouchání na konkrétním portu TCP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Pokud použijete tuto ruční techniku, je také nutné přidat pravidlo brány Windows Firewall, které povolí příchozí přenosy na tomto portu TCP.

> [!IMPORTANT]
> Určení jiného než výchozího portu je vhodné, pokud je port serveru SQL Server otevřený veřejným připojením k Internetu.

Při sql server naslouchá na non-výchozí port, je nutné zadat port při připojení. Zvažte například scénář, kde je adresa IP serveru 13.55.255.255 a SQL Server naslouchá na portu 1401. Chcete-li se připojit k `13.55.255.255,1401` serveru SQL Server, zadejte v připojovacím řetězci.

## <a name="manage-accounts"></a>Správa účtů

Nechcete, aby útočníci snadno uhodli názvy účtů nebo hesla. Nápovědou vám pomohou následující tipy:

- Vytvořte jedinečný účet místního správce s názvem **Administrator**.

- Používejte složitá silná hesla pro všechny své účty. Další informace o vytvoření silného hesla naleznete v článku [Vytvoření silného hesla.](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password)

- Ve výchozím nastavení Azure vybere ověřování systému Windows během instalace virtuálního počítače SQL Serveru. Proto je zakázáno přihlášení **sa** a heslo je přiřazeno nastavením. Doporučujeme, aby přihlášení **sa** by neměla být použita nebo povolena. Pokud musíte mít přihlášení SQL, použijte jednu z následujících strategií:

  - Vytvořte účet SQL s jedinečným názvem, který má členství **sysadmin.** Můžete to provést z portálu povolením **ověřování SQL** během zřizování.

    > [!TIP] 
    > Pokud během zřizování nepovolíte ověřování SQL, je nutné ručně změnit režim ověřování na **SQL Server a režim ověřování systému Windows**. Další informace naleznete v [tématu Change Server Authentication Mode](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Pokud je nutné použít přihlášení **SA,** povolit přihlášení po zřízení a přiřadit nové silné heslo.

## <a name="additional-best-practices"></a>Další osvědčené postupy

Kromě postupů popsaných v tomto tématu doporučujeme zkontrolovat a implementovat osvědčené postupy zabezpečení z tradičních místních postupů zabezpečení a také z osvědčených postupů zabezpečení virtuálních strojů. 

Další informace o místních postupech zabezpečení naleznete v [tématu Důležité informace o zabezpečení instalace serveru SQL Server](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) a Centra [zabezpečení](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database). 

Další informace o zabezpečení virtuálních počítačů najdete v tématu [přehled zabezpečení virtuálních počítačů](/azure/security/fundamentals/virtual-machines-overview).


## <a name="next-steps"></a>Další kroky

Pokud vás také zajímají doporučené postupy týkající se výkonu, přečtěte si informace [o doporučených postupech výkonu pro SQL Server ve virtuálních počítačích Azure](virtual-machines-windows-sql-performance.md).

Další témata související se spuštěním SQL Serveru ve virtuálních počítačích Azure najdete v tématu [SQL Server ve virtuálních počítačích Azure .](virtual-machines-windows-sql-server-iaas-overview.md) Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).

