---
title: 'Azure AD Connect: když už máte Azure AD | Microsoft Docs'
description: Toto téma popisuje, jak použít připojení, když máte existujícího tenanta Azure AD.
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
ms.topic: how-to
ms.date: 04/25/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 68251270b6273f5a07391138e5c7210f1c46ba5a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "93420525"
---
# <a name="azure-ad-connect-when-you-have-an-existing-tenant"></a>Azure AD Connect: když máte existujícího tenanta
Většina témat pro použití Azure AD Connect předpokládá, že začnete s novým tenant Azure AD a neexistují žádní uživatelé ani jiné objekty. Ale pokud jste spustili klienta Azure AD, naplnili ho uživateli a dalšími objekty a teď chcete použít připojit, bude toto téma pro vás.

## <a name="the-basics"></a>Základy
Objekt ve službě Azure AD je buď v cloudu (Azure AD), nebo v místním prostředí. Pro jeden jeden objekt nemůžete spravovat některé atributy místně a některé atributy v Azure AD. Každý objekt má příznak označující, kde je objekt spravován.

Můžete spravovat některé uživatele místně i v cloudu. Běžným scénářem této konfigurace je organizace se smíšenými pracovními pracovníky a prodejními pracovníky. Účetní pracovníci mají místní účet služby AD, ale prodejci k tomu nemají účet ve službě Azure AD. Mohli byste spravovat některé uživatele místně a v Azure AD.

Pokud jste začali spravovat uživatele ve službě Azure AD, které jsou také v místní službě AD a později chcete použít možnost připojit, je potřeba vzít v úvahu ještě nějaké další aspekty.

## <a name="sync-with-existing-users-in-azure-ad"></a>Synchronizace se stávajícími uživateli v Azure AD
Když nainstalujete Azure AD Connect a začnete synchronizovat, služba synchronizace Azure AD (v Azure AD) provede kontrolu každého nového objektu a pokusí se najít existující objekt, který se má shodovat. Pro tento proces se používají tři atributy: **userPrincipalName**, **proxyAddresses** a **sourceAnchor** / **immutableID**. Shoda v atributech **userPrincipalName** a **proxyAddresses** se označuje jako **příčasná shoda**. Shoda na **sourceAnchor** se označuje jako **těžká shoda**. Pro atribut **proxyAddresses** se pro vyhodnocení používá jenom hodnota s **SMTP:**, která je primární e-mailová adresa.

Shoda se vyhodnocuje jenom pro nové objekty, které pocházejí z připojení. Pokud změníte existující objekt tak, aby odpovídal jakémukoli z těchto atributů, zobrazí se místo toho chyba.

Pokud Azure AD najde objekt, kde jsou hodnoty atributu stejné pro objekt přicházející z připojení a že už je ve službě Azure AD, je objekt ve službě Azure AD převzatý pomocí připojit. Dřív spravovaný objekt v cloudu je označen jako místní správa. Všechny atributy v Azure AD s hodnotou v místní službě AD se přepíší pomocí místní hodnoty.

> [!WARNING]
> Vzhledem k tomu, že všechny atributy ve službě Azure AD budou přepsány místní hodnotou, ujistěte se, že máte dobrá data v místním prostředí. Pokud například máte spravované e-mailové adresy v Microsoft 365 a nezůstane aktualizované v místní služba AD DS, pak ztratíte všechny hodnoty v Azure AD nebo Microsoft 365 nepřítomné v služba AD DS.

> [!IMPORTANT]
> Pokud používáte synchronizaci hesla, která se vždycky používá v expresním nastavení, pak se heslo ve službě Azure AD přepíše heslem v místní službě AD. Pokud se uživatelé používají ke správě různých hesel, musíte je informovat, aby po instalaci připojení používali místní heslo.

Předchozí část a upozornění se musí vzít v úvahu při plánování. Pokud jste provedli mnoho změn v Azure AD, které se v místních služba AD DS neprojeví, je třeba naplánovat, jak naplnit služba AD DS aktualizované hodnoty před synchronizací objektů pomocí Azure AD Connect.

Pokud jste se shodovali s objekty, které se neshodují, pak se **sourceAnchor** přidá do objektu ve službě Azure AD, aby bylo možné později použít těžkou shodu.

>[!IMPORTANT]
> Microsoft důrazně doporučuje synchronizaci místních účtů s již existujícími účty pro správu v Azure Active Directory.

### <a name="hard-match-vs-soft-match"></a>Neshoda s pevným porovnáním
Pro novou instalaci programu Connect není k dispozici žádný praktický rozdíl mezi měkkými a tvrdými shodami. Rozdíl je v situaci zotavení po havárii. Pokud jste server ztratili pomocí Azure AD Connect, můžete znovu nainstalovat novou instanci bez ztráty dat. Objekt s sourceAnchor se pošle k připojení během počáteční instalace. Tuto shodu je pak možné vyhodnotit klientem (Azure AD Connect), což je hodně rychlejší než v Azure AD. Neshoda se vyhodnocuje jak pomocí připojení, tak pomocí Azure AD. Dočasná shoda se vyhodnocuje jenom pomocí Azure AD.

### <a name="other-objects-than-users"></a>Jiné objekty než uživatelé
U skupin a kontaktů s povoleným e-mailem se můžete na základě proxyAddresses vyrovnávat. Neshoda se nedá použít, protože pro uživatele můžete aktualizovat jenom sourceAnchor/immutableID (pomocí PowerShellu). Pro skupiny, které nejsou povolené poštou, momentálně není dostupná žádná podpora pro částečný nebo neshodu.

### <a name="admin-role-considerations"></a>Požadavky role správce
Chcete-li zabránit nedůvěryhodným místním uživatelům v párování s uživatelem cloudu, který má roli správce, Azure AD Connect nebudou odpovídat místním uživatelským objektům s objekty, které mají roli správce. Toto je ve výchozím nastavení. K řešení tohoto problému můžete postupovat takto:

1.  Odeberte role adresáře z uživatelského objektu jenom pro Cloud.
2.  Pokud při pokusu o synchronizaci uživatelů došlo k chybě, pevným odstraněním objektu v karanténě v cloudu.
3.  Aktivujte synchronizaci.
4.  Volitelně můžete přidat role adresáře zpátky do objektu uživatele v cloudu, jakmile k němu dojde k shodě.



## <a name="create-a-new-on-premises-active-directory-from-data-in-azure-ad"></a>Vytvoření nové místní služby Active Directory z dat v Azure AD
Někteří zákazníci začínají s Azure AD jenom s cloudovým řešením a nemají místní službu AD. Později chtějí využívat místní prostředky a chtít vytvořit místní službu AD založenou na datech Azure AD. Azure AD Connect vám nemůže při tomto scénáři pomáhat. Nevytváří uživatele místně a nemá žádnou možnost nastavit místní heslo na stejné místo jako v Azure AD.

Pokud je jediným důvodem, proč plánujete přidat místní službu AD, podporu objekty LOBs s (obchodní aplikace), možná byste měli zvážit použití [služby Azure AD Domain Services](../../active-directory-domain-services/index.yml) .

## <a name="next-steps"></a>Další kroky
Přečtěte si další informace o [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md).
