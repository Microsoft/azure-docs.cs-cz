---
title: Nastavit zotavení po havárii virtuálních počítačů VMware nebo fyzických serverů do sekundární lokality s Azure Site Recovery | Microsoft Docs
description: Zjistěte, jak nastavit zotavení po havárii virtuální počítače VMware, nebo systému Windows a Linux fyzických serverů, na sekundární lokalitu s Azure Site Recovery.
services: site-recovery
documentationcenter: ''
author: nsoneji
manager: gauarvd
editor: ''
ms.assetid: 68616d15-398c-4f40-8323-17b6ae1e65c0
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/12/2017
ms.author: raynew
ms.openlocfilehash: 9de0cb3d22a4e500b66640f70f936ae955bbadb8
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677417"
---
# <a name="set-up-disaster-recovery-of-on-premises-vmware-virtual-machines-or-physical-servers-to-a-secondary-site"></a>Nastavit zotavení po havárii na lokální virtuální počítače VMware nebo fyzických serverů do sekundární lokality

InMage Scout v [Azure Site Recovery](site-recovery-overview.md) poskytuje v reálném čase replikaci mezi místními servery VMware. InMage Scout je součástí předplatného služby Azure Site Recovery. 


## <a name="prerequisites"></a>Požadavky

Pro absolvování tohoto kurzu potřebujete:

