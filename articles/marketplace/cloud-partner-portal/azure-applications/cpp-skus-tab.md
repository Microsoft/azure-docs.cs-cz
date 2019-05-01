---
title: Konfigurace SKU pro nabídky aplikace Azure | Azure Marketplace
description: Jak nakonfigurovat skladové jednotky Azure spravované aplikace a šablony řešení Azure.
services: Azure, Marketplace, Cloud Partner Portal,
author: dan-wesley
ms.service: marketplace
ms.topic: conceptual
ms.date: 04/23/2019
ms.author: pabutler
ms.openlocfilehash: ef4ea2419c64d0376023ea5d291460df48a51c63
ms.sourcegitcommit: c53a800d6c2e5baad800c1247dce94bdbf2ad324
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/30/2019
ms.locfileid: "64943397"
---
# <a name="azure-application-skus-tab"></a>Karta skladové jednotky Azure aplikace

Tento článek popisuje, jak použít kartu skladové položky k vytvoření skladové položky pro vaše aplikace Azure. 

> [!IMPORTANT]
> Kroky pro konfiguraci SKU se liší pro nabídky na spravované aplikace a nabídku na šablonu řešení. Tyto rozdíly jsou popsány v tomto článku. 

## <a name="configure-azure-application-skus"></a>Konfigurovat aplikace Azure skladové položky

### <a name="create-a-new-sku"></a>Vytvoření nové skladové položky

Pomocí těchto kroků můžete vytvořit novou skladovou Položku:

1. Vyberte **SKU** kartu.
2. V části skladové položky, vyberte **+ novou skladovou Položku**.

    ![Nové skladové položky řádku](./media/azureapp-plus-sku.png)

3. V automaticky otevíraném okně novou skladovou Položku, zadejte **SKU ID**. Toto id je omezen na 50 znaků a musí obsahovat jenom malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. SKU ID nesmí končit spojovníkem.
4. SKU ID je viditelná pro zákazníky v adresách URL produktu, šablon Resource Manageru (Pokud je k dispozici) a hlásí fakturace. Toto id nelze změnit po publikování nabídky.

### <a name="sku-details-for-a-solution-template"></a>Podrobnosti o SKU pro šablonu řešení

Následující snímek obrazovky ukazuje formulář podrobné údaje SKU pro šablonu řešení.

![Formulář podrobností SKU pro šablony řešení](./media/azureapp-sku-details-solutiontemplate.png)

Zadejte následující hodnoty SKU.  Pole s hvězdičkou jsou povinná.

|    Pole         |       Popis                                                            |
|  ---------       |     ---------------                                                          |
|  **Název\***     | Název pro danou skladovou jednotku. Tento název se zobrazí v galerii pro tuto položku.   |
| **Souhrn\***    | Krátký souhrnný popis skladové Položce. (Maximální délka je 100 znaků.)  |
| **Popis\*** | Podrobný popis SKU. Základní HTML je podporována.                 | 
| **Typ SKU\***   | Typ řešení Azure aplikace, vyberte ***šablonu řešení** pro tento scénář. |
| **Dostupnost cloudu\*** | Umístění SKU. Výchozí hodnota je **veřejný Azure**.  <b/>   **Veřejné Azure** – aplikace bude možno nasadit zákazníkům ve všech veřejných oblastech Azure, které integraci webu marketplace.  <b/>   **Azure Government Cloud** – aplikace bude nasazena v cloudu Azure Government. Před publikováním na [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), společnost Microsoft doporučuje vydavatelé otestovat a ověřit svoje řešení funguje podle očekávání v tomto prostředí. Chcete-li připravit a otestovat, požádat [zkušební účet](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).  |
| **Je to privátní SKU?\*** | Vyberte **Ano** Pokud tato skladová položka je dostupná jenom pro vybranou skupinu zákazníků. |
|   |   |

  > [!NOTE] 
  > Microsoft Azure Government je cloud určený pro státní úřady s řízený přístup pro zákazníky od federální, státní, místní nebo kmenové a partnery, kteří mohou k poskytování těchto entit.


### <a name="sku-details-for-managed-application"></a>Podrobnosti o SKU pro spravované aplikace

Následující snímek obrazovky ukazuje formulář podrobné údaje SKU pro spravované aplikace.

   ![Formulář podrobností SKU pro spravované aplikace](./media/azureapp-sku-details-managedapplication.png)

Nakonfigurujte následující nastavení SKU. Pole s hvězdičkou jsou povinná.

