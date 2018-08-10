---
title: Průvodce vytvořením šablony řešení pro web Marketplace | Dokumentace Microsoftu
description: Podrobné pokyny o tom, jak vytvořit, certifikace a nasadit šablonu řešení pro více virtuálních počítačů bitové kopie pro nákup na Azure Marketplace.
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: e14e05f2-2385-4ce0-b351-0747cb74ba19
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/27/2016
ms.author: hascipio; v-divte
ms.openlocfilehash: 675316b97e821a81ca4946d57a76d7bce978361b
ms.sourcegitcommit: d16b7d22dddef6da8b6cfdf412b1a668ab436c1f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/08/2018
ms.locfileid: "39714542"
---
# <a name="guide-to-create-a-solution-template-for-azure-marketplace"></a>Průvodce Vytvořit šablonu řešení pro Azure Marketplace
Po dokončení kroku 1, [vytváření účtů a registraci][link-acct-creation], budeme vám na základě při vytváření šablony řešení kompatibilního s Azure v [technické požadavky pro vytváření Šablona řešení](marketplace-publishing-solution-template-creation-prerequisites.md). Nyní jsme vás provede kroky pro vytvoření šablony řešení pro několik virtuálních počítačů na [portálu pro publikování] [ link-pubportal] pro Azure Marketplace.

## <a name="create-your-solution-template-offer-in-the-publishing-portal"></a>Vytvoření šablony nabídky řešení na portálu publikování
Přejděte na [ https://publish.windowsazure.com ](http://publish.windowsazure.com). Při přihlašování k poprvé [portálu pro publikování](https://publish.windowsazure.com/), použijte stejný účet profil prodejce společnosti byl zaregistrován. Jakékoli zaměstnance vaší společnosti můžete později přidat jako spolusprávce na portálu publikování.

### <a name="1-select-solution-templates"></a>1. Vyberte "Šablony řešení"
  ![Kreslení][img-pubportal-menu-sol-templ]

### <a name="2-create-a-new-solution-template"></a>2. Vytvořit novou šablonu řešení
  ![Kreslení][img-pubportal-sol-templ-new]

### <a name="3-start-with-topologies"></a>3. Začínáme s topologií
Šablona řešení je „nadřazená“ všem svým topologiím. V jedné šabloně nabídky/řešení můžete definovat více topologií. Pokud je nabídka vložena do přípravy, je vložena se všemi topologiemi. Podle následujících pokynů k definování nabídky:     

* Vytvoření topologie: "Identifikátor topologie" je obvykle název topologie pro šablonu řešení. Identifikátor topologie se používá v adrese URL, jak je znázorněno níže:

  Azure Marketplace: http://azure.microsoft.com/marketplace/partners/{PublisherNamespace}/{OfferIdentifier}{TopologyIdentifier}

  Azure Portal: https://portal.azure.com/#gallery/{PublisherNamespace}.{OfferIdentifier}{TopologyIdentifier}
* Přidejte novou verzi.

### <a name="4-get-your-topology-versions-certified"></a>4. Získání vaší topologie verzí programu certified
Nahrajte soubor zip, který obsahuje všechny soubory potřebné ke zřízení této konkrétní verzi topologie. Tento soubor zip musí obsahovat následující:

* *mainTemplate.json* a *createUiDefinition.json* souboru v kořenovém adresáři.
* Žádné propojené šablony a všechny požadované skripty.

  > [!TIP]
  > Svým vývojářům práci na vytváření řešení topologií šablony a jejich certifikaci, obchodní, marketing a/nebo právní oddělení vaší společnosti můžete pracovat na marketing a právní obsah.
  >
  >

## <a name="next-steps"></a>Další postup
Teď, když jste vytvořili šablonu řešení a nahrání souboru zip, postupujte podle pokynů [Marketplace marketingového obsahu Průvodce](marketplace-publishing-push-to-staging.md) před doručením (push) nabídky do přípravy. Zobrazit kompletní marketplace Publikovat články, najdete v tématu [Začínáme: publikování nabídky na webu Azure Marketplace](marketplace-publishing-getting-started.md).

Může také mohla zajímat tyto související články:

* Image virtuálních počítačů: [o Imagí virtuálních počítačů v Azure](https://msdn.microsoft.com/library/azure/dn790290.aspx)
* Rozšíření virtuálních počítačů: [agenta virtuálního počítače a přehled rozšíření virtuálních počítačů](https://msdn.microsoft.com/library/azure/dn832621.aspx) a [funkcí a rozšíření virtuálních počítačů Azure](https://msdn.microsoft.com/library/azure/dn606311.aspx)
* Azure Resource Manageru: [Tvorba šablon Azure Resource Manageru](../azure-resource-manager/resource-group-authoring-templates.md) a [Příklady jednoduchých šablon](https://github.com/rjmax/ArmExamples)
* Účet úložiště, omezuje: [monitorování pro omezení účtu úložiště](http://blogs.msdn.com/b/mast/archive/2014/08/02/how-to-monitor-for-storage-account-throttling.aspx) a [storage úrovně Premium](../virtual-machines/windows/premium-storage.md#scalability-and-performance-targets)

[img-pubportal-menu-sol-templ]:media/marketplace-publishing-solution-template-creation/pubportal-menu-solution-templates.png
[img-pubportal-sol-templ-new]:media/marketplace-publishing-solution-template-creation/pubportal-solution-template-new.png
[link-acct-creation]:marketplace-publishing-accounts-creation-registration.md
[link-pubportal]:https://publish.windowsazure.com
