---
title: Ukázka-SWIFT CSP – CSCF v2020 podrobný plán – postup nasazení
description: Nasaďte kroky ukázky programu SWIFT CSP – CSCF v2020 details.
author: DCtheGeek
ms.author: meladie
ms.date: 09/24/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: cb17cfb343c8adbe9ca145bd4eba9b8fc332f440
ms.sourcegitcommit: 3fa4384af35c64f6674f40e0d4128e1274083487
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/24/2019
ms.locfileid: "71220123"
---
# <a name="deploy-the-swift-csp-cscf-v2020-blueprint-sample"></a>Ukázka ukázky pro SWIFT CSP – CSCF v2020 podrobný plán

K nasazení ukázky Azure modrotisky SWIFT CSP – CSCF v2020 podrobného plánu je potřeba provést tyto kroky:

> [!div class="checklist"]
> - Vytvořit nový podrobný plán z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejdřív implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako Starter.

1. Vyberte **všechny služby** a vyhledejte a v levém podokně vyberte **zásady** . Na stránce **zásady** vyberte **plány**.

1. Na stránce **Začínáme** na levé straně vyberte v části _vytvořit podrobný plán_tlačítko **vytvořit** .

1. V části _Další ukázky_ Najděte ukázku " **SWIFT CSP-CSCF v2020** Details" a vyberte **použít tuto ukázku**.

1. Zadejte _základy_ ukázky podrobného plánu:

   - **Název**podrobného plánu: Zadejte název vaší kopie ukázky programu SWIFT CSP-CSCF v2020 details.
   - **Umístění definice**: Použijte tři tečky a vyberte skupinu pro správu, do které chcete kopii ukázky Uložit.

1. Vyberte kartu _artefakty_ v horní části stránky nebo **další: Artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnohé z artefaktů mají parametry, které budeme definovat později. Po dokončení kontroly ukázkového plánu vyberte **Uložit koncept** .

## <a name="publish-the-sample-copy"></a>Publikovat ukázkovou kopii

Vaše kopie ukázky podrobného plánu se teď vytvořila ve vašem prostředí. Je vytvořená v režimu konceptu a musí být publikována před tím, než bude možné ji přiřadit a nasadit. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout od zarovnání s ovládacími prvky SWIFT CSP-CSCF v2020.

1. Vyberte **všechny služby** a vyhledejte a v levém podokně vyberte **zásady** . Na stránce **zásady** vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **publikovat podrobný plán** . Na stránce Nová na pravé straně zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud uděláte změnu později. Zadejte **poznámky ke změnám** , jako je například "první verze publikovaná z ukázky SWIFT CSP – CSCF v2020 details." Potom v dolní části stránky vyberte **publikovat** .

## <a name="assign-the-sample-copy"></a>Přiřadit ukázkovou kopii

Po úspěšném **publikování**kopie ukázky podrobného plánu je možné ji přiřadit k předplatnému v rámci skupiny pro správu, do které byl uložen. V tomto kroku je uvedeno, že jsou k dispozici parametry pro každé nasazení kopie ukázky podrobného plánu.

