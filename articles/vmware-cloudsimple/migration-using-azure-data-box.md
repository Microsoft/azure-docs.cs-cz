---
title: Řešení Azure VMware – migrace pomocí Azure Data Box
description: Jak pomocí Azure Data Box hromadně migrovat data do řešení Azure VMware.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: ab772bd9cb415045ef70cb4cf9a518791befb192
ms.sourcegitcommit: 425420fe14cf5265d3e7ff31d596be62542837fb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/20/2021
ms.locfileid: "107741641"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migrace dat do řešení Azure VMware pomocí Azure Data Box

Cloudové řešení Microsoft Azure Data Box umožňuje odeslat terabajty (TBs) data do Azure rychlým, levným a spolehlivým způsobem. Zabezpečený a rychlý přenos dat se zajišťuje zasláním speciálního zařízení úložiště Data Box. Každé úložné zařízení má maximální možnou kapacitu úložiště 80 TB a při přenosu do vašeho datového centra prostřednictvím regionálního dopravce. Zařízení má robustní velká a malá písmena, která chrání a zabezpečují vaše data během přenosu.

Pomocí Data Box můžete hromadně migrovat data VMware do privátního cloudu. Data z místního prostředí VMware vSphere se zkopírují do Data Box prostřednictvím protokolu NFS (Network File System). Migrace hromadných dat zahrnuje ukládání virtuálních počítačů, konfigurací a přidružených dat v určitém bodě do Data Box a jejich ruční odeslání do Azure.

V tomto článku se dozvíte o:

* Nastavuje se Data Box.
* Kopírování dat z místního prostředí VMware do Data Box přes systém souborů NFS.
* Příprava na vrácení Data Box.
* Připravují se data objektu BLOB pro kopírování do řešení Azure VMware.
* Kopírování dat z Azure do privátního cloudu.

## <a name="scenarios"></a>Scénáře

Pro migraci hromadných dat použijte Data Box v následujících scénářích:

* Pro migraci velkého množství dat z místního prostředí do řešení Azure VMware. Tato metoda vytváří standardní hodnoty a synchronizuje rozdíly v síti.
* Pro migraci velkého počtu virtuálních počítačů, které jsou vypnuté (studené virtuální počítače).
* K migraci dat virtuálního počítače pro nastavení vývojových a testovacích prostředí.
* K migraci velkého počtu šablon virtuálních počítačů, souborů ISO a disků virtuálních počítačů.

## <a name="before-you-begin"></a>Než začnete

* Ověřte požadavky a [data box objednávek](../databox/data-box-deploy-ordered.md) prostřednictvím Azure Portal. Během procesu objednávky musíte vybrat účet úložiště, který povoluje úložiště objektů BLOB. Jakmile obdržíte zařízení Data Box, připojte ho k místní síti a [nastavte zařízení](../databox/data-box-deploy-set-up.md) na IP adresu, která je dosažitelná ze sítě pro správu vSphere.

* Vytvořte virtuální síť a účet úložiště ve stejné oblasti, ve které je zřízené vaše řešení Azure VMware.

* Vytvořte [připojení k virtuální síti Azure](cloudsimple-azure-network-connection.md) z privátního cloudu do virtuální sítě, ve které je vytvořený účet úložiště, podle kroků v části [připojení Azure Virtual Network k CloudSimple pomocí ExpressRoute](virtual-network-connection.md).

## <a name="set-up-data-box-for-nfs"></a>Nastavení Data Box pro systém souborů NFS

Pomocí postupu v části "připojení k vašemu zařízení" se připojte k Data Box místního webového uživatelského rozhraní [: kabel a připojte se k Azure Data box](../databox/data-box-deploy-set-up.md).  Nakonfigurujte Data Box pro povolení přístupu k klientům systému souborů NFS:

1. V místním webovém uživatelském rozhraní přejdete na stránku **připojit a kopírovat** . V části **nastavení systému souborů NFS** vyberte **přístup klienta NFS**. 

    ![Konfigurace přístupu klientů systému souborů NFS 1](media/nfs-client-access.png)

