---
title: Kopírování nebo klonování objekt pro vytváření dat ve službě Azure Data Factory | Dokumentace Microsoftu
description: Zjistěte, jak kopírování nebo klonování objekt pro vytváření dat ve službě Azure Data Factory
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/09/2019
ms.author: douglasl
ms.openlocfilehash: c62581447cd395bd48a787fa7dc89659d5172486
ms.sourcegitcommit: 63b996e9dc7cade181e83e13046a5006b275638d
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 01/10/2019
ms.locfileid: "54192175"
---
# <a name="copy-or-clone-a-data-factory-in-azure-data-factory"></a>Kopírování nebo klonování objekt pro vytváření dat ve službě Azure Data Factory

Tento článek popisuje postup kopírování nebo klonování objekt pro vytváření dat ve službě Azure Data Factory.

## <a name="use-cases-for-cloning-a-data-factory"></a>Případy použití pro klonování služby data factory

Tady jsou některé okolnosti, ve kterých možná bude užitečné kopírování nebo klonování datovou továrnu:

-   **Přejmenování zdroje**. Azure nepodporuje přejmenování zdroje. Pokud chcete přejmenovat objekt pro vytváření dat, můžete klonovat objektu pro vytváření dat s jiným názvem a pak odstraňte existující.

-   **Ladění změn** při funkcí ladění nejsou dostatečná. A otestujte provedené změny, můžete někdy otestujte provedené změny před jejich použitím hlavního v různých tovární nastavení. Ve většině případů můžete použít ladění. Změny v aktivačních událostech, ale jako chování provedené změny při aktivační události je vyvolán automaticky, nebo za časové období nemusí být testovatelného snadno bez vrácení se změnami. V těchto případech je velmi výhodné klonování objekt pro vytváření a použití změn existuje. Protože především podle počtu spuštění poplatky za Azure Data Factory, nenastane druhý objekt pro vytváření žádné další poplatky.

## <a name="how-to-clone-a-data-factory"></a>Jak klonovat datové továrny

1. Uživatelské rozhraní Data Factory na webu Azure Portal umožňuje exportovat celé datové části do šablony Resource Manageru, spolu se souborem parametru, který umožňuje změnit všechny hodnoty, které chcete změnit při klonování se svým objektem pro vytváření datové továrny.

1. Předpokladem je budete muset vytvořit datovou továrnu cíl z portálu Azure portal.

1. Pokud jste v režimu GIT při každém publikování z portálu, factory pro šablony Resource Manageru se uloží do GITU ve větvi adf_publish úložiště.

1. Pro další scénáře šablony Resource Manageru můžete stáhnout kliknutím na **Export šablony Resource Manageru** tlačítko na portálu.

1. Po stažení šablony Resource Manageru můžete nasadit ho přes standardní metody nasazení šablony Resource Manageru.

1. Z bezpečnostních důvodů že vygenerovaná šablona Resource Manageru neobsahuje všechny tajné informace, jako jsou hesla pro propojené služby. V důsledku toho je nutné zadat tato hesla jako parametrů nasazení. Poskytuje parametry není žádoucí, budete muset získat připojovací řetězce a hesla z propojených služeb z Azure Key Vault.

## <a name="next-steps"></a>Další postup

Přečtěte si pokyny k vytvoření služby data factory na webu Azure Portal v [vytvoření datové továrny pomocí uživatelského rozhraní Azure Data Factory](quickstart-create-data-factory-portal.md).
