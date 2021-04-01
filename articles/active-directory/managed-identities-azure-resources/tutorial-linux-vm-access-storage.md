---
title: Kurz `:` použití spravované identity pro přístup k Azure Storage-Linux – Azure AD
description: Tento kurz vás postupně provede používáním spravované identity přiřazené systémem na virtuálním počítači s Linuxem pro přístup k Azure Storage.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 10/23/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: a4c7612188043be070ead92c88838b567b22787d
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "98131266"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Kurz: Použití spravované identity přiřazené systémem na virtuální počítači s Linuxem pro přístup k Azure Storage 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak pomocí spravované identity přiřazené systémem na virtuálním počítači s Linuxem získat přístup ke službě Azure Storage. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření účtu úložiště
> * Vytvoření kontejneru objektů blob v účtu úložiště
> * Udělení přístupu spravované identitě virtuálního počítače s Linuxem ke kontejneru Azure Storage
> * Získání přístupového tokenu a jeho použití k volání Azure Storage

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

Ukázkové skripty rozhraní příkazového řádku v tomto kurzu můžete spustit dvěma způsoby:

- Použijte [Azure Cloud Shell](~/articles/cloud-shell/overview.md) buď přímo z webu Azure Portal, nebo přes tlačítko **Vyzkoušet** umístěné v pravém horním rohu každého bloku kódu.
- Pokud upřednostňujete práci v místní konzole rozhraní příkazového řádku, [nainstalujte nejnovější verzi CLI 2.0](/cli/azure/install-azure-cli) (2.0.23 nebo novější).

## <a name="create-a-storage-account"></a>Vytvoření účtu úložiště 

V této části vytvoříte účet úložiště. 

1. Na webu Azure Portal klikněte v levém horním rohu na tlačítko **+ Vytvořit prostředek**.
2. Klikněte na **Úložiště** a pak vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.
3. V části **Název** zadejte název účtu úložiště.  
4. V polích **Model nasazení** a **Druh účtu** nastavte **Resource manager** a **Úložiště (v1 pro obecné účely)**. 
5. Ověřte, že pole **Předplatné** a **Skupina prostředků** se shodují s údaji zadanými při vytvoření virtuálního počítače v předchozím kroku.
6. Klikněte na **Vytvořit**.

    ![Vytvoření nového účtu úložiště](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Vytvoření kontejneru objektů blob a nahrání souboru do účtu úložiště

Soubory vyžadují úložiště objektů blob. Proto potřebujete vytvořit kontejner objektů blob, do kterého soubor uložíte. Potom soubor nahrajete do kontejneru objektů blob v novém účtu úložiště.

1. Přejděte zpět k nově vytvořenému účtu úložiště.
2. V části **BLOB Service** klikněte na **kontejnery**.
3. Nahoře na stránce klikněte na **+ Kontejner**.
4. V části **Nový kontejner** zadejte název kontejneru a v části **Úroveň veřejného přístupu** nechte výchozí hodnotu.

    ![Vytvoření kontejneru úložiště](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Na místním počítači vytvořte ve vámi vybraném editoru soubor s názvem *hello world.txt*.  Otevřete soubor a přidejte text (bez uvozovek) „Hello world! :)“ a pak ho uložte. 

6. Nahrajte soubor do nově vytvořeného kontejneru – klikněte na název kontejneru a vyberte **Nahrát**.
7. V podokně **Nahrát objekt blob** v části **Soubory** klikněte na ikonu složky a přejděte k souboru **hello world.txt** na místním počítači, vyberte ho a klikněte na **Nahrát**.

    ![Nahrání textového souboru](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Udělení přístupu virtuálnímu počítači ke kontejneru Azure Storage 

Spravovanou identitu virtuálního počítače můžete použít k načtení dat, která jsou v úložišti Azure Storage Blob.

>[!NOTE]
> Další informace o různých rolích, které můžete použít k udělení oprávnění ke kontrole úložiště, [autorizaci přístupu k objektům blob a frontám pomocí Azure Active Directory](../../storage/common/storage-auth-aad.md#assign-azure-roles-for-access-rights)

1. Přejděte zpět k nově vytvořenému účtu úložiště.  
2. Na panelu vlevo klikněte na odkaz **Řízení přístupu (IAM)**.  
3. Kliknutím na **+ Přidat přiřazení role** v horní části stránky přidejte nové přiřazení role pro svůj virtuální počítač.
4. V části **role** v rozevíracím seznamu vyberte **čtečka dat objektů BLOB úložiště**. 
5. V dalším rozevíracím seznamu **Přiřadit přístup k** vyberte **Virtuální počítač**.  
6. Potom se ujistěte, že v rozevíracím seznamu **Předplatné** je správné předplatné, a nastavte **Skupinu prostředků** na **Všechny skupiny prostředků**.  
7. V části **Vybrat** zvolte svůj virtuální počítač a klikněte na **Uložit**.

    ![Přiřazení oprávnění](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Získání přístupového tokenu a jeho použití k volání Azure Storage

Azure Storage nativně podporuje ověřování Azure AD, takže může přímo přijímat přístupové tokeny získané pomocí spravované identity. Je to součást integrace Azure Storage do Azure AD, ale nejde o poskytnutí přihlašovacích údajů v připojovacím řetězci.

K dokončení následujícího postupu musíte použít dříve vytvořený virtuální počítač. Abyste se k němu mohli připojit, potřebujete také klienta SSH. Pokud používáte Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](/windows/wsl/about). Pokud potřebujete pomoc při konfiguraci klíčů klienta SSH, přečtěte si, [jak na počítači s Windows v Azure používat klíče SSH](~/articles/virtual-machines/linux/ssh-from-windows.md) nebo [jak na linuxových virtuálních počítačích v Azure vytvářet a používat pár veřejného a privátního klíče SSH](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Na webu Azure Portal přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Linuxem a pak na stránce **Přehled** klikněte na **Připojit**. Zkopírujte řetězec pro připojení k vašemu virtuálnímu počítači.
2. **Připojte** se vybraným klientem SSH k virtuálnímu počítači. 
3. V okně terminálu pomocí nástroje CURL odešlete do místního koncového bodu spravované identity žádost o přístupový token pro Azure Storage.
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fstorage.azure.com%2F' -H Metadata:true
    ```
4. Teď tento přístupový token použijte pro přístup ke službě Azure Storage například za účelem čtení obsahu ukázkového souboru, který jste do kontejneru nahráli dříve. Nahraďte hodnoty `<STORAGE ACCOUNT>`, `<CONTAINER NAME>` a `<FILE NAME>` hodnotami, které jste zadali dříve, a `<ACCESS TOKEN>` tokenem vráceným v předchozím kroku.

   ```bash
   curl https://<STORAGE ACCOUNT>.blob.core.windows.net/<CONTAINER NAME>/<FILE NAME> -H "x-ms-version: 2017-11-09" -H "Authorization: Bearer <ACCESS TOKEN>"
   ```

   Odpověď bude obsahovat obsah souboru:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak spravované identitě přiřazené systémem na virtuálním počítači s Linuxem povolit přístup k Azure Storage.  Další informace o Azure Storage:

> [!div class="nextstepaction"]
> [Azure Storage](../../storage/common/storage-introduction.md)
