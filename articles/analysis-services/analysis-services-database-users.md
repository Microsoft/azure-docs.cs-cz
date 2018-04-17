---
title: Spravovat role databáze a uživatele ve službě Azure Analysis Services | Microsoft Docs
description: Zjistěte, jak spravovat role databáze a uživatele na serveru služby Analysis Services v Azure.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3c35fb1ee70544b8b01bbadaf72ee38145179b27
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/16/2018
---
# <a name="manage-database-roles-and-users"></a>Spravovat role databáze a uživatele

Na úrovni databáze modelu všichni uživatelé musí patřit roli. Role definují uživatelé s konkrétní oprávnění pro model databáze. Každý uživatel nebo skupina zabezpečení přidaný k roli musí mít účet v klient služby Azure AD ve stejném předplatném jako server.

Jak definovat role se liší v závislosti na tom, které můžete použít nástroj, ale účinek je stejný.

Oprávnění role patří:
*  **Správce** -uživatelé mají úplná oprávnění pro databázi. Databázové role s oprávněními správce se liší od správce serveru.
*  **Proces** -uživatelé připojení a provádět operace procesu na databázi a analyzovat data databáze modelu.
*  **Čtení** -uživatelů můžete použít klientskou aplikaci pro připojení k a analyzovat data databáze modelu.

