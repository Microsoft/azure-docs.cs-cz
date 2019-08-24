---
title: Konfigurace vlastního klíče pro šifrování dat služby Azure Event Hubs v klidovém prostředí
description: Tento článek poskytuje informace o tom, jak nakonfigurovat vlastní klíč pro šifrování služby Azure Event Hubs data REST.
services: event-hubs
ms.service: event-hubs
documentationcenter: ''
author: spelluru
ms.topic: conceptual
ms.date: 08/13/2019
ms.author: spelluru
ms.openlocfilehash: 794f8f136521acbb51139b9e781a30c71a1560e6
ms.sourcegitcommit: 007ee4ac1c64810632754d9db2277663a138f9c4
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/23/2019
ms.locfileid: "69992754"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-event-hubs-data-at-rest-by-using-the-azure-portal"></a>Konfigurace klíčů spravovaných zákazníkem pro šifrování dat Azure Event Hubs v klidovém formátu pomocí Azure Portal
Azure Event Hubs poskytuje šifrování neaktivních dat pomocí šifrování služby Azure Storage (Azure SSE). Event Hubs spoléhá na Azure Storage uložení dat a ve výchozím nastavení se všechna data uložená pomocí Azure Storage šifrují pomocí klíčů spravovaných Microsoftem. 

## <a name="overview"></a>Přehled
Azure Event Hubs teď podporuje možnost šifrování neaktivních dat buď pomocí klíčů spravovaných Microsoftem nebo klíčů spravovaných zákazníkem (Bring Your Own Key – BYOK). Tato funkce umožňuje vytvořit, otočit, zakázat a odvolat přístup k klíčům spravovaným zákazníkem, které se používají k šifrování neaktivních dat služby Azure Event Hubs.

Povolení funkce BYOK je jednorázovým procesem nastavení v oboru názvů.

> [!NOTE]
> Funkce BYOK je podporována Event Hubs vyhrazenými clustery [s jedním tenantů](event-hubs-dedicated-overview.md) . Nedá se povolit pro standardní Event Hubs obory názvů.

Pomocí Azure Key Vault můžete spravovat klíče a auditovat používání klíčů. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../key-vault/key-vault-overview.md)

