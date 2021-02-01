---
title: Role Synapse RBAC
description: Tento článek popisuje předdefinované role RBAC synapse
author: billgib
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: security
ms.date: 12/1/2020
ms.author: billgib
ms.reviewer: jrasnick
ms.openlocfilehash: 1ffbb5579ea19d7d608dd9c9d600342cd89d371c
ms.sourcegitcommit: 2dd0932ba9925b6d8e3be34822cc389cade21b0d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/01/2021
ms.locfileid: "99225070"
---
# <a name="synapse-rbac-roles"></a>Synapse role RBAC

Článek popisuje předdefinované role RBAC synapse, oprávnění, která udělují, a obory, ve kterých se dají použít.  

## <a name="whats-changed-since-the-preview"></a>Co se od verze Preview změnilo?
Pro uživatele, kteří znají role synapse RBAC poskytované v rámci verze Preview, platí následující změny:
- Správce pracovního prostoru je přejmenován na **správce synapse**
- Apache Spark správce byl přejmenován na **Synapse Apache Spark správce** a má oprávnění k zobrazení všech publikovaných artefaktů kódu, včetně skriptů SQL.  Tato role již neposkytuje oprávnění k použití MSI pracovního prostoru, které vyžaduje roli uživatele přihlašovací údaje synapse.  Toto oprávnění je nutné ke spuštění kanálů. 
- Správce SQL se přejmenoval na **synapse Administrator** a má oprávnění k zobrazení všech publikovaných artefaktů kódu, včetně notebooků Spark a úloh.  Tato role již neposkytuje oprávnění k použití MSI pracovního prostoru, které vyžaduje roli uživatele přihlašovací údaje synapse. Toto oprávnění je nutné ke spuštění kanálů.
- Zavedly se **nové jemnější role synapse RBAC** , které se zaměřují na podporu vývoje a provozu osoby spíše než konkrétní analytické moduly.  
- **Nové obory nižší úrovně** jsou představené pro několik rolí.  Tyto obory umožňují omezit role na konkrétní prostředky nebo objekty.

>[!Note]
>**Nové role RBAC synapse a rozsahy nižší úrovně jsou momentálně ve verzi Preview**.  Doporučujeme používat tyto nové role a obory, které jsou plně podporované, a poskytovat zpětnou vazbu k jejich použití.

## <a name="built-in-synapse-rbac-roles-and-scopes"></a>Předdefinované role a obory synapse RBAC

Následující tabulka popisuje předdefinované role a obory, ve kterých se dají použít.

>[!Note]
> Uživatelé s rolí synapse RBAC v jakémkoli oboru mají automaticky roli uživatele synapse v oboru pracovního prostoru. 

