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
ms.date: 01/18/2019
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 7b27c811214def7f5646f886b955d035a50c0725
ms.sourcegitcommit: fcb674cc4e43ac5e4583e0098d06af7b398bd9a9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2019
ms.locfileid: "56342469"
---
# <a name="troubleshoot-rbac-for-azure-resources"></a>Řešení potíží s RBAC pro prostředky Azure

V tomto článku, abyste věděli, co očekávat při používání role na webu Azure portal a můžete řešení problémů s přístupem k odpovědi na běžné dotazy o řízení přístupu na základě rolí (RBAC) pro prostředky Azure.

## <a name="problems-with-rbac-role-assignments"></a>Potíže s přiřazováním rolí RBAC

- Pokud se nemůžete přidat přiřazení role, protože **přidat přiřazení role** možnost je zakázaná, nebo protože se zobrazí chybu oprávnění, zkontrolujte, že používáte roli, která má `Microsoft.Authorization/roleAssignments/*` oprávnění v oboru, který se pokoušíte přiřazení role. Pokud toto oprávnění nemáte, obraťte se na správce vašeho předplatného.
- Pokud dojde k chybě oprávnění při pokusu o vytvoření prostředku, zkontrolujte, že používáte roli, která má oprávnění k vytváření prostředků na vybraný obor. Potřebujete například stát přispěvatelem. Pokud nemáte oprávnění, obraťte se na správce předplatného.
- Pokud dojde k chybě oprávnění při pokusu o vytvoření nebo aktualizaci lístek podpory, zkontrolujte, že používáte roli, která má `Microsoft.Support/*` oprávnění, jako například [podpory požádat o Přispěvatel](built-in-roles.md#support-request-contributor).
- Pokud při pokusu o přiřazení role dojde k chybě kvůli překročení počtu přiřazení rolí, zkuste snížit počet přiřazení rolí tím, že místo toho přiřadíte role ke skupinám. Azure podporuje až **2000** přiřazení rolí na jedno předplatné.

## <a name="problems-with-custom-roles"></a>Potíže s vlastními rolemi

- Pokud se nemůžete aktualizovat existující vlastní roli, zkontrolujte, jestli máte `Microsoft.Authorization/roleDefinition/write` oprávnění.
- Pokud nemůžete aktualizovat existující vlastní roli, zkontrolujte, zda jeden nebo více přiřaditelnými obory se odstranily v tenantovi. `AssignableScopes` Vlastnost pro ovládací prvky vlastní roli [který můžete vytvořit, odstranit, aktualizovat nebo zobrazit vlastní roli](custom-roles.md#who-can-create-delete-update-or-view-a-custom-role).
- Pokud dojde k chybě, která překročila maximální počet definic rolí při pokusu o vytvoření nové role, odstranit vlastní role, ke kterým se dá použít. Můžete také zkusit konsolidovat nebo opakovaně používat všechny stávající vlastní role. Azure podporuje až **2000** vlastní role v tenantovi.
- Pokud nemůžete odstranit vlastní roli, zkontrolujte, zda jeden nebo více přiřazení rolí stále používají vlastní roli.

## <a name="recover-rbac-when-subscriptions-are-moved-across-tenants"></a>Obnovení RBAC při přesouvání předplatných mezi tenanty

- Pokud potřebujete zjistit, jak postupovat při přenosu předplatného do jiného tenanta, přečtěte si téma [Přenos vlastnictví předplatného Azure na jiný účet](../billing/billing-subscription-transfer.md).
- Pokud přenesete předplatné na jiného tenanta, všechna přiřazení rolí se trvale odstraní z tenanta zdroje a nebudou migrována k cílovému tenantovi. Budete muset znovu vytvořit přiřazení role v cílovému tenantovi.
- Pokud jsou globální správy a jste ztratili přístup k předplatnému, použijte **Access management pro prostředky Azure** tímto přepínačem můžete dočasně [zvýšení vaší přístupu](elevate-access-global-admin.md) moct znovu přistupovat ke předplatné.

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

