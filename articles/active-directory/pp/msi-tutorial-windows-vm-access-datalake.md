---
title: "Jak používat pro přístup k Azure Data Lake Store Windows virtuálního počítače spravované služby Identity (MSI)"
description: "Kurz ukazuje, jak používat pro přístup k Azure Data Lake Store Windows virtuálního počítače spravované služby Identity (MSI)."
services: active-directory
documentationcenter: 
author: daveba
manager: mtillman
editor: 
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: skwan
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: 742b76e17b7ad00a70b0d18895c0b59ebe044d47
ms.sourcegitcommit: a0be2dc237d30b7f79914e8adfb85299571374ec
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/14/2018
---
# <a name="use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Používat pro přístup k Azure Data Lake Store Windows virtuálního počítače spravované služby Identity (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

V tomto kurzu se dozvíte, jak používat a spravovat službu Identity (MSI) pro virtuální počítač (VM) systému Windows pro přístup k Azure Data Lake Store. Identita spravované služby je automaticky prováděna nástrojem Azure a umožňují ověření pro služby, které podporují ověřování Azure AD, aniž by museli přihlašovací údaje vložit do vašeho kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolit MSI v systému Windows virtuálního počítače 
> * Udělit přístup virtuálních počítačů Azure Data Lake Store
> * Získání přístupového tokenu pomocí identity virtuálního počítače a použít ho pro přístup k Azure Data Lake Store

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k portálu Azure v [ https://portal.azure.com ](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Windows v nové skupině prostředků.

V tomto kurzu vytvoříme nový virtuální počítač s Windows.  Můžete také povolit MSI na existující virtuální počítač.

1. Klikněte na tlačítko **vytvořit prostředek** v levém horním rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 
3. Zadejte informace o virtuálním počítači. **Uživatelské jméno** a **heslo** vytvořený, zde je přihlašovací údaje, které používáte k přihlášení k virtuálnímu počítači.
4. Vyberte správnou **předplatné** pro virtuální počítač v rozevírací nabídce.
5. Chcete-li vybrat nový **skupiny prostředků** , během které můžete vytvořit virtuální počítač vyberte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. Na stránce nastavení ponechejte výchozí hodnoty a klikněte na tlačítko **OK**.

   ![Obrázek alternativní text](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Povolit MSI na vašem virtuálním počítači 

Virtuální počítač MSI umožňuje získat přístupové tokeny z Azure AD, aniž by bylo třeba uvést přihlašovací údaje do vašeho kódu. Povolení MSI informuje Azure k vytvoření spravovaného identity pro virtuální počítač. V pozadí, povolení MSI provádí dvě věci: nainstaluje rozšíření virtuálního počítače MSI na vašem virtuálním počítači a umožňuje MSI ve službě Správce prostředků Azure.

1. Vyberte **virtuálního počítače** , které chcete povolit MSI v.  
2. V levém navigačním panelu klikněte na tlačítko **konfigurace**. 
3. Zobrazí **identita spravované služby**. Registrovat a povolit soubor MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne. 
4. Ujistěte se, kliknete na tlačítko **Uložit** konfiguraci uložíte.  
   ![Obrázek alternativní text](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Pokud chcete zkontrolovat a ověřit, která rozšíření jsou na tomto virtuálním počítači, klikněte na tlačítko **rozšíření**. Pokud je povoleno MSI, pak **ManagedIdentityExtensionforWindows** se zobrazí v seznamu.

   ![Obrázek alternativní text](~/articles/active-directory/media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Udělit přístup virtuálních počítačů do Azure Data Lake Store

Nyní můžete udělit váš virtuální počítač přístup k souborům a složkám v Azure Data Lake Store.  Pro tento krok můžete použít existující Data Lake Store nebo vytvořte novou.  Chcete-li vytvořit nové Data Lake Store pomocí portálu Azure, postupujte podle to [rychlý start Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-get-started-portal.md). Existují také – elementy QuickStart, použít rozhraní příkazového řádku Azure a prostředí Azure PowerShell v [dokumentace Azure Data Lake Store](~/articles/data-lake-store/data-lake-store-overview.md).

V Data Lake Store vytvořte novou složku a udělte oprávnění MSI virtuálních počítačů pro čtení, zápisu a spouštět soubory v této složce:

1. Na portálu Azure klikněte na tlačítko **Data Lake Store** v levém navigačním panelu.
2. Klikněte na tlačítko Data Lake Store, kterou chcete použít pro tento kurz.
3. Klikněte na tlačítko **Průzkumníku dat** na panelu příkazů.
4. Je vybrána složka kořenové Data Lake Store.  Klikněte na tlačítko **přístup** na panelu příkazů.
5. Klikněte na tlačítko **Add** (Přidat).  V **vyberte** pole, zadejte název vašeho virtuálního počítače, například **DevTestVM**.  Kliknutím vyberte virtuální počítač ve výsledcích hledání a pak klikněte na **vyberte**.
6. Klikněte na tlačítko **vyberte oprávnění**.  Vyberte **čtení** a **Execute**, přidejte do **tato složka**a přidat jako **oprávnění přístupu jenom**.  Klikněte na tlačítko **OK**.  Oprávnění by měla být úspěšně přidají.
7. Zavřít **přístup** okno.
8. V tomto kurzu vytvořte novou složku.  Klikněte na tlačítko **novou složku** v řádku nabídek a udělte do nové složky název, například **TestFolder**.  Klikněte na tlačítko **OK**.
9. Klikněte na složku, které jste vytvořili a pak klikněte na **přístup** na panelu příkazů.
10. Podobně jako v kroku 5, klikněte na tlačítko **přidat**v **vyberte** pole zadejte název vašeho virtuálního počítače, vyberte ho a klikněte na tlačítko **vyberte**.
11. Podobná krok 6, klikněte na tlačítko **vyberte oprávnění**, vyberte **čtení**, **zápisu**, a **Execute**, přidejte do **tato složka**a přidejte jako **položka oprávnění k přístupu a výchozí položku oprávnění**.  Klikněte na tlačítko **OK**.  Oprávnění by měla být úspěšně přidají.

Vaše MSI virtuálního počítače teď může provádět všechny operace na soubory ve složce, kterou jste vytvořili.  Pro další informace o správě přístupu k Data Lake Store, přečtěte si tento článek na [řízení přístupu v Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Získání přístupového tokenu pomocí Instalační služby MSI virtuálního počítače a použít jej k vyvolání systém souborů Azure Data Lake Store

Azure Data Lake Store nativně podporuje Azure AD ověřování, mohl přímo přijímat tokeny přístupu získat pomocí Instalační služby MSI.  K ověření na systém souborů Data Lake Store je odeslat přístupový token vydán Azure AD pro svůj koncový bod Data Lake Store systému souborů, v hlavičce autorizace ve formátu "Nosiče < ACCESS_TOKEN_VALUE >".  Další informace o Data Lake Store podporu pro ověřování Azure AD, přečtěte si [ověřování s Data Lake Store pomocí Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

> [!NOTE]
> Klient systému souborů pro Data Lake Store SDK zatím nepodporují identita spravované služby.  V tomto kurzu se aktualizují, když dojde k přidání podpory k sadě SDK.

V tomto kurzu ověřovat Data Lake Store systém souborů, které požadavky REST API REST pomocí prostředí PowerShell. Pokud chcete použít soubor MSI virtuálních počítačů pro ověřování, je třeba, aby žádosti z virtuálního počítače.

1. Na portálu, přejděte na **virtuální počítače**, přejděte na virtuální počítač Windows a **přehled** klikněte na tlačítko **Connect**.
2. Zadejte ve vaší **uživatelské jméno** a **heslo** pro které jste přidali při vytváření virtuálního počítače Windows. 
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete **prostředí PowerShell** ve vzdálené relaci. 
4. Pomocí prostředí PowerShell na `Invoke-WebRequest`, vytvořte žádost na místní koncový bod MSI k získání tokenu přístupu pro Azure Data Lake Store.  Identifikátor prostředku pro Data Lake Store je "https://datalake.azure.net/".  Data Lake nepodporuje přesnou shodu v identifikátor prostředku a je důležité do adresy koncové lomítko.

   ```powershell
   $response = Invoke-WebRequest -Uri http://localhost:50342/oauth2/token -Method GET -Body @{resource="https://datalake.azure.net/"} -Headers @{Metadata="true"}
   ```
    
   Převeďte odpověď z objektu JSON na objekt prostředí PowerShell. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Rozbalte tokenu přístupu z odpovědi.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Pomocí Powershellu 'Invoke-WebRequest', vytvořte žádost na koncový bod REST vaše Data Lake Store k zobrazení seznamu složek v kořenové složce.  Toto je jednoduchý způsob, jak zkontrolovat, že je všechno správně nastavené.  Je důležité, že řetězec "Nosiče" v hlavičce autorizace obsahuje velké "B".  Můžete najít název Data Lake Store v **přehled** části okně Data Lake Store na portálu Azure.

   ```powershell
   Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -Headers @{Authorization="Bearer $AccessToken"}
   ```

   Úspěšná odpověď bude vypadat takto:

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

6. Nyní můžete zkusit nahrání souboru do Data Lake Store.  Nejprve vytvořte soubor k odeslání.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Pomocí prostředí PowerShell na `Invoke-WebRequest`, vytvořte žádost na koncový bod REST vaše Data Lake Store nahrát soubor do složky, které jste vytvořili dříve.  Tento požadavek trvá dva kroky.  V prvním kroku vytvořte žádost na a získat přesměrování, kde by měl být soubor nahrát.  V druhém kroku ve skutečnosti nahrajte soubor.  Nezapomeňte nastavit název složky a souboru správně, pokud jste použili jiné hodnoty než v tomto kurzu. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Je-li si prohlédnout hodnotu `$HdfsRedirectResponse` by měl vypadat jako následující odpověď:

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

   Dokončení nahrávání odesláním požadavku na koncový bod přesměrování:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Úspěšná odpověď vypadá jako:

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

Filesystem další Data Lake Store pomocí rozhraní API můžete připojit k souborům, stahovat soubory a další.

Blahopřejeme!  Jste ověřeni pro systém souborů Data Lake Store pomocí MSI virtuálních počítačů.

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Pro správu operations Data Lake Store využívá Azure Resource Manager.  Další informace o používání virtuálních počítačů MSI k ověření do Resource Manager, najdete v tématu [používat Linux virtuálního počítače spravované služby Identity (MSI) pro přístup k Resource Manager](msi-tutorial-linux-vm-access-arm.md).
- Další informace o [ověřování s Data Lake Store pomocí Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Další informace o [operací systému souborů v Azure Data Lake Store pomocí rozhraní REST API](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) nebo [rozhraní API webhdfs, které FileSystem](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Další informace o [řízení přístupu v Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.