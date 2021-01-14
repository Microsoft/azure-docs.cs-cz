---
title: Virtuální počítače Zvýhodněné hybridní využití Azure a Linux
description: Přečtěte si, jak vám Zvýhodněné hybridní využití Azure můžou ušetřit peníze na virtuálních počítačích s Linux běžícími na Azure.
services: virtual-machines
documentationcenter: ''
author: mathapli
manager: westonh
ms.service: virtual-machines-linux
ms.topic: conceptual
ms.workload: infrastructure-services
ms.date: 09/22/2020
ms.author: mathapli
ms.openlocfilehash: 990c396c859477c83e0ef421659a299920ee0c35
ms.sourcegitcommit: f5b8410738bee1381407786fcb9d3d3ab838d813
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/14/2021
ms.locfileid: "98209130"
---
# <a name="how-azure-hybrid-benefit-applies-for-linux-virtual-machines"></a>Jak Zvýhodněné hybridní využití Azure platí pro virtuální počítače se systémem Linux

Zvýhodněné hybridní využití Azure je zvýhodněné licencování, které vám pomůže významně snížit náklady na spouštění vašich Red Hat Enterprise Linux (RHEL) a virtuálních počítačů SUSE Linux Enterprise Server (SLES) v cloudu. S touto výhodou platíte jenom za náklady na infrastrukturu vašeho virtuálního počítače, protože předplatné RHEL nebo SLES pokrývá poplatky za software. Tato výhoda je dostupná pro všechny image RHEL a SLES Marketplace s průběžnými platbami (PAYG).

Zvýhodněné hybridní využití Azure pro virtuální počítače se systémem Linux je teď veřejně dostupný.

## <a name="benefit-description"></a>Popis výhod

Prostřednictvím Zvýhodněné hybridní využití Azure můžete migrovat místní servery RHEL a SLES do Azure tím, že převedete stávající virtuální počítače RHEL a SLES PAYG v Azure a budete mít k disu fakturaci na vlastní předplatné (BYOS). Virtuální počítače nasazené z imagí PAYG v Azure obvykle budou účtovat poplatek za infrastrukturu a poplatek za software. Pomocí Zvýhodněné hybridní využití Azure můžou být virtuální počítače PAYG převedené do modelu fakturace BYOS bez opětovného nasazení, takže se můžete vyhnout jakémukoli nebezpečí výpadku.

:::image type="content" source="./media/ahb-linux/azure-hybrid-benefit-cost.png" alt-text="Zvýhodněné hybridní využití Azure vizualizace nákladů na virtuální počítače se systémem Linux.":::

Po povolení výhod na VIRTUÁLNÍm počítači s RHEL nebo SLES se už nebudete účtovat za další poplatek za software, který se obvykle účtuje na virtuálním počítači s PAYG. Místo toho začne váš virtuální počítač počítat s BYOS poplatky, které zahrnují jenom výpočetní a licenční poplatek za daný software.

Můžete také převést virtuální počítač s povoleným přínosem zpátky na PAYG model fakturace.

## <a name="scope-of-azure-hybrid-benefit-eligibility-for-linux-vms"></a>Rozsah Zvýhodněné hybridní využití Azure způsobilosti pro virtuální počítače se systémem Linux

Zvýhodněné hybridní využití Azure je k dispozici pro všechny obrázky PAYG a SLES z Azure Marketplace. Tato výhoda ještě není dostupná pro image RHEL nebo SLES BYOS nebo vlastní image z Azure Marketplace.

Rezervované instance, instance vyhrazených hostitelů Azure a zvýhodněné hybridní využití SQL nejsou způsobilé pro Zvýhodněné hybridní využití Azure, pokud už tuto výhodu používáte s virtuálními počítači Linux.

## <a name="get-started"></a>Začínáme

### <a name="red-hat-customers"></a>Zákazníci se Red Hat

Zvýhodněné hybridní využití Azure pro RHEL je k dispozici pro zákazníky se Red Hat, kteří splňují obě tato kritéria:

