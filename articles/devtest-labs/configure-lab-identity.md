---
title: Konfigurace identity testovacího prostředí v Azure DevTest Labs
description: Naučte se konfigurovat identitu testovacího prostředí ve službě Azure DevTest.
ms.topic: article
ms.date: 08/20/2020
ms.openlocfilehash: a652eb5751f9b723911a1c1baaaaf9860febc5b6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88719643"
---
# <a name="configure-a-lab-identity"></a>Konfigurace identity testovacího prostředí

Běžnou výzvou při vytváření cloudových aplikací je, jak v kódu spravovat přihlašovací údaje pro ověřování u cloudových služeb. Zajištění zabezpečení těchto přihlašovacích údajů je důležitý úkol. V ideálním případě by se přihlašovací údaje nikdy neměly nacházet na vývojářských pracovních stanicích ani se vracet se změnami do správy zdrojového kódu. Azure Key Vault poskytuje způsob bezpečného ukládání přihlašovacích údajů, tajných kódů a dalších klíčů, ale váš kód se musí ověřit, aby se Key Vault mohl načíst. 

Tento problém řeší funkce spravované identity pro prostředky Azure v Azure Active Directory (Azure AD). Tato funkce poskytuje službám Azure automaticky spravovanou identitu v Azure AD. Tuto identitu můžete použít k ověření u jakékoli služby, která podporuje ověřování Azure AD, včetně služby Key Vault, aniž byste ve vašem kódu museli mít přihlašovací údaje. Přečtěte si další informace o [spravovaných identitách v Azure](../active-directory/managed-identities-azure-resources/overview.md). 

Existují dva typy spravovaných identit: 

## <a name="system-assigned-managed-identity"></a>Spravovaná identita přiřazená systémem  

 **Spravovaná identita přiřazená systémem**   je povolena přímo v instanci služby Azure. Když je tato identita povolená, Azure vytvoří identitu pro instanci v tenantovi Azure AD důvěryhodném pro předplatné instance. Po vytvoření identity se přihlašovací údaje zřídí do instance. Životní cyklus identity přiřazené systémem je přímo vázaný na instanci služby Azure, na které je povolený. Pokud se instance odstraní, Azure automaticky vyčistí přihlašovací údaje a identitu v Azure AD. 

### <a name="scenarios-for-using-labs-system-assigned-identity"></a>Scénáře použití identity přiřazené systémem v testovacím prostředí  

Každá DevTest Labs se vytvoří s identitou přiřazenou systémem, která zůstává platná pro celou dobu životnosti testovacího prostředí. Identita přiřazená systémem se používá pro následující účely:  

- Všechna [Azure Resource Manager](devtest-lab-create-environment-from-arm.md) založená na nasazeních, která se používají k vykonání několika virtuálních počítačů a platforem jako služby, se spustí pomocí identity přiřazené systémem v testovacím prostředí.  
- Šifrování disku pro disky testovacího prostředí pomocí klíče spravovaného zákazníkem se podporuje prostřednictvím identity přiřazené systémem v testovacím prostředí. Díky poskytnutí explicitního přístupu k identitě testovacího prostředí pro přístup k sadě pro šifrování disků může testovací prostředí šifrovat všechny disky virtuálních počítačů vaším jménem. Přečtěte si další informace o [tom, jak povolit šifrování disku](encrypt-disks-customer-managed-keys.md) pro disky testovacího prostředí pomocí spravovaného klíče zákazníka.  

### <a name="configure-identity"></a>Konfigurace identity

Tato část ukazuje, jak nakonfigurovat zásady identity testovacího prostředí.

> [!NOTE]
> Pro laboratoře vytvořené před 8/10/2020 bude identita přiřazená systému nastavená na vypnuto. Jako vlastník testovacího prostředí ho můžete zapnout v případě, že chcete použít cvičení pro účely uvedené v předchozí části.  
>
> Pro novou laboratoř vytvořenou po 8/10/2020 je identita přiřazená systému v testovacím prostředí ve výchozím nastavení zapnutá a vlastník testovacího prostředí ho nebude moct vypnout pro životní cyklus testovacího prostředí.  

1. Přihlaste se k [portálu Azure Portal](https://portal.azure.com).
1. Vyhledejte **DevTest Labs**.
1. V seznamu cvičení vyberte testovací prostředí, které chcete.
1. Vyberte **Konfigurace a zásady**  ->  **identita (Preview)**. 

> [!div class="mx-imgBorder"]
> ![Konfigurace identity](./media/configure-lab-identity/configure-identity.png)

## <a name="user-assigned-managed-identity"></a>Spravovaná identita přiřazená uživatelem  

Spravovaná identita přiřazená uživatelem se vytvoří jako samostatný prostředek Azure. Prostřednictvím procesu vytvoření Azure vytvoří identitu v tenantovi Azure AD důvěryhodném pro použité předplatné. Po vytvoření identity je možné ji přiřadit k jedné nebo několika instancím služeb Azure. Životní cyklus uživatelsky přiřazené identity se spravuje nezávisle na životním cyklu instancí služby Azure, ke kterým je přiřazený. 

DevTest Labs podporuje identity přiřazené uživateli pro virtuální počítače i prostředí založené na Azure Resource Manager.  Další informace najdete v následujících tématech:

- [Přidání uživatelsky přiřazené identity pro nasazení laboratorních Azure Resource Manager prostředí](use-managed-identities-environments.md)
- [Přidání uživatelsky přiřazených identit pro nasazení virtuálních počítačů testovacího prostředí](enable-managed-identities-lab-vms.md)

## <a name="next-steps"></a>Další kroky

Kontrola [Konfigurace správy nákladů](devtest-lab-configure-cost-management.md)