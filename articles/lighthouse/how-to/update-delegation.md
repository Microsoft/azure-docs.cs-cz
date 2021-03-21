---
title: Aktualizace delegování
description: Přečtěte si, jak aktualizovat delegování pro zákazníka dříve připojeného do Azure Lighthouse.
ms.date: 02/16/2021
ms.topic: how-to
ms.openlocfilehash: f0ed5222cdbac3d0e4d193941c2a6f233d15938c
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100555767"
---
# <a name="update-a-delegation"></a>Aktualizace delegování

Po připojení předplatného (nebo skupiny prostředků) k Azure Lighthouse může být nutné provést změny. Zákazník například může chtít provést další úlohy správy, které vyžadují jinou integrovanou roli Azure, nebo může být nutné změnit tenanta, se kterým je předplatné zákazníka delegované.

> [!TIP]
> I když v tomto tématu odkazujeme na poskytovatele služeb a zákazníky, můžou podniky, které [spravují víc tenantů](../concepts/enterprise.md) , použít stejný postup k nastavení Azure Lighthouse a konsolidovat prostředí pro správu.

Pokud jste [zákazníkovi připojili Azure Resource Manager šablon (šablony ARM)](onboard-customer.md), musí se pro tohoto zákazníka provést nové nasazení. V závislosti na tom, co měníte, možná budete chtít aktualizovat původní nabídku nebo odebrat původní nabídku a vytvořit novou.

- **Pokud měníte jenom autorizaci**: delegování můžete aktualizovat tak, že změníte jenom část **autorizace** v šabloně ARM.
- **Pokud měníte tenanta pro správu**: musíte vytvořit novou šablonu ARM pomocí jiného **mspOfferName** než předchozí nabídky.

## <a name="update-your-arm-template"></a>Aktualizace šablony ARM

Pokud chcete aktualizovat delegování, budete muset nasadit šablonu ARM, která obsahuje změny, které byste chtěli udělat.

Pokud provádíte aktualizaci jenom autorizací (například přidání nové skupiny uživatelů s dříve zahrnutou rolí, nebo změnou role existujícího uživatele), můžete použít stejný **mspOfferName** jako v [šabloně ARM](onboard-customer.md#create-an-azure-resource-manager-template) , kterou jste použili pro předchozí delegování. Jako výchozí bod můžete použít předchozí šablonu. Pak proveďte potřebné změny, jako je například nahrazení jedné předdefinované role Azure jiným nebo přidáním zcela nové autorizace do šablony.

Změníte-li **mspOfferName**, bude tato nabídka považována za novou a samostatnou nabídku. To je nutné v případě, že měníte tenanta pro správu.

Není nutné měnit **mspOfferName** v případě, že tenant pro správu zůstává stejný. Ve většině případů doporučujeme, abyste v jednom zákazníkovi a správě tenanta používali jenom jeden **mspOfferName** . Pokud se rozhodnete, že ji chcete změnit, ujistěte se, že předchozí delegování zákazníka bylo odebráno před nasazením nového.

## <a name="remove-the-previous-delegation"></a>Odebrat předchozí delegování

Před provedením nového nasazení budete možná chtít [Odebrat přístup k předchozímu delegování](remove-delegation.md). Tím se zajistí, že se odeberou všechna předchozí oprávnění, která vám umožní začít čistit s přesnými uživateli, skupinami a rolemi, které by se měly použít.

> [!IMPORTANT]
> Pokud použijete nový **mspOfferName** a zachováte stejné **principalId** hodnoty, musíte před nasazením nové nabídky odebrat přístup k předchozímu delegování. Pokud jste tuto nabídku neodebrali jako první, můžou uživatelé, kteří mají dřív udělené oprávnění, ztratit přístup úplně kvůli konfliktním přiřazením.

Pokud měníte tenanta pro správu, můžete opustit předchozí nabídku na místě, pokud chcete, aby oba klienti mohli i nadále mít přístup. Pokud chcete, aby měl nový tenant pro správu přístup, je nutné odebrat předchozí nabídku. Tato možnost se dá provést před nebo po zprovoznění nové nabídky.

Pokud aktualizujete nabídku jenom pro úpravu autorizací a zachováte stejné **mspOfferName**, nemusíte odebrat předchozí delegování. Nové nasazení nahradí předchozí delegování a použije se jenom autorizace v nejnovější šabloně.

:::image type="content" source="../media/update-delegation.jpg" alt-text="Diagram znázorňující, kdy změnit mspOfferName a odebrat předchozí delegování":::

Odebrání přístupu k delegování může provést libovolný uživatel ve správě tenanta, kterému bylo uděleno [přiřazení registrace spravovaných služeb](../../role-based-access-control/built-in-roles.md#managed-services-registration-assignment-delete-role) v původním delegování. Pokud žádný uživatel ve vašem tenantovi pro správu nemá tuto roli, můžete požádat zákazníka, aby [odebral přístup k této nabídce v Azure Portal](view-manage-service-providers.md#add-or-remove-service-provider-offers).

> [!TIP]
> Pokud jste předchozí delegování odebrali podle výše uvedených kroků a stále nemůžete nasadit novou šablonu ARM, možná budete muset [definici registrace úplně odebrat](/powershell/module/az.managedservices/remove-azmanagedservicesdefinition). To může udělat libovolný uživatel s rolí, která má oprávnění, jako je například `Microsoft.Authorization/roleAssignments/write` [vlastník](../../role-based-access-control/built-in-roles.md#owner), v tenantovi zákazníka.  

## <a name="deploy-the-arm-template"></a>Nasazení šablony ARM

Vaši zákazníci můžou [nasazovat aktualizovanou šablonu](onboard-customer.md#deploy-the-azure-resource-manager-templates) stejným způsobem jako předtím: v Azure Portal pomocí PowerShellu nebo pomocí rozhraní příkazového řádku Azure CLI.

Po dokončení nasazení [potvrďte, že bylo úspěšné](onboard-customer.md#confirm-successful-onboarding). Aktualizovaná autorizace pak bude platit pro předplatné nebo skupiny prostředků, které zákazník delegoval.

## <a name="updating-managed-service-offers"></a>Aktualizace nabídek spravovaných služeb

Pokud jste zákazník zavedli prostřednictvím nabídky spravované služby publikované do Azure Marketplace a chcete aktualizovat autorizaci, můžete aktualizovat delegování [publikováním nové verze vaší nabídky](../../marketplace/partner-center-portal/update-existing-offer.md) s [autorizací](../../marketplace/plan-managed-service-offer.md) , kterou chcete v plánu pro daného zákazníka použít. Zákazník pak bude moct aktualizovat na nejnovější verzi v Azure Portal.

Pokud chcete změnit klienta správy, budete muset [vytvořit a publikovat novou nabídku spravované služby](../../marketplace/plan-managed-service-offer.md) , kterou zákazník přijme.

> [!TIP]
> Jak už bylo zmíněno dříve, doporučujeme, abyste mezi stejným zákazníkem a správou tenanta nepoužívali více různých nabídek. Pokud publikujete novou nabídku pro stejného zákazníka, který používá stejného spravovaného tenanta, ujistěte se, že je předchozí nabídka odebrána, než zákazník nabídne novější nabídku.

## <a name="next-steps"></a>Další kroky

- V **Azure Portal můžete** [Zobrazit a spravovat zákazníky](view-manage-customers.md) .
- Naučte se, jak [Odebrat přístup k](remove-delegation.md) dříve zadanému delegování.
