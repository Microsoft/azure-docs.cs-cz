---
title: Přehled značek služeb Azure Firewall
description: Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74168688"
---
# <a name="azure-firewall-service-tags"></a>Značky služeb Azure Firewall

Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nemůžete vytvořit vlastní značku služby ani určit, které IP adresy jsou ve značce zahrnuté. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje.

Značky služeb Azure Firewall lze použít v cílovém poli pravidel sítě. Můžete je použít místo konkrétních IP adres.

## <a name="supported-service-tags"></a>Podporované značky služeb

Seznam značek služeb, které jsou k dispozici pro použití v síťových pravidlech brány firewall Azure, najdete v tématu [Skupiny zabezpečení.](../virtual-network/security-overview.md#service-tags)

## <a name="next-steps"></a>Další kroky

Další informace o pravidlech azure firewallu najdete v článku [Logika zpracování pravidel brány Azure Firewall](rule-processing.md).