|    Pole         |       Popis                                                            |
|  ---------       |     ---------------                                                          |
|  **Název\***     | Název pro danou skladovou jednotku. Tento název se zobrazí v galerii pro tuto položku.   |
| **Souhrn\***    | Krátký souhrnný popis skladové Položce. (Maximální délka je 100 znaků.)  |
| **Popis\*** | Podrobný popis SKU. Základní HTML je podporována.                 | 
| **Typ SKU\***   | Typ řešení Azure aplikace, vyberte ***spravované aplikace** pro tento scénář. 
| **Dostupnost cloudu\*** | Umístění SKU. Výchozí hodnota je **veřejný Azure**.  <b/>   **Veřejné Azure** – aplikace bude možno nasadit zákazníkům ve všech veřejných oblastech Azure, které integraci webu marketplace.  <b/>   **Azure Government Cloud** – aplikace bude nasazena v cloudu Azure Government. Před publikováním na [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), společnost Microsoft doporučuje vydavatelé otestovat a ověřit svoje řešení funguje podle očekávání v tomto prostředí. Chcete-li připravit a otestovat, požádat [zkušební účet](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).   Microsoft Azure Government je cloud určený pro státní úřady s řízený přístup pro zákazníky od federální, státní, místní nebo kmenové a partnery, kteří mohou k poskytování těchto entit. |
| **Je to privátní SKU?\*** | Vyberte **Ano** Pokud tato skladová položka je dostupná jenom pro vybranou skupinu zákazníků. |
| **Dostupnost země/oblast\*** | Použití **vybrat oblasti** k zobrazení seznamu země/oblasti, které jsou k dispozici. Zkontrolujte každé země/oblasti a pak vyberte **OK** k uložení vaší položky.  <b/>   ![Seznam zemí a oblastí dostupnosti](./media/azure-app-select-country-region.png)  |
| **Staré ceny\*** | Ceny pro SKU v USD za měsíc. Ceny se nastavují v místní měně, pomocí aktuálního směnné kurzy na konfiguraci. Ověření tyto, protože nakonec vlastní nastavení. Nastavit nebo zobrazit ceny každé země/oblast jednotlivě, cenové tabulku exportovat a importovat s vlastní ceny.  Musí uložit změny cen Povolit export a import data ocenění.  |
| **Ceny zjednodušené měny\*** | Ceny pro SKU v USD za měsíc. Musí být stejný jako původní ceny. Další informace najdete v tématu [zjednodušená ceny měny](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer). |
|  |  |


