---
title: Hybridní identita návrh – požadavky na synchronizaci adresářů Azure | Dokumentace Microsoftu
description: Určit, jaké požadavky jsou nezbytné k synchronizaci všech uživatelů mezi = v místním prostředí a cloudu pro podniky.
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
ms.openlocfilehash: 21558c4eccf0cd1f4e9e1d630f0e89dbb6f01c51
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56201699"
---
# <a name="determine-directory-synchronization-requirements"></a>Určete požadavky na synchronizaci adresáře
Synchronizace se točí kolem identity v cloudu na základě jejich identity v místním, že uživatel. Jestli použijí synchronizované účet pro ověřování nebo federovaného ověřování, uživatelé stále musíte nastavit identitu, která v cloudu.  Tato identita bude muset udržované a pravidelně aktualizuje.  Aktualizace mohou mít mnoho forem, od změn názvu pro změny hesla.  

Začněte tím, že vaše rozhodnutí vyzkoušet organizace místní požadavky na řešení a uživatele identit. Toto testování je důležité definovat technické požadavky pro způsob vytváření a v cloudu udržuje identity uživatelů.  Pro většinu organizací Active Directory je místní a v místním adresáři, který uživatelé se tak synchronizovány z, ale v některých případech to nebude tento případ.  

Ujistěte se, že odpovědět na následující otázky:

* Máte jednu doménovou strukturu AD, více nebo žádná?
  
  * Je počet adresářů služby Azure AD se nesynchronizuje do?
    
    1. Používáte filtrování?
    2. Máte víc serverů služby Azure AD Connect plánovaná?
* Nyní máte synchronizace nástroje místní?
  
  * Pokud ano, se uživatelům, pokud mají uživatelé virtuální adresář/integrace identit?
* Máte k dispozici žádné další místní adresáře, které se mají synchronizovat (například adresář LDAP, databáze HR, etc)?
  * Chystáte se provádět jakékoli GALSync?
  * Co je aktuální stav UPN ve vaší organizaci? 
  * Máte jiný adresář, který uživatelé ověřovat vůči?
  * Používá vaše společnost Microsoft Exchange?
    * Jejich plán s hybridního nasazení exchange?

Teď, když máte představu o synchronizačních požadavcích, je nutné určit, který nástroj je tu správnou budou odpovídat vašim požadavkům.  Společnost Microsoft poskytuje několik nástrojů k provedení integrace adresáře a synchronizací.  Zobrazit [hybridní identita adresář integrace nástroje srovnávací tabulka](plan-hybrid-identity-design-considerations-tools-comparison.md) Další informace. 

Teď, když máte vaše požadavky na synchronizaci a nástroj, který bude dosáhnout vaší společnosti, budete muset vyhodnotit aplikací, které používají tyto služby adresáře. Toto testování je důležité definovat technické požadavky, můžete integrovat tyto aplikace do cloudu. Ujistěte se, že odpovědět na následující otázky:

* Tyto aplikace se přesune do cloudu a používat adresář?
* Existují speciální atributy, které je potřeba se synchronizují do cloudu, aby tyto aplikace mohly úspěšně používat?
* Tyto aplikace potřebovat k zapsání znovu využít výhod cloudové ověřování?
* Tyto aplikace nadále live místní, zatímco uživatelé přístup k jejich použití cloudové identitě?

Musíte také určit zabezpečení synchronizace adresářů požadavky a omezení. Toto testování je potřeba získat seznam všech požadavků, které se mají provést, aby bylo možné vytvářet a spravovat identity uživatele v cloudu. Ujistěte se, že odpovědět na následující otázky:

* Pokud serveru pro synchronizaci se umístí?
* Stane se připojené k doméně?
* Server umístí na síť s omezeným přístupem za bránou firewall, například hraniční sítě?
  * Budete moci otevřít porty brány firewall požadovaná pro podporu synchronizace?
* Máte v plánu zotavení po havárii pro server synchronizace?
* Máte účet se správnými oprávněními pro všechny doménové struktury, že který chcete synchronizovat s?
  * Pokud vaše společnost nebude vědět i odpověď na tuto otázku, projděte si část "Oprávnění pro synchronizaci hesel" v článku [nainstalovat službu Azure Active Directory Sync](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) a určit, pokud již máte účet s těmito oprávněními nebo pokud je potřeba ho vytvořit.
* Pokud máte třeba doménovými strukturami synchronizace je synchronizační server získají pro každou doménovou strukturu?

> [!NOTE]
> Ujistěte se, že každá odpověď a pochopení odůvodnění odpověď. [Určení požadavků na reakce na incidenty](plan-hybrid-identity-design-considerations-incident-response-requirements.md) se přenášejí prostřednictvím možnosti, které jsou k dispozici. Po zodpovězení těchto otázek, které vyberete která možnost nejlépe vyhovuje stylu vaší firmě potřebuje.
> 
> 

## <a name="next-steps"></a>Další postup
[Určení požadavků na ověření službou Multi-Factor Authentication](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Další informace najdete v tématech
[Přehled aspektů návrhu](plan-hybrid-identity-design-considerations-overview.md)

