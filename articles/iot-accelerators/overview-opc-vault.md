---
title: Co je Azure IoT OPC UA certificate managementu | Dokumentace Microsoftu
description: Přehled trezoru OPC
author: dominicbetts
ms.author: dobett
ms.date: 11/26/2018
ms.topic: overview
ms.service: iot-industrialiot
services: iot-industrialiot
manager: philmea
ms.openlocfilehash: ba3313d927ec4317d6c051f6058d75a415b92288
ms.sourcegitcommit: 563f8240f045620b13f9a9a3ebfe0ff10d6787a2
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/01/2019
ms.locfileid: "58759248"
---
# <a name="what-is-azure-iot-open-platform-communications-opc-ua-certificate-management"></a>Co je Správa certifikátů UA Azure IoT otevřené platformy komunikací OPC ()?

Azure IoT OPC UA Správa certifikátů a také znát pod některým z trezoru OPC, mikroslužby, můžete nakonfigurovat registr, je správa životního cyklu certifikátu pro OPC UA serverových a klientských aplikací v cloudu. Tento článek popisuje případech stačí použít trezor OPC.

## <a name="certificate-management"></a>Správa certifikátů

Například výrobní společnost potřebuje pro připojení jejich počítačů serveru OPC UA k jejich nově vytvořený klientská aplikace. Výrobce provádí počáteční přístup počítače serveru, se okamžitě zobrazí chybovou zprávu na aplikace serveru OPC UA k označení, že klientská aplikace není zabezpečený. Tento mechanismus je postavená na počítači serveru OPC UA zabránit Nepovolená aplikace přístup, což zabrání zákeřnému pracovat na dílně.

## <a name="application-security-management"></a>Správa zabezpečení aplikací
Profesionální zabezpečení používá trezor OPC mikroslužeb snadno povolit server OPC UA pro komunikaci s všechny klientské aplikace, protože trezor OPC má všechny funkce pro certifikát registru, ukládání a správa životního cyklu. Teď je bezpečné připojení serveru OPC UA, může komunikovat na nově vytvořený klientské aplikace

## <a name="the-complete-opc-vault-architecture"></a>Kompletní architekturu OPC trezoru
Následující diagram znázorňuje kompletní architekturu OPC trezoru.

![Architektura OPC trezoru](media/overview-opc-vault-architecture/opc-vault.png)