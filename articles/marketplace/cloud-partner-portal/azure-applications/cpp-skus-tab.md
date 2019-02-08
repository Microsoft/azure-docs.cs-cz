---
title: Konfigurace SKU pro nabídky aplikace Azure | Dokumentace Microsoftu
description: Jak nakonfigurovat skladové jednotky Azure spravované aplikace a šablony řešení Azure.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: dan-wesley
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: conceptual
ms.date: 12/06/2018
ms.author: pbutlerm
ms.openlocfilehash: 7f73e71677a700ba8047246addb2d27799c5a616
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/08/2019
ms.locfileid: "55890888"
---
# <a name="azure-application-skus-tab"></a>Karta skladové jednotky Azure aplikace

Tento článek popisuje, jak použít kartu skladové položky k vytvoření skladové položky pro vaše aplikace Azure. 

>[!IMPORTANT] 
Kroky pro konfiguraci SKU se liší pro nabídky na spravované aplikace a nabídku na šablonu řešení. Tyto rozdíly jsou popsány v tomto článku. 

## <a name="configure-azure-application-skus"></a>Konfigurovat aplikace Azure skladové položky

### <a name="create-a-new-sku"></a>Vytvoření nové skladové položky

Pomocí těchto kroků můžete vytvořit novou skladovou Položku:

1. Vyberte **SKU** kartu.
2. V části skladové položky, vyberte **+ novou skladovou Položku**.

    ![Nové skladové položky řádku](./media/azureapp-plus-sku.png)

3. V automaticky otevíraném okně novou skladovou Položku, zadejte **SKU ID**. Toto id je omezen na 50 znaků a musí obsahovat jenom malá písmena, alfanumerické znaky, pomlčky nebo podtržítka. SKU ID nesmí končit spojovníkem.
4. SKU ID je viditelná pro zákazníky v adresách URL produktu, šablon Resource Manageru (Pokud je k dispozici) a hlásí fakturace. Toto id nelze změnit po publikování nabídky.

### <a name="sku-details-for-a-solution-template"></a>Podrobnosti o SKU pro šablonu řešení

Zadejte následující nastavení SKU:

- **Název** – název pro danou skladovou jednotku. Tento název se zobrazí v galerii pro tuto položku.
- **Souhrn** – krátký souhrnný popis skladové Položce. (Maximální délka je 100 znaků.)
- **Popis** – podrobný popis SKU.
- **Typ SKU** – rozevírací seznam s těmito hodnotami: "Šablona řešení" a "Spravované aplikace". V tomto scénáři vyberte **šablonu řešení**.
- **Dostupnost cloudu** – umístění SKU. Výchozí hodnota je **veřejný Azure**.
Veřejné Azure – tento virtuální počítač bude nasaditelný zákazníkům ve všech veřejných oblastech Azure, které integraci webu Marketplace.
- **Azure Government Cloud** -tento virtuální počítač se nasadí v cloudu Azure Government. Před publikováním na [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), společnost Microsoft doporučuje vydavatelé otestovat a ověřit svoje řešení funguje podle očekávání v prostředí. Chcete-li připravit a otestovat, požádat [zkušební účet](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).

  >[!NOTE] 
  >Microsoft Azure Government je cloud určený pro státní úřady s řízený přístup pro zákazníky od federální, státní, místní nebo kmenové a partnery, kteří mohou k poskytování těchto entit.

- **Je to privátní SKU?** – Pokud vyberte Ano toto SKU je dostupná jenom pro vybranou skupinu zákazníků.

    ![Formulář podrobností SKU pro šablony řešení](./media/azureapp-sku-details-solutiontemplate.png)

### <a name="sku-details-for-managed-application"></a>Podrobnosti o SKU pro spravované aplikace

Následující snímek obrazovky ukazuje formulář podrobné údaje SKU pro spravované aplikace.

   ![Formulář podrobností SKU pro spravované aplikace](./media/azureapp-sku-details-managedapplication.png)

