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
ms.date: 04/03/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: f76dd423cb3f7fbae6cc88d064e49dc2d56f1a1c
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "60766053"
---
# <a name="app-service-environment-management-addresses"></a>Adresy pro správu aplikace app Service Environment

App Service Environment (ASE) je nasazení jednoho tenanta služby Azure App Service, která běží ve službě Azure Virtual Network (VNet).  Zatímco služby ASE spouštět ve vaší virtuální síti, musí i nadále být přístupný z mnoha vyhrazené IP adresy, které se používají ve službě Azure App Service pro správu služby.  V případě služby ASE prochází provoz správy sítě řízené uživatelem. Pokud tento provoz je blokován nebo nesprávně směrovanými, že služba ASE zablokuje. Podrobnosti o sítí závislostí služby ASE najdete [síťových aspekty a App Service Environment][networking]. Obecné informace služby ase, můžete začít s [Úvod do služby App Service Environment][intro].

Všechny služby ase mají veřejnou virtuální IP Adresou stává jaký provoz správy. Příchozí provoz správy z tyto adresy pocházejí z na portech 454 a 455 na veřejných virtuálních IP adres vaší služby ase. Tento dokument obsahuje seznam zdrojové adresy služby App Service pro provoz správy do služby ASE. Tyto adresy jsou také v značka služby IP adresy s názvem AppServiceManagement.

Níže uvedené adresy je možné nakonfigurovat ve směrovací tabulce, aby se zabránilo asymetrického směrování potíže s přenosy dat správy. Trasy reagovat na provoz na úrovni protokolu IP a nemají povědomí o směr přenosu nebo, který provoz je součástí zprávy s odpovědí protokolu TCP. Pokud adresa odpověď pro požadavek protokolu TCP je jiná než adresa, kdy byla vyslána, je nutné kvůli problému asymetrického směrování. Pokud chcete vyhnout asymetrického směrování problémy s vaší služby ASE provoz správy, je potřeba zajistit, že odpovědi jsou odesílány zpět ze stejné adresy, které byly odeslány do. Podrobnosti o tom, jak nakonfigurovat službu ASE fungovaly v prostředí, ve kterém odeslání odchozího provozu v místním prostředí, najdete v článku [konfigurace služby ASE vynucené tunelování][forcedtunnel]

## <a name="list-of-management-addresses"></a>Seznam adres pro správu ##

| Oblast | Adresy |
|--------|-----------|
| Všech veřejných oblastech | 13.64.115.203, 13.75.127.117, 13.94.141.115, 13.94.143.126, 13.94.149.179, 23.102.135.246, 23.102.188.65, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.124.47.188, 52.151.25.45, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 157.55.176.93, 157.55.208.185, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Konfiguruje se skupina zabezpečení sítě

S použitím skupin zabezpečení sítě není nutné se starat o jednotlivé adresy nebo udržování vlastní konfigurace. Existuje značku IP služby s názvem AppServiceManagement, který je pořád aktuální se všechny adresy. Pokud chcete použít tuto značku služby IP adresu pro nsg, přejděte na portál, otevřete uživatelské rozhraní skupin zabezpečení sítě a vyberte pravidla zabezpečení příchozích dat. Pokud máte existující pravidlo k řízení příchozích přenosů, upravte ho. Pokud tato skupina zabezpečení sítě nebylo vytvořeno pomocí služby ASE nebo pokud se všechny nové, pak vyberte **přidat**. V části zdrojové rozevírací nabídce vyberte **značka služby**.  V části značka zdrojové služby vyberte **AppServiceManagement**. Nastavit zdroj rozsahů portů \*, cíl **jakékoli**, rozsahy cílových portů k **454 455**, protokol k **TCP**a akci **povolit** . Pokud provádíte pravidlo, budete muset nastavit prioritu. 

![vytvořit skupinu zabezpečení sítě se značkou služby][1]

## <a name="configuring-a-route-table"></a>Konfigurace směrovací tabulky

