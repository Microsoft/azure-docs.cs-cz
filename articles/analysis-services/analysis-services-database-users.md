---
title: Správa databázových rolí a uživatelů v Azure Analysis Services | Microsoft Docs
description: Naučte se spravovat databázové role a uživatele na Analysis Servicesm serveru v Azure.
author: minewiskan
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 04/15/2020
ms.author: owend
ms.reviewer: minewiskan
ms.custom: references_regions
ms.openlocfilehash: 31910e92ba4d5cbb1f133eaff6880fafb809b772
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "99054089"
---
# <a name="manage-database-roles-and-users"></a>Správa databázových rolí a uživatelů

Na úrovni databáze modelu musí všichni uživatelé patřit do role. Role definují uživatele s konkrétními oprávněními pro modelovou databázi. Každý uživatel nebo skupina zabezpečení přidaná do role musí mít účet v tenantovi Azure AD ve stejném předplatném jako server. 

Způsob, jakým definujete role, se liší v závislosti na použitém nástroji, ale účinek je stejný.

Oprávnění role zahrnují:
*  **Správce** – uživatelé mají úplná oprávnění pro databázi. Databázové role s oprávněními správce se liší od správců serverů.
*  **Proces** – uživatelé se mohou k databázi připojit a provádět operace procesu a analyzovat data databáze modelu.
*  **Čtení** – uživatelé můžou použít klientskou aplikaci pro připojení k datům databáze modelu a k jejich analýze.

Při vytváření projektu s tabelárním modelem vytvoříte role a přidáte uživatele nebo skupiny k těmto rolím pomocí Správce rolí v aplikaci Visual Studio s Analysis Services projekty. Při nasazení na server použijte SQL Server Management Studio (SSMS), [Analysis Services rutiny prostředí PowerShell](/analysis-services/powershell/analysis-services-powershell-reference)nebo TMSL ( [Tabular model Scripting Language](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference) ) k přidání nebo odebrání rolí a členů uživatelů.

Při přidávání **skupiny zabezpečení** použijte `obj:groupid@tenantid` .

Při přidávání **instančního objektu** `app:appid@tenantid` .

## <a name="to-add-or-manage-roles-and-users-in-visual-studio"></a>Přidání nebo Správa rolí a uživatelů v aplikaci Visual Studio  
  
1.  V **Průzkumníku tabulkových modelů** klikněte pravým tlačítkem na **role**.  
  
2.  Ve **Správci rolí** klikněte na **Nový**.  
  
3.  Zadejte název role.  
  
     Ve výchozím nastavení je název výchozí role pro každou novou roli přírůstkově očíslovaný. Doporučuje se zadat název, který jednoznačně identifikuje typ člena, například finanční manažery nebo specialisty na lidské zdroje.  
  
4.  Vyberte jedno z následujících oprávnění:  
  
    |Oprávnění|Description|  
    |----------------|-----------------|  
    |**Žádný**|Členové nemohou číst ani upravovat schéma modelu a nemohou zadávat dotazy na data.|  
    |**Oprávnění**|Členové se můžou dotazovat na data (na základě filtrů řádků), ale nemůžou upravovat schéma modelu.|  
    |**Čtení a zpracování**|Členové mohou zadávat dotazy na data (na základě filtrů na úrovni řádků) a spouštět proces a zpracovávat všechny operace, ale nemohou upravovat schéma modelu.|  
    |**Proces**|Členové mohou spustit proces a zpracovat všechny operace. Nelze číst ani upravovat schéma modelu a nelze zadávat dotazy na data.|  
    |**Správce**|Členové mohou upravit schéma modelu a dotazovat se na všechna data.|   
  
5.  Pokud role, kterou vytváříte, má oprávnění ke čtení nebo čtení a zpracování, můžete přidat filtry řádků pomocí vzorce DAX. Klikněte na kartu **filtry řádků** a pak vyberte tabulku, klikněte na pole **filtru DAX** a pak zadejte vzorec DAX.
  
6.  Klikněte na **Členové**  >  **Přidat externí**.  
  
