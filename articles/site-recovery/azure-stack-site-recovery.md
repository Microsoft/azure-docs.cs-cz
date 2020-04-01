---
title: Replikace virtuálních počítačů azure stacku do Azure pomocí Azure Site Recovery | Dokumenty společnosti Microsoft
description: Zjistěte, jak nastavit zotavení po havárii na virtuální počítače Azure for Azure Stack pomocí služby Azure Site Recovery.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: ab35463ca8c3b29e6b4ae8abc781a7081091b214
ms.sourcegitcommit: efefce53f1b75e5d90e27d3fd3719e146983a780
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2020
ms.locfileid: "80478508"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replikace virtuálních počítačů Azure Stack do Azure

Tento článek ukazuje, jak nastavit zotavení po havárii azure zásobníku virtuálních monů do Azure pomocí [služby Azure Site Recovery](site-recovery-overview.md).

Site Recovery přispívá k vaší obchodní kontinuity a zotavení po havárii (BCDR) strategie. Služba zajišťuje, že vaše úlohy virtuálních počítače zůstanou k dispozici, když dojde k očekávaným a neočekávaným výpadkům.

- Site Recovery orchestruje a spravuje replikaci virtuálních počítačů do úložiště Azure.
- Když dojde k výpadku ve vaší primární lokalitě, použijete obnovení webu k převzetí služeb při selhání do Azure.
- Při převzetí služeb při selhání se virtuální počítače Azure vytvářejí z uložených dat virtuálních počítačů a uživatelé můžou dál přistupovat k úlohám spuštěným na těchto virtuálních počítačích Azure.
- Až bude všechno zase v provozu, můžeš vrátit na vlastní účet Azure do primární lokality a začít se znovu replikovat do úložiště Azure.


V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * **Krok 1: Příprava virtuálních počítačích azure zásobníku pro replikaci**. Zkontrolujte, zda virtuální servery splňují požadavky na obnovení webu, a připravte se na instalaci služby Mobility site recovery. Tato služba je nainstalovaná na každém virtuálním počítači, který chcete replikovat.
> * **Krok 2: Nastavení trezoru služby Recovery Services**. Nastavte trezor pro obnovení webu a určete, co chcete replikovat. Součásti a akce obnovení webu jsou konfigurovány a spravovány v úschovně.
> * **Krok 3: Nastavení prostředí zdrojové replikace**. Nastavte konfigurační server obnovení sítě. Konfigurační server je jeden virtuální počítač azure zásobníku, který spouští všechny součásti potřebné pro site recovery. Po nastavení konfiguračního serveru jej zaregistrujete v úschovně.
> * **Krok 4: Nastavení cílového prostředí replikace**. Vyberte svůj účet Azure a účet úložiště Azure a síť, kterou chcete použít. Během replikace se data virtuálních zařízení zkopírují do úložiště Azure. Po převzetí služeb při selhání jsou virtuální počítače Azure připojeny k zadané síti.
> * **Krok 5: Povolit replikaci**. Nakonfigurujte nastavení replikace a povolte replikaci pro virtuální počítače. Služba Mobility se nainstaluje na virtuální počítač, když je povolena replikace. Obnovení lokality provádí počáteční replikaci virtuálního serveru a pak začne probíhající replikace.
> * **Krok 6: Spuštění cvičení zotavení po havárii**: Po zpracování replikace ověříte, že převzetí služeb při selhání bude fungovat podle očekávání spuštěním vrtáku. Chcete-li zahájit cvičení, spustíte zkušební převzetí služeb při selhání v obnovení lokality. Převzetí služeb při selhání testu nemá vliv na produkční prostředí.

Po dokončení těchto kroků pak můžete spustit úplné převzetí služeb při selhání do Azure podle potřeby.

## <a name="architecture"></a>Architektura

![Architektura](./media/azure-stack-site-recovery/architecture.png)

