---
title: Použití spravované identity virtuálního počítače s Linuxem pro přístup k Azure Storage
description: Tento kurz vás provede procesem použití systémem přiřazené spravované identity na virtuálním počítači s Linuxem pro přístup ke službě Azure Storage.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/09/2018
ms.author: daveba
ms.openlocfilehash: fb67d1eea588d96129c4b58a8c1b2f569c9663bf
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904403"
---
# <a name="tutorial-use-a-linux-vms-managed-identity-to-access-azure-storage"></a>Kurz: Použití spravované identity virtuálního počítače s Linuxem pro přístup k Azure Storage 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]


V tomto kurzu se dozvíte, jak vytvořit spravovanou identitu virtuálního počítače s Linuxem a použít ji pro přístup ke službě Azure Storage. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Linuxem v nové skupině prostředků
> * Povolení spravované identity na virtuálním počítači s Linuxem
> * Vytvoření kontejneru objektů blob v účtu úložiště
> * Udělení přístupu spravované identitě virtuálního počítače s Linuxem ke kontejneru Azure Storage
> * Získání přístupového tokenu a jeho použití k volání Azure Storage

> [!NOTE]
> Ověřování pomocí Azure Active Directory pro Azure Storage je ve verzi Public Preview.

## <a name="prerequisites"></a>Požadavky

Pokud ještě nemáte účet Azure, [zaregistrujte si bezplatný účet](https://azure.microsoft.com) před tím, než budete pokračovat.

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

Ukázkové skripty rozhraní příkazového řádku v tomto kurzu můžete spustit dvěma způsoby:

- Použijte [Azure Cloud Shell](~/articles/cloud-shell/overview.md) buď přímo z webu Azure Portal, nebo přes tlačítko **Vyzkoušet** umístěné v pravém horním rohu každého bloku kódu.
- Pokud upřednostňujete práci v místní konzole rozhraní příkazového řádku, [nainstalujte nejnovější verzi CLI 2.0](https://docs.microsoft.com/cli/azure/install-azure-cli) (2.0.23 nebo novější).

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Linuxem v nové skupině prostředků

V této části vytvoříte virtuální počítač s Linuxem, kterému později udělíte spravovanou identitu.

1. V levém horním rohu webu Azure Portal vyberte tlačítko **Nový**.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. Jako **Typ ověřování** vyberte **Veřejný klíč SSH** nebo **Heslo**. Vytvořené přihlašovací údaje umožňují přihlásit se k virtuálnímu počítači.

   ![Podokno Základy při vytváření virtuálního počítače](../media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. V seznamu **Předplatné** vyberte předplatné pro virtuální počítač.
5. Pokud chcete vybrat novou skupinu prostředků, ve které chcete virtuální počítač vytvořit, vyberte **Skupina prostředků** > **Vytvořit novou**. Jakmile budete hotovi, vyberte **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. V podokně nastavení nechte výchozí hodnoty a vyberte **OK**.

## <a name="enable-managed-identity-on-your-vm"></a>Povolení spravované identity na virtuálním počítači

Spravovaná identita virtuálního počítače umožňuje získat z Azure AD přístupové tokeny bez nutnosti vložení přihlašovacích údajů do kódu. Po povolení spravované identity na virtuálním počítači na webu Azure Portal se stanou dvě věci: virtuální počítač se zaregistruje v Azure AD, aby se vytvořila jeho spravovaná identita, a tato identita se nakonfiguruje na virtuálním počítači.

1. Přejděte ke skupině prostředků nového virtuálního počítače a vyberte virtuální počítač, který jste vytvořili v předchozím kroku.
2. V kategorii **Nastavení** klikněte na **Konfigurace**.
3. Pokud chcete spravovanou identitu povolit, vyberte **Ano**.
4. Klikněte na **Uložit**, aby se konfigurace použila. 

## <a name="create-a-storage-account"></a>vytvořit účet úložiště 

V této části vytvoříte účet úložiště. 

1. Na webu Azure Portal klikněte v levém horním rohu na tlačítko **+ Vytvořit prostředek**.
2. Klikněte na **Úložiště** a pak vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.
3. V části **Název** zadejte název účtu úložiště.  
4. V polích **Model nasazení** a **Druh účtu** nastavte **Resource manager** a **Úložiště (pro obecné účely v1)**. 
5. Ověřte, že se pole **Předplatné** a **Skupina prostředků** shodují s údaji zadanými při vytváření virtuálního počítače v předchozím kroku.
6. Klikněte na možnost **Vytvořit**.

    ![Vytvoření nového účtu úložiště](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Vytvoření kontejneru objektů blob a nahrání souboru do účtu úložiště

Soubory vyžadují úložiště objektů blob. Proto potřebujete vytvořit kontejner objektů blob, do kterého soubor uložíte. Potom soubor nahrajete do kontejneru objektů blob v novém účtu úložiště.

1. Vraťte se k nově vytvořenému účtu úložiště.
2. V části **Blob service** klikněte na **Kontejnery**.
3. Nahoře na stránce klikněte na **+ Kontejner**.
4. V části **Nový kontejner** zadejte název kontejneru a v části **Úroveň veřejného přístupu** nechte výchozí hodnotu.

    ![Vytvoření kontejneru úložiště](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Na místním počítači vytvořte v libovolném editoru soubor *hello world.txt*.  Otevřete soubor a přidejte text (bez uvozovek) „Hello world! :)“ a pak ho uložte. 

6. Nahrajte soubor do nově vytvořeného kontejneru – klikněte na název kontejneru a vyberte **Nahrát**.
7. V podokně **Nahrát objekt blob** v části **Soubory** klikněte na ikonu složky a přejděte k souboru **hello world.txt** na místním počítači, vyberte ho a klikněte na **Nahrát**.

    ![Nahrání textového souboru](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Udělení přístupu virtuálnímu počítači ke kontejneru Azure Storage 

Spravovanou identitu virtuálního počítače můžete použít k načtení dat, která jsou v úložišti Azure Storage Blob.   

1. Vraťte se k nově vytvořenému účtu úložiště.  
2. Na panelu vlevo klikněte na odkaz **Řízení přístupu (IAM)**.  
3. Nahoře na stránce klikněte na **+ Přidat** a přiřaďte virtuálnímu počítači novou roli.
4. V části **Role** vyberte v rozevírací nabídce **Čtenář dat objektu blob služby Storage (Preview)**. 
5. V dalším rozevíracím seznamu **Přiřadit přístup k** zvolte **Virtuální počítač**.  
6. Potom se ujistěte, že v rozevíracím seznamu **Předplatné** je správné předplatné, a nastavte **Skupinu prostředků** na **Všechny skupiny prostředků**.  
7. V části **Vybrat** zvolte svůj virtuální počítač a klikněte na **Uložit**.

    ![Přiřazení oprávnění](~/articles/active-directory/managed-service-identity/media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Získání přístupového tokenu a jeho použití k volání Azure Storage

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

   Odpověď bude obsahovat obsah souboru:

   ```bash
   Hello world! :)
   ```

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak spravované identitě virtuálního počítače s Linuxem povolit přístup ke službě Azure Storage.  Další informace o službě Azure Storage najdete tady:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)
