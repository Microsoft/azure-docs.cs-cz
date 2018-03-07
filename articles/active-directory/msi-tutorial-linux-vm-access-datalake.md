---
title: "Identita spravované služby, pro virtuální počítač s Linuxem používat pro přístup k Azure Data Lake Store"
description: "Kurz ukazuje, jak spravovat službu Identity (MSI) pro virtuální počítač s Linuxem používat pro přístup k Azure Data Lake Store."
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
ms.date: 11/20/2017
ms.author: skwan
ms.openlocfilehash: fdf1c49c644a97ff2f66a8b217ee54896ed54131
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/03/2018
---
# <a name="use-managed-service-identity-for-a-linux-vm-to-access-azure-data-lake-store"></a>Identita spravované služby, pro virtuální počítač s Linuxem používat pro přístup k Azure Data Lake Store

[!INCLUDE[preview-notice](../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak používat identita spravované služby pro systém Linux virtuálního počítače (VM) pro přístup k Azure Data Lake Store. Azure automaticky spravuje identity, které vytvoříte pomocí Instalační služby MSI. MSI můžete použít k ověření služby, které podporují ověřování Azure Active Directory (Azure AD), aniž by museli přihlašovací údaje vložit do vašeho kódu. 

V tomto kurzu se naučíte:

> [!div class="checklist"]
> * Povolte MSI na virtuální počítač s Linuxem. 
> * Udělte přístup virtuálních počítačů do Azure Data Lake Store.
> * Získání tokenu přístupu pomocí identita virtuálního počítače a použít ho pro přístup k Azure Data Lake Store.

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu [Azure Portal](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvořit virtuální počítač s Linuxem do nové skupiny prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Linuxem. Můžete také povolit MSI na existující virtuální počítač.

1. Vyberte **nový** tlačítko v levém horním rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. Pro **typ ověřování**, vyberte **veřejný klíč SSH** nebo **heslo**. Vytvořené pověření umožňují přihlášení k virtuálnímu počítači.

   ![Podokno "Základy" k vytvoření virtuálního počítače](media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. V **předplatné** seznamu, vyberte předplatné, pro virtuální počítač.
5. Chcete-li vybrat novou skupinu prostředků, které chcete virtuální počítač, který má být vytvořen v, vyberte **skupiny prostředků** > **vytvořit nový**. Po dokončení, vyberte **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. V podokně nastavení ponechat výchozí nastavení a vyberte **OK**.

## <a name="enable-msi-on-your-vm"></a>Povolit MSI na vašem virtuálním počítači

MSI pro virtuální počítač můžete získat přístupové tokeny z Azure AD bez nutnosti uvést přihlašovací údaje do vašeho kódu. Povolení MSI nainstaluje rozšíření virtuálního počítače MSI na vašem virtuálním počítači, a umožňuje MSI ve službě Správce prostředků Azure.  

1. Pro **virtuálního počítače**, vyberte virtuální počítač, který chcete povolit MSI v.
2. V levém podokně vyberte **konfigurace**.
3. Zobrazí **identita spravované služby**. Registrovat a povolit MSI, vyberte **Ano**. Pokud chcete zakázat, vyberte **ne**.
   ![Výběr "Registrovat s Azure Active Directory"](media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)
4. Vyberte **Uložit**.
5. Pokud chcete zkontrolovat, jaká rozšíření se na tento virtuální počítač Linux, vyberte **rozšíření**. Pokud je povoleno MSI, **ManagedIdentityExtensionforLinux** se zobrazí v seznamu.

   ![Seznam přípon](media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

## <a name="grant-your-vm-access-to-azure-data-lake-store"></a>Udělit přístup virtuálních počítačů do Azure Data Lake Store

Nyní můžete udělit váš virtuální počítač přístup k souborům a složkám v Azure Data Lake Store. Pro tento krok můžete použít existující instanci Data Lake Store nebo vytvořte novou. Chcete-li vytvořit instanci Data Lake Store pomocí portálu Azure, postupujte [rychlý start Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-get-started-portal). Existují také – elementy QuickStart, použít rozhraní příkazového řádku Azure a prostředí Azure PowerShell v [dokumentace Azure Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-overview).

V Data Lake Store vytvořte novou složku a udělení MSI oprávnění ke čtení, zápisu a spouštět soubory v této složce:

1. Na portálu Azure vyberte **Data Lake Store** v levém podokně.
2. Vyberte instanci Data Lake Store, který chcete použít.
3. Vyberte **Průzkumníku dat** na panelu příkazů.
4. Je vybrána složka kořenové instance Data Lake Store. Vyberte **přístup** na panelu příkazů.
5. Vyberte **Přidat**.  V **vyberte** pole, zadejte název vašeho virtuálního počítače – například **DevTestVM**. Ve výsledcích hledání vyberte virtuální počítač a potom klikněte na **vyberte**.
6. Klikněte na tlačítko **vyberte oprávnění**.  Vyberte **čtení** a **Execute**, přidejte do **tato složka**a přidat jako **oprávnění přístupu jenom**. Vyberte **Ok**.  Oprávnění by měla být úspěšně přidají.
7. Zavřít **přístup** podokně.
8. V tomto kurzu vytvořte novou složku. Vyberte **novou složku** na panelu příkazů a udělte do nové složky název – například **TestFolder**.  Vyberte **Ok**.
9. Vyberte složku, kterou jste vytvořili a potom vyberte **přístup** na panelu příkazů.
10. Podobně jako ke kroku 5, vyberte **přidat**. V **vyberte** zadejte název vašeho virtuálního počítače. Ve výsledcích hledání vyberte virtuální počítač a potom klikněte na **vyberte**.
11. Podobně jako krok 6, vyberte **vyberte oprávnění**. Vyberte **čtení**, **zápisu**, a **Execute**, přidejte do **tato složka**a přidejte jako **položka oprávnění k přístupu a výchozí Položka oprávnění**. Vyberte **Ok**.  Oprávnění by měla být úspěšně přidají.

MSI teď můžete provádět všechny operace se soubory ve složce, kterou jste vytvořili. Další informace o správě přístupu k Data Lake Store najdete v tématu [řízení přístupu v Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-and-call-the-data-lake-store-file-system"></a>Získání přístupového tokenu a volání systému souborů Data Lake Store

Azure Data Lake Store nativně podporuje Azure AD ověřování, mohl přímo přijímat tokeny přístupu získat prostřednictvím MSI. K ověření do systému souborů Data Lake Store, můžete odeslat přístupový token vydán Azure AD pro koncový bod služby Data Lake Store soubor systému. Přístupový token je v hlavičce autorizace ve formátu "nosiče \<ACCESS_TOKEN_VALUE\>".  Další informace o podpoře Data Lake Store pro ověřování Azure AD najdete v tématu [ověřování s Data Lake Store pomocí Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).

V tomto kurzu ověřování rozhraní REST API pro systém souborů Data Lake Store pomocí cURL provádět požadavky na REST.

> [!NOTE]
> Klientské sady SDK pro systém souborů Data Lake Store zatím nepodporují identita spravované služby.

K dokončení těchto kroků, potřebujete klientem SSH. Pokud používáte systém Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc konfigurace klíče klient SSH, přečtěte si téma [klíče použití SSH se systémem Windows v Azure](../virtual-machines/linux/ssh-from-windows.md) nebo [jak vytvořit a používat SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem v Azure](../virtual-machines/linux/mac-create-ssh-keys.md).

1. Na portálu přejděte k virtuálním počítačům s Linuxem. V **přehled**, vyberte **Connect**.  
2. Připojte k virtuálnímu počítači pomocí SSH klienta podle vašeho výběru. 
3. V okně terminálu pomocí cURL, vytvořte žádost na místní koncový bod MSI získat přístupový token pro systém souborů Data Lake Store. Identifikátor prostředku pro Data Lake Store je "https://datalake.azure.net/".  Je důležité zahrnout do adresy koncové lomítko identifikátor prostředku.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Úspěšná odpověď vrátí přístupový token, který použijete k ověření do Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Pomocí cURL vytvořte žádost na koncový bod REST systému souborů Data Lake Store k zobrazení seznamu složek v kořenové složce. Toto je jednoduchý způsob, jak zkontrolujte, jestli všechno, co je nakonfigurované správně. Zkopírujte hodnotu tokenu přístupu z předchozího kroku. Je důležité, aby řetězec "Nosiče" v hlavičce autorizace obsahuje velké "B." Můžete najít název instance Data Lake Store v **přehled** části **Data Lake Store** podokně na portálu Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Úspěšná odpověď vypadá takto:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Nyní můžete zkusit nahrání souboru do Data Lake Store instanci. Nejprve vytvořte soubor k odeslání.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Pomocí cURL vytvořte žádost na koncový bod REST systému souborů Data Lake Store nahrát soubor do složky, kterou jste vytvořili dříve. Nahrávání zahrnuje přesměrování a cURL sleduje přesměrování automaticky. 

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

Pomocí jiných rozhraní API pro systém souborů Data Lake Store přidáte-li soubory, soubory ke stažení a další.

Blahopřejeme! Došlo k ověření do systému souborů Data Lake Store pomocí Instalační služby MSI pro virtuální počítač s Linuxem.

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](../active-directory/msi-overview.md).
- Data Lake Store pro operace správy, používá Azure Resource Manager.  Další informace o použití Instalační služby MSI k ověření pro správce prostředků najdete v tématu [používat Linux virtuálního počítače spravované služby Identity (MSI) pro přístup k Resource Manager](https://docs.microsoft.com/azure/active-directory/msi-tutorial-linux-vm-access-arm).
- Další informace o [ověřování s Data Lake Store pomocí Azure Active Directory](https://docs.microsoft.com/azure/data-lake-store/data-lakes-store-authentication-using-azure-active-directory).
- Další informace o [souboru operace systému v Azure Data Lake Store pomocí rozhraní REST API](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-data-operations-rest-api) nebo [rozhraní API webhdfs, které FileSystem](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Další informace o [řízení přístupu v Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.