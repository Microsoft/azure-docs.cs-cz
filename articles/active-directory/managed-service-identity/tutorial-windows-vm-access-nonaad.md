---
title: Použití identity spravované služby virtuálního počítače s Windows k přístupu ke službě Azure Key Vault
description: Tento kurz vás provede procesem použití identity spravované služby virtuálního počítače s Windows k přístupu ke službě Azure Key Vault.
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
ms.openlocfilehash: 314e3ba608f41ff83565ddaa32e9cdeed2205211
ms.sourcegitcommit: f1e6e61807634bce56a64c00447bf819438db1b8
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/24/2018
ms.locfileid: "42886539"
---
# <a name="tutorial-use-a-windows-vm-managed-service-identity-to-access-azure-key-vault"></a>Kurz: Použití Identity spravované služby virtuálního počítače s Windows k přístupu ke službě Azure Key Vault 

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak pomocí identity přiřazené systémem pro virtuální počítač s Windows získat přístup ke službě Azure Key Vault. Služba Key Vault se používá ke spuštění. Umožňuje klientské aplikaci použít tajný kód pro přístup k prostředkům, které nejsou zabezpečené službou Azure Active Directory (AD). Identity spravovaných služeb, které se spravují automaticky v Azure, slouží k ověření přístupu ke službám podporujícím ověřování Azure AD bez nutnosti vložení přihlašovacích údajů do kódu. 

Získáte informace o těchto tématech:


> [!div class="checklist"]
> * Udělení přístupu virtuálnímu počítači k tajnému kódu uloženému ve službě Key Vault 
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k načtení tajného kódu ze služby Key Vault 

## <a name="prerequisites"></a>Požadavky

[!INCLUDE [msi-qs-configure-prereqs](../../../includes/active-directory-msi-qs-configure-prereqs.md)]

[!INCLUDE [msi-tut-prereqs](../../../includes/active-directory-msi-tut-prereqs.md)]

- [Přihlášení k webu Azure Portal](https://portal.azure.com)

- [Vytvoření virtuálního počítače s Windows](/azure/virtual-machines/windows/quick-create-portal)

- [Povolení identity přiřazené systémem pro váš virtuální počítač](/azure/active-directory/managed-service-identity/qs-configure-portal-windows-vm#enable-system-assigned-identity-on-an-existing-vm)

## <a name="grant-your-vm-access-to-a-secret-stored-in-a-key-vault"></a>Udělení přístupu virtuálnímu počítači k tajnému kódu uloženému ve službě Key Vault 
 
Když použijete identitu spravované služby, může kód získat přístupové tokeny používané při ověření přístupu k prostředkům, které podporují ověřování Azure AD.  Všechny služby Azure ale nepodporují ověřování Azure AD. Pokud chcete používat identitu spravované služby s těmito službami, uložte přihlašovací údaje služby do Azure Key Vault a použijte identitu spravované služby k získání přístupu ke službě Key Valut kvůli načtení přihlašovacích údajů. 

Napřed potřebujete vytvořit trezor klíčů a pak k němu udělíte přístup identitě virtuálního počítače.   

1. Na navigačním panelu vlevo nahoře vyberte **Vytvořit prostředek** > **Zabezpečení a identita** > **Key Vault**.  
2. Zadejte **název** nového trezoru klíčů. 
3. Umístěte trezor klíčů do stejného předplatného a stejné skupiny prostředků jako virtuální počítač, který jste vytvořili dříve. 
4. Vyberte **Zásady přístupu** a klikněte na **Přidat novou**. 
5. V nabídce Konfigurace ze šablony vyberte **Správa tajných kódů**. 
6. Zvolte **Výběr objektu zabezpečení** a do vyhledávacího pole zadejte název dříve vytvořeného virtuálního počítače.  V seznamu výsledků vyberte virtuální počítač a klikněte na **Vybrat**. 
7. Kliknutím na **OK** dokončete přidání nové zásady přístupu. Kliknutím na **OK** dokončete výběr zásady přístupu. 
8. Kliknutím na **Vytvořit** dokončete vytvoření trezoru klíčů. 

    ![Text k alternativnímu obrázku](../managed-service-identity/media/msi-tutorial-windows-vm-access-nonaad/msi-blade.png)


Potom přidejte do trezoru klíčů tajný kód, abyste ho mohli později načíst kódem spuštěným na vašem virtuálním počítači: 

1. Vyberte **Všechny prostředky** a najděte a vyberte trezor klíčů, který jste vytvořili. 
2. Vyberte **Tajné kódy** a klikněte na **Přidat**. 
3. V nabídce **Možnosti nahrání** vyberte **Ručně**. 
4. Zadejte název a hodnotu tajného kódu.  Může jít o libovolnou hodnotu. 
5. Nechte datum aktivace i datum konce platnosti nevyplněné a **Povoleno** nechte nastavené na **Ano**. 
6. Kliknutím na **Vytvořit** vytvořte tajný kód. 
 
## <a name="get-an-access-token-using-the-vm-identity-and-use-it-to-retrieve-the-secret-from-the-key-vault"></a>Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k načtení tajného kódu ze služby Key Vault  

Pokud nemáte nainstalovaný PowerShell 4.3.1 nebo novější, budete si muset [stáhnout a nainstalovat nejnovější verzi](https://docs.microsoft.com/powershell/azure/overview).

Nejdřív použijeme identitu spravované služby virtuálního počítače k získání přístupového tokenu pro ověření ve službě Key Vault:
 
1. Na portálu přejděte na **Virtuální počítače**, přejděte na svůj virtuální počítač s Windows a v části **Přehled** klikněte na **Připojit**.
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
    
Jakmile z Key Vault načtete tajný kód, můžete ho použít k ověření přístupu ke službě, která vyžaduje jméno a heslo. 

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se dozvěděli, jak vytvořit Identitu spravované služby pro přístup ke službě Azure Key Vault.  Další informace o Azure Key Vault:

> [!div class="nextstepaction"]
>[Azure Key Vault](/azure/key-vault/key-vault-whatis)
