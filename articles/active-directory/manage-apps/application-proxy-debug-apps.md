---
title: Ladění aplikace Proxy aplikací – Azure Active Directory | Dokumentace Microsoftu
description: Ladění problémů s Proxy aplikací Azure Active Directory (Azure AD) aplikace.
services: active-directory
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: conceptual
ms.date: 05/21/2019
ms.author: mimart
ms.reviewer: japere
ms.openlocfilehash: d0a12bde119e9dae3f950603fac4bce060bb5f91
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66172262"
---
# <a name="debug-application-proxy-application-issues"></a>Ladění problémů v aplikacích Proxy aplikací 

Tento článek pomáhá řešit problémy s aplikací Proxy aplikací Azure Active Directory (Azure AD). Pokud používáte službu Proxy aplikací pro vzdálený přístup k webové aplikaci lokálně, ale máte potíže s připojením k aplikaci, použijte tento vývojový diagram pro ladění problémů v aplikacích. 

## <a name="before-you-begin"></a>Než začnete

Při řešení potíží s Proxy aplikací, doporučujeme že začít s konektory. Nejprve, postupujte podle řešení problémů s tok v [ladění Application Proxy Connector vystavuje](application-proxy-debug-connectors.md) k Ujistěte se, že jsou správně nakonfigurované konektory Proxy aplikací. Pokud stále máte problémy, vraťte k tomuto článku řešení potíží s aplikace.  

Další informace o Proxy aplikací najdete v tématu:

- [Vzdálený přístup k místním aplikacím přes Proxy aplikací](application-proxy.md)
- [Konektory Proxy aplikací](application-proxy-connectors.md)
- [Instalace a registrace konektoru](application-proxy-add-on-premises-application.md)
- [Poradce při potížích s Proxy aplikací problémy a chybové zprávy](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Vývojový diagram pro problémů v aplikacích

Tato vývojový diagram vás provede kroky pro ladění některé běžné problémy s připojením k aplikaci. Podrobnosti o jednotlivých kroků najdete v tabulce následujících vývojový diagram.

![Vývojový diagram znázorňující postup pro ladění aplikace](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Akce | Popis | 
|---------|---------|---------|
|1 | Otevřete prohlížeč, přístup k aplikaci a zadejte svoje přihlašovací údaje | Zkuste použít vaše přihlašovací údaje pro přihlášení k aplikaci a zkontrolujte chyby související s uživateli, jako je třeba [nelze získat přístup k této podnikové aplikace](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Ověření přiřazení uživatele k aplikaci | Ujistěte se, že váš uživatelský účet má oprávnění pro přístup k aplikaci uvnitř podnikové sítě a poté otestujte přihlášení k aplikaci pomocí následujících kroků v [testování aplikace](application-proxy-add-on-premises-application.md#test-the-application). Pokud problémy s přihlašováním vyskytovat i dál, přečtěte si téma [řešení potíží s chybami přihlášení](https://docs.microsoft.com/azure/active-directory/reports-monitoring/howto-troubleshoot-sign-in-errors).  |
|3 | Otevřete prohlížeč a zkuste přístup k aplikaci | Pokud okamžitě se zobrazí chyba, zkontrolujte, jestli je správně nakonfigurovaný Proxy aplikací. Podrobnosti o určité chybové zprávy najdete v tématu [Poradce při potížích s Proxy aplikací problémy a chybové zprávy](application-proxy-troubleshoot.md).  |
|4 | Kontrola nastavení vlastní domény nebo řešení potíží s chybou | Pokud na stránce vůbec nezobrazí, ujistěte se, že vaše vlastní doména je správně nakonfigurován kontrolou [práce s vlastními doménami](application-proxy-configure-custom-domain.md).<br></br>Pokud se zobrazí chybová zpráva na stránku nenačte, vyřešte chybu rekapitulací [Poradce při potížích s Proxy aplikací problémy a chybové zprávy](application-proxy-troubleshoot.md). <br></br>Pokud to trvá déle, než 20 sekund, zobrazí se chybová zpráva, může být problém s připojením. Přejděte [konektorů Proxy aplikace služby ladění](application-proxy-debug-connectors.md) článek pro řešení potíží.  |
|5 | Pokud problémy přetrvávají, přejděte k ladění konektoru | Může existovat problém s připojením mezi proxy serveru a konektoru nebo mezi konektoru a back-endu. Přejděte [konektorů Proxy aplikace služby ladění](application-proxy-debug-connectors.md) článek pro řešení potíží. |
|6 | Publikovat všechny prostředky, zkontrolujte Prohlížeč vývojářské nástroje a opravit odkazy | Ujistěte se, že cesta publikování obsahuje všechny nezbytné obrázky, skripty a šablony stylů pro vaši aplikaci. Podrobnosti najdete v tématu [přidat místní aplikace do služby Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Použití prohlížeče vývojářské nástroje (nástrojů F12 v aplikaci Internet Explorer nebo Microsoft Edge) a kontrola pro publikování problémy, jak je popsáno v [stránka aplikace se nezobrazuje správně](application-proxy-page-appearance-broken-problem.md). <br></br>Projděte si možnosti pro řešení nefunkční odkazy ve [nefungují odkazy na stránce](application-proxy-page-links-broken-problem.md). |
|7 | Kontrola latence sítě | Pokud se stránka načte pomalu, přečtěte si o způsobech, jakými kvůli minimalizaci latence sítě v [důležité informace pro snížení latence](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Zobrazit další nápovědu k řešení potíží | Pokud problémy přetrvávají, najít odkazy na další články v [Proxy aplikací dokumentace k řešení problémů](application-proxy-page-appearance-broken-problem.md). |

## <a name="next-steps"></a>Další postup


* [Publikování aplikací na samostatných sítí a umístění s využitím skupiny konektorů](application-proxy-connector-groups.md)
* [Práce s existující místní proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)
* [Řešení potíží s chybami Proxy aplikací a konektoru](application-proxy-troubleshoot.md)
* [Postup při bezobslužné instalaci Azure AD Application Proxy Connector](application-proxy-register-connector-powershell.md)
