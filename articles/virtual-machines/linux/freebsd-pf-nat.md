---
title: Pomocí filtru paketů FreeBSD vytvořit bránu firewall v Azure
description: Naučte se, jak nasadit bránu firewall překladu adres (NAT) pomocí FreeBSD PF v Azure.
author: KylieLiang
ms.service: virtual-machines-linux
ms.topic: how-to
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 02/20/2017
ms.author: kyliel
ms.openlocfilehash: c7027dd86945e53149b3c4daeb9d6faee3593e95
ms.sourcegitcommit: e7152996ee917505c7aba707d214b2b520348302
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/20/2020
ms.locfileid: "97702238"
---
# <a name="how-to-use-freebsds-packet-filter-to-create-a-secure-firewall-in-azure"></a>Jak pomocí filtru paketů FreeBSD vytvořit zabezpečenou bránu firewall v Azure
Tento článek popisuje, jak nasadit firewall překladu adres (NAT) pomocí filtru FreeBSD Pack Azure Resource Manager prostřednictvím šablony pro běžný scénář webového serveru.

## <a name="what-is-pf"></a>Co je PF?
PF (filtr paketů, také napsaný PF) je filtr licencovaných stavových paketů BSD, střední část softwaru pro bránu firewall. Vzhledem k tomu, že je k dispozici, bylo z BF možné rychle vyvíjet a teď má několik výhod oproti ostatním dostupným Překlad síťových adres (NAT) je na adrese PF od jednoho dne a služba Plánovač paketů a aktivní fronty byla integrována do BF, protože integruje ALTQ a usnadňuje konfiguraci prostřednictvím konfigurace PF. Další funkce, jako jsou pfsync a CARP pro převzetí služeb při selhání a redundanci, Authpf pro ověřování relací a FTP – proxy server pro usnadnění brány firewall pro obtížné protokol FTP, mají také rozšířenou PF. V krátkém případě je BF k dispozici pro bránu firewall s bohatou funkcí. 

## <a name="get-started"></a>Začínáme
Pokud vás zajímá nastavení zabezpečené brány firewall v cloudu pro vaše webové servery, můžeme začít. Můžete také použít skripty používané v této šabloně Azure Resource Manager k nastavení síťové topologie.
Šablona Azure Resource Manager nastavila virtuální počítač s FreeBSD, který provádí překlad adres/Redirection pomocí PF a dva FreeBSD virtuální počítače s nainstalovaným a nakonfigurovaným webovým serverem Nginx. Kromě provádění překladu adres (NAT) pro dva webové servery s odchozí přenos dat zachycuje virtuální počítač NAT/přesměrování požadavky HTTP a přesměruje je na dva webové servery v rámci kruhové dotazování. Virtuální síť používá privátní adresní prostor IP adres 10.0.0.2/24 a parametry šablony můžete upravit. Šablona Azure Resource Manager také definuje směrovací tabulku pro celou virtuální síť, což je kolekce jednotlivých tras používaných k přepsání výchozích tras Azure na základě cílové IP adresy. 

![Diagram znázorňuje veřejnou I P adresu v instanci překladu adres (NAT), která přesměrovává metodou kruhového dotazování na dva virtuální počítače back-end, které hostují Nginx webové servery.](./media/freebsd-pf-nat/pf_topology.jpg)
    
### <a name="deploy-through-azure-cli"></a>Nasazení prostřednictvím rozhraní příkazového řádku Azure
Potřebujete nainstalovat nejnovější rozhraní příkazového [řádku Azure](/cli/azure/install-az-cli2) a přihlásit se k účtu Azure pomocí [AZ Login](/cli/azure/reference-index). Vytvořte skupinu prostředků pomocí příkazu [az group create](/cli/azure/group). Následující příklad vytvoří název skupiny prostředků `myResourceGroup` v `West US` umístění.

```azurecli
az group create --name myResourceGroup --location westus
```

Dále nasaďte šablonu PF-FreeBSD-Setup pomocí [AZ Deployment Group Create](/cli/azure/deployment/group). Stáhněte si azuredeploy.parameters.jspod stejnou cestou a definujte vlastní hodnoty prostředků, například `adminPassword` , `networkPrefix` a `domainNamePrefix` . 

```azurecli
az deployment group create --resource-group myResourceGroup --name myDeploymentName \
    --template-uri https://raw.githubusercontent.com/Azure/azure-quickstart-templates/master/pf-freebsd-setup/azuredeploy.json \
    --parameters '@azuredeploy.parameters.json' --verbose
```

Po asi pět minut se zobrazí informace o `"provisioningState": "Succeeded"` . Pak můžete pomocí protokolu SSH přejít k virtuálnímu počítači front-end (NAT) nebo přistupovat k webovému serveru Nginx v prohlížeči pomocí veřejné IP adresy nebo plně kvalifikovaného názvu domény (NAT). Následující příklad vypíše plně kvalifikovaný název domény a veřejnou IP adresu, která se přiřadí virtuálnímu počítači front-endu (NAT) ve `myResourceGroup` skupině prostředků. 

```azurecli
az network public-ip list --resource-group myResourceGroup
```
    
## <a name="next-steps"></a>Další kroky
Chcete v Azure nastavit vlastní překlad adres (NAT)? Open Source, zdarma, ale výkonné? Pak je PF dobrá volba. Pomocí šablony PF-FreeBSD-Setup budete potřebovat jenom pět minut, než nastavíte bránu firewall překladu adres (NAT) s vyrovnáváním zatížení s kruhovým dotazováním pomocí služby FreeBSD PF v Azure pro běžný scénář webového serveru. 

Pokud se chcete seznámit s nabídkou FreeBSD v Azure, přečtěte si [Úvod do FreeBSD v Azure](freebsd-intro-on-azure.md).

Pokud chcete získat další informace o PF, přečtěte si příručku pro [FreeBSD](https://www.freebsd.org/doc/handbook/firewalls-pf.html) nebo [uživatele PF](https://www.freebsd.org/doc/handbook/firewalls-pf.html).
