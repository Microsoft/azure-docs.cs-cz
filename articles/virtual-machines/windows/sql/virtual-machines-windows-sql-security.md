---
title: Důležité informace o zabezpečení pro Server SQL v Azure | Microsoft Docs
description: Toto téma obsahuje obecné pokyny pro zabezpečení SQL Server běžící virtuálním počítači Azure.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
editor: ''
tags: azure-service-management
ms.assetid: d710c296-e490-43e7-8ca9-8932586b71da
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 03/23/2018
ms.author: jroth
ms.openlocfilehash: bba9f62a78dea4db1d88f877029796739b023e46
ms.sourcegitcommit: b6319f1a87d9316122f96769aab0d92b46a6879a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/20/2018
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Informace o zabezpečení pro SQL Server v Azure Virtual Machines

Toto téma obsahuje celkový pokyny pro zabezpečení, které pomáhají vytvořit zabezpečený přístup k instance systému SQL Server v Azure virtuální počítač (VM).

Azure v souladu se několik oborové směrnice brát a standardů, které můžete povolit sestavení řešení kompatibilní se systémem SQL Server běží na virtuálním počítači. Informace o dodržování legislativní předpisů s Azure najdete v tématu [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Řízení přístupu k virtuálnímu počítači SQL

Při vytváření virtuálního počítače systému SQL Server, zvážit, jakým způsobem pečlivě řízení, kdo má přístup k počítači a k systému SQL Server. Obecně platí proveďte následující:

- Omezte přístup k systému SQL Server jenom na aplikace a klienti, kteří ho potřebují.
- Dodržujte doporučené postupy pro správu uživatelské účty a hesla.

Následující části obsahují návrhy na přemýšlení prostřednictvím těchto bodů.

## <a name="secure-connections"></a>Zabezpečené připojení

Při vytváření virtuálního počítače s SQL serverem pomocí bitovou kopii Galerie **připojení k serveru SQL** možnost vám dává možnost **místní (uvnitř virtuálního počítače)**, **privátní (uvnitř virtuální sítě)**, nebo **veřejné (Internet)**.

![Připojení k SQL serveru](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Nejvyšší zabezpečení zvolte možnost nejvíc omezující pro váš scénář. Například pokud používáte aplikaci který přistupuje k systému SQL Server na stejný virtuální počítač, pak **místní** je nejbezpečnější volbou. Pokud používáte aplikaci Azure, která vyžaduje přístup k systému SQL Server, pak **privátní** zabezpečuje komunikaci pro SQL Server pouze v rámci zadaného [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Pokud budete potřebovat **veřejné** (internet) přístup k virtuální počítač SQL Server, ujistěte se, postupujte podle ostatní osvědčené postupy v tomto tématu, snížit útoku útoku.

Vybrané možnosti na portálu používat příchozí pravidla zabezpečení do virtuálního počítače [skupinu zabezpečení sítě](../../../virtual-network/security-overview.md) (NSG), aby povolili nebo zakázali síťový provoz do virtuálního počítače. Můžete upravit nebo vytvořit nový příchozí pravidla NSG, které chcete povolit přenosy na portu SQL Server (standardně 1433). Můžete také zadat konkrétní IP adresy, které jsou povoleny pro komunikaci prostřednictvím tohoto portu.

![Pravidla skupiny zabezpečení sítě](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Kromě pravidel NSG omezit síťový provoz můžete také použít brány Windows Firewall na virtuálním počítači.

Pokud používáte koncových bodů s modelem nasazení classic, odeberte žádné koncové body na virtuálním počítači, pokud je nepoužíváte. Pokyny pro koncové body pomocí seznamů řízení přístupu, v tématu [spravovat seznam ACL u koncového bodu](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint). To není nutné pro virtuální počítače, které používají Resource Manager.

Nakonec je třeba zvážit povolení šifrované připojení pro instanci databázového stroje SQL Server ve virtuálním počítači Azure. Instance systému SQL server nakonfigurujte certifikát podepsaný držitelem. Další informace najdete v tématu [povolit šifrované připojení k databázovému stroji](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) a [syntaxi připojovacího řetězce](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Použijte jiný než výchozí port

Ve výchozím nastavení SQL Server naslouchá na dobře známé port 1433. Pro zvýšení zabezpečení nakonfigurujte SQL Server tak, aby naslouchala na jiný než výchozí port, jako je například 1401. Pokud zřizujete Galerie bitovou kopii systému SQL Server na portálu Azure, můžete zadat Tento port v **nastavení systému SQL Server** okno.

Chcete-li konfiguraci po zřízení, máte dvě možnosti:

- Pro virtuální počítače správce prostředků, můžete vybrat **konfigurace systému SQL Server** v okně Přehled virtuálních počítačů. To nabízí možnost, chcete-li změnit port.

  ![Změnit TCP port portálu](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Klasické virtuální počítače nebo virtuální počítače serveru SQL, které nebyly zřízeny na portálu můžete ručně nakonfigurovat port vzdálené připojení k virtuálnímu počítači. Postup konfigurace najdete v tématu [konfigurace serveru pro naslouchání na specifickém portu TCP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Pokud použijete tento Ruční postup, musíte taky přidat pravidlo brány Windows Firewall povolit příchozí přenosy na portu TCP.

> [!IMPORTANT]
> Určení jiný než výchozí port je vhodné, pokud vaše port serveru SQL Server je otevřená a veřejného připojení k Internetu.

Pokud SQL Server naslouchá na jiný než výchozí port, musíte zadat port při připojení. Zvažte například scénář, kde je IP adresa serveru je 13.55.255.255 a SQL Server naslouchá na portu 1401. Pro připojení k systému SQL Server, zadejte `13.55.255.255,1401` v připojovacím řetězci.

## <a name="manage-accounts"></a>Správa účtů

Nechcete, aby útočníci snadno uhodnout názvy účtů nebo hesel. Chcete, použijte následující tipy:

- Vytvořte účet jedinečný místního správce, který není s názvem **správce**.

- Používejte silná hesla, komplexní pro všechny účty. Další informace o tom, jak vytvořit silné heslo, najdete v části [vytvořit silné heslo](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) článku.

- Ve výchozím nastavení vybere Azure ověřování systému Windows během instalace virtuálního počítače systému SQL Server. Proto **SA** přihlášení je zakázané a heslo přiděluje instalační program. Doporučujeme, aby **SA** přihlášení by neměly být použít nebo povoleno. Pokud máte přihlašovací jméno SQL, použijte jednu z následujících strategií:

  - Vytvoření účtu SQL s jedinečným názvem, který má **sysadmin** členství. Můžete k tomu z portálu povolením **ověřování SQL** během zřizování.

    > [!TIP] 
    > Pokud nepovolíte ověřování SQL při zřizování, musíte ručně změnit režim ověřování systému **systému SQL Server a režim ověřování systému Windows**. Další informace najdete v tématu [Změna režimu ověřování serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Pokud je nutné použít **SA** přihlášení, povolit přihlášení po zřízení a přiřazení nové silné heslo.

## <a name="follow-on-premises-best-practices"></a>Použijte místní osvědčené postupy

Kromě postupů popsaných v tomto tématu doporučujeme zkontrolovat a implementovat tradičních místních postupy zabezpečení, kde je to možné. Další informace najdete v tématu [důležité informace o zabezpečení pro instalaci serveru SQL](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>Další kroky

Pokud vás zajímá také osvědčené postupy týkající se výkonu, najdete v části [osvědčené postupy z hlediska výkonu pro SQL Server v Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Další témata související se systémem SQL Server ve virtuálních počítačích Azure, najdete v části [SQL Server na virtuálních počítačích Azure přehled](virtual-machines-windows-sql-server-iaas-overview.md). Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).

