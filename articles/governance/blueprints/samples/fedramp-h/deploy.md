---
title: Ukázka nasazení FedRAMP High details
description: Nasaďte kroky pro ukázku FedRAMP High Details, včetně podrobností parametrů artefaktu podrobného plánu.
ms.date: 01/31/2020
ms.topic: sample
ms.openlocfilehash: 96b033c153789b3edbfb6a197981fe3d56f12076
ms.sourcegitcommit: 581aaca8956b1717b7bc1c1d7710c782c22e6320
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 06/29/2020
ms.locfileid: "85517130"
---
# <a name="deploy-the-fedramp-high-blueprint-sample"></a>Nasazení ukázky FedRAMP High details

K nasazení ukázky Azure modrotisky FedRAMP High Details je potřeba provést tyto kroky:

> [!div class="checklist"]
> - Vytvořit nový podrobný plán z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, [vytvořte si bezplatný účet](https://azure.microsoft.com/free), ještě než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejdřív implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako Starter.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na stránce **Začínáme** na levé straně vyberte v části _vytvořit podrobný plán_tlačítko **vytvořit** .

1. V části _Další ukázky_ Najděte ukázku **FedRAMP High** Details a vyberte **použít tuto ukázku**.

1. Zadejte _základy_ ukázky podrobného plánu:

   - **Název**podrobného plánu: zadejte název vaší kopie ukázky FedRAMP High detailal.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _artefakty_ v horní části stránky nebo **Další: artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnohé z artefaktů mají parametry, které budeme definovat později. Po dokončení kontroly ukázkového plánu vyberte **Uložit koncept** .

## <a name="publish-the-sample-copy"></a>Publikovat ukázkovou kopii

Vaše kopie ukázky podrobného plánu se teď vytvořila ve vašem prostředí. Je vytvořená v režimu **konceptu** a musí být **publikována** před tím, než bude možné ji přiřadit a nasadit. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo zarovnání s FedRAMP vysokými ovládacími prvky.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **publikovat podrobný plán** . Na stránce Nová na pravé straně zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud uděláte změnu později. Zadejte **poznámky ke změnám** , například "první verze publikovaná z ukázky FedRAMP High details." Potom v dolní části stránky vyberte **publikovat** .

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
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|ID pracovního prostoru Log Analytics, pro který by se měly virtuální počítače nakonfigurovat|Toto je ID (GUID) Log Analyticsho pracovního prostoru, pro který by se měly virtuální počítače nakonfigurovat.|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Seznam typů prostředků, které by měly mít povolené diagnostické protokoly|Seznam typů prostředků, které se mají auditovat v případě, že nastavení diagnostického protokolu není povolené. Přijatelné hodnoty najdete v [Azure monitor schématech diagnostických protokolů](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Seznam uživatelů, kteří mají být vyloučeni ze skupiny správců virtuálních počítačů s Windows|Středníkem oddělený seznam členů, kteří by měli být vyloučení v místní skupině Administrators. Např.: Správce; myUser1; myUser2|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Seznam uživatelů, které by měly být zahrnuté ve skupině Správci virtuálních počítačů s Windows|Středníkem oddělený seznam členů, kteří by měli být zahrnutí do místní skupiny Administrators. Např.: Správce; myUser1; myUser2|
|\[Verze Preview \] : nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)|Přiřazení zásad|Log Analytics pracovní prostor pro Linux VM Scale Sets (VMSS)|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Verze Preview \] : nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)|Přiřazení zásad|Volitelné: seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů:\[\]|
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux|Přiřazení zásad|Log Analytics pracovní prostor pro virtuální počítače se systémem Linux|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux|Přiřazení zásad|Volitelné: seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů:\[\]|
|\[Verze Preview \] : nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)|Přiřazení zásad|Log Analytics pracovní prostor pro Windows VM Scale Sets (VMSS)|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Verze Preview \] : nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)|Přiřazení zásad|Volitelné: seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů:\[\]|
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows|Přiřazení zásad|Log Analytics pracovní prostor pro virtuální počítače s Windows|Pokud je tento pracovní prostor mimo rozsah přiřazení, je nutné ručně udělit oprávnění "Log Analytics přispěvatele" (nebo podobné) ID objektu zabezpečení přiřazení zásad.|
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows|Přiřazení zásad|Volitelné: seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Prázdné pole se dá použít k označení žádných volitelných parametrů:\[\]|
|Nasazení rozšířené ochrany před internetovými útoky na účty úložiště|Přiřazení zásad|Efekt|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|Nasazení auditování na SQL serverech|Přiřazení zásad|Hodnota v dnech doby uchování (0 označuje neomezené uchovávání)|Počet dnů uchování (volitelné, 180 dní, pokud není zadaný)|
|Nasazení auditování na SQL serverech|Přiřazení zásad|Název skupiny prostředků pro účet úložiště pro auditování SQL serveru|Audit zapisuje události databáze do protokolu auditu ve vašem účtu Azure Storage (účet úložiště se vytvoří v každé oblasti, kde se vytvoří SQL Server, který bude sdílen všemi servery v této oblasti). Důležité: kvůli správnému fungování auditu neodstraňujte ani neměňte skupinu prostředků ani účty úložiště.|
|Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě|Přiřazení zásad|Předpona účtu úložiště pro diagnostiku skupiny zabezpečení sítě|Tato předpona bude kombinována s umístěním skupiny zabezpečení sítě, aby vytvořila název vytvořeného účtu úložiště.|
|Nasadit nastavení diagnostiky pro skupiny zabezpečení sítě|Přiřazení zásad|Název skupiny prostředků pro účet úložiště pro diagnostiku skupiny zabezpečení sítě (musí existovat)|Skupina prostředků, ve které se bude účet úložiště vytvořit. Tato skupina prostředků už musí existovat.|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Povolená umístění pro prostředky a skupiny prostředků|Seznam umístění Azure, která může vaše organizace zadat při nasazování prostředků. Tato poskytnutá hodnota se používá taky v rámci zásad "povolené umístění" v rámci této iniciativy.|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Na spravovaných instancích SQL by mělo být povolené posouzení ohrožení zabezpečení|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Na vašich serverech SQL by mělo být povolené posouzení ohrožení zabezpečení|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Posouzení ohrožení zabezpečení by mělo být povoleno na Virtual Machines|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Pro účty úložiště by mělo být povoleno geograficky redundantní úložiště.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Pro Azure Database for MariaDB by měla být povolená geograficky redundantní záloha.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Pro Azure Database for MySQL by měla být povolená geograficky redundantní záloha.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Pro Azure Database for PostgreSQL by měla být povolená geograficky redundantní záloha.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Pravidla skupiny zabezpečení sítě pro virtuální počítače s přístupem k Internetu by měla být zesílená.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Webová aplikace by měla být přístupná jen přes protokol HTTPS|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Function App by měl být přístupný jenom přes HTTPS|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Z vašeho předplatného byste měli odebrat externí účty s oprávněním k zápisu.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Z vašeho předplatného by se měly odebrat externí účty s oprávněním ke čtení.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Z vašeho předplatného byste měli odebrat externí účty s oprávněním vlastníka.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Zastaralé účty s oprávněním vlastníka by se měly odebrat z vašeho předplatného.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Zastaralé účty by se měly odebírat z předplatného.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|CORS by neměl všem prostředkům umožňovat přístup k vaší webové aplikaci|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Musí být nainstalované aktualizace systému ve virtuálních počítačích Virtual Machine Scale Sets.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Pro účty s oprávněním ke čtení vašeho předplatného by se měla povolit vícefaktorové ověřování.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Pro účty s oprávněním vlastníka pro vaše předplatné by se měla povolit vícefaktorové ověřování.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Pro účty s oprávněním k zápisu do vašeho předplatného by se měla povolit vícefaktorové ověřování.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|
|\[Verze Preview \] : Auditovat FedRAMP High Controls a nasazovat specifická rozšíření virtuálních počítačů pro podporu požadavků na audit|Přiřazení zásad|Pro databáze SQL Azure by mělo být povolené dlouhodobé geograficky redundantní zálohování.|Informace o účincích na zásady najdete v [porozumět Azure Policych efektech](../../../policy/concepts/effects.md).|


## <a name="next-steps"></a>Další kroky

Teď, když jste si prohlédli postup nasazení ukázky FedRAMP High Details, si přečtěte následující články, kde se dozvíte o mapování podrobného plánu a řízení:

> [!div class="nextstepaction"]
> [FedRAMP vysoký podrobný plán – přehled](./index.md) 
>  [Mapování ovládacích prvků FedRAMP High Details](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)