---
title: Co jsou tokeny Azure blockchain
description: Tokeny Azure blockchain jsou platforma jako služba (PaaS) pro vystavování a správu tokenů.
services: azure-blockchain
author: PatAltimore
ms.author: patricka
ms.date: 11/04/2019
ms.topic: overview
ms.service: azure-blockchain
ms.reviewer: brendal
ms.openlocfilehash: cd41d52e06a5c1833dca9669881cbe48f362d81d
ms.sourcegitcommit: f4d8f4e48c49bd3bc15ee7e5a77bee3164a5ae1b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2019
ms.locfileid: "73579754"
---
# <a name="what-is-azure-blockchain-tokens"></a>Co jsou tokeny Azure blockchain?

[!INCLUDE [Preview note](./includes/preview.md)]

Tokeny Azure blockchain jsou platforma jako služba (PaaS) pro vystavování a správu standardizovaných tokenů v rámci Blockchainch knih v Azure.

Pomocí tokenů Azure blockchain můžete vytvořit standardizované tokeny pro vaše řešení blockchain s použitím předem připravené šablony tokenu. Pomocí služby můžete také vytvořit vlastní šablonu tokenu. Po vytvoření použijte tokeny Azure blockchain k připojení a vydávání tokenů na blockchain. Po vydání můžete tyto tokeny spravovat napříč několika blockchain sítěmi.

## <a name="templates"></a>Šablony

Pomocí tokenů Azure blockchain můžete vybrat předem vytvořenou šablonu tokenu nebo vytvořit vlastní šablonu tokenu. Tokeny Azure blockchain podporují vytváření šablon tokenů, které vám umožní vytvořit vlastní šablonu tokenu na základě podporovaného chování. Šablony tokenů se dají použít pro většinu řešení blockchain, protože se mapují na nejběžněji používané tokeny. Můžete začít se šablonou, přizpůsobit ji a nasazovat tokeny pro vaše řešení.

Další informace o šablonách tokenů Azure blockchain najdete v tématu [šablony tokenů Azure blockchain](templates.md).

## <a name="management"></a>Správa

Tokeny Azure blockchain poskytují Azure Portal správu a rozhraní API pro připojení k existující síti blockchain. V současné době se můžete připojit ke [službě Azure blockchain](../service/overview.md) nebo k jinému blockchain Family ethereem.

Po připojení k jedné nebo více sítím blockchain můžete použít rozhraní API Azure blockchain tokeny k vydávání a správě tokenů pro použití ve vašem řešení blockchain. Pomocí rozhraní API můžete integrovat správu tokenů do obchodních aplikací a logiky. Můžete například použít REST API ke správě tokenů namísto správy tokenů přímo na blockchain.

## <a name="blockchains-and-accounts"></a>Blockchainy a účty

Tokeny Azure blockchain poskytují Azure Portal pro správu a rozhraní API k vytváření nových skupin a nových účtů blockchain v připojených sítích blockchain. Nové účty můžete vytvářet přímo v připojených sítích a tokeny Azure blockchain spravuje privátní klíče vašeho účtu vaším jménem. Pomocí skupin můžete seskupovat různé účty blockchain z více sítí a spravovat řízení přístupu přes skupiny.

Další informace o správě účtů tokenů Azure blockchain najdete v tématu [Správa účtů Azure blockchain tokens](account-management.md).

## <a name="token-taxonomy-framework"></a>Rozhraní taxonomie tokenů

Tokeny Azure blockchain jsou založené na standardizovaných základech s názvem token taxonomie (TTF). TTF je sada dodávek vytvořených z pracovní skupiny tokenů pro TTI ( [token taxonomie](https://entethalliance.org/participate/token-taxonomy-initiative/) ). Pracovní skupina TTI definuje obchodní taxonomii pro tokeny a jejich chování, které lze použít ve všech hlavních knihách, včetně Ethereem, kvora, šňůry a prostředků infrastruktury hlavní knihy. Cílem pracovní skupiny je vytvořit architekturu, která bude standardizovat použití tokenů z obchodní perspektivy pro účely zjednodušení a vývoje na základě tokenu Demokratizujte. Tím, že umožníte odvětví definovat tyto tokeny a jejich chování na úrovni firmy, je podrobná implementace tokenů z obchodní logiky, která zpracovává tokeny, abstraktní.

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o dostupných [šablonách tokenů Azure blockchain](templates.md).
