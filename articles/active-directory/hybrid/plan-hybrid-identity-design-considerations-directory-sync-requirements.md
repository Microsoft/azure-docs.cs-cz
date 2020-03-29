---
title: Návrh hybridní identity – požadavky na synchronizaci adresářů Azure | Dokumenty společnosti Microsoft
description: Určete, jaké požadavky jsou potřeba pro synchronizaci všech uživatelů mezi on=premises a cloudem pro podnik.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60381157"
---
# <a name="determine-directory-synchronization-requirements"></a>Určení požadavků na synchronizaci adresářů
Synchronizace je především o poskytování uživatelům identitu v cloudu na základě jejich místní identity. Bez ohledu na to, zda budou používat synchronizovaný účet pro ověřování nebo federované ověřování, uživatelé budou stále potřebovat identitu v cloudu.  Tuto identitu bude nutné pravidelně udržovat a aktualizovat.  Aktualizace mohou mít mnoho podob, od změn názvu až po změny hesla.  

Začněte vyhodnocením místního řešení identit organizace a požadavků uživatelů. Toto hodnocení je důležité definovat technické požadavky na způsob, jakým budou vytvářeny a udržovány identity uživatelů v cloudu.  Pro většinu organizací je služba Active Directory místní a bude místním adresářem, ze kterého budou uživatelé synchronizováni, ale v některých případech tomu tak nebude.  

Ujistěte se, že jste odpověděli na následující otázky:

* Máte jednu doménovou strukturu služby AD, více nebo žádnou?
  
  * Kolik adresářů Azure AD budete synchronizovat?
    
    1. Používáte filtrování?
    2. Máte naplánované několik serverů Azure AD Connect?
* Máte aktuálně aktuálně aktuálně místní synchronizační nástroj?
  
  * Pokud ano, mají uživatelé, pokud mají uživatelé virtuální adresář / integraci identit?
* Máte jiný místní adresář, který chcete synchronizovat (např. adresář LDAP, databáze lidských zdrojů atd.)?
  * Budete dělat nějaké GALSync?
  * Jaký je aktuální stav upn ve vaší organizaci? 
  * Máte jiný adresář, který uživatelé ověřit proti?
  * Používá vaše společnost Microsoft Exchange?
    * Mají v plánu mít hybridní exchange nasazení?

Nyní, když máte představu o požadavcích na synchronizaci, musíte určit, který nástroj je správný, aby tyto požadavky splňovaly.  Společnost Microsoft poskytuje několik nástrojů k provedení integrace a synchronizace adresářů.  Další informace naleznete v [tabulce porovnání nástrojů pro integraci adresářů hybridní identity.](plan-hybrid-identity-design-considerations-tools-comparison.md) 

Nyní, když máte požadavky na synchronizaci a nástroj, který bude provádět tuto pro vaši společnost, je třeba vyhodnotit aplikace, které používají tyto adresářové služby. Toto hodnocení je důležité definovat technické požadavky na integraci těchto aplikací do cloudu. Ujistěte se, že jste odpověděli na následující otázky:

* Budou tyto aplikace přesunuty do cloudu a používat adresář?
* Existují speciální atributy, které je třeba synchronizovat s cloudem, aby je tyto aplikace mohly úspěšně používat?
* Budou tyto aplikace muset být přepsány, aby bylo možné využívat cloudové auth?
* Budou tyto aplikace nadále žít místně, zatímco k nim uživatelé přistupují pomocí cloudové identity?

Je také třeba určit požadavky na zabezpečení a synchronizaci adresářů omezení. Toto hodnocení je důležité získat seznam požadavků, které budou potřebné k vytvoření a údržbě identit uživatele v cloudu. Ujistěte se, že jste odpověděli na následující otázky:

* Kde bude synchronizační server umístěn?
* Bude se k ní připojit doména?
* Bude server umístěn v omezené síti za bránou firewall, například v souboru DMZ?
  * Budete moci otevřít požadované porty brány firewall pro podporu synchronizace?
* Máte plán zotavení po havárii pro synchronizační server?
* Máte účet se správnými oprávněními pro všechny doménové struktury, se kterými chcete synchronizovat?
  * Pokud vaše společnost nezná odpověď na tuto otázku, přečtěte si část "Oprávnění pro synchronizaci hesel" v článku [Nainstalujte službu Azure Active Directory Sync service](https://msdn.microsoft.com/library/azure/dn757602.aspx#BKMK_CreateAnADAccountForTheSyncService) a zjistěte, zda již máte účet s těmito oprávněními nebo pokud ho potřebujete vytvořit.
* Pokud máte synchronizaci mutli-forest, je synchronizační server schopen se dostat do každé doménové struktury?

> [!NOTE]
> Každou odpověď si poznamenejte a ujistěte se, že dobře chápete důvody, které vás k ní vedly. [Určit požadavky na reakci na incident](plan-hybrid-identity-design-considerations-incident-response-requirements.md) y naleznete možnosti, které jsou k dispozici. Tím, že odpověděl na tyto otázky, budete vybírat, která možnost nejlépe vyhovuje vašim obchodním potřebám.
> 
> 

## <a name="next-steps"></a>Další kroky
[Určení vícefaktorových požadavků na ověřování](plan-hybrid-identity-design-considerations-multifactor-auth-requirements.md)

## <a name="see-also"></a>Viz také
[Přehled aspekty návrhu](plan-hybrid-identity-design-considerations-overview.md)

