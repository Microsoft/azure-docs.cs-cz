---
title: Podpora vzdálené práce Azure Firewall
description: Tento článek ukazuje, jak Azure Firewall můžou podporovat požadavky na vzdálené pracovní síly.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: conceptual
ms.date: 03/25/2020
ms.author: victorh
ms.openlocfilehash: 5abe9344b0512433c48df50335cce5cf1e3e3547
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/28/2020
ms.locfileid: "80289638"
---
# <a name="azure-firewall-remote-work-support"></a>Podpora vzdálené práce Azure Firewall

Azure Firewall je spravovaná cloudová služba zabezpečení sítě, která chrání vaše prostředky virtuální sítě Azure. Jedná se o plně stavovou bránu firewall jako službu s integrovanou vysokou dostupností a neomezenou škálovatelností v cloudu. 

## <a name="firewall-rules"></a>Pravidla brány firewall

Pomocí Azure Firewall můžete zabezpečit příchozí přístup protokolu RDP (Virtual Desktop Infrastructure) ke službě Azure Virtual Network pomocí [pravidel Azure firewall DNAT](rule-processing.md). Virtuální počítač s Windows (WVD) nevyžaduje, abyste otevřeli žádný příchozí přístup k vaší virtuální síti. Musíte ale pro virtuální počítače s WVD, které běží ve vaší virtuální síti, poskytnout sadu odchozích síťových připojení. Další informace najdete v tématu [co je to virtuální počítač s Windows?](../virtual-desktop/overview.md#requirements)

Tento odchozí přístup můžete nakonfigurovat pomocí Azure Firewall pravidel aplikací. Další informace najdete v tématu [kurz: nasazení a konfigurace Azure firewall pomocí Azure Portal](tutorial-firewall-deploy-portal.md).

## <a name="next-steps"></a>Další kroky

Přečtěte si další informace o [virtuálním počítači s Windows](https://docs.microsoft.com/azure/virtual-desktop/).