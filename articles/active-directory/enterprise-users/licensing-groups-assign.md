---
title: Přiřazení licencí ke skupině – Azure Active Directory | Microsoft Docs
description: Přiřazení licencí uživatelům pomocí Azure Active Directory licencování skupin
services: active-directory
keywords: Licencování Azure AD
documentationcenter: ''
author: curtand
manager: daveba
ms.service: active-directory
ms.subservice: enterprise-users
ms.topic: how-to
ms.workload: identity
ms.date: 12/02/2020
ms.author: curtand
ms.reviewer: sumitp
ms.custom: it-pro
ms.collection: M365-identity-device-management
ms.openlocfilehash: 1c1a6957d347681f1c7dd9979429fffb153ccb39
ms.sourcegitcommit: 65db02799b1f685e7eaa7e0ecf38f03866c33ad1
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/03/2020
ms.locfileid: "96546603"
---
# <a name="assign-licenses-to-users-by-group-membership-in-azure-active-directory"></a>Přiřazení licencí uživatelům podle členství ve skupině v Azure Active Directory

Tento článek vás provede přiřazením licencí k produktu skupině uživatelů a ověřením, že jsou správně licencované v Azure Active Directory (Azure AD).

V tomto příkladu organizace Azure AD obsahuje skupinu zabezpečení nazvanou **personální oddělení**. Tato skupina zahrnuje všechny členy oddělení lidských zdrojů (přibližně 1 000 uživatelů). Chcete přiřadit licence Office 365 Enterprise E3 celému oddělení. Podniková služba Yammer, která je součástí produktu, musí být dočasně zakázána, dokud bude oddělení připraveno ho začít používat. Chcete také nasadit licence Enterprise Mobility + Security do stejné skupiny uživatelů.

> [!NOTE]
> Některé služby Microsoft nejsou dostupné ve všech umístěních. Předtím, než může být licence přiřazena uživateli, musí správce zadat vlastnost umístění používání u uživatele.
>
> Pro přiřazení licence skupiny budou mít všichni uživatelé bez zadaného umístění v adresáři k dispozici umístění adresáře. Pokud máte uživatele ve více umístěních, doporučujeme, abyste vždycky nastavili umístění pro využití jako součást toku vytváření uživatele v Azure AD (např. prostřednictvím konfigurace služby AAD Connect), která zajišťuje, že výsledek přiřazení licence je vždycky správný a uživatelé neobdrží služby v umístěních, která nejsou povolená.

## <a name="step-1-assign-the-required-licenses"></a>Krok 1: přiřazení požadovaných licencí

