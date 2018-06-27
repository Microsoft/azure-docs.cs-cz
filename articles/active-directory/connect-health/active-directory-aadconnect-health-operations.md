---
title: Operace v Azure Active Directory Connect Health
description: Tento článek popisuje další operace, které lze provést po nasazení Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: karavar
manager: mtillman
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/18/2017
ms.author: billmath
ms.openlocfilehash: 4377e9902696a4ee3b5bbb5fcb44d972ec14f822
ms.sourcegitcommit: 0408c7d1b6dd7ffd376a2241936167cc95cfe10f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/26/2018
ms.locfileid: "36958721"
---
# <a name="azure-active-directory-connect-health-operations"></a>Operace v Azure Active Directory Connect Health
Toto téma popisuje různé operace, které můžete provádět pomocí Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Povolení e-mailových oznámení
Můžete nakonfigurovat službu Azure AD Connect Health k odesílání e-mailová oznámení, pokud výstrahy indikují, že infrastruktury identity není v pořádku. K tomu dojde, pokud je vygenerována výstraha a pokud je vyřešený.

![Nastavení oznámení e-mailu snímek Azure AD Connect Health](./media/active-directory-aadconnect-health/email_noti_discover.png)

> [!NOTE]
> E-mailová oznámení jsou ve výchozím nastavení povolené.
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Chcete-li povolit e-mailová oznámení Azure AD Connect Health
1. Otevřete **výstrahy** okno pro službu, pro který chcete dostávat e-mailové oznámení.
2. Na panelu akcí klikněte na **nastavení oznámení**.
3. V e-mailové oznámení přepínači, vyberte **ON**.
4. Zaškrtněte políčko, pokud chcete, aby všichni globální správci pro příjem e-mailová oznámení.
5. Pokud chcete dostávat e-mailová oznámení na jiné e-mailové adresy, zadejte je do **další příjemců e-mailu** pole. Chcete-li odebrat e-mailovou adresu z tohoto seznamu, klikněte pravým tlačítkem myši na položku a vyberte **odstranit**.
6. Chcete-li dokončení změn, klikněte na tlačítko **Uložit**. Změny se projeví až po uložení.

## <a name="delete-a-server-or-service-instance"></a>Odstranění instance serveru nebo služby

>[!NOTE] 
> Je nutné postup odstranění Azure AD premium licenci.

V některých případech můžete chtít odebrání serveru z monitorovány. Zde je, co potřebujete vědět o odebrání serveru z služby Azure AD Connect Health.

Pokud odstraňujete serveru, nezapomeňte z následujících akcí:

* Tato akce zastaví sběr žádná další data z tohoto serveru. Tento server je odebrán ze služby sledování. Po této akci nejste schopni zobrazit nové výstrahy, monitorování nebo využití analytická data pro tento server.
* Tato akce neodinstaluje agenta stavu ze serveru. Pokud jste agenta stavu neodinstalovali před provedením tohoto kroku, může se zobrazit chyby související s agenta stavu na serveru.
* Tato akce neodstraní data již shromážděná z tohoto serveru. Data odstraněna podle zásad uchovávání dat Azure.
* Po provedení této akce, pokud chcete začít monitorovat stejný server znovu, musíte odinstalovat a znovu nainstalujte agenta stavu na tomto serveru.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Odstranění serveru ze služby Azure AD Connect Health

>[!NOTE] 
> Je nutné postup odstranění Azure AD premium licenci.

Azure AD Connect Health pro Active Directory Federation Services (AD FS) a Azure AD Connect (Sync):

1. Otevřete **Server** okno z **seznam serverů** tak, že vyberete serveru název odeberou.
2. Na **Server** , z panelu akcí klikněte na **odstranit**.
3. Potvrďte zadáním názvu serveru v poli potvrzení.
4. Klikněte na **Odstranit**.

Azure AD Connect Health pro službu Azure Active Directory Domain Services:

