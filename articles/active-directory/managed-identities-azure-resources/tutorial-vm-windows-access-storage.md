---
title: Přístup k Azure Storage pomocí spravované identity přiřazené systémem Windows VM | Microsoft Docs
description: Tento kurz vás postupně provede používáním spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Storage.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/14/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: de1cc69b3cfdac307edf6dfe999a5d538c2cb811
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "89263174"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-storage"></a>Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Storage

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak pomocí spravované identity přiřazené systémem pro virtuální počítač s Windows získat přístup ke službě Azure Storage. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření kontejneru objektů blob v účtu úložiště
> * Udělení přístupu k účtu úložiště spravovaným identitám přiřazeným systémem na virtuálním počítači s Windows
> * Získání a použití přístupu k volání Azure Storage

> [!NOTE]
> Ověřování Azure Active Directory pro Azure Storage je ve veřejné verzi Preview.

## <a name="prerequisites"></a>Předpoklady

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]



## <a name="enable"></a>Povolit

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Udělení přístupu


### <a name="create-storage-account"></a>Vytvoření účtu úložiště

V této části vytvoříte účet úložiště.

1. Na webu Azure Portal klikněte v levém horním rohu na tlačítko **+ Vytvořit prostředek**.
2. Klikněte na **Úložiště** a pak vyberte **Účet úložiště – objekt blob, soubor, tabulka, fronta**.
3. V části **Název** zadejte název účtu úložiště.
4. V polích **Model nasazení** a **Druh účtu** nastavte **Resource manager** a **Úložiště (v1 pro obecné účely)**.
5. Ověřte, že pole **Předplatné** a **Skupina prostředků** se shodují s údaji zadanými při vytvoření virtuálního počítače v předchozím kroku.
6. Klikněte na **Vytvořit**.

    ![Vytvoření nového účtu úložiště](./media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

### <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Vytvoření kontejneru objektů blob a nahrání souboru do účtu úložiště

Soubory vyžadují úložiště objektů blob. Proto potřebujete vytvořit kontejner objektů blob, do kterého soubor uložíte. Potom soubor nahrajete do kontejneru objektů blob v novém účtu úložiště.

1. Přejděte zpět k nově vytvořenému účtu úložiště.
2. V části **BLOB Service** klikněte na **kontejnery**.
3. Nahoře na stránce klikněte na **+ Kontejner**.
4. V části **Nový kontejner** zadejte název kontejneru a v části **Úroveň veřejného přístupu** nechte výchozí hodnotu.

    ![Vytvoření kontejneru úložiště](./media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Na místním počítači vytvořte ve vámi vybraném editoru soubor s názvem *hello world.txt*. Otevřete soubor a přidejte text (bez uvozovek) „Hello world! :)“ a pak ho uložte.
6. Nahrajte soubor do nově vytvořeného kontejneru – klikněte na název kontejneru a vyberte **Nahrát**.
7. V podokně **Nahrát objekt blob** v části **Soubory** klikněte na ikonu složky a přejděte k souboru **hello world.txt** na místním počítači, vyberte ho a klikněte na **Nahrát**.
    ![Nahrání textového souboru](./media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

### <a name="grant-access"></a>Udělení přístupu

V této části se dozvíte, jak udělit VIRTUÁLNÍmu počítači přístup k kontejneru Azure Storage. Spravovanou identitu přiřazenou systémem na virtuálním počítači můžete použít k načtení dat, která jsou v úložišti Azure Storage Blob.

1. Přejděte zpět k nově vytvořenému účtu úložiště.
2. Na panelu vlevo klikněte na odkaz **Řízení přístupu (IAM)**.
3. Kliknutím na **+ Přidat přiřazení role** v horní části stránky přidejte nové přiřazení role pro svůj virtuální počítač.
4. V části **role** v rozevíracím seznamu vyberte **čtečka dat objektů BLOB úložiště**.
5. V dalším rozevíracím seznamu **Přiřadit přístup k** vyberte **Virtuální počítač**.
6. Potom se ujistěte, že v rozevíracím seznamu **Předplatné** je správné předplatné, a nastavte **Skupinu prostředků** na **Všechny skupiny prostředků**.
7. V části **Vybrat** zvolte svůj virtuální počítač a klikněte na **Uložit**.

    ![Přiřazení oprávnění](./media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="access-data"></a>Přístup k datům 

Azure Storage nativně podporuje ověřování Azure AD, takže může přímo přijímat přístupové tokeny získané pomocí spravované identity. Je to součást integrace Azure Storage do Azure AD, ale nejde o poskytnutí přihlašovacích údajů v připojovacím řetězci.

Tady je příklad kódu .NET pro otevření připojení k Azure Storage pomocí přístupového tokenu a následného čtení obsahu souboru, který jste vytvořili dříve. Tento kód je potřeba spustit na virtuálním počítači, aby byl možný přístup ke koncovému bodu spravované identity virtuálního počítače. K použití metody přístupového tokenu se vyžaduje .NET Framework 4,6 nebo vyšší. Parametr `<URI to blob file>` nahraďte odpovídající hodnotou. Tuto hodnotu získáte, když přejdete k vytvořenému souboru, který jste nahráli do úložiště objektů blob, a zkopírujete **adresu URL**, která je na stránce **Přehled** v části **Vlastnosti**.

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

Odpověď bude obsahovat obsah souboru:

`Hello world! :)`


## <a name="disable"></a>Zakázat

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak identitě přiřazené systémem na virtuálním počítači s Windows povolit přístup k Azure Storage.  Další informace o Azure Storage:

> [!div class="nextstepaction"]
> [Azure Storage](../../storage/common/storage-introduction.md)