V tomto článku se dozvíte, jak nakonfigurovat Trezor klíčů pomocí klíčů spravovaných zákazníkem pomocí Azure Portal. Informace o tom, jak vytvořit Trezor klíčů pomocí Azure Portal, najdete v článku [] rychlý Start: Nastavte a načtěte tajný klíč z Azure Key Vault pomocí Azure Portal] (.. /key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Použití klíčů spravovaných zákazníkem v Azure Event Hubs vyžaduje, aby měl Trezor klíčů nakonfigurované dvě požadované vlastnosti. Jsou to tyto:  **Obnovitelné odstranění** a **Nemazat**. Tyto vlastnosti jsou ve výchozím nastavení povolené, když v Azure Portal vytvoříte nový trezor klíčů. Pokud ale potřebujete tyto vlastnosti v existujícím trezoru klíčů povolit, musíte použít buď PowerShell, nebo rozhraní příkazového řádku Azure CLI.

## <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem
Pokud chcete povolit klíčům spravovaným zákazníkem v Azure Portal, postupujte následovně:

1. Přejděte do clusteru Event Hubs úrovně Dedicated.
1. Vyberte obor názvů, na kterém chcete povolit BYOK.
1. Na stránce **Nastavení** v oboru názvů Event Hubs vyberte **šifrování (Preview)** . 
1. Vyberte **šifrování klíče spravovaného zákazníkem v klidovém** formátu, jak je znázorněno na následujícím obrázku. 

    ![Povolit spravovaný klíč zákazníka](./media/configure-customer-managed-key/enable-customer-managed-key.png)

## <a name="set-up-a-key-vault-with-keys"></a>Nastavení trezoru klíčů s klíči
Po povolení klíčů spravovaných zákazníkem je potřeba přidružit spravovaný klíč zákazníka k vašemu oboru názvů Azure Event Hubs. Event Hubs podporuje pouze Azure Key Vault. Pokud zapnete možnost **šifrování pomocí klíče spravovaného zákazníkem** v předchozí části, je potřeba, abyste klíč importovali do Azure Key Vault. Klíče musí také obsahovat **obnovitelné odstranění** a pro tento klíč **se** neodstraňují konfigurace. Tato nastavení se dají nakonfigurovat pomocí [PowerShellu](../key-vault/key-vault-soft-delete-powershell.md) nebo rozhraní příkazového [řádku](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Pokud chcete vytvořit nový trezor klíčů, postupujte podle pokynů pro [rychlý start](../key-vault/key-vault-overview.md)Azure Key Vault. Další informace o importu existujících klíčů najdete v tématu [informace o klíčích, tajných](../key-vault/about-keys-secrets-and-certificates.md)klíčích a certifikátech.
1. Pokud chcete při vytváření trezoru zapnout ochranu pomocí obnovitelného odstranění i vyprázdnění, použijte příkaz [AZ datatrezor Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name ContosoVault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Pokud chcete přidat ochranu vyprázdnit do existujícího trezoru (který už má povolené obnovitelné odstranění), použijte příkaz [AZ klíčů trezor Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name ContosoVault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Pomocí následujících kroků vytvořte klíče:
    1. Pokud chcete vytvořit nový klíč, vyberte v nabídce **klíče** v části **Nastavení**možnost **Generovat/importovat** .
        
        ![Vybrat tlačítko pro vygenerování/import](./media/configure-customer-managed-key/select-generate-import.png)
    1. Nastavte **Možnosti** , které se mají vygenerovat, a zadejte název klíče.

        ![Vytvoření klíče](./media/configure-customer-managed-key/create-key.png) 
    1. Nyní můžete vybrat tento klíč, který chcete přidružit k oboru názvů Event Hubs pro šifrování z rozevíracího seznamu. 

        ![Vyberte klíč z trezoru klíčů.](./media/configure-customer-managed-key/select-key-from-key-vault.png)
    1. Zadejte podrobnosti pro klíč a klikněte na **Vybrat**. Tím se povolí Šifrování neaktivních dat v oboru názvů pomocí klíče spravovaného zákazníkem. 

        > [!NOTE]
        > Ve verzi Preview můžete vybrat jenom jeden klíč. 

## <a name="rotate-your-encryption-keys"></a>Otočení šifrovacích klíčů
Svůj klíč můžete v trezoru klíčů otočit pomocí mechanismu rotace trezorů klíčů Azure. Další informace najdete v tématu [Nastavení rotace klíčů a auditování](../key-vault/key-vault-key-rotation-log-monitoring.md). Data o aktivaci a vypršení platnosti je také možné nastavit na automatizaci střídání klíčů. Služba Event Hubs detekuje nové verze klíčů a automaticky je začne používat.

## <a name="revoke-access-to-keys"></a>Odvolat přístup k klíčům
Odvolání přístupu k šifrovacím klíčům neodstraní data z Event Hubs. K datům ale nelze přicházet z oboru názvů Event Hubs. Šifrovací klíč můžete odvolat pomocí zásad přístupu nebo odstraněním klíče. Přečtěte si další informace o zásadách přístupu a zabezpečení trezoru klíčů před [zabezpečeným přístupem k trezoru klíčů](../key-vault/key-vault-secure-your-key-vault.md).

Po odvolání šifrovacího klíče se služba Event Hubs v zašifrovaném oboru názvů stane nefunkčním. Pokud je povolený přístup ke klíči nebo je obnovený klíč, Event Hubs služba vybere klíč, abyste měli přístup k datům z šifrovaného názvového prostoru Event Hubs.

> [!NOTE]
> Pokud odstraníte existující šifrovací klíč z trezoru klíčů a nahradíte ho novým klíčem na Event Hubs oboru názvů, protože odstranit klíč je stále platný (protože je uložený v mezipaměti) až do hodiny, můžou být vaše stará data (která byla zašifrovaná pomocí starého klíče) i nadále dostupná společně.  s novými daty, která jsou teď dostupná jenom pomocí nového klíče. Toto chování je záměrné v rámci verze Preview této funkce. 

## <a name="set-up-diagnostic-logs"></a>Nastavení diagnostických protokolů 
Nastavení protokolů diagnostiky pro obory názvů s povoleným BYOK poskytuje požadované informace o operacích, když je obor názvů zašifrovaný pomocí klíčů spravovaných zákazníkem. Tyto protokoly je možné povolit a později streamovat do centra událostí nebo analyzovat prostřednictvím Log Analytics nebo streamovat do úložiště a provádět přizpůsobené analýzy. Další informace o diagnostických protokolech najdete v tématu [Přehled diagnostických protokolů Azure](../azure-monitor/platform/diagnostic-logs-overview.md).

## <a name="enable-user-logs"></a>Povolit protokoly uživatelů
Pomocí těchto kroků povolte protokoly pro klíče spravované zákazníkem.

1. V Azure Portal přejděte na obor názvů, který má povolenou možnost BYOK.
1. V části **monitorování**vyberte **nastavení diagnostiky** .

    ![Vybrat nastavení diagnostiky](./media/configure-customer-managed-key/select-diagnostic-settings.png)
1. Vyberte **+ Přidat nastavení diagnostiky**. 

    ![Vyberte Přidat nastavení diagnostiky.](./media/configure-customer-managed-key/select-add-diagnostic-setting.png)
1. Zadejte **název** a vyberte, kam chcete zasílat streamování protokolů.
1. Vyberte **CustomerManagedKeyUserLogs** a **uložte**. Tato akce povolí protokoly pro BYOK v oboru názvů.

    ![Vybrat možnost uživatelských protokolů pro klíč uživatele spravovaný zákazníkem](./media/configure-customer-managed-key/select-customer-managed-key-user-logs.png)

## <a name="log-schema"></a>Schéma protokolu 
Všechny protokoly se ukládají ve formátu JavaScript Object Notation (JSON). Každá položka má pole řetězce, která používají formát popsaný v následující tabulce. 

| Name | Popis |
| ---- | ----------- | 
| TaskName | Popis úkolu, který se nezdařilo. |
| ID aktivity | Interní ID, které se používá ke sledování. |
| category | Definuje klasifikaci úkolu. Například pokud je klíč z vašeho trezoru klíčů zakázaný, pak se jedná o kategorii informací, nebo pokud se klíč nedá rozdělit, může dojít k chybě. |
| resourceId | ID prostředku Azure Resource Manager |
| keyVault | Úplný název trezoru klíčů |
| key | Název klíče, který slouží k šifrování oboru názvů Event Hubs. |
| version | Verze používaného klíče |
| operation | Operace, která se provádí na klíči v trezoru klíčů. Můžete například zakázat/povolit klíč, zalamovat nebo rozbalení. |
| code | Kód, který je přidružen k operaci. Příklad: Kód chyby 404 znamená, že klíč nebyl nalezen. |
| zpráva | Jakákoli chybová zpráva přidružená k operaci |

Tady je příklad protokolu pro klíč spravovaný zákazníkem:

```json
{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111-1111-1111-1111-111111111111",
   "category": "error"
   "resourceId": "/SUBSCRIPTIONS/11111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "1111111111111111111111111111111",
   "operation": "wrapKey",
   "code": "404",
   "message": "Key not found: ehbyok0/111111111111111111111111111111",
}



{
   "TaskName": "CustomerManagedKeyUserLog",
   "ActivityId": "11111111111111-1111-1111-1111111111111",
   "category": "info"
   "resourceId": "/SUBSCRIPTIONS/111111111-1111-1111-1111-11111111111/RESOURCEGROUPS/DEFAULT-EVENTHUB-CENTRALUS/PROVIDERS/MICROSOFT.EVENTHUB/NAMESPACES/FBETTATI-OPERA-EVENTHUB",
   "keyVault": "https://mykeyvault.vault-int.azure-int.net",
   "key": "mykey",
   "version": "111111111111111111111111111111",
   "operation": "disable" | "restore",
   "code": "",
   "message": "",
}
```

## <a name="troubleshoot"></a>Řešení potíží
V rámci osvědčeného postupu doporučujeme vždy povolit protokoly, jako jsou uvedené v předchozí části. Pomáhá sledovat aktivity, když je povolené šifrování BYOK. Pomáhá také při určování rozsahu problémů.

Níže jsou uvedené běžné kódy chyb, které se hledají, když je povolené šifrování BYOK.

| Action | Kód chyby | Výsledný stav dat |
| ------ | ---------- | ----------------------- | 
| Odebrat oprávnění k zabalení a rozbalení z trezoru klíčů | 403 |    Nepřístupný |
| Odebere členství role AAD z objektu zabezpečení AAD, který udělil oprávnění k zabalení nebo rozbalení. | 403 |  Nepřístupný |
| Odstranění šifrovacího klíče z trezoru klíčů | 404 | Nepřístupný |
| Odstranění trezoru klíčů | 404 | Nedostupné (předpokládá, že je povolené obnovitelné odstranění, což je povinné nastavení.) |
| Doba vypršení platnosti šifrovacího klíče se mění tak, že už vypršela její platnost. | 403 |   Nepřístupný  |
| Mění se NBF (ne dřív), takže šifrovací klíč klíče není aktivní. | 403 | Nepřístupný  |
| Výběr možnosti **Povolení protokolu MSFT** pro bránu firewall trezoru klíčů nebo jiným blokování síťového přístupu k trezoru klíčů, který má šifrovací klíč | 403 | Nepřístupný |
| Přesun trezoru klíčů do jiného tenanta | 404 | Nepřístupný |  
| Přerušované síťové potíže nebo výpadky DNS/AAD/MSI |  | K dispozici pomocí šifrovacího klíče dat uložených v mezipaměti |

> [!IMPORTANT]
> Pokud chcete povolit geografickou možnost DR na oboru názvů, který používá šifrování BYOK, sekundární obor názvů pro párování musí být v vyhrazeném clusteru a musí mít povolenou spravovanou identitu přiřazenou systémem. Další informace najdete v tématu [spravované identity pro prostředky Azure](../active-directory/managed-identities-azure-resources/overview.md).

> [!NOTE]
> Pro BYOK Event Hubs šifrované obory názvů, koncové body služby Virtual Network (VNet) nejsou ve verzi Preview podporované. 


## <a name="next-steps"></a>Další postup
Viz následující články:
- [Přehled služby Event Hubs](event-hubs-about.md)
- [Přehled Key Vault](../key-vault/key-vault-overview.md)




