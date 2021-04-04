---
title: Použití nástrojů pro automatizaci infrastruktury
description: Naučte se používat nástroje pro automatizaci infrastruktury, jako je Ansible, Puppet, Terraformu a balírna, k vytváření a správě virtuálních počítačů v Azure.
author: cynthn
ms.service: virtual-machines
ms.topic: conceptual
ms.workload: infrastructure
ms.date: 07/17/2020
ms.author: cynthn
ms.openlocfilehash: 7a702fbf405011a9df3e7e77936a9a37f0fdfad7
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98730634"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Použití nástrojů pro automatizaci infrastruktury s virtuálními počítači v Azure

Aby bylo možné vytvářet a spravovat virtuální počítače Azure konzistentním způsobem ve velkém měřítku, obvykle je žádoucí určitá forma automatizace. Existuje mnoho nástrojů a řešení, které vám umožňují automatizovat kompletní životní cyklus nasazení a správy infrastruktury Azure. V tomto článku se seznámíte s některými nástroji pro automatizaci infrastruktury, které můžete použít v Azure. Tyto nástroje se běžně vejdou do jednoho z následujících přístupů:

- Automatizace konfigurace virtuálních počítačů
    - Mezi tyto nástroje patří [](#chef)šablony [Ansible](#ansible), [Puppet](#puppet)a [Azure Resource Manager](#azure-resource-manager-template).
    - Mezi nástroje specifické pro přizpůsobení virtuálních počítačů patří [Cloud-init](#cloud-init) pro virtuální počítače se systémem Linux, [Konfigurace požadovaného stavu (DSC) prostředí PowerShell](#powershell-dsc)a [rozšíření vlastních skriptů Azure](#azure-custom-script-extension) pro všechny virtuální počítače Azure.

- Automatizace správy infrastruktury
    - Mezi nástroje patří [balíček](#packer) pro automatizaci vlastních sestavení imagí virtuálních počítačů a [terraformu](#terraform) k automatizaci procesu sestavení infrastruktury.
    - [Azure Automation](#azure-automation) může provádět akce napříč vaší místní infrastrukturou Azure a místní infrastrukturou.

- Automatizace nasazení a poskytování aplikací
    - Příklady zahrnují [Azure DevOps Services](#azure-devops-services) a [Jenkinse](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) je automatizační modul pro správu konfigurace, vytváření virtuálních počítačů nebo nasazení aplikací. Ansible používá model bez agentů, obvykle s klíči SSH, k ověřování a správě cílových počítačů. Úlohy konfigurace jsou definované v playbooky s několika moduly Ansible, které jsou k dispozici pro provádění konkrétních úkolů. Další informace najdete v tématu [jak Ansible funguje](https://www.ansible.com/how-ansible-works).

Naučte se:

- [Nainstalujte a nakonfigurujte Ansible pro Linux pro použití s Azure](/azure/developer/ansible/install-on-linux-vm).
- [Vytvořte virtuální počítač se systémem Linux](/azure/developer/ansible/vm-configure).
- [Správa virtuálního počítače se systémem Linux](/azure/developer/ansible/vm-manage).


## <a name="chef"></a>Chef
Aplikace pro vzdálenou [správu je platforma](https://www.chef.io/) pro automatizaci, která pomáhá definovat způsob konfigurace, nasazení a správy vaší infrastruktury. Další komponenty zahrnovaly pro automatizaci životního cyklu aplikací místo infrastruktury a nespecifika systému pro správu, která pomáhá automatizovat dodržování požadavků na zabezpečení a zásady. Klienti systému OS/2 jsou nainstalováni na cílových počítačích s jedním nebo více servery centrálního systému pro správu, které ukládají a spravují konfigurace. Další informace najdete v tématu [Přehled nástroje pro vydaný objekt](https://docs.chef.io/chef_overview.html).

Naučte se:

- [Nasaďte z Azure Marketplace automatizaci](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Nainstalujte si ho do Windows a vytvořte virtuální počítače Azure](/azure/developer/chef/windows-vm-configure).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) je podniková platforma pro automatizaci, která zpracovává proces doručování a nasazování aplikací. Agenti jsou nainstalováni na cílových počítačích, aby mohl hlavní server Puppet spouštět manifesty, které definují požadovanou konfiguraci infrastruktury a virtuálních počítačů Azure. Puppet je možné integrovat s jinými řešeními, jako je Jenkinse a GitHub, pro Vylepšený pracovní postup DevOps. Další informace najdete v tématu [jak Puppet funguje](https://puppet.com/products/how-puppet-works).

Naučte se:

- [Nasaďte Puppet](https://puppet.com/docs/puppet/5.5/install_windows.html).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že se během procesu prvotního spuštění volá Cloud-init, neexistují žádné další kroky ani nepotřebné agenti pro použití konfigurace.  Další informace o tom, jak správně zformátovat `#cloud-config` soubory, najdete v [webu dokumentace k inicializaci Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` soubory jsou textové soubory kódované v kódování Base64.

Cloud-init navíc funguje v různých distribucích. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky použije nativní nástroj pro správu balíčků pro zvolenou distribuci.

Aktivně spolupracujeme s našimi distribuce partnery pro Linux, aby byly k dispozici image s povoleným cloudovým inicializacm v Azure Marketplace. Díky těmto imagím vaše nasazení a konfigurace pro cloudovou inicializaci bez problémů fungují s virtuálními počítači a sadami škálování virtuálních počítačů.
Další podrobnosti o cloud-init v Azure:

- [Podpora Cloud-init pro virtuální počítače se systémem Linux v Azure](./linux/using-cloud-init.md)
- [Vyzkoušejte si kurz týkající se automatizované konfigurace virtuálních počítačů pomocí Cloud-init](./linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[Konfigurace požadovaného stavu PowerShellu (DSC)](/powershell/scripting/dsc/overview/overview) je platforma pro správu, která definuje konfiguraci cílových počítačů. DSC se dá použít taky na platformě Linux prostřednictvím [serveru OMI (Open Management Infrastructure)](https://collaboration.opengroup.org/omi/).

Konfigurace DSC definují, co se má nainstalovat na počítač a jak se má nakonfigurovat hostitel. Místní Configuration Manager modul (LCM) běží na každém cílovém uzlu, který zpracovává požadované akce na základě nabízených konfigurací. Server Pull je webová služba, která běží na centrálním hostiteli pro ukládání konfigurací DSC a přidružených prostředků. Server Pull komunikuje s modulem LCM na každém cílovém hostiteli, aby poskytoval požadované konfigurace a sestavu dodržování předpisů.

Naučte se:

- [Vytvořte základní konfiguraci DSC](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Nakonfigurujte server vyžádané replikace DSC](/powershell/scripting/dsc/pull-server/pullserver).
- [Použijte DSC pro Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Rozšíření Azure Custom Script
Rozšíření vlastních skriptů Azure pro [Linux](./extensions/custom-script-linux.md) nebo [Windows](./extensions/custom-script-windows.md) stahuje a spouští skripty na virtuálních počítačích Azure. Rozšíření můžete použít při vytváření virtuálního počítače nebo kdykoli po použití virtuálního počítače.

Skripty si můžete stáhnout z Azure Storage nebo z libovolného veřejného umístění, jako je například úložiště GitHub. Pomocí rozšíření vlastních skriptů můžete psát skripty v jakémkoli jazyce, který běží na zdrojovém virtuálním počítači. Tyto skripty lze použít k instalaci aplikací nebo konfiguraci virtuálního počítače podle potřeby. Pro zabezpečení přihlašovacích údajů se můžou citlivé informace, jako jsou hesla, ukládat v chráněné konfiguraci. Tyto přihlašovací údaje se dešifrují jenom uvnitř virtuálního počítače.

Naučte se:

- [Vytvořte virtuální počítač se systémem Linux pomocí Azure CLI a použijte rozšíření vlastních skriptů](/previous-versions/azure/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx?toc=%2fcli%2fazure%2ftoc.json).
- [Vytvořte virtuální počítač s Windows pomocí Azure PowerShell a použijte rozšíření vlastních skriptů](/previous-versions/azure/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis).


## <a name="packer"></a>Packeru
[Balírna](https://www.packer.io) automatizuje proces sestavení při vytvoření vlastní image virtuálního počítače v Azure. Pomocí nástroje Pack můžete definovat operační systém a spouštět skripty po konfiguraci, které přizpůsobí virtuální počítač konkrétním potřebám. Po nakonfigurování se virtuální počítač zachytí jako image spravovaného disku. Balírna automatizuje proces vytvoření zdrojového virtuálního počítače, sítě a prostředků úložiště, spuštění konfiguračních skriptů a vytvoření image virtuálního počítače.

Naučte se:

- [K vytvoření image virtuálního počítače se systémem Linux v Azure použijte balíček](./linux/build-image-with-packer.md).
- [K vytvoření image virtuálního počítače s Windows v Azure použijte balíček](./windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraformu](https://www.terraform.io) je Automatizační nástroj, který umožňuje definovat a vytvořit celou infrastrukturu Azure s jazykem formátu s jedním šablonou – HashiCorp Configuration Language (HCL). Pomocí Terraformu definujete šablony, které automatizují proces vytváření sítě, úložiště a prostředků virtuálních počítačů pro dané řešení aplikace. Stávající šablony Terraformu můžete použít pro jiné platformy s Azure, abyste zajistili konzistenci a zjednodušili nasazení infrastruktury bez nutnosti převádět na šablonu Azure Resource Manager.

Naučte se:

- [Nainstalujte a nakonfigurujte terraformu pomocí Azure](/azure/developer/terraform/getting-started-cloud-shell).
- [Vytvořte infrastrukturu Azure pomocí terraformu](/azure/developer/terraform/create-linux-virtual-machine-with-infrastructure).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) používá Runbooky ke zpracování sady úloh na virtuálních počítačích, na které cílíte. Azure Automation se používá ke správě stávajících virtuálních počítačů, nikoli k vytváření infrastruktury. Azure Automation může běžet v rámci virtuálních počítačů se systémem Linux i Windows i místních virtuálních nebo fyzických počítačů pomocí Hybrid Runbook Worker. Runbooky se dají ukládat do úložiště správy zdrojového kódu, jako je GitHub. Tyto Runbooky pak můžete spustit ručně nebo podle definovaného plánu.

Azure Automation taky poskytuje požadovanou službu Konfigurace stavu (DSC), která vám umožní vytvořit definice pro způsob konfigurace dané sady virtuálních počítačů. DSC pak zajistí, že se požadovaná konfigurace použije a virtuální počítač zůstane konzistentní. Azure Automation DSC běží na počítačích s Windows i Linux.

Naučte se:

- [Vytvořte powershellový Runbook](../automation/learn/automation-tutorial-runbook-textual-powershell.md).
- [Pomocí Hybrid Runbook Worker můžete spravovat místní prostředky](../automation/automation-hybrid-runbook-worker.md).
- [Použijte Azure Automation DSC](../automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/) je sada nástrojů, které vám pomůžou sdílet a sledovat kód, používat automatizovaná sestavení a vytvářet kompletní kanál průběžné integrace a vývoje (CI/CD). Azure DevOps Services se integruje se sadou Visual Studio a dalšími editory, aby bylo možné zjednodušit využití. Azure DevOps Services taky můžou vytvářet a konfigurovat virtuální počítače Azure a potom do nich nasazovat kód.

Přečtěte si další informace:

- [Azure DevOps Services](/azure/devops/user-guide/index).


## <a name="jenkins"></a>Jenkins
[Jenkinse](https://jenkins.io) je nepřetržitý integrační server, který pomáhá nasazovat a testovat aplikace a vytvářet automatizované kanály pro doručování kódu. Existují stovky modulů plug-in pro rozšiřování základní platformy Jenkinse a můžete je také integrovat s mnoha dalšími produkty a řešeními prostřednictvím webhooků. Jenkinse můžete ručně nainstalovat do virtuálního počítače Azure, spustit Jenkinse z kontejneru Docker nebo použít předem sestavenou bitovou kopii Azure Marketplace.

Naučte se:

- [Vytvořte vývojovou infrastrukturu na virtuálním počítači Linux v Azure pomocí Jenkinse, GitHubu a Docker](/azure/developer/jenkins/pipeline-with-github-and-docker).


## <a name="azure-resource-manager-template"></a>Šablona Azure Resource Manageru
[Azure Resource Manager](../azure-resource-manager/templates/overview.md) je služba pro nasazení a správu pro Azure. Poskytuje vrstvu pro správu, která umožňuje vytvářet, aktualizovat a odstraňovat prostředky v předplatném Azure. Pomocí funkcí správy, jako jsou řízení přístupu, zámky a značky, můžete zabezpečit a organizovat prostředky po nasazení.

Naučte se:

- [Nasaďte virtuální počítače s využitím šablony Správce prostředků](./linux/spot-template.md).
- [Vytvoří virtuální počítač s Windows ze šablony Správce prostředků](./windows/ps-template.md).
- [Stáhněte si šablonu pro virtuální počítač](/previous-versions/azure/virtual-machines/windows/download-template).
- [Vytvoří šablonu Azure image Builder](./linux/image-builder-json.md).

## <a name="next-steps"></a>Další kroky
K dispozici je celá řada různých možností použití nástrojů pro automatizaci infrastruktury v Azure. Máte volnost v používání řešení, které nejlépe vyhovuje vašim potřebám a prostředí. Chcete-li začít a vyzkoušet některé z vestavěných nástrojů do Azure, přečtěte si téma Postup automatizace vlastního nastavení virtuálního počítače se systémem [Linux](./linux/tutorial-automate-vm-deployment.md) nebo [Windows](./windows/tutorial-automate-vm-deployment.md) .
