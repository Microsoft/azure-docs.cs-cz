---
title: Ukázka podrobného plánu pro cílovou zónu migrace CAF
description: Nasaďte kroky pro ukázkový postup migrace CAF do cílové zóny, včetně podrobností o parametrech artefaktu podrobného plánu.
ms.date: 03/12/2021
ms.topic: sample
ms.openlocfilehash: d5aed1d492fea102598a22921d04ed8681c727cb
ms.sourcegitcommit: 66ce33826d77416dc2e4ba5447eeb387705a6ae5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/15/2021
ms.locfileid: "103470111"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-migrate-landing-zone-blueprint-sample"></a>Ukázková ukázka podrobného plánu cílové zóny pro migraci do Azure – nasazení rozhraní Microsoft Cloud

Pokud chcete nasadit ukázku podrobného plánu pro migrační zónu Azure modrotisky CAF, musí být podniknuty tyto kroky:

> [!div class="checklist1"]
> - Doporučuje se nasadit ukázku podrobného plánu [CAF Foundation](../caf-foundation/index.md) .

> [!div class="checklist2"]
> - Vytvořte nový podrobný plán z ukázky.
> - Kopii ukázky si označte jako **publikovanou**.
> - Přiřaďte kopii podrobného plánu k existujícímu předplatnému.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejprve implementujte ukázku podrobného plánu tak, že z ukázky vytvoříte nový podrobný plán ve svém prostředí.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vlevo na stránce **Začínáme** vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. V části _Jiné vzory_ vyhledejte ukázku podrobného plánu **cílové zóny migrací CAF** a vyberte **Použít tento vzorek**.

1. Zadejte _základní informace_ o ukázce podrobného plánu:
   - **Název** podrobného plánu Zadejte název vaší kopie ukázky pro CAF cílovou zónu migrace za provozu.
   - **Umístění definice** Použijte tři tečky a vyberte skupinu pro správu, do které chcete kopii ukázky Uložit.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** dole na stránce.

1. Projděte si seznam artefaktů, které tvoří ukázku podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Až skončíte s kontrolou ukázky podrobného plánu, vyberte **Uložit koncept**.

## <a name="publish-the-sample-copy"></a>Publikování kopie ukázky

V prostředí máte teď vytvořenou kopii ukázky podrobného plánu. Kopie je vytvořená v režimu **Koncept** a než ji budete muset přiřadit a nasadit, musí být **publikovaná**. Kopii ukázky podrobného plánu je možné přizpůsobit podle prostředí a potřeb, ale tyto úpravy se mohou vzdálit doprovodným materiálům k cílové zóně migrací CAF.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. Nahoře na stránce vyberte **Publikovat podrobný plán**. Napravo na nové stránce zadejte **verzi** kopie ukázky podrobného plánu. Tato vlastnost je užitečná, protože umožňuje pozdější úpravy. Zadejte **poznámky ke změnám** , jako je například "první verze publikovaná z CAF zóna pro přechod na cílovou zónu ukázka". Na konci stránky pak vyberte **Publikovat**.

## <a name="assign-the-sample-copy"></a>Přiřazení ukázkové kopie

Po úspěšném **publikování** kopie ukázky podrobného plánu je možné ji přiřadit k předplatnému v rámci skupiny pro správu, do které byl uložen. V tomto kroku zadáte parametry pro každé nasazení kopie ukázky podrobného plánu.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **Přiřadit podrobný plán**.

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy
     - **Předplatná**: vyberte jedno nebo více předplatných ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každé z nich pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: název je předem vyplněný na základě názvu podrobného plánu.
       Podle potřeby ho změňte nebo ponechte.
     - **Umístění**: Vyberte oblast, ve které se má spravovaná identita vytvořit.
     - Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu.
       Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice** podrobného plánu: vyberte **publikovanou** verzi vaší kopie ukázky podrobného plánu.
    
   - Zamknout přiřazení

     Vyberte nastavení uzamčení podrobného plánu pro vaše prostředí. Další informace naleznete v tématu [uzamčení zdrojů plánu](../../concepts/resource-locking.md).

   - Spravovaná identita

     Vyberte buď výchozí možnost _systémem přiřazené_ spravované identity, nebo _uživatelem přiřazenou_ identitu.

   - Parametry podrobného plánu

     Parametry definované v tomto oddílu používá mnoho artefaktů v definici podrobného plánu k zajištění konzistence.

       - **Organizace**: zadejte název vaší organizace, například contoso nebo Fabrikam, musí být jedinečný.
       - **A**: vyberte jednu oblast Azure pro nasazení.
       
   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, ve kterém jsou definovány. Tyto parametry jsou [dynamické parametry](../../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte **Přiřadit** v dolní části stránky. Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba pět minut. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure Blueprints a integrované ukázky podrobného plánu jsou **bezplatné**. U prostředků Azure se [cena stanovuje podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktů podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Description|
|-|-|-|-|
|Nasadit cílovou zónu sítě vNET|Šablona Resource Manageru|IPAddress_Space|**Uzamčeno** – zadejte první dva oktety, například 10.0.|
|Nasení trezoru Key Vault|Šablona Resource Manageru|KV-AccessPolicy|ID objektu **Uzamčeno** nebo uživatele pro udělení oprávnění k v Key Vault|
|Nasazení Log Analytics|Šablona Resource Manageru|LogAnalytics_DataRetention|**Uzamčený** počet dní, po které se budou uchovávat data v Log Analytics|
|Nasazení Log Analytics|Šablona Resource Manageru|LogAnalytics_Location|**Uzamčeno** – oblast použitá při vytváření pracovního prostoru.|
|Nasazení Azure Migrate|Šablona Resource Manageru|Azure_Migrate_Location|**Uzamčené** – vyberte oblast, která se má nasadit Azure Migrate|

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali postup nasazení ukázky podrobného plánu CAF pro migraci cílové zóny, najdete informace o architektuře v následujících článcích:

> [!div class="nextstepaction"]
> [Cílová zóna migrace CAF – přehled](./index.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
