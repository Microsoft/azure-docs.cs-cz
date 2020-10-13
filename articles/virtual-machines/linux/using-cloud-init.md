---
title: Přehled podpory Cloud-init pro virtuální počítače se systémem Linux v Azure
description: Přehled možností Cloud-init pro konfiguraci virtuálního počítače v době zřizování v Azure.
author: danielsollondon
ms.service: virtual-machines-linux
ms.subservice: extensions
ms.workload: infrastructure-services
ms.topic: how-to
ms.date: 06/15/2020
ms.author: danis
ms.openlocfilehash: 7ddbb48f3598780988feb25a11729a5086d31fde
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88869265"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Podpora Cloud-init pro virtuální počítače v Azure
V tomto článku se dozvíte, jak podpora pro [Cloud-init](https://cloudinit.readthedocs.io) nakonfigurovat virtuální počítač (VM) nebo službu Virtual Machine Scale Sets v době zřizování v Azure. Tyto konfigurace Cloud-init se po prvním spuštění spustí, jakmile se prostředky zřídí v Azure.  

Zřizování virtuálních počítačů je proces, ve kterém Azure přepošle virtuální počítač, vytvoří hodnoty parametrů, jako je název hostitele, uživatelské jméno, heslo atd. a zpřístupňuje virtuálnímu počítači při jeho spuštění. Agent zřizování bude tyto hodnoty využívat, konfigurovat virtuální počítač a po dokončení nahlásit zpět. 

Azure podporuje dva zřizovací agenty [Cloud-init](https://cloudinit.readthedocs.io)a [Agent Azure Linux (Wala)](../extensions/agent-linux.md).

## <a name="cloud-init-overview"></a>Přehled Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) je široce používaný přístup k přizpůsobení virtuálního počítače se systémem Linux při prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že se během procesu prvotního spuštění volá Cloud-init, neexistují žádné další kroky ani nepotřebné agenti pro použití konfigurace.  Další informace o tom, jak správně formátovat `#cloud-config` soubory nebo jiné vstupy, najdete na [webu dokumentace k inicializaci Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` soubory jsou textové soubory kódované v kódování Base64.

Cloud-init funguje i napříč distribucí. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky používá nástroj pro správu nativních balíčků pro distribuce, který vyberete.

Aktivně spolupracujeme s našimi distribuce partnery pro Linux, aby byly k dispozici image s povoleným cloudovým inicializacm v Azure Marketplace. Díky těmto imagí budou vaše nasazení a konfigurace pro cloudovou inicializaci bez problémů fungovat s virtuálními počítači a sadami škálování virtuálních počítačů. Zpočátku spolupracujeme s schválenými partnery a distribucemi pro Linux, aby se zajistilo, že funkce Cloud-init s operačním systémem v Azure jsou aktualizované a veřejně dostupné v úložištích balíčků distribuce. 

K dispozici jsou dvě fáze pro zajištění, že se Cloud-init zpřístupní v Azure s potvrzeným operačním systémem Linux distribuce, podpora balíčků a podpora imagí:
* Podpora balíčku Cloud-init v Azure: dokumenty Cloud-init a vyšší jsou podporované nebo ve verzi Preview, takže můžete tyto balíčky použít s operačním systémem ve vlastní imagi.
* Dokumenty ' image Cloud-init Ready ', pokud je image již nakonfigurovaná na použití Cloud-init.


### <a name="canonical"></a>Canonical
| Vydavatel/verze| Nabídka | SKU | Verze | Image cloudu – inicializace připravená | Podpora balíčku Cloud-init v Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Kanonický 20,04 |UbuntuServer |18,04 – LTS |nejnovější |ano | ano |
|Kanonický 18,04 |UbuntuServer |18,04 – LTS |nejnovější |ano | ano |
|Kanonický 16,04|UbuntuServer |16.04-LTS |nejnovější |ano | ano |
|Kanonický 14,04|UbuntuServer |14.04.5-LTS |nejnovější |ano | ano |

### <a name="rhel"></a>RHEL
| Vydavatel/verze | Nabídka | SKU | Verze | Image cloudu – inicializace připravená | Podpora balíčku Cloud-init v Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |ano | Ano – podpora z verze balíčku: *18.2-1.el7_6.2*|
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 | Ano (Poznámka: Toto je náhled obrázku a **nesmí** se již používat, bude odebrána 1. září 2020) | Není k dispozici |
|RedHat 7,7 (Gen1)|RHEL |7.7 | 7.7.2020051912 | ano | Ano – podpora z verze balíčku: *18.5 -6. el7*|
|RedHat 7,7 (Gen2)|RHEL | 77 – Gen2 | 7.7.2020051913 | ano | Ano – podpora z verze balíčku: *18.5 -6. el7*|
|RedHat 7,7 (Gen1)|RHEL |7 – LVM | 7.7.2020051921 | ano | Ano – podpora z verze balíčku: *18.5 -6. el7*|
|RedHat 7,7 (Gen2)|RHEL | 7lvm – Gen2 | 7.7.2020051922  | ano | Ano – podpora z verze balíčku: *18.5 -6. el7*|
|RedHat 7,7 (Gen1) |RHEL – BYOS | RHEL – lvm77 | 7.7.20200416 | ano  | Ano – podpora z verze balíčku: *18.5 -6. el7*|
|RedHat 8,1 (Gen1) |RHEL |8,1 – CI |8.1.2020042511 | Ano (Poznámka: Toto je náhled image a jakmile všechny image RHEL 8,1 podporují Cloud-init, odstraní se 1. srpna 2020). | Ne, ETA pro plnou podporu červen 2020|
|RedHat 8,1 (Gen2) |RHEL |81-CI-Gen2 |8.1.2020042524 | Ano (Poznámka: Toto je náhled image a jakmile všechny image RHEL 8,1 podporují Cloud-init, odstraní se 1. srpna 2020). | Ne, ETA pro plnou podporu červen 2020 |

* Všechny Image RedHat: RHEL 7,8 a 8,2 (Gen1 a Gen2) se zřídí pomocí Cloud-init.

### <a name="centos"></a>CentOS

| Vydavatel/verze | Nabídka | SKU | Verze | Image cloudu – inicializace připravená | Podpora balíčku Cloud-init v Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |Ano (Poznámka: Toto je náhled obrázku a **nesmí** se již používat, bude odebrána 1. září 2020) | Není k dispozici |
|OpenLogic 7,7 |CentOS | 7.7 |7.7.2020062400 |ano | Ano – podpora z verze balíčku: `18.5-6.el7.centos.5`|
|OpenLogic 7,7 (Gen2) |CentOS | 7_7 – Gen2 |7.7.2020062401 |ano | Ano – podpora z verze balíčku: `18.5-6.el7.centos.5`|
|OpenLogic 7,7 |CentOS – HPC | 7.7 |7.6.2020062600 |ano | Ano – podpora z verze balíčku: `18.5-6.el7.centos.5`|
|OpenLogic 7,7 (Gen2) |CentOS – HPC | 7_7 – Gen2 |7.6.2020062601 |ano | Ano – podpora z verze balíčku: `18.5-6.el7.centos.5`|
|OpenLogic 8,1 |CentOS | 8_1 |8.1.2020062400 |ano | Ano – podpora z verze balíčku: `18.5-7.el8_1.1`|
|OpenLogic 8,1 (Gen2) |CentOS | 8_1 – Gen2 |8.1.2020062401 |ano | Ano – podpora z verze balíčku: `18.5-7.el8_1.1`|
|OpenLogic 8,1 |CentOS – HPC | 8_1 |8.1.2020062400 |ano | Ano – podpora z verze balíčku: `18.5-7.el8_1.1`|
|OpenLogic 8,1 (Gen2) |CentOS-HPC: 8_1-Gen2 | 8_1 – Gen2 |8.1.2020062401 |ano | Ano – podpora z verze balíčku: `18.5-7.el8_1.1`|

* Všechny Image OpenLogic: CentOS 7,8 a 8,2 (Gen1 a Gen2) se zřídí pomocí Cloud-init.

### <a name="oracle"></a>Oracle

| Vydavatel/verze | Nabídka | SKU | Verze | Image cloudu – inicializace připravená | Podpora balíčku Cloud-init v Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7,7 |Oracle-Linux |77 – CI |7.7.01| Náhled obrázku (Poznámka: Toto je obrázek verze Preview a když všechny Image Oracle 7,7 podporují Cloud-init, odstraní se i část 2020, oznámení se zobrazí). | Ne, balíček je ve verzi Preview: *18.5-3.0.1. el7*

### <a name="suse-sles"></a>SUSE SLES
Tyto image SLES se aktualizovaly, aby se zřídily pomocí Cloud-init, ale byly aktualizovány taky varianty imagí Gen2.
* SUSE: SLES-15-SP1-{Basic/BYOS/HPC/HPC-BYOS/chost-BYOS}: Gen1:2020.06.10
* SUSE: SLES-SAP-15-SP1: Gen1:2020.06.10
* SUSE: SLES-SAP-15-SP1-BYOS: Gen1:2020.06.10
* SUSE: Manager-proxy-4-BYOS: Gen1:2020.06.10
* SUSE: Manager-Server-4-BYOS: Gen1:2020.06.10
* SUSE: SLES-{BYOS/SAP/SAP-BYOS}: 15:2020.06.10
* SUSE: SLES-12-SP5: Gen1:2020.06.10
* SUSE: SLES-12-SP5 {-BYOS/Basic/HPC-BYOS/HPC}: Gen1:2020.06.10
* SUSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP4:2020.06.10
* SUSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP3:2020.06.10
* SUSE: SLES-{BYOS/SAP/SAP-BYOS}: 12-SP2:2020.06.10


### <a name="debian"></a>Debian
| Vydavatel/verze | Nabídka | SKU | Verze | Image cloudu – inicializace připravená | Podpora balíčku Cloud-init v Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
| Debian (Gen1) |Debian-10 | 10 – cloudinit |Cloud-init-Preview| Ano (jenom náhled) | Ne, ve verzi Preview. |
| Debian (Gen2) |Debian-10 | 10 – cloudinit – Gen2 |Cloud-init-Preview| Ano (jenom náhled) | Ne, ve verzi Preview. |




V současné době Azure Stack bude podporovat zřizování imagí s povoleným cloudovým inicializací.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Jaký je rozdíl mezi Cloud-init a agentem pro Linux (WALA)?
WALA je agentem specifickým pro platformu Azure, který slouží ke zřizování a konfiguraci virtuálních počítačů a zpracování [rozšíření Azure](../extensions/features-linux.md). 

Rozšiřujeme úlohu konfigurace virtuálních počítačů tak, aby používala Cloud-init místo agenta pro Linux, aby mohli stávající zákazníci s cloudovým inicializací používat své aktuální skripty Cloud-init, nebo noví zákazníci, kteří využijí bohatou funkcionalitu konfigurace Cloud-init. Pokud máte existující investice do skriptů Cloud-init pro konfiguraci systémů Linux, nemusíte pro povolení procesu Cloud-init používat **žádná další nastavení** . 

Cloud-init nemůže zpracovat rozšíření Azure, takže WALA se v imagi pořád vyžaduje pro proces rozšíření, ale bude muset mít zakázaný kód pro zřizování. pro schválené image distribuce pro Linux, které se převádějí do zřizování pomocí Cloud-init, se WALA nainstalují a instalační program správně.

Pokud při vytváření virtuálního počítače nezahrnete přepínač Azure CLI `--custom-data` při zřizování, Cloud-init nebo Wala převezme minimální parametry zřizování virtuálních počítačů, které musí zřídit virtuální počítač a dokončit nasazení s výchozím nastavením.  Pokud s přepínačem odkazujete na konfiguraci Cloud-init `--custom-data` , vše, co je obsaženo ve vašich vlastních datech, bude k dispozici pro Cloud-init při spuštění virtuálního počítače.

Konfigurace Cloud-init použité u virtuálních počítačů nemají časová omezení a nezpůsobí selhání nasazení pomocí vypršení časového limitu. Neplatí to pro WALA, pokud změníte výchozí WALA ve výchozím nastavení tak, aby zpracovával vlastní data, nemůže přesáhnout celkovou dobu zřizování virtuálních počítačů 40mins. Pokud ano, vytvoření virtuálního počítače se nezdaří.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Nasazení virtuálního počítače s povoleným inicializací cloudu
Nasazení virtuálního počítače s povoleným cloudovým inicializací je jednoduché jako odkazování na distribuci s povoleným cloudovým inicializací během nasazování.  Správa distribuce systému Linux musí být zvolena pro povolení a integraci Cloud-init do svých základních publikovaných imagí Azure. Jakmile ověříte, že bitová kopie, kterou chcete nasadit, je povolená Cloud-init, můžete image nasadit pomocí rozhraní příkazového řádku Azure CLI. 

Prvním krokem při nasazení tohoto obrázku je vytvoření skupiny prostředků pomocí příkazu [AZ Group Create](/cli/azure/group) . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```

Dalším krokem je vytvoření souboru v aktuálním prostředí s názvem *cloud-init.txt* a vložení následující konfigurace. V tomto příkladu vytvořte soubor v Cloud Shell ne na vašem místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud-init.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Vyberte #1 pro použití editoru **nano** . Ujistěte se, že se celý soubor cloud-init zkopíroval správně, zejména první řádek:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Stisknutím `ctrl-X` zavřete soubor, zadáním `y` uložte soubor a stisknutím klávesy `enter` potvrďte název souboru při ukončení.

Posledním krokem je vytvoření virtuálního počítače pomocí příkazu [AZ VM Create](/cli/azure/vm) . 

Následující příklad vytvoří virtuální počítač s názvem *centos74* a vytvoří klíče SSH, pokud ještě neexistují ve výchozím umístění klíče. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  Pomocí parametru `--custom-data` předejte svůj konfigurační soubor cloud-init. Pokud jste konfigurační soubor *cloud-init.txt* uložili mimo aktuální pracovní adresář, zadejte úplnou cestu k němu. 

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Po vytvoření virtuálního počítače se v Azure CLI zobrazí informace, které jsou specifické pro vaše nasazení. Poznamenejte si `publicIpAddress`. Tato adresa se používá pro přístup k virtuálnímu počítači.  Vytvoření virtuálního počítače bude chvíli trvat, balíčky, které se mají nainstalovat, a aplikaci, která se má spustit. Když vás Azure CLI vrátí na příkazový řádek, na pozadí stále poběží úlohy. K virtuálnímu počítači se můžete přihlásit přes SSH a pomocí kroků popsaných v části řešení potíží zobrazit protokoly Cloud-init. 

Virtuální počítač s povolenou inicializací cloudu můžete nasadit taky tak, že [v ŠABLONĚ ARM](https://docs.microsoft.com/azure/azure-resource-manager/templates/deploy-cli#inline-parameters)předáte parametry.

## <a name="troubleshooting-cloud-init"></a>Řešení potíží s cloudem a inicializací
Po zřízení virtuálního počítače se Cloud-init spustí prostřednictvím všech modulů a skriptu definovaných v nástroji, aby `--custom-data` bylo možné virtuální počítač nakonfigurovat.  Pokud potřebujete odstranit případné chyby nebo opomenutí z konfigurace, je třeba vyhledat název modulu ( `disk_setup` `runcmd` například) v protokolu Cloud-init, který se nachází v **/var/log/Cloud-init.log**.

> [!NOTE]
> Nejedná se o selhání každého modulu, protože by došlo k závažné chybě při inicializaci Cloud-init. Pokud například použijete `runcmd` modul, Cloud-init bude i nadále hlásit úspěšné zřizování, protože se spustil modul runcmd.

Další podrobnosti o protokolování Cloud-init najdete v dokumentaci ke službě [Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) . 

## <a name="next-steps"></a>Další kroky

[Řešení potíží s Cloud-init](cloud-init-troubleshooting.md).


Příklady změn konfigurace Cloud-init najdete v následujících dokumentech:
 
- [Přidání dalšího uživatele se systémem Linux k virtuálnímu počítači](cloudinit-add-user.md)
- [Spusťte Správce balíčků, aby při prvním spuštění aktualizoval existující balíčky.](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vkládání klíčů](tutorial-automate-vm-deployment.md)
 
