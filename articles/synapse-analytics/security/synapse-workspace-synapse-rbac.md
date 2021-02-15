---
title: Řízení přístupu na základě role synapse
description: Článek, který vysvětluje řízení přístupu na základě rolí ve službě Azure synapse Analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 9e96d6decba679c7a4764a77f1e9720000faf78c
ms.sourcegitcommit: 126ee1e8e8f2cb5dc35465b23d23a4e3f747949c
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/10/2021
ms.locfileid: "100105143"
---
# <a name="what-is-synapse-role-based-access-control-rbac"></a>Co je synapse řízení přístupu na základě role (RBAC)?

Synapse RBAC rozšiřuje možnosti [Azure RBAC](../../role-based-access-control/overview.md) pro pracovní prostory synapse a jejich obsah. 

Azure RBAC se používá ke správě, kdo může vytvářet, aktualizovat nebo odstraňovat pracovní prostor synapse a jeho fondy SQL, fondy Apache Spark a prostředí Integration runtime.

Synapse RBAC se používá ke správě toho, kdo může:
- Publikování artefaktů kódu a výpisu nebo přístup k artefaktům publikovaných kódu 
- Spouštění kódu ve fondech Spark Apaches a prostředích Integration runtime,
- Přístup k propojeným (datovým) službám chráněným přihlašovacími údaji 
- Sledujte nebo zrušte provádění úloh, zkontrolujte výstup úlohy a protokoly spuštění.  

>[!Note]
>I když se synapse RBAC používá ke správě přístupu k publikovaným skriptům SQL, poskytuje jenom omezený přístup k řízení fondu _SQL bez serveru a nepoužívá se_ k řízení přístupu k vyhrazeným fondům SQL.  Přístup k fondům SQL je primárně řízen pomocí zabezpečení SQL.

## <a name="what-can-i-do-with-synapse-rbac"></a>Co můžu dělat s synapse RBAC?

Tady je několik příkladů toho, co můžete dělat s synapse RBAC:
  - Umožní uživateli publikovat změny provedené v Apache Spark poznámkových blocích a úlohách do živé služby.
  - Povolí uživateli spouštět a rušit poznámkové bloky a úlohy Sparku v určitém fondu Apache Spark.
  - Povolí uživateli používat konkrétní přihlašovací údaje, aby mohli spouštět kanály zabezpečené identitou systému pracovního prostoru a přistupovat k datům v propojených službách zabezpečených s přihlašovacími údaji. 
  - Umožňuje správci spravovat, monitorovat a rušit provádění úloh v konkrétních fondech Spark.    

## <a name="how-synapse-rbac-works"></a>Jak synapse RBAC funguje
Podobně jako u Azure RBAC synapse RBAC funguje tak, že vytvoří přiřazení rolí. Přiřazování rolí se skládá ze tří prvků: objektu zabezpečení, definice role a rozsahu.  

### <a name="security-principals"></a>Objekty zabezpečení

_Objekt zabezpečení_ je uživatel, skupina, instanční objekt nebo spravovaná identita.

### <a name="roles"></a>Role
 
_Role_ je kolekce oprávnění nebo akcí, které lze provádět u konkrétních typů prostředků nebo typů artefaktů.

Synapse poskytuje předdefinované role, které definují kolekce akcí, které odpovídají potřebám různých osoby:
- Správci můžou získat úplný přístup k vytvoření a konfiguraci pracovního prostoru. 
- Vývojáři můžou vytvářet, aktualizovat a ladit skripty SQL, notebooky, kanály a datové toky, ale nemůžou tento kód publikovat ani spouštět na produkčních výpočetních prostředcích a datech.
- Operátoři mohou monitorovat a spravovat stav systému, spouštění aplikací a prohlížet protokoly bez přístupu ke kódu nebo výstupům z provádění.
- Pracovníci zabezpečení můžou spravovat a konfigurovat koncové body bez přístupu ke kódu, výpočetním prostředkům nebo datům.

[Přečtěte si další informace](./synapse-workspace-synapse-rbac-roles.md) o předdefinovaných rolích synapse. 

### <a name="scopes"></a>Obory

_Obor_ definuje prostředky nebo artefakty, na které se vztahuje přístup.  Synapse podporuje hierarchické obory.  Oprávnění udělená v oboru vyšší úrovně jsou děděna objekty na nižší úrovni.  V synapse RBAC je rozsah nejvyšší úrovně pracovním prostorem.  Přiřazení role s oborem pracovního prostoru uděluje oprávnění všem použitelným objektům v pracovním prostoru.  

Aktuální podporované obory v pracovním prostoru: fond Apache Spark, modul runtime integrace, propojená služba a přihlašovací údaje. 

Přístup k artefaktům kódu je udělen s oborem pracovního prostoru.  Udělení přístupu ke kolekcím artefaktů v rámci pracovního prostoru bude podporováno v pozdější verzi.

## <a name="resolving-role-assignments-to-determine-permissions"></a>Řešení přiřazení rolí k určení oprávnění

Přiřazení role uděluje objektu zabezpečení oprávnění definovaná rolí v zadaném oboru.

Synapse RBAC je aditivní model, jako je třeba Azure RBAC. K jednomu objektu zabezpečení a v různých oborech lze přiřadit více rolí. Při výpočtu oprávnění objektu zabezpečení, systém bere v úvahu všechny role přiřazené objektu zabezpečení a skupiny, které přímo nebo nepřímo zahrnují objekt zabezpečení.  Při určování oprávnění, která platí, bere v úvahu také rozsah jednotlivých přiřazení.  

## <a name="enforcing-assigned-permissions"></a>Vynucování přiřazených oprávnění

V nástroji synapse Studio může být konkrétní tlačítko nebo možnosti šedé nebo při pokusu o akci může být vrácena chyba oprávnění, pokud nemáte požadovaná oprávnění. 

Pokud je tlačítko nebo možnost zakázaná, ukazatel myši na tlačítko nebo možnost zobrazí popisek s požadovaným oprávněním.  Obraťte se na správce synapse a přiřaďte roli, která uděluje požadovaná oprávnění. Role, které poskytují konkrétní akce, můžete zobrazit [zde](./synapse-workspace-synapse-rbac-roles.md).

## <a name="who-can-assign-synapse-rbac-roles"></a>Kdo může přiřazovat role synapse RBAC?

Synapse role RBAC může přiřadit pouze správce synapse.  Správce synapse na úrovni pracovního prostoru může udělit přístup v jakémkoli oboru.  Správce synapse v oboru nižší úrovně může udělit přístup jenom v tomto oboru. 

Když se vytvoří nový pracovní prostor, tvůrce se automaticky dostanou role správce synapse v oboru pracovního prostoru.   

## <a name="where-do-i-manage-synapse-rbac"></a>Kde můžu spravovat synapse RBAC?

Synapse RBAC se v rámci synapse studia spravuje pomocí nástrojů pro řízení přístupu v centru pro správu. 

## <a name="next-steps"></a>Další kroky

Pochopte předdefinované [role RBAC synapse](./synapse-workspace-synapse-rbac-roles.md).

Naučte [se zkontrolovat přiřazení rolí RBAC synapse](./how-to-review-synapse-rbac-role-assignments.md) pro pracovní prostor.

Naučte [se přiřazovat role synapse RBAC](./how-to-manage-synapse-rbac-role-assignments.md) .