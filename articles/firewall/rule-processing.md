---
title: Logika zpracování pravidel Azure Firewall
description: Azure Firewall obsahuje pravidla překladu adres (NAT), pravidla sítě a aplikace. Pravidla se zpracovávají podle typu pravidla.
services: firewall
author: vhorne
ms.service: firewall
ms.topic: article
ms.date: 11/19/2018
ms.author: victorh
ms.openlocfilehash: 0fc11221e0ff79d6e17b93282403792fc135c2a4
ms.sourcegitcommit: 4821b7b644d251593e211b150fcafa430c1accf0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2019
ms.locfileid: "74166791"
---
# <a name="azure-firewall-rule-processing-logic"></a>Logika zpracování pravidel Azure Firewall
Azure Firewall obsahuje pravidla překladu adres (NAT), pravidla sítě a aplikace. Pravidla se zpracovávají podle typu pravidla.


## <a name="network-rules-and-applications-rules"></a>Pravidla sítě a pravidla pro aplikace 
Nejdřív se aplikují Síťová pravidla a pak pravidla aplikace. Pravidla se ukončí. Takže pokud se v síťových pravidlech najde shoda, pravidla aplikací se nezpracují.  Pokud se nenajde shoda s žádným pravidlem sítě a protokol paketu je HTTP nebo HTTPS, paket se vyhodnotí podle pravidel aplikace. Pokud se pořád nenajde žádná shoda, vyhodnotí se paket na základě kolekce pravidel infrastruktury. Pokud se stále nenajde žádná shoda, ve výchozím nastavení se paket odepře.

## <a name="nat-rules"></a>Pravidla NAT
Příchozí připojení je možné povolit konfigurací překladu cílové sítě (DNAT), jak je popsáno v [kurzu: filtrování příchozího provozu pomocí Azure firewall DNAT pomocí Azure Portal](tutorial-firewall-dnat.md). Jako první se aplikují DNAT pravidla. Pokud se najde shoda, přidá se implicitní odpovídající síťové pravidlo, které povolí přeložený provoz. Toto chování můžete přepsat explicitním přidáním kolekce pravidel sítě s pravidly pro odepření, která odpovídají přeloženému provozu. Pro tato připojení nejsou použita žádná pravidla použití aplikace.


## <a name="next-steps"></a>Další kroky

- Přečtěte si, jak [nasadit a nakonfigurovat Azure firewall](tutorial-firewall-deploy-portal.md).