1. Přihlaste se k [**centru pro správu Azure AD**](https://aad.portal.azure.com) pomocí účtu správce licencí. Aby bylo možné spravovat licence, musí být účet správcem licence, správcem uživatele nebo globálním správcem.

1. Výběrem možnosti **licence** otevřete stránku, kde můžete zobrazit a spravovat všechny produkty pro vystavování licencí v organizaci.

1. V části **všechny produkty** vyberte možnost Office 365 Enterprise E5 a Enterprise mobility + Security E3 výběrem názvů produktů. Pokud chcete spustit přiřazení, vyberte v horní části stránky **přiřadit** .

   ![Vybrat produkty k přiřazení licencí](./media/licensing-groups-assign/licenses-all-products-assign.png)
  
1. Na stránce **přiřadit licenci** vyberte **Uživatelé a skupiny** a otevřete tak seznam uživatelů a skupin.

1. Vyberte uživatele nebo skupinu a pak použijte tlačítko **Vybrat** v dolní části stránky k potvrzení výběru.

1. Na stránce **přiřadit licenci** klikněte na **možnosti přiřazení**, které zobrazí všechny plány služby zahrnuté do dvou produktů, které jsme vybrali dřív. Najděte si **Yammer Enterprise** a vypněte **ji,** aby se tato služba z licence na produkt zakázala. Kliknutím na **OK** v dolní části **možností licence** potvrďte.

   ![vybrat plány služeb pro licence](./media/licensing-groups-assign/assignment-options.png)
  
1. Přiřazení dokončíte tak, že na stránce **přiřadit licenci** kliknete na **přiřadit** v dolní části stránky.

1. V pravém horním rohu se zobrazí oznámení, ve kterém se zobrazuje stav a výsledek procesu. Pokud se přiřazení ke skupině nedalo dokončit (například kvůli již existujícím licencím ve skupině), kliknutím na oznámení zobrazíte podrobnosti o selhání.

Když přiřadíte licence ke skupině, Azure AD zpracuje všechny stávající členy této skupiny. Tento proces může nějakou dobu trvat, ale proměnlivě velikost skupiny. V dalším kroku se dozvíte, jak ověřit, že se proces dokončil, a zjistěte, jestli je pro vyřešení problémů potřeba další pozornost.

## <a name="step-2-verify-that-the-initial-assignment-has-finished"></a>Krok 2: ověření dokončení počátečního přiřazení

1. Přejít na **Azure Active Directory**  >  **skupiny**. Vyberte skupinu, ke které byly licence přiřazeny.

1. Na stránce skupina vyberte **licence**. To vám umožní rychle potvrdit, jestli jsou licence plně přiřazené k uživatelům, a pokud dojde k chybám, které potřebujete najít. K dispozici jsou následující informace:

   - Licence služby, které jsou aktuálně přiřazeny ke skupině. Vyberte položku pro zobrazení konkrétních služeb, které byly povoleny, a proveďte změny.

   - Aktualizace stavu nejnovějších licencí, které jsou k dispozici, pokud jsou změny zpracovávány nebo pokud bylo zpracování dokončeno pro všechny členy uživatele.

   - Informace o přiřazeních uživatelských licencí, která jsou v chybovém stavu.

   ![chyby licencování a stav licence](./media/licensing-groups-assign/assignment-errors.png)

1. Podrobnější informace o zpracování licencí najdete v části **Azure Active Directory**  >  **Uživatelé a skupiny skupiny**  >  *názvy*  >  **protokolů auditu**. Podívejte se na následující činnosti:

   - Aktivita: `Start applying group based license to users` . Tato část se zaznamená do protokolu, když systém vybere změnu přiřazení licence ve skupině a začne ji používat pro všechny členy. Obsahuje informace o změně, která byla provedena.

   - Aktivita: `Finish applying group based license to users` . Tato část se zaznamená do okamžiku, kdy systém dokončí zpracování všech uživatelů ve skupině. Obsahuje souhrn toho, kolik uživatelů bylo úspěšně zpracováno a kolik uživatelů nebylo možné přiřadit licence skupin.

   V [této části](licensing-group-advanced.md#use-audit-logs-to-monitor-group-based-licensing-activity) najdete další informace o tom, jak můžete pomocí protokolů auditu analyzovat změny provedené licencováním na základě skupin.

## <a name="step-3-check-for-license-problems-and-resolve-them"></a>Krok 3: vyhledání problémů s licencí a jejich řešení

1. Přejít na **Azure Active Directory**  >  **skupiny** a vyhledat skupinu, ke které byly licence přiřazené.
1. Na stránce skupina vyberte **licence**. Oznámení v horní části stránky ukazuje, že je k dispozici 10 uživatelů, kterým nebylo možné přiřazovat licence. Otevřete ho a zobrazí se seznam všech uživatelů v chybovém stavu licencování této skupiny.
1. Sloupec **nezdařené přiřazení** nám oznamuje, že se k uživatelům nepovedlo přiřadit licence k licenci. Sloupec **nejvyšší příčiny selhání** obsahuje příčinu selhání. V tomto případě se jedná o **konfliktní plány služeb**.

   ![licence, které se nepodařilo přiřadit](./media/licensing-groups-assign/failed-assignments.png)

1. Výběrem uživatele otevřete stránku **licence** uživatele. Tato stránka zobrazuje všechny licence, které jsou aktuálně přiřazeny uživateli. V tomto příkladu má uživatel licenci Office 365 Enterprise E1, která byla zděděná od skupiny **uživatelů veřejného terminálu** . To je v konfliktu s licencí E3, kterou se systém pokusil použít ze skupiny **oddělení lidských zdrojů** . V důsledku toho není uživateli přiřazena žádná licence z této skupiny.

   ![Zobrazit všechny konflikty licence pro uživatele](./media/licensing-groups-assign/user-licence-conflicting-service-plans.png)

1. Pokud chcete tento konflikt vyřešit, odeberte uživatele ze skupiny **Uživatelé veřejného terminálu** . Až služba Azure AD změny zpracuje, přiřadí se licence pro **personální oddělení** správně.

## <a name="next-steps"></a>Další kroky

Další informace o sadě funkcí pro přiřazení licencí pomocí skupin najdete v následujících článcích:

- [Co je licencování na základě skupin v Azure Active Directory?](../fundamentals/active-directory-licensing-whatis-azure-portal.md?context=azure%2factive-directory%2fusers-groups-roles%2fcontext%2fugr-context)
- [Identifikace a řešení potíží s licencemi pro skupinu v Azure Active Directory](licensing-groups-resolve-problems.md)
- [Postup migrace jednotlivě licencovaných uživatelů na licencování na základě skupin v Azure Active Directory](licensing-groups-migrate-users.md)
- [Postup migrace uživatelů mezi licencemi k produktu pomocí licencování na základě skupin v Azure Active Directory](licensing-groups-change-licenses.md)
- [Další scénáře licencování na základě skupin v Azure Active Directory](licensing-group-advanced.md)
- [Příklady prostředí PowerShell pro licencování na základě skupin v Azure Active Directory](licensing-ps-examples.md)