|Role |Oprávnění|Obory|
|---|---|-----|
|Správce synapse  |Úplný synapse přístup k fondům SQL bez serveru, fondům Apache Spark a modulům Integration runtime.  Zahrnuje přístup pro vytváření, čtení, aktualizace a odstraňování všech publikovaných artefaktů kódu.  Zahrnuje výpočetní operátor, propojená Data Manager a uživatelská oprávnění přihlašovacích údajů pro přihlašovací údaje identity systému pracovního prostoru.  Zahrnuje přiřazování rolí synapse RBAC. Kromě správce synapse můžou vlastníci Azure také přiřadit role synapse RBAC. K vytváření, odstraňování a správě výpočetních prostředků se vyžadují oprávnění Azure. </br></br>_Může číst a zapisovat artefakty </br> mohou provádět všechny akce pro aktivity Spark. </br> Zobrazení protokolů fondů Spark může </br> Zobrazit uložený Poznámkový blok a výstup kanálu můžou </br> použít tajné klíče uložené pomocí propojených služeb nebo přihlašovací údaje </br> se můžou připojit k koncovým bodům bez SQL serveru pomocí SQL `db_datareader` , `db_datawriter` , `connect` a `grant` oprávnění </br> může přiřazovat a odvolávat synapse role RBAC v aktuálním oboru._|Pracovní prostor </br> Fond Spark<br/>Prostředí Integration Runtime </br>Propojená služba</br>Přihlašovací údaj |
|Správce Apache Spark synapse</br>|Úplný přístup Synapse k fondům Apache Spark.  Vytváření, čtení, aktualizace a odstraňování přístupu k publikovaným definicím úloh Spark, poznámkovým blokům a jejich výstupům a ke knihovnám, propojeným službám a přihlašovacím údajům.  Zahrnuje přístup pro čtení všech ostatních publikovaných artefaktů kódu. Nezahrnuje oprávnění k používání přihlašovacích údajů a spouštění kanálů. Nezahrnuje udělení přístupu. </br></br>_Může provádět všechny akce u artefaktů Sparku, které </br> můžou provádět všechny akce v aktivitách Sparku._|Pracovní prostor</br>Fond Spark|
|Správce synapse SQL|Úplný přístup Synapse k fondům SQL bez serveru.  Vytváření, čtení, aktualizace a odstraňování přístupu k publikovaným skriptům SQL, přihlašovacím údajům a propojeným službám.  Zahrnuje přístup pro čtení všech ostatních publikovaných artefaktů kódu.  Nezahrnuje oprávnění k používání přihlašovacích údajů a spouštění kanálů. Nezahrnuje udělení přístupu. </br></br>*Může provádět všechny akce s skripty SQL <br/> , které se můžou připojit k koncovým bodům bez SQL serveru pomocí SQL `db_datareader` ,, `db_datawriter` `connect` a `grant` oprávnění.*|Pracovní prostor|
|Přispěvatel synapse|Úplný přístup Synapse k fondům SQL bez serveru, fondům Apache Spark prostředí Integration runtime.  Zahrnuje přístup k vytváření, čtení, aktualizaci a odstraňování všech publikovaných artefaktů kódu a jejich výstupů, včetně přihlašovacích údajů a propojených služeb.  Zahrnuje oprávnění operátora Compute. Nezahrnuje oprávnění k používání přihlašovacích údajů a spouštění kanálů. Nezahrnuje udělení přístupu. </br></br>_Může číst a zapisovat artefakty, které mohou </br> Zobrazit uložený Poznámkový blok a výstup kanálu, může zobrazit </br> všechny akce v aktivitách Spark, které </br> mohou zobrazovat protokoly fondů Spark_|Pracovní prostor </br> Fond Spark<br/> Prostředí Integration Runtime|
|Vydavatel artefaktů synapse|Vytváření, čtení, aktualizace a odstraňování přístupu k artefaktům publikovaných kódu a jejich výstupům. Nezahrnuje oprávnění ke spouštění kódu nebo kanálů nebo k udělení přístupu. </br></br>_Může číst publikované artefakty a publikovat artefakty </br> , které můžou zobrazovat uložený Poznámkový blok, úlohu Sparku a výstup kanálu._|Pracovní prostor
|Uživatel artefaktu synapse|Přístup pro čtení k artefaktům publikovaných kódu a jejich výstupům. Může vytvořit nové artefakty, ale nemůže publikovat změny nebo spustit kód bez dalších oprávnění.|Pracovní prostor
|Operátor COMPUTE synapse |Odešlete úlohy Sparku a poznámkové bloky a zobrazíte protokoly.  Zahrnuje zrušení úloh Sparku odeslaných libovolným uživatelem. Pro spouštění kanálů vyžaduje další oprávnění použít přihlašovací údaje pro identitu systému pracovního prostoru, zobrazení spuštění kanálu a výstupy. </br></br>_Může odesílat a rušit úlohy, včetně úloh odesílaných ostatními, </br> může zobrazit protokoly fondů Spark._|Pracovní prostor</br>Fond Spark</br>Prostředí Integration Runtime|
|Uživatel s přihlašovacími údaji synapse|Doba běhu a používání tajných kódů v rámci přihlašovacích údajů a propojených služeb v aktivitách, jako je spuštění kanálu. Aby bylo možné spustit kanály, je tato role povinná a vymezená na identitu systému pracovního prostoru. </br></br>_V oboru pro přihlašovací údaje povoluje přístup k datům přes propojenou službu chráněnou přihlašovacími údaji (vyžaduje se taky oprávnění k použití COMPUTE), které </br> umožňuje provádění kanálů chráněných přihlašovacími údaji identity systému pracovního prostoru (s dodatečným oprávněním k použití COMPUTE)._|Pracovní prostor </br>Propojená služba</br>Přihlašovací údaj
|Propojený Data Manager synapse|Vytváření a Správa spravovaných privátních koncových bodů, propojených služeb a přihlašovacích údajů. Může vytvářet spravované privátní koncové body, které používají propojené služby chráněné přihlašovacími údaji.|Pracovní prostor|
|Uživatel synapse|Vypíše a zobrazí podrobnosti fondů SQL, Apache Spark fondů, prostředí Integration runtime a publikovaných propojených služeb a přihlašovacích údajů. Neobsahuje další publikované artefakty kódu.  Může vytvořit nové artefakty, ale nemůže spustit nebo publikovat bez dalších oprávnění.</br></br>_Může vypisovat a číst fondy Spark, prostředí Integration runtime._|Pracovní prostor, fond Spark</br>Propojená služba </br>Přihlašovací údaj|

