---
title: Řešení potíží s aktivita služby Azure Active Directory protokolů chyb balíček obsahu | Microsoft Docs
description: Poskytuje seznam chybových zpráv balíček obsahu aktivita služby Azure Active Directory a kroky k jejich řešení.
services: active-directory
documentationcenter: ''
author: rolyon
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: compliance-reports
ms.date: 01/15/2018
ms.author: rolyon
ms.reviewer: dhanyahk
ms.openlocfilehash: 801c425d0d467f8df63f981a1a03c6d202b4c3c5
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34589215"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Řešení potíží s aktivita služby Azure Active Directory protokolů chyb balíček obsahu 


Při práci s balíček obsahu Power BI pro Azure Active Directory Preview, je možné, že můžete spustit do následující chyby: 

- [Aktualizace se nezdařila](active-directory-reporting-troubleshoot-content-pack.md#refresh-failed) 
- [Nepodařilo se aktualizovat pověření ke zdroji dat](active-directory-reporting-troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Import dat trvá příliš dlouho](active-directory-reporting-troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
Toto téma poskytuje informace o možných příčinách a jak tyto chyby opravit.
 
## <a name="refresh-failed"></a>Aktualizace se nezdařila 
 
**Jak se tato chyba prezentované**: e-mailu z Power BI nebo stav selhání v historii aktualizace. 


| Příčina | Informace o vyřešení |
| ---   | ---        |
| Aktualizujte selhání chyby může dojít, když přihlašovací údaje uživatelů, kteří se připojují k balíčku obsahu byly resetovat, ale neaktualizuje v nastavení připojení obsahu sady. | V Power BI vyhledejte sadu dat odpovídající řídicí panel protokoly aktivita služby Azure Active Directory (protokoly aktivita služby Azure Active Directory), vyberte plán aktualizace a pak zadejte přihlašovací údaje Azure AD. |
| Obnovení může selhat z důvodu problémů dat v základní balíček obsahu. | Soubor lístek podpory. Další podrobnosti najdete v tématu [jak získat podporu pro Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Nepodařilo se aktualizovat pověření ke zdroji dat 
 
**Jak se tato chyba prezentované**: V Power BI, když se připojujete k balíčku obsahu protokoly (preview) aktivita služby Azure Active Directory. 

| Příčina | Informace o vyřešení |
| ---   | ---        |
| Je připojující se uživatel je ani globální správce ani čtečku zabezpečení nebo správce zabezpečení. | Použijte účet, který je buď globální správce nebo čtečku zabezpečení nebo správce zabezpečení pro přístup k obsahu sady. |
| Váš klient není klienta Premium nebo nemá alespoň jeden uživatel s licencí Premium souboru. | Soubor lístek podpory. Další podrobnosti najdete v tématu [jak získat podporu pro Azure Active Directory](active-directory-troubleshooting-support-howto.md).|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>Import dat trvá příliš dlouho 
 
**Jak se tato chyba prezentované**: V Power BI, jakmile se připojíte svůj balíček obsahu procesu importu dat spustí Příprava řídicího panelu pro protokol aktivita služby Azure Active Directory. Zobrazí se zpráva: "*importu dat...* "  

| Příčina | Informace o vyřešení |
| ---   | ---        |
| V závislosti na velikosti tenanta tento krok může trvat od několika minut až 30 minut. | Právě se pacienta. Pokud zpráva nezmění na zobrazení řídicího panelu v rámci hodiny, prosím soubor lístek podpory. Další podrobnosti najdete v tématu [jak získat podporu pro Azure Active Directory](active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Další postup

Chcete-li nainstalovat balíček obsahu Power BI pro Azure Active Directory Preview, klikněte na tlačítko [zde](https://powerbi.microsoft.com/en-us/blog/azure-active-directory-meets-power-bi/).


