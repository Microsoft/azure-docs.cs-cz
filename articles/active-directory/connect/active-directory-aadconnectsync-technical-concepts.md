---
title: 'Synchronizace Azure AD Connect: technické koncepty | Microsoft Docs'
description: Vysvětluje technické koncepty synchronizace Azure AD Connect.
services: active-directory
documentationcenter: ''
author: billmath
manager: mtillman
editor: ''
ms.assetid: 731cfeb3-beaf-4d02-aef4-b02a8f99fd11
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/15/2018
ms.component: hybrid
ms.author: markvi;andkjell
ms.openlocfilehash: 591f67747316b950f32c5a113edda1080ed814a0
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/01/2018
ms.locfileid: "34592836"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Synchronizace služby Azure AD Connect: Technické koncepty
Tento článek je uveden seznam tématu [Principy architektura](active-directory-aadconnectsync-technical-concepts.md).

Synchronizace Azure AD Connect je založen na plnou metaadresáře synchronizace platformy.
V následujících částech zavést koncepty metaadresáře synchronizace.
Sestavování na serveru MIIS, ILM a FIM, synchronizaci služby Active Directory Azure poskytuje další platformy pro připojení ke zdrojům dat, synchronizaci dat mezi zdroje dat, jakož i zajišťování a rušení zajištění identity.

![Technické koncepce](./media/active-directory-aadconnectsync-technical-concepts/scenario.png)

Následující části obsahují další podrobnosti o následující aspekty synchronizační služba FIM:

* Spojovací čára
* Toku atributů
* Prostoru konektoru
* Úložiště Metaverse
* Zřizování

## <a name="connector"></a>Spojovací čára
Moduly kódu, které se používají ke komunikaci s připojený adresář se nazývají konektory (dříve označované jako agenti pro správu (MAs)).

Byly nainstalovány na počítači se systémem synchronizace Azure AD Connect. Konektory umožňují bez agentů komunikaci pomocí protokolů vzdáleného systému namísto spoléhání na nasazení specializované agentů. To znamená ke snížení rizika a dobu nasazení, zejména v případě, že zabývající se kritické aplikace a systémy.

Na obrázku výše konektor je totožná s prostoru konektoru, ale zahrnuje veškerou komunikaci s externím systému.

Konektor je zodpovědná za všechny import a export funkcí do systému a uvolní vývojáři z museli pochopit, jak se připojit do každého systému nativně při přizpůsobení transformace dat pomocí deklarativní zřizování.

Import a export pouze v případě naplánované, aby vám umožnil další izolaci od změny, ke kterým dochází v rámci systému, protože změny nejsou automaticky rozšířena do připojeného zdroje dat. Kromě toho mohou vývojáři vytvořit svoje vlastní konektory pro připojení k prakticky libovolný zdroj dat.

## <a name="attribute-flow"></a>Toku atributů
Úložiště metaverse je konsolidované zobrazení všechny připojené k identit od sousedního prostor konektoru. Ve výše uvedeném obrázku je znázorněn toku atributů linkami se šipkami pro příchozí a odchozí tok. Tok atributů je proces kopírování nebo transformace dat v jednom systému a všech atributů toků (příchozí nebo odchozí).

Tok atributů probíhá mezi prostoru konektoru a úložiště metaverse obousměrně když jsou naplánované operace synchronizace (úplná nebo rozdílová).

Tok atributů dochází pouze při spuštění těchto synchronizace. Toky atributů jsou definovány v synchronizační pravidla. To může být příchozí (ISR na obrázku výše) nebo odchozí (OSR na obrázku výše).

## <a name="connected-system"></a>Připojený systém
Připojený systém (neboli připojený adresář) je odkazující na vzdáleném systému Azure AD Connect sync připojil k a čtení a zápis dat identity do a z.

## <a name="connector-space"></a>Prostoru konektoru
Každý připojeného zdroje dat je reprezentován jako filtrované podmnožina objektů a atributů v prostoru konektoru.
To umožňuje pracovat místně bez nutnosti kontaktování vzdáleného systému při synchronizaci objekty služby sync a omezuje interakci importuje a exportuje jenom.

Pokud zdroj dat a konektor schopnost poskytovat seznam sad změn (Rozdílový import), pak provozní efektivitu zvyšuje výrazně jako pouze změny od posledního cyklu dotazování se vyměňují. Prostoru konektoru insulates připojeného zdroje dat z šíření automaticky tím, že vyžaduje, aby plán konektor importuje a exportuje změny. Tato přidané pojištění uděluje jistotu při testování, zobrazení náhledu nebo potvrzení, že příští aktualizace.

## <a name="metaverse"></a>Úložiště Metaverse
Úložiště metaverse je konsolidované zobrazení všechny připojené k identit od sousedního prostor konektoru.

Jako identity jsou spojeny dohromady a je mu přiřazená autority pro různé atributy prostřednictvím mapování toku importu, objektu centrální úložiště metaverse začne shromažďují informace z více systémů. Mapování tohoto datového toku atributů objektu provádění informace, které odchozí systémy.

Objekty vytvářejí, když je autoritativní systému projekty do úložiště metaverse. Jakmile se odeberou všechna připojení, je odstraněn objekt úložiště metaverse.

Objekty v úložišti metaverse nelze upravovat přímo. Všechna data v objektu musí být podílí prostřednictvím toku atributů. Úložiště metaverse udržuje trvalé konektory s každou prostoru konektoru. Tyto konektory nevyžadují přehodnocení pro každou synchronizaci spustit. To znamená, že synchronizace Azure AD Connect nemá najít odpovídající vzdálenému objektu pokaždé, když. Tím je zabráněno potřebu nákladná agentů, aby se zabránilo změny atributů, které obvykle bude zodpovídat za korelace objekty.

Při zjišťování nových zdrojů dat, které mohou mít dříve existující objekty, které je třeba spravovat, synchronizace Azure AD Connect procesu označovaného jako pravidlo připojení používá k vyhodnocení potenciální kandidáty ke které má vytvořit odkaz.
Po vytvoření odkazu výskytu toto testování a toku normální atributů může proběhnout mezi vzdálené připojeného zdroje dat a úložiště metaverse.

## <a name="provisioning"></a>Zřizování
Když autoritativní zdroj projekty nový objekt do úložiště metaverse nového objektu prostoru konektoru lze vytvořit v představující podřízené připojeného zdroje dat jiný konektor.

To vytváří ze své podstaty odkaz a obousměrně pokračovat toku atributů.

Vždy, když pravidlo zjistí, že je potřeba vytvořit nový objekt prostoru konektoru, nazývá se zřizování. Ale protože tato operace je provedeno pouze v rámci prostoru konektoru, je neprovádí do připojeného zdroje dat do provedení exportu.

## <a name="additional-resources"></a>Další prostředky
* [Azure AD Connect Sync: Možnosti přizpůsobení synchronizace](active-directory-aadconnectsync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](active-directory-aadconnect.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
