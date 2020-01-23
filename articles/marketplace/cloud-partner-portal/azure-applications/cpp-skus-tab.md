---
title: Konfigurace SKU pro nabídku aplikací Azure | Azure Marketplace
description: Jak nakonfigurovat skladové jednotky pro spravovanou aplikaci Azure a šablonu řešení Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: 2430d7e6fa74438c148d3cb849510be06243faa0
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/23/2020
ms.locfileid: "76543150"
---
# <a name="azure-application-skus-tab"></a>Karta SKU aplikací Azure

Tento článek popisuje, jak pomocí karty SKU vytvořit SKU pro vaši aplikaci Azure. 

> [!IMPORTANT]
> Postup konfigurace SKU se liší pro nabídku spravované aplikace a šablonu řešení. Tyto rozdíly jsou popsány v tomto článku. 

## <a name="configure-azure-application-skus"></a>Konfigurace SKU aplikací Azure

### <a name="create-a-new-sku"></a>Vytvořit novou SKLADOVOU položku

Pomocí těchto kroků vytvořte novou SKLADOVOU položku:

1. Vyberte kartu **SKU** .
2. V části SKU vyberte **+ Nová SKU**.

    ![Výzva k zadání nové SKU](./media/azureapp-plus-sku.png)

3. V místním okně nové SKU zadejte **ID SKU**. Toto ID je omezené na 50 znaků a musí obsahovat jenom malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. ID SKU nemůže končit spojovníkem.
4. ID SKU je viditelné pro zákazníky v adresách URL produktů, Správce prostředků šablonách (pokud jsou k dispozici) a fakturačních sestavách. Po publikování nabídky nelze toto ID změnit.

### <a name="sku-details-for-a-solution-template"></a>Podrobnosti o SKU pro šablonu řešení

Další snímek obrazovky zobrazuje formulář podrobností SKU pro šablonu řešení.

![Formulář podrobností SKU pro šablonu řešení](./media/azureapp-sku-details-solutiontemplate.png)

Zadejte následující hodnoty SKU.  Pole připojená pomocí hvězdičky jsou povinná.

