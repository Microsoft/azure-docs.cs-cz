---
title: Ukázkový – ISO 27001 Shared Services podrobný plán – kroky nasazení
description: Kroky vzorku ISO 27001 Shared Services podrobného plánu nasazení.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 7fea9b0d00d92c63bb4c8bbdadada9e8eb6a05fe
ms.sourcegitcommit: dec7947393fc25c7a8247a35e562362e3600552f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2019
ms.locfileid: "58202499"
---
# <a name="deploy-the-azure-blueprints-iso-27001-shared-services-blueprint-sample"></a>Nasazení ukázkové podrobného plánu Azure plány ISO 27001 sdílené služby

Abyste nasadili ukázkový plán Azure plány ISO 27001 sdílené služby, musí provést následující kroky:

> [!div class="checklist"]
> - Vytvořit nový plán, podle kterého z ukázky
> - Označte si kopii ukázky jako **publikováno**
> - Přiřazení kopie podrobný plán k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

První tak, že vytvoříte nový plán ve vašem prostředí pomocí ukázky jako starter implementaci vzorku podrobného plánu.

1. Vyberte **všechny služby** a vyhledejte a vyberte **zásady** v levém podokně. Na **zásady** stránce **plány**.

1. Z **Začínáme** stránky na levé straně, vyberte **vytvořit** tlačítko _vytvořit podrobný plán_.

1. Najít **ISO 27001: Sdílené služby** podrobného plánu ukázky v části _Další ukázky_ a vyberte **fungování této ukázky**.

