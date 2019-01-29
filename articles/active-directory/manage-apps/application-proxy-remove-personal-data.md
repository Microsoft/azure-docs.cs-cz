---
title: Odebrání osobních údajů – Azure Active Directory Application Proxy | Dokumentace Microsoftu
description: Odeberte osobní údaje z konektorů, které jsou nainstalované na zařízeních pro Azure Active Directory Application Proxy.
documentationcenter: ''
author: barbkess
manager: daveba
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 9b17d3b339bf2c873e8e4c02586e814febfefaec
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55177334"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Odebrání osobních údajů pro Azure Active Directory Application Proxy  

Azure Active Directory Application Proxy vyžaduje instalaci konektorů v zařízeních, což znamená, že na vašich zařízeních může existovat osobní údaje. Tento článek popisuje kroky pro odstranění osobních údajů ke zlepšení ochrany osobních údajů. 


## <a name="where-is-the-personal-data"></a>Kde jsou osobní údaje?
Je možné pro Proxy aplikací osobní údaje zapisovat do těchto typů protokolu:

- Protokoly událostí konektoru
- Protokoly událostí Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Odebrat osobní údaje z protokolů událostí Windows

Informace o tom, jak nakonfigurovat uchovávání dat protokolu událostí Windows, naleznete v tématu [nastavení protokolu událostí](https://technet.microsoft.com/library/cc952132.aspx). Další informace o protokolech událostí pro Windows najdete v tématu [pomocí protokolu událostí Windows](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Odebrat osobní údaje z protokolů událostí konektoru

Aby se protokoly Proxy aplikací není nutné osobní údaje, můžete buď:

- Odstranění nebo zobrazení dat v případě potřeby, nebo
- Vypnout protokolování

Jak odebrat osobní údaje z protokolů událostí konektor použijte v následujících částech. Musíte dokončit proces odebrání pro všechna zařízení, ve kterých není nainstalovaný konektor.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Zobrazení nebo exportovat konkrétní data

K zobrazení nebo exportovat konkrétní data, vyhledejte související položky v každém z protokolů událostí konektoru. Protokoly jsou umístěné na `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. 

Protože se protokoly jsou textové soubory, můžete použít [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) k vyhledání textu položky související s uživatelem.  

K vyhledání osobních údajů, vyhledejte ID uživatele soubory protokolu. 

K vyhledání osobních údajů protokolovaným v aplikaci, která používá omezené delegování protokolu Kerberos, vyhledejte tyto součásti typ uživatelského jména:

- Místní hlavní název uživatele (UPN)
- Část hlavního názvu uživatele (UPN) s uživatelským jménem
- Část místního hlavního názvu uživatele (UPN) s uživatelským jménem
- Název účtu místní zabezpečení účtů správce (SAM) 


### <a name="delete-specific-data"></a>Odstranit konkrétní data

Chcete-li odstranit specifických dat:

1. Restartujte službu Microsoft Azure AD Application Proxy Connector se vygenerovat nový soubor protokolu. Nový soubor protokolu můžete odstranit nebo upravit starých souborů protokolů. 
2. Postupujte podle [zobrazení a export dat konkrétní](#view-or-export-specific-data) proces popsaný dříve najít informace, které je nutné odstranit. Hledat ve všech protokolů konektoru.
3. Odstraňte příslušných protokolových souborů nebo selektivně odstranit pole, které obsahují osobní údaje. Pokud už nepotřebujete, můžete také odstranit všechny starých souborů protokolů.

### <a name="turn-off-connector-logs"></a>Vypnout konektor protokolů

Jednou z možností zajistit protokolů konektoru neobsahují osobních údajů je vypnout generování protokolu. Se přestávají generovat protokoly konektor, odebrat následující zvýrazněný řádek z `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`. 

![Konfigurace](./media/application-proxy-remove-personal-data/01.png)


## <a name="next-steps"></a>Další postup

Přehled Proxy aplikací, naleznete v tématu [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](application-proxy.md).

