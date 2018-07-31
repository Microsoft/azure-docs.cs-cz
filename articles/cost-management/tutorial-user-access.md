---
title: Kurz – Přiřazení přístupu do služby Azure Cost Management | Microsoft Docs
description: V tomto kurzu zjistíte, jak přiřadit přístup k datům služby Cost Management pomocí uživatelských účtů, které určují úroveň přístupu k entitám.
services: cost-management
keywords: ''
author: bandersmsft
ms.author: banders
ms.date: 07/10/2018
ms.topic: tutorial
ms.service: cost-management
ms.custom: ''
manager: dougeby
ms.openlocfilehash: 4b45d917118d5be1252c3bb232e4b24e68d7857f
ms.sourcegitcommit: bf522c6af890984e8b7bd7d633208cb88f62a841
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/20/2018
ms.locfileid: "39188178"
---
# <a name="tutorial-assign-access-to-cost-management-data"></a>Kurz: Přiřazení přístupu k datům služby Cost Management

Přístup k datům služby Cost Management zajišťuje správa uživatelů nebo entit. Přístup k *entitám* a funkcím správy určují uživatelské účty Cloudyn. Existují dva typy přístupu: přístup správce a uživatelský přístup. Pokud není přístup správce pro daného uživatele upravený, umožňuje uživateli neomezený přístup ke všem funkcím portálu Cloudyn, včetně správy uživatelů, správy seznamů příjemců a přístupu ke kořenové entitě všech dat entit. Uživatelský přístup je určený koncovým uživatelům, aby mohli zobrazovat sestavy a vytvářet je pomocí přístupu, který mají k datům entit.

Entity se používají tak, aby odrážely hierarchickou strukturu vaší obchodní organizace. Identifikují jednotlivá oddělení, divize a týmy vaší organizace ve službě Cloudyn. Hierarchie entit vám pomáhá přesně sledovat výdaje podle entit.

Při registraci vaší smlouvy nebo účtu Azure se ve službě Cloudyn vytvořil účet s oprávněním správce, takže můžete provést všechny kroky v tomto kurzu. Tento kurz se týká přístupu k datům služby Cost Management včetně správy uživatelů a správy entit. Získáte informace o těchto tématech:

> [!div class="checklist"]
> * Vytvoření uživatele s přístupem správce
> * Vytvoření uživatele s uživatelským přístupem
> * Odstranění uživatele
> * Odstranění nebo export osobních údajů
> * Vytvoření a správa entit


Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) před tím, než začnete.

## <a name="prerequisites"></a>Požadavky

- Musíte mít účet Azure.
- Musíte mít zaregistrovanou zkušební verzi nebo placené předplatné služby Azure Cost Management.

## <a name="create-a-user-with-admin-access"></a>Vytvoření uživatele s přístupem správce

I když vy už přístup správce máte, můžou ho potřebovat i další spolupracovníci z vaší organizace. Na portálu Cloudyn klikněte na symbol ozubeného kolečka v pravém horním rohu a vyberte možnost **User Management** (Správa uživatelů). Kliknutím na **Add New User** (Přidat nového uživatele) přidejte nového uživatele.

Zadejte požadované informace o uživateli. **Přihlašovací ID** musí být platná e-mailová adresa. Vyberte možnost Allow User Management (Povolit správu uživatelů), aby mohl uživatel vytvářet a upravovat jiné uživatele. Povolením možnosti Allow Recipient Lists Management (Povolit správu seznamů příjemců) umožníte uživateli upravovat seznamy příjemců. Pokud vyberete možnost **Notify user by email** (Oznámit uživateli e-mailem), služba Cloudyn uživateli e-mailem zašle odkaz s přihlašovacími údaji. Při prvním přihlášeni si uživatel nastaví heslo.

V části **User has admin access** (Uživatel má přístup správce) je vybraná kořenová entita vaší organizace. Nechte vybranou kořenovou entitu a uložte informace o uživateli. Výběrem kořenové entity dáte uživateli oprávnění správce nejen ke kořenové entitě v rámci stromu, ale také ke všem entitám zařazeným pod ní.  
  ![Přidání nového uživatele s přístupem správce](.\media\tutorial-user-access\new-admin-access.png)