Adresy pro správu je možné použít ve směrovací tabulce s dalším segmentem směrování do Internetu a ujistěte se, že se všechny příchozí přenosy správy přejít zpět prostřednictvím stejné cestě. Tyto postupy jsou potřeba při konfiguraci vynuceného tunelování. Pokud chcete vytvořit směrovací tabulku, můžete na portálu, Powershellu nebo rozhraní příkazového řádku Azure.  Níže jsou příkazy k vytvoření směrovací tabulky pomocí rozhraní příkazového řádku Azure z příkazového řádku Powershellu. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    az network route-table create --name $rt --resource-group $rg --location $location
    az network route-table route create -g $rg --route-table-name $rt -n 13.64.115.203 --next-hop-type Internet --address-prefix 13.64.115.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.75.127.117 --next-hop-type Internet --address-prefix 13.75.127.117/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.141.115 --next-hop-type Internet --address-prefix 13.94.141.115/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.143.126 --next-hop-type Internet --address-prefix 13.94.143.126/32
    az network route-table route create -g $rg --route-table-name $rt -n 13.94.149.179 --next-hop-type Internet --address-prefix 13.94.149.179/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.135.246 --next-hop-type Internet --address-prefix 23.102.135.246/32
    az network route-table route create -g $rg --route-table-name $rt -n 23.102.188.65 --next-hop-type Internet --address-prefix 23.102.188.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.120.64 --next-hop-type Internet --address-prefix 40.83.120.64/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.121.56 --next-hop-type Internet --address-prefix 40.83.121.56/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.83.125.161 --next-hop-type Internet --address-prefix 40.83.125.161/32
    az network route-table route create -g $rg --route-table-name $rt -n 40.124.47.188 --next-hop-type Internet --address-prefix 40.124.47.188/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.151.25.45 --next-hop-type Internet --address-prefix 52.151.25.45/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.152.214 --next-hop-type Internet --address-prefix 52.165.152.214/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.153.122 --next-hop-type Internet --address-prefix 52.165.153.122/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.154.193 --next-hop-type Internet --address-prefix 52.165.154.193/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.165.158.140 --next-hop-type Internet --address-prefix 52.165.158.140/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.174.22.21 --next-hop-type Internet --address-prefix 52.174.22.21/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.177.147 --next-hop-type Internet --address-prefix 52.178.177.147/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.184.149 --next-hop-type Internet --address-prefix 52.178.184.149/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.190.65 --next-hop-type Internet --address-prefix 52.178.190.65/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.178.195.197 --next-hop-type Internet --address-prefix 52.178.195.197/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.56.50 --next-hop-type Internet --address-prefix 52.187.56.50/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.59.251 --next-hop-type Internet --address-prefix 52.187.59.251/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.19 --next-hop-type Internet --address-prefix 52.187.63.19/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.187.63.37 --next-hop-type Internet --address-prefix 52.187.63.37/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.224.105.172 --next-hop-type Internet --address-prefix 52.224.105.172/32
    az network route-table route create -g $rg --route-table-name $rt -n 52.225.177.153 --next-hop-type Internet --address-prefix 52.225.177.153/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.14.230 --next-hop-type Internet --address-prefix 65.52.14.230/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.172.237 --next-hop-type Internet --address-prefix 65.52.172.237/32
    az network route-table route create -g $rg --route-table-name $rt -n 65.52.193.203 --next-hop-type Internet --address-prefix 65.52.193.203/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.57.58 --next-hop-type Internet --address-prefix 70.37.57.58/32
    az network route-table route create -g $rg --route-table-name $rt -n 70.37.89.222 --next-hop-type Internet --address-prefix 70.37.89.222/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.141 --next-hop-type Internet --address-prefix 104.44.129.141/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.243 --next-hop-type Internet --address-prefix 104.44.129.243/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.129.255 --next-hop-type Internet --address-prefix 104.44.129.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.44.134.255 --next-hop-type Internet --address-prefix 104.44.134.255/32
    az network route-table route create -g $rg --route-table-name $rt -n 104.208.54.11 --next-hop-type Internet --address-prefix 104.208.54.11/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.176.93 --next-hop-type Internet --address-prefix 157.55.176.93/32
    az network route-table route create -g $rg --route-table-name $rt -n 157.55.208.185 --next-hop-type Internet --address-prefix 157.55.208.185/32
    az network route-table route create -g $rg --route-table-name $rt -n 191.236.154.88 --next-hop-type Internet --address-prefix 191.236.154.88/32

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
