---
title: Offline zálohování pomocí Azure Data Box
description: Naučte se, jak můžete použít Azure Data Box k osazení velkých počátečních zálohovaných dat offline od agenta MARS do trezoru Recovery Services.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: e789b6c9f4ff2e8cd168e6b5c138d423911d4743
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752579"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Azure Backup offline zálohování pomocí Azure Data Box

[Azure Data box](../databox/data-box-overview.md) můžete použít k osazení velkých počátečních záloh Microsoft Azure Recovery Services (MARS) offline (bez použití sítě) do trezoru Recovery Services. Tento proces šetří čas a šířku pásma sítě, které by jinak využily přesun velkých objemů zálohovaných dat online přes síť s vysokou latencí. Toto vylepšení je momentálně ve verzi Preview. Offline zálohování na základě Azure Data Box poskytuje dvě různé výhody oproti [zálohování offline založené na službě Azure import/export](./backup-azure-backup-import-export.md):

- Nemusíte si vystarat vlastní disky a konektory, které jsou kompatibilní s Azure. Azure Data Box dodávající disky přidružené k vybrané [SKU data box](https://azure.microsoft.com/services/databox/data/).
- Azure Backup (agent MARS) může přímo zapisovat data zálohy na podporované SKU Azure Data Box. Tato funkce eliminuje nutnost zřídit pracovní umístění pro počáteční data záloh. Nepotřebujete také nástroje pro formátování a kopírování těchto dat na disky.

## <a name="azure-data-box-with-the-mars-agent"></a>Azure Data Box s agentem MARS

V tomto článku se dozvíte, jak můžete použít Azure Data Box k počátečnímu navýšení velkých počátečních dat zálohování z agenta MARS do trezoru Recovery Services.

## <a name="supported-platforms"></a>Podporované platformy

Proces pro osazení dat z agenta MARS pomocí Azure Data Box je podporován v následujících SKU systému Windows.

| **OS**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Pracovní stanice**                        |                                                              |
| Windows 10, 64bitová verze                     | Enterprise, Pro, Home                                       |
| Windows 8.1, 64bitová verze                    | Enterprise, Pro                                             |
| Windows 8, 64bitová verze                      | Enterprise, Pro                                             |
| Windows 7, 64bitová verze                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Server**                             |                                                              |
| Windows Server 2019, 64bitová verze            | Standard, Datacenter, Essentials                            |
| Windows Server 2016, 64bitová verze            | Standard, Datacenter, Essentials                            |
| Windows Server 2012 R2, 64bitová verze         | Standard, Datacenter, Foundation                            |
| Windows Server 2012, 64bitová verze            | Datacenter, Foundation, Standard                            |
| Windows Storage Server 2016, 64bitová verze    | Standard, Workgroup                                         |
| Windows Storage Server 2012 R2, 64bitová verze | Standard, Workgroup, Essential                              |
| Windows Storage Server 2012, 64bitová verze    | Standard, Workgroup                                         |
| Windows Server 2008 R2 SP1, 64bitová verze     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bitů        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Velikost dat zálohy a podporované Data Box SKU

| Velikost zálohovaných dat (po kompresi pomocí MARS) * na server | Podporovaná Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <= 7,2 TB                                                    | [Azure Data Box disk](../databox/data-box-disk-overview.md) |
| >7,2 TB a <= 80 TB * *                                      | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

* Typické kompresní frekvence se liší od 10% do 20%. <br>
* * Pokud očekáváte, že budete mít více než 80 TB počátečních dat zálohování pro jeden server MARS, kontaktujte [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>Počáteční data zálohy z jednoho serveru musí být obsažena v rámci jedné Azure Data Box instance nebo Azure Data Box disku a nemohou být sdílena mezi více zařízeními stejné nebo jiné SKU. Zařízení Azure Data Box může ale obsahovat počáteční zálohy z více serverů.

## <a name="prerequisites"></a>Předpoklady

### <a name="azure-subscription-and-required-permissions"></a>Předplatné Azure a požadovaná oprávnění

- Proces vyžaduje předplatné Azure.
- Proces vyžaduje, aby byl uživatel určený k provedení zásad offline zálohování vlastníkem předplatného Azure.
- Data Box úlohy a trezor Recovery Services (do kterých musí být data odsazený) musí být ve stejných předplatných.
- Doporučujeme, aby byl cílový účet úložiště přidružený k úloze Azure Data Box a k trezoru Recovery Services ve stejné oblasti. To ale není nutné.

### <a name="get-azure-powershell-370"></a>Získat Azure PowerShell 3.7.0

*Toto je nejdůležitější předpoklad pro tento proces*. Než nainstalujete Azure PowerShell verze 3.7.0, proveďte následující kontroly.

#### <a name="step-1-check-the-powershell-version"></a>Krok 1: ověření verze PowerShellu

1. Otevřete Windows PowerShell a spusťte následující příkaz:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1. Pokud výstup zobrazuje verzi vyšší než 3.7.0, Udělejte krok 2. V opačném případě přejděte na krok 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Krok 2: Odinstalace verze PowerShellu

Odinstalujte aktuální verzi PowerShellu.

1. Odeberte závislé moduly spuštěním následujícího příkazu v PowerShellu:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Chcete-li zajistit úspěšné odstranění všech závislých modulů, spusťte následující příkaz:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Krok 3: instalace prostředí PowerShell verze 3.7.0

Po ověření, že nejsou k dispozici žádné moduly AzureRM, nainstalujte verzi 3.7.0 pomocí jedné z následujících metod:

- Z GitHubu použijte [Tento odkaz](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).

Nebo můžete:

- Spusťte následující příkaz v okně PowerShellu:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell mohl být nainstalován také pomocí souboru MSI. Pokud ho chcete odebrat, odinstalujte ho pomocí možnosti **odinstalovat programy** v Ovládacích panelech.

### <a name="order-and-receive-the-data-box-device"></a>Objednat a přijmout Data Box zařízení

Proces offline zálohování pomocí MARS a Azure Data Box vyžaduje, aby byla Data Boxá zařízení v doručeném stavu před aktivací offline zálohování pomocí agenta MARS. Pro seřazení nejvhodnější skladové položky podle vašeho požadavku si přečtěte část [Velikost dat zálohy a podporované data box SKU](#backup-data-size-and-supported-data-box-skus). Postupujte podle kroků v [kurzu: objednání Azure Data Boxho disku](../databox/data-box-disk-deploy-ordered.md) pro objednání a příjem data box zařízení.

> [!IMPORTANT]
> Pro **druh účtu** nevybírejte *BlobStorage* . Agent MARS vyžaduje účet, který podporuje objekty blob stránky, které nejsou podporované, když je vybraná možnost *BlobStorage* . Jako **druh účtu** při vytváření cílového účtu úložiště pro úlohu Azure Data box vyberte **úložiště v2 (obecné účely v2)** .

![Volba druhu účtu v podrobnostech instance](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Instalace a nastavení agenta MARS

1. Ujistěte se, že jste odinstalovali všechny předchozí instalace agenta MARS.
1. Stáhněte si nejnovějšího agenta MARS z [tohoto webu](https://aka.ms/azurebackup_agent).
1. Spusťte *MARSAgentInstaller.exe* a proveďte *pouze* kroky pro [instalaci a registraci agenta](./install-mars-agent.md#install-and-register-the-agent) do trezoru Recovery Services, kde chcete ukládat zálohy.

   > [!NOTE]
   > Trezor Recovery Services musí být ve stejném předplatném jako úloha Azure Data Box.

   Po registraci agenta do trezoru Recovery Services postupujte podle pokynů v následujících částech.

## <a name="set-up-azure-data-box-devices"></a>Nastavení Azure Data Box zařízení

V závislosti na Azure Data Box SKU, které jste objednali, postupujte podle kroků popsaných v příslušných částech. Kroky ukazují, jak nastavit a připravit Data Box zařízení pro agenta MARS k identifikaci a přenosu počátečních dat zálohy.

### <a name="set-up-azure-data-box-disks"></a>Nastavení Azure Data Box disků

Pokud jste si objednali jeden nebo více Azure Data Box disků (každý až 8 TB), postupujte podle kroků uvedených tady a [rozbalte tak data box disk, připojte se a odemkněte](../databox/data-box-disk-deploy-set-up.md).

>[!NOTE]
>Je možné, že server s agentem MARS nemá port USB. V takové situaci můžete Azure Data Box disk připojit k jinému serveru nebo klientovi a zveřejnit kořen zařízení jako sdílenou síťovou složku.

### <a name="set-up-azure-data-box"></a>Nastavit Azure Data Box

Pokud jste objednali instanci Azure Data Box (až 100 TB), [nastavte instanci data box](../databox/data-box-deploy-set-up.md)pomocí následujících kroků.

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Připojení instance Azure Data Box jako místní systém

Agent MARS funguje v kontextu místního systému, takže vyžaduje, aby byla k dispozici stejná úroveň oprávnění pro cestu k připojení, kde je instance Azure Data Box připojená.

K zajištění toho, aby zařízení Data Box bylo možné připojit jako místní systém pomocí protokolu NFS:

1. Povolte na Windows serveru s nainstalovaným agentem MARS klienta pro funkci NFS. Zadejte alternativní zdrojový soubor *WIM: D: \zdroje\install.wim: 4*.
1. Stáhněte si PsExec ze stránky [Sysinternals](/sysinternals/downloads/psexec) na server s nainstalovaným agentem Mars.
1. Otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz s adresářem, který obsahuje *PSExec.exe* jako aktuální adresář.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Příkazové okno, které se otevře z důvodu předchozího příkazu, je v kontextu místního systému. Pomocí tohoto příkazového okna můžete provést kroky pro připojení sdílené složky Azure Page BLOB jako síťové jednotky na Windows serveru.
1. Pomocí postupu v části [připojení k data box](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) připojte Server s agentem Mars k zařízení data box přes systém souborů NFS. Spuštěním následujícího příkazu na příkazovém řádku místního systému připojte sdílenou složku objektů blob stránky Azure.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Po připojení sdílené složky zkontrolujte, jestli máte přístup k X: z vašeho serveru. Pokud můžete pokračovat v další části tohoto článku.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Přenos počátečních zálohovaných dat do zařízení Azure Data Box

1. Otevřete **Microsoft Azure Backup** aplikace na serveru.
1. V podokně **Akce** vyberte **naplánovat zálohování**.

    ![Vybrat plán zálohování](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Postupujte podle kroků v **Průvodci plánováním zálohování**.

1. Přidejte položky výběrem tlačítka **Přidat položky** . Udržujte celkovou velikost položek v rámci [omezení velikosti, které podporuje Azure Data box SKU](#backup-data-size-and-supported-data-box-skus) , které jste objednali a přijali.

    ![Přidat položky k zálohování](./media/offline-backup-azure-data-box/add-items.png)

1. Vyberte příslušný plán zálohování a zásady uchovávání informací pro **soubory a složky** a **stav systému**. Stav systému se vztahuje pouze na servery Windows a ne pro klienty Windows.
1. Na stránce **zvolit typ prvotní zálohy (soubory a složky)** v průvodci vyberte možnost **přenos pomocí Microsoft Azure Data box disky** a vyberte **Další**.

    ![Zvolit typ prvotní zálohy](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Přihlaste se k Azure po zobrazení výzvy pomocí přihlašovacích údajů uživatele, které mají oprávnění vlastníka v předplatném Azure. Po úspěšném provedení této akce by se měla zobrazit stránka, která bude vypadat jako ta.

    ![Vytváření prostředků a použití požadovaných oprávnění](./media/offline-backup-azure-data-box/creating-resources.png)

   Agent MARS potom načte úlohy Data Box v předplatném, které jsou v doručeném stavu.

    ![Načtení úloh Data Box pro ID předplatného](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Vyberte správné pořadí Data Box, pro které jste nebaleni, připojili a odemkli Data Box disk. Vyberte **Další**.

    ![Výběr objednávek Data Box](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Vyberte **rozpoznat zařízení** na stránce **data box rozpoznávání zařízení** . Díky této akci bude agent MARS místně připojen Azure Data Box disky a detekuje je.

    ![Detekce zařízení Data Box](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Pokud jste instanci Azure Data Box připojili jako sdílenou síťovou složku (kvůli nedostupnosti portů USB nebo jste objednali a připojili Data Box zařízení 100-TB), zjišťování se v první době nezdařila. Máte možnost zadat síťovou cestu k zařízení Data Box.

    ![Zadejte síťovou cestu.](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Zadejte síťovou cestu ke kořenovému adresáři Azure Data Boxho disku. Tento adresář musí obsahovat adresář s názvem *PageBlob*.
    >
    >![Kořenový adresář Azure Data Boxho disku](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Pokud je například cesta k disku `\\mydomain\myserver\disk1\` a *Disk1* obsahuje adresář s názvem *PageBlob*, cesta, kterou zadáte na stránce průvodce agentem Mars, je `\\mydomain\myserver\disk1\` .
    >
    >Pokud jste [nastavili Azure Data Box 100-TB zařízení](#set-up-azure-data-box-devices), zadejte `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` jako síťovou cestu k zařízení.

1. Vyberte **Další** a na další stránce vyberte **Dokončit** a uložte zásady zálohování a uchovávání informací s konfigurací offline zálohování pomocí Azure Data box.

   Na následující stránce se potvrdí, že se zásada úspěšně uložila.

    ![Zásada se úspěšně uložila.](./media/offline-backup-azure-data-box/policy-saved.png)

1. Na předchozí stránce vyberte **Zavřít** .

1. V podokně **Akce** konzoly agenta Mars vyberte **Zálohovat nyní** . Na stránce průvodce vyberte **zálohovat** .

    ![Průvodce zálohováním nyní](./media/offline-backup-azure-data-box/backup-now.png)

Agent MARS spustí zálohování dat, která jste vybrali do zařízení Azure Data Box. Tento proces může trvat několik hodin až pár dní. Doba závisí na počtu souborů a rychlosti připojení mezi serverem a agentem MARS a Azure Data Boxm diskem.

Po dokončení zálohování dat uvidíte stránku agenta MARS, který je podobný tomuto.

![Zobrazený průběh zálohování](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Kroky po zálohování

V této části najdete popis kroků, které je potřeba provést po úspěšném zálohování dat do Azure Data Box Disk.

- Pomocí kroků v tomto článku [dodáte Azure Data box disk do Azure](../databox/data-box-disk-deploy-picked-up.md). Pokud jste použili Azure Data Box 100-TB, postupujte podle těchto kroků a dodejte [zařízení Azure Data box do Azure](../databox/data-box-deploy-picked-up.md).

- [Monitorujte úlohu data box](../databox/data-box-disk-deploy-upload-verify.md) v Azure Portal. Po dokončení úlohy Azure Data Box se agent MARS automaticky přesune data z účtu úložiště do trezoru Recovery Services v době příštího naplánovaného zálohování. Po úspěšném vytvoření bodu obnovení pak označí úlohu zálohování jako *úlohu dokončenou* .

    >[!NOTE]
    >Agent MARS spustí zálohy v časech naplánovaných během vytváření zásad. Tyto úlohy označují příznak "čekání na dokončení Azure Data Box úlohy" až do doby, kdy je úloha dokončena.

- Poté, co agent MARS úspěšně vytvoří bod obnovení, který odpovídá prvotnímu zálohování, můžete odstranit účet úložiště nebo konkrétní obsah přidružený k Azure Data Box úlohy.

## <a name="troubleshooting"></a>Poradce při potížích

Agent Microsoft Azure Recovery Services (MARS) vytvoří ve vašem tenantovi aplikaci Azure Active Directory (Azure AD). Tato aplikace vyžaduje certifikát pro ověřování, který se vytvoří a nahraje při konfiguraci zásad pro počáteční nastavení offline. K vytvoření a nahrání certifikátu do aplikace Azure AD používáme Azure PowerShell.

### <a name="problem"></a>Problém

Při konfiguraci offline zálohování se můžete setkat s problémem v důsledku chyby v rutině Azure PowerShell. Do stejné aplikace Azure AD vytvořené agentem MAB možná nebudete moct přidat víc certifikátů. Tento problém bude mít vliv na to, jestli jste nakonfigurovali zásady osazení offline pro stejný nebo jiný server.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Ověřte, jestli problém způsobuje tato konkrétní hlavní příčina.

Chcete-li zjistit, zda je váš problém stejný jako dříve popsaný, proveďte jeden z následujících kroků.

#### <a name="step-1-of-verification"></a>Krok 1 ověřování

Zkontrolujte, jestli se v konzole MAB při konfiguraci offline zálohování zobrazuje následující chybová zpráva.

![Nepovedlo se vytvořit zásady offline zálohování pro aktuální účet Azure.](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2-of-verification"></a>Krok 2 ověření

1. Otevřete složku **TEMP** v instalační cestě. Výchozí cesta k dočasné složce je *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Vyhledejte soubor *CBUICurr* a soubor otevřete.

1. V souboru *CBUICurr* se posuňte na poslední řádek a zkontrolujte, jestli je problém stejný jako ten v této chybové zprávě: `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed` .

### <a name="workaround"></a>Alternativní řešení

Chcete-li tento problém vyřešit, postupujte podle následujících kroků a opakujte konfiguraci zásad.

#### <a name="step-1-of-workaround"></a>Krok 1 alternativního řešení

Přihlaste se k PowerShellu, který se zobrazí v uživatelském rozhraní MAB pomocí jiného účtu s přístupem správce k předplatnému, které bude mít vytvořenou úlohu Data Box.

#### <a name="step-2-of-workaround"></a>Krok 2 – alternativní řešení

Pokud na žádném jiném serveru není nakonfigurované počáteční nastavování offline a na aplikaci není závislý žádný jiný server `AzureOfflineBackup_<Azure User Id>` , odstraňte tuto aplikaci. Vyberte **Azure Portal**  >  **Azure Active Directory**  >  **Registrace aplikací**.

>[!NOTE]
> Zkontrolujte, jestli aplikace nemá `AzureOfflineBackup_<Azure User Id>` nakonfigurované žádné další offline nastavení, a jestli není na této aplikaci závislý žádný jiný server.   >  V části **veřejné klíče** pokračujte na **klíče** nastavení. Neměl by mít přidané žádné jiné veřejné klíče. Odkaz najdete na následujícím snímku obrazovky.
>
>![Veřejné klíče](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Krok 3

Ze serveru, který se pokoušíte nakonfigurovat pro offline zálohování, proveďte následující akce.

1. Přejít na kartu **Správa počítačového certifikátu**  >  **osobní** a vyhledejte certifikát s názvem `CB_AzureADCertforOfflineSeeding_<ResourceId>` .

2. Vyberte certifikát, klikněte pravým tlačítkem na **všechny úlohy** a vyberte **exportovat** bez privátního klíče ve formátu. cer.

3. V kroku 2 přejdete do aplikace Azure offline Backup. Vyberte **Nastavení**  >  **klíče**  >  **nahrát veřejný klíč**. Nahrajte certifikát, který jste exportovali v předchozím kroku.

    ![Odeslat veřejný klíč](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Na serveru otevřete registr zadáním příkazu **Regedit** v okně Spustit.

5. Přejít doComputer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider registru *.* Klikněte pravým tlačítkem na **CloudBackupProvider** a přidejte novou řetězcovou hodnotu s názvem `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Pokud chcete získat ID uživatele Azure, proveďte jednu z následujících akcí:
    >
    >- Z PowerShellu připojeného k Azure spusťte `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` příkaz.
    > - Přejít na cestu k registru `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` s názvem *CurrentUserId*.

6. Klikněte pravým tlačítkem na řetězec přidaný v předchozím kroku a vyberte **změnit**. V poli hodnota zadejte kryptografický otisk certifikátu, který jste exportovali v kroku 2. Vyberte **OK**.

7. Pokud chcete získat hodnotu kryptografického otisku, poklikejte na certifikát. Vyberte kartu **Podrobnosti** a posuňte se dolů, dokud se nezobrazí pole kryptografický otisk. Vyberte **kryptografický otisk** a zkopírujte hodnotu.

    ![Pole s kryptografickým otiskem certifikátu](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Dotazy

V případě jakýchkoli otázek nebo objasnění problémů, se kterými se sami čelíte, kontaktujte [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .
