---
title: Použití spravované identity na virtuálním počítači s Windows pro přístup k Azure Storage
description: Tento kurz vás postupně povede při použití spravované identity na virtuálním počítači s Windows pro přístup k Azure Storage.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2018
ms.author: daveba
ms.openlocfilehash: e001907b9df77eff1455043a3fd7ce5533838fcc
ms.sourcegitcommit: 7208bfe8878f83d5ec92e54e2f1222ffd41bf931
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/14/2018
ms.locfileid: "39056170"
---
# <a name="tutorial-use-a-windows-vm-managed-identity-to-access-azure-storage"></a>Kurz: Použití spravované identity na virtuálním počítači s Windows pro přístup k Azure Storage

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu si ukážeme, jak povolit spravovanou identitu na virtuálním počítači s Windows a pak ji použít pro přístup k Azure Storage.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Windows v nové skupině prostředků 
> * Povolení spravované identity na virtuálním počítači s Windows
> * Vytvoření kontejneru objektů blob v účtu úložiště
> * Udělení spravovaným identitám na virtuálních počítačích s Windows přístup k účtu úložiště 
> * Získání a použití přístupu k volání Azure Storage 

> [!NOTE]
> Ověřování Azure Active Directory pro Azure Storage je ve veřejné verzi Preview.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Windows v nové skupině prostředků

V této části vytvoříte virtuální počítač s Windows, kterému později udělíte spravovanou identitu.

1.  V levém horním rohu na webu Azure Portal klikněte na tlačítko pro **vytvoření nové služby**.
2.  Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 
3.  Zadejte informace o virtuálním počítači. Vytvořené **Uživatelské jméno** a **Heslo** použijete při přihlášení k virtuálnímu počítači.
4.  V rozevíracím seznamu zvolte pro virtuální počítač správné **předplatné**.
5.  Pokud chcete vybrat novou **skupinu prostředků**, ve které chcete vytvořit virtuální počítač, zvolte **Vytvořit novou**. Jakmile budete hotovi, klikněte na **OK**.
6.  Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

    ![Text k alternativnímu obrázku](media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

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
4. V polích **Model nasazení** a **Druh účtu** nastavte **Resource manager** a **Úložiště (v1 pro obecné účely)**. 
5. Ověřte, že pole **Předplatné** a **Skupina prostředků** se shodují s údaji zadanými při vytvoření virtuálního počítače v předchozím kroku.
6. Klikněte na možnost **Vytvořit**.

    ![Vytvoření nového účtu úložiště](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Vytvoření kontejneru objektů blob a nahrání souboru do účtu úložiště

Soubory vyžadují úložiště objektů blob. Proto potřebujete vytvořit kontejner objektů blob, do kterého soubor uložíte. Potom soubor nahrajete do kontejneru objektů blob v novém účtu úložiště.

1. Přejděte zpět k nově vytvořenému účtu úložiště.
2. V části **Blob Service** klikněte na **Kontejnery**.
3. Nahoře na stránce klikněte na **+ Kontejner**.
4. V části **Nový kontejner** zadejte název kontejneru a v části **Úroveň veřejného přístupu** nechte výchozí hodnotu.

    ![Vytvoření kontejneru úložiště](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Na místním počítači vytvořte ve vámi vybraném editoru soubor s názvem *hello world.txt*.  Otevřete soubor a přidejte text (bez uvozovek) „Hello world! :)“ a pak ho uložte. 
6. Nahrajte soubor do nově vytvořeného kontejneru – klikněte na název kontejneru a vyberte **Nahrát**.
7. V podokně **Nahrát objekt blob** v části **Soubory** klikněte na ikonu složky a přejděte k souboru **hello world.txt** na místním počítači, vyberte ho a klikněte na **Nahrát**.
    ![Nahrání textového souboru](../managed-service-identity/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Udělení přístupu virtuálnímu počítači ke kontejneru Azure Storage 

Spravovanou identitu virtuálního počítače můžete použít k načtení dat, která jsou v úložišti Azure Storage Blob.   

1. Přejděte zpět k nově vytvořenému účtu úložiště.  
2. Na panelu vlevo klikněte na odkaz **Řízení přístupu (IAM)**.  
3. Nahoře na stránce klikněte na **+ Přidat** a přiřaďte virtuálnímu počítači novou roli.
4. V části **Role** vyberte v rozevírací nabídce **Čtenář dat objektu blob služby Storage (Preview)**. 
5. V dalším rozevíracím seznamu **Přiřadit přístup k** vyberte **Virtuální počítač**.  
6. Potom se ujistěte, že v rozevíracím seznamu **Předplatné** je správné předplatné, a nastavte **Skupinu prostředků** na **Všechny skupiny prostředků**.  
7. V části **Vybrat** zvolte svůj virtuální počítač a klikněte na **Uložit**. 

    ![Přiřazení oprávnění](~/articles/active-directory/managed-service-identity/media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Získání a použití přístupového tokenu k volání Azure Storage 

Azure Storage nativně podporuje ověřování Azure AD, takže může přímo přijímat přístupové tokeny získané pomocí spravované identity. Je to součást integrace Azure Storage do Azure AD, ale nejde o poskytnutí přihlašovacích údajů v připojovacím řetězci.

Tady je příklad kódu .Net, který používá přístupový token, aby otevřel připojení k Azure Storage a načetl obsah dříve vytvořeného souboru. Tento kód je potřeba spustit na virtuálním počítači, aby byl možný přístup ke koncovému bodu spravované identity virtuálního počítače. Při použití metody přístupového tokenu se vyžaduje rozhraní .NET Framework 4.6 nebo vyšší verze. Parametr `<URI to blob file>` nahraďte odpovídající hodnotou. Tuto hodnotu získáte, když přejdete k vytvořenému souboru, který jste nahráli do úložiště objektů blob, a zkopírujete **adresu URL**, která je na stránce **Přehled** v části **Vlastnosti**.

```csharp
using System;
using System.Collections.Generic;
using System.Linq;
using System.Text;
using System.Threading.Tasks;
using System.IO;
using System.Net;
using System.Web.Script.Serialization; 
using Microsoft.WindowsAzure.Storage.Auth;
using Microsoft.WindowsAzure.Storage.Blob;

namespace StorageOAuthToken
{
    class Program
    {
        static void Main(string[] args)
        {
            //get token
            string accessToken = GetMSIToken("https://storage.azure.com/");
           
            //create token credential
            TokenCredential tokenCredential = new TokenCredential(accessToken);

            //create storage credentials
            StorageCredentials storageCredentials = new StorageCredentials(tokenCredential);

            Uri blobAddress = new Uri("<URI to blob file>");

            //create block blob using storage credentials
            CloudBlockBlob blob = new CloudBlockBlob(blobAddress, storageCredentials);
        
            //retrieve blob contents
            Console.WriteLine(blob.DownloadText());
            Console.ReadLine();
        }

        static string GetMSIToken(string resourceID)
        {
            string accessToken = string.Empty;
            // Build request to acquire MSI token
            HttpWebRequest request = (HttpWebRequest)WebRequest.Create("http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=" + resourceID);
            request.Headers["Metadata"] = "true";
            request.Method = "GET";

            try
            {
                // Call /token endpoint
                HttpWebResponse response = (HttpWebResponse)request.GetResponse();

                // Pipe response Stream to a StreamReader, and extract access token
                StreamReader streamResponse = new StreamReader(response.GetResponseStream());
                string stringResponse = streamResponse.ReadToEnd();
                JavaScriptSerializer j = new JavaScriptSerializer();
                Dictionary<string, string> list = (Dictionary<string, string>)j.Deserialize(stringResponse, typeof(Dictionary<string, string>));
                accessToken = list["access_token"];
                return accessToken;
            }
            catch (Exception e)
            {
                string errorText = String.Format("{0} \n\n{1}", e.Message, e.InnerException != null ? e.InnerException.Message : "Acquire token failed");
                return accessToken;
            }
        }            
    }
}
```

V odpovědi je obsah souboru:

`Hello world! :)`

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili na virtuálním počítači s Windows povolit spravovanou identitu pro přístup k Azure Storage.  Další informace o Azure Storage:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)



