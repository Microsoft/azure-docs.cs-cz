---
title: Zabezpečení pracovního prostoru synapse (Preview)
description: Tento článek vás seznámí s tím, jak pomocí rolí a řízení přístupu řídit aktivity a přístup k datům v pracovním prostoru synapse.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: security
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: 080e56a5b6be8ba68c901509fe87421632144643
ms.sourcegitcommit: 96918333d87f4029d4d6af7ac44635c833abb3da
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 11/04/2020
ms.locfileid: "93312045"
---
# <a name="secure-your-synapse-workspace-preview"></a>Zabezpečení pracovního prostoru synapse (Preview) 

V tomto článku se naučíte, jak používat role a řízení přístupu k řízení aktivit a přístupu k datům. Pomocí těchto pokynů je zjednodušené řízení přístupu v Azure synapse Analytics. Stačí přidat a odebrat uživatele pouze do jedné ze tří skupin zabezpečení.

## <a name="overview"></a>Přehled

Pokud chcete zabezpečit pracovní prostor synapse (Preview), postupujte podle vzoru konfigurace následujících položek:

- Role Azure (například předdefinovaná jako vlastník, přispěvatel atd.)
- Role synapse – tyto role jsou jedinečné pro synapse a nejsou založené na rolích Azure. Existují tři z těchto rolí:
  - Správce pracovního prostoru synapse
  - Správce synapse SQL
  - Apache Spark pro správce Azure synapse Analytics
- Řízení přístupu pro data v Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Řízení přístupu pro databáze SQL synapse a Spark
- 
## <a name="steps-to-secure-a-synapse-workspace"></a>Postup zabezpečení pracovního prostoru synapse

Tento dokument používá ke zjednodušení pokynů standardní názvy. Nahraďte je libovolnými názvy.

|Nastavení | Příklad hodnoty | Popis |
| :------ | :-------------- | :---------- |
| **Pracovní prostor synapse** | WS1 |  Název, který bude mít pracovní prostor synapse. |
| **Účet ADLSGEN2** | STG1 | Účet ADLS, který se má používat s vaším pracovním prostorem. |
| **Kontejner** | CNT1 | Kontejner v STG1, který bude ve výchozím nastavení používat pracovní prostor |
| **Tenant služby Active Directory** | contoso | název tenanta služby Active Directory.|
||||

## <a name="step-1-set-up-security-groups"></a>Krok 1: nastavení skupin zabezpečení

Vytvořte a naplňte tři skupiny zabezpečení pro váš pracovní prostor:

- **WS1 \_ WSAdmins** – pro uživatele, kteří potřebují plnou kontrolu nad pracovním prostorem
- **WS1 \_ SparkAdmins** – pro uživatele, kteří potřebují úplnou kontrolu nad aspekty Sparku pracovního prostoru
- **WS1 \_ SQLAdmins** – pro uživatele, kteří potřebují úplnou kontrolu nad aspekty SQL pracovního prostoru

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>Krok 2: Příprava účtu Data Lake Storage Gen2

Identifikujte tyto informace o úložišti:

- Účet ADLSGEN2, který se má použít pro váš pracovní prostor. Tento dokument volá IT STG1.  STG1 se považuje za "primární" účet úložiště pro váš pracovní prostor.
- Kontejner v rámci WS1, který bude ve výchozím nastavení používat váš pracovní prostor synapse. Tento dokument volá IT CNT1.  Tento kontejner se používá pro:
  - Ukládání souborů zálohovaných dat pro tabulky Spark
  - Protokoly spuštění pro úlohy Spark

- Pomocí Azure Portal přiřaďte skupiny zabezpečení následující role na CNT1

  - Přiřazení **WS1 \_ WSAdmins** k roli **Přispěvatel dat objektů BLOB úložiště**
  - Přiřazení **WS1 \_ SparkAdmins** k roli **Přispěvatel dat objektů BLOB úložiště**
  - Přiřazení **WS1 \_ SQLAdmins** k roli **Přispěvatel dat objektů BLOB úložiště**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>Krok 3: vytvoření a konfigurace pracovního prostoru synapse

 V Azure Portal vytvořte pracovní prostor synapse:

