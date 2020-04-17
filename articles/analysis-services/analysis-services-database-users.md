---
title: Správa databázových rolí a uživatelů ve službě Azure Analysis Services | Dokumenty společnosti Microsoft
description: Zjistěte, jak spravovat databázové role a uživatele na serveru Analysis Services v Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: b7e3cc2b9d35eafcb875efa167821a8e9ad80146
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2020
ms.locfileid: "81454199"
---
# <a name="manage-database-roles-and-users"></a>Správa databázových rolí a uživatelů

Na úrovni databáze modelu musí všichni uživatelé patřit do role. Role definují uživatele s konkrétními oprávněními pro databázi modelu. Každý uživatel nebo skupina zabezpečení přidaná do role musí mít účet v tenantovi Azure AD ve stejném předplatném jako server. 

Způsob definování rolí se liší v závislosti na nástroji, který používáte, ale efekt je stejný.

Oprávnění role zahrnují:
*  **Správce** – uživatelé mají úplná oprávnění k databázi. Role databáze s oprávněními správce se liší od úloh správců serveru.
*  **Proces** – uživatelé se mohou připojit k databázi a provádět je a provádět je a analyzovat data databáze modelu.
*  **Čtení** – uživatelé mohou používat klientskou aplikaci pro připojení a analýzu dat databáze modelu.

Při vytváření projektu tabulkového modelu vytvoříte role a přidáte uživatele nebo skupiny do těchto rolí pomocí Správce rolí v sadě Visual Studio s projekty analysis services. Při nasazení na server můžete přidat nebo odebrat role a členy uživatele pomocí sql server management studio (SSMS), [rutiny Prostředí PowerShell služby Analysis Services](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference)nebo [jazyk tabulkového skriptování modelu](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) (TMSL).

Při přidávání **skupiny zabezpečení**použijte `obj:groupid@tenantid`.

## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Přidání nebo správa rolí a uživatelů v sadě Visual Studio  
  
1.  V **Průzkumníku tabulkových modelů**klepněte pravým tlačítkem myši na **položku Role**.  
  
2.  Ve **Správci rolí**klepněte na tlačítko **Nový**.  
  
3.  Zadejte název role.  
  
     Ve výchozím nastavení je název výchozí role postupně očíslovaný pro každou novou roli. Doporučujeme zadat název, který jasně identifikuje typ člena, například finanční manažeři nebo specialisté na lidské zdroje.  
  
4.  Vyberte jedno z následujících oprávnění:  
  
    |Oprávnění|Popis|  
    |----------------|-----------------|  
    |**Žádné**|Členové nemohou číst nebo upravovat schéma modelu a nemohou zadávat dotazy na data.|  
    |**Čtení**|Členové mohou dotazovat data (na základě filtrů řádků), ale nemohou změnit schéma modelu.|  
    |**Čtení a zpracování**|Členové mohou dotazovat data (na základě filtrů na úrovni řádků) a spustit operace Proces a Všechny, ale nemohou změnit schéma modelu.|  
    |**Proces**|Členové mohou spustit proces a zpracovat všechny operace. Schéma modelu nelze číst ani upravovat a nelze zadávat dotazy na data.|  
    |**Správce**|Členové mohou upravit schéma modelu a dotazovat se na všechna data.|   
  
5.  Pokud má vytvářená role oprávnění Číst nebo Číst a zpracovat, můžete přidat filtry řádků pomocí vzorce jazyka DAX. Klikněte na kartu **Filtry řádků,** vyberte tabulku, potom klikněte na pole **Filtr jazyka DAX** a zadejte vzorec jazyka DAX.
  
6.  Klikněte na **Členové** > **přidat externí**.  
  
