---
title: Přehled značek služby Azure Firewall
description: Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: 47377817b62d33e8af79e4a0d2dceb68ba9dbdc5
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658643"
---
# <a name="azure-firewall-service-tags"></a>Značky služby Azure Firewall

Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nemůžete vytvořit vlastní značku služby ani určit, které IP adresy jsou ve značce zahrnuté. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje.

Značky služby Azure Firewall lze použít v poli cíl síťových pravidel. Můžete je použít místo konkrétních IP adres.

## <a name="supported-service-tags"></a>Podporované značky služby

Seznam značek služeb, které jsou k dispozici pro použití v síťových pravidlech brány firewall Azure, najdete v tématu [skupiny zabezpečení](../virtual-network/network-security-groups-overview.md#service-tags) .

## <a name="next-steps"></a>Další kroky

Další informace o pravidlech Azure Firewall najdete v tématu [logika zpracování pravidel Azure firewall](rule-processing.md).