---
title: 'Synchronizace služby Azure AD Connect: Technické koncepty | Dokumenty společnosti Microsoft'
description: Vysvětluje technické koncepty synchronizace Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: b8ec4a6100cfbb4419d7e30f4b97589113b88939
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "60347566"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Synchronizace služby Azure AD Connect: Technické koncepty
Tento článek je souhrnem tématu [Principy architektury](how-to-connect-sync-technical-concepts.md).

Synchronizace Azure AD Connect se staví na platformě synchronizace pevného metaadresáře.
V následujících částech jsou uvedeny koncepty synchronizace metaadresářů.
V návaznosti na MIIS, ILM a FIM, Azure Active Directory Sync Services poskytuje další platformu pro připojení ke zdrojům dat, synchronizaci dat mezi zdroji dat, stejně jako zřizování a deprovisioning identit.

![Technické koncepce](./media/how-to-connect-sync-technical-concepts/scenario.png)

V následujících částech jsou uvedeny další podrobnosti o následujících aspektech služby synchronizace FIM:

* Konektor
* Tok atributu
* Prostor konektoru
* Metaverse
* Zřizování

## <a name="connector"></a>Konektor
Moduly kódu, které se používají ke komunikaci s připojeným adresářem, se nazývají konektory (dříve známé jako agenti správy (MAs)).

Ty se nainstalované v počítači se synchronizací Azure AD Connect. Konektory poskytují možnost bez agenta konverzovat pomocí vzdálených systémových protokolů namísto spoléhání se na nasazení specializovaných agentů. To znamená snížení rizika a doby nasazení, zejména při práci s kritickými aplikacemi a systémy.

Na obrázku výše je konektor synonymem pro prostor konektoru, ale zahrnuje veškerou komunikaci s externím systémem.

Spojnice je zodpovědná za všechny funkce importu a exportu do systému a osvobozuje vývojáře od nutnosti pochopit, jak se připojit ke každému systému nativně při použití deklarativnízři přizpůsobit transformace dat.

K importu a exportu dochází pouze v rámci plánu, což umožňuje další izolaci před změnami, ke kterým dochází v systému, protože změny se automaticky nešíří do připojeného zdroje dat. Kromě toho mohou vývojáři také vytvořit vlastní konektory pro připojení k prakticky libovolnému zdroji dat.

## <a name="attribute-flow"></a>Tok atributu
Metaverse je konsolidované zobrazení všech spojených identit ze sousedních spojnicových prostorů. Na výše uvedeném obrázku je tok atributů zobrazen čarami se šipkami pro příchozí i odchozí tok. Tok atributů je proces kopírování nebo transformace dat z jednoho systému do druhého a všechny toky atributů (příchozí nebo odchozí).

Tok atributů probíhá mezi prostorem konektoru a metaverse obousměrně při synchronizaci (úplné nebo delta) operace jsou naplánovány ke spuštění.

Tok atributů dochází pouze při spuštění těchto synchronizací. Toky atributů jsou definovány v pravidlech synchronizace. Ty mohou být příchozí (ISR na obrázku výše) nebo odchozí (OSR na obrázku výše).

## <a name="connected-system"></a>Připojený systém
Připojený systém (aka připojený adresář) odkazuje na vzdálený systém Azure AD Connect synchronizace se připojil a čtení a zápis dat identity do a z.

## <a name="connector-space"></a>Prostor konektoru
Každý připojený zdroj dat je reprezentován jako filtrovaná podmnožina objektů a atributů v prostoru spojnice.
To umožňuje synchronizační služby pracovat místně bez nutnosti kontaktovat vzdálený systém při synchronizaci objektů a omezuje interakci pouze na importy a exporty.

Pokud zdroj dat a konektor mají schopnost poskytnout seznam změn (delta import), pak provozní efektivita dramaticky zvyšuje jako pouze změny od posledního cyklem dotazování jsou vyměňovány. Prostor konektoru izoluje připojený zdroj dat od změn, které se automaticky šíří, tím, že vyžaduje, aby konektor naplánoval importaci a export. Toto přidané pojištění vám poskytuje klid při testování, náhledu nebo potvrzení další aktualizace.

## <a name="metaverse"></a>Metaverse
Metaverse je konsolidované zobrazení všech spojených identit ze sousedních spojnicových prostorů.

Jako identity jsou vzájemně propojeny a autorita je přiřazena pro různé atributy prostřednictvím mapování toku importu, centrální metaverse objekt začne agregovat informace z více systémů. Z tohoto toku atributů objektu mapování přenášet informace do odchozích systémů.

Objekty jsou vytvořeny, když autoritativní systém promítá je do metaverse. Jakmile jsou všechna připojení odebrána, objekt metaverse je odstraněn.

Objekty v metaverse nelze upravovat přímo. Všechna data v objektu musí být vložena prostřednictvím toku atributů. Metaverse udržuje trvalé konektory s každým prostorem konektoru. Tyto konektory nevyžadují přecenění pro každé spuštění synchronizace. To znamená, že synchronizace Azure AD Connect nemusí pokaždé vyhledat odpovídající vzdálený objekt. Tím se zabrání nutnosti nákladné agenty, aby se zabránilo změnám atributy, které by za normálních okolností odpovědný za korelaci objektů.

Při zjišťování nových zdrojů dat, které mohou mít již existující objekty, které je třeba spravovat, azure ad connect synchronizace používá proces nazývaný pravidlo spojení k vyhodnocení potenciálních kandidátů, s nimiž chcete vytvořit propojení.
Jakmile je propojení vytvořeno, toto vyhodnocení se neopakovalo a mezi vzdáleným připojeným zdrojem dat a metaversem může dojít k normálnímu toku atributů.

## <a name="provisioning"></a>Zřizování
Když autoritativní zdroj promítne nový objekt do metaverse, může být vytvořen nový objekt prostoru konektoru v jiném konektoru představujícím následný připojený zdroj dat.

To neodmyslitelně vytvoří propojení a tok atributů může pokračovat obousměrně.

Vždy, když pravidlo určuje, že je třeba vytvořit nový objekt prostoru spojnice, nazývá se zřizování. Protože však tato operace probíhá pouze v prostoru konektoru, nepřenáší se do připojeného zdroje dat, dokud není proveden export.

## <a name="additional-resources"></a>Další zdroje
* [Synchronizace připojení Azure AD: Přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
