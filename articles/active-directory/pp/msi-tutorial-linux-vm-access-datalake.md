---
title: Jak používat Linux virtuálního počítače spravované služby Identity (MSI) pro přístup k Azure Data Lake Store
description: Kurz ukazuje, jak používat pro přístup k Azure Data Lake Store Linux virtuálního počítače spravované služby Identity (MSI).
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
ms.openlocfilehash: a70f02fca5ebf575bc009623c3af648a5a80fd70
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2018
ms.locfileid: "32153740"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Používat pro přístup k Azure Data Lake Store Linux virtuálního počítače spravované služby Identity (MSI)

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

V tomto kurzu se dozvíte, jak používat a spravovat službu Identity (MSI) pro systém Linux virtuálního počítače (VM) pro přístup k Azure Data Lake Store. Identita spravované služby je automaticky prováděna nástrojem Azure a umožňují ověření pro služby, které podporují ověřování Azure AD, aniž by museli přihlašovací údaje vložit do vašeho kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolit MSI na virtuální počítač s Linuxem 
> * Udělit přístup virtuálních počítačů Azure Data Lake Store
> * Získání přístupového tokenu pomocí identity virtuálního počítače a použít ho pro přístup k Azure Data Lake Store

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvořit virtuální počítač s Linuxem do nové skupiny prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Linuxem. Můžete také povolit MSI na existující virtuální počítač.

1. Klikněte na tlačítko **vytvořit prostředek** v levém horním rohu portálu Azure.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. Pro **typ ověřování**, vyberte **veřejný klíč SSH** nebo **heslo**. Vytvořené pověření umožňují přihlášení k virtuálnímu počítači.

   ![Obrázek alternativní text](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Vyberte **předplatné** pro virtuální počítač v rozevírací nabídce.
5. Chcete-li vybrat nový **skupiny prostředků** chcete virtuální počítač lze vytvořit v, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **zobrazit všechny** nebo změňte typ filtru disku podporované. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

## <a name="enable-msi-on-your-vm"></a>Povolit MSI na vašem virtuálním počítači

Virtuální počítač MSI umožňuje získat přístupové tokeny z Azure AD, aniž by bylo třeba uvést přihlašovací údaje do vašeho kódu. V pozadí, povolení MSI provádí dvě věci: nainstaluje rozšíření virtuálního počítače MSI na vašem virtuálním počítači a umožňuje MSI ve službě Správce prostředků Azure.  

1. Vyberte **virtuálního počítače** , které chcete povolit MSI v.
2. V levém navigačním panelu klikněte na tlačítko **konfigurace**.
3. Zobrazí **identita spravované služby**. Registrovat a povolit soubor MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne.
4. Ujistěte se, kliknete na tlačítko **Uložit** konfiguraci uložíte.

   ![Obrázek alternativní text](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Pokud chcete zkontrolovat, jaká rozšíření jsou v tomto **virtuálního počítače s Linuxem**, klikněte na tlačítko **rozšíření**. Pokud je povoleno MSI, **ManagedIdentityExtensionforLinux** se zobrazí v seznamu.

   ![Obrázek alternativní text](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

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

Vaše MSI virtuálního počítače teď může provádět všechny operace na soubory ve složce, kterou jste vytvořili.  Pro další informace o správě přístupu k Data Lake Store, přečtěte si tento článek na [řízení přístupu v Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Získání přístupového tokenu pomocí Instalační služby MSI virtuálního počítače a použít jej k vyvolání systém souborů Azure Data Lake Store

Azure Data Lake Store nativně podporuje Azure AD ověřování, mohl přímo přijímat tokeny přístupu získat pomocí Instalační služby MSI.  K ověření na systém souborů Data Lake Store odeslat přístupový token vydán Azure AD pro svůj koncový bod Data Lake Store systému souborů, v hlavičce autorizace ve formátu "nosiče \<ACCESS_TOKEN_VALUE\>".  Další informace o Data Lake Store podporu pro ověřování Azure AD, přečtěte si [ověřování s Data Lake Store pomocí Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

V tomto kurzu ověřovat Data Lake Store systém souborů, které požadavky REST API pomocí CURL REST.

> [!NOTE]
> Klient systému souborů pro Data Lake Store SDK zatím nepodporují identita spravované služby.  V tomto kurzu se aktualizují, když dojde k přidání podpory k sadě SDK.

K dokončení těchto kroků, potřebujete klientem SSH. Pokud používáte systém Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc konfigurace klíče klient SSH, přečtěte si téma [jak klíče použití SSH se systémem Windows v Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), nebo [jak vytvořit a používat SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem v Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Na portálu, přejděte k virtuálním počítačům s Linuxem a v **přehled**, klikněte na tlačítko **Connect**.  
2. **Připojit** na virtuální počítač s klientem SSH podle svého výběru. 
3. V okně terminálu pomocí CURL, vytvořte žádost na místní koncový bod MSI k získání tokenu přístupu pro systém souborů Data Lake Store.  Identifikátor prostředku pro Data Lake Store je "https://datalake.azure.net/."  Je důležité zahrnout do adresy koncové lomítko identifikátor prostředku.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Úspěšná odpověď vrátí přístupový token, který používáte k ověřování pro Data Lake Store:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Pomocí CURL, vytvořte žádost na vaše Data Lake Store filesystem koncový bod REST seznam složky v kořenové složce.  Toto je jednoduchý způsob, jak zkontrolovat, že je všechno správně nastavené.  Zkopírujte hodnotu tokenu přístupu z předchozího kroku.  Je důležité, že řetězec "Nosiče" v hlavičce autorizace obsahuje velké "B".  Můžete najít název Data Lake Store v **přehled** části okně Data Lake Store na portálu Azure.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Úspěšná odpověď bude vypadat takto:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Nyní můžete zkusit nahrání souboru do Data Lake Store.  Nejprve vytvořte soubor k odeslání.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Pomocí CURL, vytvořte žádost na koncový bod systému souborů REST Data Lake Store nahrát soubor do složky, kterou jste vytvořili dříve.  Nahrávání zahrnuje přesměrování a CURL sleduje přesměrování automaticky. 

   ```bash
   curl -i -X PUT -L -T Test1.txt -H "Authorization: Bearer <ACCESS_TOKEN>" 'https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/<FOLDER_NAME>/Test1.txt?op=CREATE' 
   ```

    Úspěšná odpověď bude vypadat takto:

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

Filesystem další Data Lake Store pomocí rozhraní API můžete připojit k souborům, stahovat soubory a další.

Blahopřejeme!  Jste ověřeni pro systém souborů Data Lake Store pomocí MSI virtuálních počítačů.

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Pro správu operations Data Lake Store využívá Azure Resource Manager.  Další informace o používání virtuálních počítačů MSI k ověření do Resource Manager, najdete v tématu [používat Linux virtuálního počítače spravované služby Identity (MSI) pro přístup k Resource Manager](../managed-service-identity/msi-tutorial-linux-vm-access-arm.md).
- Další informace o [ověřování s Data Lake Store pomocí Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Další informace o [operací systému souborů v Azure Data Lake Store pomocí rozhraní REST API](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) nebo [rozhraní API webhdfs, které FileSystem](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis.md).
- Další informace o [řízení přístupu v Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.