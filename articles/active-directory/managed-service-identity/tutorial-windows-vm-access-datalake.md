---
title: Jak použít Identitu spravované služby (MSI) virtuálního počítače s Windows pro přístup ke službě Azure Data Lake Store
description: V tomto kurzu se dozvíte, jak použít Identitu spravované služby (MSI) virtuálního počítače s Windows pro přístup ke službě Azure Data Lake Store.
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
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: afd35c963c2c1c4badb32f7e8f7dba1dce87481c
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37904284"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Kurz: Použití Identity spravované služby (MSI) virtuálního počítače s Windows pro přístup ke službě Azure Data Lake Store

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak použít Identitu spravované služby (MSI) pro virtuální počítač s Windows pro přístup ke službě Azure Data Lake Store. Identity spravovaných služeb, které se spravují automaticky v Azure, slouží k ověřování přihlášení ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolení MSI na virtuálním počítači s Windows 
> * Udělení přístupu virtuálnímu počítači ke službě Azure Data Lake Store
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k přístupu ke službě Azure Data Lake Store

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Windows v nové skupině prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Windows.  MSI můžete povolit také na stávajícím virtuálním počítači.

1. Klikněte na tlačítko **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 
3. Zadejte informace o virtuálním počítači. Zde vytvořené **Uživatelské jméno** a **Heslo** slouží jako přihlašovací údaje pro přihlášení k virtuálnímu počítači.
4. V rozevíracím seznamu zvolte pro virtuální počítač správné **Předplatné**.
5. Pokud chcete vybrat novou **skupinu prostředků**, ve které se má virtuální počítač vytvořit, zvolte **Vytvořit novou**. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. Na stránce Nastavení ponechte výchozí nastavení a klikněte na **OK**.

   ![Alternativní text k obrázku](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Povolení MSI na virtuálním počítači 

MSI virtuálního počítače umožňuje získat z Azure AD přístupové tokeny bez nutnosti vložení přihlašovacích údajů do kódu. Povolením MSI sdělíte Azure, že má pro váš virtuální počítač vytvořit spravovanou identitu. Při povolení MSI se na pozadí stanou dvě věci: virtuální počítač se zaregistruje v Azure Active Directory, aby se vytvořila jeho spravovaná identita, a tato identita se nakonfiguruje na virtuálním počítači.

1. Vyberte **virtuální počítač**, na kterém chcete MSI povolit.  
2. Na levém navigačním panelu klikněte na **Konfigurace**. 
3. Zobrazí se **Identita spravované služby**. Pokud chcete MSI zaregistrovat a povolit, vyberte **Ano**. Pokud ji chcete zakázat, zvolte Ne. 
4. Nezapomeňte konfiguraci uložit kliknutím na **Uložit**.  
   ![Alternativní text k obrázku](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Pokud chcete zkontrolovat a ověřit, která rozšíření tento virtuální počítač obsahuje, klikněte na **Rozšíření**. Pokud je povolená funkce MSI, v seznamu se zobrazí **ManagedIdentityExtensionforWindows**.

   ![Alternativní text k obrázku](../media/msi-tutorial-windows-vm-access-arm/msi-windows-extension.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Udělení přístupu virtuálnímu počítači ke službě Azure Data Lake Store

Teď můžete virtuálnímu počítači udělit přístup k souborům a složkám ve službě Azure Data Lake Store.  Pro tento krok můžete použít stávající službu Data Lake Store nebo vytvořit novou.  Pokud chcete vytvořit novou službu Data Lake Store pomocí webu Azure Portal, postupujte podle tohoto [rychlého startu ke službě Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Rychlé starty s využitím Azure CLI a Azure PowerShellu najdete v [dokumentaci ke službě Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).

Ve službě Data Lake Store vytvořte novou složku a udělte MSI virtuálního počítače oprávnění ke čtení, zápisu a spouštění souborů v této složce:

1. Na webu Azure Portal klikněte v levém navigačním panelu na **Data Lake Store**.
2. Klikněte na službu Data Lake Store, kterou chcete pro tento kurz použít.
3. Na panelu příkazů klikněte na **Průzkumník dat**.
4. Vybere se kořenová složka služby Data Lake Store.  Na panelu příkazů klikněte na **Přístup**.
5. Klikněte na tlačítko **Add** (Přidat).  Do pole **Vybrat** zadejte název vašeho virtuálního počítače, například **DevTestVM**.  Ve výsledcích hledání kliknutím vyberte váš virtuální počítač a pak klikněte na **Vybrat**.
6. Klikněte na **Vybrat oprávnění**.  Vyberte **Čtení** a **Spuštění**, přidejte oprávnění k **této složce** a přidejte ho jako **Oprávnění pouze k přístupu**.  Klikněte na tlačítko **OK**.  Oprávnění by se mělo úspěšně přidat.
7. Zavřete okno **Přístup**.
8. Pro účely tohoto kurzu vytvořte novou složku.  Na panelu příkazů klikněte na **Nová složka** a zadejte název nové složky, například **TestFolder**.  Klikněte na tlačítko **OK**.
9. Klikněte na složku, kterou jste vytvořili, a pak na panelu příkazů klikněte na **Přístup**.
10. Podobně jako v kroku 5 klikněte na **Přidat**, do pole **Vybrat** zadejte název vašeho virtuálního počítače, vyberte ho a klikněte na **Vybrat**.
11. Podobně jako v kroku 6 klikněte na **Vybrat oprávnění**, vyberte **Čtení**, **Zápis** a **Spuštění**, přidejte oprávnění k **této složce** a přidejte ho jako **Položka oprávnění k přístupu a výchozí položka oprávnění**.  Klikněte na tlačítko **OK**.  Oprávnění by se mělo úspěšně přidat.

MSI vašeho virtuálního počítače teď může provádět všechny operace se soubory ve složce, kterou jste vytvořili.  Další informace o správě přístupu ke službě Data Lake Store najdete v tomto článku o [Řízení přístupu v Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Získání přístupového tokenu pomocí MSI virtuálního počítače a jeho použití k volání systému souborů Azure Data Lake Store

Azure Data Lake Store nativně podporuje ověřování Azure AD, takže může přímo přijímat přístupové tokeny získané pomocí MSI.  Ověření v systému souborů Data Lake Store provedete tak, že do koncového bodu systému souborů Data Lake Store odešlete přístupový token vystavený službou Azure AD v autorizační hlavičce ve formátu „Bearer <HODNOTA_PŘÍSTUPOVÉHO_TOKENU>“.  Další informace o podpoře ověřování Azure AD ve službě Data Lake Store najdete v tématu [Ověřování ve službě Data Lake Store pomocí Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

> [!NOTE]
> Klientské sady SDK systému souborů Data Lake Store ještě nepodporují Identitu spravované služby.  Jakmile se do sady SDK přidá podpora, tento kurz budeme aktualizovat.

V tomto kurzu pomocí PowerShellu provedete ověření v rozhraní REST API systému souborů Data Lake Store, abyste mohli odesílat požadavky REST. Pokud k ověření chcete použít MSI virtuálního počítače, musíte požadavek odeslat z příslušného virtuálního počítače.

1. Na portálu přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Windows a v části **Přehled** klikněte na **Připojit**.
2. Zadejte své **Uživatelské jméno** a **Heslo**, které jste přidali při vytváření virtuálního počítače s Windows. 
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** virtuálního počítače, otevřete ve vzdálené relaci **PowerShell**. 
4. Pomocí `Invoke-WebRequest` v PowerShellu odešlete do místního koncového bodu MSI žádost o přístupový token pro Azure Data Lake Store.  Identifikátor prostředku pro Data Lake Store je https://datalake.azure.net/.  Služba Data Lake hledá přesnou shodu s identifikátorem prostředku, proto je koncové lomítko důležité.

   ```powershell
   $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -Method GET -Headers @{Metadata="true"}
   ```
    
   Převeďte odpověď z objektu JSON na objekt PowerShellu. 
    
   ```powershell
   $content = $response.Content | ConvertFrom-Json
   ```

   Extrahujte z odpovědi přístupový token.
    
   ```powershell
   $AccessToken = $content.access_token
   ```

5. Pomocí Invoke-WebRequest v PowerShellu odešlete do koncového bodu REST vaší služby Data Lake Store požadavek na výpis složek v kořenové složce.  Jedná se o jednoduchý způsob, jak otestovat, že je vše správně nakonfigurované.  Je důležité, aby řetězec Bearer v autorizační hlavičce měl velké B.  Název vaší služby Data Lake Store najdete v části **Přehled** okna Data Lake Store na webu Azure Portal.

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

6. Teď můžete zkusit do služby Data Lake Store nahrát soubor.  Nejprve vytvořte soubor, který chcete nahrát.

   ```powershell
   echo "Test file." > Test1.txt
   ```

7. Pomocí `Invoke-WebRequest` v PowerShellu odešlete do koncového bodu REST vaší služby Data Lake Store požadavek na nahrání souboru do složky, kterou jste vytvořili dříve.  Tento požadavek se skládá ze dvou kroků.  V prvním kroku odešlete požadavek a budete přesměrování do umístění, do kterého by se soubor měl nahrát.  V druhém kroku provedete samotné nahrání souboru.  Pokud jste použili jiné hodnoty, než jsou uvedené v tomto kurzu, nezapomeňte nastavit název složky a souboru odpovídajícím způsobem. 

   ```powershell
   $HdfsRedirectResponse = Invoke-WebRequest -Uri https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Pokud prozkoumáte hodnotu `$HdfsRedirectResponse`, měla by vypadat jako následující odpověď:

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

   Dokončete nahrávání odesláním požadavku do koncového bodu přesměrování:

   ```powershell
   Invoke-WebRequest -Uri $HdfsRedirectResponse.Headers.Location -Method PUT -Headers @{Authorization="Bearer $AccessToken"} -Infile Test1.txt -MaximumRedirection 0
   ```

   Úspěšná odpověď vypadá takto:

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

Pomocí dalších rozhraní API systému souborů Data Lake Store můžete provádět připojování k souborům, stahování souborů a další.

Blahopřejeme!  Pomocí MSI virtuálního počítače jste provedli ověření v systému souborů Data Lake Store.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak použít Identitu spravované služby pro virtuální počítač s Windows pro přístup ke službě Azure Data Lake Store. Další informace o službě Azure Data Lake Store najdete tady:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](/azure/data-lake-store/data-lake-store-overview)
