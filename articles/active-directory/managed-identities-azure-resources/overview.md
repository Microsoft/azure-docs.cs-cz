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
ms.date: 06/18/2020
ms.author: markvi
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3557bab44e1a4af5fdcbda5f8643018952e4e54e
ms.sourcegitcommit: 51718f41d36192b9722e278237617f01da1b9b4e
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/19/2020
ms.locfileid: "85099525"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Jaké jsou spravované identity prostředků Azure?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Běžnou výzvou při vytváření cloudových aplikací je, jak v kódu spravovat přihlašovací údaje pro ověřování u cloudových služeb. Zajištění zabezpečení těchto přihlašovacích údajů je důležitý úkol. V ideálním případě by se přihlašovací údaje nikdy neměly nacházet na vývojářských pracovních stanicích ani se vracet se změnami do správy zdrojového kódu. Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů, tajných kódů a dalších klíčů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst.

Tento problém řeší funkce spravovaných identit prostředků Azure v Azure Active Directory (Azure AD). Tato funkce poskytuje službám Azure automaticky spravovanou identitu v Azure AD. Tuto identitu můžete použít k ověření u jakékoli služby, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.

Funkce spravovaných identit prostředků Azure je bezplatnou součástí Azure AD, pokud máte předplatné Azure. Neúčtují se za ní žádné další poplatky.

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

## <a name="terminology"></a>Terminologie

V sadě dokumentace spravovaných identit pro prostředky Azure se používají následující výrazy:

- **ID klienta** – jedinečný identifikátor generovaný službou Azure AD, který je svázán s aplikací a instančním objektem během počátečního zřizování.
- **ID objektu zabezpečení** – ID objektu instančního objektu pro spravovanou identitu, která se používá pro udělení přístupu na základě role k prostředku Azure.
- **Azure instance metadata Service (IMDS)** – koncový bod REST přístupný všem virtuálním počítačům s IaaS vytvořeným prostřednictvím Azure Resource Manager. Koncový bod je k dispozici na dobře známé IP adrese bez směrování (169.254.169.254), ke kterým se dá získat přístup jenom z virtuálního počítače.

## <a name="managed-identity-types"></a>Spravované typy identity

Existují dva typy spravovaných identit:

- **Spravovaná identita přiřazená systémem** se povoluje přímo v instanci služby Azure. Když je tato identita povolená, Azure vytvoří identitu pro instanci v tenantovi Azure AD důvěryhodném pro předplatné instance. Po vytvoření identity se přihlašovací údaje zřídí do instance. Životní cyklus identity přiřazené systémem je přímo vázaný na instanci služby Azure, na které je povolený. Pokud se instance odstraní, Azure automaticky vyčistí přihlašovací údaje a identitu v Azure AD.
- **Spravovaná identita přiřazená uživatelem** se vytváří jako samostatný prostředek Azure. Prostřednictvím procesu vytvoření Azure vytvoří identitu v tenantovi Azure AD důvěryhodném pro použité předplatné. Po vytvoření identity je možné ji přiřadit k jedné nebo několika instancím služeb Azure. Životní cyklus uživatelsky přiřazené identity se spravuje nezávisle na životním cyklu instancí služby Azure, ke kterým je přiřazený.

Spravované identity jsou interně instančními objekty speciálního typu, které jsou uzamčené jenom pro použití s prostředky Azure. Po odstranění spravované identity se odpovídající objekt služby automaticky odebere.
I když je vytvořena uživatelem přiřazená identita nebo identita přiřazená systémem, poskytovatel spravovaných prostředků identity (MSRP) vystaví certifikát pro danou identitu interně. 

Váš kód může spravovanou identitu použít k vyžádání přístupových tokenů pro služby, které podporují ověřování Azure AD. Azure zajistí vracení přístupových údajů, které instance služby používá. 

## <a name="credential-rotation"></a>Rotace přihlašovacích údajů
Rotace přihlašovacích údajů se řídí poskytovatelem prostředků, který je hostitelem prostředku Azure. K výchozímu rotaci přihlašovacích údajů dojde každých 46 dní. Je až poskytovatele prostředků pro volání nových přihlašovacích údajů, takže poskytovatel prostředků může počkat déle než 46 dní.

Následující diagram ukazuje fungování identit spravovaných služeb s virtuálními počítači Azure:

![Identity spravovaných služeb a virtuální počítače Azure](media/overview/data-flow.png)

|  Vlastnost    | Spravovaná identita přiřazená systémem | Spravovaná identita přiřazená uživatelem |
|------|----------------------------------|--------------------------------|
| Vytvoření |  Vytvořené jako součást prostředku Azure (například virtuální počítač Azure nebo Azure App Service) | Vytvoří se jako samostatný prostředek Azure. |
| Životní cyklus | Sdílený životní cyklus s prostředkem Azure, pomocí kterého se vytvořila spravovaná identita. <br/> Při odstranění nadřazeného prostředku se odstraní také spravovaná identita. | Nezávislý životní cyklus. <br/> Je nutné explicitně odstranit. |
| Sdílení napříč prostředky Azure | Nelze sdílet. <br/> Dá se přidružit jenom k jednomu prostředku Azure. | Může být sdíleno <br/> Stejná uživatelem přiřazená spravovaná identita může být přidružená k více než jednomu prostředku Azure. |
| Běžné případy použití | Úlohy, které jsou obsaženy v rámci jednoho prostředku Azure <br/> Úlohy, pro které potřebujete nezávislé identity. <br/> Například aplikace, která běží na jednom virtuálním počítači | Úlohy, které běží na několika prostředcích a které můžou sdílet jedinou identitu. <br/> Úlohy, které vyžadují předběžnou autorizaci zabezpečeného prostředku jako součást toku zřizování. <br/> Úlohy, kde se prostředky recyklují často, ale oprávnění by měla zůstat konzistentní. <br/> Například zatížení, ve kterém více virtuálních počítačů potřebuje přístup ke stejnému prostředku |

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Jak funguje spravovaná identita přiřazená systémem s virtuálním počítačem Azure

