---
title: Řešení Azure VMware podle CloudSimple – optimalizace privátního cloudu CloudSimple pro Oracle RAC
description: Popisuje postup nasazení nového clusteru a optimalizaci virtuálního počítače pro instalaci a konfiguraci Oracle Real Application Clusters (RAC).
author: Ajayan1008
ms.author: v-hborys
ms.date: 08/06/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 3959aae5f490af10c6747cfa67d9960e0c4a203f
ms.sourcegitcommit: d7d5f0da1dda786bda0260cf43bd4716e5bda08b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/05/2021
ms.locfileid: "97899265"
---
# <a name="optimize-your-cloudsimple-private-cloud-for-installing-oracle-rac"></a>Optimalizace privátního cloudu CloudSimple pro instalaci Oracle RAC

V prostředí privátního cloudu CloudSimple můžete nasadit clustery reálné aplikace (RAC) Oracle. Tato příručka popisuje, jak nasadit nový cluster a optimalizovat virtuální počítač pro řešení Oracle RAC. Po dokončení kroků v tomto tématu můžete nainstalovat a nakonfigurovat Oracle RAC.

## <a name="storage-policy"></a>Zásady úložiště

Úspěšná implementace Oracle RAC vyžaduje dostatečný počet uzlů v clusteru.  V zásadách úložiště síti vSAN se v datových discích používaných k ukládání databází, protokolů a opětovného navýšení disků použijí chyby na tolerování (FTT).  Požadovaný počet uzlů efektivně tolerovatcích selhání je 2N + 1, kde N je hodnota FTT.

Příklad: Pokud je požadovaný FTT 2, celkový počet uzlů v clusteru musí být 2 * 2 + 1 = 5.

## <a name="overview-of-deployment"></a>Přehled nasazování

Následující části popisují, jak nastavit privátní cloudové prostředí CloudSimple pro Oracle RAC.

1. Osvědčené postupy pro konfiguraci disků
2. Nasazení clusteru CloudSimple Private Cloud vSphere
3. Nastavení sítě pro Oracle RAC
4. Nastavení zásad úložiště síti vSAN
5. Vytváření virtuálních počítačů Oracle a vytváření sdílených disků virtuálního počítače
6. Nastavení pravidel vztahů mezi virtuálními počítači a hostiteli

## <a name="best-practices-for-disk-configuration"></a>Osvědčené postupy pro konfiguraci disků

Virtuální počítače Oracle RAC mají více disků, které se používají pro konkrétní funkci.  Sdílené disky jsou připojené ke všem virtuálním počítačům, které používá cluster Oracle RAC.  Instalační disky operačního systému a softwaru se namontují jenom na jednotlivé virtuální počítače.  

![Přehled disků virtuálního počítače Oracle RAC](media/oracle-vm-disks-overview.png)

V následujícím příkladu se používají disky definované v následující tabulce.

| Disk                                      | Účel                                       | sdílený disk |
|-------------------------------------------|-----------------------------------------------|-------------|
| Operační systém                                        | Disk operačním systému                         | No          |
| MŘÍŽKY                                      | Umístění instalace pro software Oracle Grid     | No          |
| DATABÁZE                                  | Umístění instalace pro software Oracle Database | No          |
| ORAHOME                                   | Základní umístění pro binární soubory Oracle Database    | No          |
| DATA1, DATA2, DATA3, DATA4                | Disk, kde jsou uloženy soubory databáze Oracle   | Yes         |
| REDO1, REDO2, REDO3, REDO4, REDO5, REDO6  | Disky protokolu opětovného provedení                                | Yes         |
| OCR1, OCR2, OCR3, OCR4, OCR5              | Hlasovací disky                                  | Yes         |
| FRA1, FRA2                                | Rychlé disky oblasti obnovení                      | Yes         |

![Konfigurace disku virtuálního počítače Oracle](media/oracle-vmdk.png)

### <a name="virtual-machine-configuration"></a>Konfigurace virtuálního počítače

* Každý virtuální počítač je nakonfigurovaný se čtyřmi řadiči SCSI.
* Typ řadiče SCSI je nastavený na VMware Paravirtual.
* Vytvoří se několik virtuálních disků (. vmdk).
* Disky jsou připojené k různým řadičům SCSI.
* Pro sdílené disky clusteru je nastaven typ sdílení s více zapisovači.
* zásady úložiště síti vSAN jsou definované pro zajištění vysoké dostupnosti disků.

