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
ms.openlocfilehash: 9780a2adb60c690abda6880157252aaa0c562f18
ms.sourcegitcommit: 2989396c328c70832dcadc8f435270522c113229
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/19/2020
ms.locfileid: "92171788"
---
# <a name="quickstart-create-a-synapse-workspace"></a>Rychlý Start: vytvoření pracovního prostoru synapse
Tento rychlý Start popisuje kroky pro vytvoření pracovního prostoru Azure synapse pomocí Azure Portal.

## <a name="create-a-synapse-workspace"></a>Vytvoření pracovního prostoru Synapse

1. Otevřete [Azure Portal](https://portal.azure.com)a v horní části vyhledejte **synapse**.
1. Ve výsledcích hledání v části **služby**vyberte **Azure synapse Analytics (pracovní prostory verze Preview)**.
1. Vyberte **Přidat** a vytvořte pracovní prostor pomocí těchto nastavení:

    |Karta|Nastavení | Navrhovaná hodnota | Popis |
    |---|---|---|---|
    |Základy|**Název pracovního prostoru**|Můžete si je pojmenovat.| V tomto dokumentu budeme používat **MyWorkspace**.|
    |Základy|**Oblast**|Porovnává s oblastí účtu úložiště.|

1. K vytvoření pracovního prostoru potřebujete účet ADLSGEN2. Nejjednodušší volbou je vytvoření nového. Pokud chcete znovu použít stávající, musíte provést nějakou další konfiguraci. 
1. MOŽNOST 1 Vytvoření nového účtu ADLSGEN2 
    1. V části **vybrat data Lake Storage Gen 2**klikněte na **vytvořit nový** a pojmenujte ho **contosolake**.
    1. V části **vybrat data Lake Storage obecné 2**klikněte na **systém souborů** a pojmenujte ho **Uživatelé**.
1. MOŽNOST 2 viz **Příprava pokynů účtu úložiště** v dolní části tohoto dokumentu.
1. Váš pracovní prostor Azure synapse použije tento účet úložiště jako primární účet úložiště a kontejner pro uložení dat pracovního prostoru. Pracovní prostor ukládá data v Apache Sparkch tabulkách. Ukládá protokoly aplikací Spark do složky s názvem **/synapse/workspacename**.
1. Vyberte **Zkontrolovat a vytvořit** > **Vytvořit**. Váš pracovní prostor je připravený během několika minut.

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
1. Kontejneru můžete dát libovolný název. V tomto dokumentu budeme pojmenovat **uživatele**kontejneru.
1. Přijměte výchozí nastavení **úroveň veřejného přístupu**a pak vyberte **vytvořit**.

### <a name="configure-access-to-the-storage-account-from-your-workspace"></a>Konfigurace přístupu k účtu úložiště z vašeho pracovního prostoru

Spravované identity pro váš pracovní prostor Azure synapse už můžou mít přístup k účtu úložiště. Pomocí těchto kroků se ujistěte, že:

1. Otevřete [Azure Portal](https://portal.azure.com) a primární účet úložiště, který jste zvolili pro váš pracovní prostor.
1. V levém podokně vyberte **řízení přístupu (IAM)** .
1. Přiřaďte následující role nebo se ujistěte, že jsou již přiřazeny. Pro identitu pracovního prostoru používáme stejný název a název pracovního prostoru.
    * Pro roli **Přispěvatel dat objektů BLOB úložiště** v účtu úložiště přiřaďte **MyWorkspace** jako identitu pracovního prostoru.
    * Přiřaďte **MyWorkspace** jako název pracovního prostoru.

1. Vyberte **Uložit**.

## <a name="next-steps"></a>Další kroky

* [Vytvoření fondu SQL](quickstart-create-sql-pool-studio.md) 
* [Vytvoření fondu Apache Spark](quickstart-create-apache-spark-pool-portal.md)
* [Používání SQL na vyžádání](quickstart-sql-on-demand.md)
