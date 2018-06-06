---
title: Návrhu hybridní identity – požadavky na synchronizaci adresářů Azure | Microsoft Docs
description: Určit, jaké požadavky jsou nezbytné k synchronizaci všech uživatelů mezi na = místní a cloudové pro podnik.
documentationcenter: ''
services: active-directory
author: billmath
manager: mtillman
editor: ''
ms.assetid: 593eaa71-17eb-4c16-8c98-43cc62987e65
ms.service: active-directory
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 07/18/2017
ms.component: hybrid
ms.author: billmath
ms.custom: seohack1
ms.openlocfilehash: d6749960806e858909f42c6ecccd445ba8d5ec00
ms.sourcegitcommit: b7290b2cede85db346bb88fe3a5b3b316620808d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/05/2018
ms.locfileid: "34801553"
---
# <a name="determine-directory-synchronization-requirements"></a>Určení požadavků na synchronizaci adresáře
Synchronizace slouží k poskytování identity v cloudu na základě jejich identity místní uživatelé. Zda se bude používat synchronizované účet pro ověřování nebo federovaného ověřování, uživatelé budou stále musí být identity v cloudu.  Tato identita muset udržuje a pravidelně aktualizuje.  Aktualizace můžete mít mnoho forem, od title změny změny hesla.  

Spusťte vyhodnocením organizace místní identity řešení a uživatelské požadavky. Toto testování je důležité určit technické požadavky na tom, jak bude identit uživatelů vytvářené a udržované v cloudu.  Pro většinu organizací je místní služba Active Directory a místní adresář, který uživatelé budou podle synchronizovány z, ale v některých případech to nebude tak.  

Ujistěte se, že odpovězte si na následující otázky:

* Máte jednu doménovou strukturu AD, několik nebo žádné?
  
  * Kolik adresáře Azure AD bude můžete být synchronizaci?
    
    1. Používáte filtrování?
    2. Máte více serverů Azure AD Connect plánované?
* Aktuálně máte synchronizace nástroje místně?
  
  * Pokud ano, podporuje vaši uživatelé, pokud uživatelé používají virtuální adresář/integrace identit?
* Máte k dispozici žádné jiné na místní adresáře, který chcete synchronizovat (například adresář LDAP, databáze HR atd)?
  * Chystáte se to žádné GALSync?
  * Co je aktuální stav UPN ve vaší organizaci? 
  * Máte na jiný adresář, který uživatelé ověřování proti?
  * Používá vaše společnost Microsoft Exchange?
    * Jejich plánování toho, že hybridní nasazení systému exchange?

Nyní když máte představu o synchronizačních požadavcích, musíte určit, který nástroj je správná pro splnění těchto požadavků.  Společnost Microsoft poskytuje několik nástrojů k provádění integrace adresáře a synchronizace.  Najdete v článku [hybridní identita directory integrace nástrojů srovnávací tabulka](active-directory-hybrid-identity-design-considerations-tools-comparison.md) Další informace. 

Nyní když máte synchronizačních požadavcích a nástroj, který bude dosáhnout vaší společnosti, budete muset vyhodnocování aplikací, které používají tyto služby directory. Toto testování je důležité určit požadavky na technické integrovat tyto aplikace do cloudu. Ujistěte se, že odpovězte si na následující otázky:

* Tyto aplikace se přesune do cloudu a používat adresář?
* Jsou nějaké speciální atributy, které je nutné synchronizovat do cloudu, můžete tyto aplikace použít úspěšně?
* Bude potřeba tyto aplikace znovu zapsání využívat cloudové ověřování?
* Bude pokračovat za provozu místně, zatímco uživatelé přistupovat k nim pomocí cloudové identitě těchto aplikací?

Také budete muset určit synchronizace adresářů požadavky a omezení zabezpečení. Toto testování je důležité získat seznam požadavků, které bude nutné k vytváření a údržbu identity uživatele v cloudu. Ujistěte se, že odpovězte si na následující otázky:

* Kde budou synchronizačním serverem umístěné?
* Bude se připojený k doméně?
* Bude server nacházet v omezené síti za bránou firewall, jako je například DMZ?
  * Se nebudete moct otevřít porty brány firewall požadovaná kvůli podpoře synchronizace?
* Máte v plánu zotavení po havárii pro server synchronizace?
* Máte účet s správná oprávnění pro všechny doménové struktury, že chcete, aby byla synchronizována s?
  * Pokud vaše společnost nebude vědět, odpověď pro tuto otázku, projděte si část "Oprávnění pro synchronizaci hesel" v článku [instalace služby Azure Active Directory Sync](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) a zjistit, pokud již máte účet Pomocí těchto oprávnění nebo pokud potřebujete vytvořit.
* Pokud máte doménovou strukturou třeba synchronizace je synchronizační server nemůže získat pro každou doménovou strukturu?

> [!NOTE]
> Zajistěte, aby poznamenejte každou odpověď a pochopit důvody odpověď. [Stanovení požadavků na reakce na incidenty](active-directory-hybrid-identity-design-considerations-incident-response-requirements.md) budou přenášeny po dostupných možností. Po zodpovězení těchto otázek, které vyberete která možnost nejlépe vyhovuje vaší firmě potřebuje.
> 
> 

## <a name="next-steps"></a>Další postup
[Stanovení požadavků na službu Multi-Factor authentication](active-directory-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Další informace najdete v tématech
[Přehled aspektů návrhu](active-directory-hybrid-identity-design-considerations-overview.md)

