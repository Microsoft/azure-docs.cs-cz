---
title: Vytvoření předplatných Azure prostřednictvím kódu programu
description: Tento článek vám pomůže pochopit dostupné možnosti programového vytváření předplatných Azure.
author: bandersmsft
ms.service: cost-management-billing
ms.subservice: billing
ms.topic: how-to
ms.date: 03/11/2021
ms.reviewer: andalmia
ms.author: banders
ms.custom: devx-track-azurepowershell, devx-track-azurecli
ms.openlocfilehash: 9ec0ffeb930fd9285f34ad9ba9e6aa606b15b5a2
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104593884"
---
# <a name="create-azure-subscriptions-programmatically"></a>Vytvoření předplatných Azure prostřednictvím kódu programu

Tento článek vám pomůže pochopit dostupné možnosti programového vytváření předplatných Azure.

Pomocí různých rozhraní REST API můžete vytvořit předplatné pro tyto typy smluv Azure:

- Smlouva Enterprise (EA)
- Smlouva se zákazníkem Microsoftu (MCA)
- Smlouva s partnerem Microsoftu

Pro jiné typy smluv s rozhraními REST API nelze programově vytvořit další odběry.

Požadavky a podrobnosti pro vytváření předplatných se pro různé smlouvy a verze rozhraní API liší. Projděte si následující články, které odpovídají vaší situaci:

Nejnovější rozhraní API:

- [Vytvoření předplatných EA](programmatically-create-subscription-enterprise-agreement.md)
- [Vytváření předplatných MCA](programmatically-create-subscription-microsoft-customer-agreement.md)
- [Vytváření předplatných MPA](programmatically-create-subscription-microsoft-partner-agreement.md)

Tyto články také ukazují, jak vytvořit odběry pomocí šablony Azure Resource Manager (šablona ARM). Šablona ARM pomáhá automatizovat proces vytváření předplatného.

Pokud stále používáte [rozhraní API verze Preview](programmatically-create-subscription-preview.md), můžete s nimi pokračovat v vytváření předplatných. 

## <a name="next-steps"></a>Další kroky

* Teď když jste vytvořili předplatné, můžete tuto možnost poskytnout dalším uživatelům a instančním objektům. Další informace najdete v tématu [Udělení přístupu pro vytváření předplatných Azure Enterprise (Preview)](grant-access-to-create-subscription.md).
* Další informace o správě velkého počtu předplatných pomocí skupin pro správu najdete v tématu věnovaném [uspořádání prostředků pomocí skupin pro správu v Azure](../../governance/management-groups/overview.md).
