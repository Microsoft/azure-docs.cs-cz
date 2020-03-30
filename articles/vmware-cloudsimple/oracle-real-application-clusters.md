---
title: Řešení Azure VMware od CloudSimple – Optimalizace cloudového cloudu CloudSimple pro oracle RAC
description: Popisuje, jak nasadit nový cluster a optimalizovat virtuální počítač pro instalaci a konfiguraci řešení Oracle Real Application Clusters (RAC).
author: sharaths-cs
ms.author: b-shsury
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 733a225c66040cb2ab819f041647120c8b63b6a0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77016013"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Optimalizujte svůj cloud CloudSimple Privátní cloud pro instalaci řešení Oracle RAC

Oracle Real Application Clusters (RAC) můžete nasadit ve svém cloudovém prostředí privátního cloudu. Tato příručka popisuje, jak nasadit nový cluster a optimalizovat virtuální počítač pro řešení Oracle RAC. Po dokončení kroků v tomto tématu můžete nainstalovat a nakonfigurovat řešení Oracle RAC.

## <a name="storage-policy"></a>Zásady úložiště

Úspěšná implementace řešení Oracle RAC vyžaduje dostatečný počet uzlů v clusteru.  V zásadách úložiště vSAN selhání tolerovat (FTT) se používá na datové disky používané pro ukládání databáze, protokolu a znovu disky.  Požadovaný počet uzlů efektivně tolerovat selhání je 2N +1, kde N je hodnota FTT.

Příklad: Pokud je požadovaný FTT 2, musí být celkový počet uzlů v clusteru 2*2+1 = 5.

## <a name="overview-of-deployment"></a>Přehled nasazování

Následující části popisují, jak nastavit cloudové prostředí cloudového privátního cloudu pro oracle RAC.

1. Doporučené postupy pro konfiguraci disku
2. Nasazení privátního cloudového clusteru CloudSimple vSphere
3. Nastavení síťových sítí pro řešení Oracle RAC
4. Nastavení zásad úložiště vSAN
5. Vytvoření virtuálních počítačů Oracle a vytvoření sdílených disků virtuálních počítačů
6. Nastavení pravidel spřažení virtuálního hry k hostiteli

## <a name="best-practices-for-disk-configuration"></a>Doporučené postupy pro konfiguraci disku

Virtuální počítače Oracle RAC mají více disků, které se používají pro konkrétní funkci.  Sdílené disky jsou připojeny na všech virtuálních počítačích, které používá oracle RAC clusteru.  Instalační disky operačního systému a softwaru jsou připojeny pouze na jednotlivých virtuálních počítačích.  

![Přehled disků virtuálních počítačů Oracle RAC](media/oracle-vm-disks-overview.png)

Následující příklad používá disky definované v následující tabulce.

| Disk                                      | Účel                                       | Sdílený disk |
|-------------------------------------------|-----------------------------------------------|-------------|
| Operační systém                                        | Disk operačním systému                         | Ne          |
| Mřížky                                      | Instalace umístění pro software Oracle Grid     | Ne          |
| Databáze                                  | Instalace umístění pro databázový software Oracle | Ne          |
| ORAHOME                                   | Základní umístění binárních souborů databáze Oracle    | Ne          |
| DATA1, DATA2, DATA3, DATA4                | Disk, na kterém jsou uloženy databázové soubory Oracle   | Ano         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Znovu provést disky protokolu                                | Ano         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Hlasovací disky                                  | Ano         |
| FRA1, FRA2                                | Disky rychlé oblasti obnovení                      | Ano         |

