---
title: Použití MSI virtuálního počítače s Windows pro přístup ke službě Azure Key Vault
description: Tento kurz vás provede procesem použití Identity spravované služby (MSI) virtuálního počítače s Windows pro přístup ke službě Azure Key Vault.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: daveba
ms.service: active-directory
ms.component: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/20/2017
ms.author: daveba
ms.openlocfilehash: 81509108060b636e47154a8c375f5569cac73648
ms.sourcegitcommit: d551ddf8d6c0fd3a884c9852bc4443c1a1485899
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/07/2018
ms.locfileid: "37902730"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-msi-to-access-azure-key-vault"></a>Kurz: Použití Identity spravované služby (MSI) virtuálního počítače s Windows pro přístup ke službě Azure Key Vault 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak povolit identitu spravované služby (MSI) na virtuálním počítači s Windows a pak ji použít pro přístup ke službě Azure Key Vault. Key Vault slouží ke spuštění. Klientské aplikaci umožňuje použít tajný kód pro přístup k prostředkům, které nejsou zabezpečené službou Azure Active Directory (AD). Identity spravovaných služeb, které se spravují automaticky v Azure, slouží k ověřování přihlášení ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu. 

Získáte informace o těchto tématech:


> [!div class="checklist"]
> * Povolení Identity spravované služby na virtuálním počítači s Windows 
> * Udělení přístupu virtuálnímu počítači k tajnému kódu uloženému ve službě Key Vault 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k načtení tajného kódu ze služby Key Vault 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Přihlaste se k webu Azure Portal na adrese [https://portal.azure.com](https://portal.azure.com).

## <a name="create-a-windows-virtual-machine-in-a-new-resource-group"></a>Vytvoření virtuálního počítače s Windows v nové skupině prostředků

V tomto kurzu vytvoříme nový virtuální počítač s Windows. MSI můžete povolit také na stávajícím virtuálním počítači.

1.  Klikněte na tlačítko **Vytvořit prostředek** v levém horním rohu webu Azure Portal.
2.  Vyberte **Compute** a potom vyberte **Windows Server 2016 Datacenter**. 
3.  Zadejte informace o virtuálním počítači. Zde vytvořené **Uživatelské jméno** a **Heslo** slouží jako přihlašovací údaje pro přihlášení k virtuálnímu počítači.
4.  V rozevíracím seznamu zvolte pro virtuální počítač správné **Předplatné**.
5.  Pokud chcete vybrat novou **skupinu prostředků**, ve které chcete vytvořit virtuální počítač, zvolte **Vytvořit novou**. Jakmile budete hotovi, klikněte na **OK**.
6.  Vyberte velikost virtuálního počítače. Pokud chcete zobrazit další velikosti, vyberte **Zobrazit všechny** nebo změňte filtr **Podporovaný typ disku**. V okně Nastavení ponechte výchozí nastavení a klikněte na **OK**.

    ![Alternativní text k obrázku](../media/msi-tutorial-windows-vm-access-arm/msi-windows-vm.png)

## <a name="enable-msi-on-your-vm"></a>Povolení MSI na virtuálním počítači 

MSI virtuálního počítače umožňuje získat z Azure AD přístupové tokeny bez nutnosti vložení přihlašovacích údajů do kódu. Povolením MSI sdělíte Azure, že má pro váš virtuální počítač vytvořit spravovanou identitu. Při povolení MSI se na pozadí stanou dvě věci: virtuální počítač se zaregistruje v Azure Active Directory, aby se vytvořila jeho spravovaná identita, a tato identita se nakonfiguruje na virtuálním počítači.

1.  Vyberte **virtuální počítač**, na kterém chcete MSI povolit.  
2.  Na levém navigačním panelu klikněte na **Konfigurace**. 
3.  Zobrazí se **Identita spravované služby**. Pokud chcete MSI zaregistrovat a povolit, vyberte **Ano**. Pokud ji chcete zakázat, zvolte Ne. 
4.  Nezapomeňte konfiguraci uložit kliknutím na **Uložit**.  

    ![Alternativní text k obrázku](../media/msi-tutorial-linux-vm-access-arm/msi-linux-extension.png)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Udělení přístupu virtuálnímu počítači k tajnému kódu uloženému ve službě Key Vault 
 
Když použijete MSI, získá kód přístup k přístupovým tokenům používaným při přihlášení k prostředkům, které podporují ověřování Azure AD.  Všechny služby Azure ale nepodporují ověřování Azure AD. Pokud chcete používat MSI s těmito službami, uložte přihlašovací údaje služby do Azure Key Vault a použijte MSI, abyste získali přístup ke službě Key Valut kvůli načtení přihlašovacích údajů. 

Nejprve musíme vytvořit trezor klíčů a pak k němu udělíme přístup identitě virtuálního počítače.   

1. Nahoře na levém navigačním panelu vyberte **Vytvořit prostředek** > **Zabezpečení a identita** > **Key Vault**.  
2. Zadejte **název** nového trezoru klíčů. 
3. Umístěte trezor klíčů do stejného předplatného a stejné skupiny prostředků jako virtuální počítač, který jste vytvořili dříve. 
4. Vyberte **Zásady přístupu** a klikněte na **Přidat novou**. 
5. V nabídce Konfigurace ze šablony vyberte **Správa tajných kódů**. 
6. Zvolte **Výběr objektu zabezpečení** a do vyhledávacího pole zadejte název dříve vytvořeného virtuálního počítače.  V seznamu výsledků vyberte virtuální počítač a klikněte na **Vybrat**. 
7. Kliknutím na **OK** dokončete přidání nové zásady přístupu. Kliknutím na **OK** dokončete výběr zásady přístupu. 
8. Kliknutím na **Vytvořit** dokončete vytvoření trezoru klíčů. 

    ![Alternativní text k obrázku](../media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Potom přidejte do trezoru klíčů tajný kód, abyste ho mohli později načíst kódem spuštěným na vašem virtuálním počítači: 

1. Vyberte **Všechny prostředky** a najděte a vyberte trezor klíčů, který jste vytvořili. 
2. Vyberte **Tajné kódy** a klikněte na **Přidat**. 
3. V nabídce **Možnosti nahrání** vyberte **Ručně**. 
4. Zadejte název a hodnotu tajného kódu.  Můžete použít libovolnou hodnotu. 
5. Nechte datum aktivace i datum konce platnosti nevyplněné a možnost **Povoleno** nechte na hodnotě **Ano**. 
6. Kliknutím na **Vytvořit** vytvořte tajný kód. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k načtení tajného kódu ze služby Key Vault  

Pokud nemáte nainstalovaný PowerShell 4.3.1 nebo novější, budete si muset [stáhnout a nainstalovat nejnovější verzi](https://docs.microsoft.com/powershell/azure/overview).

Nejprve použijeme MSI virtuálního počítače k získání přístupového tokenu pro ověření ve službě Key Vault:
 
1. Na portálu přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Windows a v části **Přehled** klikněte na **Připojit**.
2. Zadejte své **Uživatelské jméno** a **Heslo**, které jste přidali při vytváření **virtuálního počítače s Windows**.  
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** virtuálního počítače, otevřete ve vzdálené relaci PowerShell.  
4. V PowerShellu vyvolejte na tenantovi webový požadavek na získání tokenu pro místního hostitele na konkrétním portu pro virtuální počítač.  

    Požadavek PowerShellu:
    
    ```powershell
    $response = Invoke-WebRequest -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
    ```
    
    Dále extrahujte úplnou odpověď, která je uložená jako formátovaný řetězec JSON (JavaScript Object Notation) v objektu $response.  
    
    ```powershell
    $content = $response.Content | ConvertFrom-Json 
    ```
    
    Potom z odpovědi extrahujte přístupový token.  
    
    ```powershell
    $KeyVaultToken = $content.access_token 
    ```
    
    Nakonec pomocí příkazu Invoke-WebRequest v PowerShellu načtěte tajný kód, který jste ve službě Key Vault vytvořili dříve, předáním přístupového tokenu v autorizační hlavičce.  Budete potřebovat adresu URL své služby Key Vault, kterou najdete na stránce **Přehled** služby Key Vault v části **Základy**.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    Odpověď bude vypadat takto: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Jakmile ze služby Key Vault načtete tajný kód, můžete ho použít při přihlášení ke službě, která vyžaduje jméno a heslo. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak vytvořit Identitu spravované služby pro přístup ke službě Azure Key Vault.  Další informace o službě Azure Key Vault najdete tady:

> [!div class="nextstepaction"]
>[Azure Key Vault](/azure/key-vault/key-vault-whatis)
