---
title: Ukázka podrobného plánu úrovně dopadu doD 4
description: Nasazení kroků pro ukázku podrobného plánu Úrovně dopadu dod 4 včetně podrobností o parametru artefaktu podrobného plánu.
ms.date: 03/06/2020
ms.topic: sample
ms.openlocfilehash: c92e24327bc1879b8fea89cf85a50f845e473810
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "79415304"
---
# <a name="deploy-the-dod-impact-level-4-blueprint-sample"></a>Nasazení ukázky podrobného plánu úrovně dopadu doD 4

Chcete-li nasadit ukázku podrobného plánu oddělení ochrany zabezpečení Azure (DoD IL4), je nutné provést následující kroky:

> [!div class="checklist"]
> - Vytvoření nového podrobného plánu z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free) než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvoření podrobného plánu z ukázky

Nejprve implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako startéru.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Na stránce **Začínáme** vlevo vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. Najděte ukázku podrobného plánu **Úrovně dopadu DoD 4** v části _Další ukázky_ a vyberte **Použít tuto ukázku**.

1. Zadejte _základy ukázky_ podrobného plánu:

   - **Název podrobného plánu**: Zadejte název pro kopii ukázky podrobného plánu Úrovně dopadu doD 4.
   - **Umístění definice**: Použijte tři tečky a vyberte skupinu pro správu, do které chcete uložit kopii vzorku.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Po dokončení revize ukázky podrobného plánu vyberte **Uložit koncept.**

## <a name="publish-the-sample-copy"></a>Publikování ukázkové kopie

Vaše kopie ukázky podrobného plánu byla nyní vytvořena ve vašem prostředí. Je vytvořen v režimu **konceptu** a musí být **publikovánpřed** jeho přiřazením a nasazením. Kopii ukázky podrobného plánu lze přizpůsobit vašemu prostředí a potřebám, ale tato změna ji může přesunout od zarovnání s ovládacími prvky Úrovně dopadu 4 DoD.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Definice podrobného plánu** vlevo. Pomocí filtrů vyhledejte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **Publikovat podrobný plán.** Na nové stránce vpravo zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud provedete změnu později. Zadejte **poznámky ke změně,** například "První verze publikovaná z ukázky podrobného plánu DoD IL4". Pak vdolní části stránky vyberte **Publikovat.**

## <a name="assign-the-sample-copy"></a>Přiřazení ukázkové kopie

