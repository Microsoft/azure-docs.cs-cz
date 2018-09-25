---
title: Použití filtrů paketů FreeBSD k vytvoření brány firewall v Azure | Dokumentace Microsoftu
description: Informace o nasazení brány firewall NAT pomocí FreeBSD PF v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: KylieLiang
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: ''
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 8400fa12e8776834f71740f809df18753291601f
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2018
ms.locfileid: "46992301"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Použití filtrů paketů FreeBSD k vytvoření zabezpečené brány firewall v Azure
Tento článek představuje nasazení brány firewall NAT pomocí Packeru filtrů FreeBSD pomocí šablony Azure Resource Manageru pro běžný scénář webového serveru.

## <a name="what-is-pf"></a>Co je PF?
PF (filtr paketů, zapsat také pf) je filtr paketů licencovaný BSD, střední část softwaru pro funkce brány firewall. PF od té doby se vyvinula rychle a nyní má několik výhod oproti další dostupné brány firewall. Překlad síťových adres (NAT) je v souboru PF od prvního dne, pak Plánovač paketů a aktivní fronty management byly integrovány do PF, tak, že integrace ALTQ a díky tomu je prostřednictvím PF vaší konfigurace. Funkce, jako je pfsync a CARP pro převzetí služeb při selhání a redundance, authpf relace ověřování a protokolu ftp – proxy serveru k usnadnění funkce brány firewall obtížné protokolu FTP, mají také rozšířené PF. Stručně řečeno PF je brána firewall výkonné a plně funkční. 

## <a name="get-started"></a>Začínáme
Pokud mají zájem o nastavení zabezpečené brány firewall v cloudu pro vaše webové servery, pusťme se do práce. Můžete také použít skripty do této šablony Azure Resource Managerem použít ke konfiguraci síťové topologie.
Šablony Azure Resource Manageru nastavte FreeBSD virtuální počítač, který provádí NAT /redirection PF dva virtuální počítače a FreeBSD pomocí webový server Nginx nainstalovaný a nakonfigurovaný. Kromě provádění NAT pro výchozí přenos dvě webové servery, virtuální počítač NAT/přesměrování zachycuje požadavků HTTP a přesměruje je to dva webových serverů principem kruhového. Virtuální sítě používá 10.0.0.2/24 privátní prostoru nesměrovatelných adres IP a můžete změnit parametry šablony. Šablony Azure Resource Manageru také definuje směrovací tabulku pro celý virtuální síť, což je kolekce jednotlivých tras, chcete-li přepsat Azure výchozích tras na základě cílové IP adresy. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure
Budete potřebovat nejnovější [rozhraní příkazového řádku Azure](/cli/azure/install-az-cli2) nainstalovaný a přihlášení k účtu Azure pomocí [az login](/cli/azure/reference-index#az_login). Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří název skupiny prostředků `myResourceGroup` v `West US` umístění.

```azurecli
az group create --name myResourceGroup --location westus
```

V dalším kroku nasaďte šablonu [instalační program pf freebsd](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) s [vytvořit nasazení skupiny pro az](/cli/azure/group/deployment#az_group_deployment_create). Stáhněte si [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) na stejné cestě a definovat vlastní hodnoty prostředků, jako například `adminPassword`, `networkPrefix`, a `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Po přibližně během pěti minut, zobrazí se informace o `"provisioningState": "Succeeded"`. Potom můžete ssh na front-endový virtuální počítač (NAT) nebo přístup webový server Nginx v prohlížeči pomocí veřejné IP adresy nebo plně kvalifikovaný název domény z front-endový virtuální počítač (NAT). Následující příklad vypíše v plně kvalifikovaný název domény a veřejné IP adresy přiřazené k front-endový virtuální počítač (NAT) `myResourceGroup` skupinu prostředků. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Další postup
Opravdu chcete nastavit vlastní NAT v Azure? Open Source, bezplatné, ale výkonné? Potom PF je dobrou volbou. Pomocí šablony [instalační program pf freebsd](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup), stačí pouze pět minut nastavit brány firewall NAT s využitím FreeBSD pro vyrovnávání zatížení s kruhovým uživatele PF v Azure pro běžný scénář webového serveru. 

Pokud chcete získat nabídku FreeBSD v Azure, podívejte se na [Úvod do Linuxu na Azure](freebsd-intro-on-azure.md).

Pokud chcete získat další informace o souboru PF, podívejte se na [FreeBSD Příručka](https://www.freebsd.org/doc/handbook/firewalls-pf.html) nebo [PF – uživatelská příručka](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
