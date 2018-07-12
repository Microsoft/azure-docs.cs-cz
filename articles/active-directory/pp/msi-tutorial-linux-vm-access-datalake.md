---
title: Jak používat Linux VM Identity spravované služby (MSI) pro přístup k Azure Data Lake Store
description: Kurz, který popisuje, jak používat Linux VM Identity spravované služby (MSI) pro přístup k Azure Data Lake Store.
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
ms.openlocfilehash: 358827722e8d77cd91410fae842ad2ba99967d98
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38610350"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-data-lake-store"></a>Použití Linux VM Identity spravované služby (MSI) pro přístup k Azure Data Lake Store

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

V tomto kurzu se dozvíte, jak používat Identity spravované služby (MSI) pro virtuální počítač s Linuxem (VM) pro přístup Azure Data Lake Store. Identita spravované služby je automaticky prováděna nástrojem Azure a umožňují ověření do služeb, které podporují ověřování Azure AD, aniž by bylo nutné vložit pověření do kódu. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolení MSI na virtuální počítač s Linuxem 
> * Udělení přístupu vašich virtuálních počítačů do Azure Data Lake Store
> * Získání přístupového tokenu pomocí identity virtuálního počítače a použít ho pro přístup Azure Data Lake Store

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-core-prereqs](~/includes/active-directory-msi-core-prereqs-ua.md)]

