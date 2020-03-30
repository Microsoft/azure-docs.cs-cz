---
title: Vytvoření, změna nebo odstranění tap virtuální sítě – rozhraní příkazového příkazu Azure
description: Zjistěte, jak vytvořit, změnit nebo odstranit klepnut na virtuální síť pomocí rozhraní příkazového příkazového příkazu Azure.
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
ms.date: 03/18/2018
ms.author: kaanan
ms.openlocfilehash: 56288a65dc9e5b12a12393965b9670e394146181
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80234973"
---
# <a name="work-with-a-virtual-network-tap-using-the-azure-cli"></a>Práce s rozhraním TAP ve virtuální síti pomocí rozhraní příkazového příkazu Azure

Azure virtual network TAP (Terminal Access Point) umožňuje nepřetržitě streamovat provoz sítě virtuálních strojů do kolektoru síťových paketů nebo analytického nástroje. Kolektor nebo analytický nástroj je poskytován partnerem [síťového virtuálního zařízení.](https://azure.microsoft.com/solutions/network-appliances/) Seznam partnerských řešení, která jsou ověřena pro práci s technologií TAP ve virtuální síti, naleznete [v tématu partnerská řešení](virtual-network-tap-overview.md#virtual-network-tap-partner-solutions). 

## <a name="create-a-virtual-network-tap-resource"></a>Vytvoření prostředku TAP ve virtuální síti

Před vytvořením prostředku TAP ve virtuální síti si přečtěte [požadavky.](virtual-network-tap-overview.md#prerequisites) Můžete spustit příkazy, které následují v [Prostředí Azure Cloud Shell](https://shell.azure.com/bash), nebo spuštěním rozhraní příkazového řádku Azure (CLI) z vašeho počítače. Azure Cloud Shell je bezplatná interaktivní prostředí, která nevyžaduje instalaci azure CLI na vašem počítači. K Azure se musíte přihlásit pomocí účtu, který má příslušná [oprávnění](virtual-network-tap-overview.md#permissions). Tento článek vyžaduje Azure CLI verze 2.0.46 nebo novější. Nainstalovanou verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace Azure CLI 2.0](/cli/azure/install-azure-cli). Virtuální síť TAP je v současné době k dispozici jako rozšíření. Chcete-li nainstalovat rozšíření, `az extension add -n virtual-network-tap`musíte spustit . Pokud používáte Azure CLI místně, musíte také `az login` spustit k vytvoření připojení s Azure.

1. Načíst ID předplatného do proměnné, která se používá v pozdějším kroku:

   ```azurecli-interactive
   subscriptionId=$(az account show \
   --query id \
   --out tsv)
   ```

2. Nastavte id předplatného, které budete používat k vytvoření prostředku TAP virtuální sítě.

   ```azurecli-interactive
   az account set --subscription $subscriptionId
   ```

3. Znovu zaregistrujte ID předplatného, které použijete k vytvoření prostředku TAP virtuální sítě. Pokud se při vytváření prostředku TAP zobrazí chyba registrace, spusťte následující příkaz:

   ```azurecli-interactive
   az provider register --namespace Microsoft.Network --subscription $subscriptionId
   ```

4. Pokud je cílem virtuální sítě TAP síťové rozhraní síťového zařízení pro kolektor nebo analytický nástroj -

   - Načtěte konfiguraci IP síťového rozhraní síťového zařízení do proměnné, která se používá v pozdějším kroku. ID je koncový bod, který bude agregovat provoz TAP. Následující příklad načte ID konfigurace IP *ipconfig1* pro síťové rozhraní s názvem *myNetworkInterface*ve skupině prostředků s názvem *myResourceGroup*:

      ```azurecli-interactive
       IpConfigId=$(az network nic ip-config show \
       --name ipconfig1 \
       --nic-name myNetworkInterface \
       --resource-group myResourceGroup \
       --query id \
       --out tsv)
      ```

   - Vytvořte virtuální síť TAP v oblasti westcentralus azure pomocí ID konfigurace IP jako cíl a volitelnou vlastnost portu. Port určuje cílový port v konfiguraci IP síťového rozhraní, kde bude přijat provoz TAP :  

      ```azurecli-interactive
       az network vnet tap create \
       --resource-group myResourceGroup \
       --name myTap \
       --destination $IpConfigId \
       --port 4789 \
       --location westcentralus
      ```

5. Pokud je cílem pro virtuální síť TAP azure interní nástroj pro vyrovnávání zatížení:
  
   - Načtěte front-end ovou konfiguraci IP nástroje nástroje pro interní nástroj pro vyrovnávání zatížení Azure do proměnné, která se používá v pozdějším kroku. ID je koncový bod, který bude agregovat provoz TAP. Následující příklad načte ID *frontendipconfig1 front-end* konfigurace IP konfigurace pro nástroj pro vyrovnávání zatížení s názvem *myInternalLoadBalancer*, ve skupině prostředků s názvem *myResourceGroup*:

      ```azurecli-interactive
      FrontendIpConfigId=$(az network lb frontend-ip show \
      --name frontendipconfig1 \
      --lb-name myInternalLoadBalancer \
      --resource-group myResourceGroup \
      --query id \
      --out tsv)
      ```

   - Vytvořte protokol TAP ve virtuální síti pomocí ID konfigurace ip adresy front-end jako cíl a volitelnou vlastnost portu. Port určuje cílový port v konfiguraci IP front-endu, kde bude přijat provoz TAP :  

      ```azurecli-interactive
      az network vnet tap create \
      --resource-group myResourceGroup \
      --name myTap \
      --destination $FrontendIpConfigId \
      --port 4789 \
     --location westcentralus
     ```

6. Potvrzení vytvoření virtuální sítě TAP:

   ```azurecli-interactive
   az network vnet tap show \
   --resource-group myResourceGroup
   --name myTap
   ```

## <a name="add-a-tap-configuration-to-a-network-interface"></a>Přidání konfigurace TAP do síťového rozhraní

1. Načíst ID existujícího prostředku rozhraní TAP virtuální sítě. Následující příklad načte virtuální síť TAP s názvem *myTap* ve skupině prostředků s názvem *myResourceGroup*:

   ```azurecli-interactive
   tapId=$(az network vnet tap show \
   --name myTap \
   --resource-group myResourceGroup \
   --query id \
   --out tsv)
   ```

2. Vytvořte konfiguraci TAP v síťovém rozhraní monitorovaného virtuálního počítače. Následující příklad vytvoří konfiguraci tap pro síťové rozhraní s názvem *myNetworkInterface*:

   ```azurecli-interactive
   az network nic vtap-config create \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --vnet-tap $tapId \
   --name mytapconfig \
   --subscription subscriptionId
   ```

3. Potvrďte vytvoření konfigurace TAP:

   ```azurecli-interactive
   az network nic vtap-config show \
   --resource-group myResourceGroup \
   --nic-name myNetworkInterface \
   --name mytapconfig \
   --subscription subscriptionId
   ```

## <a name="delete-the-tap-configuration-on-a-network-interface"></a>Odstranění konfigurace TAP v síťovém rozhraní

   ```azurecli-interactive
   az network nic vtap-config delete \
   --resource-group myResourceGroup \
   --nic myNetworkInterface \
   --name myTapConfig \
   --subscription subscriptionId
   ```

## <a name="list-virtual-network-taps-in-a-subscription"></a>Vyps virtuální sítě v předplatném

   ```azurecli-interactive
   az network vnet tap list
   ```

## <a name="delete-a-virtual-network-tap-in-a-resource-group"></a>Odstranění virtuální sítě TAP ve skupině prostředků

   ```azurecli-interactive
   az network vnet tap delete \
   --resource-group myResourceGroup \
   --name myTap
   ```
