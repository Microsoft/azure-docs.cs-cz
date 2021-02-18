---
title: Příklad nasazení Azure Security benchmark Foundation Sample
description: Nasaďte kroky pro ukázkový plán Azure Security benchmark Foundation, včetně podrobností parametrů artefaktu podrobného plánu.
ms.date: 02/17/2020
ms.topic: sample
ms.openlocfilehash: aaaabc8767c6d80548a26d64d8557587180fb6f3
ms.sourcegitcommit: 97c48e630ec22edc12a0f8e4e592d1676323d7b0
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "101095300"
---
# <a name="deploy-the-azure-security-benchmark-foundation-blueprint-sample"></a>Nasazení ukázky plánu Azure Security benchmark Foundation

K nasazení ukázkového plánu Azure Security benchmark Foundation je potřeba provést tyto kroky:

> [!div class="checklist"]
> - Vytvořte nový podrobný plán z ukázky.
> - Kopii ukázky si označte jako **publikovanou**.
> - Přiřaďte kopii podrobného plánu k existujícímu předplatnému.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejprve implementujte ukázku podrobného plánu tak, že z ukázky vytvoříte nový podrobný plán ve svém prostředí.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vlevo na stránce **Začínáme** vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. V části _Další ukázky_ Najděte ukázkový plán **Azure Security benchmark Foundation** a vyberte **použít tuto ukázku**.

1. Zadejte _základní informace_ o ukázce podrobného plánu:

   - **Název** podrobného plánu: zadejte název vaší kopie ukázkového plánu Azure Security benchmark Foundation.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** dole na stránce.

1. Projděte si seznam artefaktů, které tvoří ukázku podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Až skončíte s kontrolou ukázky podrobného plánu, vyberte **Uložit koncept**.

## <a name="publish-the-sample-copy"></a>Publikování kopie ukázky