[!INCLUDE [msi-tut-prereqs](~/includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Linuxem do nové skupiny prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Linuxem. Můžete také povolit MSI na existující virtuální počítač.

1. Klikněte na tlačítko **vytvořit prostředek** v levém horním rohu webu Azure portal.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. Pro **typ ověřování**vyberte **veřejný klíč SSH** nebo **heslo**. Vytvořené pověření umožňují přihlášení k virtuálnímu počítači.

   ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Zvolte **předplatné** pro virtuální počítač v rozevírací nabídce.
5. Chcete-li vybrat nový **skupiny prostředků** chcete virtuální počítač, aby se v aplikaci, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Chcete-li zobrazit další velikosti, vyberte **zobrazit všechny** nebo změňte filtr typu disku podporované. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

## <a name="enable-msi-on-your-vm"></a>Povolení MSI ve virtuálním počítači

MSI virtuálního počítače můžete k získání přístupových tokenů z Azure AD, aniž byste museli vložit pověření do kódu. Pod pokličkou, povolení MSI provede dvě věci: nainstaluje rozšíření MSI virtuálního počítače na virtuálním počítači a umožňuje MSI v Azure Resource Manageru.  

1. Vyberte **virtuální počítač** , že chcete povolit na MSI.
2. Na navigačním panelu vlevo klikněte na tlačítko **konfigurace**.
3. Zobrazí **identita spravované služby**. Chcete-li zaregistrovat a povolit MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne.
4. Zajištění kliknutí na **Uložit** uložte konfiguraci.

   ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Pokud chcete zkontrolovat, jaká rozšíření jsou na tomto **virtuálního počítače s Linuxem**, klikněte na tlačítko **rozšíření**. Pokud je povolené MSI, **ManagedIdentityExtensionforLinux** se zobrazí v seznamu.

   ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)

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

Vaše MSI virtuálního počítače teď můžete provádět všechny operace se soubory ve složce, kterou jste vytvořili.  Další informace o správě přístupu k Data Lake Store, najdete v tomto článku [řízení přístupu v Data Lake Store](https://docs.microsoft.com/azure/data-lake-store/data-lake-store-access-control).

## <a name="get-an-access-token-using-the-vm-msi-and-use-it-to-call-the-azure-data-lake-store-filesystem"></a>Získání přístupového tokenu pomocí MSI virtuálního počítače a použít jej k vyvolání systému souborů Azure Data Lake Store

Azure Data Lake Store nativně podporuje Azure AD ověřování, aby mohl přijímat přímo přístupové tokeny získat pomocí Instalační služby MSI.  K ověření systému souborů Data Lake Store odeslat přístupový token vydala Azure AD do Data Lake Store filesystem koncového bodu, v hlavičce autorizace ve formátu "nosiče \<ACCESS_TOKEN_VALUE\>".  Další informace o podpoře služby Data Lake Store pro ověřování Azure AD, [ověřování s Data Lake Store pomocí Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md)

V tomto kurzu se ověřujete na systém souborů Data Lake Store, které požadavky rozhraní REST API služby REST pomocí CURL.

> [!NOTE]
> Data Lake Store filesystem klientské sady SDK zatím ještě nepodporují identita spravované služby.  V tomto kurzu bude aktualizován, když dojde k přidání podpory pro sadu SDK.

K dokončení těchto kroků, budete potřebovat klienta SSH. Pokud používáte Windows, můžete použít klienta SSH v [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc, konfigurace klíčů vašeho klienta SSH, přečtěte si téma [jak používat klíče SSH s Windows v Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), nebo [postupy vytváření a používání veřejných a privátních pár klíčů SSH pro virtuální počítače s Linuxem v Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).

1. Na portálu přejděte k virtuálním počítačům s Linuxem a v **přehled**, klikněte na tlačítko **připojit**.  
2. **Připojit** k virtuálnímu počítači s klientem SSH podle vašeho výběru. 
3. V okně terminálu pomocí příkazu CURL, ujistěte se, požadavek na místní koncový bod MSI pro získání přístupového tokenu pro systém souborů Data Lake Store.  Identifikátor prostředku pro Data Lake Store je "https://datalake.azure.net/."  Je důležité zahrnout do adresy koncové lomítko identifikátor prostředku.
    
   ```bash
   curl http://localhost:50342/oauth2/token --data "resource=https://datalake.azure.net/" -H Metadata:true   
   ```
    
   Přístupový token, který použijete k ověřování Data Lake Store návratu úspěšné odpovědi:

   ```bash
   {"access_token":"eyJ0eXAiOiJ...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1508119757",
    "not_before":"1508115857",
    "resource":"https://datalake.azure.net/",
    "token_type":"Bearer"}
   ```

4. Pomocí příkazu CURL, vytvořte žádost na vaše Data Lake Store filesystem koncový bod REST do seznamu složek v kořenové složce.  Toto je jednoduchý způsob, jak zkontrolovat, že je všechno správně nastavené.  Zkopírujte hodnotu přístupového tokenu z předchozího kroku.  Je důležité, že řetězec "Nosiče" v hlavičce autorizace má velké "B".  Můžete najít název vaše Data Lake Store v **přehled** okna Data Lake Store na webu Azure Portal.

   ```bash
   curl https://<YOUR_ADLS_NAME>.azuredatalakestore.net/webhdfs/v1/?op=LISTSTATUS -H "Authorization: Bearer <ACCESS_TOKEN>"
   ```
    
   Úspěšná odpověď vypadá takto:

   ```bash
   {"FileStatuses":{"FileStatus":[{"length":0,"pathSuffix":"TestFolder","type":"DIRECTORY","blockSize":0,"accessTime":1507934941392,"modificationTime":1508105430590,"replication":0,"permission":"770","owner":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071","group":"bd0e76d8-ad45-4fe1-8941-04a7bf27f071"}]}}
   ```

5. Teď můžete zkusit po nahrání souboru do vaše Data Lake Store.  Nejprve vytvořte soubor k odeslání.

   ```bash
   echo "Test file." > Test1.txt
   ```

6. Pomocí příkazu CURL, vytvořte žádost na vaše Data Lake Store filesystem koncový bod REST pro nahrání souboru do složky, kterou jste vytvořili dříve.  Nahrávání zahrnuje přesměrování a CURL automaticky sleduje přesměrování. 

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

Další systémem souborů Data Lake Store pomocí rozhraní API, můžete připojit k souborům, stáhnout soubory a další.

Blahopřejeme!  Jste ověřeni pro systém souborů Data Lake Store pomocí MSI virtuálního počítače.

## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).
- Pro správu operations Data Lake Store pomocí Azure Resource Manageru.  Další informace o použití MSI virtuálního počítače k ověření do Resource Manageru, najdete v článku [používat pro přístup k Resource Manageru Linux VM Identity spravované služby (MSI)](../managed-service-identity/msi-tutorial-linux-vm-access-arm.md).
- Další informace o [ověřování s Data Lake Store pomocí Azure Active Directory](~/articles/data-lake-store/data-lakes-store-authentication-using-azure-active-directory.md).
- Další informace o [operace systému souborů v Azure Data Lake Store pomocí rozhraní REST API](~/articles/data-lake-store/data-lake-store-data-operations-rest-api.md) nebo [rozhraní FileSystem API WebHDFS](https://docs.microsoft.com/rest/api/datalakestore/webhdfs-filesystem-apis).
- Další informace o [řízení přístupu v Data Lake Store](~/articles/data-lake-store/data-lake-store-access-control.md).

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.