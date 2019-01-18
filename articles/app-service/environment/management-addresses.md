---
title: App Service Environment správu adresy – Azure
description: Seznam adres správy používá k příkazu služby App Service Environment
services: app-service
documentationcenter: na
author: ccompy
manager: stefsch
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.service: app-service
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/16/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 39ab31cd06707dbd488914da248941ab6d174c29
ms.sourcegitcommit: 9f07ad84b0ff397746c63a085b757394928f6fc0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54388761"
---
# <a name="app-service-environment-management-addresses"></a>Adresy pro správu aplikace app Service Environment

App Service Environment (ASE) je nasazení služby Azure App Service do podsítě ve službě Azure Virtual Network (VNet).  Služba ASE musí být dostupný z rovina správy používá ve službě Azure App Service.  Tato služba ASE správu přenosy prochází sítí řízené uživatelem. Pokud tento provoz je blokován nebo nesprávně směrovanými, že služba ASE zablokuje. Podrobnosti o sítí závislostí služby ASE najdete [síťových aspekty a App Service Environment][networking]. Obecné informace služby ase, můžete začít s [Úvod do služby App Service Environment][intro].

Všechny služby ase mají veřejnou virtuální IP Adresou stává jaký provoz správy. Příchozí provoz správy z tyto adresy pocházejí z na portech 454 a 455 na veřejných virtuálních IP adres vaší služby ase. Tento dokument obsahuje seznam zdrojové adresy služby App Service pro provoz správy do služby ASE. Tyto adresy jsou ve značce služby s názvem AppServiceManagement.

Značka služby s názvem AppServiceManagement ve skupinách zabezpečení sítě můžete použít k uzamčení příchozí přenosy správy do služby ASE.  

Níže uvedené adresy je možné nakonfigurovat ve směrovací tabulce. To je důležité, když provoz služby ASE platnými tunelové propojení virtuální sítě, ve kterém bude jinak pravděpodobně kvůli problému asymetrického směrování. Podrobnosti o tom, jak nakonfigurovat službu ASE fungovaly v prostředí, ve kterém odeslání odchozího provozu v místním prostředí, najdete v článku [konfigurace služby ASE vynucené tunelování][forcedtunnel]

## <a name="list-of-management-addresses"></a>Seznam adres pro správu ##

| Oblast | Adresy |
|--------|-----------|
| Všech veřejných oblastech | 70.37.57.58, 157.55.208.185, 52.174.22.21, 13.94.149.179, 13.94.143.126, 13.94.141.115, 52.178.195.197, 52.178.190.65, 52.178.184.149, 52.178.177.147, 13.75.127.117, 40.83.125.161, 40.83.121.56, 40.83.120.64, 52.187.56.50, 52.187.63.37, 52.187.59.251, 52.187.63.19, 52.165.158.140, 52.165.152.214, 52.165.154.193, 52.165.153.122, 104.44.129.255, 104.44.134.255, 104.44.129.243, 104.44.129.141, 23.102.188.65, 191.236.154.88, 13.64.115.203, 65.52.193.203, 70.37.89.222, 52.224.105.172, 23.102.135.246, 52.225.177.153, 65.52.172.237, 52.151.25.45, 40.124.47.188 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Konfiguruje se skupina zabezpečení sítě

S použitím skupin zabezpečení sítě není potřeba starat o jednotlivé adresy nebo udržování vlastní konfigurace. Je značka služby IP adresy s názvem AppServiceManagement, který je udržován v aktuálním stavu díky všechny adresy. Pokud chcete použít tuto značku služby IP adresu pro nsg, přejděte na portál, otevřete uživatelské rozhraní skupin zabezpečení sítě a vyberte pravidla zabezpečení příchozích dat. Pokud máte existující pravidlo k řízení příchozích přenosů ho upravit. Pokud tato skupina zabezpečení sítě nebylo vytvořeno pomocí služby ASE nebo pokud se všechny nové, pak vyberte **přidat**. V části zdrojové rozevírací nabídce vyberte **značka služby**.  V části značka zdrojové služby, vyberte ** AppServiceManagement **. Nastavit zdroj rozsahů portů \*, cíl **jakékoli**, rozsahy cílových portů k **454 455**, protokol k **TCP**a akci **povolit** . Pokud provádíte pravidla můžete třeba nastavit prioritu. 

![vytvořit skupinu zabezpečení sítě se značkou služby][1]

## <a name="configuring-a-route-table"></a>Konfigurace směrovací tabulky

Adresy pro správu je možné použít ve směrovací tabulce s dalším segmentem směrování do Internetu a ujistěte se, že se všechny příchozí přenosy správy přejít zpět prostřednictvím stejné cestě. Tyto postupy jsou potřeba při konfiguraci vynuceného tunelování. Pokud chcete vytvořit směrovací tabulku, můžete na portálu, Powershellu nebo rozhraní příkazového řádku Azure.  Níže jsou příkazy k vytvoření směrovací tabulky pomocí rozhraní příkazového řádku Azure z příkazového řádku Powershellu. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32 
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32 
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32

Po vytvoření směrovací tabulky, musíte nastavit na podsíti služby ASE.  

## <a name="get-your-management-addresses-from-api"></a>Získání adresy pro správu z rozhraní API ##

Můžete vytvořit seznam adresy pro správu, které odpovídají vaší služby ASE pomocí následujícího volání rozhraní API.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

Rozhraní API vrátí dokument JSON, který zahrnuje všechny příchozí adresy pro vaši službu ASE. Seznam adres zahrnuje virtuální IP adresy používané vaší služby ASE a rozsah adres podsítě služby ASE, samotný adresy pro správu.  

Pro volání rozhraní API se [armclient](https://github.com/projectkudu/ARMClient) použijte následující příkazy, ale nahraďte ID předplatného, skupiny prostředků a název služby ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
