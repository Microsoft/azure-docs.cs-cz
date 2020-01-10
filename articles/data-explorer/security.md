---
title: Zabezpečení clusterů Azure Průzkumník dat v Azure
description: Přečtěte si informace o tom, jak zabezpečit clustery v Azure Průzkumník dat.
author: saguiitay
ms.author: itsagui
ms.reviewer: orspodek
ms.service: data-explorer
ms.topic: conceptual
ms.date: 01/06/2020
ms.openlocfilehash: cc16a40b0ea53d433a5a6c592f3b9ea364ef9089
ms.sourcegitcommit: 02160a2c64a5b8cb2fb661a087db5c2b4815ec04
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/07/2020
ms.locfileid: "75725829"
---
# <a name="secure-azure-data-explorer-clusters-in-azure"></a>Zabezpečení clusterů Azure Průzkumník dat v Azure

Tento článek poskytuje Úvod do zabezpečení v Azure Průzkumník dat, který vám může pomáhat chránit vaše data a prostředky v cloudu a plnit požadavky na zabezpečení vaší firmy. Je důležité udržovat své clustery v bezpečí. Zabezpečení clusterů zahrnuje jednu nebo víc funkcí Azure, které zahrnují zabezpečený přístup a úložiště. Tento článek poskytuje informace, které vám pomůžou udržet zabezpečený cluster.

## <a name="managed-identities-for-azure-resources"></a>Spravované identity pro prostředky Azure

Běžným problémem při sestavování cloudových aplikací je Správa přihlašovacích údajů v kódu pro ověřování ke cloudovým službám. Zajištění zabezpečení těchto přihlašovacích údajů je důležitý úkol. Přihlašovací údaje by se neměly ukládat do řídicích stanic pro vývojáře ani do správy zdrojového kódu. Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů, tajných kódů a dalších klíčů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst.

Tento problém řeší funkce spravované identity Azure Active Directory (Azure AD) pro prostředky Azure. Tato funkce poskytuje službám Azure automaticky spravovanou identitu v Azure AD. Tuto identitu můžete použít k ověření u jakékoli služby, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje. Další informace o této službě najdete na stránce Přehled [spravovaných identit pro prostředky Azure](/azure/active-directory/managed-identities-azure-resources/overview) .

## <a name="data-encryption"></a>Šifrování dat

### <a name="azure-disk-encryption"></a>Azure Disk Encryption

[Azure Disk Encryption](/azure/security/azure-security-disk-encryption-overview) pomáhá chránit a chránit vaše data, aby splňovala závazky zabezpečení vaší organizace a dodržování předpisů. Poskytuje šifrování svazku pro operační systém a datové disky virtuálních počítačů vašeho clusteru. Azure Disk Encryption se taky integruje s [Azure Key Vault](/azure/key-vault/), což nám umožňuje řídit a spravovat klíče a tajné kódy disku a zajistit, aby byla všechna data na discích virtuálních počítačů šifrovaná. 

### <a name="customer-managed-keys-with-azure-key-vault"></a>Klíče spravované zákazníkem s Azure Key Vault

Ve výchozím nastavení se data šifrují pomocí klíčů spravovaných Microsoftem. Pro další kontrolu nad šifrovacími klíči můžete zadat klíče spravované zákazníkem, které budou použity k šifrování dat. Šifrování dat na úrovni úložiště můžete spravovat pomocí vlastních klíčů. Klíč spravovaný zákazníkem slouží k ochraně a řízení přístupu ke kořenovému šifrovacímu klíči, který se používá k šifrování a dešifrování všech dat. Klíče spravované zákazníkem nabízejí větší flexibilitu při vytváření, střídání, zakázání a odvolávání řízení přístupu. Můžete také auditovat šifrovací klíče používané k ochraně vašich dat.

