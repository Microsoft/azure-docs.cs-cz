---
title: Replikace virtuálních počítačů Azure Stack do Azure pomocí Azure Site Recovery | Dokumentace Microsoftu
description: Zjistěte, jak nastavit zotavení po havárii do Azure pro virtuální počítače Azure Stack pomocí služby Azure Site Recovery.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.topic: conceptual
ms.service: site-recovery
ms.date: 10/28/2018
ms.author: raynew
ms.openlocfilehash: 9da64ebe675f9d481c7474a81fec294d50e49ce7
ms.sourcegitcommit: 6e09760197a91be564ad60ffd3d6f48a241e083b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2018
ms.locfileid: "50215204"
---
# <a name="replicate-azure-stack-vms-to-azure"></a>Replikace virtuálních počítačů Azure stacku pro Azure

V tomto článku se dozvíte, jak nastavit zotavení po havárii virtuálních počítačů Azure stacku pro Azure, pomocí [služby Azure Site Recovery](https://docs.microsoft.com/azure/site-recovery/site-recovery-overview).

Site Recovery přispívá ke strategii obchodní kontinuity podnikových procesů a po havárii (BCDR) zotavení. Služba zajišťuje, že váš virtuální počítač, byly zpracovávané úlohy k dispozici, když byl očekáván a neočekávaných výpadků.

- Site Recovery orchestruje a spravuje replikaci virtuálních počítačů do Azure storage.
- Pokud v primární lokalitě dojde k výpadku, můžete pomocí Site Recovery převzetí služeb při selhání do Azure.
- Na převzetí služeb při selhání virtuální počítače Azure jsou vytvářeny z uložených dat virtuálního počítače a uživatelé můžou dál přístup k úloh spuštěných na těchto virtuálních počítačích Azure.
- Když je všechno, co znovu spuštěn a pracuje, můžete selhat obnovení virtuálních počítačů Azure do primární lokality a začaly replikovat do úložiště Azure znovu.


V tomto článku získáte informace o těchto tématech:

> [!div class="checklist"]
> * **Krok 1: Příprava virtuálních počítačů Azure stack pro replikaci**. Zkontrolujte, jestli virtuální počítače v souladu s požadavky služby Site Recovery a příprava na instalaci služby Site Recovery Mobility. Tato služba nainstaluje na každý virtuální počítač, který chcete replikovat.
> * **Krok 2: Nastavení trezoru služby Recovery Services**. Nastavíte trezor Site Recovery a určit, co chcete replikovat. Součásti Site Recovery a akce jsou nakonfigurované a spravovaných v trezoru.
> * **Krok 3: Nastavte zdrojové prostředí replikace**. Nastavení konfiguračního serveru Site Recovery. Konfigurační server je jeden virtuální počítač Azure Stack, na kterém běží všechny součásti potřebné službou Site Recovery. Jakmile nastavíte konfigurační server, zaregistrujte v trezoru.
> * **Krok 4: Nastavení cílové prostředí replikace**. Vyberte váš účet Azure a účet úložiště Azure a síť, kterou chcete použít. Během replikace virtuálních počítačů data zkopírována do úložiště Azure. Po převzetí služeb při selhání jsou připojené virtuální počítače Azure pro zadanou síť.
> * **Krok 5: Povolení replikace**. Konfigurace nastavení replikace a povolení replikace pro virtuální počítače. Po povolení replikace, se na virtuálním počítači nainstaluje služba Mobility. Site Recovery provede počáteční replikaci virtuálního počítače a poté zahájí probíhající replikaci.
> * **Krok 6: Spuštění postupu zotavení po havárii**: po replikace je v provozu, ověříte, že převzetí služeb při selhání bude fungovat podle očekávání, spuštěním přechod. Iniciování na postup zotavení spustit testovací převzetí služeb ve službě Site Recovery. Testovací převzetí služeb nebude mít vliv na vaše produkční prostředí.

Pomocí tohoto postupu dokončení můžete spustit úplné převzetí služeb při selhání do Azure jako a je potřeba.

## <a name="architecture"></a>Architektura

![Architektura](./media/azure-stack-site-recovery/architecture.png)

**Umístění** | **Komponenta** |**Podrobnosti**
--- | --- | ---
**Konfigurační server** | Běží na jednom virtuálním počítači Azure Stack. | V každém předplatném nastavíte konfigurační server virtuálního počítače. Tento virtuální počítač spustí následující součásti Site Recovery:<br/><br/> -Konfigurace serveru: koordinuje komunikaci mezi místním a Azure a spravuje replikaci dat. -Procesový server: funguje jako replikační brána. Přijímá data replikace, optimalizuje je pomocí ukládání do mezipaměti, komprese a šifrování. a odesílá je do úložiště Azure.<br/><br/> Pokud chcete replikovat virtuální počítače překročí omezení uvedená níže, můžete nastavit samostatného procesový server. [Další informace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-set-up-process-server-scale).
**Služba mobility** | Nainstalovat na každý virtuální počítač, který chcete replikovat. | V krocích v tomto článku jsme připravit účet tak, aby služba Mobility se instaluje automaticky na virtuálním počítači po povolení replikace. Pokud nechcete instalovat službu automaticky, existuje mnoho dalších metod, které můžete použít. [Další informace](https://docs.microsoft.com/azure/site-recovery/vmware-azure-install-mobility-service).
**Azure** | V Azure budete potřebovat trezor služby Recovery Services, účet úložiště a virtuální síť. |  Replikovaná data jsou uložena v účtu úložiště. Virtuální počítače Azure jsou přidány do sítě Azure, když dojde k převzetí služeb při selhání. 


Replikace funguje takto:

1. V trezoru zadáte zdroj a cíl replikace, nastavte konfigurační server, vytvořit zásady replikace a povolení replikace.
2. Služba Mobility je nainstalovaná na počítači (Pokud jste použili nabízené instalace) a počítače zahájit replikaci v souladu se zásadami replikace.
3. Počáteční kopie dat serveru se replikuje do úložiště Azure.
4. Po dokončení počáteční replikace začne probíhat rozdílová replikace do Azure. Sledované změny se pro jednotlivé počítače ukládají do souboru .hrl.
5. Konfigurační server orchestruje správu replikace s Azure (odchozí port HTTPS 443).
6. Procesový server přijímá data ze zdrojového počítače, optimalizuje zašifruje a odesílá je do služby Azure storage (odchozím portu 443).
7. Replikované počítače komunikovat s konfiguračním serverem (port HTTPS 443 příchozí kvůli správě replikace. Počítače odesílají data na procesní server (je možné upravit portu HTTPS 9443 inbound -).
8. Provoz se přes internet replikuje do veřejných koncových bodů úložiště Azure. Alternativně můžete použít veřejný partnerský vztah Azure ExpressRoute. Přenos replikačních dat přes síť site-to-site VPN z místního serveru do Azure není podporovaný.

## <a name="prerequisites"></a>Požadavky

Zde je, co je potřeba nastavit tento scénář.

**Požadavek** | **Podrobnosti**
--- | ---
**Účet předplatného Azure** | Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/).
**Oprávnění účtu Azure** | Účet Azure, které můžete použít potřebuje oprávnění:<br/><br/> -Vytvořit trezor služby Recovery Services<br/><br/> – Vytvoření virtuálního počítače ve skupině prostředků a virtuální síť, kterou používáte pro scénář<br/><br/> -Zápis do účtu úložiště, který zadáte<br/><br/> Poznámky:<br/><br/> – Pokud vytvoříte účet, jste správcem předplatného a mohou provádět všechny akce.<br/><br/> – Pokud používáte existující předplatné a nejste správce, budete muset správce přiřadit oprávnění vlastníka nebo přispěvatele.<br/><br/> – Pokud potřebujete podrobnější oprávnění, přečtěte si [v tomto článku](https://docs.microsoft.com/azure/site-recovery/site-recovery-role-based-linked-access-control). 
**Virtuální počítač Azure Stack** | Budete potřebovat virtuální počítač Azure Stack v rámci předplatného tenanta, který se nasadí jako konfigurační server Site Recovery. 


### <a name="prerequisites-for-the-configuration-server"></a>Požadavky na konfiguračním serveru

[!INCLUDE [site-recovery-config-server-reqs-physical](../../includes/site-recovery-config-server-reqs-physical.md)]


 
## <a name="step-1-prepare-azure-stack-vms"></a>Krok 1: Příprava virtuálních počítačů Azure Stack

### <a name="verify-the-operating-system"></a>Zkontrolujte operační systém

Ujistěte se, že jsou virtuální počítače s některým z operačních systémů uvedené v tabulce.


**Operační systém** | **Podrobnosti**
--- | ---
**64bitová verze Windows** | Windows Server 2016, Windows Server 2012 R2, Windows Server 2012, Windows Server 2008 R2 (z verze SP1)
**CentOS** | 5.2 na 5,11 6.1 k 6.9 7.0 pro 7.3
**Ubuntu** | 14.04 server LTS, 16.04 LTS serveru. Kontrola [podporované jádra](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#ubuntu-kernel-versions)

### <a name="prepare-for-mobility-service-installation"></a>Příprava pro instalaci služby Mobility

Každý virtuální počítač, který chcete replikovat, musí mít nainstalovaná služba Mobility. Aby na procesovém serveru a instalace služby automaticky na virtuálním počítači po povolení replikace ověřte nastavení virtuálního počítače.

#### <a name="windows-machines"></a>Počítače s Windows

- Potřebujete síťové připojení mezi virtuální počítač, na kterém chcete povolit replikaci a počítač s procesového serveru (ve výchozím nastavení je to server konfigurace virtuálního počítače).
- Budete potřebovat účet s právy správce (domény nebo místní) na počítači, pro kterou můžete povolit replikaci.
    - Tento účet zadáte při nastavování Site Recovery. Procesový server použije tento účet pro instalaci služby Mobility, když je povolená replikace.
    - Tento účet se používá pouze pomocí Site Recovery pro nabízenou instalaci a jak aktualizovat službu Mobility.
    - Pokud nepoužíváte účet domény, je nutné zakázat vzdálené řízení přístupu uživatele na virtuálním počítači:
        - V registru, vytvořte hodnotu DWORD **LocalAccountTokenFilterPolicy** pod položku HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System.
        - Nastavte hodnotu na 1.
        - Chcete-li to provést na příkazovém řádku zadejte následující příkaz: **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System Přidáte REG /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1**.
- V bráně Windows Firewall na virtuálním počítači, který chcete replikovat Povolit sdílení souborů a tiskáren a WMI.
    - Chcete-li to provést, spusťte **wf.msc** pro otevření konzoly brány Windows Firewall. Klikněte pravým tlačítkem myši **příchozí pravidla** > **nové pravidlo**. Vyberte **předdefinované**a zvolte **souborů a tiskáren sdílení** ze seznamu. Dokončete průvodce, výběrem ji povolte připojení > **Dokončit**.
    - Pro počítače domény můžete k tomu objekt zásad skupiny.

    
#### <a name="linux-machines"></a>Počítače s Linuxem

- Zkontrolujte, že existuje síťové připojení mezi počítačem s Linuxem a procesovým serverem.
- Na počítači, pro kterou povolíte replikaci budete potřebovat účet, který je na zdrojovém serveru s Linuxem uživatelem root:
    - Tento účet zadáte při nastavování Site Recovery. Procesový server použije tento účet pro instalaci služby Mobility, když je povolená replikace.
    - Tento účet se používá pouze pomocí Site Recovery pro nabízenou instalaci a jak aktualizovat službu Mobility.
- Zkontrolujte, že soubor /etc/hosts na zdrojovém serveru s Linuxem obsahuje položky, které mapují místní název hostitele na IP adresy přidružené ke všem síťovým adaptérům.
- Na počítači, který chcete replikovat, nainstalujte nejnovější balíčky openssh, openssh-server a openssl.
- Ujistěte se, že je povolený Secure Shell (SSH) a že běží na portu 22.
- V souboru sshd_config povolte subsystém SFTP a ověřování heslem:
    1. Provedete to tak, přihlaste se jako uživatel root.
    2. Vyhledejte řádek, který začíná **PasswordAuthentication**, v souboru /etc/ssh/sshd_config. Zrušte na řádku komentář a změňte hodnotu na **yes**.
    3. Vyhledejte řádek, který začíná na **Subsystem** a zrušte na něm komentář.

        ![Služba Linux Mobility](./media/azure-stack-site-recovery/linux-mobility.png)

    4. Restartujte službu sshd.


### <a name="note-the-vm-private-ip-address"></a>Poznámka: privátní IP adresu virtuálního počítače

Pro každý počítač, který chcete replikovat zjistit IP adresu:

1. Na portálu Azure Stack klikněte na virtuální počítač.
2. Na **prostředků** nabídky, klikněte na tlačítko **síťová rozhraní**.
3. Poznamenejte si privátní IP adresu.

    ![Privátní IP adresa](./media/azure-stack-site-recovery/private-ip.png)


## <a name="step-2-create-a-vault-and-select-a-replication-goal"></a>Krok 2: Vytvoření trezoru a výběr cíle replikace

1. Na webu Azure Portal vyberte **Vytvořit prostředek** > **Monitorování a správa** > **Backup a Site Recovery**.
2. Jako **Název** zadejte popisný název pro identifikaci trezoru. 
3. V **skupiny prostředků**, vytvořte nebo vyberte skupinu prostředků. Používáme **contosoRG**.
4. V **umístění**, zadejte oblast Azure. používáme oblast **Západní Evropa**.
5. Pokud chcete mít k trezoru rychlý přístup z řídicího panelu, vyberte **Připnout na řídicí panel** > **Vytvořit**.

   ![Vytvoření nového trezoru](./media/azure-stack-site-recovery/new-vault-settings.png)

   Nový trezor se zobrazí v části **Řídicí panel** > **Všechny prostředky** a na hlavní stránce **Trezory služby Recovery Services**.

### <a name="select-a-replication-goal"></a>Výběr cíle replikace

1. V **trezory služby Recovery Services** > zadejte název trezoru. Používáme **ContosoVMVault**.
2. V části **Začínáme** vyberte Site Recovery. Pak vyberte **Příprava infrastruktury**.
3. V části **Cíl ochrany** > **Kde jsou vaše počítače umístěné?** vyberte **Místní**.
4. V části **Kam chcete počítače replikovat?** vyberte **Do Azure**.
5. V **jsou vaše počítače virtualizované**vyberte **nevirtualizované/jiné**. Pak vyberte **OK**.

    ![Cíl ochrany](./media/azure-stack-site-recovery/protection-goal.png)

## <a name="step-3-set-up-the-source-environment"></a>Krok 3: Nastavení zdrojového prostředí

Nastavení konfigurace počítače serveru, zaregistroval v trezoru a zjistit počítače, které chcete replikovat.

1. Klikněte na **Připravit infrastrukturu** > **Zdroj**.
2. Na stránce **Připravit zdroj** klikněte na **+ Konfigurační server**.

    ![Nastavení zdroje](./media/azure-stack-site-recovery/plus-config-srv.png)

3. V **přidat Server**, zkontrolujte, že **konfigurační Server** se zobrazí v **typ serveru**.
5. Stáhněte si instalační soubor sjednocené instalace Site Recovery.
6. Stáhněte registrační klíč trezoru. Při spuštění sjednocené instalace potřebujete registrační klíč. Klíč je platný pět dní od jeho vygenerování.

    ![Nastavení zdroje](./media/azure-stack-site-recovery/set-source2.png)


### <a name="run-azure-site-recovery-unified-setup"></a>Spuštění Azure Site Recovery sjednocené instalace

Instalace a registrace konfiguračního serveru, proveďte připojení RDP k virtuálnímu počítači, které chcete použít pro konfigurační server a spusťte sjednocené instalace.

Než začnete, ujistěte se, že je hodin [synchronizovány s časovým serverem](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service) na virtuálním počítači, než začnete. Instalace selže, pokud čas je více než pět minut mimo místní čas.

Nyní nainstalujte konfigurační server:

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Konfigurační server, můžete ji také nainstalovat z příkazového řádku. [Další informace](physical-manage-configuration-server.md#install-from-the-command-line).

> Může trvat 15 minut nebo déle, než se název účtu objeví na portálu. Pokud chcete provést aktualizaci okamžitě, vyberte **Konfigurační servery** > ***název serveru*** > **Aktualizovat server**.

## <a name="step-4-set-up-the-target-environment"></a>Krok 4: Nastavení cílového prostředí

Vyberte a zkontrolujte cílové prostředky.

1. V **připravit infrastrukturu** > **cílové**, vyberte předplatné Azure, kterou chcete použít.
2. Zadejte model nasazení cíl.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure. Pokud se nenajde, je, musíte vytvořit aspoň jeden účet úložiště a virtuální sítě, aby bylo možné dokončit průvodce.


## <a name="step-5-enable-replication"></a>Krok 5: Povolení replikace

### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Klikněte na tlačítko **připravit infrastrukturu** > **nastavení replikace**.
2. V **vytvoření zásady replikace**, zadejte název zásady.
3. V **prahová hodnota cíle bodu obnovení**, zadejte cíle (RPO) limit bodů obnovení.
    - V souladu s sady času jsou vytvořeny body obnovení pro replikovaná data.
    - Toto nastavení nemá vliv na replikaci, která je souvislý. Jednoduše vydá výstrahu, pokud je dosažen limit prahové hodnoty bez vytváří bod obnovení.
4. V **uchování bodu obnovení**, určete, jak dlouho zůstane každý bod obnovení. Replikované virtuální počítače můžete obnovit do libovolného bodu v zadané časové okno.
5. V **frekvence snímků konzistentní vzhledem k**, určete, jak často vytvářejí snímky konzistentní s aplikací.

    - Snímky konzistentní s aplikací je snímek dat aplikací ve virtuálním počítači v daném okamžiku.
    - Služba Stínová kopie svazku (VSS) zajišťuje, že aplikace na virtuálním počítači v konzistentním stavu při pořízení snímku.
6. Vyberte **OK** a vytvořte zásadu.


### <a name="confirm-deployment-planning"></a>Potvrzení plánování nasazení

Teď můžete tento krok přeskočit. V **plánování nasazení** rozevíracího seznamu, klikněte na tlačítko **Ano, mám to hotové**.



### <a name="enable-replication"></a>Povolení replikace

Ujistěte se, že jste dokončili všechny úkoly v [krok 1: Příprava počítače](#step-1-prepare-azure-stack-vms). Pak následujícím způsobem povolte replikaci:

1. Vyberte **Replikovat aplikaci** > **Zdroj**.
2. V části **Zdroj** vyberte konfigurační server.
3. V **typ počítače**vyberte **fyzické počítače**.
4. Vyberte procesní server (konfigurační server). Pak klikněte na **OK**.
5. V **cílové**, vyberte předplatné a skupinu prostředků, ve kterém chcete vytvořit virtuální počítače po převzetí služeb při selhání. Vyberte model nasazení, který chcete použít pro virtuální počítače s převzetím služeb při selhání.
6. Vyberte účet úložiště Azure, ve kterém chcete ukládají replikovaná data.
7. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se vytvoří po převzetí služeb při selhání.
8. Výběrem možnosti **Nakonfigurovat pro vybrané počítače** použijte nastavení sítě pro všechny počítače, které jste vybrali pro ochranu. Vyberte **nakonfigurovat později** Pokud budete chtít vyberte síť Azure, samostatně pro každý počítač.
9. V **fyzické počítače**, klikněte na tlačítko **+ fyzický počítač**. Zadejte název IP adresu každého počítače a operační systém, který chcete replikovat.

    - Použijte interní IP adresu počítače.
    - Pokud určíte, že veřejné IP adresy replikace nemusí fungovat podle očekávání.

10. V **vlastnosti** > **konfigurovat vlastnosti**, vyberte účet, který použije procesový server při automatické instalaci služby Mobility na počítači.
11. V **nastavení replikace** > **konfigurace nastavení replikace**, zkontrolujte, že je vybraná správná zásada replikace.
12. Klikněte na **Povolit replikaci**.
13. Sledovat průběh **povolení ochrany** úlohy **nastavení** > **úlohy** > **úlohy Site Recovery**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.

> [!NOTE]
> Jakmile bude replikace virtuálního počítače povolená, Site Recovery nainstaluje službu mobility.

> Může to trvat 15 minut nebo déle, než se změny projeví a objeví se na portálu.

> Pokud chcete monitorovat virtuální počítače, které jste přidali, zkontrolujte čas posledního zjištění virtuálních počítačů v části **Konfigurační servery** > **Poslední kontakt**. Pokud chcete přidat virtuální počítače bez čekání na naplánované zjišťování, zvýrazněte konfigurační server (nevybírejte ho) a vyberte **Aktualizovat**.


## <a name="step-6-run-a-disaster-recovery-drill"></a>Krok 6: Spuštění postupu zotavení po havárii

Spustit testovací převzetí služeb do Azure, abyste měli jistotu, že vše funguje podle očekávání. Toto převzetí služeb při selhání nebude mít vliv na vaše produkční prostředí.

### <a name="verify-machine-properties"></a>Ověřte vlastnosti počítače

Předtím, než spustíte testovací převzetí služeb, ověřte vlastnosti počítače a ujistěte se, že jsou v souladu s [požadavky služby Azure](https://docs.microsoft.com/azure/site-recovery/vmware-physical-azure-support-matrix#azure-vm-requirements). Můžete zobrazit a upravit vlastnosti následujícím způsobem:

1. V části **Chráněné položky** klikněte na **Replikované položky** a pak na virtuální počítač.
2. V podokně **Replikovaná položka** se zobrazí souhrn informací o virtuálním počítači, jeho stav a nejnovější dostupné body obnovení. Kliknutím na **Vlastnosti** zobrazíte další podrobnosti.
3. V **výpočty a síť**, podle potřeby upravte nastavení.

    - Můžete upravit název virtuálního počítače Azure, skupinu prostředků, cílovou velikost, [dostupnosti](../virtual-machines/windows/tutorial-availability-sets.md)a nastavení spravovaného disku.
    - Můžete také zobrazit a upravit nastavení sítě. Mezi ně patří sítě a podsítě, ke kterému je připojený virtuální počítač Azure po převzetí služeb při selhání a IP adresu, která se přiřadí k virtuálnímu počítači.
1. V **disky**, zobrazit informace o operačním systému a datové disky na virtuálním počítači.
   

### <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Když spustíte testovací převzetí služeb při selhání, stane se následující:

1. Spustí se kontrola předpokladů, která zjistí, zda jsou splněné všechny podmínky pro převzetí služeb při selhání.
2. Převzetí služeb při selhání zpracovává data pomocí Zadaný bod obnovení:
    - **Nejnovější zpracovaný**: počítač selže přes k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Časové razítko je vidět. Tato možnost neztrácí žádný čas zpracováním dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
    - **Nejnovější konzistentní vzhledem k**. Tento počítač převezme služby při selhání do nejnovějšího bodu obnovení s konzistentní aplikací.
    - **Vlastní**. Vyberte bod obnovení pro převzetí služeb při selhání.

3. Virtuální počítač Azure je vytvořený pomocí zpracovaná data.
4. Virtuální počítače Azure vytvořené během na postup zotavení můžete automaticky vyčistit testovací převzetí služeb při selhání.

Spustíte testovací převzetí služeb virtuálního počítače následujícím způsobem:

1. V části **Nastavení** > **Replikované položky** klikněte na virtuální počítač a pak na **+ Testovací převzetí služeb při selhání**.
2. V tomto návodu, vybereme použít **nejnovější zpracovaný** bod obnovení. 
3. V **testovací převzetí služeb při selhání**, vyberte cílovou síť Azure.
4. Kliknutím na **OK** zahajte převzetí služeb při selhání.
5. Průběh můžete sledovat kliknutím na virtuálním počítači otevřete jeho vlastnosti. Nebo klikněte na tlačítko **testovací převzetí služeb při selhání** úlohy *název_trezoru* > **nastavení** > **úlohy**  > **Úlohy site Recovery**.
6. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Zkontrolujte, že je virtuální počítač odpovídající velikost, připojený k správné síti a běží.
7. Nyní byste se měli moct k replikovanému virtuálnímu počítači v Azure připojit. [Další informace](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover).
8. Virtuální počítače Azure vytvořené během testu převzetí služeb při selhání odstraníte kliknutím na **Vyčistit testovací převzetí služeb při selhání** na virtuálním počítači. V **poznámky**, uložte jakékoli připomínky související s testovací převzetí služeb.

## <a name="fail-over-and-fail-back"></a>Převzetí služeb při selhání a navrácení služeb po obnovení

Poté, co jste nastavili replikaci a spustit procházení k Ujistěte se, že všechno funguje, můžete převzít služby počítače do Azure podle potřeby.

Před spuštěním převzetí služeb při selhání, pokud se chcete připojit k počítači v Azure po převzetí služeb se pak [Příprava připojení](https://docs.microsoft.com/azure/site-recovery/site-recovery-test-failover-to-azure#prepare-to-connect-to-azure-vms-after-failover) před zahájením.

Spusťte převzetí služeb při selhání následujícím způsobem:


1. V **nastavení** > **replikované položky**, klikněte na počítač > **převzetí služeb při selhání**.
2. Vyberte bod obnovení, který chcete použít.
3. V **testovací převzetí služeb při selhání**, vyberte cílovou síť Azure.
4. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. S tímto nastavením Site Recovery se pokusí vypnout zdrojový počítač před spuštěním převzetí služeb. Převzetí služeb při selhání ale dál, i když se vypnutí nepovede. 
5. Kliknutím na **OK** zahajte převzetí služeb při selhání. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
6. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Pokud jste připravili připojení po převzetí služeb při selhání, zkontrolujte, že je virtuální počítač odpovídající velikost, připojený ke správné síti a spouštění.
7. Po ověření tento virtuální počítač, klikněte na tlačítko **potvrzení** dokončete převzetí služeb při selhání. Tím se odstraní všechny dostupné body obnovení.

> [!WARNING]
> Nepřerušujte převzetí služeb při selhání v průběhu: před spuštěním převzetí služeb při selhání, se zastaví replikace virtuálního počítače. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.


### <a name="fail-back-to-azure-stack"></a>Selhání zpátky do služby Azure Stack

Když je primární lokalita je znovu spuštěn a pracuje, můžete službu navrátit z Azure do Azure Stack. K tomuto účelu, budete muset stáhnout virtuální pevný disk virtuálního počítače Azure a nahrajte ho do služby Azure Stack.

1. Vypněte virtuální počítač Azure tak, aby si můžete stáhnout virtuální pevný disk. 
2. Pokud chcete začít, stahování virtuální pevný disk, nainstalujte [Průzkumníka služby Azure Storage](https://azure.microsoft.com/features/storage-explorer/).
3. Přejděte do virtuálního počítače na webu Azure Portal (s použitím názvu virtuálního počítače).
4. V **disky**, klikněte na název disku a shromážděte nastavení.

    - Jako příklad, URI virtuálního pevného disku se používá v testovacím: https://502055westcentralus.blob.core.windows.net/wahv9b8d2ceb284fb59287/copied-3676553984.vhd je možné rozdělit zobrazíte následující vstupní parametry, které se používají ke stažení virtuálního pevného disku.
        - Účet úložiště: 502055westcentralus
        - Kontejner: wahv9b8d2ceb284fb59287
        - Název virtuálního pevného disku: 3676553984.vhd zkopírovat

5. Teď pomocí Průzkumníka služby Azure Storage stáhnout virtuální pevný disk.
6. Nahrání virtuálního pevného disku do služby Azure Stack s [tyto kroky](https://docs.microsoft.com/azure/azure-stack/user/azure-stack-manage-vm-disks#use-powershell-to-add-multiple-unmanaged-disks-to-a-vm).
7. V existující virtuální počítač nebo nového virtuálního počítače, připojit nahraný virtuální pevné disky.
8. Zkontrolujte správnost Disk s operačním systémem a spuštění virtuálního počítače.


V této fázi dokončení navrácení služeb po obnovení.


## <a name="conclusion"></a>Závěr

V tomto článku jsme zásobníku virtuálních počítačů Azure replikovaných do Azure. Díky replikaci na místě jsme narazili zotavení po havárii zajistit, aby pracoval převzetí služeb při selhání do Azure podle očekávání. Tento článek také uvedené kroky pro spouštění úplné převzetí služeb při selhání do Azure a navrácení služeb na Azure Stack.

## <a name="next-steps"></a>Další postup

Po navrácení služeb po obnovení, možné znovunastavení ochrany virtuálního počítače a začaly replikovat do Azure znovu, chcete-li to provést, opakujte kroky v tomto článku.

