---
title: 'Azure AD Connect synchronizace: technické koncepty | Microsoft Docs'
description: Vysvětluje technické koncepce Azure AD Connect synchronizace.
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
ms.topic: how-to
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 01e53b30a4c27296e30e031ffb771697afa8e1e9
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "87019671"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Synchronizace služby Azure AD Connect: Technické koncepty
Tento článek je souhrnem tématu [Principy architektury](how-to-connect-sync-technical-concepts.md).

Azure AD Connect synchronizovat buildy na pevné platformě pro synchronizaci Metaadresáře.
V následujících částech jsou uvedeny koncepty pro Metaadresáře synchronizaci.
Služba Azure Active Directory Sync zajišťuje na MIIS (Microsoft Identity Integration Server), ILM (správce životního cyklu identit) a FIM (Forefront Identity Manager) další platformu pro připojení ke zdrojům dat, synchronizaci dat mezi zdroji dat a také zřizování a rušení zřizování identit.

![Technické koncepce](./media/how-to-connect-sync-technical-concepts/scenario.png)

Následující části obsahují další podrobnosti o následujících aspektech služby synchronizace FIM:

* Konektor
* Tok atributu
* Místo konektoru
* Metaverse
* Zřizování

## <a name="connector"></a>Konektor
Kódové moduly, které se používají ke komunikaci s připojeným adresářem, se nazývají konektory (dříve označované jako agenti pro správu (MAs)).

Tyto jsou nainstalovány na počítači se spuštěným Azure AD Connect synchronizace. Konektory poskytují nezávisle na nasazení specializovaných agentů možnost konverzace pomocí vzdálených systémových protokolů. To znamená, že se snížila rizika a doby nasazení, zejména při práci s důležitými aplikacemi a systémy.

Na obrázku výše je konektor synonymem mezery mezi konektory, ale zahrnuje veškerou komunikaci s externím systémem.

Konektor zodpovídá za všechny funkce importu a exportu systému a vývojářům uvolňuje informace o tom, jak se připojit ke každému systému nativně při použití deklarativního zřizování pro přizpůsobení transformace dat.

K importu a exportu dochází pouze tehdy, když je naplánováno, což umožňuje další izolaci změn, ke kterým dochází v rámci systému, protože změny se nešíří automaticky do připojeného zdroje dat. Kromě toho mohou vývojáři také vytvořit vlastní konektory pro připojení k prakticky libovolnému zdroji dat.

## <a name="attribute-flow"></a>Tok atributu
Metaverse je konsolidované zobrazení všech připojených identit z prostorů sousedících konektorů. Na obrázku výše je tok atributů zobrazen pomocí čar se šipkami pro příchozí i výstupní tok. Tok atributů je proces kopírování nebo transformace dat z jednoho systému do druhého a všech toků atributů (příchozí nebo odchozí).

Tok atributů probíhá mezi prostorem konektoru a Metaverse v úložišti Metaverse v době, kdy je naplánováno spuštění synchronizace (úplné nebo rozdílové) operace.

Tok atributů probíhá pouze v případě, že jsou spuštěny tyto synchronizace. V pravidlech synchronizace jsou definovány toky atributů. Můžou to být příchozí (ISR na obrázku výše) nebo odchozí (OSR na obrázku výše).

## <a name="connected-system"></a>Připojený systém
Připojený systém (označuje se také jako připojený adresář) odkazuje na vzdálený systém Azure AD Connect synchronizace se připojila k a čte a zapisuje data identity do a z.

## <a name="connector-space"></a>Místo konektoru
Každý připojený zdroj dat je reprezentován jako filtrovaná podmnožina objektů a atributů v prostoru konektoru.
Díky tomu může synchronizační služba fungovat místně, aniž by bylo nutné kontaktovat vzdálený systém při synchronizaci objektů a omezit interakci jenom na Import a export.

Když zdroj dat a konektor mají možnost poskytnout seznam změn (rozdílový import), pak se provozní efektivita zvyšuje až od posledního vyměňování cyklu cyklického dotazování. Prostor konektoru zaizolovaný s připojeným zdrojem dat ze změn, které se šíří automaticky, vyžaduje, aby plán konektoru import a export. Toto přidané pojištění vám uděluje pocit při testování, náhledu nebo potvrzení další aktualizace.

## <a name="metaverse"></a>Metaverse
Metaverse je konsolidované zobrazení všech připojených identit z prostorů sousedících konektorů.

Jelikož jsou identity spojeny společně a autorita je přiřazena k různým atributům prostřednictvím mapování toků importu, začíná centrálním objektem Metaverse agregované informace z více systémů. Z tohoto toku atributů objektu mapování přenáší informace do odchozích systémů.

Objekty se vytvoří, když je autoritativní systém projektuje do úložiště metaverse. Jakmile se všechna připojení odeberou, objekt Metaverse se odstraní.

Objekty v úložišti Metaverse nelze upravovat přímo. Všechna data v objektu musí být předávána prostřednictvím toku atributů. Metaverse udržuje trvalé konektory s každým místem konektoru. Tyto konektory nevyžadují opakované vyhodnocení každého spuštění synchronizace. To znamená, že Azure AD Connect synchronizace nemusí pokaždé vyhledat odpovídající vzdálený objekt. Tím se vyhnete nutnosti nákladným agentům zabránit změnám atributů, které by normálně byly zodpovědné za korelaci objektů.

Při zjišťování nových zdrojů dat, které mohou mít předem existující objekty, které je třeba spravovat, Azure AD Connect synchronizace používá proces nazvaný pravidlo JOIN k vyhodnocení potenciálních kandidátů, se kterými se vytvoří odkaz.
Po navázání spojení se toto vyhodnocení neprojeví a normální tok atributů může nastat mezi vzdáleným připojeným zdrojem dat a úložištěm Metaverse.

## <a name="provisioning"></a>Zřizování
Když autoritativní zdrojové projekty vytvoří nový objekt do úložiště metaverse, dá se vytvořit nový objekt prostoru konektoru v jiném konektoru, který představuje datový zdroj připojený pro příjem dat.

To v podstatě vytvoří odkaz a tok atributů může pokračovat v obousměrném směru.

Pokaždé, když pravidlo určí, že je potřeba vytvořit nový objekt prostoru konektoru, se nazývá zřizování. Vzhledem k tomu, že tato operace probíhá pouze v prostoru konektoru, nebude přenesena do připojeného zdroje dat, dokud nebude proveden export.

## <a name="additional-resources"></a>Další materiály
* [Azure AD Connect synchronizace: přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
