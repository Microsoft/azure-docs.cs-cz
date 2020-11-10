---
title: Virtuální počítače Zvýhodněné hybridní využití Azure a Linux
description: Zvýhodněné hybridní využití Azure vám umožní ušetřit peníze na virtuálních počítačích Linux běžících v Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: feaa2471f2867257deb06ab32ed5fc0a26a0d37e
ms.sourcegitcommit: 6109f1d9f0acd8e5d1c1775bc9aa7c61ca076c45
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/10/2020
ms.locfileid: "94443428"
---
# <a name="public-preview-azure-hybrid-benefit--how-it-applies-for-linux-virtual-machines"></a>Public Preview: Zvýhodněné hybridní využití Azure – jak se vztahuje na Linux Virtual Machines

## <a name="overview"></a>Přehled

Zvýhodněné hybridní využití Azure vám umožní snadněji migrovat místní virtuální počítače s Red Hat Enterprise Linux (RHEL) a SUSE Linux Enterprise Server (SLES) do Azure pomocí vlastního předplatného Red Hat nebo SUSE softwaru. S touto výhodou platíte jenom za náklady na infrastrukturu vašeho virtuálního počítače, protože poplatek za software je pokrytý vaším předplatným RHEL nebo SLES. Tato výhoda se vztahuje na všechny image RHEL a SLES Marketplace s průběžnými platbami (PAYG).

