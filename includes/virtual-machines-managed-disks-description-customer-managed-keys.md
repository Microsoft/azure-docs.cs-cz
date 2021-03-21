---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 03/02/2021
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: 52b9bee1d43c0f136889a6a54277d4bb45dd4a45
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "101751021"
---
Můžete zvolit správu šifrování na úrovni každého spravovaného disku s vlastními klíči. Šifrování na straně serveru pro spravované disky pomocí klíčů spravovaných zákazníkem nabízí integrované prostředí s Azure Key Vault. Můžete buď importovat [klíče RSA](../articles/key-vault/keys/hsm-protected-keys.md) do svého Key Vault, nebo vygenerovat nové klíče rsa v Azure Key Vault. 

Azure Managed disks zpracovává šifrování a dešifrování plně transparentním způsobem pomocí [šifrování obálek](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Šifruje data pomocí šifrovacího klíče založeného na [standardu AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (klíč DEK), který je zase chráněn pomocí vašich klíčů. Služba úložiště generuje klíče pro šifrování dat a šifruje je pomocí klíčů spravovaných zákazníkem pomocí šifrování RSA. Šifrování obálek vám umožní pravidelně otáčet (měnit) klíče podle zásad dodržování předpisů, aniž by to mělo vliv na vaše virtuální počítače. Po otočení klíče služba úložiště znovu zašifruje klíče šifrování dat pomocí nových klíčů spravovaných zákazníkem. 

#### <a name="full-control-of-your-keys"></a>Úplné řízení klíčů

Abyste mohli používat klíče pro šifrování a dešifrování klíč DEK, musíte udělit přístup ke spravovaným diskům ve vašem Key Vault. To vám umožní plnou kontrolu nad daty a klíči. Můžete kdykoli zakázat vaše klíče nebo odvolat přístup ke spravovaným diskům. Pomocí monitorování Azure Key Vault taky můžete auditovat použití šifrovacího klíče a zajistit, aby se ke klíčům přistupovaly jenom spravované disky nebo jiné důvěryhodné služby Azure.

Pro Premium SSD, Standard SSD a Standard HDD: když zakážete nebo odstraníte klíč, všechny virtuální počítače s disky, které tento klíč používají, se automaticky vypnou. Po tomto případě nebudou virtuální počítače použitelné, pokud se klíč znovu nepovolí nebo přiřadíte nový klíč.    

Pro disky Ultra: když zakážete nebo odstraníte klíč, všechny virtuální počítače s disky Ultra s použitím klíče se automaticky neukončí. Po zrušení přidělení a restartu virtuálních počítačů disky přestanou používat klíč a virtuální počítače se nevrátí do režimu online. Chcete-li virtuální počítače převést zpět do režimu online, je nutné přiřadit nový klíč nebo povolit existující klíč.    

Následující diagram ukazuje, jak spravované disky používají Azure Active Directory a Azure Key Vault k vytváření požadavků pomocí klíče spravovaného zákazníkem:

:::image type="content" source="media/virtual-machines-managed-disks-description-customer-managed-keys/customer-managed-keys-sse-managed-disks-workflow.png" alt-text="Pracovní postup spravovaného disku a klíčů spravovaných zákazníkem. Správce vytvoří Azure Key Vault a pak vytvoří sadu šifrování disku a nastaví sadu šifrování disku. Sada je přidružená k virtuálnímu počítači, který umožňuje disku využívat Azure AD k ověřování.":::

Následující seznam popisuje diagram podrobněji:

1. Správce Azure Key Vault vytvoří prostředky trezoru klíčů.
1. Správce trezoru klíčů buď naimportuje své klíče RSA, aby Key Vault nebo vygeneroval nové klíče RSA v Key Vault.
1. Tento správce vytvoří instanci prostředku sady šifrování disků a určí ID Azure Key Vault a adresu URL klíče. Sada šifrování disků je nově zavedený prostředek, který zjednodušuje správu klíčů pro spravované disky. 
1. Když se vytvoří sada pro šifrování disků, vytvoří se [spravovaná identita přiřazená systémem](../articles/active-directory/managed-identities-azure-resources/overview.md) v Azure Active Directory (AD) a přidružená k sadě šifrování disku. 
1. Správce trezoru klíčů Azure pak udělí oprávnění spravované identity k provádění operací v trezoru klíčů.
1. Uživatel virtuálního počítače vytvoří disky jejich přidružením k sadě šifrování disku. Uživatel virtuálního počítače může také povolit šifrování na straně serveru pomocí klíčů spravovaných zákazníkem pro existující prostředky jejich přidružením k sadě šifrování disku. 
1. Spravované disky používají spravovanou identitu k posílání požadavků do Azure Key Vault.
1. Pro čtení nebo zápis dat odesílají spravované disky požadavky na Azure Key Vault k šifrování (zabalení) a dešifrování (rozbalení) šifrovacího klíče dat, aby bylo možné provádět šifrování a dešifrování dat. 

Pokud chcete odvolat přístup k klíčům spravovaným zákazníkem, přečtěte si téma [Azure Key Vault PowerShellu](/powershell/module/azurerm.keyvault/) a [Azure Key Vault CLI](/cli/azure/keyvault). Odvolání přístupu efektivně zablokuje přístup ke všem datům v účtu úložiště, protože šifrovací klíč je nepřístupný Azure Storage.

#### <a name="automatic-key-rotation-of-customer-managed-keys-preview"></a>Automatické střídání klíčů pro klíče spravované zákazníkem (Preview)

Můžete zvolit, že se má povolit automatické střídání klíčů na nejnovější verzi klíče. Disk odkazuje na klíč přes sadu Disk Encryption. Když povolíte automatické otočení pro sadu šifrování disků, systém automaticky aktualizuje všechny spravované disky, snímky a image, které odkazují na šifrování disků, aby používala novou verzi klíče do jedné hodiny. Tato funkce je aktuálně dostupná v omezených oblastech ve verzi Preview. Informace o regionální dostupnosti najdete v části [podporované oblasti](#supported-regions) .