## <a name="synapse-rbac-roles-and-the-actions-they-permit"></a>Synapse role RBAC a akce, které povolují

>[!Note]
>- Všechny akce uvedené v následujících tabulkách jsou předponou "Microsoft. synapse/...".</br>
>- Všechny akce čtení, zápisu a odstranění artefaktů jsou v souladu s publikovanými artefakty v živé službě.  Tato oprávnění neovlivňují přístup k artefaktům v připojeném úložišti Git.  

V následující tabulce jsou uvedeny předdefinované role a akce/oprávnění, které každá z nich podporuje.

Role|Akce
--|--
Správce synapse|pracovní prostory/čtení</br>pracovní prostory/roleAssignments/Write, DELETE</br>pracovní prostory/managedPrivateEndpoint/Write, DELETE</br>pracovní prostory/bigDataPools/useCompute/Action</br>pracovní prostory/bigDataPools/viewLogs/Action</br>pracovní prostory/integrationRuntimes/useCompute/Action</br>pracovní prostory/artefakty/čtení</br>pracovní prostory/notebooky/zápis, odstranění</br>pracovní prostory/sparkJobDefinitions/Write, DELETE</br>pracovní prostory/sqlScripts/Write, DELETE</br>pracovní prostory/tok dat/zápis, odstranění</br>pracovní prostory/kanály/zapisovat, odstranit</br>pracovní prostory/triggery/zapisovat, odstranit</br>pracovní prostory/datové sady/zapisovat, odstranit</br>pracovní prostory/knihovny/zápis, odstranění</br>pracovní prostory/linkedServices/Write, DELETE</br>pracovní prostory/přihlašovací údaje/zapsat, odstranit</br>pracovní prostory/poznámkové bloky/viewOutputs/Action</br>pracovní prostory/kanály/viewOutputs/Action</br>pracovní prostory/linkedServices/useSecret/Action</br>pracovní prostory/přihlašovací údaje/useSecret/akce|
|Správce Apache Spark synapse|pracovní prostory/čtení</br>pracovní prostory/bigDataPools/useCompute/Action</br>pracovní prostory/bigDataPools/viewLogs/Action</br>pracovní prostory/poznámkové bloky/viewOutputs/Action</br>pracovní prostory/artefakty/čtení</br>pracovní prostory/notebooky/zápis, odstranění</br>pracovní prostory/sparkJobDefinitions/Write, DELETE</br>pracovní prostory/knihovny/zápis, odstranění</br>pracovní prostory/linkedServices/Write, DELETE</br>pracovní prostory/přihlašovací údaje/zapsat, odstranit|
|Správce synapse SQL|pracovní prostory/čtení</br>pracovní prostory/artefakty/čtení</br>pracovní prostory/sqlScripts/Write, DELETE</br>pracovní prostory/linkedServices/Write, DELETE</br>pracovní prostory/přihlašovací údaje/zapsat, odstranit|
|Přispěvatel synapse|pracovní prostory/čtení</br>pracovní prostory/bigDataPools/useCompute/Action</br>pracovní prostory/bigDataPools/viewLogs/Action</br>pracovní prostory/integrationRuntimes/useCompute/Action</br>pracovní prostory/integrationRuntimes/viewLogs/Action</br>pracovní prostory/artefakty/čtení</br>pracovní prostory/notebooky/zápis, odstranění</br>pracovní prostory/sparkJobDefinitions/Write, DELETE</br>pracovní prostory/sqlScripts/Write, DELETE</br>pracovní prostory/tok dat/zápis, odstranění</br>pracovní prostory/kanály/zapisovat, odstranit</br>pracovní prostory/triggery/zapisovat, odstranit</br>pracovní prostory/datové sady/zapisovat, odstranit</br>pracovní prostory/knihovny/zápis, odstranění</br>pracovní prostory/linkedServices/Write, DELETE</br>pracovní prostory/přihlašovací údaje/zapsat, odstranit</br>pracovní prostory/poznámkové bloky/viewOutputs/Action</br>pracovní prostory/kanály/viewOutputs/Action|
|Vydavatel artefaktů synapse|pracovní prostory/čtení</br>pracovní prostory/artefakty/čtení</br>pracovní prostory/notebooky/zápis, odstranění</br>pracovní prostory/sparkJobDefinitions/Write, DELETE</br>pracovní prostory/sqlScripts/Write, DELETE</br>pracovní prostory/tok dat/zápis, odstranění</br>pracovní prostory/kanály/zapisovat, odstranit</br>pracovní prostory/triggery/zapisovat, odstranit</br>pracovní prostory/datové sady/zapisovat, odstranit</br>pracovní prostory/knihovny/zápis, odstranění</br>pracovní prostory/linkedServices/Write, DELETE</br>pracovní prostory/přihlašovací údaje/zapsat, odstranit</br>pracovní prostory/poznámkové bloky/viewOutputs/Action</br>pracovní prostory/kanály/viewOutputs/Action|
|Uživatel artefaktu synapse|pracovní prostory/čtení</br>pracovní prostory/artefakty/čtení</br>pracovní prostory/poznámkové bloky/viewOutputs/Action</br>pracovní prostory/kanály/viewOutputs/Action|
|Operátor COMPUTE synapse |pracovní prostory/čtení</br>pracovní prostory/bigDataPools/useCompute/Action</br>pracovní prostory/bigDataPools/viewLogs/Action</br>pracovní prostory/integrationRuntimes/useCompute/Action</br>pracovní prostory/integrationRuntimes/viewLogs/Action|
|Uživatel s přihlašovacími údaji synapse|pracovní prostory/čtení</br>pracovní prostory/linkedServices/useSecret/Action</br>pracovní prostory/přihlašovací údaje/useSecret/akce|
|Propojený Data Manager synapse|pracovní prostory/čtení</br>pracovní prostory/managedPrivateEndpoint/Write, DELETE</br>pracovní prostory/linkedServices/Write, DELETE</br>pracovní prostory/přihlašovací údaje/zapsat, odstranit|
|Uživatel synapse|pracovní prostory/čtení|

