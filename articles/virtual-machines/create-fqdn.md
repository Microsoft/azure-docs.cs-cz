---
title: Vytvoření plně kvalifikovaného názvu domény pro virtuální počítač v Azure Portal
description: Naučte se vytvořit plně kvalifikovaný název domény (FQDN) pro virtuální počítač v Azure Portal.
author: cynthn
ms.service: virtual-machines
ms.subservice: networking
ms.topic: how-to
ms.workload: infrastructure-services
ms.date: 11/03/2020
ms.author: cynthn
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 08d5e20bf0755a71e70a0e446cf96d33bb42ad59
ms.sourcegitcommit: 99955130348f9d2db7d4fb5032fad89dad3185e7
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93351881"
---
# <a name="create-a-fully-qualified-domain-name-in-the-azure-portal-for-a-linux-vm"></a>Vytvoření plně kvalifikovaného názvu domény v Azure Portal pro virtuální počítač se systémem Linux

Když v [Azure Portal](https://portal.azure.com)vytvoříte virtuální počítač, automaticky se vytvoří prostředek veřejné IP adresy pro virtuální počítač. Tuto IP adresu použijete pro vzdálený přístup k virtuálnímu počítači. I když portál nevytváří [plně kvalifikovaný název domény](https://en.wikipedia.org/wiki/Fully_qualified_domain_name)nebo plně kvalifikovaný název domény, můžete ho přidat po vytvoření virtuálního počítače. Tento článek popisuje postup vytvoření názvu DNS nebo plně kvalifikovaného názvu domény. 

## <a name="create-a-fqdn"></a>Vytvoření plně kvalifikovaného názvu domény
V tomto článku se předpokládá, že jste už vytvořili virtuální počítač. V případě potřeby můžete na portálu vytvořit virtuální počítač se systémem [Linux](./linux/quick-create-portal.md) nebo [Windows](./windows/quick-create-portal.md) . Když je virtuální počítač v provozu a spuštěný, postupujte podle těchto kroků:


1. Na portálu vyberte svůj virtuální počítač. V části **název DNS** vyberte **Konfigurovat**.
2. Zadejte název DNS a potom v horní části stránky vyberte **Uložit** .
3. Pokud se chcete vrátit do okna s přehledem virtuálního počítače, zavřete okno **Konfigurace** tak, že v pravém horním rohu vyberete **X** . 
4. Ověřte, zda je *název DNS* nyní zobrazen správně.
   



## <a name="next-steps"></a>Další kroky
Když teď váš virtuální počítač má veřejnou IP adresu a název DNS, můžete nasadit společné aplikační architektury nebo služby, jako je Nginx, MongoDB a Docker.

Můžete si také přečíst další informace o [použití Správce prostředků](../azure-resource-manager/management/overview.md) pro tipy k vytváření nasazení Azure.