### <a name="operating-system-and-software-disk-configuration"></a>Konfigurace operačního systému a softwarového disku

Každý virtuální počítač Oracle je nakonfigurovaný s více disky pro operační systém hostitele, prohození, instalaci softwaru a další funkce operačního systému.  Tyto disky nejsou sdíleny mezi virtuálními počítači.  

* Tři disky pro každý virtuální počítač jsou nakonfigurovány jako virtuální disky a připojeny na virtuální počítače Oracle RAC.
    * Disk s operačním systémem
    * Disk pro ukládání souborů instalace mřížky Oracle
    * Disk pro ukládání instalačních souborů databáze Oracle
* Disky je možné nakonfigurovat jako **dynamicky zřízené**.
* Každý disk je připojený k prvnímu řadiči SCSI (SCSI0).  
* Sdílení je nastaveno na **bez sdílení**.
* Redundance je definována v úložišti pomocí zásad síti vSAN.  

![Diagram znázorňující fyzickou konfiguraci disku s operačním systémem Oracle RAC](media/oracle-vm-os-disks.png)

### <a name="data-disk-configuration"></a>Konfigurace datových disků

Datové disky se primárně používají k ukládání souborů databáze.  

* Čtyři disky jsou nakonfigurovány jako virtuální disky a připojeny na všechny virtuální počítače Oracle RAC.
* Každý disk je připojený k jinému řadiči SCSI.
* Každý virtuální disk je nakonfigurovaný jako **silné zřízení Eager s nulovou hodnotou**.  
* Sdílení je nastaveno na **Vícenásobný zápis**.  
* Disky musí být nakonfigurované jako skupina disků s automatickou správou úložiště (ASM).  
* Redundance je definována v úložišti pomocí zásad síti vSAN.  
* Redundance ASM je nastavená na **externí** redundanci.

![Konfigurace skupiny datových disků Oracle RAC](media/oracle-vm-data-disks.png)

### <a name="redo-log-disk-configuration"></a>Konfigurace disku protokolu opětovného provedení

Soubory protokolu opětovného provedení se používají k ukládání kopie změn provedených v databázi.  Soubory protokolu se používají, když je potřeba obnovit data po jakémkoli selhání.

* Disky protokolu opětovného záznamu musí být nakonfigurovány jako více skupin disků.  
* Na všech virtuálních počítačích Oracle RAC je vytvořeno a připojeno šest disků.
* Disky jsou připojené k různým řadičům SCSI.
* Každý virtuální disk je nakonfigurovaný jako **silné zřízení Eager s nulovou hodnotou**.
* Sdílení je nastaveno na **Vícenásobný zápis**.  
* Disky musí být nakonfigurované jako dvě skupiny disků ASM.
* Každá skupina disků ASM obsahuje tři disky, které jsou na různých řadičích SCSI.  
* Redundance ASM je nastavená na **normální** redundanci.
* Pět souborů protokolu opětovného provedení se vytvoří ve skupině protokolů opětovného provedení ASM.

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

![Konfigurace skupiny disků protokolu opětovného nastavení pro Oracle RAC](media/oracle-vm-redo-log-disks.png)

### <a name="oracle-voting-disk-configuration"></a>Konfigurace hlasovacího disku Oracle

Hlasovací disky poskytují funkci disku kvora jako dodatečný komunikační kanál, abyste se vyhnuli jakékoli situaci v případě potřeby rozdělení.

* Na všech virtuálních počítačích Oracle RAC je vytvořeno a připojeno pět disků.
* Disky jsou připojené k jednomu řadiči SCSI.
* Každý virtuální disk je nakonfigurovaný jako **silné zřízení Eager s nulovou hodnotou**.
* Sdílení je nastaveno na **Vícenásobný zápis**.  
* Disky musí být nakonfigurované jako skupina disků ASM.  
* Redundance ASM je nastavená na **vysokou** redundanci.

![Konfigurace skupiny hlasovacích disků Oracle RAC](media/oracle-vm-voting-disks.png)

### <a name="oracle-fast-recovery-area-disk-configuration-optional"></a>Konfigurace disku pro oblast rychlého obnovení Oracle (volitelné)

