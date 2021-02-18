---
title: Kurz `:` použití spravované identity pro přístup k Azure Key Vault-Windows-Azure AD
description: Tento kurz vás postupně provede používáním spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Key Vaultu.
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
ms.date: 12/10/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 0f29c9daba80ffc57b96cf5bd82690dea9ac6429
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101093738"
---
# <a name="tutorial-use-a-windows-vm-system-assigned-managed-identity-to-access-azure-key-vault"></a>Kurz: Použití spravované identity přiřazené systémem na virtuálním počítači s Windows pro přístup k Azure Key Vaultu 

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

V tomto kurzu se dozvíte, jak může virtuální počítač s Windows používat spravovanou identitu přiřazenou systémem pro přístup k [Azure Key Vault](../../key-vault/general/overview.md). Jako spouštěcí Key Vault umožňuje, aby klientská aplikace používala tajný klíč k přístupu k prostředkům, které nejsou zabezpečené pomocí Azure Active Directory (AD). Identity spravované služby se automaticky spravují přes Azure a umožňují vám ověřovat služby, které podporují ověřování Azure AD, a to bez zahrnutí ověřovacích informací do kódu.

Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Udělení přístupu virtuálnímu počítači k tajnému kódu uloženému ve službě Key Vault
> * Získání přístupového tokenu pomocí identity virtuálního počítače a jeho použití k načtení tajného kódu ze služby Key Vault 

## <a name="prerequisites"></a>Požadavky

