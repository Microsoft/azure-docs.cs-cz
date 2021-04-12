---
title: Spravované identity pro prostředky Azure
description: Přehled informací o spravovaných identitách prostředků Azure
services: active-directory
documentationcenter: ''
author: barclayn
manager: daveba
editor: ''
ms.assetid: 0232041d-b8f5-4bd2-8d11-27999ad69370
ms.service: active-directory
ms.subservice: msi
ms.devlang: ''
ms.topic: overview
ms.custom: mvc
ms.date: 04/07/2021
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7fabb8bbdb42212dffd3781f4e98204abb518e6b
ms.sourcegitcommit: 5f482220a6d994c33c7920f4e4d67d2a450f7f08
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2021
ms.locfileid: "107105574"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Jaké jsou spravované identity prostředků Azure?

Běžnou výzvou pro vývojáře je Správa tajných klíčů a přihlašovacích údajů sloužících k zabezpečení komunikace mezi různými součástmi, které tvoří řešení. Spravované identity eliminují potřebu vývojářů spravovat přihlašovací údaje. Spravované identity poskytují identitu pro aplikace, které se mají používat při připojování k prostředkům, které podporují ověřování Azure Active Directory (Azure AD). Aplikace můžou pomocí spravované identity získat tokeny Azure AD. Aplikace může například používat spravovanou identitu pro přístup k prostředkům, jako je [Azure Key Vault](../../key-vault/general/overview.md) , kde můžou vývojáři ukládat přihlašovací údaje zabezpečeným způsobem nebo získat přístup k účtům úložiště.

K čemu se dá použít spravovaná identita?</br>

