---
title: Identita spravované služby pro prostředky Azure
description: Přehled funkce Identita spravované služby pro prostředky Azure.
services: active-directory
documentationcenter: ''
author: daveba
manager: mtillman
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.component: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 03/28/2018
ms.author: daveba
ms.openlocfilehash: 1edd6f846d539b1f263877a5e0af107148513c6e
ms.sourcegitcommit: d4c076beea3a8d9e09c9d2f4a63428dc72dd9806
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/01/2018
ms.locfileid: "39398381"
---
# <a name="what-is-managed-service-identity-for-azure-resources"></a>Co je Identita spravované služby pro prostředky Azure?

[!INCLUDE[preview-notice](../../../includes/active-directory-msi-preview-notice.md)]

Běžnou výzvou při vytváření cloudových aplikací je, jak v kódu spravovat přihlašovací údaje pro ověřování u cloudových služeb. Zajištění zabezpečení těchto přihlašovacích údajů je důležitý úkol. V ideálním případě by se přihlašovací údaje nikdy neměly nacházet na vývojářských pracovních stanicích ani se vracet se změnami do správy zdrojového kódu. Azure Key Vault nabízí možnost bezpečného ukládání přihlašovacích údajů, tajných kódů a dalších klíčů, ale váš kód se musí ověřit ve službě Key Vault, aby je mohl načíst. 

Tento problém řeší funkce Identita spravované služby v Azure Active Directory (Azure AD). Tato funkce poskytuje službám Azure automaticky spravovanou identitu v Azure AD. Tuto identitu můžete použít k ověření u jakékoli služby, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje.

Funkce Identita spravované služby je pro předplatná Azure zdarma v rámci Azure AD. Neúčtují se za ní žádné další poplatky.

## Jak tato funkce funguje?<a name="how-does-it-work"></a>

Existují dva typy spravovaných identit:

- **Identita přiřazená systémem** se povoluje přímo v instanci služby Azure. Když je tato identita povolená, Azure vytvoří identitu pro instanci v tenantovi Azure AD důvěryhodném pro předplatné instance. Po vytvoření identity se přihlašovací údaje zřídí do instance. Životní cyklus identity přiřazené systémem je přímo svázaný s instancí služby Azure, pro kterou je povolená. Pokud se instance odstraní, Azure automaticky vyčistí přihlašovací údaje a identitu v Azure AD.
- **Identita přiřazená uživatelem** se vytváří jako samostatný prostředek Azure. Prostřednictvím procesu vytvoření Azure vytvoří identitu v tenantovi Azure AD důvěryhodném pro použité předplatné. Po vytvoření identity je možné ji přiřadit k jedné nebo několika instancím služeb Azure. Životní cyklus identity přiřazené uživatelem se spravuje nezávisle na životním cyklu instancí služeb Azure, ke kterým je přiřazená.

Váš kód může k vyžádání přístupových tokenů pro služby podporující ověřování Azure AD použít identitu spravované služby. Azure zajistí vracení přístupových údajů, které instance služby používá.

Následující diagram ukazuje fungování identit spravovaných služeb s virtuálními počítači Azure:

![Identity spravovaných služeb a virtuální počítače Azure](media/overview/msi-vm-vmextension-imds-example.png)

### <a name="how-a-system-assigned-identity-works-with-an-azure-vm"></a>Jak s virtuálním počítačem Azure funguje identita přiřazená systémem

1. Azure Resource Manager obdrží požadavek na povolení identity přiřazené systémem na virtuálním počítači.
2. Azure Resource Manager pro identitu virtuálního počítače vytvoří instanční objekt v Azure AD. Instanční objekt se vytvoří v tenantovi Azure AD důvěryhodném pro dané předplatné.
3. Azure Resource Manager nakonfiguruje identitu na virtuálním počítači:
    1. Aktualizuje koncový bod identity služby Azure Instance Metadata Service s použitím ID klienta a certifikátu instančního objektu.
    1. Zřídí rozšíření virtuálního počítače a přidá ID klienta a certifikát instančního objektu. (Tento krok plánujeme vyřadit.)
4. Jakmile bude virtuální počítač mít identitu, s použitím informací o instančním objektu udělte virtuálnímu počítači přístup k prostředkům Azure. Pokud chcete volat Azure Resource Manager, přiřaďte k instančnímu objektu virtuálního počítače odpovídající roli pomocí řízení přístupu na základě role (RBAC) v Azure AD. Pokud chcete volat službu Key Vault, udělte kódu přístup ke konkrétnímu tajnému kódu nebo klíči ve službě Key Vault.
5. Váš kód spuštěný na virtuálním počítači si může vyžádat token ze dvou koncových bodů přístupných pouze z daného virtuálního počítače:

    - Koncový bod identity služby Azure Instance Metadata Service (doporučeno): `http://169.254.169.254/metadata/identity/oauth2/token`
        - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.
        - Parametr verze rozhraní API určuje verzi IMDS. Použijte api-version=2018-02-01 nebo novější.
    - Koncový bod rozšíření virtuálního počítače (plánuje se jeho vyřazení): `http://localhost:50342/oauth2/token` 
        - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.

6. Zavolá se služba Azure AD s požadavkem na přístupový token (jak je popsáno v kroku 5) s použitím ID klienta a certifikátu nakonfigurovaných v kroku 3. Azure AD vrátí přístupový token JSON Web Token (JWT).
7. Váš kód odešle přístupový token prostřednictvím volání do služby, která podporuje ověřování Azure AD.

### <a name="how-a-user-assigned-identity-works-with-an-azure-vm"></a>Jak s virtuálním počítačem Azure funguje identita přiřazená uživatelem

