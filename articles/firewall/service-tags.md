---
title: Přehled značky služeb Brána Firewall služby Azure
description: Tento článek představuje přehled značky služeb Brána Firewall služby Azure.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 6/27/2019
ms.author: victorh
ms.openlocfilehash: d0ac36e415c056dffc9c75d00968ff74c2156e63
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/28/2019
ms.locfileid: "67450169"
---
# <a name="azure-firewall-service-tags"></a>Značky služeb Azure bránu Firewall

Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nemůžete vytvořit vlastní značku služby ani určit, které IP adresy jsou ve značce zahrnuté. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje.

Značky služeb Azure bránu Firewall je možné v síti pravidla cílového pole. Můžete je použít místo konkrétních IP adres.

## <a name="supported-service-tags"></a>Podporované značky služeb

Zobrazit [skupiny zabezpečení](../virtual-network/security-overview.md#service-tags) seznam značek služeb, které jsou k dispozici pro použití v pravidlech brány firewall Azure sítě.

## <a name="next-steps"></a>Další postup

Další informace o pravidlech brány Firewall Azure najdete v tématu [pravidlo brány Firewall na Azure zpracování logiky](rule-processing.md).