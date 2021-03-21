---
title: Ladit konektory proxy aplikací – Azure Active Directory | Microsoft Docs
description: Ladění problémů s konektory proxy aplikací služby Azure Active Directory (Azure AD).
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
ms.openlocfilehash: c656bfe7f9cc8923859a99f31210cd554aa66a41
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99256962"
---
# <a name="debug-application-proxy-connector-issues"></a>Ladění problémů s konektorem Proxy aplikací 

Tento článek vám pomůže vyřešit problémy s konektory proxy aplikací služby Azure Active Directory (Azure AD). Pokud používáte službu proxy aplikací pro vzdálený přístup k místní webové aplikaci, ale máte potíže s připojením k aplikaci, použijte tento vývojový diagram k ladění problémů konektoru. 

## <a name="before-you-begin"></a>Než začnete

V tomto článku se předpokládá, že máte nainstalovaný konektor proxy aplikací a máte problém. Při řešení potíží s proxy aplikací doporučujeme začít s tímto tokem řešení potíží, abyste zjistili, jestli jsou konektory proxy aplikací správně nakonfigurované. Pokud stále dochází k potížím s připojením k aplikaci, postupujte podle pokynů v části řešení potíží s [aplikací proxy při ladění aplikace](application-proxy-debug-apps.md).  


Další informace o proxy aplikací a používání jeho konektorů najdete v těchto tématech:

- [Vzdálený přístup k místním aplikacím prostřednictvím proxy aplikací](application-proxy.md)
- [Konektory proxy aplikací](application-proxy-connectors.md)
- [Instalace a registrace konektoru](application-proxy-add-on-premises-application.md)
- [Řešení potíží s Proxy aplikací a souvisejícími chybovými zprávami](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Vývojový diagram pro problémy s konektorem

Tento vývojový diagram vás provede kroky pro ladění některých častých problémů s konektorem. Podrobnosti o jednotlivých krocích najdete v tabulce, která následuje po tomto vývojovém diagramu.

![Vývojový diagram znázorňující kroky pro ladění konektoru](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

| Krok | Akce | Popis |
|---------|---------|---------|
|1 | Najít skupinu konektorů přiřazenou k aplikaci | Máte pravděpodobně nainstalovaný konektor na více serverech. v takovém případě by se měly konektory [přiřadit ke skupinám konektorů](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Další informace o skupinách konektorů najdete v tématu [publikování aplikací v samostatných sítích a umístěních pomocí skupin konektorů](application-proxy-connector-groups.md). |
|2 | Instalace konektoru a přiřazení skupiny | Pokud nemáte nainstalovaný konektor, přečtěte si téma [instalace a registrace konektoru](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Pokud máte problémy s instalací konektoru, přečtěte si téma [problém instalace konektoru](application-proxy-connector-installation-problem.md).<br></br> Pokud konektor není přiřazen ke skupině, přečtěte si téma [přiřazení konektoru ke skupině](application-proxy-connector-groups.md#create-connector-groups).<br></br>Pokud aplikace není přiřazena ke skupině konektorů, přečtěte si téma [přiřazení aplikace do skupiny konektorů](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Spustit test portu na serveru konektoru | Na serveru konektoru spusťte test portu pomocí [protokolu Telnet](/windows-server/administration/windows-commands/telnet) nebo jiného nástroje pro testování portů, abyste zkontrolovali, jestli [jsou porty 443 a 80 otevřené](application-proxy-add-on-premises-application.md#open-ports).|
|4 | Konfigurace domén a portů | [Ujistěte se, že vaše domény a porty jsou správně nakonfigurovány](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) . Aby konektor správně fungoval, existují určité porty, které musí být otevřené a adresy URL, ke kterým musí mít server přístup. |
|5 | Zkontroluje, jestli se používá back-end proxy. | Zkontrolujte, jestli konektory používají back-end proxy servery, nebo je obejít. Podrobnosti najdete v tématu [řešení problémů proxy serveru konektoru a problémů s připojením služby](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Aktualizace konektoru a aktualizačního programu pro použití back-end proxy serveru | Pokud se back-end proxy používá, je vhodné se ujistit, že konektor používá stejný proxy server. Podrobnosti o řešení potíží a konfiguraci konektorů pro práci s proxy servery najdete v tématu [práce se stávajícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Načíst interní adresu URL aplikace na serveru konektoru | Na serveru konektoru načtěte interní adresu URL aplikace. |
|8 | Ověření připojení k interní síti | Ve vaší interní síti je problém s připojením, který tento tok ladění nemůže diagnostikovat. Aby bylo možné konektory fungovat, musí být aplikace přístupné interně. Protokoly událostí konektoru můžete povolit a zobrazovat, jak je popsáno v tématu [konektory proxy aplikací](application-proxy-connectors.md#under-the-hood). |
|9 | Prodloužit hodnotu časového limitu na back-endu | V **dalších nastaveních** aplikace změňte nastavení **časový limit back-end aplikace** na **Long**. Viz [Přidání místní aplikace do služby Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Pokud problémy přetrvávají, zaměřte se na konkrétní problémy s tokem, Projděte si toky ladění aplikací a jednotného přihlašování | Použijte k řešení potíží s [aplikací proxy aplikace ladění](application-proxy-debug-apps.md) . |

## <a name="next-steps"></a>Další kroky


* [Publikování aplikací v samostatných sítích a umístěních pomocí skupin konektorů](application-proxy-connector-groups.md)
* [Práce se stávajícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)
* [Řešení chyb proxy aplikací a konektorů](application-proxy-troubleshoot.md)
* [Postup při tiché instalaci konektoru služby Azure Proxy aplikací služby AD](application-proxy-register-connector-powershell.md)