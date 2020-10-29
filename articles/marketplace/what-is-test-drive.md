---
title: Co je testovací verze? Komerční tržiště Microsoftu
description: Vysvětlení funkce Test Drive na webu Marketplace
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: article
author: keferna
ms.author: keferna
ms.date: 06/19/2020
ms.openlocfilehash: 4f78aa110ed42e17db873b9593de62602aa4193f
ms.sourcegitcommit: d76108b476259fe3f5f20a91ed2c237c1577df14
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 10/29/2020
ms.locfileid: "92911131"
---
# <a name="what-is-a-test-drive"></a>Co je testovací verze?

Testovací jednotka je skvělým způsobem, jak předvést vaši nabídku potenciálním zákazníkům tím, že jim nabídne možnost vyzkoušet si, jak si můžete koupit, vygenerovat vysoce kvalifikované potenciální zákazníky a výsledkem je vyšší konverze. Testovací jednotka přináší produkt do životnosti ve scénáři implementace reálného světa. Zákazníci, kteří si vyzkoušeli váš produkt, vykazují jasný záměr koupit podobné řešení. Využijte tyto výhody a využijte pokročilejších zájemců.

Vaše zákazníci mají i z testovacích jednotek. Tím, že jim umožníte vyzkoušet si produkt jako první, zmenšíte tření procesu nákupu. Kromě toho je testovací jednotka předem zřízena, tj. zákazníci nemusí stahovat, nastavovat ani konfigurovat produkt.

## <a name="how-does-it-work"></a>Jak to funguje?

Testovací jednotky jsou spravované instance, které spouštějí vaše řešení nebo aplikace na vyžádání pro zákazníky, kteří si je vyžádají. Jakmile je instance testovací jednotky přiřazená, je k dispozici pro daného zákazníka za nastavené období. Po skončení období se pak odstraní a vytvoří se místo pro jiného zákazníka.

Jako vydavatel spravujete a konfigurujete nastavení testovacích jednotek v partnerském centru. Podrobnosti technické konfigurace se liší v závislosti na typu nabídky. Podrobné pokyny najdete v [technické konfiguraci testovacích jednotek](https://docs.microsoft.com/azure/marketplace/test-drive-technical-configuration).

Potenciální zákazníci zjišťují vaši zkušební verzi jako výzva na vaší nabídce na [AppSource](https://appsource.microsoft.com/en-US/). Poskytují své kontaktní údaje a souhlasím s podmínkami vaší nabídky a zásadami ochrany osobních údajů. potom získáte přístup k předkonfigurovanému prostředí a vyzkoušíte ho po určitou dobu. Zákazníci obdrží praktickou zkušební verzi klíčových funkcí vašeho produktu a výhod a dostanete cenného zákazníka.

## <a name="types-of-test-drives"></a>Typy testovacích jednotek

Na komerčním webu Marketplace jsou k dispozici různé testovací jednotky pro vybrané nabídky v závislosti na typu produktu, scénáře a Marketplace, na kterém jste:

- Azure Resource Manager
    - Aplikace Azure
    - SaaS
    - Virtual Machines
- Hostovaný testovací disk
    - Dynamics 365 pro firmy – střed (aktuálně Nepodporováno)
    - Dynamics 365 for Customer Engagement
    - Dynamics 365 for Operations
- Aplikace logiky (jenom v režimu podpory)
- Power BI

Podrobnosti o konfiguraci jedné z těchto testovacích jednotek najdete v tématu [Technická konfigurace testovacích](https://docs.microsoft.com/azure/marketplace/test-drive-technical-configuration)jednotek. 

### <a name="azure-resource-manager-test-drive"></a>Azure Resource Manager testovacích jednotek

Tato šablona nasazení obsahuje všechny prostředky Azure, které tvoří vaše řešení. Produkty, které vyhovují tomuto scénáři, používají pouze prostředky Azure. K dispozici je Azure Resource Manager testovací jednotka pro tyto typy nabídek: 

- Aplikace Azure
- SaaS
- Virtuální počítače

>[!NOTE]
>Toto je jediná možnost testovacího disku pro virtuální počítač a nabídky aplikací Azure.

### <a name="hosted-test-drive-recommended"></a>Hostovaný testovací disk (doporučeno)

Hostovaná testovací jednotka odebere složitost nastavení tím, že umožní hostování společnosti Microsoft a údržbu služby, která provede test zřizování uživatelů a zrušení zřizování. Pokud máte nabídku Microsoft AppSource, sestavte testovací jednotku pro připojení k instanci Dynamics AX/CRM. Můžete použít následující typy nabídek AppSource:

- Pro zákaznickou práci se zákaznickým systémem, jako je například prodej, služba, služba projektu a služba pole, použijte [Dynamics 365 pro zapojení zákazníka](partner-center-portal/create-new-customer-engagement-offer.md) .
- Pro operace s plánováním podnikových prostředků podnikových zdrojů, jako jsou finance, operace a výrobní, dodavatelský řetězec, použijte [Dynamics 365](partner-center-portal/create-new-operations-offer.md) .

### <a name="logic-app-test-drive"></a>Testovací jednotka aplikace logiky

Tento typ testovacích jednotek není hostitelem Microsoft a používá šablony Azure Resource Manager (ARM) pro typy nabídek Dynamics AX/CRM. K vytvoření požadovaných prostředků ve vašem předplatném Azure budete muset spustit šablonu ARM. Testovací jednotka aplikace logiky je momentálně zapnutá jenom v režimu podpory a Microsoft nedoporučuje podrobnosti o konfiguraci testovacích jednotek aplikace logiky, viz [Technická konfigurace testovacích jednotek](https://docs.microsoft.com/azure/marketplace/test-drive-technical-configuration).

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

- [Osvědčené postupy testovacích jednotek](https://github.com/Azure/AzureTestDrive/wiki/Test-Drive-Best-Practices)
- [Přehled](https://assetsprod.microsoft.com/mpn/azure-marketplace-appsource-test-drives.pdf) (PDF; Ujistěte se, že je blokování automaticky otevíraných oken vypnuté)

## <a name="next-step"></a>Další krok

- [Technická konfigurace testovací verze](test-drive-technical-configuration.md)
