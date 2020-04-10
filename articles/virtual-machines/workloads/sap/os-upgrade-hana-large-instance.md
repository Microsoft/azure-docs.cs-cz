---
title: Upgrade operačního systému pro SAP HANA v Azure (velké instance)| Dokumenty společnosti Microsoft
description: Provedení upgradu operačního systému pro SAP HANA v Azure (velké instance)
services: virtual-machines-linux
documentationcenter: ''
author: saghorpa
manager: juergent
editor: ''
ms.service: virtual-machines-linux
ms.topic: article
ms.tgt_pltfrm: vm-linux
ms.workload: infrastructure
ms.date: 07/04/2019
ms.author: juergent
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 7fea0f74a90bc7b786a9b302d6282f9fb70e5412
ms.sourcegitcommit: a53fe6e9e4a4c153e9ac1a93e9335f8cf762c604
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/09/2020
ms.locfileid: "80991479"
---
# <a name="operating-system-upgrade"></a>Upgrade operačního systému
Tento dokument popisuje podrobnosti o upgradech operačního systému na velkých instancích HANA.

>[!NOTE]
>Upgrade operačního systému je odpovědností zákazníka, podpora operací společnosti Microsoft vás může vést ke klíčovým oblastem, které si můžete během upgradu dát pozor. Měli byste se poradit s dodavatelem operačního systému také před plánováním upgradu.

Během zřizování jednotek HLI operační tým společnosti Microsoft nainstaluje operační systém.
V průběhu času jste povinni udržovat operační systém (Příklad: Oprava, ladění, upgrade atd.) na jednotce HLI.

Před prováděním zásadních změn operačního systému (například upgrade aktualizace SP1 na aktualizací SP2) je třeba kontaktovat tým Microsoft Operations otevřením lístku podpory a konzultovat.

Zahrnout do letenky:

* Vaše ID předplatného HLI.
* Název serveru.
* Úroveň opravy, kterou plánujete použít.
* Datum, kdy plánujete tuto změnu. 

Doporučujeme otevřít tento lístek alespoň jeden týden před žádoucí datum upgradu z důvodu, že operační tým kontroluje, zda upgrade firmwaru bude nutné na serveru.


