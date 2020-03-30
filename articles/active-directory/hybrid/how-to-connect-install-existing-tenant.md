---
title: 'Azure AD Connect: Když už máte Azure AD | Dokumenty společnosti Microsoft'
description: Toto téma popisuje, jak používat připojení, když máte existující klienta Azure AD.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: ''
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 3636b88b14cf7e76e4fb023434316e7ee31ded04
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "71336826"
---
# <a name="azure-ad-connect-when-you-have-an-existent-tenant"></a>Azure AD Connect: Když máte existujícího klienta
Většina témat, jak používat Azure AD Connect předpokládá, že začnete s novým klientem Azure AD a že tam nejsou žádní uživatelé nebo jiné objekty. Ale pokud jste začali s tenantem Azure AD, naplnil ho s uživateli a dalšími objekty a teď chcete použít Připojit, pak toto téma je pro vás.

## <a name="the-basics"></a>Základy
Objekt ve službě Azure AD je mastered v cloudu (Azure AD) nebo místně. Pro jeden objekt nelze spravovat některé atributy místní a některé další atributy ve službě Azure AD. Každý objekt má příznak označující, kde je objekt spravován.

Některé uživatele můžete spravovat místně a další v cloudu. Běžným scénářem pro tuto konfiguraci je organizace se směsí účetních pracovníků a pracovníků prodeje. Účetní pracovníci mají místní účet služby AD, ale prodejci nemají, mají účet ve službě Azure AD. Byste spravovat některé uživatele místní a některé ve službě Azure AD.

Pokud jste začali spravovat uživatele ve službě Azure AD, které jsou také v místním aD a později chcete použít připojení, pak existují některé další obavy, které je třeba zvážit.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synchronizace s existujícími uživateli ve službě Azure AD
Když nainstalujete Azure AD Connect a začnete synchronizovat, služba synchronizace Azure AD (ve službě Azure AD) prověří každý nový objekt a pokusí se najít existující objekt, který se bude shodovat. Existují tři atributy používané pro tento proces: **userPrincipalName**, **proxyAddresses**a **sourceAnchor**/**immutableID**. Shoda na **userPrincipalName** a **proxyAddresses** se označuje jako **měkká shoda**. Shoda na **sourceAnchor** je označována jako **tvrdá shoda**. Pro **atribut proxyAddresses** se pro vyhodnocení používá pouze hodnota s **smtp:**, což je primární e-mailová adresa.

Shoda je vyhodnocena pouze pro nové objekty přicházející z aplikace Connect. Pokud změníte existující objekt tak, aby odpovídal některéz těchto atributů, zobrazí se místo toho chyba.

Pokud Azure AD najde objekt, kde hodnoty atributů jsou stejné pro objekt pocházející z Připojení a že je již k dispozici ve službě Azure AD, pak objekt ve službě Azure AD převezme Connect. Dříve objekt spravovaný v cloudu je označen jako místní spravované. Všechny atributy ve službě Azure AD s hodnotou v místním ad jsou přepsány místní hodnotou. Výjimkou je, když atribut má hodnotu **NULL** v místním prostředí. V tomto případě zůstane hodnota ve službě Azure AD, ale stále můžete změnit pouze místně na něco jiného.

> [!WARNING]
> Vzhledem k tomu, že všechny atributy ve službě Azure AD budou přepsány místní hodnotou, ujistěte se, že máte dobrá data v místním prostředí. Pokud jste například měli jenom spravovanou e-mailovou adresu v Office 365 a neaktualizovali ji v místním ad DS, ztratíte všechny hodnoty ve službě Azure AD/Office 365, která se ve službě AD DS nenachází.

> [!IMPORTANT]
> Pokud používáte synchronizaci hesel, která se vždy používá expresní nastavení, pak heslo ve službě Azure AD je přepsáno heslem v místním ad. Pokud se uživatelé používají ke správě různých hesel, musíte je informovat, že by měli používat místní heslo po instalaci služby Connect.

Předchozí část a upozornění musí být zohledněny při plánování. Pokud jste provedli mnoho změn ve službě Azure AD, které se neprojeví v místním službu AD DS, musíte naplánovat, jak naplnit službu AD DS aktualizovanými hodnotami před synchronizací objektů s Azure AD Connect.

Pokud jste spárovali objekty s měkkou shodou, pak **sourceAnchor** je přidán do objektu ve službě Azure AD, takže tvrdá shoda lze použít později.

>[!IMPORTANT]
> Společnost Microsoft důrazně doporučuje nesynchronizovat místní účty s již existujícími účty pro správu ve službě Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Tvrdý zápas vs Soft-zápas
Pro novou instalaci Connect, není žádný praktický rozdíl mezi soft- a hard-match. Rozdíl je v situaci zotavení po havárii. Pokud jste ztratili server pomocí Služby Azure AD Connect, můžete přeinstalovat novou instanci bez ztráty dat. Objekt s sourceAnchor je odeslána připojit během počáteční instalace. Shoda pak může vyhodnotit klienta (Azure AD Connect), což je mnohem rychlejší než dělat totéž ve službě Azure AD. Tvrdá shoda je vyhodnocována pomocí connect a Azure AD. Měkká shoda je pouze vyhodnocována azure ad.

### <a name="other-objects-than-users"></a>Jiné objekty než uživatelé
Pro skupiny a kontakty s povolenou poštou můžete soft-match na základě proxyAdresy. Hard-match není použitelná, protože můžete aktualizovat pouze sourceAnchor/immutableID (pomocí prostředí PowerShell) pouze pro uživatele. Pro skupiny, které nejsou povoleny poštou, v současné době neexistuje žádná podpora pro soft-match nebo hard-match.

### <a name="admin-role-considerations"></a>Důležité informace o roli správce
Chcete-li zabránit nedůvěryhodným místním uživatelům ve shodě s uživatelem cloudu, který má jakoukoli roli správce, Azure AD Connect nebude odpovídat místní uživatelské objekty s objekty, které mají roli správce. Toto je ve výchozím nastavení. Chcete-li toto chování vyřešit, postupujte takto:

1.  Odeberte role adresáře z objektu uživatele pouze v cloudu.
2.  Pokud došlo k neúspěšnému pokusu o synchronizaci uživatele, pevný odstranit objekt v karanténě v cloudu.
3.  Spusťte synchronizaci.
4.  Volitelně přidat role adresáře zpět do objektu uživatele v cloudu, jakmile dojde k odpovídající.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Vytvoření nového místního služby Active Directory z dat ve službě Azure AD
Někteří zákazníci začínají s řešením pouze pro cloud s Azure AD a nemají místní službu AD. Později chtějí využívat místní prostředky a chtějí vytvořit místní službu AD založenou na datech Azure AD. Azure AD Connect vám nemůže pomoci pro tento scénář. Nevytváří uživatele v místním prostředí a nemá žádnou možnost nastavit místní heslo na stejné jako ve službě Azure AD.

Pokud jediný důvod, proč plánujete přidat místní služby AD je podpora LOBBs (Line-of-Business aplikace), pak možná byste měli zvážit použití [služby domény Azure AD](../../active-directory-domain-services/index.yml) místo.

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