8.  Do **části Přidat externího člena**zadejte uživatele nebo skupiny do služby Azure AD klienta podle e-mailové adresy. Po klepnutí na tlačítko OK a zavření Správce rolí se role a členové rolí zobrazí v Průzkumníku tabulkových modelů. 
 
     ![Role a uživatelé v Průzkumníku tabulkových modelů](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Nasaďte na server Služby Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Přidání nebo správa rolí a uživatelů v SSMS

Chcete-li přidat role a uživatele do nasazené databáze modelu, musíte být připojeni k serveru jako správce serveru nebo již v databázové roli s oprávněními správce.

1. V objektu Exporer klikněte pravým tlačítkem myši na **Role** > **Nová role**.

2. V **části Vytvořit roli**zadejte název a popis role.

3. Vyberte oprávnění.

   |Oprávnění|Popis|  
   |----------------|-----------------|  
   |**Úplné řízení (správce)**|Členové mohou upravit schéma modelu, proces a může dotazovat všechna data.| 
   |**Process database**|Členové mohou spustit proces a zpracovat všechny operace. Schéma modelu nelze změnit a nelze zadat dotaz na data.|  
   |**Čtení**|Členové mohou dotazovat data (na základě filtrů řádků), ale nemohou změnit schéma modelu.|  
  
4. Klikněte na **Členství**a zadejte uživatele nebo skupinu do služby Azure AD klienta podle e-mailové adresy.

     ![Přidání uživatele](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Pokud má vytvářená role oprávnění ke čtení, můžete přidat filtry řádků pomocí vzorce jazyka DAX. Klikněte na **Filtry řádků**, vyberte tabulku a do pole **Filtr jazyka DAX** zadejte vzorec jazyka DAX. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Přidání rolí a uživatelů pomocí skriptu TMSL

Skript TMSL můžete spustit v okně XMLA v ssms nebo pomocí prostředí PowerShell. Použijte příkaz [CreateOrReplace](https://docs.microsoft.com/analysis-services/tmsl/createorreplace-command-tmsl) a objekt [Roles.](https://docs.microsoft.com/analysis-services/tmsl/roles-object-tmsl)

**Ukázkový skript TMSL**

V této ukázce b2B externí uživatel a skupina jsou přidány do role Analytik s oprávněními ke čtení pro salesbi databáze. Externí uživatel i skupina musí být ve stejném tenantovi Azure AD.

```
{
  "createOrReplace": {
    "object": {
      "database": "SalesBI",
      "role": "Analyst"
    },
    "role": {
      "name": "Users",
      "description": "All allowed users to query the model",
      "modelPermission": "read",
      "members": [
        {
          "memberName": "user1@contoso.com",
          "identityProvider": "AzureAD"
        },
        {
          "memberName": "group1@adventureworks.com",
          "identityProvider": "AzureAD"
        }
      ]
    }
  }
}
```

## <a name="to-add-roles-and-users-by-using-powershell"></a>Přidání rolí a uživatelů pomocí PowerShellu

Modul [SqlServer](https://docs.microsoft.com/analysis-services/powershell/analysis-services-powershell-reference) poskytuje rutiny správy databáze specifické pro úlohy a rutinu pro obecné účely Invoke-ASCmd, která přijímá dotaz nebo skript tabulkového jazyka skriptování modelu (TMSL). Následující rutiny se používají pro správu databázových rolí a uživatelů.
  
|Rutina|Popis|
|------------|-----------------| 
|[Člen role doplňku](https://docs.microsoft.com/powershell/module/sqlserver/Add-RoleMember)|Přidejte člena do role databáze.| 
|[Odebrat člen role](https://docs.microsoft.com/powershell/module/sqlserver/remove-rolemember)|Odeberte člena z databázové role.|   
|[Vyvolat-ASCmd](https://docs.microsoft.com/powershell/module/sqlserver/invoke-ascmd)|Spusťte skript TMSL.|

## <a name="row-filters"></a>Filtry řádků  

Filtry řádků definují, které řádky v tabulce mohou být dotazovány členy určité role. Filtry řádků jsou definovány pro každou tabulku v modelu pomocí vzorců jazyka DAX.  
  
Filtry řádků lze definovat pouze pro role s oprávněními Číst a číst a zpracovat. Ve výchozím nastavení, pokud filtr řádků není definován pro určitou tabulku, mohou členové dotazovat všechny řádky v tabulce, pokud křížové filtrování neplatí z jiné tabulky.
  
 Filtry řádků vyžadují vzorec jazyka DAX, který musí být vyhodnocen na hodnotu PRAVDA/NEPRAVDA, aby bylo možné definovat řádky, na které mohou být dotazováni členové dané konkrétní role. Řádky, které nejsou zahrnuty ve vzorci jazyka DAX, nelze dotazovat. Například tabulka Zákazníci s následujícím výrazem filtrů řádků= *Zákazníci [Země] = "USA"*, členové role Prodej mohou zobrazit pouze zákazníky v USA.  
  
Filtry řádků platí pro zadané řádky a související řádky. Pokud má tabulka více relací, filtry aplikují zabezpečení na aktivní relaci. Filtry řádků jsou protnuty s ostatními filery řádků definovanými pro související tabulky, například:  
  
|Table|Výraz JAZYKA DAX|  
|-----------|--------------------|  
|Region (Oblast)|=Region[Země]="USA"|  
|ProductCategory|=ProductCategory[Název]="Jízdní kola"|  
|Transakce|=Transakce[Rok]=2016|  
  
 Čistým efektem je, že členové mohou dotazovat řádky dat, kde se zákazník nachází v USA, kategorie produktu jsou jízdní kola a rok je 2016. Uživatelé nemohou dotazovat transakce mimo USA, transakce, které nejsou jízdníkola nebo transakce nejsou v roce 2016, pokud nejsou členem jiné role, která uděluje tato oprávnění.
  
 Pomocí filtru *=FALSE()* můžete odepřít přístup ke všem řádkům pro celou tabulku.

## <a name="next-steps"></a>Další kroky

  [Správa správců serveru](analysis-services-server-admins.md)   
  [Správa služby Azure Analysis Services pomocí PowerShellu](analysis-services-powershell.md)  
  [Referenční příručka pro skriptovací jazyk tabulkového modelu (TMSL)](https://docs.microsoft.com/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)

