---
title: Kurz `:` použití spravované identity pro přístup k Azure Key Vault-Linux – Azure AD
description: Tento kurz vás postupně provede používáním spravované identity přiřazené systémem na virtuálním počítači s Linuxem pro přístup k Azure Resource Manageru.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: daveba
ms.service: active-directory
ms.subservice: msi
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 11/03/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7cfcaec38a939291090da7d2229c4a95f984bf28
ms.sourcegitcommit: 6a902230296a78da21fbc68c365698709c579093
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/05/2020
ms.locfileid: "93360433"
---
# <a name="tutorial-use-a-linux-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Linuxem pro přístup k Azure Key Vaultu 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak může virtuální počítač se systémem Linux používat spravovanou identitu přiřazenou systémem pro přístup k [Azure Key Vault](../../key-vault/general/overview.md). Jako spouštěcí Key Vault umožňuje, aby klientská aplikace používala tajný klíč k přístupu k prostředkům, které nejsou zabezpečené pomocí Azure Active Directory (AD). Identity spravované služby se automaticky spravují přes Azure a umožňují vám ověřovat služby, které podporují ověřování Azure AD, a to bez zahrnutí ověřovacích informací do kódu.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Udělení přístupu virtuálnímu počítači k tajnému kódu uloženému ve službě Key Vault 
> * Použití identity virtuálního počítače k získání přístupového tokenu a použití tohoto tokenu k načtení tajného kódu z Key Vault 
 
## <a name="prerequisites"></a>Požadavky