**Umístění** | **Komponenta** |**Podrobnosti**
--- | --- | ---
**Konfigurační server** | Běží na jednom virtuálním počítači azure zásobníku. | V každém předplatném nastavíte virtuální virtuální počítače konfiguračního serveru. Tento virtuální virtuální server spouští následující součásti obnovení webu:<br/><br/> - Konfigurační server: Koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat. - Procesní server: Funguje jako replikační brána. Přijímá replikační data, optimalizuje ukládání do mezipaměti, kompresi a šifrování; a odešle ji do úložiště Azure.<br/><br/> Pokud virtuální servery, které chcete replikovat, překročí níže uvedené limity, můžete nastavit samostatný procesní server. [Další informace](vmware-azure-set-up-process-server-scale.md).
**Služba Mobility** | Nainstalovaný na každém virtuálním počítači, který chcete replikovat. | V krocích v tomto článku připravíme účet tak, aby se služba Mobility nainstalovala automaticky na virtuální ms, když je povolena replikace. Pokud nechcete službu instalovat automaticky, můžete použít řadu dalších metod. [Další informace](vmware-azure-install-mobility-service.md).
**Azure** | V Azure potřebujete trezor služby Recovery Services, účet úložiště a virtuální síť. |  Replikovaná data jsou uložena v účtu úložiště. Virtuální počítače Azure se přidají do sítě Azure, když dojde k převzetí služeb při selhání.


Replikace funguje takto:

1. V úschovně určíte zdroj a cíl replikace, nastavíte konfigurační server, vytvoříte zásadu replikace a povolíte replikaci.
2. Služba Mobility je nainstalována v počítači (pokud jste použili nabízenou instalaci) a počítače zahájí replikaci v souladu se zásadami replikace.
3. Počáteční kopie dat serveru se replikuje do úložiště Azure.
4. Po dokončení počáteční replikace začne replikace rozdílových změn do Azure. Sledované změny se pro jednotlivé počítače ukládají do souboru .hrl.
5. Konfigurační server orchestruje správu replikace pomocí Azure (odchozí port HTTPS 443).
6. Procesní server přijímá data ze zdrojových počítačů, optimalizuje je a šifruje a odesílá je do úložiště Azure (odchozí port 443).
7. Replikované počítače komunikují s konfiguračním serverem (příchozí port HTTPS 443 pro správu replikace. Počítače odesílají data replikace na procesní server (příchozí port HTTPS 9443 – lze upravit).
8. Provoz se přes internet replikuje do veřejných koncových bodů úložiště Azure. Alternativně můžete použít veřejný partnerský vztah Azure ExpressRoute. Přenos replikačních dat přes síť site-to-site VPN z místního serveru do Azure není podporovaný.

## <a name="prerequisites"></a>Požadavky

Zde je to, co potřebujete k nastavení tohoto scénáře.

**Požadavek** | **Podrobnosti**
--- | ---
**Účet předplatného Azure** | Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
**Oprávnění účtu Azure** | Účet Azure, který používáte, potřebuje oprávnění k:<br/><br/> - Vytvoření trezoru služby obnovení<br/><br/> - Vytvoření virtuálního počítače ve skupině prostředků a virtuální síti, kterou používáte pro scénář<br/><br/> - Zápis do účtu úložiště, který zadáte<br/><br/> Poznámky:<br/><br/> -Pokud si vytvoříte účet, jste správcem předplatného a můžete provádět všechny akce.<br/><br/> - Pokud používáte stávající předplatné a nejste správce, musíte spolupracovat s správcem a přiřadit vám oprávnění vlastníka nebo přispěvatele.<br/><br/> - Pokud potřebujete podrobnější oprávnění, přečtěte si [tento článek](site-recovery-role-based-linked-access-control.md).
**Virtuální počítač azure zásobníku** | Potřebujete virtuální počítač zásobníku Azure v předplatném klienta, který se nasadí jako konfigurační server site recovery.


### <a name="prerequisites-for-the-configuration-server"></a>Požadavky pro konfigurační server

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]



## <a name="step-1-prepare-azure-stack-vms"></a>Krok 1: Příprava virtuálních počítačích azure zásobníku

### <a name="verify-the-operating-system"></a>Ověření operačního systému

Ujistěte se, že virtuální počítače jsou spuštěny jeden z operačních systémů shrnuty v tabulce.


