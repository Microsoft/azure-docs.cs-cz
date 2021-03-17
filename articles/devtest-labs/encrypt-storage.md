---
title: Šifrování účtu úložiště Azure používaného testovacím prostředím v Azure DevTest Labs
description: Naučte se konfigurovat šifrování úložiště Azure používaného testovacím prostředím v Azure DevTest Labs
ms.topic: how-to
ms.date: 07/29/2020
ms.openlocfilehash: dcede89fb23c532742e41121688bcb51a5a73833
ms.sourcegitcommit: dbe434f45f9d0f9d298076bf8c08672ceca416c6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/17/2020
ms.locfileid: "92149307"
---
# <a name="encrypt-azure-storage-used-by-a-lab-in-azure-devtest-labs"></a>Šifrování Azure Storage používaného testovacím prostředím v Azure DevTest Labs
Každé testovací prostředí vytvořené v Azure DevTest Labs se vytvoří s přidruženým účtem služby Azure Storage. Účet úložiště se používá pro následující účely: 

- Ukládání dokumentů [vzorců](devtest-lab-manage-formulas.md) , které lze použít k vytvoření virtuálních počítačů.
- Ukládání výsledků artefaktů, které zahrnují protokoly nasazení a rozšíření vygenerované při použití artefaktů. 
- [Nahrávají se virtuální pevné disky (VHD) pro vytváření vlastních imagí v testovacím prostředí.](devtest-lab-create-template.md)
- Ukládání často používaných [artefaktů](add-artifact-vm.md) a [šablon Azure Resource Manager](devtest-lab-create-environment-from-arm.md) pro rychlejší načítání během vytváření virtuálního počítače nebo prostředí.

> [!NOTE]
> Výše uvedené informace jsou pro fungování testovacího prostředí kritické. Ukládá se po dobu životnosti testovacího prostředí (a prostředků testovacího prostředí), pokud je explicitně neodstraníte. Ruční odstranění těchto prostředků může vést k chybám při vytváření virtuálních počítačů v testovacím prostředí nebo vzorců, které se stanou poškozeny pro budoucí použití. 

## <a name="locate-the-storage-account-and-view-its-contents"></a>Vyhledání účtu úložiště a zobrazení jeho obsahu

1. Na domovské stránce testovacího prostředí vyberte **skupinu prostředků** na stránce **Přehled** . Měla by se zobrazit stránka **skupiny prostředků** pro skupinu prostředků, která obsahuje testovací prostředí. 

    :::image type="content" source="./media/encrypt-storage/overview-resource-group-link.png" alt-text="Výběr skupiny prostředků na stránce Přehled":::
1. Vyberte účet Azure Storage testovacího prostředí. Konvence pojmenování pro účet úložiště v testovacím prostředí je: `a<labNameWithoutInvalidCharacters><4-digit number>` . Například pokud je název testovacího prostředí `contosolab` , může být název účtu úložiště `acontosolab7576` . 

    :::image type="content" source="./media/encrypt-storage/select-storage-account.png" alt-text="Výběr skupiny prostředků na stránce Přehled":::
3. Na stránce **účet úložiště** vyberte v nabídce vlevo možnost **Průzkumník služby Storage (Preview)** a pak vyberte **kontejnery objektů BLOB** pro nalezení relevantního obsahu souvisejícího s testovacím prostředím. 

   :::image type="content" source="./media/encrypt-storage/storage-explorer.png" alt-text="Výběr skupiny prostředků na stránce Přehled" lightbox="./media/encrypt-storage/storage-explorer.png":::

## <a name="encrypt-the-lab-storage-account"></a>Šifrování účtu úložiště testovacího prostředí
Azure Storage automaticky šifruje vaše data, když se trvale uloží do cloudu. Šifrování Azure Storage chrání vaše data a pomáhá splnit závazky zabezpečení a dodržování předpisů vaší organizace. Další informace najdete v tématu [Azure Storage šifrování pro](../storage/common/storage-service-encryption.md)neaktivní neaktivní data.