- [Zkontrolujte](site-recovery-support-matrix-to-sec-site.md) požadavky na podporu pro všechny součásti.
- Ujistěte se, že počítače, které chcete replikovat vyhovují [replikovat podpora počítačů](site-recovery-support-matrix-to-sec-site.md#support-for-replicated-machine-os-versions).


## <a name="create-a-vault"></a>Vytvoření trezoru

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="choose-a-protection-goal"></a>Vyberte cíl ochrany

Vyberte obsah k replikovat a kam jej chcete replikovat.

1. Klikněte na tlačítko **Site Recovery** > **Příprava infrastruktury** > **cíl ochrany**.
2. Vyberte **k obnovení lokality** > **Ano, s hypervisoru VMware vSphere**. Pak klikněte na **OK**.
3. V **Scout instalace**, stáhněte si software GA InMage Scout 8.0.1 a registrační klíč. Instalační soubory pro všechny součásti jsou zahrnuty v souboru ZIP staženého.

## <a name="download-and-install-component-updates"></a>Stáhnout a nainstalovat aktualizace součástí

 Přečtěte si a nainstalujte si nejnovější verzi [aktualizace](#updates). Aktualizace by měla být nainstalována na serverech v následujícím pořadí:

1. RX serveru (pokud existuje)
2. Konfigurace serverů
3. Proces servery
4. Servery hlavního cílového serveru
5. vContinuum servery
6. Zdrojový server (Windows i servery se systémem Linux)

Nainstalujte aktualizace následujícím způsobem:

> [!NOTE]
>Všechny součásti Scout souboru aktualizovanou verzi nemusí být stejný v souboru ZIP aktualizace. Starší verzi označení, že žádná změna v komponentě od předchozí aktualizace této aktualizace.

Stažení [aktualizace](https://aka.ms/asr-scout-update6) soubor .zip. Soubor obsahuje následující součásti: 
  - RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz
  - CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe
  - UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe
  - UA_RHEL6-64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
  - vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe
  - UA update4 bits for RHEL5, OL5, OL6, SUSE 10, SUSE 11: UA_<Linux OS>_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz
1. Rozbalte soubory .zip.
2. **RX server**: kopírování **RX_8.0.4.0_GA_Update_4_8725872_16Sep16.tar.gz** k serveru RX a rozbalte ho. V rozbalené složce spustit **/Install**.
3. **Konfigurační server a procesový server**: kopírování **CX_Windows_8.0.6.0_GA_Update_6_13746667_18Sep17.exe** konfigurační server a procesový server. Dvakrát klikněte na spustit.<br>
4. **Windows hlavní cílový server**: aktualizace jednotná agenta, zkopírujte **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** k serveru. Dvojím kliknutím ji spustit. Stejné aktualizace jednotná agenta platí také pro zdrojový server. Pokud zdroj nebyl aktualizován na aktualizace 4, je třeba aktualizovat unified agent.
  Aktualizace není potřeba použít na hlavním cílovém připravený pomocí **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** , protože tento novým instalačním programem GA vzhledem k nejnovějším změnám.
5. **vContinuum server**: kopírování **vCon_Windows_8.0.6.0_GA_Update_6_11525767_21Sep17.exe** k serveru.  Ujistěte se, že jste zavřeli Průvodce vContinuum. Poklikejte na soubor ji spustit.
    Aktualizace není potřeba použít na hlavního cíle připravený pomocí **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** , protože tento novým instalačním programem GA vzhledem k nejnovějším změnám.
6. **Hlavní cílový server Linux**: aktualizace jednotná agenta, zkopírujte **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** na hlavní server cílového serveru a rozbalte ho. V rozbalené složce spustit **/Install**.
7. **Zdrojový server Windows**: aktualizace jednotná agenta, zkopírujte **UA_Windows_8.0.5.0_GA_Update_5_11525802_20Apr17.exe** na zdrojovém serveru. Poklikejte na soubor ji spustit. 
    Nepotřebujete pro instalaci agenta aktualizací 5 na zdrojovém serveru, pokud se už aktualizovala Update 4 nebo s nejnovější základní verzi instalačního programu je nainstalován agent zdroj **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe**.
8. **Zdrojový server Linux**: aktualizace jednotná agenta, zkopírujte odpovídající verzi souboru jednotná agenta na Linux server a rozbalte ho. V rozbalené složce spustit **/Install**.  Příklad: Pro RHEL 6,7 64bitový server, zkopírujte **UA_RHEL6 64_8.0.4.0_GA_Update_4_9035261_26Sep16.tar.gz** na server a rozbalte ho. V rozbalené složce spustit **/Install**.

## <a name="enable-replication"></a>Povolení replikace

1. Nastavení replikace mezi zdrojové a cílové lokality VMware.
2. Získáte další informace o instalaci, ochrany a obnovení následujících dokumentech:

   * [Poznámky k verzi](https://aka.ms/asr-scout-release-notes)
   * [Matice kompatibility](https://aka.ms/asr-scout-cm)
   * [Uživatelská příručka](https://aka.ms/asr-scout-user-guide)
   * [RX uživatelská příručka](https://aka.ms/asr-scout-rx-user-guide)
   * [Příručka pro rychlou instalaci](https://aka.ms/asr-scout-quick-install-guide)

## <a name="updates"></a>Aktualizace

### <a name="site-recovery-scout-801-update-6"></a>Aktualizace Scout 8.0.1 obnovení lokality 6 
Aktualizované: 12 říjen 2017

Stáhněte si [Scout aktualizací 6](https://aka.ms/asr-scout-update6).

Scout aktualizací 6 je kumulativní aktualizace. Obsahuje všechny opravy z Update 1 aktualizací 5 plus nové opravy a vylepšení, které jsou popsané dál. 

#### <a name="new-platform-support"></a>Nová podpora platformy
* Byla přidána podpora pro zdroj systému Windows Server 2016
* Byla přidána podpora pro následující operační systémy Linux:
    - Red Hat Enterprise Linux (RHEL) 6.9
    - CentOS 6.9
    - Oracle Linux 5,11
    - Oracle Linux 6.8
* Byla přidána podpora pro verze 6.5 Center VMware

> [!NOTE]
> * Základní Unified Agent(UA) Instalační služby systému Windows byla aktualizovat podpory systému Windows Server 2016. Nového instalačního programu **InMage_UA_8.0.1.0_Windows_GA_28Sep2017_release.exe** je součástí balíčku balíček základní Scout GA (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Stejný instalační program použije pro všechny podporované verze systému Windows. 
> * Základní Windows vContinuum & hlavního cíle instalační program byl aktualizovaný pro podporu systému Windows Server 2016. Nového instalačního programu **InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_10Oct2017_release.exe** je součástí balíčku balíček základní Scout GA (**InMage_Scout_Standard_8.0.1 GA-Oct17.zip**). Stejný instalační program použije pro nasazení systému Windows 2016 hlavního cíle a hlavního cíle Windows 2012 R2.
> * Stáhněte si balíček GA z portálu, jak je popsáno v [vytvoření trezoru](#create-a-vault).
>

#### <a name="bug-fixes-and-enhancements"></a>Opravy chyb a vylepšení
- Navrácení služeb po obnovení selže ochrany pro virtuální počítač s Linuxem se seznam disků, které se budou replikovat je prázdný na konci konfigurace.

### <a name="site-recovery-scout-801-update-5"></a>Aktualizace Scout 8.0.1 obnovení lokality 5
Kumulativní aktualizace je Scout aktualizace 5. Obsahuje všechny opravy z Update 1 do 4 aktualizace a nové opravy se popisuje níže.
- Opravy z lokality obnovení Scout aktualizace 4 až 5 aktualizace jsou speciálně určené pro hlavní cíl a vContinuum součásti.
- Pokud zdrojové servery, hlavní cíl, konfiguraci, proces a RX servery běží již aktualizace 4, můžete použít pouze v hlavní cílový server. 

#### <a name="new-platform-support"></a>Nová podpora platformy
* SUSE Linux Enterprise Server 11 Service Pack 4(SP4)
* 64bitový SLES 11 SP4 **InMage_UA_8.0.1.0_SLES11-SP4-64_GA_13Apr2017_release.tar.gz** je součástí balíčku balíček základní Scout GA (**InMage_Scout_Standard_8.0.1 GA.zip**). Stáhněte si balíček GA z portálu, jak je popsáno v [vytvoření trezoru](#create-a-vault).


#### <a name="bug-fixes-and-enhancements"></a>Opravy chyb a vylepšení

* Opravy pro vyšší clusteru systému Windows podporují spolehlivosti:
    * Fixed – některé P2V MSCS diskům stát clusteru RAW po obnovení.
    * Obnovení clusteru fixed-P2V MSCS nezdaří z důvodu neshody pořadí disku.
    * Fixed – MSCS clusteru operace přidání disky selže s chybou neshoda velikost disku.
    * Kontrola připravenosti v pro zdrojový clusteru MSCS pomocí mapování RDM logických jednotek v velikost ověření selže.
    * Fixed-jeden uzel clusteru ochrana se nezdaří z důvodu neshody problémem SCSI. 
    * Znovu zapnout fixed-ochrana P2V server systému Windows clusteru selže, pokud nejsou cílové disky clusteru. 
    
* Pevná: Během navrácení služeb po obnovení ochrany, pokud vybraný hlavní cíl serveru není na stejném serveru jako chráněný zdrojový počítač ESXi (během dopředného ochrany), pak vContinuum převezme nesprávný hlavní cílový server během obnovení navrácení služeb po obnovení a obnovení operace se nezdaří.

> [!NOTE]
> * Opravy clusteru P2V se vztahují pouze na fyzické MSCS clustery, které nově jsou chráněné službou Site Recovery Scout aktualizací 5. Nainstalovat na chráněné P2V MSCS clusterů s aktualizací starší opravy clusteru, postupujte podle kroků upgradu uvedené v části 12 [poznámky k verzi lokality obnovení Scout](https://aka.ms/asr-scout-release-notes).
> * Pokud v době znovu zapnout ochrana, jsou na všech uzlech clusteru aktivní stejnou sadu disků, jako když původně chráněný, pak znovu zapnout ochrana fyzického clusteru MSCS pouze znovu použít stávající cílové disky. Pokud ne, použijte ruční kroky v části 12 [poznámky k verzi lokality obnovení Scout](https://aka.ms/asr-scout-release-notes), přesunout cílové disky straně do správného úložiště cesty pro opakované použití během znovu zapnout ochrana. Pokud znovu nastavte ochranu clusteru MSCS v režimu P2V bez upgradu kroků vytvoří na cílovém serveru ESXi nový disk. Musíte ručně odstranit staré disky z úložiště.
> * Když řádně restartování serveru zdroj SLES11 nebo SLES11 (s žádné aktualizace service pack), pak ručně označit **kořenové** disku páry replikace na opětovnou synchronizaci. Nejsou žádná oznámení v CX rozhraní. Pokud nemáte označit kořenovém disku pro opětovnou synchronizaci, můžete si všimnout problémy s integritou dat.


### <a name="azure-site-recovery-scout-801-update-4"></a>Azure Site Recovery Scout 8.0.1 aktualizací 4
Scout Update 4 je kumulativní aktualizace. Obsahuje všechny opravy od 1 aktualizace Update 3 a nové opravy se popisuje níže.

#### <a name="new-platform-support"></a>Nová podpora platformy

* Byla přidána podpora pro vCenter/vSphere 6.0, 6.1 a 6.2
* Byla přidána podpora těchto operačních systémů Linux:
  * Red Hat Enterprise Linux (RHEL) 7.0, 7.1 a 7.2
  * CentOS 7.0, 7.1 a 7.2
  * Red Hat Enterprise Linux (RHEL) 6.8
  * CentOS 6.8

> [!NOTE]
> RHEL nebo CentOS 7 64bitové **InMage_UA_8.0.1.0_RHEL7-64_GA_06Oct2016_release.tar.gz** je součástí balíčku balíček základní Scout GA **InMage_Scout_Standard_8.0.1 GA.zip**. Stáhněte si balíček Scout GA z portálu, jak je popsáno v [vytvoření trezoru](#create-a-vault).

#### <a name="bug-fixes-and-enhancements"></a>Opravy chyb a vylepšení

* Vylepšené vypnutí zpracování pro následující operační systémy Linux a klonovat, aby se zabránilo problémům nežádoucí opakované synchronizace:
    * Red Hat Enterprise Linux (RHEL) 6.x
    * Oracle Linux (OL) 6.x
* Pro systémy Linux jsou všechna oprávnění k přístupu v instalačním adresáři nástroje unified agent nyní omezena na místního uživatele.
* V systému Windows, oprava vypršení časového limitu problém, který došlo k chybě při vystavování běžné distribuované konzistence záložky na velkém zatížení distribuované aplikace jako clustery SQL Server a bodu sdílené složky.
* Oprava související protokolu v instalačním programu základní konfigurace serveru.
* Odkaz ke stažení na VMware vCLI 6.0 byl přidán do základní instalační hlavní cíl.
* Další kontroly a protokoly byly přidány, pro změn konfigurace sítě během převzetí služeb při selhání a po havárii obnovení podrobněji.
* Oprava problému, která způsobila, že informace o zachování není třeba uvádět konfigurační server.  
* Pro fyzické clustery opravu problému, která způsobila selhání v Průvodci vContinuum při zmenšování zdrojový svazek změnu velikosti svazku.
* Oprava problému ochrany clusteru, který se nezdařilo s chybou: "Nepodařilo se najít podpisu disku", pokud je disk clusteru PRDM disku.
* Oprava serveru havárie cxps přenosu, způsobené výjimku out-of-range.
* Název serveru a IP adresu sloupce lze nyní upravit **nabízené instalace** stránce průvodce vContinuum.
* Vylepšení RX rozhraní API:
  * Pět nejnovější dostupné běžné konzistence body nyní k dispozici (pouze zaručena značek).
  * Kapacita a volné místo podrobnosti jsou zobrazeny pro všechny chráněné zařízení.
  * Stav Scout ovladačů na zdrojovém serveru je k dispozici.

> [!NOTE]
> * **InMage_Scout_Standard_8.0.1_GA.zip** základní balíček obsahuje:
    * Aktualizovanou konfiguraci instalační program základní server (**InMage_CX_8.0.1.0_Windows_GA_26Feb2015_release.exe**)
    * Hlavním cílovém základní instalační služby systému Windows (**InMage_Scout_vContinuum_MT_8.0.1.0_Windows_GA_26Feb2015_release.exe**).
    * Pro všechna nová zařízení použijte nové konfigurační server a Windows hlavní cíl GA bits.
> * Aktualizaci 4 můžete nainstalovat přímo na 8.0.1 Všeobecné
> * Konfigurační server a aktualizace RX nedá se vrátit zpět po jste použili.


### <a name="azure-site-recovery-scout-801-update-3"></a>Azure Site Recovery Scout 8.0.1 aktualizací 3

Všechny Site Recovery aktualizace jsou kumulativní. Aktualizace 3 obsahuje všechny opravy z Update 1 a Update 2. Update 3 můžete použít přímo na 8.0.1 Všeobecné Konfigurační server a aktualizace RX nedá se vrátit zpět po jste použili.

#### <a name="bug-fixes-and-enhancements"></a>Opravy chyb a vylepšení
Aktualizace 3 řeší následující problémy:

* Konfigurační server a RX nejsou registrované v trezoru, kdy jsou za proxy.
* Počet hodin, ve kterých nebylo dosaženo plánovaného bodu obnovení (RPO) se neaktualizuje v sestavě stavu.
* Konfigurační server není synchronizuje s RX při podrobnosti hardwaru ESX nebo podrobnosti o síti, obsahují znaky znakové sady UTF-8.
* Řadiče domény systému Windows Server 2008 R2 nespouštějte po obnovení.
* Offline synchronizace nefungují podle očekávání.
* Po převzetí služeb při selhání virtuálního počítače nepodporuje odstranění replikace páru průběhu v konzole nástroje Konfigurace serveru po dlouhou dobu. Uživatele nelze dokončit navrácení služeb po obnovení nebo obnovit operace.
* Celkové byly optimalizovány snímku operací úloha konzistence, snížit aplikace odpojí např. do klientů systému SQL Server.
* Vylepšili jsme výkon konzistence nástroj (VACP.exe). Snížil se využití paměti, které jsou potřebné pro vytváření snímků v systému Windows.
* Vynucené instalace služby dojde k chybě při heslo je větší než 16 znaků.
* vContinuum není zkontrolujte a vyzvat, aby nová pověření vCenter při změně přihlašovacích údajů.
* V systému Linux není správce mezipaměti hlavní cíl (cachemgr) stahování souborů z procesového serveru. Výsledkem omezení pár replikace.
* Když pořadí převzetí služeb při selhání fyzického disku clusteru (MSCS) není stejná ve všech uzlech, replikace není nastaven pro některé svazky clusteru. Cluster musí být reprotected využít této opravy.  
* Funkce SMTP nefunguje dle očekávání, po upgradu RX Scout 7.1 k Scout 8.0.1.
* V protokolu operaci vrácení zpět, sledovat čas potřebný k dokončení ho byly přidány další statistiky.
* Byla přidána podpora pro operační systémy Linux na zdrojovém serveru:
  * Red Hat Enterprise Linux (RHEL) 6 aktualizace 7
  * CentOS 6 aktualizace 7
* Konfigurační server a RX konzoly nyní zobrazit oznámení pro dvojici, která přejde do režimu rastrového obrázku.
* V RX byly přidány následující opravy zabezpečení:
    * Autorizace vynechat prostřednictvím parametru manipulaci: omezený přístup k uživatelům nepoužitelné.
    * Padělání požadavku posílaného mezi weby: koncept stránky token byl implementován a generuje náhodně na každé stránce. To znamená, že existuje pouze jedna přihlášení instance pro stejného uživatele, a nebude fungovat stránce aktualizace. Místo toho je přesměrován na řídicí panel.
    * Nahrávání souborů škodlivý: soubory jsou omezeny na konkrétní rozšíření: z, aiff, amp, avi, bmp, csv, dokumentů, docx, fla, flv, gif, gz, gzip, jpeg, jpg, protokolu, mid mov, mp3, mp4, mpc, mpeg, mpg, ods, odt, pdf, png, ppt, pptx, pxd, RT, paměti ram, rar, rm, rmi, rmvb, rtf , sdc, sitd, swf, sxc, sxw, vkládání, tgz, tif, tiff, txt, vsd, wav, wma, wmv, xls, xlsx, xml a zip.
    * Trvalé skriptování mezi servery: ověření vstupu byly přidány.

### <a name="azure-site-recovery-scout-801-update-2-update-03dec15"></a>Azure Site Recovery Scout 8.0.1 Update 2 (03 aktualizace DEC – 15)

Opravy v aktualizaci Update 2 patří:

* **Konfigurační server**: problémy, které brání volné 31 dnů měření funkce fungovat podle očekávání, když se konfigurační server se zaregistroval v Site Recovery.
* **Jednotná agenta**: oprava problému v Update 1, jejichž výsledkem není nainstalována na hlavním cílovém serveru během upgradu z verze 8.0 k 8.0.1 aktualizace.

### <a name="azure-site-recovery-scout-801-update-1"></a>Azure Site Recovery Scout 8.0.1 Update 1
Aktualizace 1 zahrnuje následující oprav chyb a nové funkce:

* 31 dní volné ochrany na instanci serveru. To umožňuje testování funkčnosti, nebo nastavit testování konceptu.
* Všechny operace na serveru, včetně převzetí služeb při selhání a navrácení služeb po obnovení, jsou volné první 31 dní. Čas spustí, když server nejprve chráněné pomocí Scout obnovení lokality. Z 32nd den každý chráněný server výši rychlost standardní instance pro ochranu Site Recovery k lokalitě vlastněných zákazníků.
* V každém okamžiku je k dispozici na počet chráněných serverů aktuálně účtovat **řídicí panel** v trezoru.
* Byla přidána podpora rozhraní příkazového řádku (vCLI) vSphere 5.5 Update 2.
* Přidali jsme podporu pro tyto operační systémy Linux na zdrojovém serveru:
    * RHEL 6 Update 6
    * RHEL 5 Update 11
    * CentOS 6 Update 6
    * CentOS 5 Update 11
* Opravy chyb pro adres následující problémy:
  * Pro konfigurační server nebo RX server selže registrace trezoru.
  * Svazky clusteru se nezobrazí, jako očekávané když clusterovaný, že virtuální počítače se znovu jako v případě jejich obnovení.
  * Navrácení služeb po obnovení selže, když hlavní cílový server je hostovaná na jiném serveru ESXi od provozu místní virtuální počítače.
  * Při upgradu na 8.0.1 se změní konfiguraci souboru oprávnění. Tato změna ovlivňuje ochrany a operace.
  * Prahová hodnota Opakovaná synchronizace se nevynucuje podle očekávání, způsobuje nekonzistentní replikace chování.
  * Nastavení plánovaného bodu obnovení v konzole nástroje Konfigurace serveru nezobrazí správně. Hodnota nekomprimovaných dat se nesprávně zobrazuje komprimované hodnota.
  * Operace odebrání nedojde k odstranění podle očekávání v Průvodci vContinuum a replikace není odstraněný z konzole pro konfiguraci serveru.
  * V Průvodci vContinuum disku je automaticky při kliknutí na tlačítko **podrobnosti** v zobrazení disk během ochrany MSCS virtuálních počítačů.
  * Ve scénáři, fyzického na virtuální (P2V) nejsou požadované HP služby (například CIMnotify a CqMgHost) přesunout na ruční v obnovení virtuálního počítače. Tento problém výsledkem další spuštění.
  * Virtuální počítač s Linuxem ochrana se nezdaří, pokud jsou více než 26 disků na hlavním cílovém serveru.

