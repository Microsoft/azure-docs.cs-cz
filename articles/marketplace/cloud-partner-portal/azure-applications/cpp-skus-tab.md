---
title: Konfigurace skum pro nabídku aplikace Azure | Azure Marketplace
description: Jak nakonfigurovat skumy pro spravovanou aplikaci Azure a šablonu řešení Azure.
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: dsindona
ms.openlocfilehash: 043394a1303456ce5b209bb84b5afaf09f6beba4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80289069"
---
# <a name="azure-application-skus-tab"></a>Karta SKU aplikace Azure

Tento článek popisuje, jak pomocí karty SKU k vytvoření sous pro vaši aplikaci Azure. 

> [!IMPORTANT]
> Postup konfigurace skladové položky se liší pro nabídku spravované aplikace a nabídku šablony řešení. Tyto rozdíly jsou popsány v tomto článku. 

## <a name="configure-azure-application-skus"></a>Konfigurace souaplikací aplikací Azure

### <a name="create-a-new-sku"></a>Vytvoření nové skladové položky

Pomocí následujících kroků vytvořte novou skladovou položku:

1. Vyberte kartu **SKU.**
2. V části Skladové položky vyberte **+ Nová skladová položka**.

    ![Výzva k nové skladové položkě](./media/azureapp-plus-sku.png)

3. Do místního okna Nová skladová položka zadejte **ID skladové položky**. Toto id je omezeno na 50 znaků a musí se skládat pouze z malé, alfanumerické znaky, pomlčky nebo podtržítka. ID soupoložky nemůže skončit pomlčkou.
4. ID skladové položky je zákazníkům viditelné v adresách URL produktů, šablonách Správce prostředků (pokud je to možné) a v sestavách fakturace. Toto id nelze po publikování nabídky změnit.

### <a name="sku-details-for-a-solution-template"></a>Podrobnosti skladové položky pro šablonu řešení

Další snímek obrazovky zobrazuje formulář Podrobnosti skladové položky pro šablonu řešení.

![Formulář podrobností skladové položky pro šablonu řešení](./media/azureapp-sku-details-solutiontemplate.png)

Zadejte následující hodnoty skladové položky.  Jsou povinná pole připojená hvězdičkou.