- Porozumění spravovaným identitám. Pokud ještě neznáte funkci spravovaných identit pro prostředky Azure, podívejte se na tento [přehled](overview.md). 
- Účet Azure, [Zaregistrujte si bezplatný účet](https://azure.microsoft.com/free/).
- Oprávnění "vlastník" v příslušném oboru (vaše předplatné nebo skupina prostředků) k provedení požadovaných kroků vytváření prostředků a správy rolí. Pokud potřebujete pomoc s přiřazením role, přečtěte si téma [přiřazení rolí Azure ke správě přístupu k prostředkům předplatného Azure](../../role-based-access-control/role-assignments-portal.md).
- Budete také potřebovat virtuální počítač s Windows, který má povolené spravované identity přiřazené systémem.
  - Pokud pro tento kurz potřebujete vytvořit virtuální počítač, můžete postupovat podle článku [s názvem vytvořit virtuální počítač s povolenou identitou přiřazenou systémem](./qs-configure-portal-windows-vm.md#system-assigned-managed-identity) .

## <a name="create-a-key-vault"></a>Vytvoření trezoru klíčů  

V této části se dozvíte, jak udělit přístup k VIRTUÁLNÍmu počítači pro tajný kód uložený v Key Vault. Když použijete spravované identity pro prostředky Azure, může kód získat přístupové tokeny sloužící k ověření přístupu k prostředkům, které podporují ověřování Azure AD.Všechny služby Azure ale nepodporují ověřování Azure AD. Pokud chcete používat spravované identity pro prostředky Azure s těmito službami, uložte přihlašovací údaje služby do Azure Key Vaultu a použijte spravovanou identitu virtuálního počítače k získání přístupu ke Key Vaultu, abyste načetli přihlašovací údaje.

Napřed potřebujete vytvořit Key Vault a pak k němu udělíte přístup spravované identitě přiřazené systémem virtuálního počítače.

1. Otevření webu Azure [Portal](https://portal.azure.com/)
1. V horní části levého navigačního panelu vyberte **vytvořit prostředek** .  
1. Do pole **Hledat na Marketplace** zadejte **Key Vault** a stiskněte **ENTER**.  
1. Z výsledků vyberte **Key Vault** .
1. Vyberte **Vytvořit**.
1. Zadejte **název** nového trezoru klíčů.

    ![Vytvoření obrazovky trezoru klíčů](./media/msi-tutorial-windows-vm-access-nonaad/create-key-vault.png)

1. Vyplňte všechny požadované informace a ujistěte se, že jste zvolili předplatné a skupinu prostředků, ve které jste vytvořili virtuální počítač, který používáte pro tento kurz.
1. Vybrat **kontrolu + vytvořit**
1. Vyberte **Vytvořit**.

### <a name="create-a-secret"></a>Vytvoření tajného klíče

Potom do Key Vault přidejte tajný klíč, abyste ho mohli později načíst pomocí kódu spuštěného na vašem VIRTUÁLNÍm počítači. Pro účely tohoto kurzu používáme PowerShell, ale stejné koncepty platí i pro veškerý kód spuštěný na tomto virtuálním počítači.

1. Přejděte k nově vytvořeným Key Vault.
1. Vyberte **Tajné kódy** a klikněte na **Přidat**.
1. Vybrat **vygenerovat/importovat**
1. Na obrazovce **vytvořit tajný kód** z **možností odeslání** ponechte možnost **ručně** vybraná.
1. Zadejte název a hodnotu tajného kódu.  Může jít o libovolnou hodnotu. 
1. Nechte datum aktivace i datum konce platnosti nevyplněné a **Povoleno** nechte nastavené na **Ano**. 
1. Kliknutím na **Vytvořit** vytvořte tajný kód.

   ![Vytvoření tajného klíče](./media/msi-tutorial-windows-vm-access-nonaad/create-secret.png)

## <a name="grant-access"></a>Udělení přístupu

Spravovaná identita, kterou používá virtuální počítač, musí mít udělený přístup pro čtení tajného klíče, který ukládáme do Key Vault.

1. Přejděte k nově vytvořeným Key Vault
1. V nabídce na levé straně vyberte **zásady přístupu** .
1. Vyberte **Přidat zásady přístupu** .

   ![obrazovka zásad přístupu pro vytvoření trezoru klíčů](./media/msi-tutorial-windows-vm-access-nonaad/key-vault-access-policy.png)

1. V části **Přidat zásady přístupu** v části **Konfigurovat ze šablony (volitelné)** vyberte **Správa tajných klíčů** z rozevírací nabídky.
1. Zvolte **Výběr objektu zabezpečení** a do vyhledávacího pole zadejte název dříve vytvořeného virtuálního počítače.  V seznamu výsledků vyberte virtuální počítač a zvolte **Vybrat**.
1. Vyberte **Přidat**.
1. Vyberte **Uložit**.


## <a name="access-data"></a>Přístup k datům  

V této části se dozvíte, jak získat přístupový token pomocí identity virtuálního počítače a jak ho použít k načtení tajného kódu z Key Vault. Pokud nemáte nainstalovaný PowerShell 4.3.1 nebo novější, budete si muset [stáhnout a nainstalovat nejnovější verzi](/powershell/azure/).

Nejdřív použijeme spravovanou identitu přiřazenou systémem virtuálního počítače k získání přístupového tokenu pro ověření v Key Vaultu:
 
1. Na portálu přejděte na **Virtuální počítače**, přejděte ke svému virtuálnímu počítači s Windows a v části **Přehled** klikněte na **Připojit**.
2. Do svého **uživatelského jména** a **hesla** zadejte svoje uživatelské jméno a heslo, které jste přidali při vytváření **virtuálního počítače s Windows**.  
3. Teď, když jste vytvořili **připojení ke vzdálené ploše** virtuálního počítače, otevřete ve vzdálené relaci PowerShell.  
4. V PowerShellu vyvolejte na tenantovi webový požadavek na získání tokenu pro místního hostitele na konkrétním portu pro virtuální počítač.  

Požadavek PowerShellu:

```powershell
$Response = Invoke-RestMethod -Uri 'http://169.254.169.254/metadata/identity/oauth2/token?api-version=2018-02-01&resource=https%3A%2F%2Fvault.azure.net' -Method GET -Headers @{Metadata="true"} 
```

Můžete vidět, co odpověď vypadá níže:

![požadavek s odpovědí tokenu](./media/msi-tutorial-windows-vm-access-nonaad/token.png)

Potom z odpovědi extrahujte přístupový token.  

```powershell
   $KeyVaultToken = $Response.access_token
```

Nakonec pomocí příkazu Invoke-WebRequest v PowerShellu načtěte tajný kód, který jste ve službě Key Vault vytvořili dříve, předáním přístupového tokenu v autorizační hlavičce.  Budete potřebovat adresu URL své služby Key Vault, kterou najdete na stránce **Přehled** služby Key Vault v části **Základy**.  

```powershell
Invoke-RestMethod -Uri https://<your-key-vault-URL>/secrets/<secret-name>?api-version=2016-10-01 -Method GET -Headers @{Authorization="Bearer $KeyVaultToken"}
```

Odpověď bude vypadat takto: 

```powershell
  value       id                                                                                    attributes
  -----       --                                                                                    ----------
  'My Secret' https://mi-lab-vault.vault.azure.net/secrets/mi-test/50644e90b13249b584c44b9f712f2e51 @{enabled=True; created=16…
```

Jakmile ze služby Key Vault načtete tajný kód, můžete ho použít při přihlášení ke službě, která vyžaduje jméno a heslo.

## <a name="clean-up-resources"></a>Vyčištění prostředků

Pokud chcete prostředky vyčistit, přejděte na [Azure Portal](https://portal.azure.com), vyberte **skupiny prostředků**, najděte a vyberte skupinu prostředků, která se vytvořila v procesu tohoto kurzu (například `mi-test` ), a pak použijte příkaz **Odstranit skupinu prostředků** .

Případně můžete to provést také prostřednictvím [PowerShellu nebo rozhraní](../../azure-resource-manager/management/delete-resource-group.md) PŘÍKAZového řádku.

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste zjistili, jak používat spravovanou identitu přiřazenou systémem Windows VM pro přístup k Azure Key Vault.  Další informace o službě Azure Key Vault najdete tady:

> [!div class="nextstepaction"]
>[Azure Key Vault](../../key-vault/general/overview.md)