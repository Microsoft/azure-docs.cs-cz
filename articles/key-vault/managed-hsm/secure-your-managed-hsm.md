---
title: Zabezpečený přístup ke spravovanému modulu HSM spravovanému modulem HSM Azure Key Vault
description: Naučte se zabezpečit přístup ke spravovanému hardwarovému HSM pomocí Azure RBAC a místního spravovaného HSM RBAC.
services: key-vault
author: amitbapat
tags: azure-resource-manager
ms.service: key-vault
ms.subservice: managed-hsm
ms.topic: tutorial
ms.date: 09/15/2020
ms.author: ambapat
ms.openlocfilehash: 18ffa0f878effda8888200c13ab312851aaebdcd
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "91000718"
---
# <a name="secure-access-to-your-managed-hsms"></a>Zabezpečený přístup ke spravovaným HSM

Azure Key Vault spravovaný modul HSM je cloudová služba, která chrání šifrovací klíče. Vzhledem k tomu, že tato data jsou citlivá a důležitá pro podnikání, je potřeba zabezpečit přístup ke spravovaným HSM tím, že povolíte přístup jenom autorizovaným aplikacím a uživatelům. Tento článek poskytuje přehled spravovaného modelu řízení přístupu HSM. Vysvětluje ověřování a autorizaci a popisuje, jak zabezpečit přístup ke spravovaným HSM.

V tomto kurzu se seznámíte s jednoduchým příkladem, který vám ukáže, jak dosáhnout oddělení povinností a řízení přístupu pomocí Azure RBAC a místního spravovaného HSM RBAC. Další informace o spravovaném modelu řízení přístupu HSM najdete v tématu [spravované řízení přístupu HSM](access-control.md) .

## <a name="prerequisites"></a>Požadavky

K dokončení kroků v tomto článku musíte mít následující položky:

