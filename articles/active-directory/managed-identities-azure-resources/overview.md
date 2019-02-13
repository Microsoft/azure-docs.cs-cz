---
title: Spravované identity pro prostředky Azure
description: Přehled informací o spravovaných identitách prostředků Azure
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 10/23/2018
ms.author: priyamo
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4dc56384d550854c05a813157b32ac36f5ebfb76
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56211916"
---
# <a name="what-is-managed-identities-for-azure-resources"></a>Co jsou spravované identity prostředků Azure?

[!INCLUDE [preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Běžnou výzvou při vytváření cloudových aplikací je, jak v kódu spravovat přihlašovací údaje pro ověřování u cloudových služeb. Zajištění zabezpečení těchto přihlašovacích údajů je důležitý úkol. V ideálním případě by se přihlašovací údaje nikdy neměly nacházet na vývojářských pracovních stanicích ani se vracet se změnami do správy zdrojového kódu. Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů, tajných kódů a dalších klíčů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. 

Tento problém řeší funkce spravovaných identit prostředků Azure v Azure Active Directory (Azure AD). Tato funkce poskytuje službám Azure automaticky spravovanou identitu v Azure AD. Tuto identitu můžete použít k ověření u jakékoli služby, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.

Funkce spravovaných identit prostředků Azure je bezplatnou součástí Azure AD, pokud máte předplatné Azure. Neúčtují se za ní žádné další poplatky.

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

## <a name="terminology"></a>Terminologie

V rámci spravovaných identit pro prostředky Azure dokumentaci sady se používají následující termíny:

- **ID klienta** – jedinečný identifikátor generovaný aplikací Azure AD, která se váže k instančnímu objektu služby a aplikace během počátečního zřízení.
- **ID objektu zabezpečení** – ID objektu instanční objekt pro vaši spravovanou identitu, která se používá k udělení přístupu na základě role k prostředku Azure.
- **Azure Instance Metadata služby (IMDS)** – koncový bod REST, která je přístupná pro všechny virtuální počítače IaaS vytvořené prostřednictvím Azure Resource Manageru. Koncový bod je k dispozici na dobře známé nesměrovatelných IP adrese (169.254.169.254), který je přístupný pouze z v rámci virtuálního počítače.

## Jak funguje spravovaných identit pro prostředky Azure?<a name="how-does-it-work"></a>

Existují dva typy spravovaných identit:

- **Spravovaná identita přiřazená systémem** se povoluje přímo v instanci služby Azure. Když je tato identita povolená, Azure vytvoří identitu pro instanci v tenantovi Azure AD důvěryhodném pro předplatné instance. Po vytvoření identity se přihlašovací údaje zřídí do instance. Životní cyklus identity přiřazené systémem je přímo spojený s instancí služby Azure, pro kterou je povolená. Pokud se instance odstraní, Azure automaticky vyčistí přihlašovací údaje a identitu v Azure AD.
- **Spravovaná identita přiřazená uživatelem** se vytváří jako samostatný prostředek Azure. Prostřednictvím procesu vytvoření Azure vytvoří identitu v tenantovi Azure AD důvěryhodném pro použité předplatné. Po vytvoření identity je možné ji přiřadit k jedné nebo několika instancím služeb Azure. Životní cyklus identity přiřazené uživatelem se spravuje nezávisle na životním cyklu instancí služeb Azure, ke kterým je přiřazená.

Váš kód může spravovanou identitu použít k vyžádání přístupových tokenů pro služby, které podporují ověřování Azure AD. Azure zajistí vracení přístupových údajů, které instance služby používá.

Následující diagram ukazuje fungování identit spravovaných služeb s virtuálními počítači Azure:

![Identity spravovaných služeb a virtuální počítače Azure](media/overview/msi-vm-vmextension-imds-example.png)

### <a name="how-a-system-assigned-managed-identity-works-with-an-azure-vm"></a>Jak funguje spravovaná identita přiřazená systémem s virtuálním počítačem Azure

1. Azure Resource Manager přijme požadavek, aby na virtuálním počítači povolil spravovanou identitu přiřazenou systémem.
2. Azure Resource Manager pro identitu virtuálního počítače vytvoří instanční objekt v Azure AD. Instanční objekt se vytvoří v tenantovi Azure AD důvěryhodném pro dané předplatné.
3. Azure Resource Manager nakonfiguruje identitu na virtuálním počítači:
    1. Aktualizuje koncový bod identity služby Azure Instance Metadata Service s použitím ID klienta a certifikátu instančního objektu.
    1. Zřizuje rozšíření virtuálního počítače (plánované k vyřazení v lednu 2019) a přidává ID klienta a certifikát instančního objektu služby. (Tento krok plánujeme vyřadit.)
4. Jakmile bude virtuální počítač mít identitu, s použitím informací o instančním objektu udělte virtuálnímu počítači přístup k prostředkům Azure. Pokud chcete volat Azure Resource Manager, přiřaďte k instančnímu objektu virtuálního počítače odpovídající roli pomocí řízení přístupu na základě role (RBAC) v Azure AD. Pokud chcete volat službu Key Vault, udělte kódu přístup ke konkrétnímu tajnému kódu nebo klíči ve službě Key Vault.
5. Váš kód spuštěný na virtuálním počítači si může vyžádat token ze dvou koncových bodů přístupných pouze z daného virtuálního počítače:

    - Koncový bod identity služby Azure Instance Metadata Service (doporučeno): `http://169.254.169.254/metadata/identity/oauth2/token`
        - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.
        - Parametr verze rozhraní API určuje verzi IMDS. Použijte api-version=2018-02-01 nebo novější.
    - Koncový bod rozšíření virtuálního počítače (plánovaný k vyřazení v lednu 2019): `http://localhost:50342/oauth2/token` 
        - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.

6. Zavolá se služba Azure AD s požadavkem na přístupový token (jak je popsáno v kroku 5) s použitím ID klienta a certifikátu nakonfigurovaných v kroku 3. Azure AD vrátí přístupový token JSON Web Token (JWT).
7. Váš kód odešle přístupový token prostřednictvím volání do služby, která podporuje ověřování Azure AD.

### <a name="how-a-user-assigned-managed-identity-works-with-an-azure-vm"></a>Jak funguje spravovaná identita přiřazená uživatelem s virtuálním počítačem Azure

1. Azure Resource Manager přijme požadavek na vytvoření spravované identity přiřazené uživatelem.
2. Azure Resource Manager vytvoří pro spravovanou identitu přiřazenou uživatelem v Azure AD instanční objekt. Instanční objekt se vytvoří v tenantovi Azure AD důvěryhodném pro dané předplatné.
3. Azure Resource Manager přijme požadavek na konfiguraci spravované identity přiřazené uživatelem na virtuálním počítači:
    1. Aktualizuje koncový bod identity služby Azure Instance Metadata Service s použitím ID klienta a certifikátu instančního objektu spravované identity přiřazené uživatelem.
    1. Zajistí rozšíření virtuálního počítače a přidá ID klienta a certifikát instančního objektu spravované identity přiřazené uživatelem. (Tento krok plánujeme vyřadit.)
4. Jakmile vytvoříte spravovanou identitu přiřazenou uživatelem, použijte informace o instančním objektu a udělte identitě přístup k prostředkům Azure. Pokud chcete volat Azure Resource Manager, přiřaďte instančnímu objektu identity přiřazené uživatelem odpovídající roli. Použijte k tomu řízení přístupu na základě role (RBAC) v Azure AD. Pokud chcete volat službu Key Vault, udělte kódu přístup ke konkrétnímu tajnému kódu nebo klíči ve službě Key Vault.

   > [!Note]
   > Tento krok můžete provést také před krokem 3.

5. Váš kód spuštěný na virtuálním počítači si může vyžádat token ze dvou koncových bodů přístupných pouze z daného virtuálního počítače:

    - Koncový bod identity služby Azure Instance Metadata Service (doporučeno): `http://169.254.169.254/metadata/identity/oauth2/token`
        - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.
        - Parametr ID klienta určuje identitu, pro kterou se token požaduje. Tato hodnota je nutná k jednoznačnému určení v případě, že je na jednom virtuálním počítači více identit přiřazených uživatelem.
        - Parametr verze rozhraní API určuje verzi služby Azure Instance Metadata Service. Použijte `api-version=2018-02-01` nebo novější.

    - Koncový bod rozšíření virtuálního počítače (plánovaný k vyřazení v lednu 2019): `http://localhost:50342/oauth2/token`
        - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.
        - Parametr ID klienta určuje identitu, pro kterou se token požaduje. Tato hodnota je nutná k jednoznačnému určení v případě, že je na jednom virtuálním počítači více identit přiřazených uživatelem.
6. Zavolá se služba Azure AD s požadavkem na přístupový token (jak je popsáno v kroku 5) s použitím ID klienta a certifikátu nakonfigurovaných v kroku 3. Azure AD vrátí přístupový token JSON Web Token (JWT).
7. Váš kód odešle přístupový token prostřednictvím volání do služby, která podporuje ověřování Azure AD.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Jak mám použít spravované identity prostředků Azure?

Informace o použití spravovaných identit pro přístup k různým prostředkům Azure najdete v těchto kurzech.

> [!NOTE]
> Podívejte se [implementace identit spravovaných prostředků Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing) kurzu podrobné informace o spravovaných identit, včetně videa návody podporovaných scénářů.

Informace o použití spravované identity ve virtuálním počítači s Windows:

* [Přístup ke službě Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Přístup k Azure Resource Manageru](tutorial-windows-vm-access-arm.md)
* [Přístup k Azure SQL](tutorial-windows-vm-access-sql.md)
* [Přístup ke službě Azure Storage pomocí přístupové klávesy](tutorial-windows-vm-access-storage.md)
* [Přístup ke službě Azure Storage pomocí sdílených přístupových podpisů](tutorial-windows-vm-access-storage-sas.md)
* [Přístup k prostředku mimo Azure AD pomocí služby Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Informace o použití spravované identity ve virtuálním počítači s Linuxem:

* [Přístup ke službě Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Přístup k Azure Resource Manageru](tutorial-linux-vm-access-arm.md)
* [Přístup ke službě Azure Storage pomocí přístupové klávesy](tutorial-linux-vm-access-storage.md)
* [Přístup ke službě Azure Storage pomocí sdílených přístupových podpisů](tutorial-linux-vm-access-storage-sas.md)
* [Přístup k prostředku mimo Azure AD pomocí služby Azure Key Vault](tutorial-linux-vm-access-nonaad.md)

Informace o použití spravované identity s dalšími službami Azure:

* [Azure App Service](/azure/app-service/overview-managed-identity)
* [Azure Functions](/azure/app-service/overview-managed-identity)
* [Azure Logic Apps](/azure/logic-apps/create-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)
* [Azure Container Instances](../../container-instances/container-instances-managed-identity.md)

## Které služby Azure tuto funkci podporují?<a name="which-azure-services-support-managed-identity"></a>

Spravované identity prostředků Azure můžete použít k ověřování ve službách, které podporují ověřování Azure AD. Seznam služeb Azure, které podporují funkci spravovaných identit prostředků Azure, najdete v článku o [službách podporujících spravované identity prostředků Azure](services-support-msi.md).

## <a name="next-steps"></a>Další postup

Pokud chcete začít používat funkci spravovaných identit prostředků Azure, projděte si následující články Rychlý start:

* [Použití spravované identity přiřazené systémem ve virtuálním počítači s Windows pro přístup k Resource Manageru](tutorial-windows-vm-access-arm.md)
* [Použití spravované identity přiřazené systémem ve virtuálním počítači s Linuxem pro přístup k Resource Manageru](tutorial-linux-vm-access-arm.md)
