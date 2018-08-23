---
title: Řešení potíží s Azure Active Directory aktivity chyby balíčku obsahu protokolů | Dokumentace Microsoftu
description: Poskytuje seznam chybových zpráv balíčku obsahu aktivit Azure Active Directory a kroky a opravte je.
services: active-directory
documentationcenter: ''
author: priyamohanram
manager: mtillman
editor: ''
ms.assetid: ffce7eb1-99da-4ea7-9c4d-2322b755c8ce
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.component: report-monitor
ms.date: 01/15/2018
ms.author: priyamo
ms.reviewer: dhanyahk
ms.openlocfilehash: eafbe25a5a0fa9182030304e9142a6013c9fb29b
ms.sourcegitcommit: 30c7f9994cf6fcdfb580616ea8d6d251364c0cd1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/18/2018
ms.locfileid: "42058349"
---
# <a name="troubleshooting-azure-active-directory-activity-logs-content-pack-errors"></a>Řešení potíží s Azure Active Directory aktivity chyby balíčku obsahu protokolů 


Při práci s balíčkem obsahu Power BI pro Azure Active Directory, ve verzi Preview, je možné, že narazíte na následující chyby: 

- [Aktualizace se nezdařila.](troubleshoot-content-pack.md#refresh-failed) 
- [Nepovedlo se aktualizovat přihlašovací údaje ke zdroji dat](troubleshoot-content-pack.md#failed-to-update-data-source-credentials) 
- [Import dat trvá moc dlouho](troubleshoot-content-pack.md#importing-of-data-is-taking-too-long) 
 
Tento článek obsahuje informace o možných příčinách a jak tyto chyby opravit.
 
## <a name="refresh-failed"></a>Aktualizace se nezdařila. 
 
**Jak se zobrazí tato chyba**: e-mailů z Power BI nebo stavu selhání v historii aktualizace. 


| Příčina | K vyřešení |
| ---   | ---        |
| Aktualizujte chyby, které chyby může být způsobena přihlašovacích údajů uživatele, připojení k balíčku obsahu byly obnovit, ale není aktualizovaná. v nastavení připojení balíčku obsahu. | V Power BI vyhledejte sadu dat odpovídající řídicí panel protokolů aktivit Azure Active Directory (Azure Active Directory protokoly aktivit), zvolte naplánovat aktualizaci a pak zadejte svoje přihlašovací údaje Azure AD. |
| Obnovení může selhat kvůli problémům s daty v základní balíčku obsahu. | Lístek podpory. Další informace najdete v tématu [jak získat podporu pro Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 
 
## <a name="failed-to-update-data-source-credentials"></a>Nepovedlo se aktualizovat přihlašovací údaje ke zdroji dat 
 
**Jak se zobrazí tato chyba**: V Power BI, když se připojujete k balíčku obsahu aktivit Azure Active Directory protokoly (preview). 

| Příčina | K vyřešení |
| ---   | ---        |
| Je připojující se uživatel není globální správce nebo čtenáře zabezpečení nebo správcem zabezpečení. | Použijte účet, který je globálním správcem nebo Čtenář zabezpečení nebo správce zabezpečení pro přístup k obsahu sady. |
| Váš tenant není Premium tenanta nebo nemá alespoň jeden uživatel s licencí Premium souboru. | Lístek podpory. Další informace najdete v tématu [jak získat podporu pro Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|
 

 

## <a name="importing-of-data-is-taking-too-long"></a>Import dat trvá moc dlouho 
 
**Jak se zobrazí tato chyba**: V Power BI po připojení balíčku obsahu, proces importu dat začíná Příprava řídicí panel pro protokol aktivit Azure Active Directory. Zobrazí se zpráva: "*importu dat...* "  

| Příčina | K vyřešení |
| ---   | ---        |
| V závislosti na velikosti tenanta tento krok může od trvat několik minut až 30 minut. | Právě buďte prosím trpěliví. Pokud zprávu nezmění na zobrazení řídicího panelu do jedné hodiny, požádejte prosím lístek podpory. Další informace najdete v tématu [jak získat podporu pro Azure Active Directory](../fundamentals/active-directory-troubleshooting-support-howto.md).|

## <a name="next-steps"></a>Další postup

Chcete-li nainstalovat balíček obsahu Power BI pro Azure Active Directory, ve verzi Preview, klikněte na tlačítko [tady](https://powerbi.microsoft.com/blog/azure-active-directory-meets-power-bi/).


