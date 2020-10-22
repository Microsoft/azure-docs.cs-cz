---
title: Replikace Azure Stackch virtuálních počítačů do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Naučte se, jak nastavit zotavení po havárii do Azure pro Azure Stack virtuálních počítačů pomocí služby Azure Site Recovery.
ms.topic: conceptual
ms.date: 08/05/2019
ms.openlocfilehash: 36e11bfe5354644f9ef6603ffe20cb2e86074323
ms.sourcegitcommit: 28c5fdc3828316f45f7c20fc4de4b2c05a1c5548
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/22/2020
ms.locfileid: "92370521"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replikace virtuálních počítačů Azure Stack do Azure

V tomto článku se dozvíte, jak nastavit zotavení po havárii Azure Stack virtuálních počítačů do Azure pomocí [služby Azure Site Recovery](site-recovery-overview.md).

Site Recovery přispívá ke strategii pro provozní kontinuitu a zotavení po havárii (BCDR). Služba zajišťuje, aby vaše úlohy virtuálních počítačů zůstaly k dispozici, i když se očekává a dojde k neočekávanému výpadku.

- Site Recovery orchestruje a spravuje replikaci virtuálních počítačů do služby Azure Storage.
- Když v primární lokalitě dojde k výpadku, použijete Site Recovery k převzetí služeb při selhání do Azure.
- Při převzetí služeb při selhání se vytvoří virtuální počítače Azure z uložených dat virtuálního počítače a uživatelé můžou pokračovat v přístupu k úlohám běžícím na těchto virtuálních počítačích Azure.
- Po opětovném zprovoznění všech virtuálních počítačů Azure v primární lokalitě a opětovném spuštění replikace do Azure Storage.


V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * **Krok 1: Příprava virtuálních počítačů Azure Stack pro replikaci** Ověřte, že virtuální počítače splňují požadavky Site Recovery a připravte se na instalaci služby Site Recovery mobility. Tato služba je nainstalovaná na všech virtuálních počítačích, které chcete replikovat.
> * **Krok 2: nastavte trezor Recovery Services**. Nastavte trezor pro Site Recovery a určete, co chcete replikovat. Site Recovery komponenty a akce jsou konfigurovány a spravovány v trezoru.
> * **Krok 3: nastavení prostředí pro replikaci zdrojového kódu** Nastavte Site Recovery konfigurační server. Konfigurační server je jeden Azure Stack virtuální počítač, který spouští všechny součásti, které vyžaduje Site Recovery. Po nastavení konfiguračního serveru ho zaregistrujete v trezoru.
> * **Krok 4: nastavte cílové prostředí replikace**. Vyberte svůj účet Azure, účet úložiště Azure a síť, kterou chcete použít. Během replikace se data virtuálních počítačů zkopírují do služby Azure Storage. Po převzetí služeb při selhání jsou virtuální počítače Azure připojené k zadané síti.
> * **Krok 5: povolení replikace** Nakonfigurujte nastavení replikace a Povolte replikaci pro virtuální počítače. Služba mobility se nainstaluje na virtuální počítač, pokud je povolená replikace. Site Recovery provede počáteční replikaci virtuálního počítače a spustí se průběžná replikace.
> * **Krok 6: spuštění postupu zotavení po havárii**: po zprovoznění replikace ověříte, že převzetí služeb při selhání bude fungovat podle očekávání, a to spuštěním podrobného postupu. Pokud chcete zahájit procházení, spusťte testovací převzetí služeb při selhání v Site Recovery. Testovací převzetí služeb při selhání nemá vliv na vaše produkční prostředí.

Po dokončení těchto kroků můžete spustit úplné převzetí služeb při selhání do Azure jako a v případě potřeby.

## <a name="architecture"></a>Architektura

![Diagram zobrazuje trezory Recovery Services pro dva klienty v cloudech přidružených k předplatným klientů v rámci společné infrastruktury Azure Stack.](./media/azure-stack-site-recovery/architecture.png)

