---
title: Přesun virtuálních počítačů Azure IaaS mezi Azure Government a veřejných oblastech pomocí služby Azure Site Recovery | Dokumentace Microsoftu
description: Přesun virtuálních počítačů Azure IaaS mezi Azure Government a veřejných oblastech pomocí Azure Site Recovery.
services: site-recovery
author: rajani-janaki-ram
ms.service: site-recovery
ms.topic: tutorial
ms.date: 04/16/2019
ms.author: rajanaki
ms.custom: MVC
ms.openlocfilehash: fe2620c7a07389b2a86d36420eadd2ef5883f5da
ms.sourcegitcommit: 61c8de2e95011c094af18fdf679d5efe5069197b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/23/2019
ms.locfileid: "62119623"
---
# <a name="move-azure-vms-between-azure-government-and-public-regions"></a>Přesuňte virtuální počítače Azure mezi Azure Government a veřejných oblastech 

Můžete chtít vaše virtuální počítače IaaS přesunout mezi Azure Government a veřejných oblastech, chcete-li zvýšit dostupnost vašich stávajících virtuálních počítačů, zlepšit možnosti správy nebo z důvodu zásad správného řízení, jak je uvedeno [tady](azure-to-azure-move-overview.md).

Kromě použití [Azure Site Recovery](site-recovery-overview.md) služby ke správě a orchestraci zotavení po havárii místních počítačů a virtuálních počítačů Azure pro účely provozní kontinuitu a zotavení po havárii (BCDR), můžete použít také lokality Obnovení pro správu přesune virtuální počítače Azure do sekundární oblasti.       

V tomto kurzu se dozvíte, jak přesouvat virtuální počítače Azure mezi Azure Government a veřejných oblastech pomocí Azure Site Recovery. Stejné je možné rozšířit na přesun virtuálních počítačů mezi párování oblastí, které nejsou ve stejné zeměpisné clusteru. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Ověření požadavků
> * Příprava zdrojové virtuální počítače
> * Připravte cílové oblasti
> * Kopírování dat do cílové oblasti
> * Otestujte konfiguraci
> * Provedení přesunu
> * Zahodit prostředky ve zdrojové oblasti