1. Otevřete **řadiče domény** řídicího panelu.
2. Vyberte řadič domény odeberou.
3. Na panelu akcí klikněte na **odstranit vybrané**.
4. Potvrzení této akce se odstranit server.
5. Klikněte na **Odstranit**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Odstranění instance služby ze služby Azure AD Connect Health
V některých případech můžete chtít odebrat instanci služby. Zde je, co potřebujete vědět, abyste mohli odebrat instanci služby ze služby Azure AD Connect Health.

Pokud odstraňujete instance služby, uvědomte si z následujících akcí:

* Tato akce odebere aktuální instanci služby ze služby sledování.
* Tato akce neodinstaluje ani neodebere agenta stavu ze všech serverů, které byly monitorovány v rámci této instance služby. Pokud jste agenta stavu neodinstalovali před provedením tohoto kroku, může se zobrazit chyby související s agenta stavu na serveru.
* Všechna data z této instance služby se odstraní podle zásad uchovávání dat Azure.
* Po provedení této akce, pokud chcete začít sledovat tuto službu, odinstalujte a znovu nainstalujte agenta stavu ve všech serverech. Po provedení této akce, pokud chcete začít monitorovat stejný server znovu, odinstalovat, přeinstalovat a registrace agenta stavu na tomto serveru.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Chcete-li odstranit instanci služby ze služby Azure AD Connect Health
1. Otevřete **služby** okno z **seznam služeb** okno výběrem identifikátor služby (název farmy), který chcete odebrat.
2. Na **Server** , z panelu akcí klikněte na **odstranit**.
3. Potvrďte zadáním názvu služby do pole pro potvrzení (například:. sts.contoso.com).
4. Klikněte na **Odstranit**.
   <br><br>