|    Pole         |       Popis                                                            |
|  ---------       |     ---------------                                                          |
|  **Název\***     | Název skladové položky. Tento název je zobrazen v galerii pro tuto položku.   |
| **Souhrn\***    | Stručný souhrnný popis skladové položky. (Maximální délka je 100 znaků.)  |
| **Popis\*** | Podrobný popis skladové položky. Základní HTML je podporován.                 | 
| **Typ skladové položky\***   | Typ aplikačního řešení Azure, vyberte ***Šablona řešení** pro tento scénář. |
| **Dostupnost cloudu\*** | Umístění skladové položky. Výchozí hodnota je **Veřejný Azure**.  <b/>   **Veřejné Azure** – aplikace bude nasaditelná pro zákazníky ve všech veřejných oblastech Azure, které mají integraci marketplace.  <b/>   **Azure Government Cloud** – aplikace se nasadí v Azure Government Cloud. Před publikováním na [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), Microsoft doporučuje vydavatelé test a ověřit jejich řešení funguje podle očekávání v tomto prostředí. Chcete-li fázi a testování, požádejte o [zkušební účet](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **Je to soukromá SKU?\*** | Vyberte **Ano,** pokud je tato skladová položka dostupná pouze pro vybranou skupinu zákazníků. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Government je cloud vládní komunity s kontrolovaným přístupem pro zákazníky z amerických federálních, státních, místních nebo kmenových a partnerů způsobilých k poskytování těchto entit.


### <a name="sku-details-for-managed-application"></a>Podrobnosti skladové položky pro spravovanou aplikaci

Další snímek obrazovky zobrazuje formulář Podrobnosti skladové položky pro spravovanou aplikaci.

   ![Formulář podrobností skladové položky pro spravovanou aplikaci](./media/azureapp-sku-details-managedapplication.png)

Nakonfigurujte následující nastavení skladové položky. Jsou povinná pole připojená hvězdičkou.

|    Pole         |       Popis                                                            |
|  ---------       |     ---------------                                                          |
|  **Název\***     | Název skladové položky. Tento název je zobrazen v galerii pro tuto položku.   |
| **Souhrn\***    | Stručný souhrnný popis skladové položky. (Maximální délka je 100 znaků.)  |
| **Popis\*** | Podrobný popis skladové položky. Základní HTML je podporován.                 | 
| **Typ skladové položky\***   | Typ aplikačního řešení Azure, vyberte ***Spravovaná aplikace** pro tento scénář. 
| **Dostupnost cloudu\*** | Umístění skladové položky. Výchozí hodnota je **Veřejný Azure**.  <b/>   **Veřejné Azure** – aplikace bude nasaditelná pro zákazníky ve všech veřejných oblastech Azure, které mají integraci marketplace.  <b/>   **Azure Government Cloud** – aplikace se nasadí v Azure Government Cloud. Před publikováním na [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), Microsoft doporučuje vydavatelé test a ověřit jejich řešení funguje podle očekávání v tomto prostředí. Chcete-li fázi a testování, požádejte o [zkušební účet](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   Microsoft Azure Government je cloud vládní komunity s kontrolovaným přístupem pro zákazníky z amerických federálních, státních, místních nebo kmenových a partnerů způsobilých k poskytování těchto entit. |
| **Je to soukromá SKU?\*** | Vyberte **Ano,** pokud je tato skladová položka dostupná pouze pro vybranou skupinu zákazníků. |
| **Dostupnost země/oblasti\*** | Pomocí **funkce Vybrat oblasti** můžete zobrazit seznam dostupných zemí nebo oblastí. Zkontrolujte jednotlivé země nebo oblasti a pak vyberte **OK,** chcete-li uložit výběr.  <b/>   ![Seznam dostupnosti zemí a oblastí](./media/azure-app-select-country-region.png)  |
| **Staré ceny\*** | Cena za Skladovou položku v USD za měsíc. Ceny jsou stanoveny v místní měně pomocí aktuálních směnných kurzů při konfiguraci. Ověřte je, protože nakonec vlastníte tato nastavení. Chcete-li nastavit nebo zobrazit cenu jednotlivých zemí nebo oblastí jednotlivě, exportujte tabulku cen a importujte s vlastními cenami.  Chcete-li povolit export nebo import dat o cenách, je nutné uložit změny cen.  |
| **Zjednodušené ceny měn\*** | Cena za Skladovou položku v USD za měsíc. To musí být stejné jako staré ceny. Další informace naleznete v [tématu Zjednodušené oceňování měn](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Podrobnosti balíčku pro šablonu řešení

![Podrobnosti o balíčku pro šablonu řešení](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Zadejte následující hodnoty **podrobnosti balíčku.**  Jsou povinná pole připojená hvězdičkou.

- **Verze\* ** - verze balíčku, který nahrajete. Značky verze musí být ve tvaru X.Y.Z, kde X, Y a Z jsou celá čísla.
- **Soubor balíčku (.zip)\* ** - Tento balíček obsahuje následující soubory uložené v souboru ZIP.
  - **mainTemplate.json\* ** - Soubor šablony nasazení, který se používá k nasazení řešení nebo aplikace a vytvoření prostředků definovaných pro řešení. Další informace naleznete v [tématu How to author deployment template files](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - **createUIDefinition.json\* ** - Tento soubor používá portál Azure ke generování uživatelského rozhraní pro zřizování tohoto řešení nebo aplikace. Další informace najdete [v tématu Vytvoření uživatelského rozhraní portálu Azure pro spravovanou aplikaci](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - Skripty (v případě potřeby) - Všechny další skripty, které `Microsoft.Compute/virtualMachines/extensions`mohou být požadovány při spuštění šablony, například .
  - Vnořené šablony (v případě potřeby) – všechny další vnořené šablony.

  > [!IMPORTANT] 
  > Tento balíček by měl obsahovat všechny vnořené šablony nebo skripty, které jsou potřebné k zřízení této aplikace. Soubor mainTemplate.json a createUIDefinition.json musí být v kořenové složce. Další informace o artefaktech nasazení najdete [v tématu Šablony Správce prostředků Azure – Průvodce doporučenými postupy](https://github.com/Azure/azure-quickstart-templates/blob/master/1-CONTRIBUTION-GUIDE/best-practices.md#deployment-artifacts-nested-templates-scripts).


### <a name="package-details-for-managed-application"></a>Podrobnosti balíčku pro spravovanou aplikaci

   ![Podrobnosti balíčku pro spravovanou aplikaci](./media/azureapp-sku-pkgdetails-managedapplication.png)

Zadejte následující podrobnosti balíčku.  Jsou povinná pole připojená hvězdičkou.

- **Verze\* ** - verze balíčku, který nahrajete. Značky verze musí být ve tvaru X.Y.Z, kde X, Y a Z jsou celá čísla.
- **Soubor balíčku (.zip)\* ** - Tento balíček obsahuje následující soubory uložené v souboru ZIP.
  - applianceMainTemplate.json - Soubor šablony nasazení, který se používá k nasazení řešení nebo aplikace a vytvoření prostředků, které jsou definovány. Další informace najdete [v tématu Úvodní příručka: Vytvoření a nasazení šablon Azure Resource Manageru pomocí portálu Azure](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition.json - Tento soubor se používá na portálu Azure ke generování uživatelského rozhraní pro zřizování tohoto řešení/aplikace. Další informace najdete [v tématu Vytvoření uživatelského rozhraní portálu Azure pro spravovanou aplikaci](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate.json - Soubor šablony, který obsahuje pouze prostředek Microsoft.Solution/appliances. Další informace najdete [v tématu Principy struktury a syntaxe šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Všimněte si následujících vlastností klíče tohoto prostředku:
    - "druh" – hodnota by měla být "Marketplace" v případě aplikace spravované marketplace.
    - "ManagedResourceGroupId" - skupina prostředků v předplatném zákazníka, kde budou nasazeny všechny prostředky definované v applianceMainTemplate.json.
    - "PublisherPackageId"- Řetězec, který jednoznačně identifikuje balíček. Tato hodnota musí být konstruována takto: je to zřetězení [publisherId]. [OfferId]-preview[SKUID]. [PackageVersion].

  >[!IMPORTANT] 
  >Tento balíček by měl obsahovat všechny vnořené šablony nebo skripty, které jsou potřebné k zřízení této aplikace. Tyto soubory musí být v kořenové složce: MainTemplate.json, applianceMainTemplate.json a applianceCreateUIDefinition.json.

- **ID\* klienta** – ID klienta služby Azure Active Directory vaší organizace.
- **Chcete povolit přístup k JIT? \* ** - Výběrem možnosti **Ano** povolíte přístup ke správě just-in-time pro nasazení zákazníků pomocí této nabídky.

  >[!NOTE] 
  >Pokud povolíte JIT, musíte aktualizovat soubor CreateUiDefinition.json pro podporu přístupu JIT.

U spravované aplikace je nutné nakonfigurovat nastavení autorizace a zásad.


#### <a name="authorization"></a>Autorizace

Přidejte identifikátor služby Azure Active Directory uživatele, skupiny nebo aplikace, které chcete udělit oprávnění ke skupině spravovaných prostředků. Udělené oprávnění je označeno ID definice role. Může to být vlastník, přispěvatel nebo jakákoli vlastní role.


#### <a name="policy-settings"></a>Zásada Nastavení

Přidejte zásady, které spravovaná aplikace splňuje. Další informace o zásadách prostředků Azure najdete v tématu [Co je zásady Azure?](../../../governance/policy/overview.md)

   ![Nastavení autorizace a zásad pro spravovanou aplikaci](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Vytvoření nové autorizace:**

1. V části **Autorizace**vyberte **možnost + Nová autorizace**.
2. Do **id jistiny**zadejte identifikátor služby Azure Active Directory uživatele, skupiny nebo aplikace, které chcete udělit oprávnění spravované skupině prostředků. Udělené oprávnění je označeno definicí role.
3. V **případě definice role**vyberte jednu z těchto možností z rozevíracího seznamu: Vlastník nebo Přispěvatel. Další informace najdete v tématu [Předdefinované role pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Lze přidat více autorizací. Doporučuje se však vytvořit skupinu uživatelů služby Active Directory a zadat její ID v "PrincipalId.". To umožní přidání více uživatelů do skupiny uživatelů bez nutnosti aktualizovat skladovou položku.

**Vytvoření nové zásady:**

1. V části **Nastavení zásad**vyberte + **Nové zásady**.
2. Do **pole Název zásady**zadejte název této zásady. Maximální délka názvu je 50 znaků.
3. V **části Zásady**vyberte jednu z možností z rozevíracího seznamu. Vyberte zásadu, kterou chce mít poskytovatel dat povolen, když aplikace používá data. Další informace najdete v tématu [Ukázky zásad Azure](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Nastavení zásad pro spravovanou aplikaci](./media/azureapp-sku-policy-settings.png)

4. Pro **skladovou položku zásad**vyberte jako typ skladové položky zásady volné nebo standardní. Standardní skladová položka je vyžadována pro zásady auditu.


## <a name="next-steps"></a>Další kroky

Dále popíšete své nabídky a dodáte marketingové prostředky na [kartě Marketplace](./cpp-marketplace-tab.md). 
