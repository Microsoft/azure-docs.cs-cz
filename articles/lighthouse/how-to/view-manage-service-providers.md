---
title: Zobrazení a správa poskytovatelů služeb
description: Zákazníci mohou pomocí stránky poskytovatelé služeb v Azure Portal zobrazit informace o poskytovatelích služeb, nabídkách poskytovatele služeb a delegovaných prostředcích.
ms.date: 12/16/2020
ms.topic: how-to
ms.openlocfilehash: 5ee897503c997ab10fdb489f7921c9d2d001e472
ms.sourcegitcommit: 86acfdc2020e44d121d498f0b1013c4c3903d3f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/17/2020
ms.locfileid: "97617200"
---
# <a name="view-and-manage-service-providers"></a>Zobrazení a správa poskytovatelů služeb

Stránka **poskytovatelé služeb** v [Azure Portal](https://portal.azure.com) poskytuje zákazníkům kontrolu a viditelnost svých poskytovatelů služeb, kteří používají [Azure Lighthouse](../overview.md). Zákazníci si můžou zobrazit podrobnosti o poskytovatelích služeb, delegovat konkrétní prostředky, nakupovat nové nabídky poskytovatelů služeb, odebírat přístup k poskytovateli služeb a další.

> [!TIP]
> V takovém případě budeme odkazováni na poskytovatele služeb a zákazníky a podniky, které [spravují víc tenantů](../concepts/enterprise.md) , můžou stejný postup využít k konsolidaci prostředí pro správu.

Pro přístup ke stránce **poskytovatelé služeb** v Azure Portal může zákazník vybrat **všechny služby** a pak vyhledat **poskytovatele služeb** a vybrat ho. Můžou je taky najít zadáním "poskytovatelé služeb" nebo "Azure Lighthouse" do vyhledávacího pole v horní části Azure Portal.

> [!NOTE]
> Aby bylo možné zobrazit stránku **poskytovatelé služeb** , musí mít uživatel v tenantovi zákazníka [vestavěnou roli Čtenář](../../role-based-access-control/built-in-roles.md#reader) (nebo jinou předdefinovanou roli, která zahrnuje přístup ke čtenářům).
>
> Chcete-li přidat nebo aktualizovat nabídky, delegovat prostředky a odebrat nabídky, musí mít uživatel [předdefinovanou roli](../../role-based-access-control/built-in-roles.md#owner) pro předplatné.

Mějte na paměti, že na stránce **poskytovatelé služeb** se zobrazují jenom informace o poskytovatelích služeb, kteří mají přístup k předplatným zákazníků nebo ke skupinám prostředků prostřednictvím Azure Lighthouse. Pokud zákazník spolupracuje s dalšími poskytovateli služeb, kteří k přístupu k prostředkům zákazníka nepoužívají Azure Lighthouse, informace o těchto poskytovatelích služeb tady nejsou uvedené.

> [!TIP]
> Poskytovatelé služeb můžou zobrazit informace o svých zákaznících tak, že v Azure Portal přejdou na **Moje zákazníky** . Další informace najdete v tématu [zobrazení a Správa zákazníků a delegovaných prostředků](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Zobrazit podrobnosti o poskytovateli služeb

Chcete-li zobrazit podrobnosti o poskytovatelích služeb, zákazník může vybrat **nabídku poskytovatele služeb** na levé straně stránky **poskytovatelé služeb** .

Pro každou nabídku poskytovatele služeb uvidí Zákazník název poskytovatele služeb a nabídku, která je k ní přidružená, spolu s názvem, který zákazník zadal během procesu připojování.

Ve sloupci **delegování** si zákazník uvidí, kolik předplatných a skupin prostředků bylo delegováno pro poskytovatele služeb této nabídky. Poskytovatel služeb bude moci získat přístup k těmto předplatným a skupinám prostředků a spravovat je podle úrovní přístupu zadaných v nabídce.

## <a name="add-or-remove-service-provider-offers"></a>Přidat nebo odebrat nabídky poskytovatele služeb

Zákazník může přidat novou nabídku poskytovatele služeb ze stránky **nabídky poskytovatele služeb** výběrem možnosti **přidat nabídku**. Poskytovatel služeb musí publikovat nabídku pro tohoto zákazníka. Zákazník pak může vybrat tuto nabídku z obrazovky **soukromé nabídky** a pak vybrat **vytvořit**.

Pokud chce zákazník odebrat nabídku poskytovatele služeb, může to provést kdykoli tak, že na řádku této nabídky vybere ikonu odpadkového koše. Po potvrzení odstranění již poskytovatel služeb nebude mít přístup k zákaznickým prostředkům, které byly dříve delegovány pro tuto nabídku.

## <a name="delegate-resources"></a>Delegování prostředků

Předtím, než může poskytovatel služeb získat přístup k prostředkům zákazníka a spravovat je, musí být delegovaný. Pokud zákazník nabídku přijal, ale ještě nedelegoval žádné prostředky, uvidí poznámku na začátku části **nabídky poskytovatele služeb** . To zákazníkovi ví, že musí provést akci předtím, než bude moci poskytovatel služeb získat přístup k některým prostředkům zákazníka.

Delegování předplatných nebo skupin prostředků:

1. Zaškrtněte políčko u řádku obsahujícího poskytovatele služeb, nabídku a název. Pak vyberte **delegovat prostředky** v horní části obrazovky.
1. V části **Podrobnosti nabídky** na stránce **delegovat prostředky** si přečtěte podrobnosti o poskytovateli služeb a nabídce. Chcete-li zkontrolovat přiřazení rolí pro nabídku, vyberte **kliknutím sem zobrazíte podrobnosti vybrané nabídky**.
1. V části **delegáta** vyberte **delegovat odběry** nebo **delegovat skupiny prostředků**.
1. Zvolte předplatná nebo skupiny prostředků, které chcete pro tuto nabídku delegovat, a pak vyberte **Přidat**.
1. Zaškrtněte políčko v dolní části stránky a potvrďte, že chcete tomuto poskytovateli služeb udělit přístup k vybraným prostředkům, a pak vyberte **delegovat**.

## <a name="update-service-provider-offers"></a>Aktualizovat nabídky poskytovatele služeb

Po přidání nabídky může poskytovatel služeb publikovat aktualizovanou verzi stejné nabídky, která Azure Marketplace. Například mohou chtít přidat novou definici role. Pokud byla publikována nová verze nabídky, na stránce **nabídky poskytovatele služeb** se zobrazí ikona "aktualizace" na řádku této nabídky. Zákazník může tuto ikonu vybrat, aby se zobrazily rozdíly mezi aktuální verzí nabídky a novou.

 ![Ikona nabídky aktualizace](../media/update-offer.jpg)

Po kontrole změn si může zákazník vybrat, že se má aktualizovat na novou verzi. Jakmile to uděláte, autorizaci a další nastavení zadaná v nové verzi budou platit pro všechna předplatná nebo skupiny prostředků, které jsou pro tuto nabídku delegované.

## <a name="view-delegations"></a>Zobrazit delegování

Delegace představují přiřazení rolí, které udělují poskytovateli služeb oprávnění k prostředkům delegovaným zákazníkem. Chcete-li zobrazit tyto informace, vyberte možnost **delegace** na levé straně stránky **poskytovatelé služeb** .

Filtry v horní části stránky umožňují řadit a seskupovat informace o delegování. Můžete také filtrovat podle konkrétních zákazníků, nabídek nebo klíčových slov.

> [!NOTE]
> Zákazníci neuvidí Tato přiřazení rolí ani žádné uživatele z klienta poskytovatele služeb, kterému byly tyto role uděleny při [zobrazení přiřazení rolí pro delegovaný obor v Azure Portal](../../role-based-access-control/role-assignments-list-portal.md#list-role-assignments-at-a-scope) nebo prostřednictvím rozhraní API.

## <a name="audit-delegations-in-your-environment"></a>Auditovat delegování ve vašem prostředí

Zákazníci mohou chtít získat přehled o předplatných nebo skupinách prostředků, které byly delegovány na Azure Lighthouse. To je užitečné hlavně pro zákazníky, kteří mají velký počet předplatných nebo kteří mají mnoho uživatelů, kteří provádějí úlohy správy.

Poskytujeme [Azure Policy vestavěnou definici zásad](../../governance/policy/samples/built-in-policies.md#lighthouse) pro [auditování delegování oborů do spravovaného tenanta](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/Lighthouse_Delegations_Audit.json). Tuto zásadu můžete přiřadit ke skupině pro správu, která zahrnuje všechna předplatná, která chcete auditovat. Když zkontrolujete dodržování předpisů pomocí této zásady, budou se všechny delegované předplatné nebo skupiny prostředků (ve skupině pro správu, ke které je zásada přiřazená) zobrazovat v nekompatibilním stavu. Potom můžete zkontrolovat výsledky a ověřit, že neexistují žádná neočekávaná delegování.

Další [definice předdefinované zásady](../../governance/policy/samples/built-in-policies.md#lighthouse) umožňuje [omezit delegování na konkrétní správu klientů](https://github.com/Azure/azure-policy/blob/master/built-in-policies/policyDefinitions/Lighthouse/AllowCertainManagingTenantIds_Deny.json). Tyto zásady se můžou použít obdobně pro skupinu pro správu, která obsahuje všechna předplatná, pro která chcete delegování omezit. Po nasazení zásady budou všechny pokusy o delegování předplatného na klienta mimo ty, které zadáte, zamítnuté.

Další informace o tom, jak přiřadit zásadu a zobrazit výsledky stavu dodržování předpisů, najdete v tématu [rychlý Start: vytvoření přiřazení zásady](../../governance/policy/assign-policy-portal.md).

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure Lighthouse](../overview.md).
- Přečtěte si, jak [Auditovat aktivitu poskytovatele služeb](view-service-provider-activity.md).
- Naučte se, jak můžou poskytovatelé služeb [zobrazovat a spravovat zákazníky](view-manage-customers.md) na stránce **moji zákazníci** v Azure Portal.
