---
title: "Filtr paketů na FreeBSD použít k vytvoření brány firewall v Azure | Microsoft Docs"
description: "Informace o nasazení pomocí FreeBSD na brány firewall NAT. PF v Azure."
services: virtual-machines-linux
documentationcenter: 
author: KylieLiang
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 0bed776526e3e2f3ee4cdce596c591d0b8419030
ms.sourcegitcommit: 8c3267c34fc46c681ea476fee87f5fb0bf858f9e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/09/2018
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Použití filtru paketů na FreeBSD v Azure vytvořit zabezpečený brány firewall
Tento článek představuje nasazení brány firewall NAT. pomocí filtru na FreeBSD balírna pomocí šablony Azure Resource Manageru pro běžný scénář webového serveru.

## <a name="what-is-pf"></a>Co je PF?
PF (filtr paketů, zapisují také pf) je filtr licencovanou paketů BSD, centrální softwarového produktu pro fungující brána firewall může. PF od vyvinul rychle a teď má několik výhod oproti další dostupné brány firewall. Překlad adres (NAT) je v souboru PF od jeden den, pak Plánovač paketů a správu active fronty integrována do PF, integrací ALTQ a díky tomu lze změnit v nastavení konfigurace na PF. Funkce jako je například pfsync a CARP pro převzetí služeb při selhání a redundance, authpf relace ověřování a ftp proxy serveru k usnadnění fungující obtížné protokolu FTP, brána firewall může také rozšířili PF. Stručně řečeno PF je výkonný a bohaté funkce Brána firewall. 

## <a name="get-started"></a>Začínáme
Pokud mají zájem o nastavení zabezpečení brány firewall v cloudu pro webové servery a potom můžeme začít. Můžete taky použít skripty použité v této šablony Azure Resource Manager nastavit topologii vaší sítě.
Šablony Azure Resource Manageru nastavte FreeBSD virtuální počítač, který provádí NAT /redirection PF a dva virtuální počítače FreeBSD pomocí Nginx webový server nainstalován a nakonfigurován. Kromě provádění NAT pro dva webové servery odchozí přenosy, virtuální počítač NAT/přesměrování zachycuje požadavků HTTP a přesměruje je to dva webové servery v kruhového dotazování. Virtuální sítě používá 10.0.0.2/24 privátní místo směrovat adresu IP a můžete změnit parametry šablony. Šablony Azure Resource Manageru také definuje směrovací tabulku pro celý virtuální síť, což je kolekce jednotlivých tras používaná k přepsání Azure výchozích tras na základě cílové IP adresy. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Nasazení pomocí rozhraní příkazového řádku Azure
Budete potřebovat nejnovější [Azure CLI 2.0](/cli/azure/install-az-cli2) nainstalován a přihlášení k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index#az_login). Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group#az_group_create). Následující příklad vytvoří název skupiny prostředků `myResourceGroup` v `West US` umístění.

```azurecli
az group create --name myResourceGroup --location westus
```

V dalším kroku nasaďte šablonu [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) s [vytvořit nasazení skupiny az](/cli/azure/group/deployment#az_group_deployment_create). Stáhněte si [azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) pod stejnou cestu a definovat vlastní hodnoty prostředků, jako například `adminPassword`, `networkPrefix`, a `domainNamePrefix`. 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Po přibližně pět minut, zobrazí se informace o `"provisioningState": "Succeeded"`. Potom můžete ssh na front-endu virtuálních počítačů (NAT) nebo přístup Nginx webového serveru v prohlížeči pomocí veřejnou IP adresu nebo plně kvalifikovaný název domény front-endu virtuálních počítačů (NAT). Následující příklad uvádí ve plně kvalifikovaný název domény a veřejnou IP adresu, která přiřazená k front-endu virtuálních počítačů (NAT) `myResourceGroup` skupinu prostředků. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Další postup
Opravdu chcete nastavit vlastní NAT v Azure? Otevřít zdroj, bezplatné ale výkonné? Potom PF je vhodné použít. Pomocí šablony [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup), stačí pouze pět minut nastavit brány firewall NAT. s vyrovnávání pomocí FreeBSD zatížení pomocí kruhového dotazování je PF v Azure pro běžný scénář webového serveru. 

Pokud chcete získat další nabídku FreeBSD v Azure, podívejte se na [Úvod do FreeBSD v Azure](freebsd-intro-on-azure.md).

Pokud chcete získat další informace o souboru PF, podívejte se na [FreeBSD k používání](https://www.freebsd.org/doc/handbook/firewalls-pf.html) nebo [PF – uživatelská příručka](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
