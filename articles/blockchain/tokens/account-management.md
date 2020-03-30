---
title: Správa účtů Tokenů Azure Blockchain
description: Pomocí správy účtů Azure Blockchain Tokens můžete vytvářet skupiny a propojovat blockchainové účty a řídit tak přístup k akcím blockchainu.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: 9931ef59e613501ba6feaedf3ac5d4721f0df752
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74326103"
---
# <a name="azure-blockchain-tokens-account-management"></a>Správa účtů Tokenů Azure Blockchain

[!INCLUDE [Preview note](./includes/preview.md)]

Pro řešení blockchain mohou uživatelé vyžadovat různé úrovně přístupu k tokenům, které jsou vytvořeny pomocí služby Azure Blockchain Tokens. Ve většině scénářů blockchainu musíte plánovat a nasazovat různé účty blockchainu, které existují v hlavní knize. Musíte také spravovat přístup mezi účastníky.Pomocí správy účtů Azure Blockchain Tokens můžete vytvářet skupiny a propojovat blockchainové účty a řídit tak přístup k akcím blockchainu.

## <a name="blockchain-networks"></a>Blockchainové sítě

Azure Blockchain Tokeny umožňují nasazení a správu tokenů v celé sadě blockchainových sítí. Ke službě můžete připojit jednu blockchainovou knihu nebo několik knih blockchain.

## <a name="accounts"></a>Účty

U blockchainových sítí připojených k tokenům Azure Blockchain služba vytváří a spravuje dvojice soukromých klíčů účtu a provádí podepisování a odesílání transakcí. Tokeny Azure Blockchain také poskytují mapování identit, které odpovídá účtům s identitou veřejného klíče v hlavní knize.

## <a name="groups"></a>Skupiny

Skupiny umožňují spravovat velké množství blockchainových účtů v připojených sítích. Můžete sledovat a auditovat, které aplikace a uživatelé v adresáři mají možnost používat účty prostřednictvím rozhraní API tokenů Azure Blockchain. Můžete například seskupit sadu účtů, které představují různé obchodní linie nebo různé role a přístup k tokenům blockchain.

Skupinu můžete také přidružit k uživateli služby Azure Active Directory nebo k objektu zabezpečení služby a tento objekt zabezpečení má oprávnění ke skupině a jejím přidruženým účtům.  

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [dostupných šablonách tokenů Azure Blockchain](templates.md).
