---
title: Zabezpečení pracovního prostoru Synapse (náhled)
description: Tento článek vás naučí používat role a řízení přístupu k řízení aktivit a přístupu k datům v pracovním prostoru Synapse.
services: synapse-analytics
author: matt1883
ms.service: synapse-analytics
ms.topic: how-to
ms.subservice: ''
ms.date: 04/15/2020
ms.author: mahi
ms.reviewer: jrasnick
ms.openlocfilehash: ae8be848b5d12e01865fe6bd3b394b460252aa3e
ms.sourcegitcommit: 5e49f45571aeb1232a3e0bd44725cc17c06d1452
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 04/17/2020
ms.locfileid: "81606011"
---
# <a name="secure-your-synapse-workspace-preview"></a>Zabezpečení pracovního prostoru Synapse (náhled)

Tento článek vás naučí používat role a řízení přístupu k řízení aktivit a přístupu k datům. Podle těchto pokynů je zjednodušeno řízení přístupu v Azure Synapse Analytics. Je třeba pouze přidat a odebrat uživatele do jedné ze tří skupin zabezpečení.

## <a name="overview"></a>Přehled

Chcete-li zabezpečit pracovní prostor Synapse (náhled), budete postupovat podle vzoru konfigurace následujících položek:

- Role Azure (například integrované role, jako je Vlastník, Přispěvatel atd.)
- Synapse role – tyto role jsou jedinečné pro Synapse a nejsou založeny na rolích Azure. Existují tři z těchto rolí:
  - Správce pracovního prostoru Synapse
  - Správce synapse SQL
  - Synapse Spark admin
- Řízení přístupu pro data v Azure Data Lake Storage Gen 2 (ADLSGEN2).
- Řízení přístupu pro databáze Synapse SQL a Spark

## <a name="steps-to-secure-a-synapse-workspace"></a>Kroky k zabezpečení pracovního prostoru Synapse

Tento dokument používá standardní názvy ke zjednodušení pokynů. Nahraďte je libovolnými jmény podle vašeho výběru.

|Nastavení | Příklad hodnoty | Popis |
| :------ | :-------------- | :---------- |
| **Synapse pracovní prostor** | WS1 |  Název, který bude mít pracovní prostor Synapse. |
| **Účet ADLSGEN2** | STG1 | Účet ADLS pro použití s pracovním prostorem. |
| **Kontejner** | CNT1 | Kontejner v STG1, který bude pracovní prostor používat ve výchozím nastavení. |
| **Klient služby Active Directory** | contoso | název klienta služby Active Directory.|
||||

## <a name="step-1-set-up-security-groups"></a>KROK 1: Nastavení skupin zabezpečení

Vytvořte a naplňte tři skupiny zabezpečení pro pracovní prostor:

- **WS1\_WSAdmins** – pro uživatele, kteří potřebují úplnou kontrolu nad pracovním prostorem
- **WS1\_SparkAdmins** – pro ty uživatele, kteří potřebují úplnou kontrolu nad aspekty Spark pracovního prostoru
- **WS1\_SQLAdmins** – pro uživatele, kteří potřebují úplnou kontrolu nad aspekty SQL pracovního prostoru
- Přidání **ws1\_wsadminů** do **ws1\_SQLAdmins**
- Přidání **ws1\_wsadminů** do **ws1\_sparku**

## <a name="step-2-prepare-your-data-lake-storage-gen2-account"></a>KROK 2: Příprava účtu Data Lake Storage Gen2

Identifikujte tyto informace o úložišti:

- Účet ADLSGEN2, který se má použít pro váš pracovní prostor. Tento dokument jej nazývá STG1.  STG1 je považován za "primární" účet úložiště pro váš pracovní prostor.
- Kontejner uvnitř WS1, který bude váš pracovní prostor Synapse používat ve výchozím nastavení. Tento dokument jej nazývá CNT1.  Tento kontejner se používá pro:
  - Ukládání záložních datových souborů pro tabulky Spark
  - Protokoly provádění úloh Spark

- Pomocí portálu Azure přiřaďte skupinám zabezpečení následující role na CNT1

  - Přiřazení **ws1\_wsadminů** k roli **přispěvatele dat objektů blob úložiště**
  - Přiřazení **správců\_WS1 SparkAdmins** k roli **přispěvatele dat objektů blob úložiště**
  - Přiřazení **ws1\_SQLAdmins** k roli **přispěvatele dat objektů blob úložiště**

## <a name="step-3-create-and-configure-your-synapse-workspace"></a>KROK 3: Vytvoření a konfigurace pracovního prostoru Synapse

Na portálu Azure vytvořte pracovní prostor Synapse:

- Pojmenování pracovního prostoru WS1
- Zvolte STG1 pro účet úložiště.
- Zvolte CNT1 pro kontejner, který se používá jako "souborový systém".
- Otevřít WS1 v Synapse Studio
- Vyberte **Spravovat** > **řízení přístupu** přiřaďte skupiny zabezpečení k následujícím rolím Synapse.
  - Přiřazení správcům pracovního prostoru **služby WS1 WS1\_**
  - Přiřazení **správců\_ms1 SparkAdmins** společnosti Synapse Spark
  - Přiřazení **ws1\_SQLAdmins** správcům Synapse SQL

## <a name="step-4-configuring-data-lake-storage-gen2-for-use-by-synapse-workspace"></a>KROK 4: Konfigurace úložiště datových jezer Gen2 pro použití v pracovním prostoru Synapse

Pracovní prostor Synapse potřebuje přístup k STG1 a CNT1, aby mohl spouštět kanály a provádět systémové úlohy.

