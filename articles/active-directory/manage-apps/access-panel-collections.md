---
title: Vytváření kolekcí pro portály moje aplikace v Azure Active Directory | Microsoft Docs
description: Pomocí kolekcí moje aplikace můžete přizpůsobit stránky Moje aplikace, aby vaši koncoví uživatelé zjednodušili své prostředí aplikace. Uspořádejte aplikace do skupin pomocí samostatných karet.
services: active-directory
documentationcenter: ''
author: kenwith
manager: daveba
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-mgmt
ms.workload: identity
ms.topic: how-to
ms.date: 02/10/2020
ms.author: kenwith
ms.reviewer: kasimpso
ms.collection: M365-identity-device-management
ms.openlocfilehash: 7743a1af54b01f848c4ac6f0cb1d4526d66f8132
ms.sourcegitcommit: d49bd223e44ade094264b4c58f7192a57729bada
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/02/2021
ms.locfileid: "99254978"
---
# <a name="create-collections-on-the-my-apps-portal"></a>Vytváření kolekcí na portálu Moje aplikace

Uživatelé můžou pomocí portálu moje aplikace zobrazit a spustit cloudové aplikace, ke kterým mají přístup. Ve výchozím nastavení jsou všechny aplikace, ke kterým má uživatel přístup, uvedené na jedné stránce. Pro lepší uspořádání této stránky pro uživatele, pokud máte licenci Azure AD Premium P1 nebo P2, můžete nastavit kolekce. Pomocí kolekce můžete seskupovat aplikace, které souvisejí (například podle role úlohy, úkolu nebo projektu) a zobrazit je na samostatné kartě. Kolekce v podstatě používá filtr pro aplikace, které uživatel už může mít přístup, takže uživatel uvidí jenom aplikace v kolekci, které jim byly přiřazeny.

> [!NOTE]
> Tento článek popisuje, jak může správce povolit a vytvořit kolekce. Informace o tom, jak používat portál a kolekce moje aplikace, najdete v tématu věnovaném [přístupu a používání kolekcí](../user-help/my-applications-portal-workspaces.md).

## <a name="enable-the-latest-my-apps-features"></a>Povolit funkce nejnovější moje aplikace

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako správce uživatele nebo globální správce.

2. Přejít na **Azure Active Directory**  >  **nastavení uživatele**.

3. V části **náhledy uživatelských funkcí** vyberte **Spravovat nastavení uživatelské funkce Náhled**.

4. V části **Uživatelé můžou používat funkce verze Preview pro moje aplikace**, vyberte jednu z následujících možností:
   * **Vybráno** – povolí funkce pro určitou skupinu. Pomocí možnosti **Vybrat skupinu** vyberte skupinu, pro kterou chcete povolit funkce.  
   * **All** – povolí funkce pro všechny uživatele.

> [!NOTE]
> K otevření portálu moje aplikace můžou uživatelé použít odkaz `https://myapps.microsoft.com` nebo vlastní odkaz pro vaši organizaci, jako je například `https://myapps.microsoft.com/contoso.com` . Po povolení prostředí nové moje aplikace se v horní části stránky Moje aplikace zobrazí nápis **aktualizované moje prostředí moje aplikace** a uživatelé si **můžou vybrat, že si budou** moct zobrazit nové prostředí. Pokud chcete přestat používat nové prostředí, uživatelé můžou v horní části stránky vybrat **Ano** v nápisu **opustit nové prostředí** .

## <a name="create-a-collection"></a>Vytvoření kolekce

Chcete-li vytvořit kolekci, musíte mít licenci Azure AD Premium P1 nebo P2.

