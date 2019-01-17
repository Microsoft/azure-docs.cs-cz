---
title: Brány firewall protokolu IP pro účty služby Azure Cosmos
description: Zjistěte, jak zabezpečit data Azure Cosmos DB pomocí zásady řízení přístupu IP pro podporu brány firewall.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 11/06/2018
ms.author: govindk
ms.openlocfilehash: 2bb3665f0d0ab15f212741a6ce032fcd7ac2620d
ms.sourcegitcommit: a408b0e5551893e485fa78cd7aa91956197b5018
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/17/2019
ms.locfileid: "54358109"
---
# <a name="ip-firewall-for-azure-cosmos-accounts"></a>Brány firewall protokolu IP pro účty služby Azure Cosmos

K zabezpečení dat uložených v účtu Azure Cosmos DB podporuje tajného kódu autorizace na základě modelu, který využívá silné Hash-based kódu (metoda HMAC Message Authentication). Kromě toho Azure Cosmos DB podporuje řízení přístupu na základě IP adresy pro podporu brány firewall pro příchozí. Tento model je podobný pravidla brány firewall tradiční databázový systém a poskytuje další úroveň zabezpečení ke svému účtu. S branami firewall můžete nakonfigurovat svůj účet Azure Cosmos a být přístupná jenom schválenou sadu počítačů nebo cloudových služeb. Přístup k datům uloženým v databázi Azure Cosmos z těchto schválených sad počítače a služby se stále vyžadují volající předložit platný autorizační token.

## <a id="ip-access-control-overview"></a>Přehled řízení přístupu IP

Ve výchozím nastavení je přístupný z Internetu, váš účet Azure Cosmos, za předpokladu, žádosti je přiložený platný autorizační token. Ke konfiguraci řízení přístupu na základě zásad IP musí uživatel poskytnout sadu IP adres nebo rozsahy IP adres ve formátu CIDR (Classless Inter-Domain se směrováním). zahrnou se jako seznam povolených klientských IP adres pro přístup k dané účtu Azure Cosmos. Po této konfigurace se použije, všechny požadavky z počítačů mimo tento seznam povolených obdržet odpověď 403 (zakázáno). Při použití brány firewall protokolu IP, se doporučuje povolit webu Azure portal přístup ke svému účtu. Se vyžaduje k povolení použití Průzkumníku dat také tak, aby načíst metriky pro svůj účet, který se zobrazí na portálu Azure portal přístup.

Na základě IP adresy brány firewall můžete kombinovat s podsítí a řízení přístupu k virtuální síti. Díky spojení jejich, můžete omezit přístup k libovolnému zdroji, který má veřejnou IP adresu a/nebo z konkrétní podsítě v rámci virtuální sítě. Další informace o používání podsítě a řízení přístupu založené na virtuální síť najdete v článku [prostředky přístup k Azure Cosmos DB z virtuálních sítí](vnet-service-endpoint.md).

Souhrnně řečeno, je vždy vyžadován autorizační token pro přístup k účtu Azure Cosmos. Pokud brány firewall protokolu IP a virtuální síť seznam řízení přístupu (ACL) nejsou nastaveny, účet Azure Cosmos můžete přistupovat pomocí autorizační token. Po Brány firewall protokolu IP nebo virtuální sítě ACL nebo obojí jsou nastavené v účtu Azure Cosmos, získat jenom žádosti pocházející ze zdroje, které jste zadali (a autorizačním tokenem) platné odpovědi. 

## <a name="next-steps"></a>Další postup

Potom můžete nakonfigurovat brány firewall protokolu IP nebo koncový bod služby virtuální sítě pro váš účet, pomocí následující dokumentace:

* [Postup konfigurace brány firewall protokolu IP pro váš účet Azure Cosmos](how-to-configure-firewall.md)
* [Přístup k prostředkům služby Azure Cosmos DB z virtuální sítě](vnet-service-endpoint.md)
* [Konfigurace koncového bodu služby virtuální sítě pro váš účet Azure Cosmos](how-to-configure-vnet-service-endpoint.md)




