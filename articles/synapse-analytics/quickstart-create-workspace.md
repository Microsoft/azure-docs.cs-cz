---
title: 'Rychlý Start: vytvoření pracovního prostoru synapse'
description: Pomocí kroků v této příručce vytvořte pracovní prostor synapse.
services: synapse-analytics
author: saveenr
ms.service: synapse-analytics
ms.topic: quickstart
ms.subservice: workspace
ms.date: 09/03/2020
ms.author: saveenr
ms.reviewer: jrasnick
ms.openlocfilehash: b25bae460ff11c3dab84e80524acd2eaf878561c
ms.sourcegitcommit: d22a86a1329be8fd1913ce4d1bfbd2a125b2bcae
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/26/2020
ms.locfileid: "96184684"
---
# <a name="quickstart-create-a-synapse-workspace"></a>Rychlý Start: vytvoření pracovního prostoru synapse
Tento rychlý Start popisuje kroky pro vytvoření pracovního prostoru Azure synapse pomocí Azure Portal.

## <a name="create-a-synapse-workspace"></a>Vytvoření pracovního prostoru Synapse

1. Otevřete [Azure Portal](https://portal.azure.com)a v horní části vyhledejte **synapse**.
1. Ve výsledcích hledání v části **služby** vyberte **Azure synapse Analytics (pracovní prostory verze Preview)**.
1. Vyberte **Přidat** a vytvořte pracovní prostor.
1. Na kartě **základy** dejte pracovnímu prostoru jedinečný název. V tomto dokumentu použijeme **mysworkspace** .
1. K vytvoření pracovního prostoru potřebujete účet ADLSGEN2. Nejjednodušší volbou je vytvoření nového. Pokud chcete znovu použít stávající, musíte provést nějakou další konfiguraci. 
1. MOŽNOST 1 Vytvoření nového účtu ADLSGEN2 
    1. V části **vybrat data Lake Storage Gen 2** klikněte na **vytvořit nový** a pojmenujte ho **contosolake**.
    1. V části **vybrat data Lake Storage obecné 2** klikněte na **systém souborů** a pojmenujte ho **Uživatelé**.
1. MOŽNOST 2 viz **Příprava pokynů účtu úložiště** v dolní části tohoto dokumentu.
1. Váš pracovní prostor Azure synapse použije tento účet úložiště jako primární účet úložiště a kontejner pro uložení dat pracovního prostoru. Pracovní prostor ukládá data v Apache Sparkch tabulkách. Ukládá protokoly aplikací Spark do složky s názvem **/synapse/workspacename**.
1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš pracovní prostor je připravený během několika minut.

> [!NOTE]
> Po vytvoření pracovního prostoru Azure synapse nebudete moct pracovní prostor přesunout do jiného tenanta Azure Active Directory. Pokud to uděláte prostřednictvím migrace předplatného nebo jinými akcemi, můžete ztratit přístup k artefaktům v pracovním prostoru.  

## <a name="open-synapse-studio"></a>Otevřít synapse Studio

Po vytvoření pracovního prostoru Azure synapse máte dva způsoby, jak otevřít synapse Studio:

* Otevřete pracovní prostor synapse ve [Azure Portal](https://portal.azure.com). V horní části **přehledu** vyberte **Spustit synapse Studio**.
* Přejít na adresu `https://web.azuresynapse.net` a přihlaste se do svého pracovního prostoru.

## <a name="prepare-an-existing-storage-account-for-use-with-synapse-analytics"></a>Příprava existujícího účtu úložiště pro použití s synapse Analytics

1. Otevřete web [Azure Portal](https://portal.azure.com).
1. Přejít na existující účet úložiště ADLSGEN2
1. V levém podokně vyberte **řízení přístupu (IAM)** . Pak přiřaďte následující role nebo ověřte, zda jsou již přiřazeny:
    * Přiřaďte roli **vlastníka** sami sobě.
    * Přiřaďte se k roli **vlastníka dat objektu BLOB úložiště** .
1. V levém podokně vyberte **kontejnery** a vytvořte kontejner.
1. Kontejneru můžete dát libovolný název. V tomto dokumentu budeme pojmenovat **uživatele** kontejneru.
1. Přijměte výchozí nastavení **úroveň veřejného přístupu** a pak vyberte **vytvořit**.

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>Konfigurace přístupu k účtu úložiště z vašeho pracovního prostoru

Spravované identity pro váš pracovní prostor Azure synapse už můžou mít přístup k účtu úložiště. Pomocí těchto kroků se ujistěte, že:

1. Otevřete [Azure Portal](https://portal.azure.com) a primární účet úložiště, který jste zvolili pro váš pracovní prostor.
1. V levém podokně vyberte **řízení přístupu (IAM)** .
1. Přiřaďte následující role nebo se ujistěte, že jsou již přiřazeny. Pro identitu pracovního prostoru používáme stejný název a název pracovního prostoru.
    * Pro roli **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště přiřaďte **MyWorkspace** jako identitu pracovního prostoru.
    * Přiřaďte **MyWorkspace** jako název pracovního prostoru.

1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

* [Vytvoření vyhrazeného fondu SQL](quickstart-create-sql-pool-studio.md) 
* [Vytvoření fondu Apache Spark bez serveru](quickstart-create-apache-spark-pool-portal.md)
* [Použití bezserverového fondu SQL](quickstart-sql-on-demand.md)