### <a name="package-details-for-solution-template"></a>Podrobné informace balíčku pro šablony řešení

   ![Podrobné informace balíčku pro šablony řešení](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

Zadejte následující **podrobnosti balíčku** hodnoty.  Pole s hvězdičkou jsou povinná.

- **Verze\***  – verze balíčku, který bude nahrávat. Verze značky musí být ve tvaru X.Y.Z, kde X, Y a jsou celá čísla.
- **Soubor balíčku (.zip)\***  -tento balíček obsahuje následující soubory uložené v souboru ZIP.
  - MainTemplate.json – soubor šablony nasazení, který se používá k nasazení řešení/aplikace a vytvořte prostředky, které jsou definovány pro řešení. Další informace najdete v tématu [jak si můžete vytvořit soubory šablony nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - createUIDefinition.json – tento soubor používá ke generování uživatelského rozhraní pro zřizování řešení/aplikaci na webu Azure portal. Další informace najdete v tématu [vytvořit Azure portal uživatelského rozhraní pro vaše spravovaná aplikace](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

  >[!IMPORTANT] 
  >Tento balíček měl obsahovat všechny vnořené šablony nebo skripty, které jsou potřebné ke zřízení této aplikace. MainTemplate.json soubor a soubor createUIDefinition.json musí být v kořenové složce.


### <a name="package-details-for-managed-application"></a>Podrobné informace balíčku pro spravované aplikace

   ![Podrobné informace balíčku pro spravované aplikace](./media/azureapp-sku-pkgdetails-managedapplication.png)

Zadejte následující podrobnosti balíčku.  Pole s hvězdičkou jsou povinná.

- **Verze\***  – verze balíčku, který bude nahrávat. Verze značky musí být ve tvaru X.Y.Z, kde X, Y a jsou celá čísla.
- **Soubor balíčku (.zip)\***  -tento balíček obsahuje následující soubory uložené v souboru ZIP.
  - applianceMainTemplate.json – soubor šablony nasazení, který se používá k nasazení řešení/aplikace a vytvořte prostředky, které jsou definovány. Další informace najdete v tématu [rychlý start: Vytvoření a nasazení šablon Azure Resource Manageru pomocí webu Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition.json – tento soubor používá ke generování uživatelského rozhraní pro zřizování řešení/aplikaci na webu Azure portal. Další informace najdete v tématu [vytvořit Azure portal uživatelského rozhraní pro vaše spravovaná aplikace](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate.json – soubor šablony, který obsahuje pouze příslušný prostředek Microsoft.Solution/appliances. Další informace najdete v tématu [Princip struktury a syntaxe šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Mějte na paměti následující klíčové vlastnosti tohoto prostředku:
    - "druh" – hodnota musí být "Marketplace" v případě Marketplace spravované aplikace.
    - "Skupiny prostředků v předplatném zákazníka nasazená všechny prostředky, které jsou definované v applianceMainTemplate.json - ManagedResourceGroupId".
    - "ID balíčku vydavatele" na řetězec, který jednoznačně identifikuje balíček. Tato hodnota je potřeba se vypočte takto: je tvořen [publisherId]. [OfferId]-[SKUID] ve verzi preview. [PackageVersion].

  >[!IMPORTANT] 
  >Tento balíček měl obsahovat všechny vnořené šablony nebo skripty, které jsou potřebné ke zřízení této aplikace. Tyto soubory musí být v kořenové složce:  MainTemplate.json applianceMainTemplate.json a applianceCreateUIDefinition.json.

- **Id tenanta\***  – id tenanta Azure Active Directory vaší organizace.
- **Povolit přístup JIT? \***  – Vyberte **Ano** Just-In-Time povolit přístup pro správu pro použití v rámci této nabídky zákaznická nasazení.

  >[!NOTE] 
  >Pokud povolíte JIT, musí se aktualizovat soubor CreateUiDefinition.json pro podporu přístup JIT.

Pro spravované aplikace musíte nakonfigurovat ověřování a nastavení zásad.


#### <a name="authorization"></a>Autorizace

Přidáte Azure Active Directory identifikátor uživatele, skupinu nebo aplikaci, ke kterému chcete udělit oprávnění pro spravovanou skupinu prostředků. Oprávnění, jemuž byl poskytnut se vyznačuje ID definice role To může být vlastníkem, přispěvatelem nebo jakoukoli vlastní roli.


#### <a name="policy-settings"></a>Nastavení zásad

Přidání zásad, které aplikace spravované v souladu s. Další informace o vytváření zásad prostředků Azure, přečtěte si téma [co je Azure Policy?](../../../governance/policy/overview.md)

   ![Nastavení autorizace a zásad pro spravované aplikace](./media/azureapp-sku-details-managedapp-auth-policy.png)

**Pokud chcete vytvořit nový autorizace:**

1. V části **autorizace**vyberte **+ nové autorizační**.
2. Pro **Id objektu zabezpečení**, zadejte Azure Active Directory identifikátor uživatele, skupinu nebo aplikaci, ke kterému chcete udělit oprávnění pro spravovanou skupinu prostředků. Oprávnění, které je udělen je indikován definice Role.
3. Pro **definice Role**, vyberte jednu z těchto možností z rozevíracího seznamu:  Vlastníkem nebo přispěvatelem. Další informace najdete v tématu [předdefinované role pro prostředky Azure](https://docs.microsoft.com/azure/role-based-access-control/built-in-roles).

>[!NOTE] 
>Je možné přidat více autorizace. Ale doporučuje se vytvořit novou skupinu uživatelů služby Active Directory a zadejte jeho ID v "PrincipalId." Tato možnost povolí přidávání více uživatelů na skupinu uživatelů, aniž byste museli aktualizovat skladovou Položku.

**Chcete-li vytvořit novou zásadu:**

1. V části **nastavení zásad**vyberte **+ nové zásady**.
2. Pro **Název_zásady**, zadejte název zásady. Maximální délka názvu je 50 znaků.
3. Pro **zásady**, vyberte jednu z možností z rozevíracího seznamu. Vyberte zásadu, která chce poskytovatel dat povolit, pokud aplikace používá data. Další informace najdete v tématu [ukázky zásad Azure](https://docs.microsoft.com/azure/governance/policy/samples/index).

    ![Nastavení zásad pro spravované aplikace](./media/azureapp-sku-policy-settings.png)

4. Pro **skladová položka zásad**, vyberte jako zásada typ skladové položky Free nebo Standard. Standardní skladová jednotka je vyžadován pro zásady auditu.


## <a name="next-steps"></a>Další postup

Bude dál popsat vaši nabídku a poskytnout marketingové prostředky v [Marketplace kartu](./cpp-marketplace-tab.md). 
