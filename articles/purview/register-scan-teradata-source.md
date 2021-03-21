---
title: Registrace zdroje Teradata a kontroly nastavení (Preview) ve službě Azure dosah
description: Tento článek popisuje, jak zaregistrovat zdroj Teradata v Azure dosah a nastavit kontrolu.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: 2008e014e9f160b643ed5f591fff81c0b215e24a
ms.sourcegitcommit: 867cb1b7a1f3a1f0b427282c648d411d0ca4f81f
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/20/2021
ms.locfileid: "102175030"
---
# <a name="register-and-scan-teradata-source-preview"></a>Registrovat a kontrolovat Zdroj Teradata (Preview)

Tento článek popisuje, jak zaregistrovat zdroj Teradata v dosah a nastavit kontrolu.

## <a name="supported-capabilities"></a>Podporované funkce

Zdroj Teradata podporuje **úplnou kontrolu** pro extrakci metadat z databáze Teradata a **načítání mezi** datovými assety.

## <a name="prerequisites"></a>Předpoklady

1.  Nastavte si nejnovější [prostředí Integration runtime](https://www.microsoft.com/download/details.aspx?id=39717)na místním hostiteli.
    Další informace najdete v tématu [Vytvoření a konfigurace prostředí Integration runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)v místním prostředí.

2.  Ujistěte se, že je na virtuálním počítači, kde je nainstalovaný modul runtime integrace v místním prostředí, nainstalovaný [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) .

3.  Ujistěte \" \" se, že je na počítači prostředí Integration runtime v místním prostředí nainstalovaná Visual C++ Redistributable 2012 Update 4. Pokud \' ho ještě nemáte nainstalovanou, Stáhněte si ho odsud. [](https://www.microsoft.com/download/details.aspx?id=30679)

4.  Na virtuální počítač, na kterém běží místní prostředí Integration runtime, budete muset ručně stáhnout ovladač JDBC Teradata.
    Spustitelný soubor JAR si můžete stáhnout z [webu](https://downloads.teradata.com/)Teradata.

    > [!Note]
    > Ovladač by měl být přístupný pro všechny účty na virtuálním počítači. Neinstalujte ho prosím v uživatelském účtu.

5.  Podporované verze databáze Teradata jsou 12. x až 16. x. Ujistěte se, že máte přístup pro čtení k skenovanému zdroji Teradata.

## <a name="setting-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu

Jediným podporovaným ověřováním pro zdroj Teradata je **základní ověřování**.

## <a name="register-a-teradata-source"></a>Registrace zdroje Teradata

Pokud chcete zaregistrovat nový zdroj Teradata v katalogu dat, udělejte toto:

1.  Přejděte k účtu dosah.
2.  V levém navigačním panelu vyberte **zdroje** .
3.  Vybrat **registraci**
4.  V nabídce registrovat zdroje vyberte **Teradata**. Vyberte **Pokračovat**.

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="registrovat možnosti Teradata" border="true":::

Na obrazovce **Registrovat zdroje (Teradata)** udělejte toto:

1.  Zadejte **název** , se kterým bude zdroj dat uveden v katalogu.

2.  Zadejte název **hostitele** pro připojení ke zdroji Teradata. Může to být také IP adresa nebo plně kvalifikovaný připojovací řetězec k serveru.

3.  Vyberte kolekci nebo vytvořte novou (volitelné).

4.  Dokončete registraci zdroje dat.

    :::image type="content" source="media/register-scan-teradata-source/register-sources-2.png" alt-text="registrovat Teradata" border="true":::

## <a name="creating-and-running-a-scan"></a>Vytvoření a spuštění kontroly

Pokud chcete vytvořit a spustit novou kontrolu, udělejte toto:

1.  V centru pro správu klikněte na **modul runtime integrace**. Ujistěte se, že je nastavený modul Integration runtime v místním prostředí. Pokud není nastavený, použijte postup uvedený [tady](https://docs.microsoft.com/azure/purview/manage-integration-runtimes) a nastavte prostředí Integration runtime v místním prostředí.

2.  Přejděte ke **zdrojům** .

3.  Vyberte registrovaný Zdroj Teradata.

4.  Vybrat **+ Nová kontrola**

5.  Zadejte následující podrobnosti:

    a.  **Name (název**): název kontroly

    b.  **Připojit prostřednictvím prostředí Integration runtime**: vyberte nakonfigurovaný místní prostředí Integration runtime.

    c.  **Přihlašovací údaje**: Vyberte přihlašovací údaje pro připojení ke zdroji dat. Nezapomeňte:

    -   Při vytváření přihlašovacích údajů vyberte základní ověřování.
    -   Zadejte uživatelské jméno pro připojení k databázovému serveru ve vstupním poli uživatelské jméno.
    -   Heslo databázového serveru uložte do tajného klíče.

        Pokud chcete získat další informace o přihlašovacích údajích, přečtěte si [tady](https://docs.microsoft.com/azure/purview/manage-credentials) odkaz.

6.  **Schema (schéma**): podmnožina schémat pro import vyjádřená jako seznam oddělený středníkem. například Schema1; schema2. Všechna schémata uživatelů jsou importována, pokud je tento seznam prázdný. Ve výchozím nastavení jsou všechna systémová schémata (například SysAdmin) a objekty ignorovány. Když je seznam prázdný, naimportují se všechna dostupná schémata.

    Přijatelné vzory názvů schémat pomocí syntaxe výrazů jako v jazyce SQL zahrnují použití%, např .%;. B % C%; Trojrozměrné
    - Začněte s nebo    
    - konec s B nebo    
    - obsahuje C nebo    
    - rovná se D

    Použití Nea speciálních znaků není přijatelné.

7.  **Umístění ovladače**: zadejte cestu k umístění ovladače JDBC ve vašem virtuálním počítači, kde je spuštěný modul runtime integrace na místním hostiteli. Mělo by se jednat o cestu k platnému umístění složky JAR.

8.  **Maximální dostupná paměť:** Maximální velikost paměti (v GB), která je k dispozici na virtuálním počítači zákazníka pro použití při skenování procesů. To závisí na velikosti zdroje Teradata, který se má prohledat.

    > [!Note] 
    > Jako pravidlo pro palec zadejte 2 GB paměti pro každou tabulku 1000.

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="Kontrola instalace" border="true":::

6.  Klikněte na **pokračovat**.

7.  Vyberte **aktivační událost kontroly**. Můžete nastavit plán nebo jednu kontrolu spouštět jednou.

8.  Zkontrolujte prověřování a klikněte na **Uložit a spustit**.

## <a name="viewing-your-scans-and-scan-runs"></a>Zobrazení kontrol a spuštění kontroly

1. Přejděte do centra pro správu. V části **zdroje a skenování** vyberte **zdroje dat** .

2. Vyberte požadovaný zdroj dat. Zobrazí se seznam existujících kontrol pro daný zdroj dat.

3. Vyberte kontrolu, jejichž výsledky chcete zobrazit.

4. Na této stránce se zobrazí všechna předchozí kontrola spuštěná spolu se metrikami a stavy pro každé spuštění skenování. Zobrazí se také informace o tom, zda byla vaše kontrola naplánovaná nebo ruční, kolik prostředků bylo použito klasifikace, kolik prostředků bylo zjištěno, čas zahájení a ukončení kontroly a celková doba kontroly.

## <a name="manage-your-scans"></a>Správa kontrol

Pokud chcete kontrolu spravovat nebo odstranit, postupujte takto:

1. Přejděte do centra pro správu. V části **zdroje a skenování** vyberte **zdroje dat** a potom vyberte požadovaný zdroj dat.

2. Vyberte kontrolu, kterou chcete spravovat. Kontrolu můžete upravit výběrem možnosti **Upravit**.

3. Kontrolu můžete odstranit výběrem možnosti **Odstranit**.

## <a name="next-steps"></a>Další kroky

- [Procházet Azure dosah Data Catalog](how-to-browse-catalog.md)
- [Hledání ve službě Azure dosah Data Catalog](how-to-search-catalog.md)