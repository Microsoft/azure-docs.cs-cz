---
title: Zotavení po havárii virtuálních počítačů/fyzických serverů VMware do sekundární lokality s Azure Site Recovery
description: Zjistěte, jak nastavit zotavení po havárii virtuálních počítačích VMware nebo fyzických serverů Windows a Linux u sekundární lokality pomocí Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 71d230c9fea25edfbf0ca4ea40f15b69779ad060
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79256806"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Nastavení zotavení po havárii místních virtuálních počítačů VMware nebo fyzických serverů do sekundární lokality

InMage Scout v [Azure Site Recovery](site-recovery-overview.md) poskytuje replikaci v reálném čase mezi místními weby VMware. InMage Scout je součástí předplatného služby Azure Site Recovery.

## <a name="end-of-support-announcement"></a>Oznámení o ukončení podpory

Scénář obnovení webu Azure pro replikaci mezi místním vmware nebo fyzickými datovými centry se blíží k ukončení podpory.

-   Od srpna 2018 nelze scénář nakonfigurovat v trezoru služby Recovery Services a software InMage Scout nelze stáhnout z trezoru. Stávající nasazení budou podporována. 
-   prosince 2020 nebude scénář podporován.
- Stávající partneři mohou do scénáře zařazovat nové zákazníky, dokud podpora neskončí.

Během let 2018 a 2019 budou vydány dvě aktualizace: 

-   Aktualizace 7: Opravuje problémy s konfigurací sítě a dodržováním předpisů a poskytuje podporu TLS 1.2.
-   Aktualizace 8: Přidává podporu pro operační systémy Linux RHEL/CentOS 7.3/7.4/7.5 a pro SUSE 12

Po aktualizaci 8 nebudou vydány žádné další aktualizace. Bude omezená podpora oprav hotfix pro operační systémy přidané v aktualizaci 8 a opravy chyb na základě nejlepšího úsilí.

Azure Site Recovery pokračuje v inovacích tím, že poskytuje zákazníkům VMware a Hyper-V bezproblémové a nejlepší řešení DRaaS ve své třídě s Azure jako lokalitou pro zotavení po havárii. Společnost Microsoft doporučuje, aby stávající zákazníci InMage / ASR Scout zvážili použití scénáře Azure Site Recovery v Zařízení Pro Azure pro jejich potřeby kontinuity podnikání. Scénář Azure Site Recovery v Oblasti Virtuálního počítače do Azure je řešení ZOTAVENÍ po celé společnosti pro aplikace VMware, které nabízí minuty rpo a RTO, podporu replikace a obnovení aplikací s více virtuálními počítači, bezproblémové přiřazování, komplexní monitorování a významnou výhodu tco.

### <a name="scenario-migration"></a>Migrace scénáře
Jako alternativu doporučujeme nastavit zotavení po havárii pro místní virtuální počítače VMware a fyzické počítače replikací do Azure. Proveďte to následujícím způsobem:

