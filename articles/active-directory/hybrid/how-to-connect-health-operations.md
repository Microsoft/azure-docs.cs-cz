---
title: Azure Active Directory Connect Health operace
description: Tento článek popisuje další operace, které lze provést po nasazení Azure AD Connect Health.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
ms.assetid: 86cc3840-60fb-43f9-8b2a-8598a9df5c94
ms.service: active-directory
ms.subservice: hybrid
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: how-to
ms.date: 07/18/2017
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 99ad0963677b9ef536deacc2122130186afe0726
ms.sourcegitcommit: 42a4d0e8fa84609bec0f6c241abe1c20036b9575
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/08/2021
ms.locfileid: "98016962"
---
# <a name="azure-active-directory-connect-health-operations"></a>Azure Active Directory Connect Health operace
V tomto tématu najdete popis různých operací, které můžete provádět pomocí služby Azure Active Directory (Azure AD) Connect Health.

## <a name="enable-email-notifications"></a>Povolit e-mailová oznámení
Službu Azure AD Connect Health můžete nakonfigurovat tak, aby odesílala e-mailová oznámení, když výstrahy indikují, že vaše infrastruktura identity není v dobrém stavu. K tomu dojde, když se vygeneruje výstraha a když se vyřeší.

![Snímek obrazovky s nastavením Azure AD Connect Health e-mailových oznámení](./media/how-to-connect-health-operations/email_noti_discover.png)

> [!NOTE]
> E-mailová oznámení jsou ve výchozím nastavení povolená.
>

### <a name="to-enable-azure-ad-connect-health-email-notifications"></a>Povolení Azure AD Connect Health e-mailových oznámení
1. Otevřete okno **výstrahy** pro službu, pro kterou chcete dostávat e-mailová oznámení.
2. Na panelu akcí klikněte na **Nastavení oznámení**.
3. V přepínači e-mailových oznámení vyberte **zapnuto**.
4. Zaškrtněte políčko, pokud chcete, aby všichni globální správci dostávali e-mailová oznámení.
5. Pokud chcete dostávat e-mailová oznámení na jakékoli jiné e-mailové adresy, zadejte je do pole **Další příjemci e-mailu** . Chcete-li odebrat e-mailovou adresu z tohoto seznamu, klikněte na položku pravým tlačítkem myši a vyberte možnost **Odstranit**.
6. Změny dokončíte kliknutím na **Uložit**. Změny se projeví až po uložení.

>[!NOTE] 
> Pokud dojde k problémům se zpracováním požadavků na synchronizaci v naší back-end službě, tato služba pošle e-mailovou zprávu s podrobnostmi o této chybě do kontaktní e-mailové adresy (ES) pro správu vašeho tenanta. Slyšeli jsme od zákazníků zpětnou vazbu, že v některých případech je objem těchto zpráv zakazují velký, takže měníme způsob, jakým tyto zprávy posíláme. 
>
> Místo odeslání zprávy pro každou chybu synchronizace pokaždé, když dojde k chybě, pošleme denní přehled všech chyb, které služba back-end vrátila. To zákazníkům umožňuje efektivně zpracovávat tyto chyby a snižuje počet duplicitních chybových zpráv.
>
> Tuto změnu plánujeme, aby se implementovala 15. ledna 2020.

## <a name="delete-a-server-or-service-instance"></a>Odstranění instance serveru nebo služby

>[!NOTE] 
> Pro kroky odstranění se vyžaduje licence Azure AD Premium.

V některých případech můžete chtít odebrat server ze sledování. Tady je přehled toho, co potřebujete znát k odebrání serveru ze služby Azure AD Connect Health.

Při odstraňování serveru mějte na paměti následující:

* Tato akce ukončí shromažďování dalších dat z tohoto serveru. Tento server je odebrán ze služby sledování. Po provedení této akce nebudete moci zobrazit nová upozornění, monitorování nebo data analýzy využití tohoto serveru.
* Tato akce neodinstaluje agenta stavu ze serveru. Pokud jste před provedením tohoto kroku neinstalovali agenta stavu, mohou se zobrazit chyby týkající se agenta stavu na serveru.
* Tato akce neodstraní data již shromážděná z tohoto serveru. Tato data se odstraní v souladu se zásadami pro uchovávání dat v Azure.
* Pokud po provedení této akce chcete znovu spustit monitorování stejného serveru, musíte na tomto serveru odinstalovat a znovu nainstalovat agenta stavu.

### <a name="delete-a-server-from-the-azure-ad-connect-health-service"></a>Odstranění serveru ze služby Azure AD Connect Health

>[!NOTE] 
> Pro kroky odstranění se vyžaduje licence Azure AD Premium.

Azure AD Connect Health pro Active Directory Federation Services (AD FS) (AD FS) a Azure AD Connect (Sync):

