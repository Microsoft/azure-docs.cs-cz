---
title: Řešení potíží s Application Gateway v Azure – interního nástroje pomocného Průvodce | Microsoft Docs
description: Přečtěte si, jak řešit potíže s aplikační bránou pomocí interní Load Balancer s App Service Environment v Azure.
services: vpn-gateway
documentationCenter: na
author: genlin
manager: dcscontentpm
editor: ''
tags: ''
ms.service: vpn-gateway
ms.devlang: na
ms.topic: troubleshooting
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 06/09/2020
ms.author: genli
ms.openlocfilehash: 8861e850e168169762d95c44a54b6a88a036f396
ms.sourcegitcommit: 772eb9c6684dd4864e0ba507945a83e48b8c16f0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "84628532"
---
# <a name="back-end-server-certificate-is-not-allow-listed-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Certifikát back-end serveru není povolený v seznamu pro aplikační bránu pomocí interní Load Balancer s App Service Environment

Tento článek řeší následující problém: certifikát není povolený, když vytváříte Aplikační bránu pomocí interního Load Balancer (interního nástroje) společně s App Service Environmentem (pomocným mechanismem) na back-endu při použití kompletního TLS v Azure.

## <a name="symptoms"></a>Příznaky

Když vytvoříte Aplikační bránu pomocí interního nástroje s pomocným mechanismem na back-endu, back-end Server může být ve špatném stavu. K tomuto problému dochází v případě, že ověřovací certifikát služby Application Gateway neodpovídá nakonfigurovanému certifikátu na back-end serveru. Podívejte se na následující scénář jako příklad:

**Konfigurace Application Gateway:**

- **Naslouchací proces:** Více lokalit
- **Port:** 443
- **Název hostitele:** test.appgwtestase.com
- **Certifikát SSL:** CN = test. appgwtestase. com
- **Back-end fond:** IP adresa nebo plně kvalifikovaný název domény
- **IP adresa:**: 10.1.5.11
- **Nastavení http:** HTTPS
- **Port:**: 443
- **Vlastní test paměti:** Název hostitele – test.appgwtestase.com
- **Ověřovací certifikát:** cer z test.appgwtestase.com
- **Stav back-endu:** Stav není v pořádku – certifikát back-end serveru není povolený v seznamu s Application Gateway.

**Konfigurace pomocného mechanismu:**

- **Interního nástroje IP adresa:** 10.1.5.11
- **Název domény:** appgwtestase.com
- **App Service:** test.appgwtestase.com
- **Vazba SSL:** SNI SSL – CN = test. appgwtestase. com

Při přístupu k aplikační bráně se zobrazí následující chybová zpráva, protože back-end Server není v pořádku:

**502 – webový server obdržel neplatnou odpověď v době, kdy se jednalo o bránu nebo proxy server.**

## <a name="solution"></a>Řešení

Pokud pro přístup k webu HTTPS nepoužíváte název hostitele, back-end server vrátí nakonfigurovaný certifikát na výchozím webu, v případě, že je SNI zakázaný. Pro interního nástroje pomocného nástroje se z certifikátu interního nástroje dostane výchozí certifikát. Pokud pro interního nástroje nejsou žádné nakonfigurované certifikáty, pochází certifikát z certifikátu aplikace pomocného mechanismu.

Pokud pro přístup k interního nástroje použijete plně kvalifikovaný název domény (FQDN), back-end server vrátí správný certifikát, který se nahrál v nastavení HTTP. V takovém případě zvažte následující možnosti:

- Pomocí plně kvalifikovaného názvu domény v rámci fondu back-end služby Application Gateway ukažte na IP adresu interního nástroje. Tato možnost funguje jenom v případě, že máte nakonfigurovanou privátní zónu DNS nebo vlastní DNS. V opačném případě je nutné vytvořit záznam "A" pro veřejnou službu DNS.

- V nastavení HTTP použijte nahraný certifikát na interního nástroje nebo výchozí certifikát (interního nástroje certifikát). Aplikační brána získá certifikát, když přistupuje k IP adrese interního nástroje pro test.

- Použijte certifikát se zástupným znakem na interního nástroje a back-end Server, aby se pro všechny weby používal certifikát běžným způsobem. Toto řešení je však možné pouze v případě subdomén, a ne v případě, že každý z webů vyžaduje různé názvy hostitelů.

- Pokud používáte IP adresu interního nástroje, zrušte zaškrtnutí možnosti **použít pro službu App Service pro službu** Application Gateway.

Chcete-li snížit režii, můžete nahrát certifikát interního nástroje v nastavení protokolu HTTP, aby bylo možné cestu k testu použít. (Tento krok je jenom pro seznam povolených. Nebude se používat pro komunikaci TLS.) Certifikát interního nástroje můžete načíst tak, že přistupujete k interního nástroje s IP adresou z prohlížeče v protokolu HTTPS a potom exportujete certifikát TLS/SSL ve formátu CER se základními 64 a nahrajete certifikát na příslušné nastavení HTTP.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.
