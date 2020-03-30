---
title: Řešení Azure VMware – migrace pomocí datové schránky Azure
description: Jak používat Azure Data Box k hromadné migraci dat do řešení Azure VMware.
author: sharaths-cs
ms.author: dikamath
ms.date: 09/27/2019
ms.topic: article
ms.service: azure-vmware-cloudsimple
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 65167169248d83ebfec2c49c308673ec9315934e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77019753"
---
# <a name="migrating-data-to-azure-vmware-solution-by-using-azure-data-box"></a>Migrace dat do řešení Azure VMware pomocí Azure Data Box

Cloudové řešení Microsoft Azure Data Box umožňuje rychlé, levné a spolehlivé odesílání terabajtů dat do Azure. Zabezpečený a rychlý přenos dat se zajišťuje zasláním speciálního zařízení úložiště Data Box. Každé úložné zařízení má maximální využitelnou úložnou kapacitu 80 TB a je do vašeho datového centra přepravováno regionálním operátorem. Zařízení má robustní pouzdro, které chrání a zabezpečuje vaše data během přepravy.

Pomocí datové schránky můžete hromadně migrovat data VMware do svého privátního cloudu. Data z místního prostředí VMware vSphere se zkopírují do datové schránky prostřednictvím protokolu NFS (Network File System). Hromadná migrace dat zahrnuje uložení kopie virtuálních počítačů v čase, konfigurace a přidružených dat do datové schránky a jejich ruční odeslání do Azure.

V tomto článku se dozvíte o:

* Nastavení datové schránky.
* Kopírování dat z místního prostředí VMware do datové schránky prostřednictvím systému souborů NFS.
* Příprava na vrácení datové schránky.
* Příprava dat objektů blob pro kopírování do řešení Azure VMware.
* Kopírování dat z Azure do vašeho privátního cloudu.

## <a name="scenarios"></a>Scénáře

Pro hromadnou migraci dat použijte datovou schránku v následujících scénářích:

* Chcete-li migrovat velké množství dat z místního do řešení Azure VMware. Tato metoda vytvoří směrný plán a synchronizuje rozdíly v síti.
* Chcete-li migrovat velký počet virtuálních počítačů, které jsou vypnuté (studené virtuální počítače).
* Migrace dat virtuálního počítače pro nastavení vývojových a testovacích prostředí.
* Chcete-li migrovat velký počet šablon virtuálních počítačů, souborů ISO a disků virtuálních počítačů.

## <a name="before-you-begin"></a>Než začnete

* Zkontrolujte požadavky a [objednejte si data box](../databox/data-box-deploy-ordered.md) prostřednictvím portálu Azure. Během procesu objednávky musíte vybrat účet úložiště, který umožňuje úložiště objektů Blob. Po přijetí zařízení Data Box ho připojte k místní síti a [nastavte zařízení](../databox/data-box-deploy-set-up.md) pomocí IP adresy, která je dostupná z vaší sítě pro správu vSphere.

* Vytvořte virtuální síť a účet úložiště ve stejné oblasti, kde se zřápává vaše řešení Azure VMware.

* Vytvořte [připojení virtuální sítě Azure](cloudsimple-azure-network-connection.md) z privátního cloudu k virtuální síti, kde se vytvoří účet úložiště podle kroků v části Připojení virtuální sítě Azure k [CloudSimple pomocí ExpressRoute](virtual-network-connection.md).

## <a name="set-up-data-box-for-nfs"></a>Nastavit datovou schránku pro nfs

Připojte se k místnímu webovému uživatelskému rozhraní datové schránky podle pokynů v části "Připojit k zařízení" [v kurzu: Kabel a připojte se k datové schránce Azure](../databox/data-box-deploy-set-up.md).  Konfigurace datové schránky pro povolení přístupu ke klientům služby NFS:

1. V místním webovém uživatelském prostředí přejděte na stránku **Připojit a zkopírovat.** V části **Nastavení služby NFS**vyberte **možnost Přístup klienta služby NFS**. 

    ![Konfigurace přístupu klientů systému souborů NFS 1](media/nfs-client-access.png)

