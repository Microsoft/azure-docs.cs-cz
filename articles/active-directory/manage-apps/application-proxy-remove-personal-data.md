---
title: Odebrat osobních údajů – Proxy aplikace služby Active Directory Azure | Microsoft Docs
description: Odeberte osobní data z konektorů pro Proxy Azure Active Directory aplikace nainstalována na zařízení.
documentationcenter: ''
author: barbkess
manager: mtillman
ms.service: active-directory
ms.component: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 05/21/2018
ms.author: barbkess
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: 94cf0464aca3c46e0c6425b0fb3e24fcd767f95c
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34654777"
---
# <a name="remove-personal-data-for-azure-active-directory-application-proxy"></a>Odebrat osobní údaje pro Proxy aplikace služby Active Directory Azure  

Azure Proxy aplikace Active Directory vyžaduje instalaci konektorů na zařízení, což znamená, že může být osobní data v zařízeních. Tento článek obsahuje kroky pro odstranění této osobní údaje ke zlepšení ochrany osobních údajů. 


## <a name="where-is-the-personal-data"></a>Kde je osobní údaje?
Je možné pro Proxy aplikace k zápisu na následující typy protokolu osobní údaje:

- Protokoly událostí konektoru
- Protokoly událostí Windows

## <a name="remove-personal-data-from-windows-event-logs"></a>Odebrat osobní data z protokolů událostí systému Windows

Informace o tom, jak nakonfigurovat uchovávání dat pro protokol událostí systému Windows najdete v tématu [nastavení protokolu událostí](https://technet.microsoft.com/library/cc952132.aspx). Další informace o protokoly událostí systému Windows najdete v tématu [pomocí protokolu událostí systému Windows](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-hybrid-note.md)]

## <a name="remove-personal-data-from-connector-event-logs"></a>Odebrat osobní data z protokolů událostí konektoru

Aby protokoly Proxy aplikace nemají osobní údaje, můžete buď:

- Odstranění nebo zobrazení dat v případě potřeby nebo
- Vypnout protokolování

K odebrání osobní data z protokolů událostí konektor použijte v následujících částech. Musíte dokončit proces odebrání pro všechna zařízení, na kterých je nainstalován konektor.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

### <a name="view-or-export-specific-data"></a>Zobrazení nebo exportovat konkrétní data

Zobrazení nebo exportovat konkrétní data, vyhledejte související položky v každé z protokolů událostí konektor. Protokoly jsou umístěny na `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. 

Vzhledem k tomu, že protokoly jsou textové soubory, můžete použít [findstr](https://docs.microsoft.com/windows-server/administration/windows-commands/findstr) k vyhledání text položky související s uživatelem.  

Najít osobní údaje, soubory protokolu vyhledejte ID uživatele. 

K vyhledání osobních dat zaznamenaných v aplikaci, která používá omezené delegování Kerberos, vyhledejte těchto součástí typu uživatelské jméno:

- Místní hlavní název uživatele (UPN)
- Část hlavního názvu uživatele (UPN) s uživatelským jménem
- Část místního hlavního názvu uživatele (UPN) s uživatelským jménem
- Název účtu místní zabezpečení účtů správce (SAM) 


### <a name="delete-specific-data"></a>Odstranit konkrétní data

Chcete-li odstranit konkrétní data:

1. Restartujte službu Microsoft Azure AD Application Proxy Connector vygenerovat nový soubor protokolu. Nový soubor protokolu můžete odstranit nebo upravit staré soubory protokolu. 
2. Postupujte podle [zobrazení nebo export konkrétní data](#view-or-export-specific-data) proces popsané najít informace, které je třeba odstranit. Vyhledejte všechny protokoly konektor.
3. Buď odstraňte příslušných protokolových souborů nebo selektivně pole, které obsahují osobní údaje. Můžete také odstranit všechny staré soubory protokolu, pokud je již nejsou potřebné.

### <a name="turn-off-connector-logs"></a>Vypnout protokoly konektoru

Jednou z možností zajistit protokoly konektor neobsahují osobních údajů je k vypnutí možnosti generování protokolu. Chcete-li zastavit generování konektor protokoly, odeberte následující zvýrazněný řádek z `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config`. 

![Konfigurace](./media/application-proxy-remove-personal-data/01.png)


## <a name="next-steps"></a>Další postup

Přehled Proxy aplikací najdete v tématu [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](application-proxy.md).

