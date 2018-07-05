---
title: Správa databázových rolí a uživatelů ve službě Azure Analysis Services | Dokumentace Microsoftu
description: Další informace o správě databázových rolí a uživatelů na serveru služby Analysis Services v Azure.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 8c777d5376614f7afe59342dc5a9fbfa37ca4556
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441052"
---
# <a name="manage-database-roles-and-users"></a>Spravovat databázové role a uživatele

Na úrovni databáze, modelu všichni uživatelé musí patřit do role. Role definují uživatelé s konkrétní oprávnění pro modelové databáze. Každý uživatel nebo skupina zabezpečení přidán do role musí mít účet v tenantovi Azure AD ve stejném předplatném jako server. 

Jak definovat role se liší v závislosti na tom, které můžete použít nástroj, ale efekt je stejný.

Oprávnění role patří:
*  **Správce** – uživatelé mají úplná oprávnění pro databázi. Databázové role s oprávněním správce se liší od správce serveru.
*  **Proces** – uživateli můžete připojit k a provádění operací procesu v databázi a analýza dat z databáze modelu.
*  **Čtení** – uživatelé můžou pomocí klientské aplikace k připojení a analýza dat z databáze modelu.

Při vytváření projektu s tabelárním modelem, vytvářet role a přidat uživatele nebo skupiny do těchto rolí pomocí Správce rolí v SSDT. Po nasazení na server pomocí aplikace SSMS, [rutiny Powershellu služby analýzy](https://msdn.microsoft.com/library/hh758425.aspx), nebo [tabulkový Model skriptování jazyk](https://msdn.microsoft.com/library/mt614797.aspx) TMSL () přidat nebo odebrat role a členy.

> [!NOTE]
> Skupiny zabezpečení musí mít `MailEnabled` nastavenou na `True`.

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>Chcete-li přidat nebo spravovat role a uživatele v sadě SSDT  
  
1.  V sadě SSDT > **Průzkumníku tabelárních modelů**, klikněte pravým tlačítkem na **role**.  
  
2.  Ve **Správci rolí** klikněte na **Nový**.  
  
3.  Zadejte název role.  
  
     Ve výchozím nastavení je název výchozí role číslované postupně pro každou novou roli. Doporučuje se, že zadáte název, který jasně identifikuje typ členu, například finanční správci nebo specialisty lidských zdrojů.  
  
4.  Vyberte jednu z následujících oprávnění:  
  
    |Oprávnění|Popis|  
    |----------------|-----------------|  
    |**None**|Členy nelze upravit schéma modelu a nelze zadávat dotazy na data.|  
    |**Čtení**|Členy můžete zadávat dotazy na data (podle filtry řádků), ale nelze změnit schéma modelu.|  
    |**Číst a zpracovat**|Členové můžou dotazy na data (na základě filtrů na úrovni řádků) a spusťte proces a proces všechny operace, ale nelze změnit schéma modelu.|  
    |**Proces**|Členy můžete spustit proces a proces všechny operace. Nelze změnit schéma modelu a nelze zadávat dotazy na data.|  
    |**Správce**|Členy můžete upravit schéma modelu a dotazování všechna data.|   
  
5.  Pokud jste roli vytváření má ke čtení nebo oprávnění číst a zpracovat, můžete přidat filtry řádků pomocí vzorce DAX. Klikněte na tlačítko **filtry řádků** kartu, pak vyberte tabulku a pak klikněte na tlačítko **filtr jazyka DAX** pole a potom zadat vzorec jazyka DAX.
  
6.  Klikněte na tlačítko **členy** > **přidat externí**.  
  
8.  V **přidat externí člen**, zadejte uživatele nebo skupiny ve vašem tenantovi Azure AD pomocí e-mailovou adresu. Poté, co klikněte na tlačítko OK a zavřete Správce Role, role a role členy se zobrazí v Průzkumníku tabelárních modelů. 
 
     ![Rolí a uživatelů v Průzkumníku tabelárních modelů](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Nasazení do vašeho serveru Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Chcete-li přidat nebo spravovat role a uživatele v aplikaci SSMS
K přidání rolí a uživatelů do nasazené modelovou databázi, musíte být připojeni k serveru jako správce serveru nebo již v databázi roli s oprávněními správce.

1. V objektu Exporer, klikněte pravým tlačítkem na **role** > **novou roli**.

2. V **vytvořit roli**, zadejte název role a popis.

3. Vyberte oprávnění.
   |Oprávnění|Popis|  
   |----------------|-----------------|  
   |**Úplné řízení (správce)**|Členové mohou změnit schéma modelu zpracování a zadávat dotazy všechna data.| 
   |**Proces databáze**|Členy můžete spustit proces a proces všechny operace. Nelze změnit schéma modelu a nelze zadávat dotazy na data.|  
   |**Čtení**|Členy můžete zadávat dotazy na data (podle filtry řádků), ale nelze změnit schéma modelu.|  
  
4. Klikněte na tlačítko **členství**, pak zadejte uživatele nebo skupinu ve vašem tenantovi Azure AD pomocí e-mailovou adresu.

     ![Přidání uživatele](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Pokud vámi vytvářené role má oprávnění ke čtení, můžete přidat filtry řádků pomocí vzorce DAX. Klikněte na tlačítko **filtry řádků**, vyberte tabulku a potom zadat vzorec jazyka DAX v **filtr jazyka DAX** pole. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>Chcete-li přidat role a uživatele pomocí skriptu TMSL
V okně XMLA v aplikaci SSMS nebo Powershellu můžete spustit skript TMSL. Použití [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) příkazu a [role](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) objektu.

**Ukázkový skript TMSL**

V této ukázce B2B externího uživatele a skupiny se přidají do role analytik s oprávněním ke čtení pro databázi SalesBI. Externí uživatele i skupiny musí být ve stejném tenantovi Azure AD.

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

## <a name="to-add-roles-and-users-by-using-powershell"></a>Chcete-li přidat role a uživatele pomocí Powershellu
[SqlServer](https://msdn.microsoft.com/library/hh758425.aspx) modul obsahuje rutiny pro správu databáze specifické pro úlohy a pro obecné účely rutinu Invoke-ASCmd, který přijímá dotazu tabulkový Model skriptování jazyk TMSL () nebo skript. Pro správu databázových rolí a uživatelů se používají následující rutiny.
  
|Rutina|Popis|
|------------|-----------------| 
|[Přidat RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Přidáte člena do role databáze.| 
|[Odebrat RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Odebrání člena z role databáze.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Spusťte skript TMSL.|

## <a name="row-filters"></a>Filtry řádků  
Filtry řádků definovat, které řádky v tabulce může být dotázán členy určité role. Filtry řádků jsou definované pro každou tabulku v modelu pomocí vzorce DAX.  
  
Filtry řádků lze definovat pouze pro role se pro čtení a číst a oprávnění procesu. Ve výchozím nastavení Pokud pro konkrétní tabulku, není definován řádkový filtr členy můžete dotazovat všechny řádky v tabulce pokud křížové filtrování platí z jiné tabulky.
  
 Filtry řádků vyžadují vzorec DAX, který se musí vyhodnotit na hodnotu TRUE nebo FALSE, chcete-li definovat řádky, které může být dotázán členy dané konkrétní role. Nelze se dotazovat na řádky, které nejsou zahrnuty ve vzorci DAX. Například následující řádek tabulky Customers filtry výrazu, *= zákazníci [Země] = "USA"*, členové role prodeje uvidí jenom zákazníkům v USA.  
  
Filtry řádků platí pro zadané řádky a související řádky. Pokud má tabulka více vztahů, použít filtry zabezpečení pro relaci, která je aktivní. Filtry řádků budou prolínají pomocí dalších filtrech řádků definované pro související tabulky, například:  
  
|Table|Výraz jazyka DAX|  
|-----------|--------------------|  
|Oblast|= Oblast [Země] = "USA"|  
|ProductCategory|= ProductCategory [název] = "Jízdní kola"|  
|Transakce|= Transakce [rok] = 2016|  
  
 Výsledkem je, že členy můžete dotazovat řádky dat, kde se zákazník v USA, kategorie produktů je jízdních kol a je v roce 2016. Uživatelé nemohou spustit dotaz transakce mimo USA, transakcí, které nejsou jízdní kola nebo transakce není v 2016 Pokud nejsou členem jiné role, která uděluje oprávnění.
  
 Filtr, můžete použít *=FALSE()*, odepření přístupu pro všechny řádky pro celou tabulku.

## <a name="next-steps"></a>Další postup
  [Správa správců serveru](analysis-services-server-admins.md)   
  [Správa služby Azure Analysis Services pomocí Powershellu](analysis-services-powershell.md)  
  [Tabular Model Scripting Reference jazyka (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

