---
title: Vyrovnanost tokenů Azure Blockchain
description: Vyrovnanost tokenů Azure Blockchain poskytuje flexibilitu při vytváření tokenů pro pokročilé scénáře.
ms.date: 11/04/2019
ms.topic: conceptual
ms.reviewer: brendal
ms.openlocfilehash: a3fe1b290917de20b7c3af31fe386ed93580d850
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74325116"
---
# <a name="azure-blockchain-tokens-composability"></a>Vyrovnanost tokenů Azure Blockchain

[!INCLUDE [Preview note](./includes/preview.md)]

Kompostovatelnost tokenů poskytuje flexibilitu při vytváření tokenů pro pokročilé scénáře. Můžete mít složitý scénář, který nelze implementovat pomocí [čtyř předem vytvořených šablon tokenů](templates.md#base-token-types). Kompostování tokenu umožňuje navrhnout vlastní šablony tokenů přidáním nebo odebráním definovaného chování pro vytvoření vlastní šablony tokenu. Při vytváření nové šablony tokenu tokeny Azure Blockchain ověří všechna pravidla gramatiky tokenu. Složené šablony se ukládají do služby Azure Blockchain Tokens pro vydávání v připojených blockchainových sítích.

Chování [tokenů](templates.md#token-behaviors) v následujících částech můžete použít k návrhu šablony tokenu.

## <a name="burnable-b"></a>Spalovatelné (b)

Možnost odebrat tokeny z dodávky.

Pokud například uplatníte online body kreditní karty za dárkovou kartu, body kreditní karty se spálí.

## <a name="delegable-g"></a>Delegovatelné (g)

Možnost delegovat akce provedené na tokenu, který vlastníte.

Delegát může provádět akce jako vlastník tokenu. Můžete například použít delegovatelný token k implementaci hlasování. Delegovatelný token umožňuje majiteli tokenu hlasování, aby někdo jiný hlasoval jejich jménem.

## <a name="logable-l"></a>Logovatelné (l)

Schopnost logovat.

Můžete například vydat logable token pro distribuci filmu do každého divadla zobrazující konkrétní film. Aby se film přehrál, musí promítání zaznamenávat transakci pro každé promítání, protože výplaty licenčních poplatků jsou na promítání během spuštění filmu. Sestavení actors můžete použít filmové tokeny k ověření výplaty na film ukazuje na divadlo v distribuci.

## <a name="mint-able-m"></a>Mincovna (m)

Možnost mincovny další žetony pro třídu tokenu. Minter role zahrnuje mintable chování.

Například maloobchodní společnost, která chce implementovat věrnostní program, může pro svůj věrnostní program používat mintable tokeny. Mohou mincovny další věrnostní body pro své zákazníky, jak jejich zákaznická základna roste.  

## <a name="non-subdividable-or-whole-d"></a>Nerozdělitelné nebo celé (~d)

Omezení zabránit tokenu z rozdělení na menší části.

Například jeden umělecký obraz nelze rozdělit na více menších částí. 

## <a name="non-transferable-t"></a>Nepřenosná (~t)

Omezení zabránit změně vlastnictví od původního vlastníka tokenu.

Například univerzitní diplom je nepřenosný token. Jakmile je diplom předán absolventovi, nemůže být převeden z absolventa na jinou osobu.

## <a name="roles-r"></a>Role (r)

Možnost definovat role v rámci třídy šablony tokenu pro konkrétní chování.

Můžete zadat seznam názvů rolí, které token podporuje v době vytvoření tokenu. Pokud jsou zadány role, uživatel může přiřadit role k těmto chování. V současné době je podporována pouze role minter.

## <a name="singleton-s"></a>Singleton (y)

Omezení povolit dodávku jednoho tokenu.

Například muzejní artefakt je singleton token. Muzejní artefakty jsou jedinečné. Token představující artefakt má pouze jednu položku v dodávce.

## <a name="subdividable-d"></a>Subdividable (d)

Schopnost rozdělit token na menší části.

Například dolar může být rozdělen na centy.

## <a name="transferable-t"></a>Přenosná (t)

Možnost převést vlastnictví tokenu.

Například vlastnické právo je převoditelný token, který lze při prodeji nemovitosti převést z jedné osoby na druhou.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [správě účtů tokenů Azure Blockchain](account-management.md).