1. Azure Resource Manager obdrží požadavek na vytvoření identity přiřazené uživatelem.
2. Azure Resource Manager pro identitu přiřazenou uživatelem vytvoří instanční objekt v Azure AD. Instanční objekt se vytvoří v tenantovi Azure AD důvěryhodném pro dané předplatné.
3. Azure Resource Manager obdrží požadavek na konfiguraci identity přiřazené uživatelem na virtuálním počítači:
    1. Aktualizuje koncový bod identity služby Azure Instance Metadata Service s použitím ID klienta a certifikátu instančního objektu identity přiřazené uživatelem.
    1. Zřídí rozšíření virtuálního počítače a přidá ID klienta a certifikát instančního objektu identity přiřazené uživatelem. (Tento krok plánujeme vyřadit.)
4. Po vytvoření identity přiřazené uživatelem s použitím informací o instančním objektu udělte identitě přístup k prostředkům Azure. Pokud chcete volat Azure Resource Manager, přiřaďte k instančnímu objektu identity přiřazené uživatelem odpovídající roli pomocí řízení přístupu na základě role (RBAC) v Azure AD. Pokud chcete volat službu Key Vault, udělte kódu přístup ke konkrétnímu tajnému kódu nebo klíči ve službě Key Vault.

   > [!Note]
   > Tento krok můžete provést také před krokem 3.

5. Váš kód spuštěný na virtuálním počítači si může vyžádat token ze dvou koncových bodů přístupných pouze z daného virtuálního počítače:

    - Koncový bod identity služby Azure Instance Metadata Service (doporučeno): `http://169.254.169.254/metadata/identity/oauth2/token`
        - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.
        - Parametr ID klienta určuje identitu, pro kterou se token požaduje. Tato hodnota je potřeba k odstranění nejednoznačností v případě, že je na jednom virtuálním počítači více než jedna identita přiřazená uživatelem.
        - Parametr verze rozhraní API určuje verzi služby Azure Instance Metadata Service. Použijte `api-version=2018-02-01` nebo novější.

    - Koncový bod rozšíření virtuálního počítače (plánuje se jeho vyřazení): `http://localhost:50342/oauth2/token`
        - Parametr resource (prostředek) určuje službu, do které se token odešle. K ověření v Azure Resource Manageru použijte `resource=https://management.azure.com/`.
        - Parametr ID klienta určuje identitu, pro kterou se token požaduje. Tato hodnota je potřeba k odstranění nejednoznačností v případě, že je na jednom virtuálním počítači více než jedna identita přiřazená uživatelem.
6. Zavolá se služba Azure AD s požadavkem na přístupový token (jak je popsáno v kroku 5) s použitím ID klienta a certifikátu nakonfigurovaných v kroku 3. Azure AD vrátí přístupový token JSON Web Token (JWT).
7. Váš kód odešle přístupový token prostřednictvím volání do služby, která podporuje ověřování Azure AD.

## <a name="how-can-i-use-managed-service-identities"></a>Jak se používají identity spravovaných služeb?

Informace o použití identit spravovaných služeb k přístupu k různým prostředkům Azure najdete v těchto kurzech.

Informace o použití identity spravované služby s virtuálním počítačem s Windows:

* [Přístup ke službě Azure Data Lake Store](tutorial-windows-vm-access-datalake.md)
* [Přístup k Azure Resource Manageru](tutorial-windows-vm-access-arm.md)
* [Přístup k Azure SQL](tutorial-windows-vm-access-sql.md)
* [Přístup ke službě Azure Storage pomocí přístupové klávesy](tutorial-windows-vm-access-storage.md)
* [Přístup ke službě Azure Storage pomocí sdílených přístupových podpisů](tutorial-windows-vm-access-storage-sas.md)
* [Přístup k prostředku mimo Azure AD pomocí služby Azure Key Vault](tutorial-windows-vm-access-nonaad.md)

Informace o použití identity spravované služby s virtuálním počítačem s Linuxem:

* [Přístup ke službě Azure Data Lake Store](tutorial-linux-vm-access-datalake.md)
* [Přístup k Azure Resource Manageru](tutorial-linux-vm-access-arm.md)
* [Přístup ke službě Azure Storage pomocí přístupové klávesy](tutorial-linux-vm-access-storage.md)
* [Přístup ke službě Azure Storage pomocí sdílených přístupových podpisů](tutorial-linux-vm-access-storage-sas.md)
* [Přístup k prostředku mimo Azure AD pomocí služby Azure Key Vault](tutorial-linux-vm-access-nonaad.md)

Informace o použití identity spravované služby s dalšími službami Azure:

* [Azure App Service](/azure/app-service/app-service-managed-service-identity)
* [Azure Functions](/azure/app-service/app-service-managed-service-identity)
* [Azure Service Bus](../../service-bus-messaging/service-bus-managed-service-identity.md)
* [Azure Event Hubs](../../event-hubs/event-hubs-managed-service-identity.md)
* [Azure API Management](../../api-management/api-management-howto-use-managed-service-identity.md)

## Které služby Azure tuto funkci podporují?<a name="which-azure-services-support-managed-service-identity"></a>

Identity spravovaných služeb je možné použít k ověřování ve službách, které podporují ověřování Azure AD. Seznam služeb Azure podporujících funkci Identita spravované služby najdete v tématu [Služby, které podporují Identitu spravované služby](services-support-msi.md).

## <a name="next-steps"></a>Další kroky

Následující rychlé starty vám pomůžou začít s funkcí Identita spravované služby:

* [Použití identity spravované služby virtuálního počítače s Windows k přístupu k Resource Manageru](tutorial-windows-vm-access-arm.md)
* [Použití identity spravované služby virtuálního počítače s Linuxem k přístupu k Resource Manageru](tutorial-linux-vm-access-arm.md)
