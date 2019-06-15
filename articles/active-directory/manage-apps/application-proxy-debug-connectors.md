---
title: 'Ladění konektory Proxy aplikací: Azure Active Directory | Dokumentace Microsoftu'
description: Ladění problémů s konektory Proxy aplikací Azure Active Directory (Azure AD).
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
ms.openlocfilehash: c3088ae777fe1a64be218105d36fdb9e01d7b798
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/13/2019
ms.locfileid: "66172232"
---
# <a name="debug-application-proxy-connector-issues"></a>Ladění problémů konektor Proxy aplikací 

Tento článek pomůže při odstraňování problémů s konektory Proxy aplikací Azure Active Directory (Azure AD). Pokud používáte službu Proxy aplikací pro vzdálený přístup k webové aplikaci lokálně, ale máte potíže s připojením k aplikaci, použijte tento vývojový diagram pro ladění problémů konektoru. 

## <a name="before-you-begin"></a>Než začnete

Tento článek předpokládá jste nainstalovali konektor Proxy aplikací a se vyskytl problém. Při řešení potíží s Proxy aplikací, doporučujeme že začínat tohoto řešení problémů s toku můžete určit, pokud jsou správně nakonfigurované konektory Proxy aplikací. Pokud stále máte potíže s připojením k aplikaci, postupujte podle řešení problémů s tok v [problémy ladit aplikaci Proxy aplikací](application-proxy-debug-apps.md).  


Další informace o Proxy aplikací a používání jeho konektorů najdete v tématu:

- [Vzdálený přístup k místním aplikacím přes Proxy aplikací](application-proxy.md)
- [Konektory Proxy aplikací](application-proxy-connectors.md)
- [Instalace a registrace konektoru](application-proxy-add-on-premises-application.md)
- [Poradce při potížích s Proxy aplikací problémy a chybové zprávy](application-proxy-troubleshoot.md)

## <a name="flowchart-for-connector-issues"></a>Vývojový diagram pro Connectoru

Tato vývojový diagram vás provede kroky pro některé běžné problémy konektor ladění. Podrobnosti o jednotlivých kroků najdete v tabulce následujících vývojový diagram.

![Vývojový diagram znázorňující postup pro ladění konektoru](media/application-proxy-debug-connectors/application-proxy-connector-debugging-flowchart.png)

|  | Akce | Popis | 
|---------|---------|---------|
|1 | Najít skupinu konektorů, které jsou přiřazené k aplikaci | Pravděpodobně konektoru nainstalovaná na několika serverech, v takovém případě by měl být konektory [přiřadit skupinám konektor](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups). Další informace o skupinách konektoru najdete v tématu [publikování aplikací na samostatných sítí a umístění s využitím skupiny konektorů](application-proxy-connector-groups.md). |
|2 | Nainstalujte connector a přiřazují skupinu | Pokud nemáte nainstalovaný konektor, přečtěte si téma [nainstalujte a zaregistrujte konektor](application-proxy-add-on-premises-application.md#install-and-register-a-connector).<br></br>Pokud konektor není přiřazená do skupiny, přečtěte si téma [přiřadit skupině konektoru](application-proxy-connector-groups.md#create-connector-groups).<br></br>Pokud aplikace není přiřazená do skupiny konektorů, přečtěte si téma [přiřadit aplikace do skupiny konektorů](application-proxy-connector-groups.md#assign-applications-to-your-connector-groups).|
|3 | Spuštění testu port na serveru pro konektor | Na serveru pro konektor spuštění port testu s použitím [telnet](https://docs.microsoft.com/windows-server/administration/windows-commands/telnet) nebo jiné testování port nástroje pro kontrolu, pokud jsou otevřené porty 443 a 80.|
|4 | Konfigurace domén a portů | [Ujistěte se, zda jsou správně nakonfigurovány vašich domén a porty](application-proxy-add-on-premises-application.md#prepare-your-on-premises-environment) pro konektor fungovalo správně, existují určité porty, které musí být otevřený a adresy URL, které je nutné mít přístup k serveru. |
|5 | Zkontrolujte, jestli je proxy server back-end používána | Zaškrtněte, pokud chcete zjistit, zda jsou konektorů pomocí back-end proxy servery nebo vynechání je. Podrobnosti najdete v tématu [s řešením problémů proxy serveru konektoru a problémy s připojením služby](application-proxy-configure-connectors-with-proxy-servers.md#troubleshoot-connector-proxy-problems-and-service-connectivity-issues). |
|6 | Aktualizace konektoru a aktualizační používat proxy server back-end | Pokud proxy server back-end se používá, je vhodné zajistit, aby že konektor používá stejný server proxy. Podrobnosti o řešení potíží a konfiguraci konektorů pro práci s proxy serverů najdete v tématu [práce s existující místní proxy servery](application-proxy-configure-connectors-with-proxy-servers.md). |
|7 | Načíst interní adresa URL aplikace na serveru pro konektor | Na serveru pro konektor načtěte interní adresa URL aplikace. |
|8 | Zkontrolujte připojení k interní síti | V interní síti, které tento tok ladění není schopen diagnostikovat nastane problém s připojením. Aplikace musí být přístupné interně pro konektory pro práci. Můžete povolit a zobrazit protokoly událostí konektoru, jak je popsáno v [konektory Proxy aplikací](application-proxy-connectors.md#under-the-hood). |
|9 | Prodloužit hodnotu časového limitu na back-endu | V **další nastavení** pro vaši aplikaci změnit **časový limit pro back-endu aplikace** nastavení **dlouhé**. Zobrazit [přidat místní aplikace do služby Azure AD](application-proxy-add-on-premises-application.md#add-an-on-premises-app-to-azure-ad). |
|10 | Pokud problémy přetrvávají, cílit na konkrétní tok problémy, revizi aplikace a ladění toky jednotného přihlašování | Použití [problémy ladit aplikaci Proxy aplikací](application-proxy-debug-apps.md) řešení potíží s toku. |

## <a name="next-steps"></a>Další postup


* [Publikování aplikací na samostatných sítí a umístění s využitím skupiny konektorů](application-proxy-connector-groups.md)
* [Práce s existující místní proxy servery](application-proxy-configure-connectors-with-proxy-servers.md)
* [Řešení potíží s chybami Proxy aplikací a konektoru](application-proxy-troubleshoot.md)
* [Postup při bezobslužné instalaci Azure AD Application Proxy Connector](application-proxy-register-connector-powershell.md)
