---
title: Příklad nasazení FedRAMP pro střední plán
description: Nasaďte kroky pro ukázku FedRAMP pro střední plán včetně podrobností parametrů artefaktu podrobného plánu.
ms.date: 01/08/2021
ms.topic: sample
ms.openlocfilehash: 9fa92bebf6ef5829796c45e0e83070a85395caf8
ms.sourcegitcommit: 910a1a38711966cb171050db245fc3b22abc8c5f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100594687"
---
# <a name="deploy-the-fedramp-moderate-blueprint-sample"></a>Nasazení ukázky FedRAMP pro střední plán

K nasazení ukázky Azure modrotisky FedRAMP pro střední plán je potřeba provést tyto kroky:

> [!div class="checklist"]
> - Vytvořte nový podrobný plán z ukázky.
> - Kopii ukázky si označte jako **publikovanou**.
> - Přiřaďte kopii podrobného plánu k existujícímu předplatnému.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejprve implementujte ukázku podrobného plánu tak, že z ukázky vytvoříte nový podrobný plán ve svém prostředí.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vlevo na stránce **Začínáme** vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. V části _Další ukázky_ Najděte vzorek **FedRAMP mírná** podrobný plán a vyberte **použít tuto ukázku**.

1. Zadejte _základní informace_ o ukázce podrobného plánu:

   - **Název** podrobného plánu: zadejte název vaší kopie ukázky FedRAMP pro střední plán.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** dole na stránce.

1. Projděte si seznam artefaktů, které tvoří ukázku podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Až skončíte s kontrolou ukázky podrobného plánu, vyberte **Uložit koncept**.

## <a name="publish-the-sample-copy"></a>Publikování kopie ukázky

V prostředí máte teď vytvořenou kopii ukázky podrobného plánu. Kopie je vytvořená v režimu **Koncept** a než ji budete muset přiřadit a nasadit, musí být **publikovaná**. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo zarovnání s FedRAMP středními ovládacími prvky.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. Nahoře na stránce vyberte **Publikovat podrobný plán**. Napravo na nové stránce zadejte **verzi** kopie ukázky podrobného plánu. Tato vlastnost je užitečná, protože umožňuje pozdější úpravy. Zadejte **poznámky ke změnám** , jako je například první verze publikovaná z ukázky FedRAMP pro střední plán. Na konci stránky pak vyberte **Publikovat**.

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
     - **Umístění**: Vyberte oblast, ve které se má spravovaná identita vytvořit. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice** podrobného plánu: vyberte **publikovanou** verzi vaší kopie ukázky podrobného plánu.

   - Zamknout přiřazení

     Vyberte nastavení uzamčení podrobného plánu pro vaše prostředí. Další informace naleznete v tématu [uzamčení zdrojů plánu](../../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí _systém přiřazenou_ možnost spravovaná identita.

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, ve kterém jsou definovány. Tyto parametry jsou [dynamické parametry](../../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte **Přiřadit** v dolní části stránky. Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure Blueprints a integrované ukázky podrobného plánu jsou **bezplatné**. U prostředků Azure se [cena stanovuje podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktů podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Description|
|-|-|-|-|
|\[Verze Preview \] : FedRAMP moderované ovládací prvky a nasazení specifických rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásady|ID pracovního prostoru Log Analytics, pro který by se měly virtuální počítače nakonfigurovat|Toto je ID (GUID) Log Analyticsho pracovního prostoru, pro který by se měly virtuální počítače nakonfigurovat.|
|\[Verze Preview \] : FedRAMP moderované ovládací prvky a nasazení specifických rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásady|Seznam typů prostředků, které by měly mít povolené diagnostické protokoly|Seznam typů prostředků, které se mají auditovat v případě, že nastavení diagnostického protokolu není povolené. Přijatelné hodnoty najdete v [Azure monitor schématech diagnostických protokolů](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas).|
|\[Verze Preview \] : FedRAMP moderované ovládací prvky a nasazení specifických rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásady|Seznam uživatelů, kteří mají být vyloučeni ze skupiny správců virtuálních počítačů s Windows|Středníkem oddělený seznam členů, kteří by měli být vyloučení v místní skupině Administrators. Např.: Správce; myUser1; myUser2|
|\[Verze Preview \] : FedRAMP moderované ovládací prvky a nasazení specifických rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásady|Seznam uživatelů, které by měly být zahrnuté ve skupině Správci virtuálních počítačů s Windows|Středníkem oddělený seznam členů, kteří by měli být zahrnutí do místní skupiny Administrators. Např.: Správce; myUser1; myUser2|
|\[Verze Preview \] : nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)|Přiřazení zásady|Log Analytics pracovní prostor pro Linux VM Scale Sets (VMSS)|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Verze Preview \] : nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)|Přiřazení zásady|Volitelné: seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů: \[\]|
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux|Přiřazení zásady|Log Analytics pracovní prostor pro virtuální počítače se systémem Linux|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux|Přiřazení zásady|Volitelné: seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů: \[\]|
|\[Verze Preview \] : nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)|Přiřazení zásady|Log Analytics pracovní prostor pro Windows VM Scale Sets (VMSS)|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Verze Preview \] : nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)|Přiřazení zásady|Volitelné: seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů: \[\]|
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows|Přiřazení zásady|Log Analytics pracovní prostor pro virtuální počítače s Windows|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows|Přiřazení zásady|Volitelné: seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů: \[\]|
|Nasazení rozšířené ochrany před internetovými útoky na účty úložiště|Přiřazení zásady|Účinek|Informace o účincích na zásady najdete v [porozumět Azure Policych důsledcích](../../../policy/concepts/effects.md) .|
|Nasazení auditování na SQL serverech|Přiřazení zásady|Hodnota v dnech doby uchování (0 označuje neomezené uchovávání)|Počet dnů uchování (volitelné, 180 dní, pokud není zadaný)|
|Nasazení auditování na SQL serverech|Přiřazení zásady|Název skupiny prostředků pro účet úložiště pro auditování SQL serveru|Audit zapisuje události databáze do protokolu auditu ve vašem účtu Azure Storage (účet úložiště se vytvoří v každé oblasti, kde se vytvoří SQL Server, který bude sdílen všemi servery v této oblasti). Důležité: kvůli správnému fungování auditu neodstraňujte ani neměňte skupinu prostředků ani účty úložiště.|
|Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě|Přiřazení zásady|Předpona účtu úložiště pro diagnostiku skupiny zabezpečení sítě|Tato předpona bude kombinována s umístěním skupiny zabezpečení sítě, aby vytvořila název vytvořeného účtu úložiště.|
|Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě|Přiřazení zásady|Název skupiny prostředků pro účet úložiště pro diagnostiku skupiny zabezpečení sítě (musí existovat)|Skupina prostředků, ve které se bude účet úložiště vytvořit. Tato skupina prostředků už musí existovat.|

## <a name="next-steps"></a>Další kroky

Teď, když jste si prohlédli postup nasazení ukázky FedRAMP pro střední plán, si přečtěte následující články, kde se dozvíte o mapování podrobného plánu a řízení:

> [!div class="nextstepaction"]
> [FedRAMP – střední podrobný plán – přehled](./index.md) 
>  [FedRAMP střední podrobný plán – mapování ovládacích prvků](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
