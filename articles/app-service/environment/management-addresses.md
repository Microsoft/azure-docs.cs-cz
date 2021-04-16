---
title: Adresy pro správu
description: Vyhledá adresy pro správu používané pro řízení App Service Environment. Nakonfigurují se v směrovací tabulce, aby se předešlo problémům s asymetrickým směrováním.
author: ccompy
ms.assetid: a7738a24-89ef-43d3-bff1-77f43d5a3952
ms.topic: article
ms.date: 03/22/2021
ms.author: ccompy
ms.custom: seodec18, references_regions, devx-track-azurecli
ms.openlocfilehash: aaaa190935da8c016c43832712f553a116332974
ms.sourcegitcommit: afb79a35e687a91270973990ff111ef90634f142
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/14/2021
ms.locfileid: "107482564"
---
# <a name="app-service-environment-management-addresses"></a>Adresy pro správu App Service Environment

App Service Environment (pomocného programu) je nasazení jednoho tenanta Azure App Service, které běží ve službě Azure Virtual Network (VNet).  I když je služba pomocného mechanismu spuštěná ve vaší virtuální síti, musí být stále přístupná z řady vyhrazených IP adres, které Azure App Service používá ke správě služby.  V případě pomocného mechanismu řízení prochází provoz pro správu síť řízenou uživatelem. Pokud je tento provoz blokovaný nebo nesměrovaný, pozastaví se. Podrobnosti o závislostech sítě služby pomocného mechanismu najdete v článku [o požadavcích na síť a App Service Environment][networking]. Obecné informace o pomocném mechanismu služby můžete začít s [úvodem do App Service Environment][intro].

Všechny služby ASE mají veřejnou VIP, do které patří provoz správy. Příchozí provoz správy z těchto adres se dostane do portů 454 a 455 ve veřejné virtuální IP adrese vašeho pomocného programu. Tento dokument obsahuje App Service zdrojové adresy pro provoz správy do pomocného mechanismu služby. Tyto adresy jsou také ve značce služby IP s názvem AppServiceManagement.

Níže uvedené adresy se dají nakonfigurovat v směrovací tabulce, aby se předešlo problémům se asymetrickým směrováním s provozem správy. Trasy působí na provoz na úrovni protokolu IP a nemají žádné povědomí o směru přenosu dat nebo o tom, že je přenos součástí zprávy s odpovědí TCP. Pokud se adresa pro odpověď pro požadavek TCP liší od adresy, na kterou byl odeslán, dojde k potížím s asymetrickým směrováním. Abyste se vyhnuli problémům s asymetrickým směrováním u provozu správy pomocného mechanismu, musíte zajistit, aby se odpovědi poslaly zpátky ze stejné adresy, na kterou byly odeslány. Podrobnosti o tom, jak nakonfigurovat přihlašování k provozu v prostředí, ve kterém se odchozí přenosy odesílají místně, najdete v tématu [konfigurace vašeho přístupového mechanismu služby s vynuceným tunelovým propojením][forcedtunnel] .

## <a name="list-of-management-addresses"></a>Seznam adres pro správu ##

| Oblast | Adresy |
|--------|-----------|
| Všechny veřejné oblasti | 13.66.140.0, 13.67.8.128, 13.69.64.128, 13.69.227.128, 13.70.73.128, 13.71.170.64, 13.71.194.129, 13.75.34.192, 13.75.127.117, 13.77.50.128, 13.78.109.0, 13.89.171.0, 13.94.141.115, 13.94.143.126, 13.94.149.179, 20.36.106.128, 20.36.114.64, 20.37.74.128, 23.96.195.3, 23.102.135.246, 23.102.188.65, 40.69.106.128, 40.70.146.128, 40.71.13.64, 40.74.100.64, 40.78.194.128, 40.79.130.64, 40.79.178.128, 40.83.120.64, 40.83.121.56, 40.83.125.161, 40.112.242.192, 51.107.58.192, 51.107.154.192, 51.116.58.192, 51.116.155.0, 51.120.99.0, 51.120.219.0, 51.140.146.64, 51.140.210.128, 52.151.25.45, 52.162.106.192, 52.165.152.214, 52.165.153.122, 52.165.154.193, 52.165.158.140, 52.174.22.21, 52.178.177.147, 52.178.184.149, 52.178.190.65, 52.178.195.197, 52.187.56.50, 52.187.59.251, 52.187.63.19, 52.187.63.37, 52.224.105.172, 52.225.177.153, 52.231.18.64, 52.231.146.128, 65.52.172.237, 65.52.250.128, 70.37.57.58, 104.44.129.141, 104.44.129.243, 104.44.129.255, 104.44.134.255, 104.208.54.11, 104.211.81.64, 104.211.146.128, 157.55.208.185, 191.233.50.128, 191.233.203.64, 191.236.154.88 |
| Microsoft Azure Government | 23.97.29.209, 13.72.53.37, 13.72.180.105, 52.181.183.11, 52.227.80.100, 52.182.93.40, 52.244.79.34, 52.238.74.16 |
| Azure (Čína) | 42.159.4.236, 42.159.80.125 |

## <a name="configuring-a-network-security-group"></a>Konfigurace skupiny zabezpečení sítě

