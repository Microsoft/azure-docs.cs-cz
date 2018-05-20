---
title: Vývoj aplikací pro Azure zásobníku | Microsoft Docs
description: Vývoj důležité informace týkající se vytváření prototypů aplikací v Azure zásobníku
services: azure-stack
documentationcenter: ''
author: brenduns
manager: femila
editor: ''
ms.assetid: d3ebc6b1-0ffe-4d3e-ba4a-388239d6cdc3
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/15/2018
ms.author: brenduns
ms.reviewer: ''
ms.openlocfilehash: a1b5a90ca40ce2b19186220344b22ec0ae77e34b
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/16/2018
---
# <a name="develop-for-azure-stack"></a>Vývoj pro Azure Stack

*Platí pro: Azure zásobníku integrované systémy a Azure zásobníku Development Kit*

Můžete začít používat vývoj aplikací dnes, i když nebudou mít přístup k prostředí Azure zásobníku. Protože zásobník Azure poskytuje služby Microsoft Azure, které běží ve vašem datovém centru, můžete použít podobné procesy a nástroje pro vývoj proti zásobník Azure stejně jako s Azure. Některé přípravy a pomocí pokynů v následujících tématech můžete emulovat prostředí Azure zásobník Azure.

* V Azure můžete vytvořit šablony Azure Resource Manager, které jsou dostupné pro nasazení do Azure zásobníku. V tématu [aspekty šablon](azure-stack-develop-templates.md) pokyny k vývoji šablony zajistit přenositelnost.
* Existují v dostupnost služeb a správa verzí služeb rozdíly mezi Azure a Azure zásobníku. Můžete použít [modul zásad Azure zásobníku](azure-stack-policy-module.md) omezit typy dostupnosti a prostředek služby Azure na co je k dispozici v zásobníku Azure. Chovaly služby zajišťuje, že vaše aplikace závisí na služby, které jsou k dispozici pro zásobník Azure.
* [Šablony rychlý start Azure zásobníku](https://github.com/Azure/AzureStack-QuickStart-Templates) jsou běžné scénáře příklady, které ukazují, jak vyvíjet šablony, které lze nasadit do Azure a Azure zásobníku.
