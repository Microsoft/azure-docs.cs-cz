---
title: Nasazení ukázky plánu úlohy iso 27001 ASE/SQL
description: Nasazení kroků iso 27001 App Service prostředí nebo SQL database plán pracovního plánu ukázku včetně podrobností o parametru artefakt u podrobného plánu.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: 6b8f3b753f1dd8cfbc247a77f2004e3c4d3423bb
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75922584"
---
# <a name="deploy-the-iso-27001-app-service-environmentsql-database-workload-blueprint-sample"></a>Nasazení ukázky pracovního plánu prostředí služby ISO 27001 App Service/SQL Database

Chcete-li nasadit azure plány ISO 27001 App Service prostředí nebo SQL database podrobné hopodrobný plán, je nutné podniknout následující kroky:

> [!div class="checklist"]
> - Nasazení ukázky podrobného plánu [sdílených služeb ISO 27001](../iso27001-shared/index.md)
> - Vytvoření nového podrobného plánu z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free) než začnete.

## <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Nasazení ukázky podrobného plánu sdílených služeb ISO 27001

Před nasazením této ukázky podrobného plánu musí být ukázka podrobného plánu [sdílených služeb ISO 27001](../iso27001-shared/index.md) nasazena do cílového předplatného. Bez úspěšného nasazení ukázky podrobného plánu sdílených služeb ISO 27001 bude v této ukázce podrobného plánu chybět závislosti infrastruktury a během nasazení se nezdaří.

> [!IMPORTANT]
> Tato ukázka podrobného plánu musí být přiřazena ve stejném předplatném jako ukázka podrobného plánu [sdílených služeb ISO 27001.](../iso27001-shared/index.md)

## <a name="create-blueprint-from-sample"></a>Vytvoření podrobného plánu z ukázky

Nejprve implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako startéru.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Na stránce **Začínáme** vlevo vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. Najděte **ukázku plánu ISO 27001: ASE/SQL workload** blueprint v části _Další ukázky_ a vyberte **Použít tuto ukázku**.

1. Zadejte _základy ukázky_ podrobného plánu:

   - **Název podrobného**plánu : Zadejte název pro kopii ukázky plánu pracovního vytížení ase/SQL ISO 27001.
   - **Umístění definice**: Použijte tři tečky a vyberte skupinu pro správu, do které chcete uložit kopii vzorku.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Po dokončení revize ukázky podrobného plánu vyberte **Uložit koncept.**

## <a name="publish-the-sample-copy"></a>Publikování ukázkové kopie