* Předplatné Microsoft Azure. Pokud žádný nemáte, můžete si zaregistrovat [bezplatnou zkušební verzi](https://azure.microsoft.com/pricing/free-trial).
* Verze Azure CLI 2.12.0 nebo novější. Verzi zjistíte spuštěním příkazu `az --version`. Pokud potřebujete instalaci nebo upgrade, přečtěte si téma [Instalace rozhraní příkazového řádku Azure CLI]( /cli/azure/install-azure-cli).
* Spravovaný modul HSM v rámci vašeho předplatného. Informace najdete v tématu [rychlý Start: zřízení a aktivace spravovaného modulu HSM pomocí Azure CLI](quick-create-cli.md) , který umožňuje zřídit a aktivovat spravovaný modul HSM.

[!INCLUDE [cloud-shell-try-it.md](../../../includes/cloud-shell-try-it.md)]

## <a name="sign-in-to-azure"></a>Přihlášení k Azure

Pokud se chcete přihlásit k Azure pomocí rozhraní příkazového řádku, můžete zadat:

```azurecli
az login
```

Další informace o možnostech přihlášení prostřednictvím rozhraní příkazového řádku najdete v tématu [přihlášení pomocí Azure CLI](/cli/azure/authenticate-azure-cli?view=azure-cli-latest&preserve-view=true) .

## <a name="example"></a>Příklad

V tomto příkladu vyvíjíme aplikaci, která pro operace podepisování používá RSA 2 048 bitový klíč. Naše aplikace běží na virtuálním počítači Azure se [spravovanou identitou](../../active-directory/managed-identities-azure-resources/overview.md). Klíč RSA použitý k podepisování je uložený v našem spravovaném modulu HSM.

Identifikovali jsme následující role, které spravují, nasazují a auditují naši aplikaci:

- **Bezpečnostní tým**: zaměstnanci oddělení IT od kanceláře CSO (hlavní bezpečnostní důstojník) nebo podobné přispěvatelé. Bezpečnostní tým je zodpovědný za správné úschovy klíčů. Klíče RSA nebo ES pro podepisování a klíče RSA nebo AES pro šifrování dat.
- **Vývojáři a operátoři**: zaměstnanci, kteří aplikaci vyvíjejí a nasazují v Azure. Členové tohoto týmu nejsou součástí bezpečnostních pracovníků. Nemají přístup k citlivým datům, jako jsou klíče RSA. K těmto citlivým datům by měli mít přístup jenom aplikace, kterou nasazuje.
- **Auditori**: Tato role je určena pro přispěvatele, kteří nejsou členy vývoje nebo obecných zaměstnanců IT. Kontrolují použití a údržbu certifikátů, klíčů a tajných klíčů, aby bylo zajištěno dodržování standardů zabezpečení.

Existuje jiná role, která je mimo rozsah naší aplikace: Správce předplatného (nebo skupiny prostředků). Správce předplatného nastavuje počáteční přístupová oprávnění pro bezpečnostní tým. Poskytují přístup k bezpečnostnímu týmu pomocí skupiny prostředků, která má prostředky požadované aplikací.

Pro naše role musíme autorizovat tyto operace:

**Bezpečnostní tým**
- Vytvořte spravovaný modul HSM.
- Stažení spravované domény zabezpečení HSM (pro zotavení po havárii)
- Zapněte protokolování.
- Generování nebo import klíčů
- Vytvořte spravované zálohy modulu HSM pro zotavení po havárii.
- Nastavení místní RBAC pro správu HSM pro udělení oprávnění uživatelům a aplikacím pro konkrétní operace.
- Pravidelně převeďte klíče.

**Vývojáři a operátoři**
- Získejte odkaz (identifikátor URI klíče) od týmu zabezpečení a klíč RSA, který se používá pro podepisování.
- Vytvořte a nasaďte aplikaci, která programově přistupuje ke klíči.

**Auditoři**
- Zkontrolujte data vypršení platnosti klíčů, abyste zajistili aktuálnost klíčů.
- Monitorování přiřazení rolí, aby se k klíčům mohly přihlížet jenom autorizovaní uživatelé/aplikace
- Zkontrolujte spravované protokoly HSM a potvrďte správné použití klíčů v dodržování standardů zabezpečení dat.

Následující tabulka shrnuje přiřazení rolí k týmům a prostředkům pro přístup ke spravovanému modulu HSM.

| Role | Role roviny správy | Role roviny dat |
| --- | --- | --- |
| Bezpečnostní tým | Spravovaný Přispěvatel HSM | Spravovaný správce HSM |
| Vývojáři a operátoři | Žádné | Žádné |
| Auditoři | Žádné | Spravovaný auditor kryptografie HSM |
| Spravované označení virtuálního počítače používaného aplikací| Žádné | Spravovaný kryptografický uživatel HSM |
| Spravovaná identita účtu úložiště používaného aplikací| Žádné| Spravované šifrování služby HSM |

Tři role týmu potřebují přístup k dalším prostředkům společně s spravovanými oprávněními HSM. K nasazení virtuálních počítačů (nebo Web Apps funkce Azure App Service) potřebují vývojáři a operátoři `Contributor` přístup k těmto typům prostředků. Auditoři potřebují přístup pro čtení k účtu úložiště, ve kterém jsou uložené spravované protokoly HSM.

Pokud chcete přiřadit role roviny správy (Azure RBAC), můžete použít Azure Portal nebo libovolné jiné rozhraní pro správu, jako je Azure CLI nebo Azure PowerShell. Pokud chcete přiřadit role spravovaného datového roviny HSM, musíte použít Azure CLI.

Fragmenty rozhraní příkazového řádku Azure CLI v této části jsou sestavené s následujícími předpoklady:

- Správce Azure Active Directory vytvořil skupiny zabezpečení, které reprezentují tři role: tým zabezpečení contoso, contoso App DevOps a contoso App auditory. Správce přidal uživatele do příslušných skupin.
- Všechny prostředky jsou umístěné ve skupině prostředků **ContosoAppRG** .
- Spravované protokoly HSM se ukládají v účtu úložiště **contosologstorage** .
- **ContosoMHSM** spravovaný HSM a účet úložiště **contosologstorage** jsou ve stejném umístění Azure.

Správce předplatného přiřadí `Managed HSM Contributor` roli bezpečnostnímu týmu. Tato role umožňuje bezpečnostnímu týmu spravovat existující spravovaná HSM a vytvářet nové. Pokud existují spravované HSM, bude jim potřeba přiřadit roli spravovaného správce HSM, aby je bylo možné spravovat.

```azurecli-interactive
# This role assignment allows Contoso Security Team to create new Managed HSMs
az role assignment create --assignee-object-id $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --assignee-principal-type Group --role "Managed HSM Contributor"

# This role assignment allows Contoso Security Team to become administrator of existing managed HSM
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso Security Team' --query 'objectId' -o tsv) --scope / --role "Managed HSM Administrator"
```

Tým zabezpečení nastavuje protokolování a přiřazuje role auditorům a aplikaci virtuálních počítačů.

```azurecli-interactive
# Enable logging
hsmresource=$(az keyvault show --hsm-name ContosoMHSM --query id -o tsv)
storageresource=$(az storage account show --name contosologstorage --query id -o tsv)
az monitor diagnostic-settings create --name MHSM-Diagnostics --resource $hsmresource --logs    '[{"category": "AuditEvent","enabled": true}]' --storage-account $storageresource

# Assign the "Crypto Auditor" role to Contoso App Auditors group. It only allows them to read.
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az ad group show -g 'Contoso App Auditors' --query 'objectId' -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Grant the "Crypto User" role to the VM's managed identity. It allows to create and use keys. 
# However it cannot permanently delete (purge) keys
az keyvault role assignment create  --hsm-name ContosoMHSM --assignee $(az vm identity show --name "vmname" --resource-group "ContosoAppRG" --query objectId -o tsv) --scope / --role "Managed HSM Crypto Auditor"

# Assign "Managed HSM Crypto Service Encryption" role to the Storage account ID
storage_account_principal=$(az storage account show --id $storageresource --query identity.principalId -o tsv)
# (if no identity exists), then assign a new one
[ "$storage_account_principal" ] || storage_account_principal=$(az storage account update --assign-identity --id $storageresource)

az keyvault role assignment create --hsm-name ContosoMHSM --role "Managed HSM Crypto Service Encryption" --assignee $storage_account_principal
```

V tomto kurzu se zobrazí pouze akce týkající se řízení přístupu pro většinu částí. Jiné akce a operace související s nasazením aplikace ve vašem VIRTUÁLNÍm počítači – zapnutím šifrování pomocí spravovaného klíče zákazníka pro účet úložiště se tady nezobrazuje vytváření spravovaného modulu HSM, aby se příklad zaměřil na řízení přístupu a správu rolí.

Náš příklad popisuje jednoduchý scénář. Scénáře reálného života můžou být složitější. V závislosti na vašich potřebách můžete upravit oprávnění k vašemu trezoru klíčů. Předpokládali jsme, že tým zabezpečení poskytuje klíče a tajné odkazy (identifikátory URI a kryptografické otisky), které jsou používány DevOps zaměstnanci ve svých aplikacích. Vývojáři a operátoři nevyžadují přístup k rovině dat. Zaměřili jsme se na to, jak váš Trezor klíčů zabezpečit. Při zabezpečování [virtuálních počítačů](https://azure.microsoft.com/services/virtual-machines/security/), [účtů úložiště](../../storage/blobs/security-recommendations.md)a dalších prostředků Azure dejte k podobným pozornost.

## <a name="resources"></a>Zdroje informací

- [Dokumentace ke službě Azure RBAC](../../role-based-access-control/overview.md)
- [Azure RBAC: předdefinované role](../../role-based-access-control/built-in-roles.md)
- [Správa služby Azure RBAC pomocí Azure CLI](../../role-based-access-control/role-assignments-cli.md)

## <a name="next-steps"></a>Další kroky

Úvodní kurz pro správce najdete v tématu [co je spravovaný modul HSM?](overview.md).

Další informace o protokolování využití spravovaného protokolování HSM najdete v tématu [MANAGED HSM Logging](logging.md).