1. Otevřete [**Azure Portal**](https://portal.azure.com/) a přihlaste se jako správce s licencí Azure AD Premium P1 nebo P2.

2. Přejít na **Azure Active Directory**  >  **podnikové aplikace**

3. V části **Spravovat** vyberte **kolekce**.

4. Vyberte **Nová kolekce**. Na stránce **Nová kolekce** zadejte **název** kolekce (v názvu nedoporučujeme používat "kolekci". Pak zadejte **Popis**.

   ![Stránka nové kolekce](media/acces-panel-collections/new-collection.png)

5. Vyberte kartu **aplikace** . Vyberte **+ Přidat aplikaci** a pak na stránce **Přidat aplikace** vyberte všechny aplikace, které chcete přidat do kolekce, nebo pomocí **vyhledávacího** pole vyhledejte aplikace.

   ![Přidání aplikace do kolekce](media/acces-panel-collections/add-applications.png)

6. Až skončíte s přidáváním aplikací, vyberte **Přidat**. Zobrazí se seznam vybraných aplikací. Pořadí aplikací v seznamu můžete změnit pomocí šipek nahoru. Pokud chcete aplikaci přesunout dolů nebo ji odstranit z kolekce, vyberte **Další** nabídku (**...**).

7. Vyberte kartu **vlastníci** . Vyberte **+ Přidat uživatele a skupiny** a pak na stránce **Přidat uživatele a skupiny** vyberte uživatele nebo skupiny, kterým chcete přiřadit vlastnictví. Až budete hotovi s výběrem možnosti uživatelé a skupiny, zvolte **Vybrat**.

9. Vyberte kartu **Uživatelé a skupiny** . Vyberte **+ Přidat uživatele a skupiny** a pak na stránce **Přidat uživatele a skupiny** vyberte uživatele nebo skupiny, kterým chcete kolekci přiřadit. Případně můžete vyhledat uživatele nebo skupiny pomocí **vyhledávacího** pole. Až budete hotovi s výběrem možnosti uživatelé a skupiny, zvolte **Vybrat**.

   ![Přidat uživatele a skupiny](media/acces-panel-collections/add-users-and-groups.png)

11. Vyberte **Zkontrolovat a vytvořit**. Zobrazí se vlastnosti nové kolekce.


## <a name="view-audit-logs"></a>Zobrazení protokolů auditu

Protokoly auditu zaznamenávají operace kolekcí moje aplikace, včetně akcí vytváření kolekcí koncových uživatelů. Z mých aplikací se generují tyto události:

* Vytvořit kolekci
* Upravit kolekci
* Odstranění kolekce
* Spuštění aplikace (koncový uživatel)
* Samoobslužné přidávání aplikací (koncový uživatel)
* Samoobslužné odstraňování aplikací (koncový uživatel)

Protokoly auditu můžete získat v [Azure Portal](https://portal.azure.com) tak, že   >  v části aktivita vyberete Azure Active Directory protokoly auditu **podnikových aplikací**  >   . V případě **služby** vyberte **Moje aplikace**.

## <a name="get-support-for-my-account-pages"></a>Získat podporu pro stránky Můj účet

Na stránce Moje aplikace může **uživatel vybrat můj účet**  >  **Zobrazit můj** účet a otevřít jeho nastavení účtu. Na stránce **můj účet** Azure AD můžou uživatelé spravovat svoje bezpečnostní údaje, zařízení, hesla a další. Můžou také přistupovat ke svým nastavením účtu Office.

V případě, že potřebujete odeslat žádost o podporu k problému na stránce účtu Azure AD nebo na stránce účtu Office, postupujte podle těchto kroků, aby se vaše žádost správně směrovala: 

* Pokud máte problémy se stránkou **můj účet služby Azure AD** , otevřete žádost o podporu v rámci Azure Portal. Přejít na **Azure Portal**  >  **Azure Active Directory**  >  **novou žádost o podporu**.

* Pokud máte problémy se stránkou **Office můj účet** , otevřete žádost o podporu v centru pro správu Microsoft 365. Přejít na **Microsoft 365 podpora centra pro správu**  >   

## <a name="next-steps"></a>Další kroky
[Prostředí koncových uživatelů pro aplikace v Azure Active Directory](end-user-experiences.md)