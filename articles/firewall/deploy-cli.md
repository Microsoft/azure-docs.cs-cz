---
title: Nasazení a konfigurace Azure Firewall pomocí rozhraní příkazového příkazu Azure
description: V tomto článku se dozvíte, jak nasadit a nakonfigurovat Azure Firewall pomocí rozhraní příkazového příkazu Konstatování Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.date: 08/29/2019
ms.author: victorh
ms.topic: article
ms.openlocfilehash: e97783d1a32916cad151f1d0858a8190d0005fd0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "73831968"
---
# <a name="deploy-and-configure-azure-firewall-using-azure-cli"></a>Nasazení a konfigurace Azure Firewall pomocí rozhraní příkazového příkazu Azure

Řízení odchozího síťového přístupu je důležitou součástí celkového plánu zabezpečení sítě. Můžete například omezit přístup k webovým serverům. Nebo můžete chtít omezit odchozí IP adresy a porty, které jsou přístupné.

Jedním ze způsobů, jak můžete řídit odchozí síťový přístup z podsítě Azure, je použít Azure Firewall. Azure Firewall umožňuje nakonfigurovat:

* Pravidla aplikace, která definují plně kvalifikované názvy domén, ke kterým je možné získat přístup z podsítě. FQDN může také [obsahovat instance SQL](sql-fqdn-filtering.md).
* Pravidla sítě, která definují zdrojovou adresu, protokol, cílový port a cílovou adresu.

Síťový provoz podléhá nakonfigurovaným pravidlům brány firewall, když ho směrujete na bránu firewall jako na výchozí bránu podsítě.

Pro tento článek vytvoříte zjednodušenou jednu virtuální síť se třemi podsítěmi pro snadné nasazení. Pro nasazení v produkčním prostředí se doporučuje [model rozbočovače a paprsku.](https://docs.microsoft.com/azure/architecture/reference-architectures/hybrid-networking/hub-spoke) Brána firewall je ve vlastní virtuální síti. Servery úloh jsou v partnerských virtuálních sítích ve stejné oblasti s jednou nebo více podsítěmi.

* **AzureFirewallSubnet** – v této podsíti bude brána firewall.
* **Workload-SN** – v této podsíti bude server úloh. Provoz této podsítě bude procházet bránou firewall.
* **Jump-SN** – v této podsíti bude „jump“ server. Jump server má veřejnou IP adresu, ke které se můžete připojit pomocí vzdálené plochy. Odtud se pak můžete připojit k serveru úloh (pomocí další vzdálené plochy).

![Kurz síťové infrastruktury](media/tutorial-firewall-rules-portal/Tutorial_network.png)

V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * Nastavit testovací síťové prostředí
> * Nasadit bránu firewall
> * Vytvoření výchozí trasy
> * Konfigurace pravidla aplikace pro povolení přístupu k www.google.com
> * Nakonfigurovat pravidlo sítě pro povolení přístupu k externím serverům DNS
> * Testování brány firewall

Pokud chcete, můžete tento postup provést pomocí [portálu Azure nebo](tutorial-firewall-deploy-portal.md) [Azure PowerShell](deploy-ps.md).

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) než začnete.

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

## <a name="prerequisites"></a>Požadavky

### <a name="azure-cli"></a>Azure CLI

Pokud se rozhodnete nainstalovat a používat příkaz cli místně, spusťte Azure CLI verze 2.0.4 nebo novější. Chcete-li najít verzi, **spusťte az --version**. Informace o instalaci nebo upgradu najdete [v tématu Instalace příkazového příkazového příkazu k webu Azure]( /cli/azure/install-azure-cli).

Nainstalujte rozšíření Azure Firewall:

```azurecli-interactive
az extension add -n azure-firewall
```


## <a name="set-up-the-network"></a>Nastavit síť

Nejprve vytvořte skupinu prostředků obsahující prostředky potřebné k nasazení brány firewall. Potom vytvořte virtuální síť, podsítě a testovací servery.

### <a name="create-a-resource-group"></a>Vytvoření skupiny prostředků

Skupina prostředků obsahuje všechny prostředky pro nasazení.