[//]: # (Začátek RBAC oddílu)
## <a name="manage-access-with-role-based-access-control"></a>Správa přístupu pomocí řízení přístupu na základě rolí
[Řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/role-assignments-portal.md) pro Azure AD Connect Health poskytuje přístup uživatelům a skupinám než globální správci. RBAC přiřadí role pro příslušné uživatele a skupiny a poskytuje mechanismus pro omezení globálních správců v adresáři.

### <a name="roles"></a>Role
Azure AD Connect Health podporuje následující předdefinované role:

| Role | Oprávnění |
| --- | --- |
| Vlastník |Mohou vlastníci *spravovat přístup* (například přiřazení role uživatele nebo skupiny), *prohlížet všechny informace* (například zobrazení výstrahy) z portálu, a *změnit nastavení* (pro Příklad, e-mailových oznámení) v rámci Azure AD Connect Health. <br>Ve výchozím nastavení tuto roli nemají přiřazenou globální správce Azure AD a toto nastavení nelze změnit. |
| Přispěvatel |Přispěvatelé můžou *prohlížet všechny informace* (například zobrazení výstrahy) z portálu, a *změnit nastavení* (například e-mailová oznámení) v rámci Azure AD Connect Health. |
| Čtenář |Můžete čtečky *prohlížet všechny informace* (například zobrazení výstrahy) z portálu v Azure AD Connect Health. |

Všechny ostatní role (například uživatel přístup správci nebo uživatelé DevTest Labs) nemají žádný vliv na přístup v rámci Azure AD Connect Health, i když tyto role jsou k dispozici v prostředí portálu.

### <a name="access-scope"></a>Obor přístupu
Azure AD Connect Health podporuje správu přístupu na dvou úrovních:

* **Všechny instance služby**: Jedná se o doporučený ve většině případů. Určuje přístup pro všechny instance služby (například farmu služby AD FS), typů role, které jsou monitorovány pomocí Azure AD Connect Health.
* **Instance služby**: V některých případech možná budete muset oddělit přístupu založených na typech role nebo instance služby. V takovém případě můžete spravovat přístup na úrovni instance služby.  

Je povoleno, pokud koncový uživatel má přístup buď na directory nebo služby na úrovni instance.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Povolit uživatelů nebo skupin přístup k Azure AD Connect Health
Následující kroky ukazují, jak povolit přístup.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Krok 1: Vyberte odpovídající přístup obor
Povolit přístup uživatele na *všech instancí služby* úrovně v rámci Azure AD Connect Health, otevřete okno hlavní v Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Krok 2: Přidání uživatelů a skupin a přiřadit role
1. Z **konfigurace** klikněte na tlačítko **uživatelé**.<br>
   ![Snímek obrazovky Azure AD Connect Health prostředků bočním panelu](./media/active-directory-aadconnect-health/startRBAC.png)
2. Vyberte **Přidat**.
3. V **vyberte roli** podokně, vyberte roli (například **vlastníka**).<br>
   ![Snímek Azure AD Connect Health RBAC uživatelé okno](./media/active-directory-aadconnect-health/RBAC_add.png)
4. Zadejte název nebo identifikátor cílové uživatele nebo skupinu. Současně můžete vybrat jeden nebo více uživatelů nebo skupin. Klikněte na **Vybrat**.
   ![Snímek Azure AD Connect Health RBAC uživatelé okno](./media/active-directory-aadconnect-health/RBAC_select_users.png)
5. Vyberte **OK**.<br>
6. Po dokončení přiřazení role uživatele a skupiny v seznamu se zobrazí.<br>
   ![Připojení stavu RBAC uživatelům okno snímek obrazovky Azure AD, s nových uživatelů zvýrazněných](./media/active-directory-aadconnect-health/RBAC_user_list.png)

Nyní uvedené uživatele a skupiny mají přístup, podle jejich přiřazené role.

> [!NOTE]
> * Globální správci vždy mají plný přístup ke všem operacím, ale nejsou k dispozici v předchozím seznamu účtů globální správce.
> * Funkce pozvat uživatele není podporována v rámci Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Krok 3: Sdílejte okno umístění k uživatelům nebo skupinám
1. Po přiřazení oprávnění, může uživatel získat přístup Azure AD Connect Health přechodem [zde](https://aka.ms/aadconnecthealth).
2. V okně uživatele můžete Připnout okno nebo různé části, na řídicí panel. Jednoduše klikněte **připnout na řídicí panel** ikonu.<br>
   ![Snímek Azure AD Connect Health RBAC Připnout okno, se zvýrazněnou ikonu Připnutí](./media/active-directory-aadconnect-health/RBAC_pin_blade.png)

> [!NOTE]
> Uživatel s přiřazenou roli čtečky není možné získat rozšíření Azure AD Connect Health z Azure Marketplace. Uživatele nelze provést nezbytné operace Uděláte to tak "vytvoření". Uživatele můžete pořád dostat do okna přechodem předchozí odkaz na. Pro následné použití můžete uživatele Připnout okno na řídicí panel.
>
>

### <a name="remove-users-or-groups"></a>Odebrat uživatele nebo skupiny
Můžete odebrat uživatele nebo skupinu přidat do Azure AD Connect Health RBAC. Jednoduše klikněte pravým tlačítkem na uživatele nebo skupinu a vyberte **odebrat**.<br>
![Připojení stavu RBAC uživatelům okno snímek obrazovky Azure AD, s odebrat zvýrazněná](./media/active-directory-aadconnect-health/RBAC_remove.png)

[//]: # (Konci části RBAC)

## <a name="next-steps"></a>Další postup
* [Azure AD Connect Health](active-directory-aadconnect-health.md)
* [Instalace agenta služby Azure AD Connect Health](active-directory-aadconnect-health-agent-install.md)
* [Používání služby Azure AD Connect Health se službou AD FS](active-directory-aadconnect-health-adfs.md)
* [Používání služby Azure AD Connect Health pro synchronizaci](active-directory-aadconnect-health-sync.md)
* [Používání služby Azure AD Connect Health se službou AD DS](active-directory-aadconnect-health-adds.md)
* [Azure AD Connect Health – nejčastější dotazy](active-directory-aadconnect-health-faq.md)
* [Historie verzí služby Azure AD Connect Health](active-directory-aadconnect-health-version-history.md)