1. Otevřete okno **Server** v okně **seznam serverů** tak, že vyberete název serveru, který chcete odebrat.
2. V okně **Server** klikněte na panelu akcí na **Odstranit**.
![Snímek obrazovky Azure AD Connect Health odstranění serveru](./media/how-to-connect-health-operations/DeleteServer2.png)
3. Potvrďte zadáním názvu serveru do potvrzovacího pole.
4. Klikněte na **Odstranit**.

Azure AD Connect Health pro Azure Active Directory Domain Services:

1. Otevřete řídicí panel **řadiče domény** .
2. Vyberte řadič domény, který se má odebrat.
3. Na panelu akcí klikněte na **Odstranit vybrané**.
4. Potvrďte akci odstranění serveru.
5. Klikněte na **Odstranit**.

### <a name="delete-a-service-instance-from-azure-ad-connect-health-service"></a>Odstraní instanci služby z Azure AD Connect Health služby.
V některých případech je vhodné odebrat instanci služby. Tady je přehled toho, co potřebujete znát k odebrání instance služby z Azure AD Connect Health služby.

Při odstraňování instance služby mějte na paměti následující skutečnosti:

* Tato akce odebere aktuální instanci služby ze služby sledování.
* Tato akce neprovádí odinstalaci nebo odebrání agenta stavu ze všech serverů, které byly monitorovány jako součást této instance služby. Pokud jste před provedením tohoto kroku neinstalovali agenta stavu, mohou se zobrazit chyby týkající se agenta stavu na serverech.
* Všechna data z této instance služby se odstraní podle zásad uchovávání dat Azure.
* Pokud po provedení této akce chcete spustit monitorování služby, odinstalujte a znovu nainstalujte agenta stavu na všech serverech. Pokud po provedení této akce chcete znovu spustit monitorování stejného serveru, odinstalujte, znovu nainstalujte a zaregistrujte agenta stavu na tomto serveru.

#### <a name="to-delete-a-service-instance-from-the-azure-ad-connect-health-service"></a>Odstranění instance služby ze služby Azure AD Connect Health
1. Otevřete okno **služby** v okně **seznam služeb** tak, že vyberete identifikátor služby (název farmy), který chcete odebrat. 
2. V okně **Služba** klikněte na panelu akcí na **Odstranit**. 
![Snímek obrazovky Azure AD Connect Health odstranění služby](./media/how-to-connect-health-operations/DeleteServer.png)
3. Potvrďte zadáním názvu služby do potvrzovacího pole (například: sts.contoso.com).
4. Klikněte na **Odstranit**.
   <br><br>