```azurecli-interactive
az group create --name Test-FW-RG --location eastus
```

### <a name="create-a-vnet"></a>Vytvoření virtuální sítě

Tato virtuální síť má tři podsítě.

> [!NOTE]
> Velikost podsítě AzureFirewallSubnet je /26. Další informace o velikosti podsítě najdete v [tématu Nejčastější dotazy k bráně Azure Firewall](firewall-faq.md#why-does-azure-firewall-need-a-26-subnet-size).

```azurecli-interactive
az network vnet create \
  --name Test-FW-VN \
  --resource-group Test-FW-RG \
  --location eastus \
  --address-prefix 10.0.0.0/16 \
  --subnet-name AzureFirewallSubnet \
  --subnet-prefix 10.0.1.0/26
az network vnet subnet create \
  --name Workload-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.2.0/24
az network vnet subnet create \
  --name Jump-SN \
  --resource-group Test-FW-RG \
  --vnet-name Test-FW-VN   \
  --address-prefix 10.0.3.0/24
```

### <a name="create-virtual-machines"></a>Vytvoření virtuálních počítačů

Teď vytvoříte virtuální počítače pro jump server a server úloh a umístíte je do příslušných podsítí.
Po zobrazení výzvy zadejte heslo pro virtuální počítač.

Vytvořte virtuální počítač Srv-Jump.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Jump \
    --location eastus \
    --image win2016datacenter \
    --vnet-name Test-FW-VN \
    --subnet Jump-SN \
    --admin-username azureadmin
az vm open-port --port 3389 --resource-group Test-FW-RG --name Srv-Jump
```



Vytvořte nic pro Srv-Work s konkrétními IP adresami serveru DNS a bez veřejné IP adresy, se kterou by bylo možné testovat.

```azurecli-interactive
az network nic create \
    -g Test-FW-RG \
    -n Srv-Work-NIC \
   --vnet-name Test-FW-VN \
   --subnet Workload-SN \
   --public-ip-address "" \
   --dns-servers 209.244.0.3 209.244.0.4
```

Teď vytvořte virtuální počítač pracovního vytížení.
Po zobrazení výzvy zadejte heslo pro virtuální počítač.

```azurecli-interactive
az vm create \
    --resource-group Test-FW-RG \
    --name Srv-Work \
    --location eastus \
    --image win2016datacenter \
    --nics Srv-Work-NIC \
    --admin-username azureadmin
```

## <a name="deploy-the-firewall"></a>Nasazení brány firewall

Teď nasaďte bránu firewall do virtuální sítě.

```azurecli-interactive
az network firewall create \
    --name Test-FW01 \
    --resource-group Test-FW-RG \
    --location eastus
az network public-ip create \
    --name fw-pip \
    --resource-group Test-FW-RG \
    --location eastus \
    --allocation-method static \
    --sku standard
az network firewall ip-config create \
    --firewall-name Test-FW01 \
    --name FW-config \
    --public-ip-address fw-pip \
    --resource-group Test-FW-RG \
    --vnet-name Test-FW-VN
az network firewall update \
    --name Test-FW01 \
    --resource-group Test-FW-RG 
az network public-ip show \
    --name fw-pip \
    --resource-group Test-FW-RG
fwprivaddr="$(az network firewall ip-config list -g Test-FW-RG -f Test-FW01 --query "[?name=='FW-config'].privateIpAddress" --output tsv)"
```

Poznamenejte si privátní IP adresu. Budete ji potřebovat později při vytváření výchozí trasy.

## <a name="create-a-default-route"></a>Vytvoření výchozí trasy

Vytvoření tabulky se zakázaným šířením trasy protokolu BGP

```azurecli-interactive
az network route-table create \
    --name Firewall-rt-table \
    --resource-group Test-FW-RG \
    --location eastus \
    --disable-bgp-route-propagation true
```

Vytvořte trasu.

```azurecli-interactive
az network route-table route create \
  --resource-group Test-FW-RG \
  --name DG-Route \
  --route-table-name Firewall-rt-table \
  --address-prefix 0.0.0.0/0 \
  --next-hop-type VirtualAppliance \
  --next-hop-ip-address $fwprivaddr
```

Přidružení tabulky postupu k podsíti

```azurecli-interactive
az network vnet subnet update \
    -n Workload-SN \
    -g Test-FW-RG \
    --vnet-name Test-FW-VN \
    --address-prefixes 10.0.2.0/24 \
    --route-table Firewall-rt-table
```

## <a name="configure-an-application-rule"></a>Konfigurace pravidla aplikace

Pravidlo aplikace umožňuje odchozí přístup k www.google.com.

```azurecli-interactive
az network firewall application-rule create \
   --collection-name App-Coll01 \
   --firewall-name Test-FW01 \
   --name Allow-Google \
   --protocols Http=80 Https=443 \
   --resource-group Test-FW-RG \
   --target-fqdns www.google.com \
   --source-addresses 10.0.2.0/24 \
   --priority 200 \
   --action Allow
```

Brána Azure Firewall obsahuje předdefinovanou kolekci pravidel pro infrastrukturu plně kvalifikovaných názvů domén, které jsou ve výchozím nastavení povolené. Tyto plně kvalifikované názvy domén jsou specifické pro tuto platformu a pro jiné účely je nelze použít. Další informace najdete v tématu [Plně kvalifikované názvy domén infrastruktury](infrastructure-fqdns.md).

## <a name="configure-a-network-rule"></a>Konfigurace pravidla sítě

Síťové pravidlo umožňuje odchozí přístup ke dvěma ADRESám IP na portu 53 (DNS).

```azurecli-interactive
az network firewall network-rule create \
   --collection-name Net-Coll01 \
   --destination-addresses 209.244.0.3 209.244.0.4 \
   --destination-ports 53 \
   --firewall-name Test-FW01 \
   --name Allow-DNS \
   --protocols UDP \
   --resource-group Test-FW-RG \
   --priority 200 \
   --source-addresses 10.0.2.0/24 \
   --action Allow
```

## <a name="test-the-firewall"></a>Testování brány firewall

Nyní otestujte bránu firewall a potvrďte, že funguje podle očekávání.

1. Všimněte si privátní IP adresy virtuálního počítače **Srv-Work:**

   ```azurecli-interactive
   az vm list-ip-addresses \
   -g Test-FW-RG \
   -n Srv-Work
   ```

1. Připojte vzdálenou plochu k virtuálnímu počítači **Srv-Jump** a přihlaste se. Odtud otevřete připojení ke vzdálené ploše k privátní IP adrese **Srv-Work** a přihlaste se.

3. Na **SRV-Work**otevřete okno Prostředí PowerShell a spusťte následující příkazy:

   ```
   nslookup www.google.com
   nslookup www.microsoft.com
   ```

   Oba příkazy by měly vracet odpovědi, což ukazuje, že vaše dotazy DNS procházejí bránou firewall.

1. Spusťte následující příkazy:

   ```
   Invoke-WebRequest -Uri https://www.google.com
   Invoke-WebRequest -Uri https://www.google.com

   Invoke-WebRequest -Uri https://www.microsoft.com
   Invoke-WebRequest -Uri https://www.microsoft.com
   ```

   Požadavky `www.google.com` by měly být `www.microsoft.com` úspěšné a požadavky by měly selhat. To ukazuje, že pravidla brány firewall fungují podle očekávání.

Takže teď jste ověřili, že pravidla brány firewall fungují:

* Názvy DNS můžete přeložit pomocí nakonfigurovaného externího serveru DNS.
* Můžete přejít na jediný povolený plně kvalifikovaný název domény, ale jinam už ne.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Prostředky brány firewall můžete zachovat pro další kurz, nebo pokud již není potřeba, odstraňte skupinu prostředků **Test-FW-RG** a odstraňte všechny prostředky související s bránou firewall:

```azurecli-interactive
az group delete \
  -n Test-FW-RG
```

## <a name="next-steps"></a>Další kroky

* [Kurz: Monitorování protokolů brány Azure Firewall](./tutorial-diagnostics.md)
