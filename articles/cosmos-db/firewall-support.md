---
title: Ip brána firewall pro účty Azure Cosmos
description: Zjistěte, jak zabezpečit data Azure Cosmos DB pomocí zásad řízení přístupu IP pro podporu brány firewall.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "66241076"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Brána firewall protokolu IP ve službě Azure Cosmos DB

K zabezpečení dat uložených ve vašem účtu podporuje Azure Cosmos DB tajný autorizační model, který využívá silný kód ověřování zpráv založených na hash (HMAC). Azure Cosmos DB navíc podporuje ovládací prvky přístupu založené na PROTOKOLU IP pro příchozí podporu brány firewall. Tento model je podobný pravidlům brány firewall tradičního databázového systému a poskytuje další úroveň zabezpečení vašeho účtu. Pomocí bran firewall můžete nakonfigurovat svůj účet Azure Cosmos tak, aby byl přístupný jenom ze schválené sady počítačů nebo cloudových služeb. Přístup k datům uloženým v databázi Azure Cosmos z těchto schválených sad počítačů a služeb bude stále vyžadovat, aby volající předložil platný autorizační token.

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>Přehled řízení přístupu k ip adres

Ve výchozím nastavení je váš účet Azure Cosmos přístupný z internetu, pokud je požadavek doprovázen platným tokenem autorizace. Chcete-li nakonfigurovat řízení přístupu na základě zásad IP, musí uživatel zadat sadu IP adres nebo rozsahů IP adres ve formuláři CIDR (Classless Inter-Domain Routing), který má být zahrnut jako povolený seznam klientských IP adres pro přístup k danému účtu Azure Cosmos. Po použití této konfigurace obdrží všechny požadavky pocházející z počítačů mimo tento povolený seznam odpověď 403 (Zakázáno). Při použití IP firewallu se doporučuje povolit Portálu Azure přístup k vašemu účtu. Přístup je nutné povolit použití průzkumníka dat, stejně jako načíst metriky pro váš účet, které se zobrazí na webu Azure Portal. Při použití průzkumníka dat, kromě povolení Azure Portal pro přístup k vašemu účtu, je také potřeba aktualizovat nastavení brány firewall přidat aktuální IP adresu do pravidel brány firewall. Všimněte si, že změny brány firewall může trvat až 15 min k šíření. 

Bránu firewall založenou na protokolu IP můžete kombinovat s řízením přístupu podsítě a virtuální sítě. Jejich kombinací můžete omezit přístup k libovolnému zdroji, který má veřejnou IP adresu nebo z konkrétní podsítě v rámci virtuální sítě. Další informace o používání podsítě a řízení přístupu založenéna virtuální sítě najdete [v tématu Access Azure Cosmos DB prostředky z virtuálních sítí](vnet-service-endpoint.md).

Chcete-li shrnout, autorizační token je vždy nutné pro přístup k účtu Azure Cosmos. Pokud není nastavena brána firewall IP a seznam řízení přístupu virtuální sítě (Seznam adres AC), účet Azure Cosmos je přístupný pomocí autorizačního tokenu. Po IP firewall nebo Virtuální sítě ACL nebo obojí jsou nastaveny na účet Azure Cosmos, pouze požadavky pocházející ze zdrojů, které jste zadali (a s tokenem autorizace) získat platné odpovědi. 

## <a name="next-steps"></a>Další kroky

Dále můžete nakonfigurovat ip firewall nebo koncový bod služby Virtuální sítě pro váš účet pomocí následujících dokumentů:

* [Jak nakonfigurovat bránu firewall IP pro váš účet Azure Cosmos](how-to-configure-firewall.md)
* [Přístup k prostředkům Azure Cosmos DB z virtuálních sítí](vnet-service-endpoint.md)
* [Jak nakonfigurovat koncový bod služby virtuální sítě pro váš účet Azure Cosmos](how-to-configure-vnet-service-endpoint.md)




