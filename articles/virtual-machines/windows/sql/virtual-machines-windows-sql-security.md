---
title: Důležité informace o zabezpečení pro SQL Server v Azure | Dokumentace Microsoftu
description: Toto téma obsahuje obecné pokyny pro zabezpečení SQL serveru běžícího virtuálním počítači Azure.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
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
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 61cfc458ed965fd4f1446ff8cfb5e9a6e244f246
ms.sourcegitcommit: dede0c5cbb2bd975349b6286c48456cfd270d6e9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2019
ms.locfileid: "54332383"
---
# <a name="security-considerations-for-sql-server-in-azure-virtual-machines"></a>Informace o zabezpečení pro SQL Server v Azure Virtual Machines

Toto téma obsahuje pokyny pro celkové zabezpečení, které vám usnadní vytváření zabezpečený přístup k instancím systému SQL Server v Azure virtuální počítač (VM).

Azure splňuje několik oborových předpisů a standardů, které vám k vytvoření řešení kompatibilní se serverem SQL Server běžící na virtuálním počítači. Informace o dodržování legislativních předpisů v Azure najdete v tématu [Centrum zabezpečení Azure](https://azure.microsoft.com/support/trust-center/).

[!INCLUDE [learn-about-deployment-models](../../../../includes/learn-about-deployment-models-both-include.md)]

## <a name="control-access-to-the-sql-vm"></a>Řízení přístupu k virtuálnímu počítači SQL

Při vytváření virtuálního počítače systému SQL Server, zvažte, jak pečlivě řídit, kdo má přístup k počítači a k systému SQL Server. Obecně platí proveďte následující:

- Omezení přístupu k systému SQL Server jenom na aplikace a klienti, kteří ji potřebují.
- Dodržujte doporučené postupy zabezpečení pro správu uživatelských účtů a hesel.

Následující části obsahují návrhy na přemýšlení prostřednictvím těchto bodů.

## <a name="secure-connections"></a>Zabezpečená připojení

Při vytváření virtuálního počítače s SQL serverem pomocí image Galerie **připojení k SQL serveru** možnost vám poskytne možnost **místní (uvnitř virtuálního počítače)**, **privátní (uvnitř virtuální sítě)**, nebo **veřejné (Internet)**.

![Připojení k SQL serveru](./media/virtual-machines-windows-sql-security/sql-vm-connectivity-option.png)

Nejvyšší zabezpečení zvolte nejvíce omezující volbu pro váš scénář. Například pokud spustíte aplikaci, která má přístup k systému SQL Server na stejném virtuálním počítači, pak **místní** je nejbezpečnější volbou. Pokud používáte Azure aplikaci, která vyžaduje přístup k SQL serveru, pak **privátní** chrání vaše komunikace se serverem SQL jenom v rámci zadaného [Azure Virtual Network](../../../virtual-network/virtual-networks-overview.md). Pokud budete potřebovat **veřejné** (internet) přístup k virtuálnímu počítači SQL serveru a ujistěte se, že dodržovat doporučené postupy v tomto tématu pro omezení možností útoku.

Vybrané možnosti na portálu použijte pravidla zabezpečení příchozích dat na virtuálním počítači na [skupinu zabezpečení sítě](../../../virtual-network/security-overview.md) (NSG) k povolení nebo odpírají síťový provoz do virtuálního počítače. Můžete upravit nebo vytvořit nového příchozího pravidla NSG pro povolení provozu na port serveru SQL Server (standardně 1433). Můžete také určit konkrétní IP adresy, které můžou komunikovat přes tento port.

![Pravidla skupiny zabezpečení sítě](./media/virtual-machines-windows-sql-security/sql-vm-network-security-group-rules.png)

Kromě pravidla skupiny zabezpečení sítě je možné omezit síťový provoz můžete také použít bránu Windows Firewall na virtuálním počítači.

Pokud použijete koncové body pomocí modelu nasazení classic, odeberte žádné koncové body na virtuálním počítači, pokud je nepoužíváte. Pokyny k používání seznamů ACL s koncovými body najdete v tématu [spravovat seznam ACL v koncovém bodě](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint). To není nutné pro virtuální počítače využívající Resource Manager.

Nakonec je třeba zvážit povolení šifrované připojení k instanci databázového stroje SQL Server ve virtuálním počítači Azure. Konfigurovat instanci SQL serveru s podepsaným certifikátem. Další informace najdete v tématu [povolit šifrované připojení databázový stroj](https://docs.microsoft.com/sql/database-engine/configure-windows/enable-encrypted-connections-to-the-database-engine) a [syntaxe připojovacího řetězce](https://msdn.microsoft.com/library/ms254500.aspx).

## <a name="use-a-non-default-port"></a>Použít jiný než výchozí port

Ve výchozím nastavení SQL Server naslouchá na dobře známém portu 1433. Pro zvýšení zabezpečení a konfigurace SQL serveru tak, aby naslouchala na jiný než výchozí port, jako je například 1401. Pokud si zřídíte Galerie image SQL serveru na webu Azure Portal, můžete zadat Tento port v **nastavení systému SQL Server** okno.

Pokud chcete nastavit tuto konfiguraci po zřízení, máte dvě možnosti:

- Pro virtuální počítače Resource Manageru, můžete vybrat **konfigurace systému SQL Server** v okně Přehled virtuálních počítačů. To poskytuje možnost změnit číslo portu.

  ![Změňte TCP port na portálu](./media/virtual-machines-windows-sql-security/sql-vm-change-tcp-port.png)

- Klasické virtuální počítače nebo virtuální počítače SQL serveru, které nebyly zřízené s portálem můžete ručně nakonfigurovat port ve vzdálené připojení k virtuálnímu počítači. Postup konfigurace najdete v tématu [konfigurace serveru tak, aby naslouchal specifickému portu TCP](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-server-to-listen-on-a-specific-tcp-port). Pokud použijete tento Ruční postup, musíte také přidat pravidlo brány Windows Firewall umožňuje příchozí provoz na portu TCP.

> [!IMPORTANT]
> Zadání jiné než výchozí port je vhodné, pokud je port serveru SQL Server je otevřená pro veřejné připojení k Internetu.

Pokud SQL Server naslouchá na jiný než výchozí port, musíte zadat port, když se připojíte. Zvažte například scénář, kde je IP adresa serveru je 13.55.255.255 a SQL Server naslouchá na portu 1401. Pro připojení k SQL serveru, zadali byste `13.55.255.255,1401` v připojovacím řetězci.

## <a name="manage-accounts"></a>Správa účtů

Nechcete, aby útočníci snadno uhodnutelné názvy účtů nebo hesel. Pomocí následující tipy:

- Vytvoření účtu místního správce jedinečné, která neobsahuje název **správce**.

- Používejte komplexní silná hesla pro všechny vaše účty. Další informace o tom, jak vytvořit silné heslo, naleznete v tématu [vytvořit silné heslo](https://support.microsoft.com/instantanswers/9bd5223b-efbe-aa95-b15a-2fb37bef637d/create-a-strong-password) článku.

- Ve výchozím nastavení Azure vybere ověřování Windows během instalace virtuálních počítačů s SQL serverem. Proto **SA** přihlášení je zakázané a heslo je přiděleno instalačním programem. Doporučujeme, aby **SA** přihlášení by neměly být použity nebo povolena. Pokud potřebujete přihlašovací jméno SQL, použijte jednu z následujících strategií:

  - Vytvořit účet SQL s jedinečným názvem, který má **sysadmin** členství. Můžete to provést z portálu povolením **ověřování SQL** během zřizování.

    > [!TIP] 
    > Pokud nepovolíte ověřování SQL při zřizování, musíte ručně změnit režim ověřování systému **systému SQL Server a Windows Authentication Mode**. Další informace najdete v tématu [změnit režim ověřování serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode).

  - Pokud je nutné použít **SA** přihlášení, povolit přihlášení po zřízení a přiřazení nové silné heslo.

## <a name="follow-on-premises-best-practices"></a>Dodržujte doporučené postupy zabezpečení místní

Kromě postupů popsaných v tomto tématu doporučujeme zkontrolovat a implementovat tradičních místních postupy zabezpečení, kde je to možné. Další informace najdete v tématu [aspekty zabezpečení pro instalaci SQL serveru](https://docs.microsoft.com/sql/sql-server/install/security-considerations-for-a-sql-server-installation)

## <a name="next-steps"></a>Další kroky

Pokud vás zajímají také osvědčené postupy výkonu, přečtěte si téma [osvědčené postupy z hlediska výkonu pro SQL Server ve službě Azure Virtual Machines](virtual-machines-windows-sql-performance.md).

Další témata související s SQL serverem na virtuálních počítačích Azure, najdete v části [systému SQL Server na Azure Virtual Machines – přehled](virtual-machines-windows-sql-server-iaas-overview.md). Pokud máte dotazy k virtuálním počítačům s SQL Serverem, přečtěte si [Nejčastější dotazy](virtual-machines-windows-sql-server-iaas-faq.md).

