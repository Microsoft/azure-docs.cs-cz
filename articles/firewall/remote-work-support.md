---
title: Podpora vzdálené práce azure firewallu
description: Tento článek ukazuje, jak azure firewall může podporovat vaše požadavky na vzdálené pracovní síly.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289638"
---
# <a name="azure-firewall-remote-work-support"></a>Podpora vzdálené práce azure firewallu

Azure Firewall je spravovaná cloudová služba zabezpečení sítě, která chrání prostředky virtuálnísítě Azure. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností v cloudu. 

## <a name="firewall-rules"></a>Pravidla brány firewall

Pomocí azure firewall můžete zabezpečit příchozí přístup rdp virtuální plochy (VDI) k vaší virtuální síti Azure pomocí [pravidel DNAT](rule-processing.md)azure firewallu . Windows Virtual Desktop (WVD) nevyžaduje, abyste otevřeli příchozí přístup k virtuální síti. Je však nutné povolit sadu odchozích síťových připojení pro virtuální počítače WVD, které běží ve vaší virtuální síti. Další informace naleznete v tématu [Co je virtuální plocha windows?](../virtual-desktop/overview.md#requirements)

Tento odchozí přístup můžete nakonfigurovat pomocí pravidel aplikací Azure Firewall. Další informace najdete [v tématu Výuka: Nasazení a konfigurace brány Azure Firewall pomocí portálu Azure](tutorial-firewall-deploy-portal.md).

## <a name="next-steps"></a>Další kroky

Další informace o [windows virtuální ploše](https://docs.microsoft.com/azure/virtual-desktop/).