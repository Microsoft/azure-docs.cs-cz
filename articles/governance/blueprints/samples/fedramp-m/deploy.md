---
title: Příklad nasazení FedRAMP pro střední plán
description: Nasaďte kroky pro ukázku FedRAMP pro střední plán včetně podrobností parametrů artefaktu podrobného plánu.
ms.date: 10/31/2019
ms.topic: sample
ms.openlocfilehash: f38aad3adb625a0874d6287d25b1fe60f2579291
ms.sourcegitcommit: a678f00c020f50efa9178392cd0f1ac34a86b767
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2019
ms.locfileid: "74544492"
---
# <a name="deploy-the-fedramp-moderate-blueprint-sample"></a>Nasazení ukázky FedRAMP pro střední plán

K nasazení ukázky Azure modrotisky FedRAMP pro střední plán je potřeba provést tyto kroky:

> [!div class="checklist"]
> - Vytvořit nový podrobný plán z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejdřív implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako Starter.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na stránce **Začínáme** na levé straně vyberte v části _vytvořit podrobný plán_tlačítko **vytvořit** .

1. V části _Další ukázky_ Najděte vzorek **FedRAMP mírná** podrobný plán a vyberte **použít tuto ukázku**.

1. Zadejte _základy_ ukázky podrobného plánu:

   - **Název**podrobného plánu: zadejte název vaší kopie ukázky FedRAMP pro střední plán.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _artefakty_ v horní části stránky nebo **Další: artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnohé z artefaktů mají parametry, které budeme definovat později. Po dokončení kontroly ukázkového plánu vyberte **Uložit koncept** .

## <a name="publish-the-sample-copy"></a>Publikovat ukázkovou kopii

Vaše kopie ukázky podrobného plánu se teď vytvořila ve vašem prostředí. Je vytvořená v režimu **konceptu** a musí být **publikována** před tím, než bude možné ji přiřadit a nasadit. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo zarovnání s FedRAMP středními ovládacími prvky.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **publikovat podrobný plán** . Na stránce Nová na pravé straně zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud uděláte změnu později. Zadejte **poznámky ke změnám** , jako je například první verze publikovaná z ukázky FedRAMP pro střední plán. Potom v dolní části stránky vyberte **publikovat** .

## <a name="assign-the-sample-copy"></a>Přiřadit ukázkovou kopii

Po úspěšném **publikování**kopie ukázky podrobného plánu je možné ji přiřadit k předplatnému v rámci skupiny pro správu, do které byl uložen. V tomto kroku je uvedeno, že jsou k dispozici parametry pro každé nasazení kopie ukázky podrobného plánu.

