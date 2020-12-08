---
title: Zaregistrujte kontroly zdrojů a nastavení Teradata (Preview).
description: Tento článek popisuje, jak zaregistrovat zdroj Teradata v Azure dosah a nastavit kontrolu.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 12/06/2020
ms.openlocfilehash: 170ece293f8d24f3a29774c64c14f9334fe0930c
ms.sourcegitcommit: 8b4b4e060c109a97d58e8f8df6f5d759f1ef12cf
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/07/2020
ms.locfileid: "96841919"
---
# <a name="register-and-scan-teradata-source-preview"></a>Registrovat a kontrolovat Zdroj Teradata (Preview)

Tento článek popisuje, jak zaregistrovat zdroj Teradata v dosah a nastavit kontrolu.

> [!IMPORTANT]
> Tento zdroj dat je momentálně ve verzi Preview. Můžete si to vyzkoušet a sdělit nám svůj názor.

## <a name="supported-capabilities"></a>Podporované možnosti

Zdroj Teradata podporuje **úplnou kontrolu** pro extrakci metadat z databáze Teradata a **načítání mezi** datovými assety.

## <a name="prerequisites"></a>Předpoklady

- Konektor podporuje úložiště dat umístěné pouze v místní síti, ve virtuální síti Azure nebo ve virtuálním privátním cloudu Amazon. Proto musíte nastavit [prostředí Integration runtime](manage-integration-runtimes.md) v místním prostředí, aby se k němu mohlo připojit.

- Ujistěte se, že je na vašem virtuálním počítači, kde je nainstalovaný modul runtime integrace v místním prostředí, nainstalovaný Java Runtime Environment (JRE).
 
- Ujistěte se, že je na počítači prostředí Integration runtime v místním prostředí nainstalovaná verze Visual C++ Redistributable 2012 Update 4. Pokud ho ještě nemáte nainstalovanou, [Stáhněte si ho odsud.](https://www.microsoft.com/download/details.aspx?id=30679)

- Na místní virtuální počítač budete muset ručně nainstalovat ovladač s názvem Teradata JDBC Driver. Spustitelný soubor JAR si můžete stáhnout z [webu Teradata](https://downloads.teradata.com/).

    > [!Note] 
    > Ovladač by měl být přístupný pro všechny účty na virtuálním počítači. Neinstalujte ho prosím v uživatelském účtu.

- Podporované verze databáze Teradata jsou **12. x až 16. x**. Ujistěte se, že máte přístup pro čtení k skenovanému zdroji Teradata.

### <a name="feature-flag"></a>Příznak funkce

Registrace a kontrola zdroje Teradata jsou k dispozici za příznakem funkce. Přidejte k adrese URL následující: *? funkce. ext. DataSource =% 7b "Teradata": "true"% 7d* 

Například úplná adresa URL [ https://web.purview.azure.com/?feature.ext.datasource=%7b "metadata": "pravda"% 7d](https://web.purview.azure.com/?feature.ext.datasource=%7b"teradata":"true"%7d)

## <a name="setting-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu
Jediným způsobem, který podporuje nastavení ověřování pro zdroj Teradata, je **základní ověřování databáze** .

## <a name="register-a-teradata-source"></a>Registrace zdroje Teradata

Pokud chcete zaregistrovat nový zdroj Teradata v katalogu dat, udělejte toto:

1. Přejděte k účtu dosah.
2. Výběr **zdrojů** na levém navigačním panelu
3. Vybrat **registraci**
4. V nabídce **Registrovat zdroje** vyberte **Teradata**
5. Vyberte **Pokračovat**.

Na obrazovce **Registrovat zdroje (Teradata)** udělejte toto:

1. Zadejte **název** , se kterým bude zdroj dat uveden v katalogu.
2. Zadejte název **hostitele** pro připojení ke zdroji Teradata. Může to být také IP adresa nebo plně kvalifikovaný připojovací řetězec k serveru.
3. Vyberte kolekci nebo vytvořte novou (volitelné).
4. **Dokončete** registraci zdroje dat.

    :::image type="content" source="media/register-scan-teradata-source/register-sources.png" alt-text="Možnosti registrace zdrojů" border="true":::

## <a name="creating-and-running-a-scan"></a>Vytvoření a spuštění kontroly

Pokud chcete vytvořit a spustit novou kontrolu, udělejte toto:
1. V centru pro správu klikněte na *modul runtime integrace*. Ujistěte se, že je nastavený modul Integration runtime v místním prostředí. Pokud není nastavený, použijte postup uvedený [tady](manage-integration-runtimes.md) k vytvoření prostředí Integration runtime v místním prostředí pro vyhledávání na místním nebo virtuálním počítači Azure, který má přístup k vaší místní síti.

2. Dále přejděte do **zdrojů** .

3. Vyberte registrovaný Zdroj Teradata.

4. Vybrat + nová kontrola
 
5. Zadejte následující podrobnosti:

    - Name (název): název kontroly

    - Připojit prostřednictvím prostředí Integration Runtime: vyberte nakonfigurovaný místní prostředí Integration runtime.

    - Metoda ověřování: jedinou podporovanou možností je ověřování databáze. Tato možnost bude vybrána ve výchozím nastavení.

    - Uživatelské jméno: uživatelské jméno pro připojení k databázovému serveru. Toto uživatelské jméno by mělo mít přístup pro čtení k serveru.

    - Heslo: heslo uživatele používané pro připojení k databázovému serveru

    - Schema (schéma): podmnožina schémat pro import vyjádřená jako seznam oddělený středníkem. např. Schema1; schema2. Všechna schémata uživatelů jsou importována, pokud je tento seznam prázdný. Ve výchozím nastavení jsou všechna systémová schémata (například SysAdmin) a objekty ignorovány.

        Přijatelné vzory názvů schémat pomocí syntaxe výrazů jako v jazyce SQL zahrnují použití%, např .%;. B % C%; Trojrozměrné
        - Začněte s nebo    
        - konec s B nebo    
        - obsahuje C nebo    
        - rovná se D

        Použití Nea speciálních znaků není přijatelné.

    - Umístění ovladače: úplná cesta k umístění ovladače Teradata na virtuálním počítači zákazníka. Název ovladače Teradata JDBC musí být: com. Teradata. JDBC. TeraDriver

    - Maximální dostupná paměť: maximální velikost paměti (v GB), která je dostupná na virtuálním počítači zákazníka, který se bude používat při skenování procesů. To závisí na velikosti zdroje Teradata, který se má prohledat.

    > [!Note] 
    > Jako pravidlo pro palec zadejte 2 GB paměti pro každou tabulku 1000.

    :::image type="content" source="media/register-scan-teradata-source/setup-scan.png" alt-text="Kontrola instalace" border="true":::

6. Klikněte na *pokračovat.*

7. Vyberte aktivační událost kontroly. Můžete nastavit plán nebo jednu kontrolu spouštět jednou.

    :::image type="content" source="media/register-scan-teradata-source/scan-trigger.png" alt-text="aktivační událost kontroly" border="true":::

8. Zkontrolujte prověřování a klikněte na *Uložit a spustit.*

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
