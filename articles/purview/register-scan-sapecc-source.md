---
title: Registrace zdroje SAP ECC a kontroly nastavení (Preview) ve službě Azure dosah
description: Tento článek popisuje, jak zaregistrovat zdroj SAP ECC ve službě Azure dosah a nastavit kontrolu.
author: chandrakavya
ms.author: kchandra
ms.service: purview
ms.subservice: purview-data-catalog
ms.topic: overview
ms.date: 2/25/2021
ms.openlocfilehash: b72538c61d1a68bca655b0d9529f654068727f86
ms.sourcegitcommit: c27a20b278f2ac758447418ea4c8c61e27927d6a
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/03/2021
ms.locfileid: "101696140"
---
# <a name="register-and-scan-sap-ecc-source-preview"></a>Registrace a kontrola zdroje SAP ECC (verze Preview)

Tento článek popisuje, jak v dosah zaregistrovat zdroj SAP ECC a nastavit kontrolu.

## <a name="supported-capabilities"></a>Podporované možnosti

Zdroj SAP ECC podporuje **úplnou kontrolu** pro extrakci metadat z instance SAP ECC a načítá mezi datovými assety **čáru** .

## <a name="prerequisites"></a>Požadavky

1.  Nastavte si nejnovější [prostředí Integration runtime](https://www.microsoft.com/download/details.aspx?id=39717)na místním hostiteli.
    Další informace najdete v tématu [Vytvoření a konfigurace prostředí Integration runtime](https://docs.microsoft.com/azure/data-factory/create-self-hosted-integration-runtime)v místním prostředí.

2.  Ujistěte se, že je na virtuálním počítači, kde je nainstalovaný modul runtime integrace v místním prostředí, nainstalovaný [JDK 11](https://www.oracle.com/java/technologies/javase-jdk11-downloads.html) .

3.  Ujistěte \" \" se, že je na počítači prostředí Integration runtime v místním prostředí nainstalovaná Visual C++ Redistributable 2012 Update 4. Pokud \' ho ještě nemáte nainstalovanou, Stáhněte si ho odsud. [](https://www.microsoft.com/download/details.aspx?id=30679)

4.  Stáhněte si balíček [SAP konektor 64 pro Microsoft .net 3,0](https://support.sap.com/en/product/connectors/msnet.html) z webu SAP \' s a nainstalujte ho do počítače s místním prostředím Integration runtime. Během instalace se ujistěte, že jste v okně **volitelné kroky instalace** vybrali možnost **instalovat sestavení do GAC** .

    :::image type="content" source="media/register-scan-sapecc-source/requirement.png" alt-text="před požadavky" border="true":::

5.  Konektor čte metadata z SAP pomocí rozhraní Java Connector (JCo) 3,0 API. Proto zajistěte, aby byl konektor Java k dispozici ve vašem virtuálním počítači, kde je nainstalován místní prostředí Integration runtime.
    Ujistěte se, že používáte správnou distribuci JCo pro vaše prostředí. Například na počítači se systémem Microsoft Windows se ujistěte, že jsou k dispozici soubory sapjco3. jar a sapjco3.dll.

    > [!Note] 
    > Ovladač by měl být přístupný pro všechny účty na virtuálním počítači. Neinstalujte ji do uživatelského účtu.

6.  Pomocí postupu uvedeného v [Průvodci nasazením ABAP Functions](abap-functions-deployment-guide.md)nasaďte na Server SAP modul funkce pro extrakci metadat ABAP. K vytvoření modulu funkce RFC na serveru SAP budete potřebovat vývojářský účet ABAP. Uživatelský účet vyžaduje dostatečná oprávnění pro připojení k serveru SAP a spouštění následujících modulů funkcí RFC:
    -   STFC_CONNECTION (ověřte připojení)
    -   RFC_SYSTEM_INFO (kontrolu systémových informací)


## <a name="setting-up-authentication-for-a-scan"></a>Nastavení ověřování pro kontrolu

Jediným podporovaným ověřováním pro zdroj SAP ECC je **základní ověřování**.

## <a name="register-sap-ecc-source"></a>Registrovat zdroj SAP ECC

Pokud chcete zaregistrovat nový zdroj SAP ECC v katalogu dat, udělejte toto:

1.  Přejděte k účtu dosah.
2.  V levém navigačním panelu vyberte **zdroje** .
3.  Vybrat **registraci**
4.  V nabídce zdroje registru vyberte **SAP ECC**. Vyberte **pokračovat.**

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc.png" alt-text="registrace možností SAPECC" border="true":::

Na obrazovce **Registrovat zdroje (SAP ECC)** udělejte toto:

1.  Zadejte **název** , který bude zdroj dat uveden v katalogu.

2.  Zadejte název **aplikačního serveru** pro připojení ke zdroji SAP ECC.
    Může to být také IP adresa hostitele aplikačního serveru SAP.

3.  Zadejte **číslo systému** SAP. Toto je dvouciferné celé číslo mezi 00 a 99.

4.  Vyberte kolekci nebo vytvořte novou (volitelné).

5.  Dokončete registraci zdroje dat.

    :::image type="content" source="media/register-scan-sapecc-source/register-sapecc-2.png" alt-text="zaregistrovat SAPECC" border="true":::

## <a name="creating-and-running-a-scan"></a>Vytvoření a spuštění kontroly

Pokud chcete vytvořit a spustit novou kontrolu, udělejte toto:

1.  V centru pro správu klikněte na modul runtime integrace. Ujistěte se, že je nastavený modul Integration runtime v místním prostředí. Pokud není nastavený, použijte postup uvedený [tady](https://docs.microsoft.com/azure/purview/manage-integration-runtimes) k vytvoření prostředí Integration runtime v místním prostředí.

2.  Přejít ke **zdrojům**

3.  Vyberte registrovaný zdroj SAP ECC.

4.  Vybrat **+ Nová kontrola**

5.  Zadejte následující podrobnosti:

    a.  **Name (název**): název kontroly

    b.  **Připojit prostřednictvím prostředí Integration runtime**: vyberte nakonfigurovaný místní prostředí Integration runtime.

    c.  **Přihlašovací údaje**: Vyberte přihlašovací údaje pro připojení ke zdroji dat. Nezapomeňte:

    -   Při vytváření přihlašovacích údajů vyberte základní ověřování.
    -   Zadejte ID uživatele pro připojení k serveru SAP ve vstupním poli uživatelské jméno.
    -   Uložte heslo uživatele používané pro připojení k serveru SAP v tajném klíči.

    d.  **ID klienta**: Zadejte ID klienta SAP. Toto je tři číslice čísla od 000 do 999.

    e.  **Cesta ke knihovně JCO**: cesta k adresáři, kde jsou umístěny knihovny JCO

    f.  **Maximální dostupná paměť:** Maximální velikost paměti (v GB), která je k dispozici na virtuálním počítači zákazníka pro použití při skenování procesů. Tato hodnota závisí na velikosti zdroje SAP ECC, který se má prohledat.

    :::image type="content" source="media/register-scan-sapecc-source/scan-sapecc.png" alt-text="Prohledat SAPECC" border="true":::

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