2. Zadejte IP adresu VMware ESXi hostitelů a vyberte **Přidat**. Přístup pro všechny hostitele v clusteru vSphere můžete nakonfigurovat opakováním tohoto kroku. Vyberte **OK**.

    ![Konfigurace přístupu klientů systému souborů NFS 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené ve sdílených složkách objektů blob bloku a objektů blob stránky představují kontejnery, do kterých se data nahrávají jako objekty blob. Soubory nemůžete kopírovat přímo do *kořenové* složky v účtu úložiště.

Ve sdílených složkách objektů blob bloku a objektů blob stránky jsou entitami první úrovně kontejnery a entitami druhé úrovně objekty blob. V části sdílené složky pro soubory Azure jsou entity nejvyšší úrovně sdílenými složkami a entitami druhé úrovně jsou soubory.

Následující tabulka uvádí cestu UNC ke sdíleným složkám ve vašem Data Boxu a adresu URL cesty ke službě Azure Storage, ve které jsou data nahraná. Konečnou adresu URL cesty ke službě Azure Storage je možné odvodit z cesty UNC ke sdílené složce.
 
| Objekty blob a soubory | Cesta a adresa URL |
|---------------- | ------------ |
| Objekty blob bloku Azure | <li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty blob stránky Azure  | <li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Soubory Azure       |<li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> K kopírování dat VMware použijte objekty blob bloku Azure.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Připojte sdílenou složku NFS jako úložiště dat na místním clusteru vCenter a zkopírujte data.

Sdílená složka NFS z vašich Data Box musí být připojená jako úložiště dat na místním clusteru vCenter nebo VMware ESXi hostitele, aby bylo možné zkopírovat data do úložiště dat NFS:

1. Přihlaste se k místnímu serveru vCenter.

2. Klikněte pravým tlačítkem myši na **datové centrum**, vyberte **úložiště**, vyberte **nové úložiště dat** a pak vyberte **Další**.

   ![Přidat nové úložiště dat](media/databox-migration-add-datastore.png)

3. V kroku 1 Průvodce přidáním úložiště dat vyberte v části **typ** možnost **NFS** .

   ![Přidat nový typ úložiště dat](media/databox-migration-add-datastore-type.png)

4. V kroku 2 průvodce vyberte jako verzi systému souborů NFS **3 systém souborů** NFS a pak vyberte **Další**.

   ![Přidat novou verzi systému souborů NFS úložiště](media/databox-migration-add-datastore-nfs-version.png)

5. V kroku 3 průvodce zadejte název úložiště dat, cestu a Server. Pro server můžete použít IP adresu vašeho Data Box. Cesta ke složce bude ve `/<StorageAccountName_BlockBlob>/<ContainerName>/` formátu.

   ![Přidat novou konfiguraci úložiště dat pro systém souborů NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. V kroku 4 průvodce vyberte hostitele ESXi, kam chcete připojit úložiště dat, a pak vyberte **Další**.  V clusteru vyberte všechny hostitele, aby se zajistila migrace virtuálních počítačů.

   ![Přidat nové úložiště dat – vybrat hostitele](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. V kroku 5 průvodce zkontrolujte souhrn a vyberte **Dokončit**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Kopírovat data do úložiště dat Data Box NFS

Virtuální počítače je možné migrovat nebo klonovat do nového úložiště dat.  Všechny nevyužité virtuální počítače, které chcete migrovat, se dají migrovat do úložiště dat Data Box NFS pomocí možnosti **úložiště vMotion** . Aktivní virtuální počítače lze klonovat do úložiště dat Data Box NFS.

* Identifikujte a vypíšete seznam virtuálních počítačů, které se dají **přesunout**.
* Identifikujte a vypíšete virtuální počítače, které musí být **naklonovány**.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Přesunutí virtuálního počítače do úložiště dat Data Box

1. Klikněte pravým tlačítkem myši na virtuální počítač, který chcete přesunout do úložiště dat Data Box, a pak vyberte **migrovat**.

    ![Migrovat virtuální počítač](media/databox-migration-vm-migrate.png)

2. Vyberte možnost **změnit úložiště pouze** pro typ migrace a poté vyberte možnost **Další**.

    ![Migrovat virtuální počítač – jenom úložiště](media/databox-migration-vm-migrate-change-storage.png)

3. Jako cíl vyberte **Databox a úložiště dat** a pak vyberte **Další**.

    ![Migrovat virtuální počítač – výběr úložiště dat](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Zkontrolujte informace a vyberte **Dokončit**.

5. Opakujte kroky 1 až 4 pro další virtuální počítače.

> [!TIP]
> Můžete vybrat několik virtuálních počítačů, které jsou ve stejném stavu napájení (zapnuté nebo vypnuté), a hromadně je migrovat.

Virtuální počítač se migruje do úložiště dat NFS z Data Box. Po migraci všech virtuálních počítačů můžete vypnout (vypnout) aktivní virtuální počítače v části Příprava na migraci dat do řešení Azure VMware.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Naklonování virtuálního počítače nebo šablony virtuálního počítače do úložiště dat Data Box

1. Klikněte pravým tlačítkem na virtuální počítač nebo na šablonu virtuálního počítače, kterou chcete klonovat. Vyberte **klonovat**  >  **klon na virtuálním počítači**.

    ![Klon virtuálního počítače](media/databox-migration-vm-clone.png)

2. Vyberte název klonovaného virtuálního počítače nebo šablony virtuálního počítače.

3. Vyberte složku, do které chcete uložit Klonovaný objekt, a pak vyberte **Další**.

4. Vyberte cluster nebo fond zdrojů, kam chcete vložit Klonovaný objekt, a pak vyberte **Další**.

5. Jako umístění úložiště vyberte **Databox (úložiště dat** ) a pak vyberte **Další**.

    ![Klonování virtuálního počítače – výběr úložiště dat](media/databox-migration-vm-clone-select-datastore.png)

6. Pokud chcete přizpůsobit jakékoli možnosti pro Klonovaný objekt, vyberte možnosti vlastního nastavení a pak vyberte **Další**.

7. Zkontrolujte konfigurace a vyberte **Dokončit**.

8. Opakujte kroky 1 až 7 pro další virtuální počítače nebo šablony virtuálních počítačů.

Virtuální počítače budou naklonovány a uloženy v úložišti dat NFS z Data Box. Po naklonování virtuálních počítačů se ujistěte, že jsou vypnuté při přípravě na migraci dat do řešení Azure VMware.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Kopírovat soubory ISO do úložiště Data Box dat

1. Z místního webového uživatelského rozhraní vCenter přejít do **úložiště**.  Vyberte **Databox-DataStore** a pak vyberte **soubory**. Vytvoří novou složku pro ukládání souborů ISO.

    ![Kopírovat ISO – vytvořit novou složku](media/databox-migration-create-folder.png)

2. Zadejte název složky, do které budou uloženy soubory ISO.

3. Dvakrát klikněte na nově vytvořenou složku a otevřete ji.

4. Vyberte **Odeslat soubory** a pak vyberte soubory ISO, které chcete nahrát.
    
    ![Kopírování souborů pro nahrání ISO](media/databox-migration-upload-iso.png)

> [!TIP]
> Pokud již máte soubory ISO v místním úložišti dat, můžete vybrat soubory a **zkopírovat** soubory do a zkopírovat je do úložiště dat NFS data box.


## <a name="prepare-data-box-for-return"></a>Příprava Data Box pro návrat

Po zkopírování všech dat virtuálního počítače, dat šablony virtuálního počítače a všech souborů ISO do úložiště Data Box NFS se systémem souborů NFS můžete odpojit úložiště dat z vCenter. Před odpojením úložiště dat musí být všechny virtuální počítače a šablony virtuálních počítačů odebrány z inventáře.

### <a name="remove-objects-from-inventory"></a>Odebrat objekty ze inventáře

1. Z místního webového uživatelského rozhraní vCenter přejít do **úložiště**. Vyberte **Databox-DataStore** a pak vyberte **virtuální počítače**.

    ![Odebrat virtuální počítače ze inventáře – vypnuto](media/databox-migration-select-databox-vm.png)

2. Ujistěte se, že jsou všechny virtuální počítače vypnuté.

3. Vyberte všechny virtuální počítače, klikněte pravým tlačítkem a pak vyberte **Odebrat ze inventáře**.

    ![Odebrat virtuální počítače ze inventáře](media/databox-migration-remove-vm-from-inventory.png)

4. **Ve složkách vyberte šablony virtuálních počítačů** a opakujte krok 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Odebrání úložiště dat Data Box NFS z vCenter

Úložiště dat Data Box NFS se musí před přípravou pro návrat odpojit od VMware ESXi hostitelů.

1. Z místního webového uživatelského rozhraní vCenter přejít do **úložiště**.

2. Klikněte pravým tlačítkem na **Databox – úložiště dat** a vyberte **Odpojit úložiště dat**.

    ![Odpojit Data Box úložiště dat](media/databox-migration-unmount-datastore.png)

3. Vyberte všechny hostitele ESXi, kde je úložiště dat připojené, a vyberte **OK**.

    ![Odpojení Data Box úložiště dat – vybrat hostitele](media/databox-migration-unmount-datastore-select-hosts.png)

4. Zkontrolujte a potvrďte všechna upozornění a vyberte **OK**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Připravte Data Box pro návrat a pak ji vraťte

Postupujte podle kroků uvedených v článku [vrácení Azure Data box a ověření, že se data nahrávají do Azure a](../databox/data-box-deploy-picked-up.md) vrátíte data box. Ověřte stav kopírování dat do účtu úložiště Azure. Jakmile se stav zobrazí jako dokončený, můžete ověřit data v účtu úložiště Azure.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Kopírování dat z Azure Storage do Azure VMware Solution

Data zkopírovaná do vašeho zařízení Data Box budou k dispozici v účtu úložiště Azure, jakmile se stav objednávky vašeho Data Box ukáže jako dokončený. Data se teď dají zkopírovat do vašeho řešení Azure VMware. Data v účtu úložiště je nutné zkopírovat do úložiště síti vSAN datacloud v privátním cloudu pomocí protokolu NFS. 

Nejdřív zkopírujte data služby Blob Storage na spravovaný disk na virtuálním počítači se systémem Linux v Azure pomocí **AzCopy**. Zpřístupněte spravovaný disk prostřednictvím systému souborů NFS, připojte sdílenou složku NFS jako úložiště dat ve vašem privátním cloudu a potom zkopírujte data. Tato metoda umožňuje rychlejší kopírování dat do privátního cloudu.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Zkopírujte data do privátního cloudu pomocí virtuálního počítače se systémem Linux a spravovaných disků a pak exportujte jako sdílenou složku NFS.

1. Vytvořte [virtuální počítač pro Linux](../virtual-machines/linux/quick-create-portal.md) v Azure ve stejné oblasti, ve které je vytvořený účet úložiště, a virtuální síť Azure s připojením k privátnímu cloudu.

2. Vytvořte spravovaný disk, jehož kapacita úložiště je větší než množství dat objektu blob, a [Připojte ho k virtuálnímu počítači se systémem Linux](../virtual-machines/linux/attach-disk-portal.md).  Pokud je množství dat objektu BLOB větší než kapacita nejvyššího dostupného spravovaného disku, je třeba data zkopírovat v několika krocích nebo pomocí více spravovaných disků.

3. Připojte se k virtuálnímu počítači se systémem Linux a připojte spravovaný disk.

4. Nainstalujte [AzCopy na virtuální počítač se systémem Linux](../storage/common/storage-use-azcopy-v10.md).

5. Stáhněte si data z úložiště objektů BLOB v Azure na spravovaný disk pomocí AzCopy.  Syntaxe příkazu: `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"` .  Nahraďte `<storage-account-name>` názvem účtu služby Azure Storage a `<container-name>` kontejnerem, který obsahuje data zkopírovaná pomocí data box.

6. Instalace serveru NFS na virtuálním počítači se systémem Linux:

    - Při distribuci Ubuntu/Debian: `sudo apt install nfs-kernel-server` .
    - Na distribuci Enterprise Linux: `sudo yum install nfs-utils` .

7. Změňte oprávnění složky na spravovaném disku, kde se zkopírovala data z úložiště objektů BLOB v Azure.  Změňte oprávnění pro všechny složky, které chcete exportovat jako sdílenou složku NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Úpravou souboru přiřaďte oprávnění pro IP adresy klientů pro přístup ke sdílené složce systému souborů NFS `/etc/exports` .

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

9. Exportujte sdílené složky systému souborů NFS pomocí `sudo exportfs -a` příkazu.

10. Restartujte server jádra systému souborů NFS pomocí `sudo systemctl restart nfs-kernel-server` příkazu.


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Připojte sdílenou složku Linux Virtual Machine NFS jako úložiště dat v privátním cloudovém clusteru vCenter a pak zkopírujte data.

Sdílená složka NFS z virtuálního počítače se systémem Linux musí být připojena jako úložiště dat v clusteru vCenter vašeho privátního cloudu. Po připojení se data dají kopírovat z úložiště pro systém souborů NFS do úložiště dat síti vSAN privátního cloudu.

1. Přihlaste se ke svému privátnímu cloudu vCenter Server.

2. Klikněte pravým tlačítkem myši na **datové centrum**, vyberte **úložiště**, vyberte **nové úložiště dat** a pak vyberte **Další**.

   ![Přidat nové úložiště dat](media/databox-migration-add-datastore.png)

3. V kroku 1 Průvodce přidáním úložiště dat vyberte typ **NFS** .

   ![Přidat nový typ úložiště dat](media/databox-migration-add-datastore-type.png)

4. V kroku 2 průvodce vyberte jako verzi systému souborů NFS **3 systém souborů** NFS a pak vyberte **Další**.

   ![Přidat novou verzi systému souborů NFS úložiště](media/databox-migration-add-datastore-nfs-version.png)

5. V kroku 3 průvodce zadejte název úložiště dat, cestu a Server.  Pro server můžete použít IP adresu vašeho virtuálního počítače se systémem Linux.  Cesta ke složce bude ve `/<folder>/<subfolder>/` formátu.

   ![Přidat novou konfiguraci úložiště dat pro systém souborů NFS](media/databox-migration-add-datastore-nfs-configuration.png)

6. V kroku 4 průvodce vyberte hostitele ESXi, kam chcete připojit úložiště dat, a pak vyberte **Další**.  V clusteru vyberte všechny hostitele, aby se zajistila migrace virtuálních počítačů.

   ![Přidat nové úložiště dat – vybrat hostitele](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. V kroku 5 průvodce zkontrolujte souhrn a pak vyberte **Dokončit**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Přidání virtuálních počítačů a šablon virtuálních počítačů z úložiště dat NFS do inventáře

1. Z webového uživatelského rozhraní vCenter vašeho privátního cloudu si přečtěte do **úložiště**.  Vyberte úložiště pro Linux Virtual Machine NFS a pak vyberte **soubory**.

    ![Vybrat soubory z úložiště dat NFS](media/databox-migration-datastore-select-files.png)

2. Vyberte složku, která obsahuje virtuální počítač nebo šablonu virtuálního počítače.  V podokně podrobností vyberte soubor. VMX pro virtuální počítač nebo soubor. příponu VMTX pro šablonu virtuálního počítače.

3. Vyberte **zaregistrovat** virtuální počítač pro registraci virtuálního počítače ve vašem privátním cloudu vCenter.

    ![Registrovat virtuální počítač](media/databox-migration-datastore-register-vm.png)

4. Vyberte datové centrum, složku a fond prostředků, ve kterém chcete virtuální počítač zaregistrovat.

4. Zopakujte kroky 3 a 4 pro všechny virtuální počítače a šablony virtuálních počítačů.

5. Přejít do složky, která obsahuje soubory ISO.  Vyberte soubory ISO a pak vyberte **Kopírovat do** a zkopírujte soubory do složky v úložišti dat síti vSAN.

Virtuální počítače a šablony virtuálních počítačů jsou teď dostupné ve vašem privátním cloudu vCenter. Tyto virtuální počítače je potřeba přesunout z úložiště dat NFS do úložiště dat síti vSAN a teprve potom je zapnout. Můžete použít možnost **vMotion úložiště** a vybrat úložiště dat síti vSAN jako cíl pro virtuální počítače.

Šablony virtuálních počítačů musí být klonovány z úložiště dat pro Linux Virtual Machine NFS do úložiště dat síti vSAN.

### <a name="clean-up-your-linux-virtual-machine"></a>Vyčištění virtuálního počítače se systémem Linux

Až se všechna data zkopírují do privátního cloudu, můžete z vašeho privátního cloudu odebrat úložiště dat NFS:

1. Ujistěte se, že všechny virtuální počítače a šablony jsou přesunuté a naklonované do úložiště dat síti vSAN.

2. Odeberte ze inventáře všechny šablony virtuálních počítačů z úložiště dat NFS.

3. Odpojte úložiště dat virtuálního počítače Linux z vašeho privátního cloudu vCenter.

4. Odstraňte virtuální počítač a spravovaný disk z Azure.

5. Pokud nechcete zachovat data přenesená Data Box ve vašem účtu úložiště, odstraňte účet Azure Storage.  
    


## <a name="next-steps"></a>Další kroky

* Přečtěte si další informace o [data box](../databox/data-box-overview.md).
* Přečtěte si další informace o různých možnostech [migrace úloh do privátního cloudu](migrate-workloads.md).
