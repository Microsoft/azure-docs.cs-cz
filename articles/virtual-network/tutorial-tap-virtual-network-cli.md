---
title: Vytvoření, změna nebo odstranění virtuální sítě klepněte na Azure CLI
description: Naučte se, jak vytvořit, změnit nebo odstranit virtuální síť, klepněte na Azure CLI.
services: virtual-network
documentationcenter: na
author: karthikananth
manager: ganesr
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-network
ms.devlang: NA
ms.topic: how-to
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/18/2018
ms.author: kaanan
ms.custom: devx-track-azurecli
ms.openlocfilehash: 8e3a56e4a6eb1fb6eb633021178ef78f8ac7287d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "96014784"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Práce s virtuální sítí klepněte pomocí Azure CLI.

KLEPNUTÍ na virtuální síť Azure (terminálový přístupový bod) umožňuje nepřetržitě streamovat síťový provoz virtuálního počítače do nástroje pro shromažďování síťových paketů nebo pro analýzu. Sběrač nebo nástroj pro analýzu poskytuje síťový partner pro [síťové virtuální zařízení](https://azure.microsoft.com/solutions/network-appliances/) . Seznam partnerských řešení, která jsou ověřená pro práci s virtuální sítí, najdete v tématu [Partnerská řešení](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Vytvoření virtuální sítě klepnutím na prostředek

[Požadavky](virtual-network-tap-overview.md#prerequisites) na čtení před vytvořením virtuální sítě klepněte na prostředek. Můžete spustit příkazy, které následují v [Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku Azure (CLI) z počítače. Azure Cloud Shell je bezplatné interaktivní prostředí, které nevyžaduje instalaci rozhraní příkazového řádku Azure CLI do vašeho počítače. Musíte se přihlásit k Azure pomocí účtu, který má příslušná [oprávnění](virtual-network-tap-overview.md#permissions). Tento článek vyžaduje Azure CLI verze 2.0.46 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Klepnutí na virtuální síť je aktuálně k dispozici jako rozšíření. Chcete-li nainstalovat rozšíření, které je třeba spustit `az extension add -n virtual-network-tap` . Pokud používáte Azure CLI místně, musíte také spustit, `az login` aby se vytvořilo připojení k Azure.

1. Načtěte ID vašeho předplatného na proměnnou, která se používá v pozdějším kroku:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Nastavte ID předplatného, které použijete k vytvoření virtuální sítě klepněte na prostředek.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Znovu zaregistrujte ID předplatného, které použijete k vytvoření virtuální sítě klepněte na prostředek. Pokud se při vytváření zdroje klepnutím zobrazí chyba registrace, spusťte následující příkaz:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Pokud je cíl pro virtuální síť klepněte na síťové rozhraní síťového virtuálního zařízení pro kolektor nebo nástroj pro analýzu –

   - Načtěte konfiguraci IP síťového rozhraní síťového virtuálního zařízení do proměnné, která se používá v pozdějším kroku. ID je koncový bod, který bude agregovat provoz klepnutím. Následující příklad načte ID konfigurace IP adresy *ipconfig1* pro síťové rozhraní s názvem *myNetworkInterface* ve skupině prostředků s názvem *myResourceGroup*:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Vytvořte virtuální síť klepnutím v westcentralus oblasti Azure pomocí ID konfigurace IP adresy jako cíle a volitelné vlastnosti portu. Port Určuje cílový port pro konfiguraci protokolu IP rozhraní sítě, kde bude přijímán přenos klepněte:  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Pokud je cíl pro virtuální síť klepněte na interní nástroj pro vyrovnávání zatížení Azure:
  
   - Načtěte konfiguraci IP adresy front-endu interního nástroje pro vyrovnávání zatížení do proměnné, která se používá v pozdějším kroku. ID je koncový bod, který bude agregovat provoz klepnutím. Následující příklad načte ID konfigurace front-end IP adresy *frontendipconfig1* pro nástroj pro vyrovnávání zatížení s názvem *myInternalLoadBalancer* ve skupině prostředků s názvem *myResourceGroup*:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Vytvořte virtuální síť klepnutím na použít ID konfigurace IP adresy front-endu jako cíle a volitelné vlastnosti portu. Port Určuje cílový port pro konfiguraci IP adresy front-endu, kde bude přijatý přenos klepněte:  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Potvrďte vytvoření virtuální sítě klepnutím na:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Přidat konfiguraci klepnutím do síťového rozhraní

1. Načte ID existující virtuální sítě klepněte na prostředek. Následující příklad načte virtuální síť s názvem *myTap* ve skupině prostředků s názvem *myResourceGroup*:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Vytvořte klepnutím konfiguraci v síťovém rozhraní monitorovaného virtuálního počítače. Následující příklad vytvoří konfiguraci klepnutím pro síťové rozhraní s názvem *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Potvrďte vytvoření konfigurace klepnutí:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Odstraní konfiguraci klepnutí na síťové rozhraní.

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Rozevírací seznam virtuálních sítí v rámci předplatného

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Odstranění virtuální sítě klepnutím na skupinu prostředků

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