2. Zadejte IP adresu hostitelů VMware ESXi a vyberte **Přidat**. Můžete nakonfigurovat přístup pro všechny hostitele v clusteru vSphere opakováním tohoto kroku. Vyberte **OK**.

    ![Konfigurace přístupu klientů systému souborů NFS 2](media/nfs-client-access2.png)
> [!IMPORTANT]
> **Vždy vytvořte složku pro soubory, které chcete kopírovat, v rámci sdílené složky a potom je zkopírujte do této složky**. Složky vytvořené ve sdílených složkách objektů blob bloku a objektů blob stránky představují kontejnery, do kterých se data nahrávají jako objekty blob. Soubory nelze kopírovat přímo do *kořenové* složky v účtu úložiště.

Ve sdílených složkách objektů blob bloku a objektů blob stránky jsou entitami první úrovně kontejnery a entitami druhé úrovně objekty blob. V rámci sdílených složek pro soubory Azure jsou entity první úrovně sdílené složky a entity druhé úrovně jsou soubory.

Následující tabulka uvádí cestu UNC ke sdíleným složkám ve vašem Data Boxu a adresu URL cesty ke službě Azure Storage, ve které jsou data nahraná. Konečnou adresu URL cesty ke službě Azure Storage je možné odvodit z cesty UNC ke sdílené složce.
 
|                   |                                                            |
|-------------------|--------------------------------------------------------------------------------|
| Objekty blob bloku Azure | <li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddress>/<StorageAccountName_BlockBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li> |  
| Objekty blob stránky Azure  | <li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddres>/<StorageAccountName_PageBlob>/<ContainerName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.blob.core.windows.net/<ContainerName>/files/a.txt`</li>   |  
| Soubory Azure       |<li>Cesta UNC ke sdíleným složkám: `//<DeviceIPAddres>/<StorageAccountName_AzFile>/<ShareName>/files/a.txt`</li><li>Adresa URL služby Azure Storage: `https://<StorageAccountName>.file.core.windows.net/<ShareName>/files/a.txt`</li>        |

> [!NOTE]
> K kopírování dat v mware v mware použijte objekty BLOB Azure Block.

## <a name="mount-the-nfs-share-as-a-datastore-on-your-on-premises-vcenter-cluster-and-copy-the-data"></a>Připojení sdílené složky systému souborů NFS jako úložiště dat v místním clusteru vcenter a kopírování dat

Sdílená funkce systému souborů NFS z datové schránky musí být připojena jako úložiště dat v místním clusteru vcenter nebo hostiteli vsystému VMware ESXi, aby bylo možné zkopírovat data do úložiště dat systému souborů NFS:

1. Přihlaste se k místnímu serveru vCenter.

2. Klepněte pravým tlačítkem myši na **položku Datové centrum**, vyberte **možnost Úložiště**, vyberte nové **úložiště dat**a pak vyberte **další**.

   ![Přidání nového úložiště dat](media/databox-migration-add-datastore.png)

3. V kroku 1 průvodce přidáním úložiště dat vyberte v části **Typ** **položku NFS** .

   ![Přidat nové úložiště dat - typ](media/databox-migration-add-datastore-type.png)

4. V kroku 2 průvodce vyberte **nfs 3** jako verzi systému nfs a pak vyberte **Další**.

   ![Přidat nové úložiště dat - verze nfs](media/databox-migration-add-datastore-nfs-version.png)

5. V kroku 3 průvodce zadejte název úložiště dat, cestu a server. Pro server můžete použít IP adresu datové schránky. Cesta ke složce `/<StorageAccountName_BlockBlob>/<ContainerName>/` bude ve formátu.

   ![Přidání nového úložiště dat – konfigurace systému nfs](media/databox-migration-add-datastore-nfs-configuration.png)

