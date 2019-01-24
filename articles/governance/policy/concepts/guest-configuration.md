---
title: Naučte se provádět audit uvnitř virtuálního počítače
description: Zjistěte, jak Azure Policy používá hostovaný konfigurace auditování nastavení ve virtuálním počítači Azure.
services: azure-policy
author: DCtheGeek
ms.author: dacoulte
ms.date: 01/23/2019
ms.topic: conceptual
ms.service: azure-policy
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 0a571084819c5dfed3f8d6891b59032ef2eecdd6
ms.sourcegitcommit: 8115c7fa126ce9bf3e16415f275680f4486192c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/24/2019
ms.locfileid: "54856396"
---
# <a name="understand-azure-policys-guest-configuration"></a>Porozumět konfiguraci hosta Azure Policy

Kromě auditování a [oprava](../how-to/remediate-resources.md) prostředky Azure, Azure Policy je schopen auditování uvnitř virtuálního počítače. Ověření se provede tak, že rozšíření konfigurace hosta a klienta. Toto rozšíření prostřednictvím klienta, ověří nastavení jako konfigurace operačního systému, konfigurace aplikace nebo přítomnost, nastavení prostředí a další.

> [!IMPORTANT]
> V současné době pouze **integrované** zásady jsou podporované s konfigurací hosta.

## <a name="extension-and-client"></a>Rozšíření a klienta

Auditování nastavení uvnitř virtuálního počítače, [rozšíření virtuálního počítače](../../../virtual-machines/extensions/overview.md) je povolená. Rozšíření stahuje použitelné zásady přiřazení a odpovídající definici konfigurace.

### <a name="register-guest-configuration-resource-provider"></a>Registrace poskytovatele prostředků konfigurace hosta

Před použitím konfigurace hosta, zaregistrujte poskytovatele prostředků. Můžete zaregistrovat prostřednictvím portálu nebo pomocí Powershellu.

#### <a name="registration---portal"></a>Registrace – portál

Registrace poskytovatele prostředků pro konfiguraci hostovaný na webu Azure portal, postupujte podle těchto kroků:

1. Spusťte na webu Azure portal a klikněte na **všechny služby**. Vyhledejte a vyberte **předplatná**.

1. Vyhledejte a klikněte na předplatné, které chcete povolit konfiguraci hosta pro.

1. V levé nabídce **předplatné** klikněte na **poskytovatelů prostředků**.

1. Filtrovat nebo můžete najít pomocí posuvníku **Microsoft.GuestConfiguration**, pak klikněte na tlačítko **zaregistrovat** na stejném řádku.

#### <a name="registration---powershell"></a>Registrace – PowerShell

Zaregistrovat poskytovatele prostředků pro konfiguraci typu Host pomocí prostředí PowerShell, spusťte následující příkaz:

```azurepowershell-interactive
# Login first with Connect-AzAccount if not using Cloud Shell
Register-AzResourceProvider -ProviderNamespace 'Microsoft.GuestConfiguration'
```

### <a name="validation-tools"></a>Nástroje pro ověření

Ve virtuálním počítači hosta konfigurace klienta použije místní nástroje pro spuštění auditu.

V následující tabulce je seznam nástrojů pro místní použít na všech podporovaných operačních systémech:

