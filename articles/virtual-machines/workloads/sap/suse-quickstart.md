---
title: Testování SAP Netweaveru na virtuálních počítačích Microsoft Azure operačním systémem SUSE Linux | Dokumentace Microsoftu
description: Testování SAP NetWeaveru na virtuálních počítačích Microsoft Azure se SUSE Linuxem
services: virtual-machines-linux
documentationcenter: ''
author: msjuergent
manager: patfilot
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
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 032ab2a221f64d01af25056a4eff3ee3384de0c3
ms.sourcegitcommit: 33091f0ecf6d79d434fa90e76d11af48fd7ed16d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/09/2019
ms.locfileid: "54157220"
---
# <a name="running-sap-netweaver-on-microsoft-azure-suse-linux-vms"></a>Provoz SAP NetWeaveru na virtuálních počítačích Microsoft Azure se SUSE Linuxem
Tento článek popisuje různé věci k uvážení při používáte SAP NetWeaver na virtuálních počítačích Microsoft Azure s operačním systémem SUSE Linuxem (VM). Od 19. května 2016 se oficiálně podporuje SAP NetWeaver na virtuálních počítačích s operačním systémem SUSE Linux v Azure. Všechny podrobnosti týkající se verze Linuxu, verze jádra SAP a další požadavky najdete v SAP 1928533 poznámka "aplikace SAP v Azure: Podporované produkty a typy virtuálních počítačů Azure ".
Další dokumentaci k SAPU ve virtuálních počítačích s Linuxem najdete tady: [Použití SAP na virtuálních počítačích s Linuxem (VM)](get-started.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Tyto informace by měly umožňuje vyhnout se některé potenciálních problémech.

## <a name="suse-images-on-azure-for-running-sap"></a>Image SUSE na Azure pro spuštění SAP
Pro spuštění SAP NetWeaver v Azure, použití operačního systému SUSE Linux Enterprise Server SLES 12 (SPx) nebo SLES pro SAP – viz poznámka SAP 1928533 také. Je speciální image operačního systému SUSE na webu Azure Marketplace ("SLES 11 SP3 pro SAP CAL"), ale image není určený pro obecné použití. Nepoužívejte tuto bitovou kopii, protože je vyhrazený pro [SAP Cloud Appliance Library](https://cal.sap.com/) řešení.  

Budete muset použít architektura pro nasazení Azure Resource Manageru pro všechna zařízení v Azure. K vyhledání imagí SUSE SLES a verzí s využitím Azure Powershellu nebo rozhraní příkazového řádku Azure (CLI), pomocí příkazů níže. Pak můžete výstup, například definovat image operačního systému v šabloně JSON pro nasazení nového virtuálního počítače s operačním systémem SUSE Linuxem.
Tyto příkazy prostředí PowerShell jsou platné pro prostředí Azure PowerShell verze 1.0.1 a novější.

I když je stále možné použít standardní imagí SLES pro SAP instalace, doporučuje se používat nové SLES pro SAP Image. Tyto Image jsou nyní dostupné v galerii imagí Azure. Další informace o těchto imagí najdete u odpovídajícího [stránku na webu Azure Marketplace]( https://azuremarketplace.microsoft.com/en-us/marketplace/apps/SUSE.SLES-SAP ) nebo [webové stránky SUSE – nejčastější dotazy o SLES pro SAP]( https://www.suse.com/products/sles-for-sap/frequently-asked-questions/ ).


* Hledejte existujících vydavatelů, včetně SUSE:
  
   ```
   PS  : Get-AzureRmVMImagePublisher -Location "West Europe"  | where-object { $_.publishername -like "*US*"  }
   CLI : azure vm image list-publishers westeurope | grep "US"
   ```
* Vyhledejte stávající nabídky od společnosti SUSE:
  
   ```
   PS  : Get-AzureRmVMImageOffer -Location "West Europe" -Publisher "SUSE"
   CLI : azure vm image list-offers westeurope SUSE
   ```
* Vyhledejte nabídky SUSE SLES:
  
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

## <a name="installing-walinuxagent-in-a-suse-vm"></a>Instalace WALinuxAgent virtuálního počítače s operačním systémem SUSE
Agentovi nazvaném WALinuxAgent je součástí SLES imagí v Tržišti Azure Marketplace. Informace o instalaci ručně (například při nahrávání operačním systémem SLES virtuální pevný disk (VHD) z místních) najdete tady:

* [openSUSE](http://software.opensuse.org/package/WALinuxAgent)
* [Azure](../../linux/endorsed-distros.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [SUSE](https://www.suse.com/communities/blog/suse-linux-enterprise-server-configuration-for-windows-azure/)

## <a name="sap-enhanced-monitoring"></a>SAP "enhanced monitoring"
SAP "rozšířené, monitorování" je povinné předpokladem pro spuštění SAP v Azure. Zkontrolujte podrobnosti v SAP mějte na paměti 2191498 "SAP v Linuxu se službou Azure: Rozšířené monitorování".

## <a name="attaching-azure-data-disks-to-an-azure-linux-vm"></a>Připojení Azure datové disky na virtuálním počítači Azure s Linuxem
Nikdy připojení Azure datové disky na virtuálním počítači Azure s Linuxem s použitím ID zařízení. Místo toho použijte univerzálně jedinečným identifikátorem (UUID). Buďte opatrní při použití grafické nástroje pro připojení Azure datových disků, např. Zkontrolujte položky v /etc/fstab.

Problém s ID zařízení je, že může změnit, a pak virtuální počítač Azure může reagovat v procesu spouštění. Jak tyto problémy zmírnit, můžete přidat parametr nofail v /etc/fstab. Ale buďte opatrní s nofail vzhledem k tomu, že aplikace může používat přípojného bodu jako před a může zapisovat do systému souborů kořenové v případě, že při spuštění nebyl připojený disk s externí data Azure.

K jediné výjimce připojení přes UUID je disk s operačním systémem pro odstraňování potíží, připojení, jak je popsáno v části, které následují.

## <a name="troubleshooting-a-suse-vm-that-isnt-accessible-anymore"></a>Řešení potíží s operačním systémem SUSE virtuálního počítače, který už není dostupný
Můžou nastat situace, kdy virtuálního počítače v Azure s operačním systémem SUSE přestane reagovat v procesu spouštění (třeba index Mei chybu související s připojením disků). Tento problém můžete ověřit pomocí funkce diagnostiky spouštění pro Azure Virtual Machines v2 na webu Azure Portal. Další informace najdete v tématu [Diagnostika spouštění](https://azure.microsoft.com/blog/boot-diagnostics-for-virtual-machines-v2/).

Připojit disk s operačním systémem k jinému virtuálnímu počítači operačním systémem SUSE na Azure z poškozeného virtuálního počítače je jedním ze způsobů řešení problému. Pak proveďte potřebné změny, jako jsou úpravy /etc/fstab nebo odebíráním pravidel udev sítě, jak je popsáno v další části.

Existuje jedna důležité vzít v úvahu. Nasazení několika virtuálních počítačích SUSE ze stejné image Azure Marketplace (například SLES 11 SP4) způsobí, že vždy potřeba připojit ve stejný identifikátor UUID disku s operačním systémem. Proto pomocí identifikátoru UUID připojit disk s operačním systémem z různých virtuálních počítačů, který je nasazený pomocí stejných výsledků image Azure Marketplace v dva stejné identifikátory UUID. Dva stejné identifikátory UUID příčina virtuálního počítače používají k řešení potíží, spouštění z disku s operačním systémem připojené a poškozená namísto původní disk s operačním systémem.

Existují dva způsoby, jak se vyhnout problémům:

* Použijte jinou image Azure Marketplace pro řešení potíží virtuální počítače (například SLES 11 SPx místo SLES 12).
* Nepřipojovat poškozený disk s operačním systémem z jiného virtuálního počítače s použitím identifikátoru UUID – použít něco jiného.

## <a name="uploading-a-suse-vm-from-on-premises-to-azure"></a>Nahrává se SUSE virtuálního počítače z místního do Azure
Popis postupu nahrajte SUSE virtuálního počítače z místního Azure najdete v tématu [Příprava virtuálního počítače se SLES nebo openSUSE pro Azure](../../linux/suse-create-upload-vhd.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pokud chcete uložit virtuální počítač bez zrušení zřízení kroku na konci (třeba kvůli zachování existující instalace SAP, stejně jako název hostitele), zkontrolujte následující položky:

* Ujistěte se, že je připojený disk s operačním systémem pomocí UUID a není ID zařízení. Změna na UUID metody just-in /etc/fstab není dostatečné pro disk s operačním systémem. Také nezapomeňte si přizpůsobit spouštěcí zavaděč prostřednictvím YaST nebo úpravou /boot/grub/menu.lst.
* Pokud používáte formát VHDX pro disk s operačním systémem SUSE a pro jeho odeslání do Azure ho převést na virtuální pevný disk, je pravděpodobné, že síťové zařízení se změní z eth0 eth1. Aby nedocházelo k problémům při spouštění je už v Azure, později, změnit zpět na eth0 jak je popsáno v [stanovení eth0 v klonovat SLES 11 VMware](https://dartron.wordpress.com/2013/09/27/fixing-eth1-in-cloned-sles-11-vmware/).

Kromě co je popsáno v článku doporučujeme tento soubor odebrat:

   /lib/udev/rules.d/75-persistent-net-generator.rules

Můžete také nainstalovat agenta Azure Linux (waagent) můžete předejít problémům, za předpokladu, nejsou k dispozici několik síťových karet.

## <a name="deploying-a-suse-vm-on-azure"></a>Nasazení virtuálního počítače v Azure s operačním systémem SUSE
Třeba vytvořit nové virtuální počítače SUSE pomocí souborů šablon JSON v novém modelu Azure Resource Manageru. Po vytvoření souboru JSON šablony můžete nasadit virtuální počítač pomocí následujícího příkazu rozhraní příkazového řádku jako alternativu k prostředí PowerShell:

   ```
   azure group deployment create "<deployment name>" -g "<resource group name>" --template-file "<../../filename.json>"

   ```
Další informace o souborech šablon JSON najdete v tématu [šablon pro vytváření Azure Resource Manageru](../../../resource-group-authoring-templates.md) a [šablony rychlý start Azure](https://azure.microsoft.com/documentation/templates/).

Další informace o Azure classic CLI a Azure Resource Manageru najdete v tématu [pomocí Azure classic CLI pro Mac, Linux a Windows pomocí Azure Resource Manageru](../../../xplat-cli-azure-resource-manager.md).

## <a name="sap-license-and-hardware-key"></a>Klíč licence a hardware a SAP
Oficiální certifikaci SAP Azure byla zavedena nový mechanismus pro výpočet SAP hardwarový klíč, který se používá pro licence SAP. Jádra SAP musel být upraveny, aby využívání nový algoritmus. Dřívější verze SAP jádra pro Linux neobsahuje tuto změnu kódu. Proto v určitých situacích (třeba virtuální počítač Azure změnou velikosti), změnit hardwarový klíč SAP a se už nebude platná licence SAP. Řešení je součástí novější SAP Linuxových jádrech.  Podrobné jádra opravy SAP jsou popsány v Poznámka SAP 1928533.

## <a name="suse-sapconf-package--tuned-adm"></a>Balíček operačního systému SUSE sapconf / vyladěný adm
SUSE obsahuje balíček s názvem "sapconf", který spravuje sadu nastavení specifická pro SAP. Další informace o činnosti tohoto balíčku a jak nainstalovat a používat ho naleznete v tématu:  [Příprava SUSE Linux Enterprise Server ke spouštění systémů SAP pomocí sapconf](https://www.suse.com/communities/blog/using-sapconf-to-prepare-suse-linux-enterprise-server-to-run-sap-systems/) a [co sapconf nebo jak připravit operačním systémem SUSE Linux Enterprise Server pro systémy SAP?](http://scn.sap.com/community/linux/blog/2014/03/31/what-is-sapconf-or-how-to-prepare-a-suse-linux-enterprise-server-for-running-sap-systems).

Do té doby je nový nástroj, který nahradí "sapconf – vyladěný adm". Jeden najdete další informace o tomto nástroji na dva odkazy:

- SLES dokumentaci o profilu "vyladěný adm" sap-hana najdete [zde](https://www.suse.com/documentation/sles-for-sap-12/book_s4s/data/sec_saptune.html) 

- Ladění systémy pro úlohy SAP s "vyladěný adm" - najdete [tady](https://www.suse.com/documentation/sles-for-sap-12/pdfdoc/book_s4s/book_s4s.pdf) v kapitole 6.2

## <a name="nfs-share-in-distributed-sap-installations"></a>V distribuované instalace SAP sdílených složek NFS
Pokud máte distribuované instalaci – například pokud chcete nainstalovat databázi a aplikační servery SAP v samostatné virtuální počítače – můžete sdílet /sapmnt adresáře prostřednictvím systému souborů NFS. Pokud máte problémy s instalační postup, který po vytvoření sdílené složky NFS pro /sapmnt, zaškrtněte, pokud chcete zjistit, zda je "no_root_squash" nastavení sdílené složky.

## <a name="logical-volumes"></a>Logické svazky
V minulosti podle potřeby jeden logický obrovských na několik disků Azure data (například pro databázi SAP), se doporučuje použít nástroj pro správu Raid MDADM od Linuxu správce logické svazků (LVM) nebyl ověřen plně ještě v Azure. Informace o nastavení pole RAID Linux v Azure pomocí mdadm najdete v tématu [konfigurace softwarového pole RAID v Linuxu](../../linux/configure-raid.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json). Od začátku. května 2016 do té doby, Správce logických svazků systému Linux je plně podporované v Azure a může sloužit jako alternativu k MDADM. Další informace týkající se LVM v Azure přečtěte si:  
[Konfigurace LVM na virtuální počítač s Linuxem v Azure](../../linux/configure-lvm.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="azure-suse-repository"></a>Úložiště Azure SUSE
Pokud máte potíže s přístupem do standardního úložiště Azure SUSE, můžete použít příkaz resetovat ho. Tyto problémy může dojít, pokud vytvoříte privátní image operačního systému v jedné oblasti Azure a potom zkopírovat image do jiné oblasti Azure k nasazení nových virtuálních počítačů, které jsou založeny na tento privátní image operačního systému. Spusťte následující příkaz v tomto virtuálním počítači:

   ```
   service guestregister restart
   ```

## <a name="gnome-desktop"></a>Gnome desktop
Pokud chcete použít k instalaci celý systém SAP ukázku uvnitř jednoho virtuálního počítače – včetně grafickým uživatelským rozhraním SAP Gnome desktop prohlížeče a konzoly pro správu SAP – použijte k instalaci na imagích Azure SLES Tento pomocný parametr:

   Pro SLES 11:

   ```
   zypper in -t pattern gnome
   ```

   Pro SLES 12:

   ```
   zypper in -t pattern gnome-basic
   ```

## <a name="sap-support-for-oracle-on-linux-in-the-cloud"></a>Podpora SAP pro Oracle v Linuxu v cloudu
Existuje omezení podpory od Oraclu v Linuxu ve virtualizovaných prostředích. I když toto omezení podpory se nevztahuje tématu týkající se Azure, je důležité pochopit. SAP nepodporuje Oracle na SUSE nebo Red Hat ve veřejném cloudu, jako je Azure. Do té doby spuštění, databáze Oracle v Azure je plně podporováno SAP v Oracle Linuxu (viz poznámka 1928533 SAP). Pokud potřebujete další kombinace, kontaktujte přímo Oracle.