|    Pole         |       Popis                                                            |
|  ---------       |     ---------------                                                          |
|  **\* názvu**     | Název SKU. Tento název se zobrazí v galerii pro tuto položku.   |
| **Souhrn\***    | Krátký souhrnný popis skladové položky. (Maximální délka je 100 znaků.)  |
| **Popis\*** | Podrobný popis SKU. Podporuje se základní HTML.                 | 
| **\* typu SKU**   | Typ řešení aplikace Azure, vyberte ***Šablona řešení** pro tento scénář. |
| **\* dostupnosti cloudu** | Umístění SKU. Výchozí hodnota je **Public Azure**.  <b/>**veřejné Azure** -aplikace budou nasazené pro zákazníky ve všech veřejných oblastech Azure, které mají integraci na webu Marketplace.  **cloudová** aplikace <b/>Azure Government bude nasazená v cloudu Azure Government. Před publikováním [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)společnost Microsoft doporučuje vydavatelům otestovat a ověřit, že řešení funguje v tomto prostředí jako očekávané. Pro přípravu a testování si vyžádejte [zkušební účet](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **Je to privátní SKU?\*** | Vyberte **Ano** , pokud je tato SKU k dispozici pouze pro vybranou skupinu zákazníků. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Government je Cloud pro státní správu, který má řízený přístup pro zákazníky od federální, státní, místní nebo samosprávnéé organizace a partneři, kteří mají nárok na poskytování těchto entit.


### <a name="sku-details-for-managed-application"></a>Podrobnosti o SKU pro spravovanou aplikaci

Další snímek obrazovky zobrazuje formulář podrobností SKU pro spravovanou aplikaci.

   ![Formulář podrobností SKU pro spravovanou aplikaci](./media/azureapp-sku-details-managedapplication.png)

Nakonfigurujte následující nastavení SKU. Pole připojená pomocí hvězdičky jsou povinná.

|    Pole         |       Popis                                                            |
|  ---------       |     ---------------                                                          |
|  **\* názvu**     | Název SKU. Tento název se zobrazí v galerii pro tuto položku.   |
| **Souhrn\***    | Krátký souhrnný popis skladové položky. (Maximální délka je 100 znaků.)  |
| **Popis\*** | Podrobný popis SKU. Podporuje se základní HTML.                 | 
| **\* typu SKU**   | Typ řešení aplikace Azure, vyberte ***spravovaná aplikace** pro tento scénář. 
| **\* dostupnosti cloudu** | Umístění SKU. Výchozí hodnota je **Public Azure**.  <b/>**veřejné Azure** -aplikace budou nasazené pro zákazníky ve všech veřejných oblastech Azure, které mají integraci na webu Marketplace.  **cloudová** aplikace <b/>Azure Government bude nasazená v cloudu Azure Government. Před publikováním [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners)společnost Microsoft doporučuje vydavatelům otestovat a ověřit, že řešení funguje v tomto prostředí jako očekávané. Pro přípravu a testování si vyžádejte [zkušební účet](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   Microsoft Azure Government je Cloud pro státní správu, který má řízený přístup pro zákazníky od federální, státní, místní nebo samosprávnéé organizace a partneři, kteří mají nárok na poskytování těchto entit. |
| **Je to privátní SKU?\*** | Vyberte **Ano** , pokud je tato SKU k dispozici pouze pro vybranou skupinu zákazníků. |
| **\* dostupnosti země nebo oblasti** | Pomocí **Vybrat oblasti** můžete zobrazit seznam zemí nebo oblastí, které jsou k dispozici. Zkontrolujte každou zemi nebo oblast a pak vyberte **OK** . tím uložíte vybrané.  seznam dostupnosti ![země a oblasti <b/>](./media/azure-app-select-country-region.png)  |
| **Staré cenové\*** | Cena za SKLADOVOU položku v USD za měsíc. Ceny se v místní měně nastavují pomocí současných směnných kurzů po konfiguraci. Ověřte je, protože tato nastavení nakonec vlastníte. Pokud chcete nastavit nebo zobrazit cenu každé země/oblasti jednotlivě, exportujte prosím tabulku cen a importujte ji s vlastními cenami.  Aby bylo možné povolit export a import dat cen, je nutné uložit změny cen.  |
| **\* ceny pro zjednodušenou měnu** | Cena za SKLADOVOU položku v USD za měsíc. Toto musí být stejné jako staré ceny. Další informace najdete v článku o [cenách za zjednodušenou měnu](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Podrobnosti balíčku pro šablonu řešení

![Podrobnosti balíčku pro šablonu řešení](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Zadejte následující hodnoty **podrobností balíčku** .  Pole připojená pomocí hvězdičky jsou povinná.

- **Verze\*** – verze balíčku, který budete nahrávat. Značky verze musí mít formát X. Y. Z, kde X, Y a Z jsou celá čísla.
- **Soubor balíčku (. zip)\*** – tento balíček obsahuje následující soubory, které jsou uložené v souboru. zip.
  - **mainTemplate. json\*** – soubor šablony nasazení, který se používá k nasazení řešení nebo aplikace a vytvoření prostředků definovaných pro řešení. Další informace najdete v tématu [vytváření souborů šablon nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - **createUIDefinition. json\*** – tento soubor používá Azure Portal k vygenerování uživatelského rozhraní pro zřízení tohoto řešení/aplikace. Další informace najdete v tématu [vytvoření Azure Portal uživatelského rozhraní pro spravovanou aplikaci](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - Skripty (v případě potřeby) – jakékoli další skripty, které mohou být vyžadovány při spuštění šablony, například `Microsoft.Compute/virtualMachines/extensions`.
  - Vnořené šablony (Pokud je potřeba) – jakékoli další vnořené šablony.

  > [!IMPORTANT] 
  > Tento balíček by měl obsahovat všechny vnořené šablony nebo skripty, které jsou potřeba k zřízení této aplikace. Soubor mainTemplate. JSON a soubor createUIDefinition. JSON musí být v kořenové složce. Další informace o artefaktech nasazení najdete v tématu [Azure Resource Manager šablon – Průvodce osvědčenými postupy](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts).


### <a name="package-details-for-managed-application"></a>Podrobnosti balíčku pro spravovanou aplikaci

   ![Podrobnosti balíčku pro spravovanou aplikaci](./media/azureapp-sku-pkgdetails-managedapplication.png)

Zadejte následující podrobnosti o balíčku.  Pole připojená pomocí hvězdičky jsou povinná.

- **Verze\*** – verze balíčku, který budete nahrávat. Značky verze musí mít formát X. Y. Z, kde X, Y a Z jsou celá čísla.
- **Soubor balíčku (. zip)\*** – tento balíček obsahuje následující soubory, které jsou uložené v souboru. zip.
  - applianceMainTemplate. JSON – soubor šablony nasazení, který se používá k nasazení řešení nebo aplikace a vytvoření prostředků, které jsou definovány. Další informace najdete v tématu [rychlý Start: vytvoření a nasazení šablon Azure Resource Manager pomocí Azure Portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition. JSON – tento soubor používá Azure Portal k vygenerování uživatelského rozhraní pro zřízení tohoto řešení nebo aplikace. Další informace najdete v tématu [vytvoření Azure Portal uživatelského rozhraní pro spravovanou aplikaci](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate. JSON – soubor šablony, který obsahuje jenom prostředek Microsoft. Solution/spotřebičs. Další informace najdete v tématu [pochopení struktury a syntaxe šablon Azure Resource Manager](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Všimněte si následujících klíčových vlastností tohoto prostředku:
    - "druh" – hodnota by měla být "Marketplace" v případě aplikace spravované na webu Marketplace.
    - "ManagedResourceGroupId" – skupina prostředků v předplatném zákazníka, kde budou nasazeny všechny prostředky definované v applianceMainTemplate. JSON.
    - "PublisherPackageId" – řetězec, který jedinečně identifikuje balíček. Tato hodnota musí být konstruována takto: Jedná se o zřetězení [publisherId]. [Hodnotami OfferId]-Preview [SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Tento balíček by měl obsahovat všechny vnořené šablony nebo skripty, které jsou potřeba k zřízení této aplikace. Tyto soubory musí být v kořenové složce: MainTemplate. JSON, applianceMainTemplate. JSON a applianceCreateUIDefinition. JSON.

- **ID tenanta\*** – id tenanta Azure Active Directory vaší organizace.
- **Povolit přístup JIT?\*** – vyberte **Ano** , pokud chcete povolit přístup pro správu za běhu pro zákaznická nasazení pomocí této nabídky.

  >[!NOTE] 
  >Pokud povolíte JIT, je nutné aktualizovat soubor CreateUiDefinition. JSON pro podporu přístupu JIT.

Pro spravovanou aplikaci musíte nakonfigurovat nastavení autorizace a zásady.


#### <a name="authorization"></a>Autorizace

Přidejte Azure Active Directory identifikátor uživatele, skupiny nebo aplikace, ke kterému chcete udělit oprávnění pro spravovanou skupinu prostředků. Udělené oprávnění je určeno ID definice role. Může to být vlastník, přispěvatel nebo libovolná vlastní role.


#### <a name="policy-settings"></a>Nastavení zásad

Přidejte zásady, se kterými spravovaná aplikace vyhovuje. Další informace o zásadách prostředků Azure najdete v tématu [co je Azure Policy?](../../../governance/policy/overview.md)

   ![Nastavení autorizace a zásad pro spravovanou aplikaci](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Chcete-li vytvořit novou autorizaci:**

1. V části **autorizace**vyberte **+ nová autorizace**.
2. Do pole **ID objektu zabezpečení**zadejte Azure Active Directory identifikátor uživatele, skupiny nebo aplikace, ke kterému chcete udělit oprávnění pro spravovanou skupinu prostředků. Udělené oprávnění je určeno definicí role.
3. V poli **definice role**vyberte jednu z těchto možností z rozevíracího seznamu: vlastník nebo Přispěvatel. Další informace najdete v tématu [Předdefinované role pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Dá se přidat víc autorizací. Doporučuje se však vytvořit skupinu uživatelů služby Active Directory a zadat její ID v "PrincipalId". Tím se umožní přidání dalších uživatelů do skupiny uživatelů bez nutnosti aktualizace SKU.

**Vytvoření nové zásady:**

1. V části **nastavení zásad**vyberte **+ Nová zásada**.
2. Do pole **název zásady**zadejte název zásady. Maximální délka názvu je 50 znaků.
3. V rozevíracím seznamu pro **zásady**vyberte jednu z možností. Vyberte zásadu, kterou chce zprostředkovatel dat povolit, když aplikace používá data. Další informace najdete v [ukázkách Azure Policy](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Nastavení zásad pro spravovanou aplikaci](./media/azureapp-sku-policy-settings.png)

4. Jako položku **SKU zásady**vyberte jako typ SKU zásady možnost Free nebo Standard. Pro zásady auditu se vyžaduje standardní SKU.


## <a name="next-steps"></a>Další kroky

Na [kartě Marketplace](./cpp-marketplace-tab.md)budete podrobněji popsáni nabídky a poskytnutím marketingových materiálů. 
