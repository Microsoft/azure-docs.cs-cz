---
title: Offline zálohování s Azure Data Box pro DPM a MABS
description: Azure Data Box můžete použít k počátečnímu počátečnímu zálohování dat v režimu offline z aplikace DPM a MABS.
ms.topic: conceptual
ms.date: 08/12/2020
ms.openlocfilehash: 1cfd9131099ad6a8ccd3d43e93f3d97641514f03
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "96752545"
---
# <a name="offline-seeding-using-azure-data-box-for-dpm-and-mabs-preview"></a>Offline osazení pomocí Azure Data Box pro DPM a MABS (Preview)

> [!NOTE]
> Tato funkce je k dispozici pro Data Protection Manager (DPM) 2019 UR2 a novější.<br><br>
> Tato funkce je aktuálně ve verzi Preview pro Microsoft Azure Backup Server (MABS). Pokud vás zajímá použití Azure Data Box pro online osazení s využitím MABS, můžete nás kontaktovat na adrese [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com) .

V tomto článku se dozvíte, jak můžete pomocí Azure Data Box naplnit počáteční zálohovaná data offline z aplikace DPM a MABS do trezoru služby Azure Recovery Services.

[Azure Data box](../databox/data-box-overview.md) můžete použít k osazení velkých počátečních a MABS záloh aplikace DPM v režimu offline (bez použití sítě) do trezoru Recovery Services. Tento proces šetří čas a šířku pásma sítě, které by jinak využily přesun velkých objemů zálohovaných dat online přes síť s vysokou latencí. Tato funkce je aktuálně ve verzi Preview.

Offline zálohování na základě Azure Data Box poskytuje dvě různé výhody oproti [zálohování offline založené na službě Azure import/export](backup-azure-backup-server-import-export.md):