Matice podpory různých verzí SAP HANA s různými verzemi Linuxu najdete v [tématu SAP Note #2235581](https://launchpad.support.sap.com/#/notes/2235581).


## <a name="known-issues"></a>Známé problémy

Následuje několik běžných známých problémů během upgradu:
- U skladové položky SKU třídy SKU je software pro základy softwaru (SFS) odebrán po upgradu operačního systému. Po upgradu operačního systému je třeba přeinstalovat kompatibilní systém SFS.
- Ovladače ethernetových karet (ENIC a FNIC) se vrátily ke starší verzi. Po upgradu je třeba přeinstalovat kompatibilní verzi ovladačů.

## <a name="sap-hana-large-instance-type-i-recommended-configuration"></a>Doporučená konfigurace velké instance SAP HANA (typ I)

Konfigurace operačního systému může v průběhu času přejít od doporučených nastavení z důvodu oprav, upgradů systému a změn provedených zákazníky. Společnost Microsoft navíc identifikuje aktualizace potřebné pro stávající systémy, aby bylo zajištěno, že jsou optimálně nakonfigurovány pro nejlepší výkon a odolnost proti chybám. Následující pokyny popisují doporučení, která řeší výkon sítě, stabilitu systému a optimální výkon HANA.

### <a name="compatible-enicfnic-driver-versions"></a>Kompatibilní verze ovladačů eNIC/fNIC
  Aby byl zajištěn správný výkon sítě a stabilita systému, doporučuje me zajistit, aby byla nainstalována příslušná verze ovladačů eNIC a fNIC specifická pro operační systém, jak je znázorněno v následující tabulce kompatibility. Servery jsou dodávány zákazníkům s kompatibilními verzemi. Všimněte si, že v některých případech během opravy OS/Kernel mohou být ovladače vráceny zpět na výchozí verze ovladačů. Ujistěte se, že příslušná verze ovladače je spuštěna po operacích opravy Operačního systému /jádra.
       
      
  |  Dodavatel operačního sazí    |  Verze balíčku operačního systému     |  Verze firmwaru  |  ovladač eNIC |  fNIC Ovladač | 
  |---------------|-------------------------|--------------------|--------------|--------------|
  |   Suse        |  SLES 12 SP2            |   3.1.3h           |  2.3.0.40    |   1.6.0.34   |
  |   Suse        |  SLES 12 SP3            |   3.1.3h           |  2.3.0.44    |   1.6.0.36   |
  |   Suse        |  SLES 12 SP4            |   3.2.3b           |  2.3.0.47    |   2.0.0.54   |
  |   Red Hat     |  RHEL 7,2               |   3.1.3h           |  2.3.0.39    |   1.6.0.34   |
 

### <a name="commands-for-driver-upgrade-and-to-clean-old-rpm-packages"></a>Příkazy pro upgrade ovladače a čištění starých balíčků rpm
```
rpm -U driverpackage.rpm
rpm -e olddriverpackage.rpm
```

#### <a name="commands-to-confirm"></a>Příkazy k potvrzení
```
modinfo enic
modinfo fnic
```

### <a name="suse-hlis-grub-update-failure"></a>SuSE HLIs GRUB selhání aktualizace
SAP na Azure HANA velké instance (typ I) může být ve stavu bez spuštění po upgradu. Níže uvedený postup řeší tento problém.
#### <a name="execution-steps"></a>Kroky spuštění


*   Spusťte `multipath -ll` příkaz.
*   Získejte ID logické jednotky, jehož velikost je přibližně 50 G, nebo použijte příkaz:`fdisk -l | grep mapper`
*   Aktualizovat `/etc/default/grub_installdevice` soubor `/dev/mapper/<LUN ID>`řádkem . Příklad: /dev/mapper/3600a09803830372f483f495242534a56
>[!NOTE]
>ID logické jednotky se u jednotlivých serverů liší.


### <a name="disable-edac"></a>Zakázat edac 
   Modul detekce a opravy chyb (EDAC) pomáhá při zjišťování a opravování chyb paměti. Základní hardware pro SAP HANA ve velkých instanci Azure (typ I) však již provádí stejnou funkci. Povolení stejné funkce na úrovních hardwaru a operačního systému (OS) může způsobit konflikty a může vést k občasným neplánovaným vypnutím serveru. Proto se doporučuje zakázat modul z operačního operačního modulu.

#### <a name="execution-steps"></a>Kroky spuštění

* Zkontrolujte, zda je povolen modul EDAC. Pokud je výstup vrácen pod příkazem, znamená to, že modul je povolen. 
```
lsmod | grep -i edac 
```
* Zakázání modulů připojením následujících řádků k souboru`/etc/modprobe.d/blacklist.conf`
```
blacklist sb_edac
blacklist edac_core
```
K provedení změn na místě je nutné restartovat počítač. Spusťte `lsmod` příkaz a ověřte, že modul není přítomen ve výstupu.


### <a name="kernel-parameters"></a>Parametry jádra
   Zkontrolujte, zda `transparent_hugepage`je `numa_balancing` `processor.max_cstate`použito `ignore_ce` `intel_idle.max_cstate` správné nastavení pro , , a.

* intel_idle.max_cstate=1
* procesor.max_cstate=1
* transparent_hugepage=nikdy
* numa_balancing=zakázat
* mce=ignore_ce


#### <a name="execution-steps"></a>Kroky spuštění

* Přidání těchto parametrů `GRB_CMDLINE_LINUX` do řádku v souboru`/etc/default/grub`
```
intel_idle.max_cstate=1 processor.max_cstate=1 transparent_hugepage=never numa_balancing=disable mce=ignore_ce
```
* Vytvořte nový soubor grub.
```
grub2-mkconfig -o /boot/grub2/grub.cfg
```
* Restartujte systém.


## <a name="next-steps"></a>Další kroky
- Viz [Zálohování a obnovení](hana-overview-high-availability-disaster-recovery.md) pro třídu sku zálohování operačního systému typu I.
- Odkazovat [záloha operačního systému pro typ II skladové položky revize 3 razítka](os-backup-type-ii-skus.md) pro třídu SKU typu II.