1.  Projděte si níže uvedené rychlé srovnání. Než budete moci replikovat místní počítače, musíte zkontrolovat, zda splňují [požadavky na](./vmware-physical-azure-support-matrix.md#replicated-machines) replikaci do Azure. Pokud replikujete virtuální počítače VMware, doporučujeme zkontrolovat [pokyny pro plánování kapacity](./site-recovery-plan-capacity-vmware.md)a spustit [nástroj Plánovač nasazení](./site-recovery-deployment-planner.md) s požadavky na kapacitu identit a ověřit dodržování předpisů.
2.  Po spuštění Plánovače nasazení můžete nastavit replikaci: o Pro virtuální počítače VMware postupujte podle těchto kurzů a [připravte Azure](./tutorial-prepare-azure.md), [připravte místní prostředí VMware](./vmware-azure-tutorial-prepare-on-premises.md)a [nastavte zotavení po havárii](./vmware-azure-tutorial-prepare-on-premises.md).
o Pro fyzické stroje postupujte podle tohoto [kurzu](./physical-azure-disaster-recovery.md).
3.  Po replikaci počítačů do Azure můžete spustit [cvičení pro zotavení po havárii](./site-recovery-test-failover-to-azure.md) a ujistěte se, že vše funguje podle očekávání.

### <a name="quick-comparison"></a>Rychlé porovnání

**Funkce** | **Replikace do Azure** |**Replikace mezi datovými centry VMware**
--|--|--
**Požadované součásti** |Služba mobility na replikovaných počítačích. Místní konfigurační server, procesní server, hlavní cílový server. Dočasný procesní server v Azure pro navrácení služeb po službě napodobení.|Služba mobility, procesní server, konfigurační server a hlavní cíl
**Konfigurace a orchestrace** |Trezor služby Recovery Services na webu Azure Portal | Použití vContinuum 
**Replikované** |Disk (Windows a Linux) |Systém Pro su-svazek<br> Disk-Linux
**Cluster sdíleného disku** |Nepodporuje se|Podporuje se
**Limity konve (průměr)** |10 MB/s dat na disk<br> Data 25 MB/s na virtuální virtuální měn<br> [Další informace](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > 10 MB/s dat na disk  <br> > 25 MB/s dat na virtuální hod
**Sledování** |Z portálu Azure|Z CX (konfigurační server)
**Matice podpory** | [Klikněte zde pro podrobnosti](./vmware-physical-azure-support-matrix.md)|[Stáhnout ASR Scout kompatibilní matice](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Požadavky
Pro absolvování tohoto kurzu potřebujete:

- [Zkontrolujte](vmware-physical-secondary-support-matrix.md) požadavky na podporu pro všechny součásti.
- Ujistěte se, že počítače, které chcete replikovat, splňují [podporu replikovaného počítače](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Stažení a instalace aktualizací součástí

 Zkontrolujte a nainstalujte nejnovější [aktualizace](#updates). Aktualizace by měly být nainstalovány na serverech v následujícím pořadí:

1. RX server (je-li k dispozici)
2. Konfigurační servery
3. Procesní servery
4. Hlavní cílové servery
5. vContinuum servery
6. Zdrojový server (servery Windows i Linux)

Aktualizace nainstalujte následujícím způsobem:

> [!NOTE]
>Verze aktualizace souboru všech součástí scoutů nemusí být v souboru ZIP aktualizace stejná. Starší verze označuje, že od předchozí aktualizace této aktualizace nedošlo k žádné změně součásti.

Stáhněte si [soubor .zip aktualizace](https://aka.ms/asr-scout-update7) a konfigurační soubory [upgradu MySQL a PHP.](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) Soubor ZIP aktualizace obsahuje všechny základní binární soubory a kumulativní binární soubory upgradu následujících součástí: 
- InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015.tar.gz
- RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release.tar.gz
- InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release.tar.gz
  1. Extrahujte soubory ZIP.
  2. **RX server**: Zkopírujte **RX_8.0.7.0_GA_Update_7_2965621_28Dec18.tar.gz** na server RX a extrahujte jej. V extrahované složce spusťte **/Install**.
  3. **Konfigurační server a procesní server**: Zkopírujte **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** na konfigurační a procesní server. Poklepáním ji spusťte.<br>
  4. **Hlavní cílový server systému Windows**: Chcete-li aktualizovat sjednoceného agenta, zkopírujte na server **soubor InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe.** Poklepáním ji spusťte. Stejný soubor lze také použít pro novou instalaci. Stejná aktualizace sjednoceného agenta je použitelná také pro zdrojový server.
  Aktualizace nemusí platit pro hlavní cíl připravený s **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe,** protože se jedná o nový instalační program GA se všemi nejnovějšími změnami.
  5. **vContinuum server**: Zkopírujte na server **soubor InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.0_Windows_GA_27Dec2018_releasee.**  Ujistěte se, že jste průvodce vContinuum zavřeli. Poklepáním na soubor jej spusťte.
  6. **Hlavní cílový server Linuxu**: Chcete-li aktualizovat sjednoceného agenta, zkopírujte **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** na server Linux Master Target a extrahujte jej. V extrahované složce spusťte **/Install**.
  7. **Zdrojový server systému Windows**: Chcete-li aktualizovat sjednoceného agenta, zkopírujte **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** na zdrojový server. Poklepáním na soubor jej spusťte. 
  8. **Zdrojový server Linuxu**: Chcete-li aktualizovat sjednoceného agenta, zkopírujte odpovídající verzi souboru sjednoceného agenta na server Linuxu a extrahujte jej. V extrahované složce spusťte **/Install**.  Příklad: Pro 64bitový server RHEL zkopírujte na server **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release.tar.gz** a extrahujte jej. V extrahované složce spusťte **/Install**.
  9. Po upgradu Konfigurační server, Procesní server a RX server s výše uvedenými instalátory, PHP a MySQL knihovny je třeba upgradovat ručně s kroky uvedenými v části 7.4 [rychlé instalace průvodce](https://aka.ms/asr-scout-quick-install-guide).

## <a name="enable-replication"></a>Povolení replikace

1. Nastavte replikaci mezi zdrojovými a cílovými weby VMware.
2. Další informace o instalaci, ochraně a obnovení naleznete v následujících dokumentech:

   * [Poznámky k verzi](https://aka.ms/asr-scout-release-notes)
   * [Matice kompatibility](https://aka.ms/asr-scout-cm)
   * [Uživatelská příručka](https://aka.ms/asr-scout-user-guide)
   * [Uživatelská příručka RX](https://aka.ms/asr-scout-rx-user-guide)
   * [Průvodce rychlou instalací](https://aka.ms/asr-scout-quick-install-guide)
   * [Upgrade knihoven MYSQL a PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Aktualizace

### <a name="site-recovery-scout-801-update-7"></a>Obnova webu Scout 8.0.1 Aktualizace 7 
Aktualizováno: Prosinec 31, 2018 Stáhnout [Scout update 7](https://aka.ms/asr-scout-update7).
Scout Update 7 je úplný instalační program, který lze použít pro novou instalaci, stejně jako pro upgrade stávajících agentů / MT, které jsou na předchozích aktualizacích (od aktualizace 1 až po aktualizaci 6). Obsahuje všechny opravy od aktualizace 1 až po aktualizaci 6 a nové opravy a vylepšení popsané níže.
 
#### <a name="new-features"></a>Nové funkce
* Dodržování předpisů PCI
* Podpora TLS v1.2

#### <a name="bug-and-security-fixes"></a>Opravy chyb a zabezpečení
* Oprava: Cluster/samostatné počítače se systémem Windows mají nesprávnou konfiguraci PROTOKOLU IP při obnovení/dr-Drill.
* Oprava: Někdy se operace přidání disku nezdaří pro cluster V2V.
* Opraveno: Průvodce vContinuum se během fáze obnovení zasekne, pokud je hlavním cílem Windows Server 2016
* Opraveno: Problémy se zabezpečením MySQL jsou zmírněny upgradem MySQL na verzi 5.7.23

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Ruční upgrade pro PHP a MySQL na CS, PS a RX
Skriptovací platforma PHP by měla být upgradována na verzi 7.2.10 na konfiguračním serveru, procesním serveru a RX serveru.
Systém správy databází MySQL by měl být upgradován na verzi 5.7.23 na konfiguračním serveru, procesním serveru a RX serveru.
Postupujte podle pokynů uvedených v [průvodci rychlou instalací](https://aka.ms/asr-scout-quick-install-guide) pro upgrade verzí PHP a MySQL.

### <a name="site-recovery-scout-801-update-6"></a>Obnova webu Scout 8.0.1 Aktualizace 6 
Aktualizováno: 12.

Stáhnout [Scout update 6](https://aka.ms/asr-scout-update6).

Scout Update 6 je kumulativní aktualizace. Obsahuje všechny opravy z aktualizace 1 na aktualizaci 5 a nové opravy a vylepšení popsané níže. 

#### <a name="new-platform-support"></a>Nová podpora platformy
* Byla přidána podpora pro zdrojový systém Windows Server 2016.
* Byla přidána podpora pro následující operační systémy Linux:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - Centos 6,9
    - Oracle Linux 5.11
    - Oracle Linux 6.8
* Byla přidána podpora pro VMware Center 6.5

Aktualizace nainstalujte následujícím způsobem:

> [!NOTE]
>Verze aktualizace souboru všech součástí scoutů nemusí být v souboru ZIP aktualizace stejná. Starší verze označuje, že od předchozí aktualizace této aktualizace nedošlo k žádné změně součásti.

Stáhněte [soubor ZIP aktualizace.](https://aka.ms/asr-scout-update6) Soubor obsahuje následující součásti: 
- RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA update4 bity pro RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_\<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  1. Extrahujte soubory ZIP.
  2. **RX server**: Zkopírujte **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** na server RX a extrahujte jej. V extrahované složce spusťte **/Install**.
  3. **Konfigurační server a procesní server**: Zkopírujte **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** na konfigurační a procesní server. Poklepáním ji spusťte.<br>
  4. **Hlavní cílový server systému Windows**: Chcete-li aktualizovat sjednoceného agenta, zkopírujte na server **soubor UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.0_GA_Update_5_11525802_20Apr17.exe.** Poklepáním ji spusťte. Stejná aktualizace sjednoceného agenta je použitelná také pro zdrojový server. Pokud zdroj nebyl aktualizován na aktualizaci 4, měli byste aktualizovat sjednoceného agenta.
  Aktualizace nemusí platit pro hlavní cíl připravený s **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe,** protože se jedná o nový instalační program GA se všemi nejnovějšími změnami.
  5. **vContinuum server**: Zkopírujte na server **soubor vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe.**  Ujistěte se, že jste průvodce vContinuum zavřeli. Poklepáním na soubor jej spusťte.
  Aktualizace nemusí platit pro hlavní cíl připravený s **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe,** protože se jedná o nový instalační program GA se všemi nejnovějšími změnami.
  6. **Hlavní cílový server Linuxu**: Chcete-li aktualizovat sjednoceného agenta, zkopírujte **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** na hlavní cílový server a extrahujte jej. V extrahované složce spusťte **/Install**.
  7. **Zdrojový server systému Windows**: Chcete-li aktualizovat sjednoceného agenta, zkopírujte **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** na zdrojový server. Poklepáním na soubor jej spusťte. 
  Agenta aktualizace 5 není třeba instalovat na zdrojový server, pokud již byl aktualizován na aktualizaci 4 nebo je nainstalován zdrojový agent s nejnovějším základním instalačním **programem InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Zdrojový server Linuxu**: Chcete-li aktualizovat sjednoceného agenta, zkopírujte odpovídající verzi souboru sjednoceného agenta na server Linuxu a extrahujte jej. V extrahované složce spusťte **/Install**.  Příklad: Pro 64bitový server RHEL zkopírujte **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** na server a extrahujte jej. V extrahované složce spusťte **/Install**.


> [!NOTE]
> * Instalační služba Základní sjednocený agent (UA) pro Windows byla aktualizována, aby podporovala Windows Server 2016. Nový instalační **program InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** je zabalen se základním balíčkem Scout GA **(InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Stejný instalační program bude použit pro všechny podporované verze systému Windows. 
> * Základní instalační program systému Windows vContinuum & hlavní cíl byl aktualizován, aby podporoval systém Windows Server 2016. Nový instalační **program InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** je zabalen se základním balíčkem Scout GA **(InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Stejný instalační program se použije k nasazení hlavních cílů systému Windows 2016 a hlavního cíle systému Windows 2012R2.
> * Asr Scout nepodporuje Windows Server 2016 na fyzickém serveru. Podporuje pouze Windows Server 2016 VMware VM. 
>

#### <a name="bug-fixes-and-enhancements"></a>Opravy a vylepšení chyb
- Ochrana proti pooperačním uchvátáčem se nezdaří pro virtuální počítač s Linuxem se seznamem disků, které mají být replikovány, je na konci konfigurace prázdná.

### <a name="site-recovery-scout-801-update-5"></a>Obnova webu Scout 8.0.1 Aktualizace 5
Scout Update 5 je kumulativní aktualizace. Obsahuje všechny opravy od aktualizace 1 až po aktualizaci 4 a nové opravy popsané níže.
- Opravy z site recovery scout update 4 na aktualizaci 5 jsou speciálně pro hlavní cíl a vContinuum komponenty.
- Pokud zdrojové servery, hlavní cíl, konfigurace, proces a rx servery jsou již spuštěna aktualizace 4, pak ji použít pouze na hlavní cílový server. 

#### <a name="new-platform-support"></a>Nová podpora platformy
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)
* SLES 11 SP4 64 bit **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** je balen se základním balíčkem Scout GA **(InMage_Scout_Standard_8.0.1 GA.zip**). Stáhněte si balíček GA z portálu, jak je popsáno v vytvoření trezoru.


#### <a name="bug-fixes-and-enhancements"></a>Opravy a vylepšení chyb

* Opravy zvýšené spolehlivosti clusteru Windows:
    * Opraveno– některé disky clusteru P2V MSCS se po obnovení stanou raw.
    * Oprava obnovení clusteru P2V MSCS se nezdaří z důvodu neshody pořadí disku.
    * Opravena operace clusteru MSCS pro přidání disků se nezdaří s chybou neshody velikosti disku.
    * Opravena - kontrola připravenosti pro zdrojový cluster MSCS s mapováním LULun RDM se nezdaří ve velikosti ověření.
    * Oprava : Ochrana clusteru s jedním uzlem se nezdaří z důvodu problému neshody SCSI. 
    * Pevná - Re-ochrana clusterového serveru P2V Windows selže, pokud jsou k dispozici disky cílového clusteru. 
    
* Opraveno: Během ochrany proti navrácení služeb po obnovení, pokud vybraný hlavní cílový server není na stejném serveru ESXi jako chráněný zdrojový počítač (během ochrany vpřed), pak vContinuum zvedne nesprávný hlavní cílový server během obnovení po obnovení a obnovení operace se nezdaří.

> [!NOTE]
> * Opravy clusteru P2V se vztahují pouze na fyzické clustery MSCS, které jsou nově chráněny aktualizací Site Recovery Scout Update 5. Chcete-li nainstalovat opravy clusteru v chráněných clusterech P2V MSCS se staršími aktualizacemi, postupujte podle kroků upgradu uvedených v části 12 [poznámek k verzi site recovery .](https://aka.ms/asr-scout-release-notes)
> * pokud v době opětovné ochrany, stejná sada disků jsou aktivní na každém z uzlů clusteru, jako byly při původně chráněné, pak znovu ochranu fyzického clusteru MSCS lze použít pouze znovu existující cílové disky. Pokud ne, použijte ruční kroky v části 12 [poznámky k verzi Site Recovery Scout](https://aka.ms/asr-scout-release-notes), abyste přesunuli cílové boční disky na správnou cestu k úložišti dat, abyste je znovu použili během opětovné ochrany. Pokud cluster MSCS znovu přizamknete v režimu P2V bez provedení kroků upgradu, vytvoří se na cílovém serveru ESXi nový disk. Budete muset ručně odstranit staré disky z úložiště dat.
> * Při řádném restartování zdrojového serveru SLES11 nebo SLES11 (s libovolnou aktualizací Service Pack) označte ručně dvojice replikace **kořenového** disku pro opětovnou synchronizaci. V rozhraní CX není žádné oznámení.Pokud neoznačíte kořenový disk pro resynchronizaci, můžete si všimnout problémů s integritou dat.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Aktualizace 4
Scout Update 4 je kumulativní aktualizace. Obsahuje všechny opravy od aktualizace 1 až po aktualizaci 3 a nové opravy popsané níže.

#### <a name="new-platform-support"></a>Nová podpora platformy

* Byla přidána podpora pro vCenter/vSphere 6.0, 6.1 a 6.2
* Byla přidána podpora pro tyto operační systémy Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 a 7.2
  * CentOS 7,0, 7,1 a 7,2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * Centos 6,8

> [!NOTE]
> RHEL/CentOS 7 64 bit **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** je balen se základním balíčkem Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**. Stáhněte si balíček Scout GA z portálu, jak je popsáno v vytvoření trezoru.

#### <a name="bug-fixes-and-enhancements"></a>Opravy a vylepšení chyb

* Vylepšené zpracování vypnutí pro následující operační systémy a klony Linuxu, aby se zabránilo nežádoucím problémům s resynchronizací:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Pro Linux jsou nyní všechna přístupová oprávnění ke složkám v instalačním adresáři sjednoceného agenta omezena pouze na místního uživatele.
* V systému Windows, oprava problému vypršení časového limitu, ke kterému došlo při vydávání běžných adresářů distribuované konzistence, u silně načtených distribuovaných aplikací, jako jsou clustery SQL Server a Share Point.
* Oprava související s protokolem v základním instalačním programu konfiguračního serveru.
* Odkaz ke stažení na vMware vCLI 6.0 byl přidán do základního instalačního programu hlavního cílového systému Windows.
* Byly přidány další kontroly a protokoly pro změny konfigurace sítě během převzetí služeb při selhání a cvičení zotavení po havárii.
* Oprava problému, který způsobil, že informace o uchovávání informací nebyly hlášeny konfiguračnímu serveru.  
* U fyzických clusterů oprava problému, který způsoboval selhání velikosti svazku v průvodci vContinuum při zmenšení zdrojového svazku.
* Oprava problému ochrany clusteru, který se nezdařil s chybou: "Nepodařilo se najít podpis disku", pokud je disk clusteru diskPRDM.
* Oprava havárie serveru přenosu cxps, způsobené výjimkou mimo rozsah.
* Sloupce s názvem serveru a adresami IP jsou nyní možné na stránce **Nabízená instalace** průvodce vContinuum.
* Vylepšení rozhraní RX API:
  * Pět nejnovějších dostupných společných bodů konzistence, které jsou nyní k dispozici (pouze zaručené značky).
  * Pro všechna chráněná zařízení jsou zobrazeny údaje o kapacitě a volném prostoru.
  * Je k dispozici stav ovladače scoutna zdrojovém serveru.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** základní balíček má:
>     * Aktualizovaný základní instalační program konfiguračního serveru (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Základní instalační program hlavního cílového kódu systému Windows (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * Pro všechny nové instalace použijte nový konfigurační server a hlavní cílové bity GA systému Windows.
> * Aktualizace 4 lze použít přímo na 8.0.1 GA.
> * Konfigurační server a aktualizace RX nelze vrátit zpět po jejich použití.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Aktualizace 3

Všechny aktualizace obnovení webu jsou kumulativní. Aktualizace 3 obsahuje všechny opravy z aktualizace 1 a aktualizace 2. Aktualizace 3 lze přímo použít na 8.0.1 GA. Konfigurační server a aktualizace RX nelze vrátit zpět po jejich použití.

#### <a name="bug-fixes-and-enhancements"></a>Opravy a vylepšení chyb
Aktualizace 3 řeší následující problémy:

* Konfigurační server a RX nejsou registrovány v trezoru, když jsou za proxy serverem.
* Počet hodin, ve kterých nebylo dosaženo cíle bodu obnovení (RPO), není v sestavě stavu aktualizován.
* Konfigurační server není synchronizován s RX, pokud podrobnosti o hardwaru ESX nebo podrobnosti o síti obsahují znaky UTF-8.
* Řadiče domény systému Windows Server 2008 R2 se po obnovení nespustí.
* Offline synchronizace nefunguje podle očekávání.
* Po převzetí služeb při selhání virtuálního počítače odstranění dvojice replikace neprobíhá v konzole konfiguračního serveru po dlouhou dobu. Uživatelé nemohou dokončit operace navrácení služeb po obnovení nebo obnovit.
* Celkové operace snímek úlohy konzistence byly optimalizovány, aby pomohly snížit odpojení aplikací, jako jsou klienti SQL Server.
* Byl vylepšen výkon nástroje konzistence (VACP.exe). Využití paměti potřebné pro vytváření snímků v systému Windows bylo sníženo.
* Služba push install dojde k chybě, pokud je heslo větší než 16 znaků.
* vContinuum nekontroluje a nezobrazuje výzvu k zadání nových přihlašovacích údajů vCenter při změně pověření.
* V Linuxu hlavní správce cílové mezipaměti (cachemgr) nestahuje soubory z procesního serveru. Výsledkem je omezení dvojice replikací.
* Pokud pořadí disku fyzického clusteru s podporou převzetí služeb při selhání (MSCS) není stejné ve všech uzlech, replikace není nastavena pro některé svazky clusteru. Chcete-li tuto opravu využít, musí být cluster znovu chráněn.  
* Funkce SMTP nefunguje očekávaným způsobem po upgradu rx ze scoutu 7.1 na Scout 8.0.1.
* Další statistiky byly přidány do protokolu pro operaci vrácení zpět, sledovat čas, který byl dokončen.
* Byla přidána podpora operačních systémů Linux na zdrojovém serveru:
  * Red Hat Enterprise Linux (RHEL) 6 aktualizace 7
  * Aktualizace CentOS 6 7
* Konfigurační server a konzoly RX nyní zobrazují oznámení pro dvojici, která přejde do bitmapového režimu.
* Do rx byly přidány následující opravy zabezpečení:
    * Obejít autorizaci pomocí manipulace s parametry: Omezený přístup k nepoužitelným uživatelům.
    * Padělání požadavků mezi weby: Koncept tokenu stránky byl implementován a generuje náhodně pro každou stránku. To znamená, že pro stejného uživatele existuje pouze jedna instance přihlášení a aktualizace stránky nefunguje. Místo toho přesměruje na řídicí panel.
    * Škodlivé nahrávání souborů: Soubory jsou omezeny na konkrétní rozšíření: z, aif, asf, avi, bmp, csv, doc, docx, fla, flv, gif, gz, gzip, jpeg, jpg, log, střední, mov, mp3, mp4, mpc, mpeg, mpg, ODS, ODT, pdf, png, ppt, pptx, pxd, qt, ram, rar, rm, rmi, rmvb, rtf , SDC, sitd, swf, sxc, sxw, tar, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml a zip.
    * Trvalé skriptování mezi weby: Byla přidána vstupní ověření.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Aktualizace 2 (aktualizace 03Dec15)

Opravy v aktualizaci 2 zahrnují:

* **Konfigurační server**: Problémy, které zabránily 31denní funkci bezplatného měření fungovat podle očekávání, když byl konfigurační server zaregistrován do trezoru obnovení webu Azure.
* **Unified Agent**: Oprava problému v aktualizaci 1, který vedl k tomu, že aktualizace nebyla nainstalována na hlavním cílovém serveru během upgradu z verze 8.0 na 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Aktualizace 1
Aktualizace 1 obsahuje následující opravy chyb a nové funkce:

* 31 dní bezplatné ochrany na server ové instanci. To umožňuje otestovat funkce nebo nastavit proof-of-concept.
* Všechny operace na serveru, včetně převzetí služeb při selhání a navrácení služeb po selhání, jsou zdarma po dobu prvních 31 dnů. Čas začíná, když je server poprvé chráněn pomocí site recovery scout. od 32.
* Počet aktuálně účtovaných chráněných serverů je kdykoli k dispozici na **řídicím panelu** v trezoru.
* Podpora byla přidána pro rozhraní vSphere Command-Line Interface (vCLI) 5.5 Update 2.
* Byla přidána podpora pro tyto operační systémy Linux na zdrojovém serveru:
    * AKTUALIZACE RHEL 6 6
    * AKTUALIZACE RHEL 5 11
    * Centos 6 Aktualizace 6
    * Centos 5 Aktualizace 11
* Opravy chyb k řešení následujících problémů:
  * Registrace úložiště se nezdaří pro konfigurační server nebo server RX.
  * Svazky clusteru se nezobrazují podle očekávání, když jsou clusterované virtuální počítače znovu chráněny při jejich obnovení.
  * Navrácení služeb po selhání se nezdaří, pokud je hlavní cílový server hostovaný na jiném serveru ESXi než místní produkční virtuální počítač.
  * Při upgradu na 8.0.1 se změní oprávnění konfiguračního souboru. Tato změna ovlivňuje ochranu a operace.
  * Prahová hodnota synchronizace není vynucena podle očekávání, což způsobuje nekonzistentní chování replikace.
  * Nastavení rpo se v konzole konfiguračního serveru nezobrazí správně. Nekomprimovaná hodnota dat nesprávně zobrazuje komprimovoci.
  * Operace Odebrat se v průvodci vContinuum neodstraní podle očekávání a replikace není odstraněna z konzoly konfiguračního serveru.
  * V průvodci vContinuum je disk automaticky nevybraný, když klepnete na **tlačítko Podrobnosti** v zobrazení disku během ochrany virtuálních počítačů Služby MSCS.
  * Ve scénáři fyzicky virtuální (P2V) požadované služby HP (například CIMnotify a CqMgHost) nejsou přesunuty do ruční v obnovení virtuálního počítače. Tento problém má za následek další čas spuštění.
  * Ochrana virtuálních počítačů s Linuxem se nezdaří, pokud je na hlavním cílovém serveru více než 26 disků.