8.  V rámci **Přidat externí člen** zadejte uživatele nebo skupiny ve vašem TENANTOVI Azure AD podle e-mailové adresy. Po kliknutí na tlačítko OK a zavření správce rolí se role a členové role zobrazí v Průzkumníkovi tabulkových modelů. 
 
     ![Role a uživatelé v Průzkumníku tabulkových modelů](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Nasaďte na server Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Přidání nebo Správa rolí a uživatelů v SSMS

Chcete-li přidat role a uživatele do nasazené databáze modelů, musíte být připojeni k serveru jako správce serveru nebo již v roli databáze s oprávněními správce.

1. V části Object Exporer klikněte pravým tlačítkem na **role**  >  **Nová role**.

2. V nástroji **vytvořit roli** zadejte název a popis role.

3. Vyberte oprávnění.

   |Oprávnění|Description|  
   |----------------|-----------------|  
   |**Úplné řízení (správce)**|Členové můžou upravovat schéma modelu, proces a můžou se dotazovat na všechna data.| 
   |**Process database**|Členové mohou spustit proces a zpracovat všechny operace. Schéma modelu nelze upravovat a nelze zadávat dotazy na data.|  
   |**Oprávnění**|Členové se můžou dotazovat na data (na základě filtrů řádků), ale nemůžou upravovat schéma modelu.|  
  
4. Klikněte na **členství** a pak zadejte uživatele nebo skupinu do svého TENANTA Azure AD podle e-mailové adresy.

     ![Přidat uživatele](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Pokud má role, kterou vytváříte, oprávnění ke čtení, můžete přidat filtry řádků pomocí vzorce DAX. Klikněte na **filtry řádků**, vyberte tabulku a potom do pole **Filtr DAX** zadejte vzorec DAX. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Přidání rolí a uživatelů pomocí skriptu TMSL

Skript TMSL můžete spustit v okně XMLA v SSMS nebo pomocí PowerShellu. Použijte příkaz [CreateOrReplace](/analysis-services/tmsl/createorreplace-command-tmsl) a objekt [role](/analysis-services/tmsl/roles-object-tmsl) .

**Ukázkový skript TMSL**

V této ukázce je externí uživatel B2B a skupina přidaný do role analytika s oprávněním ke čtení pro databázi SalesBI. Externí uživatel i skupina musí být ve stejném tenantovi Azure AD.

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

## <a name="to-add-roles-and-users-by-using-powershell"></a>Přidání rolí a uživatelů pomocí prostředí PowerShell

Modul [SQLServer](/analysis-services/powershell/analysis-services-powershell-reference) poskytuje rutiny pro správu databáze specifické pro úlohu a rutinu pro obecné účely Invoke-ASCmd, která přijímá dotaz nebo skript TMSL (Tabular model Scripting Language). Následující rutiny se používají ke správě databázových rolí a uživatelů.
  
|Rutina|Popis|
|------------|-----------------| 
|[Add-RoleMember](/powershell/module/sqlserver/Add-RoleMember)|Přidejte člena do databázové role.| 
|[Remove-RoleMember](/powershell/module/sqlserver/remove-rolemember)|Odebere člena z databázové role.|   
|[Invoke – ASCmd](/powershell/module/sqlserver/invoke-ascmd)|Spusťte skript TMSL.|

## <a name="row-filters"></a>Filtry řádků  

Filtry řádků definují, na které řádky v tabulce mohou být dotazováni členové určité role. Filtry řádků jsou definovány pro každou tabulku v modelu pomocí vzorců DAX.  
  
Filtry řádků lze definovat pouze pro role s oprávněním pro čtení a čtení a zpracování. Ve výchozím nastavení platí, že pokud pro konkrétní tabulku není definován filtr řádků, mohou členové zadat dotaz na všechny řádky v tabulce, pokud není křížové filtrování použito z jiné tabulky.
  
 Filtry řádků vyžadují vzorec DAX, který se musí vyhodnotit na hodnotu TRUE nebo FALSE, aby bylo možné definovat řádky, na které mohou být dotazováni členové příslušné role. Nelze zadat dotaz na řádky, které nejsou součástí vzorce DAX. Například tabulka Zákazníci s následujícím výrazem filtry řádků, *= zákazníci [Země] = "USA"*, členové prodejní role mohou vidět pouze zákazníky v USA.  
  
Filtry řádků se použijí na zadané řádky a související řádky. Pokud má tabulka více relací, filtry aplikují zabezpečení na aktivní relaci. Filtry řádků se protínají s ostatními filers řádků definovanými pro související tabulky, například:  
  
|Tabulka|Výraz DAX|  
|-----------|--------------------|  
|Oblast|= Region [Země] = "USA"|  
|ProductCategory|= ProductCategory [název] = "jízdní kola"|  
|Transakce|= Transakcí [rok] = 2016|  
  
 Čistý efekt se může dotazovat na řádky dat, kde se zákazník nachází v USA, kategorie produktu je jízdní kola a rok je 2016. Uživatelé nemohou zadávat dotazy na transakce mimo USA, transakce, které nejsou jízdní kola, ani transakce, které nejsou v 2016, pokud nejsou členy jiné role, která uděluje tato oprávnění.
  
 K odepření přístupu ke všem řádkům celé tabulky můžete použít filtr, *= false ()*.

## <a name="next-steps"></a>Další kroky

  [Správa správců serverů](analysis-services-server-admins.md)   
  [Správa služby Azure Analysis Services pomocí PowerShellu](analysis-services-powershell.md)  
  [Referenční dokumentace jazyka TMSL (Tabular model Scripting Language)](/analysis-services/tmsl/tabular-model-scripting-language-tmsl-reference)
