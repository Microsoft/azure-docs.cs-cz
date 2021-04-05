---
title: Ladit aplikace proxy aplikací – Azure Active Directory | Microsoft Docs
description: Problémy s laděním u aplikací proxy aplikace Azure Active Directory (Azure AD).
services: active-directory
author: kenwith
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: troubleshooting
ms.date: 05/21/2019
ms.author: kenwith
ms.reviewer: japere
ms.openlocfilehash: 2066030b04feae4e50fee3442a663209801d5875
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "99253387"
---
# <a name="debug-application-proxy-application-issues"></a>Ladění problémů s aplikací Proxy aplikací 

Tento článek vám pomůže vyřešit problémy s aplikacemi proxy aplikace Azure Active Directory (Azure AD). Pokud používáte službu proxy aplikací pro vzdálený přístup k místní webové aplikaci, ale máte potíže s připojením k aplikaci, použijte tento vývojový diagram k ladění problémů s aplikacemi. 

## <a name="before-you-begin"></a>Než začnete

Při řešení potíží s proxy aplikací doporučujeme začít s konektory. Nejdřív postupujte podle postupu pro řešení potíží v části [ladění proxy serveru aplikace](application-proxy-debug-connectors.md) a ujistěte se, že konektory proxy aplikací jsou správně nakonfigurované. Pokud stále dochází k problémům, vraťte se k tomuto článku a vyřešte potíže s aplikací.  

Další informace o proxy aplikací najdete v těchto tématech:

- [Vzdálený přístup k místním aplikacím prostřednictvím proxy aplikací](application-proxy.md)
- [Konektory proxy aplikací](application-proxy-connectors.md)
- [Instalace a registrace konektoru](application-proxy-add-on-premises-application.md)
- [Řešení potíží s Proxy aplikací a souvisejícími chybovými zprávami](application-proxy-troubleshoot.md)

## <a name="flowchart-for-application-issues"></a>Vývojový diagram pro problémy s aplikacemi

Tento vývojový diagram vás provede kroky pro ladění některých nejběžnějších potíží s připojením k aplikaci. Podrobnosti o jednotlivých krocích najdete v tabulce, která následuje po tomto vývojovém diagramu.

![Vývojový diagram znázorňující kroky pro ladění aplikace](media/application-proxy-debug-apps/application-proxy-apps-debugging-flowchart.png)

| Krok | Akce | Popis |
|---------|---------|---------|
|1 | Otevřete prohlížeč, přejděte do aplikace a zadejte svoje přihlašovací údaje. | Zkuste použít přihlašovací údaje pro přihlášení k aplikaci a vyhledat všechny chyby související s uživatelem, třeba [Tato podniková aplikace není dostupná](application-proxy-sign-in-bad-gateway-timeout-error.md). |
|2 | Ověření přiřazení uživatele k aplikaci | Ujistěte se, že váš uživatelský účet má oprávnění pro přístup k aplikaci zevnitř podnikové sítě, a pak otestujte přihlášení k aplikaci pomocí kroků v části [test aplikace](application-proxy-add-on-premises-application.md#test-the-application). Pokud se problémy s přihlášením trvají, přečtěte si téma [jak řešit chyby při přihlašování](../reports-monitoring/concept-provisioning-logs.md?context=azure/active-directory/manage-apps/context/manage-apps-context).  |
|3 | Otevřete prohlížeč a pokuste se získat přístup k aplikaci. | Pokud se zobrazí chyba okamžitě, zkontrolujte, jestli je proxy aplikací správně nakonfigurovaný. Podrobnosti o konkrétních chybových zprávách najdete v tématu [řešení potíží se službou Application proxy a chybovými zprávami](application-proxy-troubleshoot.md).  |
|4 | Projděte si vlastní nastavení domény nebo odstraňte chybu. | Pokud se stránka vůbec nezobrazuje, ujistěte se, že je vaše vlastní doména správně nakonfigurovaná, a to tak, že zkontrolujete [práci s vlastními doménami](application-proxy-configure-custom-domain.md).<br></br>Pokud se stránka nenačte a zobrazí se chybová zpráva, vyřešte problém pomocí odkazu na  [řešení potíží s proxy aplikací a chybové zprávy](application-proxy-troubleshoot.md). <br></br>Pokud se zobrazí chybová zpráva delší než 20 sekund, může dojít k potížím s připojením. Přejít na článek řešení potíží [konektory proxy aplikace pro ladění](application-proxy-debug-connectors.md) .  |
|5 | Pokud problémy přetrvávají, přejdete na ladění konektoru. | Mohlo dojít k potížím s připojením mezi proxy serverem a konektorem nebo mezi konektorem a back-end. Přejít na článek řešení potíží [konektory proxy aplikace pro ladění](application-proxy-debug-connectors.md) . |
|6 | Publikování všech prostředků, kontroly vývojářských nástrojů v prohlížeči a oprava odkazů | Ujistěte se, že cesta publikování zahrnuje všechny nezbytné obrázky, skripty a šablony stylů pro vaši aplikaci. Podrobnosti najdete v tématu [Přidání místní aplikace do služby Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). <br></br>Použijte vývojářské nástroje v prohlížeči (nástroje F12 v aplikaci Internet Explorer nebo Microsoft Edge) a vyhledejte problémy publikování, jak je popsáno na [stránce aplikace. nezobrazuje se správně](application-proxy-page-appearance-broken-problem.md). <br></br>Kontrola možností pro řešení přerušených odkazů v [odkazech na stránce nefunguje](application-proxy-page-links-broken-problem.md). |
|7 | Kontrolovat latenci sítě | Pokud se stránka načítá pomalu, přečtěte si informace o způsobech minimalizace latence sítě v části [požadavky na snížení latence](application-proxy-network-topology.md#considerations-for-reducing-latency). | 
|8 | Zobrazit další pomoc při řešení potíží | Pokud potíže přetrvávají, vyhledejte další články týkající se řešení potíží v [dokumentaci k řešení potíží s proxy aplikací](application-proxy-troubleshoot.md). |

## <a name="next-steps"></a>Další kroky


* [Publikování aplikací v samostatných sítích a umístěních pomocí skupin konektorů](application-proxy-connector-groups.md)
* [Práce se stávajícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)
* [Řešení chyb proxy aplikací a konektorů](application-proxy-troubleshoot.md)
* [Postup při tiché instalaci konektoru služby Azure Proxy aplikací služby AD](application-proxy-register-connector-powershell.md)
