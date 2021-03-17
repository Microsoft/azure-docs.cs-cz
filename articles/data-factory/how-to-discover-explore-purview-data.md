---
title: Zjišťování a zkoumání dat v ADF pomocí dosah
description: Naučte se zjišťovat, zkoumat data v Azure Data Factory pomocí dosah.
ms.service: data-factory
ms.topic: conceptual
author: lrtoyou1223
ms.author: lle
ms.custom: seo-lt-2019
ms.date: 01/15/2021
ms.openlocfilehash: 031e7ceee739ffd666ad367bd6778865a297a25d
ms.sourcegitcommit: d4734bc680ea221ea80fdea67859d6d32241aefc
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 02/14/2021
ms.locfileid: "100386267"
---
# <a name="discover-and-explore-data-in-adf-using-purview"></a>Zjišťování a zkoumání dat v ADF pomocí dosah

[!INCLUDE[appliesto-adf-xxx-md](includes/appliesto-adf-xxx-md.md)]

V tomto článku zaregistrujete účet Azure dosah do Data Factory. Toto připojení umožňuje zjišťovat prostředky Azure dosah a pracovat s nimi prostřednictvím funkcí ADF. 

V ADF můžete provádět následující úlohy: 
- Pomocí vyhledávacího pole v horní části můžete najít dosah assety na základě klíčových slov. 
- Pochopení dat na základě metadat, řádků a poznámek 
- Propojení těchto dat s datovou továrnou pomocí propojených služeb nebo datových sad 

## <a name="prerequisites"></a>Požadavky 
- [Účet Azure dosah](../purview/create-catalog-portal.md) 
- [Data Factory](./quickstart-create-data-factory-portal.md) 
- [Připojení účtu Azure dosah k Data Factory](./connect-data-factory-to-azure-purview.md) 

## <a name="using-azure-purview-in-data-factory"></a>Používání Azure dosah v Data Factory 

Použití Azure dosah v Data Factory vyžaduje, abyste měli přístup k tomuto účtu dosah. Data Factory projde vaše oprávnění dosah. Pokud máte například roli oprávnění kurátor, budete moci upravovat metadata prověřená službou Azure dosah. 

### <a name="data-discovery-search-datasets"></a>Zjišťování dat: hledání datových sad 

K vyhledání dat registrovaných a zkontrolovaných službou Azure dosah můžete použít panel hledání v horním centru portálu Data Factory. Ujistěte se, že jste vybrali možnost Azure dosah, kde můžete vyhledat všechna data vaší organizace. 

:::image type="content" source="./media/data-factory-purview/search-dataset.png" alt-text="Snímek obrazovky pro provádění datových sad":::

### <a name="actions-that-you-can-perform-over-datasets-with-data-factory-resources"></a>Akce, které můžete u datových sad provádět pomocí Data Factorych prostředků 
Pomocí dat, která vyhledáváte pomocí Azure dosah, můžete přímo vytvořit propojenou službu, datovou sadu nebo tok dat.

:::image type="content" source="./media/data-factory-purview/actions-over-purview-data.png" alt-text="Snímek obrazovky, který ukazuje, jak můžete přímo vytvořit propojenou službu, datovou sadu nebo tok dat pomocí dat vyhledaných službou Azure dosah.":::

##  <a name="nextsteps"></a>Další kroky 

- [Registrace a kontrola Azure Data Factorych prostředků v Azure dosah](../purview/register-scan-azure-synapse-analytics.md)
- [Jak vyhledávat data v Azure dosah Data Catalog](../purview/how-to-search-catalog.md)