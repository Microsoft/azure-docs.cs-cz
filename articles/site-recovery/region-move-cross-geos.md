---
title: Přesunutí virtuálních počítačů Azure mezi státní mise a veřejné oblasti pomocí Azure Site Recovery
description: Azure Site Recovery slouží k přesunu virtuálních počítače Azure mezi azure government a veřejnými oblastmi.
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: acaf16e7469b3ea4e5e391db91e37dc76be3b261
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "78298526"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Přesun virtuálních počítačů Azure mezi oblastmi Azure Government a veřejnými oblastmi 

Můžete chtít přesunout virtuální počítače IaaS mezi Azure Government a veřejné oblasti zvýšit dostupnost vašich stávajících virtuálních počítačů, zlepšit možnosti správy nebo z důvodů zásad správného řízení, jak je podrobně [uvedeno zde](azure-to-azure-move-overview.md).

Kromě použití služby [Azure Site Recovery](site-recovery-overview.md) ke správě a orchestraci zotavení po havárii místních počítačů a virtuálních počítačů Azure pro účely kontinuity podnikání a zotavení po havárii (BCDR) můžete také použít Site Recovery ke správě přesunutí virtuálních počítačů Azure do sekundární oblasti.       

Tento kurz ukazuje, jak přesunout virtuální počítače Azure mezi Azure Government a veřejných oblastí pomocí Azure Site Recovery. Totéž lze rozšířit přesunout virtuálních klíčů mezi dvojice oblastí, které nejsou ve stejném geografickém clusteru. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověření požadavků
> * Příprava zdrojových virtuálních počítačů
> * Příprava cílové oblasti
> * Kopírování dat do cílové oblasti
> * Otestujte konfiguraci
> * Provedení přesunu
> * Zahodit prostředky ve zdrojové oblasti

