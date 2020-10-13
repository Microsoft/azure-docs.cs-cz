---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-machines
author: roygara
ms.service: virtual-machines
ms.topic: include
ms.date: 06/05/2020
ms.author: rogarana
ms.custom: include file
ms.openlocfilehash: c301ed2b612c2f3a7aca40ed5ed733800323adcc
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "86230966"
---
Můžete zvolit správu šifrování na úrovni každého spravovaného disku s vlastními klíči. Šifrování na straně serveru pro spravované disky pomocí klíčů spravovaných zákazníkem nabízí integrované prostředí s Azure Key Vault. Můžete buď importovat [klíče RSA](../articles/key-vault/keys/hsm-protected-keys.md) do svého Key Vault, nebo vygenerovat nové klíče rsa v Azure Key Vault. 

Azure Managed disks zpracovává šifrování a dešifrování plně transparentním způsobem pomocí [šifrování obálek](../articles/storage/common/storage-client-side-encryption.md#encryption-and-decryption-via-the-envelope-technique). Šifruje data pomocí šifrovacího klíče založeného na [standardu AES](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard) 256 (klíč DEK), který je zase chráněn pomocí vašich klíčů. Služba úložiště generuje klíče pro šifrování dat a šifruje je pomocí klíčů spravovaných zákazníkem pomocí šifrování RSA. Šifrování obálek vám umožní pravidelně otáčet (měnit) klíče podle zásad dodržování předpisů, aniž by to mělo vliv na vaše virtuální počítače. Po otočení klíče služba úložiště znovu zašifruje klíče šifrování dat pomocí nových klíčů spravovaných zákazníkem. 

#### <a name="full-control-of-your-keys"></a>Úplné řízení klíčů

Abyste mohli používat klíče pro šifrování a dešifrování klíč DEK, musíte udělit přístup ke spravovaným diskům ve vašem Key Vault. To vám umožní plnou kontrolu nad daty a klíči. Můžete kdykoli zakázat vaše klíče nebo odvolat přístup ke spravovaným diskům. Pomocí monitorování Azure Key Vault taky můžete auditovat použití šifrovacího klíče a zajistit, aby se ke klíčům přistupovaly jenom spravované disky nebo jiné důvěryhodné služby Azure.

Když klíč zakážete nebo odstraníte, všechny virtuální počítače s disky, které tento klíč používají, se automaticky vypnou. Po tomto případě nebudou virtuální počítače použitelné, pokud se klíč znovu nepovolí nebo přiřadíte nový klíč.

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

Pokud chcete odvolat přístup k klíčům spravovaným zákazníkem, přečtěte si téma [Azure Key Vault PowerShellu](https://docs.microsoft.com/powershell/module/azurerm.keyvault/) a [Azure Key Vault CLI](https://docs.microsoft.com/cli/azure/keyvault). Odvolání přístupu efektivně zablokuje přístup ke všem datům v účtu úložiště, protože šifrovací klíč je nepřístupný Azure Storage.
