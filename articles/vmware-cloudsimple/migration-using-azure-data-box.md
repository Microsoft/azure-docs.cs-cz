---
title: Řešení Azure VMware podle CloudSimple – migrace pomocí Azure Data Box
description: Hromadné migrace dat do řešení Azure VMware pomocí CloudSimple pomocí Azure Data Box
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 5f19b98fbbbad2f43227bfa2f73eab47bf7b1699
ms.sourcegitcommit: 80da36d4df7991628fd5a3df4b3aa92d55cc5ade
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2019
ms.locfileid: "71817459"
---
# <a name="migrating-data-to-azure-vmware-solution-by-cloudsimple-using-azure-data-box"></a>Migrace dat do řešení Azure VMware pomocí CloudSimple pomocí Azure Data Box

Cloudové řešení Microsoft Azure Data Box umožňuje do Azure odeslat rychle, levně a bezpečně terabajty dat. Zabezpečený a rychlý přenos dat se zajišťuje zasláním speciálního zařízení úložiště Data Box. Každé zařízení úložiště má maximální využitelnou kapacitu úložiště 80 TB a do datového centra ho transportuje místní přepravce. Zařízení má robustní obal, který data během převozu chrání a zabezpečí.

Pomocí Azure Data Box můžete přenést Hromadná data VMware do privátního cloudu.  Data z místního prostředí vSphere se zkopírují do Data Box pomocí protokolu NFS.  Migrace hromadných dat zahrnuje kopírování virtuálních počítačů, konfigurací a přidružených dat k určitému bodu v čase do Data Box a odeslání do Azure.

V tomto článku se dozvíte o:

* Nastavuje se Azure Data Box.
* Kopírování dat z místního prostředí VMware do Data Box pomocí systému souborů NFS.
* Příprava na vrácení Azure Data Box.
* Připravte data objektu BLOB pro kopírování do řešení Azure VMware pomocí CloudSimple.
* Kopírování dat z Azure do privátního cloudu.

## <a name="scenarios"></a>Scénáře

Pro migraci hromadných dat by se měla použít Azure Data Box v následujících scénářích.

* Migrujte velké objemy dat z místního prostředí do řešení Azure VMware podle CloudSimple, které se použije jako rozdíly mezi základními a synchronizovanými sítěmi.
* Migrujte velké množství vypínání virtuálních počítačů (studené virtuální počítače).
* Migrujte data virtuálního počítače pro nastavení vývojových a testovacích prostředí.
* Migrujte velký počet šablon virtuálních počítačů, soubory ISO disky virtuálních počítačů.

## <a name="before-you-begin"></a>Než začnete

* Ověřte požadavky a [pořadí data box](../databox/data-box-deploy-ordered.md) z Azure Portal.  Během procesu objednávky musíte vybrat účet úložiště, který umožňuje úložiště objektů BLOB.  Jakmile Data Box obdržíte, připojte ho k místní síti a [nastavte data box](../databox/data-box-deploy-set-up.md) s IP adresou dosažitelnou ze sítě pro správu vSphere.

* Vytvořte virtuální síť a účet úložiště ve stejné oblasti Azure, kde se zřídí řešení Azure VMware podle CloudSimple.

* Vytvořte [připojení k virtuální síti Azure](cloudsimple-azure-network-connection.md) z vašeho privátního cloudu do virtuální sítě, ve které je vytvořený účet úložiště, pomocí kroků uvedených v článku [připojení Azure Virtual Network k CloudSimple pomocí ExpressRoute](virtual-network-connection.md) .

## <a name="set-up-azure-data-box-for-nfs"></a>Nastavení Azure Data Box pro systém souborů NFS

Připojte se k Azure Data Box místním webovým uživatelským rozhraním pomocí kroků uvedených v části **připojení k vašemu zařízení** v článku [kurz: kabel a připojení k Azure Data box](../databox/data-box-deploy-set-up.md).  Nakonfigurujte Data Box pro povolení přístupu k klientům NFS.

1. V místním webovém uživatelském rozhraní přejděte na stránku **Připojit a kopírovat**. V části **Nastavení systému souborů NFS** klikněte na **Přístup klientů systému souborů NFS**. 

    ![Konfigurace přístupu klientů systému souborů NFS 1](media/nfs-client-access.png)

