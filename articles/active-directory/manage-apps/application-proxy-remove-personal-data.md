---
title: Odebrání osobních údajů – proxy aplikace služby Azure Active Directory
description: Odeberte osobní data z konektorů nainstalovaných na zařízeních pro proxy aplikace služby Azure Active Directory.
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: mimart
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: fe97956d99bd5c677e499b532ef85a1bb4d324ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "74275411"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Odebrání osobních dat pro proxy aplikace služby Azure Active Directory

Proxy aplikace Azure Active Directory vyžaduje instalaci konektorů na vašich zařízeních, což znamená, že na vašich zařízeních mohou být osobní data. Tento článek obsahuje postup, jak tyto osobní údaje odstranit, aby se zlepšila ochrana osobních údajů.

## <a name="where-is-the-personal-data"></a>Kde jsou osobní údaje?

Pro proxy aplikace je možné zapisovat osobní údaje do následujících typů protokolů:

- Protokoly událostí konektoru
- Protokoly událostí Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Odebrání osobních údajů z protokolů událostí systému Windows

Informace o konfiguraci uchovávání dat pro protokoly událostí systému Windows naleznete v [tématu Nastavení protokolů událostí](https://technet.microsoft.com/library/cc952132.aspx). Další informace o protokolech událostí systému Windows naleznete [v tématu Použití protokolu událostí systému Windows](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Odebrání osobních údajů z protokolů událostí programu Konektor

Chcete-li zajistit, aby protokoly proxy aplikace neměly osobní údaje, můžete buď:

- V případě potřeby odstraňte nebo zobrazte data, nebo
- Vypnutí protokolování

Následující části slouží k odebrání osobních údajů z protokolů událostí konektoru. Proces odebrání je nutné dokončit u všech zařízení, na kterých je konektor nainstalován.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Zobrazení nebo export určitých dat

Chcete-li zobrazit nebo exportovat určitá data, vyhledejte související položky v každém protokolu událostí konektoru. Protokoly jsou umístěny na adrese `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`.

Vzhledem k tomu, že protokoly jsou textové soubory, můžete použít [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) k vyhledávání textových položek souvisejících s uživatelem.  

Chcete-li najít osobní údaje, vyhledejte soubory protokolu pro UserID.

Chcete-li vyhledat osobní údaje zaznamenané aplikací, která používá delegování s omezeným i protokolem Kerberos, vyhledejte tyto součásti typu uživatelského jména:

- Místní hlavní jméno uživatele
- Uživatelské jméno část hlavního jména uživatele
- Uživatelské jméno část místního uživatelského hlavního jména
- Název účtu správce místních účtů zabezpečení (SAM)

### <a name="delete-specific-data"></a>Odstranění konkrétních dat

Odstranění konkrétních dat:

1. Chcete-li vygenerovat nový soubor protokolu, restartujte službu Microsoft Azure AD Application Proxy Connector. Nový soubor protokolu umožňuje odstranit nebo upravit staré soubory protokolu. 
1. Chcete-li najít informace, které je třeba odstranit, postupujte podle výše popsaného procesu [zobrazení nebo exportu konkrétních dat.](#view-or-export-specific-data) Prohledejte všechny protokoly konektorů.
1. Odstraňte příslušné soubory protokolu nebo selektivně odstraňte pole obsahující osobní údaje. Můžete také odstranit všechny staré soubory protokolu, pokud je již nepotřebujete.

### <a name="turn-off-connector-logs"></a>Vypnutí protokolů konektorů

Jednou z možností, jak zajistit, aby protokoly konektorů neobsahovaly osobní údaje, je vypnout generování protokolu. Chcete-li zastavit generování protokolů konektorů, `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`odeberte z programu .

![Zobrazí fragment kódu se zvýrazněným kódem, který chcete odebrat.](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Další kroky

Přehled proxy aplikací najdete v tématu [Jak zajistit zabezpečený vzdálený přístup k místním aplikacím](application-proxy.md).