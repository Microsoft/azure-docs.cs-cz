---
title: Přehled řízení přístupu k pracovnímu prostoru synapse
description: Tento článek popisuje mechanismy používané k řízení přístupu k pracovnímu prostoru synapse a artefaktům a artefaktům kódu, které obsahuje.
services: synapse-analytics
author: RonyMSFT
ms.service: synapse-analytics
ms.topic: overview
ms.subservice: security
ms.date: 12/03/2020
ms.author: ronytho
ms.reviewer: jrasnick
ms.openlocfilehash: 20614b1c397bdf24e807d48d3de33f0033da14bc
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/29/2021
ms.locfileid: "100105109"
---
# <a name="synapse-access-control"></a>Řízení přístupu synapse 

Tento článek poskytuje přehled mechanismů, které jsou k dispozici pro řízení přístupu k synapse výpočetním prostředkům a datům.  

## <a name="overview"></a>Přehled

Synapse poskytuje komplexní a jemně odstupňovaný systém řízení přístupu, který integruje: 
- **Role Azure** pro správu prostředků a přístup k datům v úložišti, 
- **Synapse role** pro správu živého přístupu k kódu a provádění 
- **Role SQL** pro přístup k rovině dat v fondech SQL a 
- **Oprávnění Gitu** pro správu zdrojového kódu, včetně podpory průběžné integrace a nasazení.  

Role synapse poskytují sady oprávnění, které je možné použít v různých oborech. Tato členitost usnadňuje udělení správného přístupu správcům, vývojářům, bezpečnostním pracovníkům a operátorům pro výpočetní prostředky a data.

Řízení přístupu se dá zjednodušit pomocí skupin zabezpečení, které jsou zarovnané na role úloh lidé. Pro správu přístupu stačí přidat a odebrat uživatele z příslušných skupin zabezpečení.

## <a name="access-control-elements"></a>Prvky řízení přístupu

### <a name="creating-and-managing-synapse-compute-resources"></a>Vytváření a Správa výpočetních prostředků synapse

Role Azure slouží k řízení správy: 
- Vyhrazené fondy SQL, 
- Fondy Apache Spark a 
- Prostředí Integration runtime. 

Pokud chcete *vytvořit* tyto prostředky, musíte být vlastníkem nebo přispěvatelem Azure ve skupině prostředků. Pokud je chcete po vytvoření *Spravovat* , musíte být vlastníkem nebo přispěvatelem Azure v buď skupině prostředků, nebo v jednotlivých prostředcích. 

### <a name="developing-and-executing-code-in-synapse"></a>Vývoj a spouštění kódu v synapse 

Synapse podporuje dva vývojové modely.

- **Synapse živý vývoj**. Vyvíjíte a ladíte kód v synapse studiu a pak ho **publikujete** pro uložení a spuštění.  Služba synapse je zdrojem pravdy pro úpravy kódu a provádění.  Po zavření synapse studia ztratíte veškerou nepublikovanou práci.  
- **Vývoj s povoleným Git**. Vyvíjíte a ladíte kód v synapse studiu a **potvrdíte** změny pracovní větve úložiště Git. Práce z jedné nebo více větví je integrována do větve pro spolupráci, odkud ji **publikujete** do služby. Úložiště Git je zdrojem pravdy pro úpravy kódu, zatímco služba je zdrojem pravdy pro provádění. Před ukončením synapse studia se musí změny potvrdit do úložiště Git nebo publikovat do služby. [Přečtěte si další informace](../cicd/continuous-integration-deployment.md) o používání synapse Analytics s Git.

V obou vývojových modelech může libovolný uživatel s přístupem k synapse studiu vytvářet artefakty kódu. K publikování artefaktů do služby ale potřebujete další oprávnění, přečtěte si publikované artefakty, potvrďte změny v Gitu, spusťte kód a získejte přístup k propojeným datům chráněným přihlašovacími údaji.

### <a name="synapse-roles"></a>Role synapse

Role synapse slouží k řízení přístupu ke službě synapse, která vám umožní: 
- Výpis publikovaných artefaktů kódu 
- Publikování artefaktů kódu, propojených služeb a definic pověření
- Spustit kód nebo kanály, které používají výpočetní prostředky synapse,
- Spustit kód nebo kanály, které přistupují k propojeným datům chráněným přihlašovacími údaji
- Zobrazit výstupy přidružené k artefaktům publikovaných kódu,
- Monitorovat stav výpočetních prostředků a zobrazovat běhové protokoly.

Role synapse se dají přiřadit v oboru pracovního prostoru nebo ve jemnějších oborech, abyste omezili oprávnění udělená konkrétním prostředkům synapse.

### <a name="git-permissions"></a>Oprávnění Gitu

Při použití vývoje s povoleným Git v režimu Git vaše oprávnění Git řídí, jestli můžete číst a potvrzovat změny artefaktů kódu, včetně propojených služeb a definic přihlašovacích údajů.   
   