- Mít aktivní nebo nepoužívané předplatná RHEL, která mají nárok na použití v Azure
- Povolili jste jedno nebo více předplatných pro použití v Azure s programem [Red Hat Cloud Access](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) .

> [!IMPORTANT]
> Ujistěte se, že v programu [cloudového přístupu](https://www.redhat.com/en/technologies/cloud-computing/cloud-access) bylo povolené správné předplatné.

Pokud chcete začít využívat výhody pro Red Hat, postupujte takto:

1. Povolte jedno nebo více vašich oprávněných předplatných RHEL pro použití v Azure pomocí [rozhraní Red Hat Cloud Access](https://access.redhat.com/management/cloud).

   Předplatné Azure, které zadáte během procesu povolení pro cloudový přístup Red Hat, pak budou moci používat funkci Zvýhodněné hybridní využití Azure.
1. Použijte Zvýhodněné hybridní využití Azure na libovolný ze stávajících virtuálních počítačů PAYG s RHEL a všech nových virtuálních počítačů RHEL, které nasadíte z Azure Marketplacech imagí PAYG. Tuto výhodu můžete povolit pomocí Azure Portal nebo Azure CLI.
1. Použijte doporučené [Další kroky](https://access.redhat.com/articles/5419341) pro konfiguraci zdrojů aktualizací pro virtuální počítače s RHEL a pokyny pro dodržování předpisů RHEL pro předplatné.


### <a name="suse-customers"></a>Zákazníci SUSE

Pokud chcete začít využívat výhody pro SUSE:

1. Zaregistrujte se pomocí programu veřejné cloudy SUSE.
1. Využijte výhod pro nově vytvořené nebo existující virtuální počítače prostřednictvím Azure Portal nebo pomocí Azure CLI.
1. Zaregistrujte své virtuální počítače, které obdrží výhodu, pomocí samostatného zdroje aktualizací.

## <a name="enable-and-disable-the-benefit-in-the-azure-portal"></a>Povolení nebo zakázání výhod v Azure Portal

Výhodou pro existující virtuální počítače můžete povolit tak, že navštívíte možnost **Konfigurace** na levé straně a pak provedete následující kroky. V rámci prostředí pro vytváření virtuálních počítačů můžete povolit výhody pro nové virtuální počítače.

### <a name="azure-portal-example-to-enable-the-benefit-for-an-existing-vm"></a>Azure Portal příklad povolení výhody pro existující virtuální počítač:
1. Navštívit [portál Microsoft Azure](https://portal.azure.com/)
1. Na portálu přejdete na stránku vytvořit virtuální počítač.
 ![AHB při vytváření virtuálního počítače](./media/azure-hybrid-benefit/create-vm-ahb.png)
1. Kliknutím na zaškrtávací políčko povolíte konverzi AHB a použijete licence pro cloudový přístup.
 ![Zaškrtávací políčko AHB při vytváření virtuálního počítače](./media/azure-hybrid-benefit/create-vm-ahb-checkbox.png)
1. Vytvořit virtuální počítač za další sadu instrukcí
1. Zkontrolujte okno **Konfigurace** a zobrazí se možnost povoleno. 
![Okno Konfigurace AHB po vytvoření](./media/azure-hybrid-benefit/create-configuration-blade.png)

### <a name="azure-portal-example-to-enable-the-benefit-during-creation-of-vm"></a>Azure Portal příklad povolení výhod během vytváření virtuálního počítače:
1. Navštívit [portál Microsoft Azure](https://portal.azure.com/)
1. Otevřete stránku virtuálního počítače, na které chcete použít převod.
1. Na levé straně otevřete možnost **Konfigurace** . Zobrazí se část licencování. Pokud chcete povolit převod AHB, zaškrtněte přepínač Ano a zaškrtněte políčko Potvrdit.
![Okno Konfigurace AHB po vytvoření](./media/azure-hybrid-benefit/create-configuration-blade.png)


>[!NOTE]
> Pokud jste vytvořili **vlastní snímek** nebo **sdílenou bitovou kopii (SIG)** image RHEL nebo SLES PAYG Marketplace, můžete k povolení zvýhodněné hybridní využití Azure použít jenom Azure CLI. Toto je známé omezení a momentálně není k dispozici žádná časová osa pro poskytování této funkce na webu Azure Portal.



## <a name="enable-and-disable-the-benefit-in-the-azure-cli"></a>Povolení nebo zakázání výhod v Azure CLI

`az vm update`K aktualizaci existujících virtuálních počítačů můžete použít příkaz. Pro virtuální počítače s RHEL spusťte příkaz s `--license-type` parametrem `RHEL_BYOS` . Pro virtuální počítače s SLES spusťte příkaz s `--license-type` parametrem `SLES_BYOS` .

### <a name="cli-example-to-enable-the-benefit"></a>Příklad rozhraní příkazového řádku pro povolení výhody
```azurecli
# This will enable the benefit on a RHEL VM
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS

# This will enable the benefit on a SLES VM
az vm update -g myResourceGroup -n myVmName --license-type SLES_BYOS
```
### <a name="cli-example-to-disable-the-benefit"></a>Příklad rozhraní příkazového řádku pro zakázání výhody
Pokud chcete tuto výhodu zakázat, použijte `--license-type` hodnotu `None` :

```azurecli
# This will disable the benefit on a VM
az vm update -g myResourceGroup -n myVmName --license-type None
```

### <a name="cli-example-to-enable-the-benefit-on-a-large-number-of-vms"></a>Příklad rozhraní příkazového řádku pro povolení výhody pro velký počet virtuálních počítačů
Pokud chcete tuto výhodu povolit pro velký počet virtuálních počítačů, můžete použít `--ids` parametr v Azure CLI:

```azurecli
# This will enable the benefit on a RHEL VM. In this example, ids.txt is an
# existing text file that contains a delimited list of resource IDs corresponding
# to the VMs using the benefit
az vm update -g myResourceGroup -n myVmName --license-type RHEL_BYOS --ids $(cat ids.txt)
```

Následující příklady znázorňují dvě metody získání seznamu ID prostředků: jednu na úrovni skupiny prostředků a jednu na úrovni předplatného.

```azurecli
# To get a list of all the resource IDs in a resource group:
$(az vm list -g MyResourceGroup --query "[].id" -o tsv)

# To get a list of all the resource IDs of VMs in a subscription:
az vm list -o json | jq '.[] | {VMName: .name, ResourceID: .id}'
```

## <a name="apply-the-azure-hybrid-benefit-at-vm-create-time"></a>Použít Zvýhodněné hybridní využití Azure v čase vytvoření virtuálního počítače
Kromě použití Zvýhodněné hybridní využití Azure pro existující virtuální počítače s průběžnými platbami, můžete ji vyvolat v době vytváření virtuálního počítače. Výhody tohoto postupu jsou threefold:
- Pomocí stejného obrazu a procesu můžete zřídit virtuální počítače PAYG i BYOS.
- Umožňuje budoucí změny v režimu licencování, není k dispozici s BYOS imagí nebo pokud přenesete vlastní virtuální počítač.
- Virtuální počítač bude ve výchozím nastavení připojen ke službě RHUI (Red Hat Update Infrastructure), aby bylo zajištěno, že bude stále aktuální a zabezpečený. Aktualizovaný mechanismus můžete po nasazení změnit kdykoli.

## <a name="check-the-azure-hybrid-benefit-status-of-a-vm"></a>Zkontroluje stav Zvýhodněné hybridní využití Azure virtuálního počítače.
Stav Zvýhodněné hybridní využití Azure virtuálního počítače můžete zobrazit pomocí rozhraní příkazového řádku Azure nebo pomocí Azure Instance Metadata Service.

### <a name="azure-cli"></a>Azure CLI

`az vm get-instance-view`K tomuto účelu můžete použít příkaz. Vyhledejte `licenseType` pole v odpovědi. Pokud `licenseType` pole existuje a hodnota je `RHEL_BYOS` nebo `SLES_BYOS` , má váš virtuální počítač povolený přínos.

```azurecli
az vm get-instance-view -g MyResourceGroup -n MyVm
```

### <a name="azure-instance-metadata-service"></a>Azure Instance Metadata Service

V samotném virtuálním počítači můžete zadat dotaz na ověřená metadata v Azure Instance Metadata Service k určení hodnoty virtuálního počítače `licenseType` . `licenseType`Hodnota `RHEL_BYOS` nebo `SLES_BYOS` bude označovat, že váš virtuální počítač má povolený přínos. Další [informace o ověřených metadatech](./instance-metadata-service.md#attested-data)

## <a name="compliance"></a>Dodržování předpisů

### <a name="red-hat"></a>Red Hat

Zákazníci, kteří používají Zvýhodněné hybridní využití Azure pro RHEL, souhlasí se standardními [právními podmínkami](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Cloud_Software_Subscription_Agreement_for_Microsoft_Azure.pdf) a [prohlášením o zásadách ochrany osobních údajů](http://www.redhat.com/licenses/cloud_CSSA/Red_Hat_Privacy_Statement_for_Microsoft_Azure.pdf) , které jsou přidružené k nabídkám Azure Marketplace RHEL.

Zákazníci, kteří používají Zvýhodněné hybridní využití Azure pro RHEL, mají tři možnosti poskytování aktualizací softwaru a oprav těchto virtuálních počítačů:

- [Infrastruktura aktualizace Red Hat](../workloads/redhat/redhat-rhui.md) (výchozí možnost)
- Red Hat satelitní Server
- Správce předplatného Red Hat

Zákazníci, kteří zvolí možnost RHUI, můžou dál používat RHUI jako hlavní zdroj aktualizace pro své Zvýhodněné hybridní využití Azure virtuální počítače RHEL bez připojení k těmto virtuálním počítačům RHEL odběry. Zákazníci, kteří zvolí možnost RHUI, jsou odpovědni za zajištění dodržování předpisů RHEL pro předplatné.

Zákazníci, kteří zvolí systém Red Hat satelitní Server nebo správce předplatného Red Hat, by měli odebrat konfiguraci RHUI a pak připojit předplatné RHEL s povoleným přístupem ke svým Zvýhodněné hybridní využití Azurem virtuálním počítačům RHEL.  

Další informace o dodržování předpisů pro předplatné Red Hat, aktualizacích softwaru a zdrojích pro Zvýhodněné hybridní využití Azure virtuálních počítačů s RHEL najdete v [článku o použití RHEL předplatných s zvýhodněné hybridní využití Azure na Red Hat](https://access.redhat.com/articles/5419341).

### <a name="suse"></a>SUSE

Pokud chcete použít Zvýhodněné hybridní využití Azure pro virtuální počítače s SLES a informace o přechodu z SLES PAYG na BYOS nebo při přesunu z SLES BYOS na PAYG, přečtěte si téma [SUSE Linux Enterprise a zvýhodněné hybridní využití Azure](https://www.suse.com/c/suse-linux-enterprise-and-azure-hybrid-benefit/). 

## <a name="frequently-asked-questions"></a>Nejčastější dotazy
*Otázka: mohu použít typ licence `RHEL_BYOS` s SLES obrázkem nebo naopak?*

Odpověď: Ne, nemůžete. Když se pokusíte zadat typ licence, který nesprávně odpovídá distribuci běžící na vašem VIRTUÁLNÍm počítači, neaktualizují se žádná fakturační metadata. Pokud ale omylem zadáte špatný typ licence, budete moct znovu aktualizovat svůj virtuální počítač na správný typ licence.

*Otázka: zaregistroval (a) se službou Red Hat Cloud Access, ale na mých virtuálních počítačích s RHEL pořád nemůže povolit výhodu. Co mám dělat?*

Odpověď: může trvat nějakou dobu, než se registrace předplatného služby Red Hat pro cloudový přístup došíří z Red Hat do Azure. Pokud se chyba stále zobrazuje po jednom pracovním dni, obraťte se na podporu Microsoftu.

*Otázka: nasadil (a) jsem virtuální počítač pomocí RHEL BYOS "zlatý image". Můžu převést fakturaci na těchto obrázcích z BYOS na PAYG?*

Odpověď: Ne, nemůžete. Zvýhodněné hybridní využití Azure podporuje převod jenom na image s průběžnými platbami.

*Otázka: nahráli jsem vlastní RHEL image z místního prostředí (přes Azure Migrate, Azure Site Recovery nebo jinak) do Azure. Můžu převést fakturaci na těchto obrázcích z BYOS na PAYG?*

Odpověď: Ne, nemůžete. Funkce Zvýhodněné hybridní využití Azure je v tuto chvíli dostupná jenom pro image RHEL a SLES v Azure Marketplace. 

*Otázka: nahráli jsem vlastní RHEL image z místního prostředí (přes Azure Migrate, Azure Site Recovery nebo jinak) do Azure. Musím od Zvýhodněné hybridní využití Azure něco dělat?*

Odpověď: Ne, nemusíte. RHEL obrázky se už považují za BYOS a účtují se jenom za náklady na infrastrukturu Azure. Zodpovídáte za náklady na předplatné RHEL, stejně jako u vašich místních prostředí. 

*Otázka: je možné použít Zvýhodněné hybridní využití Azure na virtuálních počítačích nasazených z Azure Marketplacech imagí SAP RHEL a SLES?*

Odpověď: Ano, můžete. Můžete použít typ licence `RHEL_BYOS` pro virtuální počítače s RHEL a `SLES_BYOS` pro převody virtuálních počítačů nasazených z Azure Marketplace imagí RHEL a SLES SAP.

*Otázka: je možné použít Zvýhodněné hybridní využití Azure na Virtual Machine Scale Sets pro RHEL a SLES?*

Odpověď: Ne, nemůžete. Služba Virtual Machine Scale Sets není aktuálně v oboru Zvýhodněné hybridní využití Azure pro RHEL a SLES.

*Otázka: je možné použít Zvýhodněné hybridní využití Azure u rezervovaných instancí pro RHEL a SLES?*

Odpověď: Ne, nemůžete. Rezervované instance nejsou aktuálně v rozsahu Zvýhodněné hybridní využití Azure pro RHEL a SLES.

*Otázka: je možné použít Zvýhodněné hybridní využití Azure na virtuálním počítači nasazeném pro SQL Server na obrázcích RHEL?*

Odpověď: Ne, nemůžete. Pro podporu těchto virtuálních počítačů není k dispozici žádný plán.

*Otázka: je možné použít Zvýhodněné hybridní využití Azure v předplatném svého virtuálního datového centra RHEL?*

Odpověď: Ne, nemůžete. VDC se v Azure vůbec nepodporuje, včetně AHB.  
 

## <a name="common-problems"></a>Běžné problémy
V této části jsou uvedené běžné problémy, se kterými se můžete setkat, a postup pro zmírnění rizik.

| Chyba | Omezení rizik |
| ----- | ---------- |
| Akce nemohla být dokončena, protože naše záznamy ukazují, že jste v předplatném Azure nepovolili přístup do cloudu Red Hat.... | Pokud chcete využít výhod s virtuálními počítači s RHEL, musíte nejdřív [zaregistrovat vaše předplatná Azure pomocí služby Red Hat Cloud Access](https://access.redhat.com/management/cloud).

## <a name="next-steps"></a>Další kroky
* [Naučte se vytvářet a aktualizovat virtuální počítače a přidávat typy licencí (RHEL_BYOS, SLES_BYOS) pro Zvýhodněné hybridní využití Azure pomocí Azure CLI.](/cli/azure/vm?preserve-view=true&view=azure-cli-latest)