- Vyberte své předplatné.
- Vyberte skupinu prostředků. musíte mít přístup ke skupině prostředků, ke které máte přiřazenou roli **vlastníka** .
- Pojmenujte pracovní prostor WS1
- Pro účet úložiště vyberte STG1. Pro kontejner, který se používá jako systém souborů, vyberte CNT1.
- Otevřít WS1 v synapse studiu
- Vyberte **Spravovat**  >  **Access Control** přiřaďte skupiny zabezpečení následujícím rolím synapse.
  - Přiřazení **WS1 \_ WSAdmins** správcům pracovních prostorů synapse
  - Přiřazení **WS1 \_ SparkAdmins** správcům synapse Spark
  - Přiřazení **WS1 \_ SQLAdmins** SPRÁVCům synapse SQL

## <a name="step-4-configure-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>Krok 4: Konfigurace Data Lake Storage Gen2 pro použití v pracovním prostoru synapse

Pracovní prostor synapse potřebuje přístup k STG1 a CNT1, aby mohl spouštět kanály a provádět systémové úlohy.

- Otevřete Azure Portal.
- Najít STG1
- Přejít na CNT1
- Zajistěte, aby se služba MSI (Identita spravované služby) pro WS1 přiřadila k roli **Přispěvatel dat objektů BLOB úložiště** v CNT1.
  - Pokud se vám přiřazení nezobrazuje, přiřaďte ho.
  - Soubor MSI má stejný název jako pracovní prostor. V takovém případě by se &quot; WS1 &quot; .

## <a name="step-5-configure-admin-access-for-synapse-sql"></a>Krok 5: Konfigurace přístupu správce pro synapse SQL

- Otevřete Azure Portal.
- Přejít na WS1
- V části **Nastavení** vyberte **Správce služby SQL Active Directory** .
- Vyberte **nastavit správce** a zvolte WS1 \_ SQLAdmins

## <a name="step-6-maintain-access-control"></a>Krok 6: udržování řízení přístupu

Konfigurace je dokončená.

Chcete-li nyní spravovat přístup pro uživatele, můžete přidat nebo odebrat uživatele do tří skupin zabezpečení.

I když můžete uživatele přiřadit k rolím synapse, pokud tak učiníte, nekonfigurují se konzistentně. Místo toho přidejte nebo odeberte uživatele do skupin zabezpečení.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>Krok 7: ověření přístupu pro uživatele v rolích

Uživatelé v každé roli musí provést následující kroky:

| Číslo | Krok | Správci pracovního prostoru | Správci Spark | Správci SQL |
| --- | --- | --- | --- | --- |
| 1 | Nahrání souboru Parquet do CNT1 | ANO | ANO | ANO |
| 2 | Přečtěte si soubor Parquet s využitím fondu SQL bez serveru. | ANO | NO | ANO |
| 3 | Vytvoření fondu Apache Spark bez serveru | ANO [1] | ANO [1] | NO  |
| 4 | Přečte soubor Parquet pomocí poznámkového bloku. | ANO | ANO | NO |
| 5 | Vytvoření kanálu z poznámkového bloku a aktivace kanálu, který se má spustit hned | ANO | NO | NO |
| 6 | Vytvořte vyhrazený fond SQL a spusťte skript SQL, jako je například &quot; Vyberte 1.&quot; | ANO [1] | NO | ANO [1] |

> [!NOTE]
> [1] Chcete-li vytvořit fondy SQL nebo Spark, musí mít uživatel alespoň roli Přispěvatel v pracovním prostoru synapse.
>
 
>[!TIP]
> - Některé kroky se v závislosti na této roli nepovolují záměrně.
> - Mějte na paměti, že některé úlohy můžou selhat, pokud zabezpečení nebylo úplně nakonfigurované. Tyto úlohy jsou uvedeny v tabulce.

## <a name="step-8-network-security"></a>Krok 8: zabezpečení sítě