Se skupinami zabezpečení sítě si nemusíte dělat starosti s jednotlivými adresami nebo zachovat vlastní konfiguraci. K dispozici je značka služby IP s názvem AppServiceManagement, která je stále aktuální se všemi adresami. Pokud chcete tuto značku služby IP v NSG použít, navštivte portál, otevřete uživatelské rozhraní skupin zabezpečení sítě a vyberte příchozí pravidla zabezpečení. Pokud máte již existující pravidlo pro příchozí provoz správy, upravte ho. Pokud se tento NSG nevytvořil s vaším pomocným mechanismem, nebo pokud je to vše nového, vyberte **Přidat**. V rozevíracím seznamu zdroj vyberte možnost **značka služby**.  V části značka zdrojové služby vyberte **AppServiceManagement**. Nastavte rozsahy zdrojových portů na \* , cíl na **libovolný**, rozsah cílových portů na **454-455**, protokol na **TCP** a akci, která má být **povolena**. Pokud pravidlo vytváříte, musíte nastavit prioritu. 

![Vytvoření NSG pomocí značky služby][1]

## <a name="configuring-a-route-table"></a>Konfigurace směrovací tabulky

Adresy pro správu můžete umístit do směrovací tabulky s dalším segmentem směrování Internetu, aby se zajistilo, že všechny příchozí přenosy pro správu budou moct přejít zpátky přes stejnou cestu. Tyto trasy jsou potřeba při konfiguraci vynuceného tunelování. Směrovací tabulku můžete vytvořit pomocí portálu, PowerShellu nebo rozhraní příkazového řádku Azure.  Níže jsou uvedené příkazy k vytvoření směrovací tabulky pomocí Azure CLI z příkazového řádku PowerShellu. 

```azurepowershell-interactive
$rg = "resource group name"
$rt = "route table name"
$location = "azure location"
$managementAddresses = "13.66.140.0", "13.67.8.128", "13.69.64.128", "13.69.227.128", "13.70.73.128", "13.71.170.64", "13.71.194.129", "13.75.34.192", "13.75.127.117", "13.77.50.128", "13.78.109.0", "13.89.171.0", "13.94.141.115", "13.94.143.126", "13.94.149.179", "20.36.106.128", "20.36.114.64", "20.37.74.128", "23.96.195.3", "23.102.135.246", "23.102.188.65", "40.69.106.128", "40.70.146.128", "40.71.13.64", "40.74.100.64", "40.78.194.128", "40.79.130.64", "40.79.178.128", "40.83.120.64", "40.83.121.56", "40.83.125.161", "40.112.242.192", "51.107.58.192", "51.107.154.192", "51.116.58.192", "51.116.155.0", "51.120.99.0", "51.120.219.0", "51.140.146.64", "51.140.210.128", "52.151.25.45", "52.162.106.192", "52.165.152.214", "52.165.153.122", "52.165.154.193", "52.165.158.140", "52.174.22.21", "52.178.177.147", "52.178.184.149", "52.178.190.65", "52.178.195.197", "52.187.56.50", "52.187.59.251", "52.187.63.19", "52.187.63.37", "52.224.105.172", "52.225.177.153", "52.231.18.64", "52.231.146.128", "65.52.172.237", "65.52.250.128", "70.37.57.58", "104.44.129.141", "104.44.129.243", "104.44.129.255", "104.44.134.255", "104.208.54.11", "104.211.81.64", "104.211.146.128", "157.55.208.185", "191.233.50.128", "191.233.203.64", "191.236.154.88"

az network route-table create --name $rt --resource-group $rg --location $location
foreach ($ip in $managementAddresses) {
    az network route-table route create -g $rg --route-table-name $rt -n $ip --next-hop-type Internet --address-prefix ($ip + "/32")
}
```

Po vytvoření směrovací tabulky je nutné ji nastavit v podsíti přihlášeného mechanismu.  

## <a name="get-your-management-addresses-from-api"></a>Získání adres pro správu z rozhraní API ##

Pomocí následujícího volání rozhraní API můžete zobrazit seznam adres pro správu, které se shodují s vaším MECHANISMem řízení.

```http
get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

Rozhraní API vrátí dokument JSON, který obsahuje všechny příchozí adresy pro váš pomocného programu. Seznam adres zahrnuje adresy pro správu, VIP využívané vaším pomocným mechanismem a rozsah adres podsítě pro pomocného mechanismu.  

Pro volání rozhraní API pomocí [armclient](https://github.com/projectkudu/ARMClient) použijte následující příkazy, ale NAHRAĎte ID předplatného, skupinu prostředků a název pomocného programu.  

```azurepowershell-interactive
armclient login
armclient get /subscriptions/<subscription ID>/resourceGroups/<resource group>/providers/Microsoft.Web/hostingEnvironments/<ASE Name>/inboundnetworkdependenciesendpoints?api-version=2016-09-01
```

<!--IMAGES-->
[1]: ./media/management-addresses/managementaddr-nsg.png

<!-- LINKS -->
[networking]: ./network-info.md
[intro]: ./intro.md
[forcedtunnel]: ./forced-tunnel-support.md
