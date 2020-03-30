---
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 04/11/2019
ms.author: cynthn
ms.openlocfilehash: 9cbc48d8bca2f7491d0464be1c5bd64054927dc9
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "77608736"
---
Chcete-li vytvořit a spravovat virtuální počítače Azure (VMs) konzistentním způsobem ve velkém měřítku, některé formy automatizace je obvykle žádoucí. Existuje mnoho nástrojů a řešení, které vám umožní automatizovat kompletní životní cyklus nasazení a správy infrastruktury Azure. Tento článek představuje některé nástroje pro automatizaci infrastruktury, které můžete použít v Azure. Tyto nástroje se běžně vejdou do jednoho z následujících přístupů:

- Automatizace konfigurace virtuálních počítačů
    - Mezi nástroje patří [Ansible](#ansible), [Chef](#chef)a [Puppet](#puppet).
    - Mezi nástroje specifické pro přizpůsobení virtuálních počítačů patří [cloud-init](#cloud-init) pro virtuální počítače s [Linuxem, konfigurace požadovaného stavu PowerShellu (DSC)](#powershell-dsc)a [rozšíření vlastního skriptu Azure](#azure-custom-script-extension) pro všechny virtuální počítače Azure.
 
- Automatizace správy infrastruktury
    - Mezi nástroje patří [Packer](#packer) pro automatizaci vlastních sestavení image virtuálních zařízení a [Terraform](#terraform) pro automatizaci procesu sestavení infrastruktury.
    - [Azure Automation](#azure-automation) můžou provádět akce napříč vaší Azure a místní infrastrukturou.

- Automatizace nasazení a doručení aplikací
    - Mezi příklady patří [Služby Azure DevOps](#azure-devops-services) services a [Jenkins](#jenkins).

## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) je automat automatizace pro správu konfigurace, vytváření virtuálních počítačů nebo nasazení aplikací. Ansible používá model bez agenta, obvykle s klíči SSH, k ověření a správě cílových počítačů. Konfigurační úlohy jsou definovány v playbookech s řadou modulů Ansible, které jsou k dispozici pro provádění konkrétních úkolů. Další informace naleznete v tématu [Jak ansible funguje](https://www.ansible.com/how-ansible-works).

Naučte se:

- [Nainstalujte a nakonfigurujte Ansible na Linuxu pro použití s Azure](../articles/ansible/ansible-install-configure.md).
- [Vytvořte virtuální počítač s Linuxem](../articles/ansible/ansible-create-vm.md).
- [Správa linuxového virtuálního počítače](../articles/ansible/ansible-manage-linux-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) je automatizační platforma, která pomáhá definovat, jak je vaše infrastruktura konfigurována, nasazena a spravována. Další komponenty zahrnovaly Chef Habitat pro automatizaci životního cyklu aplikací spíše než infrastrukturu a Chef InSpec, který pomáhá automatizovat dodržování požadavků na zabezpečení a zásady. Klienti chef jsou nainstalováni na cílových počítačích s jedním nebo více centrálními servery chef, které ukládají a spravují konfigurace. Další informace naleznete [v tématu Přehled chef](https://docs.chef.io/chef_overview.html).

Naučte se:

- [Nasazení chef automate z Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Nainstalujte chefa do Windows a vytvořte virtuální počítače Azure](../articles/chef/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) je platforma pro automatizaci připravená pro podniky, která zpracovává proces doručování a nasazování aplikací. Agenti jsou nainstalované na cílových počítačích, aby loutkář mohl spouštět manifesty, které definují požadovanou konfiguraci infrastruktury Azure a virtuálních počítačů. Loutka můžete integrovat s dalšími řešeními, jako je Jenkins a GitHub pro lepší devops workflow. Další informace naleznete v tématu [Jak funguje loutka](https://puppet.com/products/how-puppet-works).

Naučte se:

- [Nasazení loutky z Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/Puppet.puppet-agent-windows-asm?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že cloud-init je volána během počátečního procesu spouštění, neexistují žádné další kroky nebo požadované agenty použít konfiguraci.  Další informace o správném formátování `#cloud-config` souborů naleznete na [webu dokumentace cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`soubory jsou textové soubory kódované v base64.

Cloud-init navíc funguje v různých distribucích. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky použije nativní nástroj pro správu balíčků pro zvolenou distribuci.

Aktivně spolupracujeme s našimi schválenými partnery pro distribuci Linuxu, abychom měli na webu Azure Marketplace k dispozici image s podporou cloudu. Díky těmto bitovým kopiím budou nasazení a konfigurace v cloudu bezproblémově fungovat s virtuálními počítači a škálovacími sadami virtuálních počítačů. Další informace o cloud-initu v Azure:

- [Podpora cloud-init pro virtuální počítače s Linuxem v Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Vyzkoušejte kurz o automatické konfiguraci virtuálních počítačů pomocí cloud-init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[Konfigurace požadovaného stavu prostředí PowerShell (DSC)](/powershell/scripting/dsc/overview/overview) je platforma pro správu, která definuje konfiguraci cílových počítačů. DSC lze také použít na Linuxu prostřednictvím [serveru Open Management Infrastructure (OMI).](https://collaboration.opengroup.org/omi/)

Konfigurace DSC definují, co se má do počítače nainstalovat a jak nakonfigurovat hostitele. Modul místního configuration manageru (LCM) běží na každém cílovém uzlu, který zpracovává požadované akce na základě nabízených konfigurací. Server vyžádat je webová služba, která běží na centrálním hostiteli pro ukládání konfigurací DSC a přidružených prostředků. Server pro vyžádat komunikuje s modulem LCM na každém cílovém hostiteli a poskytuje požadované konfigurace a zprávy o dodržování předpisů.

Naučte se:

- [Vytvořte základní konfiguraci DSC](/powershell/scripting/dsc/quickstarts/website-quickstart).
- [Konfigurace serveru pro vyžádat protokol DSC](/powershell/scripting/dsc/pull-server/pullserver).
- [Použijte DSC pro Linux](/powershell/scripting/dsc/getting-started/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Rozšíření Azure Custom Script
Azure Custom Script Extension pro [Linux](../articles/virtual-machines/linux/extensions-customscript.md) nebo [Windows](../articles/virtual-machines/windows/extensions-customscript.md) stahuje a spouští skripty na virtuálních počítačích Azure. Rozšíření můžete použít při vytváření virtuálního virtuálního soudu nebo kdykoli po použití virtuálního soudu. 

Skripty se můžou stahovat z úložiště Azure nebo z jakéhokoli veřejného umístění, jako je úložiště GitHub. S rozšířením Vlastní skript můžete psát skripty v libovolném jazyce, který běží na zdrojovém virtuálním počítači. Tyto skripty lze použít k instalaci aplikací nebo nakonfigurovat virtuální počítače podle potřeby. Chcete-li zabezpečit pověření, citlivé informace, jako jsou hesla mohou být uloženy v chráněné konfiguraci. Tato pověření jsou dešifrovány pouze uvnitř virtuálního soudu.

Naučte se:

- [Vytvořte virtuální počítač s Linuxem pomocí azure cli a použijte rozšíření vlastní skript](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Vytvořte virtuální počítač s Windows pomocí Azure PowerShellu a použijte rozšíření Custom Script Extension](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Packer
[Packer](https://www.packer.io) automatizuje proces sestavení při vytváření vlastní image virtuálního počítače v Azure. Pomocí packeru můžete definovat operační systém a spouštět skripty po konfiguraci, které přizpůsobují virtuální počítač vašim konkrétním potřebám. Po nakonfigurování se virtuální modul zachytí jako bitovou kopii spravovaného disku. Packer automatizuje proces k vytvoření zdrojového virtuálního počítače, síťových a úložných prostředků, spuštění konfiguračních skriptů a následném vytvoření image virtuálního počítače.

Naučte se:

- [Pomocí Packeru vytvořte image virtuálního počítače s Linuxem v Azure](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Pomocí Packeru vytvořte image virtuálního počítače s Windows v Azure](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) je automatizační nástroj, který umožňuje definovat a vytvořit celou infrastrukturu Azure s jazykem jednoho formátu šablony – jazykem konfigurace HashiCorp (HCL). Pomocí Terraform definujete šablony, které automatizují proces vytváření prostředků sítě, úložiště a virtuálních her pro dané aplikační řešení. Stávající šablony Terraform můžete použít pro jiné platformy s Azure, abyste zajistili konzistenci a zjednodušili nasazení infrastruktury, aniž byste museli převést na šablonu Azure Resource Manager.

Naučte se:

- [Nainstalujte a nakonfigurujte Terraform pomocí Azure](../articles/terraform/terraform-install-configure.md).
- [Vytvořte infrastrukturu Azure s Terraform](../articles/terraform/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Azure Automation](https://azure.microsoft.com/services/automation/) používá runbooky ke zpracování sady úloh na virtuálních počítačích, na které cílíte. Azure Automation se používá ke správě stávajících virtuálních počítačů, nikoli k vytvoření infrastruktury. Azure Automation může běžet napříč linuxovými i virtuálními počítači s Windows, stejně jako místní virtuální nebo fyzické počítače s hybridním pracovníkem runbooku. Sady Runbook lze uložit do úložiště správy zdrojového kódu, jako je github. Tyto sady Runbook pak lze spustit ručně nebo podle definovaného plánu.

Azure Automation také poskytuje službu konfigurace požadovaného stavu (DSC), která umožňuje vytvářet definice pro konfiguraci dané sady virtuálních počítačů. DSC pak zajišťuje, že je použita požadovaná konfigurace a virtuální ho dm zůstane konzistentní. Azure Automation DSC běží na počítačích s Windows i Linuxem.

Naučte se:

- [Vytvořte runbook prostředí PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Pomocí hybridního pracovníka runbooku můžete spravovat místní prostředky](../articles/automation/automation-hybrid-runbook-worker.md).
- [Použijte Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="azure-devops-services"></a>Azure DevOps Services
[Azure DevOps Services](https://www.visualstudio.com/team-services/) je sada nástrojů, které vám pomohou sdílet a sledovat kód, používat automatizovaná sestavení a vytvářet úplný kanál průběžné integrace a vývoje (CI/CD). Azure DevOps Services se integruje s Visual Studio a dalšími editory pro zjednodušení využití. Azure DevOps Services můžou taky vytvářet a konfigurovat virtuální počítače Azure a pak do nich nasazovat kód.

Další informace:

- [Azure DevOps Services](https://docs.microsoft.com/azure/devops/user-guide/index?view=vsts).


## <a name="jenkins"></a>Jenkins
[Jenkins](https://www.jenkins.io) je server průběžné integrace, který pomáhá nasazovat a testovat aplikace a vytvářet automatizované kanály pro doručování kódu. Existují stovky pluginů pro rozšíření základní platformy Jenkins a můžete také integrovat s mnoha dalšími produkty a řešeními prostřednictvím webhooků. Jenkinse můžete nainstalovat ručně na virtuální počítač Azure, spustit Jenkinse z kontejneru Dockeru nebo použít předem vytvořenou image Azure Marketplace.

Naučte se:

- [Vytvořte vývojovou infrastrukturu na virtuálním počítači SNI v Azure pomocí Jenkinse, GitHubu a Dockeru](../articles/jenkins/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Další kroky
Existuje mnoho různých možností použití nástrojů pro automatizaci infrastruktury v Azure. Máte svobodu používat řešení, které nejlépe vyhovuje vašim potřebám a prostředí. Pokud chcete začít a vyzkoušet některé z nástrojů integrovaných do Azure, podívejte se, jak automatizovat přizpůsobení [virtuálního počítače s Linuxem](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) nebo [Windows.](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md)