> [!IMPORTANT]
> Zvýhodněné hybridní využití Azure pro virtuální počítače se systémem Linux je aktuálně ve verzi Public Preview.
> Tato verze Preview se poskytuje bez smlouvy o úrovni služeb a nedoporučuje se pro úlohy v produkčním prostředí. Některé funkce se nemusí podporovat nebo mohou mít omezené možnosti. Další informace najdete v [dodatečných podmínkách použití pro verze Preview v Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="benefit-description"></a>Popis výhod

Prostřednictvím Zvýhodněné hybridní využití Azure můžete snadněji migrovat místní servery RHEL a SLES do Azure tím, že převedete stávající virtuální počítače RHEL a SLES PAYG v Azure a budete mít k disu fakturaci s vlastními předplatnými (BYOS). Virtuální počítače nasazené z imagí PAYG v Azure se obvykle účtují jako poplatek za infrastrukturu i jako poplatek za software. Pomocí Zvýhodněné hybridní využití Azure můžete virtuální počítače s PAYG převést na model fakturace BYOS bez nutnosti opětovného nasazení, což zaloučí případné riziko výpadku.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Zvýhodněné hybridní využití Azure vizualizace nákladů na virtuální počítače se systémem Linux.":::

Po povolení výhody na virtuálním počítači s RHEL nebo SLES se už nebudete účtovat za další poplatek za software, který se obvykle účtuje na virtuálním počítači s PAYG. Místo toho začne váš virtuální počítač vysílat poplatek za BYOS, který zahrnuje jenom výpočetní a licenční poplatek.

Pokud budete chtít, můžete také převést virtuální počítač s povoleným přínosem zpátky na PAYG model fakturace.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Rozsah Zvýhodněné hybridní využití Azure způsobilosti pro virtuální počítače se systémem Linux

Zvýhodněné hybridní využití Azure je k dispozici pro všechny PAYG image SLES na webu Marketplace. Tato výhoda ještě není dostupná pro Image BYOS nebo vlastní image RHEL nebo SLES Marketplace.

Rezervované instance, vyhrazené hostitele a zvýhodněné hybridní využití SQL nejsou způsobilé pro Zvýhodněné hybridní využití Azure, pokud už tuto výhodu používáte s virtuálními počítači se systémem Linux.

## <a name="how-to-get-started"></a>Jak začít

Zvýhodněné hybridní využití Azure je aktuálně ve fázi Preview pro virtuální počítače se systémem Linux. Jakmile získáte přístup k verzi Preview, můžete povolit výhody pomocí Azure CLI.

### <a name="public-preview"></a>Public Preview

Zvýhodněné hybridní využití Azure (pro Linux) je teď ve fázi Public Preview. K povolení výhod pro distribuce Red Hat a SUSE můžete použít následující kroky. 

### <a name="red-hat-customers"></a>Zákazníci se Red Hat

1.    Registrace v [programu Red Hat Cloud Access](https://aka.ms/rhel-cloud-access)
1.    Umožněte vašim předplatným Azure pro cloudový přístup a povolte předplatná obsahující virtuální počítače, které chcete využívat.
1.    Využijte výhod pro existující virtuální počítače prostřednictvím rozhraní příkazového řádku Azure
1.    Zaregistrujte si virtuální počítače, které obdrží výhodu, pomocí samostatného zdroje aktualizací.


### <a name="suse-customers"></a>Zákazníci SUSE

1.    Registrace pomocí programu veřejné cloudy SUSE
1.    Využijte výhod pro existující virtuální počítače prostřednictvím rozhraní příkazového řádku Azure
1.    Zaregistrujte si virtuální počítače, které obdrží výhodu, pomocí samostatného zdroje aktualizací.


### <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Povolení nebo zakázání výhod v Azure CLI

K aktualizaci stávajících virtuálních počítačů můžete použít příkaz AZ VM Update. U virtuálních počítačů s RHEL spusťte příkaz s parametrem--License-Type typu "RHEL_BYOS". U virtuálních počítačů s SLES spusťte příkaz s parametrem--License-Type typu "SLES_BYOS".

#### <a name="cli-example-to-enable-the-benefit"></a>Příklad rozhraní příkazového řádku pro povolení výhod:
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
#### <a name="cli-example-to-disable-the-benefit"></a>Příklad rozhraní příkazového řádku pro zakázání výhody:
Pokud chcete tuto výhodu zakázat, použijte hodnotu typu licence None.
```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

#### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Příklad rozhraní příkazového řádku pro povolení výhody pro velký počet virtuálních počítačů
Pokud chcete tuto výhodu povolit pro velký počet virtuálních počítačů, můžete použít `--ids` parametr v Azure CLI.

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file containing a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Následující příklady znázorňují dvě metody získání seznamu ID prostředků – jednu na úrovni skupiny prostředků, jednu na úrovni předplatného.
```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="check-ahb-status-of-a-vm"></a>Zkontroluje stav AHB virtuálního počítače.
Stav AHB virtuálního počítače můžete zobrazit dvěma způsoby: pomocí rozhraní příkazového řádku Azure nebo pomocí Azure Instance Metadata Service (Azure IMDS).


### <a name="azure-cli"></a>Azure CLI

`az vm get-instance-view`Příkaz lze použít k tomuto účelu. Vyhledejte v odpovědi pole licenseType. Pokud pole licenseType existuje a hodnota je "RHEL_BYOS" nebo "SLES_BYOS", má váš virtuální počítač povolený přínos.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

V samotném virtuálním počítači můžete zadat dotaz na metadata IMDS identity, abyste určili licenseType virtuálního počítače. Hodnota licenseType "RHEL_BYOS" nebo "SLES_BYOS" bude označovat, že má váš virtuální počítač povolené výhody. [Tady](./instance-metadata-service.md#attested-data) najdete další informace o ověřených metadatech.

## <a name="compliance"></a>Dodržování předpisů

### <a name="red-hat"></a>Red Hat

Aby bylo možné použít Zvýhodněné hybridní využití Azure pro virtuální počítače s RHEL, musíte se nejdřív zaregistrovat v programu Red Hat Cloud Access. To můžete provést prostřednictvím webu Red Hat Cloud Access. Jakmile na svém VIRTUÁLNÍm počítači povolíte tuto výhodu, musíte virtuální počítač zaregistrovat s vlastním zdrojem aktualizací pomocí Správce předplatných Red Hat nebo Red Hat satelit. Registrace aktualizací vám zajistí, že zůstanete v podporovaném stavu.

### <a name="suse"></a>SUSE

Aby bylo možné použít Zvýhodněné hybridní využití Azure pro virtuální počítače s SLES, musíte být nejprve registrováni pomocí programu SUSE Public Cloud. Přečtěte si další informace o programu. Po zakoupení předplatných SUSE musíte své virtuální počítače zaregistrovat pomocí těchto předplatných pro vlastní zdroj aktualizací, a to buď pomocí centra SUSE pro zákazníky, serveru nástroje pro správu předplatného nebo správce SUSE.

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
*Otázka: mohu použít typ licence "RHEL_BYOS" s SLES obrázkem, nebo naopak?*

Odpověď: Ne, nemůžete. Pokud se pokusíte zadat typ licence, který nesprávně odpovídá distribuce běžícímu na vašem VIRTUÁLNÍm počítači, neaktualizují se žádná fakturační metadata. Pokud ale omylem zadáte špatný typ licence, budete moct znovu aktualizovat svůj virtuální počítač na správný typ licence.

*Otázka: zaregistroval (a) se službou Red Hat Cloud Access, ale stále není možné povolit výhodu na mých virtuálních počítačích RHEL. Co mám dělat?*

Odpověď: může trvat nějakou dobu, než se registrace předplatného služby Red Hat pro cloudový přístup došíří z Red Hat do Azure. Pokud se vám i po jednom pracovním dni stále zobrazuje chyba, obraťte se na podporu Microsoftu.

## <a name="common-issues"></a>Běžné problémy
Tato část obsahuje seznam běžných problémů, které by mohly být zjištěny, a kroky pro zmírnění rizik.

| Chyba | Omezení rizik |
| ----- | ---------- |
| Akce nemohla být dokončena, protože naše záznamy ukazují, že jste v předplatném Azure nepovolili přístup do cloudu Red Hat.... | Aby bylo možné využívat výhod s virtuálními počítači s RHEL, musíte nejdřív zaregistrovat vaše předplatná Azure s přístupem Red Hat Cloud Access. Navštivte tento odkaz, kde najdete další informace o registraci předplatných Azure pro cloudový přístup Red Hat.

## <a name="next-steps"></a>Další kroky
* Přečtěte si, jak vytvořit a aktualizovat virtuální počítače a přidat typy licencí (RHEL_BYOS, SLES_BYOS) pro Zvýhodněné hybridní využití Azure pomocí [Azure CLI tady.](https://docs.microsoft.com/cli/azure/vm?view=azure-cli-latest&preserve-view=true)
