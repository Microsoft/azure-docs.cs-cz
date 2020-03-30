---
title: Hostování zdrojového kódu mobilní aplikace v cloudu pomocí GitHubu a Azure DevOps
description: Přečtěte si o službách pro hostování kódu mobilní aplikace v cloudu se službami Microsoftu.
author: codemillmatt
ms.assetid: 12a8a079-9b3c-4faf-2222-ccff02097224
ms.service: vs-appcenter
ms.topic: article
ms.date: 03/24/2020
ms.author: masoucou
ms.openlocfilehash: 66d8980ab53010af0703d789fbe791c60a32052d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: cs-CZ
ms.lasthandoff: 03/28/2020
ms.locfileid: "80240961"
---
# <a name="cloud-hosted-source-code-management-services"></a>Služby správy zdrojového kódu hostované v cloudu
Pokud máte vývojové týmy s více členy týmu, kteří pracují na stejném základu kódu, musíte najít vhodné místo pro hostování kódu. Ukládání dat v cloudu a centralizované úložiště umožňuje všem uživatelům nahrávat, upravovat a spravovat soubory kódu. Mohou také komunikovat s ostatními vývojáři na projektech. Kód může být snadno dostupný bez ohledu na to, kde se právě uvidíte, pokud máte připojení k internetu.

Cloudhosting je mnohem jednodušší než místní možnosti. Vyžaduje méně hardwarové konfigurace a umožňuje organizacím dokončit proces implementace agilnějším způsobem.

## <a name="benefits-of-hosting-source-code-in-the-cloud"></a>Výhody hostování zdrojového kódu v cloudu
- **Centralizované cloudové úložiště:** Zobrazení a správa dat odkudkoli.
- **Lepší spolupráce a čistší kód:** Sledujte kód v rámci týmů a spravujte projekty, abyste zajistili nepřetržité doručování vynikajícího softwaru.
- **Snadnější zapojení:** Snadno přispívat do vašich projektů.
- **Rychlejší cyklus uvolňování:** Pracujte rychleji ve svých týmech a snadno přispějte do svých projektů.
- **Snižte náklady:** Nebojte se o údržbu vlastního hardwaru, serverů, VPN a tak dále.

K hostování dat aplikací v cloudu použijte následující služby.

## <a name="github"></a>GitHub
[GitHub](https://github.com/) je hostingová služba s otevřeným zdrojovým kódem, která hostuje projekty zdrojového kódu v různých programovacích jazycích. GitHub sleduje různé změny provedené v každé iteraci.

**Klíčové funkce**
- Použijte kód hosting, aby všechny vaše kód na jednom místě. Privátní, veřejná a open source úložiště jsou vybavena nástroji, které vám pomohou hostovat, verze a kód vydání.
- Zkontrolujte kód a pomocí integrovaných nástrojů pro kontrolu, aby se kontrola kódu neměnná součást procesu každého týmu:
    - Chraňte pobočky, navrhujte změny a vyžádejte si recenze.
    - Spot rozdíly, komentář v kontextu, a získat jasnou zpětnou vazbu.
- Koordinujte se včas, zůstaňte v souladu a zvládáte více práce s nástroji githubu pro řízení projektů:
    - Podívejte se na celkový obraz projektu.
    - Používejte vývěsky úloh, které jsou hned vedle vašeho kódu uvnitř GitHubu.
    - Přetáhněte karty a přiřaďte členům týmu problémy nebo žádosti o přijetí vyžádat.
    - Nastavte milníky pro uspořádání a sledování průběhu.
    - Psát poznámky zachytit nápady, které mohou být užitečné, ale nepatří k určitému problému nebo žádosti o přijetí vyžádat.
- Snadno objevte a vyberte správné nástroje pro lepší komunikaci a automatizaci práce nákupem aplikací z [GitHub Marketplace](https://github.com/marketplace).
- Spravujte a rozšiřujte týmy pomocí: 
    - Role uživatelů, které pomáhají organizovat týmy a získat přístup k oprávněním.
    - Nástroje diskusního vlákna, které udržují konverzace na téma a týmové zaměření.
    - Pokyny pro komunitu pro rychlé nastavení nových členů týmu s účtem.
- Procházejte a hvězdy populární projekty, které je následují.
- sítě a učit se od ostatních v průmyslu.

**Odkazy**
- [GitHub](https://github.com/)
- [Průvodce GitHubem](https://guides.github.com/)
- [Komunitní fórum GitHubu](https://github.community/)
- [GitHub Marketplace](https://github.com/marketplace)

## <a name="azure-devops"></a>Azure DevOps
[Azure DevOps](https://azure.microsoft.com/services/devops/) podporuje [Azure Repos](https://azure.microsoft.com/services/devops/repos/) a [Team Foundation Version Control (TFVC)](https://docs.microsoft.com/azure/devops/repos/tfvc/index?view=azure-devops) jako možnosti správy zdrojového kódu. Má neomezené bezplatné soukromé repozitáře s kolaborativními kontrolami kódu, pokročilou správou souborů, vyhledáváním kódu a zásadami větví, které zajišťují vysoce kvalitní kód. Azure Repos je skvělý pro malé projekty a velké organizace, které potřebují nativní podporu Azure Active Directory a pokročilé zásady.
    
**Klíčové funkce**
- Pro vaše veřejné a soukromé úložiště úložiště použijte neomezené úložiště zdrojového kódu Git hostované v cloudu:
    - Získejte podporu pro každého klienta Git.
    - Používejte webové háčky a integraci rozhraní API.
- Nastartujte další sestavení z žádosti o přijetí na vyžádání repo:
    - Spolupracujte na vytváření lepšího kódu pomocí diskuse pod vlákny a průběžné integrace pro každou změnu.
    - Nastavte průběžnou integraci/průběžné doručování (CI/CD) pro automatické spouštění sestavení, testů a nasazení s každou dokončenou žádostí o přijetí vyžádat. Můžete použít Azure Pipelines nebo nástroje.
    - Chraňte kvalitu kódu pomocí zásad větvení.
- Udržujte centralizované řízení verzí pomocí správy verzí Team Foundation, která zahrnuje kontrolu kódu.
- Připojte se ke kódu pomocí Xcode, Eclipse, IntelliJ, Android Studio, Visual Studio, Visual Studio Code a další.
- Používejte výkonné vyhledávání sémantického kódu.
- Získejte výhody z funkcí připravených pro podniky. Azure DevOps má nativní integraci s Azure Active Directory, což zjednodušuje proces správy přístupu k úložištím kódu.
- Zajistěte kvalitu kódu pomocí zásad větve, jako je minimální počet kontrol kódu, požadavky na úspěšná sestavení a vynucení strategií sloučení Gitu.
- Připojte své oblíbené vývojové prostředí pro přístup k repos a spravovat práci.

**Odkazy**
- [Začínáme s Azure Repos](https://azure.microsoft.com/services/devops/repos/) 
- [Dokumentace azure repos](/azure/devops/repos/?view=azure-devops)