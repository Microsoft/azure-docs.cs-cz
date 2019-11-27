---
title: Správa účtů tokenů Azure blockchain
description: Pomocí služby Azure blockchain tokens Management můžete vytvořit skupiny a propojit účty blockchain za účelem řízení přístupu k akcím blockchain.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: b77e97709663c0c9f84d95c1f0578fcfcb3b2a6c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/22/2019
ms.locfileid: "74326103"
---
# <a name="azure-blockchain-tokens-account-management"></a>Správa účtů tokenů Azure blockchain

[!INCLUDE [Preview note](./includes/preview.md)]

Pro řešení blockchain můžou uživatelé vyžadovat různé úrovně přístupu k tokenům vytvořeným pomocí služby tokenů Azure blockchain. Ve většině scénářů Blockchain je potřeba naplánovat a nasadit různé účty blockchain, které existují v hlavní knize. Musíte taky spravovat přístup mezi účastníky. Pomocí služby Azure blockchain tokens Management můžete vytvořit skupiny a propojit účty blockchain za účelem řízení přístupu k akcím blockchain.

## <a name="blockchain-networks"></a>Blockchain sítě

Tokeny Azure blockchain umožňují nasazení a správu tokenů napříč sadou blockchain sítí. Ke službě můžete připojit jednu blockchain knihu nebo několik knih blockchain.

## <a name="accounts"></a>Účty

Pro sítě blockchain připojené k tokenům Azure blockchain služba vytvoří a spravuje páry klíčů privátního veřejného klíče a provádí podepisování a odesílání transakcí. Tokeny Azure blockchain také poskytuje mapování identit tak, aby odpovídalo účtům s identitou veřejného klíče v hlavní knize.

## <a name="groups"></a>Skupiny

Skupiny vám umožní spravovat velký počet blockchain účtů napříč propojenými sítěmi. Můžete sledovat a auditovat, které aplikace a uživatelé v adresáři mají schopnost používat účty prostřednictvím rozhraní API pro tokeny Azure blockchain. Můžete například seskupit sadu účtů, které reprezentují různé řádky obchodních nebo různých rolí a přístup k tokenům blockchain.

Skupinu můžete také přidružit k Azure Active Directory uživateli nebo instančnímu objektu a tento objekt zabezpečení má oprávnění ke skupině a jejím přidruženým účtům.  

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o dostupných [šablonách tokenů Azure blockchain](templates.md).