Data v účtu úložiště testovacího prostředí se šifrují pomocí **klíče spravovaného Microsoftem**. Pro šifrování vašich dat můžete spoléhat na klíče spravované Microsoftem, nebo můžete šifrování spravovat pomocí vlastních klíčů. Pokud se rozhodnete spravovat šifrování s vlastními klíči pro účet úložiště testovacího prostředí, můžete zadat **klíč spravovaný zákazníkem** , který Azure Key Vault použít pro šifrování a dešifrování dat v úložišti objektů BLOB a ve službě soubory Azure. Další informace o klíčích spravovaných zákazníkem najdete v tématu [použití klíčů spravovaných zákazníkem se Azure Key Vault ke správě šifrování Azure Storage](../storage/common/customer-managed-keys-overview.md).

Informace o konfiguraci klíčů spravovaných zákazníkem pro šifrování Azure Storage najdete v následujících článcích: 

- [Azure Portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)


## <a name="manage-the-azure-blob-storage-life-cycle"></a>Správa životního cyklu služby Azure Blob Storage
Jak už bylo zmíněno, informace uložené v účtu úložiště testovacího prostředí jsou důležité, aby testovací prostředí fungovalo bez chyb. Pokud je výslovně neodstraníte, budou tato data nadále zůstat v účtu úložiště testovacího prostředí po dobu životnosti testovacího prostředí nebo v závislosti na typu dat.

### <a name="uploaded-vhds"></a>Nahrané virtuální pevné disky
Tyto virtuální pevné disky slouží k vytváření vlastních imagí. Když je odeberete, nebude už možné vytvářet vlastní image z těchto VHD.

### <a name="artifacts-cache"></a>Mezipaměť artefaktů
Tyto mezipaměti budou znovu vytvořeny, kdykoli budou aplikovány artefakty. Budou obnoveny pomocí nejnovějšího obsahu z příslušných odkazovaných úložišť. Pokud tedy odstraníte tyto informace za účelem uložení výdajů souvisejících s úložištěm, bude úleva dočasná.

### <a name="azure-resource-manager-template-cache"></a>Mezipaměť šablon Azure Resource Manager
Tyto mezipaměti se znovu vytvoří, kdykoli se připojí úložiště šablon založená na Azure Resource Manager, která jsou připojená a provedou se v testovacím prostředí. Budou obnoveny pomocí nejnovějšího obsahu z příslušných odkazovaných úložišť. Pokud tedy odstraníte tyto informace za účelem uložení výdajů souvisejících s úložištěm, bude úleva dočasná.

### <a name="formulas"></a>Vzorce
Tyto dokumenty slouží k podpoře možnosti vytváření vzorců z existujících virtuálních počítačů a vytváření virtuálních počítačů ze vzorců. Odstranění těchto dokumentů vzorců může vést k chybám při provádění následujících operací:

- Vytvoření vzorce z existujícího testovacího virtuálního počítače
- Vytváření a aktualizace vzorců 
- Vytvoření virtuálního počítače ze vzorce.

### <a name="artifact-results"></a>Výsledky artefaktů
Při použití artefaktů se velikost příslušných výsledků artefaktů může v závislosti na počtu a typu artefaktů, které běží na virtuálních počítačích testovacích prostředí, zvýšit v čase. Takže jako vlastník testovacího prostředí můžete chtít řídit životní cyklus takových dokumentů. Další informace najdete v tématu [Správa životního cyklu služby Azure Blob Storage](../storage/blobs/storage-lifecycle-management-concepts.md).

> [!IMPORTANT]
> Pro snížení nákladů spojených s Azure Storage účtem doporučujeme udělat tento krok. 

Například následující pravidlo se používá k nastavení 90ho pravidla vypršení platnosti specificky pro výsledky artefaktů. Zajišťuje, aby se starší výsledky artefaktů recykloval z účtu úložiště pravidelně tempo.

```json
{
  "rules": [
    {
      "name": "expirationRule",
      "enabled": true,
      "type": "Lifecycle",
      "definition": {
        "filters": {
          "blobTypes": [ "blockBlob" ],
          "prefixMatch": [ "artifacts/results" ]
        },
        "actions": {
          "baseBlob": {
            "delete": { "daysAfterModificationGreaterThan": 90 }
          },
          "snapshot": {
            "delete": { "daysAfterCreationGreaterThan": 90 }
          }
        }
      }
    }
  ]
}
```

## <a name="next-steps"></a>Další kroky
Informace o konfiguraci klíčů spravovaných zákazníkem pro šifrování Azure Storage najdete v následujících článcích: 

- [Azure Portal](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure PowerShell](../storage/common/customer-managed-keys-configure-key-vault.md)
- [Azure CLI](../storage/common/customer-managed-keys-configure-key-vault.md)