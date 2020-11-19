---
title: Jak povolit prostředek řešení Azure VMware
description: Přečtěte si, jak odeslat žádost o podporu pro povolení prostředku řešení Azure VMware. Ve svém stávajícím privátním cloudu řešení Azure VMware si také můžete vyžádat další uzly.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 8e1b891559cb2d01adc9fdf834ef3c9714fe1233
ms.sourcegitcommit: 230d5656b525a2c6a6717525b68a10135c568d67
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/19/2020
ms.locfileid: "94888432"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Jak povolit prostředek řešení Azure VMware
Přečtěte si, jak odeslat žádost o podporu pro povolení prostředku [Řešení Azure VMware](introduction.md) . Ve svém stávajícím privátním cloudu řešení Azure VMware si také můžete vyžádat další uzly.

## <a name="eligibility-criteria"></a>Kritéria způsobilosti

Budete potřebovat účet Azure v předplatném Azure. Předplatné Azure musí splňovat jedno z následujících kritérií:

* Předplatné v rámci [Azure smlouva Enterprise (EA)](../cost-management-billing/manage/ea-portal-agreements.md) s Microsoftem.
* Předplatné spravovaného Cloud Solution Provider (CSP) v rámci plánu Azure.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Povolit řešení Azure VMware pro zákazníky se smlouvou EA
Před vytvořením prostředku řešení Azure VMware budete muset odeslat lístek podpory, abyste měli své uzly přidělené. Jakmile tým podpory obdrží vaši žádost, trvá vám až pět pracovních dní, aby vaši žádost zkontroloval a rozdělil vaše uzly. Pokud máte existující privátní cloud řešení Azure VMware a chcete přidělit více uzlů, Projděte si stejný postup.


1. V Azure Portal v části **pomoc a podpora** vytvořte **[novou žádost o podporu](https://rc.portal.azure.com/#create/Microsoft.Support)** a zadejte pro lístek následující informace:
   - **Typ problému:** Odbornou
   - **Předplatné:** Vyberte své předplatné.
   - **Služba:** Všechny služby > řešení Azure VMware
   - **Prostředek:** Obecná otázka 
   - **Shrnutí:** Potřebná kapacita
   - **Typ problému:** Problémy se správou kapacity
   - **Podtyp problému:** Požadavek zákazníka na další kvótu/kapacitu hostitele

1. V **popisu** lístku podpory zadejte na kartě **Podrobnosti** :

   - KONCEPCE nebo produkce 
   - Název oblasti
   - Počet uzlů
   - Všechny další podrobnosti

   >[!NOTE]
   >Řešení Azure VMware doporučuje minimálně tři uzly, aby se vymíchoval váš privátní cloud a pro redundanci N + 1 uzlů. 

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** odešlete žádost.

   Může trvat až pět pracovních dnů, než se zástupce podpory potvrdí vaší žádosti.

   >[!IMPORTANT] 
   >Pokud už máte nějaké řešení Azure VMware a požadujete další uzly, je potřeba si uvědomit, že k alokaci uzlů potřebujeme pět pracovních dnů. 

1. Než budete moci zřídit své uzly, nezapomeňte zaregistrovat poskytovatele prostředků **Microsoft. AVS** v Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Další způsoby registrace poskytovatele prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Povolit řešení Azure VMware pro zákazníky CSP 

Poskytovatelé CSP musí používat [Partnerské centrum Microsoftu](https://partner.microsoft.com) k povolení řešení Azure VMware pro své zákazníky. 

   >[!IMPORTANT] 
   >Služba řešení Azure VMware neposkytuje víceklientské prostředí, proto hostující partneři ještě nejsou podporované. 

1. V **partnerském centru** vyberte **CSP** pro přístup k oblasti **Customers** .

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Oblast pro zákazníky partnerského centra Microsoftu" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Vyberte zákazníka a pak vyberte **Přidat produkty**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Partnerské centrum Microsoftu" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Vyberte **plán Azure** a pak vyberte **Přidat do košíku**. 

1. Přečtěte si a dokončete Obecné nastavení předplatného Azure pro vaše zákazníky. Další informace najdete v [dokumentaci k Microsoft Partner Center](https://docs.microsoft.com/partner-center/azure-plan-manage).

Po nakonfigurování plánu Azure a povolení oprávnění vSphere RBAC jako CSP zahájíte Microsoftu, aby se povolila kvóta pro předplatné plánu Azure. Přístup k Azure Portal z partnerského centra pomocí procedury **správce jménem** (administrate).

1. Přihlaste se k [partnerskému centru](https://partner.microsoft.com).

1. Vyberte **CSP** pro přístup k oblasti **Customers** .

1. Rozbalte položku Podrobnosti o zákazníkovi a vyberte **portál pro správu Microsoft Azure**.

1. V Azure Portal v části **pomoc a podpora** vytvořte **[novou žádost o podporu](https://rc.portal.azure.com/#create/Microsoft.Support)** a zadejte pro lístek následující informace:
   - **Typ problému:** Odbornou
   - **Předplatné:** Vyberte své předplatné.
   - **Služba:** Všechny služby > řešení Azure VMware
   - **Prostředek:** Obecná otázka 
   - **Shrnutí:** Potřebná kapacita
   - **Typ problému:** Problémy se správou kapacity
   - **Podtyp problému:** Požadavek zákazníka na další kvótu/kapacitu hostitele

1. V **popisu** lístku podpory zadejte na kartě **Podrobnosti** :

   - KONCEPCE nebo produkce 
   - Název oblasti
   - Počet uzlů
   - Všechny další podrobnosti
   - Je určeno k hostování více zákazníků?

   >[!NOTE]
   >Řešení Azure VMware doporučuje minimálně tři uzly, aby se vymíchoval váš privátní cloud a pro redundanci N + 1 uzlů. 

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** odešlete žádost.

   Může trvat až pět pracovních dnů, než se zástupce podpory potvrdí vaší žádosti.

   >[!IMPORTANT] 
   >Pokud už máte nějaké řešení Azure VMware a požadujete další uzly, je potřeba si uvědomit, že k alokaci uzlů potřebujeme pět pracovních dnů. 

1. Po přidání do plánu Azure a s povolenou kvótou může zákazník nebo Partnerská aplikace nasadit privátní cloud řešení Azure VMware prostřednictvím Azure Portal. Než budete moci zřídit své uzly, nezapomeňte zaregistrovat poskytovatele prostředků **Microsoft. AVS** v Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Další způsoby registrace poskytovatele prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="next-steps"></a>Další kroky

Po povolení prostředku řešení Azure VMware a máte k dispozici správné sítě, můžete [vytvořit privátní cloud](tutorial-create-private-cloud.md).