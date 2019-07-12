---
title: Umožňuje zobrazit a spravovat zákazníky a delegované prostředků na webu Azure Portal
description: Jako poskytovatel služeb pomocí Azure delegovat správu prostředků, můžete zobrazit všechny prostředky delegovaný zákazníků a předplatných tak, že přejdete do své zákazníky na webu Azure Portal.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: acc90afa258fa7140cd7dfa8711dd64b554df45d
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809852"
---
# <a name="view-and-manage-customers-and-delegated-resources"></a>Umožňuje zobrazit a spravovat zákazníky a delegované zdroje

Poskytovatelé služeb pomocí [delegovat správu prostředků Azure](../concepts/azure-delegated-resource-management.md) můžete použít **zákazníci** stránku [webu Azure portal](https://portal.azure.com) k zobrazení prostředků služby delegované zákazníka a předplatná. Zatímco budeme odkazovat poskytovatelů služeb a zákazníků, podnikům správu více tenantů můžete použít stejný postup pro konsolidaci jejich prostředí pro správu.

Přístup **zákazníci** stránky na webu Azure Portal, vyberte **všechny služby**, vyhledejte **své zákazníky** a vyberte ji. Také ho můžete vyhledat zadáním "Zákazníci" do vyhledávacího pole v horní části webu Azure portal.

Mějte na paměti, která **zákazníci** stránka zobrazuje jenom informace o zákaznících, kteří měli přidělená předplatných nebo skupinách prostředků. Pokud pracujete s jinými zákazníky (například prostřednictvím [programu Cloud Solution Provider](https://docs.microsoft.com/partner-center/csp-overview), informace o těchto zákazníků se nezobrazí, pokud můžete připojit svoje prostředky pro delegovaný správy prostředků.

> [!NOTE]
> Zákazníky můžete zobrazit informace o poskytovatelé služeb tak, že přejdete do **poskytovatelé služeb** na webu Azure Portal. Další informace najdete v tématu [zobrazení a Správa poskytovatelů služeb](view-manage-service-providers.md).

## <a name="view-and-manage-customer-details"></a>Umožňuje zobrazit a spravovat podrobnosti o zákazníkovi

Chcete-li zobrazit podrobnosti o zákazníkovi, vyberte **zákazníkům** na levé straně **zákazníci** stránky.

Pro každého zákazníka zobrazí jméno zákazníka, ID zákazníka (ID tenanta) a nabídky přidružené k zapojení. V **delegování** sloupec, zobrazí se počet delegovaného předplatná a/nebo počet skupin delegovanému prostředku.

Filtry v horní části stránky umožňují seřadit a seskupit informace o zákaznících nebo filtrovat podle některých zákazníků, nabídky a klíčová slova.

Můžete zobrazit následující informace, které z této stránky:

- Zobrazíte všechna předplatná, nabídky a delegování spojené se zákazníkem, vyberte název zákazníka.
- Zobrazte podrobnosti o nabídku a její delegování, vyberte název nabídky.
- Chcete-li zobrazit další podrobnosti o přiřazení acrolecess pro delegované předplatných nebo skupinách prostředků, vyberte položku v **delegace** sloupce.

## <a name="view-delegations"></a>Zobrazení delegace

Delegace ukazují předplatného/skupiny prostředků, který byl delegován, spolu s uživatelů a oprávnění, která k ní máte přístup. Chcete-li zobrazit tyto informace, vyberte **delegace** na levé straně **zákazníci** stránky.

Filtry v horní části stránky umožňují seřadit a seskupit informace o přiřazení přístupu nebo filtrovat podle některých zákazníků, nabídky a klíčová slova.

Uživatele a oprávnění, které jsou spojené s každou delegování joinkind **přiřazení rolí** sloupce. Můžete vybrat jednotlivé položky zobrazit úplný seznam uživatelů, skupin a instanční objekty, které byl udělen přístup k předplatnému nebo skupině prostředků. Odtud můžete vybrat konkrétní uživatele, skupinu nebo hlavní název služby, přečtěte si další podrobnosti.

## <a name="work-in-the-context-of-a-delegated-subscription"></a>Práce v rámci delegované předplatné

Můžete pracovat přímo v kontextu delegované předplatné na webu Azure portal, bez nutnosti přepínat na adresář, který používáte. Postup:

1. Vyberte **adresář a předplatné** ikonu v horní části webu Azure portal.
2. V **globálních předplatných** filtrovat, ujistěte se, pouze pole pro vybrané delegované předplatného. Můžete použít **aktuální + delegované adresáře** rozevíracího seznamu zobrazíte jenom předplatná v rámci konkrétní adresář. (Nepoužívejte **přepnout adresář** možnost, protože se mění adresáře, ke kterému jste přihlášení.)

Pokud pak přístup ke službě, která podporuje [napříč tenanty činnosti správy](../concepts/cross-tenant-management-experience.md), služba bude použita výchozí kontext delegované předplatné, které jste vybrali. Toto můžete změnit pomocí následujících kroků výše a kontrola, zda **Vybrat vše** pole (nebo výběru jednoho nebo více předplatných pro práci místo).

> [!NOTE]
> Pokud vám byl udělen přístup k jedné nebo více skupin prostředků, a ne přístup k celé předplatné, můžete vybrat předplatné, do které patří příslušné skupině prostředků. Potom budete pracovat v rámci daného předplatného, ale pouze budou moci pracovat se skupinami prostředků určené.

Funkce související s delegované předplatná nebo skupiny prostředků z v rámci služeb, které podporují správu napříč tenanty prostředí tak, že vyberete předplatné nebo skupinu prostředků z v rámci této služby se také zpřístupní.

## <a name="next-steps"></a>Další postup

- Další informace o [napříč tenanty činnosti správy](../concepts/cross-tenant-management-experience.md).
- Zjistěte, jak vaši zákazníci můžou [zobrazení a Správa poskytovatelů služeb](view-manage-service-providers.md) tak, že přejdete do **poskytovatelé služeb** na webu Azure Portal.
