---
title: Otázky zabezpečení | Microsoft Docs
description: V tomto tématu najdete obecné pokyny pro zabezpečení SQL Server běžících na virtuálním počítači Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.topic: conceptual
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 04634a6efb6c17a823532a29ec273b088a4ad843
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/25/2020
ms.locfileid: "91272391"
---
# <a name="security-considerations-for-sql-server-on-azure-virtual-machines"></a>Požadavky na zabezpečení pro SQL Server v Azure Virtual Machines
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

Toto téma obsahuje obecné pokyny pro zabezpečení, které vám pomůžou navázat zabezpečený přístup k SQL Server instancí na virtuálním počítači Azure (VM).

Azure vyhovuje několika oborovým předpisům a standardům, které vám umožní vytvořit vyhovující řešení s SQL Server běžícím na virtuálním počítači. Informace o dodržování legislativních předpisů s Azure najdete v tématu [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-virtual-machine"></a>Řízení přístupu k virtuálnímu počítači SQL

Při vytváření virtuálního počítače s SQL Server zvažte pečlivé řízení, kdo má přístup k počítači a SQL Server. Obecně platí, že byste měli provést následující akce:

- Omezte přístup k SQL Server jenom na aplikace a klienty, které ho potřebují.
- Dodržujte osvědčené postupy pro správu uživatelských účtů a hesel.

V následujících částech jsou uvedeny návrhy na promyšlení těchto bodů.

## <a name="secure-connections"></a>Zabezpečená připojení

Když vytvoříte virtuální počítač s SQL Server s imagí galerie, možnost **připojení SQL Server** vám umožní vybrat **místní (uvnitř virtuálního počítače)**, **privátní (v rámci Virtual Network)** nebo **veřejné (Internet)**.

![SQL Server připojení](./media/security-considerations-best-practices/sql-vm-connectivity-option.png)

Pro zajištění nejlepšího zabezpečení vyberte nejvíce omezující možnost pro váš scénář. Například pokud používáte aplikaci, která přistupuje k SQL Server na stejném virtuálním počítači, pak je **místní** volba nejbezpečnější. Pokud používáte aplikaci Azure, která vyžaduje přístup k SQL Server, pak **soukromá** zabezpečená komunikace s SQL Server jenom v rámci zadané [virtuální sítě Azure](../../../virtual-network/virtual-networks-overview.md). Pokud požadujete **veřejný** přístup k virtuálnímu počítači s SQL Server (Internet), nezapomeňte podle dalších osvědčených postupů v tomto tématu snížit prostor pro útoky.

Vybrané možnosti portálu používají pravidla zabezpečení příchozí pro [skupinu zabezpečení sítě](../../../active-directory/identity-protection/security-overview.md) (NSG) virtuálního počítače a povolují nebo zakazují síťový provoz na virtuálním počítači. Můžete upravit nebo vytvořit nová příchozí pravidla NSG, která povolí provoz na port SQL Server (standardně 1433). Můžete také zadat konkrétní IP adresy, které můžou komunikovat přes tento port.

![Pravidla skupiny zabezpečení sítě](./media/security-considerations-best-practices/sql-vm-network-security-group-rules.png)

Kromě pravidel NSG, jak omezit síťový provoz, můžete také použít bránu Windows Firewall na virtuálním počítači.

Pokud používáte koncové body s modelem nasazení Classic, odeberte všechny koncové body na virtuálním počítači, pokud je nepoužíváte. Pokyny k používání seznamů ACL s koncovými body najdete v tématu [Správa seznamu ACL na koncovém bodu](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint). To není nutné pro virtuální počítače, které používají Azure Resource Manager.

Nakonec zvažte povolení šifrovaných připojení pro instanci databázového stroje SQL Server na virtuálním počítači Azure. Nakonfigurujte instanci systému SQL Server s podepsaným certifikátem. Další informace najdete v tématu [Povolení šifrovaných připojení k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) a [syntaxi připojovacího řetězce](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="encryption"></a>Šifrování

Spravované disky nabízejí šifrování na straně serveru a Azure Disk Encryption. [Šifrování na straně serveru](/azure/virtual-machines/windows/disk-encryption) poskytuje šifrování v klidovém prostředí a chrání vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. [Azure Disk Encryption](/azure/security/fundamentals/azure-disk-encryption-vms-vmss) používá technologii BITLOCKER nebo dm-crypt a integruje se s Azure Key Vault k šifrování operačního systému a datových disků. 

## <a name="use-a-non-default-port"></a>Použít jiný než výchozí port

Ve výchozím nastavení SQL Server naslouchá na dobře známém portu 1433. Pro zvýšení zabezpečení nakonfigurujte SQL Server, aby naslouchala na jiný než výchozí port, například 1401. Pokud zřizujete obrázek Galerie SQL Server v Azure Portal, můžete tento port zadat v okně **nastavení SQL Server** .

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Pokud to chcete nakonfigurovat po zřízení, máte dvě možnosti:

- V případě Správce prostředků virtuálních počítačů můžete vybrat **zabezpečení** z [prostředku virtuálních počítačů SQL](manage-sql-vm-portal.md#access-the-sql-virtual-machines-resource). Tato možnost umožňuje změnit port.

  ![Změna portu TCP na portálu](./media/security-considerations-best-practices/sql-vm-change-tcp-port.png)

- U klasických virtuálních počítačů nebo u SQL Server virtuálních počítačů, které nebyly zřízené s portálem, můžete port ručně nakonfigurovat připojením k virtuálnímu počítači. Postup konfigurace najdete v tématu [Konfigurace serveru pro naslouchání na specifickém portu TCP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Používáte-li tuto ruční techniku, je také nutné přidat pravidlo brány Windows Firewall, které povoluje příchozí přenosy na daném portu TCP.

> [!IMPORTANT]
> Pokud je port SQL Server otevřený pro veřejná internetová připojení, je vhodné zadat jiný než výchozí port.

Pokud SQL Server naslouchá na portu, který není výchozí, je nutné při připojování zadat port. Představte si třeba situaci, kdy se IP adresa serveru 13.55.255.255 a SQL Server naslouchá na portu 1401. Pokud se chcete připojit k SQL Server, zadali jste `13.55.255.255,1401` v připojovacím řetězci.

## <a name="manage-accounts"></a>Správa účtů

Nechcete, aby útočníci mohli snadno uhodnout názvy účtů nebo hesla. Následující tipy vám pomůžou:

- Vytvořte jedinečný účet místního správce, který se nejmenuje jako **správce**.

- Používejte složitá silná hesla pro všechny vaše účty. Další informace o tom, jak vytvořit silné heslo, najdete v článku o [vytvoření silného](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) hesla.

- Ve výchozím nastavení Azure během SQL Server nastavení virtuálního počítače vybere ověřování systému Windows. Proto je přihlašovací jméno **SA** zakázané a instalační program přiřadí heslo. Doporučujeme, abyste přihlášení **SA** nepoužívali ani nepovolili. Pokud potřebujete přihlášení SQL, použijte jednu z následujících strategií:

  - Vytvořte účet SQL s jedinečným názvem, který má členství **sysadmin** . To můžete provést z portálu povolením **ověřování SQL** během zřizování.

    > [!TIP] 
    > Pokud během zřizování nepovolíte ověřování SQL, musíte ručně změnit režim ověřování na **SQL Server a režim ověřování systému Windows**. Další informace najdete v tématu [Změna režimu ověřování serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Pokud musíte použít přihlašovací jméno **SA** , povolte přihlašovací údaje po zřízení a přiřaďte nové silné heslo.

## <a name="additional-best-practices"></a>Další osvědčené postupy

Kromě postupů popsaných v tomto tématu doporučujeme, abyste provedli kontrolu a implementaci osvědčených postupů zabezpečení z tradičních místních postupů zabezpečení i osvědčených postupů zabezpečení virtuálních počítačů. 

Další informace o místních postupech zabezpečení najdete v tématu požadavky na [zabezpečení pro instalaci SQL Server](/sql/sql-server/install/security-considerations-for-a-sql-server-installation) a [služby Security Center](/sql/relational-databases/security/security-center-for-sql-server-database-engine-and-azure-sql-database). 

Další informace o zabezpečení virtuálních počítačů najdete v tématu [Přehled zabezpečení virtuálních počítačů](/azure/security/fundamentals/virtual-machines-overview).


## <a name="next-steps"></a>Další kroky

Pokud vás zajímá i osvědčené postupy týkající se výkonu, přečtěte si téma [osvědčené postupy výkonu pro SQL Server v Azure Virtual Machines](performance-guidelines-best-practices.md).

Další témata související se spouštěním SQL Server ve virtuálních počítačích Azure najdete v tématu [SQL Server na azure Virtual Machines přehled](sql-server-on-azure-vm-iaas-what-is-overview.md). Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](frequently-asked-questions-faq.md).

