---
title: Přehled virtuálních počítačů se systémem Linux v Azure
description: Přehled virtuálních počítačů se systémem Linux v Azure.
author: cynthn
ms.service: virtual-machines-linux
ms.topic: overview
ms.workload: infrastructure
ms.date: 11/14/2019
ms.author: cynthn
ms.custom: mvc
ms.openlocfilehash: 4067fc4e47dce83852af971daae6cda3933d0e39
ms.sourcegitcommit: b4647f06c0953435af3cb24baaf6d15a5a761a9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2021
ms.locfileid: "101667182"
---
# <a name="linux-virtual-machines-in-azure"></a>Virtuální počítače s Linuxem v Azure

Azure Virtual Machines (VM) je jedním z několika typů [škálovatelných výpočetních prostředků na vyžádání](/azure/architecture/guide/technology-choices/compute-decision-tree), které Azure nabízí. Obvykle zvolíte virtuální počítač, když potřebujete větší kontrolu nad výpočetním prostředí, než nabízí jiné možnosti. Tento článek obsahuje informace o tom, co byste měli zvážit před vytvořením virtuálního počítače, jak ho vytvořit a jak ho spravovat.

Virtuální počítač Azure vám nabídne flexibilitu virtualizace bez nutnosti zakoupení a údržby fyzického hardwaru, na kterém běží. Nevyhnete se však údržbě virtuálního počítače prováděním úloh, jako jsou konfigurace, aplikování oprav chyb a instalace softwaru, který na něm běží.

Virtuální počítače Azure lze použít různými způsoby. Tady je několik příkladů:

* **Vývoj a testování** – Virtuální počítače Azure nabízejí rychlý a snadný způsob, jak vytvořit počítač s konkrétní konfigurací pro kódování a testování aplikací.
* **Aplikace v cloudu** – Protože poptávka po aplikaci může kolísat, z ekonomického hlediska může mít smysl spouštět ji na virtuálním počítači v Azure. Za další virtuální počítače platíte, když je potřebujete, a když ne, tak je vypnete.
* **Rozšířené datové centrum** – Virtuální počítače ve virtuální síti Azure můžete snadno připojit k síti vaší organizace.

Počet virtuálních počítačů, které vaše aplikace používá, lze vertikálně nebo horizontálně navýšit pro splnění vašich požadavků.

## <a name="what-do-i-need-to-think-about-before-creating-a-vm"></a>Co je třeba zvážit před vytvořením virtuálního počítače?
Při sestavování infrastruktury aplikace v Azure vždy existuje velké množství [aspektů návrhu](/azure/architecture/reference-architectures/n-tier/linux-vm). Než začnete, je důležité zvážit následující aspekty virtuálního počítače:

* Názvy prostředků vaší aplikace
* Umístění, kam se ukládají prostředky
* Velikost virtuálního počítače
* Maximální počet virtuálních počítačů, které lze vytvořit
* Operační systém, který běží na virtuálním počítači
* Konfigurace virtuálního počítače po jeho spuštění
* Související prostředky, které virtuální počítač potřebuje

