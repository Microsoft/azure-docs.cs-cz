---
title: Co je testovací verze? Komerční tržiště Microsoftu
description: Vysvětlení funkce Test Drive na webu Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 06/19/2020
ms.openlocfilehash: bd3b77f80a414dd3db1d5106929fa0e215e34c1f
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/09/2020
ms.locfileid: "88121863"
---
# <a name="what-is-a-test-drive"></a>Co je testovací verze?

Testovací jednotka představuje skvělý způsob, jak předvést vaši nabídku potenciálním zákazníkům tím, že jim nabídne možnost *vyzkoušet*si, jak si je koupit, a výsledkem je vyšší konverze a generace vysoce kvalifikovaných potenciálních zákazníků. Testovací jednotka přináší vašemu produktu život v reálném scénáři implementace a zároveň generuje vysoce kvalifikované potenciální zákazníky.

Testovací jednotky jsou spravované instance, které nasazují vaše řešení nebo aplikace na vyžádání pro zákazníky, kteří si je vyžádají. Jakmile je instance testovací jednotky přiřazena, je k dispozici pro použití v nastaveném časovém intervalu a pak se odstraní, aby se vytvořila místnost pro jiného zákazníka.

Jako vydavatel spravujete a konfigurujete nastavení testovacích jednotek v partnerském centru. Podrobnosti technické konfigurace se liší v závislosti na typu nabídky, se kterou pracujete. Podrobné pokyny najdete v části [Další krok](#next-step) na konci tohoto tématu.

Potenciální zákazníci si vyzkouší vaši testovací jednotku na komerčním webu Marketplace. Poskytují své kontaktní údaje a souhlasím s podmínkami vaší nabídky a zásadami ochrany osobních údajů a pak získají přístup k předkonfigurovanému prostředí, aby je bylo možné vyzkoušet po určitou dobu. Zákazníci obdrží praktickou zkušební verzi klíčových funkcí vašeho produktu a výhod a dostanete cenného zákazníka.

## <a name="how-does-it-work"></a>Jak to funguje?

Jako vydavatel spravujete a konfigurujete nastavení testovacích jednotek v partnerském centru. Po dokončení instalace se spustí spravovaná instance, která bude na vyžádání nasazena pro zákazníka požadujícího IT. Jakmile je instance testovací jednotky přiřazena, je k dispozici pro použití v nastaveném časovém intervalu a pak se odstraní, aby se vytvořila místnost pro jiného zákazníka.

## <a name="types-of-test-drives"></a>Typy testovacích jednotek

Na komerčním webu Marketplace jsou k dispozici různé testovací jednotky pro vybrané nabídky v závislosti na typu produktu, scénáře a Marketplace, na kterém jste:

- Azure Resource Manager
- Hostovaný testovací disk
    - Dynamics 365 for Business Central
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Aplikace logiky
- Power BI

Podrobnosti o konfiguraci jedné z těchto testovacích jednotek najdete v části [Další krok](#next-step) na konci tohoto tématu.

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager testovacích jednotek

Tato šablona nasazení obsahuje všechny prostředky Azure, které tvoří vaše řešení. Produkty, které vyhovují tomuto scénáři, používají pouze prostředky Azure. K dispozici je Azure Resource Manager testovací jednotka pro tyto typy nabídek: 

- Aplikace Azure
- Dynamics 365 for Business Central
- Dynamics 365 pro Customer Engagement & PowerApps
- Dynamics 365 for Operations
- SaaS
- Virtuální počítače

>[!NOTE]
>Toto je jediná možnost testovacího disku pro virtuální počítač a nabídky aplikací Azure.

### <a name="hosted-test-drive"></a>Hostovaný testovací disk

Hostovaná testovací jednotka odebere složitost nastavení tím, že umožní hostování Microsoftu a údržbu služby, která provede testovací zřízení, nasazení a zrušení zřizování uživatelů. Pokud máte k dispozici nabídku Microsoft AppSource, Sestavte si testovací jednotku pro připojení k instanci Dynamics AX/CRM nebo jinému prostředku mimo platformu Azure. Tento typ použijte pro AppSource nabídky pro připojení k těmto nabídkám Dynamics 365:

- Použijte [Dynamics 365 pro firmy Business](partner-center-portal/create-new-operations-offer.md) Central pro systém plánování podnikových prostředků, jako jsou finance, operace, dodavatelský řetězec a CRM.
- Pro zákaznickou práci se zákaznickým systémem, jako je například prodej, služba, služba projektu a služba pole, použijte [Dynamics 365 pro zapojení zákazníka](partner-center-portal/create-new-customer-engagement-offer.md) .
- Pro operace s plánováním podnikových prostředků podnikových zdrojů, jako jsou finance, operace a výrobní, dodavatelský řetězec, použijte [Dynamics 365](partner-center-portal/create-new-operations-offer.md) .

### <a name="logic-app-test-drive"></a>Testovací jednotka aplikace logiky

Tento typ testovacích jednotek není hostitelem společnosti Microsoft. Použijte ho pro připojení k nabídce Dynamics 365 nebo k jinému vlastnímu prostředku.

### <a name="power-bi-test-drive"></a>Power BI testovacích jednotek

Toto je jednoduše vložené propojení na vlastní řídicí panel. Každý produkt, který ukazuje pouze interaktivní Power BI vizuál, by měl používat tento typ testovacích jednotek.

## <a name="transforming-examples"></a>Příklady transformace

Proces zapnutí architektury prostředků do testovacích jednotek může být těžké. Podívejte se na tyto příklady, jak nejlépe transformovat aktuální architektury.

[Transformace šablony webu na testovací jednotku](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Website-Deployment-Template-for-Test-Drive)

[Transformace šablony virtuálního počítače na testovací jednotku](https://github.com/Azure/AzureTestDrive/wiki/Transforming-Virtual-Machine-Deployment-Template-for-Test-Drive)

[Transformuje stávající šablonu Správce prostředků na testovací jednotku.](https://github.com/Azure/AzureTestDrive/wiki/Deploying-Existing-Solutions)

## <a name="generate-leads-from-your-test-drive"></a>Generování zájemců z testovacího disku

Testovací jednotka komerčního tržiště je skvělým nástrojem pro pracovníky marketingu. Doporučujeme, abyste ho zahrnuli do úsilí o uvedení na trh, když spustíte pro vygenerování dalších zájemců pro vaši firmu. Podrobné pokyny najdete v [zákaznických zákaznících z nabídky komerčních webů na webu Marketplace](https://github.com/MicrosoftDocs/azure-docs/blob/master/articles/marketplace/partner-center-portal/commercial-marketplace-get-customer-leads.md).

Pokud uzavřete obchod se zájemcem testovacích jednotek, nezapomeňte ho zaregistrovat na [webu Microsoft Partner Sales Connect](https://support.microsoft.com/help/3155788/getting-started-with-microsoft-partner-sales-connect). Rádi bychom měli slyšet informace o zákazníkovi, kde zkušební jednotka přehrála roli.

## <a name="other-resources"></a>Další prostředky

Další zdroje testovacích jednotek:

- [Technické osvědčené postupy](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Přehled](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; Ujistěte se, že je blokování automaticky otevíraných oken vypnuté)

## <a name="next-step"></a>Další krok

- [Technická konfigurace testovací verze](test-drive-technical-configuration.md)
