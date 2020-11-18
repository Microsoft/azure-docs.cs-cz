---
title: Odebrat osobní data – Proxy aplikací služby Azure Active Directory
description: Odebrat osobní data z konektorů nainstalovaných na zařízeních pro Proxy aplikací služby Azure Active Directory.
documentationcenter: ''
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 05/21/2018
ms.author: kenwith
ms.reviewer: harshja
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99fb466debd5a2ee4bb659aea3494469a8bbe8e1
ms.sourcegitcommit: 8e7316bd4c4991de62ea485adca30065e5b86c67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/17/2020
ms.locfileid: "94658413"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Odebrat osobní data pro Proxy aplikací služby Azure Active Directory

Proxy aplikací služby Azure Active Directory vyžaduje, abyste na zařízení nainstalovali konektory, což znamená, že na vašich zařízeních můžou být osobní údaje. Tento článek popisuje postup odstranění těchto osobních údajů pro zlepšení ochrany osobních údajů.

## <a name="where-is-the-personal-data"></a>Kde jsou osobní údaje?

Proxy aplikací může zapisovat osobní data do následujících typů protokolů:

- Protokoly událostí konektoru
- Protokoly událostí Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Odebrání osobních údajů z protokolů událostí systému Windows

Informace o tom, jak nakonfigurovat uchovávání dat v protokolech událostí systému Windows, najdete v tématu [nastavení pro protokoly událostí](https://technet.microsoft.com/library/cc952132.aspx). Další informace o protokolech událostí systému Windows najdete v tématu [použití protokolu událostí systému Windows](/windows/win32/wes/using-windows-event-log).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Odebrat osobní data z protokolů událostí konektoru

Chcete-li zajistit, aby protokoly proxy aplikací neměly osobní údaje, můžete:

- V případě potřeby data odstraňte nebo zobrazte.
- Vypnout protokolování

Pomocí následujících částí můžete odebrat osobní data z protokolů událostí konektoru. Je nutné dokončit proces odstranění pro všechna zařízení, na kterých je konektor nainstalován.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Zobrazit nebo exportovat konkrétní data

Chcete-li zobrazit nebo exportovat konkrétní data, vyhledejte související položky v každém z protokolů událostí konektoru. Protokoly jsou umístěny na adrese `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace` .

Vzhledem k tomu, že protokoly jsou textové soubory, můžete pomocí nástroje [findstr](/windows-server/administration/windows-commands/findstr) vyhledat textové položky týkající se uživatele.  

Pokud chcete najít osobní údaje, vyhledejte soubory protokolu pro uživatele UserID.

Chcete-li najít osobní údaje zaznamenané aplikací, které používají vynucené delegování protokolu Kerberos, vyhledejte tyto součásti typu uživatelského jména:

- Místní hlavní název uživatele
- Část hlavního názvu uživatele v uživatelském jménu
- Část místního hlavního názvu uživatele v uživatelském jménu
- Místní název účtu správce zabezpečení účtů (SAM)

### <a name="delete-specific-data"></a>Odstranit konkrétní data

Odstranění konkrétních dat:

1. Restartujte službu Microsoft Azure AD proxy aplikace konektor pro vygenerování nového souboru protokolu. Nový soubor protokolu umožňuje odstranit nebo změnit staré soubory protokolu. 
1. Pokud chcete najít informace, které je třeba odstranit, postupujte podle výše popsaných kroků v tématu [zobrazení nebo export konkrétního data](#view-or-export-specific-data) . Prohledejte všechny protokoly konektoru.
1. Buď odstraňte příslušné soubory protokolu, nebo selektivně odstraňte pole obsahující osobní údaje. Pokud už je nepotřebujete, můžete taky odstranit všechny staré soubory protokolu.

### <a name="turn-off-connector-logs"></a>Vypnout protokoly konektorů

Jedna z možností, jak zajistit, aby protokoly konektoru neobsahují osobní údaje, je vypnutí generování protokolu vypnuté. Chcete-li zastavit generování protokolů konektoru, odeberte následující zvýrazněný řádek z `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` .

![Zobrazuje fragment kódu se zvýrazněným kódem, který se má odebrat.](./media/application-proxy-remove-personal-data/01.png)

## <a name="next-steps"></a>Další kroky

Přehled aplikačního proxy serveru najdete v tématu [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](application-proxy.md).