**Operační systém** | **Podrobnosti**
--- | ---
**64bitový systém Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (z aktualizace SP1)
**Centos** | 5,2 až 5,11, 6,1 až 6,9, 7,0 až 7,3
**Ubuntu** | 14.04 LTS server, 16.04 LTS server. Kontrola [podporovaných jader](vmware-physical-azure-support-matrix.md#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Příprava na instalaci služby Mobility

Každý virtuální virtuální ms, který chcete replikovat, musí mít nainstalovanou službu Mobility. Aby mohl procesový server automaticky nainstalovat službu na virtuální počítač, když je povolena replikace, ověřte nastavení virtuálního počítače.

#### <a name="windows-machines"></a>Počítače se systémem Windows

- Potřebujete síťové připojení mezi virtuálním počítačem, na kterém chcete povolit replikaci, a počítačem se spuštěným procesním serverem (ve výchozím nastavení se jedná o virtuální počítač konfiguračního serveru).
- Potřebujete účet s právy správce (domény nebo místní) v počítači, pro který povolíte replikaci.
    - Tento účet zadáte při nastavování obnovení webu. Procesní server pak použije tento účet k instalaci služby Mobility, pokud je povolena replikace.
    - Tento účet bude službou Site Recovery používán pouze pro nabízenou instalaci a k aktualizaci služby Mobility.
    - Pokud nepoužíváte účet domény, musíte zakázat vzdálené řízení přístupu uživatelů na virtuálním počítači:
        - V registru vytvořte hodnotu DWORD **LocalAccountTokenFilterPolicy** pod HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Nastavte její hodnotu na 1.
        - Chcete-li to provést na příkazovém řádku, zadejte následující **příkaz: REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Microsoft\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- V bráně Windows Firewall na virtuálním počítači, který chcete replikovat, povolte sdílení souborů a tiskáren a službu WMI.
    - Chcete-li to provést, spusťte soubor **wf.msc** a otevřete konzolu brány Windows Firewall. Pravým tlačítkem myši klepněte na příkaz **Příchozí pravidla** > **Nové pravidlo**. Vyberte **Předdefinované**a ze seznamu zvolte **Sdílení souborů a tiskáren.** Vyplňte průvodce výběrem možnosti povolení připojení > **Dokončit**.
    - Pro počítače domény můžete k tomu použít soubor gpo.


#### <a name="linux-machines"></a>Linuxové stroje

- Ujistěte se, že existuje síťové připojení mezi počítačem Linux a procesovým serverem.
- Na počítači, pro který povolíte replikaci, potřebujete účet, který je kořenovým uživatelem na zdrojovém serveru Linux:
    - Tento účet zadáte při nastavování obnovení webu. Procesní server pak použije tento účet k instalaci služby Mobility, pokud je povolena replikace.
    - Tento účet bude službou Site Recovery používán pouze pro nabízenou instalaci a k aktualizaci služby Mobility.
- Zkontrolujte, že soubor /etc/hosts na zdrojovém serveru s Linuxem obsahuje položky, které mapují místní název hostitele na IP adresy přidružené ke všem síťovým adaptérům.
- Na počítači, který chcete replikovat, nainstalujte nejnovější balíčky openssh, openssh-server a openssl.
- Ujistěte se, že je povolený Secure Shell (SSH) a že běží na portu 22.
- V souboru sshd_config povolte subsystém SFTP a ověřování heslem:
    1. Chcete-li to provést, přihlaste se jako root.
    2. Najděte řádek, který začíná **passwordauthentication**, v souboru /etc/ssh/sshd_config. Zrušte na řádku komentář a změňte hodnotu na **yes**.
    3. Vyhledejte řádek, který začíná na **Subsystem** a zrušte na něm komentář.

        ![Linux Mobility služba](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Restartujte službu sshd.


### <a name="note-the-vm-private-ip-address"></a>Všimněte si privátní IP adresy virtuálního počítačů

U každého počítače, který chcete replikovat, vyhledejte adresu IP:

1. Na portálu zásobníku Azure klikněte na virtuální počítač.
2. V nabídce **Prostředek** klepněte na **položku Síťová rozhraní**.
3. Poznamenejte si soukromou IP adresu.

    ![Privátní IP adresa](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Krok 2: Vytvoření trezoru a výběr cíle replikace

1. Na webu Azure Portal vyberte **Vytvořit** > nástroje pro**správu** > prostředků**zálohování a obnovení lokality**.
2. Jako **Název** zadejte popisný název pro identifikaci trezoru.
3. Ve **skupině Prostředků**vytvořte nebo vyberte skupinu prostředků. Používáme **contosoRG**.
4. Do **umístění**zadejte oblast Azure. používáme oblast **Západní Evropa**.
5. Chcete-li rychle přistupovat k úschovně z řídicího panelu, vyberte **připnout k řídicímu panelu** > **Vytvořit**.

   ![Vytvoření nového trezoru](./media/azure-stack-site-recovery/new-vault-settings.png)

   Nový trezor se zobrazí na **řídicím panelu** > **Všechny prostředky**a na hlavní stránce **úschovny služby Recovery Services.**

### <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. V **trezorech služby Recovery Services** > zadat název úschovny. Používáme **ContosoVMVault**.
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V **adrese Protection goal** > **Where are your machines located**vyberte **On-premises**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V **aplikaci Jsou vaše počítače virtualizovány**vyberte **Možnost Není virtualizována/Jiná**. Pak vyberte **OK**.

    ![Cíl ochrany](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Krok 3: Nastavení zdrojového prostředí

Nastavte počítač konfiguračního serveru, zaregistrujte ho v trezoru a objevte počítače, které chcete replikovat.

1. Klepněte na tlačítko Připravit > **zdroj** **infrastruktury**.
2. Na stránce **Připravit zdroj** klikněte na **+ Konfigurační server**.

    ![Nastavení zdroje](./media/azure-stack-site-recovery/plus-config-srv.png)

3. V **části Přidat server**zkontrolujte, zda se **konfigurační server** zobrazuje v části Typ **serveru**.
5. Stáhněte instalační soubor sjednoceného instalačního programu pro obnovení webu.
6. Stáhněte registrační klíč trezoru. Při spuštění sjednoceného nastavení potřebujete registrační klíč. Klíč je platný pět dní od jeho vygenerování.

    ![Nastavení zdroje](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Spuštění sjednoceného nastavení obnovení webu Azure

Chcete-li nainstalovat a zaregistrovat konfigurační server, proveďte připojení PROTOKOLU RDP k virtuálnímu počítači, který chcete použít pro konfigurační server, a spusťte sjednocené nastavení.

Než začnete, ujistěte se, že hodiny je [synchronizováns časový server](/windows-server/networking/windows-time-service/windows-time-service-top) na virtuálním počítači před spuštěním. Instalace se nezdaří, pokud je čas více než pět minut mimo místní čas.

Nyní nainstalujte konfigurační server:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurační server lze také nainstalovat z příkazového řádku. [Další informace](physical-manage-configuration-server.md#install-from-the-command-line).
>
> Může trvat 15 minut nebo déle, než se název účtu objeví na portálu. Chcete-li aktualizovat okamžitě, vyberte***název serveru*** >  **Konfigurační server** > **.**

## <a name="step-4-set-up-the-target-environment"></a>Krok 4: Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. V části Připravit**cíl** **infrastruktury** > vyberte předplatné Azure, které chcete použít.
2. Zadejte cílový model nasazení.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure. Pokud je nenajde, musíte vytvořit alespoň jeden účet úložiště a virtuální síť, abyste průvodce dokončili.


## <a name="step-5-enable-replication"></a>Krok 5: Povolení replikace

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Klepněte na tlačítko Připravit**nastavení replikace** **infrastruktury** > .
2. V části **Vytvoření zásad replikace** zadejte název zásady.
3. V části **Prahová hodnota cíle bodu obnovení** (RPO) zadejte omezení cíle bodu obnovení.
    - Body obnovení pro replikovaná data jsou vytvořeny v souladu se stanoveným časem.
    - Toto nastavení nemá vliv na replikaci, která je průběžná. Jednoduše vydá výstrahu, pokud je dosaženo prahové hodnoty bez vytvoření bodu obnovení.
4. V **bodě obnovení uchovávání**, určete, jak dlouho je každý bod obnovení zachována. Replikované virtuální aplikace lze obnovit do libovolného bodu v zadaném časovém okně.
5. V **četnosti snímků konzistentních s aplikací**určete, jak často se vytvářejí snímky konzistentní s aplikací.

    - Snímek konzistentní s aplikací je snímek dat aplikace v čase uvnitř virtuálního počítače.
    - Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace na virtuálním počítači jsou v konzistentním stavu při pořízení snímku.
6. Vyberte **OK** a vytvořte zásadu.


### <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

Tento krok můžete nyní přeskočit. V rozevíracím seznamu **Plánování nasazení** klepněte na tlačítko **Ano, udělal(a) jsem .**



### <a name="enable-replication"></a>Povolení replikace

Ujistěte se, že jste dokončili všechny úkoly v [kroku 1: Příprava stroje](#step-1-prepare-azure-stack-vms). Potom povolte replikaci následujícím způsobem:

1. Vyberte **možnost Replikovat** > **zdroj**aplikace .
2. V části **Zdroj** vyberte konfigurační server.
3. V **pole Typ stroje**vyberte fyzické **stroje**.
4. Vyberte procesní server (konfigurační server). Pak klikněte na **OK**.
5. V **cílové**, vyberte předplatné a skupinu prostředků, ve kterém chcete vytvořit virtuální chod po převzetí služeb při selhání. Vyberte model nasazení, který chcete použít pro virtuální počítače s převzetím počítače s klientem.
6. Vyberte účet úložiště Azure, do kterého chcete uložit replikovaná data.
7. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se vytvoří po převzetí služeb při selhání.
8. Výběrem možnosti **Nakonfigurovat pro vybrané počítače** použijte nastavení sítě pro všechny počítače, které jste vybrali pro ochranu. Pokud chcete vybrat síť Azure pro každý počítač, vyberte **možnost Konfigurovat později.**
9. Ve **fyzické matné sfce**, klepněte na **+Fyzický stroj**. Zadejte název, adresu IP a typ operačního serveru každého počítače, který chcete replikovat.

    - Použijte interní IP adresu stroje.
    - Pokud zadáte veřejnou IP adresu, replikace nemusí fungovat podle očekávání.

10. V **části Vlastnosti** > **Konfigurace vlastností**vyberte účet, který bude procesní server používat k automatické instalaci služby Mobility Service do počítače.
11. V >  **nastavení replikace**Konfigurace nastavení**replikace**zkontrolujte, zda je vybrána správná zásada replikace.
12. Klepněte na tlačítko **Povolit replikaci**.
13. Sledujte průběh úlohy **Povolit ochranu** v části**Úlohy** > **obnovení webu** **nastavení** > . Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.

> [!NOTE]
> Jakmile bude replikace virtuálního počítače povolená, Site Recovery nainstaluje službu mobility.
>
> Může to trvat 15 minut nebo déle, než se změny projeví a objeví se na portálu.
>
> Chcete-li sledovat virtuální počítače, které přidáte, zkontrolujte čas posledního zjištěné ho pro virtuální počítače v **konfiguračních serverech** > **poslední kontakt na**. Pokud chcete přidat virtuální počítače bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (nevybírejte ho) a vyberte **Aktualizovat**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Krok 6: Spuštění cvičení pro zotavení po havárii

Spustíte test převzetí služeb při selhání do Azure a ujistěte se, že vše funguje podle očekávání. Toto převzetí služeb při selhání nebude mít vliv na produkční prostředí.

### <a name="verify-machine-properties"></a>Ověření vlastností počítače

Před spuštěním testu převzetí služeb při selhání, ověřte vlastnosti počítače a ujistěte se, že splňují [požadavky Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements). Vlastnosti můžete zobrazit a upravit následujícím způsobem:

1. V části **Chráněné položky** klikněte na **Replikované položky** a pak na virtuální počítač.
2. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.
3. V **části Výpočetní výkon a síť**upravte nastavení podle potřeby.

    - Můžete upravit název virtuálního počítače Azure, skupinu prostředků, velikost cíle, [sadu dostupnosti](/azure/virtual-machines/windows/tutorial-availability-sets)a nastavení spravovaného disku.
    - Můžete také zobrazit a upravit nastavení sítě. Patří mezi ně síť/podsíť, ke kterému je virtuální počítač Azure připojen po převzetí služeb při selhání a IP adresu, která se bude přiřazovat k virtuálnímu počítači.
1. V **části Disky**můžete zobrazit informace o operačním systému a datových discích na virtuálním počítači.


### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Když spustíte testovací převzetí služeb při selhání, stane se následující:

1. Spustí se kontrola předpokladů, která zjistí, zda jsou splněné všechny podmínky pro převzetí služeb při selhání.
2. Převzetí služeb při selhání zpracovává data pomocí zadaného bodu obnovení:
    - **Poslední zpracované**: Stroj převezme služby při selhání na nejnovější bod obnovení zpracovaný site recovery. Zobrazí se časové razítko. Tato možnost neztrácí žádný čas zpracováním dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
    - **Nejnovější aplikace konzistentní**: Počítač převezme služby při selhání na nejnovější bod obnovení konzistentní s aplikací.
    - **Vlastní**: Vyberte bod obnovení použitý pro převzetí služeb při selhání.

3. Virtuální počítač Azure se vytvoří pomocí zpracovaných dat.
4. Testování převzetí služeb při selhání můžete automaticky vyčistit virtuální počítače Azure vytvořené během přechodu k podrobnostem.

Spuštění testu převzetí služeb při selhání pro virtuální hod následujícím způsobem:

1. V **nastavení** > **replikované položky**klepněte na položku > **+Test Failover**.
2. V tomto návodu vybereme použití **posledního zpracovaného** bodu obnovení.
3. V **části Test failover**vyberte cílovou síť Azure.
4. Kliknutím na **OK** zahajte převzetí služeb při selhání.
5. Sledujte průběh kliknutím na virtuální počítač a otevřete jeho vlastnosti. Nebo klepněte na testovací úlohu **převzetí služeb při selhání** v *vault name* > **Settings** > **úlohách** >**Site Recovery jobs**nastavení webu nastavení úložiště .
6. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Zkontrolujte, zda je virtuální počítače odpovídající velikost, připojený ke správné síti a spuštěný.
7. Nyní byste se měli moct k replikovanému virtuálnímu počítači v Azure připojit. [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
8. Virtuální počítače Azure vytvořené během testu převzetí služeb při selhání odstraníte kliknutím na **Vyčistit testovací převzetí služeb při selhání** na virtuálním počítači. V **poznámkách**uložte všechna pozorování spojená s převzetím služeb při selhání testu.

## <a name="fail-over-and-fail-back"></a>Převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace a spuštění vrtáku, abyste se ujistili, že vše funguje, můžete počítače selhat v Azure podle potřeby.

Před spuštěním převzetí služeb při selhání, pokud se chcete připojit k počítači v Azure po převzetí služeb při selhání, pak [připravte se na připojení](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) před spuštěním.

Potom spusťte převzetí služeb při selhání takto:


1. V **nastavení** > **replikovaných položek**klepněte na počítač > převzetí služeb při **selhání**.
2. Vyberte bod obnovení, který chcete použít.
3. V **části Test failover**vyberte cílovou síť Azure.
4. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. S tímto nastavením site recovery pokusí vypnout zdrojový počítač před spuštěním převzetí služeb při selhání. Převzetí služeb při selhání však pokračuje i v případě, že se vypnutí nezdaří.
5. Kliknutím na **OK** zahajte převzetí služeb při selhání. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
6. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Pokud jste připraveni se připojit po převzetí služeb při selhání, zkontrolujte, zda je virtuální počítače odpovídající velikost, připojené ke správné síti a běží.
7. Po ověření virtuálního virtuálního virtuálního ms klikněte na **Potvrdit** a dokončete převzetí služeb při selhání. Tím odstraníte všechny dostupné body obnovení.

> [!WARNING]
> Nepřerušujte v průběhu proces převzetí služeb při selhání: před zahájením procesu převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.


### <a name="fail-back-to-azure-stack"></a>Vrácení služeb při selhání do azure zásobníku

Když je vaše primární lokalita znovu spuštěná, můžete si vrátit z Azure do Azure Stacku. K tomu je třeba stáhnout virtuální ho disponu VM Azure a nahrát do Azure Stack.

1. Vypněte virtuální počítač Azure, aby bylo možné stáhnout virtuální pevný disk.
2. Chcete-li začít stahovat virtuální pevný disk, nainstalujte [Azure Storage Explorer](https://azure.microsoft.com/features/storage-explorer/).
3. Přejděte na virtuální počítač na webu Azure Portal (pomocí názvu virtuálního počítače).
4. V **části Disky**klikněte na název disku a shromážděte nastavení.

    - Jako příklad identifikátor URI virtuálního pevného `https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd` disku použitý v našem testu: lze rozdělit, aby se získaly následující vstupní parametry, které se používají ke stažení virtuálního pevného disku.
        - Účet úložiště: 502055westcentralus
        - Kontejner: wahv9b8d2ceb284fb59287
        - Název VHD: zkopírovaný-3676553984.vhd

5. Teď si pomocí Průzkumníka úložiště Azure stáhněte virtuální pevný disk.
6. Nahrajte virtuální pevný disk do azure zásobníku [pomocí těchto kroků](/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-disks-to-a-vm).
7. V existujícím virtuálním virtuálním nebo novém virtuálním ms připojte nahrané virtuální disponiály.
8. Zkontrolujte, zda je disk operačního systému správný, a spusťte virtuální ho.


V této fázi je navrácení služeb po selhání dokončeno.


## <a name="conclusion"></a>Závěr

V tomto článku jsme replikovali virtuální počítače Azure Stack do Azure. S replikací na místě jsme spustili cvičení pro zotavení po havárii, abychom zajistili, že převzetí služeb při selhání do Azure funguje podle očekávání. Článek také obsahuje kroky pro spuštění úplné převzetí služeb při selhání do Azure a vrácení služeb při selhání zpět do Azure Stack.

## <a name="next-steps"></a>Další kroky

Po selhání zpět, můžete znovu zamknout virtuální počítač a začít replikovat do Azure znovu Chcete-li to provést, opakujte kroky v tomto článku.

