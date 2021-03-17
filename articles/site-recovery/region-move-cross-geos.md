---
title: Přesun virtuálních počítačů Azure mezi státními a veřejnými regiony pomocí Azure Site Recovery
description: K přesunutí virtuálních počítačů Azure mezi vládou Azure a veřejnými regiony použijte Azure Site Recovery.
author: sideeksh
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: sideeksh
ms.custom: MVC
ms.openlocfilehash: a76ebf95b92b6e1251a04daa9ffb48a9abe15b50
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89425343"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Přesun virtuálních počítačů Azure mezi oblastmi Azure Government a veřejnými oblastmi 

Můžete chtít přesunout virtuální počítače s IaaS mezi Azure Government a veřejnými oblastmi a zvýšit tak dostupnost stávajících virtuálních počítačů, zlepšit spravovatelnost nebo pro účely správy, jak je popsáno [zde](azure-to-azure-move-overview.md).

Kromě používání služby [Azure Site Recovery](site-recovery-overview.md) ke správě a orchestraci zotavení po havárii místních počítačů a virtuálních počítačů Azure pro účely kontinuity podnikových prostředí a zotavení po havárii (BCDR), můžete pomocí Site Recovery spravovat přesun virtuálních počítačů Azure do sekundární oblasti.       

V tomto kurzu se dozvíte, jak přesunout virtuální počítače Azure mezi Azure Government a veřejnými oblastmi pomocí Azure Site Recovery. Stejný postup je možné rozšířit tak, aby se přesunuly virtuální počítače mezi páry oblastí, které nejsou v rámci stejného geografického clusteru. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověření požadavků
> * Příprava zdrojových virtuálních počítačů
> * Příprava cílové oblasti
> * Kopírovat data do cílové oblasti
> * Otestování konfigurace
> * Provést přesun
> * Zahodí prostředky ve zdrojové oblasti.

