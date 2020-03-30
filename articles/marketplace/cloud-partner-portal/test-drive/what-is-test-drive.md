---
title: Co je testovací verze? | Azure Marketplace
description: Vysvětlení funkce Marketplace Test Drive
author: dsindona
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 09/13/2018
ms.author: dsindona
ms.openlocfilehash: bdfadf359195464c4024b28c5e597c571305481a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80278225"
---
<a name="what-is-test-drive"></a>Co je testovací verze?
===================

Test Drive je skvělý způsob, jak předvést svou nabídku \'potenciálním zákazníkům tím, že jim možnost vyzkoušet si před zakoupením\', což vede ke zvýšení konverze a generování vysoce kvalifikovaných zájemců.

Po poskytnutí kontaktních informací mají zákazníci přístup k vašemu předem vytvořenému prostředí testovací\'jízdy: praktické zkušební verzi klíčových funkcí produktu s vlastním průvodcem a výhody, které jsou demonstrovány ve scénáři implementace v reálném světě.

Test Drive vám umožní přivést svůj produkt k životu a generovat vysoce kvalifikované zájemce v tomto procesu.

<a name="how-does-a-test-drive-work"></a>Jak funguje testovací jízda?
---------------------------

Potenciální zákazník zjistí vaši aplikaci na Marketplace, přihlásí se a souhlasí s podmínkami použití. V tomto okamžiku zákazník obdrží předem nakonfigurované prostředí, aby se pokusil o pevný počet hodin, zatímco obdržíte vysoce kvalifikovaného zájemce, se kterým chcete sledovat.

![Krok jedna. Zobrazená nabídka marketplace](./media/what-is-test-drive/step1.png)

![Krok dva. Obrazovka pro přihlášení k nabídce marketplace](./media/what-is-test-drive/step1andahalf.png)

![Krok tři. Obrazovka smlouvy Smarketplace Nabídka vydavatele](./media/what-is-test-drive/step2.png)

![Krok čtyři. Obrazovka nastavení testovací jednotky](./media/what-is-test-drive/step3.png)

A zde níže je příklad toho, jak nabídka vypadá, když potřebuje čas na nasazení:

![Nabídka Marketplace se nasazuje](./media/what-is-test-drive/step4.png)

![Obrazovka připravenosti na testovací disku marketplace](./media/what-is-test-drive/step5.png)

![Obrazovka Smarketplace Test Drive je celá](./media/what-is-test-drive/step6.png)

Bez ohledu na to, jak složitá je vaše aplikace, microsoft test drive vám pomůže přivést váš produkt k životu pro zákazníka. Dnes nabízíme tři různé typy testovacích jednotek na základě typu produktu, scénáře a tržiště, na kterých se nacházíte.

- **[Azure Resource Manager](./azure-resource-manager-test-drive.md)**: Azure Resource Manager Test Drive je šablona nasazení, která obsahuje všechny prostředky Azure, které obsahují řešení, které se staví vydavatel. Produkty, které odpovídají tomuto scénáři jsou ty, které používají pouze prostředky Azure.
- **[Aplikace logiky](./logic-app-test-drive.md)**: Testovací jednotka aplikace logiky je šablona nasazení, která má zahrnovat všechny architektury komplexních řešení. Všechny aplikace Dynamics nebo vlastní produkty by měly používat tento typ testovací jednotky.
- **Power BI**: Testovací jednotka Power BI je jednoduše vložený odkaz na vlastní sestavený řídicí panel. Každý produkt, který chce jen předvést interaktivní vizuál Power BI, by měl používat tento typ testovací hodu.
    Tady stačí nahrát vložená adresa URL Power BI.

<a name="what-goes-on-in-the-background"></a>Co se děje v pozadí?
-------------------------------

Služba Test Drive je navržena tak, aby nepřetržitě podporovala a sloužila vašim zákazníkům, aniž by od vás vyžadovala jakékoli ruční úsilí. Jako vydavatel je vaším úkolem spravovat a konfigurovat nastavení testovací jednotky z portálu partnerů cloudu a toto nastavení bude přímo dostupné pro vaše zákazníky.

Je to proto, že po nastavení konfigurace pro testovací disk se každá testovací jednotka stane spravovanou instancí, která bude nasazena na vyžádání pro zákazníka, který o ni požádá. Po přiřazení instance testovací jednotky je testovací jednotka k dispozici pro použití po nastavenou dobu a poté se odstraní, aby se vytvořilprostor pro jiného zákazníka.

<a name="next-steps"></a>Další kroky
----------

Teď, když víte, o čem je testovací jízda, navštivte konkrétní typ testovací jednotky, který chcete publikovat, abyste se dozvěděli vše o požadovaných polích.

- **[Správce prostředků Azure](./azure-resource-manager-test-drive.md)**
- **[Aplikace logiky](./logic-app-test-drive.md)**

Máte-li další dotazy, hledáte rady pro řešení potíží nebo chcete, aby byla vaše testovací jízda úspěšnější, přejděte na [nejčastější dotazy, řešení potíží & doporučené postupy](./marketing-and-best-practices.md).
