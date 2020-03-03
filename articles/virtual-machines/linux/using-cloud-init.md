---
title: Přehled podpory Cloud-init pro virtuální počítače se systémem Linux v Azure
description: Přehled možností Cloud-init pro konfiguraci virtuálního počítače v době zřizování v Azure.
services: virtual-machines-linux
documentationcenter: ''
author: danielsollondon
manager: gwallace
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 01/23/2019
ms.author: danis
ms.openlocfilehash: 0309d9a794a978c736ffc4689c46565ee8fb5b00
ms.sourcegitcommit: 390cfe85629171241e9e81869c926fc6768940a4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/02/2020
ms.locfileid: "78226695"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Podpora Cloud-init pro virtuální počítače v Azure
V tomto článku se dozvíte, jak podpora pro [Cloud-init](https://cloudinit.readthedocs.io) nakonfigurovat virtuální počítač (VM) nebo službu Virtual Machine Scale Sets v době zřizování v Azure. Tyto konfigurace Cloud-init se po prvním spuštění spustí, jakmile se prostředky zřídí v Azure.  

Zřizování virtuálních počítačů je proces, ve kterém Azure přepošle virtuální počítač, vytvoří hodnoty parametrů, jako je název hostitele, uživatelské jméno, heslo atd. a zpřístupňuje virtuálnímu počítači při jeho spuštění. Agent zřizování bude tyto hodnoty využívat, konfigurovat virtuální počítač a po dokončení nahlásit zpět. 

Azure podporuje dva zřizovací agenty [Cloud-init](https://cloudinit.readthedocs.io)a [Agent Azure Linux (Wala)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).

## <a name="cloud-init-overview"></a>Přehled Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) je široce používaný přístup k přizpůsobení virtuálního počítače se systémem Linux při prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Protože cloud-init je volána v průběhu procesu prvotního spuštění, nejsou žádné další kroky ani agenty vyžaduje použití vaší konfigurace.  Další informace o tom, jak správně formátovat `#cloud-config` soubory nebo jiné vstupy, najdete v [dokumentaci ke cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  soubory `#cloud-config` jsou textové soubory kódované v kódování Base64.

Cloud-init funguje i napříč distribucí. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky používá nástroj pro správu nativních balíčků pro distribuce, který vyberete.

Aktivně Pracujeme s našimi partnery doporučené distribuce Linuxu aby cloud-init povolené imagí dostupných v Tržišti Azure marketplace. Díky těmto imagí budou vaše nasazení a konfigurace pro cloudovou inicializaci bez problémů fungovat s virtuálními počítači a sadami škálování virtuálních počítačů. Zpočátku spolupracujeme s schválenými partnery a distribucemi pro Linux, aby se zajistilo, že funkce Cloud-init s operačním systémem v Azure jsou aktualizované a veřejně dostupné v úložištích balíčků distribuce. 

K dispozici jsou dvě fáze pro zajištění, že se Cloud-init zpřístupní v Azure s potvrzeným operačním systémem Linux distribuce, podpora balíčků a podpora imagí:
* Podpora balíčku Cloud-init v Azure: dokumenty Cloud-init a vyšší jsou podporované nebo ve verzi Preview, takže můžete tyto balíčky použít s operačním systémem ve vlastní imagi.
* Dokumenty ' image Cloud-init Ready ', pokud je image již nakonfigurovaná na použití Cloud-init.


### <a name="canonical"></a>Canonical
| Vydavatel/verze| Nabídka | Skladová položka | Version | Image cloudu – inicializace připravená | Podpora balíčku Cloud-init v Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Kanonický 18,04 |UbuntuServer |18,04 – LTS |nejnovější |ano | ano |
|Kanonický 16,04|UbuntuServer |16.04-LTS |nejnovější |ano | ano |
|Kanonický 14,04|UbuntuServer |14.04.5-LTS |nejnovější |ano | ano |

### <a name="rhel"></a>RHEL
| Vydavatel/verze | Nabídka | Skladová položka | Version | Image cloudu – inicializace připravená | Podpora balíčku Cloud-init v Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |ano | Ano – podpora z verze balíčku: *18.2-1. el7_6.2*|
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 | Ano (Všimněte si, že toto je image ve verzi Preview a že všechny image RHEL 7,7 podporují Cloud-init, odstraní se i část 2020, oznámení se zobrazí). | Ano – podpora z verze balíčku: *18.5 -3. el7*|
|RedHat 7,7 |RHEL |7 – RAW | neuvedeno| žádné aktualizace obrázků k začátku února 2020| Ano – podpora z verze balíčku: *18.5 -3. el7*|
|RedHat 7,7 |RHEL |7 – LVM | neuvedeno| žádné aktualizace obrázků k začátku února 2020| Ano – podpora z verze balíčku: *18.5 -3. el7*|
|RedHat 7,7 |RHEL |7,7 | neuvedeno| žádné aktualizace obrázků k začátku února 2020 | Ano – podpora z verze balíčku: *18.5 -3. el7*|
|RedHat 7,7 |RHEL – BYOS | RHEL – lvm77 | neuvedeno|žádné aktualizace obrázků k začátku února 2020  | Ano – podpora z verze balíčku: *18.5 -3. el7*|

### <a name="centos"></a>CentOS

| Vydavatel/verze | Nabídka | Skladová položka | Version | Image cloudu – inicializace připravená | Podpora balíčku Cloud-init v Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |Ano (Všimněte si, že toto je image ve verzi Preview a že všechny image CentOS 7,7 podporují Cloud-init, odstraní se i část 2020, oznámení se zobrazí). | Ano – podpora z verze balíčku: *18.5 -3. el7. CentOS*|

* Image CentOS 7,7, které budou povolené pro cloudovou inicializaci, se aktualizují tady v únoru 2020. 

### <a name="oracle"></a>Oracle

| Vydavatel/verze | Nabídka | Skladová položka | Version | Image cloudu – inicializace připravená | Podpora balíčku Cloud-init v Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Oracle 7,7 |Oracle – Linux |77 – CI |7.7.01| Náhled obrázku (Všimněte si, že se jedná o image ve verzi Preview a když všechny Image Oracle 7,7 podporují Cloud-init, odstraní se i část 2020, oznámení se zobrazí). | Ne, balíček je ve verzi Preview: *18.5-3.0.1. el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
V současnosti pracujeme na podpoře verze Preview, očekáváme aktualizace v únoru a březnu 2020.

V současné době Azure Stack bude podporovat zřizování imagí s povoleným cloudovým inicializací.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Jaký je rozdíl mezi Cloud-init a agentem pro Linux (WALA)?
WALA je agentem specifickým pro platformu Azure, který slouží ke zřizování a konfiguraci virtuálních počítačů a zpracování [rozšíření Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux). 

Rozšiřujeme úlohu konfigurace virtuálních počítačů tak, aby používala Cloud-init místo agenta pro Linux, aby mohli stávající zákazníci s cloudovým inicializací používat své aktuální skripty Cloud-init, nebo noví zákazníci, kteří využijí bohatou funkcionalitu konfigurace Cloud-init. Pokud máte existující investice do skriptů Cloud-init pro konfiguraci systémů Linux, nemusíte pro povolení procesu Cloud-init používat **žádná další nastavení** . 

Cloud-init nemůže zpracovat rozšíření Azure, takže WALA se v imagi pořád vyžaduje pro proces rozšíření, ale bude muset mít zakázaný kód pro zřizování. pro schválené image distribuce pro Linux, které se převádějí na zřizování pomocí Cloud-init, budou mít WALA instalace a nastavení je správné.

Pokud při vytváření virtuálního počítače nezahrnete přepínač Azure CLI `--custom-data` v době zřizování, Cloud-init nebo WALA převezme minimální parametry zřizování virtuálního počítače, které jsou potřeba k zřízení virtuálního počítače, a dokončení nasazení s výchozími hodnotami.  Pokud odkazujete na konfiguraci Cloud-init s přepínačem `--custom-data`, vše, co je ve vašich vlastních datech, bude k dispozici pro Cloud-init při spuštění virtuálního počítače.

Konfigurace Cloud-init použité u virtuálních počítačů nemají časová omezení a nezpůsobí selhání nasazení pomocí vypršení časového limitu. Neplatí to pro WALA, pokud změníte výchozí WALA ve výchozím nastavení tak, aby zpracovával vlastní data, nemůže přesáhnout celkovou dobu zřizování virtuálních počítačů 40mins. Pokud ano, vytvoření virtuálního počítače se nezdaří.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Nasazení virtuálního počítače s povoleným inicializací cloudu
Nasazení virtuálního počítače s povoleným cloudovým inicializací je jednoduché jako odkazování na distribuci s povoleným cloudovým inicializací během nasazování.  Správa distribuce systému Linux musí být zvolena pro povolení a integraci Cloud-init do svých základních publikovaných imagí Azure. Jakmile ověříte, že bitová kopie, kterou chcete nasadit, je povolená Cloud-init, můžete image nasadit pomocí rozhraní příkazového řádku Azure CLI. 

Prvním krokem při nasazení tohoto obrázku je vytvoření skupiny prostředků pomocí příkazu [AZ Group Create](/cli/azure/group) . Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Dalším krokem je vytvoření souboru ve vašem aktuálním prostředí s názvem *Cloud-init. txt* a vložení následující konfigurace. V tomto příkladu vytvořte soubor v Cloud Shell ne na vašem místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud-init.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Vyberte #1 pro použití editoru **nano** . Ujistěte se, že se celý soubor cloud-init zkopíroval správně, zejména první řádek:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Stisknutím `ctrl-X` ukončete soubor, zadejte `y` a uložte soubor a stisknutím `enter` potvrďte název souboru při ukončení.

Posledním krokem je vytvoření virtuálního počítače pomocí příkazu [AZ VM Create](/cli/azure/vm) . 

Následující příklad vytvoří virtuální počítač s názvem *centos74* a vytvoří klíče SSH, pokud ještě neexistují ve výchozím umístění klíče. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  Pomocí parametru `--custom-data` předejte svůj konfigurační soubor cloud-init. Pokud jste konfigurační soubor *cloud-init.txt* uložili mimo aktuální pracovní adresář, zadejte úplnou cestu k němu. Následující příklad vytvoří virtuální počítač s názvem *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Po vytvoření virtuálního počítače se v Azure CLI zobrazí informace, které jsou specifické pro vaše nasazení. Poznamenejte si `publicIpAddress`. Tato adresa se používá pro přístup k virtuálnímu počítači.  Vytvoření virtuálního počítače bude chvíli trvat, balíčky, které se mají nainstalovat, a aplikaci, která se má spustit. Jakmile vás Azure CLI vrátí na příkazový řádek, na pozadí stále poběží úlohy. K virtuálnímu počítači se můžete přihlásit přes SSH a pomocí kroků popsaných v části řešení potíží zobrazit protokoly Cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Řešení potíží s cloudem a inicializací
Po zřízení virtuálního počítače se Cloud-init spustí pomocí všech modulů a skriptu definovaného v `--custom-data`, aby se virtuální počítač nakonfiguroval.  Pokud potřebujete odstranit případné chyby nebo opomenutí z konfigurace, je třeba vyhledat název modulu (například`disk_setup` nebo `runcmd`) v protokolu Cloud-init – nachází se v **/var/log/Cloud-init.log**.

> [!NOTE]
> Nejedná se o selhání každého modulu, protože by došlo k závažné chybě při inicializaci Cloud-init. Pokud například použijete modul `runcmd`e, Cloud-init bude i nadále nahlásit úspěšné zřizování, protože se spustil modul runcmd.

Další podrobnosti o protokolování Cloud-init najdete v dokumentaci ke službě [Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) . 

## <a name="next-steps"></a>Další kroky
Příklady změn konfigurace Cloud-init najdete v následujících dokumentech:
 
- [Přidání dalšího uživatele se systémem Linux k virtuálnímu počítači](cloudinit-add-user.md)
- [Spusťte Správce balíčků, aby při prvním spuštění aktualizoval existující balíčky.](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vkládání klíčů](tutorial-automate-vm-deployment.md)
 
