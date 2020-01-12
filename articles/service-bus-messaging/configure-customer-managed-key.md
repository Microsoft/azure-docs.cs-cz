---
title: Konfigurace vlastního klíče pro šifrování Azure Service Busch dat v klidovém prostředí
description: Tento článek poskytuje informace o tom, jak nakonfigurovat vlastní klíč pro šifrování Azure Service Bus data REST.
services: service-bus-messaging
ms.service: service-bus
documentationcenter: ''
author: axisc
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: aschhab
ms.openlocfilehash: 6d20d4031f0ed4d1be4dddf9e33946251d6dd523
ms.sourcegitcommit: 3eb0cc8091c8e4ae4d537051c3265b92427537fe
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/11/2020
ms.locfileid: "75903324"
---
# <a name="configure-customer-managed-keys-for-encrypting-azure-service-bus-data-at-rest-by-using-the-azure-portal"></a>Konfigurace klíčů spravovaných zákazníkem pro šifrování Azure Service Bus dat v klidovém formátu pomocí Azure Portal
Azure Service Bus Premium poskytuje šifrování neaktivních dat pomocí šifrování služby Azure Storage (Azure SSE). Service Bus Premium spoléhá na Azure Storage uložení dat a ve výchozím nastavení se všechna data uložená pomocí Azure Storage šifrují pomocí klíčů spravovaných Microsoftem. 

## <a name="overview"></a>Přehled
Azure Service Bus teď podporuje možnost šifrování neaktivních dat buď pomocí klíčů spravovaných Microsoftem nebo klíčů spravovaných zákazníkem (Bring Your Own Key-BYOK). Tato funkce umožňuje vytvořit, otočit, zakázat a odvolat přístup k klíčům spravovaným zákazníkem, které se používají k šifrování Azure Service Bus v klidovém umístění.

Povolení funkce BYOK je jednorázovým procesem nastavení v oboru názvů.

> [!NOTE]
> Pro šifrování na straně služby jsou k dispozici určitá upozornění ke spravovanému klíči zákazníka. 
>   * Tuto funkci podporuje Azure Service Bus úrovně [Premium](service-bus-premium-messaging.md) . Nedá se povolit pro obory názvů Service Bus úrovně Standard.
>   * Šifrování lze povolit pouze pro nové nebo prázdné obory názvů. Pokud obor názvů obsahuje data, operace šifrování selže.

Pomocí Azure Key Vault můžete spravovat klíče a auditovat používání klíčů. Můžete buď vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault k vygenerování klíčů. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](../key-vault/key-vault-overview.md)

V tomto článku se dozvíte, jak nakonfigurovat Trezor klíčů pomocí klíčů spravovaných zákazníkem pomocí Azure Portal. Informace o tom, jak vytvořit Trezor klíčů pomocí Azure Portal, najdete v tématu [rychlý Start: nastavení a načtení tajného klíče z Azure Key Vault pomocí Azure Portal](../key-vault/quick-create-portal.md).

> [!IMPORTANT]
> Použití klíčů spravovaných zákazníkem s Azure Service Bus vyžaduje, aby měl Trezor klíčů nakonfigurované dvě požadované vlastnosti. Jsou to: **obnovitelné odstranění** a **Nemazat**. Tyto vlastnosti jsou ve výchozím nastavení povolené, když v Azure Portal vytvoříte nový trezor klíčů. Pokud ale potřebujete tyto vlastnosti v existujícím trezoru klíčů povolit, musíte použít buď PowerShell, nebo rozhraní příkazového řádku Azure CLI.

## <a name="enable-customer-managed-keys"></a>Povolit klíče spravované zákazníkem
Pokud chcete povolit klíčům spravovaným zákazníkem v Azure Portal, postupujte následovně:

1. Přejděte do oboru názvů Service Bus Premium.
2. Na stránce **Nastavení** v oboru názvů Service Bus vyberte **šifrování**.
3. Vyberte **šifrování klíče spravovaného zákazníkem v klidovém** formátu, jak je znázorněno na následujícím obrázku.

    ![Povolit spravovaný klíč zákazníka](./media/configure-customer-managed-key/enable-customer-managed-key.png)


## <a name="set-up-a-key-vault-with-keys"></a>Nastavení trezoru klíčů s klíči

