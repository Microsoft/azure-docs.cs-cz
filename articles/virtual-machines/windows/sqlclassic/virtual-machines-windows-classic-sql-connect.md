---
title: Připojení k virtuálnímu počítači s SQL Server v Azure (Classic) | Microsoft Docs
description: Naučte se, jak se připojit k SQL Server běžícímu na virtuálním počítači v Azure. Toto téma používá model nasazení Classic. Scénáře se liší v závislosti na konfiguraci sítě a umístění klienta.
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
experimental_id: d51f3cc6-753b-4e
ms.openlocfilehash: 5fef230d99b871dc54ee85e8c35189a2c745502f
ms.sourcegitcommit: 44e85b95baf7dfb9e92fb38f03c2a1bc31765415
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/28/2019
ms.locfileid: "70100448"
---
# <a name="connect-to-a-sql-server-virtual-machine-on-azure-classic-deployment"></a>Připojení k virtuálnímu počítači s SQL Serverem v Azure (klasické nasazení)
> [!div class="op_single_selector"]
> * [Resource Manager](../sql/virtual-machines-windows-sql-connect.md)
> * [Classic](../classic/sql-connect.md)
> 
> 

## <a name="overview"></a>Přehled
Toto téma popisuje, jak se připojit k instanci SQL Server běžící na virtuálním počítači Azure. Zabývá se některými [obecnými scénáři připojení](#connection-scenarios) a pak poskytuje [podrobné pokyny ke konfiguraci SQL Serverho připojení ve virtuálním počítači Azure](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).

> [!IMPORTANT] 
> Azure má dva různé modely nasazení pro vytváření prostředků a práci s nimi: [Správce prostředků a klasický](../../../azure-resource-manager/resource-manager-deployment-model.md). Tento článek popisuje použití klasického modelu nasazení. Microsoft doporučuje, aby byl ve většině nových nasazení použit model Resource Manager. Pokud používáte Správce prostředků virtuálních počítačů, přečtěte si téma [připojení k virtuálnímu počítači s SQL Server na Azure pomocí Správce prostředků](../sql/virtual-machines-windows-sql-connect.md).

## <a name="connection-scenarios"></a>Scénáře připojení
Způsob, jakým se klient připojuje k SQL Server běžícímu na virtuálním počítači, se liší v závislosti na umístění klienta a konfiguraci počítače nebo sítě. Mezi tyto scénáře patří:

* [Připojení k SQL Server ve stejné cloudové službě](#connect-to-sql-server-in-the-same-cloud-service)
* [Připojení k SQL Server přes Internet](#connect-to-sql-server-over-the-internet)
* [Připojení k SQL Server ve stejné virtuální síti](#connect-to-sql-server-in-the-same-virtual-network)

> [!NOTE]
> Předtím, než se připojíte pomocí kterékoli z těchto metod, je třeba postupovat podle [kroků v tomto článku Konfigurace připojení](#steps-for-configuring-sql-server-connectivity-in-an-azure-vm).
> 
> 

### <a name="connect-to-sql-server-in-the-same-cloud-service"></a>Připojení k SQL Server ve stejné cloudové službě
Ve stejné cloudové službě se dá vytvořit několik virtuálních počítačů. Chcete-li pochopit tento scénář virtuálních počítačů, přečtěte si téma [Postup připojení virtuálních počítačů k virtuální síti nebo cloudové službě](/previous-versions/azure/virtual-machines/windows/classic/connect-vms-classic#connect-vms-in-a-standalone-cloud-service). Tento scénář je v případě, že se klient na jednom virtuálním počítači pokusí připojit k SQL Server běžícímu na jiném virtuálním počítači ve stejné cloudové službě.

V tomto scénáři se můžete připojit pomocí **názvu** virtuálního počítače (také uvedeného jako **název počítače** nebo název **hostitele** na portálu). Toto je název, který jste zadali pro virtuální počítač během vytváření. Pokud jste například najmenovali virtuální počítač SQL **mysqlvm**, může klientský virtuální počítač ve stejné cloudové službě použít k připojení následující připojovací řetězec:

    "Server=mysqlvm;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

### <a name="connect-to-sql-server-over-the-internet"></a>Připojení k SQL Server přes Internet
Pokud se chcete připojit ke svému databázovému stroji SQL Server z Internetu, musíte pro příchozí komunikaci TCP vytvořit koncový bod virtuálního počítače. Tento krok konfigurace Azure směruje příchozí provoz portu TCP na port TCP, který je pro virtuální počítač přístupný.

Pokud se chcete připojit přes Internet, musíte použít název DNS virtuálního počítače a číslo portu koncového bodu virtuálního počítače (nakonfigurovaného dále v tomto článku). Pokud chcete najít název DNS, přejděte na Azure Portal a vyberte **virtuální počítače (Classic)** . Pak vyberte svůj virtuální počítač. **Název DNS** se zobrazí v části **Přehled** .

Představte si třeba klasický virtuální počítač s názvem **mysqlvm** s názvem DNS **mysqlvm7777.cloudapp.NET** a koncovým bodem virtuálního počítače **57500**. Za předpokladu správného nakonfigurovaného připojení se pro přístup k virtuálnímu počítači odkudkoli na internetu dá použít následující připojovací řetězec:

    "Server=mycloudservice.cloudapp.net,57500;Integrated Security=false;User ID=<login_name>;Password=<your_password>"

I když to umožňuje připojení klientů přes Internet, neznamená to, že se kdokoli může připojit k vašemu SQL Server. Mimo klienty musí mít správné uživatelské jméno a heslo. Pro zvýšení zabezpečení nepoužívejte známý port 1433 pro veřejný koncový bod virtuálního počítače. A pokud je to možné, zvažte přidání seznamu ACL na koncový bod, který omezí provoz jenom na klienty, které povolíte. Pokyny k používání seznamů ACL s koncovými body najdete v tématu [Správa seznamu ACL na koncovém bodu](/previous-versions/azure/virtual-machines/windows/classic/setup-endpoints#manage-the-acl-on-an-endpoint).

> [!NOTE]
> Je důležité si uvědomit, že když použijete tento postup ke komunikaci s SQL Server, všechna odchozí data z datového centra Azure se budou vztahovat na běžné [ceny za odchozí přenosy dat](https://azure.microsoft.com/pricing/details/data-transfers/).
> 
> 

### <a name="connect-to-sql-server-in-the-same-virtual-network"></a>Připojení k SQL Server ve stejné virtuální síti
[Virtual Network](../../../virtual-network/virtual-networks-overview.md) umožňuje další scénáře. Virtuální počítače můžete připojit ve stejné virtuální síti i v případě, že tyto virtuální počítače existují v různých cloudových službách. A pomocí [sítě VPN typu Site-to-site](../../../vpn-gateway/vpn-gateway-site-to-site-create.md)můžete vytvořit hybridní architekturu, která propojuje virtuální počítače s místními sítěmi a počítači.

Virtuální sítě také umožňují připojení virtuálních počítačů Azure k doméně. Toto je jediný způsob, jak SQL Server pomocí ověřování systému Windows. Ostatní scénáře připojení vyžadují ověřování SQL s uživatelskými jmény a hesly.

Pokud budete chtít nakonfigurovat prostředí domény a ověřování systému Windows, nemusíte postupovat podle kroků v tomto článku ke konfiguraci veřejného koncového bodu nebo ověřování SQL a přihlášení. V tomto scénáři se můžete připojit k instanci SQL Server zadáním názvu SQL Server virtuálního počítače v připojovacím řetězci. Následující příklad předpokládá, že bylo nakonfigurováno ověřování systému Windows a že uživateli byl udělen přístup k instanci SQL Server.

    "Server=mysqlvm;Integrated Security=true"

## <a name="steps-for-configuring-sql-server-connectivity-in-an-azure-vm"></a>Postup konfigurace připojení SQL Server na virtuálním počítači Azure
Následující kroky ukazují, jak se připojit k instanci SQL Server přes Internet pomocí SQL Server Management Studio (SSMS). Stejný postup se ale vztahuje na to, aby byl váš SQL Server virtuální počítač dostupný pro vaše aplikace, a přitom běží místně i v Azure.

Předtím, než se můžete připojit k instanci SQL Server z jiného virtuálního počítače nebo z Internetu, je nutné provést následující úkoly, jak je popsáno v následujících částech:

* [Vytvořit koncový bod TCP pro virtuální počítač](#create-a-tcp-endpoint-for-the-virtual-machine)
* [Otevření portů TCP v bráně Windows Firewall](#open-tcp-ports-in-the-windows-firewall-for-the-default-instance-of-the-database-engine)
* [Konfigurace SQL Server pro naslouchání na protokolu TCP](#configure-sql-server-to-listen-on-the-tcp-protocol)
* [Konfigurace SQL Server pro ověřování ve smíšeném režimu](#configure-sql-server-for-mixed-mode-authentication)
* [Vytvořit SQL Server přihlášení ověřování](#create-sql-server-authentication-logins)
* [Určení názvu DNS virtuálního počítače](#determine-the-dns-name-of-the-virtual-machine)
* [Připojení k databázovému stroji z jiného počítače](#connect-to-the-database-engine-from-another-computer)

Cesta k připojení je shrnuta podle následujícího diagramu:

![Připojení k virtuálnímu počítači s SQL Server](../../../../includes/media/virtual-machines-sql-server-connection-steps/SQLServerinVMConnectionMap.png)

[!INCLUDE [Connect to SQL Server in a VM Classic TCP Endpoint](../../../../includes/virtual-machines-sql-server-connection-steps-classic-tcp-endpoint.md)]

[!INCLUDE [Connect to SQL Server in a VM](../../../../includes/virtual-machines-sql-server-connection-steps.md)]

[!INCLUDE [Connect to SQL Server in a VM Classic Steps](../../../../includes/virtual-machines-sql-server-connection-steps-classic.md)]

## <a name="next-steps"></a>Další kroky
Pokud také plánujete použít Skupiny dostupnosti AlwaysOn pro vysokou dostupnost a zotavení po havárii, měli byste zvážit implementaci naslouchacího procesu. Klienti databáze se připojují ke službě Listener, nikoli přímo k jedné z SQL Server instancí. Naslouchací proces směruje klienty do primární repliky ve skupině dostupnosti. Další informace najdete v tématu [Konfigurace naslouchacího procesu interního nástroje pro skupiny dostupnosti AlwaysOn v Azure](../classic/ps-sql-int-listener.md).

Je důležité si projít všechny osvědčené postupy zabezpečení pro SQL Server běžící na virtuálním počítači Azure. Další informace najdete v tématu [Informace o zabezpečení pro SQL Server na virtuálních počítačích Azure](../sql/virtual-machines-windows-sql-security.md).

[Projděte si mapy kurzů](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pro SQL Server na virtuálních počítačích Azure. 

Další témata související se spouštěním SQL Server ve virtuálních počítačích Azure najdete v tématu [SQL Server v Azure Virtual Machines](../sql/virtual-machines-windows-sql-server-iaas-overview.md).

