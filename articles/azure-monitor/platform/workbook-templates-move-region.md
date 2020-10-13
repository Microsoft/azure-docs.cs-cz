---
title: Azure Monitor šablon sešitu – přesun oblastí
description: Postup přesunutí šablony sešitu do jiné oblasti
services: azure-monitor
author: gardnerjr
manager: acearun
ms.workload: tbd
ms.tgt_pltfrm: ibiza
ms.topic: how-to
ms.custom: subject-moving-resources
ms.date: 08/12/2020
ms.author: jgardner
ms.openlocfilehash: f37550d914cf7efb0c75ed3dfa8854e1ec7be7e0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88875616"
---
# <a name="move-an-azure-workbook-template-to-another-region"></a>Přesunutí šablony sešitu Azure do jiné oblasti

Tento článek popisuje, jak přesunout prostředky šablony sešitu Azure do jiné oblasti Azure. Prostředky můžete přesunout do jiné oblasti z řady důvodů. Například pro využití nové oblasti Azure, k nasazení funkcí nebo služeb dostupných pouze v konkrétních oblastech, pro splnění požadavků na požadavky na interní zásady a zásady správného řízení nebo v reakci na požadavky na plánování kapacity.

V současné době není k dispozici žádné uživatelské rozhraní portálu pro vytváření zdrojů šablon sešitu. jediným způsobem, jak je vytvořit, je [pomocí nasazení šablony Azure Resource Manager šablon (ARM)](./workbooks-automate.md). Nejjednodušší způsob, jak přesunout šablonu, je použít předchozí šablonu ARM a aktualizovat ji tak, aby se nasadila do nové oblasti.

## <a name="prerequisites"></a>Požadavky

* Zajistěte, aby byly šablony sešitu podporovány v cílové oblasti.

## <a name="prepare"></a>Příprava

* Identifikujte dřív použitou šablonu pro šablonu sešitu.

## <a name="move"></a>Přesunout

1. Aktualizujte dřív použitou šablonu, aby odkazovala na novou oblast.

   > [!NOTE]
   > Možná budete muset pro šablonu sešitu použít nový název, abyste se vyhnuli jakýmkoli duplicitním názvům.

2. Nasaďte aktualizovanou šablonu pomocí nasazení šablony ARM a vytvořte novou šablonu sešitu v požadované oblasti.

## <a name="verify"></a>Ověření

Pomocí uživatelského rozhraní pro procházení sešitů Azure Najděte nově nasazenou šablonu sešitu. Zajistěte, aby umístění bylo cílovým umístěním.

## <a name="clean-up"></a>Vyčištění

Po vytvoření šablony sešitu v nové oblasti odstraňte původní šablonu sešitu v předchozí oblasti.
1. Vyhledejte šablonu sešitu v uživatelském rozhraní pro procházení sešitů Azure.
2. Vyberte šablonu sešitu, kterou chcete odstranit.
3. Vyberte příkaz Odstranit.

Pokud jste šablonu sešitu nastavili tak, aby se importovala do nové oblasti, můžete šablonu sešitu přejmenovat na předchozí název po odstranění původní položky pomocí příkazu přejmenovat v zobrazení prostředku šablony sešitu Azure.

## <a name="next-steps"></a>Další kroky

Potřebujete místo šablony přesunout sešit? Podívejte se, jak [přesunout sešit Azure do jiné oblasti](./workbooks-move-region.md).