Po povolení klíčů spravovaných zákazníkem je potřeba přidružit spravovaný klíč zákazníka k vašemu oboru názvů Azure Service Bus. Service Bus podporuje pouze Azure Key Vault. Pokud zapnete možnost **šifrování pomocí klíče spravovaného zákazníkem** v předchozí části, je potřeba, abyste klíč importovali do Azure Key Vault. Klíče musí také obsahovat **obnovitelné odstranění** a pro tento klíč **se** neodstraňují konfigurace. Tato nastavení se dají nakonfigurovat pomocí [PowerShellu](../key-vault/key-vault-soft-delete-powershell.md) nebo rozhraní příkazového [řádku](../key-vault/key-vault-soft-delete-cli.md#enabling-purge-protection).

1. Pokud chcete vytvořit nový trezor klíčů, postupujte podle pokynů pro [rychlý start](../key-vault/key-vault-overview.md)Azure Key Vault. Další informace o importu existujících klíčů najdete v tématu [informace o klíčích, tajných klíčích a certifikátech](../key-vault/about-keys-secrets-and-certificates.md).
1. Pokud chcete při vytváření trezoru zapnout ochranu pomocí obnovitelného odstranění i vyprázdnění, použijte příkaz [AZ datatrezor Create](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-create) .

    ```azurecli-interactive
    az keyvault create --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --location westus --enable-soft-delete true --enable-purge-protection true
    ```    
1. Pokud chcete přidat ochranu vyprázdnit do existujícího trezoru (který už má povolené obnovitelné odstranění), použijte příkaz [AZ klíčů trezor Update](/cli/azure/keyvault?view=azure-cli-latest#az-keyvault-update) .

    ```azurecli-interactive
    az keyvault update --name contoso-SB-BYOK-keyvault --resource-group ContosoRG --enable-purge-protection true
    ```
1. Pomocí následujících kroků vytvořte klíče:
    1. Pokud chcete vytvořit nový klíč, vyberte v nabídce **klíče** v části **Nastavení**možnost **Generovat/importovat** .
        
        ![Vybrat tlačítko pro vygenerování/import](./media/configure-customer-managed-key/select-generate-import.png)

    1. Nastavte **Možnosti** , které se mají **vygenerovat** , a zadejte název klíče.

        ![Vytvoření klíče](./media/configure-customer-managed-key/create-key.png) 

    1. Nyní můžete vybrat tento klíč, který chcete přidružit k oboru názvů Service Bus pro šifrování z rozevíracího seznamu. 

        ![Vyberte klíč z trezoru klíčů.](./media/configure-customer-managed-key/select-key-from-key-vault.png)
        > [!NOTE]
        > Pro zajištění redundance můžete přidat až 3 klíče. V případě, že jedna z klíčů vypršela, nebo k ní nelze získat přístup, budou použity jiné klíče pro šifrování.
        
    1. Zadejte podrobnosti pro klíč a klikněte na **Vybrat**. Tím se povolí Šifrování neaktivních dat v oboru názvů pomocí klíče spravovaného zákazníkem. 


> [!IMPORTANT]
> Pokud chcete použít spravovaný klíč zákazníka spolu s geografickým zotavením po havárii, přečtěte si níže 
>
> Pokud chcete povolit šifrování v klidovém formátu pomocí spravovaného klíče zákazníka, nastaví se [zásada přístupu](../key-vault/key-vault-secure-your-key-vault.md) pro spravovanou identitu Service Bus v zadaném úložišti klíčů Azure. Tím se zajistí řízený přístup k trezoru klíčů Azure z oboru názvů Azure Service Bus.
>
> Z tohoto důvodu:
> 
>   * Pokud je pro obor názvů Service Bus povolené [geografické zotavení po havárii](service-bus-geo-dr.md) a chcete povolit spravovaný klíč zákazníka, 
>     * Přerušení párování
>     * [Nastavte zásady přístupu](../key-vault/managed-identity.md) pro spravovanou identitu pro primární i sekundární obory názvů do trezoru klíčů.
>     * Nastavte šifrování v primárním oboru názvů.
>     * Přespárujte primární a sekundární obory názvů.
> 
>   * Pokud chcete povolit geografickou možnost DR pro obor názvů Service Bus, kde je už nastavený zákazníkem spravovaný klíč, pak –
>     * [Nastavte zásady přístupu](../key-vault/managed-identity.md) pro spravovanou identitu pro sekundární obor názvů do trezoru klíčů.
>     * Spáruje primární a sekundární obory názvů.


## <a name="rotate-your-encryption-keys"></a>Otočení šifrovacích klíčů

Svůj klíč můžete v trezoru klíčů otočit pomocí mechanismu rotace trezorů klíčů Azure. Další informace najdete v tématu [Nastavení rotace klíčů a auditování](../key-vault/key-vault-key-rotation-log-monitoring.md). Data o aktivaci a vypršení platnosti je také možné nastavit na automatizaci střídání klíčů. Služba Service Bus detekuje nové verze klíčů a automaticky je začne používat.

## <a name="revoke-access-to-keys"></a>Odvolat přístup k klíčům

Odvolání přístupu k šifrovacím klíčům neodstraní data z Service Bus. K datům ale nelze přicházet z oboru názvů Service Bus. Šifrovací klíč můžete odvolat pomocí zásad přístupu nebo odstraněním klíče. Přečtěte si další informace o zásadách přístupu a zabezpečení trezoru klíčů před [zabezpečeným přístupem k trezoru klíčů](../key-vault/key-vault-secure-your-key-vault.md).

Po odvolání šifrovacího klíče se služba Service Bus v zašifrovaném oboru názvů stane nefunkčním. Pokud je povolený přístup k klíči nebo dojde k obnovení odstraněného klíče, Service Bus služba vybere klíč, abyste mohli přistupovat k datům z šifrovaného názvového prostoru Service Bus.

## <a name="next-steps"></a>Další kroky
Viz následující články:
- [Přehled Service Bus](service-bus-messaging-overview.md)
- [Přehled Key Vault](../key-vault/key-vault-overview.md)


