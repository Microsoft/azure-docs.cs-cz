---
title: Operace služby Azure Active Directory Connect
description: Tento článek popisuje další operace, které lze provést po nasazení Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: ef908429d359020282920d73480a472bfde0aa60
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "79261512"
---
# <a name="azure-active-directory-connect-health-operations"></a>Operace služby Azure Active Directory Connect
Toto téma popisuje různé operace, které můžete provádět pomocí služby Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Povolení e-mailových oznámení
Službu Azure AD Connect Health můžete nakonfigurovat tak, aby odesílala e-mailová oznámení, když výstrahy označují, že infrastruktura identit není v pořádku. K tomu dochází při generování výstrahy a při její vyřešení.

![Snímek obrazovky s nastavením e-mailových oznámení služby Azure AD Connect Health](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> E-mailová oznámení jsou ve výchozím nastavení povolena.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Povolení e-mailových oznámení azure aslužby Connect Health
1. Otevřete okno **Výstrahy** pro službu, pro kterou chcete dostávat e-mailové oznámení.
2. Na panelu akcí klikněte na **Nastavení oznámení**.
3. Na přepínači e-mailových oznámení vyberte **ZAPNUTO**.
4. Toto políčko zaškrtněte, pokud chcete, aby všichni globální správci dostávali e-mailová oznámení.
5. Pokud chcete dostávat e-mailová oznámení na jiné e-mailové adresy, zadejte je do pole **Další příjemci e-mailu.** Chcete-li z tohoto seznamu odebrat e-mailovou adresu, klepněte pravým tlačítkem myši na položku a vyberte příkaz **Odstranit**.
6. Chcete-li změny dokončit, klepněte na tlačítko **Uložit**. Změny se projeví až po uložení.

>[!NOTE] 
> Pokud se v naší back-endové službě objevují problémy se zpracováním požadavků na synchronizaci, odešle tato služba e-mail s podrobnostmi o chybě na e-mailovou adresu (adresy) kontaktních adres pro správu vašeho tenanta. Slyšeli jsme zpětnou vazbu od zákazníků, že v některých případech je objem těchto zpráv nepřiměřeně velký, takže měníme způsob, jakým tyto zprávy odesíláme. 
>
> Namísto odeslání zprávy pro každou chybu synchronizace pokaždé, když k ní dojde, odešleme denní přehled všech chyb, které vrátila back-endová služba. To umožňuje zákazníkům zpracovávat tyto chyby efektivnějším způsobem a snižuje počet duplicitních chybových zpráv.
>
> Tuto změnu plánujeme provést 15. ledna 2020.

## <a name="delete-a-server-or-service-instance"></a>Odstranění instance serveru nebo služby

>[!NOTE] 
> Pro kroky odstranění je vyžadována licence Azure AD premium.

V některých případech můžete chtít odebrat server ze sledování. Tady je to, co potřebujete vědět, abyste odebrali server ze služby Azure AD Connect Health.

Při mazání serveru si uvědomte následující skutečnosti:

* Tato akce zastaví shromažďování dalších dat z tohoto serveru. Tento server je odebrán ze služby monitorování. Po této akci nelze zobrazit nová upozornění, monitorování nebo analytická data využití pro tento server.
* Tato akce neodinstaluje agenta stavu ze serveru. Pokud jste agenta stavu před provedením tohoto kroku neodinstalovali, mohou se na serveru zobrazit chyby související s agentem stavu.
* Tato akce neodstraní data, která již byla shromážděna z tohoto serveru. Tato data se odstraní v souladu se zásadami uchovávání dat Azure.
* Chcete-li po provedení této akce znovu spustit sledování stejného serveru, je nutné odinstalovat a znovu nainstalovat agenta stavu na tomto serveru.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Odstranění serveru ze služby Azure AD Connect Health

>[!NOTE] 
> Pro kroky odstranění je vyžadována licence Azure AD premium.

Azure AD Connect Health for A DS Federation Services (AD FS) a Azure AD Connect (Sync):

1. Otevřete okno **Server** z okna **Seznam serverů** výběrem názvu serveru, který má být odebrán.
2. V okně **Server** klepněte na panelu akcí na **odstranit**.
![Snímek obrazovky serveru odstranění stavu služby Azure AD Connect](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Potvrďte zadáním názvu serveru do potvrzovacího pole.
4. Klepněte na **tlačítko Odstranit**.

Azure AD Connect Health pro Azure Active Directory Domain Services:

1. Otevřete řídicí panel **Řadiče domény.**
2. Vyberte řadič domény, který chcete odebrat.
3. Na panelu akcí klikněte na **Odstranit vybrané**.
4. Potvrďte akci pro odstranění serveru.
5. Klepněte na **tlačítko Odstranit**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Odstranění instance služby ze služby Azure AD Connect Health
V některých případech můžete chtít odebrat instanci služby. Tady je to, co potřebujete vědět, abyste odebrali instanci služby ze služby Azure AD Connect Health.

Při odstranění instance služby mějte na paměti následující skutečnosti:

* Tato akce odebere aktuální instanci služby ze služby monitorování.
* Tato akce neodinstaluje ani neodebere agenta stavu z žádného ze serverů, které byly sledovány jako součást této instance služby. Pokud jste agenta stavu před provedením tohoto kroku neodinstalovali, mohou se na serverech zobrazit chyby související s agentem stavu.
* Všechna data z této instance služby se odstraní v souladu se zásadami uchovávání dat Azure.
* Chcete-li po provedení této akce spustit monitorování služby, odinstalujte a znovu nainstalujte agenta stavu na všech serverech. Chcete-li po provedení této akce znovu spustit sledování stejného serveru, odinstalujte, přeinstalujte a zaregistrujte agenta stavu na tomto serveru.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Odstranění instance služby ze služby Azure AD Connect Health
1. Otevřete okno **Service** z okna **Seznam služeb** výběrem identifikátoru služby (název farmy), který chcete odebrat. 
2. V okně **Služba** klepněte na panelu akcí na **odstranit**. 
![Snímek obrazovky se službou odstranění stavu služby Azure AD Connect](./media/how-to-connect-health-operations/DeleteServer.png)
3. Potvrďte zadáním názvu služby do potvrzovacího pole (například: sts.contoso.com).
4. Klepněte na **tlačítko Odstranit**.
   <br><br>

[//]: # (Začátek sekce RBAC)
## <a name="manage-access-with-role-based-access-control"></a>Správa přístupu prostřednictvím řízení přístupu na základě role
[Řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/role-assignments-portal.md) pro Azure AD Connect Health poskytuje přístup k uživatelům a skupinám jiným než globálním správcům. RBAC přiřazuje role zamýšleným uživatelům a skupinám a poskytuje mechanismus pro omezení globálních správců v adresáři.

### <a name="roles"></a>Role
Azure AD Connect Health podporuje následující předdefinované role:

| Role | Oprávnění |
| --- | --- |
| Vlastník |Vlastníci *můžou spravovat přístup* (například přiřadit roli uživateli nebo skupině), zobrazit všechny *informace* (například zobrazit výstrahy) z portálu a *změnit nastavení* (například e-mailová oznámení) v rámci služby Azure AD Connect Health. <br>Ve výchozím nastavení jsou globální správci Služby Azure AD přiřazena tato role a to nelze změnit. |
| Přispěvatel |Přispěvatelé můžou *zobrazit všechny informace* (například zobrazit výstrahy) z portálu a změnit *nastavení* (například e-mailová oznámení) v rámci azure ad connect health. |
| Čtenář |Čtenáři můžete *zobrazit všechny informace* (například zobrazení výstrah) z portálu v rámci Azure AD Connect Health. |

Všechny ostatní role (například správci přístupu uživatelů nebo uživatelé DevTest Labs) nemají žádný vliv na přístup v rámci azure ad connect health, i když role jsou k dispozici v prostředí portálu.

### <a name="access-scope"></a>Obor přístupu
Azure AD Connect Health podporuje správu přístupu na dvou úrovních:

* **Všechny instance služby**: Ve většině případů se jedná o doporučenou cestu. Řídí přístup pro všechny instance služby (například farmu služby AD FS) napříč všemi typy rolí, které jsou monitorovány službou Azure AD Connect Health.
* **Instance služby**: V některých případech může být nutné oddělit přístup na základě typů rolí nebo instance služby. V takovém případě můžete spravovat přístup na úrovni instance služby.  

Oprávnění je uděleno, pokud má koncový uživatel přístup na úrovni instance adresáře nebo služby.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Povolení přístupu uživatelů nebo skupin ke stavu služby Azure AD Connect
Následující kroky ukazují, jak povolit přístup.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Krok 1: Vyberte příslušný obor přístupu
Chcete-li povolit přístup uživatele na *úrovni všech instancí služby* v rámci služby Azure AD Connect Health, otevřete hlavní okno ve službě Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Krok 2: Přidání uživatelů a skupin a přiřazení rolí
1. V části **Konfigurovat** klikněte na **položku Uživatelé**.<br>
   ![Snímek obrazovky s postranním panelem prostředků Azure AD Connect Health](./media/how-to-connect-health-operations/startRBAC.png)
2. Vyberte **Přidat**.
3. V **podokně Vybrat roli** vyberte roli (například **Vlastník).**<br>
   ![Snímek obrazovky s oknem Uživatelé služby Azure AD Connect Health RBAC](./media/how-to-connect-health-operations/RBAC_add.png)
4. Zadejte název nebo identifikátor cílového uživatele nebo skupiny. Můžete vybrat jednoho nebo více uživatelů nebo skupin současně. Klepněte na **tlačítko Vybrat**.
   ![Snímek obrazovky s oknem Uživatelé služby Azure AD Connect Health RBAC](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Vyberte **OK**.<br>
6. Po dokončení přiřazení role se uživatelé a skupiny zobrazí v seznamu.<br>
   ![Snímek obrazovky s oknem Uživatelé služby Azure AD Connect Health RBAC se zvýrazněnými novými uživateli](./media/how-to-connect-health-operations/RBAC_user_list.png)

Nyní mají uvedení uživatelé a skupiny přístup podle přiřazených rolí.

> [!NOTE]
> * Globální správci mají vždy úplný přístup ke všem operacím, ale účty globálních správců nejsou v předchozím seznamu k dispozici.
> * Funkce Pozvat uživatele není v rámci služby Azure AD Connect Health podporována.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Krok 3: Sdílení umístění okna s uživateli nebo skupinami
1. Po přiřazení oprávnění může uživatel přistupovat ke službě Azure AD Connect Health tak, že přejde [sem](https://aka.ms/aadconnecthealth).
2. Na čepeli může uživatel připnout nůž nebo jeho různé části k přístrojové desce. Stačí kliknout na ikonu **Připnout k řídicímu panelu.**<br>
   ![Snímek obrazovky s pinovým nožem RBAC služby Azure AD Connect Health RBAC se zvýrazněnou ikonou špendlíku](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Uživatel s přiřazenou rolí Čtečky nemůže získat rozšíření Azure AD Connect Health z Azure Marketplace. Uživatel nemůže provést potřebnou operaci "vytvořit", aby tak učinily. Uživatel může stále získat do okna tím, že přejde na předchozí odkaz. Pro následné použití může uživatel připnout nůž na palubní desku.
>
>

### <a name="remove-users-or-groups"></a>Odebrání uživatelů nebo skupin
Můžete odebrat uživatele nebo skupiny přidané do Azure AD Connect Health RBAC. Jednoduše klikněte pravým tlačítkem myši na uživatele nebo skupinu a vyberte **odebrat**.<br>
![Snímek obrazovky s oknem Uživatelé služby Azure AD Connect Health RBAC se zvýrazněnou možností Odebrat](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Konec oddílu RBAC)

## <a name="next-steps"></a>Další kroky
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalace agenta služby Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md)
* [Použití azure ad připojení stavu pro synchronizaci](how-to-connect-health-sync.md)
* [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)
* [Historie verzí stavu azure a připojení](reference-connect-health-version-history.md)
