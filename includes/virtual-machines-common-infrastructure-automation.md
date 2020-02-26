---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 9cbc48d8bca2f7491d0464be1c5bd64054927dc9
ms.sourcegitcommit: 0cc25b792ad6ec7a056ac3470f377edad804997a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/25/2020
ms.locfileid: "77608736"
---
Vytvoření a správa virtuálních počítačů Azure (VM) konzistentním způsobem v měřítku, uvítáte nějakou formu automatizace. Existuje mnoho nástrojů a řešení, která umožňují automatizovat nasazení kompletní infrastrukturu Azure a životního cyklu správy. Tento článek představuje některé z infrastruktury automatizační nástroje, které můžete použít v Azure. Tyto nástroje běžně nevešla do jedné z následujících postupů:

- Automatizace konfigurace virtuálních počítačů
    - Mezi tyto nástroje patří [Ansible](#ansible), [Puppet](#puppet) [a.](#chef)
    - Mezi nástroje specifické pro přizpůsobení virtuálních počítačů patří [Cloud-init](#cloud-init) pro virtuální počítače se systémem Linux, [Konfigurace požadovaného stavu (DSC) prostředí PowerShell](#powershell-dsc)a [rozšíření vlastních skriptů Azure](#azure-custom-script-extension) pro všechny virtuální počítače Azure.
 
- Automatizovat správu infrastruktury
    - Mezi nástroje patří [balíček](#packer) pro automatizaci vlastních sestavení imagí virtuálních počítačů a [terraformu](#terraform) k automatizaci procesu sestavení infrastruktury.
    - [Azure Automation](#azure-automation) může provádět akce napříč vaší místní infrastrukturou Azure a místní infrastrukturou.

- Automatizace nasazení aplikací a doručování
    - Příklady zahrnují [Azure DevOps Services](#azure-devops-services) a [Jenkinse](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) je automatizační modul pro správu konfigurace, vytváření virtuálních počítačů nebo nasazení aplikací. Ansible používá model bez agentů, obvykle s klíči SSH k ověřování a správě cílového počítače. Úlohy konfigurace jsou definovány v playbooky, s celou řadou Ansible modulů dostupných provádět konkrétní úlohy. Další informace najdete v tématu [jak Ansible funguje](https://www.ansible.com/how-ansible-works).

Naučte se:

- [Nainstalujte a nakonfigurujte Ansible pro Linux pro použití s Azure](../articles/ansible/ansible-install-configure.md).
- [Vytvořte virtuální počítač se systémem Linux](../articles/ansible/ansible-create-vm.md).
- [Správa virtuálního počítače se systémem Linux](../articles/ansible/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
Aplikace pro vzdálenou [správu je platforma](https://www.chef.io/) pro automatizaci, která pomáhá definovat způsob konfigurace, nasazení a správy vaší infrastruktury. Další součásti zahrnuty Chef, Habitat automatizace životního cyklu aplikací místo infrastruktury a Chef InSpec, která pomáhá automatizovat soulad s požadavky na zásady a zabezpečení. Chef klienti jsou instalováni na cílové počítače, jeden nebo více centrální Chef se servery s ukládat a spravovat konfigurace. Další informace najdete v tématu [Přehled nástroje pro vydaný objekt](https://docs.chef.io/chef_overview.html).

Naučte se:

- [Nasaďte z Azure Marketplace automatizaci](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Nainstalujte si ho do Windows a vytvořte virtuální počítače Azure](../articles/chef/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) je podniková platforma pro automatizaci, která zpracovává proces doručování a nasazování aplikací. Na cílových počítačích povolit Puppet hlavní ke spuštění manifestů, které definují požadovanou konfiguraci infrastruktury Azure a virtuální počítače jsou nainstalovaní agenti. Puppet můžete integrovat s jinými řešeními, jako je Jenkins a Githubu pro pracovní postup vylepšení devops. Další informace najdete v tématu [jak Puppet funguje](https://puppet.com/products/how-puppet-works).

Naučte se:

- [Nasaďte Puppet z Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Puppet.puppet-agent-windows-asm?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Protože cloud-init je volána v průběhu procesu prvotního spuštění, nejsou žádné další kroky ani agenty vyžaduje použití vaší konfigurace.  Další informace o tom, jak správně zformátovat soubory `#cloud-config`, najdete v tématu [dokumentace k nástroji Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  soubory `#cloud-config` jsou textové soubory kódované v kódování Base64.

Cloud-init navíc funguje v různých distribucích. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky použije nativní nástroj pro správu balíčků pro zvolenou distribuci.

Aktivně Pracujeme s našimi partnery doporučené distribuce Linuxu aby cloud-init povolené imagí dostupných v Tržišti Azure marketplace. Tyto Image zajišťovat vaše nasazení cloud-init a konfigurace bez problému fungují s virtuálními počítači a škálovacími sadami virtuálních počítačů. Přečtěte si další informace o cloud-init na Azure:

- [Podpora Cloud-init pro virtuální počítače se systémem Linux v Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Vyzkoušejte si kurz týkající se automatizované konfigurace virtuálních počítačů pomocí Cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[Konfigurace požadovaného stavu PowerShellu (DSC)](/powershell/scripting/dsc/overview/overview) je platforma pro správu, která definuje konfiguraci cílových počítačů. DSC se dá použít taky na platformě Linux prostřednictvím [serveru OMI (Open Management Infrastructure)](https://collaboration.opengroup.org/omi/).

Konfigurace DSC definovat, co se má nainstalovat na počítač a jak nakonfigurovat hostitele. Modul místního Configuration Manageru (LCM) běží na každý cílový uzel, který zpracovává nabízená konfigurace požadované akce. Serveru vyžádané replikace je webová služba, která běží na střední hostitele k uložení konfigurace DSC a související prostředky. Serveru vyžádané replikace komunikuje s modulem LCM na každém hostiteli target k poskytování požadované konfigurace a vytvoření sestavy dodržování předpisů.

Naučte se:

- [Vytvořte základní konfiguraci DSC](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Nakonfigurujte server vyžádané replikace DSC](/powershell/scripting/dsc/pull-server/pullserver).
- [Použijte DSC pro Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Rozšíření vlastních skriptů Azure
Rozšíření vlastních skriptů Azure pro [Linux](../articles/virtual-machines/linux/extensions-customscript.md) nebo [Windows](../articles/virtual-machines/windows/extensions-customscript.md) stahuje a spouští skripty na virtuálních počítačích Azure. Rozšíření můžete použít při vytváření virtuálního počítače, nebo kdykoli po virtuálního počítače se používá. 

Skripty můžete stáhnout z úložiště Azure nebo jakékoli veřejné umístění, jako je například úložiště GitHub. Pomocí rozšíření vlastních skriptů můžete psát skripty v jakémkoli jazyce, který běží na zdrojovém virtuálním počítači. Tyto skripty můžete použít k instalaci aplikace nebo podle potřeby nakonfigurujte virtuální počítač. K zabezpečení přihlašovacích údajů, mohou být uloženy citlivé informace, jako jsou hesla v chráněné konfigurace. Tyto přihlašovací údaje se dešifrují jenom v tomto virtuálním počítači.

Naučte se:

- [Vytvořte virtuální počítač se systémem Linux pomocí Azure CLI a použijte rozšíření vlastních skriptů](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Vytvořte virtuální počítač s Windows pomocí Azure PowerShell a použijte rozšíření vlastních skriptů](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packeru
[Balírna](https://www.packer.io) automatizuje proces sestavení při vytvoření vlastní image virtuálního počítače v Azure. Pomocí Packeru můžete definovat operačního systému a spustit skripty po konfiguraci, které přizpůsobení virtuálního počítače pro vaše konkrétní potřeby. Po nakonfigurování virtuálního počítače pak zaznamená v podobě image spravovaného disku. Packeru automatizuje proces vytvoření zdroje virtuálního počítače, sítě a prostředky úložiště, spouštět skripty konfigurace a pak vytvořte image virtuálního počítače.

Naučte se:

- [K vytvoření image virtuálního počítače se systémem Linux v Azure použijte balíček](../articles/virtual-machines/linux/build-image-with-packer.md).
- [K vytvoření image virtuálního počítače s Windows v Azure použijte balíček](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraformu](https://www.terraform.io) je Automatizační nástroj, který umožňuje definovat a vytvořit celou infrastrukturu Azure s jazykem formátu s jedním šablonou – HashiCorp Configuration Language (HCL). S využitím Terraformu definujete šablony, které automatizují proces vytvoření sítě, úložiště a prostředky virtuálních počítačů pro danou aplikaci řešení. Stávající šablony Terraformu pro jiné platformy Azure můžete použít k zajištění konzistence a zjednodušit nasazení infrastruktury, aniž by bylo nutné převést na šablonu Azure Resource Manageru.

Naučte se:

- [Nainstalujte a nakonfigurujte terraformu pomocí Azure](../articles/terraform/terraform-install-configure.md).
- [Vytvořte infrastrukturu Azure pomocí terraformu](../articles/terraform/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Automatizace Azure
[Azure Automation](https://azure.microsoft.com/services/automation/) používá Runbooky ke zpracování sady úloh na virtuálních počítačích, na které cílíte. Azure Automation se používá ke správě stávající virtuální počítače, a nikoli k vytvoření infrastruktury. Azure Automation můžete provozovat i Linuxu a virtuální počítače s Windows, jakož i v místním virtuálních nebo fyzických počítačů pomocí funkce hybrid runbook worker. Sady Runbook mohou být uloženy v úložištěm správy zdrojového kódu, jako je například GitHub. Tyto sady runbook pak můžete spustit ručně nebo podle předem definovaného plánu.

Azure Automation poskytuje také služby Desired State Configuration (DSC), které vám umožní vytvořit definice pro konfiguraci danou sadu virtuálních počítačů. DSC pak zajistí, že se použije požadované konfigurace a virtuální počítač zůstane konzistentní. Azure Automation DSC běží na počítačích s Windows i Linux.

Naučte se:

- [Vytvořte powershellový Runbook](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Pomocí Hybrid Runbook Worker můžete spravovat místní prostředky](../articles/automation/automation-hybrid-runbook-worker.md).
- [Použijte Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Služby Azure DevOps
[Azure DevOps Services](https://www.visualstudio.com/team-services/) je sada nástrojů, které vám pomůžou sdílet a sledovat kód, používat automatizovaná sestavení a vytvářet kompletní kanál průběžné integrace a vývoje (CI/CD). Služby Azure DevOps se integruje se sadou Visual Studio a jiných editorech zjednodušit využití. Služby Azure DevOps můžete také vytvořit a konfigurovat virtuální počítače Azure a pak nasaďte kód na ně.

Další informace pro:

- [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkinse](https://www.jenkins.io) je nepřetržitý integrační server, který pomáhá nasazovat a testovat aplikace a vytvářet automatizované kanály pro doručování kódu. Existují stovky moduly plug-in k rozšíření na základní platformě Jenkins a můžete také integrovat s mnoha jiných produktů a řešení prostřednictvím webhooků. Můžete ručně instalace Jenkinse na Virtuálním počítači Azure, spustit Jenkinse z v rámci kontejneru Dockeru nebo použijte předem sestavené image Azure Marketplace.

Naučte se:

- [Vytvořte vývojovou infrastrukturu na virtuálním počítači Linux v Azure pomocí Jenkinse, GitHubu a Docker](../articles/jenkins/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Další kroky
Existuje mnoho různých možností pro použití nástroje pro automatizaci infrastruktury v Azure. Budete moci svobodně řešení, které nejlépe vyhovuje potřebám a prostředí. Chcete-li začít a vyzkoušet některé z vestavěných nástrojů do Azure, přečtěte si téma Postup automatizace vlastního nastavení virtuálního počítače se systémem [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) nebo [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) .
