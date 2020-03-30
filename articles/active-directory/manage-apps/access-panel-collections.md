---
title: Vytváření kolekcí pro portály Moje aplikace ve službě Azure Active Directory | Dokumenty společnosti Microsoft
description: Pomocí kolekcí Moje aplikace můžete přizpůsobit stránky Moje aplikace pro jednodušší práci s aplikacemi moje aplikace pro koncové uživatele. Uspořádejte aplikace do skupin se samostatnými kartami.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/10/2020
ms.author: mimart
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: c91b9ffc9e3487e492c91cb0f5825d0b725f9410
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/27/2020
ms.locfileid: "77120098"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Vytváření kolekcí na portálu Moje aplikace

Uživatelé mohou pomocí portálu Moje aplikace zobrazit a spustit cloudové aplikace, ke kterým mají přístup. Ve výchozím nastavení jsou všechny aplikace, ke kterým má uživatel přístup, uvedeny společně na jedné stránce. Pokud máte licenci Azure AD Premium P1 nebo P2, můžete lépe uspořádat tuto stránku pro uživatele. Pomocí kolekce můžete seskupit aplikace, které spolu souvisejí (například podle role úlohy, úkolu nebo projektu) a zobrazit je na samostatné kartě. Kolekce v podstatě použije filtr pro aplikace, ke kterým má uživatel přístup, takže uživatel vidí pouze ty aplikace v kolekci, které jim byly přiřazeny.

> [!NOTE]
> Tento článek popisuje, jak správce může povolit a vytvářet kolekce. Informace pro koncového uživatele o tom, jak používat portál a kolekce Moje aplikace, najdete v [tématu Přístup a kolekce](https://docs.microsoft.com/azure/active-directory/user-help/my-applications-portal-workspaces).

## <a name="enable-the-latest-my-apps-features"></a>Povolení nejnovějších funkcí moje aplikace

1. Otevřete [**portál Azure**](https://portal.azure.com/) a přihlaste se jako správce uživatelů nebo globální správce.

2. Přejděte na**nastavení uživatele** **služby Azure Active Directory** > .

3. V části **Náhledy uživatelských funkcí**vyberte **Spravovat nastavení náhledu uživatelských funkcí**.

4. V části **Uživatelé mohou používat funkce náhledu pro moje aplikace**zvolte jednu z následujících možností:
   * **Vybráno** - Povolí funkce pro určitou skupinu. Pomocí možnosti **Vybrat skupinu** vyberte skupinu, pro kterou chcete funkce povolit.  
   * **Vše** - Umožňuje funkce pro všechny uživatele.

> [!NOTE]
> Chcete-li otevřít portál Moje aplikace, `https://myapps.microsoft.com` mohou uživatelé použít odkaz nebo `https://myapps.microsoft.com/contoso.com`přizpůsobený odkaz pro vaši organizaci, například . Po povolení nového prostředí Moje aplikace se v horní části stránky Moje aplikace zobrazí aktualizovaný nápis **Moje aplikace** a uživatelé mohou vybrat **Vyzkoušet,** chcete-li zobrazit nové prostředí. Chcete-li přestat používat nové prostředí, mohou uživatelé vybrat **ano** z vytvořit **nový zážitek** banner v horní části stránky.

## <a name="create-a-collection"></a>Vytvoření kolekce

Chcete-li vytvořit kolekci, musíte mít licenci Azure AD Premium P1 nebo P2.

1. Otevřete [**portál Azure a**](https://portal.azure.com/) přihlaste se jako správce s licencí Azure AD Premium P1 nebo P2.

2. Přejděte na **Azure Active Directory** > **Enterprise Applications**.

3. V části **Manage**vyberte **Kolekce**.

4. Vyberte **novou kolekci**. Na stránce **Nová kolekce** zadejte **název** kolekce (doporučujeme nepoužívat "kolekce" v názvu. Potom zadejte **popis**.

   ![Nová stránka kolekce](media/acces-panel-collections/new-collection.png)

5. Vyberte kartu **Aplikace.** Vyberte **+ Přidat aplikaci**a potom na stránce **Přidat aplikace** vyberte všechny aplikace, které chcete přidat do kolekce, nebo použijte pole **Hledat** k vyhledání aplikací.

   ![Přidání aplikace do kolekce](media/acces-panel-collections/add-applications.png)

6. Po přidání aplikací vyberte **Přidat**. Zobrazí se seznam vybraných aplikací. Pomocí šipek nahoru můžete změnit pořadí aplikací v seznamu. Chcete-li aplikaci přesunout dolů nebo ji odstranit z kolekce, vyberte nabídku **Další** (**...**).

7. Vyberte kartu **Vlastníci.** Vyberte **+ Přidat uživatele a skupiny**a potom na stránce Přidat uživatele a **skupiny** vyberte uživatele nebo skupiny, kterým chcete přiřadit vlastnictví. Po dokončení výběru uživatelů a skupin zvolte **Vybrat**.

9. Vyberte kartu **+ Add users and groups** **Uživatelé a skupiny.** **Add users and groups** Nebo použijte **vyhledávací** pole k vyhledání uživatelů nebo skupin. Po dokončení výběru uživatelů a skupin zvolte **Vybrat**.

   ![Přidání uživatelů a skupin](media/acces-panel-collections/add-users-and-groups.png)

11. Vyberte **zkontrolovat + vytvořit**. Zobrazí se vlastnosti nové kolekce.


## <a name="view-audit-logs"></a>Zobrazení protokolů auditu

Protokoly auditu zaznamenávají operace kolekcí Moje aplikace, včetně akcí vytváření kolekce koncových uživatelů. Z aplikací Moje aplikace jsou generovány následující události:

* Vytvořit kolekci
* Upravit kolekci
* Odstranění kolekce
* Spuštění aplikace (koncový uživatel)
* Přidání samoobslužné aplikace (koncový uživatel)
* Odstranění samoobslužné aplikace (koncový uživatel)

K protokolům auditování na [webu Azure Portal](https://portal.azure.com) můžete přistupovat tak, že v části Aktivita vyberete**protokoly auditu** **podnikových aplikací** >  **Azure Active Directory.** >  V **možnosti Služba**vyberte **možnost Moje aplikace**.

## <a name="get-support-for-my-account-pages"></a>Získání podpory pro stránky Můj účet

Na stránce Moje aplikace může uživatel vybrat **možnost Zobrazit** > **můj účet** a otevřít si nastavení účtu. Na stránce **Můj účet** Azure AD můžou uživatelé spravovat svoje bezpečnostní údaje, zařízení, hesla a další. Mají také přístup k nastavení svého účtu Office.

V případě, že potřebujete odeslat žádost o podporu pro problém se stránkou účtu Azure AD nebo na stránce účtu Office, postupujte takto, aby byl váš požadavek správně směrován: 

* V případě problémů se stránkou **"Můj účet" azure a dlaním** otevřete žádost o podporu z portálu Azure. Přejděte na **Azure Portal** > **Azure Active Directory** > **Nová žádost o podporu**.

* V případě problémů se stránkou **Můj účet** s Office otevřete žádost o podporu z Centra pro správu Microsoftu 365. Přejděte na web **Microsoft 365** > **Centrum pro správu Podpory**. 

## <a name="next-steps"></a>Další kroky
[Prostředí koncových uživatelů pro aplikace ve službě Azure Active Directory](end-user-experiences.md)