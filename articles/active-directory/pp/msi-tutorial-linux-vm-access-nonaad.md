---
title: Použití MSI virtuálního počítače s Linuxem pro přístup k Azure Key Vault
description: Kurz vás provede procesem použití Linux VM Identity spravované služby (MSI) pro přístup k Azure Resource Manageru.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 12/15/2017
ms.author: daveba
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: a6ca37105cfff8542f0c4a8af3112fa317416c56
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2018
ms.locfileid: "38611247"
---
# <a name="use-a-linux-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Použití Linux VM Identity spravované služby (MSI) pro přístup k Azure Key Vault 

[!INCLUDE[preview-notice](~/includes/active-directory-msi-preview-notice-ua.md)]

V tomto kurzu se dozvíte, jak povolit Identity spravované služby (MSI) pro virtuální počítač s Linuxem a použít tuto identitu pro přístup k Azure Key Vault. Slouží jako bootstrap, služby Key Vault umožňuje klientské aplikace pomocí tajného klíče pro přístup k prostředkům, které není zabezpečené pomocí Azure Active Directory (AD). Identita spravované služby je automaticky prováděna nástrojem Azure a umožňují ověření do služeb, které podporují ověřování Azure AD, aniž by bylo nutné vložit pověření do kódu. 

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Povolení MSI na virtuální počítač s Linuxem 
> * Udělení přístupu vašich virtuálních počítačů k tajného klíče do služby Key Vault 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a použít ho k získání tajného klíče ze služby Key Vault 
 
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
6. Vyberte velikost virtuálního počítače. Chcete-li zobrazit další velikosti, vyberte **zobrazit všechny** nebo změňte filtr typu disku podporované. Na stránce nastavení ponechte výchozí nastavení a klikněte na tlačítko **OK**.

## <a name="enable-msi-on-your-vm"></a>Povolení MSI ve virtuálním počítači

MSI virtuálního počítače můžete k získání přístupových tokenů z Azure AD, aniž byste museli vložit pověření do kódu. Pod pokličkou, povolení MSI provede dvě věci: nainstaluje rozšíření MSI virtuálního počítače na virtuálním počítači a umožňují MSI pro virtuální počítač.  

1. Vyberte **virtuální počítač** , že chcete povolit na MSI.
2. Na navigačním panelu vlevo klikněte na tlačítko **konfigurace**.
3. Zobrazí **identita spravované služby**. Chcete-li zaregistrovat a povolit MSI, vyberte **Ano**, pokud chcete zakázat, vyberte Ne.
4. Zajištění kliknutí na **Uložit** uložte konfiguraci.

    ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

5. Pokud chcete zkontrolovat, jaká rozšíření jsou na tomto **virtuálního počítače s Linuxem**, klikněte na tlačítko **rozšíření**. Pokud je povolené MSI, **ManagedIdentityExtensionforLinux** se zobrazí v seznamu.

    ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-linux-vm-access-arm/msi-extension-value.png)


## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Váš přístup k virtuálním počítačům přidělit tajného klíče do služby Key Vault  

Použití MSI kódu můžete získat přístupové tokeny k ověření prostředků, které podporují ověřování pomocí Azure Active Directory. Ale ne všechny služby Azure podporuje ověřování Azure AD. Použití MSI pomocí těchto služeb, pověření služeb ukládat ve službě Azure Key Vault a použití MSI pro přístup ke Key Vault načíst přihlašovací údaje. 

Nejprve musíme vytvořit trezor klíčů a udělit přístup identit našich virtuálních počítačů do služby Key Vault.   

