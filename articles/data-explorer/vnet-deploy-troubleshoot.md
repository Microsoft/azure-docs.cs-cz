---
title: Poradce při potížích s přístupem, ingestováním a provozem clusteru Azure Data Explorer ve virtuální síti
description: Poradce při potížích s připojením, ingestováním, vytvářením clusteru a provozem clusteru Azure Data Explorer ve vaší virtuální síti
author: basaba
ms.author: basaba
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 03/24/2020
ms.openlocfilehash: b50b971a3b1980ad35a1a939bdf25f1c9e6ac7ba
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80241657"
---
# <a name="troubleshoot-access-ingestion-and-operation-of-your-azure-data-explorer-cluster-in-your-virtual-network"></a>Poradce při potížích s přístupem, ingestováním a provozem clusteru Azure Data Explorer ve virtuální síti

V této části se dozvíte, jak řešit problémy s připojením, provozem a vytvářením clusteru pro cluster nasazený do [virtuální sítě](/azure/virtual-network/virtual-networks-overview).

## <a name="access-issues"></a>Problémy s přístupem

Pokud máte problém při přístupu ke clusteru pomocí veřejného (cluster.region.kusto.windows.net) nebo privátního (private-cluster.region.kusto.windows.net) koncového bodu a máte podezření, že souvisí s nastavením virtuální sítě, proveďte následující kroky, abyste problém vyřešit.

### <a name="check-tcp-connectivity"></a>Kontrola připojení TCP

První krok zahrnuje kontrolu připojení TCP pomocí operačního systému Windows nebo Linux.

