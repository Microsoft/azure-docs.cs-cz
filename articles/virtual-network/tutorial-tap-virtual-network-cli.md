---
title: Vytvoření, změna nebo odstranění virtuální sítě TAP – rozhraní příkazového řádku Azure | Dokumentace Microsoftu
description: Zjistěte, jak vytvořit, změnit nebo odstranit virtuální síť TAP pomocí Azure CLI.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 09/17/2018
ms.author: kaanan
ms.openlocfilehash: 48ccbedd6e3a7da0ec487f27709a47f9364f7da3
ms.sourcegitcommit: 39397603c8534d3d0623ae4efbeca153df8ed791
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/12/2019
ms.locfileid: "56100053"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Spolupracovat s virtuální sítí TAP pomocí Azure CLI

Virtuální síť Azure TAP (terminál přístupový bod) vám umožní průběžně stream vašeho virtuálního počítače síťový provoz do síťových paketů kolekcí nebo analytics nástroj. Nástroj kolekcí nebo analytics poskytuje [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/) partnera. Seznam partnerských řešení, kteří jsou ověření pro práci s virtuální sítí TAP najdete v tématu [partnerská řešení](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Vytvoření virtuální sítě resource klepněte na

Čtení [požadavky](virtual-network-tap-overview.md#prerequisites) předtím, než vytvoříte virtuální síť klepnutím na prostředek. Můžete spouštět příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/bash), nebo pomocí rozhraní příkazového řádku Azure (CLI) z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, která nevyžaduje instalaci rozhraní příkazového řádku Azure ve vašem počítači. Musíte se přihlásit do Azure pomocí účtu, který má odpovídající [oprávnění](virtual-network-tap-overview.md#permissions). Tento článek vyžaduje použití Azure CLI verze 2.0.46 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Pokud používáte Azure CLI místně, musíte také spustit `az login` vytvořit připojení k Azure.

1. Načtení ID vašeho předplatného do proměnné, který se používá v pozdější fázi:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Nastavte id předplatného, které použijete k vytvoření virtuální sítě, klepněte na prostředek.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. ID předplatného, které budete používat při vytváření virtuální sítě resource klepněte na znovu zaregistrujte. Pokud se zobrazí chyba registrace při vytváření prostředku TAP, spusťte následující příkaz:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Pokud je cíl pro virtuální síť, klepněte na rozhraní sítě na síťové virtuální zařízení kolektoru nebo nástroj pro analýzu-

   - Načtěte konfiguraci protokolu IP síťového virtuálního zařízení síťového rozhraní do proměnné, který se používá v pozdější fázi. ID je koncový bod, který se použije k agregaci klepněte na provoz. Následující příklad načte ID *ipconfig1* konfigurace protokolu IP pro síťové rozhraní s názvem *myNetworkInterface*, ve skupině prostředků s názvem *myResourceGroup*:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Vytvoření virtuální sítě klepněte v oblasti westcentralus azure jako cíle a volitelně také portu vlastnost s použitím ID konfigurace protokolu IP. Port, který určuje v konfiguraci protokolu IP síťového rozhraní kde bude přijímat provoz klepněte na cílový port:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Pokud je cíl pro virtuální síť, klepněte na interní azure load balancer:
  
   - Načtení konfigurace IP front-endu nástroje pro vyrovnávání zatížení Azure interní do proměnné, který se používá v pozdější fázi. ID je koncový bod, který se použije k agregaci klepněte na provoz. Následující příklad načte ID *frontendipconfig1* konfigurace protokolu IP front-endu pro nástroj pro vyrovnávání zatížení s názvem *myInternalLoadBalancer*, ve skupině prostředků s názvem  *myResourceGroup*:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```
   - Vytvoření virtuální sítě s použitím ID konfigurace protokolu IP front-endu jako cíle a volitelně také portu vlastnost TAP. Port, který určuje v konfiguraci protokolu IP front-endu, kde bude přijímat provoz klepněte na cílový port:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Vytvoření virtuální sítě, klepněte na potvrďte:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Přidat klepnutím na konfiguraci na síťové rozhraní

1. Načtení ID prostředku klepněte na existující virtuální sítě. Následující příklad načte virtuální síť s názvem TAP *myTap* ve skupině prostředků s názvem *myResourceGroup*:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Vytvořte konfiguraci klepnutím na síťovém rozhraní monitorovaných virtuálních počítačů. Následující příklad vytvoří klepněte na konfiguraci pro síťové rozhraní s názvem *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Vytvoření konfigurace klepněte na potvrďte:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Odstranit klepnutím na konfiguraci na síťové rozhraní

   ```azure-cli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Virtuální síť seznamu klepne v rámci předplatného

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Odstranit virtuální síť TAP ve skupině prostředků

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
