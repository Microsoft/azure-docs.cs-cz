---
title: Programové vytváření předplatných Azure
description: Tento článek vám pomůže pochopit dostupné možnosti programového vytváření předplatných Azure.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 01/13/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: a51a158700df52ced5288cf2c2f82ebcdf3de54d
ms.sourcegitcommit: 25d1d5eb0329c14367621924e1da19af0a99acf1
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/16/2021
ms.locfileid: "98254013"
---
# <a name="create-azure-subscriptions-programatically"></a>Programové vytváření předplatných Azure

Tento článek vám pomůže pochopit dostupné možnosti programového vytváření předplatných Azure.

Pomocí různých rozhraní REST API můžete vytvořit předplatné pro tyto typy smluv Azure:

- Smlouva Enterprise (EA)
- Smlouva se zákazníkem Microsoftu (MCA)
- Smlouva s partnerem Microsoftu

Pomocí rozhraní REST API nemůžete vytvořit další p pro jiné typy smluv.

Požadavky a podrobnosti pro vytváření předplatných se pro různé smlouvy a verze rozhraní API liší. Projděte si následující články, které odpovídají vaší situaci:

Nejnovější rozhraní API:

- [Vytvoření předplatných EA](programmatically-create-subscription-enterprise-agreement.md)
- [Vytváření předplatných MCA](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Vytváření předplatných MPA](programmatically-create-subscription-microsoft-partner-agreement.md)

Pokud stále používáte [rozhraní API verze Preview](programmatically-create-subscription-preview.md), můžete dál pokračovat ve vytváření předplatných s těmito rozhraními. 

A můžete [vytvářet předplatná s využitím šablony ARM](create-subscription-template.md). Šablona ARM pomáhá automatizovat proces vytváření předplatných s využitím rozhraní REST API. 

## <a name="next-steps"></a>Další kroky

* Teď když jste vytvořili předplatné, můžete tuto možnost poskytnout dalším uživatelům a instančním objektům. Další informace najdete v tématu [Udělení přístupu pro vytváření předplatných Azure Enterprise (Preview)](grant-access-to-create-subscription.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu věnovaném [uspořádání prostředků pomocí skupin pro správu v Azure](../../governance/management-groups/overview.md).