> [!IMPORTANT]
> Tento kurz ukazuje, jak přesunout virtuální počítače Azure mezi Azure Government a public oblastech nebo mezi páry oblastí, které nejsou podporované běžným řešením zotavení po havárii pro virtuální počítače Azure. V případě, že jsou [podporovány](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support)dvojice zdrojových a cílových oblastí , naleznete v tomto [dokumentu](azure-to-azure-tutorial-migrate.md) pro přesun. Pokud je vaším požadavkem na zlepšení dostupnosti přesunutím virtuálních počítače v sadě dostupnosti do zóny připnutých virtuálních počítačích v jiné oblasti, podívejte se na kurz [zde](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Není vhodné použít tuto metodu ke konfiguraci zotavení po havárii mezi nepodporovanými dvojicemi oblastí, protože dvojice jsou definovány s ohledem na latenci dat, což je důležité pro scénář zotavení po havárii.

## <a name="verify-prerequisites"></a>Ověření požadavků

> [!NOTE]
> Ujistěte se, že rozumíte [architektuře a součástem](physical-azure-architecture.md) pro tento scénář. Tato architektura se použije k přesunutí virtuálních počítačích Azure **tím, že virtuální počítače považujete za fyzické servery**.

- Zkontrolujte [požadavky na podporu](vmware-physical-secondary-support-matrix.md) pro všechny komponenty.
- Ujistěte se, že servery, které chcete replikovat, splňují [požadavky virtuálních zařízení Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Připravte si účet pro automatickou instalaci služby Mobility na každém serveru, který chcete replikovat.

- Všimněte si, že po převzetí služeb při selhání do cílové oblasti v Azure, nelze přímo provést vrácení služeb při selhání do zdrojové oblasti. Budete muset nastavit replikaci znovu zpět do cíle.

### <a name="verify-azure-account-permissions"></a>Ověření oprávnění účtu Azure

Ujistěte se, že váš účet Azure má oprávnění pro replikaci virtuálních počítačů do Azure.

- Zkontrolujte [oprávnění,](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) která potřebujete k replikaci počítačů do Azure.
- Ověřte a upravte přístupová oprávnění [založená na rolích.](../role-based-access-control/role-assignments-portal.md) 

### <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Nastavte cílovou [síť Azure](../virtual-network/quick-create-portal.md).

- Virtuální počítače Azure se umístí do této sítě, když se vytvoří po převzetí služeb při selhání.
- Síť by měla být ve stejné oblasti jako trezor služby Recovery Services.


### <a name="set-up-an-azure-storage-account"></a>Nastavení účtu úložiště Azure

Nastavte [účet úložiště Azure](../storage/common/storage-account-create.md).

- Site Recovery replikuje místní počítače do úložiště Azure. Virtuální počítače Azure se vytvoří z úložiště po převzetí služeb při selhání dojde.
- Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.


## <a name="prepare-the-source-vms"></a>Příprava zdrojových virtuálních počítačů

### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Služba Mobility musí být nainstalována na každém serveru, který chcete replikovat. Site Recovery nainstaluje tuto službu automaticky, když povolíte replikaci serveru. Chcete-li nainstalovat automaticky, je třeba připravit účet, který site recovery bude používat pro přístup k serveru.

- Můžete použít doménu nebo místní účet
- Pokud u virtuálních měn windows nepoužíváte účet domény, zakažte řízení vzdáleného přístupu uživatelů v místním počítači. Chcete-li to provést, přidejte do registru **v HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**položku DWORD **LocalAccountTokenFilterPolicy**s hodnotou 1.
- Chcete-li přidat položku registru a zakázat nastavení z příkazového příkazového příkazu, zadejte:``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Pro Linux by měl být účet kořenna zdrojovém serveru Linux.


## <a name="prepare-the-target-region"></a>Příprava cílové oblasti

1. Ověřte, že vám předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti používané ke zotavení po havárii. O povolení požadované kvóty požádejte podporu.

2. Zkontrolujte, jestli máte v rámci předplatného k dispozici dostatek prostředků pro podporu virtuálních počítačů s takovými velikostmi, které odpovídají velikostem zdrojových virtuálních počítačů. Pokud používáte Site Recovery ke kopírování dat do cíle, vybere stejnou velikost nebo nejbližší možnou velikost pro cílový virtuální virtuální počítače.

3. Ujistěte se, že vytvoříte cílový prostředek pro každou součást identifikovanou v rozložení zdrojové sítě. To je důležité zajistit, že po řezání do cílové oblasti, vaše virtuální počítače mají všechny funkce a funkce, které jste měli ve zdroji.

    > [!NOTE]
    > Azure Site Recovery automaticky zjišťuje a vytvoří virtuální síť, když povolíte replikaci pro zdrojový virtuální počítač, nebo můžete také předem vytvořit síť a přiřadit k virtuálnímu počítači v toku uživatele pro povolení replikace. Ale pro všechny ostatní prostředky, jak je uvedeno níže, je třeba ručně vytvořit v cílové oblasti.

     Na základě zdrojové konfigurace virtuálního počítače najdete v následujících dokumentech, kde můžete vytvořit nejčastěji používané síťové prostředky, které jsou pro vás relevantní.

    - [Skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Služby vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer)
    - [Veřejná IP adresa](../virtual-network/virtual-network-public-ip-address.md)
    
    Další síťové součásti naleznete v [dokumentaci](https://docs.microsoft.com/azure/?pivot=products&panel=network)k síti .

4. Chcete-li otestovat konfiguraci před provedením konečného přeříznutého řezu do cílové oblasti, vytvořte ručně [neprodukční síť](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) v cílové oblasti. Tím vznikne minimální rušení výroby a doporučuje se.

## <a name="copy-data-to-the-target-region"></a>Kopírování dat do cílové oblasti
Níže uvedené kroky vás povedou, jak pomocí Azure Site Recovery zkopírovat data do cílové oblasti.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Vytvořte trezor v libovolné oblasti, s výjimkou zdrojové oblasti.

1. Přihlaste se ke**službě Obnovení** [portálu](https://portal.azure.com) > Azure .
2. Klepněte **na tlačítko Vytvořit** > nástroje**pro** > správu prostředků Backup a Site**Recovery**.
3. Do pole **Název** zadejte popisný název **ContosoVMVault**. Pokud máte více než jeden a. předplatného, vyberte příslušnou.
4. Vytvořte skupinu prostředků **ContosoRG**.
5. Zadejte oblast Azure. Pokud chcete zkontrolovat podporované oblasti, přečtěte si informace o geografické dostupnosti v [tématu Podrobnosti o cenách obnovení webu Azure](https://azure.microsoft.com/pricing/details/site-recovery/).
6. V trezorech služby Recovery Services klikněte na **Přehled** > **consotoVMVault** > **+Replicate**
7. **Vyberte: Azure** > **Není virtualizováno nebo jiné**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Nastavte konfigurační server pro zjišťování virtuálních počítačů.


Nastavte konfigurační server, zaregistrujte ho v trezoru a zjišťujte virtuální počítače.

1. Klikněte na **Site Recovery** > **Připravt infrastructure** > **Source**.
2. Pokud konfigurační server nemáte, klepněte na tlačítko **+Konfigurační server**.
3. V **části Přidat server**zkontrolujte, zda se **konfigurační server** zobrazuje v části Typ **serveru**.
4. Stáhněte instalační soubor sjednoceného instalačního programu pro obnovení webu.
5. Stáhněte registrační klíč trezoru. Potřebujete to při spuštění sjednoceného nastavení. Klíč je platný pět dní od jeho vygenerování.

   ![Nastavení zdroje](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrace konfiguračního serveru v úschovně

Než začnete, postupujte takto: 

#### <a name="verify-time-accuracy"></a>Ověření přesnosti času
V počítači konfiguračního serveru zkontrolujte, zda jsou systémové hodiny synchronizovány s [časovým serverem](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Mělo by se to shodovat. Pokud je to 15 minut vpředu nebo vzadu, nastavení může selhat.

#### <a name="verify-connectivity"></a>Ověření připojení
Ujistěte se, že počítač má přístup k těmto adresám URL na základě vašeho prostředí: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Pravidla brány firewall založené na IP adresách by měla umožňovat komunikaci se všemi adresami URL Azure, které jsou uvedeny výše přes port HTTPS (443). Chcete-li zjednodušit a omezit rozsahy IP adres, doporučujeme provést filtrování adres URL.

- **Komerční IP adresy** – povolte [rozsahy IP adres datového centra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)a port HTTPS (443). Povolte rozsahy IP adres pro oblast Azure vašeho předplatného pro podporu adres URL Služby AAD, Zálohování, Replikace a Úložiště.  
- **Ip adresy pro státní správu** – povolte [rozsahy IP adres Datového centra Azure vlády](https://www.microsoft.com/en-us/download/details.aspx?id=57063)a port HTTPS (443) pro všechny oblasti USGov (Virginie, Texas, Arizona a Iowa) pro podporu adres URL AAD, Zálohování, Replikace a Úložiště.  

#### <a name="run-setup"></a>Spuštění instalace
Spusťte sjednocené nastavení jako místního správce a nainstalujte konfigurační server. Procesní server a hlavní cílový server jsou také nainstalovány ve výchozím nastavení na konfiguračním serveru.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Po dokončení registrace se konfigurační server zobrazí na stránce **Nastavení** > **serverů** v úschovně.

### <a name="configure-target-settings-for-replication"></a>Konfigurace nastavení cíle pro replikaci

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **Připravit cíl infrastruktury** > **Target**a vyberte předplatné Azure, které chcete použít.
2. Zadejte cílový model nasazení.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

   ![Cíl](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Chcete-li vytvořit novou zásadu replikace, klepněte na položku**Zásady replikace infrastruktury** >  **obnovení lokality** > **+Zásady replikace**.
2. V části **Vytvoření zásad replikace** zadejte název zásady.
3. V části **Prahová hodnota cíle bodu obnovení** (RPO) zadejte omezení cíle bodu obnovení. Tato hodnota určuje, jak často jsou vytvářeny body obnovení dat. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
4. V části **Uchování bodu obnovení** zadejte (v hodinách), jak dlouhý je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu. Pro počítače replikované do úložiště úrovně Premium se podporuje uchování po dobu až 24 hodin. V případě úložiště úrovně Standard je to 72 hodin.
5. V **četnosti snímků konzistentních s aplikací**určete, jak často (v minutách) budou vytvořeny body obnovení obsahující snímky konzistentní s aplikací. Kliknutím na tlačítko **OK** vytvořte zásadu.

    ![Zásady replikace](./media/physical-azure-disaster-recovery/replication-policy.png)


Tato zásada se automaticky přidruží ke konfiguračnímu serveru. Ve výchozím nastavení se pro navrácení služeb po obnovení automaticky vytvoří zásada párování. Pokud je například zásada replikace **zásadou opakování,** vytvoří se zásada navrácení služeb po **selhání, která by nabyla na znovunavrácení služeb** po selhání. Tato zásada se nepoužije, dokud nespustíte navrácení služeb po obnovení z Azure.

### <a name="enable-replication"></a>Povolení replikace

- Obnovení sítě nainstaluje službu Mobility, pokud je povolena replikace.
- Povolíte-li replikaci serveru, může trvat 15 minut nebo déle, než se změny projeví a zobrazí se na portálu.

1. Klepněte na **položku Replikovat** > **zdroj**aplikace .
2. V části **Zdroj** vyberte konfigurační server.
3. V **pole Typ stroje**vyberte fyzické **stroje**.
4. Vyberte procesní server (konfigurační server). Pak klikněte na **OK**.
5. V **targetu**vyberte předplatné a skupinu prostředků, ve které chcete po převzetí služeb při selhání vytvořit virtuální počítače Azure. Zvolte model nasazení, který chcete použít v Azure (klasická správa prostředků nebo správa prostředků).
6. Vyberte účet úložiště Azure, který chcete použít pro replikaci dat. 
7. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se po převzetí služeb při selhání vytvoří.
8. Chcete-li použít nastavení sítě pro všechny počítače, které vyberete pro ochranu, vyberte **možnost Konfigurovat nyní pro vybrané počítače**. Chcete-li vybrat síť Azure pro každý počítač, **vyberte možnost Konfigurovat později.** 
9. Ve **fyzické matné a**klepněte na tlačítko **+Fyzický počítač**. Zadejte název a adresu IP. Vyberte operační systém počítače, který chcete replikovat. Trvá několik minut, než budou servery objeveny a uvedeny. 

   > [!WARNING]
   > Musíte zadat IP adresu virtuálního počítače Azure, který chcete přesunout.

10. V **části Vlastnosti** > **Konfigurace vlastností**vyberte účet, který bude zpracovávaný server používat k automatické instalaci služby Mobility do počítače.
11. V >  **nastavení replikace**Konfigurace nastavení**replikace**ověřte, zda je vybrána správná zásada replikace. 
12. Klepněte na tlačítko **Povolit replikaci**. Průběh úlohy **Povolit ochranu** můžete sledovat v části**Úlohy** > **obnovení webu** **nastavení** > . Po spuštění úlohy **Finalize Protection** je počítač připraven k převzetí služeb při selhání.


Chcete-li sledovat servery, které přidáte, můžete zkontrolovat čas posledního zjištění v **konfiguračních serverech** > **Poslední kontakt na**. Chcete-li přidat počítače bez čekání na naplánovaný čas zjišťování, zvýrazněte konfigurační server (neklikejte na něj) a klepněte na tlačítko **Aktualizovat**.

## <a name="test-the-configuration"></a>Otestujte konfiguraci


1. Přejděte do trezoru v **nastavení** > **Replikované položky**, klikněte na virtuální počítač, který chcete přesunout do cílové oblasti, klikněte na **+Test převzetí služeb při selhání** ikonu.
2. V části **Testovací převzetí služeb při selhání** vyberte bod obnovení, který se má pro převzetí služeb při selhání použít:

   - **Nejnovější zpracovaný:** Vrátí virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Zobrazí se časové razítko. S touto možností se neztrácí žádný čas zpracováním dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
   - **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost vrátí všechny virtuální počítače k nejnovějšímu konzistentnímu bodu obnovení vzhledem k aplikacím. Zobrazí se časové razítko.
   - **Vlastní**: Vyberete si libovolný bod obnovení.

3. Vyberte cílovou virtuální síť Azure, do které chcete přesunout virtuální počítače Azure a otestovat konfiguraci. 

   > [!IMPORTANT]
   > Doporučujeme použít samostatnou síť virtuálních počítačích Azure pro převzetí služeb při selhání testu a ne produkční síť, do které chcete přesunout virtuální počítače, která byla nakonec nastavena, když jste povolili replikaci.

4. Chcete-li zahájit testování přesunu, klepněte na tlačítko **OK**. Pokud chcete sledovat průběh, kliknutím na virtuální počítač otevřete jeho vlastnosti. Případně můžete kliknout na úlohu **Testovací převzetí služeb při selhání** v části název_trezoru > **Nastavení** > **Úlohy** > **Úlohy Site Recovery**.
5. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Ujistěte se, že je virtuální počítač spuštěný, má odpovídající velikost a je připojený k odpovídající síti.
6. Pokud chcete odstranit virtuální počítač vytvořený v rámci testování přesunutí, klikněte na možnost **Převzetí služeb při selhání testu vyčištění** u replikované položky. V **poznámkách**zaznamenáte a uložte všechna pozorování spojená s testem.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Proveďte přesun do cílové oblasti a potvrďte.

1. Přejděte do trezoru v **nastavení** > **Replikované položky**, klikněte na virtuální počítač a potom klikněte na **převzetí služeb při selhání**.
2. V okně **Převzetí služeb při selhání** vyberte **Nejnovější**. 
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Služba Site Recovery se před aktivací převzetí služeb při selhání pokusí zdrojový virtuální počítač vypnout. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**. 
4. Po dokončení úlohy zkontrolujte, že virtuální počítač se zobrazí v oblasti cílové Azure podle očekávání.
5. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a pak na **Potvrdit**. Tím dokončíte proces přesunutí do cílové oblasti. Počkejte, až bude úloha potvrzení dokončena.

## <a name="discard-the-resource-in-the-source-region"></a>Zahodit prostředek ve zdrojové oblasti 

- Přejděte na virtuální ms.  Klikněte na **Zakázat replikaci**.  Tím se zastaví proces kopírování dat pro virtuální hod.  

   > [!IMPORTANT]
   > Je důležité provést tento krok, aby se zabránilo účtování poplatku za replikaci ASR.

V případě, že nemáte žádné plány na opětovné použití některého ze zdrojových zdrojů, pokračujte v další sadě kroků.

1. Pokračujte k odstranění všech příslušných síťových prostředků ve zdrojové oblasti, které jste uvedli jako součást kroku 4 v [části Příprava zdrojových virtuálních počítačů](#prepare-the-source-vms) 
2. Odstraňte odpovídající účet úložiště ve zdrojové oblasti.



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli virtuální počítač Azure do jiné oblasti Azure. Teď můžete nakonfigurovat zotavení po havárii pro přesunutý virtuální počítače.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii po migraci](azure-to-azure-quickstart.md)