Oblast rychlé obnovy (v/v) je systém souborů spravovaný skupinou disků Oracle ASM.  Služba předplatných poskytuje sdílené umístění úložiště pro soubory zálohy a obnovení. Oracle vytvoří archivované protokoly a protokoly Flashback v oblasti rychlé obnovení. Oracle Recovery Manager (RMAN) může volitelně ukládat své zálohovací sklady a kopie imagí v oblasti rychlé obnovení a při obnovení souborů ji použije při obnovování médií.

* Na všech virtuálních počítačích Oracle RAC se vytváří a připevní dva disky.
* Disky jsou připojené na jiný řadič SCSI.
* Každý virtuální disk je nakonfigurovaný jako **silné zřízení Eager s nulovou hodnotou**.
* Sdílení je nastaveno na **Vícenásobný zápis**.  
* Disky musí být nakonfigurované jako skupina disků ASM.  
* Redundance ASM je nastavená na **externí** redundanci.

![Diagram, který zobrazuje konfiguraci skupiny hlasovacích disků Oracle RAC.](media/oracle-vm-fra-disks.png)

## <a name="deploy-cloudsimple-private-cloud-vsphere-cluster"></a>Nasazení clusteru CloudSimple Private Cloud vSphere

Pokud chcete nasadit cluster vSphere do privátního cloudu, postupujte podle tohoto postupu:

1. Na portálu CloudSimple [vytvořte privátní cloud](create-private-cloud.md). CloudSimple vytvoří výchozího uživatele vCenter s názvem "cloudowner" v nově vytvořeném privátním cloudu. Podrobnosti o výchozím modelu uživatele a oprávnění privátního cloudu najdete v tématu [informace o modelu oprávnění privátního cloudu](learn-private-cloud-permissions.md).  Tento krok vytvoří primární cluster pro správu pro váš privátní cloud.

2. Na portálu CloudSimple [rozbalte privátní cloud](expand-private-cloud.md) s novým clusterem.  Tento cluster se použije k nasazení Oracle RAC.  Vyberte počet uzlů na základě požadované odolnosti proti chybám (minimálně tři uzly).

## <a name="set-up-networking-for-oracle-rac"></a>Nastavení sítě pro Oracle RAC

