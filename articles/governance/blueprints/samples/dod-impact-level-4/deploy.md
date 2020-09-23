---
title: Ukázka podrobného plánu úrovně účinku DoD. 4
description: Nasaďte kroky pro ukázku podrobného plánu na úrovni DoD – 4, včetně podrobností parametrů artefaktu podrobného plánu.
ms.date: 09/17/2020
ms.topic: sample
ms.openlocfilehash: 7ab2e5967031b52bcad7c1b6f38b546cb8a7eb86
ms.sourcegitcommit: bdd5c76457b0f0504f4f679a316b959dcfabf1ef
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/22/2020
ms.locfileid: "90978405"
---
# <a name="deploy-the-dod-impact-level-4-blueprint-sample"></a>Nasazení ukázky podrobného plánu úrovně dopadu DoD. 4

Pro nasazení ukázky podrobného plánu úrovně dopadu na IL4 (DoD. 4) pro Azure modrotisky je potřeba provést tyto kroky:

> [!div class="checklist"]
> - Vytvořit nový podrobný plán z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejdřív implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako Starter.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na stránce **Začínáme** na levé straně vyberte v části _vytvořit podrobný plán_tlačítko **vytvořit** .

1. V části _Další ukázky_ Najděte ukázku podrobného plánu pro **Dod. 4** a vyberte **použít tuto ukázku**.

1. Zadejte _základy_ ukázky podrobného plánu:

   - **Název**podrobného plánu: zadejte název vaší kopie ukázkového plánu úrovně účinku dod. 4.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _artefakty_ v horní části stránky nebo **Další: artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnohé z artefaktů mají parametry, které budeme definovat později. Po dokončení kontroly ukázkového plánu vyberte **Uložit koncept** .

## <a name="publish-the-sample-copy"></a>Publikovat ukázkovou kopii

Vaše kopie ukázky podrobného plánu se teď vytvořila ve vašem prostředí. Je vytvořená v režimu **konceptu** a musí být **publikována** před tím, než bude možné ji přiřadit a nasadit. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo zarovnání s ovládacími prvky úrovně dopadu na změnu úrovně 4.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **publikovat podrobný plán** . Na stránce Nová na pravé straně zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud uděláte změnu později. Zadejte **poznámky ke změnám** , jako je například první verze publikovaná z ukázky DoD IL4 Details Sample. Potom v dolní části stránky vyberte **publikovat** .

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

