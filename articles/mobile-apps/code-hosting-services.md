---
title: Hostování zdrojového kódu mobilní aplikace v cloudu pomocí GitHubu a Azure DevOps
description: Přečtěte si o službách pro hostování kódu mobilní aplikace v cloudu pomocí služeb Microsoftu.
author: elamalani
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 10/22/2019
ms.author: emalani
ms.openlocfilehash: 8662b9f77614339ff514fa4fcf97dc1ee8fc7417
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 12/25/2019
ms.locfileid: "75454490"
---
# <a name="cloud-hosted-source-code-management-services"></a>Služby správy zdrojového kódu hostované v cloudu
Pokud máte vývojové týmy s více členy týmu, kteří pracují na stejném základu kódu, je nutné najít vhodné místo pro hostování vašeho kódu. Ukládání dat v cloudu a používání centralizovaného úložiště umožňuje všem ukládat, upravovat a spravovat soubory s kódem. Můžou taky spolupracovat s ostatními vývojáři na projektech. Kód může být snadno přístupný bez ohledu na to, kde jste, pokud máte připojení k Internetu.

Hostování cloudu je mnohem jednodušší než u místních možností. Vyžaduje méně konfigurace hardwaru a umožňuje organizacím dokončit proces implementace pružnější způsobem.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Výhody hostování zdrojového kódu v cloudu
- **Centralizované cloudové úložiště:** Umožňuje zobrazit a spravovat vaše data odkudkoli.
- **Lepší spolupráce a čisticí kód:** Udržujte si přehled o kódu v rámci týmů a spravujte projekty, abyste zajistili průběžné doručování skvělého softwaru.
- **Buďte snazší:** Snadno přispívat k vašim projektům.
- **Rychlejší cyklus vydávání verzí:** Pracujte rychleji ve svých týmech a snadno přispívat k vašim projektům.
- **Snižte náklady:** Nedělejte si starosti s údržbou vlastního hardwaru, serverů, sítě VPN a tak dále.

K hostování aplikačních dat v cloudu použijte následující služby.

## <a name="github"></a>GitHubu
[GitHub](https://github.com/) je open source hostovaná služba, která hostuje projekty zdrojového kódu v různých programovacích jazycích. GitHub sleduje různé změny provedené u každé iterace.

**Klíčové funkce**
- Používejte hostování kódu, abyste zachovali veškerý kód na jednom místě. Úložiště privátních, veřejných a open source jsou vybavená nástroji, které vám pomůžou s hostováním, verzí a kódem verze.
- Přečtěte si kód a používejte integrované nástroje pro kontrolu, abyste provedli zásadní část procesu týmu:
    - Chraňte pobočky, navrhujte změny a vyžádejte si recenze.
    - Rozdíl mezi rozdíly, komentář v kontextu a získání zpětné vazby
- Informujte se na začátku, zůstaňte zarovnané a získejte víc práce s nástroji pro správu projektu na GitHubu:
    - Podívejte se na velký obrázek projektu.
    - Použijte panely úloh, které jsou přímo vedle vašeho kódu v GitHubu.
    - Přetažením karet můžete přiřadit problémy nebo žádosti o přijetí změn členům týmu.
    - Nastavte milníky pro uspořádání a sledování průběhu.
    - Poznámky k zachycení nápadů, které mohou být užitečné, ale nepatří do konkrétního problému nebo žádosti o přijetí změn.
- Umožňuje snadno vyhledat a zvolit správné nástroje pro lepší komunikaci a automatizaci práce zakoupením aplikací z [webu GitHub Marketplace](https://github.com/marketplace).
- Spravujte a rozšiřujte týmy pomocí: 
    - Role uživatelů, které vám pomůžou organizovat týmy a přístupová oprávnění
    - Nástroje diskuzového vlákna, které vám umožní udržet si konverzace o zaměření na téma a tým.
    - Pokyny komunity pro rychlé nastavení nových členů týmu pomocí účtu.
- Procházejte Nejoblíbenějšími projekty a sledujte je.
- Síť a Naučte se od ostatních v oboru.

**Odkazy**
- [GitHub](https://github.com/)
- [Příručky pro GitHub](https://guides.github.com/)
- [Komunitní fórum GitHubu](https://github.community/)
- [Tržiště GitHubu](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/) podporuje [Azure Repos](https://azure.microsoft.com/services/devops/repos/) a [Správa verzí Team Foundation (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) jako možnosti správy zdrojového kódu. Má neomezená bezplatná privátní úložiště s revizemi kódu, pokročilou správou souborů, vyhledáváním kódu a zásadami větví, abyste zajistili vysoce kvalitní kód. Azure Repos je skvělé pro malé projekty a velké organizace, které potřebují nativní Azure Active Directory podporu a pokročilé zásady.
    
**Klíčové funkce**
- Používejte neomezené úložiště zdrojového kódu Git hostovaného v cloudu pro vaše veřejná a privátní úložiště:
    - Získejte podporu pro všechny klienty Git.
    - Používejte webové háky a integraci rozhraní API.
- Vyžádejte si další Build od žádosti o získání dat:
    - Spolupracujte a Sestavujte lepší kód pomocí diskusí s vlákny a průběžné integrace pro každou změnu.
    - Nastavte průběžnou integraci/průběžné doručování (CI/CD), aby se při každé dokončené žádosti o přijetí změn automaticky spouštěla sestavení, testy a nasazení. Můžete použít Azure Pipelines nebo nástroje.
    - Chraňte své kvality kódu pomocí zásad větví.
- Udržujte centralizovanou správu verzí pomocí Správa verzí Team Foundation, která zahrnuje revizi kódu.
- Připojte se ke svému kódu pomocí Xcode, zatmění, IntelliJ, Android Studio, Visual Studio, Visual Studio Code a dalších.
- Používejte výkonné vyhledávání sémantických kódů.
- Získejte výhody z funkcí připravených pro podnik. Azure DevOps má nativní integraci s Azure Active Directory, což zjednodušuje proces správy přístupu k úložištím kódu.
- Zajistěte kvalitu kódu pomocí zásad větví, jako je například minimální počet revizí kódu, požadavky na úspěšná sestavení a vynucování strategií sloučení Git.
- Připojte své oblíbené vývojové prostředí pro přístup k úložištím a správě práce.

**Odkazy**
- [Začínáme s Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Dokumentace k Azure Repos](/azure/devops/repos/?view=azure-devops)