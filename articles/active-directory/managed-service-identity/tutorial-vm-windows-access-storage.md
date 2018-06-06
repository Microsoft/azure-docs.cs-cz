---
title: Použít identitu spravovaných virtuálních počítačů systému Windows pro přístup k úložišti Azure
description: Kurz vás provede procesem pomocí identitu spravovaných virtuálních počítačů systému Windows pro přístup k úložišti Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 04/12/2018
ms.author: daveba
ms.openlocfilehash: 9ccc94727a18fbcd77f00000531934be57b8e132
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801360"
---
# <a name="tutorial-use-a-windows-vm-managed-identity-to-access-azure-storage"></a>Kurz: Použijte spravované identitu virtuálního počítače systému Windows pro přístup k úložišti Azure

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak povolit spravované Identity pro virtuální počítač Windows, a tuto identitu používat pro přístup k úložišti Azure.  Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření virtuálního počítače s Windows v nové skupině prostředků. 
> * Povolit spravované Identity na virtuálním počítači (VM) Windows
> * Vytvořte kontejner objektů blob v účtu úložiště
> * Udělení přístupu spravovat Identity Windows Virtuálního počítače k účtu úložiště 
> * Získat přístup a použít jej k vyvolání Azure Storage 

> [!NOTE]
> Ověřování Azure Active Directory pro Azure Storage je ve verzi public preview.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Windows v nové skupině prostředků.

V této části vytvoříte virtuální počítač Windows, který je později udělena identitu spravované.