- Nemusíte si vystarat vlastní disky a konektory kompatibilní s Azure. Azure Data Box dodávající disky přidružené k vybrané [SKU data box](https://azure.microsoft.com/services/databox/data/).

- Azure Backup (agent MARS) může přímo zapisovat data zálohy na podporované SKU Azure Data Box. Tato funkce eliminuje nutnost zřídit pracovní umístění pro počáteční data záloh. Nepotřebujete také nástroje pro formátování a kopírování těchto dat na disky.

## <a name="supported-platforms"></a>Podporované platformy

Podporují se tyto platformy:

- Windows Server 2019 64 bit (Standard, Datacenter, Essentials)
- Windows Server 2016 64 bit (Standard, Datacenter, Essentials)

## <a name="backup-data-size-and-supported-data-box-skus"></a>Velikost dat zálohy a podporované Data Box SKU

Podporují se následující Data Box SKU:

| Velikost dat zálohy (po kompresi pomocí MARS) \* na server | Podporovaná Azure Data Box SKU |
| --- | --- |
| \<= 7,2 TB | [Azure Data Box Disk](../databox/data-box-disk-overview.md) |
| > 7,2 TB a <= 80 TB\*\* | [Azure Data Box (100 TB)](../databox/data-box-overview.md) |

\*Typické kompresní frekvence se mezi 10-20% liší. <br>
\*\*[SystemCenterFeedback@microsoft.com](mailto:SystemCenterFeedback@microsoft.com)Pokud očekáváte, že budete mít více než 80 TB počátečních dat zálohování pro jeden zdroj dat, můžete se obrátit na.

> [!IMPORTANT]
> Počáteční data zálohy z jednoho zdroje dat musí být obsažena v rámci jednoho Azure Data Box nebo Azure Data Box disku a nemohou být sdílena mezi více zařízeními stejné nebo jiné SKU. Azure Data Box však může obsahovat počáteční zálohy z více zdrojů dat.

## <a name="before-you-begin"></a>Než začnete

Agent MARS běžící v DPM/MABS by měl být upgradován na [nejnovější verzi](https://aka.ms/azurebackup_agent) (2.0.9171.0 nebo novější).

Zajistěte, aby:

### <a name="azure-subscription-and-required-permissions"></a>Předplatné Azure a požadovaná oprávnění

- Platné předplatné Azure.
- Uživatel, který má provést zásady zálohování offline, musí být vlastníkem předplatného Azure.
- Data Box úlohy a trezor Recovery Services, do kterých musí být data odsazený, musí být k dispozici ve stejných předplatných.
    > [!NOTE]
    > Doporučujeme, aby byl cílový účet úložiště a Recovery Servicesý trezor ve stejné oblasti. To ale není povinné.

### <a name="order-and-receive-the-data-box-device"></a>Objednat a přijmout Data Box zařízení

Před aktivací offline zálohování zajistěte, aby byla požadovaná zařízení Data Box v *doručeném* stavu. V tématu [Velikost zálohovaných dat a podporovaných data box SKU](#backup-data-size-and-supported-data-box-skus) můžete seřadit nejvhodnější SKU podle vašich požadavků. Podle kroků v [tomto článku](../databox/data-box-disk-deploy-ordered.md) seřiďte a dodržujte zařízení data box.

> [!IMPORTANT]
> Pro **druh účtu** nevybírejte *BlobStorage* . Server DPM/MABS vyžaduje účet, který podporuje objekty blob stránky, které nejsou podporované, když je vybraná možnost *BlobStorage* . Jako **druh účtu** při vytváření cílového účtu úložiště pro úlohu Azure Data box vyberte **úložiště v2 (obecné účely v2)** .

![Nastavení Azure Databox](./media/offline-backup-azure-data-box-dpm-mabs/setup-azure-databox.png)

## <a name="setup-azure-data-box-devices"></a>Nastavení Azure Data Box zařízení

Jakmile obdržíte Azure Data Box zařízení, v závislosti na Azure Data Box SKU, kterou jste objednali, proveďte kroky v příslušných částech níže, abyste nastavili a připravili zařízení Data Box pro server DPM/MABS a identifikovali a přenesli počáteční data zálohy.

### <a name="setup-azure-data-box-disk"></a>Instalační Azure Data Box disk

Pokud jste si objednali jeden nebo více Azure Data Box disků (každý až 8 TB), postupujte podle kroků uvedených [tady](../databox/data-box-disk-deploy-set-up.md) a rozbalte tak data box disk, připojte se a odemkněte.

> [!NOTE]
> Je možné, že server DPM/MABS nemá port USB. V takovém scénáři můžete Azure Data Box disk připojit k jinému serveru nebo klientovi a zveřejnit kořen zařízení jako sdílenou síťovou složku.

## <a name="setup-azure-data-box"></a>Instalační Azure Data Box

Pokud jste objednali Azure Data Box (až 100 TB), postupujte podle kroků uvedených [tady](../databox/data-box-deploy-set-up.md) a nastavte data box.

### <a name="mount-your-azure-data-box-as-local-system"></a>Připojit Azure Data Box jako místní systém

Server DPM/MABS funguje v kontextu systému, takže vyžaduje, aby byla k dispozici stejná úroveň oprávnění pro cestu k připojení, kde je Azure Data Box připojená. Postupujte podle následujících kroků a ujistěte se, že zařízení Data Box můžete připojit jako místní systém pomocí protokolu NFS.

1. Povolte funkci klient pro systém souborů NFS na serveru DPM nebo MABS.
Zadejte alternativní zdroj: *WIM: D: \zdroje\install.wim: 4*
2. Stáhněte si **PsExec** ze [https://download.sysinternals.com/files/PSTools.zip](https://download.sysinternals.com/files/PSTools.zip) serveru DPM/MABS.
3. Otevřete příkazový řádek se zvýšenými oprávněními a spusťte následující příkaz s adresářem, který obsahuje *PSExec.exe* jako aktuální adresář.

   ```cmd
   psexec.exe  -s  -i  cmd.exe
   ```

4. Příkazové okno, které se otevře v důsledku výše uvedeného příkazu, je v kontextu místního systému. Pomocí tohoto příkazového okna můžete provést kroky pro připojení sdílené složky Azure Page BLOB jako síťové jednotky na Windows serveru.
5. Postupujte podle kroků uvedených [tady](../databox/data-box-deploy-copy-data-via-nfs.md#connect-to-data-box) a připojte svůj server DPM/MABS k zařízení data box přes systém souborů NFS a spusťte následující příkaz na příkazovém řádku místního systému pro připojení sdílené složky objektů blob stránky Azure:

    ```cmd
    mount -o nolock \\<DeviceIPAddres>\<StorageAccountName_PageBlob X:
    ```

6. Po připojení ověřte, jestli máte přístup k X: z vašeho serveru. Pokud můžete pokračovat v další části tohoto článku.

## <a name="transfer-initial-backup-data-to-azure-data-box-devices"></a>Přenos počátečních zálohovaných dat do zařízení Azure Data Box

1. Na serveru DPM nebo MABS postupujte podle pokynů pro [Vytvoření nové skupiny ochrany](/system-center/dpm/create-dpm-protection-groups). Pokud přidáváte online ochranu do existující skupiny ochrany, klikněte pravým tlačítkem na existující skupinu ochrany a vyberte **Přidat online ochranu** a začněte od **kroku 8**.
2. Na stránce **Vybrat členy skupiny** zadejte počítače a zdroje, které chcete zálohovat.
3. Na stránce **Vybrat způsob ochrany dat** určete způsob zpracování krátkodobého a dlouhodobého zálohování. Ujistěte se, že jste vybrali možnost **Chci online ochranu.**

   ![Vytvořit novou skupinu ochrany](./media/offline-backup-azure-data-box-dpm-mabs/create-new-protection-group.png)

4. Na stránce **Vybrat krátkodobé cíle** určete, jak se má na disk zálohovat do krátkodobého úložiště.
5. Na stránce **zkontrolovat přidělení disku** zkontrolujte přidělené místo na disku fondu úložiště pro skupinu ochrany.
6. Na stránce **Vybrat způsob vytvoření repliky** vyberte **automaticky přes síť.**
7. Na stránce **Vybrat možnosti kontroly konzistence** vyberte, jak chcete automatizovat kontroly konzistence.
8. Na stránce **zadat data online ochrany** vyberte člen, který chcete povolit online ochranu.

    ![Zadat data online ochrany](./media/offline-backup-azure-data-box-dpm-mabs/specify-online-protection-data.png)

9. Na stránce **zadat plán online zálohování** určete, jak často se má provést přírůstkové zálohování do Azure.
10. Na stránce **zadat zásady uchovávání online** určete, jak se mají v Azure uchovávat body obnovení vytvořené z denních, týdenních nebo měsíčních záloh.
11. Na obrazovce **Zvolte online replikaci** v průvodci zvolte možnost **přenos pomocí disků vlastněných společností Microsoft** a vyberte možnost **Další**.

    ![Zvolit úvodní online replikaci](./media/offline-backup-azure-data-box-dpm-mabs/choose-initial-online-replication.png)

    >[!NOTE]
    > Možnost výběru **přenosu pomocí disků vlastněných společností Microsoft** není pro MABS V3 k dispozici, protože tato funkce je ve verzi Preview. [systemcenterfeedback@microsoft.com](mailto:systemcenterfeedback@microsoft.com)Pokud chcete používat tuto funkci pro MABS v3, dostanete se na nás.

12. Přihlaste se k Azure po zobrazení výzvy pomocí přihlašovacích údajů uživatele, které mají oprávnění vlastníka v předplatném Azure. Po úspěšném přihlášení se zobrazí následující obrazovka:

    ![Po úspěšném přihlášení](./media/offline-backup-azure-data-box-dpm-mabs/after-successful-login.png)

     Server DPM/MABS následně načte úlohy Data Box v předplatném, které jsou v *doručeném* stavu.

     > [!NOTE]
     > Při prvním přihlášení trvá déle než obvykle. Modul Azure PowerShell se nainstaluje na pozadí a taky je zaregistrovaná aplikace Azure AD.
     >
     >  - Jsou nainstalovány následující moduly prostředí PowerShell:<br>
          – AzureRM. Profile     *5.8.3*<br>
          – AzureRM. Resources   *6.7.3*<br>
          – AzureRM. Storage     *5.2.0*<br>
          – Azure. Storage       *4.6.1*<br>
     >  - Aplikace Azure AD je registrovaná jako *AzureOfflineBackup_ \<object GUID of the user>*.

13. Vyberte správné pořadí datových polí, pro které jste nebaleni, připojili a odemkli Data Box disk. Vyberte **Další**.

    ![Vybrat Databox](./media/offline-backup-azure-data-box-dpm-mabs/select-databox.png)

14. Na obrazovce **zjistit Databox** zadejte cestu k vašemu zařízení data box a pak vyberte **rozpoznat zařízení**.

    ![Zadat síťovou cestu](./media/offline-backup-azure-data-box-dpm-mabs/enter-network-path.png)

    > [!IMPORTANT]
    > Zadejte síťovou cestu ke kořenovému adresáři Azure Data Boxho disku. Tento adresář musí obsahovat adresář s názvem *PageBlob* , jak je znázorněno níže:
    >
    > ![Jednotka USB](./media/offline-backup-azure-data-box-dpm-mabs/usb-drive.png)
    >
    > Pokud je například cesta k disku `\\mydomain\myserver\disk1\` a *Disk1* obsahuje adresář s názvem *PageBlob*, cesta, která se má zadat v průvodci DPM/MABS Server, je `\\mydomain\myserver\disk1\` .
    > Pokud [nastavili Azure Data Box 100 TB zařízení](./offline-backup-azure-data-box.md#set-up-azure-data-box), zadejte jako síťovou cestu k zařízení následující `\\<DeviceIPAddress>\<StorageAccountName>_PageBlob` .

15. Vyberte **Další**. Na stránce **Souhrn** zkontrolujte nastavení a vyberte **vytvořit skupinu**.

    ![Zjistit Databox](./media/offline-backup-azure-data-box-dpm-mabs/detect-databox.png)

    Na následující obrazovce se potvrdí, že se skupina ochrany úspěšně vytvořila.

    ![Skupina ochrany se vytvořila.](./media/offline-backup-azure-data-box-dpm-mabs/protection-group-created.png)

16. Na obrazovce výše vyberte **Zavřít** .

    V takovém případě se počáteční replikace dat projeví na disku DPM/MABS. Po dokončení ochrany se ve stavu skupiny na stránce **ochrana** zobrazí stav ochrany jako v **pořádku** .

17. Chcete-li spustit offline a záložní kopii na zařízení Azure Data Box, klikněte pravým tlačítkem na **skupinu ochrany** a potom zvolte možnost **vytvořit bod obnovení** . Pak zvolíte možnost **Online ochrany** .

    ![Vytvořit bod obnovení](./media/offline-backup-azure-data-box-dpm-mabs/create-recovery-point.png)

    ![Bod obnovení](./media/offline-backup-azure-data-box-dpm-mabs/recovery-point.png)

   Server DPM/MABS spustí zálohování dat, která jste vybrali do zařízení Azure Data Box. To může trvat několik hodin až několik dní v závislosti na velikosti dat a rychlosti připojení mezi serverem DPM/MABS a Azure Data Box Disk.

   Stav úlohy můžete sledovat v podokně **monitorování** . Po dokončení zálohování dat se zobrazí obrazovka podobná této:

   ![Konzola správce](./media/offline-backup-azure-data-box-dpm-mabs/administrator-console.png)

## <a name="post-backup-steps"></a>Kroky po zálohování

Po úspěšném zálohování dat do Azure Data Box Disk postupujte podle těchto kroků.

- Pomocí kroků v [tomto článku](../databox/data-box-disk-deploy-picked-up.md) dodáte Azure Data box disk do Azure. Pokud jste použili Azure Data Box 100 TB, dokončete Azure Data Box do Azure pomocí [těchto kroků](../databox/data-box-deploy-picked-up.md) .
- [Monitorujte úlohu data box](../databox/data-box-disk-deploy-upload-verify.md) v Azure Portal. Po *dokončení* úlohy Azure Data box server DPM/MABS automaticky přesune data z účtu úložiště do trezoru Recovery Services v době příštího naplánovaného zálohování. Po úspěšném vytvoření bodu obnovení bude pak označovat úlohu zálohování jako *dokončenou úlohu* .

  > [!NOTE]
  > Server DPM/MABS spustí zálohy v časech naplánovaných během vytváření skupiny ochrany. Tyto úlohy však označí čekání na *dokončení Azure Data box úlohy* až do doby, kdy je úloha dokončena.

- Po úspěšném vytvoření bodu obnovení, který odpovídá počátečnímu zálohování, pak server DPM/MABS odstraní účet úložiště (nebo konkrétní obsah) přidružený k úloze Azure Data Box.

## <a name="troubleshooting"></a>Poradce při potížích

Agent Microsoft Azure Backup (MAB) na serveru DPM vytvoří pro vás aplikaci Azure AD ve vašem tenantovi. Tato aplikace vyžaduje certifikát pro ověřování, který se vytvoří a nahraje při konfiguraci zásad pro dosazení hodnot do režimu offline.

Pro vytvoření a nahrání certifikátu do aplikace Azure AD používáme Azure PowerShell.

### <a name="issue"></a>Problém

V době konfigurace offline zálohování z důvodu vady známého kódu v rutině Azure PowerShell nemůžete přidat více certifikátů do stejné aplikace Azure AD vytvořené agentem MAB. To bude mít vliv na to, jestli jste pro stejný nebo jiný server nakonfigurovali zásady osazení offline.

### <a name="verify-if-the-issue-is-caused-by-this-specific-root-cause"></a>Ověřte, jestli problém způsobuje tato konkrétní hlavní příčina.

Chcete-li zajistit, aby chyba byla způsobena [problémem](#issue) výše, proveďte jeden z následujících kroků:

#### <a name="step-1"></a>Krok 1

Zkontrolujte, jestli se v konzole DPM/MABS v době konfigurace offline zálohování zobrazuje následující chybová zpráva:

![Agent služeb zotavení Azure](./media/offline-backup-azure-data-box-dpm-mabs/azure-recovery-services-agent.png)

#### <a name="step-2"></a>Krok 2

1. Otevřete složku **TEMP** v instalační cestě (výchozí cesta k dočasné složce je *C:\Program Files\Microsoft Azure Recovery Services Agent\Temp*. Vyhledejte soubor *CBUICurr* a otevřete soubor.
2. V souboru *CBUICurr* se posuňte na poslední řádek a ověřte, jestli nedošlo k chybě kvůli neúspěšnému vytvoření přihlašovacích údajů aplikace Azure AD v účtu zákazníka. Výjimka: aktualizace stávajícího pověření s KeyId \<some guid> není povolena.

### <a name="workaround"></a>Alternativní řešení

Chcete-li vyřešit tento problém, proveďte následující kroky a opakujte konfiguraci zásad.

1. Přihlaste se na přihlašovací stránku Azure, která se zobrazí v uživatelském rozhraní serveru DPM nebo MABS pomocí jiného účtu s přístupem správce k předplatnému, které bude mít vytvořenou úlohu Data Box.
2. Pokud na žádném jiném serveru není nakonfigurované počáteční nastavování offline a na aplikaci není závislý žádný jiný server `AzureOfflineBackup_<Azure User Id>` , odstraňte tuto aplikaci z **Azure Portal > Azure Active Directory > registrace aplikací**.

   > [!NOTE]
   > Ověřte, zda aplikace nemá `AzureOfflineBackup_<Azure User Id>` nakonfigurované žádné jiné počáteční hodnoty pro offline a zda na této aplikaci není závislý žádný jiný server. V části veřejné klíče přejdete na **nastavení > klíče** . Neměl by mít přidané žádné jiné **veřejné klíče** . Referenční informace najdete na následujícím snímku obrazovky:
   >
   > ![Veřejné klíče](./media/offline-backup-azure-data-box-dpm-mabs/public-keys.png)

#### <a name="step-3"></a>Krok 3

Ze serveru DPM nebo MABS, který se pokoušíte nakonfigurovat offline zálohování, proveďte následující akce:

1. Otevřete kartu **Správa aplikace certifikát počítače**  >  **osobní** a vyhledejte certifikát s názvem `CB_AzureADCertforOfflineSeeding_<ResourceId>` .
2. Vyberte certifikát výše, klikněte pravým tlačítkem na **všechny úlohy** a **exportujte** bez privátního klíče ve formátu. cer.
3. Přejděte na aplikaci Azure offline Backup uvedenou v **bodě 2**. V části **Nastavení**  >  **klíče**  >  **nahrávání veřejného klíče** Nahrajte certifikát exportovaný v kroku výše.

   ![Odeslat veřejné klíče](./media/offline-backup-azure-data-box-dpm-mabs/upload-public-keys.png)

4. Na serveru otevřete registr zadáním příkazu **Regedit** v okně **Spustit** .
5. Přejít do registru *Computer\HKEY \_ Local \_ MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Config\CloudBackupProvider*. Klikněte pravým tlačítkem na **CloudBackupProvider** a přidejte novou řetězcovou hodnotu s názvem `AzureADAppCertThumbprint_<Azure User Id>` .

    >[!NOTE]
    > Pokud chcete získat ID uživatele Azure, proveďte jednu z následujících akcí:
    >
    >- Z PowerShellu připojeného k Azure spusťte `Get-AzureRmADUser -UserPrincipalName "Account Holder's email as defined in the portal"` příkaz.
    > - Přejděte do cesty k registru `Computer\HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\DbgSettings\OnlineBackup` s názvem *CurrentUserId*.

6. Klikněte pravým tlačítkem na řetězec přidaný v kroku výše a vyberte **změnit**. V poli hodnota zadejte kryptografický otisk certifikátu, který jste exportovali v **bodě 2** , a vyberte **OK**.
7. Pokud chcete získat hodnotu kryptografického otisku, poklikejte na certifikát a pak vyberte **Podrobnosti**  a posuňte se dolů, dokud se nezobrazí pole kryptografický otisk. Vyberte **kryptografický otisk** a zkopírujte hodnotu.

   ![Hodnota kryptografického otisku](./media/offline-backup-azure-data-box-dpm-mabs/certificate.png)

## <a name="next-steps"></a>Další kroky

- [Offline osazení pomocí vlastního disku (pomocí služby Import/export Azure)](backup-azure-backup-server-import-export.md)
