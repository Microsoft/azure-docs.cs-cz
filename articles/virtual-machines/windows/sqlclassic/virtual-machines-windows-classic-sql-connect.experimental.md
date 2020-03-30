---
title: Připojení k virtuálnímu počítači SQL Server v Azure (classic) | Dokumenty společnosti Microsoft
description: Zjistěte, jak se připojit k SQL Serveru spuštěného na virtuálním počítači v Azure. Toto téma používá klasický model nasazení. Scénáře se liší v závislosti na konfiguraci sítě a umístění klienta.
services: virtual-machines-windows
documentationcenter: na
author: MashaMSFT
manager: craigg
tags: azure-service-management
ms.assetid: 416948af-454f-4cfe-8fd2-7cf971cbd3e9
ms.service: virtual-machines-sql
ms.topic: article
ms.tgt_pltfrm: vm-windows-sql-server
ms.workload: iaas-sql-server
ms.date: 01/31/2017
ms.author: mathoma
ms.reviewer: jroth
experimental: true
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 1c2d5ae5d85624ea172eb9a95d4086dd71287c4f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "75978205"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Připojení k virtuálnímu počítači s SQL Serverem v Azure (nasazení Classic)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Classic](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Přehled
Toto téma popisuje, jak se připojit k instanci SQL Serveru spuštěné na virtuálním počítači Azure. Zahrnuje některé [obecné scénáře připojení](#connection-scenarios) a pak poskytuje podrobné kroky pro [konfiguraci připojení SQL Server v virtuálním počítači Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření a práci s prostředky: [Resource Manager a Classic](../../../azure-resource-manager/management/deployment-models.md). Tento článek popisuje použití modelu klasické nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Pokud používáte virtuální počítače Správce prostředků, přečtěte [si tématu Připojení k virtuálnímu počítači SQL Serveru v Azure pomocí Správce prostředků](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Scénáře připojení
Způsob, jakým se klient připojuje k serveru SQL Server spuštěný ve virtuálním počítači, se liší v závislosti na umístění klienta a konfiguraci počítače/sítě. Mezi tyto scénáře patří:

* [Připojení k SQL Serveru ve stejné cloudové službě](#connect-to-sql-server-in-the-same-cloud-service)
* [Připojení k serveru SQL Server přes internet](#connect-to-sql-server-over-the-internet)
* [Připojení k serveru SQL Server ve stejné virtuální síti](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Než se připojíte k některé z těchto metod, je nutné [ke konfiguraci připojení nakonfigurovat pomocí kroků uvedených v tomto článku](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Připojení k SQL Serveru ve stejné cloudové službě
Ve stejné cloudové službě lze vytvořit více virtuálních počítačů. Informace o tomto scénáři virtuálních počítačů najdete v tématu [Jak připojit virtuální počítače k virtuální síti nebo cloudové službě](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service). V tomto scénáři se klient na jednom virtuálním počítači pokusí připojit k SERVERU SQL Server spuštěný na jiném virtuálním počítači ve stejné cloudové službě.

V tomto scénáři se můžete připojit pomocí **názvu** virtuálního počítače (také zobrazený jako **název počítače** nebo **název hostitele** na portálu). Toto je název, který jste zadali pro virtuální ho během vytváření. Například pokud jste pojmenovali sql VM **mysqlvm**, klientský virtuální počítač ve stejné cloudové službě může použít následující připojovací řetězec pro připojení:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Připojení k serveru SQL Server přes Internet
Pokud se chcete připojit k databázovému stroji SQL Server z Internetu, musíte vytvořit koncový bod virtuálního počítače pro příchozí komunikaci TCP. Tento krok konfigurace Azure směruje příchozí provoz portu TCP na port TCP, který je pro virtuální počítač přístupný.

Chcete-li se připojit přes internet, musíte použít název DNS virtuálního počítače a číslo portu koncového bodu virtuálního počítače (nakonfigurované dále v tomto článku). Pokud chcete najít název DNS, přejděte na portál Azure a vyberte **Virtuální počítače (klasické).** Pak vyberte svůj virtuální počítač. **Název DNS** je zobrazen v části **Přehled.**

Zvažte například klasický virtuální počítač s názvem **mysqlvm** s názvem DNS **mysqlvm7777.cloudapp.net** a koncovým bodem virtuálního počítače **57500**. Za předpokladu, že správně nakonfigurované připojení, následující připojovací řetězec lze použít pro přístup k virtuálnímu počítači z libovolného místa na internetu:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

Přestože tento připojovací řetězec umožňuje připojení pro klienty přes Internet, neznamená to, že kdokoli může připojit k serveru SQL Server. Externí klienti mají správné uživatelské jméno a heslo. Pro další zabezpečení nepoužívejte známý port 1433 pro koncový bod veřejného virtuálního počítače. A pokud je to možné, zvažte přidání acl na koncový bod omezit provoz pouze na klienty, které povolíte. Pokyny k použití acl s koncovými body naleznete [v tématu Správa acl na koncovém bodu](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Při použití této techniky ke komunikaci s SQL Server, všechna odchozí data z datového centra Azure podléhá normální [ceny na odchozí datové přenosy](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Připojení k serveru SQL Server ve stejné virtuální síti
[Virtuální síť](../../../virtual-network/virtual-networks-overview.md) umožňuje další scénáře. Virtuální počítače můžete připojit ve stejné virtuální síti, i když tyto virtuální počítače existují v různých cloudových službách. A s [VPN site-to-site](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)můžete vytvořit hybridní architekturu, která propojuje virtuální počítače s místními sítěmi a počítači.

Virtuální sítě také umožňují připojit vaše virtuální počítače Azure k doméně. Připojení k doméně je jediný způsob, jak používat ověřování systému Windows s SQL Server. Ostatní scénáře připojení vyžadují ověřování SQL s uživatelskými jmény a hesly.

Pokud plánujete nakonfigurovat prostředí domény a ověřování systému Windows, není nutné konfigurovat veřejný koncový bod nebo ověřování SQL a přihlášení. V tomto scénáři se můžete připojit k instanci serveru SQL Server zadáním názvu virtuálního počítače SQL Server v připojovacím řetězci. Následující příklad předpokládá, že ověřování systému Windows bylo nakonfigurováno a že uživateli byl udělen přístup k instanci serveru SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Postup konfigurace připojení SQL Serveru ve virtuálním počítači Azure
Následující kroky ukazují, jak se připojit k instanci serveru SQL Server přes internet pomocí SQL Server Management Studio (SSMS). Stejné kroky však platí pro zpřístupnění virtuálního počítače SQL Serveru pro vaše aplikace, které běží místně i v Azure.

Než se budete moct připojit k instanci SQL Serveru z jiného virtuálního serveru nebo z Internetu, musíte dokončit následující úkoly:

* [Vytvoření koncového bodu TCP pro virtuální počítač](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Otevření portů TCP v bráně firewall systému Windows](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Konfigurace naslouchání SQL Serveru pro protokol TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Konfigurace SQL Serveru pro kombinovaný režim ověřování](#configure-sql-server-for-mixed-mode-authentication)
* [Vytvoření účtů ověřování serveru SQL](#create-sql-server-authentication-logins)
* [Určení názvu DNS virtuálního počítače](#determine-the-dns-name-of-the-virtual-machine)
* [Připojení k databázovému stroji z jiného počítače](#connect-to-the-database-engine-from-another-computer)

Cesta připojení je shrnuta pomocí následujícího diagramu:

![Připojení k virtuálnímu počítači serveru SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Další kroky
Pokud také plánujete použít AlwaysOn skupiny dostupnosti pro vysokou dostupnost a zotavení po havárii, měli byste zvážit implementaci naslouchací proces. Databázoví klienti se připojují k naslouchací proces, nikoli přímo k jedné z instancí serveru SQL Server. Naslouchací proces směruje klienty do primární repliky ve skupině dostupnosti. Další informace najdete [v tématu Konfigurace naslouchací proces ILB pro skupiny dostupnosti AlwaysOn v Azure](../classic/ps-sql-int-listener.md).

Je důležité zkontrolovat všechny doporučené postupy zabezpečení pro SQL Server spuštěný na virtuálním počítači Azure. Další informace najdete v tématu [Informace o zabezpečení pro SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-security.md).

[Projděte si mapy kurzů](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pro SQL Server na virtuálních počítačích Azure. 

Další témata související se spuštěním SQL Serveru ve virtuálních počítačích Azure najdete v článku [SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