1.  Klikněte **+/ vytvořit novou službu** nalezeno tlačítko v levém horním rohu portálu Azure.
2.  Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 
3.  Zadejte informace o virtuálním počítači. **Uživatelské jméno** a **heslo** vytvořený, zde je přihlašovací údaje, které používáte k přihlášení k virtuálnímu počítači.
4.  Vyberte správnou **předplatné** pro virtuální počítač v rozevírací nabídce.
5.  Chcete-li vybrat nový **skupiny prostředků** chcete virtuální počítač lze vytvořit v, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6.  Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

    ![Obrázek alternativní text](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-managed-identity-on-your-vm"></a>Povolit spravované Identity na vašem virtuálním počítači

Spravované identitu virtuálního počítače umožňuje získat přístupové tokeny z Azure AD bez nutnosti převést přihlašovací údaje do vašeho kódu. V pozadí, povolení spravovat Identity na virtuálním počítači prostřednictvím portálu Azure provádí dvě věci: ho zaregistruje virtuální počítač s Azure AD pro vytvoření spravované identity a nakonfiguruje identitu ve virtuálním počítači. 

1. Přejděte do skupiny prostředků vašeho nového virtuálního počítače a vyberte virtuální počítač, který jste vytvořili v předchozím kroku.
2. V části **nastavení** kategorii, klikněte na tlačítko **konfigurace**.
3. Chcete-li spravovat Identity, vyberte **Ano**.
4. Klikněte na tlačítko **Uložit** použít konfiguraci. 

## <a name="create-a-storage-account"></a>vytvořit účet úložiště 

V této části vytvoříte účet úložiště. 

1. Klikněte **+ vytvořit prostředek** nalezeno tlačítko v levém horním rohu portálu Azure.
2. Klikněte na tlačítko **úložiště**, pak **účet úložiště – objekt blob, soubor, tabulka, fronta**.
3. V části **název**, zadejte název pro účet úložiště.  
4. **Model nasazení** a **účet druhu** musí být nastavena na **správce prostředků** a **úložiště (obecné účely v1)**. 
5. Ujistěte se, **předplatné** a **skupiny prostředků** odpovídat jsou zadány při vytvoření virtuálního počítače v předchozím kroku.
6. Klikněte na možnost **Vytvořit**.

    ![Vytvořit nový účet úložiště](../media/msi-tutorial-linux-vm-access-storage/msi-storage-create.png)

## <a name="create-a-blob-container-and-upload-a-file-to-the-storage-account"></a>Vytvořte kontejner objektů blob a nahrajte soubor k účtu úložiště

Soubory vyžadují úložiště objektů blob, je nutné vytvořit kontejner objektů blob, ve kterém chcete soubor uložit. Potom nahrání souboru do kontejneru objektů blob v nový účet úložiště.

1. Přejděte zpět na vaše nově vytvořený účet úložiště.
2. V části **služby objektů Blob**, klikněte na tlačítko **kontejnery**.
3. Klikněte na tlačítko **+ kontejner** horní části stránky.
4. V části **nový kontejner**, zadejte název kontejneru a v části **veřejný přístup úroveň** ponechte výchozí hodnotu.

    ![Vytvoření kontejneru úložiště](../media/msi-tutorial-linux-vm-access-storage/create-blob-container.png)

5. Pomocí editoru podle vaší volby, vytvořte soubor s názvem *hello world.txt* na místním počítači.  Otevřete soubor a přidejte text (bez uvozovek) "Hello world! :) "a pak ho uložte. 
6. Nahrát soubor do nově vytvořený kontejner pak kliknutím na název kontejneru **nahrát**
7. V **nahrávání blob** podokně v části **soubory**, klikněte na ikonu složky a vyhledejte soubor **hello_world.txt** na místním počítači, vyberte soubor a pak klikněte na **Nahrát**.
    ![Nahrát textový soubor](~/articles/active-directory/media/msi-tutorial-linux-vm-access-storage/upload-text-file.png)

## <a name="grant-your-vm-access-to-an-azure-storage-container"></a>Udělit přístup virtuálních počítačů s kontejnerem Azure Storage 

Spravované identity Virtuálního počítače můžete použít k načtení dat do objektu BLOB úložiště Azure.   

1. Přejděte zpět na vaše nově vytvořený účet úložiště.  
2. Klikněte **přístup k ovládacímu prvku (IAM)** odkaz na levém panelu.  
3. Klikněte na tlačítko **+ přidat** nad stránku přidáte nové přiřazení role pro virtuální počítač.
4. V části **Role**, z rozevíracího seznamu, vyberte **čtecí modul dat objektů Blob Storage (Preview)**. 
5. V dalším rozevíracím pod **přiřadit přístup**, zvolte **virtuální počítač**.  
6. Dále zkontrolujte správné předplatné, je uvedena ve **předplatné** rozevíracího seznamu a poté nastavte **skupiny prostředků** k **všechny skupiny zdrojů**.  
7. V části **vyberte**, vyberte virtuální počítač a pak klikněte na tlačítko **Uložit**. 

    ![Přiřadit oprávnění](~/articles/active-directory/managed-service-identity/media/tutorial-linux-vm-access-storage/access-storage-perms.png)

## <a name="get-an-access-token-and-use-it-to-call-azure-storage"></a>Získání přístupového tokenu a použít jej k vyvolání Azure Storage 

Úložiště Azure nativně podporuje Azure AD ověřování, mohl přímo přijímat tokeny přístupu získat pomocí identitu spravované. To je součástí Azure Storage integraci s Azure AD a se liší od zadávání přihlašovacích údajů v připojovacím řetězci.

Tady je příklad kódu .net otevření připojení do služby Azure Storage pomocí přístupového tokenu a pak se čte obsah souboru, který jste vytvořili dříve. Tento kód musí být spuštěn na virtuální počítač přístup k koncový bod Virtuálního počítače spravovat Identity. Rozhraní .net framework 4.6 nebo vyšší je potřeba použít v případě metody token přístupu. Nahraďte hodnotu `<URI to blob file>` odpovídajícím způsobem. Tuto hodnotu můžete získat tak, že přejdete do souboru, které jste vytvořili a nahrán do úložiště objektů blob a kopírování **URL** pod **vlastnosti** **přehled** stránky.

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

Odpověď obsahuje obsah souboru:

`Hello world! :)`

## <a name="next-steps"></a>Další postup

V tomto kurzu jste se dozvěděli, jak povolit virtuálního počítače s Windows spravované Identity pro přístup k úložišti Azure.  Další informace o Azure Storage najdete v tématu:

> [!div class="nextstepaction"]
> [Azure Storage](/azure/storage/common/storage-introduction)



