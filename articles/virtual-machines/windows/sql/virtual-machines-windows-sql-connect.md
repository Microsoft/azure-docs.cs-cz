---
title: Připojte se k virtuálnímu počítači SQL serveru (Resource Manager) | Dokumentace Microsoftu
description: Zjistěte, jak se připojit k SQL serveru spuštěné na virtuálním počítači v Azure. Toto téma používá model nasazení classic. Scénáře se liší v závislosti na konfiguraci sítě a umístění klienta.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: jroth
ms.openlocfilehash: 522ece2528e43c1037dc6bb707201ecda8074dd9
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38705958"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Připojte se k virtuálnímu počítači SQL serveru v Azure

## <a name="overview"></a>Přehled

Toto téma popisuje, jak se připojit k vaší instanci SQL serveru spuštěné na virtuálním počítači Azure. Popisuje některé [scénáře obecné připojení](#connection-scenarios) a pak poskytuje [kroků na portálu pro změnu nastavení připojení k](#change). Pokud je potřeba řešení nebo nakonfigurujte připojení mimo portál naleznete [ruční konfigurace](#manual) na konci tohoto tématu. 

Pokud byste raději chtěli celý podrobný postup zřizování a možnosti připojení, přečtěte si téma [zřízení virtuálního počítače SQL serveru v Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Scénáře připojení

Způsob, jakým se klient připojuje k SQL serveru spuštěné na virtuálním počítači se liší v závislosti na umístění klienta a o konfiguraci sítě.

Zřízení virtuálního počítače s SQL serverem na webu Azure Portal, máte možnost zadání typu **připojení SQL**.

![Veřejné možnosti připojení SQL během zřizování](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Možnosti připojení patří:

| Možnost | Popis |
|---|---|
| **Veřejné** | Připojení k SQL serveru přes internet |
| **Privátní** | Připojení k SQL serveru ve stejné virtuální síti |
| **místní** | Připojení k SQL serveru místně na stejný virtuální počítač | 

V následujících částech **veřejné** a **privátní** možností podrobněji.

## <a name="connect-to-sql-server-over-the-internet"></a>Připojení k SQL serveru přes Internet

Pokud chcete z Internetu připojit k databázovém stroji SQL serveru, vyberte **veřejné** pro **připojení SQL** typ portálu během zřizování. Na portálu automaticky provede následující kroky:

* Povolí protokol TCP/IP pro SQL Server.
* Nakonfiguruje pravidlo brány firewall pro otevření portu TCP systému SQL Server (standardně 1433).
* Umožňuje ověřování systému SQL Server, vyžaduje se pro veřejný přístup.
* Konfiguruje se skupinou zabezpečení sítě na virtuálním počítači veškerý provoz TCP na portu SQL serveru.

> [!IMPORTANT]
> Image virtuálních počítačů pro edice Express a SQL Server Developer nepovolujte automaticky protokol TCP/IP. Pro edice Developer a Express, je nutné použít SQL Server Configuration Manageru [povolit protokol TCP/IP ručně](#manualtcp) po vytvoření virtuálního počítače.

Libovolný klient s přístupem k Internetu může připojit k instanci systému SQL Server zadáním buď veřejnou IP adresu virtuálního počítače nebo libovolný popisek DNS přiřazené k této IP adrese. Pokud je port serveru SQL 1433, není potřeba je zadat v připojovacím řetězci. Následující připojovací řetězec se připojuje k virtuálnímu počítači SQL pomocí názvu DNS `sqlvmlabel.eastus.cloudapp.azure.com` pomocí ověřování SQL (můžete také použít veřejné IP adresy).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

I když to umožňuje připojení klientů přes internet, to neznamená, že kdokoli může připojit k SQL serveru. Klienti nemusí mimo správné uživatelské jméno a heslo. Za účelem zvýšení zabezpečení, ale můžete vyhnout dobře známý port 1433. Například pokud jste nakonfigurovali SQL serveru pro naslouchání na portu 1500 a navážete správné brány firewall a pravidla skupiny zabezpečení sítě, můžete se připojit k názvu serveru připojením číslo portu. Následující příklad upravuje předchozí tak, že přidáte vlastní číslo portu, **1500**, názvu serveru:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Při dotazování SQL Server na virtuálním počítači přes internet, všechny odchozí data z datové centrum Azure je v souladu s normální [ceny u odchozích přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Připojení k SQL serveru v rámci virtuální sítě

Pokud zvolíte **privátní** pro **připojení SQL** nakonfiguruje většinu nastavení stejný jako typů na portálu Azure **veřejné**. Jedním rozdílem je, že neexistuje žádná pravidla skupiny zabezpečení sítě umožňující mimo provoz na portu serveru SQL Server (standardně 1433).

> [!IMPORTANT]
> Image virtuálních počítačů pro edice Express a SQL Server Developer nepovolujte automaticky protokol TCP/IP. Pro edice Developer a Express, je nutné použít SQL Server Configuration Manageru [povolit protokol TCP/IP ručně](#manualtcp) po vytvoření virtuálního počítače.

Privátní připojení se často používá ve spojení s [virtuální sítě](../../../virtual-network/virtual-networks-overview.md), což umožňuje několik scénářů. Můžete připojit virtuální počítače ve stejné virtuální síti, i v případě, že existují tyto virtuální počítače v různých skupinách prostředků. A [site-to-site VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), můžete vytvořit hybridní architektury, která se připojuje virtuální počítače s místními sítěmi a počítače.

Virtuální sítě umožňují připojit virtuální počítače Azure k doméně. Toto je jediný způsob, jak používat ověřování Windows k systému SQL Server. Další připojení scénáře vyžadují ověřování serveru SQL pomocí uživatelského jména a hesla.

Za předpokladu, že nakonfigurujete DNS ve službě virtual network můžete připojit k instanci systému SQL Server zadáním názvu počítače virtuálního počítače s SQL serverem v připojovacím řetězci. V následujícím příkladu také předpokládá, že není nakonfigurovaná také ověřování Windows a, že uživatel má udělen přístup k instanci systému SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a> Změnit nastavení připojení SQL

Můžete změnit nastavení připojení pro váš virtuální počítač systému SQL Server na webu Azure Portal.

1. Na webu Azure Portal, vyberte **virtuálních počítačů**.

2. Vyberte virtuální počítač SQL serveru.

3. V části **nastavení**, klikněte na tlačítko **konfigurace systému SQL Server**.

4. Změnit **úroveň připojení SQL** požadovaná nastavení. Chcete-li změnit nastavení ověřování SQL nebo port serveru SQL Server můžete volitelně použít této oblasti.

   ![Změňte připojení SQL](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Počkejte několik minut, než se aktualizace dokončí.

   ![Oznámení o aktualizaci virtuálního počítače SQL](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a> Povolte protokol TCP/IP pro edice Developer a Express

Při změně nastavení připojení k serveru SQL Server, Azure neumožňuje automaticky protokol TCP/IP pro edice SQL serveru Developer a Express. Následující kroky popisují ruční povolení protokolu TCP/IP, abyste se mohli vzdáleně připojit pomocí IP adresy.

Nejdřív připojte k počítači s SQL serverem pomocí vzdálené plochy.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Dál povolte protokol TCP/IP s **Správce konfigurace systému SQL Server**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Připojení přes SSMS

Následující kroky ukazují, jak vytvořit volitelný popisek DNS pro virtuální počítač Azure a pak se připojte pomocí SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a> Ruční konfigurace a řešení potíží

I když portál poskytuje možnosti pro automatickou konfiguraci připojení, je užitečné vědět, jak ručně nakonfigurovat připojení. Principy požadavky také podporu řešení potíží.

V následující tabulce jsou uvedeny požadavky pro připojení k SQL serveru běžícího na Virtuálním počítači Azure.

| Požadavek | Popis |
|---|---|
| [Povolit režim ověřování systému SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode#SSMSProcedure) | Ověřování systému SQL Server je potřeba pro připojení k virtuálnímu počítači vzdáleně, pokud jste nakonfigurovali Active Directory ve virtuální síti. |
| [Vytvořte přihlašovací údaje SQL](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Pokud používáte ověřování SQL, potřebujete přihlašovací jméno SQL pomocí uživatelského jména a hesla, které má také oprávnění k cílové databázi. |
| [Povolení protokolu TCP/IP](#manualTCP) | SQL Server musí umožňovat připojení přes protokol TCP. |
| [Povolit pravidlo brány firewall pro port SQL serveru](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | Brána firewall na virtuálním počítači musí umožňovat příchozí provoz na portu serveru SQL Server (standardně 1433). |
| [Vytvoření pravidla skupiny zabezpečení sítě pro port TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Musíte také povolit virtuálního počítače pro příjem provozu na portu serveru SQL Server (standardně 1433), pokud se chcete připojit přes internet. Připojení místní a virtuální sítě – jen to nevyžadují. Toto je jediný krok vyžadovaný na webu Azure Portal. |

> [!TIP]
> Kroky v předchozí tabulce jsou provede za vás při konfiguraci připojení na portálu. Pouze pomocí těchto kroků potvrďte konfiguraci nebo ručně nastavit připojení k serveru SQL Server.

## <a name="next-steps"></a>Další kroky

Zřizování pokyny spolu s těmito kroky připojení najdete v tématu [zřízení virtuálního počítače SQL serveru v Azure](virtual-machines-windows-portal-sql-server-provision.md).

Další témata související s SQL serverem na virtuálních počítačích Azure, najdete v části [systému SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md).