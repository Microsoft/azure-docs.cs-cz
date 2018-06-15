---
title: Migrace virtuálních počítačů z AWS do Azure pomocí Azure Site Recovery | Microsoft Docs
description: Tento článek popisuje, jak migrovat virtuální počítače běžící ve službě Amazon Web Services (AWS) pomocí služby Azure Site Recovery do Azure.
services: site-recovery
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: tutorial
ms.date: 02/07/2018
ms.author: raynew
ms.custom: MVC
ms.openlocfilehash: 2c351313d1d75bb7fd13a34feb3a1c61188894a8
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/27/2018
ms.locfileid: "29677613"
---
# <a name="migrate-amazon-web-services-aws-vms-to-azure"></a>Migrace virtuálních počítačů služby Amazon Web Services (AWS) do Azure

V tomto kurzu se naučíte migrovat virtuální počítače služby Amazon Web Services (AWS) do virtuálních počítačů Azure pomocí služby Site Recovery. Při migraci instancí EC2 do Azure se virtuální počítače zpracovávají, jako by šlo o fyzické místní počítače. V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Příprava prostředků Azure
> * Příprava instancí EC2 služby AWS na migraci
> * Nasazení konfiguračního serveru
> * Povolení replikace pro virtuální počítače
> * Otestování převzetí služeb při selhání, aby bylo jisté, že všechno funguje.
> * Spuštění jednorázového převzetí služeb při selhání do Azure

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/pricing/free-trial/) před tím, než začnete.


## <a name="prepare-azure-resources"></a>Příprava prostředků Azure

V Azure je potřeba mít připravených několik prostředků, které budou migrované instance EC2 potřebovat. Patří mezi ně účet úložiště, trezor a virtuální síť.

### <a name="create-a-storage-account"></a>vytvořit účet úložiště

Bitové kopie replikovaných počítačů jsou uložené v úložišti Azure. Virtuální počítače Azure se vytvoří z úložiště, když převezmete služby při selhání z místního úložiště do Azure.

1. V nabídce webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek** > **Úložiště** > **Účet úložiště**.
2. Zadejte název účtu úložiště. V těchto kurzech používáme název **awsmigrated2017**. Název musí být v rámci Azure jedinečný, musí být dlouhý 3 až 24 znaků a obsahovat pouze číslice a malá písmena.
3. V polích **Model nasazení**, **Druh účtu**, **Výkon** a **Vyžadovat zabezpečený přenos** nechejte nastavené výchozí možnosti.
5. V poli **Replikace** vyberte výchozí možnost **RA-GRS** (geograficky redundantní úložiště jen pro čtení).
6. Vyberte předplatné, které chcete v tomto kurzu použít.
7. V poli **Skupina prostředků** vyberte **Vytvořit novou**. V tomto příkladu použijeme jako název **migrationRG**.
8. Jako umístění vyberte **Západní Evropa**.
9. Vytvořte účet úložiště kliknutím na **Vytvořit**.

### <a name="create-a-vault"></a>Vytvoření trezoru