**Umístění** | **Komponenta** |**Podrobnosti**
--- | --- | ---
**Konfigurační server** | Spouští se na jednom virtuálním počítači s Azure Stack. | V každém předplatném jste nastavili virtuální počítač konfiguračního serveru. Tento virtuální počítač spustí následující součásti Site Recovery:<br/><br/> -Konfigurační server: koordinuje komunikaci mezi místními a Azure a spravuje replikaci dat. -Procesový Server: funguje jako brána replikace. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování. a pošle ho do služby Azure Storage.<br/><br/> Pokud virtuální počítače, které chcete replikovat, překračují níže uvedená omezení, můžete nastavit samostatný samostatný procesový Server. [Další informace](vmware-azure-set-up-process-server-scale.md).
**Služba Mobility** | Instaluje se do každého virtuálního počítače, který chcete replikovat. | V krocích v tomto článku připravujeme účet, aby byla služba mobility na virtuálním počítači nainstalovaná automaticky, když je povolená replikace. Pokud nechcete automaticky instalovat službu, můžete použít řadu dalších metod. [Další informace](vmware-azure-install-mobility-service.md).
**Azure** | V Azure potřebujete Recovery Services trezor, účet úložiště a virtuální síť. |  Replikovaná data se ukládají v účtu úložiště. Virtuální počítače Azure se přidají do sítě Azure, když dojde k převzetí služeb při selhání.


Replikace funguje následujícím způsobem:

1. V trezoru zadáte zdroj a cíl replikace, nastavíte konfigurační server, vytvoříte zásadu replikace a povolíte replikaci.
2. Služba mobility je nainstalovaná na počítači (Pokud jste použili nabízenou instalaci) a počítače zahájí replikaci v souladu se zásadami replikace.
3. Počáteční kopie dat serveru se replikuje do služby Azure Storage.
4. Po dokončení počáteční replikace se spustí replikace rozdílových změn do Azure. Sledované změny se pro jednotlivé počítače ukládají do souboru .hrl.
5. Konfigurační server orchestruje správu replikace v Azure (odchozí přenos HTTPS 443).
6. Procesový server přijímá data ze zdrojových počítačů, optimalizuje je a šifruje a odesílá je do služby Azure Storage (odchozí port 443).
7. Replikované počítače komunikují s konfiguračním serverem (port HTTPS 443 příchozí, pro správu replikací. Počítače odesílají data replikace na procesový Server (port HTTPS 9443 příchozí – lze upravit).
8. Provoz se přes internet replikuje do veřejných koncových bodů úložiště Azure. Alternativně můžete použít veřejný partnerský vztah Azure ExpressRoute. Přenos replikačních dat přes síť site-to-site VPN z místního serveru do Azure není podporovaný.

## <a name="prerequisites"></a>Předpoklady

Tady je seznam toho, co potřebujete k nastavení tohoto scénáře.

**Požadavek** | **Podrobnosti**
--- | ---
**Účet předplatného Azure** | Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
**Oprávnění účtu Azure** | Účet Azure, který použijete, musí mít oprávnění k těmto akcím:<br/><br/> – Vytvoření trezoru služby Recovery Services<br/><br/> – Vytvoření virtuálního počítače ve skupině prostředků a virtuální síti, kterou používáte pro scénář<br/><br/> – Zapsat do účtu úložiště, který zadáte<br/><br/> Poznámky:<br/><br/> – Pokud vytvoříte účet, jste správcem předplatného a můžete provádět všechny akce.<br/><br/> – Pokud používáte stávající předplatné a nejste správcem, budete muset spolupracovat se správcem a přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> – Pokud potřebujete podrobnější oprávnění, přečtěte si [Tento článek](site-recovery-role-based-linked-access-control.md).
**Azure Stack virtuálního počítače** | V rámci předplatného tenanta budete potřebovat Azure Stack virtuální počítač, který se nasadí jako konfigurační server Site Recovery.


### <a name="prerequisites-for-the-configuration-server"></a>Předpoklady pro konfigurační server

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]



## <a name="step-1-prepare-azure-stack-vms"></a>Krok 1: Příprava Azure Stackch virtuálních počítačů

### <a name="verify-the-operating-system"></a>Ověření operačního systému

Ujistěte se, že virtuální počítače používají jeden z operačních systémů shrnutých v tabulce.


