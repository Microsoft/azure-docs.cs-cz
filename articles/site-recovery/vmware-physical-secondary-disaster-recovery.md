---
title: Zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů do sekundární lokality s Azure Site Recovery
description: Naučte se, jak nastavit zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů se systémem Windows a Linux do sekundární lokality s Azure Site Recovery.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
services: site-recovery
ms.topic: conceptual
ms.date: 11/05/2019
ms.author: raynew
ms.openlocfilehash: 71d230c9fea25edfbf0ca4ea40f15b69779ad060
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84711897"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Nastavení zotavení po havárii místních virtuálních počítačů VMware nebo fyzických serverů do sekundární lokality

InMage Scout v [Azure Site Recovery](site-recovery-overview.md) poskytuje replikaci v reálném čase mezi místními lokalitami VMware. InMage Scout je zahrnutý v předplatných služby Azure Site Recovery.

## <a name="end-of-support-announcement"></a>Oznámení o ukončení podpory

Scénář Azure Site Recovery pro replikaci mezi místními VMware nebo fyzickými datacentry se blíží konci podpory.

-   Od srpna 2018 se scénář nedá nakonfigurovat v Recovery Services trezoru a InMage Scout software nejde stáhnout z trezoru. Existující nasazení budou podporována. 
-   Od prosince 31 2020 se scénář nepodporuje.
- Stávající partneři mohou připojit nové zákazníky do scénáře až do doby, než skončí podpora.

Během 2018 a 2019 budou vydány dvě aktualizace: 

-   Aktualizace 7: opravuje problémy s konfigurací sítě a dodržováním předpisů a poskytuje podporu TLS 1,2.
-   Aktualizace 8: přidává podporu pro operační systémy Linux RHEL/CentOS 7.3/7.4/7.5 a pro SUSE 12

Po aktualizaci 8 nebudou zveřejněny žádné další aktualizace. Pro operační systémy přidané v Update 8 bude omezená podpora oprav hotfix a opravy chyb na základě nejlepšího úsilí.

Azure Site Recovery nadále inovační tím, že zákazníkům VMware a Hyper-V zajistí bezproblémové a nejlepší řešení DRaaS ve své třídě s Azure jako lokalitu pro zotavení po havárii. Microsoft doporučuje, aby stávající zákazníci InMage/ASR Scout zvážili použití scénáře Azure Site Recovery VMware do Azure pro potřeby jejich provozní kontinuity. Scénář Azure Site Recovery VMware do Azure je řešení zotavení po havárii na podnikové úrovni pro aplikace VMware, které nabízí RPO a RTO minut, podporu replikace a obnovení aplikací pro víc virtuálních počítačů, bezproblémové zprovoznění, komplexní monitorování a významnou výhodu celkových nákladů na vlastnictví.

### <a name="scenario-migration"></a>Migrace scénáře
Jako alternativu doporučujeme nastavit zotavení po havárii pro místní virtuální počítače VMware a fyzické počítače jejich replikací do Azure. Proveďte to následujícím způsobem:

