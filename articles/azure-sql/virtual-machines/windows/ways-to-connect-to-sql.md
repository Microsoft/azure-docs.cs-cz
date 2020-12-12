---
title: Připojit k SQL Servermu virtuálnímu počítači (Správce prostředků) | Microsoft Docs
description: Přečtěte si, jak se připojit k virtuálnímu počítači s SQL Server v Azure. Toto téma používá model nasazení Classic. Scénáře se liší v závislosti na konfiguraci sítě a umístění klienta.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
tags: azure-resource-manager
ms.assetid: aa5bf144-37a3-4781-892d-e0e300913d03
ms.service: virtual-machines-sql
ms.subservice: management
ms.topic: how-to
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 12/12/2017
ms.author: mathoma
ms.reviewer: jroth
ms.openlocfilehash: 28b68178b98e53b7a7d4192ad20c05a667344969
ms.sourcegitcommit: dfc4e6b57b2cb87dbcce5562945678e76d3ac7b6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2020
ms.locfileid: "97356709"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Připojení k virtuálnímu počítači s SQL Server v Azure
[!INCLUDE[appliesto-sqlvm](../../includes/appliesto-sqlvm.md)]

## <a name="overview"></a>Přehled

Toto téma popisuje, jak se připojit ke svému SQL na virtuálním počítači Azure (VM). Zabývá se některými [obecnými scénáři připojení](#connection-scenarios) a pak poskytuje [kroky na portálu pro změnu nastavení připojení](#change). Pokud potřebujete vyřešit nebo nakonfigurovat připojení mimo portál, přečtěte si [ruční konfiguraci](#manual) na konci tohoto tématu. 

Pokud byste chtěli mít úplný návod k zřizování a připojení, přečtěte si téma [zřízení virtuálního počítače s SQL Server v Azure](create-sql-vm-portal.md).

## <a name="connection-scenarios"></a>Scénáře připojení

Způsob připojení klienta k SQL Servermu virtuálnímu počítači se liší v závislosti na umístění klienta a konfiguraci sítě.

Pokud zřídíte SQL Server virtuální počítač v Azure Portal, máte možnost určit typ **připojení SQL**.

![Možnost veřejné připojení SQL během zřizování](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity.png)

Mezi možnosti připojení patří:

| Možnost | Popis |
|---|---|
| **Republik** | Připojte se k SQL Server přes Internet. |
| **Privátní** | Připojte se k SQL Server ve stejné virtuální síti. |
| **Místní** | Připojte se k SQL Server místně na stejném virtuálním počítači. | 

V následujících částech jsou podrobněji vysvětleny **veřejné** a **soukromé** možnosti.

## <a name="connect-to-sql-server-over-the-internet"></a>Připojení k SQL Server přes Internet

Pokud se chcete připojit ke svému databázovému stroji SQL Server z Internetu, vyberte během zřizování možnost **veřejné** pro typ **připojení SQL** na portálu. Portál automaticky provede následující kroky:

* Povolí protokol TCP/IP pro SQL Server.
* Nakonfiguruje pravidlo brány firewall pro otevření portu SQL Server TCP (standardně 1433).
* Povolí SQL Server ověřování, které se vyžaduje pro veřejný přístup.
* Nakonfiguruje skupinu zabezpečení sítě na virtuálním počítači na veškerý provoz TCP na portu SQL Server.

> [!IMPORTANT]
> Image virtuálních počítačů pro edice SQL Server Developer a Express nepovolují automaticky protokol TCP/IP. Pro edice Developer a Express musíte po vytvoření virtuálního počítače [ručně povolit protokol TCP/IP](#manualtcp) pomocí SQL Server Configuration Manager.

Každý klient s přístupem k Internetu se může připojit k instanci SQL Server zadáním veřejné IP adresy virtuálního počítače nebo jakéhokoli popisku DNS přiřazeného k této IP adrese. Pokud je port SQL Server 1433, nemusíte ho zadávat v připojovacím řetězci. Následující připojovací řetězec se připojí k virtuálnímu počítači SQL pomocí popisku DNS s `sqlvmlabel.eastus.cloudapp.azure.com` použitím ověřování SQL (můžete také použít veřejnou IP adresu).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

I když tento řetězec umožňuje připojení klientů přes Internet, neznamená to, že se kdokoli může připojit k vaší SQL Server instanci. Mimo klienty musí používat správné uživatelské jméno a heslo. Pro zvýšení zabezpečení však se můžete vyhnout známému portu 1433. Pokud jste například nakonfigurovali SQL Server pro naslouchání na portu 1500 a navázali patřičná pravidla brány firewall a skupiny zabezpečení sítě, můžete se připojit připojením čísla portu k názvu serveru. Následující příklad mění předchozí krok přidáním vlastního čísla portu **1500** na název serveru:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Při dotazování SQL Server na virtuálním počítači přes Internet se všechna odchozí data z datacentra Azure vztahují na běžné [ceny za odchozí přenosy dat](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Připojení k SQL Server v rámci virtuální sítě

Zvolíte-li možnost **privátní** pro typ **připojení SQL** na portálu, Azure nakonfiguruje většinu nastavení jako **veřejné**. Jediným rozdílem je, že neexistuje žádné pravidlo skupiny zabezpečení sítě, které by umožňovalo povolení mimo provoz na portu SQL Server (standardně 1433).

> [!IMPORTANT]
> Image virtuálních počítačů pro edice SQL Server Developer a Express nepovolují automaticky protokol TCP/IP. Pro edice Developer a Express musíte po vytvoření virtuálního počítače [ručně povolit protokol TCP/IP](#manualtcp) pomocí SQL Server Configuration Manager.

Privátní připojení se často používá ve spojení s [virtuální sítí](../../../virtual-network/virtual-networks-overview.md), což umožňuje několik scénářů. Virtuální počítače můžete připojit ve stejné virtuální síti i v případě, že tyto virtuální počítače existují v různých skupinách prostředků. A pomocí [sítě VPN typu Site-to-site](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md)můžete vytvořit hybridní architekturu, která propojuje virtuální počítače s místními sítěmi a počítači.

Virtuální sítě taky umožňují připojit virtuální počítače Azure k doméně. Toto je jediný způsob, jak SQL Server pomocí ověřování systému Windows. Ostatní scénáře připojení vyžadují ověřování SQL s uživatelskými jmény a hesly.

Za předpokladu, že jste ve své virtuální síti nakonfigurovali DNS, můžete se k instanci SQL Server připojit zadáním názvu SQL Server virtuálního počítače v připojovacím řetězci. Následující příklad také předpokládá, že bylo nakonfigurováno ověřování systému Windows a že uživateli byl udělen přístup k instanci SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a name="change-sql-connectivity-settings"></a><a id="change"></a> Změnit nastavení připojení SQL

[!INCLUDE [windows-virtual-machines-sql-use-new-management-blade](../../../../includes/windows-virtual-machines-sql-new-resource.md)]

Nastavení připojení pro virtuální počítač s SQL Server můžete změnit v Azure Portal.

1. V Azure Portal vyberte **virtuální počítače SQL**.

2. Vyberte svůj virtuální počítač SQL Server.

3. V části **Nastavení** vyberte **zabezpečení**.

4. Změňte **úroveň připojení SQL** na požadované nastavení. Volitelně můžete pomocí této oblasti změnit port SQL Server nebo nastavení ověřování SQL.

   ![Změna připojení SQL](./media/ways-to-connect-to-sql/sql-vm-portal-connectivity-change.png)

5. Počkejte několik minut, než se aktualizace dokončí.

   ![Oznámení o aktualizaci virtuálního počítače SQL](./media/ways-to-connect-to-sql/sql-vm-updating-notification.png)

## <a name="enable-tcpip-for-developer-and-express-editions"></a><a id="manualtcp"></a> Povolení protokolu TCP/IP pro edice Developer a Express

Při změně nastavení připojení SQL Server Azure nepovoluje automaticky protokol TCP/IP pro edice SQL Server Developer a Express. Následující kroky popisují ruční povolení protokolu TCP/IP, abyste se mohli vzdáleně připojit pomocí IP adresy.

Nejdřív se připojte k virtuálnímu počítači s SQL Server pomocí vzdálené plochy.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

V dalším kroku Povolte protokol TCP/IP s **SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Připojení přes SSMS

Následující kroky ukazují, jak vytvořit volitelný popisek DNS pro virtuální počítač Azure a pak se připojit pomocí SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a name="manual-configuration-and-troubleshooting"></a><a id="manual"></a> Ruční konfigurace a odstraňování potíží

I když portál poskytuje možnosti pro automatické nakonfigurování připojení, je užitečné zjistit, jak ručně nakonfigurovat připojení. Porozumění požadavkům může také pomoci při řešení potíží.

V následující tabulce jsou uvedeny požadavky pro připojení k SQL Server na virtuálním počítači Azure.

| Požadavek | Popis |
|---|---|
| [Povolit režim ověřování SQL Server](/sql/database-engine/configure-windows/change-server-authentication-mode#use-ssms) | Pro vzdálené připojení k virtuálnímu počítači, pokud jste službu Active Directory ve virtuální síti nenakonfigurovali, je potřeba SQL Server ověřování. |
| [Vytvoření přihlášení SQL](/sql/relational-databases/security/authentication-access/create-a-login) | Pokud používáte ověřování SQL, budete potřebovat přihlašovací jméno SQL s uživatelským jménem a heslem, které má také oprávnění k cílové databázi. |
| [Povolit protokol TCP/IP](#manualtcp) | SQL Server musí umožňovat připojení přes protokol TCP. |
| [Povolit pravidlo brány firewall pro port SQL Server](/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | Brána firewall na virtuálním počítači musí umožňovat příchozí provoz na portu SQL Server (standardně 1433). |
| [Vytvoření pravidla skupiny zabezpečení sítě pro TCP 1433](../../../virtual-network/manage-network-security-group.md#create-a-security-rule) | Pokud se chcete připojit přes Internet, musíte virtuálnímu počítači dovolit příjem provozu na portu SQL Server (standardně 1433). Připojení jenom pro místní a virtuální sítě to nevyžadují. Toto je jediný krok vyžadovaný v Azure Portal. |

> [!TIP]
> Kroky uvedené v předchozí tabulce jsou pro vás provedeny při konfiguraci připojení na portálu. Použijte tento postup jenom k potvrzení konfigurace nebo k ručnímu nastavení připojení pro SQL Server.

## <a name="next-steps"></a>Další kroky

Pokyny k zřizování spolu s těmito kroky pro připojení najdete v tématu [zřízení virtuálního počítače s SQL Server v Azure](create-sql-vm-portal.md).

Další témata související se spouštěním SQL Server na virtuálních počítačích Azure najdete v tématu [SQL Server na virtuálních počítačích Azure](sql-server-on-azure-vm-iaas-what-is-overview.md).