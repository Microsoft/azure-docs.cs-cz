---
title: Použití identity spravované služby (MSI) na virtuálním počítači s Linuxem pro přístup k Azure Data Lake Store
description: V tomto kurzu si ukážeme, jak používat identitu spravované služby (MSI) na virtuálním počítači s Linuxem pro přístup ke službě Azure Data Lake Store.
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
ms.openlocfilehash: ce38dabbe9aa69f7c54bb49888ad83e01a7c9522
ms.sourcegitcommit: e0a678acb0dc928e5c5edde3ca04e6854eb05ea6
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/13/2018
ms.locfileid: "39004876"
---
# <a name="tutorial-use-managed-service-identity-for-a-linux-vm-to-access-azure-data-lake-store"></a>Kurz: Použití identity spravované služby (MSI) na virtuálním počítači s Linuxem pro přístup k Azure Data Lake Store

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu si ukážeme, jak používat identitu spravované služby (MSI) na virtuálním počítači s Linuxem pro přístup ke službě Azure Data Lake Store. Azure automaticky spravuje vytvořené identity prostřednictvím MSI. Identita spravovaných služeb slouží k ověření přístupu ke službám, které podporují ověřování Azure Active Directory (Azure AD) bez nutnosti vložení přihlašovacích údajů do kódu. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolit MSI na linuxovém virtuálním počítači. 
> * Udělit přístup virtuálnímu počítači k Azure Data Lake Store.
> * Získat přístupový token pomocí identity virtuálního počítače a použít ho pro přístup ke službě Azure Data Lake Store.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Linuxem v nové skupině prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Linuxem. MSI také můžete povolit na stávajícím virtuálním počítači.

