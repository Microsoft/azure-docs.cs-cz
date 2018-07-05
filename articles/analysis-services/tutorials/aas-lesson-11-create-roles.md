---
title: 'Kurz služby Azure Analysis Services – Lekce 11: Vytvoření rolí | Dokumentace Microsoftu'
description: Popisuje, jak vytvořit role v projektu Kurz služby Azure Analysis Services.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 38c69b65d33a915d0b7cf43dc8ef5d43413163eb
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 07/03/2018
ms.locfileid: "37441239"
---
# <a name="create-roles"></a>Vytvoření rolí

V této lekci vytvoříte role. Role poskytují objekt databáze modelu a zabezpečení dat tím, že omezují přístup pouze na uživatele, kteří jsou členy dané role. Každá role je definována s jediným oprávněním: Žádné, Čtení, Čtení a zpracování, Zpracování nebo Správce. Role je možné definovat při vytváření modelu pomocí Správce rolí. Po nasazení modelu můžete role spravovat pomocí aplikace SQL Server Management Studio (SSMS). Další informace najdete v tématu [Role](https://docs.microsoft.com/sql/analysis-services/tabular-models/roles-ssas-tabular).
  
> [!NOTE]  
> Vytvoření rolí není nezbytné pro dokončení tohoto kurzu. Ve výchozím nastavení má účet, ke kterému jste právě přihlášeni, oprávnění správce modelu. Aby však mohli data procházet pomocí klienta sestav ostatní uživatelé ve vaší organizaci, musíte vytvořit alespoň jednu roli s oprávněním Čtení a přidat tyto uživatele jako členy.  
  
Vytvoříte tři role:  
  
-   **Manažer prodeje** – Tato role může zahrnovat uživatele ve vaší organizaci, u kterých chcete, aby měli oprávnění Čtení ke všem objektům a datům modelu.  
  
-   **Analytik prodeje v USA** – Tato role může zahrnovat uživatele ve vaší organizaci, u kterých chcete, aby mohli procházet pouze data související s prodejem v USA. Pro tuto roli pomocí vzorce DAX nadefinujete *Filtr řádků*, který členům umožní procházet pouze data pro USA.  
  
-   **Správce** – Tato role může zahrnovat uživatele, u kterých chcete, aby měli oprávnění Správce, což jim poskytne neomezený přístup a oprávnění k provádění úloh správy s databází modelu.  
  
Protože jsou účty uživatelů a skupin systému Windows ve vaší organizaci jedinečné, můžete mezi členy přidat účty z konkrétní organizace. Pro účely tohoto kurzu však můžete členy také ponechat prázdné. Účinky jednotlivých rolí otestujete později v lekci 12: Analýza v aplikaci Excel.  
  
Odhadovaný čas dokončení této lekce: **15 minut**  
  
## <a name="prerequisites"></a>Požadavky  
Toto téma je součástí kurzu tabelárního modelování, který by se měl dokončit v daném pořadí. Před provedením úkolů v této lekci byste měli mít dokončenou předchozí lekci: [Lekce 10: Vytvoření oddílů](../tutorials/aas-lesson-10-create-partitions.md).  
  
## <a name="create-roles"></a>Vytvoření rolí  
  
#### <a name="to-create-a-sales-manager-user-role"></a>Vytvoření role uživatele Manažer prodeje  
  
1.  V Průzkumníku tabelárních modelů klikněte pravým tlačítkem na **Role** > **Role**.  
  
2.  Ve Správci rolí klikněte na **Nový**.  
  
3.  Klikněte na novou roli a potom ji ve sloupci **Název** přejmenujte na **Manažer prodeje**.  
  
4.  Ve sloupci **Oprávnění** klikněte na rozevírací seznam a vyberte oprávnění **Čtení**. 

    ![aas-lesson11-new-role](../tutorials/media/aas-lesson11-new-role.png) 
  
5.  Volitelné: Klikněte na kartu **Členové** a pak na **Přidat**. V dialogovém okně **Vybrat uživatele nebo skupiny** zadejte uživatele nebo skupiny systému Windows z vaší organizace, které chcete zahrnout do role.  
  
#### <a name="to-create-a-sales-analyst-us-user-role"></a>Vytvoření role uživatele Analytik prodej v USA  
  
1.  Ve Správci rolí klikněte na **Nový**.    
  
2.  Přejmenujte roli na **Analytik prodeje v USA**.  
  
3.  Přidělte této roli oprávnění **Čtení**.  
  
4.  Klikněte na kartu Filtry řádků a pak jenom pro tabulku **DimGeography** zadejte do sloupce Filtr DAX následující vzorec:  
  
    ```Administrator
    =DimGeography[CountryRegionCode] = "US" 
    ```
    
    Vzorec filtru řádků se musí přeložit na logickou hodnotu (TRUE/FALSE). Pomocí tohoto vzorce určujete, že pro uživatele budou viditelné pouze řádky, na kterých má kód země nebo oblasti hodnotu USA.  
    ![aas-lesson11-role-filter](../tutorials/media/aas-lesson11-role-filter.png) 
  
6.  Volitelné: Klikněte na kartu **Členové** a pak na **Přidat**. V dialogovém okně **Vybrat uživatele nebo skupiny** zadejte uživatele nebo skupiny systému Windows z vaší organizace, které chcete zahrnout do role.  
  
#### <a name="to-create-an-administrator-user-role"></a>Vytvoření role uživatele Správce  
  
1.  Klikněte na možnost **Nové**.  
  
2.  Přejmenujte roli na **Správce**.  
  
3.  Přidělte této roli oprávnění **Správce**.  
  
4.  Volitelné: Klikněte na kartu **Členové** a pak na **Přidat**. V dialogovém okně **Vybrat uživatele nebo skupiny** zadejte uživatele nebo skupiny systému Windows z vaší organizace, které chcete zahrnout do role. 
  
  
## <a name="whats-next"></a>Co dále?
[Lekce 12: Analýza v aplikaci Excel](../tutorials/aas-lesson-12-analyze-in-excel.md)

  
  
