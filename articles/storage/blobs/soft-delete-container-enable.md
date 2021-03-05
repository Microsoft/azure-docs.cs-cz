---
title: Povolení a Správa obnovitelného odstranění pro kontejnery (Preview)
titleSuffix: Azure Storage
description: Povolit obnovitelné odstranění kontejnerů (Preview) pro snazší obnovení dat, když se omylem upraví nebo odstraní.
services: storage
author: tamram
ms.service: storage
ms.topic: how-to
ms.date: 03/05/2021
ms.author: tamram
ms.subservice: blobs
ms.openlocfilehash: 2097c1743e07b5563bc75d3d1cce48aa11b98e5f
ms.sourcegitcommit: f7eda3db606407f94c6dc6c3316e0651ee5ca37c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/05/2021
ms.locfileid: "102216339"
---
# <a name="enable-and-manage-soft-delete-for-containers-preview"></a>Povolení a Správa obnovitelného odstranění pro kontejnery (Preview)

Obnovitelné odstranění kontejneru (Preview) chrání vaše data před náhodným nebo omylem úpravou nebo odstraněním. Pokud je pro účet úložiště povolené obnovitelné odstranění kontejneru, může se kontejner a jeho obsah obnovit po jeho odstranění v rámci zadané doby uchování.

Pokud existuje možnost, že by vaše data mohla být omylem upravována nebo odstraněna aplikací nebo jiným uživatelem účtu úložiště, společnost Microsoft doporučuje zapnout u kontejneru obnovitelné odstranění. V tomto článku se dozvíte, jak povolit obnovitelné odstranění kontejnerů. Další podrobnosti o obnovitelném odstranění kontejneru, včetně toho, jak se zaregistrovat pro verzi Preview, najdete v tématu [obnovitelné odstranění pro kontejnery (Preview)](soft-delete-container-overview.md).

V případě komplexní ochrany dat doporučuje společnost Microsoft také povolit obnovitelné odstranění objektů BLOB a správy verzí objektů BLOB. Další informace o tom, jak povolit obnovitelné odstranění objektů blob, najdete v tématu [povolení a Správa obnovitelného odstranění pro objekty blob](soft-delete-blob-enable.md). Informace o tom, jak povolit správu verzí objektů blob, najdete v tématu [Správa verzí objektů BLOB](versioning-overview.md).

> [!IMPORTANT]
>
> Obnovitelné odstranění kontejneru je momentálně ve **verzi Preview**. Přečtěte si další [podmínky použití Microsoft Azure](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) Preview pro právní podmínky, které se vztahují na funkce Azure, které jsou ve verzi beta, Preview nebo jinak ještě nedostupné ve všeobecné dostupnosti.

## <a name="enable-container-soft-delete"></a>Povolit obnovitelné odstranění kontejneru

Můžete povolit nebo zakázat obnovitelné odstranění kontejneru pro účet úložiště, a to buď pomocí Azure Portal nebo šablony Azure Resource Manager.

# <a name="portal"></a>[Azure Portal](#tab/azure-portal)

Pokud chcete pro svůj účet úložiště povolit obnovitelné odstranění kontejneru pomocí Azure Portal, postupujte takto:

1. Na webu [Azure Portal](https://portal.azure.com/) přejděte ke svému účtu úložiště.
1. V části **BLOB Service** vyhledejte nastavení **ochrany dat** .
1. Nastavte vlastnost **měkkého odstranění kontejneru** na *povoleno*.
1. V části **zásady uchovávání informací** určete, jak dlouho se mají dočasně odstraněné kontejnery uchovávat Azure Storage.
1. Uložte provedené změny.

:::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-configure.png" alt-text="Snímek obrazovky ukazující, jak povolit obnovitelné odstranění kontejneru v Azure Portal":::

# <a name="template"></a>[Šablona](#tab/template)

Chcete-li povolit **podcontainerDeleteRetentionPolicy** odstranění kontejneru pomocí šablony Azure Resource Manager, vytvořte šablonu, která nastaví vlastnost. Následující postup popisuje, jak vytvořit šablonu v Azure Portal.

1. V Azure Portal klikněte na možnost **vytvořit prostředek**.
1. V **části Hledat na Marketplace** zadejte **šablonu Deployment** a potom stiskněte **ENTER**.
1. Zvolte **template Deployment**, zvolte **vytvořit** a potom **v editoru zvolte sestavit vlastní šablonu**.
1. V editoru šablon vložte následující kód JSON. `<account-name>`Zástupný symbol nahraďte názvem vašeho účtu úložiště.

    ```json
    {
      "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
      "contentVersion": "1.0.0.0",
      "parameters": {},
      "variables": {},
      "resources": [
          {
              "type": "Microsoft.Storage/storageAccounts/blobServices",
              "apiVersion": "2019-06-01",
              "name": "<account-name>/default",
              "properties": {
                  "containerDeleteRetentionPolicy": {
                      "enabled": true,
                      "days": 7
                  }
              }
          }
      ]
    }
    ```

---

1. Zadejte dobu uchování. Výchozí hodnota je 7.
1. Uložte šablonu.
1. Zadejte skupinu prostředků účtu a pak kliknutím na tlačítko **Revize + vytvořit** nasaďte šablonu a povolte obnovitelné odstranění kontejneru.

## <a name="view-soft-deleted-containers"></a>Zobrazit kontejnery s přípouštějím přípouštějící

Když je povolené obnovitelné odstranění, můžete v Azure Portal zobrazit kontejnery s přípouštějící přípouštějící. Dočasně odstraněné kontejnery jsou viditelné během zadané doby uchování. Po vypršení doby uchování se dočasně odstraněný kontejner trvale odstraní a už se nezobrazuje.

Chcete-li zobrazit kontejnery odstraněné v Azure Portal, postupujte takto:

1. V Azure Portal přejděte na svůj účet úložiště a zobrazte seznam kontejnerů.
1. Přepněte přepínač Zobrazit odstraněné kontejnery tak, aby zahrnoval odstraněné kontejnery v seznamu.

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-list.png" alt-text="Snímek obrazovky ukazující, jak zobrazit obnovitelné odstraněné kontejnery v Azure Portal":::

## <a name="restore-a-soft-deleted-container"></a>Obnovení podmíněného odstraněné kontejneru

V rámci doby uchování můžete obnovit dočasná odstraněný kontejner a jeho obsah. Chcete-li obnovit odstraněný kontejner v Azure Portal, postupujte podle následujících kroků:

1. V Azure Portal přejděte na svůj účet úložiště a zobrazte seznam kontejnerů.
1. Zobrazte kontextovou nabídku pro kontejner, který chcete obnovit, a v nabídce vyberte možnost **zrušit odstranění** .

    :::image type="content" source="media/soft-delete-container-enable/soft-delete-container-portal-restore.png" alt-text="Snímek obrazovky, který ukazuje, jak obnovit odstraněný kontejner v Azure Portal":::

## <a name="next-steps"></a>Další kroky

- [Obnovitelné odstranění pro kontejnery (Preview)](soft-delete-container-overview.md)
- [Obnovitelné odstranění pro objekty blob](soft-delete-blob-overview.md)
- [Správa verzí objektů BLOB](versioning-overview.md)
