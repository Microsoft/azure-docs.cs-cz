---
title: Možnosti vytváření tokenů Azure blockchain
description: Vytváření tokenů Azure blockchain poskytuje flexibilitu při vytváření tokenů pro pokročilé scénáře.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: conceptual
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: a82d7ba606eac5dcafc26b1a8527810a5a21840d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73577118"
---
# <a name="azure-blockchain-tokens-composability"></a>Možnosti vytváření tokenů Azure blockchain

[!INCLUDE [Preview note](./includes/preview.md)]

Vytváření tokenů poskytuje flexibilitu při vytváření tokenů pro pokročilé scénáře. Můžete mít složitý scénář, který nelze implementovat pomocí [čtyř předem připravených šablon tokenů](templates.md#base-token-types). Možnost vytvářet tokeny umožňuje navrhnout vlastní šablony tokenů přidáním nebo odebráním definovaného chování a vytvořit vlastní šablonu tokenu. Při vytváření nové šablony tokenu Azure blockchain tokeny ověří všechna gramatická pravidla tokenu. Složené šablony se ukládají ve službě tokenů Azure blockchain pro vydávání v připojených sítích blockchain.

Pomocí [chování tokenu](templates.md#token-behaviors) v následujících částech můžete navrhnout šablonu tokenu.

## <a name="burnable-b"></a>Vypálitelné (b)

Schopnost odebrat tokeny ze zdroje.

Například při uplatnění online platebních karet pro dárkovou kartu jsou vypáleny body platební karty.

## <a name="delegable-g"></a>Možností delegování (g)

Schopnost delegovat akce provedené na tokenu, který vlastníte.

Delegát může provádět akce jako vlastník tokenu. Můžete například použít token možností delegování k implementaci hlasování. Token možností delegování umožňuje vlastníkovi hlasovacího tokenu, aby jeho jménem měl hlasovacího práva někoho jiného.

## <a name="logable-l"></a>Logable (l)

Možnost přihlásit se.

Například můžete vystavit token logable pro distribuci filmu na každé kino ukazující konkrétní video. Aby se film mohl přehrát, musí se v zobrazení zaznamenat transakce pro každý z nich, protože výběry s licenčními poplatky jsou na základě toho, jak se v průběhu vydaných verzí videa zobrazují. Sestavení Actors může používat filmové tokeny k ověření vydaných výběrů na film ukazující v rozdělení na kino.

## <a name="mint-able-m"></a>Mentolová – možné (m)

Možnost mentolová další tokeny pro třídu tokenu. Role Minter zahrnuje chování mintable.

Například maloobchodní společnost, která chce implementovat věrnostní program, může pro svůj věrnostní program použít tokeny mintable. Můžou mentolová další věrnostní body pro zákazníky, když se jejich základna zákazník rozroste.  

## <a name="non-subdividable-or-whole-d"></a>Nerozdělitelné nebo celé (~ d)

Omezení zabraňující rozdělení tokenu na menší části.

Například vykreslování jednoho obrázku nelze rozdělit na více menších částí. 

## <a name="non-transferable-t"></a>Bez přenosu (~ t)

Omezení zabraňující změně vlastnictví od vlastníka počátečního tokenu.

Například diplom z univerzity je nepřevoditelný token. Jakmile se diplom dostaná k promoci, nedá se přenést z dopravení na jinou osobu.

## <a name="roles-r"></a>Role (r)

Možnost definovat role v rámci třídy šablony tokenu pro konkrétní chování.

Můžete zadat seznam názvů rolí, které token podporuje v době vytváření tokenu. Při zadání rolí může uživatel přiřadit role k tomuto chování. V současné době je podporována pouze role Minter.

## <a name="singleton-s"></a>Singleton (s)

Omezení umožňující poskytnout jeden token.

Například artefakt Museum je token typu singleton. Artefakty Museum jsou jedinečné. Token představující artefakt má pouze jednu položku v rámci dodávek.

## <a name="subdividable-d"></a>Rozdělitelné (d)

Schopnost rozdělit token na menší části.

Například dolar může být rozdělen na centy.

## <a name="transferable-t"></a>Přenosná (t)

Schopnost přenášet vlastnictví tokenu.

Například název vlastnosti je převoditelný token, který lze přenést z jedné osoby do druhé při prodeji vlastnosti.

## <a name="next-steps"></a>Další kroky

Přečtěte si o [správě účtů tokenů Azure blockchain](account-management.md).
