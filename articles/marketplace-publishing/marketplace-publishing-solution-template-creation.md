---
title: "Průvodce vytvořením šablony řešení pro Marketplace | Microsoft Docs"
description: "Podrobné pokyny o tom, jak vytvářet, certifikovat a nasadit šablonu více virtuálních počítačů bitové kopie řešení pro zakoupit na webu Azure Marketplace."
services: marketplace-publishing
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: mbaldwin
ms.openlocfilehash: 83e69701aa5e8c11354e7551d66f3fdd18289000
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Průvodce Vytvořit šablonu řešení pro Azure Marketplace
Po dokončení kroku 1, [vytváření účtů a registrace][link-acct-creation], jsme vám na základě na vytvoření šablony řešení kompatibilní s Azure v [technické požadavky pro vytvoření šablony řešení](marketplace-publishing-solution-template-creation-prerequisites.md). Nyní jsme vás provede kroky pro vytvoření šablony řešení pro víc virtuálních počítačů na [publikování portál] [ link-pubportal] pro Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Vytvořit nabídku šablony řešení na portálu publikování
Přejděte na [ https://publish.windowsazure.com ](http://publish.windowsazure.com). Při prvním přihlášení k [publikování portál](https://publish.windowsazure.com/), použijte stejný účet, který byl zaregistrován profil prodejce vaší společnosti. Každý zaměstnanec vaší společnosti můžete později přidat jako spolusprávce na portálu pro publikování.

### <a name="1-select-solution-templates"></a>1. Vyberte možnost "šablony řešení"
  ![Kreslení][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Vytvořte novou šablonu řešení
  ![Kreslení][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Začněte s topologie
Šablona řešení je „nadřazená“ všem svým topologiím. V jedné šabloně nabídky/řešení můžete definovat více topologií. Pokud je nabídka vložena do přípravy, je vložena se všemi topologiemi. Použijte následující postup k definování vaši nabídku:     

* Vytvořit topologii: "Identifikátor topologie" je obvykle název topologie pro šablonu řešení. Identifikátor topologie se používá v adrese URL, jak je uvedeno níže:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Portál Azure: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* Přidejte novou verzi.

### <a name="4-get-your-topology-versions-certified"></a>4. Získání verze vaší topologie certifikaci
Nahrajte soubor zip, který obsahuje všechny požadované soubory ke zřízení tohoto konkrétní verzi topologii. Tento soubor zip musí obsahovat následující:

* *mainTemplate.json* a *createUiDefinition.json* souboru v jeho kořenový adresář.
* Propojených šablon a všechny požadované skripty.

  > [!TIP]
  > Vaše vývojáři práci při vytváření šablony topologie řešení a získávání je certifikaci, business, marketing nebo právní oddělení vaší společnosti můžete pracovat na marketing a právní obsahu.
  >
  >

## <a name="next-steps"></a>Další postup
Teď, když jste vytvořili šablony řešení a nahrát soubor zip, postupujte podle pokynů v [Marketplace marketing obsahu Průvodce](marketplace-publishing-push-to-staging.md) před odesláním nabídku na pracovní. Kompletní marketplace publikování článků naleznete [Začínáme: postup publikování nabídky pro Azure Marketplace](marketplace-publishing-getting-started.md).

Může být také zájem o tyto související články:

* Image virtuálních počítačů: [o Image virtuálních počítačů v Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* Rozšíření virtuálního počítače: [agenta virtuálního počítače a přehled rozšíření virtuálního počítače](https://msdn.microsoft.com/library/azure/dn832621.aspx) a [rozšíření virtuálního počítače Azure a funkce](https://msdn.microsoft.com/library/azure/dn606311.aspx)
* Azure Resource Manager: [vytváření šablon Azure Resource Manager](../azure-resource-manager/resource-group-authoring-templates.md) a [příklady jednoduchou šablonu](https://github.com/rjmax/ArmExamples)
* Omezení účtu úložiště: [monitorování omezení účtu úložiště](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) a [storage úrovně Premium](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