1. V levém horním rohu webu Azure Portal vyberte tlačítko **Nový**.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. V poli **Typ ověřování** vyberte **Veřejný klíč SSH** nebo **Heslo**. Vytvořené přihlašovací údaje umožňují přihlásit se k virtuálnímu počítači.

   ![Podokno základních údajů při vytvoření virtuálního počítače](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. V seznamu **Předplatné** vyberte předplatné virtuálního počítače.
5. Pokud chcete vybrat novou skupinu prostředků, ve které chcete vytvořit virtuální počítač, vyberte **Skupina prostředků** > **Vytvořit nový**. Jakmile budete hotovi, vyberte **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. V podokně nastavení nechte výchozí hodnoty a vyberte **OK**.

## <a name="enable-msi-on-your-vm"></a>Povolení MSI na virtuálním počítači

Funkce MSI virtuálního počítače umožňuje získat z Azure AD přístupové tokeny bez nutnosti vložení přihlašovacích údajů do kódu. Když na virtuálním počítači povolíte MSI, stanou se dvě věci: virtuální počítač se zaregistruje v Azure Active Directory, aby se vytvořila jeho spravovaná identita, a tato identita se nakonfiguruje na virtuálním počítači.

1. V poli **Virtuální počítač** vyberte virtuální počítač, na kterém chcete povolit MSI.
2. V levém podokně vyberte **Konfigurace**.
3. Zobrazí se **Identita spravované služby**. Pokud chcete MSI zaregistrovat a povolit, vyberte **Ano**. Pokud chcete MSI zakázat, vyberte **Ne**.
   ![Výběr možnosti Zaregistrovat do Azure Active Directory](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)
4. Vyberte **Uložit**.

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Udělení přístupu virtuálnímu počítači k Azure Data Lake Store

Teď můžete virtuálnímu počítači udělit přístup k souborům a složkám ve službě Azure Data Lake Store. Pro tento krok můžete použít stávající instanci Data Lake Store nebo vytvořit novou. Pokud chcete vytvořit instanci Data Lake Store na webu Azure Portal, postupujte podle tohoto článku [Rychlý start ke službě Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). V [dokumentaci ke službě Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview) také najdete články Rychlý start pro použití Azure CLI nebo Azure PowerShellu.

V Data Lake Store vytvořte novou složku a udělte MSI oprávnění ke čtení, zápisu a spouštění souborů v této složce:

1. Na webu Azure Portal vyberte v levém podokně **Data Lake Store**.
2. Vyberte instanci Data Lake Store, kterou chcete použít.
3. Na panelu příkazů vyberte **Průzkumník dat**.
4. Vybere se kořenová složka instance Data Lake Store. Na příkazovém řádku vyberte **Přístup**.
5. Vyberte **Přidat**.  Do pole **Vybrat** zadejte název virtuálního počítače, například **DevTestVM**. Ve výsledcích hledání vyberte svůj virtuální počítač a pak klikněte na **Vybrat**.
6. Klikněte na **Vybrat oprávnění**.  Vyberte oprávnění **Číst** a **Spustit**, přidejte je k **Tato složka** a přidejte je jako **Položka oprávnění k přístupu**. Vyberte **OK**.  Oprávnění by mělo být úspěšně přidané.
7. Zavřete podokno **Přístup**.
8. Pro tento kurz vytvořte novou složku. Na panelu příkazů klikněte na **Nová složka** a zadejte název nové složky, například **TestFolder**.  Vyberte **OK**.
9. Vyberte složku, kterou jste vytvořili, a pak na panelu příkazů vyberte **Přístup**.
10. Podobně jako v 5. kroku vyberte **Přidat**. Do pole **Vybrat** zadejte název virtuálního počítače. Ve výsledcích hledání vyberte svůj virtuální počítač a pak klikněte na **Vybrat**.
11. Podobně jako v 6. kroku vyberte **Vybrat oprávnění**. Vyberte oprávnění **Číst**, **Zápis** a **Spustit**, přidejte je k **Tato složka** a přidejte je jako **Položka oprávnění k přístupu a výchozí položka oprávnění**. Vyberte **OK**.  Oprávnění by mělo být úspěšně přidané.

MSI teď může se soubory ve vytvořené složce provádět všechny operace. Další informace o správě přístupu ke službě Data Lake Store najdete v článku o [řízení přístupu v Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>Získání přístupového tokenu a volání systému souborů Data Lake Store

Azure Data Lake Store nativně podporuje ověřování Azure AD, takže může přímo přijímat přístupové tokeny získané prostřednictvím MSI. Pokud chcete ověřit přístup k systému souborů Data Lake Store, pošlete přístupový token vydaný službou Azure AD koncovému bodu systému souborů Data Lake Store. Přístupový token je v autorizační hlavičce ve formátu „Bearer \<HODNOTA_PŘÍSTUPOVÉHO_TOKENU\>“.  Další informace o podpoře ověřování Azure AD ve službě Data Lake Store najdete v článku o [ověřování ve službě Data Lake Store pomocí Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

V tomto kurzu použijete při ověření přístupu k REST API systému souborů Data Lake Store nástroj cURL pro požadavky REST.

> [!NOTE]
> Klientské sady SDK systému souborů Data Lake Store zatím nepodporují identitu spravované služby (MSI).

K dokončení tohoto postupu potřebujete klienta SSH. Pokud používáte Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc při konfiguraci klíčů klienta SSH, přečtěte si, [jak na počítači s Windows v Azure používat klíče SSH](../../virtual-machines/linux/ssh-from-windows.md) nebo [jak na linuxových virtuálních počítačích v Azure vytvářet a používat pár veřejného a privátního klíče SSH](../../virtual-machines/linux/mac-create-ssh-keys.md).

1. Na portálu přejděte ke svému linuxovému virtuálním počítači. V **Přehledu** vyberte **Připojit**.  
2. Připojte se vybraným klientem SSH k virtuálnímu počítači. 
3. V okně terminálu použijte cURL a požádejte místní koncový bod MSI o přístupový token k systému souborů Data Lake Store. Identifikátor prostředku Data Lake Store je https://datalake.azure.net/.  V identifikátoru prostředku musí být koncové lomítko.
    
   ```bash
   curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fdatalake.azure.net%2F' -H Metadata:true   
   ```
    
   V úspěšné odpovědi se vrátí přístupový token, který použijete při ověření přístupu k Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Nástrojem cURL požádejte koncový bod REST systému souborů Data Lake Store o výpis složek v kořenové složce. Je to jednoduchý způsob, jak otestovat, že je vše správně nakonfigurované. Zkopírujte hodnotu přístupového tokenu z předchozího kroku. Je důležité, aby řetězec „Bearer“ v autorizační hlavičce měl na začátku velké B. Název vaší instance Data Lake Store najdete na webu Azure Portal v podokně **Data Lake Store** v oddílu **Přehled**.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Úspěšná odpověď vypadá takto:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Teď můžete zkusit do instance Data Lake Store nahrát soubor. Napřed vytvořte soubor, který chcete nahrát.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Použijte cURL a požádejte koncový bod REST vašeho systému souborů Data Lake Store o nahrání souboru do dříve vytvořené složky. Součástí nahrání je i přesměrování. Nástroj cURL provede přesměrování automaticky. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Úspěšná odpověď vypadá takto:

   ```bash
   HTTP/1.1 100 Continue
   HTTP/1.1 307 Temporary Redirect
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: 756f6b24-0cca-47ef-aa12-52c3b45b954c
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
       
   HTTP/1.1 100 Continue
       
   HTTP/1.1 201 Created
   Cache-Control: no-cache, no-cache, no-store, max-age=0
   Pragma: no-cache
   Expires: -1
   Location: https://mytestadls.azuredatalakestore.net/webhdfs/v1/TestFolder/Test1.txt?op=CREATE&write=true
   x-ms-request-id: af5baa07-3c79-43af-a01a-71d63d53e6c4
   ContentLength: 0
   x-ms-webhdfs-version: 17.04.22.00
   Status: 0x0
   X-Content-Type-Options: nosniff
   Strict-Transport-Security: max-age=15724800; includeSubDomains
   Date: Sun, 15 Oct 2017 22:10:30 GMT
   Content-Length: 0
   ```

Pomocí jiných rozhraní API systému souborů Data Lake Store můžete přidávat do souborů, stahovat soubory a provádět další akce.

Blahopřejeme! Použili jste k ověření přístupu k systému souborů Data Lake Store identitu spravované služby (MSI) na virtuálním počítači s Linuxem.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili používat identitu spravované služby (MSI) na virtuálním počítači s Linuxem pro přístup ke službě Azure Data Lake Store. Další informace o službě Azure Data Lake Store:

> [!div class="nextstepaction"]
>[Azure Data Lake Store](/azure/data-lake-store/data-lake-store-overview)
