---
title: Kurz`:` Použití spravované identity pro přístup k Azure Key Vault – Windows – Azure AD
description: Tento kurz vás postupně provede používáním spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Key Vaultu.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 01/10/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: cd9f85e3bfd11ee655ce581c60a5b65e13f4497b
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75971827"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Key Vaultu 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak pomocí spravované identity přiřazené systémem pro virtuální počítač s Windows získat přístup ke službě Azure Key Vault. Služba Key Vault se používá ke spuštění. Umožňuje klientské aplikaci použít tajný kód pro přístup k prostředkům, které nejsou zabezpečené službou Azure Active Directory (AD). Identity spravovaných služeb, které se spravují automaticky v Azure, slouží k ověření přístupu ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu. 

Získáte informace o těchto tématech:


> [!div class="checklist"]
> * Udělení přístupu virtuálnímu počítači k tajnému kódu uloženému ve službě Key Vault 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k načtení tajného kódu ze služby Key Vault 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]


## <a name="enable"></a>Povolení

[!INCLUDE [msi-tut-enable](../../../includes/active-directory-msi-tut-enable.md)]



## <a name="grant-access"></a>Udělení přístupu  
 
Tato část ukazuje, jak udělit přístup virtuálního počítače k tajnému klíči uloženému v trezoru klíčů. Když použijete spravované identity pro prostředky Azure, může kód získat přístupové tokeny sloužící k ověření přístupu k prostředkům, které podporují ověřování Azure AD.Všechny služby Azure ale nepodporují ověřování Azure AD.Pokud chcete používat spravované identity pro prostředky Azure s těmito službami, uložte přihlašovací údaje služby do Azure Key Vaultu a použijte spravovanou identitu virtuálního počítače k získání přístupu ke Key Vaultu, abyste načetli přihlašovací údaje. 

Napřed potřebujete vytvořit Key Vault a pak k němu udělíte přístup spravované identitě přiřazené systémem virtuálního počítače.   

1. V horní části levého navigačního panelu vyberte Vytvořit trezor se **zabezpečením a** > **klíči****identity** > .  
2. Zadejte **název** nového trezoru klíčů. 
3. Umístěte trezor klíčů do stejného předplatného a stejné skupiny prostředků jako virtuální počítač, který jste vytvořili dříve. 
4. Vyberte **Zásady přístupu** a klikněte na **Přidat novou**. 
5. V nabídce Konfigurace ze šablony vyberte **Správa tajných kódů**. 
6. Zvolte **Výběr objektu zabezpečení** a do vyhledávacího pole zadejte název dříve vytvořeného virtuálního počítače.V seznamu výsledků vyberte virtuální počítač a klikněte na **Vybrat**. 
7. Kliknutím na **OK** dokončete přidání nové zásady přístupu. Kliknutím na **OK** dokončete výběr zásady přístupu. 
8. Kliknutím na **Vytvořit** dokončete vytvoření trezoru klíčů. 

    ![Alternativní text k obrázku](./media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Potom přidejte do trezoru klíčů tajný kód, abyste ho mohli později načíst kódem spuštěným na vašem virtuálním počítači: 

1. Vyberte **Všechny prostředky** a najděte a vyberte trezor klíčů, který jste vytvořili. 
2. Vyberte **Tajné kódy** a klikněte na **Přidat**. 
3. V nabídce **Možnosti nahrání** vyberte **Ručně**. 
4. Zadejte název a hodnotu tajného kódu.Může jít o libovolnou hodnotu. 
5. Nechte datum aktivace i datum konce platnosti nevyplněné a **Povoleno** nechte nastavené na **Ano**. 
6. Kliknutím na **Vytvořit** vytvořte tajný kód. 
 
## <a name="access-data"></a>Přístup k datům  

Tato část ukazuje, jak získat přístupový token pomocí identity virtuálního připojení a použít jej k načtení tajného klíče z trezoru klíčů. Pokud nemáte nainstalovaný PowerShell 4.3.1 nebo novější, budete si muset [stáhnout a nainstalovat nejnovější verzi](https://docs.microsoft.com/powershell/azure/overview).

Nejdřív použijeme spravovanou identitu přiřazenou systémem virtuálního počítače k získání přístupového tokenu pro ověření v Key Vaultu:
 
1. Na portálu přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Windows a v části **Přehled** klikněte na **Připojit**.
2. Zadejte **své uživatelské jméno** a **heslo,** pro které jste přidali při vytváření **virtuálního počítače se systémem Windows**.  
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
    
    Nakonec pomocí příkazu Invoke-WebRequest v PowerShellu načtěte tajný kód, který jste ve službě Key Vault vytvořili dříve, předáním přístupového tokenu v autorizační hlavičce.Budete potřebovat adresu URL své služby Key Vault, kterou najdete na stránce **Přehled** služby Key Vault v části **Základy**.  
    
    ```powershell
    (Invoke-WebRequest -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}).content 
    ```
    
    Odpověď bude vypadat takto: 
    
    ```powershell
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Jakmile ze služby Key Vault načtete tajný kód, můžete ho použít při přihlášení ke službě, která vyžaduje jméno a heslo. 


## <a name="disable"></a>Zakázat

[!INCLUDE [msi-tut-disable](../../../includes/active-directory-msi-tut-disable.md)]



## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak využít spravovanou identitu přiřazenou systémem na virtuálním počítači s Windows pro přístup k Azure Key Vaultu.  Další informace o službě Azure Key Vault najdete tady:

> [!div class="nextstepaction"]
>[Azure Key Vault](/azure/key-vault/key-vault-overview)