[//]: # (Začátek oddílu RBAC)
## <a name="manage-access-with-azure-rbac"></a>Správa přístupu pomocí Azure RBAC
[Řízení přístupu na základě role v Azure (Azure RBAC)](../../role-based-access-control/role-assignments-portal.md) pro Azure AD Connect Health poskytuje přístup k uživatelům a skupinám jiným než globálním správcům. Azure RBAC přiřazuje role k určeným uživatelům a skupinám a poskytuje mechanismus pro omezení globálních správců v rámci vašeho adresáře.

### <a name="roles"></a>Role
Azure AD Connect Health podporuje následující předdefinované role:

| Role | Oprávnění |
| --- | --- |
| Vlastník |Vlastníci mohou *spravovat přístup* (například přiřadit roli uživateli nebo skupině), *Zobrazit všechny informace* (například zobrazit výstrahy) z portálu a *změnit nastavení* (například e-mailová oznámení) v rámci Azure AD Connect Health. <br>Ve výchozím nastavení se této roli přiřazují globální Správci služby Azure AD a tato role se nedá změnit. |
| Přispěvatel |Přispěvatelé mohou *Zobrazit všechny informace* (například zobrazit výstrahy) z portálu a *změnit nastavení* (například e-mailová oznámení) v rámci Azure AD Connect Health. |
| Čtenář |Čtenáři můžou *Zobrazit všechny informace* (například zobrazit výstrahy) z portálu v rámci Azure AD Connect Health. |

Všechny ostatní role (například uživatelé s přístupem uživatelů nebo DevTest Labs) nemají žádný vliv na přístup v rámci Azure AD Connect Health, a to ani v případě, že role jsou k dispozici v prostředí portálu.

### <a name="access-scope"></a>Rozsah přístupu
Azure AD Connect Health podporuje správu přístupu na dvou úrovních:

* **Všechny instance služby**: Toto je doporučená cesta ve většině případů. Řídí přístup pro všechny instance služby (například AD FS farmě) napříč všemi typy rolí, které jsou monitorovány pomocí Azure AD Connect Health.
* **Instance služby**: v některých případech možná budete muset oddělit přístup na základě typů rolí nebo instance služby. V takovém případě můžete spravovat přístup na úrovni instance služby.  

Oprávnění je uděleno, pokud má koncový uživatel přístup buď na úrovni adresáře nebo instance služby.

### <a name="allow-users-or-groups-access-to-azure-ad-connect-health"></a>Dovolit uživatelům nebo skupinám přístup k Azure AD Connect Health
Následující kroky ukazují, jak přístup udělit.
#### <a name="step-1-select-the-appropriate-access-scope"></a>Krok 1: vyberte příslušný obor přístupu.
Chcete-li uživateli dovolit přístup na úrovni *všech instancí služby* v rámci Azure AD Connect Health, otevřete hlavní okno v Azure AD Connect Health.<br>

#### <a name="step-2-add-users-and-groups-and-assign-roles"></a>Krok 2: Přidání uživatelů a skupin a přiřazení rolí
1. V části **Konfigurace** klikněte na možnost **Uživatelé**.<br>
   ![Snímek obrazovky s postranním panelem Azure AD Connect Health prostředků](./media/how-to-connect-health-operations/startRBAC.png)
2. Vyberte **Přidat**.
3. V podokně **Vybrat roli** vyberte roli (například **vlastník**).<br>
   ![Snímek obrazovky s nabídkou konfigurace Azure AD Connect Health a Azure RBAC](./media/how-to-connect-health-operations/RBAC_add.png)
4. Zadejte název nebo identifikátor cílového uživatele nebo skupiny. Můžete vybrat jednoho nebo více uživatelů nebo skupin současně. Klikněte na **Vybrat**.
   ![Snímek obrazovky Azure AD Connect Health a seznamu rolí Azure](./media/how-to-connect-health-operations/RBAC_select_users.png)
5. Vyberte **OK**.<br>
6. Po dokončení přiřazení role se uživatelé a skupiny zobrazí v seznamu.<br>
   ![Snímek obrazovky s vybranými Azure AD Connect Health a Azure RBAC a novými uživateli](./media/how-to-connect-health-operations/RBAC_user_list.png)

K uvedeným uživatelům a skupinám teď mají přístup podle jejich přiřazených rolí.

> [!NOTE]
> * Globální správci mají vždycky plný přístup ke všem operacím, ale globální účty správců nejsou k dispozici v předchozím seznamu.
> * Funkce pozvat uživatele není v Azure AD Connect Health podporována.
>
>

#### <a name="step-3-share-the-blade-location-with-users-or-groups"></a>Krok 3: sdílení umístění okna s uživateli nebo skupinami
1. Po přiřazení oprávnění může uživatel k Azure AD Connect Health přistupovat tak, že [ho](https://aka.ms/aadconnecthealth)přejdeme.
2. V okně může uživatel připnout okno nebo jiné jeho části na řídicí panel. Jednoduše klikněte na ikonu **Připnout na řídicí panel** .<br>
   ![Snímek obrazovky okna Azure AD Connect Health a PIN kódu Azure RBAC se zvýrazněnou ikonou připnutí](./media/how-to-connect-health-operations/RBAC_pin_blade.png)

> [!NOTE]
> Uživatel s přiřazenou rolí Čtenář nemůže získat Azure AD Connect Health rozšíření z Azure Marketplace. Uživatel nemůže provést potřebnou operaci "vytvořit". Uživatel se přesto může dostat do okna tak, že se vrátí na předchozí odkaz. Pro následné použití může uživatel připnout okno na řídicí panel.
>
>

### <a name="remove-users-or-groups"></a>Odebrat uživatele nebo skupiny
Můžete odebrat uživatele nebo skupinu přidanou do Azure AD Connect Health a Azure RBAC. Jednoduše klikněte pravým tlačítkem myši na uživatele nebo skupinu a vyberte možnost **Odebrat**.<br>
![Snímek obrazovky Azure AD Connect Health a Azure RBAC se zvýrazněnou možností odebrat](./media/how-to-connect-health-operations/RBAC_remove.png)

[//]: # (Konec oddílu RBAC)

## <a name="next-steps"></a>Další kroky
* [Azure AD Connect Health](./whatis-azure-ad-connect.md)
* [Instalace agenta služby Azure AD Connect Health](how-to-connect-health-agent-install.md)
* [Používání služby Azure AD Connect Health se službou AD FS](how-to-connect-health-adfs.md)
* [Použití Azure AD Connect Health k synchronizaci](how-to-connect-health-sync.md)
* [Používání služby Azure AD Connect Health se službou AD DS](how-to-connect-health-adds.md)
* [Azure AD Connect Health – nejčastější dotazy](reference-connect-health-faq.md)
* [Historie verzí služby Azure AD Connect Health](reference-connect-health-version-history.md)