2. Zadejte IP adresu VMware ESXi hostitelů a klikněte na **Přidat**. Přístup pro všechny hostitele v clusteru vSphere můžete nakonfigurovat opakováním tohoto kroku. Klikněte na **OK**.

    ![Konfigurace přístupu klientů systému souborů NFS 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené ve sdílených složkách objektů blob bloku a objektů blob stránky představují kontejnery, do kterých se data nahrávají jako objekty blob. Soubory nemůžete kopírovat přímo do složky *root* v účtu úložiště.

Ve sdílených složkách objektů blob bloku a objektů blob stránky jsou entitami první úrovně kontejnery a entitami druhé úrovně objekty blob. Ve sdílených složkách souborů Azure jsou entitami první úrovně sdílené složky a entitami druhé úrovně soubory.

Následující tabulka uvádí cestu UNC ke sdíleným složkám ve vašem Data Boxu a adresu URL cesty ke službě Azure Storage, ve které jsou data nahraná. Konečnou adresu URL cesty ke službě Azure Storage je možné odvodit z cesty UNC ke sdílené složce.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Objekty blob bloku Azure | <li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty blob stránky Azure  | <li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Azure Files       |<li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> K kopírování dat VMware použijte objekty blob bloku Azure.

## <a name="mount-nfs-share-as-a-datastore-on-on-premises-vcenter-cluster-and-copy-data"></a>Připojit sdílenou složku NFS jako úložiště dat na místním clusteru vCenter a kopírovat data

Sdílená složka NFS z vašich Azure Data Box musí být připojená jako úložiště dat na místním clusteru vCenter nebo VMware ESXi hostitel pro kopírování dat.  Po připojení se data dají zkopírovat do úložiště dat NFS.

1. Přihlaste se k místnímu serveru vCenter.

2. Klikněte pravým tlačítkem na **datové centrum**, vyberte **úložiště** Přidat klikněte na **nové úložiště dat** a klikněte na **Další** .

   ![Přidat nové úložiště dat](media/databox-migration-add-datastore.png)

3. V kroku 1 Průvodce přidáním úložiště dat vyberte typ **NFS**.

   ![Přidat nový typ úložiště dat](media/databox-migration-add-datastore-type.png)

4. V kroku 2 vyberte **systém souborů NFS 3** jako verze NFS a klikněte na **Další**.

   ![Přidat novou verzi systému souborů NFS úložiště](media/databox-migration-add-datastore-nfs-version.png)

5. V kroku 3 zadejte název úložiště dat, cestu a Server.  Pro server můžete použít IP adresu vašeho Data Box.  Cesta ke složce bude ve formátu `/<StorageAccountName_BlockBlob>/<ContainerName>/`.

   ![Přidat novou konfiguraci úložiště dat pro systém souborů NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. V kroku 4 Vyberte hostitele ESXi, kam chcete připojit úložiště dat, a klikněte na **Další**.  V clusteru vyberte možnost Všichni hostitelé a zajistěte migraci virtuálních počítačů.

   ![Přidat nové úložiště dat – vybrat hostitele](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. V kroku 5 zkontrolujte souhrn a klikněte na **Dokončit** .

## <a name="copy-data-to-data-box-nfs-datastore"></a>Kopírování dat do úložiště dat Data Box NFS

Virtuální počítače je možné migrovat nebo klonovat do nového úložiště dat.  Všechny nevyužité virtuální počítače, které je potřeba migrovat, se dají migrovat do úložiště dat Data Box NFS pomocí služby **Storage vMotion**.  Aktivní virtuální počítače lze **klonovat** do úložiště dat data box NFS.

* Identifikujte seznam virtuálních počítačů, které se dají **přesunout**.
* Identifikujte seznam virtuálních počítačů, které musí být **naklonovány**.

### <a name="move-virtual-machine-to-data-box-datastore"></a>Přesunout virtuální počítač do úložiště dat Data Box

1. Klikněte pravým tlačítkem na virtuální počítač, který chcete přesunout na Data Box úložiště dat, a vyberte **migrovat**.

    ![Migrovat virtuální počítač](media/databox-migration-vm-migrate.png)

2. Vyberte možnost **změnit úložiště pouze** pro typ migrace a klikněte na tlačítko **Další**.

    ![Migrovat virtuální počítač – jenom úložiště](media/databox-migration-vm-migrate-change-storage.png)

3. Jako cíl vyberte Data Box úložiště dat a klikněte na **Další**.

    ![Migrovat virtuální počítač – výběr úložiště dat](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Zkontrolujte informace a klikněte na **Dokončit**.

5. Opakujte kroky 1 až 4 pro ostatní virtuální počítače.

> [!TIP]
> Můžete vybrat několik virtuálních počítačů ve stejném stavu napájení (zapnuté nebo vypnuté) a hromadně je migrovat.

Úložiště pro virtuální počítač se migruje do úložiště dat NFS z Azure Data Box.  Po migraci všech virtuálních počítačů můžete vypnout technologii založenou na virtuálních počítačích v části Příprava na migraci dat do řešení Azure VMware.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-data-box-datastore"></a>Naklonování virtuálního počítače nebo šablony virtuálního počítače na Data Box úložiště dat

1. Klikněte pravým tlačítkem na virtuální počítač nebo na šablonu virtuálního počítače, kterou chcete klonovat.  Výběr **klonování**, **klonování do virtuálního počítače**

    ![Klon virtuálního počítače](media/databox-migration-vm-clone.png)

2. Vyberte název klonovaného virtuálního počítače nebo šablony virtuálního počítače.

3. Vyberte složku, kam chcete umístit Klonovaný objekt, a klikněte na tlačítko **Další**.

4. Vyberte cluster nebo fond zdrojů, ve kterém chcete klonovat objekt, a klikněte na **Další**.

5. Jako umístění úložiště vyberte úložiště dat NFS Azure Data Box a klikněte na **Další**.

    ![Klonování virtuálního počítače – výběr úložiště dat](media/databox-migration-vm-clone-select-datastore.png)

6. Vyberte možnosti přizpůsobení, pokud chcete přizpůsobit jakékoli možnosti pro Klonovaný objekt a klikněte na tlačítko **Další**.

7. Zkontrolujte konfigurace a klikněte na **Dokončit**.

8. Opakujte kroky 1 až 7 pro další virtuální počítače nebo šablony virtuálních počítačů.

Virtuální počítače budou naklonovány a uloženy v úložišti dat NFS z Azure Data Box.  Po naklonování virtuálních počítačů se ujistěte, že naklonované virtuální počítače jsou vypnuté při přípravě na migraci dat do řešení Azure VMware.

### <a name="copy-iso-files-to-data-box-datastore"></a>Zkopírování souborů ISO do Data Box úložiště dat

1. Z místního webového uživatelského rozhraní vCenter přejděte do **úložiště**.  Vyberte Data Box úložiště pro systém souborů NFS a klikněte na **soubory**.  Vytvoří **novou složku** pro ukládání souborů ISO.

    ![Kopírovat ISO – vytvořit novou složku](media/databox-migration-create-folder.png)

2. Zadejte název složky, do které budou uloženy soubory ISO.

3. Dvojím kliknutím na nově vytvořenou složku získáte přístup k obsahu.

4. Klikněte na **nahrát soubory** a vyberte soubory ISO, které chcete nahrát.
    
    ![Kopírování souborů pro nahrání ISO](media/databox-migration-upload-iso.png)

> [!TIP]
> Pokud již máte soubory ISO v místním úložišti dat, můžete vybrat soubory a **zkopírovat je do** úložiště data box NFS.


## <a name="prepare-azure-data-box-for-return"></a>Příprava Azure Data Box pro návrat

Po zkopírování všech dat virtuálních počítačů, dat šablony virtuálního počítače a všech souborů ISO do úložiště Data Box NFS úložiště se úložiště dat může odpojit od vCenter.  Aby bylo možné odpojit úložiště dat, musí být všechny virtuální počítače a šablony virtuálních počítačů odebrány z inventáře.

### <a name="remove-objects-from-inventory"></a>Odebrat objekty ze inventáře

1. Z místního webového uživatelského rozhraní vCenter přejděte do **úložiště**.  Vyberte Data Box úložiště pro systém souborů NFS a klikněte na **virtuální počítače**.

2. Ujistěte se, že jsou všechny virtuální počítače vypnuté.

    ![Odebrat virtuální počítače z inventáře – vypnuté](media/databox-migration-select-databox-vm.png)

3. Vyberte všechny virtuální počítače, klikněte pravým tlačítkem a vyberte **Odebrat ze inventáře**.

    ![Odebrat virtuální počítače ze inventáře](media/databox-migration-remove-vm-from-inventory.png)

4. Z horních tlačítek vyberte **šablony virtuálních počítačů ve složkách** a opakujte krok 3. 

### <a name="remove-azure-data-box-nfs-datastore-from-vcenter"></a>Odebrat úložiště dat Azure Data Box NFS z vCenter

Úložiště dat Data Box NFS se musí před přípravou pro návrat odpojit od VMware ESXi hostitelů.

1. Z místního webového uživatelského rozhraní vCenter přejděte do **úložiště**.

2. Klikněte pravým tlačítkem na úložiště dat Data Box NFS a vyberte **Odpojit úložiště dat**.

    ![Odpojit Data Box úložiště dat](media/databox-migration-unmount-datastore.png)

3. Vyberte všechny hostitele ESXi, kde je úložiště dat připojené, a klikněte na **OK**.

    ![Odpojení Data Box úložiště dat – vybrat hostitele](media/databox-migration-unmount-datastore-select-hosts.png)

4. Zkontrolujte a potvrďte všechna upozornění a klikněte na **OK**.

### <a name="prepare-data-box-for-return-and-return-the-data-box"></a>Příprava Data Box pro návrat a vrácení Data Box

Postupujte podle kroků uvedených v článku [vrácení Azure Data box a ověření, že se data nahrávají do Azure a](../databox/data-box-deploy-picked-up.md) vrátíte data box.  Zkontrolujte stav kopírování dat do účtu služby Azure Storage a jakmile se zobrazí stav dokončeno, můžete ověřit data v účtu úložiště Azure.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution-by-cloudsimple"></a>Kopírování dat z Azure Storage do Azure VMware řešení pomocí CloudSimple

Data zkopírovaná do vašich Azure Data Box budou k dispozici v účtu úložiště Azure, jakmile se stav objednávky vašeho Data Box ukáže jako dokončený.  Data se teď dají zkopírovat do vašeho řešení Azure VMware pomocí CloudSimple.  Data v účtu úložiště je nutné zkopírovat pomocí protokolu NFS do úložiště síti vSAN DataStore vašeho privátního cloudu.  Nejdřív zkopírujte data objektu BLOB Store do spravovaného disku na virtuálním počítači Linux v Azure pomocí **AzCopy**.  Zpřístupněte spravovaný disk prostřednictvím protokolu NFS a připojte sdílenou složku NFS jako úložiště dat ve vašem privátním cloudu a zkopírujte data.  Tato metoda umožňuje rychlejší kopírování dat do privátního cloudu. 

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-export-as-nfs-share"></a>Kopírovat data do privátního cloudu pomocí virtuálního počítače Linux a spravovaných disků a exportovat jako sdílenou složku NFS

1. Vytvořte [virtuální počítač pro Linux](../virtual-machines/linux/quick-create-portal.md) v Azure ve stejné oblasti, ve které je vytvořený účet úložiště, a virtuální síť Azure s připojením k privátnímu cloudu.

2. Vytvořte spravovaný disk, který je větší než množství dat objektu BLOB a [Připojte ho k virtuálnímu počítači se systémem Linux](../virtual-machines/linux/attach-disk-portal.md).  Pokud je objem dat objektu BLOB větší, než je největší dostupný spravovaný disk, musí být data zkopírována v několika krocích nebo pomocí více spravovaných disků.

3. Připojte se k virtuálnímu počítači se systémem Linux a připojte spravovaný disk.

4. Nainstalujte [AzCopy na virtuální počítač se systémem Linux](../storage/common/storage-use-azcopy-v10.md).

5. Stáhněte si data z úložiště objektů BLOB v Azure na spravovaný disk pomocí AzCopy.  Syntaxe příkazu: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`.  Nahraďte `<storage-account-name>` názvem svého účtu služby Azure Storage a `<container-name>` s kontejnerem, který obsahuje data zkopírovaná pomocí Azure Data Box.

6. Nainstalujte server NFS na virtuálním počítači se systémem Linux.

    1. Ubuntu/Debian rozdělení: `sudo apt install nfs-kernel-server`.
    2. V případě distribuce operačního systému Linux: `sudo yum install nfs-utils`.

7. Změňte oprávnění složky na spravovaném disku, kam se zkopírovala data z úložiště objektů BLOB v Azure.  Změňte oprávnění pro všechny složky, které chcete exportovat jako sdílenou složku NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Úpravou souboru `/etc/exports` přiřaďte oprávnění klientským IP adresám k přístupu ke sdílené složce systému souborů NFS.

    ```bash
    sudo vi /etc/exports
    ```
    
    Do souboru zadejte následující řádky pro každou IP adresu hostitele ESXi vašeho privátního cloudu.  Pokud vytváříte sdílené složky pro více složek, přidejte všechny složky.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exportujte sdílené složky systému souborů NFS pomocí příkazu `sudo exportfs -a`.

10. Restartujte server jádra systému souborů NFS pomocí příkazu `sudo systemctl restart nfs-kernel-server`.


### <a name="mount-linux-virtual-machine-nfs-share-as-a-datastore-on-private-cloud-vcenter-cluster-and-copy-data"></a>Připojit sdílenou složku Linux Virtual Machine NFS jako úložiště dat v clusteru vCenter privátního cloudu a kopírovat data

Sdílená složka NFS z virtuálního počítače se systémem Linux musí být připojena jako úložiště dat v clusteru vCenter privátního cloudu pro kopírování dat.  Po připojení se data dají zkopírovat z úložiště dat NFS do úložiště dat síti vSAN privátního cloudu.

1. Přihlaste se ke svému privátnímu cloudu vCenter Server.

2. Klikněte pravým tlačítkem na **datové centrum**, vyberte **úložiště** Přidat klikněte na **nové úložiště dat** a klikněte na **Další** .

   ![Přidat nové úložiště dat](media/databox-migration-add-datastore.png)

3. V kroku 1 Průvodce přidáním úložiště dat vyberte typ **NFS**.

   ![Přidat nový typ úložiště dat](media/databox-migration-add-datastore-type.png)

4. V kroku 2 vyberte **systém souborů NFS 3** jako verze NFS a klikněte na **Další**.

   ![Přidat novou verzi systému souborů NFS úložiště](media/databox-migration-add-datastore-nfs-version.png)

5. V kroku 3 zadejte název úložiště dat, cestu a Server.  Pro server můžete použít IP adresu vašeho virtuálního počítače se systémem Linux.  Cesta ke složce bude ve formátu `/<folder>/<subfolder>/`.

   ![Přidat novou konfiguraci úložiště dat pro systém souborů NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. V kroku 4 Vyberte hostitele ESXi, kam chcete připojit úložiště dat, a klikněte na **Další**.  V clusteru vyberte možnost Všichni hostitelé a zajistěte migraci virtuálních počítačů.

   ![Přidat nové úložiště dat – vybrat hostitele](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. V kroku 5 zkontrolujte souhrn a klikněte na **Dokončit** .

### <a name="add-virtual-machines-and-virtual-machine-templates-from-nfs-datastore-to-the-inventory"></a>Přidání virtuálních počítačů a šablon virtuálních počítačů z úložiště pro systém souborů NFS do inventáře

1. Z webového uživatelského rozhraní vCenter vašeho privátního cloudu přejděte do **úložiště**.  Vyberte Linux Virtual Machine NFS úložiště úložiště a klikněte na **soubory**.

    ![Vybrat soubory z úložiště dat NFS](media/databox-migration-datastore-select-files.png)

2. Vyberte složku, která obsahuje virtuální počítač nebo šablonu virtuálního počítače.  V podokně podrobností vyberte soubor `.vmx` pro virtuální počítač nebo soubor `.vmtx` pro šablonu virtuálního počítače.

3. Kliknutím na **zaregistrovat** virtuální počítač zaregistrujete virtuální počítač do svého privátního cloudu vCenter.

    ![Registrovat virtuální počítač](media/databox-migration-datastore-register-vm.png)

4. Vyberte datové centrum, složku a fond prostředků, ve kterém chcete virtuální počítač zaregistrovat.

4. Zopakujte kroky 3 a 4 pro všechny virtuální počítače a šablony virtuálních počítačů.

5. Přejděte do složky, která obsahuje soubory ISO.  Vyberte soubory ISO a **zkopírujte je do** složky v úložišti dat síti vSAN.

Virtuální počítače a šablony virtuálních počítačů jsou teď dostupné ve vašem privátním cloudu vCenter.  Tyto virtuální počítače musí být před tím, než je zapnete, přesunuty z úložiště dat NFS do úložiště dat síti vSAN.  Můžete provést vMotion úložiště virtuálních počítačů a vybrat úložiště dat síti vSAN jako cíl pro virtuální počítač.

Šablony virtuálních počítačů musí být klonovány z úložiště dat pro Linux Virtual Machine NFS do úložiště dat síti vSAN.

### <a name="clean-up-of-your-linux-virtual-machine"></a>Vyčištění virtuálního počítače se systémem Linux

Jakmile se všechna data zkopírují do privátního cloudu, můžete z vašeho privátního cloudu odebrat úložiště dat NFS.

1. Zajistěte, aby všechny virtuální počítače a šablony byly přesunuty a naklonovány do úložiště dat síti vSAN.

2. Odeberte všechny šablony virtuálních počítačů z úložiště dat NFS z inventáře.

3. Odpojte úložiště dat virtuálního počítače se systémem Linux z vašeho privátního cloudu vCenter.

4. Odstraňte virtuální počítač a spravovaný disk z Azure.

5. Pokud nechcete data přenášet pomocí Azure Data Box v účtu úložiště, odstraňte účet Azure Storage.  
    


## <a name="next-steps"></a>Další kroky

* Další informace o [Azure Data box](../databox/data-box-overview.md)
* Další informace o různých možnostech [migrace úloh do privátního cloudu](migrate-workloads.md)
