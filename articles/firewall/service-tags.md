---
title: Přehled značek služby Azure Firewall
description: Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2019
ms.author: victorh
ms.openlocfilehash: ec9fb3c9b27d21ac65888379f59d51ecc8042eb0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "74168688"
---
# <a name="azure-firewall-service-tags"></a>Značky služby Azure Firewall

Značka služby představuje skupinu předpon IP adres a tím pomáhá minimalizovat složitost vytváření pravidla zabezpečení. Nemůžete vytvořit vlastní značku služby ani určit, které IP adresy jsou ve značce zahrnuté. Předpony adres zahrnuté ve značce služby spravuje Microsoft, a pokud se adresy změní, automaticky značku služby aktualizuje.

Značky služby Azure Firewall lze použít v poli cíl síťových pravidel. Můžete je použít místo konkrétních IP adres.

## <a name="supported-service-tags"></a>Podporované značky služby

Seznam značek služeb, které jsou k dispozici pro použití v síťových pravidlech brány firewall Azure, najdete v tématu [skupiny zabezpečení](../virtual-network/security-overview.md#service-tags) .

## <a name="next-steps"></a>Další kroky

Další informace o pravidlech Azure Firewall najdete v tématu [logika zpracování pravidel Azure firewall](rule-processing.md).