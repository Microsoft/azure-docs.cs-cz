---
title: Přehled o tom, jak vytvořit a nasadit v rámci nabídky na webu Marketplace | Dokumentace Microsoftu
description: Seznamte se s kroky museli stát schválené vývojář Microsoftu a vytvářet a nasazovat image virtuálního počítače, šablony, služba dat nebo služba pro vývojáře na webu Azure Marketplace
services: marketplace-publishing
documentationcenter: ''
author: HannibalSII
manager: hascipio
editor: ''
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: hascipio
ms.openlocfilehash: 2c8c97d8f5477e7640df87030ed6ef27c4c7b979
ms.sourcegitcommit: eb9dd01614b8e95ebc06139c72fa563b25dc6d13
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/12/2018
ms.locfileid: "53310075"
---
# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Publikovat a spravovat nabídky na webu Azure Marketplace

> [!NOTE]
> Tato dokumentace už není aktuální a není přesné. Místo toho přejděte na Azure Marketplace [Příručka pro prodejce](https://docs.microsoft.com/azure/marketplace/seller-guide/cloud-partner-portal-seller-guide) pokyny k publikování nabídky na webu Azure Marketplace.

V tomto článku je k dispozici, což vývojářům umožňuje vytvářet, nasazovat a spravovat svá řešení uvedené na webu Azure Marketplace pro ostatní zákazníky a partnery Azure nákup a používání.

## <a name="marketplace-publishing"></a>Publikování na webu Marketplace
Jako Azure vydavatele, distribuce a prodávat svoje inovativní řešení nebo služby s ostatními vývojáři, nezávislým výrobcům softwaru a odborníci na IT na webu Marketplace. Prostřednictvím webu Marketplace umožňuje oslovit zákazníky, kteří chtějí rychle vyvíjet svých cloudových aplikací a mobilních řešení. Pokud vaše řešení cílí podnikoví uživatelé, může být vhodné vzít v úvahu [AppSource](https://appsource.microsoft.com) marketplace.


## <a name="supported-types-of-solutions"></a>Podporované typy řešení
První věc, kterou chcete provést jako vydavatel je definování jaký druh řešení je nabídka vaší společnosti. Na webu Marketplace podporuje následující typy nabídek:

|Typ řešení|Virtuální počítač|Šablona řešení|
|---|---|---|
|**Definice**|Předkonfigurované Image s plně instalovaným operačním systémem a aspoň jednou aplikací. Image virtuálního počítače obsahuje informace potřebné k vytvoření a nasazení virtuálních počítačů ve službě Azure Virtual Machines.|Datová struktura, která může odkazovat na jeden nebo více různých služeb pro Azure, včetně služeb publikovaných jiných prodejci. Předplatitelé Azure ho můžete použít k nasazení jednoho nebo víc nabídek jedním, koordinovaným způsobem.|
|**Příklad**|Jako na vydavatele Azure jste vytvořili a ověřit virtuálního počítače pomocí inovativní databázové služby. Další předplatitelům Azure má s nákupem a nasadit tento virtuální počítač do své služby prostředí cloud.|Jako Azure vydavatele jste dodávat sady služeb od napříč Azure, které usnadňují rychlé nasazení cloud services pomocí služby Vyrovnávání zatížení, vyšší míru zabezpečení a vysokou dostupnost. Další předplatitelům Azure ušetřit čas tím ZAŘIZOVÁNÍM šablony řešení, která splňuje cíl. Nemají ručně vyhledejte, pořídit, nasadit a nakonfigurovat stejné nebo podobné služby Azure.|

> [!NOTE]
> Některé kroky jsou sdíleny mezi různými typy řešení a jiné se liší pro příslušný typ řešení. Tento článek poskytuje stručný přehled kroků, které potřebujete k dokončení pro jakýkoli typ řešení.

## <a name="publish-a-solution"></a>Publikování řešení
![Nominujte, registrovat, publikování](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>Nominujte vaše řešení pro předběžná schválení
Publikování virtuálního počítače [řešení](https://createopportunity.azurewebsites.net) na webu Marketplace, dokončete Microsoft Azure Certified **řešení Nominační formulář**.

>[!NOTE]
> Při práci s partnerem Account manažera nebo manažera pro partnerský DX, požádejte ho, aby nominujte vaše řešení v programu Azure Certified. Můžete také přejít na [Microsoft Azure Certified](http://createopportunity.azurewebsites.net) webové stránky a vyplňte formulář přihlášky. Zadejte e-mailu partnera Account manažera nebo manažera pro partnerský DX v **Contact Microsoft sponzor** pole.

Pokud splňují kritéria v [zásady účasti na webu Azure Marketplace](https://go.microsoft.com/fwlink/?LinkID=526833) a po schválení žádosti, můžeme začít pracovat s vámi na připojení vašeho řešení na webu Marketplace.

### <a name="register-your-account-as-a-microsoft-seller"></a>Zaregistrujte svůj účet jako prodejce Microsoftu
Zaregistrujte svůj účet Microsoft jako [účtu Microsoft Developer](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>Publikování řešení
Publikování řešení na webu Marketplace, postupujte podle těchto kroků:
1. Splnění požadavků běžné uživatele.

    a. Splnění [běžné uživatele požadavky](marketplace-publishing-pre-requisites.md).

    b. Splnění [technické požadavky virtuálních počítačů](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Splnění [technické požadavky šablony řešení](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Vytvoření vaší nabídky.

    a. Vytvoření [virtuálního počítače](marketplace-publishing-vm-image-creation.md) nabízejí.

    b. Vytvoření [šablonu řešení](marketplace-publishing-solution-template-creation.md) nabízejí.

3. Vytvoření nabídky [marketingové obsah](marketplace-publishing-push-to-staging.md).

4. Otestujte vaši nabídku v testovacím prostředí.

    a. Testování vaší nabídky virtuálních počítačů v [pracovní](marketplace-publishing-vm-image-test-in-staging.md).

    b. Test šablony nabídky řešení v [pracovní](marketplace-publishing-solution-template-test-in-staging.md).

5. Nasazení vaší nabídce [Marketplace](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Vytvářet a spravovat image virtuálního počítače
Vytvořit a spravovat image virtuálního počítače pomocí těchto prostředků:
* Vytvoření image virtuálního počítače [místní](marketplace-publishing-vm-image-creation-on-premise.md).
* Vytvoření virtuálního počítače se systémem [Windows na webu Azure Portal](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Vytvoření virtuálního počítače se systémem [Linuxu na webu Azure Portal](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Řešení běžných potíží během [vytváření virtuálního pevného disku](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>Správa vašeho řešení
Spravovat své řešení díky pomoci z následujících zdrojů:
* [Přečtěte si příručku postprodukční nabídky virtuálního počítače](marketplace-publishing-vm-image-post-publishing.md)
* [Aktualizovat běžné uživatele podrobnosti nabídky nebo SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [Aktualizovat technické podrobnosti nabídky nebo SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Přidat novou skladovou Položku v seznamu nabídky](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [Změňte počet datových disků pro uvedené SKU](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [Odstranit uvedené nabídku z webu Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Odstranit uvedené skladovou Položku z Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [Odstranit aktuální verze uvedené skladovou Položku z Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Vrátí seznam cenu s produkčními hodnotami](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [Vrátit modelu účtování až produkčními hodnotami](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Vrátí nastavení viditelnosti uvedené skladové položky na hodnotu produkčního prostředí](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)

## <a name="additional-resources"></a>Další zdroje informací:
[Nastavení prostředí Azure PowerShell](marketplace-publishing-powershell-setup.md)
