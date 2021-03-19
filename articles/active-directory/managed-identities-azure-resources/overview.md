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
ms.date: 10/06/2020
ms.author: barclayn
ms.collection: M365-identity-device-management
ms.openlocfilehash: 728ca38cc3ef3bf989a75d757c69f7ca1993d82d
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "91803108"
---
# <a name="what-are-managed-identities-for-azure-resources"></a>Jaké jsou spravované identity prostředků Azure?

Běžnou výzvou pro vývojáře je Správa tajných klíčů a přihlašovacích údajů pro zabezpečení komunikace mezi různými službami. V Azure spravované identity eliminují nutnost vývojářů spravovat přihlašovací údaje tím, že poskytuje identitu prostředku Azure ve službě Azure AD a použije ho k získání tokenů Azure Active Directory (Azure AD). Pomůže vám taky získat přístup k [Azure Key Vault](../../key-vault/general/overview.md) , kde můžou vývojáři zabezpečeným způsobem ukládat přihlašovací údaje. Spravované identity prostředků Azure tento problém řeší tím, že poskytují služby Azure s automaticky spravovanou identitou ve službě Azure AD.

K čemu se dá použít spravovaná identita?

   > [!VIDEO https://www.youtube.com/embed/5lqayO_oeEo]

Zde jsou některé výhody použití spravovaných identit:

- Nemusíte spravovat přihlašovací údaje. Přihlašovací údaje nejsou k dispozici ani pro vás.
- Spravované identity můžete použít k ověření pro libovolnou službu Azure, která podporuje ověřování Azure AD, včetně Azure Key Vault.
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

>[!IMPORTANT]
>Bez ohledu na typ identity, který jste zvolili spravovanou identitu, je instanční objekt speciálního typu, který se dá použít jenom s prostředky Azure. Po odstranění spravované identity se odpovídající objekt služby automaticky odebere.

## <a name="how-can-i-use-managed-identities-for-azure-resources"></a>Jak mám použít spravované identity prostředků Azure?

![Některé příklady, jak může vývojář použít spravované identity k získání přístupu k prostředkům z kódu bez správy ověřovacích informací](media/overview/azure-managed-identities-examples.png)

## <a name="what-azure-services-support-the-feature"></a>Které služby Azure tuto funkci podporují?<a name="which-azure-services-support-managed-identity"></a>

Spravované identity prostředků Azure můžete použít k ověřování ve službách, které podporují ověřování Azure AD. Seznam služeb Azure, které podporují funkci spravovaných identit prostředků Azure, najdete v článku o [službách podporujících spravované identity prostředků Azure](./services-support-managed-identities.md).

## <a name="next-steps"></a>Další kroky

* [Použití spravované identity přiřazené systémem ve virtuálním počítači s Windows pro přístup k Resource Manageru](tutorial-windows-vm-access-arm.md)
* [Použití spravované identity přiřazené systémem ve virtuálním počítači s Linuxem pro přístup k Resource Manageru](tutorial-linux-vm-access-arm.md)
* [Použití spravovaných identit pro App Service a Azure Functions](../../app-service/overview-managed-identity.md)
* [Použití spravovaných identit se službou Azure Container Instances](../../container-instances/container-instances-managed-identity.md)
* [Implementují se spravované identity pro prostředky Microsoft Azure](https://www.pluralsight.com/courses/microsoft-azure-resources-managed-identities-implementing).