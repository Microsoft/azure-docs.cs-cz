---
title: Co je trezor OPC – Azure | Dokumentace Microsoftu
description: Přehled trezoru OPC
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: industrial-iot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: 40a9016ac7a10175b51f0fb6f072dd089bde3a51
ms.sourcegitcommit: f10ae7078e477531af5b61a7fe64ab0e389830e8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/05/2019
ms.locfileid: "67606279"
---
# <a name="what-is-opc-vault"></a>Co je trezor OPC?

OPC trezor je mikroslužeb, která můžete konfigurovat, registrace a správa životního cyklu certifikátu pro server OPC UA a klientských aplikací v cloudu. Tento článek popisuje případech stačí použít trezor OPC.

## <a name="certificate-management"></a>Správa certifikátů

Například výrobní společnost potřebuje pro připojení jejich počítačů serveru OPC UA k jejich nově vytvořený klientská aplikace. Výrobce provádí počáteční přístup počítače serveru, se okamžitě zobrazí chybovou zprávu na aplikace serveru OPC UA k označení, že klientská aplikace není zabezpečený. Tento mechanismus je postavená na počítači serveru OPC UA zabránit Nepovolená aplikace přístup, což zabrání zákeřnému pracovat na dílně.

## <a name="application-security-management"></a>Správa zabezpečení aplikací
Profesionální zabezpečení používá trezor OPC mikroslužeb snadno povolit server OPC UA pro komunikaci s všechny klientské aplikace, protože trezor OPC má všechny funkce pro certifikát registru, ukládání a správa životního cyklu. Teď je bezpečné připojení serveru OPC UA, může komunikovat na nově vytvořený klientské aplikace

## <a name="the-complete-opc-vault-architecture"></a>Kompletní architekturu OPC trezoru
Následující diagram znázorňuje kompletní architekturu OPC trezoru.

![Architektura OPC trezoru](media/overview-opc-vault-architecture/opc-vault.png)