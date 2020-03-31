---
title: Vytvoření brány firewall v Azure pomocí filtru paketů FreeBSD
description: Přečtěte si, jak nasadit bránu firewall NAT pomocí PF FreeBSD v Azure.
author: KylieLiang
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: 9b78c0d93b57a3e3f4963088d0b93f121f57483c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78945112"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Jak pomocí filtru paketů FreeBSD vytvořit v Azure zabezpečenou bránu firewall
Tento článek představuje, jak nasadit bránu firewall NAT pomocí filtru balírny FreeBSD prostřednictvím šablony Azure Resource Manager pro běžný scénář webového serveru.

## <a name="what-is-pf"></a>Co je PF?
PF (Packet Filter, také psaný pf) je BSD licencovaný stavový paketový filtr, centrální software pro bránu firewall. PF se od té doby rychle vyvinula a nyní má několik výhod oproti jiným dostupným firewallům. Network Address Translation (NAT) je v PF od prvního dne, pak plánovač paketů a aktivní správa front byly integrovány do PF, integrací ALTQ a jeho konfigurací prostřednictvím konfigurace PF. Funkce jako pfsync a CARP pro převzetí služeb při selhání a redundanci, authpf pro ověřování relace a ftp-proxy pro usnadnění firewallování obtížného protokolu FTP také rozšířily PF. Stručně řečeno, PF je výkonný firewall bohatý na funkce. 

## <a name="get-started"></a>Začínáme
Pokud máte zájem o nastavení zabezpečené brány firewall v cloudu pro vaše webové servery, tak začneme. Můžete také použít skripty použité v této šabloně Azure Resource Manager nastavit topologii sítě.
Šablona Azure Resource Manager ustavila virtuální počítač FreeBSD, který provádí nat /přesměrování pomocí PF a dvou virtuálních počítačů FreeBSD s nainstalovaným a nakonfigurovaným webovým serverem Nginx. Kromě provádění nat pro dva webové servery odchozí provoz, NAT/přesměrování virtuální počítač zachycuje požadavky HTTP a přesměrovat je na dva webové servery v kruhovédotazování způsobem. Virtuální síť používá privátní nesměrovatelný adresní prostor IP 10.0.0.2/24 a můžete upravit parametry šablony. Šablona Azure Resource Manager také definuje směrovací tabulku pro celou virtuální síť, což je kolekce jednotlivých tras, které se používají k přepsání výchozích tras Azure na základě cílové IP adresy. 

![pf_topology](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Nasazení prostřednictvím azure cli
Potřebujete nejnovější [Azure CLI](/cli/azure/install-az-cli2) nainstalované a přihlášené k účtu Azure pomocí [az přihlášení](/cli/azure/reference-index). Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří název `myResourceGroup` skupiny `West US` prostředků v umístění.

```azurecli
az group create --name myResourceGroup --location westus
```

Dále nasaďte šablonu [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup) s [vytvořením nasazení skupiny az](/cli/azure/group/deployment). Stáhněte [si soubor azuredeploy.parameters.json](https://github.com/Azure/azure-quickstart-templates/blob/master/pf-freebsd-setup/azuredeploy.parameters.json) pod stejnou cestou `adminPassword` `networkPrefix`a `domainNamePrefix`definujte vlastní hodnoty prostředků, například , a . 

```azurecli
az group deployment create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Asi po pěti minutách získáte `"provisioningState": "Succeeded"`informace o . Pak můžete ssh na front-end UM (NAT) nebo přístup Nginx webový server v prohlížeči pomocí veřejné IP adresy nebo FQDN frontendu Virtuálního měna (NAT). V následujícím příkladu je uveden název FQDN a veřejná IP adresa, která byla přiřazena front-endu Virtuální ho virtuálního počítačů (NAT) ve `myResourceGroup` skupině prostředků. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Další kroky
Chcete si v Azure nastavit vlastní nat? Open Source, zdarma, ale silný? Pf je pak dobrá volba. Při použití šablony [pf-freebsd-setup](https://github.com/Azure/azure-quickstart-templates/tree/master/pf-freebsd-setup)potřebujete k nastavení brány firewall PRO NAT s vyrovnáváním zatížení kruhového dotazování pouze pět minut pomocí PF freebsd v Azure pro běžný scénář webového serveru. 

Pokud se chcete naučit nabídku FreeBSD v Azure, přečtěte si [úvod do FreeBSD v Azure](freebsd-intro-on-azure.md).

Chcete-li se o PF dozvědět více, přečtěte si [příručku FreeBSD](https://www.freebsd.org/doc/handbook/firewalls-pf.html) nebo [uživatelskou příručku PF](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