Jakmile je kopie ukázky podrobného plánu úspěšně **publikována**, lze ji přiřadit k předplatnému v rámci skupiny pro správu, do které byla uložena. Tento krok je, kde jsou k dispozici parametry, aby každé nasazení kopie ukázky podrobného plánu jedinečné.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Definice podrobného plánu** vlevo. Pomocí filtrů vyhledejte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky definice podrobného plánu vyberte **Přiřadit podrobný plán.**

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Odběry:** Vyberte jedno nebo více předplatných, které jsou ve skupině pro správu, do které jste uložili kopii ukázky podrobného plánu. Pokud vyberete více než jedno předplatné, bude pro každé z nich vytvořeno přiřazení pomocí zadaných parametrů.
     - **Název přiřazení**: Název je předem vyplněný na základě názvu podrobného plánu.
       Změňte podle potřeby nebo odejděte tak, jak je.
     - **Umístění**: Vyberte oblast pro spravovanou identitu, která má být vytvořena. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [spravované identity pro prostředky Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice podrobného plánu**: Vyberte **publikovanou** verzi ukázky podrobného plánu.

   - Přiřazení zámku

     Vyberte nastavení zámku podrobného plánu pro vaše prostředí. Další informace naleznete v tématu [uzamčení zdrojů plánu](../../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí možnost spravované identity _přiřazenou systému._

   - Parametry artefaktů

     Parametry definované v této části platí pro artefakt, pod kterým je definován. Tyto parametry jsou [dynamické parametry,](../../concepts/parameters.md#dynamic-parameters) protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popisy naleznete v [tabulce parametrů artefaktu](#artifact-parameters-table).

1. Po zadání všech parametrů **vyberte** Přiřadit v dolní části stránky. Přiřazení podrobného plánu je vytvořen a artefakt nasazení začíná. Nasazení trvá zhruba hodinu. Chcete-li zkontrolovat stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure Blueprints a integrované ukázky podrobných plánů jsou **zdarma**. Prostředky Azure jsou [oceněny podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na spuštění prostředků nasazených v této ukázce podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktu

V následující tabulce je uveden seznam parametrů artefaktu podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|ID pracovního prostoru Analýzy protokolů, pro které by měly být virtuální počítače nakonfigurované|Toto je ID (GUID) pracovního prostoru Analýzy protokolů, pro které by měly být virtuální počítače nakonfigurovány.|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Seznam typů prostředků, které by měly mít povoleny diagnostické protokoly|Seznam typů prostředků, které mají být auditovány, pokud není povoleno nastavení diagnostického protokolu. Přijatelné hodnoty najdete na [schémata diagnostických protokolů Azure Monitor](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Seznam uživatelů, kteří by měli být vyloučeni ze skupiny Správci virtuálních počítačů systému Windows|Středník-oddělený seznam členů, které by měly být vyloučeny v místní skupině Administrators. Příklad: Správce; myUser1; myUser2|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Seznam uživatelů, kteří by měli být zahrnuti do skupiny Správci virtuálních počítačů systému Windows|Středník-oddělený seznam členů, které by měly být zahrnuty do místní skupiny Administrators. Příklad: Správce; myUser1; myUser2|
|\[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení v Linuxu (VMSS)|Přiřazení zásad|Pracovní prostor Log Analytics pro škálovací sady virtuálních zařízení v Linuxu (VMSS)|Pokud je tento pracovní prostor mimo rozsah přiřazení, musíte ručně udělit oprávnění přispěvatele analýzy protokolů (nebo podobně) k ID hlavního objektu přiřazení zásad.|
|\[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení v Linuxu (VMSS)|Přiřazení zásad|Volitelné: Seznam imitek virtuálních počítačů, které podporují operační systém Linux pro přidání do oboru|Prázdné pole může být použito k označení žádné volitelné parametry:\[\]|
|\[Náhled:\]Nasazení agenta analýzy protokolů pro virtuální počítače s Linuxem|Přiřazení zásad|Pracovní prostor Log Analytics pro virtuální počítače s Linuxem|Pokud je tento pracovní prostor mimo rozsah přiřazení, musíte ručně udělit oprávnění přispěvatele analýzy protokolů (nebo podobně) k ID hlavního objektu přiřazení zásad.|
|\[Náhled:\]Nasazení agenta analýzy protokolů pro virtuální počítače s Linuxem|Přiřazení zásad|Volitelné: Seznam imitek virtuálních počítačů, které podporují operační systém Linux pro přidání do oboru|Prázdné pole může být použito k označení žádné volitelné parametry:\[\]|
|\[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení windows (VMSS)|Přiřazení zásad|Pracovní prostor Log Analytics pro škálovací sady virtuálních vod systému Windows (VMSS)|Pokud je tento pracovní prostor mimo rozsah přiřazení, musíte ručně udělit oprávnění přispěvatele analýzy protokolů (nebo podobně) k ID hlavního objektu přiřazení zásad.|
|\[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení windows (VMSS)|Přiřazení zásad|Volitelné: Seznam iimages virtuálních aplikací, které podporují operační systém Windows přidat do oboru|Prázdné pole může být použito k označení žádné volitelné parametry:\[\]|
|\[Náhled\]: Nasazení agenta analýzy protokolů pro virtuální servery Windows|Přiřazení zásad|Pracovní prostor Log Analytics pro virtuální servery Windows|Pokud je tento pracovní prostor mimo rozsah přiřazení, musíte ručně udělit oprávnění přispěvatele analýzy protokolů (nebo podobně) k ID hlavního objektu přiřazení zásad.|
|\[Náhled\]: Nasazení agenta analýzy protokolů pro virtuální servery Windows|Přiřazení zásad|Volitelné: Seznam iimages virtuálních aplikací, které podporují operační systém Windows přidat do oboru|Prázdné pole může být použito k označení žádné volitelné parametry:\[\]|
|Nasazení rozšířené ochrany před hrozbami na účtech úložiště|Přiřazení zásad|Účinek|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|Nasazení auditování na serverech SQL|Přiřazení zásad|Hodnota ve dnech retenčního období (0 označuje neomezené uchovávání)|Retenční dny (nepovinné, 180 dní, pokud není uvedeno jinak)|
|Nasazení auditování na serverech SQL|Přiřazení zásad|Název skupiny prostředků pro účet úložiště pro auditování serveru SQL|Auditování zapisuje události databáze do protokolu auditu v účtu služby Azure Storage (účet úložiště se vytvoří v každé oblasti, kde se vytvoří SQL Server, který bude sdílen všemi servery v této oblasti). Důležité – pro správnou funkci auditování neodstraňujte ani nepřejmenovávejte skupinu prostředků nebo účty úložiště.|
|Nasazení diagnostických nastavení pro skupiny zabezpečení sítě|Přiřazení zásad|Předpona účtu úložiště pro diagnostiku skupiny zabezpečení sítě|Tato předpona bude kombinována s umístěním skupiny zabezpečení sítě a vytvoří název vytvořeného účtu úložiště.|
|Nasazení diagnostických nastavení pro skupiny zabezpečení sítě|Přiřazení zásad|Název skupiny prostředků pro účet úložiště pro diagnostiku skupiny zabezpečení sítě (musí existovat)|Skupina prostředků, ve které bude vytvořen účet úložiště. Tato skupina prostředků již musí existovat.|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Povolená umístění pro zdroje a skupiny zdrojů|Seznam umístění Azure, které vaše organizace může zadat při nasazování prostředků. Tato zadaný hodnota je také použita zásadou "Povolená umístění" v rámci iniciativy zásad.|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Posouzení ohrožení zabezpečení by mělo být povoleno ve vašich instancích spravovaných SQL.|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Posouzení ohrožení zabezpečení by mělo být povoleno na serverech SQL|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Posouzení ohrožení zabezpečení by mělo být povoleno na virtuálních počítačích|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Geograficky redundantní úložiště by mělo být povoleno pro účty úložiště.|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Geograficky redundantní záloha by měla být povolena pro Azure Database pro MariaDB|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Geograficky redundantní záloha by měla být povolena pro Azure Database for MySQL|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Geograficky redundantní záloha by měla být povolena pro Azure Database for PostgreSQL|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Pravidla skupiny zabezpečení sítě pro virtuální počítače s připojením k internetu by měla být posílena|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Webová aplikace by měla být přístupná pouze přes protokol HTTPS.|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Aplikace function by měla být přístupná pouze přes protokol HTTPS|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Z předplatného by měly být odebrány externí účty s oprávněními k zápisu.|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Z předplatného by měly být odebrány externí účty s oprávněním ke čtení.|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Z předplatného by měly být odebrány externí účty s oprávněními vlastníka.|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Zastaralé účty s oprávněními vlastníka by měly být odebrány z vašeho předplatného|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Zastaralé účty by měly být odebrány z vašeho předplatného|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|CORS by neměl povolit přístup ke webové aplikaci pro každý prostředek.|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Aktualizace systému na škálovacísady virtuálních strojů by měly být nainstalovány|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Vícefaktorové informace by měly být povoleny u účtů s oprávněními ke čtení v rámci vašeho předplatného.|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Vícefaktorové informace by měly být povoleny u účtů s oprávněními vlastníka k vašemu předplatnému.|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Vícefaktorové informace by měly být povoleny u účtů s oprávněními k zápisu v rámci vašeho předplatného.|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|\[Náhled:\]DoD úroveň dopadu 4|Přiřazení zásad|Pro Azure SQL Databases by měla být povolena dlouhodobá geograficky redundantní záloha.|Informace o účincích zásad najdete na webu [Understand Azure Policy Effects](../../../policy/concepts/effects.md).|
|Povolená umístění|Přiřazení zásad|Povolené lokality|Tyto zásady umožňují omezit umístění, která může vaše organizace zadat při nasazování prostředků. Můžete je využít k vynucování vašich požadavků na geografické dodržování předpisů.|
|Povolená umístění pro skupiny prostředků|Přiřazení zásad |Povolené lokality|Tato zásada umožňuje omezit umístění, ve kterých může vaše organizace vytvářet skupiny prostředků. Můžete je využít k vynucování vašich požadavků na geografické dodržování předpisů.|

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali kroky nasazení ukázky podrobného plánu Úrovně dopadu dod 4, navštivte následující články, kde se dozvíte o podrobném plánu a mapování ovládacích plánů:

> [!div class="nextstepaction"]
> [Podrobný plán úrovně dopadu doD 4 – přehled](./index.md)
> [Podrobné plán úrovně dopadu dodou 4 – mapování řízení](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si o [životním cyklu podrobného plánu](../../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../../how-to/update-existing-assignments.md).