---
title: Přehled cloud-init podporu pro virtuální počítače s Linuxem v Azure | Dokumentace Microsoftu
description: Přehled cloud-init funkcí v Microsoft Azure
services: virtual-machines-linux
documentationcenter: ''
author: rickstercdn
manager: jeconnoc
editor: ''
tags: azure-resource-manager
ms.assetid: 195c22cd-4629-4582-9ee3-9749493f1d72
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: azurecli
ms.topic: article
ms.date: 11/29/2017
ms.author: rclaus
ms.openlocfilehash: 0f7660e8534a74eabe32611c4c01ae5587af7cee
ms.sourcegitcommit: 63613e4c7edf1b1875a2974a29ab2a8ce5d90e3b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/29/2018
ms.locfileid: "43188869"
---
# <a name="cloud-init-support-for-virtual-machines-in-azure"></a>Podpora cloud-init pro virtuální počítače v Azure
Tento článek vysvětluje, zda existuje pro podporu [cloud-init](https://cloudinit.readthedocs.io) ke konfiguraci virtuálního počítače (VM) nebo virtuální počítače škálovacích sad (VMSS) zřizování času v Azure. Tyto skripty cloud-init spustit při prvním spuštění, jakmile se zřizují prostředky Azure.  

## <a name="cloud-init-overview"></a>Přehled Cloud-init
[Cloud-init](https://cloudinit.readthedocs.io) je široce využívaným přístupem k přizpůsobení virtuálního počítače s Linuxem při jeho prvním spuštění. Pomocí cloud-init můžete instalovat balíčky a zapisovat soubory nebo konfigurovat uživatele a zabezpečení. Protože cloud-init je volána v průběhu procesu prvotního spuštění, nejsou žádné další kroky ani agenty vyžaduje použití vaší konfigurace.  Další informace o tom, jak správně vaše `#cloud-config` soubory, najdete v článku [cloud-init dokumentačním webu](http://cloudinit.readthedocs.io/en/latest/topics/format.html#cloud-config-data).  `#cloud-config` soubory jsou textové soubory kódovaný jako base64.

Cloud-init navíc funguje v různých distribucích. K instalaci balíčku tak například nepoužijete **apt-get install** ani **yum install**. Místo toho můžete definovat seznam balíčků pro instalaci. Cloud-init automaticky použije nativní nástroj pro správu balíčků pro zvolenou distribuci.

 Aktivně Pracujeme s našimi partnery doporučené distribuce Linuxu aby cloud-init povolené imagí dostupných v Tržišti Azure marketplace. Tyto Image způsobí, že vaše nasazení cloud-init a konfigurace bez problému fungují s virtuálními počítači a virtuální počítače Škálovací sady (škálovací sady). Následující tabulka popisuje aktuální dostupnost Image povolené cloud-init na platformě Azure:

| Vydavatel | Nabídka | Skladová jednotka (SKU) | Verze | Připraveno na cloud-init |
|:--- |:--- |:--- |:--- |:--- |
|Canonical |UbuntuServer |18.04 LTS |nejnovější |ano | 
|Canonical |UbuntuServer |17.10 |nejnovější |ano | 
|Canonical |UbuntuServer |16.04-LTS |nejnovější |ano | 
|Canonical |UbuntuServer |14.04.5-LTS |nejnovější |ano |
|CoreOS |CoreOS |Stable |nejnovější |ano |
|OpenLogic |CentOS |7-CI |nejnovější |náhled |
|RedHat |RHEL |7-RAW-CI |nejnovější |náhled |

Azure Stack aktuálně nepodporuje, zřizování RHEL 7.4 a CentOS 7.4 použití cloud-init.

## <a name="what-is-the-difference-between-cloud-init-and-the-linux-agent-wala"></a>Jaký je rozdíl mezi cloud-init a Agent systému Linux (WALA)?
WALA je agenta specifické pro platformu Azure ke zřizování a konfiguraci virtuálních počítačů a zpracování rozšíření Azure. Vylepšujeme úlohy virtuálních počítačů určených k použití cloud-init místo agenta pro Linux, aby existující cloud-init zákazníkům umožňují použít své aktuální skripty cloud-init.  Pokud jste už investovali do skriptů cloud-init pro konfiguraci operačního systému Linux, existují **žádná další nastavení vyžaduje** je chcete povolit. 

Pokud není zadána rozhraní příkazového řádku Azure `--custom-data` přepínač na čas, WALA zřízení má minimální zřizování parametrů požadovaných pro zřízení virtuálního počítače a dokončení nasazení se výchozí hodnoty virtuálního počítače.  Pokud odkazujete cloud-init `--custom-data` přepnout, cokoli, co je součástí vlastní data (individuální nastavení nebo úplná skript) přepíše výchozí hodnoty WALA. 

WALA konfigurací virtuálních počítačů jsou omezené čas pro práci v rámci maximální doba zřizování virtuálních počítačů.  Konfigurace cloud-init používá pro virtuální počítače nemají omezení času a nebude způsobit selhání tím vypršení časového limitu pro nasazení. 

## <a name="deploying-a-cloud-init-enabled-virtual-machine"></a>Nasazení cloud-init povolené virtuální počítač
Nasazení virtuálního počítače cloud-init povoleno je stejně jednoduché jako odkazující cloud-init, povolený distribuční během nasazení.  Programu distribuce Linuxu potřeba, abyste si k povolení a integrovat do své základní publikovaných imagí Azure cloud-init. Jakmile se ujistíte, že je povoleno cloud-init image, kterou chcete nasadit, můžete použít rozhraní příkazového řádku Azure k nasazení bitové kopie. 

Prvním krokem při nasazení této bitové kopie, je vytvořit skupinu prostředků pomocí [vytvořit skupiny az](/cli/azure/group#az_group_create) příkazu. Skupina prostředků Azure je logický kontejner, ve kterém se nasazují a spravují prostředky Azure. 

Následující příklad vytvoří skupinu prostředků *myResourceGroup* v umístění *eastus*.

```azurecli-interactive 
az group create --name myResourceGroup --location eastus
```
Dalším krokem je vytvoření souboru v aktuálním prostředí s názvem *cloud-init.txt* a vložte do něj následující konfiguraci. V tomto příkladu vytvoření souboru ve službě Cloud Shell není na místním počítači. Můžete použít libovolný editor podle svojí volby. Zadáním příkazu `sensible-editor cloud-init.txt` soubor vytvořte a zobrazte seznam editorů k dispozici. Zvolte #1 použít **nano** editoru. Ujistěte se, že se celý soubor cloud-init zkopíroval správně, zejména první řádek:

```yaml
#cloud-config
package_upgrade: true
packages:
  - httpd
```
Stisknutím klávesy `ctrl-X` ukončíte soubor, zadejte `y` uložte soubor a stisknutím klávesy `enter` potvrďte název souboru při ukončení.

Posledním krokem je vytvoření virtuálního počítače s [az vm vytvořit](/cli/azure/vm#az_vm_create) příkazu. 

Následující příklad vytvoří virtuální počítač s názvem *centos74* a vytvoří klíče SSH, pokud ještě neexistují ve výchozím umístění klíčů. Chcete-li použít konkrétní sadu klíčů, použijte možnost `--ssh-key-value`.  Pomocí parametru `--custom-data` předejte svůj konfigurační soubor cloud-init. Pokud jste konfigurační soubor *cloud-init.txt* uložili mimo aktuální pracovní adresář, zadejte úplnou cestu k němu. Následující příklad vytvoří virtuální počítač s názvem *centos74*:

```azurecli-interactive 
az vm create \
  --resource-group myResourceGroup \
  --name centos74 \
  --image OpenLogic:CentOS:7-CI:latest \
  --custom-data cloud-init.txt \
  --generate-ssh-keys 
```

Po vytvoření virtuálního počítače v Azure CLI zobrazí informace specifické pro vaše nasazení. Poznamenejte si `publicIpAddress`. Tato adresa se používá pro přístup k virtuálnímu počítači.  Pro vytvoření virtuálního počítače, instalace balíčků a spuštění aplikace chvíli trvat. Když vás Azure CLI vrátí na příkazový řádek, na pozadí stále poběží úlohy. Získat přístup přes SSH k virtuálnímu počítači a postupujte podle kroků uvedených v části řešení potíží k zobrazení protokolů cloud-init. 

## <a name="troubleshooting-cloud-init"></a>Řešení potíží s cloud-init
Po zřízení virtuálního počítače, cloud-init spustí prostřednictvím všech modulů a skript definovaný v `--custom-data` konfigurujete virtuální počítač.  Pokud je potřeba vyřešit všechny chyby nebo opomenutí z konfigurace, budete muset vyhledat název modulu (`disk_setup` nebo `runcmd` třeba) v protokolu cloud-init - umístěn v **/var/log/cloud-init.log**.

> [!NOTE]
> Ne každá chyba modulu výsledkem závažná cloud-init celkové Chyba konfigurace. Například použití `runcmd` modulu, pokud skript selže, cloud-init stále oznámí zřizování bylo úspěšné, protože modul runcmd proveden.

Další podrobnosti protokolování cloud-init najdete [dokumentace k cloud-init](http://cloudinit.readthedocs.io/en/latest/topics/logging.html) 

## <a name="next-steps"></a>Další postup
Příklady cloud-init změny konfigurace naleznete v následujících dokumentech:
 
- [Přidání dalších uživatelů Linuxu na virtuální počítač](cloudinit-add-user.md)
- [Spusťte Správce balíčků aktualizovat existující balíčky při prvním spuštění](cloudinit-update-vm.md)
- [Změnit místní název hostitele virtuálního počítače](cloudinit-update-vm-hostname.md) 
- [Instalace balíčku aplikace, aktualizovat konfigurační soubory a klíče pro vložení](tutorial-automate-vm-deployment.md)
