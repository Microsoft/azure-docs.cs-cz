---
title: Testování SAP NetWeaver na virtuálních počítačích Microsoft Azure SUSE Linux | Microsoft Docs
description: Testování SAP NetWeaveru na virtuálních počítačích Microsoft Azure se SUSE Linuxem
services: virtual-machines-linux
documentationcenter: ''
author: hermanndms
manager: jeconnoc
editor: ''
tags: azure-resource-manager
keywords: ''
ms.assetid: 645e358b-3ca1-4d3d-bf70-b0f287498d7a
ms.service: virtual-machines-linux
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure-services
ms.date: 09/14/2017
ms.author: hermannd
ms.openlocfilehash: 9bb13cde07141c576a0ec78c3b48c5a20d6dbb67
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34657651"
---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Provoz SAP NetWeaveru na virtuálních počítačích Microsoft Azure se SUSE Linuxem
Tento článek popisuje různé co je třeba zvážit při spuštěné SAP NetWeaver v Microsoft Azure SUSE Linux virtuální počítače (VM). Od 19. května 2016 je SAP NetWeaver oficiálně podporována na virtuálních počítačích SUSE Linux v Azure. Všechny podrobnosti týkající se verze Linux, verze SAP jádra a další požadavky naleznete v 1928533 Poznámka SAP "SAP aplikace v Azure: podporované produkty a typy virtuálních počítačů Azure".
Další dokumentaci o SAP na virtuální počítače s Linuxem naleznete zde: [pomocí SAP na Linuxové virtuální počítače (VM)](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Tyto informace by měly pomoci vyhnout se některé Potenciální nástrahy.

## <a name="suse-images-on-azure-for-running-sap"></a>SUSE Image na platformě Azure pro spuštění SAP
Pro SAP NetWeaver spuštěné v Azure, použijte SUSE Linux Enterprise Server SLES 12 (SPx) nebo SLES pro SAP – viz poznámka SAP 1928533 také. Speciální SUSE image je v Azure Marketplace ("SLES 11 SP3 pro SAP CAL"), ale bitovou kopii není určen pro obecné použití. Nepoužívejte tuto bitovou kopii, protože je vyhrazený pro [knihovny zařízení cloudu SAP](https://cal.sap.com/) řešení.  

Budete muset použít rozhraní nasazení Azure Resource Manageru pro všechny instalace v Azure. Hledání SUSE SLES obrázky a verze pomocí prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure (CLI), pomocí příkazů vidíte níže. Pak můžete výstup, například k definování bitovou kopii operačního systému v šabloně JSON pro nasazení nového virtuálního počítače SUSE Linux.
Tyto příkazy prostředí PowerShell jsou platné pro prostředí Azure PowerShell verze 1.0.1 a novější.

I když je stále možné používat standardní Image SLES pro SAP instalace, se doporučuje používat nový SLES pro SAP bitové kopie. Tyto Image jsou nyní k dispozici v galerii Azure bitové kopie. Další informace o těchto bitových kopií naleznete v příslušné [stránky Azure Marketplace]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP ) nebo [webové stránky SUSE – nejčastější dotazy o SLES pro SAP]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ ).


