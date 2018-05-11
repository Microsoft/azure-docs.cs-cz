---
title: GDPR v proxy aplikace služby Azure Active Directory | Microsoft Docs
description: Další informace o GDPR v proxy aplikace služby Azure Active Directory.
services: active-directory
documentationcenter: ''
author: MarkusVi
manager: mtillman
ms.assetid: c7186f98-dd80-4910-92a4-a7b8ff6272b9
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/16/2018
ms.author: markvi
ms.reviewer: harshja
ms.custom: it-pro
ms.openlocfilehash: a3df15743b4918d72fac5f8769a2d3ee721a452f
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/11/2018
---
# <a name="gdpr-in-the-azure-active-directory-application-proxy"></a>GDPR v proxy aplikace služby Azure Active Directory  

Proxy aplikace služby Azure Active Directory (Azure AD) je kompatibilní se standardem GDPR společně s všechny ostatní služby společnosti Microsoft a funkce. Další informace o podpoře GDPR společnosti Microsoft najdete v tématu [licenční podmínky a dokumentace](http://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31).

Protože tato funkce zahrnuje konektory na počítačích, existuje několik událostí, které je nutné sledovat zůstane GDPR kompatibilní. Proxy aplikace vytvoří následující typy protokolů, které může obsahovat EUII:

- Protokoly událostí konektoru
- Protokoly událostí Windows

Abyste mohli zůstat GDPR kompatibilní s dvěma způsoby:

- Odstraňte a exportujte požadavky při jejich vzniku

- Vypnout protokolování

Pro:

- Informace o tom, jak nakonfigurovat uchovávání dat pro protokol událostí systému Windows viz [nastavení protokolu událostí](https://technet.microsoft.com/library/cc952132.aspx). 
- Obecné informace o protokolu událostí systému Windows viz [pomocí protokolu událostí systému Windows](https://msdn.microsoft.com/library/windows/desktop/aa385772.aspx).


Můžete najít v protokolech událostí connect v `C:\ProgramData\Microsoft\Microsoft AAD Application Proxy Connector\Trace`. Následující části poskytují související kroky pro konektor protokoly událostí. Je nutné provést tyto kroky na všech počítačích konektor.
 

## <a name="processing-requests"></a>Zpracování požadavků

Existují tři různé typy požadavků, které může vést pro: 

- Odstranit
- Zobrazit
- Exportovat
 
Zobrazení procesů / export požadavky, musíte projít všechny soubory protokolu a vyhledejte odpovídající položky. 

Protože protokoly jsou textové soubory, můžete vyhledávat podle, například pomocí `findstr` příkazu najděte záznamy související s uživateli. Vzhledem k tomu může být v protokolech hledat následující pole: 

- UserId
- Typ uživatelské jméno, nakonfigurované pro všechny aplikace pomocí omezeného delegování protokolu Kerberos:
    - Hlavní název uživatele (UPN)
    - Místní hlavní název uživatele (UPN)
    - Část hlavního názvu uživatele (UPN) s uživatelským jménem
    - Část místního hlavního názvu uživatele (UPN) s uživatelským jménem
    - Místní název účtu SAM 

 
Potom můžete shromažďovat těchto polí a sdílet je s uživateli.
Při zpracování žádosti o odstranění, budete muset odstranit protokol relevantní. Možné restartovat službu konektoru (Microsoft Azure AD konektor Proxy aplikace) ke generování nový soubor protokolu. Nový soubor protokolu můžete odstranit staré soubory protokolu. Postupujte dle instrukcí pro zobrazení / export najít všechny relevantní protokoly a selektivně odstranění těchto polí nebo soubory. Můžete také vždy pouze odstranit všechny staré soubory protokolu, pokud je již nejsou potřebné.


## <a name="turn-off-connector-logs"></a>Vypnout protokoly konektoru

Chcete-li vypnout konektor protokoly, budete muset upravit `C:\Program Files\Microsoft AAD App Proxy Connector\ApplicationProxyConnectorService.exe.config` odebráním zvýrazněný řádek: 


![Konfigurace](./media/active-directory-application-proxy-gdpr/01.png)


## <a name="next-steps"></a>Další postup

Přehled proxy aplikace služby Azure AD najdete v tématu [jak poskytnout zabezpečený vzdálený přístup k místním aplikacím](manage-apps/application-proxy.md).

