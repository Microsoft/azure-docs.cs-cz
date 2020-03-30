---
title: Spravované identity pro prostředky Azure
description: Přehled informací o spravovaných identitách prostředků Azure
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 03/25/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 707b03d46615f3acfa0797d1dc0865d53ef75dc0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80282116"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Co jsou spravované identity pro prostředky Azure?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Běžnou výzvou při vytváření cloudových aplikací je, jak v kódu spravovat přihlašovací údaje pro ověřování u cloudových služeb. Zajištění zabezpečení těchto přihlašovacích údajů je důležitý úkol. V ideálním případě by se přihlašovací údaje nikdy neměly nacházet na vývojářských pracovních stanicích ani se vracet se změnami do správy zdrojového kódu. Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů, tajných kódů a dalších klíčů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst.

Tento problém řeší funkce spravovaných identit prostředků Azure v Azure Active Directory (Azure AD). Tato funkce poskytuje službám Azure automaticky spravovanou identitu v Azure AD. Tuto identitu můžete použít k ověření u jakékoli služby, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.

Funkce spravovaných identit prostředků Azure je bezplatnou součástí Azure AD, pokud máte předplatné Azure. Neúčtují se za ní žádné další poplatky.

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

## <a name="terminology"></a>Terminologie

Následující termíny se používají v celé spravované identity pro sadu dokumentace prostředků Azure:

- **ID klienta** – jedinečný identifikátor generovaný službou Azure AD, který je vázán na instanční objekt aplikace a služby během počátečního zřizování.
- **ID objektu** - ID objektu objektu instancí pro spravovanou identitu, který se používá k udělení přístupu k prostředku Azure na základě rolí.
- **Azure Instance Metadata Service (IMDS)** – koncový bod REST přístupný všem virtuálním počítačům IaaS vytvořeným prostřednictvím Správce prostředků Azure. Koncový bod je k dispozici na známé nesměrovatelné IP adrese (169.254.169.254), ke které lze přistupovat pouze z virtuálního počítačů.

## <a name="how-does-the-managed-identities-for-azure-resources-work"></a>Jak fungují spravované identity pro prostředky Azure?

Existují dva typy spravovaných identit:

- **Spravovaná identita přiřazená systémem** se povoluje přímo v instanci služby Azure. Když je tato identita povolená, Azure vytvoří identitu pro instanci v tenantovi Azure AD důvěryhodném pro předplatné instance. Po vytvoření identity se přihlašovací údaje zřídí do instance. Životní cyklus identity přiřazené systémem je přímo spojený s instancí služby Azure, pro kterou je povolená. Pokud se instance odstraní, Azure automaticky vyčistí přihlašovací údaje a identitu v Azure AD.
- **Spravovaná identita přiřazená uživatelem** se vytváří jako samostatný prostředek Azure. Prostřednictvím procesu vytvoření Azure vytvoří identitu v tenantovi Azure AD důvěryhodném pro použité předplatné. Po vytvoření identity je možné ji přiřadit k jedné nebo několika instancím služeb Azure. Životní cyklus identity přiřazené uživatelem se spravuje nezávisle na životním cyklu instancí služeb Azure, ke kterým je přiřazená.

Interně spravované identity jsou instanční objekty zvláštního typu, které jsou uzamčeny pro jenom s prostředky Azure. Při odstranění spravované identity je automaticky odebrán odpovídající instanční objekt.

Váš kód může spravovanou identitu použít k vyžádání přístupových tokenů pro služby, které podporují ověřování Azure AD. Azure zajistí vracení přístupových údajů, které instance služby používá.

Následující diagram ukazuje fungování identit spravovaných služeb s virtuálními počítači Azure:

![Identity spravovaných služeb a virtuální počítače Azure](media/overview/data-flow.png)

