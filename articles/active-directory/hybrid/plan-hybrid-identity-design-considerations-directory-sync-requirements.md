---
title: Návrh hybridní identity – požadavky na synchronizaci adresáře Azure | Microsoft Docs
description: Určete, jaké požadavky je potřeba pro synchronizaci všech uživatelů mezi místními a cloudovou organizací.
documentationcenter: ''
services: active-directory
author: billmath
manager: daveba
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.subservice: hybrid
ms.author: billmath
ms.custom: seohack1
ms.collection: M365-identity-device-management
ms.openlocfilehash: 500d226fcb60646becc49144f206dcb0dee49bd8
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "89278390"
---
# <a name="determine-directory-synchronization-requirements"></a>Určení požadavků na synchronizaci adresářů
Synchronizace je všechno, co poskytuje uživatelům identitu v cloudu na základě jejich místní identity. Bez ohledu na to, jestli budou používat synchronizovaný účet pro ověřování nebo federované ověřování, budou muset uživatelé i nadále mít v cloudu identitu.  Tuto identitu bude nutné pravidelně udržovat a aktualizovat.  Aktualizace mohou trvat mnoho forem, od změny názvu do změny hesla.  

Začněte tím, že vyhodnocujete místní řešení identit a požadavky uživatelů na organizace. Toto vyhodnocení je důležité pro definování technických požadavků na to, jak se budou vytvářet a udržovat identity uživatelů v cloudu.  V případě většiny organizací se služba Active Directory nachází v místním prostředí a bude to místní adresář, ze kterého budou uživatelé synchronizováni, ale v některých případech to nebude mít.  

Nezapomeňte odpovědět na následující otázky:

* Máte jednu doménovou strukturu služby Active Directory, více nebo žádnou?
  
  * Kolik adresářů Azure AD se bude synchronizovat?
    
    1. Používáte filtrování?
    2. Máte plánované více Azure AD Connect serverů?
* Máte momentálně místní Nástroj pro synchronizaci?
  
  * Pokud ano, uživatelé budou mít virtuální adresář nebo integraci identit?
* Máte místní adresář, který chcete synchronizovat (například adresář LDAP, databáze HR atd.)?
  * Budete provádět všechny GALSync?
  * Jaký je aktuální stav UPN ve vaší organizaci? 
  * Máte jiný adresář, ke kterému se uživatelé ověřují?
  * Používá vaše společnost Microsoft Exchange?
    * Plánuje se používat hybridní nasazení Exchange?

Teď, když máte představu o požadavcích na synchronizaci, musíte určit, který nástroj je správný, aby splňoval tyto požadavky.  Společnost Microsoft poskytuje několik nástrojů pro provádění integrace a synchronizace adresářů.  Další informace najdete v [tabulce porovnání nástrojů pro integraci adresáře Hybrid identity](plan-hybrid-identity-design-considerations-tools-comparison.md) . 

Teď, když máte požadavky na synchronizaci a nástroj, který to provede pro vaši společnost, je nutné vyhodnotit aplikace, které tyto adresářové služby používají. Toto hodnocení je důležité pro definování technických požadavků pro integraci těchto aplikací do cloudu. Nezapomeňte odpovědět na následující otázky:

* Budou tyto aplikace přesunuty do cloudu a budou používat adresář?
* Existují speciální atributy, které je třeba synchronizovat do cloudu, aby je mohli tyto aplikace úspěšně používat?
* Budou tyto aplikace muset přepsat, aby využili výhod cloudového ověřování?
* Budou tyto aplikace nadále fungovat místně, zatímco k nim budou mít přístup uživatelé pomocí cloudové identity?

Také je potřeba určit požadavky na zabezpečení a synchronizaci adresářů. Toto hodnocení je důležité pro získání seznamu požadavků, které budou potřeba k vytváření a údržbě identit uživatelů v cloudu. Nezapomeňte odpovědět na následující otázky:

* Kde bude uložen synchronizační Server?
* Bude připojen k doméně?
* Bude server umístěn v omezené síti za bránou firewall, jako je například DMZ?
  * Budete moci otevřít požadované porty brány firewall pro podporu synchronizace?
* Máte plán zotavení po havárii pro synchronizační Server?
* Máte účet se správnými oprávněními pro všechny doménové struktury, se kterými chcete provádět synchronizaci?
  * Pokud vaše společnost neznala odpověď pro tuto otázku, přečtěte si část "oprávnění k synchronizaci hesel" v článku [instalace služby Azure Active Directory Sync](/previous-versions/azure/azure-services/dn757602(v=azure.100)#BKMK_CreateAnADAccountForTheSyncService) a zjistěte, jestli už máte účet s těmito oprávněními, nebo pokud ho potřebujete vytvořit.
* Pokud máte multi synchronizaci doménové struktury, synchronizační Server se může dostat do každé doménové struktury?

> [!NOTE]
> Každou odpověď si poznamenejte a ujistěte se, že dobře chápete důvody, které vás k ní vedly. [Určení požadavků na reakci na incidenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md) přestanou platit dostupné možnosti. Po zodpovězení těchto otázek si vyberete, která možnost nejlépe vyhovuje vašim obchodním potřebám.
> 
> 

## <a name="next-steps"></a>Další kroky
[Určení požadavků na službu Multi-Factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled otázek návrhu](plan-hybrid-identity-design-considerations-overview.md)