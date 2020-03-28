---
title: Nasazení ukázky podrobného plánu sdílených služeb ISO 27001
description: Nasazení kroků pro ukázku podrobného plánu sdílených služeb ISO 27001 včetně podrobností o parametru artefaktu podrobného plánu.
ms.date: 01/13/2020
ms.topic: sample
ms.openlocfilehash: a9dabc99469321445006e449757a10fbc51aba87
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/24/2020
ms.locfileid: "75920692"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Nasazení ukázky podrobného plánu sdílených služeb ISO 27001

Chcete-li nasadit ukázku podrobného plánu sdílených služeb Azure Blueprints, je třeba podniknout následující kroky:

> [!div class="checklist"]
> - Vytvoření nového podrobného plánu z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud nemáte předplatné Azure, vytvořte si [bezplatný účet,](https://azure.microsoft.com/free) než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvoření podrobného plánu z ukázky

Nejprve implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako startéru.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **podrobné plány**.

1. Na stránce **Začínáme** vlevo vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. Najděte **ukázku plánu ISO 27001: Sdílené služby** v části _Další ukázky_ a vyberte **Použít tuto ukázku**.

1. Zadejte _základy ukázky_ podrobného plánu:

   - **Název podrobného plánu**: Zadejte název pro kopii ukázky podrobného plánu sdílených služeb ISO 27001.
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
     - **Předpona adresy podsítě sdílených služeb**: Zadejte hodnotu zápisu CIDR pro síť nasazených prostředků společně.
     - **Umístění sdílených služeb**: Určuje, do jakého umístění jsou artefakty nasazeny. Ne všechny služby jsou k dispozici ve všech lokalitách. Artefakty nasazující tyto služby poskytují možnost parametru pro umístění, do kterého má být tento artefakt nasazen.
     - **Povolené umístění (zásada: Iniciativa podrobného plánu pro ISO 27001)**: Hodnota, která označuje povolená umístění pro skupiny prostředků a zdroje.
     - **Pracovní prostor Log Analytics pro agenty virtuálních aplikací (zásada: iniciativa Podrobného plánu pro ISO 27001):** Určuje ID prostředku pracovního prostoru. Tento parametr `concat` používá funkci k vytvoření ID prostředku.

   - Parametry artefaktů

     Parametry definované v této části platí pro artefakt, pod kterým je definován. Tyto parametry jsou [dynamické parametry,](../../concepts/parameters.md#dynamic-parameters) protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popisy naleznete v [tabulce parametrů artefaktu](#artifact-parameters-table).

1. Po zadání všech parametrů **vyberte** Přiřadit v dolní části stránky. Přiřazení podrobného plánu je vytvořen a artefakt nasazení začíná. Nasazení trvá zhruba hodinu. Chcete-li zkontrolovat stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure Blueprints a integrované ukázky podrobných plánů jsou **zdarma**. Prostředky Azure jsou [oceněny podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na spuštění prostředků nasazených v této ukázce podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktu

V následující tabulce je uveden seznam parametrů artefaktu podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|\[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení v Linuxu (VMSS)|Přiřazení zásad|Volitelné: Seznam imitek virtuálních počítačů, které podporují operační systém Linux pro přidání do oboru|(Nepovinné) Výchozí hodnota je _["none"]_.|
|\[Náhled:\]Nasazení agenta analýzy protokolů pro virtuální počítače s Linuxem|Přiřazení zásad|Volitelné: Seznam imitek virtuálních počítačů, které podporují operační systém Linux pro přidání do oboru|(Nepovinné) Výchozí hodnota je _["none"]_.|
|\[Náhled:\]Nasazení agenta analýzy protokolů pro škálovací sady virtuálních zařízení windows (VMSS)|Přiřazení zásad|Volitelné: Seznam iimages virtuálních aplikací, které podporují operační systém Windows přidat do oboru|(Nepovinné) Výchozí hodnota je _["none"]_.|
|\[Náhled\]: Nasazení agenta analýzy protokolů pro virtuální servery Windows|Přiřazení zásad|Volitelné: Seznam iimages virtuálních aplikací, které podporují operační systém Windows přidat do oboru|(Nepovinné) Výchozí hodnota je _["none"]_.|
|Povolené typy prostředků|Přiřazení zásad|Povolené typy prostředků|Seznam typů prostředků, které mohou být nasazeny. Tento seznam se skládá ze všech typů prostředků nasazených ve sdílených službách.|
|Povolené skladové položky účtu úložiště|Přiřazení zásad|Povolené skladové sady úložiště|Seznam skladových položek účtu úložiště diagnostických protokolů je povolen. Výchozí hodnota je _["Standard_LRS"]_.|
|Povolené souuživatele virtuálních strojů|Přiřazení zásad|Seznam sou položek virtuálních strojů, které mohou být nasazeny. Výchozí hodnota je _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Iniciativa pro plán ISO 27001|Přiřazení zásad|Typy prostředků pro auditování diagnostických protokolů|Seznam typů prostředků, které mají být auditovány, pokud není povoleno nastavení diagnostického protokolu. Přijatelné hodnoty najdete na [schémata diagnostických protokolů Azure Monitor](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Skupina prostředků Log Analytics|Skupina prostředků|Name (Název)|**Uzamčeno** – Zřetězí **název** `-sharedsvsc-log-rg` organizace tak, aby byla skupina prostředků jedinečná.|
|Skupina prostředků Log Analytics|Skupina prostředků|Umístění|**Uzamčeno** - Používá parametr podrobného plánu.|
|Šablona log analytics|Šablona Resource Manageru|Úroveň služeb|Nastaví úroveň pracovního prostoru Log Analytics. Výchozí hodnota je _PerNode_.|
|Šablona log analytics|Šablona Resource Manageru|Uchovávání protokolu ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona log analytics|Šablona Resource Manageru|Umístění|Oblast používaná k vytvoření pracovního prostoru Analýzy protokolů. Výchozí hodnota je _Západní US 2_.|
|Skupina síťových prostředků|Skupina prostředků|Name (Název)|**Uzamčeno** – Zřetězí **název** `-sharedsvcs-net-rg` organizace tak, aby byla skupina prostředků jedinečná.|
|Skupina síťových prostředků|Skupina prostředků|Umístění|**Uzamčeno** - Používá parametr podrobného plánu.|
|Šablona Azure Firewall|Šablona Resource Manageru|Privátní IP adresa Azure firewall|Konfiguruje privátní IP adresu [brány firewall Azure](../../../../firewall/overview.md). Tato hodnota se také používá jako výchozí směrovací tabulka v podsíti sdílených služeb. Měla by být součástí zápisu CIDR definovaného v **předponě adresy podsítě Azure Firewall**. Výchozí hodnota je _10.0.4.4_.|
|Šablona Azure Firewall|Šablona Resource Manageru|Uchovávání protokolu ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona Skupiny zabezpečení sítě|Šablona Resource Manageru|Uchovávání protokolu ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona Virtuální síť a směrovací tabulka|Šablona Resource Manageru|Předpona adresy virtuální sítě|Cidr zápis pro virtuální síť. Výchozí hodnota je _10.0.0.0/16_.|
|Šablona Virtuální síť a směrovací tabulka|Šablona Resource Manageru|Povolení ochrany DDoS virtuální sítě|Konfiguruje ochranu DDoS pro virtuální síť. Výchozí hodnota je _true_.|
|Šablona Virtuální síť a směrovací tabulka|Šablona Resource Manageru|Předpona adresy podsítě sdílené služby|Zápis CIDR pro podsíť sdílených služeb. Výchozí hodnota je _10.0.0.0/24_.|
|Šablona Virtuální síť a směrovací tabulka|Šablona Resource Manageru|Předpona adresy podsítě DMZ|Zápis CIDR pro podsíť DMZ. Výchozí hodnota je _10.0.1.0/24_.|
|Šablona Virtuální síť a směrovací tabulka|Šablona Resource Manageru|Předpona adresy podsítě Aplikační brána|Zápis CIDR pro podsíť aplikační brány. Výchozí hodnota je _10.0.2.0/24_.|
|Šablona Virtuální síť a směrovací tabulka|Šablona Resource Manageru|Předpona adresy podsítě Brána virtuální sítě|Zápis CIDR pro podsíť brány virtuální sítě. Výchozí hodnota je _10.0.3.0/24_.|
|Šablona Virtuální síť a směrovací tabulka|Šablona Resource Manageru|Předpona adresy podsítě Azure Firewall|Zápis CIDR pro podsíť [brány firewall Azure.](../../../../firewall/overview.md) Měl by obsahovat parametr **privátní IP** adresy azure firewall.|
|Skupina prostředků trezoru klíčů|Skupina prostředků|Name (Název)|**Uzamčeno** – Zřetězí **název** `-sharedsvcs-kv-rg` organizace tak, aby byla skupina prostředků jedinečná.|
|Skupina prostředků trezoru klíčů|Skupina prostředků|Umístění|**Uzamčeno** - Používá parametr podrobného plánu.|
|Šablona Trezor klíčů|Šablona Resource Manageru|Uživatelské jméno správce jumpboxu|Uživatelské jméno pro jumpbox. Musí odpovídat stejné hodnotě vlastnosti v **šabloně Jumpbox**. Výchozí hodnota je _jb-admin-user_.|
|Šablona Trezor klíčů|Šablona Resource Manageru|Jumpbox admin ssh klíč nebo heslo|Klíč nebo heslo pro účet na jumpbox. Musí odpovídat stejné hodnotě vlastnosti v **šabloně Jumpbox**. Žádná výchozí hodnota a nemůže být ponechána prázdná.|
|Šablona Trezor klíčů|Šablona Resource Manageru|Uživatelské jméno správce domény|Uživatelské jméno používané pro přístup k virtuálnímu virtuálnímu ms služby Active Directory a připojení dalších virtuálních uživatelů k doméně. Musí odpovídat hodnotě vlastnosti **uživatele správce domény** v **šabloně služby Active Directory Domain Services**. Výchozí hodnota je _doména-admin-user_.|
|Šablona Trezor klíčů|Šablona Resource Manageru|Heslo správce domény|Heslo správce domény. Žádná výchozí hodnota a nemůže být ponechána prázdná.|
|Šablona Trezor klíčů|Šablona Resource Manageru|ID objektu AAD|Identifikátor objektu AAD účtu, který vyžaduje přístup k instanci trezoru klíčů. Žádná výchozí hodnota a nemůže být ponechána prázdná. Chcete-li tuto hodnotu vyhledat z webu Azure Portal, vyhledejte a vyberte "Uživatelé" v části _Služby_. Pomocí pole _Název_ můžete filtrovat název účtu a vybrat tento účet. Na stránce _Profil uživatele_ vyberte ikonu "Kliknutím na kopírování" vedle _ID objektu_.  |
|Šablona Trezor klíčů|Šablona Resource Manageru|Uchovávání protokolu ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona Trezor klíčů|Šablona Resource Manageru|Skladová položka trezoru klíčů|Určuje skladovou položku vytvořeného trezoru klíčů. Výchozí hodnota je _Premium_.|
|Skupina prostředků Jumpboxu|Skupina prostředků|Name (Název)|**Uzamčeno** – Zřetězí **název** `-sharedsvcs-jb-rg` organizace tak, aby byla skupina prostředků jedinečná.|
|Skupina prostředků Jumpboxu|Skupina prostředků|Umístění|**Uzamčeno** - Používá parametr podrobného plánu.|
|Šablona Jumpbox|Šablona Resource Manageru|Uživatelské jméno správce jumpboxu|Uživatelské jméno používané pro přístup k virtuálním ms jumpbox. Musí odpovídat stejné hodnotě vlastnosti v **šabloně trezoru klíčů**. Výchozí hodnota je _jb-admin-user_.|
|Šablona Jumpbox|Šablona Resource Manageru|Heslo správce jumpboxu (ID prostředku key vaultu)|ID prostředku trezoru klíčů. Použijte "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" `{subscriptionId}` a nahraďte `{orgName}` id předplatného a parametrem podrobného plánu **názvu organizace.**|
|Šablona Jumpbox|Šablona Resource Manageru|Heslo správce jumpboxu (tajný název trezoru klíčů)|Uživatelské jméno správce jumpboxu. Musí odpovídat hodnotě ve vlastnosti **key vault šablony** **Jumpbox admin uživatelské jméno**.|
|Šablona Jumpbox|Šablona Resource Manageru|Operační systém Jumpbox|Určuje operační systém virtuálního soudu jumpbox. Výchozí hodnota je _Windows_.|
|Skupina prostředků služby Active Directory Domain Services|Skupina prostředků|Name (Název)|**Uzamčeno** – Zřetězí **název** `-sharedsvcs-adds-rg` organizace tak, aby byla skupina prostředků jedinečná.|
|Skupina prostředků služby Active Directory Domain Services|Skupina prostředků|Umístění|**Uzamčeno** - Používá parametr podrobného plánu.|
|Šablona služby Active Directory Domain Services|Šablona Resource Manageru|Uživatelské jméno správce domény|Uživatelské jméno pro přidává jumpbox. Musí odpovídat stejné hodnotě vlastnosti v **šabloně trezoru klíčů**. Výchozí hodnota je _adds-admin-user_.|
|Šablona služby Active Directory Domain Services|Šablona Resource Manageru|Heslo správce domény (ID prostředku trezoru klíčů)|ID prostředku trezoru klíčů. Použijte "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" `{subscriptionId}` a nahraďte `{orgName}` id předplatného a parametrem podrobného plánu **názvu organizace.**|
|Šablona služby Active Directory Domain Services|Šablona Resource Manageru|Heslo správce domény (tajný název trezoru klíčů)|Uživatelské jméno správce domény. Musí odpovídat hodnotě v **vlastnosti šablony key vaultu** **Doménové jméno správce**.|
|Šablona služby Active Directory Domain Services|Šablona Resource Manageru|Název domény|Název služby Active Directory vytvořený ukázkou. Výchozí hodnota je _contoso.com_.|
|Šablona služby Active Directory Domain Services|Šablona Resource Manageru|Uživatel správce domény|Uživatelské jméno pro účet správce a pro připojení zařízení k doméně služby AD. Musí odpovídat hodnotě vlastnosti **uživatelského jména správce služby AD** v **šabloně Trezoru klíčů**. Výchozí hodnota je _doména-admin-user_.|
|Šablona služby Active Directory Domain Services|Šablona Resource Manageru|Heslo správce domény|Nastavte podrobnosti trezoru klíčů pro ukládání hesla. Žádná výchozí hodnota a nemůže být ponechána prázdná.|

## <a name="next-steps"></a>Další kroky

Nyní, když jste zkontrolovali kroky k nasazení ukázky podrobného plánu sdílených služeb ISO 27001, navštivte následující články, kde se dozvíte o architektuře a mapování ovládacích prvku:

> [!div class="nextstepaction"]
> [Plán sdílených služeb ISO 27001 – přehled](./index.md)
> [podrobný plán sdílených služeb ISO 27001 – mapování řízení](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si o [životním cyklu podrobného plánu](../../concepts/lifecycle.md).
- Pochopit, jak používat [statické a dynamické parametry](../../concepts/parameters.md).
- Naučte se přizpůsobit [pořadí sekvencování podrobných plánů](../../concepts/sequencing-order.md).
- Zjistěte, jak využít [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md).
- Přečtěte si, jak [aktualizovat existující přiřazení](../../how-to/update-existing-assignments.md).
