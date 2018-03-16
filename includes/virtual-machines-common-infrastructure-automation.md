# <a name="use-infrastructure-automation-tools-with-virtual-machines-in-azure"></a>Použití infrastruktury automatizace nástroje s virtuálními počítači v Azure
Chcete-li vytvořit a spravovat virtuální počítače Azure (VM) konzistentním způsobem v měřítku, je obvykle potřeby nějaký způsob automatizace. Existuje mnoho nástroje a řešení, které umožňují automatizovat nasazení kompletní infrastrukturu Azure a správu životního cyklu. Tento článek představuje některé nástroje automatizace infrastruktury, které můžete použít v Azure. Tyto nástroje běžně nevešla do jedné z následujících postupů:

- Automatizovat konfiguraci virtuálních počítačů.
    - Nástroje zahrnují [Ansible](#ansible), [Chef](#chef), a [Puppet](#puppet).
    - Nástroje, které jsou specifické pro přizpůsobení virtuálního počítače patří [cloudu init](#cloud-init) pro virtuální počítače s Linuxem, [prostředí PowerShell požadovaného stavu konfigurace (DSC)](#powershell-dsc)a [rozšíření vlastních skriptů Azure](#azure-custom-script-extension) pro všechny Virtuální počítače Azure.
 
- Automatizovat správu infrastruktury
    - Nástroje zahrnují [balírna](#packer) pro automatizaci vlastní virtuální sestavení bitové kopie, a [Terraform](#terraform) automatizovat proces sestavení infrastrukturu.
    - [Služby Azure Automation](#azure-automation) lze provádět akce v rámci vaší infrastruktury Azure a místní.

- Automatizovat nasazení aplikace a doručení
    - Mezi příklady patří [Visual Studio Team Services](#visual-studio-team-services) a [volaných](#jenkins).


## <a name="ansible"></a>Ansible
[Ansible](https://www.ansible.com/) je modul automatizace pro správu konfigurace, vytvoření virtuálního počítače nebo nasazení aplikace. Ansible používá model bez agenta, obvykle pomocí klíče SSH k ověření a správu cílových počítačů. Úlohy konfigurace jsou definovány v playbooks s počtem Ansible moduly k provádění specifických úloh k dispozici. Další informace najdete v tématu [funguje jak Ansible](https://www.ansible.com/how-ansible-works).

Naučte se:

- [Instalace a konfigurace Ansible v systému Linux pro použití s Azure](../articles/virtual-machines/linux/ansible-install-configure.md).
- [Vytvoření virtuálního počítače základní](../articles/virtual-machines/linux/ansible-create-vm.md).
- [Vytvořte prostředí dokončení virtuálních počítačů, včetně podpora prostředky](../articles/virtual-machines/linux/ansible-create-complete-vm.md).


## <a name="chef"></a>Chef
[Chef](https://www.chef.io/) je platformu automatizace, která pomáhá definovat konfiguraci infrastruktury, nasazují a spravují. Další součásti obsaženy Chef prostředí pro automatizace životního cyklu aplikací, nikoli infrastrukturu a Chef InSpec která pomáhá automatizovat splnění požadavků zásad a zabezpečení. Chef klienti jsou instalováni na cílových počítačích s jeden nebo více centrální Chef serverů, které ukládají a spravovat konfigurace. Další informace najdete v tématu [přehled Chef](https://docs.chef.io/chef_overview.html).

Naučte se:

- [Nasazení Chef automatizovat z Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/chef-software.chef-automate?tab=Overview).
- [Nainstalujte Chef v systému Windows a vytvořte virtuální počítače Azure](../articles/virtual-machines/windows/chef-automation.md).


## <a name="puppet"></a>Puppet
[Puppet](https://www.puppet.com) je připravené pro podniky automatizace platforma, která zpracovává proces doručení a nasazení aplikace. Jsou agenti nainstalovaní na cílových počítačích povolit hlavní Puppet ke spuštění manifesty, které definují požadovanou konfiguraci infrastruktury Azure a virtuálních počítačů. Puppet můžete integrovat s jinými řešeními například volaných a GitHub pro pracovní postup lepší devops. Další informace najdete v tématu [funguje jak Puppet](https://puppet.com/product/how-puppet-works).

Naučte se:

- [Nasazení Puppet z Azure Marketplace](https://azuremarketplace.microsoft.com/marketplace/apps/puppet.puppet-enterprise-2016-1?tab=Overview).


## <a name="cloud-init"></a>Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Protože init cloudu je volána v průběhu procesu počáteční spouštění, nejsou žádné další kroky nebo požadované agenty použít konfiguraci.  Další informace o tom, jak správně formátu vaše `#cloud-config` soubory, najdete v článku [web dokumentace cloudu init](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` soubory jsou textové soubory kódovaný jako base64.

Cloud-init navíc funguje v různých distribucích. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky použije nativní nástroj pro správu balíčků pro zvolenou distribuci.

 Aktivně Pracujeme s našimi potvrzená distro partnery Linux aby bylo možné používat cloudové inicializací povoleno imagím v Azure marketplace. Tyto Image proveďte init cloudové nasazení a konfigurace bezproblémově pracovat s virtuálními počítači a sady škálování virtuálního počítače. Následující tabulka popisuje aktuální dostupnosti cloudu inicializací povoleno bitové kopie na platformě Azure:

| Vydavatel | Nabídka | Skladová jednotka (SKU) | Verze | init cloudu připravené
|:--- |:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |16.04-LTS |nejnovější |ano | 
|Canonical |UbuntuServer |14.04.5-LTS |nejnovější |ano |
|CoreOS |CoreOS |Stable |nejnovější |ano |
|OpenLogic |CentOS |7-CI |nejnovější |náhled |
|RedHat |RHEL |7-RAW-CI |nejnovější |náhled |

Další podrobnosti o cloudu init v Azure:

- [Init cloudu – podpora pro virtuální počítače s Linuxem v Azure](../articles/virtual-machines/linux/using-cloud-init.md)
- [Vyzkoušejte si kurz na automatické konfigurace virtuálního počítače pomocí cloudu init](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md).


## <a name="powershell-dsc"></a>PowerShell DSC
[Prostředí PowerShell požadovaného stavu konfigurace (DSC)](https://msdn.microsoft.com/en-us/powershell/dsc/overview) je platforma pro správu k definování konfigurace cílových počítačů. DSC lze také v systému Linux pomocí [serveru infrastruktury OMI (Open Management Infrastructure)](https://collaboration.opengroup.org/omi/).

Konfigurace DSC definovat, co je potřeba nainstalovat na počítači a postup konfigurace hostitele. Modul Místní Configuration Manager (LCM) běží na každý cílový uzel, který zpracovává požadovaná akce na základě stisknutí konfigurací. Server vyžádání obsahu je webová služba, která běží na hostiteli centrální k uložení konfigurace DSC a přidružených prostředků. Načítacího serveru komunikuje s modulem LCM na každém cílovém hostiteli a zadejte požadované konfigurace a tvorba sestav o dodržování předpisů.

Naučte se:

- [Vytvořte základní konfigurace DSC](https://msdn.microsoft.com/powershell/dsc/quickstart).
- [Nakonfigurujte server DSC za](https://msdn.microsoft.com/powershell/dsc/pullserver).
- [Pro Linux pomocí DSC](https://msdn.microsoft.com/powershell/dsc/lnxgettingstarted).


## <a name="azure-custom-script-extension"></a>Rozšíření Azure vlastních skriptů
Rozšíření vlastních skriptů Azure pro [Linux](../articles/virtual-machines/linux/extensions-customscript.md) nebo [Windows](../articles/virtual-machines/windows/extensions-customscript.md) stahuje a spouští skripty na virtuálních počítačích Azure. Při vytvoření virtuálního počítače nebo kdykoli po virtuální počítač se používá, můžete použít rozšíření. 

Skripty si můžete stáhnout z úložiště Azure nebo všechny veřejné umístění, jako je například úložiště GitHub. Díky rozšíření vlastních skriptů můžete vytvořit skripty v libovolném jazyce, který běží na zdrojového virtuálního počítače. Tyto skripty můžete použít k instalaci aplikací nebo podle potřeby nakonfigurujte virtuální počítač. K zabezpečení přihlašovacích údajů, mohou být uloženy citlivé informace, jako jsou hesla v chráněném konfiguraci. Tyto přihlašovací údaje jsou dešifrovat jenom ve virtuálním počítači.

Naučte se:

- [Vytvoření virtuálního počítače s Linuxem pomocí rozhraní příkazového řádku Azure a používat rozšíření vlastních skriptů](../articles/virtual-machines/scripts/virtual-machines-linux-cli-sample-create-vm-nginx.md?toc=%2fcli%2fazure%2ftoc.json).
- [Vytvoření virtuálního počítače s Windows v prostředí Azure PowerShell a použít rozšíření vlastních skriptů](../articles/virtual-machines/scripts/virtual-machines-windows-powershell-sample-create-vm-iis.md?toc=%2fpowershell%2fmodule%2ftoc.json).


## <a name="packer"></a>Balírna
[Balírna](https://www.packer.io) automatizuje proces vytváření při vytváření vlastní image virtuálního počítače v Azure. Balírna můžete použít k definování operačního systému a spustit skripty po konfiguraci, které přizpůsobení virtuálního počítače svých konkrétních potřeb. Po nakonfigurování virtuální počítač se pak zaznamená jako image spravované disku. Balírna automatizuje proces vytvoření zdroje virtuálního počítače, sítě a prostředky úložiště, spusťte konfigurační skripty a poté vytvořit bitovou kopii virtuálního počítače.

Naučte se:

- [Vytvořte bitovou kopii virtuálního počítače s Linuxem v Azure pomocí balírna](../articles/virtual-machines/linux/build-image-with-packer.md).
- [Vytvořte bitovou kopii virtuálního počítače s Windows v Azure pomocí balírna](../articles/virtual-machines/windows/build-image-with-packer.md).


## <a name="terraform"></a>Terraform
[Terraform](https://www.terraform.io) je nástroj pro automatizaci, která umožňuje definovat a vytvořit celé infrastruktury Azure s jednou šablonou jazyk formátu – jazyk kompatibilního (HashiCorp konfigurace hardwaru). Terraform můžete definovat šablony, které automatizují proces vytvoření sítě, úložiště a prostředky virtuálních počítačů pro řešení dané aplikaci. Vaše stávající šablony Terraform pro jiné platformy s Azure můžete použít k zajištění konzistence a zjednodušit nasazení infrastruktury bez nutnosti převést na šablonu Azure Resource Manager.

Naučte se:

- [Instalace a konfigurace Terraform s Azure](../articles/virtual-machines/linux/terraform-install-configure.md).
- [Vytvoření infrastruktury Azure s Terraform](../articles/virtual-machines/linux/terraform-create-complete-vm.md).


## <a name="azure-automation"></a>Azure Automation
[Služby Azure Automation](https://azure.microsoft.com/services/automation/) ke zpracování sadu úloh na virtuálních počítačích můžete zacílit používá sady runbook. Služby Azure Automation se používá ke správě existujících virtuálních počítačů, a nikoli k vytvoření infrastruktury. Automatizace Azure můžete spustit v systému Linux a virtuálních počítačů Windows, jak místní virtuální nebo fyzické počítače s hybridní pracovní proces runbooku. Sady Runbook mohou být uloženy ve úložiště řízení zdrojů, jako je například Githubu. Tyto sady runbook pak spustíte ručně nebo podle definovaného plánu.

Azure Automation nabízí taky služba Konfigurace požadovaného stavu (DSC), která vám umožní vytvořit definice pro konfigurace danou sadu virtuálních počítačů. DSC pak zajišťuje, že se použije požadované konfigurace a virtuální počítač zůstane konzistentní. Azure Automation DSC se spustí na počítačích s Windows a Linux.

Naučte se:

- [Vytvoření sady runbook PowerShell](../articles/automation/automation-first-runbook-textual-powershell.md).
- [Hybridní pracovní proces Runbooku použít ke správě místních prostředků](../articles/automation/automation-hybrid-runbook-worker.md).
- [Pomocí služby Azure Automation DSC](../articles/automation/automation-dsc-getting-started.md).


## <a name="visual-studio-team-services"></a>Visual Studio Team Services
[Tým služby](https://www.visualstudio.com/team-services/) je sada nástrojů, které vám pomůžou sdílené složky a sledovat kódu, použijte automatizovaných sestaveních a vytvořit úplný průběžnou integraci a vývoj (CI/CD) kanálu. Týmové služby se integruje s Visual Studio a dalšími editory zjednodušit využití. Týmové služby můžete také vytvořit a nakonfigurovat virtuální počítače Azure a pak nasadíte kód do nich.

Naučte se:

- [Vytvoření kanálu průběžnou integraci s Team Services](../articles/virtual-machines/windows/tutorial-vsts-iis-cicd.md).


## <a name="jenkins"></a>Jenkins
[Volaných](https://www.jenkins.io) průběžnou integraci serverem, který pomáhá nasazení a testování aplikací a vytvořit automatizované kanály pro doručení kódu. Používají stovky modulů plug-in rozšířit možnosti základní platformy volaných a můžete také integrovat s mnoha dalších produktů a řešení prostřednictvím webhooky. Můžete ručně nainstalovat volaných na virtuální počítač Azure, spustit volaných z v rámci kontejner Docker nebo použít předem připravené image Azure Marketplace.

Naučte se:

- [Vytvoření vývoj infrastruktury na virtuální počítač s Linuxem v Azure pomocí volaných Githubu a Docker](../articles/virtual-machines/linux/tutorial-jenkins-github-docker-cicd.md).


## <a name="next-steps"></a>Další kroky
Existuje mnoho různých možností použití nástrojů infrastruktury automation v Azure. Máte možnost používat tato zařízení používat řešení, které nejlépe vyhovuje potřebám a prostředí. Začínáme a vyzkoušejte některé z nástrojů, které jsou integrované v Azure najdete v tématu Jak automatizovat možnosti vlastního nastavení [Linux](../articles/virtual-machines/linux/tutorial-automate-vm-deployment.md) nebo [Windows](../articles/virtual-machines/windows/tutorial-automate-vm-deployment.md) virtuálních počítačů.
