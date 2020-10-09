---
author: alkohli
ms.service: databox
ms.subservice: pod
ms.topic: include
ms.date: 09/30/2019
ms.author: alkohli
ms.openlocfilehash: ca7b83d24f2416b224963559361faf5a7775cd0d
ms.sourcegitcommit: d479ad7ae4b6c2c416049cb0e0221ce15470acf6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/01/2020
ms.locfileid: "91631528"
---
Jakmile Microsoft přijme a naskenuje zařízení, stav objednávky se změní na **Přijato**. Zařízení pak projde fyzickým ověřením poškození nebo známek manipulace.

Po dokončení ověřování se Data Box připojí k síti v datacentru Azure. Automaticky se spustí kopírování dat. V závislosti na velikosti dat může operace kopírování trvat několik hodin až dní. Průběh kopírování můžete sledovat na portálu.

Po dokončení kopírování se stav objednávky změní na **Dokončeno**.

Než odstraníte data ze zdroje, ujistěte se, že se nahrála do Azure. Vaše data můžou být v následujících umístěních:

- Vaše účty Azure Storage. Když data zkopírujete do Data Boxu, nahrají se do jedné z následujících cest v účtu služby Azure Storage:

  - Objekty blob bloku a objekty blob stránky: `https://<storage_account_name>.blob.core.windows.net/<containername>/files/a.txt`
  - Soubory Azure: `https://<storage_account_name>.file.core.windows.net/<sharename>/files/a.txt`

    Alternativně můžete přejít na svůj účet Azure Storage na webu Azure Portal a dokončit navigaci tam.

- Vaše skupiny prostředků spravovaných disků. Při vytváření spravovaných disků se virtuální pevné disky nahrají jako objekty blob stránky a pak se převedou na spravované disky. Spravované disky se připojí ke skupinám prostředků zadaným při vytváření objednávky. 

    - Pokud vaše kopírování na spravované disky v Azure proběhne úspěšně, můžete na webu Azure Portal přejít na **Podrobnosti objednávky** a poznamenat si skupiny prostředků zadané pro spravované disky.

        ![Identifikace skupin prostředků spravovaných disků](media/data-box-verify-upload-return/order-details-managed-disk-resource-groups.png)

        Přejděte do skupiny prostředků, kterou jste si poznamenali, a vyhledejte své spravované disky.

        ![Spravovaný disk připojený ke skupinám prostředků](media/data-box-verify-upload-return/managed-disks-resource-group.png)

    - Pokud kopírujete VHDX nebo dynamický nebo rozdílový virtuální pevný disk, tento VHDX nebo VHD se nahraje do přípravného účtu úložiště jako objekt blob stránky, ale převod virtuálního pevného disku na spravovaný disk selže. Přejděte do svého přípravného **účtu úložiště do části Objekty blob** a vyberte odpovídající kontejner – SSD úrovně Standard, HDD úrovně Standard nebo SSD úrovně Premium. Virtuální pevné disky se nahrají do přípravného účtu úložiště jako objekty blob stránky a naúčtují se poplatky.


## <a name="erasure-of-data-from-data-box"></a>Vymazání dat z Data Boxu
 
Po dokončení nahrávání do Azure se data z disků Data Boxu vymažou v souladu se [standardem NIST SP 800-88 Revision 1](https://csrc.nist.gov/News/2014/Released-SP-800-88-Revision-1,-Guidelines-for-Medi).


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli o tématech spojených se službou Azure Data Box Disk, jako jsou:

> [!div class="checklist"]
> * Předpoklady
> * Příprava k odeslání
> * Odeslání Data Boxu do Microsoftu
> * Ověření nahrání dat do Azure
> * Vymazání dat z Data Boxu

V následujícím článku se dozvíte, jak spravovat Data Box přes místní webové uživatelské rozhraní.

> [!div class="nextstepaction"]
> [Správa Azure Data Boxu pomocí místního webového uživatelského rozhraní](../articles/databox/data-box-local-web-ui-admin.md)