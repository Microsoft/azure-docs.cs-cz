---
title: Jak povolit prostředek řešení Azure VMware
description: Přečtěte si, jak odeslat žádost o podporu pro povolení prostředku řešení Azure VMware. V existujícím privátním cloudu řešení Azure VMware si také můžete vyžádat další hostitele.
ms.topic: how-to
ms.date: 11/12/2020
ms.openlocfilehash: 8bcb61c07a3f0310754cd50931f4576e836deffb
ms.sourcegitcommit: fa807e40d729bf066b9b81c76a0e8c5b1c03b536
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/11/2020
ms.locfileid: "97346533"
---
# <a name="how-to-enable-azure-vmware-solution-resource"></a>Jak povolit prostředek řešení Azure VMware
Přečtěte si, jak odeslat žádost o podporu pro povolení prostředku [Řešení Azure VMware](introduction.md) . V existujícím privátním cloudu řešení Azure VMware si také můžete vyžádat další hostitele.

## <a name="eligibility-criteria"></a>Kritéria způsobilosti

Budete potřebovat účet Azure v předplatném Azure. Předplatné Azure musí splňovat jedno z následujících kritérií:

* Předplatné v rámci [Azure smlouva Enterprise (EA)](../cost-management-billing/manage/ea-portal-agreements.md) s Microsoftem.
* Předplatné spravované poskytovatelem Cloud Solution Provider (CSP) v rámci existujícího zprostředkovatele CSP Azure nabízí smlouvu nebo plán Azure.


## <a name="enable-azure-vmware-solution-for-ea-customers"></a>Povolit řešení Azure VMware pro zákazníky se smlouvou EA
Před vytvořením prostředku řešení Azure VMware budete muset odeslat lístek podpory, který bude mít přidělené hostitele. Jakmile tým podpory obdrží vaši žádost, trvá vám až pět pracovních dní, aby vaši žádost zkontroloval a rozdělila své hostitele. Pokud máte existující privátní cloud řešení Azure VMware a chcete přidělit více hostitelů, Projděte si stejný postup.


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
   - Počet hostitelů
   - Všechny další podrobnosti

   >[!NOTE]
   >Řešení Azure VMware doporučuje minimálně tři hostitele, aby se nastavil váš privátní cloud a redundance N + 1 hostitelů. 

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** odešlete žádost.

   Může trvat až pět pracovních dnů, než se zástupce podpory potvrdí vaší žádosti.

   >[!IMPORTANT] 
   >Pokud už máte nějaké řešení Azure VMware a požadujete další hostitele, je potřeba si uvědomit, že k přidělení hostitelů potřebujeme pět pracovních dnů. 

1. Než budete moci zřídit hostitele, nezapomeňte zaregistrovat poskytovatele prostředků **Microsoft. AVS** v Azure Portal.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Další způsoby registrace poskytovatele prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md).

## <a name="enable-azure-vmware-solution-for-csp-customers"></a>Povolit řešení Azure VMware pro zákazníky CSP 

Poskytovatelé CSP musí používat [Partnerské centrum Microsoftu](https://partner.microsoft.com) k povolení řešení Azure VMware pro své zákazníky. Tento článek používá [plán Azure CSP](/partner-center/azure-plan-lp.md) jako příklad k ilustraci kupního postupu pro partnery.

   >[!IMPORTANT] 
   >Služba řešení Azure VMware neposkytuje víceklientské požadavky. Hostující partneři vyžadují, aby se nepodporovali. 

1. V **partnerském centru** vyberte **CSP** pro přístup k oblasti **Customers** .

   :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Oblast pro zákazníky partnerského centra Microsoftu" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::

1. Vyberte zákazníka a pak vyberte **Přidat produkty**.

   :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Partnerské centrum Microsoftu" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::

1. Vyberte **plán Azure** a pak vyberte **Přidat do košíku**. 

1. Přečtěte si a dokončete Obecné nastavení předplatného Azure pro vaše zákazníky. Další informace najdete v [dokumentaci k Microsoft Partner Center](/partner-center/azure-plan-manage.md).

Po nakonfigurování plánu Azure a nastavení potřebných [oprávnění Azure RBAC](/partner-center/azure-plan-manage.md) pro předplatné zahájíte Microsoftu, aby se povolila kvóta pro předplatné plánu Azure. Přístup k Azure Portal z [partnerského centra Microsoftu](https://partner.microsoft.com) pomocí postupu Administrate ( **admin) jménem uživatele** ().

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
   - Počet hostitelů
   - Všechny další podrobnosti
   - Je určeno k hostování více zákazníků?

   >[!NOTE]
   >Řešení Azure VMware doporučuje minimálně tři hostitele, aby se nastavil váš privátní cloud a redundance N + 1 hostitelů. 

1. Kliknutím na tlačítko **zkontrolovat + vytvořit** odešlete žádost.

   Může trvat až pět pracovních dnů, než se zástupce podpory potvrdí vaší žádosti.

   >[!IMPORTANT] 
   >Pokud už máte nějaké řešení Azure VMware a požadujete další hostitele, je potřeba si uvědomit, že k přidělení hostitelů potřebujeme pět pracovních dnů. 

1. Pokud je předplatné spravováno poskytovatelem služeb, musí mít tým pro správu přístup k Azure Portal postupovat podle pokynů **správce** (administrate) z partnerského centra. Jednu z Azure Portal spustit instanci [Cloud Shell](../cloud-shell/overview.md) a zaregistrovat poskytovatele prostředků **Microsoft. AVS** a pokračovat v nasazení privátního cloudu řešení Azure VMware.  

   ```azurecli-interactive
   az provider register -n Microsoft.AVS --subscription <your subscription ID>
   ```

   Další způsoby registrace poskytovatele prostředků najdete v tématu [poskytovatelé a typy prostředků Azure](../azure-resource-manager/management/resource-providers-and-types.md).

1. Pokud je předplatné přímo spravované zákazníkem, musí registrace poskytovatele prostředků **Microsoft. AVS** provést uživatel s dostatečnými oprávněními v rámci předplatného. Další informace a způsoby, jak registrovat poskytovatele prostředků, najdete v tématu [poskytovatelé prostředků a typy Azure](../azure-resource-manager/management/resource-providers-and-types.md) . 


## <a name="next-steps"></a>Další kroky

Po povolení prostředku řešení Azure VMware a máte k dispozici správné sítě, můžete [vytvořit privátní cloud](tutorial-create-private-cloud.md).
