---
title: Na provoz Azure Active Directory Connect Health
description: Tento článek popisuje další operace, které lze provést po nasazení služby Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: zhiweiwangmsft
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 090a066afb24c4776f9844b8850264ffad842c59
ms.sourcegitcommit: 301128ea7d883d432720c64238b0d28ebe9aed59
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/13/2019
ms.locfileid: "56187045"
---
# <a name="azure-active-directory-connect-health-operations"></a>Na provoz Azure Active Directory Connect Health
Toto téma popisuje různé operace, které můžete provést pomocí Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Povolení e-mailových oznámení
Můžete nakonfigurovat službu Azure AD Connect Health k odesílání e-mailová oznámení, pokud výstrahy indikují, že vaší infrastruktury identit není v pořádku. K tomu dochází, když je vygenerována výstraha a když je vyřešený.

![Nastavení oznámení e-mailu snímek obrazovky s Azure AD Connect Health](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> E-mailová oznámení jsou ve výchozím nastavení povolené.
>
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Chcete-li povolit e-mailová oznámení služby Azure AD Connect Health
1. Otevřít **výstrahy** okno pro službu, pro kterou chcete dostávat e-mailové oznámení.
2. Na panelu akcí klikněte na **nastavení oznámení**.
3. V přepínači e-mailové oznámení, vyberte **ON**.
4. Zaškrtněte políčko, pokud chcete, aby všichni globální správci pro příjem e-mailová oznámení.
5. Pokud chcete dostávat e-mailová oznámení na jiné e-mailové adresy, zadejte je **další příjemci e-mailu** pole. Chcete-li odebrat e-mailovou adresu z tohoto seznamu, klikněte pravým tlačítkem na položku a vyberte **odstranit**.
6. Pro dokončení změny, klikněte na tlačítko **Uložit**. Změny se projeví až po uložení.

## <a name="delete-a-server-or-service-instance"></a>Odstranění instance služby nebo serveru

>[!NOTE] 
> Postup odstranění vyžádáním licenci premium služby Azure AD.

V některých případech můžete chtít odebrat server ze monitorovány. Zde je, co potřebujete vědět o odebrání serveru ze služby Azure AD Connect Health.

Pokud při odstraňování serveru, nezapomeňte z následujících akcí:

* Tato akce přestane shromažďovat další data z tohoto serveru. Tento server bude odebrán ze služby sledování. Po této akci nejste schopni zobrazíte nové výstrahy, monitorování nebo využití analytická data pro tento server.
* Tato akce neodinstaluje agenta stavu ze serveru. Pokud jste před provedením tohoto kroku agenta stavu neodinstalovali, můžou se zobrazovat chyby vztahujících se k agentovi stavu na serveru.
* Tato akce neodstraní data již shromážděná z tohoto serveru. Tato data se odstraní v souladu se zásadami uchovávání dat Azure.
* Po provedení této akce, pokud chcete začít monitorovat na stejný server znovu, musíte odinstalovat a znovu nainstalujte agenta stavu na tomto serveru.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Odstranění serveru ze služby Azure AD Connect Health

>[!NOTE] 
> Postup odstranění vyžádáním licenci premium služby Azure AD.

Azure AD Connect Health pro Active Directory Federation Services (AD FS) a Azure AD Connect (Sync):

1. Otevřít **Server** oknu **seznam serverů** okno tak, že vyberete název serveru, která se má odebrat.
2. Na **Server** oknu, na panelu akcí klikněte na tlačítko **odstranit**.
![Snímek obrazovky s Azure AD Connect Health se odstranit server](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Potvrďte zadáním názvu serveru v potvrzovacím okně.
4. Klikněte na **Odstranit**.

Azure AD Connect Health pro službu Azure Active Directory Domain Services:

1. Otevřít **řadiče domény** řídicího panelu.
2. Vyberte řadič domény, která se má odebrat.
3. Na panelu akcí klikněte na **odstranit vybrané**.
4. Potvrďte akci odstranění serveru.
5. Klikněte na **Odstranit**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Odstraňte instanci služby ze služby Azure AD Connect Health
V některých případech můžete chtít odebrat instanci služby. Zde je, co potřebujete vědět o odebrání instance služby ze služby Azure AD Connect Health.

Když se odstranění instance služby, mějte na paměti toto:

* Tato akce odebere aktuální instanci služby ze služby sledování.
* Tato akce neodinstaluje ani neodebere agenta stavu z libovolného serveru, které byly sledovány v rámci této instance služby. Pokud jste před provedením tohoto kroku agenta stavu neodinstalovali, můžou se zobrazovat chyby související s agentem Health na serverech.
* Všechna data z této instance služby se odstraní v souladu se zásadami uchovávání dat Azure.
* Po provedení této akce, pokud chcete začít sledovat tuto službu, odinstalujte a znovu nainstalujte agenta stavu ve všech serverech. Po provedení této akce, pokud chcete začít monitorovat na stejný server znovu, odinstalovat, přeinstalovat a registrace agenta stavu na tomto serveru.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Chcete-li odstranit instanci služby ze služby Azure AD Connect Health
1. Otevřít **služby** oknu **seznamu služeb** okno tak, že vyberete identifikátor služby (název farmy), který chcete odebrat. 
2. Na **služby** oknu, na panelu akcí klikněte na tlačítko **odstranit**. 
![Snímek obrazovky s Azure AD Connect Health se odstranit službu](./media/how-to-connect-health-operations/DeleteServer.png)
3. V potvrzovacím okně zadejte název služby (například: sts.contoso.com).
4. Klikněte na **Odstranit**.
   <br><br>

[//]: # (Začátek části RBAC)
## <a name="manage-access-with-role-based-access-control"></a>Správa přístupu prostřednictvím řízení přístupu na základě Role
[Řízení přístupu na základě rolí (RBAC)](../../role-based-access-control/role-assignments-portal.md) pro Azure AD Connect Health poskytuje přístup pro uživatele a skupiny než globální správci. Přiřadí role RBAC odpovídající uživatelé a skupiny a poskytuje mechanismus pro omezení globálních správců ve svém adresáři.

### <a name="roles"></a>Role
Azure AD Connect Health podporuje následující předdefinované role:

| Role | Oprávnění |
| --- | --- |
| Vlastník |Můžou vlastníci *spravovat přístup* (například přiřadit roli uživatele nebo skupiny), *prohlížet všechny informace* (například zobrazení výstrahy) z portálu, a *změnit nastavení* (pro například, e-mailová oznámení) v rámci Azure AD Connect Health. <br>Ve výchozím nastavení tuto roli nemají přiřazenou globální správce Azure AD a toto nastavení nedá změnit. |
| Přispěvatel |Přispěvatelé můžou *prohlížet všechny informace* (například zobrazení výstrahy) z portálu, a *změnit nastavení* (například e-mailová oznámení) v rámci Azure AD Connect Health. |
| Čtenář |Čtenáři můžou *prohlížet všechny informace* (například zobrazení výstrahy) v rámci Azure AD Connect Health na portálu. |

Všechny ostatní role (jako je například správci přístupu uživatelů nebo uživatelů DevTest Labs) mít žádný vliv na přístup v rámci Azure AD Connect Health, i když tyto role jsou dostupné v prostředí portálu.

### <a name="access-scope"></a>Obor přístupu
Azure AD Connect Health podporuje správu přístupu na dvou úrovních:

* **Všechny instance služby**: Toto je doporučený ve většině případů. Určuje přístup pro všechny instance služby (například farmu služby AD FS) napříč všemi typy rolí, které jsou monitorovány pomocí služby Azure AD Connect Health.
* **Instance služby**: V některých případech můžete potřebovat k oddělení přístupu na základě typů role nebo instance služby. V takovém případě můžete spravovat přístup na úrovni instance služby.  

Pokud koncový uživatel má přístup buď na adresář nebo služby je uděleno oprávnění instance úroveň.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Povolit uživatelům nebo skupinám přístup k Azure AD Connect Health
Následující kroky ukazují, jak povolit přístup.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Krok 1: Vyberte obor odpovídající přístup
Pokud chcete povolit přístup uživatele na *všech instancí služby* úrovně v rámci Azure AD Connect Health, otevřete hlavní okno ve službě Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Krok 2: Přidání uživatelů a skupin a přiřazování rolí
1. Z **konfigurovat** klikněte na tlačítko **uživatelé**.<br>
   ![Snímek obrazovky s Azure AD Connect Health prostředků bočního panelu](./media/how-to-connect-health-operations/startRBAC.png)
2. Vyberte **Přidat**.
3. V **vybrat roli** podokně, vyberte roli (například **vlastníka**).<br>
   ![Snímek obrazovky s Azure AD Connect stavu RBAC uživatelům okna](./media/how-to-connect-health-operations/RBAC_add.png)
4. Zadejte název nebo identifikátor cíloví uživatelé nebo skupiny. Současně můžete vybrat jeden nebo více uživatelů nebo skupin. Klikněte na **Vybrat**.
   ![Snímek obrazovky s Azure AD Connect stavu RBAC uživatelům okna](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Vyberte **OK**.<br>
6. Po dokončení přiřazení role se zobrazí v seznamu uživatelů a skupin.<br>
   ![Snímek obrazovky s Azure AD Connect stavu RBAC uživatelům okna se noví uživatelé zvýrazněnou](./media/how-to-connect-health-operations/RBAC_user_list.png)

Uvedení uživatelé a skupiny teď mají přístup, podle jejich přiřazených rolí.

> [!NOTE]
> * Globální Správci mají vždy úplný přístup ke všem operacím, ale nejsou k dispozici v předchozím seznamu účty globálních správců.
> * Pozvat uživatele funkce není podporována v rámci Azure AD Connect Health.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Krok 3: Umístění okno sdílet s uživateli nebo skupinami
1. Po přiřazení oprávnění má uživatel přístup Azure AD Connect Health tak, že přejdete [tady](https://aka.ms/aadconnecthealth).
2. V okně uživatele můžete Připnout okno nebo jiné části na řídicí panel. Stačí kliknout **připnout na řídicí panel** ikonu.<br>
   ![Snímek obrazovky s Azure AD Connect Health RBAC Připnout okno, s ikona připnout zvýrazněná](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Uživatel s přiřazenou roli čtenáře není možné získat rozšíření Azure AD Connect Health z Azure Marketplace. Uživatel nemůže provést nezbytné k tomu operace "vytvoření". Uživatel může stále získat do okna tak, že přejdete na předchozím odkazu. Za další využití může uživatel Připnout okno na řídicí panel.
>
>

### <a name="remove-users-or-groups"></a>Odebrání uživatele nebo skupiny
Uživatel nebo skupina přidaná k Azure AD Connect Health RBAC můžete odebrat. Jednoduše klikněte pravým tlačítkem na uživatele nebo skupinu a vyberte **odebrat**.<br>
![Snímek obrazovky s Azure AD Connect stavu RBAC uživatelům okna se odebrat zvýrazněnou](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Konec oddílu RBAC)

## <a name="next-steps"></a>Další postup
* [Azure AD Connect Health](whatis-hybrid-identity-health.md)
* [Instalace agenta služby Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md)
* [Používání služby Azure AD Connect Health pro synchronizaci](how-to-connect-health-sync.md)
* [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)
* [Historie verzí služby Azure AD Connect Health](reference-connect-health-version-history.md)
