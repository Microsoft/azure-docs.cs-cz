---
title: Použití spravované identity přiřazené systémem na virtuální počítači s Linuxem pro přístup k Azure Storage
description: Tento kurz vás postupně provede používáním spravované identity přiřazené systémem na virtuálním počítači s Linuxem pro přístup k Azure Storage.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: priyamo
ms.openlocfilehash: f7393a476f6c6a922646663ad2c51be8082dfc40
ms.sourcegitcommit: b4755b3262c5b7d546e598c0a034a7c0d1e261ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54888375"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Kurz: Použití spravované identity přiřazené systémem na virtuální počítači s Linuxem pro přístup k Azure Storage 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak pomocí spravované identity přiřazené systémem na virtuálním počítači s Linuxem získat přístup ke službě Azure Storage. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * vytvořit účet úložiště
> * Vytvoření kontejneru objektů blob v účtu úložiště
> * Udělení přístupu spravované identitě virtuálního počítače s Linuxem ke kontejneru Azure Storage
> * Získání přístupového tokenu a jeho použití k volání Azure Storage

> [!NOTE]
> Ověřování Azure Active Directory pro Azure Storage je ve veřejné verzi Preview.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

Ukázkové skripty rozhraní příkazového řádku v tomto kurzu můžete spustit dvěma způsoby:

- Použijte [Azure Cloud Shell](~/articles/cloud-shell/overview.md) buď přímo z webu Azure Portal, nebo přes tlačítko **Vyzkoušet** umístěné v pravém horním rohu každého bloku kódu.
- Pokud upřednostňujete práci v místní konzole rozhraní příkazového řádku, [nainstalujte nejnovější verzi CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 nebo novější).

## <a name="create-a-storage-account"></a>vytvořit účet úložiště 

V této části vytvoříte účet úložiště. 

1. Na webu Azure Portal klikněte v levém horním rohu na tlačítko **+ Vytvořit prostředek**.
2. Klikněte na **Úložiště** a pak vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.
3. V části **Název** zadejte název účtu úložiště.  
4. V polích **Model nasazení** a **Druh účtu** nastavte **Resource manager** a **Úložiště (v1 pro obecné účely)**. 
5. Ověřte, že pole **Předplatné** a **Skupina prostředků** se shodují s údaji zadanými při vytvoření virtuálního počítače v předchozím kroku.
6. Klikněte na možnost **Vytvořit**.

    ![Vytvoření nového účtu úložiště](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Vytvoření kontejneru objektů blob a nahrání souboru do účtu úložiště

Soubory vyžadují úložiště objektů blob. Proto potřebujete vytvořit kontejner objektů blob, do kterého soubor uložíte. Potom soubor nahrajete do kontejneru objektů blob v novém účtu úložiště.

1. Vraťte se k nově vytvořenému účtu úložiště.
2. V části **Blob Service** klikněte na **Kontejnery**.
3. Nahoře na stránce klikněte na **+ Kontejner**.
4. V části **Nový kontejner** zadejte název kontejneru a v části **Úroveň veřejného přístupu** nechte výchozí hodnotu.

    ![Vytvoření kontejneru úložiště](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Na místním počítači vytvořte ve vámi vybraném editoru soubor s názvem *hello world.txt*.  Otevřete soubor a přidejte text (bez uvozovek) „Hello world! :)“ a pak ho uložte. 

6. Nahrajte soubor do nově vytvořeného kontejneru – klikněte na název kontejneru a vyberte **Nahrát**.
7. V podokně **Nahrát objekt blob** v části **Soubory** klikněte na ikonu složky a přejděte k souboru **hello world.txt** na místním počítači, vyberte ho a klikněte na **Nahrát**.

    ![Nahrání textového souboru](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Udělení přístupu virtuálnímu počítači ke kontejneru Azure Storage 

Spravovanou identitu virtuálního počítače můžete použít k načtení dat, která jsou v úložišti Azure Storage Blob.   

1. Přejděte zpět k nově vytvořenému účtu úložiště.  
2. Na panelu vlevo klikněte na odkaz **Řízení přístupu (IAM)**.  
3. Klikněte na tlačítko **+ přidat přiřazení role** nad stránky a přidat nové přiřazení role pro váš virtuální počítač.
4. V části **Role** vyberte v rozevírací nabídce **Čtenář dat objektu blob služby Storage (Preview)**. 
5. V dalším rozevíracím seznamu **Přiřadit přístup k** vyberte **Virtuální počítač**.  
6. Potom se ujistěte, že v rozevíracím seznamu **Předplatné** je správné předplatné, a nastavte **Skupinu prostředků** na **Všechny skupiny prostředků**.  
7. V části **Vybrat** zvolte svůj virtuální počítač a klikněte na **Uložit**.

    ![Přiřazení oprávnění](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Získání a použití přístupového tokenu k volání Azure Storage

Azure Storage nativně podporuje ověřování Azure AD, takže může přímo přijímat přístupové tokeny získané pomocí spravované identity. Je to součást integrace Azure Storage do Azure AD, ale nejde o poskytnutí přihlašovacích údajů v připojovacím řetězci.

K dokončení následujícího postupu musíte použít dříve vytvořený virtuální počítač. Abyste se k němu mohli připojit, potřebujete také klienta SSH. Pokud používáte Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc při konfiguraci klíčů klienta SSH, přečtěte si, [jak na počítači s Windows v Azure používat klíče SSH](~/articles/virtual-machines/linux/ssh-from-windows.md) nebo [jak na linuxových virtuálních počítačích v Azure vytvářet a používat pár veřejného a privátního klíče SSH](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

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

   V odpovědi je obsah souboru:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Další postup

V tomto kurzu jste zjistili, jak spravované identitě přiřazené systémem na virtuálním počítači s Linuxem povolit přístup k Azure Storage.  Další informace o Azure Storage:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)
