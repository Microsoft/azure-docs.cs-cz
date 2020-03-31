---
title: Připojení k virtuálnímu počítači serveru SQL Server (Správce prostředků) | Dokumenty společnosti Microsoft
description: Zjistěte, jak se připojit k SQL Serveru spuštěného na virtuálním počítači v Azure. Toto téma používá klasický model nasazení. Scénáře se liší v závislosti na konfiguraci sítě a umístění klienta.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: deb337d989a3658e909cefa7a9ab028e37792562
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79243169"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Připojení k virtuálnímu počítači SQL Serveru na Azure

## <a name="overview"></a>Přehled

Toto téma popisuje, jak se připojit k instanci SQL Serveru spuštěné na virtuálním počítači Azure. Zahrnuje některé [obecné scénáře připojení](#connection-scenarios) a pak poskytuje kroky na [portálu pro změnu nastavení připojení](#change). Pokud potřebujete řešit potíže nebo konfigurovat připojení mimo portál, podívejte se na [ruční konfiguraci](#manual) na konci tohoto tématu. 

Pokud byste raději měli úplný průchod zřizování i připojení, přečtěte si informace [o zřizování virtuálního počítače SQL Server v Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Scénáře připojení

Způsob, jakým se klient připojuje k serveru SQL Server spuštěný ve virtuálním počítači, se liší v závislosti na umístění klienta a konfiguraci sítě.

Pokud zřídíte virtuální počítač SQL Server na webu Azure Portal, máte možnost zadat typ **připojení SQL**.

![Možnost veřejného připojení SQL během zřizování](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Mezi možnosti připojení patří:

| Možnost | Popis |
|---|---|
| **Veřejné** | Připojení k serveru SQL Server přes internet |
| **Soukromé** | Připojení k serveru SQL Server ve stejné virtuální síti |
| **Místní** | Místní připojení k serveru SQL Server na stejném virtuálním počítači | 

Následující části popisují **možnosti Veřejné** **a Soukromé** podrobněji.

## <a name="connect-to-sql-server-over-the-internet"></a>Připojení k serveru SQL Server přes Internet

Pokud se chcete připojit k databázovému stroji SQL Server z Internetu, vyberte **veřejné** pro typ **připojení SQL** na portálu během zřizování. Portál automaticky provede následující kroky:

* Povolí protokol TCP/IP pro sql server.
* Nakonfiguruje pravidlo brány firewall pro otevření portu TCP serveru SQL Server (výchozí hodnota 1433).
* Povolí ověřování serveru SQL Server, které je vyžadováno pro veřejný přístup.
* Nakonfiguruje skupinu zabezpečení sítě na virtuálním počítači na všechny přenosy TCP na portu serveru SQL Server.

> [!IMPORTANT]
> Bitové kopie virtuálních počítačů pro edice SQL Server Developer a Express automaticky nepovolují protokol TCP/IP. Pro edice Pro vývojáře a Express je nutné použít nástroj SQL Server Configuration Manager k [ručnímu povolení protokolu TCP/IP](#manualtcp) po vytvoření virtuálního počítače.

Každý klient s přístupem k internetu se může připojit k instanci serveru SQL Server zadáním veřejné IP adresy virtuálního počítače nebo jakéhokoli popisku DNS přiřazeného k této adrese IP. Pokud je port serveru SQL Server 1433, není nutné jej zadat v připojovacím řetězci. Následující připojovací řetězec se připojuje k virtuálnímu virtuálnímu virtuálnímu provozu SQL s popiskem DNS `sqlvmlabel.eastus.cloudapp.azure.com` pomocí ověřování SQL (můžete také použít veřejnou IP adresu).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

I když to umožňuje připojení pro klienty přes internet, to neznamená, že kdokoli může připojit k serveru SQL Server. Externí klienti mají správné uživatelské jméno a heslo. Pro zvýšení zabezpečení se však můžete vyhnout známému portu 1433. Pokud jste například nakonfigurovali sql server tak, aby naslouchal na portu 1500 a vytvořil i správná pravidla brány firewall a skupiny zabezpečení sítě, můžete se připojit připojením čísla portu k názvu serveru. Následující příklad změní předchozí přidáním vlastního čísla portu **1500**, k názvu serveru:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Při dotazování SQL Server ve virtuálním počítači přes internet, všechna odchozí data z datového centra Azure podléhá běžné [ceny za odchozí datové přenosy](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Připojení k SQL Serveru v rámci virtuální sítě

Když na portálu zvolíte **Soukromé** pro typ **připojení SQL,** Azure nakonfiguruje většinu nastavení shodných s **Public**. Jeden rozdíl je, že neexistuje žádné pravidlo skupiny zabezpečení sítě povolit vnější provoz na portu serveru SQL Server (výchozí 1433).

> [!IMPORTANT]
> Bitové kopie virtuálních počítačů pro edice SQL Server Developer a Express automaticky nepovolují protokol TCP/IP. Pro edice Pro vývojáře a Express je nutné použít nástroj SQL Server Configuration Manager k [ručnímu povolení protokolu TCP/IP](#manualtcp) po vytvoření virtuálního počítače.

Privátní připojení se často používá ve spojení s [virtuální sítí](../../../virtual-network/virtual-networks-overview.md), která umožňuje několik scénářů. Virtuální počítače můžete připojit ve stejné virtuální síti, i když tyto virtuální počítače existují v různých skupinách prostředků. A s [VPN site-to-site](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)můžete vytvořit hybridní architekturu, která propojuje virtuální počítače s místními sítěmi a počítači.

Virtuální sítě také umožňují připojit vaše virtuální počítače Azure k doméně. Toto je jediný způsob, jak používat ověřování systému Windows na SQL Server. Ostatní scénáře připojení vyžadují ověřování SQL s uživatelskými jmény a hesly.

Za předpokladu, že jste nakonfigurovali službu DNS ve virtuální síti, můžete se připojit k instanci serveru SQL Server zadáním názvu počítače virtuálního počítače serveru SQL Server v připojovacím řetězci. Následující příklad také předpokládá, že ověřování systému Windows bylo také nakonfigurováno a že uživateli byl udělen přístup k instanci serveru SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a>Změna nastavení připojení SQL

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Nastavení připojení pro váš virtuální počítač SQL Server můžete změnit na webu Azure Portal.

1. Na webu Azure Portal vyberte **virtuální počítače SQL**.

2. Vyberte virtuální počítač SQL Server.

3. V části **Nastavení**vyberte **Možnost Zabezpečení**.

4. Změňte **úroveň připojení SQL** na požadované nastavení. Tuto oblast můžete volitelně použít ke změně portu serveru SQL Server nebo nastavení ověřování SQL.

   ![Změna připojení SQL](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Počkejte několik minut na dokončení aktualizace.

   ![Oznámení o aktualizaci virtuálních vod SQL](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a>Povolení protokolu TCP/IP pro vývojářské a expresní edice

Při změně nastavení připojení serveru SQL Server Azure automaticky nepovolí protokol TCP/IP pro edice SQL Server Developer a Express. Následující kroky popisují ruční povolení protokolu TCP/IP, abyste se mohli vzdáleně připojit pomocí IP adresy.

Nejprve se připojte k počítači SQL Server se vzdálenou plochou.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Dále povolte protokol TCP/IP pomocí **nástroje SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Připojení přes SSMS

Následující kroky ukazují, jak vytvořit volitelný popisek DNS pro váš virtuální počítač Azure a pak se připojit k SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a>Ruční konfigurace a řešení potíží

Přestože portál poskytuje možnosti automatické konfigurace připojení, je užitečné vědět, jak ručně konfigurovat připojení. Pochopení požadavků může také pomoci při řešení potíží.

V následující tabulce jsou uvedeny požadavky na připojení k SQL Serveru spuštěné v virtuálním počítači Azure.

| Požadavek | Popis |
|---|---|
| [Povolení režimu ověřování serveru SQL Server](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | Ověřování serveru SQL Server je potřeba pro vzdálené připojení k virtuálnímu počítači, pokud jste nenakonfigurovali službu Active Directory ve virtuální síti. |
| [Vytvoření přihlášení sql](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Pokud používáte ověřování SQL, potřebujete přihlášení SQL s uživatelským jménem a heslem, které má také oprávnění k cílové databázi. |
| [Povolení protokolu TCP/IP](#manualtcp) | SQL Server musí povolit připojení přes TCP. |
| [Povolení pravidla brány firewall pro port serveru SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | Brána firewall na virtuálním počítači musí povolit příchozí provoz na portu serveru SQL Server (výchozí 1433). |
| [Vytvoření pravidla skupiny zabezpečení sítě pro protokol TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Pokud se chcete připojit přes internet, musíte povolit, aby virtuální počítač přijímali přenosy na portu SERVERU SQL Server (výchozí 1433). Připojení pouze pro místní a virtuální síť to nevyžadují. Toto je jediný krok požadovaný na webu Azure Portal. |

> [!TIP]
> Kroky v tabulce výše jsou prováděny za vás při konfiguraci připojení na portálu. Pomocí těchto kroků můžete pouze potvrdit konfiguraci nebo nastavit připojení ručně pro SQL Server.

## <a name="next-steps"></a>Další kroky

Pokyny pro zřizování spolu s těmito kroky připojení najdete [v tématu Zřizování virtuálního počítače SQL Server v Azure](virtual-machines-windows-portal-sql-server-provision.md).

Další témata související se spuštěním SQL Serveru ve virtuálních počítačích Azure najdete v článku [SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md).