![Konfigurace disku virtuálního počítače Oracle](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Konfigurace virtuálního počítače

* Každý virtuální počítač je nakonfigurován se čtyřmi řadiči SCSI.
* Typ řadiče SCSI je nastaven na paravirtual VMware.
* Je vytvořeno více virtuálních disků (.vmdk).
* Disky jsou připojeny na různých řadičích SCSI.
* Typ sdílení pro více writerů je nastaven pro sdílené disky clusteru.
* Zásady úložiště vSAN jsou definovány pro zajištění vysoké dostupnosti disků.

### <a name="operating-system-and-software-disk-configuration"></a>Konfigurace operačního systému a softwarového disku

Každý virtuální počítač Oracle je konfigurován s více disky pro hostitelský operační systém, swap, instalaci softwaru a další funkce operačního systému.  Tyto disky nejsou sdíleny mezi virtuálními počítači.  

* Tři disky pro každý virtuální počítač jsou nakonfigurovány jako virtuální disky a připojeny na virtuálních počítačích Oracle RAC.
    * Disk s operačním systémem
    * Disk pro ukládání souborů oracle grid uvádí
    * Disk pro ukládání instalačních souborů databáze Oracle
* Disky lze nakonfigurovat jako **thin provisioned**.
* Každý disk je připojen na první řadič SCSI (SCSI0).  
* Sdílení je nastaveno na **žádné sdílení**.
* Redundance je definována v úložišti pomocí zásad vSAN.  

![Konfigurace skupiny datových disků Oracle RAC](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Konfigurace datového disku

Datové disky se používají především pro ukládání databázových souborů.  

* Čtyři disky jsou konfigurovány jako virtuální disky a připojeny na všech virtuálních počítačích Oracle RAC.
* Každý disk je připojen na jiný řadič SCSI.
* Každý virtuální disk je nakonfigurován jako **silné zřízení eager zeroed**.  
* Sdílení je nastaveno na **multi-writer**.  
* Disky musí být nakonfigurovány jako skupina disků automatické správy úložiště (ASM).  
* Redundance je definována v úložišti pomocí zásad vSAN.  
* Redundance ASM je nastavena na **externí** redundanci.

![Konfigurace skupiny datových disků Oracle RAC](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Znovu provést konfiguraci disku protokolu

Soubory protokolu opakování se používají k ukládání kopie změn provedených v databázi.  Soubory protokolu se používají, když je třeba obnovit data po všech selháních.

* Znovu nastavit disky protokolu musí být nakonfigurovány jako více skupin disků.  
* Na všech virtuálních počítačích Oracle RAC je vytvořeno a připojeno šest disků.
* Disky jsou připojeny na různých řadičích SCSI
* Každý virtuální disk je nakonfigurován jako **silné zřízení eager zeroed**.
* Sdílení je nastaveno na **multi-writer**.  
* Disky musí být nakonfigurovány jako dvě skupiny disků ASM.
* Každá skupina disků ASM obsahuje tři disky, které jsou na různých řadičích SCSI.  
* Redundance ASM je nastavena na **normální** redundanci.
* Ve skupině protokolů ASM Redo je vytvořeno pět souborů protokolu redo.

```
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 1 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
SQL > alter database add logfile thread 2 ('+ORCLRAC_REDO1','+ORCLRAC_REDO2') size 1G;
```

![Konfigurace skupiny protokolů Oracle RAC redo](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Konfigurace hlasovacího disku Oracle

Hlasovací disky poskytují funkce disku kvora jako další komunikační kanál, aby se zabránilo jakékoli situaci s rozděleným mozkem.

* Na všech virtuálních počítačích Oracle RAC je vytvořeno a připojeno pět disků.
* Disky jsou připojeny na jednom řadiči SCSI
* Každý virtuální disk je nakonfigurován jako **silné zřízení eager zeroed**.
* Sdílení je nastaveno na **multi-writer**.  
* Disky musí být nakonfigurovány jako skupina disků ASM.  
* Redundance ASM je nastavena na **vysokou** redundanci.

![Konfigurace hlasovacích disků Oracle RAC](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Konfigurace disku oblasti rychlého obnovení oracle (volitelně)

Oblast rychlého obnovení (FRA) je souborový systém spravovaný skupinou disků Oracle ASM.  Agentura FRA poskytuje sdílené umístění úložiště pro záložní a obnovovací soubory. Oracle vytváří archivované protokoly a flashback protokoly v oblasti rychlého obnovení. Oracle Recovery Manager (RMAN) může volitelně ukládat své zálohovací sady a kopie bitových kopií v oblasti rychlého obnovení a používá jej při obnově souborů během obnovení média.

* Dva disky jsou vytvořeny a připojeny na všech virtuálních počítačích Oracle RAC.
* Disky jsou připojeny na jiný řadič SCSI
* Každý virtuální disk je nakonfigurován jako **silné zřízení eager zeroed**.
* Sdílení je nastaveno na **multi-writer**.  
* Disky musí být nakonfigurovány jako skupina disků ASM.  
* Redundance ASM je nastavena na **externí** redundanci.

![Konfigurace hlasovacích disků Oracle RAC](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>Nasazení clusteru CloudSimple Priv. Cloud vSphere

Chcete-li nasadit cluster vSphere v privátním cloudu, postupujte podle tohoto postupu:

1. Z portálu CloudSimple [vytvořte privátní cloud](create-private-cloud.md). CloudSimple vytvoří výchozí ho uživatele vCenter s názvem "cloudowner" v nově vytvořeném privátním cloudu. Podrobnosti o výchozím uživateli privátního cloudu a modelu oprávnění najdete [v tématu Naučte se model oprávnění privátního cloudu](learn-private-cloud-permissions.md).  Tento krok vytvoří primární cluster pro správu privátního cloudu.

2. Z portálu CloudSimple [rozbalte privátní cloud](expand-private-cloud.md) pomocí nového clusteru.  Tento cluster se použije k nasazení řešení Oracle RAC.  Vyberte počet uzlů na základě požadované odolnosti proti chybám (minimálně tři uzly).

## <a name="set-up-networking-for-oracle-rac"></a>Nastavení sítě pro řešení Oracle RAC

1. V privátním cloudu [vytvořte dvě sítě VLAN](create-vlan-subnet.md), jednu pro veřejnou síť Oracle a jednu pro privátní síť Oracle a přiřaďte příslušné cidrs podsítě.
2. Po vytvoření vlan vytvořte [skupiny distribuovaných portů v programu Private Cloud vCenter](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Nastavte [virtuální počítač DHCP a SERVERU DNS](dns-dhcp-setup.md) v clusteru pro správu pro prostředí Oracle.
4. [Nakonfigurujte předávání DNS na serveru DNS](on-premises-dns-setup.md#create-a-conditional-forwarder) nainstalovaném v privátním cloudu.

## <a name="set-up-vsan-storage-policies"></a>Nastavení zásad úložiště vSAN

Zásady vSAN definují selhání tolerovat a disk prokládání pro data uložená na discích virtuálního počítače.  Vytvořené zásady úložiště musí být použity na discích virtuálních počítačů při vytváření virtuálního počítače.

1. [Přihlaste se k klientovi vSphere](https://docs.azure.cloudsimple.com/vsphere-access) vašeho privátního cloudu.
2. V horní nabídce vyberte **Zásady a profily**.
3. V levé nabídce vyberte **Zásady úložiště virtuálních zařízení** a pak vyberte **Vytvořit zásady úložiště virtuálních zařízení**.
4. Zadejte smysluplný název zásady a klepněte na tlačítko **DALŠÍ**.
5. V části **Struktura zásad** vyberte **Povolit pravidla pro úložiště vSAN** a klepněte na tlačítko **DALŠÍ**.
6. V části**dostupnost** **vSAN** > vyberte **možnost Žádná** pro toleranci havárie lokality. Pro selhání tolerovat, vyberte **RAID - Zrcadlení** možnost pro požadovaný FTT.
    ![nastavení vSAN](media/oracle-rac-storage-wizard-vsan.png).
7. V části **Upřesnit** vyberte počet diskových pruhů na objekt. V části Rezervace prostoru objektu vyberte **možnost Silné zřízené**. Vyberte **zakázat kontrolní součet objektů**. Klepněte na tlačítko **DALŠÍ**.
8. Podle pokynů na obrazovce zobrazte seznam kompatibilních datových úložišť vSAN, zkontrolujte nastavení a dokončete nastavení.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Vytvoření virtuálních počítačů Oracle a vytvoření sdílených disků virtuálních počítačů pro Oracle

Chcete-li vytvořit virtuální virtuální hod pro Oracle, klonovat existující virtuální hod nebo vytvořit nový.  Tato část popisuje, jak vytvořit nový virtuální virtuální soud a potom jej klonovat k vytvoření druhého po instalaci základního operačního systému.  Po vytvoření virtuálních počítačů můžete vytvořit přidat disky k nim.  Cluster Oracle používá sdílené disky pro ukládání, data, protokoly a opakování protokolů.

### <a name="create-vms"></a>Vytvoření virtuálních počítačů

1. V programu vCenter klikněte na ikonu **Hostitelé a clustery.** Vyberte cluster, který jste vytvořili pro oracle.
2. Klepněte pravým tlačítkem myši na cluster a vyberte **nový virtuální počítač**.
3. Vyberte **Vytvořit nový virtuální počítač** a klepněte na tlačítko **Další**.
4. Pojmenujte počítač, vyberte umístění virtuálního počítače Oracle a klepněte na tlačítko **Další**.
5. Vyberte prostředek clusteru a klepněte na tlačítko **Další**.
6. Vyberte úložiště dat vSAN pro cluster a klepněte na tlačítko **Další**.
7. Zachovat výchozí výběr kompatibility ESXi 6.5 a klepněte na tlačítko **Další**.
8. Vyberte hostovaného operačního příkazu ISO pro vytvářený virtuální ho disponizém a klepněte na tlačítko **Další**.
9. Vyberte velikost pevného disku, která je vyžadována pro instalaci operačního systému.
10. Chcete-li aplikaci nainstalovat na jiné zařízení, klepněte na tlačítko **Přidat nové zařízení**.
11. Vyberte možnosti sítě a přiřaďte skupinu distribuovaných portů vytvořenou pro veřejnou síť.
12. Chcete-li přidat další síťová rozhraní, klepněte na tlačítko **Přidat nové zařízení** a vyberte skupinu distribuovaných portů vytvořenou pro privátní síť.
13. V případě jednotky New DC/DVD vyberte soubor ISO úložiště dat, který obsahuje ISO pro upřednostňovanou instalaci operačního systému. Vyberte soubor, který jste dříve nahráli do složky ISO a šablony, a klepněte na tlačítko **OK**.
14. Zkontrolujte nastavení a kliknutím na **OK** vytvořte nový virtuální virtuální počítače.
15. Napájení virtuálního počítači. Instalace operačního systému a všech požadovaných aktualizací

Po instalaci operačního systému můžete klonovat druhý virtuální virtuální ms. Klikněte pravým tlačítkem myši na položku virtuálního virtuálního soudu a vyberte možnost a klonovat.

### <a name="create-shared-disks-for-vms"></a>Vytvoření sdílených disků pro virtuální počítače

Společnost Oracle používá sdílený disk k ukládání souborů protokolu, protokolů a opakování dat.  Můžete vytvořit sdílený disk na vCenter a připojit jej na obou virtuálních počítačích.  Chcete-li dosáhnout vyššího výkonu, umístěte datové disky na různé řadiče SCSI Kroky níže ukazují, jak vytvořit sdílený disk v programu vCenter a pak jej připojit k virtuálnímu počítači. Klient vCenter Flash se používá k úpravám vlastností virtuálního počítače.

#### <a name="create-disks-on-the-first-vm"></a>Vytvoření disků na prvním virtuálním počítači

1. V programu vCenter klepněte pravým tlačítkem myši na jeden z virtuálních počítače Oracle a vyberte **upravit nastavení**.
2. V nové části zařízení vyberte **řadič SCSI** a klepněte na **tlačítko Přidat**.
3. V části Nové zařízení vyberte **Nový pevný disk** a klepněte na tlačítko **Přidat**.
4. Rozbalte vlastnosti nového pevného disku.
5. Zadejte velikost pevného disku.
6. Zadejte zásady úložiště virtuálního zařízení jako zásady úložiště vSAN, které jste definovali dříve.
7. Vyberte umístění jako složku v úložišti dat vSAN. Umístění pomáhá při procházení a připojení disků k druhému virtuálnímu počítače.
8. Pro zřizování disků vyberte **možnost Tlusté zřizování eager zeroed**.
9. Pro sdílení zadejte **Multi-writer**.
10. Pro uzel virtuálního zařízení vyberte nový řadič SCSI, který byl vytvořen v kroku 2.

    ![Vytvoření disků na prvním virtuálním počítači](media/oracle-rac-new-hard-disk.png)

Opakujte kroky 2 – 10 pro všechny nové disky potřebné pro soubory protokolu Oracle, protokoly a znovu.

#### <a name="attach-disks-to-second-vm"></a>Připojení disků k druhému virtuálnímu virtuálnímu počítače

1. V programu vCenter klepněte pravým tlačítkem myši na jeden z virtuálních počítače Oracle a vyberte **upravit nastavení**.
2. V nové části zařízení vyberte **řadič SCSI** a klepněte na **tlačítko Přidat**.
3. V části Nové zařízení vyberte **Existující pevný disk** a klepněte na tlačítko **Přidat**.
4. Přejděte do umístění, kde byl disk vytvořen pro první virtuální počítače a vyberte soubor VMDK.
5. Zadejte zásady úložiště virtuálního zařízení jako zásady úložiště vSAN, které jste definovali dříve.
6. Pro zřizování disků vyberte **možnost Tlusté zřizování eager zeroed**.
7. Pro sdílení zadejte **Multi-writer**.
8. Pro uzel virtuálního zařízení vyberte nový řadič SCSI, který byl vytvořen v kroku 2.

    ![Vytvoření disků na prvním virtuálním počítači](media/oracle-rac-existing-hard-disk.png)

Opakujte kroky 2 – 7 pro všechny nové disky potřebné pro soubory protokolu Oracle, protokoly a znovu.

## <a name="set-up-vm-host-affinity-rules"></a>Nastavení pravidel spřažení hostitele virtuálního provozu

Pravidla spřažení virtuálního počítači s hostitelem zajišťují, že virtuální počítač běží na požadovaném hostiteli.  Můžete definovat pravidla v centru, abyste zajistili, že virtuální počítač Oracle běží na hostiteli s odpovídajícími prostředky a splňuje všechny specifické licenční požadavky.

1. Na portálu CloudSimple [eskalovat oprávnění](escalate-private-cloud-privileges.md) uživatele cloudvlastník.
2. [Přihlaste se k klientovi vSphere](https://docs.azure.cloudsimple.com/vsphere-access) vašeho privátního cloudu.
3. V klientovi vSphere vyberte cluster, ve kterém se nasazují virtuální počítače Oracle, a klepněte na tlačítko **Konfigurovat**.
4. V části Konfigurovat vyberte **Skupiny virtuálních hostitelů nebo hostitelů**.
5. Klepněte na tlačítko **+**.
6. Přidejte skupinu virtuálních společností. Jako typ vyberte **skupinu virtuálních montovek.** Zadejte název skupiny. Vyberte virtuální hod a kliknutím na **OK** vytvořte skupinu.
6. Přidejte skupinu hostitelů. Jako typ vyberte **Skupinu hostitelů.** Zadejte název skupiny. Vyberte hostitele, na kterých se virtuální virtuální měbudou spouštět, a kliknutím na **OK** vytvořte skupinu.
7. Chcete-li vytvořit pravidlo, klikněte na **pravidla virtuálního počítače/hostitele**.
8. Klepněte na tlačítko **+**.
9. Zadejte název pravidla a **zaškrtněte políčko Povolit**.
10. Pro typ pravidla vyberte **Virtuální počítače jako hostitel**.
11. Vyberte skupinu virtuálních aplikací, která obsahuje virtuální servery Oracle.
12. Vyberte **možnost Musí být spuštěna na hostitelích v této skupině**.
13. Vyberte skupinu hostitelů, kterou jste vytvořili.
14. Kliknutím na **OK** vytvořte pravidlo.

## <a name="references"></a>Odkazy

* [O zásadách vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [Atribut vícenásobného zapisovače VMware pro sdílené sady VMDK](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