|Operační systém|Nástroje pro ověření|Poznámky|
|-|-|-|
|Windows|[Microsoft Desired State Configuration](/powershell/dsc) v2| |
|Linux|[Chef InSpec](https://www.chef.io/inspec/)| Ruby a Python instaluje rozšíření konfigurace hosta. |

### <a name="supported-client-types"></a>Podporované klientské typy

Následující tabulka uvádí seznam podporovaný operační systém v imagích Azure:

|Vydavatel|Název|Verze|
|-|-|-|
|Canonical|Ubuntu Server|14.04, 16.04, 18.04|
|credativ|Debian|8, 9|
|Microsoft|Windows Server|Datového centra 2012, 2012 R2 Datacenter, 2016 Datacenter|
|OpenLogic|CentOS|7.3, 7.4, 7.5|
|Red Hat|Red Hat Enterprise Linux|7.4, 7.5|
|SuSE|SLES|12 SP3|

> [!IMPORTANT]
> Konfigurace hostovaného není momentálně podporovaná na vlastní Image virtuálních počítačů.

### <a name="unsupported-client-types"></a>Nepodporované klientské typy

Následující tabulka uvádí operační systémy, které nejsou podporovány:

|Operační systém|Poznámky|
|-|-|
|Klient Windows | Klientské operační systémy (například Windows 7 a Windows 10) nejsou podporovány.
|Windows Server 2016 Nano Server | Nepodporuje se.|

## <a name="guest-configuration-definition-requirements"></a>Požadavky na konfiguraci hosta definice

Každý audit spuštění hosta konfigurace vyžaduje dvě definice zásad **DeployIfNotExists** a **AuditIfNotExists**. **DeployIfNotExists** slouží k přípravě virtuálního počítače s agentem hosta konfigurace a další komponenty pro podporu [ověřovacích nástrojů](#validation-tools).

**DeployIfNotExists** definici zásad ověří a řeší následující položky:

- Ověření virtuální počítač má přiřazenou konfiguraci, kterou chcete vyhodnotit. Pokud aktuálně neexistuje žádná přiřazení, získejte přiřazení a příprava virtuálního počítače podle:
  - Ověřování pomocí virtuálního počítače [spravované identity](../../../active-directory/managed-identities-azure-resources/overview.md)
  - Instalace nejnovější verze **Microsoft.GuestConfiguration** rozšíření
  - Instalace [ověřovacích nástrojů](#validation-tools) a závislostí, v případě potřeby

Jednou **DeployIfNotExists** vyhovující předpisům, je **AuditIfNotExists** definice zásady používá nástroje pro místní ověřování k určení, zda je přiřazení přiřazených konfigurací dodržujících předpisy nebo Nevyhovující předpisům. Nástroj ověření poskytuje výsledky klientovi Configuration hosta. Klient předává výsledky hosta rozšíření, které zpřístupní je prostřednictvím poskytovatele prostředků konfigurace hosta.

Služba Azure Policy používá poskytovatele prostředků hosta konfigurace **complianceStatus** vlastností na sestavu dodržování předpisů v **dodržování předpisů** uzlu. Další informace najdete v tématu [získávají data dodržování předpisů](../how-to/getting-compliance-data.md).

> [!NOTE]
> Pro každou definici typu Host konfigurace i **DeployIfNotExists** a **AuditIfNotExists** definice zásad musí existovat.

Všechny integrované zásady pro konfiguraci hosta jsou součástí iniciativy do definice pro použití v přiřazení skupiny. Integrované iniciativu s názvem *[Preview]: Audit zabezpečení hesla uvnitř virtuálního počítače s Linuxem a Windows* obsahuje 18 zásady. Obsahuje šest **DeployIfNotExists** a **AuditIfNotExists** dvojice pro Windows a tři páry pro Linux. V každém případě logika uvnitř definice ověří pouze cílový operační systém se vyhodnocuje na základě [pravidlo zásad](definition-structure.md#policy-rule) definice.

## <a name="next-steps"></a>Další postup

- Projděte si příklady v [ukázek Azure Policy](../samples/index.md)
- Zkontrolujte [struktura definic zásad](definition-structure.md)
- Kontrola [Principy účinky zásad](effects.md)
- Pochopit postup [programové vytváření zásad](../how-to/programmatically-create.md)
- Zjistěte, jak [získat data o dodržování předpisů](../how-to/getting-compliance-data.md)
- Zjistěte, jak [opravit nekompatibilní prostředky](../how-to/remediate-resources.md)
- Připomenutí skupin pro správu v článku [Uspořádání prostředků pomocí skupin pro správu Azure](../../management-groups/index.md)