## <a name="synapse-rbac-actions-and-the-roles-that-permit-them"></a>Synapse akce RBAC a role, které je povolují

V následující tabulce jsou uvedeny akce synapse a předdefinované role, které povolují tyto akce:

Akce|Role
--|--
pracovní prostory/čtení|Správce synapse</br>Správce Apache Spark synapse</br>Správce synapse SQL</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse</br>Uživatel artefaktu synapse</br>Operátor COMPUTE synapse </br>Uživatel s přihlašovacími údaji synapse</br>Propojený Data Manager synapse</br>Uživatel synapse 
pracovní prostory/roleAssignments/Write, DELETE|Správce synapse
pracovní prostory/managedPrivateEndpoint/Write, DELETE|Správce synapse</br>Propojený Data Manager synapse
pracovní prostory/bigDataPools/useCompute/Action|Správce synapse</br>Správce Apache Spark synapse</br>Přispěvatel synapse</br>Operátor COMPUTE synapse 
pracovní prostory/bigDataPools/viewLogs/Action|Správce synapse</br>Správce Apache Spark synapse</br>Přispěvatel synapse</br>Operátor COMPUTE synapse 
pracovní prostory/integrationRuntimes/useCompute/Action|Správce synapse</br>Přispěvatel synapse</br>Operátor COMPUTE synapse 
pracovní prostory/artefakty/čtení|Správce synapse</br>Správce Apache Spark synapse</br>Správce synapse SQL</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse</br>Uživatel artefaktu synapse
pracovní prostory/notebooky/zápis, odstranění|Správce synapse</br>Správce Apache Spark synapse</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse
pracovní prostory/sparkJobDefinitions/Write, DELETE|Správce synapse</br>Správce Apache Spark synapse</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse
pracovní prostory/sqlScripts/Write, DELETE|Správce synapse</br>Správce synapse SQL</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse
pracovní prostory/tok dat/zápis, odstranění|Správce synapse</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse
pracovní prostory/kanály/zapisovat, odstranit|Správce synapse</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse
pracovní prostory/triggery/zapisovat, odstranit|Správce synapse</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse
pracovní prostory/datové sady/zapisovat, odstranit|Správce synapse</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse
pracovní prostory/knihovny/zápis, odstranění|Správce synapse</br>Správce Apache Spark synapse</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse
pracovní prostory/linkedServices/Write, DELETE|Správce synapse</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse</br>Propojený Data Manager synapse
pracovní prostory/přihlašovací údaje/zapsat, odstranit|Správce synapse</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse</br>Propojený Data Manager synapse
pracovní prostory/poznámkové bloky/viewOutputs/Action|Správce synapse</br>Správce Apache Spark synapse</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse</br>Uživatel artefaktu synapse
pracovní prostory/kanály/viewOutputs/Action|Správce synapse</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse</br>Uživatel artefaktu synapse
pracovní prostory/linkedServices/useSecret/Action|Správce synapse</br>Uživatel s přihlašovacími údaji synapse
pracovní prostory/přihlašovací údaje/useSecret/akce|Správce synapse</br>Uživatel s přihlašovacími údaji synapse

