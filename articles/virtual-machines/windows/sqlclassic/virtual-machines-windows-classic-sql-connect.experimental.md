---
title: Připojte se k virtuálnímu počítači SQL serveru v Azure (Classic) | Dokumentace Microsoftu
description: Zjistěte, jak se připojit k SQL serveru spuštěné na virtuálním počítači v Azure. Toto téma používá model nasazení classic. Scénáře se liší v závislosti na konfiguraci sítě a umístění klienta.
services: virtual-machines-windows
documentationcenter: na
author: rothja
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: jroth
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: e986440edc0b683d1b1cd49207a355dac060c53d
ms.sourcegitcommit: a1e1b5c15cfd7a38192d63ab8ee3c2c55a42f59c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/10/2018
ms.locfileid: "29398314"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Připojení k virtuálnímu počítači s SQL Serverem v Azure (klasické nasazení)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Classic](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Přehled
Toto téma popisuje, jak se připojit k vaší instanci SQL serveru spuštěné na virtuálním počítači Azure. Popisuje některé [scénáře obecné připojení](#connection-scenarios) a pak poskytuje [podrobný postup pro konfiguraci připojení k SQL serveru na Virtuálním počítači Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek se věnuje modelu nasazení Classic. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Pokud používáte virtuální počítače Resource Manageru, přečtěte si téma [připojit k SQL Server na virtuálním počítači Azure s využitím Resource Manageru](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Scénáře připojení
Způsob, jakým se klient připojuje k SQL serveru spuštěné na virtuálním počítači se liší v závislosti na umístění klienta a konfigurace sítě nebo počítačů. Mezi tyto scénáře patří:

* [Připojení k SQL serveru v rámci stejné cloudové služby](#connect-to-sql-server-in-the-same-cloud-service)
* [Připojení k SQL serveru přes internet](#connect-to-sql-server-over-the-internet)
* [Připojení k SQL serveru ve stejné virtuální síti](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Než připojíte pomocí některé z těchto metod, je nutné postupovat podle [kroky v tomto článku můžete nakonfigurovat připojení](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Připojení k SQL serveru v rámci stejné cloudové služby
Několik virtuálních počítačů lze vytvořit ve stejné cloudové službě. Tento scénář virtuálních počítačů najdete v tématu [postup připojení virtuálních počítačů pomocí virtuální sítě nebo cloudové služby](../classic/connect-vms-classic.md#connect-vms-in-a-standalone-cloud-service). V tomto scénáři klient na jeden virtuální počítač pokusí připojit k jinému virtuálnímu počítači ve stejné cloudové službě a systémem SQL Server.

V tomto scénáři můžete připojit pomocí virtuálního počítače **název** (také zobrazen jako **název_počítače** nebo **hostname** na portálu). Toto je název, který jste zadali pro virtuální počítač při vytvoření. Například pokud pojmenujete virtuálního počítače s SQL **mysqlvm**, klientského virtuálního počítače ve stejné cloudové služby může používat následující připojovací řetězec pro připojení:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Připojení k SQL serveru přes Internet
Pokud chcete z Internetu připojit k databázovém stroji SQL serveru, musíte vytvořit koncový bod virtuálního počítače pro příchozí komunikace TCP. Tento krok konfigurace Azure směruje příchozí provoz portu TCP na port TCP, který je pro virtuální počítač přístupný.

Připojení přes internet, musíte použít název DNS Virtuálního počítače a čísla portu koncového bodu virtuálního počítače (nakonfigurované dále v tomto článku). K vyhledání názvu DNS, přejděte na web Azure Portal a vyberte **virtuální počítače (classic)**. Potom vyberte virtuální počítač. **Název DNS** je zobrazena ve **přehled** oddílu.

Představte si třeba klasický virtuální počítač s názvem **mysqlvm** s názvem DNS **mysqlvm7777.cloudapp.net** a koncového bodu virtuálního počítače z **57500**. Za předpokladu, že správně nakonfigurovaná připojení, následující připojovací řetězec může sloužit k přístupu k virtuálnímu počítači z libovolného místa v síti internet:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

I když tento připojovací řetězec umožňuje připojení klientů přes internet, to neznamená, že kdokoli může připojit k SQL serveru. Klienti nemusí mimo správné uživatelské jméno a heslo. Za účelem zvýšení zabezpečení nepoužívejte dobře známý port 1433 pro koncový bod veřejné virtuálního počítače. A pokud je to možné, zvažte přidání seznamu ACL portu na váš koncový bod k omezení provozu pouze na klientech povolit. Pokyny k používání seznamů ACL s koncovými body najdete v tématu [spravovat seznam ACL v koncovém bodě](../classic/setup-endpoints.md#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Když použijete tuto techniku ke komunikaci s SQL serverem, všechny odchozí data z datové centrum Azure je v souladu s normální [ceny u odchozích přenosů dat](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Připojení k SQL serveru ve stejné virtuální síti
[Virtuální síť](../../../virtual-network/virtual-networks-overview.md) podporuje další scénáře. I když existují tyto virtuální počítače v rámci různých cloudových služeb se můžete připojit virtuální počítače ve stejné virtuální síti. A [site-to-site VPN](../../../vpn-gateway/vpn-gateway-site-to-site-create.md), můžete vytvořit hybridní architektury, která se připojuje virtuální počítače s místními sítěmi a počítače.

Virtuální sítě umožňují připojit virtuální počítače Azure k doméně. Připojení k doméně je jediný způsob, jak používat ověřování Windows se systémem SQL Server. Další připojení scénáře vyžadují ověřování serveru SQL pomocí uživatelského jména a hesla.

Pokud máte v plánu nakonfigurovat prostředí domény a ověřování Windows, není potřeba nakonfigurovat veřejný koncový bod nebo ověřování systému SQL a přihlášení. V tomto scénáři můžete připojit k vaší instanci SQL serveru tak, že zadáte název virtuálního počítače s SQL serverem v připojovacím řetězci. V následujícím příkladu se předpokládá, že byl nakonfigurován ověřování Windows a že uživatel byl udělen přístup k instanci systému SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Kroky pro konfiguraci připojení k SQL serveru na Virtuálním počítači Azure
Následující kroky ukazují, jak se připojit k instanci systému SQL Server prostřednictvím Internetu pomocí SQL Server Management Studio (SSMS). Ale stejný postup platí pro zpřístupnění služby virtuálního počítače SQL serveru pro vaše aplikace spuštěná místně i v Azure.

Před připojením k instanci SQL serveru z jiného virtuálního počítače nebo na internet, je nutné provést následující úkoly:

* [Vytvoření koncového bodu TCP pro virtuální počítač](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Otevření portů TCP v bráně Windows firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Konfigurace SQL serveru pro naslouchání protokolu TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Konfigurace systému SQL Server pro ověřování ve smíšeném režimu](#configure-sql-server-for-mixed-mode-authentication)
* [Vytvoření účtů ověřování serveru SQL](#create-sql-server-authentication-logins)
* [Určení názvu DNS virtuálního počítače](#determine-the-dns-name-of-the-virtual-machine)
* [Připojit k databázovému stroji z jiného počítače](#connect-to-the-database-engine-from-another-computer)

Cesta připojení je automaticky shrnutý podle následující diagram:

![Připojení k virtuálnímu počítači s SQL serverem](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Další kroky
Pokud plánujete také použití skupin dostupnosti AlwaysOn pro vysokou dostupnost a zotavení po havárii, měli byste zvážit, implementace naslouchací proces. Databáze klientů připojení k naslouchacímu procesu, nikoli přímo na jednu z instancí systému SQL Server. Naslouchací proces směruje klienty na primární repliku ve skupině dostupnosti. Další informace najdete v tématu [konfigurace naslouchacího procesu ILB pro skupiny dostupnosti AlwaysOn v Azure](../classic/ps-sql-int-listener.md).

Je důležité zkontrolovat všechny osvědčené postupy zabezpečení pro SQL Server běžící na virtuálním počítači Azure. Další informace najdete v tématu [Informace o zabezpečení pro SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-security.md).

[Projděte si mapy kurzů](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pro SQL Server na virtuálních počítačích Azure. 

Další témata související s SQL serverem na virtuálních počítačích Azure, najdete v části [systému SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

