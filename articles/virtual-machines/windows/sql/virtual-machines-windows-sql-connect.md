---
title: Připojení k virtuálnímu počítači s SQL serverem (Resource Manager) | Microsoft Docs
description: Zjistěte, jak se připojit k serveru SQL, které jsou spuštěny na virtuálním počítači v Azure. Toto téma používá model nasazení classic. Scénáře se liší v závislosti na konfiguraci sítě a umístění klienta.
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
ms.openlocfilehash: 7285cf47c3a5ec731cd9cfe311053e9d19886f1d
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/21/2018
ms.locfileid: "29400235"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure"></a>Připojit k virtuálnímu počítači serveru SQL v Azure

## <a name="overview"></a>Přehled

Toto téma popisuje, jak se připojit k instanci systému SQL Server spuštěna na virtuálním počítači Azure. Některé pokrývá [obecné připojení scénáře](#connection-scenarios) a pak poskytuje [kroků na portálu pro změnu nastavení připojení k](#change). Pokud potřebujete řešení nebo nakonfigurujte připojení mimo portál naleznete [ruční konfigurace](#manual) na konci tohoto tématu. 

Pokud máte by místo úplné návodu zřizování a připojení, najdete v části [zřizování virtuálního počítače systému SQL Server na platformě Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="connection-scenarios"></a>Scénáře připojení

Způsob, jakým se klient připojí k serveru SQL Server spuštěny na virtuálním počítači se liší v závislosti na umístění klienta a konfigurace sítí.

Pokud zřídíte virtuální počítač SQL Server na portálu Azure, máte možnost zadat typ **připojení SQL**.

![Veřejné možnosti připojení SQL během zřizování](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity.png)

Možnosti připojení patří:

| Možnost | Popis |
|---|---|
| **Veřejné** | Připojení k systému SQL Server přes internet |
| **Privátní** | Připojení k systému SQL Server ve stejné virtuální síti |
| **místní** | Připojení k systému SQL Server lokálně na jednom virtuálním počítači | 

Následující části popisují **veřejné** a **privátní** možnosti podrobněji.

## <a name="connect-to-sql-server-over-the-internet"></a>Připojení k systému SQL Server přes Internet

Pokud se chcete připojit k vaší databázový stroj SQL Server z Internetu, vyberte **veřejné** pro **připojení SQL** typ na portálu během zřizování. Na portálu automaticky provede následující kroky:

* Umožňuje protokol TCP/IP pro SQL Server.
* Nakonfiguruje pravidlo brány firewall pro otevření portu TCP systému SQL Server (standardně 1433).
* Umožňuje ověřování systému SQL Server, vyžaduje se pro veřejný přístup.
* Nakonfiguruje skupinu zabezpečení sítě na virtuálním počítači pro všechny přenosy TCP na portu SQL Server.

> [!IMPORTANT]
> Bitové kopie virtuálních počítačů pro SQL Server Developer a edice Express automaticky nepovolujte protokol TCP/IP. U edice Developer a Express, je nutné použít Správce konfigurace systému SQL Server na [ručně povolit protokol TCP/IP](#manualtcp) po vytvoření virtuálního počítače.

Libovolného klienta s přístupem k Internetu může připojit k instanci systému SQL Server zadáním buď veřejnou IP adresu virtuálního počítače nebo libovolný popisek DNS přiřazené tuto IP adresu. Pokud port serveru SQL Server je 1433, není potřeba zadat v připojovacím řetězci. Následující připojovací řetězec připojuje k virtuálnímu počítači SQL s jmenovkou DNS `sqlvmlabel.eastus.cloudapp.azure.com` pomocí ověřování SQL (můžete také použít veřejné IP adresy).

```
Server=sqlvmlabel.eastus.cloudapp.azure.com;Integrated Security=false;User ID=<login_name>;Password=<your_password>
```

I když to umožňuje připojení klientů přes internet, to neznamená, že každý, kdo může připojit k systému SQL Server. Klienti nemusí mimo správné uživatelské jméno a heslo. Pro dodatečné zabezpečení se však vyhnout dobře známého portu 1433. Například pokud jste nakonfigurovali SQL serveru pro naslouchání na portu 1500 a zavedené správné brány firewall a pravidel skupiny zabezpečení sítě, může připojení připojením číslo portu na název serveru. Následující příklad mění předchozímu přidáním vlastní číslo portu, **1 500**, název serveru:

```
Server=sqlvmlabel.eastus.cloudapp.azure.com,1500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"
```

> [!NOTE]
> Když dotazujete systému SQL Server ve virtuálním počítači přes internet, všechny odchozí data z datové centrum Azure podléhá normální [ceny na odchozí přenosy dat](https://azure.microsoft.com/pricing/details/data-transfers/).

## <a name="connect-to-sql-server-within-a-virtual-network"></a>Připojení k systému SQL Server v rámci virtuální sítě

Pokud vyberete **privátní** pro **připojení SQL** typ na portálu Azure nakonfiguruje většinu nastavení identické **veřejné**. Jeden rozdíl je, že neexistuje žádná pravidla skupiny zabezpečení sítě umožňuje mimo přenosy na portu systému SQL Server (standardně 1433).

> [!IMPORTANT]
> Bitové kopie virtuálních počítačů pro SQL Server Developer a edice Express automaticky nepovolujte protokol TCP/IP. U edice Developer a Express, je nutné použít Správce konfigurace systému SQL Server na [ručně povolit protokol TCP/IP](#manualtcp) po vytvoření virtuálního počítače.

Privátní připojení se často používá ve spojení s [virtuální sítě](../../../virtual-network/virtual-networks-overview.md), což umožňuje několik scénářů. I když tyto virtuální počítače existovat v různých skupinách prostředků se můžete připojit virtuální počítače ve stejné virtuální síti. A s [site-to-site VPN](../../../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md), můžete vytvořit hybridní architekturu, která připojí virtuální počítače s místními sítěmi a počítače.

Virtuální sítě umožňují připojit virtuální počítače Azure k doméně. Toto je jediným způsobem, jak použít ověřování systému Windows na serveru SQL Server. Jiné připojení scénáře vyžadují ověřování SQL pomocí uživatelského jména a hesla.

Za předpokladu, že nakonfigurujete DNS ve virtuální síti, můžete se připojit k instanci systému SQL Server zadáním názvu počítače virtuální počítač s SQL serverem v připojovacím řetězci. Následující příklad předpokládá také ověřování systému Windows také nakonfigurován a že uživatel byl udělen přístup k instanci systému SQL Server.

```
Server=mysqlvm;Integrated Security=true
```

## <a id="change"></a> Změňte nastavení připojení k SQL

Můžete změnit nastavení připojení pro virtuální počítač systému SQL Server na portálu Azure.

1. Na portálu Azure vyberte **virtuální počítače**.

2. Vyberte virtuální počítač systému SQL Server.

3. V části **nastavení**, klikněte na tlačítko **konfigurace systému SQL Server**.

4. Změna **úroveň připojení SQL** na požadované nastavení. Volitelně můžete této oblasti změnit port serveru SQL Server nebo nastavení ověřování SQL.

   ![Změnit připojení SQL](./media/virtual-machines-windows-sql-connect/sql-vm-portal-connectivity-change.png)

5. Počkejte několik minut na dokončení aktualizace.

   ![Virtuální počítač SQL aktualizace oznámení](./media/virtual-machines-windows-sql-connect/sql-vm-updating-notification.png)

## <a id="manualtcp"></a> Povolte protokol TCP/IP pro edice Developer a Express

Při změně nastavení připojení k systému SQL Server, Azure automaticky neumožňuje protokol TCP/IP pro vývojáře serveru SQL a edice Express. Následující kroky popisují ruční povolení protokolu TCP/IP, abyste se mohli vzdáleně připojit pomocí IP adresy.

Nejdřív připojte k počítači serveru SQL Server pomocí vzdálené plochy.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-remote-desktop-connect.md)]

Dál povolte protokol TCP/IP s **SQL Server Configuration Manager**.

[!INCLUDE [Connect to SQL Server VM with remote desktop](../../../../includes/virtual-machines-sql-server-connection-tcp-protocol.md)]

## <a name="connect-with-ssms"></a>Připojení přes SSMS

Následující kroky ukazují, jak vytvořit popisek volitelné DNS pro virtuální počítač Azure a potom se připojte pomocí SQL Server Management Studio (SSMS).

[!INCLUDE [Connect to SQL Server in a VM Resource Manager](../../../../includes/virtual-machines-sql-server-connection-steps-resource-manager.md)]

## <a id="manual"></a> Ruční konfigurace a řešení potíží

I když na portál poskytuje možnosti pro automatickou konfiguraci připojení, je užitečné vědět, jak ručně nakonfigurovat připojení. Seznámení s požadavky na také usnadňuje řešení potíží.

Následující tabulka uvádí požadavky na připojení k SQL Server běžící ve virtuálním počítači Azure.

| Požadavek | Popis |
|---|---|
| [Povolit režim ověřování systému SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/change-server-authentication-mode#SSMSProcedure) | Ověřování systému SQL Server je potřebné pro připojení k virtuálnímu počítači vzdáleně, pokud jste nakonfigurovali služby Active Directory ve virtuální síti. |
| [Vytvořit přihlašovací jméno SQL](https://docs.microsoft.com/sql/relational-databases/security/authentication-access/create-a-login) | Pokud používáte ověřování SQL, potřebujete přihlašovací jméno SQL pomocí uživatelského jména a hesla, která má také oprávnění k cílové databázi. |
| [Povolit protokol TCP/IP](#manualTCP) | SQL Server musí umožňovat připojení přes protokol TCP. |
| [Povolit pravidlo brány firewall pro port serveru SQL Server](https://docs.microsoft.com/sql/database-engine/configure-windows/configure-a-windows-firewall-for-database-engine-access) | V bráně firewall na virtuální počítač musí umožňovat příchozí komunikaci na portu systému SQL Server (standardně 1433). |
| [Vytvořit pravidlo pro skupinu zabezpečení sítě pro TCP 1433](../../../virtual-network/virtual-networks-create-nsg-arm-pportal.md#create-rules-in-an-existing-nsg) | Virtuální počítač přijímat přenosy na portu systému SQL Server (standardně 1433), pokud se chcete připojit přes internet, musíte povolit. Místní a virtuální sítě pouze připojení nevyžadují, aby to. Toto je jediný krok vyžadovaný na portálu Azure. |

> [!TIP]
> Kroky v předchozí tabulce jsou u můžete provést při konfiguraci připojení k portálu. Tyto kroky použijte pouze potvrzení konfiguraci nebo ručně nastavit připojení k systému SQL Server.

## <a name="next-steps"></a>Další kroky

Zřizování pokyny společně s postupem připojení najdete v tématu [zřizování virtuálního počítače systému SQL Server na platformě Azure](virtual-machines-windows-portal-sql-server-provision.md).

Další témata související se systémem SQL Server ve virtuálních počítačích Azure, najdete v části [systému SQL Server na virtuálních počítačích Azure](virtual-machines-windows-sql-server-iaas-overview.md).