Při vytváření projektu tabulkového modelu, můžete vytvořit role a přidat uživatele nebo skupiny do těchto rolí pomocí Správce rolí v sadě SSDT. Při nasazení na server, můžete pomocí aplikace SSMS, [rutiny prostředí PowerShell Analysis Services](https://msdn.microsoft.com/library/hh758425.aspx), nebo [tabulkový Model skriptovací jazyk](https://msdn.microsoft.com/library/mt614797.aspx) (TMSL) můžete přidat nebo odebrat role a uživatele členy.

## <a name="to-add-or-manage-roles-and-users-in-ssdt"></a>Přidat nebo správě rolí a uživatelů v rozšíření SSDT  
  
1.  V sadě SSDT > **tabulkový Model Explorer**, klikněte pravým tlačítkem na **role**.  
  
2.  Ve **Správci rolí** klikněte na **Nový**.  
  
3.  Zadejte název role.  
  
     Ve výchozím nastavení je název výchozí role číslované postupně pro každou novou roli. Doporučuje se, že zadáte název, který jednoznačně identifikuje typ člena, například finanční správci nebo odborníky lidských zdrojů.  
  
4.  Vyberte jednu z těchto oprávnění:  
  
    |Oprávnění|Popis|  
    |----------------|-----------------|  
    |**None**|Členy nelze změnit schéma modelu a nemůže zadat dotaz na data.|  
    |**Čtení**|Členy dotazy na data (podle řádkové filtry) ale nelze změnit schéma modelu.|  
    |**Čtení a proces**|Členy může dotazovat data (závislosti na úrovni řádků filtry) a spusťte proces a proces všechny operace, ale nelze změnit schéma modelu.|  
    |**Proces**|Členy můžete spustit proces a proces všechny operace. Nelze změnit schéma modelu a nemůže zadat dotaz na data.|  
    |**Správce**|Členy můžete upravit schéma modelu a dotaz všechna data.|   
  
5.  Pokud jste roli vytváření má ke čtení nebo oprávnění ke čtení a proces, můžete přidat řádkové filtry pomocí vzorce DAX. Klikněte na tlačítko **řádkové filtry** , pak vybrat tabulku, a potom klikněte na tlačítko **DAX filtru** pole a potom zadat vzorec jazyka DAX.
  
6.  Klikněte na tlačítko **členy** > **přidat externí**.  
  
8.  V **přidat externího člena**, zadejte uživatele nebo skupiny v klientovi služby Azure AD pomocí e-mailovou adresu. Po klikněte na tlačítko OK a zavřete Role správce, role a členy role se zobrazí v Průzkumníku tabulkový Model. 
 
     ![Role a uživatele v tabulkovém Průzkumníka modelu](./media/analysis-services-database-users/aas-roles-tmexplorer.png)

9. Nasaďte do serveru Azure Analysis Services.


## <a name="to-add-or-manage-roles-and-users-in-ssms"></a>Přidat nebo správě rolí a uživatelů v aplikaci SSMS
K přidání rolí a uživatelů do nasazené model databáze, musíte být připojení k serveru jako správce serveru nebo je již v databázové role s oprávněními správce.

1. V objektu Exporer, klikněte pravým tlačítkem na **role** > **novou roli**.

2. V **vytvořit roli**, zadejte název role a popis.

3. Vyberte oprávnění.
   |Oprávnění|Popis|  
   |----------------|-----------------|  
   |**Úplné řízení (správce)**|Členové mohou změnit schéma modelu zpracování a může prohledávat všechna data.| 
   |**Proces databáze**|Členy můžete spustit proces a proces všechny operace. Nelze změnit schéma modelu a nemůže zadat dotaz na data.|  
   |**Čtení**|Členy dotazy na data (podle řádkové filtry) ale nelze změnit schéma modelu.|  
  
4. Klikněte na tlačítko **členství**, pak zadejte uživatele nebo skupinu ve vašem klientovi Azure AD pomocí e-mailovou adresu.

     ![Přidání uživatele](./media/analysis-services-database-users/aas-roles-adduser-ssms.png)

5. Pokud roli, kterou vytváříte má oprávnění pro čtení, můžete přidat řádkové filtry pomocí vzorce DAX. Klikněte na tlačítko **řádkové filtry**, vyberte tabulku a potom zadat vzorec jazyka DAX v **DAX filtru** pole. 

## <a name="to-add-roles-and-users-by-using-a-tmsl-script"></a>K přidání rolí a uživatelů pomocí skriptu TMSL
Když spustíte skript TMSL v okně XMLA v aplikaci SSMS nebo pomocí prostředí PowerShell. Použití [CreateOrReplace](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-commands/createorreplace-command-tmsl) příkaz a [role](https://docs.microsoft.com/sql/analysis-services/tabular-models-scripting-language-objects/roles-object-tmsl) objektu.

**Ukázkový skript TMSL**

V této ukázce B2B externího uživatele a skupiny jsou přidány do role analytik s oprávnění ke čtení pro databázi SalesBI. Externí uživatele i skupiny musí být ve stejném klientovi Azure AD.

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

## <a name="to-add-roles-and-users-by-using-powershell"></a>K přidání rolí a uživatelů pomocí prostředí PowerShell
[SqlServer](https://msdn.microsoft.com/library/hh758425.aspx) modul poskytuje rutiny databáze specifické pro úlohy správy a pro obecné účely rutinu Invoke-ASCmd, která přijímá dotazu tabulkový Model skriptovací jazyk (TMSL) nebo skriptu. Pro správu role databáze a uživatelů se používají následující rutiny.
  
|Rutina|Popis|
|------------|-----------------| 
|[Přidat RoleMember](https://msdn.microsoft.com/library/hh510167.aspx)|Přidáte člena do role databáze.| 
|[Odebrat RoleMember](https://msdn.microsoft.com/library/hh510173.aspx)|Odebrání člena z databázové role.|   
|[Invoke-ASCmd](https://msdn.microsoft.com/library/hh479579.aspx)|Spuštění skriptu TMSL.|

## <a name="row-filters"></a>Řádkové filtry  
Řádkové filtry definovat, které řádky v tabulce může být dotazován členy určité role. Řádkové filtry jsou definovány pro každou tabulku v modelu s použitím vzorce DAX.  
  
Řádkové filtry lze definovat pouze pro role pro čtení a čtení a proces oprávnění. Ve výchozím nastavení Pokud pro konkrétní tabulku, není definován řádkový filtr Členové můžete dotazovat všechny řádky v tabulce Pokud křížového filtru platí z jiné tabulky.
  
 Řádkové filtry vyžadují vzorec jazyka DAX, který se musí vyhodnotit na hodnotu TRUE nebo FALSE, můžete definovat na řádky, které může být dotazován členové této konkrétní roli. Nelze se dotazovat na řádky, které nejsou zahrnuty ve vzorci jazyka DAX. Například tabulku zákazníků s následující řádek filtry výrazu *= zákazníků [Země] = "USA"*, zákazníků v USA, uvidí jenom členové role prodej.  
  
Řádkové filtry se vztahuje k zadané řádky a související řádky. Pokud tabulka obsahuje více vztahů, použít filtry zabezpečení pro vztah, který je aktivní. Řádkové filtry se prolínají s další řádek filtrech definované pro tabulky v relaci, třeba:  
  
|Table|Výraz jazyka DAX|  
|-----------|--------------------|  
|Oblast|= Oblast [Země] = "USA"|  
|ProductCategory|= ProductCategory [Name] = "Jízdních kol"|  
|Transakce|= Transakcí [rok] = 2016|  
  
 Net efekt je, že členové můžete dotazovat řádky dat, kde zákazník je v USA, kategorie produktů je jízdních kol a je roku 2016. Uživatelé se nemohou dotazovat transakce mimo USA, transakce, které nejsou jízdních kol nebo transakce není v 2016 dokud je členem jiné role, která uděluje tato oprávnění.
  
 Můžete použít možnosti filtrovat, *=FALSE()*, tak, aby odepřel přístup k všechny řádky pro celou tabulku.

## <a name="next-steps"></a>Další postup
  [Spravovat správce serveru](analysis-services-server-admins.md)   
  [Správa služby Azure Analysis Services pomocí prostředí PowerShell](analysis-services-powershell.md)  
  [Tabulkový Model skriptování referenční příručka jazyka (TMSL)](https://docs.microsoft.com/sql/analysis-services/tabular-model-scripting-language-tmsl-reference)

