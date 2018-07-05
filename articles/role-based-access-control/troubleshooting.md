---
title: Řešení potíží s RBAC v Azure | Dokumentace Microsoftu
description: Řešení potíží s řízením přístupu Azure na základě rolí (RBAC).
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
ms.date: 03/19/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 186bcf26639f5cff2dcbf1e805913ac7edab7df4
ms.sourcegitcommit: e0834ad0bad38f4fb007053a472bde918d69f6cb
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37437361"
---
# <a name="troubleshooting-rbac-in-azure"></a>Řešení potíží s RBAC v Azure

V tomto článku, abyste věděli, co očekávat při používání role na webu Azure portal a můžete řešení problémů s přístupem k odpovědi na běžné dotazy o řízení přístupu na základě rolí (RBAC). Tyto tři role zahrnují všechny typy prostředků:

* Vlastník  
* Přispěvatel  
* Čtenář  

Vlastníci a přispěvatelé mít úplný přístup k prostředí pro správu, ale přispěvatele nemůže udělovat přístup další uživatelé a skupiny. Co získáte o něco zajímavější pomocí role Čtenář tak, aby se, kde budete věnovat nějaký čas. Informace o tom, jak udělit přístup, seee [správě přístupu pomocí RBAC a webu Azure portal](role-assignments-portal.md).

## <a name="app-service"></a>App Service
### <a name="write-access-capabilities"></a>Možnosti přístup pro zápis
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

### <a name="dealing-with-related-resources"></a>Práce se souvisejícími prostředky
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

## <a name="azure-functions"></a>Azure Functions
Některé funkce [Azure Functions](../azure-functions/functions-overview.md) vyžadují přístup pro zápis. Například pokud uživatel má přiřazenou roli Čtenář, že nebudete moci zobrazit funkce v rámci aplikace function app. Na portálu se zobrazí **(bez přístupu)**.

![Funkční aplikace bez přístupu](./media/troubleshooting/functionapps-noaccess.png)

Můžete kliknout na tlačítko čtečky **funkce platformy** kartu a potom klikněte na tlačítko **všechna nastavení** zobrazení některých nastavení související se aplikace function app (podobně jako webová aplikace), ale nemohou upravovat některé z těchto nastavení.

## <a name="virtual-machine"></a>Virtuální počítač
Podobně jako s webovými aplikacemi, některé funkce v okně virtuálního počítače vyžadují k virtuálnímu počítači nebo k dalším prostředkům ve skupině prostředků přístup pro zápis.

Virtuální počítače se vztahují na názvy domény, virtuální sítě, účty úložiště a pravidla upozornění.

Tyto položky vyžadují **zápisu** přístup k **virtuálního počítače**:

* Koncové body  
* IP adresy  
* Disky  
* Rozšíření  

Vyžadují **zápisu** přístup k oběma **virtuálního počítače**a **skupiny prostředků** (spolu s názvem domény), zda je v:  

* Skupina dostupnosti  
* Vyrovnáváním zatížení  
* Pravidla upozornění  

Pokud nemůžete použít žádnou z těchto dlaždic, požádejte správce přístup přispěvatele do skupiny prostředků.

## <a name="next-steps"></a>Další postup
* [Správa přístupu pomocí RBAC a webu Azure portal](role-assignments-portal.md)
* [Zobrazení protokolů aktivit pro RBAC změny](change-history-report.md)