- Porozumění spravovaným identitám. Pokud ještě neznáte funkci spravovaných identit pro prostředky Azure, podívejte se na tento [přehled](overview.md). 
- Účet Azure, [Zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
- Oprávnění "vlastník" v příslušném oboru (vaše předplatné nebo skupina prostředků) k provedení požadovaných kroků vytváření prostředků a správy rolí. Pokud potřebujete pomoc s přiřazením role, přečtěte si téma [Použití řízení přístupu na základě role ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md).
- Potřebujete také virtuální počítač se systémem Linux, který má povolené spravované identity přiřazené systémem.
  - Pokud pro tento kurz potřebujete vytvořit virtuální počítač, můžete postupovat podle článku [s názvem vytvoření virtuálního počítače se systémem Linux pomocí Azure Portal](../../virtual-machines/linux/quick-create-portal.md#create-virtual-machine)


## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů  

V této části se dozvíte, jak udělit přístup k VIRTUÁLNÍmu počítači pro tajný kód uložený v Key Vault. Když použijete spravované identity pro prostředky Azure, může kód získat přístupové tokeny sloužící k ověření přístupu k prostředkům, které podporují ověřování Azure AD.Všechny služby Azure ale nepodporují ověřování Azure AD. Pokud chcete používat spravované identity pro prostředky Azure s těmito službami, uložte přihlašovací údaje služby do Azure Key Vaultu a použijte spravovanou identitu virtuálního počítače k získání přístupu ke Key Vaultu, abyste načetli přihlašovací údaje.

Napřed potřebujete vytvořit Key Vault a pak k němu udělíte přístup spravované identitě přiřazené systémem virtuálního počítače.

1. Otevření webu Azure [Portal](https://portal.azure.com/)
1. V horní části levého navigačního panelu vyberte **vytvořit prostředek** .  
1. Do pole **Hledat na Marketplace** zadejte **Key Vault** a stiskněte **ENTER**.  
1. Z výsledků vyberte **Key Vault** .
1. Vyberte **Vytvořit**.
1. Zadejte **název** nového trezoru klíčů.

    ![Vytvoření obrazovky trezoru klíčů](./media/tutorial-linux-vm-access-nonaad/create-key-vault.png)

1. Vyplňte všechny požadované informace a ujistěte se, že jste zvolili předplatné a skupinu prostředků, ve které jste vytvořili virtuální počítač, který používáte pro tento kurz.
1. Vybrat **kontrolu + vytvořit**
1. Vyberte **Vytvořit**.

## <a name="grant-access"></a>Udělení přístupu

Spravovaná identita, kterou používá virtuální počítač, musí mít udělený přístup pro čtení tajného klíče, který ukládáme do Key Vault.

1. Přejděte k nově vytvořeným Key Vault
1. V nabídce na levé straně vyberte **zásady přístupu** .
1. Vyberte **Přidat zásady přístupu** .

   ![obrazovka zásad přístupu pro vytvoření trezoru klíčů](./media/tutorial-linux-vm-access-nonaad/key-vault-access-policy.png)

1. V části **Přidat zásady přístupu** v části **Konfigurovat ze šablony (volitelné)** vyberte **Správa tajných klíčů** z rozevírací nabídky.
1. Zvolte **Výběr objektu zabezpečení** a do vyhledávacího pole zadejte název dříve vytvořeného virtuálního počítače.  V seznamu výsledků vyberte virtuální počítač a zvolte **Vybrat**.
1. Vyberte **Přidat**.
1. Vyberte **Uložit**.

## <a name="create-a-secret"></a>Vytvoření tajného klíče

Potom do Key Vault přidejte tajný klíč, abyste ho mohli později načíst pomocí kódu spuštěného na vašem VIRTUÁLNÍm počítači. Pro účely tohoto kurzu používáme PowerShell, ale stejné koncepty platí i pro veškerý kód spuštěný na tomto virtuálním počítači.

1. Přejděte k nově vytvořeným Key Vault.
1. Vyberte **Tajné kódy** a klikněte na **Přidat**.
1. Vybrat **vygenerovat/importovat**
1. Na obrazovce **vytvořit tajný kód** z **možností odeslání** ponechte možnost **ručně** vybraná.
1. Zadejte název a hodnotu tajného kódu.  Může jít o libovolnou hodnotu. 
1. Nechte datum aktivace i datum konce platnosti nevyplněné a **Povoleno** nechte nastavené na **Ano**. 
1. Kliknutím na **Vytvořit** vytvořte tajný kód.

   ![Vytvoření tajného klíče](./media/tutorial-linux-vm-access-nonaad/create-secret.png)
 
## <a name="access-data"></a>Přístup k datům

K dokončení tohoto postupu potřebujete klienta SSH.  Pokud používáte Windows, můžete použít klienta SSH v [subsystému Windows pro Linux](/windows/wsl/about). Pokud potřebujete pomoc při konfiguraci klíčů klienta SSH, přečtěte si, [jak na počítači s Windows v Azure používat klíče SSH](../../virtual-machines/linux/ssh-from-windows.md) nebo [jak na linuxových virtuálních počítačích v Azure vytvářet a používat pár veřejného a privátního klíče SSH](../../virtual-machines/linux/mac-create-ssh-keys.md).
 
1. Na portálu přejděte ke svému linuxovému virtuálnímu počítači a v části **Přehled** klikněte na **Připojit**. 
2. **Připojte** se vybraným klientem SSH k virtuálnímu počítači. 
3. V okně terminálu pomocí nástroje CURL odešlete do místních spravovaných identity požadavek na koncový bod prostředků Azure, abyste získali přístupový token pro Azure Key Vault.  
 
    Žádost CURL o přístupový token je níže.  
    
    ```bash
    curl 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -H Metadata:true  
    ```
    V odpovědi je přístupový token, který potřebujete pro přístup k Resource Manageru. 
    
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
    
    Tento přístupový token můžete použít k ověření přístupu do služby Azure Key Vault.  V další žádosti CURL je vidět, jak přečíst tajný kód z Key Vault pomocí CURL a rozhraní REST API služby Key Vault.  Budete potřebovat adresu URL své služby Key Vault, kterou najdete na stránce **Přehled** služby Key Vault v části **Základy**.  Budete také potřebovat přístupový token, který jste získali při předchozím volání. 
        
    ```bash
    curl https://<YOUR-KEY-VAULT-URL>/secrets/<secret-name>?api-version=2016-10-01 -H "Authorization: Bearer <ACCESS TOKEN>" 
    ```
    
    Odpověď bude vypadat takto: 
    
    ```bash
    {"value":"p@ssw0rd!","id":"https://mytestkeyvault.vault.azure.net/secrets/MyTestSecret/7c2204c6093c4d859bc5b9eff8f29050","attributes":{"enabled":true,"created":1505088747,"updated":1505088747,"recoveryLevel":"Purgeable"}} 
    ```
    
Jakmile ze služby Key Vault načtete tajný kód, můžete ho použít při přihlášení ke službě, která vyžaduje jméno a heslo.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete prostředky vyčistit, přejděte na [Azure Portal](https://portal.azure.com), vyberte **skupiny prostředků** , najděte a vyberte skupinu prostředků, která se vytvořila v procesu tohoto kurzu (například `mi-test` ), a pak použijte příkaz **Odstranit skupinu prostředků** .

Případně můžete to provést také prostřednictvím [PowerShellu nebo rozhraní](../../azure-resource-manager/management/delete-resource-group.md) PŘÍKAZového řádku.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak využít spravovanou identitu přiřazenou systémem na virtuálním počítači s Linuxem pro přístup k Azure Key Vaultu.  Další informace o službě Azure Key Vault najdete tady:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)