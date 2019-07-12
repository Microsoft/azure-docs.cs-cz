---
title: Zobrazení a Správa poskytovatelů služeb na webu Azure Portal
description: Zákazníky můžete použít stránku poskytovatelů služby na webu Azure Portal k zobrazení informací o poskytovatelů služeb, služeb a delegované prostředky.
author: JnHs
ms.author: jenhayes
ms.service: lighthouse
ms.date: 07/11/2019
ms.topic: overview
manager: carmonm
ms.openlocfilehash: a45458e7417bba058522fdc0dbc34fee04ad9af8
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/11/2019
ms.locfileid: "67809838"
---
# <a name="view-and-manage-service-providers"></a>Zobrazení a Správa poskytovatelů služeb

Zákazníci můžou využít **poskytovatelé služeb** stránku [webu Azure portal](https://portal.azure.com) Chcete-li zobrazit informace o poskytovateli služeb a služeb, delegovat konkrétní prostředky prostřednictvím [Azure Delegovaná správa prostředků](../concepts/azure-delegated-resource-management.md)a kupte další služeb. Zatímco budeme odkazovat poskytovatelů služeb a zákazníků, podnikům správu více tenantů můžete použít stejný postup pro konsolidaci jejich prostředí pro správu.

Pro přístup **poskytovatelé služeb** stránce na webu Azure Portal, Zákazník **všechny služby**, vyhledejte **poskytovatelé služeb** a vyberte ji. Může také najít ho tak, že zadáte "Poskytovatelů služeb" do vyhledávacího pole v horní části webu Azure portal.

Mějte na paměti, která **poskytovatelé služeb** stránka zobrazuje jenom informace o poskytovatelích služeb, které mají přístup k předplatným nebo skupinám prostředků prostřednictvím prostředků Azure delegované správy tohoto zákazníka. Pokud zákazník funguje s dalších poskytovatelů služeb, kteří nepoužívají správu prostředků Azure Delegovaný přístup k prostředkům zákazníka, zde není zobrazen informace o těchto poskytovatelů služeb.

> [!NOTE]
> Poskytovatelé služeb můžete zobrazit informace o svým zákazníkům tak, že přejdete do **zákazníci** na webu Azure Portal. Další informace najdete v tématu [zobrazení a Správa zákazníků a delegované prostředků](view-manage-customers.md).

## <a name="view-service-provider-details"></a>Zobrazit podrobnosti o poskytovatele služby

Chcete-li zobrazit informace o těchto poskytovatelů služby, který zákazník ve spolupráci s, můžete vybrat **aliance** na levé straně **poskytovatelé služeb** stránky.

Pro každou nabídku poskytovatele služby zákazníka zobrazí název poskytovatele služeb a nabídka spojená s ním, spolu s názvem, který zákazník zadali během procesu registrace.

V **delegace** sloupce, zákazník vidí, kolik předplatných nebo skupinách prostředků delegované poskytovatele služeb pro tuto nabídku. Poskytovatel služeb budou moct používat a spravovat tyto odběry a/nebo skupiny prostředků podle úrovně přístupu podle nabídky.

## <a name="delegate-resources"></a>Delegát prostředky

Předtím, než poskytovatele služeb můžete přístup a správa na základě prostředků, je potřeba delegovat. Pokud zákazník přijal nabídky, ale nebyla ještě nedelegovali všechny prostředky, zobrazí poznámka v horní části **aliance** oddílu. Díky tomu zákazník vědět, že bude potřeba provést akce, poskytovatele služeb mohli získat přístup ke některý z prostředků zákazníka.

Na delegáta, předplatná nebo skupiny prostředků:

1. Zaškrtněte políčko pro řádek, který obsahuje poskytovatele služeb, nabídky a název. Potom vyberte **delegovat prostředky** v horní části obrazovky.
1. V **podrobnosti nabídky** část **delegovat prostředky** stránky, podrobné informace o poskytovateli služeb a nabízí. Chcete-li zkontrolovat přiřazení rolí pro tuto nabídku, vyberte **kliknutím sem zobrazíte podrobnosti o vybrané nabídky**.
1. V **delegáta** vyberte **delegovat předplatné** nebo **delegovat skupin prostředků**.
1. Vyberte předplatná a/nebo skupiny prostředků, které chcete delegovat pro tuto nabídku a zvolte **přidat**.
1. Zaškrtněte políčko v dolní části stránky. Tím potvrdíte, že chcete udělit přístup k prostředkům, které jste vybrali, pak vyberte poskytovatele služby **delegáta**.

## <a name="add-or-remove-service-provider-offers"></a>Přidání nebo odebrání služeb

Odběratele můžete přidat nové nabídky poskytovatele služeb z **aliance** stránky tak, že vyberete **nabídka přidat**. Poskytovatel služeb musí publikovat v rámci nabídky pro tohoto zákazníka. Zákazník může pak zvolit nabídku z **soukromé nabídky** obrazovky a pak vyberte **vytvořit**.

Pokud zákazník chce odebrat nabídky poskytovatele služeb, jejich vyberte ikonu koše v řádku pro ni. Po potvrzení odstranění tohoto poskytovatele služeb už mít přístup k prostředkům zákazníka, které byly dříve delegovat pro ni.

## <a name="view-delegations"></a>Zobrazení delegace

Delegace představují přiřazení rolí, které udělují oprávnění k poskytovateli služby pro prostředky, které zákazník má delegovat. Chcete-li zobrazit tyto informace, vyberte **delegace** na levé straně **poskytovatelé služeb** stránky.

Filtry v horní části stránky umožňují seřadit a seskupit informace o delegování nebo filtrovat podle některých zákazníků, nabídky a klíčová slova.

## <a name="next-steps"></a>Další postup

- Další informace o [Azure maják](../overview.md).
- Zjistěte, jak mohou poskytovatelé služeb [umožňuje zobrazit a spravovat zákazníky](view-manage-customers.md) tak, že přejdete do **zákazníci** na webu Azure Portal.