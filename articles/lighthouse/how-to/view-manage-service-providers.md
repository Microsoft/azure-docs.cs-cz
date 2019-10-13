---
title: Zobrazení a správa poskytovatelů služeb
description: Zákazníci mohou pomocí stránky poskytovatelé služeb v Azure Portal zobrazit informace o poskytovatelích služeb, nabídkách poskytovatele služeb a delegovaných prostředcích.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: 0a7d39d95e35e30a16ce11db5b942024b6890438
ms.sourcegitcommit: e0a1a9e4a5c92d57deb168580e8aa1306bd94723
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/11/2019
ms.locfileid: "72286592"
---
# <a name="view-and-manage-service-providers"></a>Zobrazení a správa poskytovatelů služeb

Zákazníci mohou pomocí stránky **poskytovatelé služeb** v [Azure Portal](https://portal.azure.com) zobrazit informace o nabídkách poskytovatelů služeb a poskytovatelů služeb, delegovat konkrétní prostředky prostřednictvím [správy delegovaných prostředků Azure](../concepts/azure-delegated-resource-management.md)a koupit další poskytovatel služeb nabízí. V takovém případě budeme odkazováni na poskytovatele služeb a zákazníky a podniky, které spravují víc tenantů, můžou stejný postup využít k konsolidaci prostředí pro správu.

Pro přístup ke stránce **poskytovatelé služeb** v Azure Portal může zákazník vybrat **všechny služby**a pak vyhledat **poskytovatele služeb** a vybrat ho. Můžou je taky najít zadáním "poskytovatelé služeb" do vyhledávacího pole v horní části Azure Portal.

Mějte na paměti, že na stránce **poskytovatelé služeb** se zobrazují jenom informace o poskytovatelích služeb, kteří mají přístup k předplatným zákazníků nebo ke skupinám prostředků prostřednictvím správy delegovaných prostředků Azure. Pokud zákazník spolupracuje s dalšími poskytovateli služeb, kteří k přístupu k prostředkům zákazníka nepoužívají správu delegovaných prostředků Azure, informace o těchto poskytovatelích služeb tady nejsou uvedené.

> [!NOTE]
> Poskytovatelé služeb mohou zobrazit informace o svých zákaznících tak, že v Azure Portal přejdou na **Moje zákazníky** . Další informace najdete v tématu [zobrazení a Správa zákazníků a delegovaných prostředků](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Zobrazit podrobnosti o poskytovateli služeb

Chcete-li zobrazit informace o poskytovatelích služeb, se kterými zákazník pracuje, může vybrat **nabídky poskytovatele** na levé straně stránky **poskytovatelé služeb** .

Pro každou nabídku poskytovatele služeb uvidí Zákazník název poskytovatele služeb a nabídku, která je k ní přidružená, spolu s názvem, který zákazník zadal během procesu připojování.

Ve sloupci **delegování** si zákazník uvidí, kolik předplatných a skupin prostředků bylo delegováno pro poskytovatele služeb této nabídky. Poskytovatel služeb bude moci získat přístup k těmto předplatným a skupinám prostředků a spravovat je podle úrovní přístupu zadaných v nabídce.

## <a name="delegate-resources"></a>Delegovat prostředky

Předtím, než může poskytovatel služeb získat přístup k prostředkům zákazníka a spravovat je, musí být delegovaný. Pokud zákazník nabídku přijal, ale ještě nedelegoval žádné prostředky, uvidí v horní části **nabídky poskytovatele** poznámku. To zákazníkovi ví, že musí provést akci předtím, než bude moci poskytovatel služeb získat přístup k některým prostředkům zákazníka.

Delegování předplatných nebo skupin prostředků:

1. Zaškrtněte políčko u řádku obsahujícího poskytovatele služeb, nabídku a název. Pak vyberte **delegovat prostředky** v horní části obrazovky.
1. V části **Podrobnosti nabídky** na stránce **delegovat prostředky** si přečtěte podrobnosti o poskytovateli služeb a nabídce. Chcete-li zkontrolovat přiřazení rolí pro nabídku, vyberte **kliknutím sem zobrazíte podrobnosti vybrané nabídky**.
1. V části **delegáta** vyberte **delegovat odběry** nebo **delegovat skupiny prostředků**.
1. Zvolte předplatná nebo skupiny prostředků, které chcete pro tuto nabídku delegovat, a pak vyberte **Přidat**.
1. Zaškrtněte políčko v dolní části stránky a potvrďte, že chcete tomuto poskytovateli služeb udělit přístup k vybraným prostředkům, a pak vyberte **delegovat**.

## <a name="add-or-remove-service-provider-offers"></a>Přidat nebo odebrat nabídky poskytovatele služeb

Zákazník může přidat novou nabídku poskytovatele služeb ze stránky **nabídky poskytovatele** výběrem možnosti **přidat nabídku**. Poskytovatel služeb musí publikovat nabídku pro tohoto zákazníka. Zákazník pak může vybrat tuto nabídku z obrazovky **soukromé nabídky** a pak vybrat **vytvořit**.

Pokud chce zákazník odebrat nabídku poskytovatele služeb, může vybrat ikonu odpadkového koše na řádku této nabídky. Po potvrzení odstranění již poskytovatel služeb nebude mít přístup k zákaznickým prostředkům, které byly dříve delegovány pro tuto nabídku.

## <a name="view-delegations"></a>Zobrazit delegování

Delegace představují přiřazení rolí, které udělují poskytovateli služeb oprávnění k prostředkům delegovaným zákazníkem. Chcete-li zobrazit tyto informace, vyberte možnost **delegace** na levé straně stránky **poskytovatelé služeb** .

Filtry v horní části stránky umožňují seřadit a seskupit informace o delegování nebo filtrovat podle konkrétních zákazníků, nabídek nebo klíčových slov.

## <a name="next-steps"></a>Další kroky

- Přečtěte si další informace o [Azure Lighthouse](../overview.md).
- Přečtěte si, jak můžou poskytovatelé služeb [Zobrazit a spravovat zákazníky](view-manage-customers.md) tak, že na Azure Portal na **moji zákazníci** .