1. Na webu [Azure Portal](https://portal.azure.com) v levém navigačním panelu klikněte na **Všechny služby** a vyhledejte a vyberte **Trezory služby Recovery Services**.
2. Na stránce Trezory Recovery Services klikněte v horním levém rohu stránky na **+ Přidat**.
3. Do pole **Název** zadejte *myVault*.
4. Vyberte příslušné **předplatné**.
4. V části **Skupina prostředků** vyberte **Použít existující** a vyberte *migrationRG*.
5. Jako **umístění** vyberte *Západní Evropa*.
5. Pokud chcete mít k novému trezoru rychlý přístup z řídicího panelu, vyberte možnost **Připnout na řídicí panel**.
7. Až budete hotoví, klikněte na **Vytvořit**.

Nový trezor se zobrazí v části **Řídicí panel** > **Všechny prostředky** a na hlavní stránce **Trezory Recovery Services**.

### <a name="set-up-an-azure-network"></a>Nastavení sítě Azure

Když se po migraci vytvoří virtuální počítače Azure (převzetí služeb při selhání), připojí se do této sítě.

1. Na webu [Azure Portal](https://portal.azure.com) klikněte na **Vytvořit prostředek** > **Sítě** >
   **Virtuální síť**.
3. Do pole **Název** zadejte *myMigrationNetwork*.
4. V části **Adresní prostor** nechte výchozí hodnotu.
5. Vyberte příslušné **předplatné**.
6. V části **Skupina prostředků** vyberte **Použít existující** a z rozevíracího seznamu vyberte *migrationRG*.
7. Jako **umístění** vyberte **Západní Evropa**.
8. U položky **Podsíť** ponechte výchozí hodnoty jak pro **Název**, tak i pro **Rozsah IP adres**.
9. **Koncové body služeb** nechte vypnuté.
10. Až budete hotoví, klikněte na **Vytvořit**.


## <a name="prepare-the-ec2-instances"></a>Příprava instancí EC2

Budete potřebovat jeden nebo dva virtuální počítače, které chcete migrovat. Tyto instance EC2 by měly běžet na 64bitové verzi Windows Serveru 2008 R2 SP1 nebo novější, Windows Serveru 2012, Windows Serveru 2012 R2, Windows Serveru 2016 nebo Red Hat Enterprise Linuxu 6.7 (pouze virtualizované instance HVM). Server musí mít jedině ovladače Citrix PV nebo AWS PV. Instance s ovladači RedHat PV nejsou podporované.

Na každý virtuální počítač, který chcete replikovat, bude nutné nainstalovat službu Mobility. Služba Site Recovery tuto službu nainstaluje automaticky, když u virtuálního počítače zapnete replikaci. U automatické instalace musíte připravit účet na instancích EC2, který služba Site Recovery použije k získání přístupu k virtuálnímu počítači.

Můžete použít účet domény nebo místní účet. U virtuálních počítačů s Linuxem by měl být na zdrojovém serveru s Linuxem účet superuživatel. Pokud u virtuálních počítačů s Windows nepoužíváte doménový účet, zakažte na místním počítači vzdálené řízení přístupu uživatele:

  - V registru přidejte v části **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System** položku DWORD **LocalAccountTokenFilterPolicy** a hodnotu nastavte na 1.

Musíte také oddělit instanci EC2, kterou můžete používat jako konfigurační server služby Site Recovery. Tato instance musí běžet na Windows Serveru 2012 R2.


## <a name="prepare-the-infrastructure"></a>Příprava infrastruktury

Na stránce trezoru v Azure vyberte z části **Začínáme** možnost **Site Recovery** a potom klikněte na **Příprava infrastruktury**.

### <a name="1-protection-goal"></a>1 Cíl ochrany

Na stránce **Cíl ochrany** vyberete následující hodnoty:

|    |  |
|---------|-----------|
| Kde se vaše počítače nachází? | **Místní**|
| Kam chcete své počítače replikovat? |**Do Azure**|
| Jsou vaše počítače virtualizované? | **Nevirtualizované/jiné**|

Jakmile to budete mít, přejděte kliknutím na **OK** na další krok.

### <a name="2-source-prepare"></a>2 Příprava zdroje

Na stránce **Připravit zdroj** klikněte na **+ Konfigurační server**.

1. Použijte instanci EC2 běžící na Windows Serveru 2012 R2, abyste vytvořili konfiguračních server a zaregistrovali ho u svého trezoru služby Site Recovery.

2. Na virtuálním počítači instance EC2, který používáte jako konfigurační server, nakonfigurujte proxy, aby měl přístup k [adresám URL služby](site-recovery-support-matrix-to-azure.md).

3. Stáhněte si [sjednocenou instalaci Microsoft Azure Site Recovery](http://aka.ms/unifiedinstaller_wus). Můžete si ji stáhnout do svého místního počítače a potom ji zkopírovat do virtuálního počítače, který používáte jako konfigurační server.

4. Klikněte na tlačítko **Stáhnout** a stáhněte si registrační klíč trezoru. Zkopírujte stažený soubor do virtuálního počítače, který používáte jako konfiguračních server.

5. Na tomto virtuálním počítači klikněte pravým tlačítkem na stažený instalační soubor **sjednocené instalace Microsoft Azure Site Recovery** a vyberte **Spustit jako správce**.

    1. Na stránce **Než začnete** vyberte **Nainstalovat konfigurační server a procesový server** a potom klikněte na **Další**.
    2. Na stránce **Licence k softwaru jiného výrobce** vyberte **Přijímám licenční smlouvu třetí strany.** a pak klikněte na **Další**.
    3. Na stránce **Registrace** klikněte na procházet, přejděte do umístění se souborem registračního klíče trezoru a potom klikněte na **Další**.
    4. Na stránce **Nastavení internetu** vyberte **Připojit k Azure Site Recovery bez proxy serveru.** a pak klikněte na **Další**.
    5. Na stránce **Kontrola předpokladů** provede program kontrolu několika položek. Po skončení klikněte na **Další**.
    6. Na stránce **Konfigurace MySQL** poskytněte požadovaná hesla a potom klikněte na **Další**.
    7. Na stránce **Podrobnosti o prostředí** vyberte **Ne**, počítače VMware není potřeba chránit. Potom klikněte na **Další**.
    8. Na stránce **Umístění instalace** přijměte výchozí umístění kliknutím na **Další**.
    9. Na stránce **Výběr sítě** přijměte výchozí umístění kliknutím na **Další**.
    10. Na stránce **Souhrn** klikněte na **Instalovat**.
    11. **Průběh instalace** vám ukáže informace o aktuálním postupu. Po skončení klikněte na **Dokončit**. Automaticky se otevře okno informující o potřebě restartovat počítač, klikněte na **OK**. Také se automaticky otevře okno s přístupovým heslem ke konfiguračnímu serveru – heslo si zkopírujte a bezpečně ho uložte.

6. Na virtuálním počítači spusťte **cspsconfigtool.exe**, pomocí kterého vytvoříte na konfiguračním serveru jeden nebo více účtů pro správu. Ujistěte se, že účty pro správu mají oprávnění správce na instancích EC2, které chcete migrovat.

Po dokončení nastavení konfiguračního serveru přejděte zpátky na portál Azure Portal, vyberte server, který jste právě vytvořili jako **Konfigurační server**, a klikněte na *OK**, abyste přešli na třetí krok.

### <a name="3-target-prepare"></a>3 Příprava cíle

V této části zadáte informace o prostředcích, které jste vytvořili dříve v tomto kurzu v části [Příprava prostředků Azure](#prepare-azure-resources).

1. Na stránce **Předplatné** vyberte požadované předplatné Azure, které jste použili v kurzu [Příprava Azure](tutorial-prepare-azure.md).
2. Jako model nasazení vyberte **Resource Manager**.
3. Site Recovery zkontroluje, že máte minimálně jednu kompatibilní síť a účet úložiště Azure. Měli by to být prostředky, které jste vytvořili dříve v tomto kurzu v části [Příprava prostředků Azure](#prepare-azure-resources).
4. Až to budete mít, klikněte na **OK**.


### <a name="4-replication-settings-prepare"></a>4 Příprava nastavení replikace

Před povolením replikace musíte vytvořit zásady replikace.

1. Klikněte na **+ Replikovat a přidružit**.
2. Do pole **Název** zadejte **myReplicationPolicy**.
3. Zbývající pole ponechte ve výchozím nastavení a kliknutím na **OK** vytvořte zásady. Nová zásada se automaticky přidruží ke konfiguračnímu serveru.

### <a name="5-deployment-planning-select"></a>5 Výběr plánování nasazení

Na stránce **Dokončili jste plánování nasazení?** vyberte z rozevíracího seznamu **Udělám to později** a potom klikněte na **OK**.

Jakmile dokončíte všech pět kroků části **Příprava infrastruktury**, klikněte na **OK**.


## <a name="enable-replication"></a>Povolení replikace

Povolte replikaci pro všechny virtuální počítače, které chcete migrovat. Když je replikace povolená, služba Site Recovery automaticky nainstaluje službu Mobility.

1. Otevřete web [Azure Portal](htts://portal.azure.com).
1. Na stránce svého trezoru v části **Začínáme** klikněte na **Site Recovery**.
2. V části **Pro místní počítače a virtuální počítače Azure** klikněte na **Krok 1: Replikace aplikace**. Pomocí následujících informací dokončete stránky průvodce a na každé stránce klikněte po dokončení na **OK**:
    - 1 Konfigurace zdroje:

    |  |  |
    |-----|-----|
    | Zdroj: | **Místní**|
    | Zdrojové umístění:| Název instance EC2 konfiguračního serveru|
    |Typ počítače: | **Fyzický počítač**|
    | Procesový server: | Z rozevíracího seznamu vyberte konfigurační server.|

    - 2 Konfigurace cíle

    |  |  |
    |-----|-----|
    | Cíl: | Nechte zadanou výchozí hodnotu.|
    | Předplatné: | Zvolte předplatné, které jste dosud používali.|
    | Skupina prostředků po převzetí služeb při selhání:| Použijte skupinu prostředků, kterou jste vytvořili v části [Příprava prostředků Azure](#prepare-azure-resources).|
    | Model nasazení po převzetí služeb při selhání: | Zvolte **Správce prostředků**.|
    | Účet úložiště: | Zvolte účet úložiště, který jste vytvořili v části [Příprava prostředků Azure](#prepare-azure-resources).|
    | Síť Azure: | Zvolte **Nakonfigurovat pro vybrané počítače**.|
    | Síť Azure po převzetí služeb při selhání: | Zvolte síť, kterou jste vytvořili v části [Příprava prostředků Azure](#prepare-azure-resources).|
    | Podsíť: | Z rozevíracího seznamu vyberte **výchozí**.|

    - 3 Výběr fyzického počítače

        Klikněte na **+ Fyzický počítač**, potom zadejte **Název**, **IP adresu** a **Typ operačního systému** instanci EC2, kterou chcete migrovat, a nakonec klikněte na **OK**.

    - 4 Konfigurace vlastností

        Z rozevíracího seznamu vyberte účet, který jste vytvořili na konfiguračním serveru a klikněte na **OK**.

    - 5 Konfigurace nastavení replikace

        Ujistěte se, že zásady replikace vybrané v rozevíracím seznamu jsou **myReplicationPolicy** a potom klikněte na **OK**.

3. Po dokončení průvodce klikněte na **Povolit replikaci**.


Průběh úlohy **Povolení ochrany** můžete sledovat tady: **Sledování a sestavy** > **Úlohy** > **Úlohy Site Recovery**. Po spuštění úlohy **Dokončit ochranu** je počítač připravený k převzetí služeb při selhání.        

Když virtuálnímu počítači povolíte replikaci, může trvat 15 minut nebo déle, než se změny projeví a objeví se na portálu.

## <a name="run-a-test-failover"></a>Spuštění testovacího převzetí služeb při selhání

Když spustíte testovací převzetí služeb při selhání, stane se následující:

1. Spustí se kontrola předpokladů, která zjistí, zda jsou splněné všechny podmínky pro převzetí služeb při selhání.
2. Převzetí služeb při selhání tato data zpracuje, aby se mohl vytvořit virtuální počítač Azure. Pokud jste vybrali nejnovější bod obnovení, vytvoří se z těchto dat nový.
3. Pomocí dat zpracovaných v předchozím kroku se vytvoří virtuální počítač Azure.

Test převzetí služeb při selhání spustíte na portálu následovně:

1. Na stránce vašeho trezoru přejděte na **Chráněné položky** > **Replikované položky** > klikněte na virtuální počítač > **+ Testovací převzetí služeb při selhání**.

2. Vyberte bod obnovení, který chcete pro převzetí služeb při selhání použít:
    - **Nejnovější zpracovaný**: Vrátí virtuální počítač k nejnovějšímu bodu obnovení zpracovanému službou Site Recovery. Časové razítko je vidět. Tato možnost neztrácí žádný čas zpracováním dat, takže poskytuje nízkou plánovanou dobu obnovení (RTO).
    - **Nejnovější konzistentní vzhledem k aplikacím**: Tato možnost vrátí všechny virtuální počítače k nejnovějšímu konzistentnímu bodu obnovení vzhledem k aplikacím. Časové razítko je vidět.
    - **Vlastní**: Vyberete si libovolný bod obnovení.
3. V části **Testovací převzetí služeb při selhání** vyberte cílovou síť Azure, ke které se virtuální počítače Azure po převzetí služeb při selhání připojí. Měla by to být síť, kterou jste vytvořili v části [Příprava prostředků Azure](#prepare-azure-resources).
4. Kliknutím na **OK** zahajte převzetí služeb při selhání. Průběh můžete sledovat kliknutím na virtuální počítač, které otevře jeho vlastnosti. Nebo můžete na stránce svého trezoru v části **Sledování a sestavy** > **Úlohy** >
   **Úlohy Site Recovery** kliknout na úlohu **Testovací převzetí služeb při selhání**.
5. Po dokončení převzetí služeb při selhání se na portálu Azure Portal v části **Virtuální počítače** objeví replika virtuálního počítače Azure. Zkontrolujte, že má virtuální počítač odpovídající velikost, je připojený ke správné síti a běží.
6. Nyní byste se měli moct k replikovanému virtuálnímu počítači v Azure připojit.
7. Virtuální počítače Azure vytvořené během testu převzetí služeb při selhání odstraníte kliknutím na **Vyčistit testovací převzetí služeb při selhání** v plánu obnovení. V části **Poznámky** si zaznamenejte a uložte jakékoli připomínky související s testovacím převzetím služeb při selhání.

V některých scénářích vyžaduje převzetí služeb při selhání další zpracování, které trvá asi osm až deset minut.


## <a name="migrate-to-azure"></a>Migrace do Azure

Spusťte skutečné převzetí služeb při selhání pro instance EC2 a proveďte jejich migraci na virtuální počítače Azure.

1. V části **Chráněné položky** > **Replikované položky** klikněte na instance AWS > **Převzetí služeb při selhání**.
2. V části **Převzetí služeb při selhání** vyberte **Bod obnovení**, ke kterému se mají převzít služby při selhání. Vyberte nejnovější bod obnovení.
3. Pokud chcete, aby se služba Site Recovery pokusila před aktivací převzetí služeb při selhání vypnout zdrojové virtuální počítače, vyberte možnost **Před spuštěním převzetí služeb při selhání vypnout počítač**. Převzetí služeb při selhání bude pokračovat i v případě, že se vypnutí nepovede. Průběh převzetí služeb při selhání můžete sledovat na stránce **Úlohy**.
4. Zkontrolujte, zda se virtuální počítač zobrazuje na stránce **Replikované položky**.
5. Klikněte pravým tlačítkem na každý virtuální počítač > **Dokončit migraci**. Tím se dokončí proce migrace, zastaví se replikace virtuálního počítače AWS a zastaví se fakturace služby Site Recovery pro daný virtuální počítač.

    ![Dokončení migrace](./media/tutorial-migrate-aws-to-azure/complete-migration.png)

> [!WARNING]
> **Nepřerušujte v průběhu proces převzetí služeb při selhání**: před zahájením procesu převzetí služeb při selhání se zastaví replikace virtuálního počítače. Pokud proces převzetí služeb při selhání v průběhu přerušíte, tak se sice zastaví, ale virtuální počítač se znovu nereplikuje.  




## <a name="next-steps"></a>Další kroky

V tomto tématu jste se naučili, jak migrovat instance ES2 AWS do virtuálních počítačů Azure. Další informace o virtuálních počítačích Azure najdete v kurzech pro virtuální počítače s Windows.

> [!div class="nextstepaction"]
> [Kurzy pro virtuální počítače Azure s Windows](../virtual-machines/windows/tutorial-manage-vm.md)
