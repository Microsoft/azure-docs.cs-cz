---
title: Ukázka-ISO 27001 pomocného a úlohy SQL – postup nasazení
description: Nasaďte kroky ukázkového plánu úloh ISO 27001 App Service Environment/SQL Database.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: fb68b2f78e48dabf5b1377b7c811840a85604f53
ms.sourcegitcommit: b7b0d9f25418b78e1ae562c525e7d7412fcc7ba0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/08/2019
ms.locfileid: "70802426"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Nasazení ukázka ISO 27001 App Service Environment/SQL Database úlohy podrobný plán

Pokud chcete nasadit ukázkovou úlohu Azure modrotisky ISO 27001 App Service Environment/SQL Database, je potřeba provést tyto kroky:

> [!div class="checklist"]
> - Ukázka podrobného plánu pro nasazování [sdílených služeb ISO 27001](../iso27001-shared/index.md)
> - Vytvořit nový podrobný plán z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Ukázka podrobného plánu pro nasazování sdílených služeb ISO 27001

Před nasazením tohoto ukázkového podrobného plánu musí být ukázka podrobného plánu [sdílených služeb ISO 27001](../iso27001-shared/index.md) nasazená do cílového předplatného. V této ukázce podrobného plánu nemusíte mít k dispozici úspěšné nasazení ukázkového plánu sdílených služeb ISO 27001, v této ukázce podrobného plánu dojde k chybě při nasazení.

> [!IMPORTANT]
> Tato ukázka podrobného plánu musí být přiřazená ve stejném předplatném jako ukázka podrobného plánu [sdílených služeb ISO 27001](../iso27001-shared/index.md) .

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejdřív implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako Starter.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na stránce **Začínáme** na levé straně vyberte v části _vytvořit podrobný plán_tlačítko **vytvořit** .

1. **Vyhledejte ISO 27001: Pomocného pomocného nebo podrobného plánu úloh** SQL v _jiných ukázkách_ a vyberte **použít tuto ukázku**.

1. Zadejte _základy_ ukázky podrobného plánu:

   - **Název**podrobného plánu: Zadejte název vaší kopie ukázky podrobného plánu úloh pro: ISO 27001 nebo úlohy SQL.
   - **Umístění definice**: Použijte tři tečky a vyberte skupinu pro správu, do které chcete kopii ukázky Uložit.

1. Vyberte kartu _artefakty_ v horní části stránky nebo **další: Artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnohé z artefaktů mají parametry, které budeme definovat později. Po dokončení kontroly ukázkového plánu vyberte **Uložit koncept** .

## <a name="publish-the-sample-copy"></a>Publikovat ukázkovou kopii

Vaše kopie ukázky podrobného plánu se teď vytvořila ve vašem prostředí. Je vytvořená v režimu **konceptu** a musí být **publikována** před tím, než bude možné ji přiřadit a nasadit. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo Standard ISO 27001.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **publikovat podrobný plán** . Na stránce Nová na pravé straně zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud uděláte změnu později. Zadejte **poznámky ke změnám** , například "první verze publikovaná z ukázky ISO 27001 podrobného plánu". Potom v dolní části stránky vyberte **publikovat** .

## <a name="assign-the-sample-copy"></a>Přiřadit ukázkovou kopii

