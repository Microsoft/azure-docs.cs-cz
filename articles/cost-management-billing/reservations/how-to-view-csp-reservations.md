---
title: Zobrazení rezervací Azure jako poskytovatel Cloud Solution Provider
description: Zjistěte, jak můžete zobrazit rezervace Azure jako poskytovatel Cloud Solution Provider.
ms.service: cost-management-billing
author: bandersmsft
ms.reviewer: yashesvi
ms.topic: how-to
ms.date: 07/29/2020
ms.author: banders
ms.openlocfilehash: fde33640505c225167700215f32cb3243acc5196
ms.sourcegitcommit: e71da24cc108efc2c194007f976f74dd596ab013
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/29/2020
ms.locfileid: "87424925"
---
# <a name="view-azure-reservations-as-a-cloud-solution-provider-csp"></a>Zobrazení rezervací Azure jako poskytovatel Cloud Solution Provider (CSP)

Poskytovatelé Cloud Solution Provider mají přístup k rezervacím zakoupeným pro jejich zákazníky. K zobrazení rezervací na webu Azure Portal využijte následující informace.

1. Obraťte se na svého globálního správce a požádejte ho, aby vás přidal jako **agenta pro správu** ve vašem tenantovi.
    Tuto možnost mají globální správci k dispozici v Partnerském centru. Nachází se v části **Nastavení** (symbol ozubeného kolečka v pravé horní části stránky) -> **Správa uživatelů**.  
1. Jakmile budete mít oprávnění agenta pro správu, přejděte na web Azure Portal pomocí odkazu **AOBO (Admin on Behalf Of)** .
1. V Partnerském centru přejděte do části **Zákazníci**, rozbalte podrobnosti o zákazníkovi a vyberte **Portál pro správu Microsoft Azure**.
1. Na webu Azure Portal přejděte do části **Rezervace**.

> [!NOTE]
> Pokud jste hostem v tenantovi zákazníka, nemůžete zobrazit rezervace. Pokud máte přístup hosta, musíte ho z tenanta odebrat. Oprávnění agenta pro správu nepřepisuje přístup hosta.

- Pokud chcete přístup hosta odebrat, v Partnerském centru přejděte do části **Můj účet** >  **[Organizace](https://myaccount.microsoft.com/organizations)** a vyberte **Opustit organizaci**.

Případně můžete požádat jiného uživatele, který má k rezervaci přístup, o přidání vašeho účtu hosta do objednávky rezervace.

## <a name="next-steps"></a>Další kroky

- [Zobrazení rezervací Azure](view-reservations.md)