- Otevření portálu Azure
- Vyhledat STG1
- Přejít na CNT1
- Ujistěte se, že MSI (identita spravované služby) pro WS1 je přiřazena k roli **přispěvatele dat objektů blob Azure** na CNT1
  - Pokud ji nevidíte přiřazenou, přiřaďte ji.
  - MSI má stejný název jako pracovní prostor. V tomto případě by &quot;to&quot;bylo WS1 .

## <a name="step-5-configure-admin-access-for-sql-pools"></a>KROK 5: Konfigurace přístupu správce pro fondy SQL

- Otevření portálu Azure
- Přechod na WS1
- V části **Nastavení**klikněte na **správce služby ACTIVE Directory SQL.**
- Klikněte na **Nastavit** správce\_a zvolte WS1 SQLAdmins.

## <a name="step-6-maintaining-access-control"></a>KROK 6: Udržování řízení přístupu

Konfigurace je dokončena.

Chcete-li spravovat přístup uživatelů, můžete nyní přidávat a odebírat uživatele do tří skupin zabezpečení.

I když můžete ručně přiřadit uživatele k rolím Synapse, pokud tak učiníte, nebude nakonfigurovat věci konzistentně. Místo toho přidávejte nebo odeberte uživatele do skupin zabezpečení.

## <a name="step-7-verify-access-for-users-in-the-roles"></a>KROK 7: Ověření přístupu uživatelů v rolích

Uživatelé v každé roli musí provést následující kroky:

|   | Krok | Správci pracovního prostoru | Správci Spark | Správci SQL |
| --- | --- | --- | --- | --- |
| 1 | Nahrání souboru parket do CNT1 | ANO | ANO | ANO |
| 2 | Čtení souboru parket pomocí SQL na vyžádání | ANO | NO | ANO |
| 3 | Vytvoření fondu Spark | ANO [1] | ANO [1] | NO  |
| 4 | Přečte soubor parket pomocí notebooku | ANO | ANO | NO |
| 5 | Vytvoření kanálu z poznámkového bloku a aktivace kanálu, který se má spustit | ANO | NO | NO |
| 6 | Vytvoření fondu SQL a spuštění skriptu SQL, například &quot;SELECT 1&quot; | ANO [1] | NO | ANO[1] |

> [!NOTE]
> [1] Chcete-li vytvořit fondy SQL nebo Spark, musí mít uživatel alespoň roli přispěvatele v pracovním prostoru Synapse.
> [!TIP]
>
> - Některé kroky nebudou záměrně povoleny v závislosti na roli.
> - Mějte na paměti, že některé úkoly mohou selhat, pokud zabezpečení nebylo plně nakonfigurováno. Tyto úkoly jsou uvedeny v tabulce.

## <a name="step-8-network-security"></a>KROK 8: Zabezpečení sítě

Konfigurace brány firewall pracovního prostoru, virtuální sítě a [privátního spojení](../../sql-database/sql-database-private-endpoint-overview.md).

## <a name="step-9-completion"></a>KROK 9: Dokončení

Váš pracovní prostor je nyní plně nakonfigurovaný a zabezpečený.

## <a name="how-roles-interact-with-synapse-studio"></a>Interakce rolí se Studiem Synapse

Synapse Studio se bude chovat odlišně na základě uživatelských rolí. Některé položky mohou být skryté nebo zakázané, pokud uživatel není přiřazen k rolím, které poskytují odpovídající přístup. Následující tabulka shrnuje vliv na Synapse Studio.

| Úkol | Správci pracovního prostoru | Správci Spark | Správci SQL |
| --- | --- | --- | --- |
| Otevřít Synapse Studio | ANO | ANO | ANO |
| Zobrazit centrum Domů | ANO | ANO | ANO |
| Zobrazit centrum dat | ANO | ANO | ANO |
| Datové centrum / Viz propojené účty a kontejnery ADLSGen2 | ANO [1] | ANO[1] | ANO[1] |
| Datové centrum / Zobrazit databáze | ANO | ANO | ANO |
| Centrum dat / Zobrazit objekty v databázích | ANO | ANO | ANO |
| Datové centrum / Přístup k datům v databázích fondu SQL | ANO   | NO   | ANO   |
| Centrum dat / Přístup k datům v databázích SQL na vyžádání | ANO [2]  | NO  | ANO [2]  |
| Datové centrum / Přístup k datům v databázích Spark | ANO [2] | ANO [2] | ANO [2] |
| Použití centra Vývoj | ANO | ANO | ANO |
| Vývoj hubu / autor a skripty SQL | ANO | NO | ANO |
| Vývoj Hubu / autora Spark Definice zakázek | ANO | ANO | NO |
| Vývoj hubových / autorských poznámkových bloků | ANO | ANO | NO |
| Develop Hub / autor Datové toky | ANO | NO | NO |
| Použití centra Orchestrovat | ANO | ANO | ANO |
| Orchestrace rozbočovače / použití potrubí | ANO | NO | NO |
| Použití centra Správa | ANO | ANO | ANO |
| Správa fondů hubů / SQL | ANO | NO | ANO |
| Správa fondů Hub / Spark | ANO | ANO | NO |
| Správa rozbočovače / aktivačních událostí | ANO | NO | NO |
| Správa hubu / Propojené služby | ANO | ANO | ANO |
| Správa rozbočovače / řízení přístupu (přiřazení uživatelů k rolím pracovního prostoru Synapse) | ANO | NO | NO |
| Správa běhu hubu / integrace | ANO | ANO | ANO |

> [!NOTE]
> [1] Přístup k datům v kontejnerech závisí na řízení přístupu v adlsgen2 [2] SQL OD tabulky a Spark tabulky ukládat svá data v ADLSGen2 a přístup vyžaduje příslušná oprávnění na ADLSGen2.

## <a name="next-steps"></a>Další kroky

Vytvoření [synapse pracovního prostoru](../quickstart-create-workspace.md)
