---
title: zahrnout soubor
description: zahrnout soubor
services: virtual-network
author: genlin
ms.service: virtual-network
ms.topic: include
ms.date: 04/13/2018
ms.author: genli
ms.custom: include file
ms.openlocfilehash: d20ef44fd5c117e4e3a568542bb022c451ac23fc
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/23/2019
ms.locfileid: "66170818"
---
## <a name="scenario"></a>Scénář
Tento dokument vás provede nasazení, které používá více síťových rozhraní ve virtuálních počítačích v určité scénáře. V tomto scénáři máte dvouvrstvém IaaS úlohy hostované v Azure. Každá vrstva je nasazená ve vlastní podsíti ve virtuální síti (VNet). Úroveň front-endu se skládá z několika webových serverů, seskupit do sady pro zajištění vysoké dostupnosti nástroje pro vyrovnávání zatížení. Na úrovni back-end se skládá z několika databázových serverů. Databázové servery jsou nasazené se dvěma síťovými kartami, jeden pro přístup k databázi, druhá pro správu. Tento scénář také zahrnuje skupiny zabezpečení sítě (Nsg) k řízení, jaký provoz je povolený pro každou podsíť a síťovou kartu v nasazení. Následující obrázek znázorňuje základní architekturu tento scénář:

![Scénář MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

