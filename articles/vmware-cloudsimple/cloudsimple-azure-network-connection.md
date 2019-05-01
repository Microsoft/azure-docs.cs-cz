---
title: Řešení VMware podle CloudSimple – připojení k síti Azure
description: Další informace o připojení k síti CloudSimple oblasti vaší virtuální síti Azure
author: sharaths-cs
ms.author: dikamath
ms.date: 04/10/2019
ms.topic: article
ms.service: vmware
ms.reviewer: cynthn
manager: dikamath
ms.openlocfilehash: 8ea98d6493b824bfa232ef8193388e93b97c506b
ms.sourcegitcommit: 44a85a2ed288f484cc3cdf71d9b51bc0be64cc33
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/26/2019
ms.locfileid: "64576997"
---
# <a name="azure-network-connection-overview"></a>Přehled připojení sítě Azure

Při vytváření služby CloudSimple v oblasti, je:

* Vytvoří okruh Azure ExpressRoute a připojí ke službě v dané oblasti
* Oblast sítě CloudSimple se připojí k vaší virtuální síti Azure nebo vaší místní sítí pomocí Azure ExpressRoute
* Poskytuje přístup ke službám, které běží na vaše předplatné Azure nebo v místní síti, z vašeho prostředí privátního cloudu

Toto připojení je velkou šířku pásma s nízkou latencí.

## <a name="benefits"></a>Výhody

Připojení k síti Azure vám umožní:

* Použití Azure jako cíl zálohování pro virtuální počítače na privátní Cloud.
* Nasaďte servery služby správy KLÍČŮ ve vašem předplatném Azure k zašifrování vaší sítě vSAN úložiště privátního cloudu.
* Pomocí hybridních aplikací, kde běží na úrovni webové aplikace ve veřejném cloudu při aplikaci a úrovně databáze spustit v privátního cloudu.

## <a name="expressroute-connection-to-on-premises-network"></a>Připojení ExpressRoute do místní sítě

Stávající okruh Azure ExpressRoute můžete připojit k vaší CloudSimple oblasti. Globální dosah ExpressRoute funkce slouží k připojení dvou okruhů mezi sebou.  Vytvoří připojení mezi místními a okruhy CloudSimple ExpressRoute.

Tato metoda vytvoří připojení mezi těmito dvěma prostředími, které je:

* Zabezpečení
* Private
* Velká šířka pásma
* Nízká latence

K vytvoření připojení ExpressRoute k místní síti, [obraťte se na podporu](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="next-steps"></a>Další postup

* [Nastavit připojení k virtuální síti](https://docs.azure.cloudsimple.com/virtual-network-connection)