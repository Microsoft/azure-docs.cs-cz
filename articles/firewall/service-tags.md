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
ms.sourcegitcommit: 3ea45bbda81be0a869274353e7f6a99e4b83afe2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/10/2020
ms.locfileid: "97031575"
---
# <a name="azure-firewall-service-tags"></a>Značky služby Azure Firewall

Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nemůžete vytvořit vlastní značku služby ani určit, které IP adresy jsou ve značce zahrnuté. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje.

Značky služby Azure Firewall lze použít v poli cíl síťových pravidel. Můžete je použít místo konkrétních IP adres.

## <a name="supported-service-tags"></a>Podporované značky služby

Seznam značek služeb, které jsou k dispozici pro použití v sítích brány firewall Azure, najdete v tématu [značky služby virtuální sítě](../virtual-network/service-tags-overview.md#available-service-tags) .

## <a name="next-steps"></a>Další kroky

Další informace o pravidlech Azure Firewall najdete v tématu [logika zpracování pravidel Azure firewall](rule-processing.md).
