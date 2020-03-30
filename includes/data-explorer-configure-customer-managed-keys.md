---
author: orspod
ms.service: data-explorer
ms.topic: include
ms.date: 01/07/2020
ms.author: orspodek
ms.openlocfilehash: 7f5c02c6c009e8916ed063454e0ae6049892e95c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80297908"
---
Azure Data Explorer šifruje všechna data v účtu úložiště v klidovém stavu. Ve výchozím nastavení jsou data šifrována pomocí klíčů spravovaných společností Microsoft. Pro další kontrolu nad šifrovacími klíči můžete zadat klíče spravované zákazníkem, které se použijí pro šifrování dat. 

Klíče spravované zákazníkem musí být uloženy v [trezoru klíčů Azure](/azure/key-vault/key-vault-overview). Můžete vytvořit vlastní klíče a uložit je do trezoru klíčů, nebo můžete použít rozhraní API Azure Key Vault ke generování klíčů. Cluster Azure Data Explorer a trezor klíčů musí být ve stejné oblasti, ale mohou být v různých předplatných. Podrobné vysvětlení klíčů spravovaných zákazníkem najdete v [tématu klíče spravované zákazníkem pomocí služby Azure Key Vault](/azure/storage/common/storage-service-encryption). 

Tento článek ukazuje, jak nakonfigurovat klíče spravované zákazníkem.

## <a name="configure-azure-key-vault"></a>Konfigurace Azure Key Vaultu

Chcete-li nakonfigurovat klíče spravované zákazníkem pomocí Průzkumníka dat Azure, musíte [nastavit dvě vlastnosti v trezoru klíčů:](/azure/key-vault/key-vault-ovw-soft-delete) **Obnovitelné odstranění** a **Neodstraňovat**. Tyto vlastnosti nejsou ve výchozím nastavení povoleny. Chcete-li povolit tyto vlastnosti, **proveďte povolení obnovitelného odstranění** a **povolení ochrany proti vymazání** v [prostředí PowerShell](/azure/key-vault/key-vault-soft-delete-powershell) nebo [Azure CLI](/azure/key-vault/key-vault-soft-delete-cli) v novém nebo existujícím trezoru klíčů. Podporovány jsou pouze klíče RSA velikosti 2048. Další informace o klíčích naleznete v tématu [Key Vault keys](/azure/key-vault/about-keys-secrets-and-certificates#key-vault-keys).

> [!NOTE]
> Šifrování dat pomocí klíčů spravovaných zákazníkem není podporováno v [clusterech vedoucích a sledujících](/azure/data-explorer/follower). 

## <a name="assign-an-identity-to-the-cluster"></a>Přiřazení identity ke clusteru

Chcete-li povolit klíče spravované zákazníkem pro váš cluster, nejprve přiřaďte clusteru spravovanou identitu přiřazenou systémem. Tuto spravovanou identitu použijete k udělení oprávnění clusteru pro přístup k trezoru klíčů. Chcete-li nakonfigurovat spravované identity přiřazené systémem, přečtěte si informace [o spravovaných identitách](/azure/data-explorer/managed-identities).