V prostředí máte teď vytvořenou kopii ukázky podrobného plánu. Kopie je vytvořená v režimu **Koncept** a než ji budete muset přiřadit a nasadit, musí být **publikovaná**. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo plán Azure Security benchmark Foundation.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. Nahoře na stránce vyberte **Publikovat podrobný plán**. Napravo na nové stránce zadejte **verzi** kopie ukázky podrobného plánu. Tato vlastnost je užitečná, protože umožňuje pozdější úpravy. Poskytněte **poznámky ke změnám** , jako je například první verze publikovaná z ukázkového plánu Azure Security benchmark Foundation. Na konci stránky pak vyberte **Publikovat**.

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
    
     - **Předpona prostředků a skupin prostředků**: Tento řetězec se používá jako předpona pro všechny názvy prostředků a skupin prostředků.
     - **Název centra**: název centra
     - **Uchovávání protokolů (dny)**: počet dní, po které se protokoly uchovávají; Když zadáte 0, zachovají se protokoly po neomezenou dobu.
     - **Nasadit centrum**: zadáním hodnoty true nebo false určete, jestli přiřazení nasadí součásti centra architektury.
     - **Umístění centra**: umístění pro skupinu prostředků centra
     - **Cílové IP adresy**: cílové IP adresy pro odchozí připojení; čárkami oddělený seznam IP adres nebo předpon rozsahu IP adres
     - **Network Watcher název**: název prostředku Network Watcher
     - **Network Watcher název skupiny prostředků**: název pro skupinu prostředků Network Watcher
     - **Povolit ochranu DDoS**: Pokud chcete určit, jestli je ve virtuální síti povolená DDoS Protection, zadejte ' true ' nebo ' false '.
     
    > [!NOTE] 
    > Pokud je už Network Watcher povolený, doporučuje se použít existující skupinu prostředků Network Watcher. Musíte taky zadat umístění pro existující skupinu prostředků Network Watcher pro parametr artefaktu **Network Watcher umístění skupiny prostředků**.

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, ve kterém jsou definovány. Tyto parametry jsou [dynamické parametry](../../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte **Přiřadit** v dolní části stránky. Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure Blueprints a integrované ukázky podrobného plánu jsou **bezplatné**. U prostředků Azure se [cena stanovuje podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Description|
|-|-|-|-|
|Skupina prostředků centra|Skupina prostředků|Název skupiny prostředků|Předpona-Zřetězes s názvem centra|
|Skupina prostředků centra|Skupina prostředků|Umístění skupiny prostředků|Uzamčené – používá umístění centra.|
|Šablona Azure Firewall|Šablona Resource Manageru|Azure Firewall privátní IP adresa||
|Šablona pro Log Analytics a diagnostiku Azure|Šablona Resource Manageru|Umístění pracovního prostoru Log Analytics|Umístění, kde se vytvoří pracovní prostor Log Analytics; Pokud `Get-AzLocation | Where-Object Providers -like 'Microsoft.OperationalInsights' | Select DisplayName` chcete zobrazit dostupné oblasti, spusťte v Azure PowersShell.|
|Šablona pro Log Analytics a diagnostiku Azure|Šablona Resource Manageru|ID účtu Azure Automation (volitelné)|ID prostředku účtu služby Automation; slouží k vytvoření propojené služby mezi Log Analytics a účtem Automation.|
|Šablona skupiny zabezpečení sítě Azure|Šablona Resource Manageru|Povolit protokoly toku NSG|Pokud chcete povolit nebo zakázat protokoly toku NSG, zadejte hodnotu true nebo false.|
|Šablona centra Azure Virtual Network|Šablona Resource Manageru|Předpona adresy virtuální sítě|Předpona adresy virtuální sítě pro virtuální síť centra|
|Šablona centra Azure Virtual Network|Šablona Resource Manageru|Předpona adresy podsítě brány firewall|Předpona adresy podsítě brány firewall pro virtuální síť centra|
|Šablona centra Azure Virtual Network|Šablona Resource Manageru|Předpona adresy podsítě bastionu|Předpona adresy podsítě bastionu pro virtuální síť centra|
|Šablona centra Azure Virtual Network|Šablona Resource Manageru|Předpona adresy podsítě brány|Předpona adresy podsítě brány pro virtuální síť centra|
|Šablona centra Azure Virtual Network|Šablona Resource Manageru|Předpona adresy podsítě pro správu|Předpona adresy podsítě pro správu pro virtuální síť centra|
|Šablona centra Azure Virtual Network|Šablona Resource Manageru|Předpona adresy podsítě pro pole odkazů|Předpona adresy podsítě pro pole odkazů pro virtuální síť centra|
|Šablona centra Azure Virtual Network|Šablona Resource Manageru|Názvy adres podsítě (volitelné)|Pole názvů podsítí, která se mají nasadit do virtuální sítě rozbočovače; například "SUBNET1", "podsíť subnet2"|
|Šablona centra Azure Virtual Network|Šablona Resource Manageru|Předpony adres podsítě (volitelné)|Pole předpon IP adres pro volitelné podsítě pro virtuální síť rozbočovače; například "10.0.7.0/24", "10.0.8.0/24"|
|Skupina prostředků paprsků|Skupina prostředků|Název skupiny prostředků|Locked-Zřetězes předponu s názvem paprsku|
|Skupina prostředků paprsků|Skupina prostředků|Umístění skupiny prostředků|Uzamčené – používá umístění centra.|
|Šablona Azure Virtual Network paprsku|Šablona Resource Manageru|Nasazení paprsku|Zadáním hodnoty true nebo false určete, jestli přiřazení nasadí součásti paprsků architektury.|
|Šablona Azure Virtual Network paprsku|Šablona Resource Manageru|ID předplatného centra|ID předplatného, kde se nasazuje centrum; Výchozí hodnota je předplatné, ve kterém se nachází definice podrobného plánu.|
|Šablona Azure Virtual Network paprsku|Šablona Resource Manageru|Název paprsku|Název paprsku|
|Šablona Azure Virtual Network paprsku|Šablona Resource Manageru|Předpona Virtual Network adresy|Virtual Network předpony adresy pro virtuální síť paprsků|
|Šablona Azure Virtual Network paprsku|Šablona Resource Manageru|Předpona adresy podsítě|Předpona adresy podsítě pro virtuální síť paprsků|
|Šablona Azure Virtual Network paprsku|Šablona Resource Manageru|Názvy adres podsítě (volitelné)|Pole názvů podsítí, která se mají nasadit do virtuální sítě s paprsky; například "SUBNET1", "podsíť subnet2"|
|Šablona Azure Virtual Network paprsku|Šablona Resource Manageru|Předpony adres podsítě (volitelné)|Pole předpon IP adres pro volitelné podsítě pro virtuální síť hvězdicové sítě; například "10.0.7.0/24", "10.0.8.0/24"|
|Šablona Azure Virtual Network paprsku|Šablona Resource Manageru|Nasazení paprsku|Zadáním hodnoty true nebo false určete, jestli přiřazení nasadí součásti paprsků architektury.|
|Šablona Azure Network Watcher|Šablona Resource Manageru|Umístění Network Watcher|Umístění prostředku Network Watcher|
|Šablona Azure Network Watcher|Šablona Resource Manageru|Network Watcher umístění skupiny prostředků|Pokud je už Network Watcher povolený, hodnota tohoto parametru se **musí** shodovat s názvem existující skupiny prostředků Network Watcher.|

## <a name="troubleshooting"></a>Řešení potíží

Pokud dojde k chybě `The resource group 'NetworkWatcherRG' failed to deploy due to the
following error: Invalid resource group location '{location}'. The Resource group already exists in
location '{location}'.` , ověřte, že parametr podrobného plánu **Network Watcher název skupiny prostředků** určuje existující Network Watcher název skupiny prostředků a že parametr artefaktu **Network Watcher umístění skupiny prostředků** určuje existující Network Watcher umístění skupiny prostředků.

## <a name="next-steps"></a>Další kroky

Teď, když jste si prohlédli postup nasazení ukázkového plánu Azure Security benchmark Foundation, najdete v následujícím článku informace o architektuře:

> [!div class="nextstepaction"]
> [Azure Security benchmark Foundation – přehled](./index.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
