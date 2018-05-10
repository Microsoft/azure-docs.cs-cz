---
title: Používat pro přístup k Azure Key Vault MSI virtuálních počítačů Linux
description: Kurz vás provede procesem pomocí Linux virtuálního počítače spravované služby Identity (MSI) pro přístup k Azure Resource Manager.
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
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: bdd57e33e53bd8b3d83f8279bed5413ccd556549
ms.sourcegitcommit: d98d99567d0383bb8d7cbe2d767ec15ebf2daeb2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/10/2018
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Používat pro přístup k Azure Key Vault Linux virtuálního počítače spravované služby Identity (MSI) 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak povolit identita spravované služby (MSI) pro virtuální počítač s Linuxem a pak tuto identitu používat pro přístup k Azure Key Vault. Slouží jako bootstrap, Key Vault umožňuje použít tajný klíč pro přístup k prostředkům, které není zabezpečené pomocí Azure Active Directory (AD), pak klientské aplikaci. Identita spravované služby je automaticky prováděna nástrojem Azure a umožňují ověření pro služby, které podporují ověřování Azure AD, aniž by museli přihlašovací údaje vložit do vašeho kódu. 

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolit MSI na virtuální počítač s Linuxem 
> * Udělit přístup virtuálních počítačů k tajného klíče uložené v Key Vault 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a použít ho k načtení tajného klíče z trezoru klíčů 
 
## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure
Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com). 

## <a name="create-a-linux-virtual-machine-in-a-new-resource-group"></a>Vytvořit virtuální počítač s Linuxem do nové skupiny prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Linuxem. Můžete také povolit MSI na existující virtuální počítač.

1. Klikněte na tlačítko **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2. Vyberte **Compute** a potom vyberte **Ubuntu Server 16.04 LTS**.
3. Zadejte informace o virtuálním počítači. Pro **typ ověřování**, vyberte **veřejný klíč SSH** nebo **heslo**. Vytvořené pověření umožňují přihlášení k virtuálnímu počítači.

    ![Obrázek alternativní text](../media/msi-tutorial-linux-vm-access-arm/msi-linux-vm.png)

4. Vyberte **předplatné** pro virtuální počítač v rozevírací nabídce.
5. Chcete-li vybrat nový **skupiny prostředků** chcete virtuální počítač lze vytvořit v, zvolte **vytvořit nový**. Jakmile budete hotovi, klikněte na **OK**.
6. Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **zobrazit všechny** nebo změňte typ filtru disku podporované. Na stránce nastavení ponechejte výchozí hodnoty a klikněte na tlačítko **OK**.

## <a name="enable-msi-on-your-vm"></a>Povolit MSI na vašem virtuálním počítači

Virtuální počítač MSI umožňuje získat přístupové tokeny z Azure AD, aniž by bylo třeba uvést přihlašovací údaje do vašeho kódu. Povolení spravovat Identity služby na virtuálním počítači, nemá dvě věci: zaregistruje virtuální počítač s Azure Active Directory k vytvoření jeho spravovanou identitu a nakonfiguruje identitu ve virtuálním počítači.

