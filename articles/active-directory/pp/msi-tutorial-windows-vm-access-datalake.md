---
title: Jak používat pro přístup k Azure Data Lake Store Windows virtuální počítač Identity spravované služby (MSI)
description: Kurz, který popisuje, jak používat pro přístup k Azure Data Lake Store Windows virtuální počítač Identity spravované služby (MSI).
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a35d99ae6fd41ae840c5e2710135c6a2f840be65
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39007719"
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Použít pro přístup k Azure Data Lake Store Windows virtuální počítač Identity spravované služby (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

V tomto kurzu se dozvíte, jak používat Identity spravované služby (MSI) pro Windows virtuální počítač (VM) pro přístup Azure Data Lake Store. Identita spravované služby je automaticky prováděna nástrojem Azure a umožňují ověření do služeb, které podporují ověřování Azure AD, aniž by bylo nutné vložit pověření do kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolení MSI ve virtuálním počítači Windows 
> * Udělení přístupu vašich virtuálních počítačů do Azure Data Lake Store
> * Získání přístupového tokenu pomocí identity virtuálního počítače a použít ho pro přístup Azure Data Lake Store

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Windows do nové skupiny prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Windows.  Můžete také povolit MSI na existující virtuální počítač.

1. Klikněte na tlačítko **vytvořit prostředek** v levém horním rohu webu Azure portal.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 
3. Zadejte informace o virtuálním počítači. **Uživatelské jméno** a **heslo** vytvořený, tady je přihlašovací údaje použijete k přihlášení k virtuálnímu počítači.
4. Zvolte správné **předplatné** pro virtuální počítač v rozevírací nabídce.
5. Chcete-li vybrat nový **skupiny prostředků** ve kterém chcete vytvořit virtuální počítač, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. Na stránce nastavení ponechte výchozí nastavení a klikněte na tlačítko **OK**.

   ![Text ALT obrázku](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Povolení MSI ve virtuálním počítači 

MSI virtuálního počítače můžete k získání přístupových tokenů z Azure AD, aniž byste museli vložit pověření do kódu. Povolení MSI Azure zjistí, chcete-li vytvořit spravovanou identitu pro váš virtuální počítač. Pod pokličkou, povolení MSI provede dvě věci: nainstaluje rozšíření MSI virtuálního počítače na virtuálním počítači a umožňuje MSI v Azure Resource Manageru.

1. Vyberte **virtuální počítač** , že chcete povolit na MSI.  
2. Na navigačním panelu vlevo klikněte na tlačítko **konfigurace**. 
3. Zobrazí **identita spravované služby**. Chcete-li zaregistrovat a povolit MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne. 
4. Zajištění kliknutí na **Uložit** uložte konfiguraci.  
   ![Text ALT obrázku](../managed-service-identity/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Pokud chcete zkontrolovat a ověřit, jaká rozšíření jsou na tomto virtuálním počítači, klikněte na tlačítko **rozšíření**. Pokud je povolené MSI, pak **ManagedIdentityExtensionforWindows** se zobrazí v seznamu.

   ![Text ALT obrázku](../managed-service-identity/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Udělení váš virtuální počítač přístup k Azure Data Lake Store

Nyní můžete udělit váš virtuální počítač přístup k souborům a složkám v Azure Data Lake Store.  Pro tento krok můžete použít existující Data Lake Store nebo vytvořte novou.  Chcete-li vytvořit nové Store Data Lake pomocí webu Azure portal, postupujte podle to [rychlý start Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-get-started-portal.md). Existují také šablon rychlý start a používat rozhraní příkazového řádku Azure a Azure Powershellu v [dokumentace ke službě Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-overview.md).

V vaše Data Lake Store vytvořte novou složku a udělení oprávnění MSI virtuálního počítače pro čtení, zápis a spouštění soubory v této složce:

1. Na webu Azure Portal, klikněte na tlačítko **Data Lake Store** v levém navigačním panelu.
2. Klikněte na tlačítko Data Lake Store chcete použít pro účely tohoto kurzu.
3. Klikněte na tlačítko **Průzkumník dat** na panelu příkazů.
4. Je vybrané kořenové složky Data Lake Store.  Klikněte na tlačítko **přístup** na panelu příkazů.
5. Klikněte na tlačítko **Add** (Přidat).  V **vyberte** pole, zadejte název vašeho virtuálního počítače, například **DevTestVM**.  Kliknutím vyberte váš virtuální počítač ve výsledcích hledání a potom klikněte na **vyberte**.
6. Klikněte na tlačítko **vyberte oprávnění**.  Vyberte **čtení** a **Execute**, přidejte do **této složky**a přidat jako **přístupová oprávnění pouze**.  Klikněte na tlačítko **OK**.  Oprávnění by měla být úspěšně přidal.
7. Zavřít **přístup** okno.
8. Pro účely tohoto kurzu vytvořte novou složku.  Klikněte na tlačítko **novou složku** v příkazovém řádku a poskytují novou složku název, například **TestFolder**.  Klikněte na tlačítko **OK**.
9. Klikněte na složku, které jste vytvořili a pak klikněte na **přístup** na panelu příkazů.
10. Podobně jako v kroku 5, klikněte na tlačítko **přidat**v **vyberte** pole zadejte název vašeho virtuálního počítače, vyberte ho a klikněte na tlačítko **vyberte**.
11. Podobně jako v kroku 6, klikněte na **oprávnění Select**vyberte **čtení**, **zápisu**, a **Execute**, přidejte do **této složky**a přidat jako **položka oprávnění k přístupu a výchozí položka oprávnění**.  Klikněte na tlačítko **OK**.  Oprávnění by měla být úspěšně přidal.

Vaše MSI virtuálního počítače teď můžete provádět všechny operace se soubory ve složce, kterou jste vytvořili.  Další informace o správě přístupu k Data Lake Store, najdete v tomto článku [řízení přístupu v Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Získání přístupového tokenu pomocí MSI virtuálního počítače a použít jej k vyvolání systému souborů Azure Data Lake Store

Azure Data Lake Store nativně podporuje Azure AD ověřování, aby mohl přijímat přímo přístupové tokeny získat pomocí Instalační služby MSI.  K ověřování pro systém souborů Data Lake Store, které odesíláte přístupový token vydala Azure AD do Data Lake Store filesystem koncového bodu, v hlavičce autorizace ve formátu "Nosiče < ACCESS_TOKEN_VALUE >".  Další informace o podpoře služby Data Lake Store pro ověřování Azure AD, [ověřování s Data Lake Store pomocí Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

> [!NOTE]
> Data Lake Store filesystem klientské sady SDK zatím ještě nepodporují identita spravované služby.  V tomto kurzu bude aktualizován, když dojde k přidání podpory pro sadu SDK.

V tomto kurzu se ověřujete na systém souborů Data Lake Store, které požadavky rozhraní REST API služby REST pomocí prostředí PowerShell. Použití MSI virtuálního počítače pro ověřování, budete muset provést žádosti z virtuálního počítače.

1. Na portálu přejděte na **virtuálních počítačů**, přejděte k virtuálnímu počítači s Windows a v **přehled** klikněte na tlačítko **připojit**.
2. Zadejte vaše **uživatelské jméno** a **heslo** pro které jste přidali po vytvoření virtuálního počítače Windows. 
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete **Powershellu** ve vzdálené relaci. 
4. Použití Powershellu `Invoke-WebRequest`, vytvořte žádost na místním koncovým bodem MSI pro získání přístupového tokenu pro Azure Data Lake Store.  Identifikátor prostředku pro Data Lake Store je "https://datalake.azure.net/".  Data Lake nemá přesnou shodu na identifikátor prostředku a je důležité, do adresy koncové lomítko.

   ```powershell
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://datalake.azure.net/"} -Headers @{Metadata="true"}
   ```
    
   Převeďte odpovědi z objektu JSON na objekt prostředí PowerShell. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Rozbalte přístupový token z odpovědi.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Použití Powershellu "Invoke-WebRequest", vytvořte žádost na koncový bod REST vaše Data Lake Store k seznamu složek v kořenové složce.  Toto je jednoduchý způsob, jak zkontrolovat, že je všechno správně nastavené.  Je důležité, že řetězec "Nosiče" v hlavičce autorizace má velké "B".  Můžete najít název vaše Data Lake Store v **přehled** okna Data Lake Store na webu Azure Portal.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Úspěšná odpověď vypadá takto:

   ```powershell
   StatusCode        : 200
   StatusDescription : OK
   Content           : {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY", "blockSize":0,"accessTime":1507934941392, "modificationTime":1507944835699,"replication":0, "permission":"770","ow..."
   RawContent        : HTTP/1.1 200 OK
                       Pragma: no-cache
                       x-ms-request-id: b4b31e16-e968-46a1-879a-3474aa7d4528
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict-Transport-Security: ma...
   Forms             : {}
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b4b31e16-e968-46a1-879a-3474aa7d4528],
                       [x-ms-webhdfs-version, 17.04.22.00], [Status, 0x0]...}
   Images            : {}
   InputFields       : {}
   Links             : {}
   ParsedHtml        : System.__ComObject
   RawContentLength  : 556
   ```

6. Teď můžete zkusit po nahrání souboru do vaše Data Lake Store.  Nejprve vytvořte soubor k odeslání.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Použití Powershellu `Invoke-WebRequest`, vytvořte žádost na vaše Data Lake Store koncový bod REST pro nahrání souboru do složky, které jste vytvořili dříve.  Tento požadavek trvá dva kroky.  V prvním kroku vytvoříte žádost a získejte přesměrování, kde by měl být odeslán soubor.  V druhém kroku ve skutečnosti nahrajte soubor.  Nezapomeňte nastavit název složky a souboru správně, pokud používáte jiné hodnoty než v tomto kurzu. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Je-li zkontrolovat hodnoty `$HdfsRedirectResponse` by mělo vypadat jako následující odpověď:

   ```powershell
   PS C:\> $HdfsRedirectResponse

   StatusCode        : 307
   StatusDescription : Temporary Redirect
   Content           : {}
   RawContent        : HTTP/1.1 307 Temporary Redirect
                       Pragma: no-cache
                       x-ms-request-id: b7ab492f-b514-4483-aada-4aa0611d12b3
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosn...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, b7ab492f-b514-4483-aada-4aa0611d12b3], 
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

   Dokončení nahrávání odesláním požadavku do koncového bodu přesměrování:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Podívejte se úspěšné odpovědi, jako jsou:

   ```powershell
   StatusCode        : 201
   StatusDescription : Created
   Content           : {}
   RawContent        : HTTP/1.1 201 Created
                       Pragma: no-cache
                       x-ms-request-id: 1e70f36f-ead1-4566-acfa-d0c3ec1e2307
                       ContentLength: 0
                       x-ms-webhdfs-version: 17.04.22.00
                       Status: 0x0
                       X-Content-Type-Options: nosniff
                       Strict...
   Headers           : {[Pragma, no-cache], [x-ms-request-id, 1e70f36f-ead1-4566-acfa-d0c3ec1e2307],
                       [ContentLength, 0], [x-ms-webhdfs-version, 17.04.22.00]...}
   RawContentLength  : 0
   ```

Další systémem souborů Data Lake Store pomocí rozhraní API, můžete připojit k souborům, stáhnout soubory a další.

Blahopřejeme!  Jste ověřeni pro systém souborů Data Lake Store pomocí MSI virtuálního počítače.

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Pro správu operations Data Lake Store pomocí Azure Resource Manageru.  Další informace o použití MSI virtuálního počítače k ověření do Resource Manageru, najdete v článku [používat pro přístup k Resource Manageru Linux VM Identity spravované služby (MSI)](../managed-service-identity/msi-tutorial-linux-vm-access-arm.md).
- Další informace o [ověřování s Data Lake Store pomocí Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Další informace o [operace systému souborů v Azure Data Lake Store pomocí rozhraní REST API](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) nebo [rozhraní FileSystem API WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Další informace o [řízení přístupu v Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.