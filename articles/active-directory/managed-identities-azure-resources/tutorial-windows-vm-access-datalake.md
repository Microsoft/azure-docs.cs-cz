---
title: Kurz `:` použití spravované identity pro přístup k Azure Data Lake Store-Windows-Azure AD
description: V tomto kurzu se dozvíte, jak použít spravovanou identitu přiřazenou systémem virtuálního počítače s Windows pro přístup k Azure Data Lake Storu.
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
ms.date: 12/15/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd28ee509dc76ea0b2aca9264c591a7176ae2661
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093808"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-data-lake-store"></a>Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Data Lake Storu

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak pomocí spravované identity přiřazené systémem pro virtuální počítač s Windows získat přístup k Azure Data Lake Storu. Identity spravovaných služeb, které se spravují automaticky v Azure, slouží k ověření přístupu ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Udělení přístupu virtuálnímu počítači ke službě Azure Data Lake Store
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k přístupu ke službě Azure Data Lake Store

## <a name="prerequisites"></a>Požadavky

- Porozumění spravovaným identitám. Pokud ještě neznáte funkci spravovaných identit pro prostředky Azure, podívejte se na tento [přehled](overview.md). 
- Účet Azure, [Zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
- Oprávnění "vlastník" v příslušném oboru (vaše předplatné nebo skupina prostředků) k provedení požadovaných kroků vytváření prostředků a správy rolí. Pokud potřebujete pomoc s přiřazením role, přečtěte si téma [přiřazení rolí Azure ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md).
- Budete také potřebovat virtuální počítač s Windows, který má povolené spravované identity přiřazené systémem.
  - Pokud pro tento kurz potřebujete vytvořit virtuální počítač, můžete postupovat podle článku [s názvem vytvořit virtuální počítač s povolenou identitou přiřazenou systémem](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) .




## <a name="enable"></a>Povolit

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Udělení přístupu

Teď můžete virtuálnímu počítači udělit přístup k souborům a složkám ve službě Azure Data Lake Store.  Pro tento krok můžete použít stávající službu Data Lake Store nebo vytvořit novou.  Pokud chcete vytvořit novou službu Data Lake Store pomocí webu Azure Portal, postupujte podle tohoto [rychlého startu ke službě Azure Data Lake Store](../../data-lake-store/data-lake-store-get-started-portal.md). Rychlé starty s využitím Azure CLI a Azure PowerShellu najdete v [dokumentaci ke službě Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md).

V Data Lake Storu vytvořte novou složku a udělte identitě přiřazené systémem virtuálního počítače oprávnění ke čtení, zápisu a spouštění souborů v této složce:

1. Na webu Azure Portal klikněte v levém navigačním panelu na **Data Lake Store**.
2. Klikněte na službu Data Lake Store, kterou chcete pro tento kurz použít.
3. Na panelu příkazů klikněte na **Průzkumník dat**.
4. Vybere se kořenová složka služby Data Lake Store.  Na panelu příkazů klikněte na **Přístup**.
5. Klikněte na **Přidat**.  Do pole **Vybrat** zadejte název vašeho virtuálního počítače, například **DevTestVM**.  Ve výsledcích hledání kliknutím vyberte váš virtuální počítač a pak klikněte na **Vybrat**.
6. Klikněte na **Vybrat oprávnění**.  Vyberte oprávnění **Číst** a **Spustit**, přidejte je k **Tato složka** a přidejte je jako **Položka oprávnění k přístupu**.  Klikněte na **OK**.  Oprávnění by se mělo úspěšně přidat.
7. Zavřete okno **Přístup**.
8. Pro tento kurz vytvořte novou složku.  Na panelu příkazů klikněte na **Nová složka** a zadejte název nové složky, například **TestFolder**.  Klikněte na **OK**.
9. Klikněte na složku, kterou jste vytvořili, a pak na panelu příkazů klikněte na **Přístup**.
10. Podobně jako v kroku 5 klikněte na **Přidat**, do pole **Vybrat** zadejte název vašeho virtuálního počítače, vyberte ho a klikněte na **Vybrat**.
11. Podobně jako v kroku 6 klikněte na **Vybrat oprávnění**, vyberte **Čtení**, **Zápis** a **Spuštění**, přidejte oprávnění k **této složce** a přidejte ho jako **Položka oprávnění k přístupu a výchozí položka oprávnění**.  Klikněte na **OK**.  Oprávnění by se mělo úspěšně přidat.

Spravovaná identita přiřazená systémem virtuálního počítače teď může provádět všechny operace se soubory ve složce, kterou jste vytvořili.  Další informace o správě přístupu ke službě Data Lake Store najdete v tomto článku o [Řízení přístupu v Data Lake Store](../../data-lake-store/data-lake-store-access-control.md).

## <a name="access-data"></a>Přístup k datům

Azure Data Lake Store nativně podporuje ověřování Azure AD, takže může přímo přijímat přístupové tokeny získané pomocí spravovaných identit pro prostředky Azure.  Ověření v systému souborů Data Lake Store provedete tak, že do koncového bodu systému souborů Data Lake Store odešlete přístupový token vystavený službou Azure AD v autorizační hlavičce ve formátu „Bearer <HODNOTA_PŘÍSTUPOVÉHO_TOKENU>“.  Další informace o podpoře ověřování Azure AD ve službě Data Lake Store najdete v tématu [Ověřování ve službě Data Lake Store pomocí Azure Active Directory](../../data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).

> [!NOTE]
> Klientské sady SDK systému souborů Data Lake Store zatím spravované identity pro prostředky Azure nepodporují.  Jakmile se do sady SDK přidá podpora, tento kurz budeme aktualizovat.

V tomto kurzu pomocí PowerShellu provedete ověření v rozhraní REST API systému souborů Data Lake Store, abyste mohli odesílat požadavky REST. Pokud chcete k ověření použít spravovanou identitu přiřazenou systémem virtuálního počítače, musíte požadavek odeslat z příslušného virtuálního počítače.

1. Na portálu přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Windows a v části **Přehled** klikněte na **Připojit**.
2. Zadejte své **Uživatelské jméno** a **Heslo**, které jste přidali při vytváření virtuálního počítače s Windows. 
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** s virtuálním počítačem, otevřete **PowerShell** ve vzdálené relaci. 
4. Pomocí příkazu `Invoke-WebRequest` v PowerShellu požádejte místní spravované identity o koncový bod prostředků Azure k získání přístupového tokenu pro Azure Data Lake Store.  Identifikátor prostředku pro Data Lake Store je `https://datalake.azure.net/` .  Služba Data Lake hledá přesnou shodu s identifikátorem prostředku, proto je koncové lomítko důležité.

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


## <a name="disable"></a>Zakázat

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]


## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak použít spravovanou identitu přiřazenou systémem pro virtuální počítač s Windows pro přístup ke službě Azure Data Lake Store. Další informace o službě Azure Data Lake Store:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](../../data-lake-store/data-lake-store-overview.md)