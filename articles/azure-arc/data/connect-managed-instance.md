---
title: Připojení ke spravované instanci SQL ARC s povoleným voláním Azure
description: Připojení ke spravované instanci SQL ARC s povoleným voláním Azure
services: azure-arc
ms.service: azure-arc
ms.subservice: azure-arc-data
author: vin-yu
ms.author: vinsonyu
ms.reviewer: mikeray
ms.date: 09/22/2020
ms.topic: how-to
ms.openlocfilehash: 3277dc4d9c4485b117bfcfd1d6e130e7370cd8c2
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "90936093"
---
# <a name="connect-to-azure-arc-enabled-sql-managed-instance"></a>Připojení ke spravované instanci SQL ARC s povoleným voláním Azure

V tomto článku se dozvíte, jak se můžete připojit ke spravované instanci SQL ARC s povoleným voláním Azure. 

[!INCLUDE [azure-arc-data-preview](../../../includes/azure-arc-data-preview.md)]

## <a name="view-azure-arc-enabled-sql-managed-instances"></a>Zobrazit spravované instance serveru SQL s podporou ARC Azure

Pro zobrazení spravované instance SQL ARC Azure a externích koncových bodů použijte následující příkaz:

```console
azdata arc sql mi list
```

Výstup by měl vypadat takto:

```console
Name    Replicas    ExternalEndpoint    State
------  ----------  ----------------  -------
sqldemo 1/1         10.240.0.4:32023  Ready
```

Pokud používáte AKS nebo kubeadm nebo OpenShift atd., můžete zde zkopírovat externí IP adresu a číslo portu a připojit se k němu pomocí oblíbeného nástroje pro připojení k instanci SQL serveru nebo Azure SQL, jako je například Azure Data Studio nebo SQL Server Management Studio.  Pokud ale používáte virtuální počítač s rychlým startem, přečtěte si další informace o tom, jak se připojit k tomuto virtuálnímu počítači mimo Azure. 

> [!NOTE]
> Vaše podnikové zásady můžou blokovat přístup k IP adrese a portu, zejména pokud se vytvoří ve veřejném cloudu.

## <a name="connect"></a>Připojit 

Připojení pomocí Azure Data Studio, SQL Server Management Studio nebo SQLCMD

Otevřete Azure Data Studio a připojte se k instanci s IP adresou externího koncového bodu a číslem portu uvedeným výše. Pokud používáte virtuální počítač Azure, budete potřebovat _veřejnou_ IP adresu, kterou můžete identifikovat pomocí [zvláštní poznámky o nasazeních virtuálních počítačů Azure](#special-note-about-azure-virtual-machine-deployments).

Například:

- Server: 52.229.9.30, 30913
- Uživatelské jméno: SA
- Heslo: zadané heslo SQL v době zřizování

> [!NOTE]
> Můžete použít Azure Data Studio [zobrazení řídicích panelů spravované instance SQL](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards).

Pokud se chcete připojit pomocí SQLCMD nebo Linux nebo Windows, můžete použít podobný příkaz. Po zobrazení výzvy zadejte heslo SQL:

```bash
sqlcmd -S 52.229.9.30,30913 -U sa
```

## <a name="special-note-about-azure-virtual-machine-deployments"></a>Zvláštní Poznámka o nasazeních virtuálních počítačů Azure

Pokud používáte virtuální počítač Azure, IP adresa koncového bodu nebude zobrazovat veřejnou IP adresu. K vyhledání externí IP adresy použijte tento příkaz:

```console
az network public-ip list -g azurearcvm-rg --query "[].{PublicIP:ipAddress}" -o table
```

Pak můžete zkombinovat veřejnou IP adresu s portem a vytvořit tak připojení.

Také může být nutné vystavit port instance SQL prostřednictvím brány zabezpečení sítě (NSG). Pokud chcete povolit provoz přes (NSG), budete muset přidat pravidlo, které můžete provést pomocí následujícího příkazu.

Pokud chcete nastavit pravidlo, budete muset znát název vašeho NSGu, který můžete najít pomocí následujícího příkazu:

```console
az network nsg list -g azurearcvm-rg --query "[].{NSGName:name}" -o table
```

Jakmile budete mít název NSG, můžete přidat pravidlo brány firewall pomocí následujícího příkazu. Ukázkové hodnoty zde vytvoří pravidlo NSG pro port 30913 a umožní připojení z **jakékoli** zdrojové IP adresy.  Nejedná se o osvědčený postup zabezpečení.  V zájmu lepšího zabezpečení můžete zadat hodnotu -source-address-prefixes, která je specifická pro IP adresu vašeho klienta nebo rozsah IP adres, který pokrývá IP adresy vašeho týmu nebo vaší organizace.

Nahraďte hodnotu `--destination-port-ranges` parametru níže číslem portu, který jste získali z `azdata sql instance list` příkazu F výše.

```console
az network nsg rule create -n db_port --destination-port-ranges 30913 --source-address-prefixes '*' --nsg-name azurearcvmNSG --priority 500 -g azurearcvm-rg --access Allow --description 'Allow port through for db access' --destination-address-prefixes '*' --direction Inbound --protocol Tcp --source-port-ranges '*'
```

## <a name="next-steps"></a>Další kroky

- [Zobrazit řídicí panely spravované instance SQL](azure-data-studio-dashboards.md#view-the-sql-managed-instance-dashboards)
- [Zobrazení spravované instance SQL v Azure Portal](view-arc-data-services-inventory-in-azure-portal.md)