**Operační systém** | **Podrobnosti**
--- | ---
**64 – bitová okna** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (z SP1)
**CentOS** | 5,2 až 5,11, 6,1 až 6,9, 7,0 až 7,3
**Ubuntu** | 14,04 LTS Server, 16,04 LTS Server. Kontrola [podporovaných jader](vmware-physical-azure-support-matrix.md#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Příprava na instalaci služby mobility

Každý virtuální počítač, který chcete replikovat, musí mít nainstalovanou službu mobility. Aby mohl procesový server nainstalovat službu na virtuálním počítači automaticky, pokud je povolená replikace, ověřte nastavení virtuálního počítače.

#### <a name="windows-machines"></a>Počítače s Windows

- Potřebujete síťové připojení mezi virtuálním počítačem, na kterém chcete povolit replikaci, a počítačem, na kterém je spuštěný procesový Server (ve výchozím nastavení se jedná o virtuální počítač konfiguračního serveru).
- Na počítači, pro který chcete povolit replikaci, potřebujete účet s právy správce (doména nebo místní).
    - Tento účet zadáte při nastavování Site Recovery. Pak procesový Server pomocí tohoto účtu nainstaluje službu mobility, když je povolená replikace.
    - Tento účet se bude používat jenom Site Recovery pro nabízenou instalaci a k aktualizaci služby mobility.
    - Pokud nepoužíváte doménový účet, musíte na virtuálním počítači zakázat vzdálené řízení přístupu uživatele:
        - V registru vytvořte hodnotu DWORD **LocalAccountTokenFilterPolicy** pod HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Nastavte její hodnotu na 1.
        - Pokud to chcete provést na příkazovém řádku, zadejte následující příkaz: **REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System/V LocalAccountTokenFilterPolicy/t REG_DWORD/d 1**.
- V bráně Windows Firewall na virtuálním počítači, který chcete replikovat, povolte sdílení souborů a tiskáren a rozhraní WMI.
    - Uděláte to tak, že spustíte **WF. msc** a otevřete konzolu brány Windows Firewall. Klikněte pravým tlačítkem na **příchozí pravidla**  >  **nové pravidlo**. Vyberte **předdefinované**a v seznamu vyberte **sdílení souborů a tiskáren** . Dokončete Průvodce výběrem, aby bylo možné připojení > **Dokončit**.
    - V případě počítačů s doménou můžete to provést pomocí objektu zásad skupiny.


#### <a name="linux-machines"></a>Počítače se systémem Linux

- Zajistěte, aby bylo síťové připojení mezi počítačem a procesovým serverem se systémem Linux.
- Na počítači, pro který povolíte replikaci, potřebujete účet, který je kořenovým uživatelem na zdrojovém serveru Linux:
    - Tento účet zadáte při nastavování Site Recovery. Pak procesový Server pomocí tohoto účtu nainstaluje službu mobility, když je povolená replikace.
    - Tento účet se bude používat jenom Site Recovery pro nabízenou instalaci a k aktualizaci služby mobility.
- Zkontrolujte, že soubor /etc/hosts na zdrojovém serveru s Linuxem obsahuje položky, které mapují místní název hostitele na IP adresy přidružené ke všem síťovým adaptérům.
- Na počítači, který chcete replikovat, nainstalujte nejnovější balíčky openssh, openssh-server a openssl.
- Ujistěte se, že je povolený Secure Shell (SSH) a že běží na portu 22.
- V souboru sshd_config povolte subsystém SFTP a ověřování heslem:
    1. Uděláte to tak, že se přihlásíte jako kořen.
    2. Vyhledá řádek, který začíná na **PasswordAuthentication**, v souboru/etc/ssh/sshd_config. Zrušte na řádku komentář a změňte hodnotu na **yes**.
    3. Vyhledejte řádek, který začíná na **Subsystem** a zrušte na něm komentář.

        ![Služba mobility Linux](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Restartujte službu sshd.


### <a name="note-the-vm-private-ip-address"></a>Poznamenejte si privátní IP adresu virtuálního počítače.

Pro každý počítač, který chcete replikovat, vyhledejte IP adresu:

1. Na portálu Azure Stack klikněte na virtuální počítač.
2. V nabídce **prostředek** klikněte na **Síťová rozhraní**.
3. Poznamenejte si privátní IP adresu.

    ![Privátní IP adresa](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Krok 2: vytvoření trezoru a výběr cíle replikace

1. V Azure Portal vyberte **vytvořit**  >  **Nástroje pro správu**prostředků  >  **zálohování a Site Recovery**.
2. Jako **Název** zadejte popisný název pro identifikaci trezoru.
3. V **skupiny prostředků**vytvořte nebo vyberte skupinu prostředků. Používáme **contosoRG**.
4. Do pole **umístění**zadejte oblast Azure. používáme oblast **Západní Evropa**.
5. Chcete-li rychle získat přístup k trezoru z řídicího panelu, vyberte možnost **Připnout na řídicí panel**  >  **vytvořit**.

   ![Vytvoření nového trezoru](./media/azure-stack-site-recovery/new-vault-settings.png)

   Nový trezor se zobrazí v části **řídicí panel**  >  **všechny prostředky**a na hlavní stránce **Recovery Services trezory** .

### <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. V **Recovery Services trezory** > zadat název trezoru. Používáme **ContosoVMVault**.
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V nabídce **cíl ochrany**  >  ,**kde jsou umístěny vaše počítače**, vyberte možnost **místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V nástroji **jsou virtualizované počítače**, vyberte **nevirtualizované/jiné**. Pak vyberte **OK**.

    ![Cíl ochrany](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Krok 3: nastavení zdrojového prostředí

Nastavte počítač konfiguračního serveru, zaregistrujte ho v trezoru a vyhledejte počítače, které chcete replikovat.

1. Klikněte na **připravit**  >  **zdroj**infrastruktury.
2. Na stránce **Připravit zdroj** klikněte na **+ Konfigurační server**.

    ![Snímek obrazovky s dialogovým oknem + konfigurační server se zprávou "kliknutím na + konfigurační server ve výše uvedeném panelu příkazů nastavíte jednu...".](./media/azure-stack-site-recovery/plus-config-srv.png)

3. V nástroji **Přidat server**ověřte, že se **konfigurační server** zobrazuje v **typu serveru**.
5. Stáhněte instalační soubor sjednocené instalace Site Recovery.
6. Stáhněte registrační klíč trezoru. Registrační klíč potřebujete při spuštění sjednocené instalace. Klíč je platný pět dní od jeho vygenerování.

    ![Snímek obrazovky s dialogovým oknem přidat server s typem serveru nastaveným na konfigurační server a zvýrazněným tlačítkem Stáhnout registrační klíč trezoru.](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Spustit Azure Site Recovery Unified Setup

Pro instalaci a registraci konfiguračního serveru proveďte připojení RDP k virtuálnímu počítači, který chcete použít pro konfigurační server, a spusťte sjednocenou instalaci.

Než začnete, ujistěte se, že se hodiny [synchronizovaly s časovým serverem](/windows-server/networking/windows-time-service/windows-time-service-top) na virtuálním počítači, než začnete. Instalace se nezdařila, pokud je čas více než pět minut mimo místní čas.

Nyní nainstalujte konfigurační server:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurační server lze také nainstalovat z příkazového řádku. [Další informace](physical-manage-configuration-server.md#install-from-the-command-line).
>
> Může trvat 15 minut nebo déle, než se název účtu objeví na portálu. Pokud chcete aktualizaci hned aktualizovat, vyberte **konfigurační servery**  >  **_název serveru_*_ > _* obnovit server**.

## <a name="step-4-set-up-the-target-environment"></a>Krok 4: nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. V části **připravit**  >  **cíl**infrastruktury vyberte předplatné Azure, které chcete použít.
2. Zadejte cílový model nasazení.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure. Pokud je nenalezne, je nutné vytvořit alespoň jeden účet úložiště a virtuální síť, aby bylo možné průvodce Dokončit.


## <a name="step-5-enable-replication"></a>Krok 5: Povolení replikace

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Klikněte na **Příprava infrastruktury**  >  **nastavení replikace**.
2. V části **Vytvoření zásad replikace** zadejte název zásady.
3. V části **Prahová hodnota cíle bodu obnovení** (RPO) zadejte omezení cíle bodu obnovení.
    - Body obnovení pro replikovaná data se vytvářejí v souladu s nastaveným časem.
    - Toto nastavení nemá vliv na replikaci, která je nepřetržitá. Jednoduše vydá výstrahu, pokud je dosaženo mezní hodnoty, aniž by byl vytvořen bod obnovení.
4. V **části uchování bodu obnovení**určete, jak dlouho se mají uchovávat jednotlivé body obnovení. Replikované virtuální počítače je možné obnovit do libovolného bodu v určeném časovém intervalu.
5. V nastavení **frekvence snímků konzistentní vzhledem k aplikacím**určete, jak často se mají vytvářet snímky konzistentní vzhledem k aplikacím.

    - Snímek konzistentní vzhledem k aplikacím je snímkem dat aplikací v rámci virtuálního počítače v čase.
    - Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace na virtuálním počítači jsou v konzistentním stavu, kdy se snímek vybere.
6. Vyberte **OK** a vytvořte zásadu.


### <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

Tento krok můžete přeskočit hned teď. V rozevíracím seznamu **Plánování nasazení** klikněte na **Ano, mám to hotové**.



### <a name="enable-replication"></a>Povolení replikace

Ujistěte se, že jste dokončili všechny úlohy v [kroku 1: Příprava počítače](#step-1-prepare-azure-stack-vms). Pak povolte replikaci následujícím způsobem:

1. Vyberte **replikovat**  >  **zdroj**aplikace.
2. V části **Zdroj** vyberte konfigurační server.
3. V **typ počítače**vyberte **fyzické počítače**.
4. Vyberte procesní server (konfigurační server). Pak klikněte na **OK**.
5. V části **cíl**vyberte předplatné a skupinu prostředků, ve které chcete vytvořit virtuální počítače po převzetí služeb při selhání. Vyberte model nasazení, který chcete použít pro virtuální počítače s podporou převzetí služeb při selhání.
6. Vyberte účet úložiště Azure, do kterého chcete replikovaná data uložit.
7. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se vytvoří po převzetí služeb při selhání.
8. Výběrem možnosti **Nakonfigurovat pro vybrané počítače** použijte nastavení sítě pro všechny počítače, které jste vybrali pro ochranu. Pokud chcete vybrat síť Azure samostatně pro každý počítač, vyberte **Konfigurovat později** .
9. Na **fyzických počítačích**klikněte na **+ fyzický počítač**. Zadejte název, IP adresu a typ operačního systému každého počítače, který chcete replikovat.

    - Použijte interní IP adresu počítače.
    - Pokud zadáte veřejnou IP adresu, replikace nemusí fungovat podle očekávání.

10. V části **vlastnosti**  >  **Konfigurace vlastností**vyberte účet, pomocí kterého bude procesový Server na počítači automaticky instalovat službu mobility.
11. V **nastavení replikace**  >  **nakonfigurujte nastavení replikace**a ověřte, jestli je vybraná správná zásada replikace.
12. Klikněte na **Povolit replikaci**.
13. Sledovat průběh úlohy **Povolení ochrany** v **Nastavení**  >  **úlohy**  >  **Site Recovery úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.

> [!NOTE]
> Jakmile bude replikace virtuálního počítače povolená, Site Recovery nainstaluje službu mobility.
>
> Může to trvat 15 minut nebo déle, než se změny projeví a objeví se na portálu.
>
> Pokud chcete monitorovat virtuální počítače, které jste přidali, Projděte si poslední čas zjištěných virtuálních počítačů v části **konfigurační servery**  >  **Poslední kontakt na**. Pokud chcete přidat virtuální počítače bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (nevybírejte ho) a vyberte **Aktualizovat**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Krok 6: spuštění postupu zotavení po havárii

Testovací převzetí služeb při selhání spustíte do Azure, abyste se ujistili, že všechno funguje podle očekávání. Toto převzetí služeb při selhání nebude mít vliv na vaše produkční prostředí.

### <a name="verify-machine-properties"></a>Ověřit vlastnosti počítače

Před spuštěním testovacího převzetí služeb při selhání ověřte vlastnosti počítače a ujistěte se, že vyhovují [požadavkům Azure](vmware-physical-azure-support-matrix.md#azure-vm-requirements). Vlastnosti můžete zobrazit a upravit následujícím způsobem:

1. V části **Chráněné položky** klikněte na **Replikované položky** a pak na virtuální počítač.
2. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.
3. V **COMPUTE a síti**podle potřeby upravte nastavení.

    - Můžete upravit název virtuálního počítače Azure, skupinu prostředků, cílovou velikost, skupinu [dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md)a nastavení spravovaného disku.
    - Můžete také zobrazit a upravit nastavení sítě. Patří mezi ně síť nebo podsíť, ke které je virtuální počítač Azure připojený po převzetí služeb při selhání, a IP adresa, která se přiřadí k virtuálnímu počítači.
1. V části **disky**si prohlédněte informace o operačním systému a datových discích na virtuálním počítači.


### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Když spustíte testovací převzetí služeb při selhání, stane se následující:

1. Spustí se kontrola předpokladů, která zjistí, zda jsou splněné všechny podmínky pro převzetí služeb při selhání.
2. Převzetí služeb při selhání zpracuje data pomocí zadaného bodu obnovení:
    - **Poslední zpracování**: počítač převezme poslední bod obnovení zpracovaný Site Recovery. Zobrazí se časové razítko. Tato možnost neztrácí žádný čas zpracováním dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
    - **Nejnovější konzistentní vzhledem k aplikacím**: převzetí služeb při selhání počítače na nejnovější bod obnovení konzistentní vzhledem k aplikacím.
    - **Vlastní**: Vyberte bod obnovení, který se používá pro převzetí služeb při selhání.

3. Virtuální počítač Azure se vytvoří pomocí zpracovaných dat.
4. Testovací převzetí služeb při selhání může automaticky vyčistit virtuální počítače Azure vytvořené během procházení.

Spusťte testovací převzetí služeb při selhání pro virtuální počítač následujícím způsobem:

1. V **Nastavení**  >  **replikované položky**klikněte na virtuální počítač > **+ testovací převzetí služeb při selhání**.
2. Pro tento návod vybereme použití **nejnovějšího zpracovaného** bodu obnovení.
3. V části **testovací převzetí služeb při selhání**vyberte cílovou síť Azure.
4. Kliknutím na **OK** zahajte převzetí služeb při selhání.
5. Kliknutím na virtuální počítač můžete sledovat průběh a otevřít jeho vlastnosti. Případně klikněte na úlohu **testovací převzetí služeb při selhání** v nastavení *název trezoru*úlohy  >  **Settings**  >  **Jobs**  > **Site Recovery úlohy**.
6. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Ověřte, že má virtuální počítač odpovídající velikost, je připojená ke správné síti a je spuštěná.
7. Nyní byste se měli moct k replikovanému virtuálnímu počítači v Azure připojit. [Další informace](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
8. Virtuální počítače Azure vytvořené během testu převzetí služeb při selhání odstraníte kliknutím na **Vyčistit testovací převzetí služeb při selhání** na virtuálním počítači. V části **poznámky**uložte veškeré poznámky spojené s testovacím převzetím služeb při selhání.

## <a name="fail-over-and-fail-back"></a>Převzetí služeb při selhání a navrácení služeb po obnovení

Po nastavení replikace a spuštění podrobného postupu, abyste měli jistotu, že všechno funguje, můžete v případě potřeby převzít počítače do Azure.

Před spuštěním převzetí služeb při selhání se můžete připojit k počítači v Azure po převzetí služeb při selhání a potom [připravit na připojení](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover) , než začnete.

Pak spusťte převzetí služeb při selhání následujícím způsobem:


1. V **Nastavení**  >  **replikované položky**klikněte na počítač > **převzetí služeb při selhání**.
2. Vyberte bod obnovení, který chcete použít.
3. V části **testovací převzetí služeb při selhání**vyberte cílovou síť Azure.
4. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Pomocí tohoto nastavení se Site Recovery před spuštěním převzetí služeb při selhání vypnul zdrojový počítač. Převzetí služeb při selhání ale pokračuje i v případě selhání vypnutí.
5. Kliknutím na **OK** zahajte převzetí služeb při selhání. Průběh převzetí služeb při selhání můžete sledovat na stránce **úlohy** .
6. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Pokud jste připravili připojení po převzetí služeb při selhání, ověřte, že má virtuální počítač odpovídající velikost, je připojená ke správné síti a je spuštěná.
7. Po ověření virtuálního počítače kliknutím na **Potvrdit** dokončete převzetí služeb při selhání. Tím dojde k odstranění všech dostupných bodů obnovení.

> [!WARNING]
> Nepřerušujte v průběhu proces převzetí služeb při selhání: před zahájením procesu převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.


### <a name="fail-back-to-azure-stack"></a>Navrácení služeb po obnovení do Azure Stack

Když je vaše primární lokalita znovu spuštěná, můžete navrátit služby po obnovení z Azure do Azure Stack. Provedete to podle kroků uvedených [tady](/azure-stack/operator/site-recovery-failback?view=azs-2005).

## <a name="conclusion"></a>Závěr

V tomto článku jsme replikují Azure Stack virtuálních počítačů do Azure. Při replikaci jsme spustili postup zotavení po havárii, abyste se ujistili, že převzetí služeb při selhání do Azure fungovalo podle očekávání. Článek obsahuje také postup pro spuštění úplného převzetí služeb při selhání do Azure a navrácení služeb po obnovení do Azure Stack.

## <a name="next-steps"></a>Další kroky

Po navrácení služeb po obnovení můžete virtuální počítač znovu zapnout a znovu ho začít replikovat do Azure, a to tak, že zopakujete kroky v tomto článku.