## <a name="synapse-rbac-scopes-and-their-supported-roles"></a>Synapse rozsahy RBAC a jejich podporované role

Následující tabulka uvádí synapse rozsahy RBAC a role, které je možné přiřadit v jednotlivých oborech. 

>[!note]
>Chcete-li vytvořit nebo odstranit objekt, je nutné mít oprávnění v oboru vyšší úrovně.

Obor|Role
--|--
Pracovní prostor |Správce synapse</br>Správce Apache Spark synapse</br>Správce synapse SQL</br>Přispěvatel synapse</br>Vydavatel artefaktů synapse</br>Uživatel artefaktu synapse</br>Operátor COMPUTE synapse </br>Uživatel s přihlašovacími údaji synapse</br>Propojený Data Manager synapse</br>Uživatel synapse
Fond Apache Spark | Správce synapse </br>Přispěvatel synapse </br> Operátor COMPUTE synapse
Prostředí Integration Runtime | Správce synapse </br>Přispěvatel synapse </br> Operátor COMPUTE synapse
Propojená služba |Správce synapse </br>Uživatel s přihlašovacími údaji synapse
Přihlašovací údaj |Správce synapse </br>Uživatel s přihlašovacími údaji synapse
 
>[!note]
>Všechny role artefaktů a akce jsou vymezeny na úrovni pracovního prostoru. 

## <a name="next-steps"></a>Další kroky

Naučte [se zkontrolovat přiřazení rolí RBAC synapse](./how-to-review-synapse-rbac-role-assignments.md) pro pracovní prostor.

Naučte [se přiřazovat role synapse RBAC](./how-to-manage-synapse-rbac-role-assignments.md) .