### <a name="locations"></a>Umístění
Všechny prostředky vytvořené v Azure jsou distribuované mezi několik [geografických oblastí](https://azure.microsoft.com/regions/) po celém světě. Při vytváření virtuálního počítače se oblast obvykle nazývá **umístění**. U virtuálního počítače umístění určuje, kde jsou uloženy virtuální pevné disky.

Tato tabulka ukazuje několik způsobů, jak můžete získat seznam dostupných umístění.

| Metoda | Popis |
| --- | --- |
| portál Azure |Při vytváření virtuálního počítače vyberte umístění ze seznamu. |
| Azure PowerShell |Použijte příkaz [Get-AzLocation](/powershell/module/az.resources/get-azlocation) . |
| REST API |Použijte operaci [Vypsat umístění](/rest/api/resources/subscriptions). |
| Azure CLI |Použijte operaci [az account list-locations](/cli/azure/account). |

## <a name="availability"></a>Dostupnost
Platforma Azure oznámila špičkovou smlouvu o úrovni služeb (SLA) s 99,9% dostupností pro samostatné instance virtuálních počítačů za předpokladu, že virtuální počítač nasadíte se službou Premium Storage pro všechny disky.  Aby se na vaše nasazení vztahovala standardní záruka 99,95% dostupnosti virtuálního počítače podle smlouvy SLA, stále je nutné nasadit alespoň dva virtuální počítače, které vaši úlohu spouští v rámci skupiny dostupnosti. Skupina dostupnosti zajistí, že vaše virtuální počítače jsou distribuované mezi několik domén selhání v datacentrech Azure a také nasazené na hostitele s různými časovými obdobími údržby. Úplná smlouva [Azure SLA](https://azure.microsoft.com/support/legal/sla/virtual-machines/) vysvětluje garantovanou dostupnost Azure jako celku.

## <a name="vm-size"></a>Velikost virtuálního počítače
[Velikost](../sizes.md) virtuálního počítače, který použijete, se určuje podle úlohy, kterou chcete spustit. Velikost, kterou vyberete, pak určuje další faktory, jako například výpočetní výkon, paměť a kapacitu úložiště. Azure nabízí širokou škálu velikostí, které podporují mnoho typů použití.

Azure účtuje [hodinovou cenu](https://azure.microsoft.com/pricing/details/virtual-machines/linux/) na základě velikosti virtuálního počítače a operačního systému. V případě neúplných hodin Azure účtuje jenom využité minuty. Služba Storage je oceněna a účtována samostatně.

## <a name="vm-limits"></a>Omezení virtuálního počítače
Vaše předplatné má nastavené výchozí [kvóty](../../azure-resource-manager/management/azure-subscription-service-limits.md), které mohou ovlivnit nasazení velkého počtu virtuálních počítačů pro váš projekt. Aktuální limit jednoho předplatného je 20 virtuálních počítačů na oblast. Limity můžete navýšit tak, že [vyplníte lístek podpory s žádostí o navýšení](../../azure-portal/supportability/resource-manager-core-quotas-request.md).

## <a name="managed-disks"></a>Spravované disky

Spravované disky se na pozadí starají o vytvoření a správu účtu služby Azure Storage za vás a zajišťují, že si nemusíte dělat starosti s omezením škálovatelnosti účtu úložiště. Zadáte velikost disku a úroveň výkonu (Standard nebo Premium) a Azure disk vytvoří a postará se o jeho správu. S využitím úložiště si nemusíte dělat starosti ani v případě, že přidáváte disky nebo vertikálně navyšujete a snižujete kapacitu virtuálního počítače. Pokud vytváříte nové virtuální počítače, [pomocí Azure CLI](quick-create-cli.md) nebo webu Azure Portal vytvořte virtuální počítače se spravovanými disky operačního systému a datovými disky. Pokud máte virtuální počítače s nespravovanými disky, můžete [virtuální počítače převést na využívání Spravovaných disků](convert-unmanaged-to-managed-disks.md).

V jednom účtu úložiště na oblast Azure můžete také spravovat vlastní image a vytvářet pomocí nich stovky virtuálních počítačů v rámci stejného předplatného. Další informace o spravovaných discích najdete v tématu [Přehled služby Managed Disks](../managed-disks-overview.md).

## <a name="distributions"></a>Distribuce 
Microsoft Azure podporuje spouštění mnoha oblíbených distribucí Linuxu, které poskytuje a udržuje řada partnerů.  Dostupná distribuce můžete najít v Azure Marketplace. Microsoft aktivně spolupracuje s různými komunitami okolo Linuxu s cílem ještě více rozšířit seznam [distribucí Linuxu schválených pro Azure](endorsed-distros.md).

Pokud vaše upřednostňovaná distribuce Linuxu aktuálně není v galerii, můžete pro virtuální počítač použít vlastní distribuci Linuxu tak, že [vytvoříte a nahrajete VHD s Linuxem v Azure](create-upload-generic.md).

Microsoft úzce spolupracuje s partnery na zajištění aktualizace a optimalizace dostupných imagí pro modul runtime Azure.  Další informace o nabídkách partnerů Azure najdete na následujících odkazech:

* Linux v Azure – [Schválené distribuce](endorsed-distros.md)
* SUSE – [Azure Marketplace – SUSE Linux Enterprise Server](https://azuremarketplace.microsoft.com/marketplace/apps?page=1&search=suse)
* Red Hat- [Azure Marketplace-Red Hat Enterprise Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Red%20Hat%20Enterprise%20Linux)
* Kanonický [Azure Marketplace – Server s Ubuntu](https://azuremarketplace.microsoft.com/marketplace/apps/Canonical.UbuntuServer)
* Debian- [Azure Marketplace-Debian](https://azuremarketplace.microsoft.com/marketplace/apps?search=Debian&page=1)
* FreeBSD- [Azure Marketplace-FreeBSD](https://azuremarketplace.microsoft.com/marketplace/apps?search=freebsd&page=1)
* Flatcar- [Azure Marketplace-Flatcar kontejner Linux](https://azuremarketplace.microsoft.com/marketplace/apps?search=Flatcar&page=1)
* RancherOS – [Azure Marketplace – RancherOS](https://azuremarketplace.microsoft.com/marketplace/apps/rancher.rancheros)
* Bitnami – [Knihovna Bitnami pro Azure](https://azure.bitnami.com/)
* Mesosphere – [Azure Marketplace – Mesosphere DC/OS v Azure](https://azure.microsoft.com/services/kubernetes-service/mesosphere/)
* Image Docker- [Azure Marketplace-Docker](https://azuremarketplace.microsoft.com/marketplace/apps?search=docker&page=1&filters=virtual-machine-images)
* Jenkins – [Azure Marketplace – Platforma CloudBees Jenkins](https://azuremarketplace.microsoft.com/marketplace/apps/cloudbees.cloudbees-core-contact)


## <a name="cloud-init"></a>Cloud-init 

K dosažení správné kultury DevOps musí být veškerá infrastruktura tvořená kódem.  Když je veškerá infrastruktura v kódu, můžete ji snadno vytvořit znovu.  Azure spolupracuje se všemi hlavními nástroji pro automatizaci, jako jsou Ansible, Chef, SaltStack a Puppet.  Azure obsahuje také vlastní nástroj pro automatizaci:

* [Šablony Azure](create-ssh-secured-vm-from-template.md)
* [Azure `VMaccess`](../extensions/vmaccess.md)

Azure podporuje pro [Cloud-init](https://cloud-init.io/) napříč většinou Linux distribuce, která ho podporují.  Aktivně spolupracujeme s našimi distribuce partnery se systémem Linux, aby byly k dispozici image s povoleným cloudovým inicializacím na webu Azure Marketplace. Díky těmto imagí budou vaše nasazení a konfigurace pro cloudovou inicializaci bez problémů fungovat s virtuálními počítači a sadami škálování virtuálních počítačů.

* [Použití cloud-init na virtuálních počítačích Azure s Linuxem](using-cloud-init.md)

## <a name="storage"></a>Storage
* [Úvod do Microsoft Azure Storage](../../storage/common/storage-introduction.md)
* [Přidání disku do virtuálního počítače s Linuxem pomocí Azure CLI](add-disk.md)
* [Postup připojení datového disku k virtuálnímu počítači s Linuxem na webu Azure Portal](attach-disk-portal.md)

## <a name="networking"></a>Sítě
* [Přehled Virtual Network](../../virtual-network/virtual-networks-overview.md)
* [IP adresy v Azure](../../virtual-network/public-ip-addresses.md)
* [Otevření portů k virtuálnímu počítači s Linuxem v Azure](nsg-quickstart.md)
* [Vytvoření plně kvalifikovaného názvu domény (FQDN) na webu Azure Portal](../create-fqdn.md)


## <a name="data-residency"></a>Rezidence dat

Funkce pro ukládání zákaznických dat v rámci Azure je v tuto chvíli dostupná jenom v oblasti jihovýchodní Asie (Singapur) oblasti Asie a Tichomoří geografická a Brazílie – jih (stát Svatý Paulo) Brazílie geograficky. Pro všechny ostatní oblasti jsou zákaznická data uložená v geograficky. Další informace najdete v tématu [Centrum zabezpečení](https://azure.microsoft.com/global-infrastructure/data-residency/).


## <a name="next-steps"></a>Další kroky

Vytvořte svůj první virtuální počítač!

- [Azure Portal](quick-create-portal.md)
- [Azure CLI](quick-create-cli.md)
- [PowerShell](quick-create-powershell.md)
