---
title: Ukázka - plán úloh ISO 27001 ASE/SQL - kroky nasazení
description: Nasazení kroky vzorku podrobného plánu úlohy ISO 27001 App Service prostředí/službou SQL Database.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 78f608aedd53aa1071eaf88864f5a63f8f9e6072
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/08/2019
ms.locfileid: "59281195"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Nasazení ukázkové podrobného plánu úlohy ISO 27001 App Service prostředí/službou SQL Database

Abyste nasadili ukázkový plán úloh Azure plány ISO 27001 App Service prostředí/službou SQL Database, musíte provést následující kroky:

> [!div class="checklist"]
> - Nasazení [ISO 27001 Shared Services](../iso27001-shared/index.md) ukázkové podrobného plánu
> - Vytvořit nový plán, podle kterého z ukázky
> - Označte si kopii ukázky jako **publikováno**
> - Přiřazení kopie podrobný plán k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Nasazení ukázkové ISO 27001 Shared Services podrobného plánu

Předtím, než je možné nasadit tuto ukázku podrobného plánu, [ISO 27001 Shared Services](../iso27001-shared/index.md) ukázkové podrobného plánu se musí nasadit do cílového předplatného. Bez úspěšné nasazení ukázky podrobného plánu ISO 27001 sdílené služby, bude se tato ukázka podrobného plánu chybějící závislosti infrastruktury a selhání během nasazení.

> [!IMPORTANT]
> Tato ukázka podrobného plánu, musíte být přiřazeni ve stejném předplatném jako [ISO 27001 Shared Services](../iso27001-shared/index.md) ukázkové podrobného plánu.

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

První tak, že vytvoříte nový plán ve vašem prostředí pomocí ukázky jako starter implementaci vzorku podrobného plánu.

1. Vyberte **všechny služby** a vyhledejte a vyberte **zásady** v levém podokně. Na **zásady** stránce **plány**.

1. Z **Začínáme** stránky na levé straně, vyberte **vytvořit** tlačítko _vytvořit podrobný plán_.

1. Najít **ISO 27001: Úlohy služby ASE/SQL** podrobného plánu ukázky v části _Další ukázky_ a vyberte **fungování této ukázky**.

1. Zadejte _Základy_ vzorku podrobného plánu:

   - **Název podrobného plánu**: Zadejte název pro vaši kopii ukázky podrobného plánu úlohy ISO 27001 ASE/SQL.
   - **Umístění definice**: Použít na tři tečky a vyberte skupiny pro správu k uložení kopie vzorku.

1. Vyberte _artefakty_ kartě v horní části stránky nebo **Další: Artefakty** v dolní části stránky.

1. Projděte si seznam artefakty, které tvoří vzorku podrobného plánu. Řada artefakty, které má parametry, které budeme definovat později. Vyberte **uložit koncept** až dokončíte revize vzorku podrobného plánu.

## <a name="publish-the-sample-copy"></a>Publikování ukázkové kopírování

Vaši kopii ukázky podrobného plánu byla vytvořena ve vašem prostředí. Vytvoří se v **koncept** režimu a musí být **publikováno** předtím, než může být přiřazena a nasadit. Kopii ukázky podrobný plán lze přizpůsobit, aby vaše prostředí a požadavky, ale tato změna může přesunout mimo ISO 27001 se zásadami.

1. Vyberte **všechny služby** a vyhledejte a vyberte **zásady** v levém podokně. Na **zásady** stránce **plány**.

1. Vyberte **podrobný plán definice** na levé straně stránky. Pomocí filtru vyhledejte vaši kopii ukázky podrobného plánu a vyberte ji.

1. Vyberte **publikovat podrobný plán** v horní části stránky. Nová stránka na pravé straně, zadejte **verze** vaši kopii ukázky podrobného plánu. Tato vlastnost je užitečná pro, pokud provedete změny později. Zadejte **změnit poznámky** jako "první verze publikování z ukázky ISO 27001 podrobného plánu." Potom vyberte **publikovat** v dolní části stránky.

