---
title: Přehled značek služby Azure Firewall
description: Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 83e9a96573bbc72e0afff61cc0f151f95b081e30
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "97031575"
---
# <a name="azure-firewall-service-tags"></a>Značky služby Azure Firewall

Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nemůžete vytvořit vlastní značku služby ani určit, které IP adresy jsou ve značce zahrnuté. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje.

Značky služby Azure Firewall lze použít v poli cíl síťových pravidel. Můžete je použít místo konkrétních IP adres.

## <a name="supported-service-tags"></a>Podporované značky služby

Seznam značek služeb, které jsou k dispozici pro použití v sítích brány firewall Azure, najdete v tématu [značky služby virtuální sítě](../virtual-network/service-tags-overview.md#available-service-tags) .

## <a name="next-steps"></a>Další kroky

Další informace o pravidlech Azure Firewall najdete v tématu [logika zpracování pravidel Azure firewall](rule-processing.md).
