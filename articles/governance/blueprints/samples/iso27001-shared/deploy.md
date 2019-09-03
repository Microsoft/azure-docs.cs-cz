---
title: Ukázka – podrobný plán sdílených služeb ISO 27001 – postup nasazení
description: Nasaďte kroky v ukázce podrobného plánu pro sdílené služby ISO 27001.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/14/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 04285ace1713a03a812f1c79895da64e6dac42a8
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231712"
---
# <a name="deploy-the-iso-27001-shared-services-blueprint-sample"></a>Ukázka podrobného plánu pro nasazování sdílených služeb ISO 27001

Chcete-li nasadit ukázku podrobného plánu sdílených služeb Azure modrotisky ISO 27001, je nutné provést následující kroky:

> [!div class="checklist"]
> - Vytvořit nový podrobný plán z ukázky
> - Označení kopie ukázky jako **publikované**
> - Přiřazení kopie podrobného plánu k existujícímu předplatnému

Pokud ještě nemáte předplatné Azure, vytvořte si [bezplatný účet](https://azure.microsoft.com/free) před tím, než začnete.

## <a name="create-blueprint-from-sample"></a>Vytvořit podrobný plán z ukázky

Nejdřív implementujte ukázku podrobného plánu vytvořením nového podrobného plánu ve vašem prostředí pomocí ukázky jako Starter.

1. Vyberte **všechny služby** a vyhledejte a v levém podokně vyberte **zásady** . Na stránce **zásady** vyberte **plány**.

1. Na stránce **Začínáme** na levé straně vyberte v části _vytvořit podrobný plán_tlačítko **vytvořit** .

1. **Vyhledejte ISO 27001: Ukázka podrobného plánu sdílených služeb** v _jiných ukázkách_ a vyberte možnost **použít tuto ukázku**.

1. Zadejte _základy_ ukázky podrobného plánu:

   - **Název**podrobného plánu: Zadejte název vaší kopie ukázky podrobného plánu ISO 27001 Shared Services.
   - **Umístění definice**: Použijte tři tečky a vyberte skupinu pro správu, do které chcete kopii ukázky Uložit.

1. Vyberte kartu _artefakty_ v horní části stránky nebo **další: Artefakty** v dolní části stránky.

1. Zkontrolujte seznam artefaktů, které tvoří ukázku podrobného plánu. Mnohé z artefaktů mají parametry, které budeme definovat později. Po dokončení kontroly ukázkového plánu vyberte **Uložit koncept** .

## <a name="publish-the-sample-copy"></a>Publikovat ukázkovou kopii

Vaše kopie ukázky podrobného plánu se teď vytvořila ve vašem prostředí. Je vytvořená v režimu konceptu a musí být publikována před tím, než bude možné ji přiřadit a nasadit. Kopii ukázky podrobného plánu můžete přizpůsobit vašemu prostředí a potřebám, ale tato změna se může přesunout mimo Standard ISO 27001.

1. Vyberte **všechny služby** a vyhledejte a v levém podokně vyberte **zásady** . Na stránce **zásady** vyberte **plány**.

1. Na levé straně vyberte stránku **definice** podrobného plánu. Pomocí filtrů Najděte kopii ukázky podrobného plánu a vyberte ji.

1. V horní části stránky vyberte **publikovat podrobný plán** . Na stránce Nová na pravé straně zadejte **verzi** pro kopii ukázky podrobného plánu. Tato vlastnost je užitečná, pokud uděláte změnu později. Zadejte **poznámky ke změnám** , například "první verze publikovaná z ukázky ISO 27001 podrobného plánu". Potom v dolní části stránky vyberte **publikovat** .

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

   - Parametry podrobného plánu

     Parametry definované v tomto oddílu používá mnoho artefaktů v definici podrobného plánu k zajištění konzistence.

     - **Název organizace**: Zadejte krátký název vaší organizace. Tato vlastnost se primárně používá pro pojmenování prostředků.
     - **Předpona adresy podsítě sdílených služeb**: Zadejte hodnotu zápisu CIDR pro vytváření síťových nasazených prostředků společně.
     - **Umístění sdílených služeb**: Určuje umístění, do kterého jsou artefakty nasazeny. Ne všechny služby jsou k dispozici ve všech umístěních. Artefakty, které nasazují takové služby, poskytují možnost parametru pro umístění, do kterého se tento artefakt má nasadit.
     - **Povolené umístění (zásady: Plán iniciativy pro ISO 27001)** : Hodnota, která označuje povolená umístění pro skupiny prostředků a prostředky.
     - **Log Analytics pracovní prostor pro agenty virtuálních počítačů (zásady: Plán iniciativy pro ISO 27001)** : Určuje ID prostředku pracovního prostoru. Tento parametr používá `concat` funkci k vytvoření ID prostředku.

   - Parametry artefaktů

     Parametry definované v této části se vztahují na artefakt, ve kterém je definován. Tyto parametry jsou [dynamické parametry](../../concepts/parameters.md#dynamic-parameters) , protože jsou definovány během přiřazení podrobného plánu. Úplný seznam nebo parametry artefaktu a jejich popis najdete v tématu [tabulka parametrů artefaktů](#artifact-parameters-table).

1. Po zadání všech parametrů vyberte v dolní části stránky **přiřadit** . Vytvoří se přiřazení podrobného plánu a spustí se nasazení artefaktu. Nasazení trvá zhruba hodinu. Chcete-li zjistit stav nasazení, otevřete přiřazení podrobného plánu.

> [!WARNING]
> Služba Azure modrotisky a předdefinované ukázky podrobného plánu jsou **zdarma**. Ceny prostředků Azure se účtují [podle produktu](https://azure.microsoft.com/pricing/). Pomocí [cenové kalkulačky](https://azure.microsoft.com/pricing/calculator/) můžete odhadnout náklady na provozované prostředky nasazené touto ukázkou podrobného plánu.

## <a name="artifact-parameters-table"></a>Tabulka parametrů artefaktů

Následující tabulka uvádí seznam parametrů artefaktu podrobného plánu:

|Název artefaktu|Typ artefaktu|Název parametru|Popis|
|-|-|-|-|
|\[Verze\]Preview: Nasazení agenta Log Analytics pro Linux VM Scale Sets (VMSS)|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů s podporovaným operačním systémem Linux, který se má přidat do oboru|Volitelné Výchozí hodnota je _["none"]_ .|
|\[Verze\]Preview: Nasazení agenta Log Analytics pro virtuální počítače se systémem Linux|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů s podporovaným operačním systémem Linux, který se má přidat do oboru|Volitelné Výchozí hodnota je _["none"]_ .|
|\[Verze\]Preview: Nasazení agenta Log Analytics pro Windows VM Scale Sets (VMSS)|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Volitelné Výchozí hodnota je _["none"]_ .|
|\[Verze\]Preview: Nasazení agenta Log Analytics pro virtuální počítače s Windows|Přiřazení zásad|Volitelné: Seznam imagí virtuálních počítačů s podporovaným operačním systémem Windows, který se má přidat do oboru|Volitelné Výchozí hodnota je _["none"]_ .|
|Povolené typy prostředků|Přiřazení zásad|Povolené typy prostředků|Seznam typů prostředků, které je možné nasadit Tento seznam se skládá ze všech typů prostředků nasazených ve sdílených službách.|
|Povolené skladové položky účtu úložiště|Přiřazení zásad|Povolené skladové položky úložiště|Seznam povolených SKU účtu úložiště pro diagnostické protokoly. Výchozí hodnota je _["Standard_LRS"]_ .|
|Povolené skladové položky virtuálních počítačů|Přiřazení zásad|Seznam SKU virtuálních počítačů, které je možné nasadit Výchozí hodnota je _["Standard_DS1_v2", "Standard_DS2_v2"]_ .|
|Iniciativa podrobného plánu pro ISO 27001|Přiřazení zásad|Typy prostředků pro audit diagnostických protokolů|Seznam typů prostředků, které se mají auditovat v případě, že nastavení diagnostického protokolu není povolené. Přijatelné hodnoty najdete v [Azure monitor schématech diagnostických protokolů](../../../../azure-monitor/platform/diagnostic-logs-schema.md#supported-log-categories-per-resource-type).|
|Log Analytics skupina prostředků|Resource group|Name|**Uzamkl** – zřetězí **název organizace** s `-sharedsvsc-log-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Log Analytics skupina prostředků|Resource group|Location|**Locked** – použije parametr podrobného plánu.|
|Šablona pro Log Analytics|Šablona Resource Manageru|Úroveň služby|Nastaví úroveň pracovního prostoru Log Analytics. Výchozí hodnota je _PerNode_.|
|Šablona pro Log Analytics|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona pro Log Analytics|Šablona Resource Manageru|Location|Oblast, která se používá k vytvoření pracovního prostoru Log Analytics. Výchozí hodnota je _západní USA 2_.|
|Skupina síťových prostředků|Resource group|Name|**Uzamkl** – zřetězí **název organizace** s `-sharedsvcs-net-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Skupina síťových prostředků|Resource group|Location|**Locked** – použije parametr podrobného plánu.|
|Šablona pro Azure Firewall|Šablona Resource Manageru|Privátní IP adresa brány Azure Firewall|Nakonfiguruje privátní IP adresu [brány firewall Azure](../../../../firewall/overview.md). Tato hodnota se používá také jako výchozí směrovací tabulka v podsíti sdílených služeb. By měl být součástí zápisu CIDR definovaného v **Azure firewall předponu adresy podsítě**. Výchozí hodnota je _10.0.4.4_.|
|Šablona pro Azure Firewall|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona skupiny zabezpečení sítě|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Předpona adresy virtuální sítě|Zápis CIDR pro virtuální síť. Výchozí hodnota je _10.0.0.0/16_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Povolit ve virtuální síti službu DDoS Protection|Konfiguruje ochranu DDoS pro virtuální síť. Výchozí hodnota je _true_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Předpona adresy podsítě sdílených služeb|Zápis CIDR pro podsíť sdílených služeb. Výchozí hodnota je _10.0.0.0/24_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Předpona adresy podsítě DMZ|Zápis CIDR pro DMZ podsíť. Výchozí hodnota je _10.0.1.0/24_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Předpona adresy podsítě brány Application Gateway|Zápis CIDR pro podsíť aplikační brány. Výchozí hodnota je _10.0.2.0/24_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Předpona adresy podsítě Virtual Network brány|Zápis CIDR pro podsíť brány virtuální sítě. Výchozí hodnota je _10.0.3.0/24_.|
|Šablona směrovací tabulky Virtual Network a|Šablona Resource Manageru|Předpona adresy podsítě brány Azure Firewall|Zápis CIDR pro podsíť [brány firewall Azure](../../../../firewall/overview.md) . By měl obsahovat parametr **privátní IP adresy brány firewall Azure** .|
|Key Vault skupina prostředků|Resource group|Name|**Uzamkl** – zřetězí **název organizace** s `-sharedsvcs-kv-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Key Vault skupina prostředků|Resource group|Location|**Locked** – použije parametr podrobného plánu.|
|Šablona pro Key Vault|Šablona Resource Manageru|Uživatelské jméno správce serveru typu jumpbox|Uživatelské jméno pro JumpBox. V **šabloně JumpBox**musí odpovídat stejné hodnotě vlastnosti. Výchozí hodnota je _JB-Admin-User_.|
|Šablona pro Key Vault|Šablona Resource Manageru|Klíč SSH správce JumpBox nebo heslo|Klíč nebo heslo pro účet na JumpBox V **šabloně JumpBox**musí odpovídat stejné hodnotě vlastnosti. Žádná výchozí hodnota a nemůže být ponechána prázdná.|
|Šablona pro Key Vault|Šablona Resource Manageru|Uživatelské jméno správce domény|Uživatelské jméno, které se používá pro přístup k virtuálnímu počítači služby Active Directory a k připojení dalších virtuálních počítačů k doméně. Musí se shodovat s hodnotou vlastnosti **uživatele správce domény** v **šabloně Active Directory Domain Services**. Výchozí hodnota je _Domain-Admin-User_.|
|Šablona pro Key Vault|Šablona Resource Manageru|Heslo správce domény|Heslo uživatele správce domény. Žádná výchozí hodnota a nemůže být ponechána prázdná.|
|Šablona pro Key Vault|Šablona Resource Manageru|ID objektu AAD|Identifikátor objektu AAD účtu, který vyžaduje přístup k instanci Key Vault. Žádná výchozí hodnota a nemůže být ponechána prázdná. Pokud chcete tuto hodnotu najít z Azure Portal, vyhledejte a v části _služby_vyberte uživatelé. Pomocí pole _název_ vyfiltrujte název účtu a vyberte tento účet. Na stránce _Profil uživatele_ vyberte ikonu "kliknutím ke kopírování" vedle _ID objektu_.  |
|Šablona pro Key Vault|Šablona Resource Manageru|Doba uchovávání protokolu (dny)|Uchovávání dat ve dnech. Výchozí hodnota je _365_.|
|Šablona pro Key Vault|Šablona Resource Manageru|Skladová jednotka (SKU) pro Key Vault|Určuje SKLADOVOU položku Key Vault, která je vytvořena. Výchozí hodnota je _Premium_.|
|Skupina prostředků JumpBox|Resource group|Name|**Uzamkl** – zřetězí **název organizace** s `-sharedsvcs-jb-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Skupina prostředků JumpBox|Resource group|Location|**Locked** – použije parametr podrobného plánu.|
|Šablona serveru typu jumpbox|Šablona Resource Manageru|Uživatelské jméno správce serveru typu jumpbox|Uživatelské jméno, které se používá pro přístup k virtuálním počítačům s JumpBox. Musí odpovídat stejné hodnotě vlastnosti v **šabloně Key Vault**. Výchozí hodnota je _JB-Admin-User_.|
|Šablona serveru typu jumpbox|Šablona Resource Manageru|Heslo správce JumpBox (ID prostředku Key Vault)|ID prostředku Key Vault. Použijte "/Subscriptions/{SubscriptionId}/resourceGroups/{orgName}-sharedsvcs-KV-RG/Providers/Microsoft.KeyVault/Vaults/{orgName}-sharedsvcs-KV" a nahraďte `{subscriptionId}` ID vašeho předplatného a `{orgName}` **organizací. název** parametru podrobného plánu|
|Šablona serveru typu jumpbox|Šablona Resource Manageru|Heslo správce JumpBox (název tajného klíče Key Vault)|Uživatelské jméno správce JumpBox Ve vlastnosti **šablony Key Vault** **uživatelské jméno správce JumpBox**musí odpovídat hodnotě.|
|Šablona serveru typu jumpbox|Šablona Resource Manageru|Operační systém serveru typu jumpbox|Určuje operační systém virtuálního počítače JumpBox. Výchozí hodnota je _Windows_.|
|Active Directory Domain Services skupina prostředků|Resource group|Name|**Uzamkl** – zřetězí **název organizace** s `-sharedsvcs-adds-rg` cílem vytvořit skupinu prostředků jako jedinečnou.|
|Active Directory Domain Services skupina prostředků|Resource group|Location|**Locked** – použije parametr podrobného plánu.|
|Šablona pro Active Directory Domain Services|Šablona Resource Manageru|Uživatelské jméno správce domény|Uživatelské jméno pro přidání JumpBox Musí odpovídat stejné hodnotě vlastnosti v **šabloně Key Vault**. Výchozí hodnota je _přidat – admin-User_.|
|Šablona pro Active Directory Domain Services|Šablona Resource Manageru|Heslo správce domény (ID prostředku Key Vault)|ID prostředku Key Vault. Použijte "/Subscriptions/{SubscriptionId}/resourceGroups/{orgName}-sharedsvcs-KV-RG/Providers/Microsoft.KeyVault/Vaults/{orgName}-sharedsvcs-KV" a nahraďte `{subscriptionId}` ID vašeho předplatného a `{orgName}` **organizací. název** parametru podrobného plánu|
|Šablona pro Active Directory Domain Services|Šablona Resource Manageru|Heslo správce domény (Key Vault název tajného klíče)|Uživatelské jméno správce domény. V Key Vault **uživatelské jméno správce domény**se musí shodovat s hodnotou vlastnosti **šablony** .|
|Šablona pro Active Directory Domain Services|Šablona Resource Manageru|Název domény|Název adresáře služby Active Directory vytvořeného ukázkou. Výchozí hodnota je _contoso.com_.|
|Šablona pro Active Directory Domain Services|Šablona Resource Manageru|Uživatel s rolí správce domény|Uživatelské jméno účtu správce služby AD a připojení zařízení k doméně služby AD. Musí odpovídat hodnotě vlastnosti **uživatelského jména správce služby AD** v **šabloně Key Vault**. Výchozí hodnota je _Domain-Admin-User_.|
|Šablona pro Active Directory Domain Services|Šablona Resource Manageru|Heslo správce domény|Nastavte informace o Key Vault pro uložení hesla. Žádná výchozí hodnota a nemůže být ponechána prázdná.|

## <a name="next-steps"></a>Další postup

Teď, když jste si prohlédli postup nasazení ukázky podrobného plánu ISO 27001 Shared Services, najdete v následujících článcích informace o architektuře a mapování ovládacích prvků:

> [!div class="nextstepaction"]
> [Kontrolní plán sdílených služeb ISO 27001 – přehled](./index.md)
> [mapování sdílených služeb ISO 27001](./control-mapping.md)

Další články věnované podrobným plánům a postupu jejich využití:

- Přečtěte si informace o [životním cyklu](../../concepts/lifecycle.md)podrobného plánu.
- Principy použití [statických a dynamických parametrů](../../concepts/parameters.md)
- Další informace o přizpůsobení [pořadí podrobných plánů](../../concepts/sequencing-order.md)
- Použití [zamykání prostředků podrobného plánu](../../concepts/resource-locking.md)
- Další informace o [aktualizaci existujících přiřazení](../../how-to/update-existing-assignments.md)
