---
title: Diagram referenční architektury | Azure Marketplace
description: Postup vytvoření diagramu referenční architektury pro nabídku na komerčním webu Microsoft Marketplace.
author: mingshen-ms
ms.author: mingshen
ms.reviewer: stmummer
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: reference
ms.date: 02/18/2021
ms.openlocfilehash: 66e4d498ff7584188d680e35c89c6f10cc43c9cb
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "104604487"
---
# <a name="reference-architecture-diagram"></a>Diagram referenční architektury

Diagram referenční architektury je model, který představuje infrastrukturu, na které vaše nabídka spoléhá. V případě řešení IP pro Azure by měl diagram obsahovat také informace o tom, jak vaše nabídka používá cloudové služby Microsoftu na základě technických požadavků na společný prodej IP. Není navržený k vyhodnocení kvality architektury. Je navržený tak, aby zobrazoval, jak vaše řešení používá služby společnosti Microsoft.

Diagram referenční architektury se dá vytvořit přes víc nástrojů. Doporučujeme aplikaci Microsoft Visio, protože má několik vzorníků, které jsou znázorněny v modelech architektury Azure.

Užitečným výchozím bodem pro vytváření diagramů referenční architektury je využití [modelů architektury Azure](/azure/architecture/browse/).

## <a name="typical-components-of-a-reference-architecture-diagram"></a>Typické součásti diagramu referenční architektury

- Cloudové služby, které hostují a komunikují s vaší nabídkou, včetně těch, které využívají prostředky Azure
- Datová připojení, datové vrstvy a datové služby, které vaše nabídka spotřebovává
- Cloud Services, které slouží k řízení zabezpečení, ověřování a uživatelů nabídky
- Uživatelská rozhraní a další služby, které zpřístupňují nabídku uživatelům
- Hybridní nebo místní připojení, integrace nebo kombinace obou

Tento snímek obrazovky ukazuje příklad diagramu referenční architektury.

[![Tento obrázek je příkladem společného prodejního diagramu architektury.](./media/co-sell/co-sell-arch-diagram.png)](./media/co-sell/co-sell-arch-diagram.png#lightbox)

Tento příklad diagramu referenční architektury je pro vertikální obor chatovací robot, který se dá integrovat s intranetovou lokalitou a který je možné využít k odhadování scénářů požadavků pomocí algoritmu strojového učení. Toto řešení používá zásobovací řetězec a data výrobního plánu z různých systémů ERP. Robot je navržený tak, aby potvrdil otázky, když se prodejce může na možné dny doručení objednat.

## <a name="next-steps"></a>Další kroky

- [Konfigurace společného prodeje pro komerční nabídku na webu Marketplace](commercial-marketplace-co-sell.md)
