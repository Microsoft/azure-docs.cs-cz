---
title: Ladění konektorů proxy aplikací – služba Azure Active Directory | Dokumenty společnosti Microsoft
description: Problémy s laděním konektorů proxy aplikací služby Azure Active Directory (Azure AD).
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
ms.openlocfilehash: c041578932bd33eb0a2d3afc18a35c2c0458dc8b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "72311847"
---
# <a name="debug-application-proxy-connector-issues"></a>Ladění problémů s konektorem Proxy aplikací 

Tento článek vám pomůže vyřešit problémy s konektory proxy aplikací Azure Active Directory (Azure AD). Pokud používáte službu Proxy aplikace pro vzdálený přístup k místní webové aplikaci, ale máte potíže s připojením k aplikaci, použijte tento vývojový diagram k ladění problémů s konektorem. 

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá, že jste nainstalovali konektor proxy aplikace a máte problém. Při řešení problémů s proxy aplikací doporučujeme začít s tímto tokem řešení potíží a zjistit, zda jsou konektory proxy aplikace správně nakonfigurovány. Pokud máte stále potíže s připojením k aplikaci, postupujte podle toku řešení potíží s aplikací [Ladicí aplikace proxy](application-proxy-debug-apps.md).  


Další informace o proxy aplikaci a použití jeho konektorů naleznete v tématu:

- [Vzdálený přístup k místním aplikacím prostřednictvím proxy aplikace](application-proxy.md)
- [Konektory proxy aplikací](application-proxy-connectors.md)
- [Instalace a registrace konektoru](application-proxy-add-on-premises-application.md)
- [Poradce při potížích s proxy aplikacemi a chybové zprávy](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Vývojový diagram pro problémy s konektory

Tento vývojový diagram vás provede kroky pro ladění některé z více běžných problémů konektoru. Podrobnosti o jednotlivých krocích naleznete v tabulce za vývojovým diagramem.

![Vývojový diagram znázorňující kroky pro ladění spojnice](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Akce | Popis | 
|---------|---------|---------|
|1 | Vyhledání skupiny konektorů přiřazené k aplikaci | Pravděpodobně máte konektor nainstalovaný na více serverech, v takovém případě by měly být konektory [přiřazeny skupinám konektorů](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Další informace o skupinách konektorů najdete [v tématu Publikování aplikací v samostatných sítích a umístěních pomocí skupin konektorů](application-proxy-connector-groups.md). |
|2 | Instalace konektoru a přiřazení skupiny | Pokud nemáte nainstalovaný konektor, přečtěte si informace [o instalaci a registraci konektoru](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br> Pokud máte problémy s instalací konektoru, přečtěte [si informace o potížích s instalací konektoru](application-proxy-connector-installation-problem.md).<br></br> Pokud spojnice není přiřazena ke skupině, přečtěte si část [Přiřazení spojnice ke skupině](application-proxy-connector-groups.md#create-connector-groups).<br></br>Pokud aplikace není přiřazena ke skupině konektorů, přečtěte si část [Přiřazení aplikace ke skupině konektorů](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Spuštění testu portu na konektorovém serveru | Na konektorovém serveru spusťte test portu pomocí [programu telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) nebo jiného nástroje pro testování portů a zkontrolujte, zda jsou porty 443 a 80 otevřené.|
|4 | Konfigurace domén a portů | [Zkontrolujte, zda jsou domény a porty správně nakonfigurovány.](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) Aby konektor fungoval správně, existují určité porty, které musí být otevřené, a adresy URL, ke kterým musí mít server přístup. |
|5 | Zkontrolujte, zda se používá back-endový proxy server | Zkontrolujte, zda konektory používají servery proxy back-end nebo je obchází. Podrobnosti naleznete [v tématu Poradce při potížích s proxy konektorem a problémy s připojením služby](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Aktualizace konektoru a aktualizačního programu pro použití back-endového proxy serveru | Pokud se používá back-endproxy, budete chtít ujistěte se, že konektor používá stejný proxy server. Podrobnosti o řešení potíží a konfiguraci konektorů pro práci se servery proxy naleznete [v tématu Práce s existujícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Načtení interní adresy URL aplikace na konektorový server | Na konektorovém serveru načtěte interní adresu URL aplikace. |
|8 | Kontrola interního připojení k síti | V interní síti došlo k problému s připojením, který tento tok ladění nelze diagnostikovat. Aplikace musí být přístupná interně, aby konektory fungovaly. Můžete povolit a zobrazit protokoly událostí konektoru, jak je popsáno v [konektorech proxy aplikace](application-proxy-connectors.md#under-the-hood). |
|9 | Prodloužení hodnoty časového omezení na zadnístraně | V **dalším nastavení** aplikace změňte nastavení **časového času aplikace back-endu** na **Dlouhé**. Viz [Přidání místní aplikace do Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Pokud problémy přetrvávají, cílkonkrétní problémy toku, zkontrolujte aplikace a ladění přisuzovacích postupů toků | Použijte problémy s [aplikací Proxy ladících aplikací](application-proxy-debug-apps.md) při řešení potíží. |

## <a name="next-steps"></a>Další kroky


* [Publikování aplikací v samostatných sítích a umístěních pomocí skupin konektorů](application-proxy-connector-groups.md)
* [Práce s existujícími místními proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)
* [Poradce při potížích s chybami proxy aplikace a konektoru](application-proxy-troubleshoot.md)
* [Jak tiše nainstalovat konektor proxy aplikací Azure AD](application-proxy-register-connector-powershell.md)
