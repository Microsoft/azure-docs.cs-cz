---
title: zahrnout soubor
description: zahrnout soubor
services: storage
author: tamram
ms.service: storage
ms.topic: include
ms.date: 08/20/2018
ms.author: tamram
ms.custom: include file
ms.openlocfilehash: f60c23e34962396d4ea6e030912d1ca3f3e4571b
ms.sourcegitcommit: 8ebcecb837bbfb989728e4667d74e42f7a3a9352
ms.translationtype: HT
ms.contentlocale: cs-CZ
ms.lasthandoff: 08/21/2018
ms.locfileid: "40260904"
---
Jsou dva druhy účtů úložiště:

### <a name="general-purpose-storage-accounts"></a>Účty služby Storage pro obecné účely
Účet úložiště pro obecné účely poskytuje přístup ke službám Azure Storage, například k tabulkám, frontám, souborům, objektům blob a diskům virtuálních počítačů Azure v rámci jednoho účtu. Tento typ účtu úložiště má dvě úrovně výkonu:

* Úroveň výkonu standardního úložiště, které umožňuje ukládání tabulek, front, souborů, objektů blob a disků virtuálních počítačů Azure.
* Úroveň výkonu prémiového úložiště, která aktuálně podporuje jenom disky virtuálních počítačů Azure. Podrobný popis služby Premium Storage najdete v článku [Úložiště Premium: vysoce výkonné úložiště pro úlohy virtuálních počítačů Azure](../articles/virtual-machines/windows/premium-storage.md).

### <a name="blob-storage-accounts"></a>Účty služby Blob Storage
Účet služby Blob Storage je specializovaný účet úložiště pro ukládání nestrukturovaných dat v podobě objektů blob do služby Azure Storage. Účty služby Blob Storage jsou podobné účtům služby Storage pro obecné účely a mají stejně vysokou odolnost, dostupnost, škálovatelnost a výkonnost, a navíc mají 100% konzistentnost rozhraní API pro objekty blob bloků a doplňovací objekty blob. V případě aplikací, které vyžadují jenom úložiště objektů blob bloku nebo objektů blob doporučujeme používat účty úložiště objektů blob.

> [!NOTE]
> Účty úložiště Blob podporují pouze objekty blob bloku a doplňovací objekty blob, nepodporují objekty blob stránky.
> 
> 

Účty služby Blob Storage exponují atribut **Úroveň přístupu**, který můžete zadat při vytváření účtů a později ho můžete podle potřeby upravit. Existují dva typy úrovně přístupu, které můžete zadat na základě vzoru pro přístup k datům:

* **Horká** úroveň přístupu, která znamená, že k objektům v účtu úložiště budete přistupovat častěji. Tato možnost nabízí ukládání dat s nižšími přístupovými náklady.
* **Studená** úroveň přístupu, která znamená, že k objektům v účtu úložiště nebudete přistupovat tak často. Tato možnost nabízí ukládání dat s nižšími náklady na úložiště dat.

Mezi úrovněmi přístupu můžete kdykoli přepnout, třeba pokud pro vaše data existuje vzor používání. Se změnou úrovně přístupu můžou být spojené další poplatky. Další podrobnosti najdete v článku [Účty služby Blob Storage – ceny a fakturace](../articles/storage/common/storage-account-options.md#pricing-and-billing).

Další podrobnosti najdete v článku [Azure Blob Storage: úrovně Cool a Hot](../articles/storage/blobs/storage-blob-storage-tiers.md).

Před vytvořením účtu úložiště si musíte pořídit předplatné Azure. Jedná se o tarif, který vám umožní přístup k různým službám Azure. Azure můžete začít používat s [bezplatným účtem](https://azure.microsoft.com/pricing/free-trial/). Jakmile se rozhodnete koupit plán předplatného, můžete si vybrat z různých [možností nákupu](https://azure.microsoft.com/pricing/purchase-options/). Pokud jste [předplatitelem MSDN](https://azure.microsoft.com/pricing/member-offers/msdn-benefits-details/), získáte bezplatné měsíční kredity, které můžete využít na služby Azure, včetně služby Azure Storage. Informace o cenách najdete v článku [Ceny služby Azure Storage](https://azure.microsoft.com/pricing/details/storage/).

Informace o vytvoření účtu úložiště najdete v článku [Vytvoření účtu úložiště](../articles/storage/common/storage-quickstart-create-account.md). V rámci jednoho předplatného můžete vytvořit až 200 účtů úložiště s jedinečným názvem. Podrobné informace o omezeních účtu úložiště najdete v článku [Škálovatelnost a cíle výkonnosti služby Azure Storage](../articles/storage/common/storage-scalability-targets.md).

