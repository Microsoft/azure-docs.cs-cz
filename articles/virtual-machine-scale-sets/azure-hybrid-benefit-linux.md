---
title: Zvýhodněné hybridní využití Azure pro systémy Linux Virtual Machine Scale Sets
description: Přečtěte si, jak Zvýhodněné hybridní využití Azure možné použít na sadu škálování virtuálních počítačů, která vám pomůžou ušetřit peníze na virtuálních počítačích s Linux běžícími na Azure.
services: virtual-machine-scale-sets
documentationcenter: ''
author: mathapli
manager: rochakm
ms.service: virtual-machine-scale-sets
ms.subservice: linux
ms.collection: linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 03/20/2021
ms.author: mathapli
ms.openlocfilehash: 09e41b4071ad02120f7d303b32bc87bc0ba39e2c
ms.sourcegitcommit: 44edde1ae2ff6c157432eee85829e28740c6950d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2021
ms.locfileid: "105549276"
---
# <a name="azure-hybrid-benefit-for-linux-virtual-machine-scale-set-public-preview"></a>Zvýhodněné hybridní využití Azure pro Linux Virtual Machine Scale set (Public Preview)

**Zvýhodněné hybridní využití Azure pro sadu škálování virtuálního počítače se systémem Linux je nyní ve verzi Public Preview**. Výhoda AHB vám může přispět ke snížení nákladů na spouštění RHEL a SLES [sad škálování virtuálních počítačů](https://docs.microsoft.com/azure/virtual-machine-scale-sets/overview).

S touto výhodou platíte jenom náklady na infrastrukturu vaší sady škálování. Tato výhoda je dostupná pro všechny image RHEL a SLES Marketplace s průběžnými platbami (PAYG).


>[!NOTE]
> Tento článek popisuje Zvýhodněné hybridní využití Azure pro Linux VMSS. K dispozici je samostatný [článek [AHB pro virtuální počítače se systémem Linux](https://docs.microsoft.com/azure/virtual-machines/linux/azure-hybrid-benefit-linux), který je již k dispozici pro zákazníky Azure od listopadu 2020. listopadu.

## <a name="benefit-description"></a>Popis výhod
Azure Hybrid umožňuje využívat stávající licence pro cloudový přístup ze Red Hat nebo SUSE a pružně převést instance sady škálování virtuálních počítačů na fakturaci s využitím vlastního předplatného (BYOS). 

Sada škálování virtuálního počítače nasazená z imagí PAYG Marketplace bude účtovat poplatky za infrastrukturu i software. Pomocí AHB lze instance PAYG převést na model fakturace BYOS bez nutnosti opětovného nasazení.

:::image type="content" source="./media/azure-hybrid-benefit-linux/azure-hybrid-benefit-linux-cost.png" alt-text="Zvýhodněné hybridní využití Azure vizualizace nákladů na virtuální počítače se systémem Linux.":::

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux"></a>Rozsah Zvýhodněné hybridní využití Azure způsobilosti pro Linux
Zvýhodněné hybridní využití Azure je k dispozici pro všechny obrázky PAYG a SLES z Azure Marketplace. Tato výhoda ještě není dostupná pro image RHEL nebo SLES BYOS nebo vlastní image z Azure Marketplace.

Instance vyhrazených hostitelů Azure a zvýhodněné hybridní využití SQL nejsou vhodné pro Zvýhodněné hybridní využití Azure, pokud už tuto výhodu používáte s virtuálními počítači Linux.

## <a name="get-started"></a>Začínáme

### <a name="red-hat-customers"></a>Zákazníci se Red Hat

Zvýhodněné hybridní využití Azure pro RHEL je k dispozici pro zákazníky se Red Hat, kteří splňují obě tato kritéria:

- Mít aktivní nebo nepoužívané předplatná RHEL, která mají nárok na použití v Azure
- Povolili jste jedno nebo více předplatných pro použití v Azure s programem [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) .

> [!IMPORTANT]
> Ujistěte se, že v programu [cloudového přístupu](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) bylo povolené správné předplatné.

Pokud chcete začít využívat výhody pro Red Hat, postupujte takto:

1. Umožněte vašim oprávněným předplatným RHEL v Azure pomocí [rozhraní Red Hat Cloud Access](https://access.redhat.com/management/cloud).

   Předplatné Azure, které zadáte během procesu povolení pro cloudový přístup Red Hat, pak budou moci používat funkci Zvýhodněné hybridní využití Azure.
1. Použijte Zvýhodněné hybridní využití Azure pro všechny vaše stávající a nové PAYG sady škálování virtuálních počítačů s RHEL. Tuto výhodu můžete povolit pomocí Azure Portal nebo Azure CLI.
1. Použijte doporučené [Další kroky](https://access.redhat.com/articles/5419341) pro konfiguraci zdrojů aktualizací pro virtuální počítače s RHEL a pokyny pro dodržování předpisů RHEL pro předplatné.


### <a name="suse-customers"></a>Zákazníci SUSE

Pokud chcete začít využívat výhody pro SUSE:

1. Zaregistrujte se pomocí programu veřejné cloudy SUSE.
1. Využijte výhod pro nově vytvořenou nebo existující sadu škálování virtuálního počítače prostřednictvím Azure Portal nebo Azure CLI.
1. Zaregistrujte své virtuální počítače, které obdrží výhodu, pomocí samostatného zdroje aktualizací.


## <a name="enable-and-disable-the-benefit-on-azure-portal"></a>Povolení nebo zakázání výhod na webu Azure Portal 
Prostředí portálu pro povolení a zakázání AHB v sadě škálování virtuálního počítače není **aktuálně k dispozici**.

## <a name="enable-and-disable-the-benefit-using-azure-cli"></a>Povolení a zákaz zvýhodnění pomocí Azure CLI

`az vmss update`K aktualizaci existujících virtuálních počítačů můžete použít příkaz. Pro virtuální počítače s RHEL spusťte příkaz s `--license-type` parametrem `RHEL_BYOS` . Pro virtuální počítače s SLES spusťte příkaz s `--license-type` parametrem `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>Příklad rozhraní příkazového řádku pro povolení výhody
```azurecli
# This will enable the benefit on a RHEL VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VMSS
az vmss update --resource-group myResourceGroup --name myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Příklad rozhraní příkazového řádku pro zakázání výhody
Pokud chcete tuto výhodu zakázat, použijte `--license-type` hodnotu `None` :

```azurecli
# This will disable the benefit on a VM
az vmss update -g myResourceGroup -n myVmName --license-type None
```

>[!NOTE]
> Sady škálování mají ["zásady upgradu"](https://docs.microsoft.com/azure/virtual-machine-scale-sets/virtual-machine-scale-sets-upgrade-scale-set#how-to-bring-vms-up-to-date-with-the-latest-scale-set-model) , které určují, jak jsou virtuální počítače aktualizované pomocí nejnovějšího modelu sady škálování. Proto pokud vaše VMSS mají zásadu automatického upgradu, použije se při aktualizaci instancí virtuálních počítačů automaticky výhoda AHB. Pokud VMSS mají na základě naplánovaných aktualizací zásadu aktualizace "valení", použije se AHB.
V případě zásad upgradu ručního upgradu budete muset provést ruční upgrade všech existujících virtuálních počítačů.  

### <a name="cli-example-to-upgrade-virtual-machine-scale-set-instances-in-case-of-manual-upgrade-policy"></a>Příklad rozhraní příkazového řádku pro upgrade instancí sady škálování virtuálních počítačů v případě zásad ručního upgradu 
```azurecli
# This will bring VMSS instances up to date with latest VMSS model 
az vmss update-instances --resource-group myResourceGroup --name myScaleSet --instance-ids {instanceIds}
```

## <a name="apply-the-azure-hybrid-benefit-at-virtual-machine-scale-set-create-time"></a>Použít Zvýhodněné hybridní využití Azure v nastavení škálování virtuálního počítače – vytvořit čas 
Kromě použití Zvýhodněné hybridní využití Azure pro existující sadu škálování virtuálních počítačů s průběžnými platbami, můžete ji vyvolat v době vytváření sady škálování virtuálních počítačů. Výhody tohoto postupu jsou threefold:
- Můžete zřídit PAYG i BYOS instance sady škálování virtuálních počítačů pomocí stejného obrazu a procesu.
- Umožňuje budoucí změny v režimu licencování, takže není k dispozici s BYOS imagí.
- Instance sady škálování virtuálního počítače budou ve výchozím nastavení připojeny ke službě Red Hat Update Infrastructure (RHUI), aby byla zajištěna aktuálnost a zabezpečení. Aktualizovaný mechanismus můžete po nasazení změnit kdykoli.

### <a name="cli-example-to-create-virtual-machine-scale-set-with-ahb-benefit"></a>Příklad rozhraní příkazového řádku pro vytvoření sady škálování virtuálních počítačů s výhodou AHB
```azurecli
# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type RHEL_BYOS 

# This will enable the benefit while creating RHEL VMSS
az vmss create --name myVmName --resource-group myResourceGroup --vnet-name myVnet --subnet mySubnet  --image myRedHatImageURN --admin-username myAdminUserName --admin-password myPassword --instance-count myInstanceCount --license-type SLES_BYOS
```

## <a name="next-steps"></a>Další kroky
* [Naučte se vytvářet a aktualizovat virtuální počítače a přidávat typy licencí (RHEL_BYOS, SLES_BYOS) pro Zvýhodněné hybridní využití Azure pomocí Azure CLI.](/cli/azure/vmss)
