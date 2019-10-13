---
title: Přehled podpory Cloud-init pro virtuální počítače se systémem Linux v Azure | Microsoft Docs
description: Přehled možností Cloud-init v Microsoft Azure
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
ms.date: 10/11/2019
ms.author: danis
ms.openlocfilehash: b0300dd91876b651015ae78c53dbc1e72bf8dd68
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72285697"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Podpora Cloud-init pro virtuální počítače v Azure
V tomto článku se dozvíte, jak podpora pro [Cloud-init](https://cloudinit.readthedocs.io) nakonfigurovat virtuální počítač (VM) nebo službu Virtual Machine Scale Sets v době zřizování v Azure. Tyto skripty Cloud-init se spouštějí při prvním spuštění, jakmile se prostředky zřídí v Azure.  

## <a name="cloud-init-overview"></a>Přehled Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Vzhledem k tomu, že se během procesu prvotního spuštění volá Cloud-init, neexistují žádné další kroky ani nepotřebné agenti pro použití konfigurace.  Další informace o tom, jak správně zformátovat soubory `#cloud-config`, najdete v tématu věnovaném nástroji [Cloud-init Documentation](https://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  soubory `#cloud-config` jsou textové soubory kódované v kódování Base64.

Cloud-init navíc funguje v různých distribucích. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky použije nativní nástroj pro správu balíčků pro zvolenou distribuci.

Aktivně spolupracujeme s našimi distribuce partnery se systémem Linux, aby byly k dispozici image s povoleným cloudovým inicializacím na webu Azure Marketplace. Díky těmto imagí budou vaše nasazení a konfigurace pro cloudovou inicializaci bez problémů fungovat s virtuálními počítači a sadami škálování virtuálních počítačů. Následující tabulka popisuje aktuální dostupnost imagí povolených pro inicializaci cloudu na platformě Azure:

| Vydavatel | Nabídka | Skladová položka | Version | Cloud-init připraven |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04-LTS |latest |ano | 
|Canonical |UbuntuServer |16.04-LTS |latest |ano | 
|Canonical |UbuntuServer |14.04.5-LTS |latest |ano |
|CoreOS |CoreOS |Stable |latest |ano |
|OpenLogic 7,7 |CentOS |7-CI |7.7.20190920 |preview |
|RedHat 7,6 |RHEL |7-RAW-CI |7.6.2019072418 |ano |
|RedHat 7,7 |RHEL |7-RAW-CI |7.7.2019081601 |preview |
    
V současné době Azure Stack nepodporuje zřizování RHEL 7. x a CentOS 7. x pomocí Cloud-init.

* V případě balíčku Cloud-init pro RHEL 7,6 je podporovaným balíčkem: *18.2 -1. el7 _ 6.2* 
* V případě balíčku Cloud-init pro RHEL 7,7 (Preview) je balíček verze Preview: *18.5 -3. el7* .
* V případě balíčku Cloud-init pro CentOS 7,7 (Preview) je balíček verze Preview: *18.5 -3. el7. CentOS*

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Jaký je rozdíl mezi Cloud-init a agentem pro Linux (WALA)?
WALA je agentem specifickým pro platformu Azure, který slouží ke zřizování a konfiguraci virtuálních počítačů a zpracování rozšíření Azure. Rozšiřujeme úlohu konfigurace virtuálních počítačů tak, aby používala Cloud-init místo agenta pro Linux, aby mohli stávající zákazníci s cloudovým inicializací používat své aktuální skripty Cloud-init.  Pokud máte existující investice do skriptů Cloud-init pro konfiguraci systémů Linux, není **potřeba žádná další nastavení** , která by je musela povolit. 

Pokud v době zřizování nezahrnete přepínač Azure CLI `--custom-data`, WALA použije minimální parametry zřizování virtuálního počítače, které jsou potřeba ke zřízení virtuálního počítače, a dokončení nasazení s výchozími hodnotami.  Pokud odkazujete na přepínač Cloud-init `--custom-data` bez ohledu na to, co je obsaženo ve vašich vlastních datech (jednotlivá nastavení nebo úplný skript), přepíše výchozí nastavení WALA. 

WALA konfigurace virtuálních počítačů je časově omezená, aby fungovala v maximální době zřizování virtuálního počítače.  Konfigurace Cloud-init použité u virtuálních počítačů nemají časová omezení a nezpůsobí selhání nasazení pomocí vypršení časového limitu. 

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
Stisknutím `ctrl-X` ukončete soubor, zadáním `y` soubor uložte a stisknutím `enter` potvrďte název souboru při ukončení.

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

Po vytvoření virtuálního počítače se v Azure CLI zobrazí informace, které jsou specifické pro vaše nasazení. Poznamenejte si `publicIpAddress`. Tato adresa se používá pro přístup k virtuálnímu počítači.  Vytvoření virtuálního počítače bude chvíli trvat, balíčky, které se mají nainstalovat, a aplikaci, která se má spustit. Když vás Azure CLI vrátí na příkazový řádek, na pozadí stále poběží úlohy. K virtuálnímu počítači se můžete přihlásit přes SSH a pomocí kroků popsaných v části řešení potíží zobrazit protokoly Cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Řešení potíží s cloudem a inicializací
Po zřízení virtuálního počítače se Cloud-init spustí pomocí všech modulů a skriptu definovaného v `--custom-data`, aby se virtuální počítač nakonfiguroval.  Pokud potřebujete odstranit případné chyby nebo opomenutí z konfigurace, je třeba vyhledat název modulu (například `disk_setup` nebo `runcmd`) v protokolu Cloud-init – nachází se v **/var/log/Cloud-init.log**.

> [!NOTE]
> Nejedná se o selhání každého modulu, protože by došlo k závažné chybě při inicializaci Cloud-init. Pokud například použijete modul `runcmd`, Cloud-init bude i nadále nahlásit úspěšné zřizování, protože se spustil modul runcmd.

Další podrobnosti o protokolování Cloud-init najdete v dokumentaci ke službě [Cloud-init](https://cloudinit.readthedocs.io/en/latest/topics/logging.html) . 

## <a name="next-steps"></a>Další kroky
Příklady změn konfigurace Cloud-init najdete v následujících dokumentech:
 
- [Přidání dalšího uživatele se systémem Linux k virtuálnímu počítači](cloudinit-add-user.md)
- [Spusťte Správce balíčků, aby při prvním spuštění aktualizoval existující balíčky.](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizace konfiguračních souborů a vkládání klíčů](tutorial-automate-vm-deployment.md)