### <a name="accessing-data-in-sql"></a>Přístup k datům v SQL

Při práci s vyhrazenými fondy SQL serverů bez serveru je přístup k rovině dat řízen pomocí oprávnění SQL. 

Autor pracovního prostoru je přiřazený jako správce služby Active Directory v pracovním prostoru. Po vytvoření může být tato role přiřazena jinému uživateli nebo skupině zabezpečení v Azure Portal.

**Fondy SQL serverů bez serveru**: správcům synapse jsou udělená `db_owner` `DBO` oprávnění pro neserverový fond SQL (předdefinované). Aby mohli správci synapse udělit přístup k fondům SQL bez serveru, musí spustit skripty SQL na každém fondu bez serveru.  

**Vyhrazené fondy SQL**: oprávnění správce služby Active Directory je uděleno autorovi pracovního prostoru a MSI pracovního prostoru.  Oprávnění pro přístup k vyhrazeným fondům SQL není jinak uděleno automaticky. Aby mohli jiní uživatelé nebo skupiny udělit přístup k vyhrazeným fondům SQL, musí správce služby Active Directory spustit skripty SQL pro každý vyhrazený fond SQL.

Příklady skriptů SQL pro udělení oprávnění SQL ve fondech SQL najdete v tématu [jak nastavit Access Control synapse](./how-to-set-up-access-control.md) .  

 ### <a name="accessing-system-managed-data-in-storage"></a>Přístup k datům spravovaným systémem v úložišti

Neserverové fondy SQL a Apache Spark tabulky ukládají svá data do kontejneru ADLS Gen2 přidruženého k pracovnímu prostoru. Uživatelem nainstalované knihovny Apache Spark jsou také spravovány ve stejném účtu úložiště. Aby bylo možné tyto případy použití povolit, musí být uživatelům a MSI pracovního **prostoru udělen přístup k tomuto** pracovnímu prostoru adls Gen2 kontejneru úložiště.  

## <a name="using-security-groups-as-a-best-practice"></a>Použití skupin zabezpečení jako osvědčený postup

Chcete-li zjednodušit správu řízení přístupu, můžete k přiřazení rolí jednotlivcům a skupinám použít skupiny zabezpečení. Skupiny zabezpečení je možné vytvořit pro zrcadlení osoby nebo funkcí úloh ve vaší organizaci, které potřebují přístup k synapse prostředkům nebo artefaktům.  Těmto skupinám zabezpečení na základě těchto práv se pak dá přiřadit jedna nebo víc rolí Azure, rolí synapse, oprávnění SQL nebo oprávnění Gitu. Díky dobře vybraným skupinám zabezpečení je snadné přiřadit uživatele požadovaná oprávnění tak, že je přidáte do příslušné skupiny zabezpečení. 

>[!Note]
>Pokud používáte skupiny zabezpečení ke správě přístupu, je Azure Active Directory, než se změny projeví, zavede další latence. 

## <a name="access-control-enforcement-in-synapse-studio"></a>Vynucování řízení přístupu v synapse studiu

Synapse Studio se bude chovat odlišně v závislosti na vašich oprávněních a na aktuálním režimu:
- **Synapse živý režim:** Synapse Studio vám zabrání v prohlížení publikovaného obsahu, publikování obsahu nebo provádění jiných akcí, pokud nemáte požadovaná oprávnění.  V některých případech budete zabráněno vytváření artefaktů kódu, které nemůžete použít nebo Uložit. 
- **Režim git:** Máte-li oprávnění Git, která umožňují potvrdit změny v aktuální větvi, bude akce potvrzení povolena i v případě, že nemáte oprávnění k publikování změn v živé službě.  

V některých případech je možné vytvářet artefakty kódu i bez oprávnění k publikování nebo potvrzení. To vám umožní spustit kód (s požadovanými oprávněními pro spuštění). [Přečtěte si další informace](./synapse-workspace-understand-what-role-you-need.md) o rolích potřebných pro běžné úlohy. 

Pokud je funkce v synapse studiu zakázaná, zobrazí se v popisu požadovaná oprávnění. Pomocí [Průvodce rolemi synapse RBAC](./synapse-workspace-synapse-rbac-roles.md#synapse-rbac-actions-and-the-roles-that-permit-them) můžete vyhledat, která role je potřeba k poskytnutí chybějícího oprávnění.


## <a name="next-steps"></a>Další kroky

- Další informace o [RBAC synapse](./synapse-workspace-synapse-rbac.md)
- Další informace o [rolích RBAC synapse](./synapse-workspace-synapse-rbac-roles.md)
- Naučte [se, jak nastavit řízení přístupu](./how-to-set-up-access-control.md) pro pracovní prostor synapse pomocí skupin zabezpečení.
- Přečtěte [si, jak zkontrolovat přiřazení rolí RBAC synapse](./how-to-review-synapse-rbac-role-assignments.md)
- Naučte [se spravovat přiřazení rolí RBAC synapse](./how-to-manage-synapse-rbac-role-assignments.md) .
