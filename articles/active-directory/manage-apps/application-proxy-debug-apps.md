---
title: Ladění aplikací proxy aplikací – služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Problémy s laděním s aplikacemi proxy aplikací azure active directory (Azure AD).
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
ms.openlocfilehash: 575891d99c077299f5e7abf008c1ebb2b158373f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74382071"
---
# <a name="debug-application-proxy-application-issues"></a>Ladění problémů s aplikací Proxy aplikací 

Tento článek vám pomůže vyřešit problémy s aplikacemi proxy aplikací Azure Active Directory (Azure AD). Pokud používáte službu Proxy aplikace pro vzdálený přístup k místní webové aplikaci, ale máte potíže s připojením k aplikaci, použijte tento vývojový diagram k ladění problémů s aplikací. 

## <a name="before-you-begin"></a>Než začnete

Při řešení problémů s proxy aplikací doporučujeme začít s konektory. Nejprve postupujte podle toku řešení potíží s [laděním konektoru proxy aplikací](application-proxy-debug-connectors.md) a ujistěte se, že konektory proxy aplikací jsou správně nakonfigurovány. Pokud potíže přetrvávají, vraťte se k tomuto článku a vyřešte aplikaci.  

Další informace o proxy aplikaci naleznete v tématu:

- [Vzdálený přístup k místním aplikacím prostřednictvím proxy aplikace](application-proxy.md)
- [Konektory proxy aplikací](application-proxy-connectors.md)
- [Instalace a registrace konektoru](application-proxy-add-on-premises-application.md)
- [Poradce při potížích s proxy aplikacemi a chybové zprávy](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Vývojový diagram pro problémy s aplikacemi

Tento vývojový diagram vás provede kroky pro ladění některé z více běžných problémů s připojením k aplikaci. Podrobnosti o jednotlivých krocích naleznete v tabulce za vývojovým diagramem.

![Vývojový diagram znázorňující kroky pro ladění aplikace](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

|  | Akce | Popis | 
|---------|---------|---------|
|1 | Otevření prohlížeče, přístup k aplikaci a zadání přihlašovacích údajů | Zkuste se přihlásit k aplikaci pomocí přihlašovacích údajů a zkontrolujte, zda nejsou k chybám souvisejícím s uživatelem, jako [je tato podniková aplikace, která není přístupná](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Ověření přiřazení uživatele k aplikaci | Ujistěte se, že váš uživatelský účet má oprávnění k přístupu k aplikaci zevnitř podnikové sítě a pak otestujte přihlášení k aplikaci podle kroků v [části Testování aplikace](application-proxy-add-on-premises-application.md#test-the-application). Pokud problémy s přihlášením přetrvávají, [přečtěte si článek Jak vyřešit chyby přihlášení](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).  |
|3 | Otevření prohlížeče a pokus o přístup k aplikaci | Pokud se chyba zobrazí okamžitě, zkontrolujte, zda je server Proxy aplikace správně nakonfigurován. Podrobnosti o konkrétních chybových zprávách naleznete [v tématu Poradce při potížích s proxy aplikací a chybových zprávách](application-proxy-troubleshoot.md).  |
|4 | Zkontrolujte vlastní nastavení domény nebo řešení potíží s chybou | Pokud se stránka vůbec nezobrazuje, zkontrolujte, zda je vaše vlastní doména správně nakonfigurována, a to tak, že přehodnotíte [možnost Práce s vlastními doménami](application-proxy-configure-custom-domain.md).<br></br>Pokud se stránka nenačte a zobrazí se chybová zpráva, vyřešte chybu odkazem na [poradce při potížích s proxy aplikací a chybových zprávách](application-proxy-troubleshoot.md). <br></br>Pokud trvá déle než 20 sekund, než se zobrazí chybová zpráva, může být problém s připojením. Přejděte do článku řešení potíží s [konektory proxy ladících](application-proxy-debug-connectors.md) aplikací.  |
|5 | Pokud problémy přetrvávají, přejděte na ladění konektoru | Může být problém s připojením mezi proxy serverem a konektorem nebo mezi konektorem a back-endem. Přejděte do článku řešení potíží s [konektory proxy ladících](application-proxy-debug-connectors.md) aplikací. |
|6 | Publikování všech zdrojů, kontrola nástrojů pro vývojáře prohlížeče a oprava odkazů | Ujistěte se, že cesta k publikování obsahuje všechny potřebné obrázky, skripty a šablony stylů pro vaši aplikaci. Podrobnosti najdete [v tématu Přidání místní aplikace do Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Použijte vývojářské nástroje prohlížeče (nástroje F12 v aplikaci Internet Explorer nebo Microsoft Edge) a zkontrolujte, zda nejsou problémy s publikováním popsané na [stránce Aplikace zobrazeny správně](application-proxy-page-appearance-broken-problem.md). <br></br>Možnosti kontroly pro řešení nefunkčních odkazů v [části Odkazy na stránce nefungují](application-proxy-page-links-broken-problem.md). |
|7 | Kontrola latence sítě | Pokud se stránka načítá pomalu, přečtěte si informace o způsobech, jak minimalizovat latenci sítě, v [části Důležité informace o snížení latence](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Zobrazit další nápovědu k řešení potíží | Pokud potíže přetrvávají, vyhledejte další články o řešení potíží v [dokumentaci k řešení potíží s proxy aplikací](application-proxy-troubleshoot.md). |

## <a name="next-steps"></a>Další kroky


* [Publikování aplikací v samostatných sítích a umístěních pomocí skupin konektorů](application-proxy-connector-groups.md)
* [Práce s existujícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)
* [Poradce při potížích s chybami proxy aplikace a konektoru](application-proxy-troubleshoot.md)
* [Jak tiše nainstalovat konektor proxy aplikací Azure AD](application-proxy-register-connector-powershell.md)
