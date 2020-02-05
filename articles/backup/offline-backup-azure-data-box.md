---
title: Offline zálohování pomocí Azure Data Box
description: Naučte se, jak můžete použít Azure Data Box k osazení velkých počátečních zálohovaných dat offline od agenta MARS do trezoru služby Azure Recovery Services.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: 553939df8aa7a1b097b2cd6c7f29365302101324
ms.sourcegitcommit: 21e33a0f3fda25c91e7670666c601ae3d422fb9c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/05/2020
ms.locfileid: "77027027"
---
# <a name="azure-backup-offline-backup-using-azure-data-box"></a>Azure Backup offline zálohování pomocí Azure Data Box

Službu [Azure Data box](https://docs.microsoft.com/azure/databox/data-box-overview) můžete použít k osazení velkých počátečních záloh Mars v režimu offline (bez použití sítě) do trezoru služby Azure Recovery Services.  Tím se šetří jak čas, tak šířka pásma sítě, které by jinak využily přesun velkých objemů zálohovaných dat online přes síť s vysokou latencí. Toto vylepšení je momentálně ve verzi Preview. Zálohování offline založené na Azure Data Box poskytuje dvě různé výhody oproti [online zálohování založené na službě Azure import/export](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export).

1. Nemusíte si vystarat vlastní disky a konektory, které jsou kompatibilní s Azure. Služba Azure Data Box dodává disky přidružené k vybrané skladové položce [data box](https://azure.microsoft.com/services/databox/data/)

2. Azure Backup (agent MARS) může přímo zapisovat data zálohy na podporované SKU Azure Data Box. Tím se eliminuje nutnost zřídit pracovní umístění pro počáteční zálohovaná data a potřebovat nástroje pro formátování a zkopírování těchto dat na disky.

## <a name="azure-data-box-with-mars-agent"></a>Azure Data Box s agentem MARS

V tomto článku se dozvíte, jak můžete pomocí Azure Data Box naplnit Velká počáteční data zálohování offline od agenta MARS do trezoru služby Azure Recovery Services. Článek je rozdělen do následujících částí:

* Podporované platformy
* Podporované Data Box SKU
* Požadavky
* Nastavení agenta MARS
* Instalační Azure Data Box
* Přenos zálohovaných dat do Azure Data Box
* Kroky po zálohování

## <a name="supported-platforms"></a>Podporované platformy

Proces pro osazení dat z agenta MARS pomocí Azure Data Box je podporován v následujících SKU systému Windows:

| **OS**                                 | **SKU**                                                      |
| -------------------------------------- | ------------------------------------------------------------ |
| **Pracovní stanice**                        |                                                              |
| Bitová verze Windows 10 64                     | Enterprise, pro, Domovská stránka                                       |
| Bit Windows 8.1 64                    | Enterprise, pro                                             |
| Bitová verze Windows 8 64                      | Enterprise, pro                                             |
| Bitová verze Windows 7 64                      | Ultimate, Enterprise, Professional, Home Premium, Home Basic, Starter |
| **Server**                             |                                                              |
| Windows Server 2019 64 – bit            | Standard, Datacenter, základy                            |
| Windows Server 2016 64 – bit            | Standard, Datacenter, základy                            |
| Windows Server 2012 R2 64 bitů         | Standard, Datacenter, Foundation                            |
| Windows Server 2012 64 – bit            | Datacenter, základ, Standard                            |
| Windows Storage Server 2016 64 – bit    | Standard, pracovní skupina                                         |
| Windows Storage Server 2012 R2 64 bit | Standard, pracovní skupina, nezbytný                              |
| Windows Storage Server 2012 64 – bit    | Standard, pracovní skupina                                         |
| Windows Server 2008 R2 SP1 64 bit     | Standard, Enterprise, Datacenter, Foundation                |
| Windows Server 2008 SP2 64 bitů        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Velikost dat zálohy a podporované Data Box SKU

| Velikost zálohovaných dat (po kompresi po MARS) * na server | Podporovaná Azure Data Box SKU                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| < = 7,2 TB                                                    | [Azure Data Box Disk](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| > 7,2 TB a < = 80 TB * *                                      | [Azure Data Box (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

\* Typické kompresní poměry se liší mezi 10-20% <br>
\* * Pokud očekáváte, že budete mít k dispozici více než 80 TB počátečních dat pro jeden server MARS, můžete * * kontaktovat [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com) .

>[!IMPORTANT]
>Počáteční data zálohy z jednoho serveru musí být obsažena v rámci jednoho Azure Data Box nebo Azure Data Box Disk a nelze je sdílet mezi více zařízeními stejné nebo jiné SKU. Zařízení Azure Data Box ale může obsahovat počáteční zálohy z více serverů.

## <a name="pre-requisites"></a>Požadavky

### <a name="azure-subscription-and-required-permissions"></a>Předplatné Azure a požadovaná oprávnění

* Proces vyžaduje předplatné Azure.
* Proces vyžaduje, aby byl uživatel označený k provedení zásad offline zálohování vlastníkem předplatného Azure.
* Data Box úlohy a trezor Recovery Services (do kterých musí být data odsazený) musí být ve stejných předplatných.
* Doporučuje se, aby byl cílový účet úložiště přidružený k úloze Azure Data Box a trezoru Recovery Services ve stejné oblasti. To ale není nutné.

### <a name="get-azure-powershell-370"></a>Získat Azure PowerShell 3.7.0

**Toto je nejdůležitější předpoklad pro tento proces**. Před instalací Azure PowerShell (ver. 3.7.0) proveďte následující kontroly * *

#### <a name="step-1-check-powershell-version"></a>Krok 1: ověření verze prostředí PowerShell

* Otevřete Windows PowerShell a spusťte následující příkaz:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

* Pokud výstup zobrazuje verzi vyšší než **3.7.0**, proveďte krok 2 níže. Jinak přeskočte ke kroku 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Krok 2: Odinstalace verze PowerShellu

Pomocí následujících akcí odinstalujte aktuální verzi PowerShellu:

* Odeberte závislé moduly spuštěním následujícího příkazu v PowerShellu:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

* Spusťte následující příkaz, abyste zajistili úspěšné odstranění všech závislých modulů:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Krok 3: instalace prostředí PowerShell verze 3.7.0

Jakmile ověříte, že nejsou k dispozici žádné moduly AzureRM, nainstalujte pomocí jedné z následujících metod verzi 3.7.0:

* Z GitHubu, [odkaz](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017)

NEBO

* Spusťte následující příkaz v okně PowerShellu:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell mohl být nainstalován také pomocí souboru MSI. Pokud ho chcete odebrat, odinstalujte ho pomocí možnosti odinstalovat programy v Ovládacích panelech.

### <a name="order-and-receive-the-data-box-device"></a>Objednat a přijmout Data Box zařízení

Proces offline zálohování pomocí MARS a Azure Data Box vyžaduje, aby před aktivací offline zálohování pomocí agenta MARS byla povinná zařízení Data Box ve stavu "doručeno". Pro seřazení nejvhodnější SKU pro váš požadavek se podívejte na [Velikost dat zálohy a podporované skladové jednotky data box](#backup-data-size-and-supported-data-box-skus) . Podle kroků v [tomto článku](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) seřiďte a dodržujte zařízení data box.

>[!IMPORTANT]
>Pro druh účtu nevybírejte možnost BlobStorage. Agent MARS vyžaduje účet, který podporuje objekty blob stránky, které nejsou podporované, když je vybraná možnost BlobStorage. Při vytváření cílového účtu úložiště pro Azure Data Box úlohy se důrazně doporučuje jako typ účtu vybrat *Storage v2* (*obecné účely v2*).

![Volba druhu účtu v podrobnostech instance](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-setup-the-mars-agent"></a>Instalace a nastavení agenta MARS

* Ujistěte se, že jste odinstalovali všechny předchozí instalace agenta MARS.

* Stáhněte si nejnovějšího agenta MARS [odsud.](https://aka.ms/azurebackup_agent)

* Spusťte *soubor marsagentinstaller. exe* a proveďte ***pouze** kroky pro [instalaci a registraci agenta](https://docs.microsoft.com/azure/backup/backup-configure-vault#install-and-register-the-agent) do trezoru Recovery Services, do kterého chcete ukládat zálohy.

  >[!NOTE]
  > Trezor Recovery Services musí být ve stejném předplatném jako úloha Azure Data Box.

* Jakmile je agent zaregistrován v úložišti Recovery Services, postupujte podle kroků v následujících částech.

## <a name="setup-azure-data-box-devices"></a>Nastavit Azure Data Box zařízení

V závislosti na Azure Data Box SKU, které jste objednali, proveďte kroky popsané v příslušných částech níže, abyste nastavili a připravili Data Box zařízení pro agenta MARS k identifikaci a přenosu počátečních dat zálohy.

### <a name="setup-azure-data-box-disk"></a>Instalační Azure Data Box Disk

Pokud jste objednali jeden nebo více Azure Data Box disků (až do 8 TB), postupujte podle kroků uvedených tady a [rozbalte své data box disk, připojte je a odemkněte](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>Je možné, že server s agentem MARS nemá port USB. V takové situaci můžete Azure Data Box Disk připojit k jinému serveru nebo klientovi a zveřejnit kořen zařízení jako sdílenou síťovou složku.

### <a name="setup-azure-data-box"></a>Instalační Azure Data Box

Pokud jste objednali Azure Data Box (až 100 TB), postupujte podle kroků uvedených tady [a nastavte data box](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up).

#### <a name="mount-your-azure-data-box-as-local-system"></a>Připojit Azure Data Box jako místní systém

Agent MARS funguje v kontextu místního systému, a proto vyžaduje, aby byla k dispozici stejná úroveň oprávnění pro cestu k připojení, kde je Azure Data Box připojená. Postupujte podle následujících kroků a ujistěte se, že zařízení Data Box můžete připojit jako místní systém pomocí protokolu NFS:

* Povolte funkci klient pro systém souborů NFS na Windows serveru (s nainstalovaným agentem MARS).<br>
  Zadejte alternativní zdroj: *WIM: D: \zdroje\install.wim: 4*

* Stáhněte si PSExec z <https://download.sysinternals.com/files/PSTools.zip> na server s nainstalovaným agentem MARS.

* Otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz s adresářem, který obsahuje soubor PSExec. exe jako aktuální adresář:

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

* Příkazové okno, které se otevře v důsledku výše uvedeného příkazu, je v kontextu místního systému. Pomocí tohoto příkazového okna můžete provést kroky pro připojení sdílené složky Azure Page BLOB jako síťové jednotky na Windows serveru.

* Podle těchto kroků připojte Server s agentem [Mars k data box](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) zařízení přes systém souborů NFS a spusťte následující příkaz na příkazovém řádku místního systému pro připojení sdílené složky objektů blob stránky Azure:

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

* Po připojení ověřte, jestli máte přístup k X: z vašeho serveru. Pokud ano, pokračujte v další části tohoto článku.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Přenést počáteční zálohovaná data do zařízení Azure Data Box

* Otevřete **Microsoft Azure Backup** aplikace na serveru.

* V podokně **Akce** klikněte na **naplánovat zálohování** .

    ![Klikněte na naplánovat zálohování.](./media/offline-backup-azure-data-box/schedule-backup.png)

* Postupujte podle kroků v **Průvodci plánováním zálohování** .

* **Přidejte položky** tak, aby celková velikost položek byla v rámci [omezení velikosti podporovaného Azure Data box SKU](#backup-data-size-and-supported-data-box-skus) , kterou jste objednali a obdrželi.

    ![Přidat položky k zálohování](./media/offline-backup-azure-data-box/add-items.png)

* Vyberte příslušný plán zálohování a zásady uchovávání informací pro soubory a složky a stav systému (stav systému se vztahuje jenom na servery Windows a ne pro klienty Windows).

* Na obrazovce **Vybrat typ prvotní zálohy (soubory a složky)** v průvodci vyberte možnost **přenos pomocí Microsoft Azure Data box disky** a klikněte na **Další** .

    ![Zvolit typ prvotní zálohy](./media/offline-backup-azure-data-box/initial-backup-type.png)

* Přihlaste se k Azure po zobrazení výzvy pomocí přihlašovacích údajů uživatele, které mají oprávnění vlastníka v předplatném Azure. Po úspěšném provedení této akce by se měla zobrazit obrazovka podobná této:

    ![Vytváření prostředků a použití požadovaných oprávnění](./media/offline-backup-azure-data-box/creating-resources.png)

* Agent MARS potom načte úlohy Data Box v předplatném, které jsou ve stavu "doručeno".

    ![Načítají se úlohy Databox pro ID předplatného.](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

* Vyberte správné pořadí datových polí, pro které jste nebaleni, připojili a odemkli Data Box disk. Klikněte na **Další**.

    ![Vybrat pořadí Data Box](./media/offline-backup-azure-data-box/select-databox-order.png)

* Na obrazovce **detekce zařízení data box** klikněte na **rozpoznat zařízení** . Díky tomu bude agent MARS místně připojen Azure Data Box disky a detekuje je, jak je znázorněno níže:

    ![Detekce zařízení Data Box](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Pokud jste Azure Data Box připojeni jako sdílenou síťovou složku (kvůli nedostupnosti portů USB nebo jste objednali a připojili 100 TB Data Box zařízení), zjišťování se při prvním selhání nezdaří, ale poskytne vám možnost zadat síťovou cestu k Data Box zařízení jako s. hown níže:

    ![Zadejte síťovou cestu.](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Zadejte síťovou cestu ke kořenovému adresáři Azure Data Boxho disku. Tento adresář musí obsahovat adresář s názvem *PageBlob* , jak je znázorněno níže:
    >
    >![Kořenový adresář Azure Data Boxho disku](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Pokud je například cesta k disku `\\mydomain\myserver\disk1\` a *Disk1* obsahuje adresář s názvem *PageBlob*, bude cesta k dispozici v průvodci agentem Mars `\\mydomain\myserver\disk1\`
    >
    >Pokud [nastavili Azure Data Box 100 TB zařízení](#setup-azure-data-box), zadejte jako síťovou cestu k zařízení následující `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob`

* Klikněte na tlačítko **Další** a na další obrazovce klikněte na tlačítko **Dokončit** a uložte zásadu zálohování a uchovávání dat s konfigurací zálohování offline pomocí Azure Data box.

* Na následující obrazovce se potvrdí, že se zásada úspěšně uložila:

    ![Zásada se úspěšně uložila.](./media/offline-backup-azure-data-box/policy-saved.png)

* Klikněte na tlačítko **Zavřít** na obrazovce výše.

* V podokně **Akce** konzoly agenta Mars klikněte na ***Zálohovat nyní** a na obrazovce průvodce klikněte na **zálohovat** , jak je znázorněno níže:

    ![Průvodce zálohováním nyní](./media/offline-backup-azure-data-box/backup-now.png)

* Agent MARS spustí zálohování dat, která jste vybrali, do zařízení Azure Data Box. To může trvat několik hodin až několik dní v závislosti na počtu souborů a rychlosti připojení mezi serverem a agentem MARS a Azure Data Box Disk.

* Po dokončení zálohování dat se na agentovi MARS zobrazí obrazovka, která bude vypadat jako na následujícím obrázku:

    ![Zobrazený průběh zálohování](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Kroky po zálohování

V této části najdete popis kroků, které je potřeba provést po úspěšném zálohování dat do Azure Data Box Disk.

* Pomocí kroků v tomto článku [dodáte Azure Data box disk do Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Pokud jste použili Azure Data Box 100 TB, dokončete [Azure Data box do Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up)pomocí těchto kroků.

* [Monitorujte úlohu data box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) v Azure Portal. Jakmile bude úloha Azure Data Box dokončená, agent MARS automaticky přesune data z účtu úložiště do trezoru Recovery Services v době příštího naplánovaného zálohování. Po úspěšném vytvoření bodu obnovení bude úloha zálohování označena jako dokončená úloha.

    >[!NOTE]
    >Agent MARS spustí zálohování v časech naplánovaných během vytváření zásad. Tyto úlohy se ale označí jako "čekání na dokončení Azure Data Box úlohy" až do doby, kdy je úloha dokončena.

* Poté, co agent MARS úspěšně vytvoří bod obnovení odpovídající prvotnímu zálohování, můžete odstranit účet úložiště (nebo konkrétní obsah) přidružený k úloze Azure Data Box.

## <a name="troubleshooting"></a>Řešení potíží

Agent Microsoft Azure Backup (MAB) Vytvoří aplikaci Azure AD pro vás ve vašem tenantovi. Tato aplikace vyžaduje certifikát pro ověřování, který se vytvoří a nahraje při konfiguraci zásad pro počáteční nasazení offline. Pro vytvoření a nahrání certifikátu do aplikace Azure AD používáme Azure PowerShell.

### <a name="issue"></a>Problém

V době konfigurace offline zálohování se může jednat o problém, ve kterém kvůli chybě v rutině Azure PowerShell nemůžete přidat víc certifikátů do stejné aplikace Azure AD vytvořené agentem MAB. To bude mít vliv na to, jestli jste nakonfigurovali zásady dosazení offline pro stejný nebo jiný server.

### <a name="how-to-verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Jak ověřit, jestli problém způsobuje tato konkrétní hlavní příčina

Chcete-li zajistit, aby chyba byla způsobena problémem výše, proveďte jeden z následujících kroků:

#### <a name="step-1"></a>Krok 1

Zkontrolujte, jestli je v konzole MAB v době konfigurace offline zálohování zobrazená následující chybová zpráva:

![Nepovedlo se vytvořit zásady offline zálohování pro aktuální účet Azure.](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Krok 2

* Otevřete složku **TEMP** v instalační cestě (výchozí cesta k dočasné složce je *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*). Vyhledejte soubor **CBUICurr** a otevřete soubor.

* V souboru **CBUICurr** se posuňte na poslední řádek a ověřte, jestli je chyba způsobená `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`.

### <a name="workaround"></a>Alternativní řešení

Pokud chcete tento problém vyřešit, proveďte následující kroky a zkuste konfiguraci zásad zopakovat.

#### <a name="first-step"></a>První krok

Přihlaste se k PowerShellu, který se zobrazí v uživatelském rozhraní MAB pomocí jiného účtu s přístupem správce k předplatnému, které bude mít vytvořenou úlohu importu exportu.

#### <a name="second-step"></a>Druhý krok

Pokud není nakonfigurovaný žádný jiný server a na `AzureOfflineBackup_<Azure User Id>` aplikaci nezávisí žádný jiný server, odstraňte tuto aplikaci z **Azure Portal** > **Azure Active Directory** > **Registrace aplikací.**

>[!NOTE]
> Ověřte, zda `AzureOfflineBackup_<Azure User Id>` aplikace nemá nakonfigurované žádné jiné počáteční hodnoty pro offline a zda na této aplikaci není závislý žádný jiný server. V části **veřejné klíče** vyberte **Nastavení** > **klíče** . neměla by se přidat žádný jiný veřejný klíč. Referenční informace najdete na následujícím snímku obrazovky:
>
>![Veřejné klíče](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="third-step"></a>Třetí krok

Ze serveru, který se pokoušíte nakonfigurovat offline zálohování, proveďte následující akce:

1. Otevřete kartu **Správa aplikace certifikát počítače** > **osobní** karta a vyhledejte certifikát s názvem `CB_AzureADCertforOfflineSeeding_<ResourceId>`

2. Vyberte výše uvedený certifikát, klikněte pravým tlačítkem na **všechny úlohy** a **exportujte** bez privátního klíče ve formátu. cer.

3. Přejděte na aplikaci Azure offline Backup uvedenou v **bodě 2**. V > **klíče** **Nastavení** > **Odeslat veřejný klíč,** Nahrajte certifikát exportovaný v kroku výše.

    ![Odeslat veřejný klíč](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Na serveru otevřete registr zadáním příkazu **Regedit** v okně Spustit.

5. Přejít na počítač registru *\ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\Config\CloudBackupProvider.* Klikněte pravým tlačítkem na **CloudBackupProvider** a přidejte novou řetězcovou hodnotu s názvem `AzureADAppCertThumbprint_<Azure User Id>`

    >[!NOTE]
    > Pokud chcete získat ID uživatele Azure, proveďte jednu z následujících akcí:
    >
    >1. Z připojeného prostředí PowerShell Azure spusťte příkaz `Get-AzureRmADUser -UserPrincipalName “Account Holder’s email as defined in the portal”`.
    > 2. Přejděte do cesty k registru: *Computer \ HKEY_LOCAL_MACHINE \Software\microsoft\windows Azure Backup\DbgSettings\OnlineBackup*; Název: *CurrentUserId*

6. Klikněte pravým tlačítkem na řetězec přidaný v kroku výše a vyberte **změnit**. V poli hodnota zadejte kryptografický otisk certifikátu, který jste exportovali v **bodě 2** , a klikněte na **OK**.

7. Pokud chcete získat hodnotu kryptografického otisku, poklikejte na certifikát, pak vyberte kartu **Podrobnosti** a posuňte se dolů, dokud se nezobrazí pole kryptografický otisk. Klikněte na **kryptografický otisk** a zkopírujte hodnotu.

    ![Pole s kryptografickým otiskem certifikátu](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Otázky

V případě jakýchkoli otázek nebo vyjasnění z hlediska všech problémů, které čelí, se dostanete k [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)
