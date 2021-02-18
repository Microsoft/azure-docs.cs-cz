---
title: Požádat o kvótu hostitele a povolit řešení Azure VMware
description: Přečtěte si, jak požádat o kvótu a kapacitu hostitele a povolit poskytovatele prostředků řešení Azure VMware. Můžete také požádat o další hostitele v existujícím privátním cloudu řešení Azure VMware.
ms.topic: how-to
ms.custom: contperf-fy21q3
ms.date: 02/17/2021
ms.openlocfilehash: 5d154f5c63ffccdbf1729e641133b54be478d884
ms.sourcegitcommit: 227b9a1c120cd01f7a39479f20f883e75d86f062
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/18/2021
ms.locfileid: "100653158"
---
# <a name="request-host-quota-and-enable-azure-vmware-solution"></a>Požádat o kvótu hostitele a povolit řešení Azure VMware

V tomto postupu se naučíte, jak požádat o kvótu a kapacitu hostitele a povolit poskytovatele prostředků [Řešení Azure VMware](introduction.md) , který službu povoluje. Než budete moct povolit řešení Azure VMware, budete muset odeslat lístek podpory, aby se vaši hostitelé přidělili. Pokud máte existující privátní cloud řešení Azure VMware a chcete přidělit více hostitelů, budete postupovat podle stejného postupu.

>[!IMPORTANT]
>Přidělení hostitelů může trvat několik dní v závislosti na požadovaném počtu.  Proto si požádejte o to, co je potřeba k zřizování, takže nemusíte vyžádat zvýšení kvóty často.


Celkový proces je jednoduchý a obsahuje dva kroky:
- Požádat o další kvótu hostitele nebo kapacitu pro zákazníky se [smlouvou EA](#request-host-quota-for-ea-customers) nebo pro [zákazníky CSP](#request-host-quota-for-csp-customers) 
- Povolení poskytovatele prostředků Microsoft. AVS

## <a name="eligibility-criteria"></a>Kritéria způsobilosti

Budete potřebovat účet Azure v předplatném Azure. Předplatné Azure musí následovat po jednom z následujících kritérií:

- Předplatné v rámci [Azure smlouva Enterprise (EA)](../cost-management-billing/manage/ea-portal-agreements.md) s Microsoftem.
- Předplatné spravované poskytovatelem Cloud Solution Provider (CSP) v rámci existujícího zprostředkovatele CSP Azure nabízí smlouvu nebo plán Azure.

## <a name="request-host-quota-for-ea-customers"></a>Požádat o kvótu hostitele pro zákazníky se smlouvou EA

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


## <a name="request-host-quota-for-csp-customers"></a>Požádat o kvótu hostitele pro zákazníky CSP 

Poskytovatelé CSP musí používat [Partnerské centrum Microsoftu](https://partner.microsoft.com) k povolení řešení Azure VMware pro své zákazníky. Tento článek používá [plán Azure CSP](/partner-center/azure-plan-lp) jako příklad k ilustraci kupního postupu pro partnery.

Přístup k Azure Portal pomocí procedury **správce jménem** (administrate) z partnerského centra.

>[!IMPORTANT] 
>Služba řešení Azure VMware neposkytuje víceklientské požadavky. Hostující partneři vyžadují, aby se nepodporovali. 

1. Konfigurace plánu Azure CSP:

   1. V **partnerském centru** vyberte **CSP** pro přístup k oblasti **Customers** .
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-customers-screen.png" alt-text="Oblast pro zákazníky partnerského centra Microsoftu" lightbox="media/enable-azure-vmware-solution/csp-customers-screen.png":::
   
   1. Vyberte zákazníka a pak vyberte **Přidat produkty**.
   
      :::image type="content" source="media/enable-azure-vmware-solution/csp-partner-center.png" alt-text="Partnerské centrum Microsoftu" lightbox="media/enable-azure-vmware-solution/csp-partner-center.png":::
   
   1. Vyberte **plán Azure** a pak vyberte **Přidat do košíku**. 
   
   1. Přečtěte si a dokončete Obecné nastavení předplatného Azure Plan pro vašeho zákazníka. Další informace najdete v [dokumentaci k Microsoft Partner Center](/partner-center/azure-plan-manage).

1. Až nakonfigurujete plán Azure a máte pro toto předplatné potřebná [oprávnění pro Azure RBAC](/partner-center/azure-plan-manage) , vyžádáte si kvótu pro předplatné plánu Azure. 

   1. Přístup k Azure Portal z [partnerského centra Microsoftu](https://partner.microsoft.com) pomocí postupu **správce jménem** (administrate).
   
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

## <a name="register-the-microsoftavs-resource-provider"></a>Zaregistrujte poskytovatele prostředků **Microsoft. AVS** .

[!INCLUDE [register-resource-provider-steps](includes/register-resource-provider-steps.md)]


## <a name="next-steps"></a>Další kroky

Po povolení prostředku a správné síťové sítě můžete [vytvořit privátní cloud](tutorial-create-private-cloud.md).