* Vyhledejte existující vydavatelů, včetně SUSE:
  
   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```
* Vyhledejte existující nabídky z SUSE:
  
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
* Vyhledejte SUSE SLES nabídky:
  
   ```
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES"
   PS  : Get-AzureRmVMImageSku -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP"
   CLI : azure vm image list-skus westeurope SUSE SLES
   CLI : azure vm image list-skus westeurope SUSE SLES-SAP
   ```
* Podívejte se na konkrétní verzi SLES SKU:
  
   ```
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES" -skus "12-SP2"
   PS  : Get-AzureRmVMImage -Location "West Europe" -Publisher "SUSE" -Offer "SLES-SAP" -skus "12-SP2"
   CLI : azure vm image list westeurope SUSE SLES 12-SP2
   CLI : azure vm image list westeurope SUSE SLES-SAP 12-SP2
   ```

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Instalace WALinuxAgent ve virtuálním počítači SUSE
Agent názvem WALinuxAgent je součástí bitové kopie SLES v Azure Marketplace. Informace o instalaci ručně (například při nahrávání operačním systémem SLES virtuální pevný disk (VHD) z místní) najdete v tématu:

* [openSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [Azure](../../linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP "rozšířené monitorování"
SAP "rozšířené, monitorování" je povinným předpokladem pro SAP spustit v Azure. Zkontrolujte podrobnosti v SAP mějte na paměti, 2191498 "SAP na Linux s Azure: rozšířené monitorování".

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Připojení Azure datové disky na virtuální počítač Azure Linux
Nikdy připojení Azure datové disky na virtuální počítač Azure Linux pomocí ID zařízení. Místo toho použijte identifikátor (UUID). Pokud například použijete grafické nástroje pro připojení Azure datových disků, buďte opatrní. Zkontrolujte položky v /etc/fstab.

Problém s ID zařízení je, že mohou změnit, a potom virtuální počítač Azure může přečnívat v procesu spouštění. Chcete-li zmírnit problém, můžete přidat parametr nofail v /etc/fstab. Ale pozor s nofail vzhledem k tomu, že aplikace může použít přípojného bodu jako před a které může zapisovat do systému souborů kořenové v případě, že externí Azure datový disk nebyl připojené během spuštění.

Jedinou výjimku připojení prostřednictvím UUID je připojení disku operačního systému při odstraňování potíží, jak je popsáno v části, které budou následovat.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Řešení potíží s SUSE virtuální počítač, který už není dostupný
Můžou nastat situace, kdy SUSE virtuálního počítače na platformě Azure přestane reagovat v procesu spouštění (například s chybu týkající se připojení disků). Tento problém můžete ověřit pomocí funkce diagnostiky spouštění pro virtuální počítače Azure v2 na portálu Azure. Další informace najdete v tématu [spouštění diagnostiky](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Jeden způsob, jak problém vyřešit je připojit disk operačního systému z poškozeného virtuálního počítače na jiný virtuální počítač SUSE v Azure. Proveďte potřebné změny, jako jsou úpravy /etc/fstab nebo odebíráním pravidel udev sítě, jak je popsáno v následující části.

Je jednou z důležitých věcí vzít v úvahu. Nasazení několik virtuálních počítačů SUSE ze stejné image Azure Marketplace, (například SLES 11 SP4) způsobí, že chcete vždy připojit pomocí stejné UUID disk operačního systému. Proto pomocí identifikátoru UUID připojit disk operačního systému z různých virtuálního počítače, který byl nasazen pomocí stejné výsledky image Azure Marketplace v dva identické identifikátory UUID. Dva identické příčina identifikátory UUID virtuální počítač používá pro řešení potíží, spouštění z disku operačního systému připojený a poškozeného místo původní disk operačního systému.

Aby nedocházelo k problémům dvěma způsoby:

* Použijte jinou bitovou kopii Azure Marketplace pro řešení potíží virtuální počítač (například SLES 11 SPx místo SLES 12).
* Nemáte připojení poškozeného disku operačního systému z jiného virtuálního počítače pomocí UUID – použití něco jiného.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Odesílání SUSE virtuálního počítače z místního do Azure
Popis uvedeného postupu nahrajte SUSE virtuálního počítače z místního do Azure najdete v tématu [Příprava virtuálního počítače, SLES nebo openSUSE pro Azure](../../linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pokud chcete nahrát virtuální počítač bez krok deprovision na konci (třeba chcete zachovat existující instalaci SAP, stejně jako název hostitele), zkontrolujte následující položky:

* Ujistěte se, že je připojený disk operačního systému pomocí UUID a není ID zařízení. Změna na UUID právě v /etc/fstab není dost pro disk operačního systému. Navíc nezapomeňte přizpůsobit spouštěcí zavaděč prostřednictvím YaST nebo úpravou /boot/grub/menu.lst.
* Pokud používáte formát VHDX pro disk operačního systému SUSE a nahrát do Azure ho převést na virtuální pevný disk, je pravděpodobné, že síťové zařízení změny z eth0 eth1. Abyste zabránili problémům při v Azure se spouští později, změnit zpátky na eth0 jak je popsáno v [opravě eth0 v klonovat SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Kromě co je popsána v článku doporučujeme, abyste tento soubor odebrat:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Můžete také nainstalovat Azure Linux Agent (příkaz waagent) můžete předejít problémům, dokud nejsou k dispozici několik síťových adaptérů.

## <a name="deploying-a-suse-vm-on-azure"></a>Nasazení virtuálního počítače SUSE v Azure
Nové virtuální počítače SUSE musí vytvořit pomocí šablony soubory JSON v novém modelu Azure Resource Manager. Po vytvoření souboru šablony JSON, můžete nasadit virtuální počítač pomocí následujícího příkazu příkazového řádku jako alternativu k prostředí PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Další informace o souborech JSON šablony najdete v tématu [šablon pro tvorbu Azure Resource Manageru](../../../resource-group-authoring-templates.md) a [šablony Azure rychlý Start](https://azure.microsoft.com/documentation/templates/).

Další informace o rozhraní příkazového řádku a Azure Resource Manager najdete v tématu [pomocí rozhraní příkazového řádku Azure CLI pro Mac, Linux a Windows pomocí Azure Resource Manageru](../../../xplat-cli-azure-resource-manager.md).

## <a name="sap-license-and-hardware-key"></a>SAP klíč licence a hardwaru
Na oficiální certifikaci SAP Azure nového mechanismu přinesl k výpočtu SAP hardwarový klíč, který se používá pro SAP licence. SAP jádra musel být upraveny, chcete-li použít nové algoritmu. Dřívější verze SAP jádra pro Linux neobsahuje tato změna kódu. Proto v určitých situacích (třeba virtuální počítač Azure Změna velikosti), klíč hardwaru SAP změnit a SAP licence, které se již nebude platný. Řešení je k dispozici s novější SAP Linux jádra.  Podrobné opravy jádra SAP jsou popsány v Poznámka SAP 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>Balíček sapconf SUSE / přizpůsobená adm
SUSE poskytuje balíček s názvem "sapconf", který spravuje sadu nastavení specifických pro SAP. Další informace o jaké tento balíček a jak nainstalovat a použít ho najdete v tématu: [používání sapconf k přípravě SUSE Linux Enterprise Server pro systémy SAP](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) a [co je sapconf nebo postup přípravy operačního systému SUSE Linux Enterprise Server pro systémy SAP? ](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

Do té doby je nový nástroj, který nahradí 'sapconf - přizpůsobená adm'. Jeden můžete najít další informace o tomto nástroji následující dva odkazy:

- SLES dokumentaci o 'přizpůsobená adm' profil sap-hana lze nalézt [sem](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_s4s_configure_sapconf.html) 

- Ladění systémy pro SAP úloh s 'přizpůsobená adm' - naleznete [sem](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) v kapitole 6.2

## <a name="nfs-share-in-distributed-sap-installations"></a>V distribuované instalaci SAP sdílených složek NFS
Pokud máte distribuované instalaci – například, ve které chcete instalovat databáze a aplikační servery SAP v samostatné virtuální počítače – můžete sdílet adresáři /sapmnt prostřednictvím systému souborů NFS. Pokud máte problémy s kroky instalace po vytvoření sdílené složky NFS pro /sapmnt, zkontrolujte, pokud je "no_root_squash" nastavte pro sdílenou složku.

## <a name="logical-volumes"></a>Logické svazky
V minulosti Pokud logické značný zapotřebí na více disků dat Azure (například pro databázi SAP), se doporučuje použít nástroj pro správu diskového pole Raid MDADM, protože Linux logické svazku Manager (LVM) nebyla ověřena plně ještě v Azure. Zjistěte, jak nastavit Linux RAID na platformě Azure pomocí mdadm, najdete v tématu [konfigurace softwaru diskového pole RAID v systému Linux](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Od začátku může 2016 se do té doby, Správce logických svazku Linux je plně podporovaný v Azure a lze použít jako alternativu k MDADM. Další informace o LVM v Azure najdete v tématu:  
[Konfigurace LVM na virtuální počítač s Linuxem v Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-suse-repository"></a>Úložiště Azure SUSE
Pokud máte potíže s přístupem k úložišti Azure SUSE standardní, můžete ho resetovat příkaz. Takovým problémům může dojít, pokud vytvoříte privátní bitové kopie operačního systému v jedné oblasti Azure a poté zkopírujte bitovou kopii do jiné oblasti Azure chcete nasadit nové virtuální počítače, které jsou založeny na tuto privátní bitovou kopii operačního systému. Spusťte následující příkaz ve virtuálním počítači:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Gnome plochy
Pokud chcete používat plochu Gnome nainstalovat celý systém ukázku SAP uvnitř jeden virtuální počítač – včetně grafickým uživatelským rozhraním SAP prohlížeče a konzoly pro správu SAP – použijte k jeho instalaci bitové kopie Azure SLES Tento pomocný:

   Pro SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Pro SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>SAP podpora pro Oracle v systému Linux v cloudu
Je omezení podpory z databáze Oracle v systému Linux ve virtualizovaných prostředích. I když toto omezení podpory není tématu specifické pro Azure, je důležité znát. SAP nepodporuje Oracle na SUSE nebo Red Hat ve veřejném cloudu, jako je například Azure. Do té doby spuštění, Oracle DB v Azure je plně podporuje SAP na Oracle Linux (viz poznámka 1928533 SAP). Pokud jsou vyžadovány další kombinace, obraťte se přímo na Oracle.

