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
ms.openlocfilehash: cab04a7eafbc21e0d26cd5a287f3dbee8d3d22b7
ms.sourcegitcommit: 150a40d8ba2beaf9e22b6feff414f8298a8ef868
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/27/2018
ms.locfileid: "31805096"
---
## <a name="scenario"></a>Scénář
Tento dokument vás provede nasazení, které používá několik síťových adaptérů ve virtuálních počítačích v určité scénáře. V tomto scénáři máte Dvojúrovňová IaaS zatížení hostované v Azure. Každý vrstva je nasazená ve vlastní podsíti ve virtuální síti (VNet). Front-endu vrstvy se skládá z několika webových serverů, seskupeny ve službě Vyrovnávání zatížení nastavena pro zajištění vysoké dostupnosti. Úroveň back-end se skládá z několika databázových serverů. Databázové servery jsou nasazeny se dvěma síťovými adaptéry, každý, jeden pro přístup k databázi, druhý pro správu. Tento scénář zahrnuje taky skupin zabezpečení sítě (Nsg) pro přenosy, které mohou každé podsítě a síťový adaptér v nasazení. Následující obrázek ukazuje základní architekturu tohoto scénáře:

![Scénář MultiNIC](./media/virtual-network-deploy-multinic-scenario-include/Figure1.png)