> [!IMPORTANT]
> V tomto kurzu se dozvíte, jak přesunout virtuální počítače Azure mezi Azure Government a veřejných oblastech nebo mezi páry oblastí, které nejsou podporovány řešení zotavení po havárii pravidelně pro virtuální počítače Azure. V případě jsou páry oblastí vaší zdrojové a cílové [podporované](https://docs.microsoft.com/azure/site-recovery/azure-to-azure-support-matrix#region-support), najdete to [dokumentu](azure-to-azure-tutorial-migrate.md) pro přesunutí. Pokud váš požadavek je kvůli zlepšení dostupnosti díky přesunu virtuálních počítačů ve skupině dostupnosti do zóny připnuté virtuálních počítačů v jiné oblasti, podívejte se na kurz [tady](move-azure-VMs-AVset-Azone.md).

> [!IMPORTANT]
> Není vhodné použít tuto metodu pro konfiguraci zotavení po Havárii mezi párování oblastí nepodporované, jak jsou definovány následující páry, dodržujte při tom latence dat, což je velmi důležité pro scénář zotavení po Havárii.

## <a name="verify-prerequisites"></a>Ověření požadavků

> [!NOTE]
> Ujistěte se, že rozumíte [komponent a architektury](physical-azure-architecture.md) pro tento scénář. Tato architektura se použije k přesunutí virtuálních počítačů Azure a **díky tomu virtuální počítače jako s fyzickými servery, že**.

- Zkontrolujte [požadavky na podporu](vmware-physical-secondary-support-matrix.md) pro všechny komponenty.
- Ujistěte se, že servery, které chcete replikovat v souladu s [požadavky na virtuální počítač Azure](vmware-physical-secondary-support-matrix.md#replicated-vm-support).
- Příprava účtu pro automatickou instalaci služby Mobility na každém serveru, který chcete replikovat.

- Všimněte si, že jakmile neúspěšných cílové oblasti v Azure, nemůžete provést přímo selhání zpátky do zdrojové oblasti. Budete muset nastavit replikaci zase zpátky do cíle.

### <a name="verify-azure-account-permissions"></a>Ověření oprávnění účtu Azure

Ujistěte se, že váš účet Azure má oprávnění pro replikaci virtuálních počítačů do Azure.

- Zkontrolujte [oprávnění](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines) je potřeba replikovat počítače do Azure.
- Ověřte a upravte [přístupu podle rolí](../role-based-access-control/role-assignments-portal.md) oprávnění. 

### <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Nastavení na cílový [síť Azure](../virtual-network/quick-create-portal.md).

- Virtuální počítače Azure jsou umístěny v této síti při jejich vytvoření po převzetí služeb při selhání.
- Síť musí být ve stejné oblasti jako trezor služby Recovery Services


### <a name="set-up-an-azure-storage-account"></a>Nastavení účtu úložiště Azure

Nastavení [účtu služby Azure storage](../storage/common/storage-quickstart-create-account.md).

- Site Recovery replikuje místní počítače do služby Azure storage. Virtuální počítače Azure se vytvoří z úložiště, když dojde k převzetí služeb při selhání.
- Účet úložiště musí být ve stejné oblasti jako trezor služby Recovery Services.


## <a name="prepare-the-source-vms"></a>Příprava zdrojové virtuální počítače

### <a name="prepare-an-account-for-mobility-service-installation"></a>Příprava účtu pro instalaci služby Mobility

Na každém serveru, který chcete replikovat, musí se nainstalovat službu Mobility. Site Recovery tuto službu nainstaluje automaticky při aktivaci replikace pro server. Automatická instalace musíte připravit účet, který Site Recovery použije pro přístup k serveru.

- Můžete použít doménový nebo místní účet
- Pro Windows virtuální počítače, pokud nepoužíváte doménový účet, zakažte vzdálené řízení přístupu uživatele na místním počítači. Chcete-li to provést, v registru pod **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, přidejte položku DWORD **LocalAccountTokenFilterPolicy**, s hodnotou 1.
- Pokud chcete přidat položku registru zakázat nastavení z rozhraní příkazového řádku, zadejte:       ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
- Pro Linux musí být použít účet root na zdrojovém serveru s Linuxem.


## <a name="prepare-the-target-region"></a>Připravte cílové oblasti

1. Ověřte, že vám předplatné Azure umožňuje vytvářet virtuální počítače v cílové oblasti používané ke zotavení po havárii. O povolení požadované kvóty požádejte podporu.

2. Zkontrolujte, jestli máte v rámci předplatného k dispozici dostatek prostředků pro podporu virtuálních počítačů s takovými velikostmi, které odpovídají velikostem zdrojových virtuálních počítačů. Pokud použijete Site Recovery pro kopírování dat do cíle, použije stejnou nebo nejbližší možnou velikost pro cílový virtuální počítač.

3. Ujistěte se, že vytvoříte cílový prostředek pro všechny komponenty jsou identifikované v rozložení síťové zdroje. To je důležité k zajištění toho, aby, účtovat společné cílové oblasti, vaše virtuální počítače mají všechny funkce a funkce, které jste měli ve zdroji.

    > [!NOTE]
    > Azure Site Recovery automaticky zjistí a vytvoří virtuální síť, když povolíte replikaci pro zdrojový virtuální počítač, nebo také můžete předem vytvořit síť a přiřadit k virtuálnímu počítači v toku uživatele pro povolení replikace. Ale pro všechny další prostředky, jak je uvedeno níže, je potřeba vytvořit ručně v cílové oblasti.

     Najdete následující dokumenty, které se nejčastěji používanou síťovým prostředkům relevantní pro vytvoření, v závislosti na konfiguraci zdrojového virtuálního počítače.

    - [Skupiny zabezpečení sítě](https://docs.microsoft.com/azure/virtual-network/manage-network-security-group)
    - [Nástroje pro vyrovnávání zatížení](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    - [Veřejná IP adresa](https://docs.microsoft.com/azure/load-balancer/#step-by-step-tutorials)
    
    Ostatními síťovými součástmi, najdete v části sítě [dokumentaci.](https://docs.microsoft.com/azure/#pivot=products&panel=network) 

4. Ručně [vytvořit síť neprodukčním](https://docs.microsoft.com/azure/virtual-network/quick-create-portal) v cílové oblasti, pokud chcete otestovat konfiguraci, před provedením konečné vyjmutí v cílové oblasti. To vytvoří minimálním narušením s produkcí a doporučuje se.

## <a name="copy-data-to-the-target-region"></a>Kopírování dat do cílové oblasti
Níže uvedený postup se dozvíte, jak pomocí Azure Site Recovery ke zkopírování dat do cílové oblasti.

### <a name="create-the-vault-in-any-region-except-the-source-region"></a>V libovolné oblasti (s výjimkou zdrojové oblasti) vytvořte trezor.

1. Přihlaste se k webu [Azure Portal](https://portal.azure.com) > **Recovery Services**.
2. Klikněte na tlačítko **vytvořit prostředek** > **nástroje pro správu** > **Backup a Site Recovery**.
3. Do pole **Název** zadejte popisný název **ContosoVMVault**. Pokud máte více než jeden. předplatné, vyberte příslušné předplatné.
4. Vytvořte skupinu prostředků **ContosoRG**.
5. Zadejte oblast Azure. Informace o tom, které oblasti jsou podporované, najdete v části s geografickou dostupností v tématu s [podrobnostmi o cenách Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. V trezorech služby Recovery Services, klikněte na tlačítko **přehled** > **ConsotoVMVault** > **+ replikovat**
7. Vyberte **do Azure** > **nevirtualizované/jiné**.

### <a name="set-up-the-configuration-server-to-discover-vms"></a>Nastavení konfigurace serveru ke zjištění virtuálních počítačů.


Nastavit konfigurační server zaregistrujete v trezoru a zjištění virtuálních počítačů.

1. Klikněte na tlačítko **Site Recovery** > **připravit infrastrukturu** > **zdroj**.
2. Pokud nemáte k dispozici konfigurační server, klikněte na tlačítko **+ konfigurační server**.
3. V **přidat Server**, zkontrolujte, že **konfigurační Server** se zobrazí v **typ serveru**.
4. Stáhněte si instalační soubor sjednocené instalace Site Recovery.
5. Stáhněte registrační klíč trezoru. Budete potřebovat při spuštění sjednocené instalace. Klíč je platný pět dní od jeho vygenerování.

   ![Nastavení zdroje](./media/physical-azure-disaster-recovery/source-environment.png)


### <a name="register-the-configuration-server-in-the-vault"></a>Registrace konfiguračního serveru v trezoru

Než začnete, postupujte následovně: 

#### <a name="verify-time-accuracy"></a>Zkontrolujte časové přesnosti
Na počítači serveru konfigurace, ujistěte se, že jsou systémové hodiny synchronizované s [časovým serverem](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). By měl odpovídat. Instalace může selhat, pokud je 15 minut v popředí nebo pozadí.

#### <a name="verify-connectivity"></a>Ověření připojení
Ujistěte se, že tento počítač přístup k těmto adresám URL podle vašeho prostředí: 

[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]  

Pravidla brány firewall založená na adresu IP musí umožňovat komunikaci pro všechny adresy URL Azure, které jsou uvedené výše přes port HTTPS (443). Pro zjednodušení a omezit rozsahy IP adres, se doporučuje, že filtrování adres URL udělat.

- **Komerční IP adresy** -povolit [Azure rozsahů IP adres Datacentra](https://www.microsoft.com/download/confirmation.aspx?id=41653)a port HTTPS (443). Povolte rozsahy IP adres pro oblast Azure svého předplatného na podporu AAD, zálohování, replikace a adresy URL úložiště.  
- **Státní správy IP adres** -povolit [Azure Government rozsahů IP adres Datacentra](https://www.microsoft.com/en-us/download/details.aspx?id=57063)a port HTTPS (443) pro všechny oblasti USGov (Virginie, Texas, Arizona a Iowa) pro podporu AAD, zálohování, replikace a adresy URL úložiště.  

#### <a name="run-setup"></a>Spuštění instalace
Spuštění sjednocené instalace jako místní správce, chcete-li nainstalovat konfigurační server. Procesový server a hlavní cílový server jsou také nainstalované ve výchozím nastavení na konfiguračním serveru.

[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

Po dokončení registrace konfiguračního serveru se zobrazí na **nastavení** > **servery** stránky v trezoru.

### <a name="configure-target-settings-for-replication"></a>Nakonfigurujte nastavení cíle replikace

Vyberte a zkontrolujte cílové prostředky.

1. Klikněte na **Připravit infrastrukturu** > **Cíl** a vyberte předplatné Azure, které chcete použít.
2. Zadejte model nasazení cíl.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure.

   ![Cíl](./media/physical-azure-disaster-recovery/network-storage.png)


### <a name="create-a-replication-policy"></a>Vytvoření zásady replikace

1. Novou zásadu replikace vytvoříte tak, že kliknete na **Infrastruktura Site Recovery** > **Zásady replikace** > **+ Zásada replikace**.
2. V části **Vytvoření zásad replikace** zadejte název zásady.
3. V části **Prahová hodnota cíle bodu obnovení** (RPO) zadejte omezení cíle bodu obnovení. Tato hodnota určuje, jak často jsou vytvořeny body obnovení pro data. Když průběžná replikace překročí tento limit, vygeneruje se upozornění.
4. V části **Uchování bodu obnovení** zadejte (v hodinách), jak dlouhý je interval uchovávání dat pro jednotlivé body obnovení. Replikované virtuální počítače můžete v rámci okna uchování obnovit do libovolného časového bodu. Pro počítače replikované do úložiště úrovně Premium se podporuje uchování po dobu až 24 hodin. V případě úložiště úrovně Standard je to 72 hodin.
5. V **frekvence snímků konzistentní vzhledem k**, zadat jak často (v minutách) se vytvoří body obnovení obsahující snímky konzistentní s aplikací. Kliknutím na tlačítko **OK** vytvořte zásadu.

    ![Zásady replikace](./media/physical-azure-disaster-recovery/replication-policy.png)


Tato zásada se automaticky přidruží ke konfiguračnímu serveru. Ve výchozím nastavení se pro navrácení služeb po obnovení automaticky vytvoří zásada párování. Například, pokud má zásada replikace **rep-policy** pak zásady navrácení služeb po obnovení **rep-policy-failback** se vytvoří. Tato zásada se nepoužije, dokud nespustíte navrácení služeb po obnovení z Azure.

### <a name="enable-replication"></a>Povolení replikace

- Site Recovery nainstaluje službu Mobility po povolení replikace.
- Když povolíte replikaci pro server, může trvat 15 minut nebo i delší dobu se změny projeví a objeví na portálu.

1. Klikněte na **Replikovat aplikaci** > **Zdroj**.
2. V části **Zdroj** vyberte konfigurační server.
3. V **typ počítače**vyberte **fyzické počítače**.
4. Vyberte procesový server (konfigurační server). Pak klikněte na **OK**.
5. V **cílové**, vyberte předplatné a skupinu prostředků, ve kterém chcete vytvořit virtuální počítače Azure po převzetí služeb při selhání. Vyberte model nasazení, který chcete použít v Azure (classic nebo resource Manager).
6. Vyberte účet úložiště Azure, který chcete použít pro replikaci dat. 
7. Vyberte síť Azure a podsíť, ke kterým se připojí virtuální počítače Azure, když se po převzetí služeb při selhání vytvoří.
8. Výběrem možnosti **Nakonfigurovat pro vybrané počítače** použijte nastavení sítě pro všechny počítače, které jste vybrali pro ochranu. Vyberte **Nakonfigurovat později** a vyberte síť Azure pro konkrétní počítač. 
9. V **fyzické počítače**a klikněte na tlačítko **+ fyzický počítač**. Zadejte název a IP adresu. Vyberte operační systém počítače, který chcete replikovat. Trvá několik minut, než se servery, které zjišťují a uvádějí. 

   > [!WARNING]
   > Musíte zadat IP adresu virtuálního počítače Azure, máte v úmyslu přesunout

10. V **vlastnosti** > **konfigurovat vlastnosti**, vyberte účet, který použije procesový server při automatické instalaci služby Mobility na počítači.
11. V části **Nastavení replikace** > **Konfigurace nastavení replikace** zkontrolujte, jestli je vybraná správná zásada replikace. 
12. Klikněte na **Povolit replikaci**. Průběh úlohy **Povolení ochrany** můžete sledovat tady: **Nastavení** > **Úlohy** > **Úlohy Site Recovery**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.


K monitorování serverů, které přidáte, můžete zkontrolovat čas posledního zjištění u nich **konfigurační servery** > **poslední kontakt**. Chcete-li přidat počítače bez čekání naplánovaný čas zjišťování, zvýrazněte konfigurační server (neklikejte na něj) a klikněte na tlačítko **aktualizovat**.

## <a name="test-the-configuration"></a>Otestujte konfiguraci


1. Přejděte do trezoru, v **nastavení** > **replikované položky**, klikněte na virtuální počítač, který máte v úmyslu přesunout do cílové oblasti, klikněte na tlačítko **+ testovací převzetí služeb při selhání** ikona.
2. V části **Testovací převzetí služeb při selhání** vyberte bod obnovení, který se má pro převzetí služeb při selhání použít:

   - **Nejnovější zpracovaný**: Převezme virtuálního počítače k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Časové razítko je vidět. S touto možností se neztrácí žádný čas zpracováním dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
   - **Nejnovější konzistentní vzhledem k**: Tato možnost převezme služby při selhání všech virtuálních počítačů k nejnovějšímu bodu obnovení s konzistentní aplikací. Časové razítko je vidět.
   - **Vlastní**: Zvolte jakýkoli bod obnovení.

3. Vyberte cíl Azure virtuální sítě, ke které chcete přesunout virtuální počítače Azure k testování této konfigurace. 

   > [!IMPORTANT]
   > Doporučujeme použít samostatnou síť virtuálních počítačů Azure pro testovací převzetí služeb a ne produkční sítě, do kterého chcete přesunout virtuální počítače časem, které jste nastavili při povolování replikace.

4. Pro začátek testování přesunutí, klikněte na tlačítko **OK**. Pokud chcete sledovat průběh, kliknutím na virtuální počítač otevřete jeho vlastnosti. Případně můžete kliknout na úlohu **Testovací převzetí služeb při selhání** v části název_trezoru > **Nastavení** > **Úlohy** > **Úlohy Site Recovery**.
5. Po dokončení převzetí služeb při selhání se replika virtuálního počítače Azure zobrazí na webu Azure Portal v části **Virtuální počítače**. Ujistěte se, že je virtuální počítač spuštěný, má odpovídající velikost a je připojený k odpovídající síti.
6. Pokud chcete odstranit virtuální počítač vytvořen jako součást přesunu testování, klikněte na tlačítko **vyčištění testovacího převzetí služeb při selhání** na replikovanou položku. V **poznámky**si zaznamenejte a uložte jakékoli připomínky související s testem.

## <a name="perform-the-move-to-the-target-region-and-confirm"></a>Provedení přesunu do cílové oblasti a potvrďte.

1. Přejděte do trezoru, v **nastavení** > **replikované položky**, klikněte na virtuální počítač a potom klikněte na tlačítko **převzetí služeb při selhání**.
2. V okně **Převzetí služeb při selhání** vyberte **Nejnovější**. 
3. Vyberte **Před spuštěním převzetí služeb při selhání vypnout počítač**. Služba Site Recovery se před aktivací převzetí služeb při selhání pokusí zdrojový virtuální počítač vypnout. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**. 
4. Po dokončení úlohy zkontrolujte, že virtuální počítač zobrazuje v cílové oblasti Azure podle očekávání.
5. V části **Replikované položky** klikněte pravým tlačítkem na virtuální počítač a pak na **Potvrdit**. Tím se dokončí proces přesunu na cílové oblasti. Počkejte, až se dokončí úloha potvrzení.

## <a name="discard-the-resource-in-the-source-region"></a>Zahodit prostředků ve zdrojové oblasti 

- Přejděte k virtuálnímu počítači.  Klikněte na **zakázat replikaci**.  Tím se zastaví proces kopírování dat pro virtuální počítač.  

   > [!IMPORTANT]
   > Je důležité provést tento krok a vyhněte se účtovat poplatky pro replikaci Azure Site Recovery.

V případě, že nemáte v úmyslu použít některý z prostředků zdroj prosím pokračujte další sadů kroků.

1. Přejít k odstranění všech příslušných síťových prostředků ve zdrojové oblasti, která vypsali jako součást kroku 4 v [Příprava zdrojové virtuální počítače](#prepare-the-source-vms) 
2. K odstranění odpovídajícího účtu úložiště ve zdrojové oblasti.



## <a name="next-steps"></a>Další postup

V tomto kurzu přesunout virtuální počítač Azure do jiné oblasti Azure. Teď můžete nakonfigurovat zotavení po havárii pro přesunutý virtuální počítač.

> [!div class="nextstepaction"]
> [Nastavení zotavení po havárii po migraci](azure-to-azure-quickstart.md)