1. Vyberte **všechny služby** a vyhledejte a v levém podokně vyberte **zásady** . Na stránce **zásady** vyberte **plány**.

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

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, ve kterém je definován. Tyto parametry jsou [dynamické parametry](../../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte v dolní části stránky **přiřadit** . Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure modrotisky a předdefinované ukázky podrobného plánu jsou **zdarma**. Ceny prostředků Azure se účtují [podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktu podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|\[Verze\]Preview: Audit SWIFT CSP – CSCF v2020 řídí a nasazují specifická rozšíření virtuálních počítačů pro podporu požadavků na audit.|Přiřazení zásad|Seznam typů prostředků, které by měly mít povolené diagnostické protokoly|Seznam typů prostředků, které se mají auditovat v případě, že nastavení diagnostického protokolu není povolené. Přijatelné hodnoty najdete v [Azure monitor schématech diagnostických protokolů](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Verze\]Preview: Audit SWIFT CSP – CSCF v2020 řídí a nasazují specifická rozšíření virtuálních počítačů pro podporu požadavků na audit.|Přiřazení zásad|ID připojených pracovních prostorů|Středníkem oddělený seznam ID pracovních prostorů, ke kterým by měl být agent Log Analytics připojený|
|\[Verze\]Preview: Audit SWIFT CSP – CSCF v2020 řídí a nasazují specifická rozšíření virtuálních počítačů pro podporu požadavků na audit.|Přiřazení zásad|Seznam uživatelů, které by měly být zahrnuté ve skupině Správci virtuálních počítačů s Windows|Středníkem oddělený seznam členů, kteří by měli být zahrnutí do místní skupiny Administrators. Příklad: Správce myUser1; myUser2|
|\[Verze\]Preview: Audit SWIFT CSP – CSCF v2020 řídí a nasazují specifická rozšíření virtuálních počítačů pro podporu požadavků na audit.|Přiřazení zásad|Název domény (FQDN)|Plně kvalifikovaný název domény (FQDN), ke kterému by se měly připojit virtuální počítače s Windows|
|\[Verze\]Preview: Nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)|Přiřazení zásad|Log Analytics pracovní prostor pro Linux VM Scale Sets (VMSS)|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Verze\]Preview: Nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů s podporovaným operačním systémem Linux, který se má přidat do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů:\[\]|
|\[Verze\]Preview: Nasazení agenta Log Analytics pro virtuální počítače se systémem Linux|Přiřazení zásad|Log Analytics pracovní prostor pro virtuální počítače se systémem Linux|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Verze\]Preview: Nasazení agenta Log Analytics pro virtuální počítače se systémem Linux|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů s podporovaným operačním systémem Linux, který se má přidat do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů:\[\]|
|\[Verze\]Preview: Nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)|Přiřazení zásad|Log Analytics pracovní prostor pro Windows VM Scale Sets (VMSS)|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Verze\]Preview: Nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů:\[\]|
|\[Verze\]Preview: Nasazení agenta Log Analytics pro virtuální počítače s Windows|Přiřazení zásad|Log Analytics pracovní prostor pro virtuální počítače s Windows|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Verze\]Preview: Nasazení agenta Log Analytics pro virtuální počítače s Windows|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů:\[\]|
|Nasazení rozšířené ochrany před internetovými útoky na účty úložiště|Přiřazení zásad|Efekt|Informace o účincích na zásady najdete v [porozumět Azure Policych důsledcích](../../../policy/concepts/effects.md) .|
|Nasazení auditování na serverech SQL|Přiřazení zásad|Hodnota v dnech doby uchování (0 označuje neomezené uchovávání)|Počet dnů uchování (volitelné, 180 dní, pokud není zadaný)|
|Nasazení auditování na serverech SQL|Přiřazení zásad|Název skupiny prostředků pro účet úložiště pro auditování SQL serveru|Audit zapisuje události databáze do protokolu auditu ve vašem účtu Azure Storage (účet úložiště se vytvoří v každé oblasti, kde se vytvoří SQL Server, který bude sdílen všemi servery v této oblasti). Důležité: kvůli správnému fungování auditu neodstraňujte ani neměňte skupinu prostředků ani účty úložiště.|
|Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě|Přiřazení zásad|Předpona účtu úložiště pro diagnostiku skupiny zabezpečení sítě|Tato předpona bude kombinována s umístěním skupiny zabezpečení sítě, aby vytvořila název vytvořeného účtu úložiště.|
|Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě|Přiřazení zásad|Název skupiny prostředků pro účet úložiště pro diagnostiku skupiny zabezpečení sítě (musí existovat)|Skupina prostředků, ve které se bude účet úložiště vytvořit. Tato skupina prostředků už musí existovat.|

## <a name="next-steps"></a>Další kroky

Teď, když jste si prohlédli postup nasazení ukázky "SWIFT CSP-CSCF v2020 Details", přečtěte si následující články, kde se dozvíte o mapování podrobného plánu a ovládacího prvku:

> [!div class="nextstepaction"]
> [SWIFT CSP – CSCF v2020 podrobný plán – přehled](./index.md)
> [SWIFT CSP – CSCF v2020 Details-Control Mapping](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
