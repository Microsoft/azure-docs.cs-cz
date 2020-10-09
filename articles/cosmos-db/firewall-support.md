---
title: Brána firewall protokolu IP pro účty Azure Cosmos
description: Naučte se zabezpečit Azure Cosmos DB data pomocí zásad řízení přístupu IP pro podporu brány firewall.
author: kanshiG
ms.service: cosmos-db
ms.topic: conceptual
ms.date: 05/23/2019
ms.author: govindk
ms.openlocfilehash: 9398eb4038afcd17788e750fcb5c27c76e9f3f44
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "66241076"
---
# <a name="ip-firewall-in-azure-cosmos-db"></a>Brána firewall protokolu IP ve službě Azure Cosmos DB

Aby bylo možné zabezpečit data uložená ve vašem účtu, Azure Cosmos DB podporuje model autorizace založený na tajnosti, který využívá algoritmus HMAC (Strong-based Message Authentication Code). Kromě toho Azure Cosmos DB podporuje řízení přístupu na základě IP adres pro podporu brány firewall pro příchozí připojení. Tento model je podobný pravidlům brány firewall tradičního databázového systému a poskytuje další úroveň zabezpečení pro váš účet. Díky bránám firewall můžete nakonfigurovat účet Azure Cosmos tak, aby byl přístupný jenom ze schválené sady počítačů a/nebo cloudových služeb. Přístup k datům uloženým v databázi Azure Cosmos z těchto schválených sad počítačů a služeb bude nadále vyžadovat, aby volající předložil platný autorizační token.

## <a name="ip-access-control-overview"></a><a id="ip-access-control-overview"></a>Přehled řízení přístupu IP

Ve výchozím nastavení je váš účet Azure Cosmos přístupný z Internetu, pokud je žádost doprovázena platným autorizačním tokenem. Pokud chcete nakonfigurovat řízení přístupu na základě zásad protokolu IP, musí uživatel pro přístup k danému účtu Azure Cosmos zadat sadu IP adres nebo rozsahů IP adres ve formuláři CIDR (bez třídy a směrování Inter-Domain). Po použití této konfigurace obdrží všechny požadavky pocházející z počítačů mimo tento seznam povolených odpovědí 403 (zakázáno). Pokud používáte bránu firewall protokolu IP, doporučuje se, aby Azure Portal k vašemu účtu. Přístup je nutný k umožnění použití Průzkumníku dat a k načtení metrik pro váš účet, který se zobrazí na Azure Portal. Pokud používáte Průzkumníka dat a povolíte Azure Portal přístup k vašemu účtu, budete také muset aktualizovat nastavení brány firewall a přidat tak aktuální IP adresu do pravidel brány firewall. Všimněte si, že změny v bráně firewall můžou trvat až 15 minut. 

Bránu firewall založenou na protokolu IP můžete kombinovat s podsítí a řízením přístupu VNET. Kombinací těchto objektů můžete omezit přístup k jakémukoli zdroji, který má veřejnou IP adresu a/nebo z konkrétní podsítě v rámci virtuální sítě. Další informace o použití podsítí a řízení přístupu na základě virtuální sítě najdete v tématu [přístup k prostředkům Azure Cosmos DB z virtuálních sítí](vnet-service-endpoint.md).

K sumarizaci se pro přístup k účtu Azure Cosmos vždy vyžaduje autorizační token. Pokud nejsou nastavené brány firewall a seznam povolených IP adres Access Control (ACL), můžete k účtu Azure Cosmos přístup pomocí autorizačního tokenu. Po nastavení brány firewall IP nebo seznamů ACL pro virtuální síť nebo obou se nastaví v účtu Azure Cosmos jenom požadavky pocházející ze zadaných zdrojů (a pomocí autorizačního tokenu) a získají platné odpovědi. 

## <a name="next-steps"></a>Další kroky

Dále můžete pro svůj účet nakonfigurovat bránu firewall IP nebo koncový bod služby virtuální sítě pomocí následujících dokumentů:

* [Jak nakonfigurovat bránu firewall protokolu IP pro účet Azure Cosmos](how-to-configure-firewall.md)
* [Přístup k prostředkům Azure Cosmos DB z virtuálních sítí](vnet-service-endpoint.md)
* [Jak nakonfigurovat koncový bod služby virtuální sítě pro účet Azure Cosmos](how-to-configure-vnet-service-endpoint.md)




