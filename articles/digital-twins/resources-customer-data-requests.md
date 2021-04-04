---
title: Funkce pro žádosti o zákaznických datech pro digitální vlákna Azure
titleSuffix: Azure Digital Twins
description: V tomto článku se dozvíte o procesech exportu a odstraňování osobních údajů v Azure Digital autovlákna.
author: baanders
ms.author: baanders
ms.date: 05/05/2020
ms.topic: conceptual
ms.service: digital-twins
services: digital-twins
ms.openlocfilehash: 9942056e59a46c61b4e9da32aab191d4b72db968
ms.sourcegitcommit: f28ebb95ae9aaaff3f87d8388a09b41e0b3445b5
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/30/2021
ms.locfileid: "92461755"
---
# <a name="azure-digital-twins-customer-data-request-features"></a>Funkce pro žádosti o data zákaznických dat v Azure

Digitální vlákna Azure je vývojářská platforma pro vytváření zabezpečených digitálních reprezentace podnikového prostředí. Reprezentace jsou řízena živými daty o stavu ze zdrojů dat vybraných uživateli.

[!INCLUDE [gdpr-intro-sentence](../../includes/gdpr-intro-sentence.md)]

Digitální reprezentace označované jako *digitální vlákna* v rámci digitálních vláken Azure představují entity ve skutečných prostředích a jsou přidruženy k identifikátorům. Společnost Microsoft nespravuje žádné informace a nemá přístup k datům, která by umožňovala korelaciovat identifikátory uživatelů. 

Mnohé z digitálních vláken v Azure Digital autofactory nepředstavuje přímo osobní entity – typické objekty mohou být v místnosti pro schůzku nebo v továrně. Uživatelé ale můžou zvážit, že některé entity budou identifikovatelné osobně, a na základě jejich uvážení mohou uchovávat vlastní metody sledování prostředků nebo inventáře, které propojují digitální vlákna jednotlivcům. Digitální vlákna Azure spravuje a ukládá všechna data přidružená k digitálním hodnotám, jako by šlo o osobní údaje.

Pokud chcete zobrazit, exportovat a odstranit osobní údaje, které mohou být odkazovány v žádosti subjektu údajů, může správce digitálních vláken Azure použít [**Azure Portal**](https://portal.azure.com/) pro uživatele a role nebo [**rozhraní REST API**](/rest/api/azure-digitaltwins/) pro digitální vlákna Azure. Rozhraní API pro Azure Portal a REST poskytují různé metody pro poskytování požadavků subjektu dat pro uživatele.

## <a name="identifying-customer-data"></a>Identifikace zákaznických dat

Digitální vlákna Azure považují *osobní údaje* za data přidružená ke svým správcům a uživatelům. 

Digitální vlákna Azure ukládá [Azure Active Directory](../active-directory/fundamentals/active-directory-whatis.md) *ID objektu* uživatelů s přístupem k prostředí. Digitální vlákna Azure v Azure Portal zobrazuje e-mailové adresy uživatelů, ale tyto e-mailové adresy se neukládají v rámci digitálních vláken Azure. Dynamicky se vycházejí v Azure Active Directory pomocí ID objektu Azure Active Directory.

## <a name="deleting-customer-data"></a>Odstraňují se zákaznická data

Správci digitálních vláken Azure můžou použít Azure Portal k odstraňování dat souvisejících s uživateli. Je také možné provádět operace odstranění u jednotlivých digitálních vláken pomocí rozhraní REST API služby Azure Digital revláken. Další informace o dostupných rozhraních API najdete v [dokumentaci k rozhraní REST API pro digitální vlákna Azure](/rest/api/azure-digitaltwins/).

## <a name="exporting-customer-data"></a>Export zákaznických dat

Digitální vlákna Azure ukládá data týkající se digitálních vláken. Uživatelé můžou tato data načítat a zobrazovat prostřednictvím rozhraní REST API a exportovat tato data pomocí kopírování a vložení. 

Zákaznická data, včetně rolí uživatelů a přiřazení rolí, můžou být vybraná, zkopírovaná a vložená z Azure Portal. 

## <a name="links-to-additional-documentation"></a>Odkazy na další dokumentaci

Úplný seznam rozhraní API služeb digitálních vláken Azure najdete v [dokumentaci rozhraní REST API pro digitální vlákna Azure](/rest/api/azure-digitaltwins/).