---
title: Limity prostředků Azure SQL Database – spravovaná instance | Dokumentace Microsoftu
description: Tento článek obsahuje přehled limity prostředků Azure SQL Database pro spravované instance.
services: sql-database
ms.service: sql-database
ms.subservice: managed-instance
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: bonova
ms.author: bonova
ms.reviewer: carlrab, jovanpop
manager: craigg
ms.date: 10/02/2018
ms.openlocfilehash: f2b1a8e18917c1c045c715bd3424d0bbfc0cdc67
ms.sourcegitcommit: 3856c66eb17ef96dcf00880c746143213be3806a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/02/2018
ms.locfileid: "48045366"
---
# <a name="overview-azure-sql-database-managed-instance-resource-limits"></a>Přehled limity prostředků Azure SQL Database Managed Instance

Tento článek obsahuje základní informace o omezení prostředků Azure SQL Database Managed Instance a poskytuje informace o tom, jak vytvořit žádost o zvýšení počtu výchozí limity předplatného. 

> [!NOTE]
> Další omezení Managed Instance, netýkající se konkrétních samostatného předplatného najdete v části [nákupní model založený na virtuálních jádrech](sql-database-managed-instance.md#vcore-based-purchasing-model) a [úrovně služeb Managed Instance](sql-database-managed-instance.md#managed-instance-service-tiers).

## <a name="default-subscription-level-limits-per-region"></a>Výchozí omezení na úrovni předplatného na oblast

Nasazení spravované Instance aktuálně podporuje jenom pro následující typy předplatných:

- [Smlouva Enterprise (EA)](https://azure.microsoft.com/pricing/enterprise-agreement/)
- [Průběžné platby](https://azure.microsoft.com/offers/ms-azr-0003p/)
- [Poskytovatele cloudových služeb (CSP)](https://docs.microsoft.com/partner-center/csp-documents-and-learning-resources)

> [!NOTE]
> Toto omezení podpory pro pouze některé typy předplatných je dočasný.

Spravované instance má dvě výchozí limity předplatného úroveň každou oblast Azure. Různé typy předplatného mají různá místní omezení. Tyto limity můžete zvýšit tak, že vytvoříte žádost o speciální podporu na webu Azure Portal pro odběr s problémem typu **kvóty**:

- **Limitu podsítí**: maximální počet podsítí, ve které jsou nasazené spravované instance
- **Omezení počtu instancí**: maximální počet instancí na oblast

> [!IMPORTANT]
> Při plánování vašeho nasazení, vezměte v úvahu, že instance kritické obchodní (BC) (z důvodu přidání redundance) obecně využívá 4 x větší kapacitu než instance obecné účely (zásady skupiny). Ano, pro výpočty, 1 GP instance = 1 instance jednotky a 1 BC instance = 4 jednotky instance. Pro zjednodušení analýzy využití pro výchozí omezení, shrnutí instance jednotky ve všech podsítí v oblasti, ve které jsou nasazené spravované instance a porovnávat výsledky s limity instancí jednotky pro typ vašeho předplatného.

## <a name="default-limits-by-subscription-type"></a>Výchozí omezení podle typu předplatného

|Typ odběru| Maximální počet podsítí Managed Instance | Maximální počet instancí |Maximální počet GP managed instance *|Maximální počet BC managed instance *|
| :---| :--- | :--- |:--- |:--- |
|Průběžné platby|1 *|4 *|4 *|1 *|
|CSP |1 *|4 *|4 *|1 *|
|EA|3 **|12 **|12 **|3 **|

\* 1 BC nebo 4 GP instance v jedné podsíti můžete nasadit buď tak, aby celkový počet jednotek"instance" v oblasti nikdy nepřesáhne 4.

** Maximální počet instancí v rámci jedné úrovně služby platí v případě, že neexistují žádné instance v rámci jiné úrovně služby. V případě, že budete chtít míchat zásady skupiny a BC instance ve stejné podsíti, použijte jako referenci pro povolené kombinace v následující části. Jednoduché pravidlo celkový počet podsítí nemůže být delší než 3 a celkový počet jednotek instance nemůže být delší než 12.

## <a name="deployment-options-for-gp-and-bc-deployments-within-the-same-subnet"></a>Možnosti nasazení pro nasazení zásad skupiny a BC ve stejné podsíti

Následující příklady pokrývají nasazené služby se neprázdný podsítěmi a smíšené GP a BC úrovně služeb.

|Počet podsítí|podsíť 1|Podsíť 2|Podsíť 3|
|:---|:---|:---|:---|
|1|0 BC až 12 GP<br>1 BC až 8 GP<br>2 BC až 4 GP<br>3 BC|neuvedeno| neuvedeno|
|2|BC 0, až 4 GP|BC 0, až na 8 GP<br>BC 1, až 4 GP<br>2 BC|neuvedeno|
|2|1 BC|BC 0, až na 8 GP<br>BC 1, až 4 GP<br>2 BC|neuvedeno|
|2|2 BC|BC 0, až na 8 GP<br>BC 1, až 4 GP<br>2 BC|neuvedeno|
|3|BC 1, 0 GP|BC 1, 0 GP|BC 0, až 4 GP|
|3|1BC 0 GP|BC 0, až 4 GP|BC 1, 0 GP|
|3|BC 0, až 4 GP|BC 1, 0 GP|1BC 0 GP|

## <a name="obtaining-a-larger-quota-for-sql-managed-instance"></a>Získání vyšší kvóty pro spravovanou instanci SQL

Chcete-li zahájit proces získávání vyšší kvóty:

1. Otevřít **Nápověda a podpora**a klikněte na tlačítko **nová žádost o podporu**. 

   ![Nápověda a podpora](media/sql-database-managed-instance-resource-limits/help-and-support.png)
2. Na kartě základy pro novou žádost o podporu:
   - Pro **typ problému**vyberte **limity služby a předplatného (kvóty)**.
   - V části **Předplatné** vyberte své předplatné.
   - Pro **typ kvóty**vyberte **SQL Database Managed Instance**.
   - Pro **plán podpory**, vyberte plán podpory.

     ![Problém typ kvóty](media/sql-database-managed-instance-resource-limits/issue-type-quota.png)

3. Klikněte na **Další**.
4. Na kartě problém pro novou žádost o podporu:
   - Pro **závažnost**, vyberte úroveň závažnosti problému.
   - Pro **podrobnosti**, poskytují další informace o svém problému, včetně chybových zpráv.
   - Pro **nahrání souboru**, připojte soubor s dalšími informacemi (až 4 MB).

     ![Podrobnosti o problému](media/sql-database-managed-instance-resource-limits/problem-details.png)

     > [!IMPORTANT]
     > Neplatný požadavek by měl obsahovat:
     > - Oblasti, které předplatné limit musí být zvýšena
     > - Požadovaný počet instancí na úroveň služby v existující podsítě po kvótu zvýšit (je-li žádné existující podsítě potřeba rozšířit
     > - Požadované číslo nové podsítě a celkový počet instancí na úrovni služby v rámci nové podsítě (Pokud je potřeba nasadit spravované instance v nové podsítě).
5. Klikněte na **Další**.
6. Na kartě kontaktní informace pro novou žádost o podporu zadejte upřednostňovaný způsob kontaktu (e-mail nebo telefon) a kontaktní údaje.
7. Klikněte na možnost **Vytvořit**.

## <a name="next-steps"></a>Další postup

- Další informace o Managed Instance najdete v tématu [co je Managed Instance?](sql-database-managed-instance.md). 
- Informace o cenách najdete v tématu [ceny SQL Database Managed Instance](https://azure.microsoft.com/pricing/details/sql-database/managed/).
- Zjistěte, jak vytvořit první Managed Instance, najdete v článku [příručky rychlý start](sql-database-managed-instance-get-started.md).