1.  Přečtěte si rychlé porovnání uvedené níže. Než budete moct replikovat místní počítače, musíte ověřit, že splňují [požadavky](./vmware-physical-azure-support-matrix.md#replicated-machines) na replikaci do Azure. Pokud provádíte replikaci virtuálních počítačů VMware, doporučujeme vám projít si [pokyny pro plánování kapacity](./site-recovery-plan-capacity-vmware.md)a spustit [nástroj Plánovač nasazení](./site-recovery-deployment-planner.md) pro požadavky na kapacitu identity a ověřit dodržování předpisů.
2.  Po spuštění Plánovač nasazení můžete nastavit replikaci: o pro virtuální počítače VMware. podle těchto kurzů [Připravte Azure](./tutorial-prepare-azure.md), [Připravte své místní prostředí VMware](./vmware-azure-tutorial-prepare-on-premises.md)a [nastavte zotavení po havárii](./vmware-azure-tutorial-prepare-on-premises.md).
v případě fyzických počítačů postupujte podle tohoto [kurzu](./physical-azure-disaster-recovery.md).
3.  Po replikaci počítačů do Azure můžete spustit postup [zotavení po havárii](./site-recovery-test-failover-to-azure.md) , abyste měli jistotu, že všechno funguje podle očekávání.

### <a name="quick-comparison"></a>Rychlé porovnání

**Funkce** | **Replikace do Azure** |**Replikace mezi datacentry VMware**
--|--|--
**Požadované součásti** |Služba mobility na replikovaných počítačích. Místní konfigurační server, procesový Server, hlavní cílový server. Dočasný procesový Server v Azure pro navrácení služeb po obnovení.|Služba mobility, procesový Server, konfigurační server a hlavní cíl
**Konfigurace a orchestrace** |Recovery Services trezor v Azure Portal | Použití vContinuum 
**Replikovaná tabulka** |Disk (Windows a Linux) |Volume-Windows<br> Disk-Linux
**Cluster sdíleného disku** |Nepodporováno|Podporováno
**Omezení četnosti změn dat (průměr)** |10 MB/s dat na disk<br> data 25 MB JAVASCRIPTOVÉHO/s na virtuální počítač<br> [Další informace](./site-recovery-vmware-deployment-planner-analyze-report.md#azure-site-recovery-limits) | > 10 MB/s dat na disk  <br> > dat o velikosti 25 MB/s na virtuální počítač
**Monitorování** |Z Azure Portal|Z CX (konfigurační server)
**Matice podpory** | [Kliknutím sem zobrazíte podrobnosti.](./vmware-physical-azure-support-matrix.md)|[Stáhnout matrici kompatibilní s funkcí ASR Scout](https://aka.ms/asr-scout-cm)


## <a name="prerequisites"></a>Předpoklady
Pro absolvování tohoto kurzu potřebujete:

- [Zkontrolujte](vmware-physical-secondary-support-matrix.md) požadavky na podporu pro všechny komponenty.
- Ujistěte se, že počítače, které chcete replikovat, vyhovují [podpoře replikovaného počítače](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="download-and-install-component-updates"></a>Stáhnout a nainstalovat aktualizace součástí

 Zkontrolujte a nainstalujte nejnovější [aktualizace](#updates). Aktualizace by se měly instalovat na serverech v následujícím pořadí:

1. Server pro příjem (Pokud je k dispozici)
2. Konfigurační servery
3. Procesové servery
4. Hlavní cílové servery
5. servery vContinuum
6. Zdrojový server (servery se systémem Windows i Linux)

Aktualizace nainstalujte následujícím způsobem:

> [!NOTE]
>Všechny součásti Scout Components v souboru Update. zip nemusejí být stejné. Starší verze značí, že od předchozí aktualizace k této aktualizaci se nezměnila součást.

Stáhněte soubor [Update](https://aka.ms/asr-scout-update7) . zip a konfigurační soubory pro [upgrade MySQL a php](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade) . Soubor Update. zip obsahuje všechny základní binární soubory a binární soubory kumulativního upgradu následujících součástí: 
- InMage_ScoutCloud_RX_8 InMage_ScoutCloud_RX_8.0.1.0_RHEL6-64_GA_02Mar2015. tar. gz
- RX_8 RX_8.0.7.0_GA_Update_7_2965621_28Dec18. tar. gz
- InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_CX_TP_8.0.1.0_Windows_GA_26Feb2015_release.exe
- CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe
- InMage_PI_8.0.1.0_Windows_GA_26Feb2015_release.exe
- InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe
- InMage_UA_8 InMage_UA_8.0.7.0_OL5-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL5-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL6-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_OL6-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL5-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL5-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL6-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_RHEL7-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP1-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP1-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP2-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP2-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP3-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP3-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP4-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES10-SP4-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-64_GA_04Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP1-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP1-64_GA_04Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP2-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP2-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP3-32_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP3-64_GA_03Dec2018_release. tar. gz
- InMage_UA_8 InMage_UA_8.0.7.0_SLES11-SP4-64_GA_03Dec2018_release. tar. gz
  1. Extrahujte soubory. zip.
  2. **Server pro příjem**: Zkopírujte **RX_8.0.7.0_GA_Update_7_2965621_28Dec18. tar. gz** na server pro příjem a rozbalte ho. V extrahované složce spusťte příkaz **/install**.
  3. **Konfigurační server a procesový Server**: Zkopírujte **CX_Windows_8.0.7.0_GA_Update_7_2965621_28Dec18.exe** na konfigurační server a procesový Server. Dvojím kliknutím ji spusťte.<br>
  4. **Hlavní cílový server Windows**: Pokud chcete aktualizovat sjednoceného agenta, zkopírujte **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** na server. Dvakrát klikněte na ni a spusťte ji. Stejný soubor lze také použít pro novou instalaci. Stejná aktualizace sjednoceného agenta se vztahuje také na zdrojový server.
  Aktualizace nemusí platit pro hlavní cíl připravený s **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe**  , protože se jedná o nový instalační program GA se všemi nejnovějšími změnami.
  5. **vContinuum Server**: Zkopírujte **InMage_Scout_vContinuum_MT_8.0.7.0_Windows_GA_27Dec2018_release.exe** na server.  Ujistěte se, že jste Průvodce vContinuum zavřeli. Dvakrát klikněte na soubor a spusťte ho.
  6. **Server pro Linux Master Target**: Pokud chcete aktualizovat sjednoceného agenta, zkopírujte **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz** na hlavní cílový server Linux a rozbalte ho. V extrahované složce spusťte příkaz **/install**.
  7. **Zdrojový server Windows**: Pokud chcete aktualizovat sjednoceného agenta, zkopírujte **InMage_UA_8.0.7.0_Windows_GA_27Dec2018_release.exe** na zdrojový server. Dvakrát klikněte na soubor a spusťte ho. 
  8. **Zdrojový server Linux**: Chcete-li aktualizovat sjednoceného agenta, zkopírujte odpovídající verzi souboru sjednoceného agenta na server Linux a rozbalte ji. V extrahované složce spusťte příkaz **/install**.  Příklad: pro RHEL 6,7 64-bit Server zkopírujte **InMage_UA_8.0.7.0_RHEL6-64_GA_03Dec2018_release. tar. gz** na server a extrahujte ho. V extrahované složce spusťte příkaz **/install**.
  9. Po upgradu konfiguračního serveru, procesového serveru a serveru pro příjem s výše uvedenými instalačními programy je potřeba upgradovat ručně knihovny PHP a MySQL pomocí kroků uvedených v části 7,4 příručky pro [rychlé instalace](https://aka.ms/asr-scout-quick-install-guide).

## <a name="enable-replication"></a>Povolení replikace

1. Nastavte replikaci mezi zdrojovou a cílovou lokalitou VMware.
2. Další informace o instalaci, ochraně a obnovení najdete v následujících dokumentech:

   * [Zpráva k vydání verze](https://aka.ms/asr-scout-release-notes)
   * [Tabulka kompatibility](https://aka.ms/asr-scout-cm)
   * [Uživatelská příručka](https://aka.ms/asr-scout-user-guide)
   * [Uživatelská příručka pro příjem](https://aka.ms/asr-scout-rx-user-guide)
   * [Průvodce rychlou instalací](https://aka.ms/asr-scout-quick-install-guide)
   * [Upgrade knihoven MYSQL a PHP](https://aka.ms/asr-scout-u7-mysql-php-manualupgrade)

## <a name="updates"></a>Aktualizace

### <a name="site-recovery-scout-801-update-7"></a>Site Recovery Scout 8.0.1 Update 7 
Aktualizováno: 31. prosince 2018 stáhnout [Scout Update 7](https://aka.ms/asr-scout-update7).
Scout Update 7 je úplný instalační program, který se dá použít pro novou instalaci, a také k upgradu existujících agentů/MT, které se nacházejí v předchozích aktualizacích (od aktualizace 1 po aktualizaci 6). Obsahuje všechny opravy z aktualizace Update 1 pro aktualizaci 6 a nové opravy a vylepšení popsané níže.
 
#### <a name="new-features"></a>Nové funkce
* Kompatibilita sběrnice PCI
* Podpora TLS v 1.2

#### <a name="bug-and-security-fixes"></a>Opravy chyb a zabezpečení
* Opraveno: cluster systému Windows/samostatné počítače mají po obnovení nebo zotavení po havárii nesprávnou konfiguraci protokolu IP.
* Opraveno: někdy se operace přidání disku pro cluster V2V nezdařila.
* Opraveno: Průvodce vContinuum se během fáze obnovení zablokuje, pokud je hlavním cílem Windows Server 2016.
* Opraveno: problémy zabezpečení MySQL se zmírňují upgradem MySQL na verzi 5.7.23.

#### <a name="manual-upgrade-for-php-and-mysql-on-csps-and-rx"></a>Ruční upgrade pro PHP a MySQL v CS, PS a RX
Skriptovací platforma PHP by se měla upgradovat na verzi 7.2.10 na konfiguračním serveru, procesovém serveru a serveru pro příjem.
Systém správy databáze MySQL by měl být upgradován na verzi 5.7.23 na konfiguračním serveru, procesovém serveru a serveru pro příjem.
Pokud chcete upgradovat verze PHP a MySQL, postupujte prosím podle kroků v příručce k [rychlé instalaci](https://aka.ms/asr-scout-quick-install-guide) .

### <a name="site-recovery-scout-801-update-6"></a>Site Recovery Scout 8.0.1 Update 6 
Aktualizováno: 12. října 2017

Stáhněte si [aktualizaci Scout Update 6](https://aka.ms/asr-scout-update6).

Scout Update 6 je kumulativní aktualizace. Obsahuje všechny opravy z aktualizace Update 1 pro aktualizaci 5 a nové opravy a vylepšení popsané níže. 

#### <a name="new-platform-support"></a>Podpora nové platformy
* Přidala se podpora pro zdrojový Windows Server 2016.
* Přidala se podpora pro následující operační systémy Linux:
    - Red Hat Enterprise Linux (RHEL) 6,9
    - CentOS 6,9
    - Oracle Linux 5,11
    - Oracle Linux 6,8
* Pro VMware Center 6,5 se přidala podpora.

Aktualizace nainstalujte následujícím způsobem:

> [!NOTE]
>Všechny součásti Scout Components v souboru Update. zip nemusejí být stejné. Starší verze značí, že od předchozí aktualizace k této aktualizaci se nezměnila součást.

Stáhněte si soubor [Update](https://aka.ms/asr-scout-update6) . zip. Soubor obsahuje následující součásti: 
- RX_8 RX_8.0.4.0_GA_Update_4_8725872_16Sep16. tar. gz
- CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
- UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
- UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz
- vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
- UA Update4 bitů pro RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_ \<Linux OS> _8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz
  1. Extrahujte soubory. zip.
  2. **Server pro příjem**: Zkopírujte **RX_8.0.4.0_GA_Update_4_8725872_16Sep16. tar. gz** na server pro příjem a rozbalte ho. V extrahované složce spusťte příkaz **/install**.
  3. **Konfigurační server a procesový Server**: Zkopírujte **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** na konfigurační server a procesový Server. Dvojím kliknutím ji spusťte.<br>
  4. **Hlavní cílový server Windows**: Pokud chcete aktualizovat sjednoceného agenta, zkopírujte **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** na server. Dvakrát klikněte na ni a spusťte ji. Stejná aktualizace sjednoceného agenta se vztahuje také na zdrojový server. Pokud se zdroj neaktualizoval na Update 4, měli byste aktualizovat sjednoceného agenta.
  Aktualizace nemusí platit pro hlavní cíl připravený s **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe**  , protože se jedná o nový instalační program GA se všemi nejnovějšími změnami.
  5. **vContinuum Server**: Zkopírujte **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** na server.  Ujistěte se, že jste Průvodce vContinuum zavřeli. Dvakrát klikněte na soubor a spusťte ho.
  Aktualizace nemusí platit pro hlavní cíl připravený s **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** , protože se jedná o nový instalační program GA se všemi nejnovějšími změnami.
  6. **Server pro Linux Master Target**: Pokud chcete aktualizovat sjednoceného agenta, zkopírujte na hlavní cílový server **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz** a extrahujte ho. V extrahované složce spusťte příkaz **/install**.
  7. **Zdrojový server Windows**: Pokud chcete aktualizovat sjednoceného agenta, zkopírujte **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** na zdrojový server. Dvakrát klikněte na soubor a spusťte ho. 
  Nemusíte instalovat agenta Update 5 na zdrojovém serveru, pokud už je aktualizovaný na verzi Update 4 nebo když je zdrojový agent nainstalovaný s nejnovějším základním instalačním programem **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
  8. **Zdrojový server Linux**: Chcete-li aktualizovat sjednoceného agenta, zkopírujte odpovídající verzi souboru sjednoceného agenta na server Linux a rozbalte ji. V extrahované složce spusťte příkaz **/install**.  Příklad: pro RHEL 6,7 64-bit Server zkopírujte **UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16. tar. gz** na server a extrahujte ho. V extrahované složce spusťte příkaz **/install**.


> [!NOTE]
> * Instalace instalačního programu jednotného agenta (UA) pro systém Windows byla aktualizována tak, aby podporovala Windows Server 2016. Nový instalační program **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** je zabalený pomocí základního balíčku Scout GA (**InMage_Scout_Standard_8.0,1 GA-Oct17.zip**). Stejný instalační program bude použit pro všechny podporované verze systému Windows. 
> * Základní instalační program Windows vContinuum & Master byl aktualizován tak, aby podporoval systém Windows Server 2016. Nový instalační program **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** je zabalený pomocí základního balíčku Scout GA (**InMage_Scout_Standard_8.0,1 GA-Oct17.zip**). Stejný instalační program bude použit k nasazení hlavního cíle Windows 2016 a serveru Windows 2012R2 Master Target.
> * Systém Windows Server 2016 na fyzickém serveru není podporován ASR Scout. Podporuje jenom virtuální počítač VMware s Windows serverem 2016. 
>

#### <a name="bug-fixes-and-enhancements"></a>Opravy chyb a vylepšení
- Ochrana před navrácením služeb po obnovení pro virtuální počítač se systémem Linux se nezdařila, protože seznam disků, které mají být replikovány, je

### <a name="site-recovery-scout-801-update-5"></a>Site Recovery Scout 8.0.1 Update 5
Scout Update 5 je kumulativní aktualizace. Obsahuje všechny opravy z aktualizace Update 1 pro aktualizaci 4 a nové opravy popsané níže.
- Opravy z Site Recovery Scout Update 4 na aktualizaci 5 jsou určené konkrétně pro hlavní cílové a vContinuum součásti.
- Pokud jsou na zdrojových serverech, hlavní cílový server, konfigurace, proces a servery pro příjem dat již spuštěn Update 4, použijte jej pouze na hlavním cílovém serveru. 

#### <a name="new-platform-support"></a>Podpora nové platformy
* SUSE Linux Enterprise Server 11 Service Pack 4 (SP4)
* SLES 11 SP4 64 bitová  **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release. tar. gz** je zabalená pomocí základního balíčku Scout GA (**InMage_Scout_Standard_8.0,1 GA.zip**). Stáhněte si balíček GA z portálu, jak je popsáno v tématu Vytvoření trezoru.


#### <a name="bug-fixes-and-enhancements"></a>Opravy chyb a vylepšení

* Opravy pro zvýšení spolehlivosti podpory clusteru Windows:
    * Opraveno – některé disky clusteru P2V MSCS se po obnovení stanou nezpracovanými.
    * Opravené obnovení clusteru serveru MSCS se nepovede kvůli neshodě pořadí disků.
    * Opraveno – operace clusteru nástroje MSCS pro přidání disků se nezdařila s chybou neshody velikosti disku.
    * Opraveno – Kontrola připravenosti pro zdrojový cluster serveru MSCS s mapováním RDM LUN se nezdařila v rámci ověřování velikosti.
    * Ochrana clusteru s jedním uzlem se nezdařila z důvodu problému se neshodou SCSI. 
    * Pevná Ochrana clusteru P2V Windows Cluster Server se nezdařila, pokud jsou k dispozici cílové disky clusteru. 
    
* Opraveno: Pokud není vybraný hlavní cílový server na stejném serveru ESXi jako chráněný zdrojový počítač (během dopředné ochrany), pak při obnovení navrácení služeb po obnovení dojde v vContinuum k chybě a operace obnovení se nezdaří.

> [!NOTE]
> * Opravy clusteru P2V se vztahují jenom na fyzické clustery MSCS, které jsou nově chráněné pomocí Site Recovery Scout Update 5. Pokud chcete nainstalovat opravy clusteru v chráněných clusterech P2V MSCS se staršími aktualizacemi, postupujte podle kroků pro upgrade uvedených v části 12 [poznámky k verzi služby Site Recovery Scout](https://aka.ms/asr-scout-release-notes).
> * Pokud je v době opětovné ochrany na všech uzlech clusteru aktivní stejná sada disků, jako kdyby byla zpočátku chráněná, může opětovná ochrana fyzického clusteru MSCS znovu použít jenom existující cílové disky. Pokud ne, pak použijte ruční kroky v části 12 poznámky k [verzi Site Recovery Scout](https://aka.ms/asr-scout-release-notes), abyste přesunuli cílové diskové disky na správnou cestu k úložišti dat, abyste je mohli znovu použít při opětovné ochraně. Pokud znovu nastavíte ochranu clusteru MSCS v režimu P2V bez provedení kroků upgradu, vytvoří se na cílovém serveru ESXi nový disk. Staré disky budete muset odstranit z úložiště dat ručně.
> * Pokud se server SLES11 nebo SLES11 (se všemi aktualizacemi Service Pack) restartuje řádně, pak ručně označte páry replikace **kořenového** disku pro opakovanou synchronizaci. V rozhraní CX není žádné oznámení. Pokud neoznačíte kořenový disk pro opakovanou synchronizaci, můžete si všimnout problémů s integritou dat.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 je kumulativní aktualizace. Zahrnuje všechny opravy od aktualizace Update 1 až po aktualizaci 3 a nové opravy popsané níže.

#### <a name="new-platform-support"></a>Podpora nové platformy

* Přidala se podpora pro vCenter/vSphere 6,0, 6,1 a 6,2.
* Přidala se podpora pro tyto operační systémy Linux:
  * Red Hat Enterprise Linux (RHEL) 7,0, 7,1 a 7,2
  * CentOS 7,0, 7,1 a 7,2
  * Red Hat Enterprise Linux (RHEL) 6,8
  * CentOS 6,8

> [!NOTE]
> RHEL/CentOS 7 64 bitová  **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release. tar. gz** je zabalena pomocí základního balíčku Scout GA **InMage_Scout_Standard_8.0,1 GA.zip**. Stáhněte si balíček Scout GA z portálu, jak je popsáno v části Vytvoření trezoru.

#### <a name="bug-fixes-and-enhancements"></a>Opravy chyb a vylepšení

* Vylepšené zpracování vypnutí pro následující operační systémy Linux a klony, aby nedocházelo k nežádoucím potížím při opakované synchronizaci:
    * Red Hat Enterprise Linux (RHEL) 6. x
    * Oracle Linux (OL) 6. x
* Pro Linux se všechna oprávnění k přístupu ke složkám v adresáři sjednocené instalace agenta teď omezují jenom na místního uživatele.
* Ve Windows se jedná o opravu problému s časováním, ke kterému došlo při vystavování běžných distribuovaných záložek konzistence, a to na silně načtené distribuované aplikace, jako jsou SQL Server a clustery bodu sdílení.
* Oprava související s protokolem v základní instalační službě konfiguračního serveru.
* Odkaz ke stažení pro VMware vCLI 6,0 se přidal do základního instalačního programu Windows Master Target.
* Pro změny konfigurace sítě během převzetí služeb při selhání a zotavení po havárii se přidaly další kontroly a protokoly.
* Oprava problému, který způsobil, že informace o uchovávání dat nejsou hlášeny ke konfiguračnímu serveru.  
* U fyzických clusterů je oprava problému, který způsobil selhání změny velikosti svazku, v průvodci vContinuum při zmenšování zdrojového svazku.
* Oprava problému s ochranou clusteru, který se nezdařil s chybou: "Nepodařilo se najít podpis disku", pokud je disk clusteru PRDM disk.
* Oprava pro chybu přenosového serveru cxps, která je způsobená výjimkou mimo rozsah.
* Sloupce název serveru a IP adresa je teď možné měnit na stránce pro **nabízenou instalaci** v průvodci vContinuum.
* Vylepšení rozhraní API pro příjem:
  * K dispozici jsou pět nejnovějších dostupných běžných bodů konzistence (jenom garantované značky).
  * Pro všechna chráněná zařízení se zobrazí podrobnosti o kapacitě a volném prostoru.
  * Stav ovladače Scout na zdrojovém serveru je k dispozici.

> [!NOTE]
> * Základní balíček **InMage_Scout_Standard_8.0.1_GA.zip** obsahuje:
>     * Aktualizovaný základní instalační program konfiguračního serveru (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
>     * Základní instalační program systému Windows pro hlavní cíl (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
>     * Pro všechny nové instalace použijte nový konfigurační server a hlavní cílový server Windows v g bitů.
> * Aktualizaci 4 lze použít přímo na 8.0.1 GA.
> * Konfigurační server a aktualizace pro příjem se po použití nedají vrátit zpátky.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3

Všechny aktualizace Site Recovery jsou kumulativní. Aktualizace 3 obsahuje všechny opravy z Update 1 a Update 2. Aktualizaci 3 lze použít přímo na 8.0.1 GA. Konfigurační server a aktualizace pro příjem se po použití nedají vrátit zpátky.

#### <a name="bug-fixes-and-enhancements"></a>Opravy chyb a vylepšení
Aktualizace 3 opravuje následující problémy:

* Konfigurační server a RX nejsou v trezoru zaregistrované, pokud se nachází za proxy serverem.
* Počet hodin, ve kterých se nedosáhlo cíle bodu obnovení (RPO), se v sestavě stavu neaktualizuje.
* Konfigurační server se nesynchronizuje se systémem RX, pokud podrobnosti o hardwaru ESX nebo podrobnosti sítě obsahují jakékoli znaky UTF-8.
* Řadiče domény se systémem Windows Server 2008 R2 se po obnovení nespustí.
* Offline synchronizace nefunguje podle očekávání.
* Po převzetí služeb virtuálního počítače při selhání nepokračuje v konzole konfiguračního serveru po dlouhou dobu odstranění páru replikace. Uživatelé nemůžou dokončit operace navrácení služeb po obnovení nebo obnovení.
* Byly optimalizované celkové operace snímkování úlohou konzistence, které vám pomůžou snižovat odpojení aplikací, jako jsou SQL Server klienti.
* Vylepšili jsme výkon nástroje pro konzistenci (VACP.exe). Využití paměti vyžadované pro vytváření snímků ve Windows se snížilo.
* Pokud je heslo delší než 16 znaků, služba push Install dojde k chybě.
* vContinuum nekontroluje a vyzývá nové přihlašovací údaje pro vCenter při změně přihlašovacích údajů.
* V systému Linux hlavní cílový správce mezipaměti (cachemgr) nestahuje soubory z procesového serveru. Výsledkem je omezení dvojice replikací.
* Pokud není pořadí fyzického disku clusteru s podporou převzetí služeb při selhání na všech uzlech stejný, replikace není nastavená pro některé svazky clusteru. Aby bylo možné tuto opravu využít, musí být cluster znovu chráněn.  
* Funkce protokolu SMTP nefunguje podle očekávání, po upgradu na RX z verze Scout 7,1 na Scout 8.0.1.
* Do protokolu pro operaci vrácení zpět se přidalo více statistik, aby se mohla sledovat doba potřebná k jejímu dokončení.
* Na zdrojovém serveru se přidala podpora pro operační systémy Linux:
  * Red Hat Enterprise Linux (RHEL) 6 – aktualizace 7
  * CentOS 6 – aktualizace 7
* Konfigurační server a Konzola pro příjem nyní zobrazují oznámení pro dvojici, která přecházejí do režimu rastrového obrázku.
* V RX byly přidány následující opravy zabezpečení:
    * Obcházení autorizace prostřednictvím manipulace s parametry: omezený přístup k neplatným uživatelům.
    * Padělání žádostí mezi lokalitami: koncept tokenu stránky byl implementován a vygeneruje se náhodně pro každou stránku. To znamená, že pro stejného uživatele je k dispozici pouze jedna instance jednotného přihlašování a aktualizace stránky nefunguje. Místo toho se přesměruje na řídicí panel.
    * Nahrání škodlivého souboru: soubory jsou omezené na konkrétní rozšíření: z, AIFF, ASF, AVI, BMP, CSV, doc, DOCX, FLA, FLV, GIF, GZ, gzip, JPEG, jpg, log, Mid, MOV, MP3, MP4, MPC, MPEG, MPG, ODS, ODT, PDF, PNG, PPT, PPTX, PXD, QT, RAM, RAR,, VSD,, sitd, SXC, tar, SXW, TIF, TIFF, txt, VSD, WAV, WMA, WMV, XLS , XLSX, XML a zip.
    * Trvalé skriptování mezi weby: přidaly se vstupní ověřování.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Update 2 (aktualizace 03Dec15)

Opravy ve Update 2 zahrnují:

* **Konfigurační server**: problémy, které zabránily funkci měření na 31 dní v případě, že byl konfigurační server zaregistrován do Azure Site Recovery trezoru, nefunguje podle očekávání.
* **Unified agent**: Opravte problém v aktualizaci Update 1, který vedl k instalaci aktualizace na hlavní cílový server během upgradu z verze 8,0 na 8.0.1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Aktualizace 1 obsahuje následující opravy chyb a nové funkce:

* 31 dní bezplatné ochrany na instanci serveru. To vám umožní testovat funkce nebo nastavit testování konceptu.
* Všechny operace na serveru, včetně převzetí služeb při selhání a navrácení služeb po obnovení, jsou v prvních 31 dnech zdarma. Čas začíná při první ochraně serveru pomocí Site Recovery Scout. Od 32nd dne se každý chráněný Server účtuje za standardní sazbu instance, která se Site Recovery ochrana na webu vlastněné zákazníkem.
* Počet chráněných serverů, které jsou momentálně účtované, je kdykoli k dispozici na **řídicím panelu** v trezoru.
* Byla přidána podpora pro rozhraní vSphere Command-Line Interface (vCLI) 5,5 Update 2.
* Na zdrojovém serveru se přidala podpora pro tyto operační systémy Linux:
    * RHEL 6 – aktualizace 6
    * RHEL 5 – aktualizace 11
    * CentOS 6 – aktualizace 6
    * CentOS 5 – aktualizace 11
* Opravy chyb, které řeší následující problémy:
  * Registrace trezoru se pro konfigurační server nebo server pro příjem nezdařila.
  * Po obnovení ochrany clusterových virtuálních počítačů se svazky clusteru nezobrazí podle očekávání.
  * Navrácení služeb po obnovení se nepovede, když je hlavní cílový server hostovaný na jiném serveru ESXi z místních produkčních virtuálních počítačů.
  * Oprávnění ke konfiguračnímu souboru se po upgradu na 8.0.1 mění. Tato změna má vliv na ochranu a operace.
  * Prahová hodnota opakované synchronizace není vynucena podle očekávání a způsobuje nekonzistentní chování replikace.
  * Nastavení RPO se v konzole konfiguračního serveru nezobrazují správně. Hodnota nekomprimovaných dat nesprávně zobrazuje komprimovanou hodnotu.
  * Operace odebrání se v průvodci vContinuum neodstraní podle očekávání a replikace se z konzoly konfiguračního serveru neodstraní.
  * Když v průvodci vContinuum kliknete na **Podrobnosti** v zobrazení disku, během ochrany virtuálních počítačů MSCS se disk automaticky nevybere.
  * V případě fyzického na virtuální (P2V) se požadované služby HP (například CIMnotify a CqMgHost) nepřesunou na ruční v rámci obnovení virtuálních počítačů. Výsledkem tohoto problému je další čas spuštění.
  * Ochrana virtuálního počítače se systémem Linux se nezdařila, pokud je na hlavním cílovém serveru více než 26 disků.

