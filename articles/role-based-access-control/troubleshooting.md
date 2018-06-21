---
title: Řešení potíží s RBAC v Azure | Microsoft Docs
description: Vyřešte problémy s řízení přístupu Azure na základě rolí (RBAC).
services: azure-portal
documentationcenter: na
author: rolyon
manager: mtillman
ms.assetid: df42cca2-02d6-4f3c-9d56-260e1eb7dc44
ms.service: role-based-access-control
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/19/2018
ms.author: rolyon
ms.reviewer: bagovind
ms.custom: seohack1
ms.openlocfilehash: 557d3330ef155181c050a18b14d31b65ba1f2dcf
ms.sourcegitcommit: 1438b7549c2d9bc2ace6a0a3e460ad4206bad423
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/20/2018
ms.locfileid: "36295388"
---
# <a name="troubleshooting-rbac-in-azure"></a>Řešení potíží s RBAC v Azure

Tento článek obsahuje odpovědi časté otázky týkající se řízení přístupu na základě role (RBAC), abyste věděli, co očekávat při použití role v portálu Azure a může poradce při potížích přístup. Tyto tři role popisuje všechny typy prostředků:

* Vlastník  
* Přispěvatel  
* Čtenář  

Vlastníci a přispěvatelé mají plný přístup k prostředí pro správu, ale Přispěvatel nelze udělit přístup k jiné uživatele nebo skupiny. Věcí získat něco zajímavějšího k roli čtečky tak, aby se, kde budete Věnujte nějaký čas. Informace o udělení přístupu, seee [Správa přístupu pomocí RBAC a webu Azure portal](role-assignments-portal.md).

## <a name="app-service"></a>App Service
### <a name="write-access-capabilities"></a>Možnosti přístup pro zápis
Když udělíte přístup jen pro čtení uživatelů do jedné webové aplikace, některé funkce jsou vypnuté, nemusí očekáváte. Následující funkce správy vyžadují **zápisu** přístup do webové aplikace (Přispěvatel nebo vlastníka) a nejsou k dispozici v žádném scénáři jen pro čtení.

* Příkazy (například spuštění, zastavení, atd.)
* Změna nastavení, jako je obecná konfigurace, nastavení škálování, nastavení zálohování a nastavení monitorování
* Přístup k pověření k publikování a jiné tajné klíče, jako je nastavení aplikace a připojovacích řetězců
* Protokoly streamování
* Konfigurace diagnostických protokolů
* Konzole (příkazového řádku)
* Aktivní a poslední nasazení (pro místní git průběžné nasazování)
* Předpokládaná útrata
* Webové testy
* Virtuální síť (viditelné pouze pro čtečku, pokud uživatel s přístup pro zápis byl dříve nakonfigurován virtuální sítě).

Pokud nemůžete použít žádnou z těchto dlaždicích, budete muset požádat správce pro přispěvatele přístup do webové aplikace.

### <a name="dealing-with-related-resources"></a>Práci s související informační zdroje
Webové aplikace jsou ztěžuje přítomnost několik různých prostředků, které vztahu. Tady je skupina prostředků typické se několik webů:

![Skupina prostředků webové aplikace](./media/troubleshooting/website-resource-model.png)

V důsledku toho, pokud někdo udělíte přístup do právě webové aplikace, mnoho funkcí v okně Web na portálu Azure je zakázán.

Tyto položky vyžadují **zápisu** přístup k **plán služby App Service** odpovídající na váš web:  

* Zobrazení webové aplikace je cenová úroveň (volné nebo Standard)  
* Škálování konfigurace (počet instancí, velikost virtuálního počítače, nastavení automatického škálování)  
* Kvóty (úložiště, šířku pásma, procesoru)  

Tyto položky vyžadují **zápisu** přístup k celé **skupiny prostředků** svůj web, který obsahuje:  

* Certifikáty SSL a vazeb (certifikáty SSL lze sdílet mezi lokalitami ve stejné skupině prostředků a geografického umístění)  
* Pravidla upozornění  
* nastavení automatického škálování  
* Komponenty Application Insights  
* Webové testy  

## <a name="azure-functions"></a>Azure Functions
Některé funkce [Azure Functions](../azure-functions/functions-overview.md) vyžadují přístup pro zápis. Například pokud uživatel je přiřadit role Čtenář, že nebudete moci zobrazit funkce v rámci funkce aplikace. Na portálu se zobrazí **(bez přístupu)**.

![Funkce aplikace bez přístupu](./media/troubleshooting/functionapps-noaccess.png)

Čtečka čipových karet můžete kliknout na tlačítko **funkce** a pak klikněte **všechna nastavení** zobrazíte některá nastavení související s aplikaci funkce (podobně jako webové aplikace), ale nemohou upravovat některé z těchto nastavení.

## <a name="virtual-machine"></a>Virtuální počítač
Podobně jako s webovými aplikacemi, některé funkce v okně virtuálního počítače vyžadují přístup pro zápis k virtuálnímu počítači nebo k jiným prostředkům ve skupině prostředků.

Virtuální počítače jsou související s názvy domény, virtuálních sítí, účty úložiště a pravidla výstrah.

Tyto položky vyžadují **zápisu** přístup k **virtuální počítač**:

* Koncové body  
* IP adresy  
* Disky  
* Rozšíření  

Vyžadují **zápisu** přístup do obou **virtuálního počítače**a **skupiny prostředků** (spolu s názvem domény) je to v:  

* Skupina dostupnosti  
* Skupině s vyrovnáváním zatížení  
* Pravidla upozornění  

Pokud nemůžete použít žádnou z těchto dlaždicích, požádejte správce pro přispěvatele přístup ke skupině prostředků.

## <a name="next-steps"></a>Další postup
* [Správa přístupu pomocí RBAC a portálu Azure](role-assignments-portal.md)
* [Zobrazit protokoly aktivity pro RBAC změny](change-history-report.md)