> [!IMPORTANT]
> V tomto kurzu se dozvíte, jak přesunout virtuální počítače Azure mezi Azure Government a veřejné oblasti nebo mezi páry oblastí, které nejsou podporované běžným řešením zotavení po havárii pro virtuální počítače Azure. V případě, že jsou [podporovány](./azure-to-azure-support-matrix.md#region-support)páry zdrojové a cílové oblasti, přečtěte si tento [dokument](azure-to-azure-tutorial-migrate.md) . Pokud vaše požadavky vylepšit dostupnost přesunutím virtuálních počítačů ve skupině dostupnosti do připnuté virtuální počítače v jiné oblasti, přečtěte si [Tento kurz.](move-azure-VMs-AVset-Azone.md)

> [!IMPORTANT]
> Tuto metodu není vhodné používat ke konfiguraci DR mezi nepodporovanými páry oblastí, protože páry jsou definované tak, aby zachovává latenci dat, což je pro scénář zotavení po havárii důležité.

## <a name="verify-prerequisites"></a>Ověření požadavků

> [!NOTE]
> Ujistěte se, že rozumíte [architektuře a komponentám](physical-azure-architecture.md) pro tento scénář. Tato architektura se použije k přesunu virtuálních počítačů Azure **tím, že se virtuální počítače považují za fyzické servery**.

- Zkontrolujte [požadavky na podporu](vmware-physical-secondary-support-matrix.md) pro všechny komponenty.
- Ujistěte se, že servery, které chcete replikovat, jsou v rozporu s [požadavky na virtuální počítače Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Připravte účet na automatickou instalaci služby mobility na každý server, který chcete replikovat.

- Všimněte si, že když převezmete služby při selhání do cílové oblasti v Azure, nemůžete přímo provést navrácení služeb po obnovení do zdrojové oblasti. Znovu budete muset nastavit replikaci zpátky na cíl.

### <a name="verify-azure-account-permissions"></a>Ověření oprávnění účtu Azure

Ujistěte se, že váš účet Azure má oprávnění k replikaci virtuálních počítačů do Azure.

- Zkontrolujte [oprávnění](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) , která potřebujete k replikaci počítačů do Azure.
- Ověřte a upravte oprávnění [řízení přístupu na základě role Azure (Azure RBAC)](../role-based-access-control/role-assignments-portal.md) . 

### <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Nastavte cílovou [síť Azure](../virtual-network/quick-create-portal.md).

- Virtuální počítače Azure jsou umístěné v této síti, když se vytvoří po převzetí služeb při selhání.
- Síť by měla být ve stejné oblasti jako úložiště Recovery Services.


### <a name="set-up-an-azure-storage-account"></a>Nastavení účtu úložiště Azure

Nastavte [účet úložiště Azure](../storage/common/storage-account-create.md).

- Site Recovery replikuje místní počítače do služby Azure Storage. Virtuální počítače Azure se vytvoří z úložiště po převzetí služeb při selhání.
- Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.


## <a name="prepare-the-source-vms"></a>Příprava zdrojových virtuálních počítačů

### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na každém serveru, který chcete replikovat, musí být nainstalovaná služba mobility. Site Recovery tuto službu nainstaluje automaticky, když povolíte replikaci pro server. K automatické instalaci musíte připravit účet, který Site Recovery použít pro přístup k serveru.

- Můžete použít doménový nebo místní účet.
- Pokud v případě virtuálních počítačů s Windows nepoužíváte doménový účet, zakažte na místním počítači vzdálené řízení přístupu uživatele. To provedete tak, že v registru pod **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**PŘIDÁTE položku DWORD **LocalAccountTokenFilterPolicy**s hodnotou 1.
- Chcete-li přidat položku registru pro zakázání nastavení z rozhraní příkazového řádku, zadejte:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Pro Linux by měl být účet rootem na zdrojovém serveru Linux.


## <a name="prepare-the-target-region"></a>Příprava cílové oblasti

1. Ověřte, že vám předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti používané ke zotavení po havárii. O povolení požadované kvóty požádejte podporu.

2. Zkontrolujte, jestli máte v rámci předplatného k dispozici dostatek prostředků pro podporu virtuálních počítačů s takovými velikostmi, které odpovídají velikostem zdrojových virtuálních počítačů. Pokud používáte Site Recovery ke kopírování dat do cíle, pro cílový virtuální počítač se vybere stejná velikost nebo nejbližší možná velikost.

3. Ujistěte se, že jste vytvořili cílový prostředek pro každou součást identifikovanou v rozložení zdrojové sítě. To je důležité, aby se zajistilo, že po rozřezání do cílové oblasti budou mít vaše virtuální počítače všechny funkce a funkce, které jste měli ve zdroji.

    > [!NOTE]
    > Azure Site Recovery automaticky zjistí a vytvoří virtuální síť, když povolíte replikaci pro zdrojový virtuální počítač, nebo můžete také předem vytvořit síť a přiřadit ji k virtuálnímu počítači v toku uživatele pro povolení replikace. U jakýchkoli jiných prostředků, jak je uvedeno níže, je třeba je ručně vytvořit v cílové oblasti.

     Pro vytvoření nejčastěji používaných síťových prostředků, které jsou pro vás relevantní, použijte následující dokumenty v závislosti na konfiguraci zdrojového virtuálního počítače.

    - [Skupiny zabezpečení sítě](../virtual-network/manage-network-security-group.md)
    - [Služby vyrovnávání zatížení](../load-balancer/index.yml)
    - [Veřejná IP adresa](../virtual-network/virtual-network-public-ip-address.md)
    
    Další síťové součásti najdete v [dokumentaci](../index.yml?pivot=products&panel=network)k síti.

4. Ruční [Vytvoření neprodukční sítě](../virtual-network/quick-create-portal.md) v cílové oblasti, pokud chcete otestovat konfiguraci před dokončením finálního kopírování do cílové oblasti. Tím se vytvoří minimální interference s produkčním prostředím a doporučuje se.

## <a name="copy-data-to-the-target-region"></a>Kopírovat data do cílové oblasti
Následující kroky vám pomohou použít Azure Site Recovery ke kopírování dat do cílové oblasti.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>Vytvořte trezor v libovolné oblasti, s výjimkou zdrojové oblasti.

1. Přihlaste se [Azure portal](https://portal.azure.com)k  >  **Recovery Services**Azure Portal.
2. Klikněte na **vytvořit**  >  **Nástroje pro správu**prostředků  >  **zálohování a Site Recovery**.
3. Do pole **Název** zadejte popisný název **ContosoVMVault**. Pokud máte více než jeden. Vyberte příslušný odběr.
4. Vytvořte skupinu prostředků **ContosoRG**.
5. Zadejte oblast Azure. Pokud chcete zjistit podporované oblasti, přečtěte si článek geografická dostupnost v [Azure Site Recovery podrobnosti o cenách](https://azure.microsoft.com/pricing/details/site-recovery/).
6. V Recovery Services trezorů klikněte na **Přehled**  >  **ConsotoVMVault**  >  **+ replikovat** .
7. Výběr **do Azure**  >  **není virtualizovaný/jiný**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Nastavte konfigurační server pro zjišťování virtuálních počítačů.


Nastavte konfigurační server, zaregistrujte ho v trezoru a vyhledejte virtuální počítače.

1. Klikněte na **Site Recovery**  >  **připravit**  >  **zdroj**infrastruktury.
2. Pokud nemáte konfigurační server, klikněte na **+ konfigurační server**.
3. V nástroji **Přidat server**ověřte, že se **konfigurační server** zobrazuje v **typu serveru**.
4. Stáhněte instalační soubor sjednocené instalace Site Recovery.
5. Stáhněte registrační klíč trezoru. Budete ho potřebovat při spuštění sjednocené instalace. Klíč je platný pět dní od jeho vygenerování.

   ![Nastavení zdroje](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrace konfiguračního serveru v trezoru

Než začnete, udělejte toto: 

#### <a name="verify-time-accuracy"></a>Ověřit časovou přesnost
Na počítači konfiguračního serveru se ujistěte, že jsou systémové hodiny synchronizované s [časovým serverem](/windows-server/networking/windows-time-service/windows-time-service-top). Měla by odpovídat. Pokud je v popředí nebo na pozadí 15 minut, instalace může selhat.

#### <a name="verify-connectivity"></a>Ověřit připojení
Ujistěte se, že počítač má přístup k těmto adresám URL na základě vašeho prostředí: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Pravidla brány firewall založená na IP adrese by měla umožňovat komunikaci se všemi adresami URL Azure, které jsou uvedené výše přes port HTTPS (443). Pro zjednodušení a omezení rozsahu IP adres doporučujeme, abyste provedli filtrování adres URL.

- **Komerční IP adresy** – povolte [rozsahy IP adres datacentra Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653)a port HTTPS (443). Povolte rozsahy IP adres pro oblast Azure svého předplatného, aby se podporovaly adresy URL AAD, zálohy, replikace a úložiště.  
- **IP adresy státní správy** – povoluje [rozsahy IP adres datového centra Azure Government](https://www.microsoft.com/en-us/download/details.aspx?id=57063)a port HTTPS (443) pro všechny oblasti USGov () – Virginia, Texas, Arizona a Iowa) pro podporu AAD, zálohování, replikace a adres URL úložiště.  

#### <a name="run-setup"></a>Spuštění instalace
Chcete-li nainstalovat konfigurační server, spusťte sjednocené nastavení jako místní správce. Procesový Server a hlavní cílový server jsou také nainstalovány ve výchozím nastavení na konfiguračním serveru.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Po dokončení registrace se konfigurační server zobrazí na stránce **Nastavení**  >  **serverů** v trezoru.

### <a name="configure-target-settings-for-replication"></a>Konfigurace nastavení cíle pro replikaci

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **připravit**  >  **cíl**infrastruktury a vyberte předplatné Azure, které chcete použít.
2. Zadejte cílový model nasazení.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

   ![Cíl](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Pokud chcete vytvořit novou zásadu replikace, klikněte na **Site Recovery**  >  **Zásady replikace**infrastruktury  >  **a zásady replikace**.
2. V části **Vytvoření zásad replikace** zadejte název zásady.
3. V části **Prahová hodnota cíle bodu obnovení** (RPO) zadejte omezení cíle bodu obnovení. Tato hodnota určuje, jak často se budou vytvářet body obnovení dat. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
4. V části **Uchování bodu obnovení** zadejte (v hodinách), jak dlouhý je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu. Pro počítače replikované do úložiště úrovně Premium se podporuje uchování po dobu až 24 hodin. V případě úložiště úrovně Standard je to 72 hodin.
5. V nastavení **frekvence snímků konzistentní vzhledem k aplikacím**určete, jak často (v minutách) se mají vytvářet body obnovení obsahující snímky konzistentní vzhledem k aplikacím. Kliknutím na tlačítko **OK** vytvořte zásadu.

    ![Zásady replikace](./media/physical-azure-disaster-recovery/replication-policy.png)


Tato zásada se automaticky přidruží ke konfiguračnímu serveru. Ve výchozím nastavení se pro navrácení služeb po obnovení automaticky vytvoří zásada párování. Pokud je například zásada replikace zásadou pro **replikaci** , vytvoří se zásady navrácení služeb **po obnovení.** Tato zásada se nepoužije, dokud nespustíte navrácení služeb po obnovení z Azure.

### <a name="enable-replication"></a>Povolení replikace

- Site Recovery nainstaluje službu mobility, když je povolená replikace.
- Pokud povolíte replikaci pro server, může trvat 15 minut nebo déle, než se změny projeví a objeví se na portálu.

1. Klikněte na **replikovat**  >  **zdroj**aplikace.
2. V části **Zdroj** vyberte konfigurační server.
3. V **typ počítače**vyberte **fyzické počítače**.
4. Vyberte procesový Server (konfigurační server). Pak klikněte na **OK**.
5. V části **cíl**vyberte předplatné a skupinu prostředků, ve které chcete vytvořit virtuální počítače Azure po převzetí služeb při selhání. Vyberte model nasazení, který chcete použít v Azure (Classic nebo Správa prostředků).
6. Vyberte účet úložiště Azure, který chcete použít pro replikaci dat. 
7. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se po převzetí služeb při selhání vytvoří.
8. Vyberte **nakonfigurovat pro vybrané počítače**a použijte nastavení sítě pro všechny počítače, které vyberete pro ochranu. Vyberte **Konfigurovat později** a vyberte síť Azure na jeden počítač. 
9. Na **fyzických počítačích**klikněte na **+ fyzický počítač**. Zadejte název a IP adresu. Vyberte operační systém počítače, který chcete replikovat. Zjištění a vypsání serverů trvá několik minut. 

   > [!WARNING]
   > Musíte zadat IP adresu virtuálního počítače Azure, který máte v úmyslu přesunout.

10. V části **vlastnosti**  >  **Konfigurace vlastností**vyberte účet, který bude procesový Server používat k automatické instalaci služby mobility na počítači.
11. V **nastavení replikace**  >  **nakonfigurujte nastavení replikace**a ověřte, jestli je vybraná správná zásada replikace. 
12. Klikněte na **Povolit replikaci**. Průběh úlohy **Povolení ochrany** můžete sledovat v části **Nastavení**  >  **úlohy**  >  **Site Recovery úlohy**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený na převzetí služeb při selhání.


Chcete-li monitorovat servery, které jste přidali, můžete zjistit čas poslední zjištěné konfigurace v části **konfigurační servery**  >  **Poslední kontakt na**. Chcete-li přidat počítače bez čekání na naplánovaný čas zjišťování, zvýrazněte konfigurační server (neklikejte na něj) a klikněte na tlačítko **aktualizovat**.

## <a name="test-the-configuration"></a>Otestování konfigurace


1. Přejděte do trezoru, v **Nastavení**  >  **replikované položky**klikněte na virtuální počítač, který chcete přesunout do cílové oblasti, klikněte na ikonu **+ testovat převzetí služeb při selhání** .
2. V části **Testovací převzetí služeb při selhání** vyberte bod obnovení, který se má pro převzetí služeb při selhání použít:

   - **Nejnovější zpracovaný:** Vrátí virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Zobrazí se časové razítko. S touto možností se neztrácí žádný čas zpracováním dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
   - **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost vrátí všechny virtuální počítače k nejnovějšímu konzistentnímu bodu obnovení vzhledem k aplikacím. Zobrazí se časové razítko.
   - **Vlastní**: Vyberete si libovolný bod obnovení.

3. Vyberte cílovou virtuální síť Azure, do které chcete virtuální počítače Azure přesunout a otestovat konfiguraci. 

   > [!IMPORTANT]
   > Pro testovací převzetí služeb při selhání doporučujeme použít samostatnou síť virtuálních počítačů Azure, nikoli produkční síť, do které chcete virtuální počítače přesunout, když jste povolili replikaci.

4. Chcete-li zahájit testování přesunu, klikněte na tlačítko **OK**. Pokud chcete sledovat průběh, kliknutím na virtuální počítač otevřete jeho vlastnosti. Případně můžete kliknout na úlohu **Testovací převzetí služeb při selhání** v části název_trezoru > **Nastavení** > **Úlohy** > **Úlohy Site Recovery**.
5. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Ujistěte se, že je virtuální počítač spuštěný, má odpovídající velikost a je připojený k odpovídající síti.
6. Pokud chcete odstranit virtuální počítač vytvořený jako součást testování přesunu, klikněte na tlačítko **vyčistit testovací převzetí služeb při selhání** u replikované položky. V části **poznámky**si zaznamenejte a uložte všechny poznámky spojené s testem.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Proveďte přesun do cílové oblasti a potvrďte.

1. Přejděte do trezoru, v části **Nastavení**  >  **replikované položky**klikněte na virtuální počítač a pak klikněte na **převzetí služeb při selhání**.
2. V okně **Převzetí služeb při selhání** vyberte **Nejnovější**. 
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Služba Site Recovery se před aktivací převzetí služeb při selhání pokusí zdrojový virtuální počítač vypnout. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **úlohy** . 
4. Po dokončení úlohy ověřte, že se virtuální počítač zobrazuje v cílové oblasti Azure podle očekávání.
5. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a pak na **Potvrdit**. Tím se dokončí proces přesunu do cílové oblasti. Počkejte, až se úloha potvrzení dokončí.

## <a name="discard-the-resource-in-the-source-region"></a>Zahodí prostředek ve zdrojové oblasti. 

- Přejděte k virtuálnímu počítači.  Klikněte na **Zakázat replikaci**.  Tím se zastaví proces kopírování dat pro virtuální počítač.  

   > [!IMPORTANT]
   > Je důležité provést tento krok, abyste se vyhnuli tomu, že se bude účtovat replikace ASR.

V případě, že nemáte žádné plány k opakovanému použití některého ze zdrojových prostředků, pokračujte dalším postupem.

1. Pokračujte v odstraňování všech relevantních síťových prostředků ve zdrojové oblasti, které jste vyhlásili jako součást kroku 4 v části [Příprava zdrojových virtuálních počítačů](#prepare-the-source-vms) . 
2. Odstraňte odpovídající účet úložiště ve zdrojové oblasti.



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste přesunuli virtuální počítač Azure do jiné oblasti Azure. Nyní můžete pro přesunutý virtuální počítač nakonfigurovat zotavení po havárii.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii po migraci](azure-to-azure-quickstart.md)