## <a name="assign-the-sample-copy"></a>Přiřazení kopie vzorku

Jakmile kopii ukázky podrobný plán byl úspěšně **publikováno**, je možné přiřadit na předplatné v rámci se uložila do skupiny pro správu. Tento krok je, kde jsou k dispozici parametry aby každého nasazení kopii ukázky podrobného plánu jedinečný.

1. Vyberte **všechny služby** a vyhledejte a vyberte **zásady** v levém podokně. Na **zásady** stránce **plány**.

1. Vyberte **podrobný plán definice** na levé straně stránky. Pomocí filtru vyhledejte vaši kopii ukázky podrobného plánu a vyberte ji.

1. Vyberte **přiřazení podrobného plánu** v horní části stránky definice podrobného plánu.

1. Zadejte hodnoty parametrů pro přiřazení podrobného plánu:

   - Základy

     - **Předplatná**: Vyberte jednu nebo několik předplatných, které jsou ve skupině pro správu můžete uložit kopii ukázku podrobného plánu. Pokud vyberete více než jedno předplatné, vytvoří se pro každý pomocí zadaných parametrů přiřazení.
     - **Název přiřazení**: Název je předem vyplněna za vás na základě názvu podrobný plán.
       Nechte, jak je změnit podle potřeby.
     - **Umístění**: Vyberte oblast pro spravovanou identitu, která má být vytvořen v. Podrobný plán Azure Blueprint používá tuto spravovanou identitu k aplikaci všech artefaktů v přiřazené podrobného plánu. Další informace najdete v tématu [Spravované identity pro zdroje Azure](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Verze definice podrobného plánu**: Vyberte **publikováno** verzi vaší kopii ukázky podrobného plánu.

   - Uzamknout přiřazení

     Vyberte nastavení pro vaše prostředí zámek podrobného plánu. Další informace naleznete v tématu [uzamčení zdrojů plánu](../../concepts/resource-locking.md).

   - Spravovaná identita

     Ponechte výchozí nastavení _přiřazenou systémem_ spravované identity možnost.

   - Parametry podrobného plánu

     Parametry definované v této části používají řadu artefaktů v definici podrobného plánu se dá zajistit konzistence.

     - **Název organizace**: Zadejte krátký název pro vaši organizaci. Tato vlastnost se používá především pro pojmenování prostředků.
     - **ID odběru služby sdílené**: ID předplatného kde [ISO 27001 Shared Services](../iso27001-shared/index.md) ukázkový plán, podle kterého je přiřazen.
     - **Předpona adresy podsítě výchozí**: Zápis CIDR pro výchozí podsíť virtuální sítě.
       Výchozí hodnota je _10.1.0.0/16_.
     - **Umístění úlohy**: Určuje umístění, jaké artefakty, které jsou nasazené na. Ne všechny služby jsou k dispozici ve všech umístěních. Artefakty nasazení tyto služby poskytují možnost parametr pro umístění k nasazení tohoto artefaktu, který se.

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, pod kterým je definována. Tyto parametry jsou [dynamických parametrů](../../concepts/parameters.md#dynamic-parameters) protože máte definovaný během přiřazování podrobný plán. Úplný seznam nebo artefaktů parametry a jejich popis najdete v tématu [artefaktů parametry tabulky](#artifact-parameters-table).

1. Po zadání všech parametrů se vyberte **přiřadit** v dolní části stránky. Přiřazení podrobného plánu se vytvoří a začne artefaktů nasazení. Nasazení trvá přibližně hodinu. Chcete-li zkontrolovat stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure plány a ukázky předdefinovaných podrobného plánu **zdarma**. Jsou prostředky Azure [ceny podle produktu](https://azure.microsoft.com/pricing/). Použití [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) odhadnout náklady na provozování prostředky nasazené v tomto příkladu podrobného plánu.

## <a name="artifact-parameters-table"></a>Parametry tabulky artefaktu

Následující tabulka obsahuje seznam podrobný plán artefaktů parametry:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|Skupina prostředků log Analytics|Skupina prostředků|Název|**Uzamčeno** – zřetězí **název organizace** s `-workload-log-rg` aby skupiny prostředků jedinečný.|
|Skupina prostředků log Analytics|Skupina prostředků|Umístění|**Uzamčeno** -používá parametr podrobného plánu.|
|Šablona pro Log Analytics|Šablona Resource Manageru|Úroveň služeb|Nastaví úroveň pracovního prostoru Log Analytics. Výchozí hodnota je _PerNode_.|
|Šablona pro Log Analytics|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona pro Log Analytics|Šablona Resource Manageru|Umístění|Oblasti používané pro vytvoření pracovního prostoru Log Analytics. Výchozí hodnota je _USA – západ 2_.|
|Skupina síťových prostředků|Skupina prostředků|Název|**Uzamčeno** – zřetězí **název organizace** s `-workload-net-rg` aby skupiny prostředků jedinečný.|
|Skupina síťových prostředků|Skupina prostředků|Umístění|**Uzamčeno** -používá parametr podrobného plánu.|
|Šablona skupiny zabezpečení sítě|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona pro virtuální síť a tabulku směrování|Šablona Resource Manageru|Privátní IP adresa brány Azure Firewall|Nakonfiguruje privátní IP adresu [brány Azure](../../../../firewall/overview.md). Musí být součástí notaci CIDR definované v _ISO 27001: Sdílené služby_ artefaktů parametr **předponu adresy podsítě brány Firewall Azure**. Výchozí hodnota je _10.0.4.4_.|
|Šablona pro virtuální síť a tabulku směrování|Šablona Resource Manageru|ID předplatného sdílených služeb|Hodnota slouží k povolení Partnerský vztah mezi sdílené služby a úlohy.|
|Šablona pro virtuální síť a tabulku směrování|Šablona Resource Manageru|Předpona adresy virtuální sítě|Zápis CIDR pro virtuální síť. Výchozí hodnota je _10.1.0.0/16_.|
|Šablona pro virtuální síť a tabulku směrování|Šablona Resource Manageru|Výchozí předpona adresy podsítě|Zápis CIDR pro výchozí podsíť virtuální sítě. Výchozí hodnota je _10.1.0.0/16_.|
|Šablona pro virtuální síť a tabulku směrování|Šablona Resource Manageru|Přidá IP adresu|IP adresa z první virtuální počítač přidá. Tato hodnota se používá jako vlastní DNS virtuální sítě.|
|Skupina prostředků trezoru klíčů|Skupina prostředků|Název|**Uzamčeno** – zřetězí **název organizace** s `-workload-kv-rg` aby skupiny prostředků jedinečný.|
|Skupina prostředků trezoru klíčů|Skupina prostředků|Umístění|**Uzamčeno** -používá parametr podrobného plánu.|
|Šablona pro Key Vault|Šablona Resource Manageru|ID objektu AAD|Identifikátor objektu AAD účtu, který vyžaduje přístup k instanci služby Key Vault. Žádné výchozí hodnoty a nemůže být ponecháno prázdné. K vyhledání této hodnoty na webu Azure Portal, vyhledejte a vyberte "Uživatelé" v části _služby_. Použití _název_ pole k filtrování pro název účtu a vyberte tento účet. Na _profilu uživatele_ stránky, vyberte ikonu "Kliknutím zkopírujete" vedle _ID objektu_.|
|Šablona pro Key Vault|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona pro Key Vault|Šablona Resource Manageru|Skladová jednotka (SKU) pro Key Vault|Určuje SKU služby Key Vault, který je vytvořen. Výchozí hodnota je _Premium_.|
|Šablona pro Key Vault|Šablona Resource Manageru|Uživatelské jméno správce Azure SQL Serveru|Uživatelské jméno používané pro přístup k serveru SQL Azure. Musí odpovídat stejnou hodnotu vlastnosti v **šablony Azure SQL Database**. Výchozí hodnota je _správce sql_.|
|Skupina prostředků Azure SQL Database|Skupina prostředků|Název|**Uzamčeno** – zřetězí **název organizace** s `-workload-azsql-rg` aby skupiny prostředků jedinečný.|
|Skupina prostředků Azure SQL Database|Skupina prostředků|Umístění|**Uzamčeno** -používá parametr podrobného plánu.|
|Šablona Microsoft Azure SQL Database|Šablona Resource Manageru|Uživatelské jméno správce Azure SQL Serveru|Uživatelské jméno pro Server Azure SQL. Musí odpovídat stejnou hodnotu vlastnosti v **šablony služby Key Vault**. Výchozí hodnota je _správce sql_.|
|Šablona Microsoft Azure SQL Database|Šablona Resource Manageru|Správce hesel služby Azure SQL Server (ID prostředku trezoru klíč)|ID prostředku trezoru klíčů. Použití "/ subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" a nahraďte `{subscriptionId}` svým ID předplatného a `{orgName}` s  **Název organizace** podrobný plán parametru.|
|Šablona Microsoft Azure SQL Database|Šablona Resource Manageru|Správce hesel služby Azure SQL Server (název pro Key Vault tajný klíč)|Uživatelské jméno SQL správce serveru. Musí odpovídat hodnotě ve **šablony služby Key Vault** vlastnost **uživatelské jméno správce serveru SQL Azure**.|
|Šablona Microsoft Azure SQL Database|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona Microsoft Azure SQL Database|Šablona Resource Manageru|ID objektu správce AAD|ID objektu AAD uživatele, který bude přiřadit jako správce služby Active Directory. Žádné výchozí hodnoty a nemůže být ponecháno prázdné. K vyhledání této hodnoty na webu Azure Portal, vyhledejte a vyberte "Uživatelé" v části _služby_. Použití _název_ pole k filtrování pro název účtu a vyberte tento účet. Na _profilu uživatele_ stránky, vyberte ikonu "Kliknutím zkopírujete" vedle _ID objektu_.|
|Šablona Microsoft Azure SQL Database|Šablona Resource Manageru|Přihlašovací jméno správce AAD|V současné době nelze účty Microsoft (jako je live.com nebo outlook.com) nastavit jako správce. Jenom uživatelé a skupiny zabezpečení ve vaší organizaci je možné nastavit jako správce. Žádné výchozí hodnoty a nemůže být ponecháno prázdné. K vyhledání této hodnoty na webu Azure Portal, vyhledejte a vyberte "Uživatelé" v části _služby_. Použití _název_ pole k filtrování pro název účtu a vyberte tento účet. Na _profilu uživatele_ stránky, zkopírujte _uživatelské jméno_.|
|Skupina prostředků App Service Environment|Skupina prostředků|Název|**Uzamčeno** – zřetězí **název organizace** s `-workload-ase-rg` aby skupiny prostředků jedinečný.|
|Skupina prostředků App Service Environment|Skupina prostředků|Umístění|**Uzamčeno** -používá parametr podrobného plánu.|
|Šablona funkce App Service Environment|Šablona Resource Manageru|Název domény|Název služby Active Directory vytvořené v rámci ukázky. Výchozí hodnota je _contoso.com_.|
|Šablona funkce App Service Environment|Šablona Resource Manageru|Umístění služby ASE|Umístění app Service Environment. Výchozí hodnota je _USA – západ 2_.|
|Šablona funkce App Service Environment|Šablona Resource Manageru|Doba uchovávání protokolů Application Gateway ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|

## <a name="next-steps"></a>Další postup

Teď, když jste zkontrolovali kroky, abyste nasadili ukázkový plán úloh ISO 27001 App Service prostředí/službou SQL Database, najdete v následujících článcích se dozvíte o architektuře a mapování ovládacích prvků:

> [!div class="nextstepaction"]
> [Podrobný plán úloh ISO 27001 App Service prostředí/službou SQL Database – přehled](./index.md)
> [podrobného plánu úlohy ISO 27001 App Service prostředí/službou SQL Database – mapování ovládacích prvků](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)