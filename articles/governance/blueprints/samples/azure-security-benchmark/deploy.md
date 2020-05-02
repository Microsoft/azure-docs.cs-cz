---
title: Ukázka nasazení Azure Security benchmark details
description: Nasaďte kroky pro ukázku Azure Security test Details, včetně podrobností parametrů artefaktu podrobného plánu.
ms.date: 05/01/2020
ms.topic: sample
ms.openlocfilehash: 0e967e2217b90e036d809ce7baf500c3bc791c6f
ms.sourcegitcommit: 366e95d58d5311ca4b62e6d0b2b47549e06a0d6d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 05/01/2020
ms.locfileid: "82725963"
---
# <a name="deploy-the-azure-security-benchmark-blueprint-sample"></a>Nasazení ukázky Azure Security test details

Pokud chcete nasadit ukázku Azure Security test Testal na Azure, musíte provést tyto kroky:

> [!div class="checklist"]
> - Vytvořit nový podrobný plán z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejdřív implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako Starter.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na stránce **Začínáme** na levé straně vyberte v části _vytvořit podrobný plán_tlačítko **vytvořit** .

1. V části _Další ukázky_ Najděte ukázku **Azure Security test** Details a vyberte tuto ukázku kliknutím na název.

1. Zadejte _základy_ ukázky podrobného plánu:

   - **Název**podrobného plánu: zadejte název vaší kopie ukázky testu výkonnosti Azure Security test.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _artefakty_ v horní části stránky nebo **Další: artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnohé z artefaktů mají parametry, které budeme definovat později. Po dokončení kontroly ukázkového plánu vyberte **Uložit koncept** .

## <a name="publish-the-sample-copy"></a>Publikovat ukázkovou kopii

Vaše kopie ukázky podrobného plánu se teď vytvořila ve vašem prostředí. Je vytvořená v režimu **konceptu** a musí být **publikována** před tím, než bude možné ji přiřadit a nasadit. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo zarovnání s doporučeními benchmarku pro zabezpečení Azure.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **publikovat podrobný plán** . Na stránce Nová na pravé straně zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud uděláte změnu později. Zadejte **poznámky ke změnám** , jako je například první verze publikovaná z ukázkového plánu zabezpečení Azure Security test. Potom v dolní části stránky vyberte **publikovat** .

## <a name="assign-the-sample-copy"></a>Přiřadit ukázkovou kopii

Po úspěšném **publikování**kopie ukázky podrobného plánu je možné ji přiřadit k předplatnému v rámci skupiny pro správu, do které byl uložen. V tomto kroku je uvedeno, že jsou k dispozici parametry pro každé nasazení kopie ukázky podrobného plánu.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **přiřadit podrobný plán** .

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Předplatná**: vyberte jedno nebo více předplatných ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každý pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: název je předem vyplněný na základě názvu podrobného plánu.
       Změňte podle potřeby nebo ponechte tak, jak je.
     - **Umístění**: Vyberte oblast, ve které se má spravovaná identita vytvořit. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice**podrobného plánu: vyberte **publikovanou** verzi vaší kopie ukázky podrobného plánu.

   - Zamknout přiřazení

     Vyberte nastavení zámku podrobného plánu pro vaše prostředí. Další informace naleznete v tématu [uzamčení zdrojů plánu](../../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí _systém přiřazenou_ možnost spravovaná identita.

   - Parametry artefaktu

     Parametry definované v této části se vztahují na artefakt, ve kterém je definován. Tyto parametry jsou [dynamické parametry](../../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte v dolní části stránky **přiřadit** . Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure modrotisky a předdefinované ukázky podrobného plánu jsou **zdarma**. Ceny prostředků Azure se účtují [podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktu podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásad|
Seznam uživatelů vyloučených ze skupiny správců virtuálních počítačů s Windows|Středníkem oddělený seznam členů, kteří by měli být vyloučení v místní skupině Administrators. Např.: Správce; myUser1; myUser2|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásad|Seznam uživatelů, které musí být zahrnuté ve skupině Správci virtuálních počítačů s Windows|Středníkem oddělený seznam členů, kteří by měli být zahrnutí do místní skupiny Administrators. Např.: Správce; myUser1; myUser2|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásad|Seznam uživatelů, které skupina správců virtuálních počítačů s Windows smí zahrnovat *jenom*|Středníkem oddělený seznam všech očekávaných členů místní skupiny Administrators. Např.: Správce; myUser1; myUser2|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásad|Seznam oblastí, kde by měla být povolena Network Watcher|Úplný seznam oblastí získáte pomocí Get-AzLocation.|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásad|Virtuální síť, ve které by se měly připojit virtuální počítače|Příklad:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásad|Síťová brána, kterou by měly virtuální sítě používat|Příklad:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásad|Seznam ID pracovních prostorů, kde se mají Log Analytics agenti připojit|Středníkem oddělený seznam ID pracovních prostorů, ke kterým by měl být agent Log Analytics připojený|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásad|Seznam typů prostředků, které by měly mít povolené diagnostické protokoly|Auditování nastavení diagnostiky pro vybrané typy prostředků|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásad|Nejnovější verze PHP|Nejnovější podporovaná verze PHP pro App Services|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásad|Nejnovější verze Java|Nejnovější podporovaná verze Java pro App Services|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásad|Nejnovější verze Windows Pythonu|Nejnovější podporovaná verze Pythonu pro App Services|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásad|Nejnovější verze Pythonu pro Linux|Nejnovější podporovaná verze Pythonu pro App Services|

## <a name="next-steps"></a>Další kroky

Teď, když jste si prohlédli postup nasazení ukázky Azure Security test Details, najdete v následujících článcích informace o mapování podrobného plánu a ovládacího prvku:

> [!div class="nextstepaction"]
> [Azure Security test Details Details-Overview – Přehled](./index.md)
> [Azure Security test Details – mapování ovládacích prvků](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