|  Vlastnost    | Spravovaná identita přiřazená systémem | Spravovaná identita přiřazená uživatelem |
|------|----------------------------------|--------------------------------|
| Vytvoření |  Vytvořeno jako součást prostředku Azure (například virtuální počítač Azure nebo služba Azure App Service). | Vytvořeno jako samostatný prostředek Azure |
| Životní cyklus | Sdílený životní cyklus s prostředků Azure, se kterým se vytvoří spravovaná identita. <br/> Při odstranění nadřazeného prostředku se odstraní také spravovaná identita. | Nezávislý životní cyklus. <br/> Musí být explicitně odstraněn. |
| Sdílení mezi prostředky Azure | Nelze sdílet. <br/> Může být přidružena jenom k jednomu prostředku Azure. | Lze sdílet <br/> Stejná spravovaná identita přiřazená uživatelem může být přidružena k více než jednomu prostředku Azure. |
| Běžné případy použití | Úlohy, které jsou obsaženy v rámci jednoho prostředku Azure <br/> Úlohy, pro které potřebujete nezávislé identity. <br/> Například aplikace, která běží na jednom virtuálním počítači | Úlohy, které běží na více prostředků a které mohou sdílet jednu identitu. <br/> Úlohy, které potřebují předběžnou autorizaci k zabezpečenému prostředku jako součást toku zřizování. <br/> Úlohy, kde jsou prostředky často recyklovány, ale oprávnění by měla zůstat konzistentní. <br/> Například zatížení, kde více virtuálních počítačů potřebují přístup ke stejnému prostředku |

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Jak funguje spravovaná identita přiřazená systémem s virtuálním počítačem Azure

1. Azure Resource Manager přijme požadavek, aby na virtuálním počítači povolil spravovanou identitu přiřazenou systémem.

2. Azure Resource Manager pro identitu virtuálního počítače vytvoří instanční objekt v Azure AD. Instanční objekt se vytvoří v tenantovi Azure AD důvěryhodném pro dané předplatné.

3. Azure Resource Manager konfiguruje identitu na virtuálním počítači aktualizací koncového bodu identity služby Metadat instance Azure s ID klienta instancí a certifikátem.

4. Jakmile bude virtuální počítač mít identitu, s použitím informací o instančním objektu udělte virtuálnímu počítači přístup k prostředkům Azure. Pokud chcete volat Azure Resource Manager, přiřaďte k instančnímu objektu virtuálního počítače odpovídající roli pomocí řízení přístupu na základě role (RBAC) v Azure AD. Pokud chcete volat službu Key Vault, udělte kódu přístup ke konkrétnímu tajnému kódu nebo klíči ve službě Key Vault.

5. Váš kód, který běží na virtuálním počítači, může požádat o token z koncového bodu služby Metadat instance Azure, který je přístupný jenom z virtuálního počítače:`http://169.254.169.254/metadata/identity/oauth2/token`
    - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.
    - Parametr verze rozhraní API určuje verzi IMDS. Použijte api-version=2018-02-01 nebo novější.

6. Zavolá se služba Azure AD s požadavkem na přístupový token (jak je popsáno v kroku 5) s použitím ID klienta a certifikátu nakonfigurovaných v kroku 3. Azure AD vrátí přístupový token JSON Web Token (JWT).

7. Váš kód odešle přístupový token prostřednictvím volání do služby, která podporuje ověřování Azure AD.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Jak funguje spravovaná identita přiřazená uživatelem s virtuálním počítačem Azure

1. Azure Resource Manager přijme požadavek na vytvoření spravované identity přiřazené uživatelem.

2. Azure Resource Manager vytvoří pro spravovanou identitu přiřazenou uživatelem v Azure AD instanční objekt. Instanční objekt se vytvoří v tenantovi Azure AD důvěryhodném pro dané předplatné.

3. Azure Resource Manager obdrží požadavek na konfiguraci uživatelem přiřazené spravované identity na virtuálním počítači a aktualizuje koncový bod identity služby Metadat instance Azure pomocí uživatelem přiřazeného id klienta a certifikátu spravovaného objektu identity.

4. Jakmile vytvoříte spravovanou identitu přiřazenou uživatelem, použijte informace o instančním objektu a udělte identitě přístup k prostředkům Azure. Pokud chcete volat Azure Resource Manager, přiřaďte instančnímu objektu identity přiřazené uživatelem odpovídající roli. Použijte k tomu řízení přístupu na základě role (RBAC) v Azure AD. Pokud chcete volat službu Key Vault, udělte kódu přístup ke konkrétnímu tajnému kódu nebo klíči ve službě Key Vault.

   > [!Note]
   > Tento krok můžete provést také před krokem 3.

