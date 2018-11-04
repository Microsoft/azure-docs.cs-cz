---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 10/26/2018
ms.author: cynthn
ms.openlocfilehash: 28c943b2588270af823c0712d4e510a4f018da3e
ms.sourcegitcommit: 1fc949dab883453ac960e02d882e613806fabe6f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/03/2018
ms.locfileid: "50981400"
---
# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Použití nástrojů automatizace infrastruktury s virtuálními počítači v Azure
Vytvoření a správa virtuálních počítačů Azure (VM) konzistentním způsobem v měřítku, uvítáte nějakou formu automatizace. Existuje mnoho nástrojů a řešení, která umožňují automatizovat nasazení kompletní infrastrukturu Azure a životního cyklu správy. Tento článek představuje některé z infrastruktury automatizační nástroje, které můžete použít v Azure. Tyto nástroje běžně nevešla do jedné z následujících postupů:

- Automatizace konfigurace virtuálních počítačů
    - Nástroje zahrnují [Ansible](#ansible), [Chef](#chef), a [Puppet](#puppet).
    - Nástroje, které jsou specifické pro přizpůsobení virtuálního počítače zahrnují [cloud-init](#cloud-init) pro virtuální počítače s Linuxem, [PowerShell Desired State Configuration (DSC)](#powershell-dsc)a [rozšíření vlastních skriptů Azure](#azure-custom-script-extension) pro všechny Virtuální počítače Azure.
 
- Automatizovat správu infrastruktury
    - Nástroje zahrnují [Packeru](#packer) k automatizaci vlastního virtuálního počítače vytvoří image, a [Terraformu](#terraform) automatizace infrastruktury procesu sestavení.
    - [Azure Automation](#azure-automation) může provádět akce v Azure a místní infrastruktuře.

- Automatizace nasazení aplikací a doručování
    - Mezi příklady patří [Azure DevOps služby](#visual-studio-team-services) a [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) je modul služby automation pro správu konfigurace, vytvoření virtuálního počítače nebo nasazení aplikace. Ansible používá model bez agentů, obvykle s klíči SSH k ověřování a správě cílového počítače. Úlohy konfigurace jsou definovány v playbooky, s celou řadou Ansible modulů dostupných provádět konkrétní úlohy. Další informace najdete v tématu [funguje jak Ansible](https://www.ansible.com/how-ansible-works).

Naučte se:

- [Instalace a konfigurace Ansible v Linuxu pro použití s Azurem](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Vytvoření virtuálního počítače s Linuxem](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Správa virtuálního počítače s Linuxem](../articles/virtual-machines/linux/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) je platformu automatizace, která pomáhá definovat způsob konfigurace vaší infrastruktury, nasazují a spravují. Další součásti zahrnuty Chef, Habitat automatizace životního cyklu aplikací místo infrastruktury a Chef InSpec, která pomáhá automatizovat soulad s požadavky na zásady a zabezpečení. Chef klienti jsou instalováni na cílové počítače, jeden nebo více centrální Chef se servery s ukládat a spravovat konfigurace. Další informace najdete v tématu [přehled Chef](https://docs.chef.io/chef_overview.html).

Naučte se:

- [Nasazení Chef automatizovat na webu Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Nainstalovat Windows Chef a vytvořit virtuální počítače Azure](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) platformu automatizace připraveno pro podniky, která zpracovává proces dodávek a nasazení aplikace. Na cílových počítačích povolit Puppet hlavní ke spuštění manifestů, které definují požadovanou konfiguraci infrastruktury Azure a virtuální počítače jsou nainstalovaní agenti. Puppet můžete integrovat s jinými řešeními, jako je Jenkins a Githubu pro pracovní postup vylepšení devops. Další informace najdete v tématu [funguje jak Puppet](https://puppet.com/product/how-puppet-works).

Naučte se:

- [Nasazení na webu Azure Marketplace Puppet](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2017-2?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Protože cloud-init je volána v průběhu procesu prvotního spuštění, nejsou žádné další kroky ani agenty vyžaduje použití vaší konfigurace.  Další informace o tom, jak správně vaše `#cloud-config` soubory, najdete v článku [cloud-init dokumentačním webu](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` soubory jsou textové soubory kódovaný jako base64.

Cloud-init navíc funguje v různých distribucích. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky použije nativní nástroj pro správu balíčků pro zvolenou distribuci.

 Aktivně Pracujeme s našimi partnery doporučené distribuce Linuxu aby cloud-init povolené imagí dostupných v Tržišti Azure marketplace. Tyto Image zajišťovat vaše nasazení cloud-init a konfigurace bez problému fungují s virtuálními počítači a škálovacími sadami virtuálních počítačů. Následující tabulka popisuje aktuální dostupnost Image povolené cloud-init na platformě Azure:

| Vydavatel | Nabídka | Skladová jednotka (SKU) | Verze | Připraveno na cloud-init
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-LTS |nejnovější |ano | 
|Canonical |UbuntuServer |14.04.5-LTS |nejnovější |ano |
|CoreOS |CoreOS |Stable |nejnovější |ano |
|OpenLogic |CentOS |7-CI |nejnovější |preview |
|RedHat |RHEL |7-RAW-CI |nejnovější |preview |

Přečtěte si další informace o cloud-init na Azure:

- [Pro virtuální počítače s Linuxem v Azure podporují cloud-init](../articles/virtual-machines/linux/using-cloud-init.md)
- [Vyzkoušejte kurz na automatizovanou konfiguraci virtuálního počítače pomocí cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[Prostředí PowerShell Desired State Configuration (DSC)](https://msdn.microsoft.com/powershell/dsc/overview) je platforma pro správu k definování konfigurace cílových počítačích. DSC, je také možné v Linuxu prostřednictvím [Open Management Infrastructure (OMI) server](https://collaboration.opengroup.org/omi/).

Konfigurace DSC definovat, co se má nainstalovat na počítač a jak nakonfigurovat hostitele. Modul místního Configuration Manageru (LCM) běží na každý cílový uzel, který zpracovává nabízená konfigurace požadované akce. Serveru vyžádané replikace je webová služba, která běží na střední hostitele k uložení konfigurace DSC a související prostředky. Serveru vyžádané replikace komunikuje s modulem LCM na každém hostiteli target k poskytování požadované konfigurace a vytvoření sestavy dodržování předpisů.

Naučte se:

- [Vytvořte základní konfiguraci DSC](https://msdn.microsoft.com/powershell/dsc/quickstart).
- [Konfigurace serveru vyžádané replikace DSC](https://msdn.microsoft.com/powershell/dsc/pullserver).
- [Použití DSC pro Linux](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Rozšíření vlastních skriptů Azure
Rozšíření vlastních skriptů Azure pro [Linux](../articles/virtual-machines/linux/extensions-customscript.md) nebo [Windows](../articles/virtual-machines/windows/extensions-customscript.md) stahuje a spouští skripty na virtuálních počítačích Azure. Rozšíření můžete použít při vytváření virtuálního počítače, nebo kdykoli po virtuálního počítače se používá. 

Skripty můžete stáhnout z úložiště Azure nebo jakékoli veřejné umístění, jako je například úložiště GitHub. Pomocí rozšíření vlastních skriptů můžete psát skripty v jakémkoli jazyce, který běží na zdrojovém virtuálním počítači. Tyto skripty můžete použít k instalaci aplikace nebo podle potřeby nakonfigurujte virtuální počítač. K zabezpečení přihlašovacích údajů, mohou být uloženy citlivé informace, jako jsou hesla v chráněné konfigurace. Tyto přihlašovací údaje se dešifrují jenom v tomto virtuálním počítači.

Naučte se:

- [Vytvoření virtuálního počítače s Linuxem pomocí Azure CLI a pomocí rozšíření vlastních skriptů](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Vytvoření virtuálního počítače s Windows pomocí Azure Powershellu a pomocí rozšíření vlastních skriptů](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packeru
[Packeru](https://www.packer.io) automatizuje proces sestavení, když vytvoříte vlastní image virtuálního počítače v Azure. Pomocí Packeru můžete definovat operačního systému a spustit skripty po konfiguraci, které přizpůsobení virtuálního počítače pro vaše konkrétní potřeby. Po nakonfigurování virtuálního počítače pak zaznamená v podobě image spravovaného disku. Packeru automatizuje proces vytvoření zdroje virtuálního počítače, sítě a prostředky úložiště, spouštět skripty konfigurace a pak vytvořte image virtuálního počítače.

Naučte se:

- [Vytvoření image virtuálního počítače s Linuxem v Azure pomocí Packeru](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Vytvoření image virtuálního počítače Windows v Azure pomocí Packeru](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) je automatizační nástroj, který umožňuje definovat a vytvořit celou infrastrukturu Azure s jednou šablonou jazyk formát – HashiCorp konfigurace jazyka (HCL). S využitím Terraformu definujete šablony, které automatizují proces vytvoření sítě, úložiště a prostředky virtuálních počítačů pro danou aplikaci řešení. Stávající šablony Terraformu pro jiné platformy Azure můžete použít k zajištění konzistence a zjednodušit nasazení infrastruktury, aniž by bylo nutné převést na šablonu Azure Resource Manageru.

Naučte se:

- [Instalace a konfigurace Terraformu v Azure](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Vytvoření infrastruktury Azure s využitím Terraformu](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) používá sady runbook ke zpracování sadu úloh na virtuálních počítačích cílíte. Azure Automation se používá ke správě stávající virtuální počítače, a nikoli k vytvoření infrastruktury. Azure Automation můžete provozovat i Linuxu a virtuální počítače s Windows, jakož i v místním virtuálních nebo fyzických počítačů pomocí funkce hybrid runbook worker. Sady Runbook mohou být uloženy v úložištěm správy zdrojového kódu, jako je například GitHub. Tyto sady runbook pak můžete spustit ručně nebo podle předem definovaného plánu.

Azure Automation poskytuje také služby Desired State Configuration (DSC), které vám umožní vytvořit definice pro konfiguraci danou sadu virtuálních počítačů. DSC pak zajistí, že se použije požadované konfigurace a virtuální počítač zůstane konzistentní. Azure Automation DSC běží na počítačích s Windows i Linux.

Naučte se:

- [Vytvoření Powershellového runbooku](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Umožňuje spravovat místní prostředky Hybrid Runbook Worker](../articles/automation/automation-hybrid-runbook-worker.md).
- [Použití Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Služby Azure DevOps](https://www.visualstudio.com/team-services/) je sada nástrojů, které umožňují sdílení a sledování kódu, pomocí automatizovaného sestavení a vytvoření kompletní průběžnou integraci a vývoj (CI/CD) kanálu. Služby Azure DevOps se integruje se sadou Visual Studio a jiných editorech zjednodušit využití. Služby Azure DevOps můžete také vytvořit a konfigurovat virtuální počítače Azure a pak nasaďte kód na ně.

Další informace:

- [Služby Azure DevOps](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) je server pro průběžnou integraci, která pomáhá nasazení a testování aplikací a vytvářejte automatizované kanály pro doručování kódu. Existují stovky moduly plug-in k rozšíření na základní platformě Jenkins a můžete také integrovat s mnoha jiných produktů a řešení prostřednictvím webhooků. Můžete ručně instalace Jenkinse na Virtuálním počítači Azure, spustit Jenkinse z v rámci kontejneru Dockeru nebo použijte předem sestavené image Azure Marketplace.

Naučte se:

- [Vytváření vývojové infrastruktury na virtuální počítač s Linuxem v Azure pomocí Jenkinse, Githubu a Dockeru](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Další postup
Existuje mnoho různých možností pro použití nástroje pro automatizaci infrastruktury v Azure. Budete moci svobodně řešení, které nejlépe vyhovuje potřebám a prostředí. K zahájení práce a vyzkoušejte některou z nástroje integrované v Azure, zjistit, jak automatizovat přizpůsobení nástroje [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) nebo [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) virtuálního počítače.