Vaše kopie ukázky podrobného plánu byla nyní vytvořena ve vašem prostředí. Je vytvořen v režimu **konceptu** a musí být **publikovánpřed** jeho přiřazením a nasazením. Kopii ukázky podrobného plánu lze přizpůsobit vašemu prostředí a potřebám, ale tato změna ji může přesunout od standardu ISO 27001.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Vyberte stránku **Definice podrobného plánu** vlevo. Pomocí filtrů vyhledejte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **Publikovat podrobný plán.** Na nové stránce vpravo zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud provedete změnu později. Zadejte **poznámky ke změně,** například "První verze publikovaná z ukázky podrobného plánu ISO 27001". Pak vdolní části stránky vyberte **Publikovat.**

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

   - Parametry podrobného plánu

     Parametry definované v této části jsou používány mnoho artefaktů v definici podrobného plánu poskytnout konzistenci.

     - **Název organizace**: Zadejte krátký název pro vaši organizaci. Tato vlastnost se používá především pro pojmenování prostředků.
     - **ID předplatného sdílené služby:** ID předplatného, kde je přiřazena ukázka podrobného plánu [sdílených služeb ISO 27001.](../iso27001-shared/index.md)
     - **Předpona výchozí adresy podsítě**: Zápis CIDR pro výchozí podsíť virtuální sítě.
       Výchozí hodnota je _10.1.0.0/16_.
     - **Umístění pracovního vytížení**: Určuje, do jakého umístění jsou artefakty nasazeny. Ne všechny služby jsou k dispozici ve všech lokalitách. Artefakty nasazující tyto služby poskytují možnost parametru pro umístění, do kterého má být tento artefakt nasazen.

   - Parametry artefaktů

     Parametry definované v této části platí pro artefakt, pod kterým je definován. Tyto parametry jsou [dynamické parametry,](../../concepts/parameters.md#dynamic-parameters) protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popisy naleznete v [tabulce parametrů artefaktu](#artifact-parameters-table).

1. Po zadání všech parametrů **vyberte** Přiřadit v dolní části stránky. Přiřazení podrobného plánu je vytvořen a artefakt nasazení začíná. Nasazení trvá zhruba hodinu. Chcete-li zkontrolovat stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure Blueprints a integrované ukázky podrobných plánů jsou **zdarma**. Prostředky Azure jsou [oceněny podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na spuštění prostředků nasazených v této ukázce podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktu

V následující tabulce je uveden seznam parametrů artefaktu podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|Skupina prostředků Log Analytics|Skupina prostředků|Name (Název)|**Uzamčeno** – Zřetězí **název** `-workload-log-rg` organizace tak, aby byla skupina prostředků jedinečná.|
|Skupina prostředků Log Analytics|Skupina prostředků|Umístění|**Uzamčeno** - Používá parametr podrobného plánu.|
|Šablona log analytics|Šablona Resource Manageru|Úroveň služeb|Nastaví úroveň pracovního prostoru Log Analytics. Výchozí hodnota je _PerNode_.|
|Šablona log analytics|Šablona Resource Manageru|Uchovávání protokolu ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona log analytics|Šablona Resource Manageru|Umístění|Oblast používaná k vytvoření pracovního prostoru Analýzy protokolů. Výchozí hodnota je _Západní US 2_.|
|Skupina síťových prostředků|Skupina prostředků|Name (Název)|**Uzamčeno** – Zřetězí **název** `-workload-net-rg` organizace tak, aby byla skupina prostředků jedinečná.|
|Skupina síťových prostředků|Skupina prostředků|Umístění|**Uzamčeno** - Používá parametr podrobného plánu.|
|Šablona Skupiny zabezpečení sítě|Šablona Resource Manageru|Uchovávání protokolu ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona Virtuální síť a směrovací tabulka|Šablona Resource Manageru|Privátní IP adresa Azure firewall|Konfiguruje privátní IP adresu [brány firewall Azure](../../../../firewall/overview.md). Měla by být součástí zápisu CIDR definovaného v _ISO 27001: Parametr artefaktu sdílené služby_ **Azure Firewall předokonum podsítě**. Výchozí hodnota je _10.0.4.4_.|
|Šablona Virtuální síť a směrovací tabulka|Šablona Resource Manageru|ID předplatného sdílených služeb|Hodnota používaná k povolení partnerského vztahu virtuální sítě mezi workload a sdílenými službami.|
|Šablona Virtuální síť a směrovací tabulka|Šablona Resource Manageru|Předpona adresy virtuální sítě|Cidr zápis pro virtuální síť. Výchozí hodnota je _10.1.0.0/16_.|
|Šablona Virtuální síť a směrovací tabulka|Šablona Resource Manageru|Předpona výchozí adresy podsítě|Zápis CIDR pro výchozí podsíť virtuální sítě. Výchozí hodnota je _10.1.0.0/16_.|
|Šablona Virtuální síť a směrovací tabulka|Šablona Resource Manageru|PŘIDÁ IP adresu|IP adresa prvního virtuálního virtuálního počítačů ADDS. Tato hodnota se používá jako vlastní virtuální síť DNS.|
|Skupina prostředků trezoru klíčů|Skupina prostředků|Name (Název)|**Uzamčeno** – Zřetězí **název** `-workload-kv-rg` organizace tak, aby byla skupina prostředků jedinečná.|
|Skupina prostředků trezoru klíčů|Skupina prostředků|Umístění|**Uzamčeno** - Používá parametr podrobného plánu.|
|Šablona Trezor klíčů|Šablona Resource Manageru|ID objektu AAD|Identifikátor objektu AAD účtu, který vyžaduje přístup k instanci trezoru klíčů. Žádná výchozí hodnota a nemůže být ponechána prázdná. Chcete-li tuto hodnotu vyhledat z webu Azure Portal, vyhledejte a vyberte "Uživatelé" v části _Služby_. Pomocí pole _Název_ můžete filtrovat název účtu a vybrat tento účet. Na stránce _Profil uživatele_ vyberte ikonu "Kliknutím na kopírování" vedle _ID objektu_.|
|Šablona Trezor klíčů|Šablona Resource Manageru|Uchovávání protokolu ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona Trezor klíčů|Šablona Resource Manageru|Skladová položka trezoru klíčů|Určuje skladovou položku vytvořeného trezoru klíčů. Výchozí hodnota je _Premium_.|
|Šablona Trezor klíčů|Šablona Resource Manageru|Uživatelské jméno správce Azure SQL Serveru|Uživatelské jméno používané pro přístup k Azure SQL Server. V šabloně Azure SQL Database musí odpovídat stejné **hodnotě vlastnosti**. Výchozí hodnota je _sql-admin-user_.|
|Skupina prostředků databáze Azure SQL|Skupina prostředků|Name (Název)|**Uzamčeno** – Zřetězí **název** `-workload-azsql-rg` organizace tak, aby byla skupina prostředků jedinečná.|
|Skupina prostředků databáze Azure SQL|Skupina prostředků|Umístění|**Uzamčeno** - Používá parametr podrobného plánu.|
|Šablona databáze Azure SQL|Šablona Resource Manageru|Uživatelské jméno správce Azure SQL Serveru|Uživatelské jméno pro Azure SQL Server. Musí odpovídat stejné hodnotě vlastnosti v **šabloně trezoru klíčů**. Výchozí hodnota je _sql-admin-user_.|
|Šablona databáze Azure SQL|Šablona Resource Manageru|Heslo správce Azure SQL Serveru (ID prostředku trezoru klíčů)|ID prostředku trezoru klíčů. Použijte "/subscription/{subscriptionId}/resourceGroups/{orgName}-workload-kv/providers/Microsoft.KeyVault/vaults/{orgName}-workload-kv" `{subscriptionId}` a nahraďte `{orgName}` id předplatného a parametrem podrobného plánu **názvu organizace.**|
|Šablona databáze Azure SQL|Šablona Resource Manageru|Heslo správce Azure SQL Serveru (tajný název trezoru klíčů)|Uživatelské jméno správce serveru SQL Server. Musí odpovídat hodnotě ve vlastnosti **šablony úložiště klíčů** Azure SQL Server admin uživatelské **jméno**.|
|Šablona databáze Azure SQL|Šablona Resource Manageru|Uchovávání protokolu ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona databáze Azure SQL|Šablona Resource Manageru|ID objektu správce AAD|ID objektu AAD uživatele, který bude přiřazen jako správce služby Active Directory. Žádná výchozí hodnota a nemůže být ponechána prázdná. Chcete-li tuto hodnotu vyhledat z webu Azure Portal, vyhledejte a vyberte "Uživatelé" v části _Služby_. Pomocí pole _Název_ můžete filtrovat název účtu a vybrat tento účet. Na stránce _Profil uživatele_ vyberte ikonu "Kliknutím na kopírování" vedle _ID objektu_.|
|Šablona databáze Azure SQL|Šablona Resource Manageru|Přihlášení správce AAD|V současné době nelze účty Microsoft (například live.com nebo outlook.com) nastavit jako správce. Jako správce lze nastavit pouze uživatele a skupiny zabezpečení ve vaší organizaci. Žádná výchozí hodnota a nemůže být ponechána prázdná. Chcete-li tuto hodnotu vyhledat z webu Azure Portal, vyhledejte a vyberte "Uživatelé" v části _Služby_. Pomocí pole _Název_ můžete filtrovat název účtu a vybrat tento účet. Na stránce _Profil uživatele_ zkopírujte _uživatelské jméno_.|
|Skupina prostředků prostředí služby App Service|Skupina prostředků|Name (Název)|**Uzamčeno** – Zřetězí **název** `-workload-ase-rg` organizace tak, aby byla skupina prostředků jedinečná.|
|Skupina prostředků prostředí služby App Service|Skupina prostředků|Umístění|**Uzamčeno** - Používá parametr podrobného plánu.|
|Šablona Prostředí služby App Service|Šablona Resource Manageru|Název domény|Název služby Active Directory vytvořený ukázkou. Výchozí hodnota je _contoso.com_.|
|Šablona Prostředí služby App Service|Šablona Resource Manageru|Umístění služby ASE|Umístění prostředí služby App Service. Výchozí hodnota je _Západní US 2_.|
|Šablona Prostředí služby App Service|Šablona Resource Manageru|Uchovávání protokolu brány aplikace ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|

## <a name="next-steps"></a>Další kroky

Teď, když jste zkontrolovali kroky k nasazení iso 27001 App Service prostředí nebo SQL Database plán plánu úlohy ukázku, navštivte následující články se dozvíte o architektuře a mapování ovládacích prvku:

> [!div class="nextstepaction"]
> [ISO 27001 App Service Environment/SQL Database ústuz – přehled](./index.md)
> [ISO 27001 App Service Environment/SQL Database plán pracovního vytížení – mapování ovládacích sil](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si o [životním cyklu podrobného plánu](../../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../../how-to/update-existing-assignments.md).