5. Váš kód, který běží na virtuálním počítači, může požádat o token z koncového bodu identity služby metadat instance Azure, který je přístupný jenom z virtuálního počítače:`http://169.254.169.254/metadata/identity/oauth2/token`
    - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.
    - Parametr ID klienta určuje identitu, pro kterou se token požaduje. Tato hodnota je nutná k jednoznačnému určení v případě, že je na jednom virtuálním počítači více identit přiřazených uživatelem.
    - Parametr verze rozhraní API určuje verzi služby Azure Instance Metadata Service. Použijte `api-version=2018-02-01` nebo novější.

6. Zavolá se služba Azure AD s požadavkem na přístupový token (jak je popsáno v kroku 5) s použitím ID klienta a certifikátu nakonfigurovaných v kroku 3. Azure AD vrátí přístupový token JSON Web Token (JWT).
7. Váš kód odešle přístupový token prostřednictvím volání do služby, která podporuje ověřování Azure AD.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Jak mám použít spravované identity prostředků Azure?

Informace o použití spravovaných identit pro přístup k různým prostředkům Azure najdete v těchto kurzech.

> [!NOTE]
> Další informace o spravovaných identitách, včetně podrobných video návodů k několika podporovaným scénářům, najdete v kurzu [Implementace spravovaných identit pro prostředky Microsoft Azure.](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing)

Informace o použití spravované identity ve virtuálním počítači s Windows:

* [Přístup ke službě Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Přístup k Azure Resource Manageru](tutorial-windows-vm-access-arm.md)
* [Přístup k Azure SQL](tutorial-windows-vm-access-sql.md)
* [Přístup ke službě Azure Storage pomocí přístupové klávesy](tutorial-vm-windows-access-storage.md)
* [Přístup ke službě Azure Storage pomocí sdílených přístupových podpisů](tutorial-windows-vm-access-storage-sas.md)
* [Přístup k prostředku mimo Azure AD pomocí služby Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Informace o použití spravované identity ve virtuálním počítači s Linuxem:

* [Přístup ke službě Azure Container Registry](../../container-registry/container-registry-authentication-managed-identity.md)
* [Přístup ke službě Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Přístup k Azure Resource Manageru](tutorial-linux-vm-access-arm.md)
* [Přístup ke službě Azure Storage pomocí přístupové klávesy](tutorial-linux-vm-access-storage.md)
* [Přístup ke službě Azure Storage pomocí sdílených přístupových podpisů](tutorial-linux-vm-access-storage-sas.md)
* [Přístup k prostředku mimo Azure AD pomocí služby Azure Key Vault](tutorial-linux-vm-access-nonaad.md)

Informace o použití spravované identity s dalšími službami Azure:

* [Azure App Service](/azure/app-service/overview-managed-identity)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Úlohy Azure Container Registry](../../container-registry/container-registry-tasks-authentication-managed-identity.md)
* [Centra událostí Azure](../../event-hubs/authenticate-managed-identity.md)
* [Azure Functions](/azure/app-service/overview-managed-identity)
* [Azure Kubernetes Service](/azure/aks/use-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Data Factory](../../data-factory/data-factory-service-identity.md)


## <a name="what-azure-services-support-the-feature"></a>Které služby Azure tuto funkci podporují?<a name="which-azure-services-support-managed-identity"></a>

Spravované identity prostředků Azure můžete použít k ověřování ve službách, které podporují ověřování Azure AD. Seznam služeb Azure, které podporují funkci spravovaných identit prostředků Azure, najdete v článku o [službách podporujících spravované identity prostředků Azure](services-support-msi.md).

## <a name="next-steps"></a>Další kroky

Pokud chcete začít používat funkci spravovaných identit prostředků Azure, projděte si následující články Rychlý start:

* [Použití spravované identity přiřazené systémem ve virtuálním počítači s Windows pro přístup k Resource Manageru](tutorial-windows-vm-access-arm.md)
* [Použití spravované identity přiřazené systémem ve virtuálním počítači s Linuxem pro přístup k Resource Manageru](tutorial-linux-vm-access-arm.md)
