---
title: Adresy pro správu
description: Najděte adresy správy používané k řízení prostředí služby App Service. Nakonfiguroval je v tabulce tras, aby se zabránilo asymetrickým problémům s směrováním.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 11/13/2019
ms.author: ccompy
ms.custom: seodec18
ms.openlocfilehash: 7d7f97552e8faadee1af928a9ce4e1eea2df476e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74687116"
---
# <a name="app-service-environment-management-addresses"></a>Adresy správy prostředí služby App Service

Prostředí služby App Service Environment (ASE) je jednoklientské nasazení služby Azure App Service, která běží ve vaší virtuální síti Azure (Virtuální síť).  Zatímco služba ASE běží ve vaší virtuální síti, musí být stále přístupná z několika vyhrazených IP adres, které služba Azure App Service používá ke správě služby.  V případě rozhraní ASE prochází provoz správy sítí řízenou uživatelem. Pokud je tento provoz blokován nebo nesprávně směrován, služba ASE bude pozastavena. Podrobnosti o závislostech sítě služby ASE načtete [v části Důležité informace o síti a prostředí služby App Service][networking]. Obecné informace o službě ASE můžete začít s [úvodem do prostředí služby App Service][intro].

Všechny ASEs mají veřejné VIP, které řízení provozu přichází do. Příchozí provoz správy z těchto adres přichází z portů 454 a 455 na veřejné VIP vaší službě ASE. Tento dokument obsahuje seznam zdrojových adres služby App Service pro provoz správy služby ASE. Tyto adresy jsou také v výrobním číslo IP s názvem AppServiceManagement.

Níže uvedené adresy lze nakonfigurovat v tabulce tras, aby se zabránilo asymetrickým problémům s směrováním s provozem správy. Trasy působí na provoz na úrovni IP a nemají povědomí o směru provozu nebo že provoz je součástí zprávy odpovědi TCP. Pokud se adresa odpovědi pro požadavek TCP liší od adresy, na kterou byla odeslána, máte problém s asymetrickým směrováním. Chcete-li se vyhnout asymetrickým problémům se směrováním s provozu správy služby ASE, je třeba zajistit, aby odpovědi byly odeslány zpět ze stejné adresy, na kterou byly odeslány. Podrobnosti o konfiguraci služby ASE pro provoz v prostředí, kde je odchozí provoz odesílán místně, [načtete-li službu ASE s vynuceným tunelovým propojením.][forcedtunnel]

## <a name="list-of-management-addresses"></a>Seznam adres pro správu ##

| Region (Oblast) | Adresy |
|--------|-----------|
| Všechny veřejné regiony | 13.64.115.203, 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 23.100.226.236, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.90.240.166, 40.91.126.196, 40.112.242.192, 40.119.4.111, 40.124.47.188, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.80.89, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.14.230, 65.52.172.237, 65.52.193.203, 70.37.57.58, 70.37.89.222, 104.43.242.137, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 104.214.49.0, 157.55.176.93, 157.55.208.185, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 23.97.0.17, 23.97.16.184 |

## <a name="configuring-a-network-security-group"></a>Konfigurace skupiny zabezpečení sítě

Se skupinami zabezpečení sítě se nemusíte starat o jednotlivé adresy nebo údržbu vlastní konfigurace. Existuje značka služby IP s názvem AppServiceManagement, která je udržována v aktuálním stavu se všemi adresami. Chcete-li použít tuto značku služby IP v souboru zabezpečení sítě, přejděte na portál, otevřete ui skupiny zabezpečení sítě a vyberte příchozí pravidla zabezpečení. Pokud máte již existující pravidlo pro příchozí provoz správy, upravte ho. Pokud tento soubor nsg nebyl vytvořen se se sousto, nebo pokud je vše nové, vyberte **přidat**. V rozevíracím okně Zdroj vyberte **Výrobní číslo**.  Pod výrobní majek Zdroj vyberte **AppServiceManagement**. Nastavte rozsahy zdrojových \*portů na , Cíl na **Libovolný**, Rozsahy cílových portů na **454-455**, Protokol na **TCP**a Akce **povolit**. Pokud vytváříte pravidlo, musíte nastavit prioritu. 

![vytvoření nsg s výrobním štítkem][1]

## <a name="configuring-a-route-table"></a>Konfigurace tabulky tras

Adresy pro správu lze umístit do směrovací tabulky s dalším směrováním internetu, aby bylo zajištěno, že veškerý příchozí provoz správy se bude moci vrátit stejnou cestou. Tyto trasy jsou potřebné při konfiguraci vynuceného tunelového propojení. Chcete-li vytvořit směrovací tabulku, můžete použít portál, PowerShell nebo Azure CLI.  Příkazy k vytvoření směrovací tabulky pomocí azure cli z výzvy PowerShell jsou níže. 

    $rg = "resource group name"
    $rt = "route table name"
    $location = "azure location"
    $managementAddresses = "13.64.115.203", "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "23.100.226.236", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.90.240.166", "40.91.126.196", "40.112.242.192", "40.119.4.111", "40.124.47.188", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.80.89", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.14.230", "65.52.172.237", "65.52.193.203", "70.37.57.58", "70.37.89.222", "104.43.242.137", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "104.214.49.0", "157.55.176.93", "157.55.208.185", "191.233.203.64", "191.236.154.88"

    az network route-table create --name $rt --resource-group $rg --location $location
    foreach ($ip in $managementAddresses) {
        az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
    }

Po vytvoření směrovací tabulky je třeba ji nastavit v podsíti ase.  

## <a name="get-your-management-addresses-from-api"></a>Získání adres pro správu z rozhraní API ##

Můžete uvést adresy správy, které odpovídají vaší ase s následujícím voláním rozhraní API.

    get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01

Rozhraní API vrátí dokument JSON, který obsahuje všechny příchozí adresy pro službu ASE. Seznam adres obsahuje adresy pro správu, virtuální ip adresu používanou vaší ase a samotný rozsah adres podsítě ASE.  

Chcete-li volat rozhraní API s [armclient](https://github.com/projectkudu/ARMClient) použít následující příkazy, ale nahradit v ID předplatného, skupiny prostředků a název služby ASE.  

    armclient login
    armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01


<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