Konfigurace brány firewall pracovního prostoru, virtuální sítě a [privátního odkazu](../../azure-sql/database/private-endpoint-overview.md).

## <a name="step-9-completion"></a>Krok 9: dokončení

Váš pracovní prostor je teď plně nakonfigurovaný a zabezpečený.

## <a name="how-roles-interact-with-synapse-studio"></a>Jak role komunikují s synapse Studio

Synapse Studio se bude chovat odlišně na základě rolí uživatelů. Některé položky mohou být skryté nebo zakázané, pokud uživatel není přiřazen k rolím, které poskytují odpovídající přístup. Následující tabulka shrnuje efekt v synapse studiu.

| Úkol | Správci pracovního prostoru | Správci Spark | Správci SQL |
| --- | --- | --- | --- |
| Otevřít synapse Studio | ANO | ANO | ANO |
| Zobrazit centrum domů | ANO | ANO | ANO |
| Zobrazit centrum dat | ANO | ANO | ANO |
| Data hub/viz propojené ADLS Gen2 účty a kontejnery | ANO [1] | ANO [1] | ANO [1] |
| Data hub/viz databáze | ANO | ANO | ANO |
| Data hub/zobrazit objekty v databázích | ANO | ANO | ANO |
| Data hub/přístup k datům v databázích SQL synapse | ANO   | NO   | ANO   |
| Data hub/přístup k datům v databázích fondu SQL bez serveru | ANO [2]  | NO  | ANO [2]  |
| Data hub/přístup k datům v databázích Spark | ANO [2] | ANO [2] | ANO [2] |
| Použití centra pro vývoj | ANO | ANO | ANO |
| Vývoj a vytváření skriptů SQL na střed | ANO | NO | ANO |
| Vývoj definice úloh v centru/vytváření Sparku | ANO | ANO | NO |
| Vývoj poznámkových bloků centra/vytváření | ANO | ANO | NO |
| Vývoj toku dat centra/autora | ANO | NO | NO |
| Použití centra Orchestration | ANO | ANO | ANO |
| Orchestrace centra/použití kanálů | ANO | NO | NO |
| Použití centra pro správu | ANO | ANO | ANO |
| Správa centra/synapse SQL | ANO | NO | ANO |
| Správa hub a fondů Spark | ANO | ANO | NO |
| Spravovat centrum a triggery | ANO | NO | NO |
| Správa hub/propojených služeb | ANO | ANO | ANO |
| Spravovat centrum/Access Control (přiřadit uživatele k rolím pracovního prostoru synapse) | ANO | NO | NO |
| Správa modulů runtime v centru/Integrcích | ANO | ANO | ANO |
| Použití centra monitorování | ANO | ANO | ANO |
| Monitorování centra/orchestrace/spuštění kanálu  | ANO | NO | NO |
| Monitorování centra/orchestrace/spuštění triggeru  | ANO | NO | NO |
| Monitorování centra/orchestrace/prostředí Integration runtime  | ANO | ANO | ANO |
| Monitorovat centra/aktivity/aplikace Spark | ANO | ANO | NO  |
| Monitorovat centra/aktivity/požadavky SQL | ANO | NO | ANO |
| Monitorování hub/aktivit/fondů Spark | ANO | ANO | NO  |
| Monitorování centra/triggerů | ANO | NO | NO |
| Správa hub/propojených služeb | ANO | ANO | ANO |
| Spravovat centrum/Access Control (přiřadit uživatele k rolím pracovního prostoru synapse) | ANO | NO | NO |
| Správa modulů runtime v centru/Integrcích | ANO | ANO | ANO |


> [!NOTE]
> [1] přístup k datům v kontejnerech závisí na řízení přístupu v ADLS Gen2. </br>
> [2] SQL z tabulek a tabulek Spark ukládá data do ADLS Gen2 a přístup vyžaduje příslušná oprávnění pro ADLS Gen2.

## <a name="next-steps"></a>Další kroky

Vytvoření [pracovního prostoru synapse](../quickstart-create-workspace.md)