1. Azure Resource Manager přijme požadavek, aby na virtuálním počítači povolil spravovanou identitu přiřazenou systémem.

2. Azure Resource Manager pro identitu virtuálního počítače vytvoří instanční objekt v Azure AD. Instanční objekt se vytvoří v tenantovi Azure AD důvěryhodném pro dané předplatné.

3. Azure Resource Manager konfiguruje identitu na virtuálním počítači tím, že aktualizuje koncový bod Azure Instance Metadata Service identity s ID a certifikátem klienta instančního objektu.

4. Jakmile bude virtuální počítač mít identitu, s použitím informací o instančním objektu udělte virtuálnímu počítači přístup k prostředkům Azure. Pokud chcete volat Azure Resource Manager, přiřaďte k instančnímu objektu virtuálního počítače odpovídající roli pomocí řízení přístupu na základě role (RBAC) v Azure AD. Pokud chcete volat službu Key Vault, udělte kódu přístup ke konkrétnímu tajnému kódu nebo klíči ve službě Key Vault.

5. Váš kód, který běží na virtuálním počítači, může požádat o token z koncového bodu služby metadat instance Azure, který je přístupný jenom z virtuálního počítače:`http://169.254.169.254/metadata/identity/oauth2/token`
    - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.
    - Parametr verze rozhraní API určuje verzi IMDS. Použijte api-version=2018-02-01 nebo novější.

6. Zavolá se služba Azure AD s požadavkem na přístupový token (jak je popsáno v kroku 5) s použitím ID klienta a certifikátu nakonfigurovaných v kroku 3. Azure AD vrátí přístupový token JSON Web Token (JWT).

7. Váš kód odešle přístupový token prostřednictvím volání do služby, která podporuje ověřování Azure AD.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Jak funguje spravovaná identita přiřazená uživatelem s virtuálním počítačem Azure

1. Azure Resource Manager přijme požadavek na vytvoření spravované identity přiřazené uživatelem.

2. Azure Resource Manager vytvoří pro spravovanou identitu přiřazenou uživatelem v Azure AD instanční objekt. Instanční objekt se vytvoří v tenantovi Azure AD důvěryhodném pro dané předplatné.

3. Azure Resource Manager obdrží požadavek na konfiguraci spravované identity přiřazené uživatelem na virtuálním počítači a aktualizuje koncový bod Azure Instance Metadata Service identity s uživatelem přiřazeným ID a certifikátem klienta instančního objektu spravované identity.

4. Jakmile vytvoříte spravovanou identitu přiřazenou uživatelem, použijte informace o instančním objektu a udělte identitě přístup k prostředkům Azure. Pokud chcete volat Azure Resource Manager, přiřaďte instančnímu objektu identity přiřazené uživatelem odpovídající roli. Použijte k tomu řízení přístupu na základě role (RBAC) v Azure AD. Pokud chcete volat službu Key Vault, udělte kódu přístup ke konkrétnímu tajnému kódu nebo klíči ve službě Key Vault.

   > [!Note]
   > Tento krok můžete provést také před krokem 3.

5. Váš kód, který běží na virtuálním počítači, může požádat o token z koncového bodu Azure Instance Metadata Service identity přístupný jenom z virtuálního počítače:`http://169.254.169.254/metadata/identity/oauth2/token`
    - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.
    - Parametr ID klienta určuje identitu, pro kterou se token požaduje. Tato hodnota je nutná k jednoznačnému určení v případě, že je na jednom virtuálním počítači více identit přiřazených uživatelem.
    - Parametr verze rozhraní API určuje verzi služby Azure Instance Metadata Service. Použijte `api-version=2018-02-01` nebo novější.

6. Zavolá se služba Azure AD s požadavkem na přístupový token (jak je popsáno v kroku 5) s použitím ID klienta a certifikátu nakonfigurovaných v kroku 3. Azure AD vrátí přístupový token JSON Web Token (JWT).
7. Váš kód odešle přístupový token prostřednictvím volání do služby, která podporuje ověřování Azure AD.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Jak mám použít spravované identity prostředků Azure?

Informace o použití spravovaných identit pro přístup k různým prostředkům Azure najdete v těchto kurzech.

> [!NOTE]
> Další informace o spravovaných identitách najdete v kurzu [implementace spravovaných identit pro Microsoft Azure zdrojů](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) , včetně podrobných návodů k videu několika podporovaných scénářů.

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
* [Azure Event Hubs](../../event-hubs/authenticate-managed-identity.md)
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