|Název artefaktu|Typ artefaktu|Název parametru|Description|
|-|-|-|-|
|Povolená umístění|Přiřazení zásad|Povolené lokality|Tyto zásady umožňují omezit umístění, která může vaše organizace zadat při nasazování prostředků. Můžete je využít k vynucování vašich požadavků na geografické dodržování předpisů.|
|Povolená umístění pro skupiny prostředků|Přiřazení zásad |Povolené lokality|Tato zásada vám umožní omezit umístění, ve kterých může organizace vytvářet skupiny prostředků. Můžete je využít k vynucování vašich požadavků na geografické dodržování předpisů.|
|Nasazení auditování na SQL serverech|Přiřazení zásady|Hodnota v dnech doby uchování (0 označuje neomezené uchovávání)|Počet dnů uchování (volitelné, 180 dní, pokud není zadaný)|
|Nasazení auditování na SQL serverech|Přiřazení zásady|Název skupiny prostředků pro účet úložiště pro auditování SQL serveru|Audit zapisuje události databáze do protokolu auditu ve vašem účtu Azure Storage (účet úložiště se vytvoří v každé oblasti, kde se vytvoří SQL Server, který bude sdílen všemi servery v této oblasti). Důležité: kvůli správnému fungování auditu neodstraňujte ani neměňte skupinu prostředků ani účty úložiště.|
|Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě|Přiřazení zásady|Předpona účtu úložiště pro diagnostiku skupiny zabezpečení sítě|Tato předpona bude kombinována s umístěním skupiny zabezpečení sítě, aby vytvořila název vytvořeného účtu úložiště.|
|Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě|Přiřazení zásady|Název skupiny prostředků pro účet úložiště pro diagnostiku skupiny zabezpečení sítě (musí existovat)|Skupina prostředků, ve které se bude účet úložiště vytvořit. Tato skupina prostředků už musí existovat.|
|Nasazení agenta Log Analytics pro Linux Virtual Machine Scale Sets|Přiřazení zásady|Log Analytics pracovní prostor pro systémy Linux Virtual Machine Scale Sets|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|Nasazení agenta Log Analytics pro Linux Virtual Machine Scale Sets|Přiřazení zásady|Volitelné: seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů: \[\]|
|Nasazení agenta Log Analytics pro virtuální počítače se systémem Linux|Přiřazení zásady|Log Analytics pracovní prostor pro virtuální počítače se systémem Linux|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|Nasazení agenta Log Analytics pro virtuální počítače se systémem Linux|Přiřazení zásady|Volitelné: seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů: \[\]|
|Nasazení agenta Log Analytics pro Windows Virtual Machine Scale Sets|Přiřazení zásady|Log Analytics pracovní prostor pro Windows Virtual Machine Scale Sets|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|Nasazení agenta Log Analytics pro Windows Virtual Machine Scale Sets|Přiřazení zásady|Volitelné: seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů: \[\]|
|Nasazení agenta Log Analytics pro virtuální počítače s Windows|Přiřazení zásady|Log Analytics pracovní prostor pro virtuální počítače s Windows|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|Nasazení agenta Log Analytics pro virtuální počítače s Windows|Přiřazení zásady|Volitelné: seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů: \[\]|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Členové, kteří mají být zahrnuti do místní skupiny Administrators|Středníkem oddělený seznam členů, kteří by měli být vyloučení v místní skupině Administrators. Např.: Správce; myUser1; myUser2|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Členové, kteří mají být vyloučeni v místní skupině Administrators|Středníkem oddělený seznam členů, kteří by měli být zahrnutí do místní skupiny Administrators. Např.: Správce; myUser1; myUser2|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Seznam typů prostředků, které by měly mít povolené diagnostické protokoly|Seznam typů prostředků, které se mají auditovat v případě, že nastavení diagnostického protokolu není povolené. Přijatelné hodnoty najdete v [Azure monitor schématech diagnostických protokolů](../../../../azure-monitor/platform/resource-logs-schema.md#service-specific-schemas).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|ID pracovního prostoru Log Analytics, pro který by se měly virtuální počítače nakonfigurovat|Toto je ID (GUID) Log Analyticsho pracovního prostoru, pro který by se měly virtuální počítače nakonfigurovat.|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Pro databáze SQL Azure by mělo být povolené dlouhodobé geograficky redundantní zálohování.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Pro účty úložiště by mělo být povoleno geograficky redundantní úložiště.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Pro Azure Database for MySQL by měla být povolená geograficky redundantní záloha.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Pro Azure Database for PostgreSQL by měla být povolená geograficky redundantní záloha.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Webová aplikace by měla být přístupná jen přes protokol HTTPS|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Function App by měl být přístupný jenom přes HTTPS|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Zastaralé účty by se měly odebírat z předplatného.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|CORS by neměl všem prostředkům umožňovat přístup k vaší webové aplikaci|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Náhled \] : úroveň dopadu DoD 4|Přiřazení zásady|Pro účty s oprávněním k zápisu do vašeho předplatného by se měla povolit vícefaktorové ověřování.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|

## <a name="next-steps"></a>Další kroky

Teď, když jste si prohlédli postup nasazení ukázky podrobného plánu o dopadu DoD. 4, Projděte si následující články, kde se dozvíte o mapování podrobných plánů a ovládacích prvků:

> [!div class="nextstepaction"]
> [Úroveň dopadu dod. 4 podrobný plán – přehled](./index.md) 
>  [Úroveň dopadu dod. 4 – mapování ovládacích prvků](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
