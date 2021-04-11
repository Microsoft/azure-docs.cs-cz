---
title: Upgrade operačního systému pro SAP HANA v Azure (velké instance) | Microsoft Docs
description: Provedení upgradu operačního systému pro SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-sap
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 97c07f010fad6c12424b1684d4ff5e12c7cac3ce
ms.sourcegitcommit: b0557848d0ad9b74bf293217862525d08fe0fc1d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/07/2021
ms.locfileid: "106553239"
---
# <a name="operating-system-upgrade"></a>Upgrade operačního systému
Tento dokument popisuje podrobnosti o upgradech operačního systému ve velkých instancích HANA.

>[!NOTE]
>Upgrade operačního systému je zodpovědností zákazníka. Microsoft Operations support vám může v klíčových oblastech sledovat během upgradu. Před plánováním upgradu byste se měli obrátit na dodavatele operačního systému.

Během zřizování jednotky HLI nainstaluje Microsoft Operations Team operační systém.
V průběhu času je nutné zachovat operační systém (například opravy, ladění, upgradování atd.) na HLI jednotce.

Před prováděním podstatných změn v operačním systému (například upgrade SP1 na verzi SP2) se poradíte s provozním týmem Microsoftu otevřením lístku podpory, který vám podá informace.

Zahrnout do lístku:

* ID předplatného HLI
* Název vašeho serveru.
* Úroveň opravy, kterou plánujete použít.
* Datum, kdy plánujete tuto změnu. 

Doporučujeme, abyste tento lístek otevřeli aspoň jeden týden před požadovaným upgradem, který týmu opration ví o požadované verzi firmwaru.

Pro matrici podpory různých verzí SAP HANA s různými verzemi systému Linux najdete informace v tématu [SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).

## <a name="known-issues"></a>Známé problémy

V následující části najdete několik běžných známých problémů během upgradu:
- U SKU třídy b typu SKU se Software Foundation software (SFS) odebere po upgradu operačního systému. Po upgradu operačního systému je nutné přeinstalovat kompatibilní SFS.
- Ovladače karet Ethernet (ENIC a FNIC) se vrátily zpátky na starší verzi. Po upgradu musíte přeinstalovat kompatibilní verze ovladačů.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>SAP HANA velká instance (Type I) doporučená konfigurace

Konfigurace operačního systému se může v průběhu času v důsledku oprav, upgradů systému a změn provedených zákazníky odsílat od doporučeného nastavení. Kromě toho společnost Microsoft identifikuje aktualizace potřebné pro stávající systémy, aby bylo zajištěno, že jsou optimálně nakonfigurované pro dosažení optimálního výkonu a odolnosti. Následující pokyny popisují doporučení, která řeší výkon sítě, stabilitu systému a optimální výkon HANA.

### <a name="compatible-enicfnic-driver-versions"></a>Kompatibilní verze ovladače eNIC/fNIC
  Aby bylo zajištěno správné fungování sítě a stabilita systému, doporučujeme, abyste zajistili, že jsou v následující tabulce kompatibility nainstalovány odpovídající verze ovladačů eNIC a fNIC specifické pro operační systém. Servery jsou doručovány zákazníkům s kompatibilními verzemi. V některých případech se během oprav operačního systému/jádra můžou ovladače vrátit zpátky na výchozí verze ovladačů. Zajistěte, aby příslušná verze ovladače běžela po operacích s operačním systémem nebo opravou jádra.
       
      
  |  Dodavatel operačního systému    |  Verze balíčku operačního systému     |  Verze firmwaru  |  Ovladač eNIC |  Ovladač fNIC | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   SuSE        |  SLES 12 SP2            |   3.1.3 h           |  2.3.0.40    |   1.6.0.34   |
  |   SuSE        |  SLES 12 SP3            |   3.1.3 h           |  2.3.0.44    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP2            |   3.2.3 i           |  2.3.0.45    |   1.6.0.37   |
  |   SuSE        |  SLES 12 SP3            |   3.2.3 i           |  2.3.0.43    |   1.6.0.36   |
  |   SuSE        |  SLES 12 SP4            |   3.2.3 i           |  4.0.0.6     |   2.0.0.60   |
  |   SuSE        |  SLES 12 SP5            |   4.1.1 b           |  4.0.0.6     |   2.0.0.59   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1 b           |  4.0.0.8     |   2.0.0.60   |
  |   SuSE        |  SLES 15 SP1            |   4.1.1 b           |  4.0.0.8     |   2.0.0.60   |
  |   Red Hat     |  RHEL 7,2               |   3.1.3 h           |  2.3.0.39    |   1.6.0.34   |
  |   Red Hat     |  RHEL 7,6               |   3.2.3 i           |  3.1.137.5   |   2.0.0.50   |
  |   Red Hat     |  RHEL 7,6               |   4.1.1 b           |  4.0.0.8     |   2.0.0.60   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Příkazy pro upgrade ovladače a vyčištění starých balíčků ot./min.

