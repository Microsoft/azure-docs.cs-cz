---
title: Řešení potíží s Application Gateway v Azure – služba ASE s ILB | Dokumentace Microsoftu
description: Zjistěte, jak řešit služby application gateway pomocí interního nástroje Load Balancer v Azure App Service Environment
services: vpn-gateway
documentationCenter: na
author: genlin
manager: cshepard
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: 16cfe4c1db8fe9ba4c80f6451611237e3ee12c55
ms.sourcegitcommit: b62f138cc477d2bd7e658488aff8e9a5dd24d577
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/13/2018
ms.locfileid: "51617878"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Certifikát serveru back-end není povolený pro službu application gateway pomocí interního nástroje Load Balancer pomocí služby App Service Environment

Tento článek ho. následující problém: certifikát není na seznamu povolených při vytvoření služby application gateway s využitím interní zatížení nástroje pro vyrovnávání (ILB) společně s App Service Environment (ASE) na back-endu při použití – kompletního protokolu SSL v Azure.

## <a name="symptoms"></a>Příznaky

Když vytvoříte aplikační brány pomocí ILB ASE v back-endu, back-end server může přestat není v pořádku. K tomuto problému dochází, pokud certifikát ověřování služby application gateway pravděpodobně neshoduje s nakonfigurovaným certifikátem na back endového serveru. Jako příklad najdete v následujícím scénáři:

**Konfigurace aplikační brány:**

- **Naslouchací proces:** Multi-Site
- **Port:** 443
- **Název hostitele:** test.appgwtestase.com
- **Certifikát SSL:** CN=test.appgwtestase.com
- **Back-Endového fondu:** IP adresu nebo plně kvalifikovaný název domény
- **IP adresa:**: 10.1.5.11
- **Nastavení protokolu HTTP:** HTTPS
- **Port:**: 443
- **Vlastní test paměti:** název hostitele – test.appgwtestase.com
- **Certifikát pro ověřování:** .cer test.appgwtestase.com
- **Stav back-endu:** není v pořádku – certifikát back-end serveru není na seznamu povolených pomocí služby Application Gateway.

**Konfigurace ASE:**

- **IP ADRESA ILB:** 10.1.5.11
- **Název domény:** appgwtestase.com
- **App Service:** test.appgwtestase.com
- **Vytvoření vazby SSL:** SNI SSL – CN=test.appgwtestase.com

Při přístupu k application gateway zobrazí následující chybová zpráva, protože není v pořádku back-end serverů:

**502 – Webový server obdržel neplatnou odpověď době, kdy fungoval jako brána nebo proxy server.**

## <a name="solution"></a>Řešení

Když nepoužíváte název hostitele pro přístup k webu HTTPS, vrátí back endového serveru nakonfigurovaný certifikát na výchozím webu. Pro službu ASE výchozí certifikát pochází z certifikátu ILB. Pokud neexistují žádné nakonfigurované certifikáty pro ILB, certifikát pochází z certifikát aplikace služby ASE.

Při použití plně kvalifikovaný název domény (FQDN) pro přístup k ILB back endového serveru vrátí správný certifikát, který se odeslal v nastavení protokolu HTTP. V takovém případě zvažte následující možnosti:

- Použijte plně kvalifikovaný název domény v back endovém fondu služby application gateway tak, aby odkazoval na IP adresu ILB. Tato možnost funguje jenom v případě budete mít privátní zóny DNS nebo vlastní DNS nakonfigurovaný. V opačném případě je nutné vytvořit záznam "A" pro veřejná služba DNS.

- Použití se nahraný certifikát na ILB nebo výchozí certifikát v nastavení protokolu HTTP. Application gateway získá certifikát, když přistupuje ILB IP pro test paměti.

- Použijte certifikát se zástupným znakem na ILB a back endového serveru.

- Zrušte **použití pro službu App service** možnost pro službu application gateway.

Aby se snížila režie, můžete nahrát certifikát ILB v nastavení protokolu HTTP, aby cesta testu paměti fungovat. (Tento krok je jen pro přidávání na seznam povolených. To se nepoužije pro komunikaci protokolem SSL.) Můžete načíst certifikát ILB použitím ILB s jeho IP adresu protokolu HTTPS a SSL Export certifikátu v Base-64 kódování, formátu CER a nahrává se certifikát na příslušné nastavení HTTP.

## <a name="need-help-contact-support"></a>Potřebujete pomoct? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