1. Zadejte _Základy_ vzorku podrobného plánu:

   - **Název podrobného plánu**: Zadejte název pro vaši kopii ukázky ISO 27001 Shared Services podrobného plánu.
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
     - **Předpona adresy podsíť sdílených služeb**: Zadejte hodnotu notaci CIDR sítě nasazené prostředky společně.
     - **Sdílené umístění služby**: Určuje umístění, jaké artefakty, které jsou nasazené na. Ne všechny služby jsou k dispozici ve všech umístěních. Artefakty nasazení tyto služby poskytují možnost parametr pro umístění k nasazení tohoto artefaktu, který se.
     - **Povolená umístění (zásad: Podrobný plán iniciativy ISO 27001)**: Hodnota, která určuje povolené umístění skupiny prostředků a prostředky.
     - **Pracovní prostor log Analytics pro agenty virtuálních počítačů (zásad: Podrobný plán iniciativy ISO 27001)**: Určuje ID prostředku pracovního prostoru. Tento parametr se používá `concat` funkce k vytvoření ID prostředku.

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, pod kterým je definována. Tyto parametry jsou [dynamických parametrů](../../concepts/parameters.md#dynamic-parameters) protože máte definovaný během přiřazování podrobný plán. Úplný seznam nebo artefaktů parametry a jejich popis najdete v tématu [artefaktů parametry tabulky](#artifact-parameters-table).

1. Po zadání všech parametrů se vyberte **přiřadit** v dolní části stránky. Přiřazení podrobného plánu se vytvoří a začne artefaktů nasazení. Nasazení trvá přibližně hodinu. Chcete-li zkontrolovat stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure plány a ukázky předdefinovaných podrobného plánu **zdarma**. Jsou prostředky Azure [ceny podle produktu](https://azure.microsoft.com/en-us/pricing/). Použití [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) odhadnout náklady na provozování prostředky nasazené v tomto příkladu podrobného plánu.

## <a name="artifact-parameters-table"></a>Parametry tabulky artefaktu

Následující tabulka obsahuje seznam podrobný plán artefaktů parametry:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|[Preview]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů s Linuxem (VMSS)|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do rozsahu|(Volitelné) Výchozí hodnota je _["none"]_.|
|[Preview]: Nasadit agenta Log Analytics pro virtuální počítače s Linuxem|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do rozsahu|(Volitelné) Výchozí hodnota je _["none"]_.|
|[Preview]: Nasadit agenta Log Analytics pro Škálovací sady virtuálních počítačů Windows (VMSS)|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů, které mají podporovaný operační systém Windows pro přidání do rozsahu|(Volitelné) Výchozí hodnota je _["none"]_.|
|[Preview]: Nasadit agenta Log Analytics pro virtuální počítače s Windows|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů, které mají podporovaný operační systém Windows pro přidání do rozsahu|(Volitelné) Výchozí hodnota je _["none"]_.|
|Povolené typy prostředků|Přiřazení zásad|Povolené typy prostředků|Seznam typů prostředků, které jsou povolené pro nasazení. Tento seznam se skládá z všem typům prostředků nasazené ve službách sdílené.|
|Povolené skladové položky účtu úložiště|Přiřazení zásad|Povolené skladové položky storage|Seznam diagnostické protokoly povolené skladové položky účtu úložiště. Výchozí hodnota je _["Standard_LRS"]_.|
|Povolené skladové položky virtuálních počítačů|Přiřazení zásad|Seznam skladových položek virtuálních počítačů je povolené nasazení. Výchozí hodnota je _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Iniciativa podrobného plánu pro ISO 27001|Přiřazení zásad|Typy prostředků pro diagnostické protokoly auditu|Seznam typů prostředků pro audit, jestli není zapnutá diagnostických protokolů. Přípustné hodnoty lze nalézt v [Azure Monitor diagnostické protokoly, schémata](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Skupina prostředků log Analytics|Skupina prostředků|Název|**Uzamčeno** – zřetězí **název organizace** s `-sharedsvsc-log-rg` aby skupiny prostředků jedinečný.|
|Skupina prostředků log Analytics|Skupina prostředků|Umístění|**Uzamčeno** -používá parametr podrobného plánu.|
|Šablona pro Log Analytics|Šablona Resource Manageru|Úroveň služeb|Nastaví úroveň pracovního prostoru Log Analytics. Výchozí hodnota je _PerNode_.|
|Šablona pro Log Analytics|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona pro Log Analytics|Šablona Resource Manageru|Umístění|Oblasti používané pro vytvoření pracovního prostoru Log Analytics. Výchozí hodnota je _USA – západ 2_.|
|Skupina síťových prostředků|Skupina prostředků|Název|**Uzamčeno** – zřetězí **název organizace** s `-sharedsvcs-net-rg` aby skupiny prostředků jedinečný.|
|Skupina síťových prostředků|Skupina prostředků|Umístění|**Uzamčeno** -používá parametr podrobného plánu.|
|Šablona pro Azure Firewall|Šablona Resource Manageru|Privátní IP adresa brány Azure Firewall|Nakonfiguruje privátní IP adresu [brány Azure](../../../../firewall/overview.md). Tato hodnota se také používá jako výchozí směrovací tabulka na podsíť sdílených služeb. Musí být součástí notaci CIDR definované v **předponu adresy podsítě brány Firewall Azure**. Výchozí hodnota je _10.0.4.4_.|
|Šablona pro Azure Firewall|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona skupiny zabezpečení sítě|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona pro virtuální síť a tabulku směrování|Šablona Resource Manageru|Předpona adresy virtuální sítě|Zápis CIDR pro virtuální síť. Výchozí hodnota je _10.0.0.0/16_.|
|Šablona pro virtuální síť a tabulku směrování|Šablona Resource Manageru|Povolit ve virtuální síti službu DDoS Protection|Nakonfiguruje ochrana před útoky DDoS pro virtuální síť. Výchozí hodnota je _true_.|
|Šablona pro virtuální síť a tabulku směrování|Šablona Resource Manageru|Sdílené služby předponu adresy podsítě|Zápis CIDR podsítě Shared Services. Výchozí hodnota je _10.0.0.0/24_.|
|Šablona pro virtuální síť a tabulku směrování|Šablona Resource Manageru|Předpona adresy podsítě DMZ|Zápis CIDR podsítě DMZ. Výchozí hodnota je _10.0.1.0/24_.|
|Šablona pro virtuální síť a tabulku směrování|Šablona Resource Manageru|Předpona adresy podsítě brány Application Gateway|Zápis CIDR podsítě služby application gateway. Výchozí hodnota je _10.0.2.0/24_.|
|Šablona pro virtuální síť a tabulku směrování|Šablona Resource Manageru|Předpona adresy podsítě Brány virtuální sítě|Zápis CIDR pro podsíť brány virtuální sítě. Výchozí hodnota je _10.0.3.0/24_.|
|Šablona pro virtuální síť a tabulku směrování|Šablona Resource Manageru|Předpona adresy podsítě brány Azure Firewall|Zápis CIDR [brány Azure](../../../../firewall/overview.md) podsítě. By měl obsahovat **privátní IP adresa brány firewall Azure** parametru.|
|Skupina prostředků trezoru klíčů|Skupina prostředků|Název|**Uzamčeno** – zřetězí **název organizace** s `-sharedsvcs-kv-rg` aby skupiny prostředků jedinečný.|
|Skupina prostředků trezoru klíčů|Skupina prostředků|Umístění|**Uzamčeno** -používá parametr podrobného plánu.|
|Šablona pro Key Vault|Šablona Resource Manageru|Uživatelské jméno správce serveru typu jumpbox|Uživatelské jméno jumpboxu. Musí odpovídat stejnou hodnotu vlastnosti v **Jumpbox šablony**. Výchozí hodnota je _jb správce_.|
|Šablona pro Key Vault|Šablona Resource Manageru|Klíč SSH nebo heslo správce serveru typu jumpbox|Klíč nebo heslo pro účet na jumpbox. Musí odpovídat stejnou hodnotu vlastnosti v **Jumpbox šablony**. Žádné výchozí hodnoty a nemůže být ponecháno prázdné.|
|Šablona pro Key Vault|Šablona Resource Manageru|Uživatelské jméno správce domény|Uživatelské jméno používané pro přístup k Active Directory virtuálního počítače a připojte se k jiné virtuální počítače k doméně. Musí odpovídat **uživatel s rolí správce domény** hodnoty vlastností v **Active Directory Domain Services šablony**. Výchozí hodnota je _správce domény_.|
|Šablona pro Key Vault|Šablona Resource Manageru|Heslo správce domény|Heslo uživatele správce domény. Žádné výchozí hodnoty a nemůže být ponecháno prázdné.|
|Šablona pro Key Vault|Šablona Resource Manageru|ID objektu AAD|Identifikátor objektu AAD účtu, který vyžaduje přístup k instanci služby Key Vault. Žádné výchozí hodnoty a nemůže být ponecháno prázdné. K vyhledání této hodnoty na webu Azure Portal, vyhledejte a vyberte "Uživatelé" v části _služby_. Použití _název_ pole k filtrování pro název účtu a vyberte tento účet. Na _profilu uživatele_ stránky, vyberte ikonu "Kliknutím zkopírujete" vedle _ID objektu_.  |
|Šablona pro Key Vault|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona pro Key Vault|Šablona Resource Manageru|Skladová jednotka (SKU) pro Key Vault|Určuje SKU služby Key Vault, který je vytvořen. Výchozí hodnota je _Premium_.|
|Jumpbox skupiny prostředků|Skupina prostředků|Název|**Uzamčeno** – zřetězí **název organizace** s `-sharedsvcs-jb-rg` aby skupiny prostředků jedinečný.|
|Jumpbox skupiny prostředků|Skupina prostředků|Umístění|**Uzamčeno** -používá parametr podrobného plánu.|
|Šablona serveru typu jumpbox|Šablona Resource Manageru|Uživatelské jméno správce serveru typu jumpbox|Uživatelské jméno používané pro přístup k jumpboxu virtuálních počítačů. Musí odpovídat stejnou hodnotu vlastnosti v **šablony služby Key Vault**. Výchozí hodnota je _jb správce_.|
|Šablona serveru typu jumpbox|Šablona Resource Manageru|Heslo správce Jumpbox (ID prostředku trezoru klíč)|ID prostředku trezoru klíčů. Použití "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" a nahraďte `{subscriptionId}` svým ID předplatného a `{orgName}` s  **Název organizace** podrobný plán parametru.|
|Šablona serveru typu jumpbox|Šablona Resource Manageru|Heslo správce Jumpbox (název pro Key Vault tajný klíč)|Uživatelské jméno jumpboxu správce. Musí odpovídat hodnotě ve **šablony služby Key Vault** vlastnost **uživatelské jméno Jumpboxu správce**.|
|Šablona serveru typu jumpbox|Šablona Resource Manageru|Operační systém serveru typu jumpbox|Určuje operační systém virtuálnímu počítači jumpboxu. Výchozí hodnota je _Windows_.|
|Skupiny prostředků služby Active Directory Domain Services|Skupina prostředků|Název|**Uzamčeno** – zřetězí **název organizace** s `-sharedsvcs-adds-rg` aby skupiny prostředků jedinečný.|
|Skupiny prostředků služby Active Directory Domain Services|Skupina prostředků|Umístění|**Uzamčeno** -používá parametr podrobného plánu.|
|Šablona pro Active Directory Domain Services|Šablona Resource Manageru|Uživatelské jméno správce domény|Uživatelské jméno jumpboxu AD DS. Musí odpovídat stejnou hodnotu vlastnosti v **šablony služby Key Vault**. Výchozí hodnota je _přidá uživatele správce_.|
|Šablona pro Active Directory Domain Services|Šablona Resource Manageru|Heslo správce domény (ID prostředku trezoru klíč)|ID prostředku trezoru klíčů. Použití "/ subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" a nahraďte `{subscriptionId}` svým ID předplatného a `{orgName}` s  **Název organizace** podrobný plán parametru.|
|Šablona pro Active Directory Domain Services|Šablona Resource Manageru|Heslo správce domény (název pro Key Vault tajný klíč)|Uživatelské jméno správce domény. Musí odpovídat hodnotě ve **šablony služby Key Vault** vlastnost **uživatelské jméno správce domény**.|
|Šablona pro Active Directory Domain Services|Šablona Resource Manageru|Název domény|Název služby Active Directory vytvořené v rámci ukázky. Výchozí hodnota je _contoso.com_.|
|Šablona pro Active Directory Domain Services|Šablona Resource Manageru|Uživatel s rolí správce domény|Uživatelské jméno pro účet správce AD a pro připojení zařízení k doméně AD. Musí odpovídat **uživatelské jméno správce AD** hodnoty vlastností v **šablony služby Key Vault**. Výchozí hodnota je _správce domény_.|
|Šablona pro Active Directory Domain Services|Šablona Resource Manageru|Heslo správce domény|Nastavte podrobnosti služby Key Vault pro ukládání heslo. Žádné výchozí hodnoty a nemůže být ponecháno prázdné.|

## <a name="next-steps"></a>Další postup

Teď, když si kroky pro nasazení ukázky podrobného plánu ISO 27001 Shared Services naleznete v následujících článcích se dozvíte o architektuře a mapování ovládacích prvků:

> [!div class="nextstepaction"]
> [Podrobný plán ISO 27001 Shared Services - přehled](./index.md)
> [podrobného plánu ISO 27001 Shared Services - mapování ovládacích prvků](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)