K ukládání klíčů spravovaných zákazníkem použijte Azure Key Vault. Můžete vytvářet vlastní klíče a ukládat je do trezoru klíčů nebo můžete použít rozhraní Azure Key Vault API k vygenerování klíčů. Cluster Azure Průzkumník dat a Azure Key Vault musí být ve stejné oblasti, ale můžou být v různých předplatných. Další informace o Azure Key Vault najdete v tématu [co je Azure Key Vault?](/azure/key-vault/key-vault-overview). Podrobné vysvětlení klíčů spravovaných zákazníkem najdete v tématu [klíče spravované zákazníkem pomocí Azure Key Vault](/azure/storage/common/storage-service-encryption)

> [!Note]
> Klíče spravované zákazníkem spoléhají na spravované identity prostředků Azure, což je funkce Azure Active Directory (Azure AD). Ke konfiguraci klíčů spravovaných zákazníkem v Azure Portal musíte pro svůj cluster nakonfigurovat spravovanou identitu **SystemAssigned** .

#### <a name="store-customer-managed-keys-in-azure-key-vault"></a>Ukládání klíčů spravovaných zákazníkem v Azure Key Vault

Pokud chcete povolit klíče spravované zákazníkem v clusteru, použijte Azure Key Vault k uložení klíčů. V trezoru klíčů musíte povolit jak **obnovitelné odstranění** , tak i **Nemazat** vlastnosti. Trezor klíčů se musí nacházet ve stejném předplatném jako cluster. Azure Průzkumník dat používá spravované identity pro prostředky Azure k ověření v trezoru klíčů pro operace šifrování a dešifrování. Spravované identity nepodporují scénáře pro více adresářů.

#### <a name="rotate-customer-managed-keys"></a>Otočit klíče spravované zákazníkem

Klíč spravovaný zákazníkem můžete v Azure Key Vault otočit podle vašich zásad dodržování předpisů. Když je klíč otočen, je nutné aktualizovat cluster tak, aby používal nový identifikátor URI klíče. Otáčení klíče neaktivuje opětovné šifrování dat v clusteru. 

#### <a name="revoke-access-to-customer-managed-keys"></a>Odvolat přístup k klíčům spravovaným zákazníkem

K odvolání přístupu ke klíčům spravovaným zákazníkem použijte PowerShell nebo Azure CLI. Další informace najdete v tématu [Azure Key Vault PowerShellu](/powershell/module/az.keyvault/) nebo rozhraní příkazového [řádku Azure Key Vault](/cli/azure/keyvault). Odvolání přístupu blokuje přístup ke všem datům na úrovni úložiště clusteru, protože šifrovací klíč je následně nepřístupný v Azure Průzkumník dat.

> [!Note]
> Když Azure Průzkumník dat identifikuje, že přístup k klíčům spravovaným zákazníkem je odvolán, bude automaticky pozastaven clusterem, aby odstranil všechna data uložená v mezipaměti. Po vrácení přístupu ke klíči je potřeba cluster obnovit ručně.

## <a name="role-based-access-control"></a>Řízení přístupu založené na rolích

Pomocí [řízení přístupu na základě role (RBAC)](/azure/role-based-access-control/overview)můžete oddělit povinnosti v rámci svého týmu a udělit pouze požadovaný přístup pro uživatele clusteru. Místo udělení všech neomezených oprávnění ke clusteru můžete použít jenom určité akce. [Řízení přístupu pro databáze](/azure/data-explorer/manage-database-permissions) můžete nakonfigurovat v [Azure Portal](/azure/role-based-access-control/role-assignments-portal)pomocí rozhraní příkazového [řádku Azure](/azure/role-based-access-control/role-assignments-cli)nebo [Azure PowerShell](/azure/role-based-access-control/role-assignments-powershell).

## <a name="next-steps"></a>Další kroky

* [Konfigurace spravovaných identit pro cluster Azure Průzkumník dat](managed-identities.md)
* [Zabezpečte svůj cluster v Azure Průzkumník dat – na portálu](manage-cluster-security.md) tím, že povolíte šifrování v klidovém prostředí.
* [Konfigurace klíčů spravovaných zákazníkem pomocí šablony Azure Resource Manager](customer-managed-keys-resource-manager.md)
* [Konfigurace klíčů spravovaných zákazníkem pomocíC#](customer-managed-keys-csharp.md)

