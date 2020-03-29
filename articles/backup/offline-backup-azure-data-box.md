---
title: Offline zálohování pomocí Azure Data Box
description: Zjistěte, jak můžete pomocí Azure Data Box umocnit velká počáteční záložní data offline z agenta MARS do trezoru služby Recovery Services.
ms.topic: conceptual
ms.date: 1/27/2020
ms.openlocfilehash: a031a8cac357e7d212f8f6a3a5dbec749fbccc21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "78672970"
---
# <a name="azure-backup-offline-backup-by-using-azure-data-box"></a>Zálohování Azure offline zálohování pomocí Azure Data Box

[Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-overview) můžete použít k nastavení velkých počátečních záloh služby Microsoft Azure Recovery Services (MARS) offline (bez použití sítě) do trezoru služby Recovery Services. Tento proces šetří čas a šířku pásma sítě, která by jinak byla spotřebována přesunutím velkého množství záložních dat online přes síť s vysokou latencí. Toto vylepšení je v současné době ve verzi Preview. Offline zálohování založené na Azure Data Box poskytuje dvě odlišné výhody oproti [offline zálohování na základě služby Azure Import/Export](https://docs.microsoft.com/azure/backup/backup-azure-backup-import-export):

* Není třeba obstarávat vlastní disky a konektory kompatibilní s Azure. Azure Data Box dodává disky přidružené k vybrané [skladové jednotce datové schránky](https://azure.microsoft.com/services/databox/data/).
* Azure Backup (MARS Agent) může přímo zapisovat záložní data do podporovaných skladových částek Azure Data Box. Tato funkce eliminuje potřebu zřídit pracovní umístění pro počáteční záložní data. K formátování a kopírování dat na disky také nepotřebujete nástroje.

## <a name="azure-data-box-with-the-mars-agent"></a>Datová schránka Azure s agentem MARS

Tento článek vysvětluje, jak můžete použít Azure Data Box k osazení velkých počátečních záložních dat offline z agenta MARS do trezoru služby Recovery Services.

## <a name="supported-platforms"></a>Podporované platformy

Proces osiva dat z agenta MARS pomocí Azure Data Box je podporován na následujících skum Windows.

| **OS**                                 | **Sku**                                                      |
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
| Bit Windows Server 2008 SP2 64        | Standard, Enterprise, Datacenter                            |

## <a name="backup-data-size-and-supported-data-box-skus"></a>Velikost záložních dat a podporované skladové sady datových schrově

| Velikost záložních dat (post-komprese pomocí MARS)* na server | Podporovaná skladová položka Azure Data Box                                      |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| <=7,2 TB                                                    | [Disk datové schránky Azure](https://docs.microsoft.com/azure/databox/data-box-disk-overview) |
| >7,2 TB a <=80 TB**                                      | [Datová skříňka Azure (100 TB)](https://docs.microsoft.com/azure/databox/data-box-overview) |

*Typické kompresní rychlosti se pohybují mezi 10% a 20%. <br>
**Pokud očekáváte, že budete mít více než 80 TB [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)počátečních záložních dat pro jeden server MARS, kontaktujte .

>[!IMPORTANT]
>Počáteční data zálohování z jednoho serveru musí být obsažena v rámci jedné instance Azure Data Box nebo disku Azure Data Box a nelze je sdílet mezi více zařízeními stejné nebo jiné skladové složky. Ale zařízení Azure Data Box může obsahovat počáteční zálohy z více serverů.

## <a name="prerequisites"></a>Požadavky

### <a name="azure-subscription-and-required-permissions"></a>Předplatné Azure a požadovaná oprávnění

* Tento proces vyžaduje předplatné Azure.
* Tento proces vyžaduje, aby uživatel určený k provádění zásad zálohování offline je vlastníkem předplatného Azure.
* Úloha data box a úložiště služby Recovery Services (do kterého musí být data nasazena) musí být ve stejném předplatném.
* Doporučujeme, aby cílový účet úložiště přidružený k úloze Azure Data Box a trezoru služby Recovery Services se nacházejí ve stejné oblasti. To však není nutné.

### <a name="get-azure-powershell-370"></a>Získání Azure PowerShellu 3.7.0

*To je nejdůležitější předpoklad pro proces*. Před instalací Azure PowerShellu verze 3.7.0 proveďte následující kontroly.

#### <a name="step-1-check-the-powershell-version"></a>Krok 1: Kontrola verze PowerShellu

1. Spusťte prostředí Windows PowerShell a spusťte následující příkaz:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

1.  Pokud se na výstupu zobrazí verze vyšší než 3.7.0, proveďte krok 2. V opačném případě přejděte na krok 3.

#### <a name="step-2-uninstall-the-powershell-version"></a>Krok 2: Odinstalace verze PowerShellu

Odinstalujte aktuální verzi PowerShellu.

1. Odebrat závislé moduly spuštěním následujícího příkazu v Prostředí PowerShell:

    ```powershell
    foreach ($module in (Get-Module -ListAvailable AzureRM*).Name |Get-Unique)  { write-host "Removing Module $module" Uninstall-module $module }
    ```

2. Chcete-li zajistit úspěšné odstranění všech závislých modulů, spusťte následující příkaz:

    ```powershell
    Get-Module -ListAvailable AzureRM*
    ```

#### <a name="step-3-install-powershell-version-370"></a>Krok 3: Instalace prostředí PowerShell verze 3.7.0

Po ověření, že nejsou k dispozici žádné moduly AzureRM, nainstalujte verzi 3.7.0 pomocí jedné z následujících metod:

* Z GitHubu použijte [tento odkaz](https://github.com/Azure/azure-powershell/releases/tag/v3.7.0-March2017).

Nebo můžete:

* V okně PowerShellu spusťte následující příkaz:

    ```powershell
    Install-Module -Name AzureRM -RequiredVersion 3.7.0
    ```

Azure PowerShell mohl být taky nainstalovaný pomocí souboru msi. Chcete-li jej odebrat, odinstalujte ji pomocí možnosti **Odinstalovat programy** v Ovládacích panelech.

### <a name="order-and-receive-the-data-box-device"></a>Objednejte a přijměte zařízení Datové schránky

Proces zálohování offline pomocí MARS a Azure Data Box vyžaduje, aby data box zařízení být ve stavu Dodáno před aktivací zálohování offline pomocí MARS Agent. Chcete-li objednat nejvhodnější skladovou položku pro váš požadavek, přečtěte si informace [o velikosti dat a podporovaných skladových položkách datové schránky](#backup-data-size-and-supported-data-box-skus). Postupujte podle kroků v [kurzu: Objednejte disk Azure Data Box](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-ordered) pro objednání a příjem zařízení datové schránky.

> [!IMPORTANT]
> Nevybírejte *BlobStorage* pro **typ účtu**. Agent MARS vyžaduje účet, který podporuje objekty BLOB stránky, které nejsou podporovány, když je *vybránblobStorage.* Při vytváření cílového účtu úložiště pro úlohu Azure Data Box vyberte **úložiště V2 (pro obecné účely v2)** jako **typ účtu.**

![Zvolit druh účtu v podrobnostech instance](./media/offline-backup-azure-data-box/instance-details.png)

## <a name="install-and-set-up-the-mars-agent"></a>Instalace a nastavení agenta MARS

1. Ujistěte se, že odinstalujete všechny předchozí instalace agenta MARS.
1. Stáhněte si nejnovější MARS Agent z [této webové stránky](https://aka.ms/azurebackup_agent).
1. Spusťte *program MARSAgentInstaller.exe*a proveďte *pouze* kroky k [instalaci a registraci agenta](https://docs.microsoft.com/azure/backup/install-mars-agent#install-and-register-the-agent) do trezoru služby Recovery Services, kde chcete uložit zálohy.

   > [!NOTE]
   > Trezor služby Recovery Services musí být ve stejném předplatném jako úloha Azure Data Box.

   Po registraci agenta do trezoru služby Recovery Services postupujte podle kroků v následujících částech.

## <a name="set-up-azure-data-box-devices"></a>Nastavení zařízení Azure Data Box

V závislosti na sku datové schránky Azure, kterou jste si objednali, proveďte kroky uvedené v příslušných částech, které následují. Postup ukazuje, jak nastavit a připravit zařízení datové schránky pro agenta MARS k identifikaci a přenosu počátečních záložních dat.

### <a name="set-up-azure-data-box-disks"></a>Nastavení disků Azure Data Box

Pokud jste si objednali jeden nebo více disků Azure Data Box (každý až 8 TB), postupujte podle zde uvedených kroků [k rozbalení, připojení a odemknutí disku databoxu](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-set-up).

>[!NOTE]
>Je možné, že server s agentem MARS nemá port USB. V takovém případě můžete připojit disk Azure Data Box k jinému serveru nebo klientovi a vystavit kořen zařízení jako sdílenou síťovou složku.

### <a name="set-up-azure-data-box"></a>Nastavení datové schránky Azure

Pokud jste si objednali instanci Azure Data Box (až 100 TB), postupujte podle pokynů zde [a nastavte instanci datové schránky](https://docs.microsoft.com/azure/databox/data-box-deploy-set-up).

#### <a name="mount-your-azure-data-box-instance-as-a-local-system"></a>Připojení instance datové schránky Azure jako místního systému

Agent MARS pracuje v kontextu místního systému, takže vyžaduje stejnou úroveň oprávnění, která má být poskytnuta na cestu připojení, kde je připojena instance Azure Data Box. 

Chcete-li zajistit, že zařízení Data Box můžete připojit jako místní systém pomocí protokolu nfs:

1. Povolte klienta pro funkci systému zabezpečení systému připojení k systému windows na serveru se systémem Windows, na který je nainstalován agent MARS. Zadejte alternativní zdroj *WIM:D:\Sources\Install.wim:4*.
1. Stáhněte psexec ze <https://download.sysinternals.com/files/PSTools.zip> serveru s nainstalovaným agentem MARS.
1. Otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz s adresářem, který obsahuje *soubor PSExec.exe* jako aktuální adresář.

    ```cmd
    psexec.exe  -s  -i  cmd.exe
    ```

   Příkazové okno, které se otevře v důsledku předchozího příkazu, je v kontextu Místní systém. Pomocí tohoto příkazového okna můžete provést kroky k připojení sdílené položky objektu blob stránky Azure jako síťové jednotky na serveru windows.
1. Podle pokynů v části [Připojit k datové schránce](https://docs.microsoft.com/azure/databox/data-box-deploy-copy-data-via-nfs#connect-to-data-box) připojte server k agentovi MARS k zařízení Data Box přes systém Sdílení připojení ke službě Microsoft FFS. Spusťte následující příkaz na příkazovém řádku Místní systém pro připojení sdílené položky objektů blob stránky Azure.

    ```cmd
    mount -o nolock \\<DeviceIPAddress>\<StorageAccountName_PageBlob X:  
    ```

   Po připojení sdílené složky zkontrolujte, zda máte přístup k X: ze serveru. Pokud je to možné, pokračujte v další části tohoto článku.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Přenos počátečních záložních dat do zařízení Azure Data Box

1. Otevřete na serveru aplikaci **Microsoft Azure Backup.**
1. V podokně **Akce** vyberte **Naplánovat zálohování**.

    ![Vybrat možnost Naplánovat zálohování](./media/offline-backup-azure-data-box/schedule-backup.png)

1. Postupujte podle pokynů průvodce **zálohováním naplánujte**.

1. Přidejte položky výběrem tlačítka **Přidat položky.** Udržujte celkovou velikost položek v rámci [omezení velikosti podporovaných skladovou položkou datové schránky Azure,](#backup-data-size-and-supported-data-box-skus) kterou jste si objednali a přijali.

    ![Přidání položek do zálohy](./media/offline-backup-azure-data-box/add-items.png)

1. Vyberte příslušné zásady zálohování a uchovávání informací pro **soubory a složky** a **stav systému**. Stav systému je použitelný pouze pro servery Systému Windows a nikoli pro klienty systému Windows.
1. Na stránce **Zvolit počáteční typ zálohy (soubory a složky)** průvodce vyberte možnost **Přenést pomocí disků Microsoft Azure Data Box** a vyberte **Další**.

    ![Zvolit počáteční typ zálohy](./media/offline-backup-azure-data-box/initial-backup-type.png)

1. Po zobrazení výzvy pomocí přihlašovacích údajů uživatele, které mají přístup vlastníka k předplatnému Azure, se přihlaste k Azure. Poté, co se vám to podaří, měli byste vidět stránku, která se podobá této.

    ![Vytvoření prostředků a použití požadovaných oprávnění](./media/offline-backup-azure-data-box/creating-resources.png)

   Agent MARS pak načte úlohy datové schránky v předplatném, které jsou ve stavu Dodáno.

    ![Úlohy načítání datové schránky pro ID předplatného](./media/offline-backup-azure-data-box/fetching-databox-jobs.png)

1. Vyberte správné pořadí datových schrápů, pro které jste rozbalili, připojili a odemkli disk Datové schránky. Vyberte **další**.

    ![Vybrat objednávky datových schronů](./media/offline-backup-azure-data-box/select-databox-order.png)

1. Na stránce **Detekce zařízení datového pole** vyberte **Rozpoznat zařízení.** Tato akce umožňuje agentmars prohledává místně připojené disky Azure Data Box a detekuje je.

    ![Detekce zařízení datové schránky](./media/offline-backup-azure-data-box/databox-device-detection.png)

    Pokud jste připojili instanci Azure Data Box jako sdílenou síťovou složku (z důvodu nedostupnosti portů USB nebo proto, že jste si objednali a připojili zařízení datové schránky o velikosti 100 TB), zjišťování se nejprve nezdaří. Máte možnost zadat síťovou cestu k zařízení Data Box.

    ![Zadání síťové cesty](./media/offline-backup-azure-data-box/enter-network-path.png)

    >[!IMPORTANT]
    > Zadejte síťovou cestu ke kořenovému adresáři disku Azure Data Box. Tento adresář musí obsahovat adresář s názvem *PageBlob*.
    >
    >![Kořenový adresář disku Azure Data Box](./media/offline-backup-azure-data-box/root-directory.png)
    >
    >Pokud je `\\mydomain\myserver\disk1\` například cesta k disku a *disk1* obsahuje adresář s názvem *PageBlob*, `\\mydomain\myserver\disk1\`je cesta, kterou zadáte na stránce průvodce MARS .
    >
    >Pokud [nastavíte zařízení Azure Data Box 100 TB](#set-up-azure-data-box-devices), zadejte `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` jako síťovou cestu k zařízení.

1. Vyberte **Další**a na další stránce vyberte **Dokončit,** chcete-li uložit zásady zálohování a uchovávání informací s konfigurací offline zálohování pomocí Azure Data Box.

   Následující stránka potvrzuje, že zásada je úspěšně uložena.

    ![Zásady jsou úspěšně uloženy.](./media/offline-backup-azure-data-box/policy-saved.png)

1. Na předchozí stránce vyberte **Zavřít.**

1. V podokně **Akce** konzoly MARS Agent vyberte **Zálohovat.** Na stránce průvodce vyberte **Zálohovat.**

    ![Průvodce zálohováním](./media/offline-backup-azure-data-box/backup-now.png)

Agent MARS spustí zálohování dat, která jste vybrali do zařízení Azure Data Box. Tento proces může trvat několik hodin až několik dní. Doba závisí na počtu souborů a rychlosti připojení mezi serverem s agentem MARS a diskem Azure Data Box.

Po dokončení zálohování dat se zobrazí stránka na agentovi MARS, která se podobá této.

![Zobrazen průběh zálohování](./media/offline-backup-azure-data-box/backup-progress.png)

## <a name="post-backup-steps"></a>Kroky po zálohování

Tato část vysvětluje kroky, které je třeba provést po úspěšném zálohování dat na disk datové schránky Azure.

* Podle pokynů v tomto článku [dodejte disk Azure Data Box do Azure](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-picked-up). Pokud jste použili zařízení Azure Data Box s kapacitou 100 TB, postupujte takto a [dodejte zařízení Azure Data Box do Azure](https://docs.microsoft.com/azure/databox/data-box-deploy-picked-up).

* [Sledujte úlohu datové schránky](https://docs.microsoft.com/azure/databox/data-box-disk-deploy-upload-verify) na webu Azure Portal. Po dokončení úlohy Datové schránky Azure agent mars automaticky přesune data z účtu úložiště do trezoru služby Recovery Services v době další naplánované zálohy. Potom označí úlohu zálohování jako *úlohu dokončena,* pokud je úspěšně vytvořen bod obnovení.

    >[!NOTE]
    >Agent MARS aktivuje zálohování v časech plánovaných během vytváření zásad. Tyto úlohy příznak "Čekání na úlohu Azure Data Box, které mají být dokončeny" až do doby dokončení úlohy.

* Poté, co agent MARS úspěšně vytvoří bod obnovení, který odpovídá počáteční zálohě, můžete odstranit účet úložiště nebo konkrétní obsah přidružený k úloze Azure Data Box.

## <a name="troubleshooting"></a>Řešení potíží

Agent Zálohování Microsoft Azure (MAB) vytvoří aplikaci Azure Active Directory (Azure AD) pro vás ve vašem tenantovi. Tato aplikace vyžaduje certifikát pro ověřování, který je vytvořen a odeslán při konfiguraci zásady offline osiva. Azure PowerShell používáme k vytvoření a nahrání certifikátu do aplikace Azure AD.

### <a name="problem"></a>Problém

Při konfiguraci zálohování offline může dojít k problému z důvodu chyby v rutině Prostředí Azure PowerShell. Pravděpodobně nebude možné přidat více certifikátů do stejné aplikace Azure AD vytvořené agentem MAB. Tento problém se vás bude týkat, pokud jste nakonfigurovali zásady offline osevací pro stejný nebo jiný server.

### <a name="verify-if-the-problem-is-caused-by-this-specific-root-cause"></a>Ověřte, zda je problém způsoben touto konkrétní hlavní příčinou

Chcete-li zjistit, zda je váš problém stejný jako dříve popsaný, proveďte jeden z následujících kroků.

#### <a name="step-1"></a>Krok 1

Zkontrolujte, zda se při konfiguraci zálohování offline zobrazí následující chybová zpráva v konzole MAB.

![Nelze vytvořit zásady offline zálohování pro aktuální účet Azure.](./media/offline-backup-azure-data-box/unable-to-create-policy.png)

#### <a name="step-2"></a>Krok 2

1. Otevřete složku **Temp** v instalační cestě. Výchozí cesta dočasné složky je *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Vyhledejte soubor *CBUICurr* a otevřete soubor.

1. V *souboru CBUICurr* přejděte na poslední řádek a zkontrolujte, zda je problém `Unable to create an Azure AD application credential in customer's account. Exception: Update to existing credential with KeyId <some guid> is not allowed`stejný jako problém v této chybové zprávě: .

### <a name="workaround"></a>Alternativní řešení

Jako řešení tohoto problému proveďte následující kroky a opakujte konfiguraci zásad.

#### <a name="step-1"></a>Krok 1

Přihlaste se k Prostředí PowerShell, který se zobrazí na uživatelském rozhraní MAB pomocí jiného účtu s přístupem správce k předplatnému, který bude mít vytvořenou úlohu importu nebo exportu.

#### <a name="step-2"></a>Krok 2

Pokud žádný jiný server nemá nakonfigurováno offline osevování a žádný jiný server není závislý na `AzureOfflineBackup_<Azure User Id>` aplikaci, odstraňte tuto aplikaci. Vyberte**Registrace aplikací**Azure Active**Directory** > na **webu** > Azure Portal .

>[!NOTE]
> Zkontrolujte, zda `AzureOfflineBackup_<Azure User Id>` aplikace nemá jiné offline osevní nakonfigurován a také v případě, že žádný jiný server je závislá na této aplikaci. Přejděte do **části Nastavení** > **v** části **Veřejné klíče.** Neměly by být přidány žádné další veřejné klíče. Viz následující snímek obrazovky pro referenci.
>
>![Veřejné klíče](./media/offline-backup-azure-data-box/public-keys.png)

#### <a name="step-3"></a>Krok 3

Na serveru, který se pokoušíte nakonfigurovat pro zálohování offline, proveďte následující akce.

1. Přejděte na**kartu** **Spravovat aplikaci** > osobní počítač a `CB_AzureADCertforOfflineSeeding_<ResourceId>`vyhledejte certifikát s názvem .

2. Vyberte certifikát, klepněte pravým tlačítkem myši na **všechny úkoly**a vyberte **exportovat** bez soukromého klíče ve formátu CER.

3. Přejděte do aplikace pro zálohování azure offline uvedenou v kroku 2. Vyberte **možnost Nastavení** > **Klávesy** > **Nahrát veřejný klíč**. Nahrajte certifikát, který jste exportovali v předchozím kroku.

    ![Nahrát veřejný klíč](./media/offline-backup-azure-data-box/upload-public-key.png)

4. Na serveru otevřete registr zadáním **regedit** v okně spustit.

5. Přejděte do registru *Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider.* Klepněte pravým tlačítkem myši na **položku CloudBackupProvider**a přidejte novou hodnotu řetězce s názvem `AzureADAppCertThumbprint_<Azure User Id>`.

    >[!NOTE]
    > Pokud chcete získat ID uživatele Azure, proveďte jednu z těchto akcí:
    >
    >* Z PowerShellu připojeného k `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` Azure spusťte příkaz.
    > * Přejděte na cestu registru *Počítač\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup* s názvem *CurrentUserId*.

6. Klepněte pravým tlačítkem myši na řetězec přidaný v předchozím kroku a vyberte **příkaz Změnit**. V hodnotě zadejte kryptografický otisk certifikátu, který jste exportovali v kroku 2. Vyberte **OK**.

7. Chcete-li získat hodnotu kryptografického otisku, poklepejte na certifikát. Vyberte kartu **Podrobnosti** a posuňte se dolů, dokud se nezobrazí pole kryptografického otisku. Vyberte **Kryptografický otisk**a zkopírujte hodnotu.

    ![Pole kryptografického potisku certifikátu](./media/offline-backup-azure-data-box/thumbprint-field.png)

## <a name="questions"></a>Dotazy

Máte-li jakékoli dotazy nebo vysvětlení týkající [AskAzureBackupTeam@microsoft.com](mailto:AskAzureBackupTeam@microsoft.com)se jakýchkoli problémů, kterým čelíte, obraťte se na společnost .