Po úspěšném **publikování**kopie ukázky podrobného plánu je možné ji přiřadit k předplatnému v rámci skupiny pro správu, do které byl uložen. V tomto kroku je uvedeno, že jsou k dispozici parametry pro každé nasazení kopie ukázky podrobného plánu.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **přiřadit podrobný plán** .

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základní informace

     - **Předplatná:** Vyberte jedno nebo více předplatných, které jsou ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každý pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: Název je předem vyplněný pro vás na základě názvu podrobného plánu.
       Změňte podle potřeby nebo ponechte tak, jak je.
     - **Umístění**: Vyberte oblast, ve které se má spravovaná identita vytvořit. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [Spravované identity pro zdroje Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice**podrobného plánu: Vyberte **publikovanou** verzi vaší kopie ukázky podrobného plánu.

   - Uzamknout přiřazení

     Vyberte nastavení zámku podrobného plánu pro vaše prostředí. Další informace naleznete v tématu [uzamčení zdrojů plánu](../../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí _systém přiřazenou_ možnost spravovaná identita.

   - Parametry podrobného plánu

     Parametry definované v tomto oddílu používá mnoho artefaktů v definici podrobného plánu k zajištění konzistence.

     - **Název organizace**: Zadejte krátký název vaší organizace. Tato vlastnost se primárně používá pro pojmenování prostředků.
     - **ID předplatného sdílené služby**: ID předplatného, ke kterému je přiřazený ukázkový plán [sdílených služeb ISO 27001](../iso27001-shared/index.md)
     - **Výchozí předpona adresy podsítě**: Zápis CIDR pro výchozí podsíť virtuální sítě.
       Výchozí hodnota je _10.1.0.0/16_.
     - **Umístění úlohy**: Určuje umístění, do kterého jsou artefakty nasazeny. Ne všechny služby jsou k dispozici ve všech umístěních. Artefakty, které nasazují takové služby, poskytují možnost parametru pro umístění, do kterého se tento artefakt má nasadit.

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, ve kterém je definován. Tyto parametry jsou [dynamické parametry](../../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte v dolní části stránky **přiřadit** . Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure modrotisky a předdefinované ukázky podrobného plánu jsou **zdarma**. Ceny prostředků Azure se účtují [podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktu podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|Log Analytics skupina prostředků|Resource group|Name|**Uzamkl** – zřetězí **název organizace** s `-workload-log-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Log Analytics skupina prostředků|Resource group|Location|**Locked** – použije parametr podrobného plánu.|
|Šablona pro Log Analytics|Šablona Resource Manageru|Úroveň služby|Nastaví úroveň pracovního prostoru Log Analytics. Výchozí hodnota je _PerNode_.|
|Šablona pro Log Analytics|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona pro Log Analytics|Šablona Resource Manageru|Location|Oblast, která se používá k vytvoření pracovního prostoru Log Analytics. Výchozí hodnota je _západní USA 2_.|
|Skupina síťových prostředků|Resource group|Name|**Uzamkl** – zřetězí **název organizace** s `-workload-net-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Skupina síťových prostředků|Resource group|Location|**Locked** – použije parametr podrobného plánu.|
|Šablona skupiny zabezpečení sítě|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Privátní IP adresa brány Azure Firewall|Nakonfiguruje privátní IP adresu [brány firewall Azure](../../../../firewall/overview.md). By měl být součástí zápisu CIDR definovaného v _ISO 27001: Parametr artefaktu sdílených služeb_ **Azure firewall předponu adresy podsítě**. Výchozí hodnota je _10.0.4.4_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|ID předplatného sdílených služeb|Hodnota, která slouží k povolení partnerského vztahu virtuálních sítí mezi úlohou a sdílenými službami.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Předpona adresy virtuální sítě|Zápis CIDR pro virtuální síť. Výchozí hodnota je _10.1.0.0/16_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Výchozí předpona adresy podsítě|Zápis CIDR pro výchozí podsíť virtuální sítě. Výchozí hodnota je _10.1.0.0/16_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Přidá IP adresu|IP adresa prvního přidání virtuálního počítače Tato hodnota se používá jako vlastní virtuální síť DNS.|
|Key Vault skupina prostředků|Resource group|Name|**Uzamkl** – zřetězí **název organizace** s `-workload-kv-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Key Vault skupina prostředků|Resource group|Location|**Locked** – použije parametr podrobného plánu.|
|Šablona pro Key Vault|Šablona Resource Manageru|ID objektu AAD|Identifikátor objektu AAD účtu, který vyžaduje přístup k instanci Key Vault. Žádná výchozí hodnota a nemůže být ponechána prázdná. Pokud chcete tuto hodnotu najít z Azure Portal, vyhledejte a v části _služby_vyberte uživatelé. Pomocí pole _název_ vyfiltrujte název účtu a vyberte tento účet. Na stránce _Profil uživatele_ vyberte ikonu "kliknutím ke kopírování" vedle _ID objektu_.|
|Šablona pro Key Vault|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona pro Key Vault|Šablona Resource Manageru|Skladová jednotka (SKU) pro Key Vault|Určuje SKLADOVOU položku Key Vault, která je vytvořena. Výchozí hodnota je _Premium_.|
|Šablona pro Key Vault|Šablona Resource Manageru|Uživatelské jméno správce Azure SQL Serveru|Uživatelské jméno použité pro přístup k Azure SQL Server. Musí odpovídat stejné hodnotě vlastnosti v **šabloně Azure SQL Database**. Výchozí hodnota je _SQL-Admin-User_.|
|Azure SQL Database skupina prostředků|Resource group|Name|**Uzamkl** – zřetězí **název organizace** s `-workload-azsql-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Azure SQL Database skupina prostředků|Resource group|Location|**Locked** – použije parametr podrobného plánu.|
|Šablona Microsoft Azure SQL Database|Šablona Resource Manageru|Uživatelské jméno správce Azure SQL Serveru|Uživatelské jméno pro Azure SQL Server. Musí odpovídat stejné hodnotě vlastnosti v **šabloně Key Vault**. Výchozí hodnota je _SQL-Admin-User_.|
|Šablona Microsoft Azure SQL Database|Šablona Resource Manageru|Heslo správce Azure SQL Server (ID prostředku Key Vault)|ID prostředku Key Vault. Použijte "/Subscription/{SubscriptionId}/resourceGroups/{orgName}-Workload-KV/Providers/Microsoft.KeyVault/Vaults/{orgName}-Workload-KV" a nahraďte `{subscriptionId}` ID vašeho předplatného a `{orgName}` názvem organizace. parametr podrobného plánu|
|Šablona Microsoft Azure SQL Database|Šablona Resource Manageru|Heslo správce Azure SQL Server (název Key Vault tajného klíče)|Uživatelské jméno správce SQL Server. Musí odpovídat hodnotě v **Key Vault** vlastnosti šablony **uživatelské jméno správce Azure SQL Server**.|
|Šablona Microsoft Azure SQL Database|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona Microsoft Azure SQL Database|Šablona Resource Manageru|ID objektu Správce AAD|ID objektu AAD uživatele, který se přiřadí jako správce služby Active Directory. Žádná výchozí hodnota a nemůže být ponechána prázdná. Pokud chcete tuto hodnotu najít z Azure Portal, vyhledejte a v části _služby_vyberte uživatelé. Pomocí pole _název_ vyfiltrujte název účtu a vyberte tento účet. Na stránce _Profil uživatele_ vyberte ikonu "kliknutím ke kopírování" vedle _ID objektu_.|
|Šablona Microsoft Azure SQL Database|Šablona Resource Manageru|Přihlášení správce AAD|V současné době se účty Microsoft (například live.com nebo outlook.com) nedají nastavit jako správci. Pouze uživatelé a skupiny zabezpečení v rámci vaší organizace lze nastavit jako správce. Žádná výchozí hodnota a nemůže být ponechána prázdná. Pokud chcete tuto hodnotu najít z Azure Portal, vyhledejte a v části _služby_vyberte uživatelé. Pomocí pole _název_ vyfiltrujte název účtu a vyberte tento účet. Na stránce _Profil uživatele_ zkopírujte _uživatelské jméno_.|
|App Service Environment skupina prostředků|Resource group|Name|**Uzamkl** – zřetězí **název organizace** s `-workload-ase-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|App Service Environment skupina prostředků|Resource group|Location|**Locked** – použije parametr podrobného plánu.|
|Šablona funkce App Service Environment|Šablona Resource Manageru|Název domény|Název adresáře služby Active Directory vytvořeného ukázkou. Výchozí hodnota je _contoso.com_.|
|Šablona funkce App Service Environment|Šablona Resource Manageru|Umístění pomocného mechanismu|App Service Environment umístění. Výchozí hodnota je _západní USA 2_.|
|Šablona funkce App Service Environment|Šablona Resource Manageru|Doba uchování protokolu Application Gateway ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|

## <a name="next-steps"></a>Další kroky

Teď, když jste si prohlédli postup 27001 nasazení ukázky App Service Environment/SQL Database úlohy podrobného plánu úloh, najdete v následujících článcích informace o architektuře a mapování ovládacích prvků:

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database úlohy podrobného plánu – přehled](./index.md)
> [ISO 27001 App Service Environment/SQL Database úlohový plán – mapování ovládacích prvků](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)