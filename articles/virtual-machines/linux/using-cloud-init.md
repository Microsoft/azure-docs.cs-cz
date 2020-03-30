---
title: Přehled podpory cloud-init pro virtuální počítače s Linuxem v Azure
description: Přehled cloud-init funkcí pro konfiguraci virtuálního počítače v době zřizování v Azure.
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
ms.openlocfilehash: 1f0395956fa6977be5d1d6f4f4faf06b84c094d8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79465035"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>podpora cloud-init pro virtuální počítače v Azure
Tento článek vysvětluje podporu, která existuje pro [cloud-init](https://cloudinit.readthedocs.io) pro konfiguraci virtuálního počítače (VM) nebo škálovací sady virtuálních strojů v době zřizování v Azure. Tyto konfigurace cloud-init se spouštějí při prvním spuštění, jakmile jsou prostředky zřízeny Azure.  

Zřizování virtuálních počítačů je proces, při kterém Azure předá hodnoty parametrů Create vašeho virtuálního počítače, jako je název hostitele, uživatelské jméno, heslo atd., a zpřístupní je virtuálnímu počítači při jeho spuštění. "Zřizovací agent" bude využívat tyto hodnoty, nakonfigurujte virtuální ho a sestavy zpět po dokončení. 

Azure podporuje dva zřizovací agenti [cloud-init](https://cloudinit.readthedocs.io)a [Azure Linux Agent (WALA)](https://docs.microsoft.com/azure/virtual-machines/extensions/agent-linux).

## <a name="cloud-init-overview"></a>přehled cloud-init
[cloud-init](https://cloudinit.readthedocs.io) je široce používaný přístup k přizpůsobení virtuálního počítače SIP při prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že cloud-init je volána během počátečního procesu spouštění, neexistují žádné další kroky nebo požadované agenty použít konfiguraci.  Další informace o správném formátování `#cloud-config` souborů nebo jiných vstupů naleznete [na webu dokumentace cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config`soubory jsou textové soubory kódované v base64.

cloud-init funguje také napříč distribucemi. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. cloud-init automaticky používá nativní nástroj pro správu balíčků pro distro, které vyberete.

Aktivně spolupracujeme s našimi schválenými partnery pro distribuci Linuxu, abychom měli na webu Azure Marketplace k dispozici image s podporou cloudu. Díky těmto bitovým kopiím budou nasazení a konfigurace v cloudu bezproblémově fungovat s virtuálními počítači a škálovacími sadami virtuálních počítačů. Zpočátku spolupracujeme s schválenými partnery distribuce Linuxu a upstream, abychom zajistili funkce cloud-init s operačním systémem v Azure, pak jsou balíčky aktualizovány a zpřístupněny veřejnosti v úložištích distro balíčků. 

Existují dvě fáze zpřístupnění cloud-initu schválenému operačnímu systému Linux distro v Azure, podpora balíčků a pak podpora image:
* "Podpora balíčků cloud-init v Azure", které jsou balíčky cloud-init podporovány nebo ve verzi Preview, takže tyto balíčky můžete použít s osem ve vlastní image.
* dokumenty "image cloud-init ready", pokud je obraz již nakonfigurován tak, aby používal cloud-init.


### <a name="canonical"></a>Canonical
| Vydavatel / verze| Nabídka | Skladová jednotka (SKU) | Version | image cloud-init připraven | podpora balíčků cloud-init v Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Kanonický 18,04 |UbuntuServer |18.04-LTS |nejnovější |ano | ano |
|Kanonický 16,04|UbuntuServer |16.04-LTS |nejnovější |ano | ano |
|Kanonický 14,04|UbuntuServer |14.04.5-LTS |nejnovější |ano | ano |

### <a name="rhel"></a>RHEL
| Vydavatel / verze | Nabídka | Skladová jednotka (SKU) | Version | image cloud-init připraven | podpora balíčků cloud-init v Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |ano | ano - podpora z verze balíčku: *18.2-1.el7_6.2*|
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 | ano (všimněte si, že se jedná o náhled obrázku, a jakmile všechny obrázky RHEL 7.7 podporují cloud-init, bude to odstraněno v polovině roku 2020, bude to dáno) | ano - podpora z verze balíčku: *18.5-3.el7*|
|RedHat 7,7 |RHEL |7-SUROVÝ | neuvedeno| ne - aktualizace obrázků do konce dubna 2020| ano - podpora z verze balíčku: *18.5-3.el7*|
|RedHat 7,7 |RHEL |7-LVM | neuvedeno| ne - aktualizace obrázků do konce dubna| ano - podpora z verze balíčku: *18.5-3.el7*|
|RedHat 7,7 |RHEL |7.7 | neuvedeno| ne - aktualizace obrázků do konce dubna | ano - podpora z verze balíčku: *18.5-3.el7*|
|RedHat 7,7 |rhel-byos | rým-lvm77 | neuvedeno|ne - aktualizace obrázků do konce dubna  | ano - podpora z verze balíčku: *18.5-3.el7*|

### <a name="centos"></a>CentOS

| Vydavatel / verze | Nabídka | Skladová jednotka (SKU) | Version | image cloud-init připraven | podpora balíčků cloud-init v Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |Ano (všimněte si, že se jedná o náhled obrázku, a jakmile všechny obrázky CentOS 7.7 podporují cloud-init, bude to odstraněno v polovině roku 2020, bude to dáno) | ano - podpora z verze balíčku: *18.5-3.el7.centos*|

* Obrázky CentOS 7.7, které budou povoleny v cloudu, budou aktualizovány v březnu 2020 

### <a name="oracle"></a>Oracle

| Vydavatel / verze | Nabídka | Skladová jednotka (SKU) | Version | image cloud-init připraven | podpora balíčků cloud-init v Azure|
|:--- |:--- |:--- |:--- |:--- |:--- |
|Řešení Oracle 7.7 |Společnost Oracle-Linux |77-ci |7.7.01| náhled obrázku (všimněte si, že se jedná o náhled obrazu, a to jakmile všechny obrazy Oracle 7.7 podporují cloud-init, bude to odstraněno v polovině roku 2020, bude vydáno oznámení) | ne, ve verzi preview, balíček je: *18.5-3.0.1.el7*

### <a name="debian--suse-sles"></a>Debian & SuSE SLES
V současné době pracujeme na náhledu podpory, očekáváme aktualizace v únoru a březnu 2020.

V současné době Azure Stack bude podporovat zřizování cloud-init povolené image.


## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Jaký je rozdíl mezi cloud-init a Linux Agent (WALA)?
WALA je agent specifický pro platformu Azure, který se používá k zřizování a konfiguraci virtuálních počítačů a zpracování [rozšíření Azure](https://docs.microsoft.com/azure/virtual-machines/extensions/features-linux). 

Vylepšujeme úlohu konfigurace virtuálních počítačů tak, aby používaly cloud-init místo Linux Agenta, aby stávajícím zákazníkům v cloudu mohli používat své aktuální skripty cloud-init, nebo novým zákazníkům, aby mohli využívat bohaté funkce konfigurace cloud-init. Pokud máte existující investice do cloud-init skripty pro konfiguraci linuxových systémů, nejsou **k dispozici žádné další nastavení potřebné** k povolení cloud-init jejich zpracování. 

cloud-init nemůže zpracovat rozšíření Azure, takže WALA je stále vyžadována v obraze ke zpracování rozšíření, ale bude muset mít jeho zřizovací kód zakázán, pro schválené linuxové distribuce, které jsou převáděny na poskytování cloud-init, budou mít WALA správně nainstalována a správně nastavena.

Při vytváření virtuálního počítače, pokud nezahrnete přepínač Azure CLI `--custom-data` v době zřizování, cloud-init nebo WALA trvá minimální parametry zřizování virtuálních počítačů potřebné k zřízení virtuálního počítače a dokončení nasazení s výchozí.  Pokud odkazujete na konfiguraci cloud-init s přepínačem, `--custom-data` cokoli, co je obsaženo ve vašich vlastních datech, bude k dispozici pro cloud-init při spuštění virtuálního počítače.

konfigurace cloud-init použité pro virtuální počítače nemají časová omezení a nezpůsobí selhání nasazení časovým limitem. To neplatí pro WALA, pokud změníte výchozí wala pro zpracování vlastních dat, nemůže překročit celkový časový limit zřizování virtuálních služeb 40 min, pokud ano, vytvoření virtuálního aplikace se nezdaří.

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Nasazení virtuálního počítače s podporou cloud-initu
Nasazení virtuálního počítače s podporou cloud-init je stejně jednoduché jako odkazování na distribuci povolenou cloud-init během nasazení.  Správci distribuce Linuxu se musí rozhodnout povolit a integrovat cloud-init do svých základních ibi publikovaných Azure. Jakmile potvrdíte image, kterou chcete nasadit, je povolená cloud-init, můžete použít rozhraní příkazového příkazu Azure k nasazení image. 

Prvním krokem při nasazování této bitové kopie je vytvoření skupiny prostředků pomocí příkazu [az group create.](/cli/azure/group) Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Dalším krokem je vytvoření souboru v aktuálním prostředí s názvem *cloud-init.txt* a vložení následující konfigurace. V tomto příkladu vytvořte soubor v prostředí Cloud Shell není v místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud-init.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1, abyste použili **editor nano.** Ujistěte se, že se celý soubor cloud-init zkopíroval správně, zejména první řádek:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Stisknutím `ctrl-X` souboru ukončete soubor, `y` `enter` zadáním souboru soubor uložte a stisknutím tlačítka potvrďte název souboru při ukončení.

Posledním krokem je vytvoření virtuálního virtuálního virtuálního montovana pomocí [příkazu az vm create.](/cli/azure/vm) 

Následující příklad vytvoří virtuální ho disponiál s názvem *centos74* a vytvoří klíče SSH, pokud ještě neexistují ve výchozím umístění klíče. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  Pomocí parametru `--custom-data` předejte svůj konfigurační soubor cloud-init. Pokud jste konfigurační soubor *cloud-init.txt* uložili mimo aktuální pracovní adresář, zadejte úplnou cestu k němu. Následující příklad vytvoří virtuální hod s názvem *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS-CI:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Po vytvoření virtuálního počítače azure cli zobrazí informace specifické pro vaše nasazení. Poznamenejte si `publicIpAddress`. Tato adresa se používá pro přístup k virtuálnímu počítači.  Trvá nějakou dobu, než se vytvoří virtuální hod, nainstaluje se balíčky a spustí se aplikace. Když vás Azure CLI vrátí na příkazový řádek, na pozadí stále poběží úlohy. Můžete SSH do virtuálního provozu a pomocí kroků popsaných v části Řešení potíží k zobrazení protokolů cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Řešení problémů s cloud-init
Po zřízení virtuálního počítače, cloud-init se spustí přes všechny moduly a skript definované v `--custom-data` za účelem konfigurace virtuálního počítače.  Pokud potřebujete vyřešit případné chyby nebo opomenutí z konfigurace, musíte`disk_setup` vyhledat `runcmd` název modulu (nebo například) v protokolu cloud-init - umístěném v **/var/log/cloud-init.log**.

> [!NOTE]
> Ne každý modul selhání má za následek selhání selhání celkové konfigurace cloud-init. Například pomocí `runcmd` modulu, pokud se nezdaří skript, cloud-init bude stále sestavy zřizování úspěšné, protože runcmd modul spuštěn.

Další podrobnosti o protokolování cloud-init, naleznete [v dokumentaci cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Další kroky
Příklady změn konfigurace cloud-init najdete v následujících dokumentech:
 
- [Přidání dalšího uživatele Linuxu do virtuálního počítače](cloudinit-add-user.md)
- [Spuštění správce balíčků pro aktualizaci existujících balíčků při prvním spuštění](cloudinit-update-vm.md)
- [Změna názvu místního hostitele virtuálního_kmontíva](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vložení klíčů](tutorial-automate-vm-deployment.md)
 