> [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

Zde jsou některé výhody použití spravovaných identit:

- Nemusíte spravovat přihlašovací údaje. Přihlašovací údaje nejsou k dispozici ani pro vás.
- Spravované identity můžete použít k ověření pro libovolný prostředek, který podporuje [ověřování Azure Active Directory](../authentication/overview-authentication.md) , včetně vašich vlastních aplikací.
- Spravované identity lze použít bez jakýchkoli dalších nákladů.

> [!NOTE]
> Spravované identity prostředků Azure jsou novým názvem služby, která se dříve jmenovala Identita spravované služby (MSI).

## <a name="managed-identity-types"></a>Spravované typy identity

Existují dva typy spravovaných identit:

- **Přiřazeno systémem** Některé služby Azure umožňují povolit spravovanou identitu přímo v instanci služby. Když povolíte spravovanou identitu přiřazenou systémem, vytvoří se ve službě Azure AD identita, která je vázaná na životní cyklus této instance služby. Takže když se prostředek odstraní, Azure pro vás automaticky odstraní identitu. Podle návrhu jenom tento prostředek Azure může tuto identitu použít k vyžádání tokenů z Azure AD.
- **Přiřazeno uživatelem** Můžete také vytvořit spravovanou identitu jako samostatný prostředek Azure. Můžete [vytvořit spravovanou identitu přiřazenou uživatelem](how-to-manage-ua-identity-portal.md) a přiřadit ji k jedné nebo více instancím služby Azure. V případě uživatelem přiřazených spravovaných identit se identita spravuje odděleně od prostředků, které ji používají. </br></br>

> [!VIDEO https://www.youtube.com/embed/OzqpxeD3fG0]

V následující tabulce jsou uvedeny rozdíly mezi dvěma typy spravovaných identit.

|  Vlastnost    | Spravovaná identita přiřazená systémem | Spravovaná identita přiřazená uživatelem |
|------|----------------------------------|--------------------------------|
| Vytvoření |  Vytvořené jako součást prostředku Azure (například virtuální počítač Azure nebo Azure App Service) | Vytvoří se jako samostatný prostředek Azure. |
| Životní cyklus | Sdílený životní cyklus s prostředkem Azure, pomocí kterého se vytvořila spravovaná identita. <br/> Při odstranění nadřazeného prostředku se odstraní také spravovaná identita. | Nezávislý životní cyklus. <br/> Je nutné explicitně odstranit. |
| Sdílení napříč prostředky Azure | Nelze sdílet. <br/> Dá se přidružit jenom k jednomu prostředku Azure. | Může být sdíleno <br/> Stejná uživatelem přiřazená spravovaná identita může být přidružená k více než jednomu prostředku Azure. |
| Běžné případy použití | Úlohy, které jsou obsaženy v rámci jednoho prostředku Azure <br/> Úlohy, pro které potřebujete nezávislé identity. <br/> Například aplikace, která běží na jednom virtuálním počítači | Úlohy, které běží na několika prostředcích a které můžou sdílet jedinou identitu. <br/> Úlohy, které vyžadují předběžnou autorizaci zabezpečeného prostředku jako součást toku zřizování. <br/> Úlohy, kde se prostředky recyklují často, ale oprávnění by měla zůstat konzistentní. <br/> Například zatížení, ve kterém více virtuálních počítačů potřebuje přístup ke stejnému prostředku |

> [!IMPORTANT]
> Bez ohledu na typ identity, který jste zvolili spravovanou identitu, je instanční objekt speciálního typu, který se dá použít jenom s prostředky Azure. Po odstranění spravované identity se odpovídající objekt služby automaticky odebere.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Jak mám použít spravované identity prostředků Azure?

![Některé příklady, jak může vývojář použít spravované identity k získání přístupu k prostředkům z kódu bez správy ověřovacích informací](media/overview/when-use-managed-identities.png)

## <a name="what-azure-services-support-the-feature"></a>Které služby Azure tuto funkci podporují?<a name="which-azure-services-support-managed-identity"></a>

Spravované identity prostředků Azure můžete použít k ověřování ve službách, které podporují ověřování Azure AD. Seznam služeb Azure, které podporují funkci spravovaných identit prostředků Azure, najdete v článku o [službách podporujících spravované identity prostředků Azure](./services-support-managed-identities.md).

## <a name="which-operations-can-i-perform-using-managed-identities"></a>Které operace je možné provést pomocí spravovaných identit?

Prostředky, které podporují spravované identity přiřazené systémem, umožňují:

- Povolte nebo zakažte spravované identity na úrovni prostředků.
- K [udělení oprávnění](howto-assign-access-portal.md)použijte role RBAC.
- Zobrazit operace vytvoření, čtení, aktualizace a odstranění (CRUD) v [protokolech aktivit Azure](../../azure-resource-manager/management/view-activity-logs.md)
- Zobrazit přihlašovací aktivitu v [protokolech přihlášení](../reports-monitoring/concept-sign-ins.md)služby Azure AD.

Pokud místo toho zvolíte spravovanou identitu přiřazenou uživatelem:

- Můžete [vytvářet, číst, aktualizovat a odstraňovat](how-to-manage-ua-identity-portal.md) identity.
- Přiřazení rolí RBAC můžete použít k [udělení oprávnění](howto-assign-access-portal.md).
- Spravované identity přiřazené uživateli lze použít ve více než jednom prostředku.
- Operace CRUD jsou k dispozici ke kontrole v [protokolech aktivit Azure](../../azure-resource-manager/management/view-activity-logs.md).
- Zobrazit přihlašovací aktivitu v [protokolech přihlášení](../reports-monitoring/concept-sign-ins.md)služby Azure AD.

Operace se spravovanými identitami se můžou provádět pomocí šablony Azure Resource Manager (ARM), webu Azure Portal, Azure CLI, PowerShellu a rozhraní REST API.

## <a name="next-steps"></a>Další kroky

* [Použití spravované identity přiřazené systémem ve virtuálním počítači s Windows pro přístup k Resource Manageru](tutorial-windows-vm-access-arm.md)
* [Použití spravované identity přiřazené systémem ve virtuálním počítači s Linuxem pro přístup k Resource Manageru](tutorial-linux-vm-access-arm.md)
* [Použití spravovaných identit pro App Service a Azure Functions](../../app-service/overview-managed-identity.md)
* [Použití spravovaných identit se službou Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Implementují se spravované identity pro prostředky Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing).
