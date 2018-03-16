---
title: "Přehled o tom, jak vytvořit a nasadit nabídku Marketplace | Microsoft Docs"
description: "Seznamte se s kroky potřebné k stát schválené Microsoft developer a vytvořit a nasadit bitovou kopii virtuálního počítače, šablony, data služby nebo služby pro vývojáře v Azure Marketplace"
services: marketplace-publishing
documentationcenter: 
author: msmbaldwin
manager: mbaldwin
editor: 
ms.assetid: 5343bd26-c6e4-4589-85b7-4a2c00bba8ab
ms.service: marketplace
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 01/05/2017
ms.author: mbaldwin
ms.openlocfilehash: 02df78de48c8466f3488ef5f8346e1852b4e3ded
ms.sourcegitcommit: 8aab1aab0135fad24987a311b42a1c25a839e9f3
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/16/2018
---
> [!NOTE]
> Tato dokumentace již není aktuální a není přesný. Místo toho přejděte do Azure Marketplace [prodejce průvodce](https://docs.microsoft.com/azure/marketplace/seller-guide/cloud-partner-portal-seller-guide) pokyny k publikování nabídky pro Azure Marketplace.

# <a name="publish-and-manage-an-offer-in-the-azure-marketplace"></a>Publikovat a spravovat nabídku v Azure Marketplace
Tento článek je určena k vývojářům vytvářet, nasazovat a spravovat svá řešení uvedené v Azure Marketplace pro jiné Azure zákazníci a partneři zakoupit a používat.

## <a name="marketplace-publishing"></a>Publikování webu Marketplace
Jako Azure vydavatele, můžete distribuovat a prodal inovativní řešení nebo služby jinými vývojáři nezávislí výrobci softwaru a IT odborníky na webu Marketplace. Přes Marketplace mohou být využity zákazníkům, kteří chtějí rychle vyvíjet vlastní cloudové aplikace a mobilní řešení. Pokud vaše řešení cílem podnikoví uživatelé, můžete chtít zvážit [AppSource](http://appsource.microsoft.com) marketplace.


## <a name="supported-types-of-solutions"></a>Podporované typy řešení
První věcí, kterou chcete nastavit jako vydavatel je určit, jaký druh řešení nabídka je vaší společnosti. Na webu Marketplace podporuje následující typy nabízí:

|Typ řešení|Virtuální počítač|Šablona řešení|
|---|---|---|
|**Definice**|Předkonfigurované obrázky s plně nainstalovaného operačního systému a jednu nebo více aplikací. Image virtuálního počítače poskytuje informace potřebné k vytvoření a nasazení virtuálních počítačů ve službě Azure Virtual Machines.|Datová struktura, která může odkazovat na jeden nebo více služeb odlišné Azure, včetně služeb publikovat další prodejci. Předplatitelé služby Azure můžete nasadit jeden nebo více nabídek jeden, koordinovaným způsobem.|
|**Příklad**|Jako Azure vydavatele jste vytvořili a ověřit virtuálního počítače pomocí služby inovativní databáze. Další předplatitelé služby Azure chcete obstarat a nasadit tento virtuální počítač do jejich prostředí cloudové služby.|Jako Azure vydavatele jste dodávat sadu služeb z napříč Azure, aby byla rychle nasadit cloudové služby Vyrovnávání zatížení, lepší zabezpečení a vysokou dostupnost. Další předplatitelé služby Azure můžete ušetřit čas tím nákup šablony řešení, která splňuje svůj cíl. Nemají ručně vyhledejte, pořídit, nasadit a nakonfigurovat stejné nebo podobné služeb Azure.|

> [!NOTE]
> Některé kroky jsou sdílené mezi různé typy řešení a jiné jsou jedinečné pro příslušného typu řešení. Tento článek poskytuje stručný přehled kroků, které potřebujete k dokončení pro libovolnému typu řešení.

## <a name="publish-a-solution"></a>Publikování řešení
![Určit, registrace, publikování](media/marketplace-publishing-getting-started/img01.png)

### <a name="nominate-your-solution-for-pre-approval"></a>Jmenovat řešení pro předběžné schválení
K publikování virtuálního počítače [řešení](https://createopportunity.azurewebsites.net) na Marketplace, dokončete Microsoft Azure certifikované **řešení navrženém formuláře**.

>[!NOTE]
> Pokud pracujete s partnera účet manažera nebo partnera správce Directx, požádejte je, určíme řešení pro program certifikované Azure. Můžete také přejít na [Microsoft Azure certifikované](http://createopportunity.azurewebsites.net) webovou stránku a vyplňte formulář žádosti. Zadejte e-mailu partnera Account manažera nebo partnera Manager Directx v **Microsoft sponzor kontaktujte** pole.

Pokud splňují kritéria způsobilosti v [zásady zapojení Azure Marketplace](http://go.microsoft.com/fwlink/?LinkID=526833) a aplikace je schválená, můžeme začít pracovat s vámi zařadit do řešení na webu Marketplace.

### <a name="register-your-account-as-a-microsoft-seller"></a>Registrace účtu jako prodejce Microsoft
Registrace účtu Microsoft jako [účtu Microsoft Developer](marketplace-publishing-accounts-creation-registration.md).

### <a name="publish-your-solution"></a>Publikování řešení
K publikování řešení na webu Marketplace, postupujte takto:
1. Splnění požadavků na běžné uživatele.

    a. Splnění [běžné uživatele požadavky](marketplace-publishing-pre-requisites.md).

    b. Splnění [technické požadavky virtuálního počítače](marketplace-publishing-vm-image-creation-prerequisites.md).

    c. Splnění [technické předpoklady šablony řešení](marketplace-publishing-solution-template-creation-prerequisites.md).

2. Vytvořte nabídku.

    a. Vytvoření [virtuální počítač](marketplace-publishing-vm-image-creation.md) nabízejí.

    b. Vytvoření [šablona řešení](marketplace-publishing-solution-template-creation.md) nabízejí.

3. Vytvořit nabídku [marketingové obsah](marketplace-publishing-push-to-staging.md).

4. Při přípravě otestujte vaši nabídku.

    a. Otestovat vaši nabídku virtuálních počítačů v [pracovní](marketplace-publishing-vm-image-test-in-staging.md).

    b. Otestovat vaši nabídku šablony řešení v [pracovní](marketplace-publishing-solution-template-test-in-staging.md).

5. Nasadit vaši nabídku [Marketplace](marketplace-publishing-push-to-production.md).


### <a name="create-and-manage-a-virtual-machine-image"></a>Vytvářet a spravovat bitové kopie virtuálního počítače
Vytvoření a správa image virtuálního počítače pomocí těchto prostředků:
* Vytvoření image virtuálního počítače [místní](marketplace-publishing-vm-image-creation-on-premise.md).
* Vytvoření virtuálního počítače se systémem [Windows na portálu Azure](../virtual-machines/virtual-machines-windows-hero-tutorial.md?toc=%2fazure%2fvirtual-machines%2fwindows%2ftoc.json).
* Vytvoření virtuálního počítače se systémem [Linux na portálu Azure](../virtual-machines/linux/quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).
* Řešení běžných problémů s došlo během [vytvoření virtuálního pevného disku](marketplace-publishing-vm-image-creation-troubleshooting.md).

## <a name="manage-your-solution"></a>Spravovat vaše řešení
Řešení pomoci spravovat z následujících zdrojů:
* [Čtení po provozní příručky pro virtuální počítač nabídky](marketplace-publishing-vm-image-post-publishing.md)
* [Aktualizovat podrobnosti běžné uživatele nabídku nebo SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-nontechnical-details-of-an-offer-or-a-sku)
* [Aktualizovat technické informace o nabídku nebo SKU](marketplace-publishing-vm-image-post-publishing.md#update-the-technical-details-of-a-sku)
* [Přidat nové skladová položka v seznamu nabídky](marketplace-publishing-vm-image-post-publishing.md#add-a-new-sku-under-a-listed-offer)
* [Změnit počet datových disků pro uvedené SKU](marketplace-publishing-vm-image-post-publishing.md#change-the-data-disk-count-for-a-listed-sku)
* [Odstranit uvedené nabídka z Marketplace](marketplace-publishing-vm-image-post-publishing.md)
* [Odstranit uvedené SKU z Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-a-listed-sku-from-the-marketplace)
* [Odstranit aktuální verzi uvedené SKU z Marketplace](marketplace-publishing-vm-image-post-publishing.md#delete-the-current-version-of-a-listed-sku-from-the-marketplace)
* [Vrátit výpis cena na produkční hodnoty](marketplace-publishing-vm-image-post-publishing.md#revert-the-listing-price-to-production-values)
* [Vrátit model fakturace na produkční hodnoty](marketplace-publishing-vm-image-post-publishing.md#revert-the-billing-model-to-production-values)
* [Obnovit nastavení viditelnosti uvedené SKU pro produkční hodnota](marketplace-publishing-vm-image-post-publishing.md#revert-the-visibility-setting-of-a-listed-sku-to-the-production-value)

## <a name="additional-resources"></a>Další zdroje informací:
[Nastavení prostředí Azure PowerShell](marketplace-publishing-powershell-setup.md)
