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
ms.openlocfilehash: ad52d2b1df458d04a1ca9bd52a99bab38ddabef1
ms.sourcegitcommit: f7be3cff2cca149e57aa967e5310eeb0b51f7c77
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/15/2019
ms.locfileid: "56308577"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Certifikát serveru back-end není povolený pro službu application gateway pomocí interního nástroje Load Balancer pomocí služby App Service Environment

Tento článek ho. následující problémy: Certifikát není na seznamu povolených, při vytváření služby application gateway s využitím interní zatížení nástroje pro vyrovnávání (ILB) společně s App Service Environment (ASE) na back-endu při použití – kompletního protokolu SSL v Azure.

## <a name="symptoms"></a>Příznaky

Když vytvoříte aplikační brány pomocí ILB ASE v back-endu, back-end server může přestat není v pořádku. K tomuto problému dochází, pokud certifikát ověřování služby application gateway pravděpodobně neshoduje s nakonfigurovaným certifikátem na back endového serveru. Jako příklad najdete v následujícím scénáři:

**Konfigurace aplikační brány:**

- **Naslouchací proces:** Pro více lokalit
- **Port:** 443
- **Hostname:** test.appgwtestase.com
- **Certifikát SSL:** CN=test.appgwtestase.com
- **Back-Endového fondu:** IP adresa nebo plně kvalifikovaný název domény
- **IP adresa:**: 10.1.5.11
- **Nastavení protokolu HTTP:** HTTPS
- **Port:**: 443
- **Vlastní test paměti:** Název hostitele – test.appgwtestase.com
- **Certifikát pro ověřování:** .cer test.appgwtestase.com
- **Stav back-endu:** Není v pořádku – certifikát back-end serveru není na seznamu povolených pomocí služby Application Gateway.

**Konfigurace ASE:**

- **ILB IP:** 10.1.5.11
- **Název domény:** appgwtestase.com
- **App Service:** test.appgwtestase.com
- **Vytvoření vazby SSL:** SNI SSL – CN=test.appgwtestase.com

Při přístupu k application gateway zobrazí následující chybová zpráva, protože není v pořádku back-end serverů:

**502 – Webový server obdržel neplatnou odpověď době, kdy fungoval jako brána nebo proxy server.**

## <a name="solution"></a>Řešení

Když nepoužíváte název hostitele pro přístup k webu HTTPS, back endového serveru vrátí nakonfigurovaným certifikátem na výchozím webu, v případě, že SNI je zakázaná. Pro službu ASE výchozí certifikát pochází z certifikátu ILB. Pokud neexistují žádné nakonfigurované certifikáty pro ILB, certifikát pochází z certifikát aplikace služby ASE.

Při použití plně kvalifikovaný název domény (FQDN) pro přístup k ILB back endového serveru vrátí správný certifikát, který se odeslal v nastavení protokolu HTTP. Pokud tedy tomu tak není, zvažte následující možnosti:

- Použijte plně kvalifikovaný název domény v back endovém fondu služby application gateway tak, aby odkazoval na IP adresu ILB. Tato možnost funguje jenom v případě budete mít privátní zóny DNS nebo vlastní DNS nakonfigurovaný. V opačném případě je nutné vytvořit záznam "A" pro veřejná služba DNS.

- Použití se nahraný certifikát na ILB nebo výchozí certifikát (ILB) v nastavení protokolu HTTP. Application gateway získá certifikát, když přistupuje ILB IP pro test paměti.

- Použijte certifikát se zástupným znakem na ILB a back endového serveru tak, aby pro všechny weby, certifikát je běžné. Toto řešení je ale možné jenom v případě subdomény a ne v případě, že všechny weby vyžadují různé názvy hostitelů.

- Zrušte **použití pro službu App service** možnost pro službu application gateway v případě, že používáte IP adresu ILB.

Aby se snížila režie, můžete nahrát certifikát ILB v nastavení protokolu HTTP, aby cesta testu paměti fungovat. (Tento krok je jen pro přidávání na seznam povolených. To se nepoužije pro komunikaci protokolem SSL.) Můžete načíst certifikát ILB použitím ILB s jeho IP adresa v prohlížeči na adrese HTTPS a export certifikátu protokolu SSL v Base-64 kódování formátu CER a nahrává se certifikát na příslušné nastavení HTTP.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) pro rychlé vyřešení problému.
