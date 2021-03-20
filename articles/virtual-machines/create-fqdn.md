---
title: Vytvoření plně kvalifikovaného názvu domény pro virtuální počítač v Azure Portal
description: Naučte se vytvořit plně kvalifikovaný název domény (FQDN) pro virtuální počítač v Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 1/12/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: aadf4000da4ab88a3c3b4dee37d2179eb49d39d7
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "98132060"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Vytvoření plně kvalifikovaného názvu domény v Azure Portal pro virtuální počítač se systémem Linux

Když v [Azure Portal](https://portal.azure.com)vytvoříte virtuální počítač, automaticky se vytvoří prostředek veřejné IP adresy pro virtuální počítač. Tuto IP adresu použijete pro vzdálený přístup k virtuálnímu počítači. I když portál nevytváří [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)nebo plně kvalifikovaný název domény, můžete ho přidat po vytvoření virtuálního počítače. Tento článek popisuje postup vytvoření názvu DNS nebo plně kvalifikovaného názvu domény. 

## <a name="create-a-fqdn"></a>Vytvoření plně kvalifikovaného názvu domény
V tomto článku se předpokládá, že jste už vytvořili virtuální počítač. V případě potřeby můžete na portálu vytvořit virtuální počítač se systémem [Linux](./linux/quick-create-portal.md) nebo [Windows](./windows/quick-create-portal.md) . Když je virtuální počítač v provozu a spuštěný, postupujte podle těchto kroků:


1. Na portálu vyberte svůj virtuální počítač. 
1. V nabídce vlevo vyberte **Konfigurace** .
1. V části **popisek názvu DNS** zadejte předponu, kterou chcete použít.
1. V horní části stránky vyberte **Save (Uložit** ).
1. V nabídce vlevo vyberte **Přehled** a vraťte se do okna s přehledem virtuálního počítače. 
1. Ověřte, že se *název DNS* zobrazuje správně. 

## <a name="next-steps"></a>Další kroky

DNS můžete spravovat také pomocí [Azure DNS zón](../dns/dns-getstarted-portal.md).