#### <a name="command-to-check-existing-installed-drivers"></a>Příkaz pro kontrolu existujících nainstalovaných ovladačů
```
rpm -qa | grep enic/fnic 
```
#### <a name="delete-existing-enicfnic-rpm"></a>Odstranit existující eNIC/fNIC ot./min.
```
rpm -e <old-rpm-package>
```
#### <a name="install-the-recommended-enicfnic-driver-packages"></a>Nainstalujte Doporučené balíčky ovladačů eNIC/fNIC.
```
rpm -ivh <enic/fnic.rpm> 
```

#### <a name="commands-to-confirm-the-installation"></a>Příkazy pro potvrzení instalace
```
modinfo enic
modinfo fnic
```

#### <a name="steps-for-enicfnic-drivers-installation-during-os-upgrade"></a>Kroky při instalaci ovladačů eNIC/fNIC během upgradu operačního systému

* Upgradovat verzi operačního systému
* Odebrat staré balíčky ot./min.
* Nainstalovat kompatibilní ovladače eNIC/fNIC podle nainstalované verze operačního systému
* Restartovat systém
* Po restartování ověřte verzi eNIC/fNIC.


### <a name="suse-hlis-grub-update-failure"></a>Selhání aktualizace SuSE HLIs GRUB
SAP ve velkých instancích Azure HANA (typ I) může být po upgradu v nespouštěcím stavu. Následující postup opravuje tento problém.
#### <a name="execution-steps"></a>Kroky provedení


*   Provést `multipath -ll` příkaz.
*   Získejte ID logické jednotky (LUN), jejíž velikost je přibližně 50G, nebo použijte příkaz: `fdisk -l | grep mapper`
*   Aktualizuje `/etc/default/grub_installdevice` soubor řádek `/dev/mapper/<LUN ID>` . Příklad:/dev/Mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>ID logické jednotky (LUN) se liší od serveru k serveru.


### <a name="disable-edac"></a>Zakázat EDAC 
   Modul detekce chyb a opravy (EDAC) pomáhá zjišťovat a opravovat chyby paměti. Základní hardware pro SAP HANA ve velkých instancích Azure (Type I) však již provádí stejnou funkci. Stejné funkce, které jsou povolené na úrovni hardwaru a operačního systému (OS), můžou způsobit konflikty a můžou vést k příležitostnému a neplánovanému vypnutí serveru. Proto se doporučuje modul zakázat z operačního systému.

#### <a name="execution-steps"></a>Kroky provedení

* Zkontroluje, jestli je povolený modul EDAC. Pokud se výstup vrátí v níže uvedeném příkazu, znamená to, že je modul povolený. 
```
lsmod | grep -i edac 
```
* Zakázat moduly připojením následujících řádků k souboru `/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
Aby se změny projevily, je potřeba restartovat počítač. Spusťte `lsmod` příkaz a ověřte, že modul není přítomen ve výstupu.

### <a name="kernel-parameters"></a>Parametry jádra
   Ujistěte se, že je použito správné nastavení pro `transparent_hugepage` , `numa_balancing` , a `processor.max_cstate` `ignore_ce` `intel_idle.max_cstate` .

* intel_idle intel_idle.max_cstate = 1
* processor.max_cstate = 1
* transparent_hugepage = nikdy
* numa_balancing = zakázat
* MCE = ignore_ce

#### <a name="execution-steps"></a>Kroky provedení

* Přidat tyto parametry do `GRB_CMDLINE_LINUX` řádku v souboru `/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Vytvořte nový soubor grub.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Restartujte systém.


## <a name="next-steps"></a>Další kroky
- Přečtěte si téma [zálohování a obnovení](hana-overview-high-availability-disaster-recovery.md) pro třídu SKU typu zálohování operačního systému.
- Přečtěte si [zálohování operačního systému pro položky typu II SKU revize 3](os-backup-type-ii-skus.md) pro třídu SKU typu II.
