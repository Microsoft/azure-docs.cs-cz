---
title: Konfigurace front-endové IP adresy pro Azure Application Gateway
description: Tento článek popisuje, jak nakonfigurovat front-end IP adresu Azure Application Gateway.
services: application-gateway
author: vhorne
ms.service: application-gateway
ms.topic: conceptual
ms.date: 09/09/2020
ms.author: surmb
ms.openlocfilehash: eff63510f70dd7b4cdd522cc5a2a68096cda7166
ms.sourcegitcommit: e6de1702d3958a3bea275645eb46e4f2e0f011af
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102548712"
---
# <a name="application-gateway-front-end-ip-address-configuration"></a>Application Gateway konfigurace front-endové IP adresy

Aplikační bránu můžete nakonfigurovat tak, aby měla veřejnou IP adresu, soukromou IP adresu nebo obojí. Veřejná IP adresa se vyžaduje při hostování back-endu, ke kterému musí klienti přistupovat přes Internet prostřednictvím internetové virtuální IP adresy (VIP).

## <a name="public-and-private-ip-address-support"></a>Podpora veřejných a privátních IP adres

Application Gateway V2 v současné době nepodporuje pouze režim privátních IP adres. Podporuje následující kombinace:

* Privátní IP adresa a veřejná IP adresa
* Jenom veřejná IP adresa

Další informace najdete v článku [Nejčastější dotazy k Application Gateway](application-gateway-faq.yml#how-do-i-use-application-gateway-v2-with-only-private-frontend-ip-address).


Veřejná IP adresa není nutná pro interní koncový bod, který není vystavený pro Internet. To se říká jako koncový bod *interního nástroje pro vyrovnávání zatížení* (interního nástroje) nebo privátní IP adresa front-endu. Služba Application Gateway interního nástroje je užitečná pro interní obchodní aplikace, které nejsou přístupné pro Internet. Je to také užitečné pro služby a vrstvy v rámci hranice zabezpečení, které nejsou přístupné z Internetu, ale vyžadují distribuci zatížení kruhového dotazování, vytrvalost relace nebo ukončení protokolu TLS.

Podporovaná je jenom jedna veřejná IP adresa a jedna privátní IP adresa. Při vytváření aplikační brány zvolíte front-end IP adresu.

- U veřejné IP adresy můžete vytvořit novou veřejnou IP adresu nebo použít stávající veřejnou IP adresu ve stejném umístění jako Aplikační brána. Další informace najdete v tématu [statická a dynamická veřejná IP adresa](./application-gateway-components.md#static-versus-dynamic-public-ip-address).

- U privátních IP adres můžete zadat privátní IP adresu z podsítě, ve které je Aplikační brána vytvořená. Pokud nezadáte žádnou hodnotu, z podsítě se automaticky vybere Libovolná IP adresa. Vybraný typ IP adresy (static nebo Dynamic) nelze později změnit. Další informace najdete v tématu [Vytvoření aplikační brány s interním nástrojem pro vyrovnávání zatížení](./application-gateway-ilb-arm.md).

Front-end IP adresa je přidružená k *naslouchacího procesu*, který kontroluje příchozí požadavky na front-endové IP adresu.

## <a name="next-steps"></a>Další kroky

- [Informace o konfiguraci naslouchacího procesu](configuration-listeners.md)