1. V privátním cloudu [vytvořte dvě sítě VLAN](create-vlan-subnet.md), jednu pro veřejnou síť Oracle a jednu pro privátní síť Oracle a přiřaďte příslušné CIDRs podsítě.
2. Po vytvoření sítí VLAN vytvořte [distribuované skupiny portů v privátním cloudu vCenter](create-vlan-subnet.md#use-vlan-information-to-set-up-a-distributed-port-group-in-vsphere).
3. Nastavte [virtuální počítač DHCP a server DNS](dns-dhcp-setup.md) na cluster pro správu pro prostředí Oracle.
4. [Nakonfigurujte předávání DNS na serveru DNS](on-premises-dns-setup.md#create-a-conditional-forwarder) nainstalovaném v privátním cloudu.

## <a name="set-up-vsan-storage-policies"></a>Nastavení zásad úložiště síti vSAN

zásady síti vSAN definují selhání pro tolerovat a diskové obložení pro data uložená na discích virtuálních počítačů.  Vytvořené zásady úložiště musí být na discích virtuálních počítačů při vytváření virtuálního počítače aplikovány.

1. [Přihlaste se ke klientovi vSphere](./vcenter-access.md) vašeho privátního cloudu.
2. V horní nabídce vyberte **zásady a profily**.
3. V nabídce vlevo vyberte **zásady úložiště virtuálního počítače** a pak vyberte **vytvořit zásadu úložiště virtuálního počítače**.
4. Zadejte smysluplný název zásady a klikněte na **Další**.
5. V části **Struktura zásad** vyberte **Povolit pravidla pro úložiště síti vSAN** a klikněte na **Další**.
6. V části   >  **dostupnost** síti vSAN vyberte **žádné** pro odolnost sítě po havárii. Pokud chcete chyby tolerovat, vyberte možnost **zrcadlení RAID** pro požadovaný FTT.
    ![](media/oracle-rac-storage-wizard-vsan.png)Nastavení síti vSAN
7. V části **Upřesnit** vyberte počet diskových pruhů na jeden objekt. V případě rezervovaného prostoru objektu vyberte **silný zřízený**. Vyberte **Zakázat kontrolní součet objektu**. Klikněte na tlačítko **Další**.
8. Podle pokynů na obrazovce zobrazte seznam kompatibilních úložišť síti vSAN, zkontrolujte nastavení a dokončete instalaci.

## <a name="create-oracle-vms-and-create-shared-vm-disks-for-oracle"></a>Vytvoření virtuálních počítačů Oracle a vytvoření sdílených disků virtuálního počítače pro Oracle

Pokud chcete vytvořit virtuální počítač pro Oracle, naklonujte existující virtuální počítač nebo vytvořte nový.  V této části se dozvíte, jak vytvořit nový virtuální počítač a pak ho naklonovat, abyste po instalaci základního operačního systému vytvořili druhý.  Po vytvoření virtuálních počítačů můžete do nich vytvořit přidat disky.  Cluster Oracle používá sdílené disky pro ukládání, data, protokoly a protokoly opětovného provedení.

### <a name="create-vms"></a>Vytvoření virtuálních počítačů

1. V vCenter klikněte na ikonu **hostitelé a clustery** . Vyberte cluster, který jste vytvořili pro Oracle.
2. Klikněte pravým tlačítkem na cluster a vyberte **Nový virtuální počítač**.
3. Vyberte **vytvořit nový virtuální počítač** a klikněte na **Další**.
4. Pojmenujte počítač, vyberte umístění virtuálního počítače Oracle a klikněte na **Další**.
5. Vyberte prostředek clusteru a klikněte na **Další**.
6. Vyberte úložiště dat síti vSAN pro cluster a klikněte na **Další**.
7. Zachovejte výchozí výběr kompatibility ESXi 6,5 a klikněte na tlačítko **Další**.
8. Pro vytvářený virtuální počítač vyberte hostovaný operační systém ISO a klikněte na **Další**.
9. Vyberte velikost pevného disku, která je nutná pro instalaci operačního systému.
10. Pokud chcete aplikaci nainstalovat na jiné zařízení, klikněte na **Přidat nové zařízení**.
11. Vyberte možnosti sítě a přiřaďte distribuovanou skupinu portů vytvořenou pro veřejnou síť.
12. Pokud chcete přidat další síťová rozhraní, klikněte na **Přidat nové zařízení** a vyberte distribuovanou skupinu portů vytvořenou pro privátní síť.
13. V případě nové jednotky DC/DVD vyberte soubor ISO úložiště dat, který obsahuje bitovou kopii ISO pro upřednostňovanou instalaci operačního systému. Vyberte soubor, který jste dříve nahráli do složky soubory ISO a Templates a klikněte na **OK**.
14. Zkontrolujte nastavení a kliknutím na **OK** vytvořte nový virtuální počítač.
15. Zapněte virtuální počítač. Nainstalujte operační systém a všechny požadované aktualizace.

Po instalaci operačního systému můžete naklonovat druhý virtuální počítač. Klikněte pravým tlačítkem myši na položku virtuálního počítače a vyberte možnost klonování a.

### <a name="create-shared-disks-for-vms"></a>Vytvoření sdílených disků pro virtuální počítače

Oracle používá sdílený disk k ukládání dat, protokolů a souborů protokolu opětovného provedení.  Můžete vytvořit sdílený disk na vCenter a připojit ho na virtuálním počítači.  Pokud chcete dosáhnout vyššího výkonu, umístěte datové disky do různých kroků řadičů SCSI níže v tématu Postup vytvoření sdíleného disku v vCenter a jeho připojení k virtuálnímu počítači. pro úpravu vlastností virtuálního počítače se používá klient vCenter Flash.

#### <a name="create-disks-on-the-first-vm"></a>Vytvoření disků na prvním virtuálním počítači

1. V vCenter klikněte pravým tlačítkem na jeden z virtuálních počítačů Oracle a vyberte **Upravit nastavení**.
2. V části nové zařízení vyberte **řadič SCSI** a klikněte na **Přidat**.
3. V části nové zařízení vyberte **nový pevný disk** a klikněte na **Přidat**.
4. Rozbalte vlastnosti nového pevného disku.
5. Zadejte velikost pevného disku.
6. Zadejte zásady úložiště virtuálních počítačů, které mají být síti vSAN zásadami úložiště, které jste definovali dříve.
7. Vyberte umístění jako složku v úložišti dat síti vSAN. Umístění pomáhá s procházením a připojením disků k druhému virtuálnímu počítači.
8. V případě zřizování disků vyberte možnost **silné zřízení Eager s nulovou hodnotou**.
9. Pro sdílení zadejte **Vícenásobný zapisovač**.
10. V uzlu virtuální zařízení vyberte nový řadič SCSI, který byl vytvořen v kroku 2.

    ![Snímek obrazovky, který zvýrazní pole potřebná k vytvoření disků na prvním virtuálním počítači.](media/oracle-rac-new-hard-disk.png)

Opakujte kroky 2 – 10 pro všechny nové disky požadované pro soubory protokolu Oracle data, protokoly a znovu.

#### <a name="attach-disks-to-second-vm"></a>Připojit disky k druhému virtuálnímu počítači

1. V vCenter klikněte pravým tlačítkem na jeden z virtuálních počítačů Oracle a vyberte **Upravit nastavení**.
2. V části nové zařízení vyberte **řadič SCSI** a klikněte na **Přidat**.
3. V části nové zařízení vyberte **existující pevný disk** a klikněte na **Přidat**.
4. Přejděte do umístění, kde byl vytvořen disk pro první virtuální počítač, a vyberte soubor VMDK.
5. Zadejte zásady úložiště virtuálních počítačů, které mají být síti vSAN zásadami úložiště, které jste definovali dříve.
6. V případě zřizování disků vyberte možnost **silné zřízení Eager s nulovou hodnotou**.
7. Pro sdílení zadejte **Vícenásobný zapisovač**.
8. V uzlu virtuální zařízení vyberte nový řadič SCSI, který byl vytvořen v kroku 2.

    ![Vytvořit disky na prvním virtuálním počítači](media/oracle-rac-existing-hard-disk.png)

Opakujte kroky 2 – 7 pro všechny nové disky požadované pro soubory protokolu Oracle data, protokoly a znovu.

## <a name="set-up-vm-host-affinity-rules"></a>Nastavení pravidel spřažení hostitelů virtuálních počítačů

Pravidla spřažení z virtuálního počítače na hostitele zajistí, že virtuální počítač běží na požadovaném hostiteli.  Můžete definovat pravidla na vCenter, aby se zajistilo, že se virtuální počítač Oracle spustí na hostiteli s odpovídajícími prostředky a splňuje všechny konkrétní požadavky na licencování.

1. Na portálu CloudSimple povýšit [oprávnění](escalate-private-cloud-privileges.md) uživatele cloudowner.
2. Přihlaste se ke klientovi vSphere vašeho privátního cloudu.
3. V klientovi vSphere vyberte cluster, ve kterém jsou nasazené virtuální počítače Oracle, a klikněte na **Konfigurovat**.
4. V části konfigurovat vyberte **skupiny VM/hostitelů**.
5. Klikněte na **+** .
6. Přidejte skupinu virtuálních počítačů. Jako typ vyberte **Skupina virtuálních počítačů** . Zadejte název skupiny. Vyberte virtuální počítače a potom kliknutím na **OK** vytvořte skupinu.
6. Přidejte skupinu hostitelů. Jako typ vyberte **Skupina hostitelů** . Zadejte název skupiny. Vyberte hostitele, na kterých se budou virtuální počítače spouštět, a pak kliknutím na **OK** vytvořte skupinu.
7. Pokud chcete vytvořit pravidlo, klikněte na **pravidla virtuálního počítače nebo hostitele**.
8. Klikněte na **+** .
9. Zadejte název pravidla a zaškrtněte **Povolit**.
10. Pro typ pravidla vyberte **Virtual Machines pro hostování**.
11. Vyberte skupinu virtuálních počítačů, která obsahuje virtuální počítače Oracle.
12. Vyberte **musí běžet na hostitelích v této skupině**.
13. Vyberte skupinu hostitelů, kterou jste vytvořili.
14. Kliknutím na **OK** vytvořte pravidlo.

## <a name="references"></a>Reference

* [Informace o zásadách síti vSAN](https://docs.vmware.com/en/VMware-vSphere/6.7/com.vmware.vsphere.virtualsan.doc/GUID-08911FD3-2462-4C1C-AE81-0D4DBC8F7990.html)
* [Atribut VMware multi-Writer pro Shared VMDK](https://docs.vmware.com/en/VMware-Cloud-on-AWS/solutions/VMware-Cloud-on-AWS.df6735f8b729fee463802083d46fdc75/GUID-A7642A82B3D6C5F7806DB40A3F2766D9.html)
