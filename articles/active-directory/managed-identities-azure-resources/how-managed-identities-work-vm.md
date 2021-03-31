---
title: Jak fungují spravované identity pro prostředky Azure s virtuálními počítači Azure
description: Popis spravovaných identit pro prostředky Azure pracuje s virtuálními počítači Azure.
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: conceptual
ms.custom: mvc
ms.date: 06/11/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: b93f45b05e6d7773afc2f750fd1a9a034c01ca1e
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "89178667"
---
# <a name="how-managed-identities-for-azure-resources-work-with-azure-virtual-machines"></a>Jak spravované identity prostředků Azure fungují s virtuálními počítači Azure

Spravované identity pro prostředky Azure poskytují služby Azure s automaticky spravovanou identitou v Azure Active Directory. Tuto identitu můžete použít k ověření pro libovolnou službu, která podporuje ověřování Azure AD, a to bez nutnosti přihlašovacích údajů ve vašem kódu.

V tomto článku se dozvíte, jak spravované identity fungují s virtuálními počítači Azure.


## <a name="how-it-works"></a>Jak to funguje

Spravované identity jsou interně instančními objekty speciálního typu, které se dají používat jenom s prostředky Azure. Po odstranění spravované identity se odpovídající objekt služby automaticky odebere.
Při vytvoření User-Assigned nebo System-Assigned identity poskytovatel prostředků spravované identity (MSRP) k této identitě vydává certifikát interně. 

Váš kód může spravovanou identitu použít k vyžádání přístupových tokenů pro služby, které podporují ověřování Azure AD. Azure zajistí vracení přístupových údajů, které instance služby používá. 

Následující diagram ukazuje fungování identit spravovaných služeb s virtuálními počítači Azure:

![Identity spravovaných služeb a virtuální počítače Azure](media/how-managed-identities-work-vm/data-flow.png)

|  Vlastnost    | Spravovaná identita přiřazená systémem | Spravovaná identita přiřazená uživatelem |
|------|----------------------------------|--------------------------------|
| Vytvoření |  Vytvořena jako součást prostředku Azure (například virtuální počítač Azure nebo Azure App Service). | Vytvoří se jako samostatný prostředek Azure. |
| Životní cyklus | Sdílený životní cyklus s prostředkem Azure, pomocí kterého se vytvořila spravovaná identita. <br/> Při odstranění nadřazeného prostředku se odstraní také spravovaná identita. | Nezávislý životní cyklus. <br/> Je nutné explicitně odstranit. |
| Sdílení napříč prostředky Azure | Nelze sdílet. <br/> Dá se přidružit jenom k jednomu prostředku Azure. | Lze sdílet. <br/> Stejná uživatelem přiřazená spravovaná identita může být přidružená k více než jednomu prostředku Azure. |
| Běžné případy použití | Úlohy, které jsou obsaženy v rámci jednoho prostředku Azure. <br/> Úlohy, pro které potřebujete nezávislé identity. <br/> Například aplikace, která běží na jednom virtuálním počítači | Úlohy, které běží na několika prostředcích a které můžou sdílet jedinou identitu. <br/> Úlohy, které vyžadují předběžnou autorizaci zabezpečeného prostředku jako součást toku zřizování. <br/> Úlohy, kde se prostředky recyklují často, ale oprávnění by měla zůstat konzistentní. <br/> Například zatížení, ve kterém více virtuálních počítačů potřebuje přístup ke stejnému prostředku |

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem

1. Azure Resource Manager přijme požadavek, aby na virtuálním počítači povolil spravovanou identitu přiřazenou systémem.

2. Azure Resource Manager pro identitu virtuálního počítače vytvoří instanční objekt v Azure AD. Instanční objekt se vytvoří v tenantovi Azure AD důvěryhodném pro dané předplatné.

3. Azure Resource Manager konfiguruje identitu na virtuálním počítači tím, že aktualizuje koncový bod Azure Instance Metadata Service identity s ID a certifikátem klienta instančního objektu.

4. Jakmile bude virtuální počítač mít identitu, s použitím informací o instančním objektu udělte virtuálnímu počítači přístup k prostředkům Azure. Pokud chcete volat Azure Resource Manager, použijte řízení přístupu na základě role Azure (Azure RBAC) a přiřaďte příslušné roli k instančnímu objektu virtuálního počítače. Pokud chcete volat službu Key Vault, udělte kódu přístup ke konkrétnímu tajnému kódu nebo klíči ve službě Key Vault.

5. Váš kód, který běží na virtuálním počítači, může požádat o token z koncového bodu služby metadat instance Azure, který je přístupný jenom z virtuálního počítače: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.
    - Parametr verze rozhraní API určuje verzi IMDS. Použijte api-version=2018-02-01 nebo novější.

6. Zavolá se služba Azure AD s požadavkem na přístupový token (jak je popsáno v kroku 5) s použitím ID klienta a certifikátu nakonfigurovaných v kroku 3. Azure AD vrátí přístupový token JSON Web Token (JWT).

7. Váš kód odešle přístupový token prostřednictvím volání do služby, která podporuje ověřování Azure AD.

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem

1. Azure Resource Manager přijme požadavek na vytvoření spravované identity přiřazené uživatelem.

2. Azure Resource Manager vytvoří pro spravovanou identitu přiřazenou uživatelem v Azure AD instanční objekt. Instanční objekt se vytvoří v tenantovi Azure AD důvěryhodném pro dané předplatné.

3. Azure Resource Manager obdrží požadavek na konfiguraci spravované identity přiřazené uživatelem na virtuálním počítači a aktualizuje koncový bod Azure Instance Metadata Service identity s uživatelem přiřazeným ID a certifikátem klienta instančního objektu spravované identity.

4. Jakmile vytvoříte spravovanou identitu přiřazenou uživatelem, použijte informace o instančním objektu a udělte identitě přístup k prostředkům Azure. Pokud chcete volat Azure Resource Manager, pomocí služby Azure RBAC přiřaďte příslušné roli instančnímu objektu identity přiřazené uživatelem. Pokud chcete volat službu Key Vault, udělte kódu přístup ke konkrétnímu tajnému kódu nebo klíči ve službě Key Vault.

   > [!Note]
   > Tento krok můžete provést také před krokem 3.

5. Váš kód, který běží na virtuálním počítači, může požádat o token z koncového bodu Azure Instance Metadata Service identity přístupný jenom z virtuálního počítače: `http://169.254.169.254/metadata/identity/oauth2/token`
    - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.
    - Parametr ID klienta určuje identitu, pro kterou se token požaduje. Tato hodnota je nutná k jednoznačnému určení v případě, že je na jednom virtuálním počítači více identit přiřazených uživatelem.
    - Parametr verze rozhraní API určuje verzi služby Azure Instance Metadata Service. Použijte `api-version=2018-02-01` nebo novější.

6. Zavolá se služba Azure AD s požadavkem na přístupový token (jak je popsáno v kroku 5) s použitím ID klienta a certifikátu nakonfigurovaných v kroku 3. Azure AD vrátí přístupový token JSON Web Token (JWT).
7. Váš kód odešle přístupový token prostřednictvím volání do služby, která podporuje ověřování Azure AD.


## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat funkci spravovaných identit prostředků Azure, projděte si následující články Rychlý start:

* [Použití spravované identity přiřazené systémem ve virtuálním počítači s Windows pro přístup k Resource Manageru](tutorial-windows-vm-access-arm.md)
* [Použití spravované identity přiřazené systémem ve virtuálním počítači s Linuxem pro přístup k Resource Manageru](tutorial-linux-vm-access-arm.md)
