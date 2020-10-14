---
title: Ukázka podrobného plánu srovnávacího testu zabezpečení Azure
description: Přehled ukázky podrobného plánu srovnávacího testu zabezpečení Azure. Tento ukázkový podrobný plán pomáhá zákazníkům vyhodnotit konkrétní kontroly.
ms.date: 10/14/2020
ms.topic: sample
ms.openlocfilehash: 6b147ffd4efbf814b6119f5d7e90dc7af2ea2687
ms.sourcegitcommit: 1b47921ae4298e7992c856b82cb8263470e9e6f9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/14/2020
ms.locfileid: "92057582"
---
# <a name="azure-security-benchmark-blueprint-sample"></a>Ukázka Azure Security test details

Ukázka podrobného plánu srovnávacího testu zabezpečení Azure poskytuje ochranné mantinely zásad správného řízení s využitím [Azure Policy](../../policy/overview.md), které pomáhají vyhodnotit specifické kontroly [srovnávacího testu zabezpečení Azure](../../../security/benchmarks/overview.md). Tento podrobný plán pomáhá zákazníkům nasadit základní sadu zásad pro libovolnou architekturu nasazenou v Azure, ve které chtějí implementovat kontroly srovnávacího testu zabezpečení Azure.

## <a name="control-mapping"></a>Mapování kontrol

[Mapování ovládacího prvku Azure Policy](../../policy/samples/azure-security-benchmark.md) poskytuje podrobné informace o definicích zásad zahrnutých v tomto podrobném plánu a o tom, jak se tyto definice zásad mapují k doménám a **ovládacím prvkům** **dodržování předpisů** v testu zabezpečení Azure. Při přiřazení k architektuře jsou prostředky vyhodnocovány Azure Policym při nedodržení předpisů s přiřazenými definicemi zásad. Další informace najdete v tématu [Azure Policy](../../policy/overview.md).

## <a name="deploy"></a>Nasazení

Pokud chcete nasadit ukázku Azure Security test Testal na Azure, musíte provést tyto kroky:

> [!div class="checklist"]
> - Vytvořte nový podrobný plán z ukázky.
> - Kopii ukázky si označte jako **publikovanou**.
> - Přiřaďte kopii podrobného plánu k existujícímu předplatnému.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free).

### <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejprve implementujte ukázku podrobného plánu tak, že z ukázky vytvoříte nový podrobný plán ve svém prostředí.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vlevo na stránce **Začínáme** vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. V části _Další ukázky_ Najděte ukázku **Azure Security test** Details a vyberte název pro výběr této ukázky.

1. Zadejte _základní informace_ o ukázce podrobného plánu:

   - **Název**podrobného plánu: zadejte název vaší kopie ukázky testu výkonnosti Azure Security test.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** dole na stránce.

1. Zkontrolujte seznam artefaktů, které jsou zahrnuty v ukázce podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Až skončíte s kontrolou ukázky podrobného plánu, vyberte **Uložit koncept**.

### <a name="publish-the-sample-copy"></a>Publikování kopie ukázky

V prostředí máte teď vytvořenou kopii ukázky podrobného plánu. Kopie je vytvořená v režimu **Koncept** a než ji budete muset přiřadit a nasadit, musí být **publikovaná**. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo zarovnání s doporučeními benchmarku pro zabezpečení Azure.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. Nahoře na stránce vyberte **Publikovat podrobný plán**. Napravo na nové stránce zadejte **verzi** kopie ukázky podrobného plánu. Tato vlastnost je užitečná, protože umožňuje pozdější úpravy. Zadejte **poznámky ke změnám** , jako je například první verze publikovaná z ukázkového plánu zabezpečení Azure Security test. Na konci stránky pak vyberte **Publikovat**.

### <a name="assign-the-sample-copy"></a>Přiřazení ukázkové kopie

Po úspěšném **publikování**kopie ukázky podrobného plánu je možné ji přiřadit k předplatnému v rámci skupiny pro správu, do které byl uložen. V tomto kroku zadáte parametry pro každé nasazení kopie ukázky podrobného plánu.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **Přiřadit podrobný plán**.

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Předplatná**: vyberte jedno nebo více předplatných ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každé z nich pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: název je předem vyplněný na základě názvu podrobného plánu.
       Podle potřeby ho změňte nebo ponechte.
     - **Umístění**: Vyberte oblast, ve které se má spravovaná identita vytvořit. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice**podrobného plánu: vyberte **publikovanou** verzi vaší kopie ukázky podrobného plánu.

   - Zamknout přiřazení

     Vyberte nastavení uzamčení podrobného plánu pro vaše prostředí. Další informace naleznete v tématu [uzamčení zdrojů plánu](../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí _systém přiřazenou_ možnost spravovaná identita.

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, ve kterém jsou definovány. Tyto parametry jsou [dynamické parametry](../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte **Přiřadit** v dolní části stránky. Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure Blueprints a integrované ukázky podrobného plánu jsou **bezplatné**. U prostředků Azure se [cena stanovuje podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

### <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktů podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Description|
|-|-|-|-|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásady|Seznam uživatelů vyloučených ze skupiny správců virtuálních počítačů s Windows|Středníkem oddělený seznam členů, kteří by měli být vyloučení v místní skupině Administrators. Např.: Správce; myUser1; myUser2|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásady|Seznam uživatelů, které musí být zahrnuté ve skupině Správci virtuálních počítačů s Windows|Středníkem oddělený seznam členů, kteří by měli být zahrnutí do místní skupiny Administrators. Např.: Správce; myUser1; myUser2|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásady|Seznam uživatelů, které skupina správců virtuálních počítačů s Windows smí zahrnovat *jenom*|Středníkem oddělený seznam všech očekávaných členů místní skupiny Administrators. Např.: Správce; myUser1; myUser2|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásady|Seznam oblastí, kde by měla být povolena Network Watcher|Úplný seznam oblastí můžete zobrazit Get-AzLocation|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásady|Virtuální síť, ve které by se měly připojit virtuální počítače|Příklad:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroupName/providers/Microsoft.Network/virtualNetworks/Name|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásady|Síťová brána, kterou by měly virtuální sítě používat|Příklad:/subscriptions/YourSubscriptionId/resourceGroups/YourResourceGroup/providers/Microsoft.Network/virtualNetworkGateways/Name|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásady|Seznam ID pracovních prostorů, kde se mají Log Analytics agenti připojit|Středníkem oddělený seznam ID pracovních prostorů, ke kterým by měl být agent Log Analytics připojený|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásady|Seznam typů prostředků, které by měly mít povolené diagnostické protokoly|Auditování nastavení diagnostiky pro vybrané typy prostředků|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásady|Nejnovější verze PHP|Nejnovější podporovaná verze PHP pro App Services|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásady|Nejnovější verze Java|Nejnovější podporovaná verze Java pro App Services|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásady|Nejnovější verze Windows Pythonu|Nejnovější podporovaná verze Pythonu pro App Services|
|Auditování doporučení srovnávacích testů zabezpečení Azure a nasazení specifických podpůrných rozšíření virtuálních počítačů|Přiřazení zásady|Nejnovější verze Pythonu pro Linux|Nejnovější podporovaná verze Pythonu pro App Services|

## <a name="next-steps"></a>Další kroky

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../how-to/update-existing-assignments.md)