---
title: Poradce při potížích s aplikační bránou v Azure – služba ASE ILB | Dokumenty společnosti Microsoft
description: Zjistěte, jak řešit potíže s aplikační bránou pomocí interního vyrovnávání zatížení s prostředím služby App Service v Azure
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
ms.date: 11/06/2018
ms.author: genli
ms.openlocfilehash: 4edeea749ba22bef173c15f3a0855679b784ce33
ms.sourcegitcommit: 67addb783644bafce5713e3ed10b7599a1d5c151
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/05/2020
ms.locfileid: "80668559"
---
# <a name="back-end-server-certificate-is-not-whitelisted-for-an-application-gateway-using-an-internal-load-balancer-with-an-app-service-environment"></a>Certifikát serveru back-end není na seznamu povolených pro aplikační bránu používající interní vyrovnávání zatížení s prostředím služby App Service

Tento článek řeší následující problém: Certifikát není na seznamu povolených při vytváření brány aplikace pomocí interní vyrovnávání zatížení (ILB) spolu s prostředím služby App Service (ASE) na back-end při použití end-to-end TLS v Azure.

## <a name="symptoms"></a>Příznaky

Při vytváření brány aplikace pomocí ILB se službou ASE na back-endu, server back-end se může stát není v pořádku. K tomuto problému dochází, pokud ověřovací certifikát aplikační brány neodpovídá nakonfigurovaný certifikát na serveru back-end. Viz následující scénář jako příklad:

**Konfigurace aplikační brány:**

- **Posluchač:** Více míst
- **Přístav:** 443
- **Název hostitele:** test.appgwtestase.com
- **Certifikát SSL:** CN = test.appgwtestase.com
- **Fond back-endu:** IP adresa nebo vícenežony
- **IP adresa:**: 10.1.5.11
- **Nastavení protokolu HTTP:** Protokol HTTPS
- **Přístav:**: 443
- **Vlastní sonda:** Název hostitele – test.appgwtestase.com
- **Ověřovací certifikát:** .cer test.appgwtestase.com
- **Stav back-endu:** Není v pořádku – back-endový certifikát serveru není na seznamu povolených s aplikační bránou.

**Konfigurace služby ASE:**

- **IP ILB:** 10.1.5.11
- **Název domény:** appgwtestase.com
- **Služba aplikace:** test.appgwtestase.com
- **Vazba SSL:** SNI SSL – CN=test.appgwtestase.com

Při přístupu k bráně aplikace se zobrazí následující chybová zpráva, protože server back-end není v pořádku:

**502 – Webový server obdržel neplatnou odpověď při jednání jako brána nebo proxy server.**

## <a name="solution"></a>Řešení

Pokud nepoužíváte název hostitele pro přístup k webu HTTPS, server back-end vrátí nakonfigurovaný certifikát na výchozí web v případě, že je SNI zakázáno. Pro službu ASE ILB výchozí certifikát pochází z certifikátu ILB. Pokud pro ILB nejsou žádné nakonfigurované certifikáty, certifikát pochází z certifikátu aplikace ASE.

Pokud pro přístup k ILB použijete plně kvalifikovaný název domény (FQDN), vrátí server back-end správný certifikát, který je odeslán v nastavení protokolu HTTP. Pokud tomu tak není , zvažte následující možnosti:

- Pomocí hlavního název souboru Sekvšak v back-endovém fondu aplikační brány přejděte na IP adresu ILB. Tato možnost funguje pouze v případě, že máte nakonfigurovanou soukromou zónu DNS nebo vlastní službu DNS. V opačném případě budete muset vytvořit záznam "A" pro veřejné DNS.

- V nastavení protokolu HTTP použijte nahraný certifikát na ilb nebo výchozí certifikát (certifikát ILB). Aplikační brána získá certifikát při přístupu k IP ip ilb pro sondu.

- Použijte certifikát se zástupnými symboly na serveru ILB a back-end, takže pro všechny weby je certifikát běžný. Toto řešení je však možné pouze v případě subdomén a ne v případě, že každý z webových stránek vyžaduje různé názvy hostitelů.

- Zrušte zaškrtnutí možnosti **Použít pro aplikaci** pro aplikační bránu v případě, že používáte IP adresu ILB.

Chcete-li snížit režii, můžete nahrát certifikát ILB v nastavení PROTOKOLU HTTP, aby cesta sondy fungovala. (Tento krok je jen pro whitelisting. Nebude použit pro komunikaci TLS.) Certifikát ILB můžete načíst tak, že z prohlížeče v protokolu HTTPS načtete ilb s jeho IP adresou a potom exportujete certifikát TLS/SSL ve formátu CER s kódováním Base-64 a nahrajete certifikát v příslušném nastavení PROTOKOLU HTTP.

## <a name="need-help-contact-support"></a>Potřebujete pomoc? Kontaktování podpory

Pokud stále potřebujete pomoc, [obraťte se na podporu](https://portal.azure.com/?#blade/Microsoft_Azure_Support/HelpAndSupportBlade) a ta vám pomůže váš problém rychle vyřešit.