Nakonfigurujte následující nastavení SKU:

- **Název** – název pro danou skladovou jednotku. Tento název se zobrazí v galerii pro tuto položku.
- **Souhrn** – krátký souhrnný popis skladové Položce. (Maximální délka je 100 znaků.)
- **Popis** – podrobný popis SKU.
- **Typ SKU** – rozevírací seznam s těmito hodnotami: "Šablona řešení" a "Spravované aplikace". V tomto scénáři vyberte **spravované aplikace**.
- **Dostupnost cloudu** – umístění SKU. Výchozí hodnota je **veřejný Azure**.
- **Veřejné Azure** -tento virtuální počítač bude možno nasadit zákazníkům ve všech veřejných oblastech Azure, které integraci webu Marketplace.
- **Azure Government Cloud** -tento virtuální počítač se nasadí v cloudu Azure Government. Před publikováním na [Azure Government](https://docs.microsoft.com/azure/azure-government/documentation-government-manage-marketplace-partners), společnost Microsoft doporučuje vydavatelé otestovat a ověřit svoje řešení funguje podle očekávání v prostředí. Chcete-li připravit a otestovat, požádat [zkušební účet](https://azure.microsoft.com/offers/ms-azr-usgov-0044p/).

  >[!NOTE] 
  >Microsoft Azure Government je cloud určený pro státní úřady s řízený přístup pro zákazníky od federální, státní, místní nebo kmenové a partnery, kteří mohou k poskytování těchto entit.

- **Je to privátní SKU?** – Pokud vyberte Ano toto SKU je dostupná jenom pro vybranou skupinu zákazníků.
- **Země/oblast dostupnosti** – použití **vybrat oblasti** k zobrazení seznamu země/oblasti, které jsou k dispozici. Zkontrolujte každé země/oblasti a pak vyberte **OK** k uložení vaší položky. 

   ![Seznam zemí a oblastí dostupnosti](./media/azure-app-select-country-region.png)

- **Staré ceny** – zadejte ceny pro SKU v USD za měsíc. Ceny se nastavují v místní měně, pomocí aktuálního směnné kurzy na konfiguraci. Ověření tyto, protože nakonec vlastní nastavení. Nastavit nebo zobrazit ceny každé země/oblast jednotlivě, cenové tabulku exportovat a importovat s vlastní ceny.

  >[!NOTE]
  >Uložte provedené změny cen Povolit export a import data ocenění.

- **Zjednodušená ceny měny** – zadejte ceny pro SKU v USD za měsíc. Musí být stejný jako původní ceny. Další informace najdete v tématu [zjednodušená ceny měny](https://docs.microsoft.com/azure/marketplace/cloud-partner-portal-orig/cloud-partner-portal-update-existing-offer).

### <a name="package-details-for-solution-template"></a>Podrobné informace balíčku pro šablony řešení

Zadejte následující podrobnosti balíčku:

- **Verze** – verze balíčku, který bude nahrávat. Verze značky musí být ve tvaru X.Y.Z, kde X, Y a jsou celá čísla.
- **Soubor balíčku (.zip)** -tento balíček obsahuje následující soubory uložené v souboru ZIP.
  - MainTemplate.json – soubor šablony nasazení, který se používá k nasazení řešení/aplikace a vytvořte prostředky, které jsou definovány pro řešení. Další informace najdete v tématu [jak si můžete vytvořit soubory šablony nasazení](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-create-first-template).
  - createUIDefinition.json – tento soubor používá ke generování uživatelského rozhraní pro zřizování řešení/aplikaci na webu Azure portal. Další informace najdete v tématu [vytvořit Azure portal uživatelského rozhraní pro vaše spravovaná aplikace](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).

  >[!IMPORTANT] 
  >Tento balíček měl obsahovat všechny vnořené šablony nebo skripty, které jsou potřebné ke zřízení této aplikace. MainTemplate.json soubor a soubor createUIDefinition.json musí být v kořenové složce.

   ![Podrobné informace balíčku pro šablony řešení](./media/azureapp-sku-pkgdetails-solutiontemplate.png)

### <a name="package-details-for-managed-application"></a>Podrobné informace balíčku pro spravované aplikace

Zadejte následující podrobnosti balíčku:

- **Verze** – verze balíčku, který bude nahrávat. Verze značky musí být ve tvaru X.Y.Z, kde X, Y a jsou celá čísla.
- **Soubor balíčku (.zip)** -tento balíček obsahuje následující soubory uložené v souboru ZIP.
  - applianceMainTemplate.json – soubor šablony nasazení, který se používá k nasazení řešení/aplikace a vytvořte prostředky, které jsou definovány. Další informace najdete v tématu [rychlý start: Vytvoření a nasazení šablon Azure Resource Manageru pomocí webu Azure portal](https://docs.microsoft.com/azure/azure-resource-manager/resource-manager-quickstart-create-templates-use-the-portal). 
  - applianceCreateUIDefinition.json – tento soubor používá ke generování uživatelského rozhraní pro zřizování řešení/aplikaci na webu Azure portal. Další informace najdete v tématu [vytvořit Azure portal uživatelského rozhraní pro vaše spravovaná aplikace](https://docs.microsoft.com/azure/azure-resource-manager/managed-application-createuidefinition-overview).
  - mainTemplate.json – soubor šablony, který obsahuje pouze příslušný prostředek Microsoft.Solution/appliances. Další informace najdete v tématu [Princip struktury a syntaxe šablon Azure Resource Manageru](https://docs.microsoft.com/azure/azure-resource-manager/resource-group-authoring-templates). <br>
Mějte na paměti následující klíčové vlastnosti tohoto prostředku:
    - "druh" – hodnota musí být "Marketplace" v případě Marketplace spravované aplikace.
    - "Skupiny prostředků v předplatném zákazníka nasazená všechny prostředky, které jsou definované v applianceMainTemplate.json - ManagedResourceGroupId".
    - "ID balíčku vydavatele" na řetězec, který jednoznačně identifikuje balíček. Tato hodnota je potřeba se vypočte takto: je tvořen [publisherId]. [OfferId]-[SKUID] ve verzi preview. [PackageVersion].

  >[!IMPORTANT] 
  >Tento balíček měl obsahovat všechny vnořené šablony nebo skripty, které jsou potřebné ke zřízení této aplikace. Tyto soubory musí být v kořenové složce:  MainTemplate.json applianceMainTemplate.json a applianceCreateUIDefinition.json.

- **Id tenanta** – id tenanta Azure Active Directory vaší organizace.
- **Povolit přístup JIT?** – Vyberte **Ano** povolit přístup ke správě Just-In-Time pro použití v rámci této nabídky zákaznická nasazení.

  >[!NOTE] 
  >Pokud povolíte JIT, musí se aktualizovat soubor CreateUiDefinition.json pro podporu přístup JIT.

   ![Podrobné informace balíčku pro spravované aplikace](./media/azureapp-sku-pkgdetails-managedapplication.png)

Pro spravované aplikace musíte nakonfigurovat ověřování a nastavení zásad.

#### <a name="authorization"></a>Autorizace

Přidáte Azure Active Directory identifikátor uživatele, skupinu nebo aplikaci, ke kterému chcete udělit oprávnění pro spravovanou skupinu prostředků. Oprávnění, jemuž byl poskytnut se vyznačuje ID definice role Je možné vlastníkem, přispěvatelem nebo jakoukoli vlastní roli.

#### <a name="policy-settings"></a>Nastavení zásad

Přidání zásad, které aplikace spravované v souladu s. Další informace o vytváření zásad prostředků Azure, přečtěte si téma [co je Azure Policy?](https://docs.microsoft.com/azure/azure-policy/azure-policy-introduction) 


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

[Karta Marketplace](./cpp-marketplace-tab.md)