1. V levém podokně vyberte **všechny služby** . Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **přiřadit podrobný plán** .

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Předplatná**: vyberte jedno nebo více předplatných ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každý pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: název je předem vyplněný na základě názvu podrobného plánu.
       Změňte podle potřeby nebo ponechte tak, jak je.
     - **Umístění**: Vyberte oblast, ve které se má spravovaná identita vytvořit. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [Spravované identity pro zdroje Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
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
|\[Preview\]: Auditovat FedRAMP moderované ovládací prvky a nasazovat konkrétní rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|ID pracovního prostoru Log Analytics, pro který by se měly virtuální počítače nakonfigurovat|Toto je ID (GUID) Log Analyticsho pracovního prostoru, pro který by se měly virtuální počítače nakonfigurovat.|
|\[Preview\]: Auditovat FedRAMP moderované ovládací prvky a nasazovat konkrétní rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Seznam typů prostředků, které by měly mít povolené diagnostické protokoly|Seznam typů prostředků, které se mají auditovat v případě, že nastavení diagnostického protokolu není povolené. Přijatelné hodnoty najdete v [Azure monitor schématech diagnostických protokolů](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Preview\]: Auditovat FedRAMP moderované ovládací prvky a nasazovat konkrétní rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Seznam uživatelů, kteří mají být vyloučeni ze skupiny správců virtuálních počítačů s Windows|Středníkem oddělený seznam členů, kteří by měli být vyloučení v místní skupině Administrators. Např.: Správce; myUser1; myUser2|
|\[Preview\]: Auditovat FedRAMP moderované ovládací prvky a nasazovat konkrétní rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Seznam uživatelů, které by měly být zahrnuté ve skupině Správci virtuálních počítačů s Windows|Středníkem oddělený seznam členů, kteří by měli být zahrnutí do místní skupiny Administrators. Např.: Správce; myUser1; myUser2|
|\[Preview\]: nasazení Log Analytics agenta pro Linux VM Scale Sets (VMSS)|Přiřazení zásad|Log Analytics pracovní prostor pro Linux VM Scale Sets (VMSS)|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Preview\]: nasazení Log Analytics agenta pro Linux VM Scale Sets (VMSS)|Přiřazení zásad|Volitelné: seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů: \[\]|
|\[Preview\]: nasazení Log Analytics agenta pro virtuální počítače se systémem Linux|Přiřazení zásad|Log Analytics pracovní prostor pro virtuální počítače se systémem Linux|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Preview\]: nasazení Log Analytics agenta pro virtuální počítače se systémem Linux|Přiřazení zásad|Volitelné: seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů: \[\]|
|\[Preview\]: nasazení Log Analytics agenta pro Windows VM Scale Sets (VMSS)|Přiřazení zásad|Log Analytics pracovní prostor pro Windows VM Scale Sets (VMSS)|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Preview\]: nasazení Log Analytics agenta pro Windows VM Scale Sets (VMSS)|Přiřazení zásad|Volitelné: seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů: \[\]|
|\[Preview\]: nasazení Log Analytics agenta pro virtuální počítače s Windows|Přiřazení zásad|Log Analytics pracovní prostor pro virtuální počítače s Windows|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Preview\]: nasazení Log Analytics agenta pro virtuální počítače s Windows|Přiřazení zásad|Volitelné: seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů: \[\]|
|Nasazení rozšířené ochrany před internetovými útoky na účty úložiště|Přiřazení zásad|Efekt|Informace o účincích na zásady najdete v [porozumět Azure Policych důsledcích](../../../policy/concepts/effects.md) .|
|Nasazení auditování na SQL serverech|Přiřazení zásad|Hodnota v dnech doby uchování (0 označuje neomezené uchovávání)|Počet dnů uchování (volitelné, 180 dní, pokud není zadaný)|
|Nasazení auditování na SQL serverech|Přiřazení zásad|Název skupiny prostředků pro účet úložiště pro auditování SQL serveru|Audit zapisuje události databáze do protokolu auditu ve vašem účtu Azure Storage (účet úložiště se vytvoří v každé oblasti, kde se vytvoří SQL Server, který bude sdílen všemi servery v této oblasti). Důležité: kvůli správnému fungování auditu neodstraňujte ani neměňte skupinu prostředků ani účty úložiště.|
|Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě|Přiřazení zásad|Předpona účtu úložiště pro diagnostiku skupiny zabezpečení sítě|Tato předpona bude kombinována s umístěním skupiny zabezpečení sítě, aby vytvořila název vytvořeného účtu úložiště.|
|Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě|Přiřazení zásad|Název skupiny prostředků pro účet úložiště pro diagnostiku skupiny zabezpečení sítě (musí existovat)|Skupina prostředků, ve které se bude účet úložiště vytvořit. Tato skupina prostředků už musí existovat.|

## <a name="next-steps"></a>Další kroky

Teď, když jste si prohlédli postup nasazení ukázky FedRAMP pro střední plán, si přečtěte následující články, kde se dozvíte o mapování podrobného plánu a řízení:

> [!div class="nextstepaction"]
> [FedRAMP mírný podrobný plán – přehled](./index.md)
> [FedRAMP, střední mapování ovládacích prvků](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)