1. V horní části levého navigačního panelu vyberte **+ nová** pak **zabezpečení + Identita** pak **služby Key Vault**.  
2. Zadejte **název** pro nové služby Key Vault. 
3. Vyhledejte služby Key Vault stejného předplatného a skupiny prostředků jako virtuální počítač, který jste vytvořili dříve. 
4. Vyberte **zásady přístupu** a klikněte na tlačítko **přidat nový**. 
5. V konfigurace ze šablony, vyberte **správy tajných kódů**. 
6. Zvolte **výběr objektu zabezpečení**a do vyhledávacího pole zadejte název virtuálního počítače, které jste vytvořili dříve.  V seznamu výsledků vyberte virtuální počítač a klikněte na tlačítko **vyberte**. 
7. Klikněte na tlačítko **OK** k dokončení přidání nové zásady přístupu a **OK** dokončete výběr zásad přístupu. 
8. Klikněte na tlačítko **vytvořit** na dokončení vytvoření služby Key Vault. 

    ![Text ALT obrázku](~/articles/active-directory/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)

V dalším kroku přidání tajného klíče do služby Key Vault tak, aby později můžete získat tajný kód pomocí kódu je spuštěný ve virtuálním počítači: 

1. Vyberte **všechny prostředky**a vyhledejte a vyberte trezor klíčů, který jste vytvořili. 
2. Vyberte **tajných kódů**a klikněte na tlačítko **přidat**. 
3. Vyberte **ruční**, z **možnosti nahrání**. 
4. Zadejte název a hodnotu pro tajný kód.  Hodnota může být cokoliv, co chcete. 
5. Datum aktivace a datum vypršení platnosti jasné a dál necháte **povoleno** jako **Ano**. 
6. Klikněte na tlačítko **vytvořit** vytvořit tajný kód. 
 
## <a name="get-an-access-token-using-the-vms-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Získání přístupového tokenu pomocí identity Virtuálního počítače a použít ho k získání tajného klíče ze služby Key Vault  

K dokončení těchto kroků, budete potřebovat klienta SSH.  Pokud používáte Windows, můžete použít klienta SSH v [subsystém Windows pro Linux](https://msdn.microsoft.com/commandline/wsl/about). Pokud potřebujete pomoc, konfigurace klíčů vašeho klienta SSH, přečtěte si téma [jak používat klíče SSH s Windows v Azure](~/articles/virtual-machines/linux/ssh-from-windows.md), nebo [postupy vytváření a používání veřejných a privátních pár klíčů SSH pro virtuální počítače s Linuxem v Azure](~/articles/virtual-machines/linux/mac-create-ssh-keys.md).
 
1. Na portálu přejděte k virtuálním počítačům s Linuxem a v **přehled**, klikněte na tlačítko **připojit**. 
2. **Připojit** k virtuálnímu počítači s klientem SSH podle vašeho výběru. 
3. V okně terminálu pomocí příkazu CURL, ujistěte se, požadavek na místní koncový bod MSI pro získání přístupového tokenu pro Azure Key Vault.  
 
    CURL žádost o přístupový token je níže.  
    
    ```bash
    curl http://localhost:50342/oauth2/token --data "resource=https://vault.azure.net" -H Metadata:true  
    ```
    Odpověď obsahuje přístupový token, je potřeba přístup k Resource Manageru. 
    
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
    
    Tento token přístupu můžete použít k ověření do služby Azure Key Vault.  Další požadavek CURL znázorňuje způsob čtení tajného klíče ze služby Key Vault pomocí CURL a rozhraní REST API Key Vault.  Budete potřebovat adresu URL služby Key Vault, což je v **Essentials** část **přehled** stránky vaší služby Key Vault.  Budete také potřebovat přístupový token, který jste získali v předchozí volání. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Odpověď bude vypadat nějak takto: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Jakmile jste načíst tajného klíče ze služby Key Vault, můžete k ověření služby, který vyžaduje název a heslo.


## <a name="related-content"></a>Související obsah

- Přehled MSI najdete v tématu [identita spravované služby přehled](msi-overview.md).

Pomocí následujícího oddílu pro komentáře na svůj názor a Pomozte nám vylepšit a obrazce náš obsah.




