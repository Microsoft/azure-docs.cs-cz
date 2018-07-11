---
title: Nastavení zotavení po havárii virtuálních počítačů VMware nebo fyzické servery do sekundární lokality pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak nastavit zotavení po havárii virtuálních počítačů VMware, nebo Windows a Linuxem fyzických serverů do sekundární lokality pomocí Azure Site Recovery.
services: site-recovery
author: nsoneji
manager: gauarvd
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.topic: article
ms.date: 07/06/2018
ms.author: raynew
ms.openlocfilehash: 251e2b1f8785408bf441bcbcf3d0fcbdd767a358
ms.sourcegitcommit: a06c4177068aafc8387ddcd54e3071099faf659d
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/09/2018
ms.locfileid: "37917774"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Nastavení zotavení po havárii virtuálních počítačů VMware v místním nebo fyzické servery do sekundární lokality

Nástroje InMage Scout v [Azure Site Recovery](site-recovery-overview.md) poskytuje v reálném čase replikaci mezi místní servery VMware. Nástroje InMage Scout je součástí předplatných služby Azure Site Recovery. 


## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- [Kontrola](vmware-physical-secondary-support-matrix.md) požadavky na podporu pro všechny součásti.
- Ujistěte se, že počítače, které chcete replikovat v souladu s [počítač podporu replikovaných](vmware-physical-secondary-support-matrix.md#replicated-vm-support).


## <a name="create-a-vault"></a>Vytvoření trezoru

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="choose-a-protection-goal"></a>Zvolte cíl ochrany

Vyberte, co replikovat a kam jej chcete replikovat.

1. Klikněte na tlačítko **Site Recovery** > **připravit infrastrukturu** > **cíl ochrany**.
2. Vyberte **do lokality pro obnovení** > **Ano, s VMware vSphere Hypervisor**. Pak klikněte na **OK**.
3. V **Scout nastavení**, stáhněte si nástroj InMage Scout 8.0.1 software verze GA. a registrační klíč. Instalační soubory pro všechny součásti jsou zahrnuty v souboru ZIP staženého.

## <a name="download-and-install-component-updates"></a>Stáhněte a nainstalujte aktualizace součástí

 Zkontrolovat a nainstalovat nejnovější [aktualizace](#updates). Aktualizace musí být nainstalován na servery v následujícím pořadí:

1. RX serveru (Pokud je k dispozici)
2. Konfigurace serverů
3. Procesových serverů
4. Hlavní cílové servery
5. vContinuum servery
6. Zdrojový server (Windows a servery se systémem Linux)

Nainstalujte aktualizace následujícím způsobem:

> [!NOTE]
>Všechny komponenty Scout soubor aktualizovanou verzi nemusí být stejné v souboru ZIP aktualizace. Starší verze znamenat, že není žádná změna v komponentě od předchozí aktualizace v této aktualizaci.

Stáhněte si [aktualizovat](https://aka.ms/asr-scout-update6) soubor .zip. Soubor obsahuje následující součásti: 
  - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
  - CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
  - UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
  - UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  - vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
  - Uživatelský Agent update4 bity pro počítač RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
1. Rozbalte soubory .zip.
2. **RX server**: kopírování **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** k příjmu serveru a rozbalte ho. Ve složce extrahované spustit **/Install**.
3. **Konfigurační server a procesový server**: kopírování **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** konfigurační server a procesový server. Dvakrát klikněte na spustit ho.<br>
4. **Windows hlavní cílový server**: Chcete-li aktualizovat nástroj unified agent, zkopírujte **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** k serveru. Dvojím kliknutím ho spustit. Stejnou aktualizaci nástroj unified agent platí také pro zdrojový server. Pokud zdroj se neaktualizoval na s aktualizací Update 4, měli byste aktualizovat nástroj unified agent.
  Aktualizace není nutné použít na hlavním cílovém připravený pomocí **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** je to nový instalační program GA s nejnovějšími změnami.
5. **vContinuum server**: kopírování **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** k serveru.  Ujistěte se, že jsme uzavřeli Průvodce vContinuum. Poklikejte na soubor k jeho spuštění.
    Aktualizace není nutné použít na hlavním cíli připravený pomocí **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** je to nový instalační program GA s nejnovějšími změnami.
6. **Hlavního linuxového cílového serveru**: Chcete-li aktualizovat nástroj unified agent, zkopírujte **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** na hlavní cílový server a rozbalte ho. Ve složce extrahované spustit **/Install**.
7. **Zdrojový server Windows**: Chcete-li aktualizovat nástroj unified agent, zkopírujte **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** na zdrojovém serveru. Poklikejte na soubor k jeho spuštění. 
    Není nutné pro instalaci agenta Update 5 na zdrojovém serveru, pokud již byl aktualizován na aktualizaci Update 4 nebo agenta zdroje nástroje je nainstalovaný nejnovější základní instalační služby systému **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
8. **Zdrojový server Linux**: Chcete-li aktualizovat nástroj unified agent, zkopírujte odpovídající verzi souboru nástroj unified agent na Linux server a rozbalte jej. Ve složce extrahované spustit **/Install**.  Příklad: Pro RHEL 6,7 64bitový server, zkopírujte **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** na server a rozbalte ho. Ve složce extrahované spustit **/Install**.

## <a name="enable-replication"></a>Povolení replikace

1. Nastavení replikace mezi zdrojové a cílové servery VMware.
2. Najdete v následujících dokumentech získat další informace o instalaci, ochranu a obnovení:

   * [Poznámky k verzi](https://aka.ms/asr-scout-release-notes)
   * [Matice kompatibility](https://aka.ms/asr-scout-cm)
   * [Uživatelská příručka](https://aka.ms/asr-scout-user-guide)
   * [Uživatelská příručka k příjmu](https://aka.ms/asr-scout-rx-user-guide)
   * [Příručka pro rychlou instalaci](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Aktualizace

### <a name="site-recovery-scout-801-update-6"></a>Aktualizace služby Site Recovery Scout 8.0.1 6 
Aktualizace: Říjen 12. 2017

Stáhněte si [Scout aktualizace 6](https://aka.ms/asr-scout-update6).

Scout aktualizací 6 je kumulativní aktualizace. Obsahuje všechny opravy od 1 aktualizace Update 5 a nové opravy a vylepšení popsaných níže. 

#### <a name="new-platform-support"></a>Nová podpora platformy
* Byla přidána podpora pro zdroje Windows Server 2016
* Byla přidána podpora pro následujících operačních systémů Linux:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5,11
    - Oracle Linux 6.8
* Byla přidána podpora pro System Center 6.5 VMware

> [!NOTE]
> * Základní Unified Agent(UA) installer pro Windows se obnovil podpory systému Windows Server 2016. V novém instalačním programu **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** je součástí balíčku balíček základní Scout GA (**InMage_Scout_Standard_8.0.1 GA – Oct17.zip**). Stejný instalační program se použije pro všechny podporované verze Windows. 
> * Základní Windows vContinuum & hlavní cílový instalační program byl aktualizován pro podporu Windows serveru 2016. V novém instalačním programu **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** je součástí balíčku balíček základní Scout GA (**InMage_Scout_Standard_8.0.1 GA – Oct17.zip**). Stejný instalační program se použije k nasazení Windows 2016 hlavního cíle a hlavního cíle Windows 2012 R2.
> * Stáhnout balíček verze GA z portálu, jak je popsáno v [vytvořit trezor](#create-a-vault).
>

#### <a name="bug-fixes-and-enhancements"></a>Opravy chyb a vylepšení
- Na konci konfigurace nic selže a ochranu navrácení služeb po obnovení pro virtuální počítač s Linuxem zobrazí se seznam disků k replikaci.

### <a name="site-recovery-scout-801-update-5"></a>Aktualizace služby Site Recovery Scout 8.0.1 5
Aktualizace Scout 5 je kumulativní aktualizace. Obsahuje všechny opravy z Update 1 na aktualizaci Update 4 a nové opravy je popsáno níže.
- Opravy Site Recovery Scout Update 4 Update 5 jsou speciálně pro hlavní cíl a vContinuum součásti.
- Pokud zdrojové servery, hlavní cílový, konfiguraci, proces a příjmu servery jsou již spuštěna verze Update 4 a pak použít pouze na hlavním cílovém serveru. 

#### <a name="new-platform-support"></a>Nová podpora platformy
* SUSE Linux Enterprise Server 11, aktualizace Service Pack 4(SP4)
* 64bitový SLES 11 SP4 **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** je součástí balíčku balíček základní Scout GA (**InMage_Scout_Standard_8.0.1 GA.zip**). Stáhnout balíček verze GA z portálu, jak je popsáno v [vytvořit trezor](#create-a-vault).


#### <a name="bug-fixes-and-enhancements"></a>Opravy chyb a vylepšení

* Opravy pro zvýšení clusteru Windows podporují spolehlivosti:
    * Oprava – některé P2V MSCS clusteru disky budou RAW po obnovení.
    * Obnovení clusteru fixed-P2V MSCS selže z důvodu neshody objednávky disku.
    * Oprava – The MSCS clusteru operace přidání disků selže a zobrazí se chyba neshoda velikosti disku.
    * Kontrola připravenosti v pro zdrojový clusteru MSCS pomocí mapování RDM logických jednotek v velikost ověření selže.
    * Fixed-jeden uzel clusteru ochrana se nezdaří kvůli potížím neshoda SCSI. 
    * Opětovné fixed-ochrany serveru se službou cluster Windows P2V selže, pokud jsou k dispozici cílové disky clusteru. 
    
* Opraveno: Při navrácení služeb po obnovení ochrany, pokud vybraný hlavní cílový server není na stejném serveru ESXi jako chráněný zdrojový počítač (při přesměrování ochrany), pak vContinuum vybere nesprávný hlavní cílový server během navrácení služeb po obnovení pro obnovení a obnovení operace se nezdaří.

> [!NOTE]
> * Opravy clusteru P2V platí pouze pro fyzického clusteru MSCS clustery, které jsou nově chránit pomocí Site Recovery Scout Update 5. Pokud chcete nainstalovat opravy clusteru chráněného clusteru MSCS P2V clusterů s starší aktualizace, provést upgrade kroky uvedené v části 12 [zpráva k vydání verze pro Site Recovery Scout](https://aka.ms/asr-scout-release-notes).
> * Pokud během opětovné ochrany, jsou na všech uzlech clusteru aktivní stejnou sadu disků, jako když původně chráněný, pak znovu ochranu fyzického clusteru MSCS můžete opakovaně používat pouze existující cílové disky. Pokud ne, pak použijte ruční kroky v oddílu 12 [zpráva k vydání verze pro Site Recovery Scout](https://aka.ms/asr-scout-release-notes), přesunout cílové disky na straně do správného úložiště dat cesty pro opakované použití při opětovné ochrany. Pokud znovu nastavit ochranu clusteru MSCS v režimu P2V bez upgradu kroků vytvoří nový disk na cílovém serveru ESXi. Musíte ručně odstranit staré disky z úložiště.
> * Zdroj SLES11 nebo SLES11 (s žádné aktualizace service pack) server se restartoval řádně, pak ručně označit **kořenové** disku páry replikace na opětovnou synchronizaci. Se neobjeví žádná oznámení v rozhraní CX. Pokud není označí disk kořenové pro opětovnou synchronizaci, můžete si všimnout problémy s integritou dat.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 Update 4
Scout Update 4 je kumulativní aktualizace. Zahrnuje všechny opravy aktualizace 1 pro s aktualizací Update 3 a nové opravy je popsáno níže.

#### <a name="new-platform-support"></a>Nová podpora platformy

* Byla přidána podpora pro vCenter/vSphere 6.0, 6.1 a 6.2
* Byla přidána podpora těchto operačních systémů Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 a 7.2
  * CentOS 7.0, 7.1 a 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL/CentOS 7 64-bit **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** je součástí balíčku balíček základní Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**. Stáhněte si balíček Scout GA z portálu, jak je popsáno v [vytvořit trezor](#create-a-vault).

#### <a name="bug-fixes-and-enhancements"></a>Opravy chyb a vylepšení

* Vylepšené vypnutí zpracování následujících operačních systémů Linux a duplicity, aby se zabránilo problémům nežádoucí opětovnou synchronizaci:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Pro Linux je omezena na místního uživatele nyní všechna oprávnění k přístupu v instalačním adresáři nástroje unified agent.
* Na Windows, opravu pro vypršení časového limitu problém, ke které došlo při vydávání běžné distribuované konzistence záložky v silně zatížen distribuovaných aplikací, jako jsou clustery systému SQL Server a bod sdílené složky.
* Oprava protokolu související v instalačním programu základní pro konfigurační server.
* Odkaz ke stažení pro VMware vCLI 6.0 přidal do Windows instalační program základní hlavní cíl.
* Další kontroly a protokoly byly přidány, změny v konfiguraci sítě během cvičení obnovení převzetí služeb při selhání a po havárii.
* Oprava problému, který způsobil informace o zachování nechcete oznámený ke konfiguračnímu serveru.  
* V případě fyzických clusterů opravy problému, který způsobil selhání Průvodce vContinuum při zmenšování zdrojového svazku změnu velikosti svazku.
* Opravu problému ochranu clusteru, která se nezdařila s chybou: "Nepovedlo se najít podpis disku", pokud je disk clusteru PRDM disku.
* Oprava pro server selhání cxps přenosu, způsobené výjimku mimo rozsah.
* Název serveru a IP adresu sloupce jsou nyní změnit v **nabízené instalace** stránky průvodce vContinuum.
* Vylepšení rozhraní API příjmu:
  * Pět nejnovější dostupné běžné konzistence odkazuje nyní k dispozici (zaručeno, značky).
  * Kapacita a volné místo podrobnosti se zobrazí u všech chráněných prostředků.
  * Stav ovladače Scout na zdrojovém serveru je k dispozici.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** základní balíček obsahuje:
    * Instalátor aktualizovanou konfiguraci serveru základní (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * Instalační program základní s Windows hlavní cíl (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
    * Pro všechny nové instalace použijte nové konfigurační server a hlavní cílový GA bits Windows.
> * Aktualizace 4 se dá použít přímo na 8.0.1 Obecné dostupnosti.
> * Konfigurační server a příjmu aktualizace nelze vrátit zpět poté, co jste použili.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 Update 3

Všechny aktualizace Site Recovery jsou kumulativní. Aktualizací Update 3 obsahuje všechny opravy z Update 1 a Update 2. Aktualizací Update 3 je možné použít přímo na 8.0.1 Obecné dostupnosti. Konfigurační server a příjmu aktualizace nelze vrátit zpět poté, co jste použili.

#### <a name="bug-fixes-and-enhancements"></a>Opravy chyb a vylepšení
Aktualizace 3 řeší následující problémy:

* Konfigurační server a příjmu nejsou registrovány v trezoru, když jsou za proxy serverem.
* Počet hodin, ve kterých nebylo dosaženo plánovaného bodu obnovení (RPO), není aktualizován v sestavě o stavu.
* Konfigurační server nesynchronizuje s RX při podrobnosti o hardwaru ESX nebo podrobnosti o síti, obsahovat žádné znaky znakové sady UTF-8.
* Řadiče domény systému Windows Server 2008 R2 nespouštět po obnovení.
* Offline synchronizace nefunguje podle očekávání.
* Po převzetí služeb při selhání virtuálního počítače nebude odstranění replikace páru průběh v konzole nástroje Konfigurace serveru po dlouhou dobu. Uživatele nelze dokončit navrácení služeb po obnovení nebo obnovit provoz.
* Celkově snímkových operací úlohou konzistence byly optimalizovány odstraněním, chcete-li snížit aplikace odpojí např. do klientů systému SQL Server.
* Vylepšili jsme výkon nástroje (VACP.exe) konzistence. Zmenšili jsme využití paměti vyžadované pro vytváření snímků na Windows.
* Vynucené instalace služby chyb, pokud heslo je větší než 16 znaků.
* vContinuum nebude zkontrolujte a výzvu k zadání nové přihlašovací údaje k vCenter, při změně přihlašovacích údajů.
* V Linuxu není správce mezipaměti hlavní cíl (cachemgr) stahování souborů z procesového serveru. Výsledkem je omezení šířky pásma replikace páru.
* Když pořadí převzetí služeb při selhání fyzického disku clusteru (MSCS) není stejná ve všech uzlech, replikaci není nastavena pro některý ze svazků clusteru. Cluster musí znovunastavení ochrany, abyste mohli využít tuto opravu.  
* SMTP funkce nepracuje dle očekávání, po upgradu RX 7.1 Scout k Scout 8.0.1.
* Další statistiky byly přidány do protokolu pro operaci vrácení zpět, sledovat čas potřebný k jejímu dokončení.
* Byla přidána podpora pro operační systémy Linux na zdrojovém serveru:
  * Aktualizace Red Hat Enterprise Linux (RHEL) 6, 7
  * CentOS 6 aktualizace 7
* Konfigurační server a příjmu konzoly teď zobrazit oznámení pro dvojici, která přejde do režimu rastrového obrázku.
* Byly přidány následující opravy zabezpečení v příjmu:
    * Obejít ověřování prostřednictvím parametru manipulaci: omezený přístup uživatelům není použitelné.
    * Padělání žádosti více webů: koncept token stránka byla implementována a vygeneruje náhodně na každou stránku. To znamená, že existuje pouze jedna přihlášení instance pro stejného uživatele, a aktualizace stránky nefunguje. Místo toho je přesměrován na řídicí panel.
    * Nahrání souboru se škodlivým: soubory jsou omezeny na konkrétní rozšíření: z aiff, amp, avi, bmp, sdíleného svazku clusteru, doc, docx, příznaků, flv, gif, gz, gzip, jpeg, jpg, protokolu, střední, mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, qt, paměti ram, rar, rm, rmi, rmvb, formátu rtf , sdc, sitd, swf, sxc, sxw, cíl, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml a zip.
    * Trvalé skriptování napříč weby: ověření vstupu byly přidány.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Aktualizace služby Azure Site Recovery Scout 8.0.1 2 (03 aktualizace 15 prosince)

Opravy v aktualizaci Update 2 patří:

* **Konfigurační server**: problémy, které brání bezplatné 31 dny měření funkce funkčním očekávání, pokud byl konfigurační server zaregistrovaný ve službě Site Recovery.
* **Nástroj Unified agent**: oprava pro problém, kvůli němuž není nainstalována na hlavním cílovém serveru během upgradu z verze 8.0 na 8.0.1 aktualizace Update 1.

### <a name="azure-site-recovery-scout-801-update-1"></a>Aktualizace služby Azure Site Recovery Scout 8.0.1 1
Aktualizace Update 1 zahrnuje následující opravy chyb a nové funkce:

* 31 dnů zdarma ochrany na instanci serveru. To umožňuje otestovat funkci nebo nastavení testování konceptu.
* Všechny operace na serveru, včetně převzetí služeb při selhání a navrácení služeb po obnovení, je zdarma pro prvních 31 dní. Čas začne, když server je nejprve chránit pomocí Site Recovery Scout. Od 32. dne každý chráněný server se účtuje sazbou standardní instance pro ochranu Site Recovery do lokality vlastněné zákazníkem.
* V každém okamžiku je k dispozici na počet chráněných serverů se účtuje **řídicí panel** v trezoru.
* Byla přidána podpora rozhraní příkazového řádku (vCLI) vSphere 5.5 Update 2.
* Přidali jsme podporu těchto operačních systémů Linux na zdrojovém serveru:
    * RHEL 6 aktualizace 6
    * RHEL 5 aktualizace 11
    * CentOS 6 aktualizace 6
    * CentOS 5 aktualizace 11
* Opravy chyb pro řeší následující problémy:
  * Pro konfigurační server nebo RX server neúspěšné registrace trezoru.
  * Svazky clusteru nejsou zobrazeny jako očekávané při clusterovaný, že znovunastavení ochrany virtuálních počítačů podle jejich obnovení.
  * Navrácení služeb po obnovení selže, když hlavní cílový server je hostovaná na jiném serveru ESXi z produkčního prostředí v místním virtuálním počítačům.
  * Konfigurační soubor oprávnění se mění při upgradu na 8.0.1. Tato změna ovlivní ochranu a operace.
  * Prahová hodnota opětovné synchronizace se nevynucuje podle očekávání, způsobuje nekonzistentní replikace chování.
  * Nastavení plánovaného bodu obnovení v konzole pro konfiguraci serveru nezobrazí správně. Hodnota nekomprimovaných dat nesprávně ukazuje komprimované hodnoty.
  * Operace odebrání nedojde k odstranění podle očekávání v nástroji Průvodce vContinuum a replikace se neodstraní z konzoly pro konfigurační server.
  * V Průvodci vContinuum disku je automaticky při kliknutí na **podrobnosti** v zobrazení disku během ochrany virtuálních počítačů v clusteru MSCS.
  * V případě fyzického na virtuální (P2V) nejsou požadované HP služby (například CIMnotify a CqMgHost) přesunout na ruční obnovení virtuálního počítače. Tento problém má za následek další spuštění.
  * Virtuální počítač s Linuxem ochrana se nezdaří, pokud existuje více než 26 disků na hlavním cílovém serveru.

