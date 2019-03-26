---
title: Řešení potíží s RBAC pro prostředky Azure | Dokumentace Microsoftu
description: Řešení potíží s řízením přístupu na základě rolí (RBAC) pro prostředky Azure.
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 03/24/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: d85c49cc8533b88382de81f8f12fde7116afb69a
ms.sourcegitcommit: 280d9348b53b16e068cf8615a15b958fccad366a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/25/2019
ms.locfileid: "58407585"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Řešení potíží s RBAC pro prostředky Azure

V tomto článku, abyste věděli, co očekávat při používání role na webu Azure portal a můžete řešení problémů s přístupem k odpovědi na běžné dotazy o řízení přístupu na základě rolí (RBAC) pro prostředky Azure.

## <a name="problems-with-rbac-role-assignments"></a>Potíže s přiřazováním rolí RBAC

- Pokud se nemůžete přidat přiřazení role na webu Azure Portal na **řízení přístupu (IAM)** protože **přidat** > **přidat přiřazení role** možnost je zakázaná, nebo vzhledem k tomu, že se zobrazí chyba oprávnění "Klient s id objektu nemá oprávnění k provedení akce", zkontrolujte, že jste aktuálně přihlášení jako uživatel, který má přiřazenou roli, která má `Microsoft.Authorization/roleAssignments/write` oprávnění jako [vlastníka](built-in-roles.md#owner) nebo [správce uživatelských přístupů](built-in-roles.md#user-access-administrator) v oboru, který se snažíte přiřadit roli.
- Pokud se zobrazí chybová zpráva "žádné další přiřazení rolí je možné vytvořit. (kód: RoleAssignmentLimitExceeded) "při pokusu o přiřazení role se snaží snížit počet přiřazení rolí tak, že místo toho přiřadíte role do skupin. Azure podporuje až **2000** přiřazení rolí na jedno předplatné.

## <a name="problems-with-custom-roles"></a>Potíže s vlastními rolemi

- Pokud potřebujete postup, jak vytvořit vlastní roli, najdete v kurzech vlastní role pomocí [prostředí Azure PowerShell](tutorial-custom-role-powershell.md) nebo [rozhraní příkazového řádku Azure](tutorial-custom-role-cli.md).
- Pokud se nemůžete aktualizovat existující vlastní roli, zkontrolujte, že jste aktuálně přihlášení jako uživatel, který má přiřazenou roli, která má `Microsoft.Authorization/roleDefinition/write` oprávnění jako [vlastníka](built-in-roles.md#owner) nebo [správce uživatelských přístupů](built-in-roles.md#user-access-administrator).
- Pokud nemůžete odstranit vlastní roli a zobrazí se chybová zpráva "existuje jsou odkazují stávající přiřazení rolí role (kód: RoleDefinitionHasAssignments) ", pak přiřazení rolí se pořád používá vlastní roli. Odebrání těchto přiřazení rolí a zkuste to znovu odstranit vlastní roli.
- Pokud se zobrazí chybová zpráva "maximální počet definic rolí překročena. Je možné vytvořit další definice rolí (kód: RoleDefinitionLimitExceeded) "při pokusu o vytvoření nové vlastní role odstranit všechny vlastní role, které nejsou používány. Azure podporuje až **2000** vlastní role v tenantovi.
- Pokud podobně jako "klient má oprávnění k provedení akce"Microsoft.Authorization/roleDefinitions/write"v rozsahu '/Subscriptions/ {subscriptionid}', ale nebyl nalezen propojenému předplatnému" dojde k chybě při pokusu aktualizovat vlastní roli, zkontrolujte zda jeden nebo více [přiřaditelnými obory](role-definitions.md#assignablescopes) byly odstraněny v tenantovi. Pokud byla odstraněna oboru, vytvořte lístek podpory jako neexistuje žádné řešení samoobslužné služby v tuto chvíli k dispozici.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Obnovení RBAC při přesouvání předplatných mezi tenanty

- Pokud potřebujete postup na převod předplatného do jiné služby Azure AD tenanta, přečtěte si téma [přenos vlastnictví předplatného Azure na jiný účet](../billing/billing-subscription-transfer.md).
- Převod předplatného do jiné služby Azure AD tenanta, všechna přiřazení rolí se trvale odstraní z tenanta Azure AD zdroje a nebudou migrována do cílového tenanta Azure AD. Budete muset znovu vytvořit přiřazení role v cílovému tenantovi.
- Pokud jste Azure AD globálního správce a nemají přístup k předplatnému po byl přesunut mezi tenanty, použijte **Access management pro prostředky Azure** tímto přepínačem můžete dočasně [zvýšení vaší přístupu](elevate-access-global-admin.md) získat přístup k předplatnému.

## <a name="issues-with-service-admins-or-co-admins"></a>Problémy se správci služeb nebo spolusprávci

- Pokud máte problémy s pomocí Správce služeb nebo spolupracující správce, přečtěte si téma [přidat nebo změnit správce předplatného Azure](../billing/billing-add-change-azure-subscription-administrator.md) a [Classic role správců předplatného, role Azure RBAC a Azure AD role správce](rbac-and-directory-admin-roles.md).

## <a name="access-denied-or-permission-errors"></a>Přístup byl odepřen nebo chyby oprávnění

- Pokud se zobrazí chyba oprávnění "Klient s id objektu nemá oprávnění k provedení akce rozsahu (kód: AuthorizationFailed) "při pokusu o vytvoření prostředku, zkontrolujte, že jste aktuálně přihlášení jako uživatel, který je přiřazena role, který má oprávnění k zápisu do zdroje na vybraný obor. Třeba ke správě virtuálních počítačů ve skupině prostředků, měli byste mít [Přispěvatel virtuálních počítačů](built-in-roles.md#virtual-machine-contributor) role na skupinu prostředků (nebo nadřazený obor). Seznam oprávnění pro každou integrovanou roli najdete v tématu [předdefinované role pro prostředky Azure](built-in-roles.md).
- Pokud se zobrazí chyba oprávnění "Nemáte oprávnění k vytvoření žádosti o podporu" při pokusu o vytvoření nebo aktualizaci lístek podpory, zkontrolujte, že jste aktuálně přihlášení jako uživatel, který má přiřazenou roli, která má `Microsoft.Support/supportTickets/write` oprávnění, jako je například [Přispěvatel žádostí o podporu](built-in-roles.md#support-request-contributor).

## <a name="rbac-changes-are-not-being-detected"></a>Nebyly detekovány změny RBAC

Azure Resource Manageru někdy ukládá do mezipaměti, konfigurace a dat s cílem zlepšit výkon. Při vytváření nebo odstraňování přiřazení rolí, může trvat až 30 minut, než se změny projevily. Pokud používáte web Azure portal, prostředí Azure PowerShell nebo rozhraní příkazového řádku Azure, můžete vynutit aktualizaci změn přiřazení role odhlášení a přihlášení. Pokud provádíte změny přiřazení role pomocí volání rozhraní REST API, můžete vynutit aktualizaci obnovením přístupového tokenu.

## <a name="web-app-features-that-require-write-access"></a>Webové aplikace funkce, které vyžadují přístup pro zápis

Pokud udělíte přístup uživatele jen pro čtení na jednu webovou aplikaci, některé funkce jsou zakázané, nemusíte očekávat. Následující možnosti správy vyžadují **zápisu** přístup do webové aplikace (Přispěvatel nebo vlastník) a nejsou k dispozici ve všech scénářích jen pro čtení.

* Příkazy (jako je spuštění, zastavení, atd.)
* Změny nastavení, jako jsou obecné konfigurace, nastavení škálování, nastavení zálohování a nastavení monitorování
* Přístup k přihlašovací údaje pro publikování a dalších tajných kódů jako nastavení aplikace a připojovacích řetězců
* Protokoly streamování
* Konfigurace diagnostických protokolů
* Konzoly (příkazového řádku)
* Aktivní a poslední nasazení (pro místní nepřetržité nasazení z gitu)
* Předpokládaná útrata
* Webové testy
* Virtuální síť (viditelné pouze pro čtečku, pokud virtuální sítě byl dříve nakonfigurován uživatel s oprávněním k zápisu).

Pokud nemůžete použít žádnou z těchto dlaždic, budete muset požádat správce o přístup přispěvatele do webové aplikace.

## <a name="web-app-resources-that-require-write-access"></a>Webové aplikace prostředky, které vyžadují přístup pro zápis

Webové aplikace jsou složité přítomností několik různých prostředků, které souhra grafů. Tady je skupina prostředků typické se několik webů:

![Skupiny prostředků webové aplikace](./media/troubleshooting/website-resource-model.png)

Ve výsledku Pokud někomu udělíte přístup jenom na webovou aplikaci, většina funkcí v okně Web na webu Azure Portal je zakázaný.

Tyto položky vyžadují **zápisu** přístup k **plán služby App Service** , který odpovídá na váš web:  

* Zobrazení webovou aplikaci prvku cenová úroveň (Free nebo Standard)  
* Konfigurace škálování (počet instancí, velikost virtuálního počítače, nastavení automatického škálování)  
* Kvóty (úložiště, šířku pásma, využití procesoru)  

Tyto položky vyžadují **zápisu** přístup k celé **skupiny prostředků** svůj web, který obsahuje:  

* Certifikáty SSL a vazeb (certifikáty protokolu SSL je sdílet mezi servery ve stejné skupině prostředků a zeměpisné polohy)  
* Pravidla upozornění  
* Nastavení automatického škálování  
* Komponenty Application Insights  
* Webové testy  

## <a name="virtual-machine-features-that-require-write-access"></a>Funkce virtuálních počítačů, které vyžadují přístup pro zápis

Podobně jako u webových aplikací, některé funkce v okně virtuálního počítače vyžadují k virtuálnímu počítači nebo k dalším prostředkům ve skupině prostředků přístup pro zápis.

Virtuální počítače se vztahují na názvy domény, virtuální sítě, účty úložiště a pravidla upozornění.

Tyto položky vyžadují **zápisu** přístup k **virtuálního počítače**:

* Koncové body  
* IP adresy  
* Disky  
* Rozšíření  

Vyžadují **zápisu** přístup k oběma **virtuálního počítače**a **skupiny prostředků** (spolu s názvem domény), zda je v:  

* Skupina dostupnosti  
* Skupina s vyrovnáváním zatížení  
* Pravidla upozornění  

Pokud nemůžete použít žádnou z těchto dlaždic, požádejte správce přístup přispěvatele do skupiny prostředků.

## <a name="azure-functions-and-write-access"></a>Služba Azure Functions a přístup pro zápis

Některé funkce [Azure Functions](../azure-functions/functions-overview.md) vyžadují přístup pro zápis. Například pokud uživatel má přiřazenou roli Čtenář, že nebudete moci zobrazit funkce v rámci aplikace function app. Na portálu se zobrazí **(bez přístupu)**.

![Funkční aplikace bez přístupu](./media/troubleshooting/functionapps-noaccess.png)

Můžete kliknout na tlačítko čtečky **funkce platformy** kartu a potom klikněte na tlačítko **všechna nastavení** zobrazení některých nastavení související se aplikace function app (podobně jako webová aplikace), ale nemohou upravovat některé z těchto nastavení.

## <a name="next-steps"></a>Další postup
* [Správa přístupu k prostředkům Azure pomocí RBAC a webu Azure portal](role-assignments-portal.md)
* [Zobrazení protokolů aktivit se změny prostředků Azure RBAC](change-history-report.md)

