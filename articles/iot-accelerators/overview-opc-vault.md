---
title: Co je OPC Vault – Azure | Dokumenty společnosti Microsoft
description: Tento článek obsahuje přehled OPC Vault. Může konfigurovat, registrovat a spravovat životní cyklus certifikátů pro aplikace OPC UA v cloudu.
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 66a322d4f60d9553a68207136ae609c1f9b50dbc
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/26/2020
ms.locfileid: "73826202"
---
# <a name="what-is-opc-vault"></a>Co je OPC Vault?

OPC Vault je mikroslužba, která dokáže konfigurovat, registrovat a spravovat životní cyklus certifikátů pro server OPC UA a klientské aplikace v cloudu. Tento článek popisuje jednoduché případy použití OPC Vault.

## <a name="certificate-management"></a>Správa certifikátů

Například výrobní společnost potřebuje připojit svůj serverový počítač OPC UA k nově vytvořené klientské aplikaci. Když výrobce provede počáteční přístup k serveru počítače, zobrazí se okamžitě v aplikaci serveru OPC UA chybová zpráva označující, že klientská aplikace není zabezpečená. Tento mechanismus je postaven v opc UA server stroj, aby se zabránilo neoprávněnému přístupu k aplikacím, který zabraňuje brutální hacking v dílně.

## <a name="application-security-management"></a>Správa zabezpečení aplikací
Odborník na zabezpečení používá mikroslužbu OPC Vault ke snadnému povolení komunikace serveru OPC UA s libovolnou klientskou aplikací, protože OPC Vault má všechny funkce pro správu registru certifikátů, úložiště a životního cyklu. Nyní je OPC UA server bezpečně připojen, může komunikovat s nově vytvořenou klientskou aplikací

## <a name="the-complete-opc-vault-architecture"></a>Kompletní architektura OPC Vault
Následující diagram znázorňuje úplnou architekturu OPC Vault.

![Architektura OPC Vault](media/overview-opc-vault-architecture/opc-vault.png)

## <a name="next-steps"></a>Další kroky

Nyní, když jste se dozvěděli o OPC Vault a jeho použití, zde je navrhl další krok:

> [!div class="nextstepaction"]
> [Architektura OPC Vault](overview-opc-vault-architecture.md)