# <a name="windows"></a>[Windows](#tab/windows)

   1. Stáhněte [tcping](https://www.elifulkerson.com/projects/tcping.php) do počítače, který se připojuje ke clusteru.
   2. Příkaz ping na cíl ze zdrojového počítače pomocí následujícího příkazu:

    ```cmd
     C:\> tcping -t yourcluster.kusto.windows.net 443 
    
     ** Pinging continuously.  Press control-c to stop **
    
     Probing 1.2.3.4:443/tcp - Port is open - time=100.00ms
     ```

# <a name="linux"></a>[Linux](#tab/linux)

   1. Instalace *netcatu* do počítače, který se připojuje ke clusteru

    ```bash
    $ apt-get install netcat
     ```

   2. Příkaz ping na cíl ze zdrojového počítače pomocí následujícího příkazu:

     ```bash
     $ netcat -z -v yourcluster.kusto.windows.net 443
    
     Connection to yourcluster.kusto.windows.net 443 port [tcp/https] succeeded!
     ```
---

Pokud test není úspěšný, postupujte podle následujících kroků. Pokud je test úspěšný, problém není způsoben problémem s připojením TCP. Přejděte k [provozním problémům](#cluster-creation-and-operations-issues) a dále se vyřešte.

### <a name="check-the-network-security-group-nsg"></a>Zkontrolujte skupinu zabezpečení sítě (NSG)

   Zkontrolujte, zda [skupina zabezpečení sítě](/azure/virtual-network/security-overview) (NSG) připojená k podsíti clusteru má příchozí pravidlo, které umožňuje přístup z IP adresy klienta pro port 443.

### <a name="check-route-table"></a>Zkontrolovat tabulku tras

   Pokud má podsíť clusteru nastavení vynuceného tunelového propojení do brány firewall (podsíť s [trasovací tabulkou](/azure/virtual-network/virtual-networks-udr-overview) obsahující výchozí trasu 0.0.0.0/0), ujistěte se, že ip adresa počítače má trasu s [dalším typem směrování](/azure/virtual-network/virtual-networks-udr-overview) do virtualnetwork/internet. Tato trasa je nutná, aby se zabránilo problémům s asymetrickou cestou.

## <a name="ingestion-issues"></a>Problémy s požitím

Pokud máte problémy s požitím a máte podezření, že to souvisí s nastavením virtuální sítě, proveďte následující kroky.

### <a name="check-ingestion-health"></a>Kontrola stavu požití

Zkontrolujte, zda [metriky ingestování clusteru](/azure/data-explorer/using-metrics#ingestion-health-and-performance-metrics) označují stav v pořádku.

### <a name="check-security-rules-on-data-source-resources"></a>Kontrola pravidel zabezpečení prostředků zdrojů dat

Pokud metriky označují, že ze zdroje dat nebyly zpracovány žádné události *(metrika Události zpracované* pro centra Událostí/IoT), ujistěte se, že prostředky zdroje dat (Event Hub nebo Storage) umožňují přístup z podsítě clusteru v pravidlech brány firewall nebo koncových bodech služby.

### <a name="check-security-rules-configured-on-clusters-subnet"></a>Kontrola pravidel zabezpečení nakonfigurovaných v podsíti clusteru

Ujistěte se, že podsíť clusteru má správně nakonfigurovaná pravidla nsg, UDR a brány firewall. Kromě toho otestujte připojení k síti pro všechny závislé koncové body. 

## <a name="cluster-creation-and-operations-issues"></a>Problémy s vytvářením a provozem clusteru

Pokud máte problémy s vytvářením nebo provozem clusteru a máte podezření, že to souvisí s nastavením virtuální sítě, postupujte podle následujících kroků k řešení problému.

### <a name="diagnose-the-virtual-network-with-the-rest-api"></a>Diagnostika virtuální sítě pomocí rozhraní REST API

[Klient ARMClient](https://chocolatey.org/packages/ARMClient) se používá k volání rozhraní REST API pomocí prostředí PowerShell. 

1. Přihlášení pomocí klienta ARMClient

   ```powerShell
   armclient login
   ```

1. Vyvolat operaci diagnostiky

    ```powershell
    $subscriptionId = '<subscription id>'
    $clusterName = '<name of cluster>'
    $resourceGroupName = '<resource group name>'
    $apiversion = '2019-11-09'
    
    armclient post "https://management.azure.com/subscriptions/$subscriptionId/resourceGroups/$resourceGroupName/providers/Microsoft.Kusto/clusters/$clusterName/diagnoseVirtualNetwork?api-version=$apiversion" -verbose
    ```

1. Zkontrolujte odpověď

    ```powershell
    HTTP/1.1 202 Accepted
    ...
    Azure-AsyncOperation: https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    ...
    ```

1. Počkejte na dokončení operace

    ```powershell
    armclient get https://management.azure.com/subscriptions/$subscriptionId/providers/Microsoft.Kusto/locations/{location}/operationResults/{operation-id}?api-version=2019-11-09
    
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "[Running/Failed/Completed]",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {...}
    }
    ```
    
   Počkejte, až se vlastnost *stavu* zobrazí *Dokončeno*, pak by mělo pole *vlastností* zobrazit:

    ```powershell
    {
      "id": "/subscriptions/{subscription-id}/providers/Microsoft.Kusto/locations/{location}/operationresults/{operation-id}",
      "name": "{operation-name}",
      "status": "Completed",
      "startTime": "{start-time}",
      "endTime": "{end-time}",
      "properties": {
        "Findings": [...]
      }
    }
    ```

Pokud *Findings* vlastnost zobrazuje prázdný výsledek, znamená to, že všechny síťové testy předány a žádná připojení jsou přerušena. Pokud je zobrazena následující chyba, *odchozí závislost {dependencyName}:{port}' nemusí být splněna (Odchozí)*, cluster nemůže dosáhnout koncových bodů závislé služby. Pokračujte následujícími kroky.

### <a name="check-network-security-group-nsg"></a>Zkontrolovat skupinu zabezpečení sítě (NSG)

Ujistěte se, že [skupina zabezpečení sítě](/azure/virtual-network/security-overview) je správně nakonfigurovaná podle pokynů v [závislostech pro nasazení virtuální sítě.](/azure/data-explorer/vnet-deployment#dependencies-for-vnet-deployment)

### <a name="check-route-table"></a>Zkontrolovat tabulku tras

Pokud má podsíť clusteru vynucené tunelování nastavené na bránu firewall (podsíť s [trasovou tabulkou](/azure/virtual-network/virtual-networks-udr-overview) obsahující výchozí trasu '0.0.0/0') ujistěte se, že [adresy IP pro správu](vnet-deployment.md#azure-data-explorer-management-ip-addresses)) a [IP adresy pro sledování stavu](vnet-deployment.md#health-monitoring-addresses) mají trasu s [dalším typem směrování](/azure/virtual-network/virtual-networks-udr-overview##next-hop-types-across-azure-tools) *Internet*a [předponu zdrojové adresy](/azure/virtual-network/virtual-networks-udr-overview#how-azure-selects-a-route) na *"management-ip/32"* a *"health-monitoring-ip/32"*. Tato trasa je nutná, aby se zabránilo problémům s asymetrickou cestou.

### <a name="check-firewall-rules"></a>Kontrola pravidel brány firewall

Pokud vynutíte odchozí provoz podsítě tunelového propojení na bránu firewall, ujistěte se, že všechny závislosti FQDN (například *.blob.core.windows.net*) jsou povoleny v konfiguraci brány firewall, jak je popsáno v [zabezpečení odchozího provozu pomocí brány firewall](/azure/data-explorer/vnet-deployment#securing-outbound-traffic-with-firewall).