1. Vyberte **virtuálního počítače** , které chcete povolit MSI v.
2. V levém navigačním panelu klikněte na tlačítko **konfigurace**.
3. Zobrazí **identita spravované služby**. Registrovat a povolit soubor MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne.
4. Ujistěte se, kliknete na tlačítko **Uložit** konfiguraci uložíte.

    ![Obrázek alternativní text](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Udělit přístup virtuálních počítačů tajného klíče uložené v Key Vault  

Pomocí Instalační služby MSI kódu můžete získat přístupové tokeny k ověřování k prostředkům, které podporují ověřování Azure Active Directory. Ne všechny služby Azure, ale podporují ověřování Azure AD. Chcete-li MSI pomocí těchto služeb, uložit přihlašovací údaje služby Azure Key Vault a použijte MSI pro přístup k Key Vault se načíst přihlašovací údaje. 

Nejprve musíme vytvořit Key Vault a udělit přístup identity naše Virtuálního počítače do služby Key Vault.   

1. V horní části na levém navigačním panelu, vyberte **vytvořit prostředek** > **zabezpečení a identita** > **Key Vault**.  
2. Zadejte **název** pro nové Key Vault. 
3. Vyhledejte Key Vault ve stejné skupině předplatného a prostředků jako virtuální počítač, který jste vytvořili dříve. 
4. Vyberte **zásady přístupu** a klikněte na tlačítko **přidat nový**. 
5. V konfigurace ze šablony, vyberte **správu tajný klíč**. 
6. Zvolte **vyberte hlavní**a do pole vyhledávání zadejte název virtuálního počítače, které jste vytvořili dříve.  V seznamu výsledků vyberte virtuální počítač a klikněte na tlačítko **vyberte**. 
7. Klikněte na tlačítko **OK** k dokončení přidání nové zásady přístupu a **OK** dokončete výběr zásad přístupu. 
8. Klikněte na tlačítko **vytvořit** dokončete vytváření Key Vault. 

    ![Obrázek alternativní text](../media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

V dalším kroku přidejte tajného klíče do služby Key Vault, tak, aby později můžete načíst tajný klíč pomocí kód spuštěný ve vašem virtuálním počítači: 

1. Vyberte **všechny prostředky**a najděte a vyberte Key Vault, které jste vytvořili. 
2. Vyberte **tajné klíče**a klikněte na tlačítko **přidat**. 
3. Vyberte **ruční**, z **možnosti odesílání**. 
4. Zadejte název a hodnota pro tajný klíč.  Hodnota může být nic, co chcete. 
5. Nechte aktivace datum a datum vypršení platnosti, zrušte zaškrtnutí a nechte **povoleno** jako **Ano**. 
6. Klikněte na tlačítko **vytvořit** vytvořit tajný klíč. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Získání přístupového tokenu pomocí identity Virtuálního počítače a použít ho k načtení tajného klíče z trezoru klíčů  

K dokončení těchto kroků, potřebujete klientem SSH.  Pokud používáte systém Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc konfigurace klíče klient SSH, přečtěte si téma [jak klíče použití SSH se systémem Windows v Azure](../../virtual-machines/linux/ssh-from-windows.md), nebo [jak vytvořit a používat SSH pár veřejného a privátního klíče pro virtuální počítače s Linuxem v Azure](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. Na portálu, přejděte k virtuálním počítačům s Linuxem a v **přehled**, klikněte na tlačítko **Connect**. 
2. **Připojit** na virtuální počítač s klientem SSH podle svého výběru. 
3. V okně terminálu pomocí CURL, vytvořte žádost na místní koncový bod MSI k získání tokenu přístupu pro Azure Key Vault.  
 
    Níže je CURL požadavek na přístupový token.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    Odpověď obsahuje přístupový token, je třeba získat přístup správce prostředků. 
    
    Odpověď:  
    
    ```bash
    {"access_token":"eyJ0eXAi...",
    "refresh_token":"",
    "expires_in":"3599",
    "expires_on":"1504130527",
    "not_before":"1504126627",
    "resource":"https://vault.azure.net",
    "token_type":"Bearer"} 
    ```
    
    Tento token přístupu můžete použít k ověření do Azure Key Vault.  Další požadavek CURL ukazuje, jak číst tajného klíče z trezoru klíč pomocí CURL a REST API trezoru klíč.  Budete potřebovat adresu URL služby Key Vault, která se **Essentials** části **přehled** stránka služby Key Vault.  Budete také potřebovat přístupový token, který jste získali v předchozí volání. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Odpověď bude vypadat takto: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Jakmile jste načíst tajného klíče z trezoru klíčů, můžete ke svému ověření u služby, která vyžaduje zadání jména a hesla.


## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](overview.md).

Použijte následující sekci komentáře k poskytnutí zpětné vazby a Pomozte nám vylepšit a utvářejí náš obsah.




