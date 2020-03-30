---
title: Zabezpečené clustery Azure Data Explorer v Azure
description: Přečtěte si, jak zabezpečit clustery v Azure Data Exploreru.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: 786950011f10e25d6bcb72061212c1878e79d45a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77373349"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Zabezpečené clustery Azure Data Explorer v Azure

Tento článek obsahuje úvod k zabezpečení v Průzkumníku dat Azure, který vám pomůže chránit vaše data a prostředky v cloudu a splnit bezpečnostní potřeby vaší firmy. Je důležité, aby vaše clustery v bezpečí. Zabezpečení clusterů zahrnuje jednu nebo více funkcí Azure, které zahrnují zabezpečený přístup a úložiště. Tento článek obsahuje informace, které vám pomohou zabezpečit cluster.

## <a name="managed-identities-for-azure-resources"></a>Spravované identity pro prostředky Azure

Běžnou výzvou při vytváření cloudových aplikací je správa přihlašovacích údajů ve vašem kódu pro ověřování cloudových služeb. Zajištění zabezpečení těchto přihlašovacích údajů je důležitý úkol. Přihlašovací údaje by neměly být uloženy v pracovních stanicích pro vývojáře nebo se změnami do správy zdrojového kódu. Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů, tajných kódů a dalších klíčů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst.

Azure Active Directory (Azure AD) spravované identity pro prostředky Azure funkce řeší tento problém. Tato funkce poskytuje službám Azure automaticky spravovanou identitu v Azure AD. Tuto identitu můžete použít k ověření u jakékoli služby, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje. Další informace o této službě najdete na stránce [spravované identity pro](/azure/active-directory/managed-identities-azure-resources/overview) přehled prostředků Azure.

## <a name="data-encryption"></a>Šifrování dat

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) pomáhá chránit a chránit vaše data, aby splňovala vaše závazky v oblasti zabezpečení a dodržování předpisů v organizaci. Poskytuje šifrování svazku pro operační systém a datové disky virtuálních počítačů clusteru. Azure Disk Encryption se také integruje s [Azure Key Vault](/azure/key-vault/), což nám umožňuje řídit a spravovat šifrovací klíče a tajné klíče disku a zajistit, aby všechna data na discích virtuálních počítačů byla šifrovaná. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Klíče spravované zákazníkem s trezorem klíčů Azure

Ve výchozím nastavení jsou data šifrována pomocí klíčů spravovaných společností Microsoft. Pro další kontrolu nad šifrovacími klíči můžete zadat klíče spravované zákazníkem, které se použijí pro šifrování dat. Šifrování dat můžete spravovat na úrovni úložiště pomocí vlastních klíčů. Klíč spravovaný zákazníkem se používá k ochraně a řízení přístupu ke kořenovému šifrovacímu klíči, který se používá k šifrování a dešifrování všech dat. Klíče spravované zákazníkem nabízejí větší flexibilitu při vytváření, otáčení, zakázání a odvolání ovládacích prvků přístupu. Můžete také auditovat šifrovací klíče používané k ochraně dat.

K ukládání klíčů spravovaných zákazníky použijte Azure Key Vault. Můžete vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault ke generování klíčů. Cluster Azure Data Explorer a Trezor klíčů Azure musí být ve stejné oblasti, ale mohou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [Co je Azure Key Vault?](/azure/key-vault/key-vault-overview). Podrobné vysvětlení klíčů spravovaných zákazníkem najdete v [tématu Klíče spravované zákazníkem pomocí trezoru klíčů Azure](/azure/storage/common/storage-service-encryption). Konfigurace klíčů spravovaných zákazníky v clusteru Azure Data Explorer pomocí [C#](/azure/data-explorer/customer-managed-keys-csharp) nebo [šablony Azure Resource Manager](/azure/data-explorer/customer-managed-keys-resource-manager)

> [!Note]
> Klíče spravované zákazníkem spoléhají na spravované identity pro prostředky Azure, což je funkce Azure Active Directory (Azure AD). Chcete-li nakonfigurovat klíče spravované zákazníky na webu Azure Portal, musíte nakonfigurovat **spravovanou** identitu SystemAssigned do clusteru, jak je podrobně popsáno v [části Konfigurace spravovaných identit pro cluster Azure Data Explorer](/azure/data-explorer/managed-identities).

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ukládání klíčů spravovaných zákazníky v trezoru klíčů Azure

Chcete-li povolit klíče spravované zákazníkem v clusteru, použijte k uložení klíčů trezor azure klíče. Je nutné povolit vlastnosti **Obnovitelné odstranění** a **Nevyčistit** v trezoru klíčů. Trezor klíčů musí být umístěn ve stejném předplatném jako cluster. Azure Data Explorer používá spravované identity pro prostředky Azure k ověření do trezoru klíčů pro operace šifrování a dešifrování. Spravované identity nepodporují scénáře mezi adresáři.

#### <a name="rotate-customer-managed-keys"></a>Otočení klíčů spravovaných zákazníkem

Klíč spravovaný zákazníkem můžete v trezoru klíčů Azure v souladu s vašimi zásadami dodržování předpisů otočit. Při otočení klíče je nutné aktualizovat cluster tak, aby používal nový identifikátor URI klíče. Otočením klíče nespustíte opětovné šifrování dat v clusteru. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Odvolání přístupu ke klíčům spravovaným zákazníkem

Pokud chcete odvolat přístup ke klíčům spravovaným zákazníkem, použijte PowerShell nebo Azure CLI. Další informace najdete [v tématu Azure Key Vault PowerShell](/powershell/module/az.keyvault/) nebo [Azure Key Vault CLI](/cli/azure/keyvault). Zrušení přístupu blokuje přístup ke všem datům na úrovni úložiště clusteru, protože šifrovací klíč je proto nepřístupný pro Azure Data Explorer.

> [!Note]
> Když Průzkumník dat Azure zjistí, že je odvolán přístup ke klíči spravovanému zákazníkem, automaticky pozastaví cluster a odstraní všechna data uložená v mezipaměti. Po vrácení přístupu ke klíči je třeba cluster uobnovit ručně.

## <a name="role-based-access-control"></a>Řízení přístupu na základě role

Pomocí [řízení přístupu na základě rolí (RBAC)](/azure/role-based-access-control/overview)můžete oddělit povinnosti v rámci týmu a udělit pouze požadovaný přístup k uživatelům clusteru. Místo toho, aby všichni neomezená oprávnění v clusteru, můžete povolit pouze určité akce. Řízení [přístupu pro databáze](/azure/data-explorer/manage-database-permissions) můžete nakonfigurovat na [webu Azure Portal](/azure/role-based-access-control/role-assignments-portal), pomocí rozhraní [příkazového příkazu Azure](/azure/role-based-access-control/role-assignments-cli)nebo Azure [PowerShellu](/azure/role-based-access-control/role-assignments-powershell).

## <a name="next-steps"></a>Další kroky

* [Zabezpečte svůj cluster v Průzkumníku dat Azure – portál](manage-cluster-security.md) povolením šifrování v klidovém stavu.
* [Konfigurace spravovaných identit pro cluster Azure Data Explorer](managed-identities.md)
* [Konfigurace klíčů spravovaných zákazníkem pomocí šablony Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Konfigurace klíčů spravovaných zákazníkem pomocí c #](customer-managed-keys-csharp.md)

