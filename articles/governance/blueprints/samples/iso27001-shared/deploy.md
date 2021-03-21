---
title: Ukázka podrobného plánu nasazení sdílených služeb ISO 27001
description: Nasaďte kroky pro ukázka podrobného plánu sdílených služeb ISO 27001, včetně podrobností o parametrech artefaktu podrobného plánu.
ms.date: 02/05/2021
ms.topic: sample
ms.openlocfilehash: 2eb9ed2199b28ad7cb349e6088c3cc583d8f011d
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/19/2021
ms.locfileid: "100577326"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Ukázka podrobného plánu pro nasazování sdílených služeb ISO 27001

Chcete-li nasadit ukázku podrobného plánu sdílených služeb Azure modrotisky ISO 27001, je nutné provést následující kroky:

> [!div class="checklist"]
> - Vytvořte nový podrobný plán z ukázky.
> - Kopii ukázky si označte jako **publikovanou**.
> - Přiřaďte kopii podrobného plánu k existujícímu předplatnému.

Pokud ještě předplatné Azure nemáte, vytvořte si napřed [bezplatný účet](https://azure.microsoft.com/free).

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejprve implementujte ukázku podrobného plánu tak, že z ukázky vytvoříte nový podrobný plán ve svém prostředí.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vlevo na stránce **Začínáme** vyberte tlačítko **Vytvořit** v části _Vytvořit podrobný plán_.

1. V části _Další ukázky_ Najděte ukázku podrobného plánu **ISO 27001: Shared Services** a vyberte **použít tuto ukázku**.

1. Zadejte _základní informace_ o ukázce podrobného plánu:

   - **Název** podrobného plánu: zadejte název vaší kopie ukázkového plánu sdílených služeb ISO 27001.
   - **Umístění definice**: použijte tři tečky a vyberte skupinu pro správu, do které se uloží vaše kopie ukázky.

1. Vyberte kartu _Artefakty_ v horní části stránky nebo **Další: Artefakty** dole na stránce.

1. Projděte si seznam artefaktů, které tvoří ukázku podrobného plánu. Mnoho artefaktů má parametry, které budeme definovat později. Až skončíte s kontrolou ukázky podrobného plánu, vyberte **Uložit koncept**.

## <a name="publish-the-sample-copy"></a>Publikování kopie ukázky

V prostředí máte teď vytvořenou kopii ukázky podrobného plánu. Kopie je vytvořená v režimu **Koncept** a než ji budete muset přiřadit a nasadit, musí být **publikovaná**. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo Standard ISO 27001.

1. V levém podokně vyberte **Všechny služby**. Vyhledejte a vyberte **Podrobné plány**.

1. Vyberte stránku **Definice podrobných plánů** vlevo. Pomocí filtrů najděte kopii ukázky podrobného plánu a vyberte ji.

1. Nahoře na stránce vyberte **Publikovat podrobný plán**. Napravo na nové stránce zadejte **verzi** kopie ukázky podrobného plánu. Tato vlastnost je užitečná, protože umožňuje pozdější úpravy. Zadejte **poznámky ke změnám** , například "první verze publikovaná z ukázky ISO 27001 podrobného plánu". Na konci stránky pak vyberte **Publikovat**.

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

   - Parametry podrobného plánu

     Parametry definované v tomto oddílu používá mnoho artefaktů v definici podrobného plánu k zajištění konzistence.

     - **Název organizace**: zadejte krátký název vaší organizace. Tato vlastnost se primárně používá pro pojmenování prostředků.
     - **Předpona adresy podsítě sdílených služeb**: zadejte hodnotu zápisu CIDR pro síťové nasazení nasazených prostředků.
     - **Umístění sdílených služeb**: Určuje umístění, do kterého byly artefakty nasazeny. Ne všechny služby jsou k dispozici ve všech umístěních. Artefakty, které nasazují takové služby, poskytují možnost parametru pro umístění, do kterého se tento artefakt má nasadit.
     - **Povolené umístění (Policy: Plánská iniciativa pro ISO 27001)**: hodnota, která označuje povolená umístění pro skupiny prostředků a prostředky.
     - **Log Analytics pracovní prostor pro agenty virtuálních počítačů (zásady: podrobný plán pro ISO 27001)**: Určuje ID prostředku pracovního prostoru. Tento parametr používá `concat` funkci k vytvoření ID prostředku.

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, ve kterém jsou definovány. Tyto parametry jsou [dynamické parametry](../../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte **Přiřadit** v dolní části stránky. Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure Blueprints a integrované ukázky podrobného plánu jsou **bezplatné**. U prostředků Azure se [cena stanovuje podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktů podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Description|
|-|-|-|-|
|\[Verze Preview \] : nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)|Přiřazení zásady|Volitelné: seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do oboru|Volitelné Výchozí hodnota je _["none"]_.|
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače se systémem Linux|Přiřazení zásady|Volitelné: seznam imagí virtuálních počítačů, které mají podporovaný operační systém Linux pro přidání do oboru|Volitelné Výchozí hodnota je _["none"]_.|
|\[Verze Preview \] : nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)|Přiřazení zásady|Volitelné: seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Volitelné Výchozí hodnota je _["none"]_.|
|\[Verze Preview \] : nasazení agenta Log Analytics pro virtuální počítače s Windows|Přiřazení zásady|Volitelné: seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Volitelné Výchozí hodnota je _["none"]_.|
|Povolené typy prostředků|Přiřazení zásady|Povolené typy prostředků|Seznam typů prostředků, které je možné nasadit Tento seznam se skládá ze všech typů prostředků nasazených ve sdílených službách.|
|Povolené skladové položky účtu úložiště|Přiřazení zásady|Povolené skladové položky úložiště|Seznam povolených SKU účtu úložiště pro diagnostické protokoly. Výchozí hodnota je _["Standard_LRS"]_.|
|Povolené SKU virtuálních počítačů|Přiřazení zásady|Seznam SKU virtuálních počítačů, které je možné nasadit Výchozí hodnota je _["Standard_DS1_v2", "Standard_DS2_v2"]_.|
|Plán iniciativy pro ISO 27001|Přiřazení zásady|Typy prostředků pro audit diagnostických protokolů|Seznam typů prostředků, které se mají auditovat v případě, že nastavení diagnostického protokolu není povolené. Přijatelné hodnoty najdete v [Azure monitor schématech diagnostických protokolů](../../../../azure-monitor/essentials/resource-logs-schema.md#service-specific-schemas).|
|Log Analytics skupina prostředků|Skupina prostředků|Name|**Uzamkl** – zřetězí **název organizace** s `-sharedsvsc-log-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Log Analytics skupina prostředků|Skupina prostředků|Umístění|**Locked** – použije parametr podrobného plánu.|
|Šablona Log Analytics|Šablona Resource Manageru|Úroveň služby|Nastaví úroveň pracovního prostoru Log Analytics. Výchozí hodnota je _PerNode_.|
|Šablona Log Analytics|Šablona Resource Manageru|Uchovávání protokolů ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona Log Analytics|Šablona Resource Manageru|Umístění|Oblast, která se používá k vytvoření pracovního prostoru Log Analytics. Výchozí hodnota je _západní USA 2_.|
|Skupina síťových prostředků|Skupina prostředků|Name|**Uzamkl** – zřetězí **název organizace** s `-sharedsvcs-net-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Skupina síťových prostředků|Skupina prostředků|Umístění|**Locked** – použije parametr podrobného plánu.|
|Šablona Azure Firewall|Šablona Resource Manageru|Privátní IP adresa brány Azure firewall|Nakonfiguruje privátní IP adresu [brány firewall Azure](../../../../firewall/overview.md). Tato hodnota se používá také jako výchozí směrovací tabulka v podsíti sdílených služeb. By měl být součástí zápisu CIDR definovaného v **Azure firewall předponu adresy podsítě**. Výchozí hodnota je _10.0.4.4_.|
|Šablona Azure Firewall|Šablona Resource Manageru|Uchovávání protokolů ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona skupiny zabezpečení sítě|Šablona Resource Manageru|Uchovávání protokolů ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Předpona Virtual Network adresy|Zápis CIDR pro virtuální síť. Výchozí hodnota je _10.0.0.0/16_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Povolit ochranu Virtual Network DDoS|Konfiguruje ochranu DDoS pro virtuální síť. Výchozí hodnota je _true_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Předpona adresy podsítě sdílených služeb|Zápis CIDR pro podsíť sdílených služeb. Výchozí hodnota je _10.0.0.0/24_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Předpona adresy podsítě DMZ|Zápis CIDR pro DMZ podsíť. Výchozí hodnota je _10.0.1.0/24_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Application Gateway předpony adresy podsítě|Zápis CIDR pro podsíť aplikační brány. Výchozí hodnota je _10.0.2.0/24_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Předpona adresy podsítě Virtual Network brány|Zápis CIDR pro podsíť brány virtuální sítě. Výchozí hodnota je _10.0.3.0/24_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Azure Firewall předpony adresy podsítě|Zápis CIDR pro podsíť [brány firewall Azure](../../../../firewall/overview.md) . By měl obsahovat parametr **privátní IP adresy brány firewall Azure** .|
|Key Vault skupina prostředků|Skupina prostředků|Name|**Uzamkl** – zřetězí **název organizace** s `-sharedsvcs-kv-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Key Vault skupina prostředků|Skupina prostředků|Umístění|**Locked** – použije parametr podrobného plánu.|
|Šablona Key Vault|Šablona Resource Manageru|Uživatelské jméno správce JumpBox|Uživatelské jméno pro JumpBox. V **šabloně JumpBox** musí odpovídat stejné hodnotě vlastnosti. Výchozí hodnota je _JB-Admin-User_.|
|Šablona Key Vault|Šablona Resource Manageru|Klíč SSH správce JumpBox nebo heslo|Klíč nebo heslo pro účet na JumpBox V **šabloně JumpBox** musí odpovídat stejné hodnotě vlastnosti. Žádná výchozí hodnota a nemůže být ponechána prázdná.|
|Šablona Key Vault|Šablona Resource Manageru|Uživatelské jméno správce domény|Uživatelské jméno, které se používá pro přístup k virtuálnímu počítači služby Active Directory a k připojení dalších virtuálních počítačů k doméně. Musí se shodovat s hodnotou vlastnosti **uživatele správce domény** v **šabloně Active Directory Domain Services**. Výchozí hodnota je _Domain-Admin-User_.|
|Šablona Key Vault|Šablona Resource Manageru|Heslo správce domény|Heslo uživatele správce domény. Žádná výchozí hodnota a nemůže být ponechána prázdná.|
|Šablona Key Vault|Šablona Resource Manageru|ID objektu AAD|Identifikátor objektu AAD účtu, který vyžaduje přístup k instanci Key Vault. Žádná výchozí hodnota a nemůže být ponechána prázdná. Pokud chcete tuto hodnotu najít z Azure Portal, vyhledejte a v části _služby_ vyberte uživatelé. Pomocí pole _název_ vyfiltrujte název účtu a vyberte tento účet. Na stránce _Profil uživatele_ vyberte ikonu "kliknutím ke kopírování" vedle _ID objektu_.  |
|Šablona Key Vault|Šablona Resource Manageru|Uchovávání protokolů ve dnech|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona Key Vault|Šablona Resource Manageru|SKU Key Vault|Určuje SKLADOVOU položku Key Vault, která je vytvořena. Výchozí hodnota je _Premium_.|
|Skupina prostředků JumpBox|Skupina prostředků|Name|**Uzamkl** – zřetězí **název organizace** s `-sharedsvcs-jb-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Skupina prostředků JumpBox|Skupina prostředků|Umístění|**Locked** – použije parametr podrobného plánu.|
|Šablona JumpBox|Šablona Resource Manageru|Uživatelské jméno správce JumpBox|Uživatelské jméno, které se používá pro přístup k virtuálním počítačům s JumpBox. Musí odpovídat stejné hodnotě vlastnosti v **šabloně Key Vault**. Výchozí hodnota je _JB-Admin-User_.|
|Šablona JumpBox|Šablona Resource Manageru|Heslo správce JumpBox (ID prostředku Key Vault)|ID prostředku Key Vault. Použijte "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" a nahraďte `{subscriptionId}` ID vašeho předplatného a `{orgName}` parametrem **název organizace** podrobný plán.|
|Šablona JumpBox|Šablona Resource Manageru|Heslo správce JumpBox (název tajného klíče Key Vault)|Uživatelské jméno správce JumpBox Ve vlastnosti **šablony Key Vault** **uživatelské jméno správce JumpBox** musí odpovídat hodnotě.|
|Šablona JumpBox|Šablona Resource Manageru|Operační systém JumpBox|Určuje operační systém virtuálního počítače JumpBox. Výchozí hodnota je _Windows_.|
|Active Directory Domain Services skupina prostředků|Skupina prostředků|Name|**Uzamkl** – zřetězí **název organizace** s `-sharedsvcs-adds-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Active Directory Domain Services skupina prostředků|Skupina prostředků|Umístění|**Locked** – použije parametr podrobného plánu.|
|Šablona Active Directory Domain Services|Šablona Resource Manageru|Uživatelské jméno správce domény|Uživatelské jméno pro přidání JumpBox Musí odpovídat stejné hodnotě vlastnosti v **šabloně Key Vault**. Výchozí hodnota je _přidat – admin-User_.|
|Šablona Active Directory Domain Services|Šablona Resource Manageru|Heslo správce domény (ID prostředku Key Vault)|ID prostředku Key Vault. Použijte "/subscriptions/{subscriptionId}/resourceGroups/{orgName}-sharedsvcs-kv-rg/providers/Microsoft.KeyVault/vaults/{orgName}-sharedsvcs-kv" a nahraďte `{subscriptionId}` ID vašeho předplatného a `{orgName}` parametrem **název organizace** podrobný plán.|
|Šablona Active Directory Domain Services|Šablona Resource Manageru|Heslo správce domény (Key Vault název tajného klíče)|Uživatelské jméno správce domény. V Key Vault **uživatelské jméno správce domény** se musí shodovat s hodnotou vlastnosti **šablony** .|
|Šablona Active Directory Domain Services|Šablona Resource Manageru|Název domény|Název adresáře služby Active Directory vytvořeného ukázkou. Výchozí hodnota je _contoso.com_.|
|Šablona Active Directory Domain Services|Šablona Resource Manageru|Uživatel s rolí správce domény|Uživatelské jméno účtu správce služby AD a připojení zařízení k doméně služby AD. Musí odpovídat hodnotě vlastnosti **uživatelského jména správce služby AD** v **šabloně Key Vault**. Výchozí hodnota je _Domain-Admin-User_.|
|Šablona Active Directory Domain Services|Šablona Resource Manageru|Heslo správce domény|Nastavte informace o Key Vault pro uložení hesla. Žádná výchozí hodnota a nemůže být ponechána prázdná.|

## <a name="next-steps"></a>Další kroky

Teď, když jste si prohlédli postup nasazení ukázky podrobného plánu ISO 27001 Shared Services, najdete v následujících článcích informace o architektuře a mapování ovládacích prvků:

> [!div class="nextstepaction"]
> [Plán sdílených služeb ISO 27001 – přehled](./index.md) 
>  [Kontrolní plán sdílených služeb ISO 27001 – mapování ovládacích prvků](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Další informace o [životním cyklu podrobného plánu](../../concepts/lifecycle.md)
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
