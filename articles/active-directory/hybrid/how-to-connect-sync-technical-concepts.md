---
title: 'Synchronizace Azure AD Connect: Technické koncepce | Dokumentace Microsoftu'
description: Popisuje technické koncepty synchronizace Azure AD Connect.
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
ms.topic: article
ms.date: 01/15/2018
ms.subservice: hybrid
ms.author: billmath
ms.openlocfilehash: e56d5b7a38a5cc467ee0752e1af40255856f860b
ms.sourcegitcommit: d3200828266321847643f06c65a0698c4d6234da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/29/2019
ms.locfileid: "55198125"
---
# <a name="azure-ad-connect-sync-technical-concepts"></a>Synchronizace Azure AD Connect: Technické koncepce
Tento článek je uveden seznam tématu [vysvětlení architektury](how-to-connect-sync-technical-concepts.md).

Synchronizace Azure AD Connect navazuje na platformě solid metaadresáře synchronizace.
Následující části představují koncepty metaadresáře synchronizace.
Sestavení na serveru MIIS, ILM a FIM, služby Azure Active Directory Sync poskytuje další platformu pro připojení ke zdrojům dat, synchronizaci dat mezi zdroje dat, jakož i zřizování a rušení zřízení identit.

![Technické koncepce](./media/how-to-connect-sync-technical-concepts/scenario.png)

Následující části obsahují další informace o následujících charakteristik ve FIM Synchronization Service:

* Spojovací čára
* Tok atributů
* Prostor konektoru
* Úložiště Metaverse
* Zřizování

## <a name="connector"></a>Spojovací čára
Konektory (dříve označované jako agenti pro správu (MAs)), se nazývají moduly kódu, které se používají ke komunikaci s připojený adresář.

Tyto jsou nainstalovány v počítači se službou Azure AD Connect sync. Konektory umožňují bez agentů komunikaci s použitím protokolů vzdálený systém, aniž byste museli spoléhat na nasazení specializované agenty. To znamená snížení rizik a časy nasazení, zejména při práci s důležitá data a systémy.

Na obrázku výše konektor je synonymní s prostoru konektoru, ale zahrnuje veškerá komunikace s externím systémem.

Konektor je zodpovědná za všechny import a export funkcí do systému a vaši vývojáři před nutností pochopit, jak se připojit k každý systém nativně při přizpůsobení transformace dat pomocí deklarativní zřizování.

Importy a exporty dojde, jenom když naplánované, umožňující další izolaci od změn, ke kterým dochází v rámci systému, protože změny nejsou automaticky rozšířena do zdroje dat. Vývojáři navíc také vytvořit svoje vlastní konektory pro připojení k prakticky libovolný zdroj dat.

## <a name="attribute-flow"></a>Tok atributů
Konsolidované zobrazení všechny připojené k doméně identit z sousedních prostor konektoru se dá úložiště metaverse. Ve výše uvedeném obrázku je znázorněn tok atributů těmito řádky pomocí šipky pro příchozí i odchozí tok. Tok atributů je proces kopírování nebo transformace dat z jednoho systému do druhého a označte všechny toky (příchozí nebo odchozí).

Tok atributů nastane mezi connector space a metaverse obousměrně operací (úplnou nebo rozdílovou) synchronizaci jsou naplánovány ke spuštění.

Tok atributů dochází pouze při spuštění těchto synchronizace. Toky atributů jsou definované v pravidlech synchronizace. Mohou to být příchozí (ISR na obrázku výše) nebo odchozí (OSR na obrázku výše).

## <a name="connected-system"></a>Připojený systém
Připojený systém (neboli připojený adresář) odkazující na vzdáleném systému Azure AD Connect sync se připojil k a čte a zapisuje data identit do a z.

## <a name="connector-space"></a>Prostor konektoru
Každý zdroj dat je vyjádřena jako filtrované podmnožinu objektů a atributů v prostoru konektoru.
To umožňuje službě synchronizace pracovat místně bez nutnosti kontaktovat vzdáleného systému při synchronizaci objektů a omezuje interakce importy a exportuje pouze.

Pokud zdroj dat a konektor schopnost poskytovat seznam změn (Rozdílový import), pak provozní efektivitu zvyšuje výrazně jako pouze změny od posledního cyklu dotazování se vyměňují. V prostoru konektoru insulates připojeného zdroje dat od změn šíření automaticky tak, že vyžaduje, že plán konektoru importy a exporty. Tento přidaný pojištění uděluje klid při testování, náhled nebo potvrzení příští aktualizace.

## <a name="metaverse"></a>Úložiště Metaverse
Konsolidované zobrazení všechny připojené k doméně identit z sousedních prostor konektoru se dá úložiště metaverse.

Identity jsou spojeny dohromady a autorita je přiřazené pro různé atributy prostřednictvím import mapování toku, objektu centrální úložiště metaverse začne shromažďují informace z několika systémů. Z tohoto toku atributů objektu mapování nesou informaci na odchozí systémy.

Objekty jsou vytvořeny při autoritativní systému projektů je do úložiště metaverse. Jakmile se odeberou všechna připojení, odstranění objektu úložiště metaverse.

Objekty v úložišti metaverse nelze přímo upravovat. Všechna data v objektu musí přispět prostřednictvím toku atributů. Úložiště metaverse udržuje trvalé konektory s každou prostoru konektoru. Tyto konektory nevyžadují opětovného hodnocení pro každou synchronizaci spustit. To znamená, že synchronizace Azure AD Connect nemusí najít odpovídající vzdáleného objektu pokaždé, když. Tím se vyhnete potřebu nákladných agentů účelem Neumožnit změny atributů, které obvykle bude zodpovídat za korelace objekty.

Při zjištění nové zdroje dat, které můžou mít dříve existující objekty, které je potřeba spravovat, synchronizace Azure AD Connect používá proces s názvem pravidla spojení vyhodnotit potenciální kandidáty, pomocí kterého se má vytvořit propojení.
Po vytvoření propojení toto vyhodnocení není objevit znovu a tok atributů normální mohla probíhat vzdálené připojeného zdroje dat a metaverse.

## <a name="provisioning"></a>Zřizování
Když autoritativní source projekty můžete v jiný konektor představující podřízené připojeného zdroje dat vytvořen nový objekt do úložiště metaverse nový objekt prostoru konektoru.

Tím se ze své podstaty vytvoří odkaz a tok atributů obousměrně pokračovat.

Pokaždé, když se pravidlo zjistí, že je potřeba vytvořit nový objekt prostoru konektoru, je volána zřizování. Ale protože tuto operaci je provedeno pouze v prostoru konektoru, to není přenesou do připojeného zdroje dat dokud probíhá export.

## <a name="additional-resources"></a>Další prostředky
* [Synchronizace služby Azure AD Connect: Přizpůsobení možností synchronizace](how-to-connect-sync-whatis.md)
* [Integrování místních identit do služby Azure Active Directory](whatis-hybrid-identity.md)

<!--Image references-->
[1]: ./media/active-directory-aadsync-technical-concepts/ic750598.png