## <a name="create-a-user-with-user-access"></a>Vytvoření uživatele s uživatelským přístupem
Běžní uživatelé, které potřebují přístup k datům služby Cost Management, jako jsou řídicí panely a sestavy, by měli mít k jejich zobrazení uživatelský přístup. Nového uživatele s uživatelským přístupem vytvoříte podobně jako předtím uživatele s přístupem správce, jenom s těmito rozdíly:

- Zrušte zaškrtnutí políček **Allow User Management** (Povolit správu uživatelů), **Allow Recipient Lists Management** (Povolit správu seznamů příjemců) a všech políček v seznamu **User has admin access** (Uživatel má přístup správce).
- V seznamu **User has user access** (Uživatel má uživatelský přístup) vyberte entity, ke kterým uživatel potřebuje mít přístup.
- Podle potřeby můžete povolit přístup k určitým entitám také správci.

![Přidání nového uživatele s uživatelským přístupem](.\media\tutorial-user-access\new-user-access.png)

Pokud chcete zhlédnout výukové video o přidávání uživatelů, podívejte se na video [Adding Users to Azure Cost Management](https://youtu.be/Nzn7GLahx30) (Přidávání uživatelů do služby Azure Cost Management).

## <a name="delete-a-user"></a>Odstranění uživatele

Když odstraníte uživatele, všechny entity, ke kterým má uživatel přístup, zůstanou beze změny. Při odstranění uživatele dojde k odebrání uložených *osobních* sestav. Uložené *veřejné* sestavy vytvořené uživatelem se neodstraní.

Jako uživatel nemůžete odebrat sami sebe.

> [!WARNING]
> Odstraněný uživatel se nedá obnovit.

1.  Na portálu Cloudyn klikněte na symbol ozubeného kolečka v pravém horním rohu a vyberte možnost **User Management** (Správa uživatelů).
2.  V seznamu uživatelů vyberte uživatele, kterého chcete odstranit, a klikněte na tlačítko pro **odstranění uživatele** (symbol odpadkového koše).
3.  V rámečku Delete User (Odstranit uživatele) klikněte na **Yes** (Ano) a potom na **OK**.


## <a name="delete-or-export-personal-data"></a>Odstranění nebo export osobních údajů

Pokud chcete odstranit osobní údaje nebo je exportovat z portálu Cloudyn, je potřeba vytvořit lístek podpory. Vytvořený lístek podpory funguje jako formální žádost – žádost subjektu dat. Microsoft potom účet urychleně odebere a odstraní všechny údaje zákazníka nebo osobní údaje. Další informace o tom, jak požádat o odstranění nebo export dat, najdete v článku [Žádosti subjektu dat týkající se dat na portálu Cloudyn](https://www.cloudyn.com/cloudyn-gdpr-requests).

## <a name="create-and-manage-entities"></a>Vytvoření a správa entit

Při určování nákladové hierarchie entit se osvědčilo identifikovat strukturu vaší organizace. Entity vám umožní segmentovat výdaje podle jednotlivých účtů nebo předplatných. Můžete vytvořit entity nákladů a vytvářet logické skupiny pro správu a sledování výdajů. Při vytváření stromu se zamyslete nad tím, jak chcete nebo potřebujete rozdělit náklady podle obchodních jednotek, nákladových středisek, prostředí a obchodních oddělení. Strom entit ve službě Cloudyn je díky dědičnosti entit flexibilní.

Jednotlivé odběry pro vaše cloudové účty jsou propojené s konkrétními entitami. Entitu můžete přidružit k účtu nebo předplatnému poskytovatele cloudové služby. Entity jsou tedy víceklientské. Konkrétním uživatelům můžete pomocí entit přiřadit přístup jenom ke konkrétnímu segmentu vaší společnosti. Tím zajistíte izolaci dat, dokonce i ve velkých částech společnosti, jako jsou pobočky. Izolace dat pomáhá v řízení.  

Při registraci smlouvy Azure nebo účtu ve službě Cloudyn proběhlo zkopírování vašich dat prostředků Azure včetně využití, výkonu, fakturace a dat značek z vašich odběrů do účtu služby Cloudyn. Strom entit je ale potřeba vytvořit ručně. Pokud jste přeskočili registraci Azure Resource Manageru, jsou na portálu Cloudyn dostupná jenom fakturační data a pár sestav prostředků.

Na portálu Cloudyn klikněte na symbol ozubeného kolečka v pravém horním rohu a vyberte **Cloud Accounts** (Účty v cloudu). Začnete s jednou entitou (kořenovou entitou) a pod ní začnete vytvářet strom entit. Tady je příklad hierarchie entit, která se můžete po dokončení stromu podobat řadě organizací v oblasti IT:

![Strom entit](.\media\tutorial-user-access\entity-tree.png)

Vedle položky **Entities** (Entity) klikněte na **Add Entity** (Přidat entitu). Zadejte informace o osobě nebo oddělení, které chcete přidat. Pole **Full Name** (Celé jméno) a **Email** (E-mail) nemusejí odpovídat existujícím uživatelům. Pokud chcete zobrazit seznam úrovní přístupu, vyhledejte v nápovědě téma *Adding an entity* (Přidání entity).

![Přidání entity](.\media\tutorial-user-access\add-entity.png)

Po dokončení uložte entitu kliknutím na **Save** (Uložit).

### <a name="entity-access-levels"></a>Úrovně přístupu entit

Úrovně přístupu entit ve spojení s přístupem uživatele umožňují definovat, jaký typ akcí je k dispozici na portálu Cloudyn.

- **Enterprise** (Firemní) – Poskytuje možnost vytvářet a spravovat entity nákladů podřízených položek.
- **Enterprise + Cost Allocation** (Firemní + přidělení nákladů) – Poskytuje možnost vytvářet a spravovat entity nákladů podřízených položek včetně přidělování nákladů pro konsolidované účty.
- **Enterprise, Cost based on parent cost allocation** (Firemní, podle nákladů při přiřazení nákladů nadřazené položky) – Poskytuje možnost vytvářet a spravovat entity nákladů podřízených položek. Náklady na účet jsou založené na modelu přiřazování nákladů nadřazeného objektu.
- **Custom Dashboards Only** (Jenom vlastní řídicí panely) – Umožňuje uživateli zobrazit jenom předdefinované vlastní řídicí panely.
- **Dashboards Only** (Jenom řídicí panely) – Umožňuje uživateli zobrazit jenom řídicí panely.

### <a name="create-a-cost-entity-hierarchy"></a>Vytvoření hierarchie entit nákladů

Pokud chcete vytvořit hierarchii entit nákladů, musí mít účet s přístupem Enterprise nebo Enterprise + Cost allocation.

Na portálu Cloudyn klikněte na symbol ozubeného kolečka v pravém horním rohu a vyberte **Cloud Accounts** (Účty v cloudu). V levém podokně se zobrazí strom **Entities** (Entity). V případě potřeby rozbalte stromovou strukturu entit, abyste se mohli podívat na entitu, kterou chcete přidružit k účtu.  Účty poskytovatele cloudové služby jsou zobrazené na kartách v pravém podokně. Vyberte kartu a pak myší přetáhněte účet nebo předplatné na entitu. Pole **Move** (Přesunout) vás informuje, že účet se úspěšně přesunul. Klikněte na **OK**.

Entitě můžete také přidružit více účtů. Vyberte účty a pak klikněte na **Move**. V dialogovém okně Move Accounts (Přesunout účty) vyberte entitu, kam chcete přesunout účet, a potom klikněte na **Save** (Uložit). Pole Move Accounts vás požádá o ověření, že chcete účty přesunout. Klikněte na **Yes** (Ano) a potom na **OK**.

Pokud chcete zhlédnout výukové video o vytváření nákladové hierarchie entit, podívejte se na video [Creating a Cost Entity Hierarchy in Azure Cost Management](https://youtu.be/dAd9G7u0FmU) (Vytváření nákladové hierarchie entit ve službě Azure Cost Management).

Pokud jste uživatel se smlouvou Azure Enterprise, podívejte se na výukové video o přiřazování účtů a předplatných k entitám [Connecting to Azure Resource Manager with Azure Cost Management](https://youtu.be/oCIwvfBB6kk) (Připojení k Azure Resource Manageru ve službě Azure Cost Management).

## <a name="next-steps"></a>Další kroky

V tomto kurzu jste se naučili:

> [!div class="checklist"]
> * Vytvoření uživatele s přístupem správce
> * Vytvoření uživatele s uživatelským přístupem
> * Odstranění uživatele
> * Odstranění nebo export osobních údajů
> * Vytvoření a správa entit


Pokud jste pro své účty ještě nepovolili přístup k rozhraní API Azure Resource Manageru, pokračujte k následujícímu článku.

> [!div class="nextstepaction"]
> [Aktivace účtů a předplatných Azure](activate-subs-accounts.md)