6. V kroku 4 průvodce vyberte hostitele ESXi, na kterých chcete úložiště dat připojit, a pak vyberte **další**.  V clusteru vyberte všechny hostitele, abyste zajistili migraci virtuálních počítačů.

   ![Přidat nové úložiště dat – vybrat hostitele](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. V kroku 5 průvodce zkontrolujte souhrn a vyberte **Dokončit**.

## <a name="copy-data-to-the-data-box-nfs-datastore"></a>Kopírování dat do úložiště dat nfs datové schránky

Virtuální počítače lze migrovat nebo klonovat do nového úložiště dat.  Všechny nepoužívané virtuální počítače, které chcete migrovat, lze migrovat do úložiště dat NFS datové schránky pomocí **možnosti úložiště vMotion.** Aktivní virtuální počítače lze klonovat do úložiště dat NFS datové schránky.

* Identifikujte a uveďte virtuální počítače, které lze **přesunout**.
* Identifikujte a uveďte virtuální počítače, které musí být **klonovány**.

### <a name="move-a-virtual-machine-to-a-data-box-datastore"></a>Přesunutí virtuálního počítače do datového úložiště datových schronů

1. Klikněte pravým tlačítkem myši na virtuální počítač, který chcete přesunout do úložiště dat datové schránky, a pak vyberte **Migrovat**.

    ![Migrace virtuálního počítače](media/databox-migration-vm-migrate.png)

2. Vyberte **Změnit úložiště jenom** pro typ migrace a pak vyberte **Další**.

    ![Migrace virtuálního počítače – jenom úložiště](media/databox-migration-vm-migrate-change-storage.png)

3. Jako cíl vyberte **Databox-Datastore** a pak vyberte **Další**.

    ![Migrace virtuálního počítače – výběr úložiště dat](media/databox-migration-vm-migrate-change-storage-select-datastore.png)

4. Zkontrolujte informace a vyberte **dokončit**.

5. Opakujte kroky 1 až 4 pro další virtuální počítače.

> [!TIP]
> Můžete vybrat více virtuálních počítačů, které jsou ve stejném stavu napájení (zapnuté nebo vypnuté) a migrovat je hromadně.

Virtuální počítač bude migrován do úložiště dat nfs z datové schránky. Po migraci všech virtuálních počítačů můžete vypnout (vypnout) aktivní virtuální počítače v rámci přípravy na migraci dat do řešení Azure VMware.

### <a name="clone-a-virtual-machine-or-a-virtual-machine-template-to-the-data-box-datastore"></a>Klonování virtuálního počítače nebo šablony virtuálního počítače do úložiště dat datové schránky

1. Klikněte pravým tlačítkem myši na virtuální počítač nebo na šablonu virtuálního počítače, kterou chcete klonovat. Vyberte **Klonovat** > **do virtuálního počítače**.

    ![Klon virtuálního počítače](media/databox-migration-vm-clone.png)

2. Vyberte název klonovaného virtuálního počítače nebo šablony virtuálního počítače.

3. Vyberte složku, do které chcete klonovaný objekt umístit, a pak vyberte **Další**.

4. Vyberte cluster nebo fond prostředků, do kterého chcete klonovaný objekt umístit, a pak vyberte **další**.

5. Jako umístění úložiště vyberte **Databox-Datastore** a pak vyberte **Další**.

    ![Klon virtuálního počítače – výběr úložiště dat](media/databox-migration-vm-clone-select-datastore.png)

6. Pokud chcete přizpůsobit všechny možnosti klonovaného objektu, vyberte možnosti vlastního nastavení a pak vyberte **Další**.

7. Zkontrolujte konfigurace a vyberte **Dokončit**.

8. Opakujte kroky 1 až 7 pro další virtuální počítače nebo šablony virtuálních počítačů.

Virtuální počítače budou klonovány a uloženy v datovém úložišti NFS z datové schránky. Po klonování virtuálních počítačů se ujistěte, že jsou vypnuté v rámci přípravy na migraci dat do řešení Azure VMware Solution.

### <a name="copy-iso-files-to-the-data-box-datastore"></a>Kopírování souborů ISO do datového úložiště datových schronů

1. Z místního webového uživatelského uživatelského prostředí vCenter přejděte na **Web Storage**.  Vyberte **Databox-Datastore** a pak vyberte **Soubory**. Vytvořte novou složku pro ukládání souborů ISO.

    ![Kopírovat ISO - vytvořit novou složku](media/databox-migration-create-folder.png)

2. Zadejte název složky, do které budou uloženy soubory ISO.

3. Poklepáním na nově vytvořenou složku ji otevřete.

4. Vyberte **Nahrát soubory** a pak vyberte soubory ISO, které chcete nahrát.
    
    ![Kopírovat ISO - nahrát soubory](media/databox-migration-upload-iso.png)

> [!TIP]
> Pokud již máte soubory ISO v místním úložišti dat, můžete vybrat soubory a **Kopírovat do** zkopírovat soubory do úložiště dat datové schránky NFS.


## <a name="prepare-data-box-for-return"></a>Připravit datovou schránku pro vrácení

Po všechna data virtuálního počítače, data šablony virtuálního počítače a všechny iso soubory jsou zkopírovány do úložiště dat Datové schránky NFS, můžete odpojit úložiště dat z vašeho vCenter. Všechny virtuální počítače a šablony virtuálních počítačů musí být odebrány z inventáře před odpojením úložiště dat.

### <a name="remove-objects-from-inventory"></a>Odebrání objektů ze skladu

1. Z místního webového uživatelského uživatelského prostředí vCenter přejděte na **Web Storage**. Vyberte **Úložiště dat datové schránky** a pak vyberte virtuální **ms**.

    ![Odebrání virtuálních počítačů ze skladu – vypnuto](media/databox-migration-select-databox-vm.png)

2. Ujistěte se, že jsou všechny virtuální počítače vypnuté.

3. Vyberte všechny virtuální počítače, klikněte pravým tlačítkem myši a pak vyberte **Odebrat ze skladu**.

    ![Odebrání virtuálních počítačů ze skladu](media/databox-migration-remove-vm-from-inventory.png)

4. Vyberte **šablony virtuálních počítače ve složkách** a opakujte krok 3.

### <a name="remove-the-data-box-nfs-datastore-from-vcenter"></a>Odebrání úložiště dat nfs datové schránky z aplikace VCenter

Úložiště dat NFS datové schránky musí být odpojeno od hostitelů VMware ESXi před přípravou na vrácení.

1. Z místního webového uživatelského uživatelského prostředí vCenter přejděte na **Web Storage**.

2. Klikněte pravým tlačítkem myši na **Databox-Datastore** a vyberte **Odpojit datastore**.

    ![Odpojit úložiště dat datové schránky](media/databox-migration-unmount-datastore.png)

3. Vyberte všechny hostitele ESXi, kde je úložiště dat připojeno, a vyberte **OK**.

    ![Odpojit datové úložiště datových schronů - vybrat hostitele](media/databox-migration-unmount-datastore-select-hosts.png)

4. Zkontrolujte a přijměte všechna upozornění a vyberte **OK**.

### <a name="prepare-data-box-for-return-and-then-return-it"></a>Připravte datovou schránku na vrácení a pak ji vraťte

Postupujte podle kroků popsaných v článku [Vrátit Azure Data Box a ověřit data nahrát do Azure](../databox/data-box-deploy-picked-up.md) vrátit data box. Zkontrolujte stav kopie dat do účtu úložiště Azure. Po dokončení stavu můžete ověřit data v účtu úložiště Azure.

## <a name="copy-data-from-azure-storage-to-azure-vmware-solution"></a>Kopírování dat z úložiště Azure do řešení Azure VMware Solution

Data zkopírovaná do vašeho zařízení Data Box budou dostupná na vašem účtu úložiště Azure poté, co se stav objednávky datové schránky zobrazí jako dokončený. Data teď můžete zkopírovat do vašeho řešení Azure VMware. Data v účtu úložiště musí být zkopírována do úložiště dat vSAN vašeho privátního cloudu pomocí protokolu NFS. 

Nejprve zkopírujte data úložiště objektů Blob na spravovaný disk na virtuálním počítači SIP v Azure pomocí **AzCopy**. Zpřístupněte spravovaný disk prostřednictvím systému souborů NFS, připojte sdílenou složku systému souborů NFS jako úložiště dat v privátním cloudu a potom data zkopírujte. Tato metoda umožňuje rychlejší kopírování dat do vašeho privátního cloudu.

### <a name="copy-data-to-your-private-cloud-using-a-linux-virtual-machine-and-managed-disks-and-then-export-as-nfs-share"></a>Kopírování dat do privátního cloudu pomocí virtuálního počítače s Linuxem a spravovaných disků a export ovat jako sdílenou položku systému souborů NFS

1. Vytvořte [virtuální počítač S Linuxem](../virtual-machines/linux/quick-create-portal.md) v Azure ve stejné oblasti, kde se vytvoří váš účet úložiště a má připojení virtuální sítě Azure k vašemu privátnímu cloudu.

2. Vytvořte spravovaný disk, jehož kapacita úložiště je větší než množství dat objektů blob, a [připojte ho k virtuálnímu počítači s Linuxem](../virtual-machines/linux/attach-disk-portal.md).  Pokud je množství dat objektů blob větší než kapacita největšího spravovaného disku, který je k dispozici, musí být data zkopírována ve více krocích nebo pomocí více spravovaných disků.

3. Připojte se k virtuálnímu počítači Linuxu a připojte spravovaný disk.

4. Nainstalujte [AzCopy na virtuální mši Linux](../storage/common/storage-use-azcopy-v10.md).

5. Stáhněte si data z úložiště objektů blob Azure na spravovaný disk pomocí AzCopy.  Syntaxe `azcopy copy "https://<storage-account-name>.blob.core.windows.net/<container-name>/*" "<local-directory-path>/"`příkazu: .  Nahraďte `<storage-account-name>` název účtu `<container-name>` úložiště Azure a kontejner, který obsahuje data zkopírovaná prostřednictvím datové schránky.

6. Nainstalujte server NFS do virtuálního počítače s Linuxem:

    - Na distribuci Ubuntu/Debian: `sudo apt install nfs-kernel-server`.
    - Na distribuci Enterprise `sudo yum install nfs-utils`Linux: .

7. Změňte oprávnění složky na spravovaném disku, kde byla zkopírována data z úložiště objektů Blob Azure.  Změňte oprávnění pro všechny složky, které chcete exportovat jako sdílenou složku systému souborů NFS.

    ```bash
    chmod -R 755 /<folder>/<subfolder>
    chown nfsnobody:nfsnobody /<folder>/<subfolder>
    ```

8. Přiřaďte oprávnění pro klientské IP adresy pro `/etc/exports` přístup ke sdílené složce systému souborů nFS úpravou souboru.

    ```bash
    sudo vi /etc/exports
    ```
    
    Do souboru zadejte následující řádky pro každou hostitelskou IP adresu ESXi vašeho privátního cloudu.  Pokud vytváříte sdílené složky pro více složek, přidejte všechny složky.

    ```bash
    /<folder>/<subfolder> <ESXiNode1IP>(rw,sync,no_root_squash,no_subtree_check)
    /<folder>/<subfolder> <ESXiNode2IP>(rw,sync,no_root_squash,no_subtree_check)
    .
    .
    ```

9. Exportujte sdílené složky nfs pomocí příkazu. `sudo exportfs -a`

10. Restartujte server jádra systému NFS pomocí příkazu. `sudo systemctl restart nfs-kernel-server`


### <a name="mount-the-linux-virtual-machine-nfs-share-as-a-datastore-on-a-private-cloud-vcenter-cluster-and-then-copy-data"></a>Připojení sdílené složky systému souborů NFS virtuálního počítače pro Linux jako úložiště dat v privátním cloudovém clusteru vCenter a kopírování dat

Sdílená služba NFS z vašeho virtuálního počítače s Linuxem musí být připojena jako úložiště dat v privátním cloudovém clusteru vCenter. Po připojení mohou být data zkopírována z úložiště dat nfs do úložiště dat v privátního cloudu vSAN.

1. Přihlaste se k privátnímu cloudovému serveru vCenter.

2. Klepněte pravým tlačítkem myši na **položku Datové centrum**, vyberte **možnost Úložiště**, vyberte nové **úložiště dat**a pak vyberte **další**.

   ![Přidání nového úložiště dat](media/databox-migration-add-datastore.png)

3. V kroku 1 průvodce přidáním úložiště dat vyberte typ **nfs.**

   ![Přidat nové úložiště dat - typ](media/databox-migration-add-datastore-type.png)

4. V kroku 2 průvodce vyberte **nfs 3** jako verzi systému nfs a pak vyberte **Další**.

   ![Přidat nové úložiště dat - verze nfs](media/databox-migration-add-datastore-nfs-version.png)

5. V kroku 3 průvodce zadejte název úložiště dat, cestu a server.  Pro server můžete použít IP adresu vašeho virtuálního počítače s Linuxem.  Cesta ke složce `/<folder>/<subfolder>/` bude ve formátu.

   ![Přidání nového úložiště dat – konfigurace systému nfs](media/databox-migration-add-datastore-nfs-configuration.png)

6. V kroku 4 průvodce vyberte hostitele ESXi, na kterých chcete úložiště dat připojit, a pak vyberte **další**.  V clusteru vyberte všechny hostitele, abyste zajistili migraci virtuálních počítačů.

   ![Přidat nové úložiště dat – vybrat hostitele](media/databox-migration-add-datastore-nfs-select-hosts.png)

7. V kroku 5 průvodce zkontrolujte souhrn a vyberte **Dokončit**.

### <a name="add-virtual-machines-and-virtual-machine-templates-from-an-nfs-datastore-to-the-inventory"></a>Přidání virtuálních počítačů a šablon virtuálních počítačů z úložiště dat nfs do inventáře

1. Z webového uživatelského uživatelského uživatelského nastavení privátního cloudu vCenter přejděte do **úložiště**.  Vyberte úložiště dat nfs virtuálního počítače s Linuxem a pak vyberte **Soubory**.

    ![Výběr souborů ze úložiště dat systému Souborů NFS](media/databox-migration-datastore-select-files.png)

2. Vyberte složku, která obsahuje virtuální počítač nebo šablonu virtuálního počítače.  V podokně podrobností vyberte soubor .vmx pro virtuální počítač nebo soubor .vmtx pro šablonu virtuálního počítače.

3. Vyberte **Registrovat virtuální počítač** a zaregistrujte virtuální počítač ve vašem privátním cloudu vCenter.

    ![Registrace virtuálního počítače](media/databox-migration-datastore-register-vm.png)

4. Vyberte datové centrum, složku a fond clusteru a prostředků, ve kterém chcete virtuální počítač zaregistrovat.

4. Opakujte kroky 3 a 4 pro všechny virtuální počítače a šablony virtuálních počítačů.

5. Přejděte do složky, která obsahuje soubory ISO.  Vyberte soubory ISO a pak vyberte **Kopírovat, chcete-li** soubory zkopírovat do složky v úložišti dat vSAN.

Virtuální počítače a šablony virtuálních počítačů jsou teď dostupné ve vašem privátním cloudu vCenter. Tyto virtuální počítače musí být přesunuty z úložiště dat nfs do úložiště dat vSAN před jejich zapnutím. Můžete použít **možnost úložiště vMotion** a vybrat úložiště dat vSAN jako cíl pro virtuální počítače.

Šablony virtuálních strojů musí být klonovány z úložiště dat nfs virtuálního počítače s Linuxem do úložiště dat vSAN.

### <a name="clean-up-your-linux-virtual-machine"></a>Vyčištění virtuálního počítače s Linuxem

Po zkopírování všech dat do vašeho privátního cloudu můžete úložiště dat nfs odebrat z privátního cloudu:

1. Ujistěte se, že všechny virtuální počítače a šablony jsou přesunuty a klonovány do úložiště dat vSAN.

2. Odeberte ze skladu všechny šablony virtuálních strojů ze úložiště dat služby NFS.

3. Odpojte úložiště dat virtuálního počítače linuxu z vašeho privátního cloudu vCenter.

4. Odstraňte virtuální počítač a spravovaný disk z Azure.

5. Pokud nechcete uchovávat data, která byla přenesena data box ve vašem účtu úložiště, odstraňte účet úložiště Azure.  
    


## <a name="next-steps"></a>Další kroky

* Další informace o [datové schránce](../databox/data-box-overview.md).
* Přečtěte si další informace o různých možnostech [migrace úloh do privátního cloudu](migrate-workloads.md).
